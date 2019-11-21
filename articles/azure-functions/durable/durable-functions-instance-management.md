---
title: Gerenciar instâncias nas Funções Duráveis – Azure
description: Saiba como gerenciar instâncias na extensão de Funções Duráveis do Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: ab9cc9b093008730d175fa3fde4391f9de236a84
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231374"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Gerenciar instâncias em Durable Functions no Azure

If you're using the [Durable Functions](durable-functions-overview.md) extension for Azure Functions, or want to start doing so, make sure you're getting the best use out of it. You can optimize your Durable Functions orchestration instances by learning more about how to manage them. Este artigo mostra os detalhes de cada operação de gerenciamento de instância.

You can start and terminate instances, for example, and you can query instances, including the ability to query all instances and query instances with filters. Additionally, you can send events to instances, wait for orchestration completion, and retrieve HTTP management webhook URLs. This article covers other management operations, too, including rewinding instances, purging instance history, and deleting a task hub.

In Durable Functions, you have options for how you want to implement each of these management operations. This article provides examples that use the [Azure Functions Core Tools](../functions-run-local.md) for both .NET (C#) and JavaScript.

## <a name="start-instances"></a>Start instances

It's important to be able to start an instance of orchestration. This is commonly done when you are using a Durable Functions binding in another function's trigger.

The `StartNewAsync` (.NET) or `startNew` (JavaScript) method on the [orchestration client binding](durable-functions-bindings.md#orchestration-client) starts a new instance. Internally, this method enqueues a message into the control queue, which then triggers the start of a function with the specified name that uses the [orchestration trigger binding](durable-functions-bindings.md#orchestration-trigger).

Essa operação assíncrona é concluída quando o processo de orquestração é agendado com êxito.

The parameters for starting a new orchestration instance are as follows:

* **Name**: o nome da função de orquestrador a ser agendada.
* **Input**: Qualquer dado serializável em JSON, que deve ser passado como a entrada para a função de orquestrador.
* **InstanceId**: (opcional) a ID exclusiva da instância. If you don't specify this parameter, the method uses a random ID.

> [!TIP]
> Use um identificador aleatório para a ID de instância. Random instance IDs help ensure an equal load distribution when you're scaling orchestrator functions across multiple VMs. The proper time to use non-random instance IDs is when the ID must come from an external source, or when you're implementing the [singleton orchestrator](durable-functions-singletons.md) pattern.

The following code is an example function that starts a new orchestration instance:

### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorldManualStart")]
public static async Task Run(
    [ManualTrigger] string input,
    [DurableClient] IDurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
}
```

> [!NOTE]
> The previous C# code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `OrchestrationClient` attribute instead of the `DurableClient` attribute, and you must use the `DurableOrchestrationClient` parameter type instead of `IDurableOrchestrationClient`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

### <a name="javascript"></a>JavaScript

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

You can also start an instance directly by using the [Azure Functions Core Tools](../functions-run-local.md) `durable start-new` command. Ele usa os seguintes parâmetros:

* **`function-name` (required)** : Name of the function to start.
* **`input` (optional)** : Input to the function, either inline or through a JSON file. For files, add a prefix to the path to the file with `@`, such as `@path/to/file.json`.
* **`id` (optional)** : a ID da instância de orquestração. If you don't specify this parameter, the command uses a random GUID.
* **`connection-string-setting` (opcional)** : nome da configuração do aplicativo que contém a cadeia de caracteres de conexão de armazenamento para usar. O padrão é AzureWebJobsStorage.
* **`task-hub-name` (optional)** : Name of the Durable Functions task hub to use. O padrão é DurableFunctionsHub. You can also set this in [host.json](durable-functions-bindings.md#host-json) by using durableTask:HubName.

> [!NOTE]
> Core Tools commands assume you are running them from the root directory of a function app. If you explicitly provide the `connection-string-setting` and `task-hub-name` parameters, you can run the commands from any directory. Although you can run these commands without a function app host running, you might find that you can't observe some effects unless the host is running. For example, the `start-new` command enqueues a start message into the target task hub, but the orchestration doesn't actually run unless there is a function app host process running that can process the message.

The following command starts the function named HelloWorld, and passes the contents of the file `counter-data.json` to it:

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>Query instances

As part of your effort to manage your orchestrations, you'll most likely need to gather information about the status of an orchestration instance (for example, whether it has completed normally or failed).

The `GetStatusAsync` (.NET) or the `getStatus` (JavaScript) method on the [orchestration client binding](durable-functions-bindings.md#orchestration-client) queries the status of an orchestration instance.

Ele usa um `instanceId` (obrigatório), `showHistory` (opcional) e `showHistoryOutput` (opcional) e `showInput` (opcional) como parâmetros.

* **`showHistory`** : If set to `true`, the response contains the execution history.
* **`showHistoryOutput`** : If set to `true`, the execution history contains activity outputs.
* **`showInput`** : If set to `false`, the response won't contain the input of the function. O valor padrão é `true`.

O método retorna um objeto com as seguintes propriedades:

* **Name**: o nome da função de orquestrador.
* **InstanceId**: a ID da instância de orquestração (deve ser o mesmo que a entrada `instanceId`).
* **CreatedTime**: a hora em que a função de orquestrador começou a ser executada.
* **LastUpdatedTime**: a hora em que a orquestração passou por uma verificação pontual pela última vez.
* **Input**: a entrada da função como um valor JSON. This field isn't populated if `showInput` is false.
* **CustomStatus**: status de orquestração personalizado no formato JSON.
* **Output**: a saída da função como um valor JSON (se a função tiver sido concluída). If the orchestrator function failed, this property includes the failure details. If the orchestrator function was terminated, this property includes the reason for the termination (if any).
* **RuntimeStatus**: um dos seguintes valores:
  * **Pendente**: A instância foi agendada, mas ainda não foi iniciado em execução.
  * **Running**: a instância começou a ser executada.
  * **Completed**: a instância foi concluída normalmente.
  * **ContinuedAsNew**: a instância reiniciou a si mesma com um novo histórico. This state is a transient state.
  * **Failed**: a instância falhou com um erro.
  * **Terminated**: a instância foi interrompida abruptamente.
* **Histórico**: O histórico de execução de orquestração. Este campo é preenchido somente se `showHistory` é definido como `true`.

This method returns `null` (.NET) or `undefined` (JavaScript) if the instance doesn't exist.

### <a name="c"></a>C#

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    DurableOrchestrationStatus status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

> [!NOTE]
> The previous C# code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `OrchestrationClient` attribute instead of the `DurableClient` attribute, and you must use the `DurableOrchestrationClient` parameter type instead of `IDurableOrchestrationClient`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

### <a name="javascript-functions-2x-only"></a>JavaScript (somente Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
}
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

It's also possible to get the status of an orchestration instance directly, by using the [Azure Functions Core Tools](../functions-run-local.md) `durable get-runtime-status` command. Ele usa os seguintes parâmetros:

* **`id` (required)** : ID of the orchestration instance.
* **`show-input` (optional)** : If set to `true`, the response contains the input of the function. O valor padrão é `false`.
* **`show-output` (optional)** : If set to `true`, the response contains the output of the function. O valor padrão é `false`.
* **`connection-string-setting` (opcional)** : nome da configuração do aplicativo que contém a cadeia de caracteres de conexão de armazenamento para usar. O padrão é `AzureWebJobsStorage`.
* **`task-hub-name` (optional)** : Name of the Durable Functions task hub to use. O padrão é `DurableFunctionsHub`. It can also be set in [host.json](durable-functions-bindings.md#host-json), by using durableTask:HubName.

The following command retrieves the status (including input and output) of an instance with an orchestration instance ID of 0ab8c55a66644d68a3a8b220b12d209c. It assumes that you are running the `func` command from the root directory of the function app:

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

You can use the `durable get-history` command to retrieve the history of an orchestration instance. Ele usa os seguintes parâmetros:

* **`id` (required)** : ID of the orchestration instance.
* **`connection-string-setting` (opcional)** : nome da configuração do aplicativo que contém a cadeia de caracteres de conexão de armazenamento para usar. O padrão é `AzureWebJobsStorage`.
* **`task-hub-name` (optional)** : Name of the Durable Functions task hub to use. O padrão é `DurableFunctionsHub`. It can also be set in host.json, by using durableTask:HubName.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>Query all instances

Rather than query one instance in your orchestration at a time, you might find it more efficient to query all of them at once.

É possível usar o método `GetStatusAsync` (.NET) ou `getStatusAll` (JavaScript) para consultar os status de todas as instâncias de orquestração. In .NET, you can pass a `CancellationToken` object in case you want to cancel it. O método retorna objetos com as mesmas propriedades que o método `GetStatusAsync` com parâmetros.

### <a name="c"></a>C#

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    IList<DurableOrchestrationStatus> instances = await client.GetStatusAsync(); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

> [!NOTE]
> The previous C# code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `OrchestrationClient` attribute instead of the `DurableClient` attribute, and you must use the `DurableOrchestrationClient` parameter type instead of `IDurableOrchestrationClient`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

### <a name="javascript-functions-2x-only"></a>JavaScript (somente Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instances = await client.getStatusAll();
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

It's also possible to query instances directly, by using the [Azure Functions Core Tools](../functions-run-local.md) `durable get-instances` command. Ele usa os seguintes parâmetros:

* **`top` (opcional)** : este comando dá suporte à paginação. Esse parâmetro corresponde ao número de instâncias recuperadas por solicitação. O padrão é 10.
* **`continuation-token` (optional)** : A token to indicate which page or section of instances to retrieve. Cada `get-instances` execução retorna um token para o próximo conjunto de instâncias.
* **`connection-string-setting` (opcional)** : nome da configuração do aplicativo que contém a cadeia de caracteres de conexão de armazenamento para usar. O padrão é `AzureWebJobsStorage`.
* **`task-hub-name` (optional)** : Name of the Durable Functions task hub to use. O padrão é `DurableFunctionsHub`. It can also be set in [host.json](durable-functions-bindings.md#host-json), by using durableTask:HubName.

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>Query instances with filters

What if you don't really need all the information that a standard instance query can provide? For example, what if you're just looking for the orchestration creation time, or the orchestration runtime status? You can narrow your query by applying filters.

Use the `GetStatusAsync` (.NET) or `getStatusBy` (JavaScript) method to get a list of orchestration instances that match a set of predefined filters.

### <a name="c"></a>C#

```csharp
[FunctionName("QueryStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    var runtimeStatus = new List<OrchestrationRuntimeStatus> {
        OrchestrationRuntimeStatus.Completed,
        OrchestrationRuntimeStatus.Running
    };
    IList<DurableOrchestrationStatus> instances = await starter.GetStatusAsync(
        new DateTime(2018, 3, 10, 10, 1, 0),
        new DateTime(2018, 3, 10, 10, 23, 59),
        runtimeStatus
    ); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

> [!NOTE]
> The previous C# code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `OrchestrationClient` attribute instead of the `DurableClient` attribute, and you must use the `DurableOrchestrationClient` parameter type instead of `IDurableOrchestrationClient`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

### <a name="javascript-functions-2x-only"></a>JavaScript (somente Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const runtimeStatus = [
        df.OrchestrationRuntimeStatus.Completed,
        df.OrchestrationRuntimeStatus.Running,
    ];
    const instances = await client.getStatusBy(
        new Date(2018, 3, 10, 10, 1, 0),
        new Date(2018, 3, 10, 10, 23, 59),
        runtimeStatus
    );
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

In the Azure Functions Core Tools, you can also use the `durable get-instances` command with filters. In addition to the aforementioned `top`, `continuation-token`, `connection-string-setting`, and `task-hub-name` parameters, you can use three filter parameters (`created-after`, `created-before`, and `runtime-status`).

* **`created-after` (opcional)** : recuperar as instâncias criadas após essa data/hora (UTC). Datetimes formato ISO 8601 aceito.
* **`created-before` (opcional)** : recuperar as instâncias criadas antes dessa hora/data (UTC). Datetimes formato ISO 8601 aceito.
* **`runtime-status` (optional)** : Retrieve the instances with a particular status (for example, running or completed). Pode fornecer o status de vários (separado por espaço).
* **`top` (opcional)** : número de instâncias recuperadas por solicitação. O padrão é 10.
* **`continuation-token` (optional)** : A token to indicate which page or section of instances to retrieve. Cada `get-instances` execução retorna um token para o próximo conjunto de instâncias.
* **`connection-string-setting` (opcional)** : nome da configuração do aplicativo que contém a cadeia de caracteres de conexão de armazenamento para usar. O padrão é `AzureWebJobsStorage`.
* **`task-hub-name` (optional)** : Name of the Durable Functions task hub to use. O padrão é `DurableFunctionsHub`. It can also be set in [host.json](durable-functions-bindings.md#host-json), by using durableTask:HubName.

If you don't provide any filters (`created-after`, `created-before`, or `runtime-status`), the command simply retrieves `top` instances, with no regard to runtime status or creation time.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>Terminate instances

If you have an orchestration instance that is taking too long to run, or you just need to stop it before it completes for any reason, you have the option to terminate it.

You can use the `TerminateAsync` (.NET) or the `terminate` (JavaScript) method of the [orchestration client binding](durable-functions-bindings.md#orchestration-client) to terminate instances. The two parameters are an `instanceId` and a `reason` string, which are written to logs and to the instance status. A terminated instance stops running as soon as it reaches the next `await` (.NET) or `yield` (JavaScript) point, or it terminates immediately if it's already on an `await` or `yield`.

### <a name="c"></a>C#

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

> [!NOTE]
> The previous C# code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `OrchestrationClient` attribute instead of the `DurableClient` attribute, and you must use the `DurableOrchestrationClient` parameter type instead of `IDurableOrchestrationClient`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

### <a name="javascript-functions-2x-only"></a>JavaScript (somente Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

> [!NOTE]
> Instance termination doesn't currently propagate. Activity functions and sub-orchestrations run to completion, regardless of whether you've terminated the orchestration instance that called them.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

You can also terminate an orchestration instance directly, by using the [Azure Functions Core Tools](../functions-run-local.md) `durable terminate` command. Ele usa os seguintes parâmetros:

* **`id` (required)** : ID of the orchestration instance to terminate.
* **`reason` (optional)** : Reason for termination.
* **`connection-string-setting` (opcional)** : nome da configuração do aplicativo que contém a cadeia de caracteres de conexão de armazenamento para usar. O padrão é `AzureWebJobsStorage`.
* **`task-hub-name` (optional)** : Name of the Durable Functions task hub to use. O padrão é `DurableFunctionsHub`. It can also be set in [host.json](durable-functions-bindings.md#host-json), by using durableTask:HubName.

The following command terminates an orchestration instance with an ID of 0ab8c55a66644d68a3a8b220b12d209c:

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>Send events to instances

In some scenarios, it's important for your orchestrator functions to be able to wait and listen for external events. This includes [monitor functions](durable-functions-overview.md#monitoring) and functions that are waiting for [human interaction](durable-functions-overview.md#human).

Send event notifications to running instances by using the `RaiseEventAsync` (.NET) method or the `raiseEvent` (JavaScript) method of the [orchestration client binding](durable-functions-bindings.md#orchestration-client). Instances that can handle these events are those that are awaiting a call to `WaitForExternalEvent` (.NET) or yielding to a `waitForExternalEvent` (JavaScript) call.

The parameters to `RaiseEventAsync` (.NET) and `raiseEvent` (JavaScript) are as follows:

* **InstanceId**: a ID exclusiva da instância.
* **EventName**: o nome do evento a ser enviado.
* **EventData**: uma carga serializável em JSON para enviar para a instância.

### <a name="c"></a>C#

```csharp
[FunctionName("RaiseEvent")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

> [!NOTE]
> The previous C# code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `OrchestrationClient` attribute instead of the `DurableClient` attribute, and you must use the `DurableOrchestrationClient` parameter type instead of `IDurableOrchestrationClient`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

### <a name="javascript-functions-2x-only"></a>JavaScript (somente Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

> [!NOTE]
> If there is no orchestration instance with the specified instance ID, the event message is discarded. If an instance exists but it is not yet waiting for the event, the event will be stored in the instance state until it is ready to be received and processed.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

You can also raise an event to an orchestration instance directly, by using the [Azure Functions Core Tools](../functions-run-local.md) `durable raise-event` command. Ele usa os seguintes parâmetros:

* **`id` (required)** : ID of the orchestration instance.
* **`event-name`** : Name of the event to raise.
* **`event-data` (opcional)** : dados a serem enviados para a instância de orquestração. This can be the path to a JSON file, or you can provide the data directly on the command line.
* **`connection-string-setting` (opcional)** : nome da configuração do aplicativo que contém a cadeia de caracteres de conexão de armazenamento para usar. O padrão é `AzureWebJobsStorage`.
* **`task-hub-name` (optional)** : Name of the Durable Functions task hub to use. O padrão é `DurableFunctionsHub`. It can also be set in [host.json](durable-functions-bindings.md#host-json), by using durableTask:HubName.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Aguardar a conclusão da orquestração

In long-running orchestrations, you may want to wait and get the results of an orchestration. In these cases, it's also useful to be able to define a timeout period on the orchestration. If the timeout is exceeded, the state of the orchestration should be returned instead of the results.

The `WaitForCompletionOrCreateCheckStatusResponseAsync` (.NET) or the `waitForCompletionOrCreateCheckStatusResponse` (JavaScript) method can be used to get the actual output from an orchestration instance synchronously. By default, these methods use a default value of 10 seconds for `timeout`, and 1 second for `retryInterval`.  

Veja um exemplo de função de gatilho HTTP que demonstra como usar essa API:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

Call the function with the following line. Use 2 seconds for the timeout and 0.5 seconds for the retry interval:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

Dependendo do tempo necessário para obter a resposta da instância de orquestração, há dois casos:

* The orchestration instances complete within the defined timeout (in this case 2 seconds), and the response is the actual orchestration instance output, delivered synchronously:

    ```http
        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2018 06:14:29 GMT
        Transfer-Encoding: chunked

        [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ]
    ```

* The orchestration instances can't complete within the defined timeout, and the response is the default one described in [HTTP API URL discovery](durable-functions-http-api.md):

    ```http
        HTTP/1.1 202 Accepted
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2018 06:13:51 GMT
        Location: http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}
        Retry-After: 10
        Transfer-Encoding: chunked

        {
            "id": "d3b72dddefce4e758d92f4d411567177",
            "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177/raiseEvent/{eventName}?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
        }
    ```

> [!NOTE]
> The format of the webhook URLs might differ, depending on which version of the Azure Functions host you are running. O exemplo acima refere-se ao host do Azure Functions 2.0.

## <a name="retrieve-http-management-webhook-urls"></a>Retrieve HTTP management webhook URLs

You can use an external system to monitor or to raise events to an orchestration. External systems can communicate with Durable Functions through the webhook URLs that are part of the default response described in [HTTP API URL discovery](durable-functions-http-features.md#http-api-url-discovery). The webhook URLs can alternatively be accessed programmatically using the [orchestration client binding](durable-functions-bindings.md#orchestration-client). The `CreateHttpManagementPayload` (.NET) or the `createHttpManagementPayload` (JavaScript) methods can be used to get a serializable object that contains these webhook URLs.

The `CreateHttpManagementPayload` (.NET) and `createHttpManagementPayload` (JavaScript) methods have one parameter:

* **instanceId**: a ID exclusiva da instância.

The methods return an object with the following string properties:

* **Id**: a ID da instância da orquestração (deve ser a mesma da entrada `InstanceId`).
* **StatusQueryGetUri**: a URL de status da instância de orquestração.
* **SendEventPostUri**: a URL "raise event" da instância de orquestração.
* **TerminatePostUri**: a URL "terminate" da instância de orquestração.
* **PurgeHistoryDeleteUri**: The "purge history" URL of the orchestration instance.

Functions can send instances of these objects to external systems to monitor or raise events on the corresponding orchestrations, as shown in the following examples:

### <a name="c"></a>C#

```csharp
[FunctionName("SendInstanceInfo")]
public static void SendInstanceInfo(
    [ActivityTrigger] IDurableActivityContext ctx,
    [DurableClient] IDurableOrchestrationClient client,
    [DocumentDB(
        databaseName: "MonitorDB",
        collectionName: "HttpManagementPayloads",
        ConnectionStringSetting = "CosmosDBConnection")]out dynamic document)
{
    HttpManagementPayload payload = client.CreateHttpManagementPayload(ctx.InstanceId);

    // send the payload to Cosmos DB
    document = new { Payload = payload, id = ctx.InstanceId };
}
```

> [!NOTE]
> The previous C# code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableActivityContext` instead of `IDurableActivityContext`, you must use the `OrchestrationClient` attribute instead of the `DurableClient` attribute, and you must use the `DurableOrchestrationClient` parameter type instead of `IDurableOrchestrationClient`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

### <a name="javascript-functions-2x-only"></a>JavaScript (somente Functions 2.x)

```javascript
const df = require("durable-functions");

modules.exports = async function(context, ctx) {
    const client = df.getClient(context);

    const payload = client.createHttpManagementPayload(ctx.instanceId);

    // send the payload to Cosmos DB
    context.bindings.document = JSON.stringify({
        id: ctx.instanceId,
        payload,
    });
};
```

## <a name="rewind-instances-preview"></a>Rewind instances (preview)

If you have an orchestration failure for an unexpected reason, you can *rewind* the instance to a previously healthy state by using an API built for that purpose.

> [!NOTE]
> Essa API não pretende ser uma substituição para as políticas de repetição e de tratamento de erros apropriadas. Em vez disso, destina-se a ser usada apenas em casos em que as instâncias de orquestração falhem por motivos inesperados. For more information on error handling and retry policies, see the [Error handling](durable-functions-error-handling.md) article.

Use the `RewindAsync` (.NET) or `rewind` (JavaScript) method of the [orchestration client binding](durable-functions-bindings.md#orchestration-client) to put the orchestration back into the *Running* state. This method will also rerun the activity or sub-orchestration execution failures that caused the orchestration failure.

For example, let's say you have a workflow involving a series of [human approvals](durable-functions-overview.md#human). Suppose there are a series of activity functions that notify someone that their approval is needed, and wait out the real-time response. After all of the approval activities have received responses or timed out, suppose that another activity fails due to an application misconfiguration, such as an invalid database connection string. O resultado é uma falha de orquestração profundamente no fluxo de trabalho. With the `RewindAsync` (.NET) or `rewind` (JavaScript) API, an application administrator can fix the configuration error, and rewind the failed orchestration back to the state immediately before the failure. None of the human-interaction steps need to be re-approved, and the orchestration can now complete successfully.

> [!NOTE]
> The *rewind* feature doesn't support rewinding orchestration instances that use durable timers.

### <a name="c"></a>C#

```csharp
[FunctionName("RewindInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "Orchestrator failed and needs to be revived.";
    return client.RewindAsync(instanceId, reason);
}
```

> [!NOTE]
> The previous C# code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `OrchestrationClient` attribute instead of the `DurableClient` attribute, and you must use the `DurableOrchestrationClient` parameter type instead of `IDurableOrchestrationClient`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

### <a name="javascript-functions-2x-only"></a>JavaScript (somente Functions 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

You can also rewind an orchestration instance directly by using the [Azure Functions Core Tools](../functions-run-local.md) `durable rewind` command. Ele usa os seguintes parâmetros:

* **`id` (required)** : ID of the orchestration instance.
* **`reason` (optional)** : Reason for rewinding the orchestration instance.
* **`connection-string-setting` (opcional)** : nome da configuração do aplicativo que contém a cadeia de caracteres de conexão de armazenamento para usar. O padrão é `AzureWebJobsStorage`.
* **`task-hub-name` (optional)** : Name of the Durable Functions task hub to use. By default, the task hub name in the [host.json](durable-functions-bindings.md#host-json) file is used.

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Purge instance history

To remove all the data associated with an orchestration, you can purge the instance history. For example, you might want to delete any Azure Table rows and large message blobs associated with a completed instance. To do so, use the `PurgeInstanceHistoryAsync` (.NET) or `purgeInstanceHistory` (JavaScript) method of the [orchestration client binding](durable-functions-bindings.md#orchestration-client).

This method has two overloads. The first overload purges history by the ID of the orchestration instance:

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    return client.purgeInstanceHistory(instanceId);
};
```

The next example shows a timer-triggered function that purges the history for all orchestration instances that completed after the specified time interval. In this case, it removes data for all instances completed 30 or more days ago. It's scheduled to run once per day, at 12 AM:

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [TimerTrigger("0 0 12 * * *")]TimerInfo myTimer)
{
    return client.PurgeInstanceHistoryAsync(
        DateTime.MinValue,
        DateTime.UtcNow.AddDays(-30),  
        new List<OrchestrationStatus>
        {
            OrchestrationStatus.Completed
        });
}
```

> [!NOTE]
> The previous C# code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `OrchestrationClient` attribute instead of the `DurableClient` attribute, and you must use the `DurableOrchestrationClient` parameter type instead of `IDurableOrchestrationClient`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

**JavaScript** The `purgeInstanceHistoryBy` method can be used to conditionally purge instance history for multiple instances.

> [!NOTE]
> For the purge history operation to succeed, the runtime status of the target instance must be **Completed**, **Terminated**, or **Failed**.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

You can purge an orchestration instance's history by using the [Azure Functions Core Tools](../functions-run-local.md) `durable purge-history` command. Similar to the second C# example in the preceding section, it purges the history for all orchestration instances created during a specified time interval. You can further filter purged instances by runtime status. O comando tem vários parâmetros:

* **`created-after` (opcional)** : limpar as instâncias criadas após essa data/hora (UTC). Datetimes formato ISO 8601 aceito.
* **`created-before` (opcional)** : limpar as instâncias criadas depois dessa data/hora (UTC). Datetimes formato ISO 8601 aceito.
* **`runtime-status` (optional)** : Purge the history of instances with a particular status (for example, running or completed). Pode fornecer o status de vários (separado por espaço).
* **`connection-string-setting` (opcional)** : nome da configuração do aplicativo que contém a cadeia de caracteres de conexão de armazenamento para usar. O padrão é `AzureWebJobsStorage`.
* **`task-hub-name` (optional)** : Name of the Durable Functions task hub to use. By default, the task hub name in the [host.json](durable-functions-bindings.md#host-json) file is used.

The following command deletes the history of all failed instances created before November 14, 2018 at 7:35 PM (UTC).

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>Delete a task hub

Using the [Azure Functions Core Tools](../functions-run-local.md) `durable delete-task-hub` command, you can delete all storage artifacts associated with a particular task hub, including Azure storage tables, queues, and blobs. O comando tem dois parâmetros:

* **`connection-string-setting` (opcional)** : nome da configuração do aplicativo que contém a cadeia de caracteres de conexão de armazenamento para usar. O padrão é `AzureWebJobsStorage`.
* **`task-hub-name` (optional)** : Name of the Durable Functions task hub to use. By default, the task hub name in the [host.json](durable-functions-bindings.md#host-json) file is used.

The following command deletes all Azure storage data associated with the `UserTest` task hub.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Saiba como lidar com o controle de versão](durable-functions-versioning.md)

> [!div class="nextstepaction"]
> [Built-in HTTP API reference for instance management](durable-functions-http-api.md)