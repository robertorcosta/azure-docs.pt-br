---
title: Gerenciar instâncias nas Funções Duráveis – Azure
description: Saiba como gerenciar instâncias na extensão de Funções Duráveis do Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 1837d342c4476633ee33a8579abe7389ac9bbddf
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476819"
---
# <a name="manage-instances-in-durable-functions-in-azure"></a>Gerenciar instâncias em Durable Functions no Azure

Se você estiver usando a extensão [Funções Duráveis](durable-functions-overview.md) para Funções Azure, ou quiser começar a fazê-lo, certifique-se de que você está recebendo o melhor uso fora dele. Você pode otimizar suas instâncias de orquestração de funções duráveis, aprendendo mais sobre como gerenciá-las. Este artigo mostra os detalhes de cada operação de gerenciamento de instância.

Você pode iniciar e encerrar instâncias, por exemplo, e pode consultar instâncias, incluindo a capacidade de consultar todas as instâncias e instâncias de consulta com filtros. Além disso, você pode enviar eventos para instâncias, esperar a conclusão da orquestração e recuperar URLs de webhook de gerenciamento HTTP. Este artigo abrange outras operações de gerenciamento, também, incluindo rebobinar instâncias, limpar o histórico de instâncias e excluir um hub de tarefas.

Em Funções Duráveis, você tem opções de como deseja implementar cada uma dessas operações de gerenciamento. Este artigo fornece exemplos que usam as [Ferramentas Principais de Funções do Azure](../functions-run-local.md) para o .NET (C#) e o JavaScript.

## <a name="start-instances"></a>Iniciar instâncias

É importante ser capaz de iniciar uma instância de orquestração. Isso é comumente feito quando você está usando uma vinculação de funções duráveis no gatilho de outra função.

O `StartNewAsync` método (.NET) ou `startNew` (JavaScript) na vinculação do cliente de [orquestração](durable-functions-bindings.md#orchestration-client) inicia uma nova instância. Internamente, este método enfileira uma mensagem na fila de controle, que então aciona o início de uma função com o nome especificado que usa a vinculação do [gatilho de orquestração](durable-functions-bindings.md#orchestration-trigger).

Essa operação assíncrona é concluída quando o processo de orquestração é agendado com êxito.

Os parâmetros para iniciar uma nova instância de orquestração são os seguintes:

* **Name**: o nome da função de orquestrador a ser agendada.
* **Input**: Qualquer dado serializável em JSON, que deve ser passado como a entrada para a função de orquestrador.
* **InstanceId**: (opcional) a ID exclusiva da instância. Se você não especificar este parâmetro, o método usará um ID aleatório.

> [!TIP]
> Use um identificador aleatório para a ID de instância. IDs de instâncias aleatórias ajudam a garantir uma distribuição de carga igual quando você está dimensionando funções orquestradoras em várias VMs. O tempo adequado para usar IDs de instância não aleatória é quando o ID deve vir de uma fonte externa, ou quando você está implementando o padrão [orquestrador singleton.](durable-functions-singletons.md)

O código a seguir é uma função de exemplo que inicia uma nova instância de orquestração:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("HelloWorldQueueTrigger")]
public static async Task Run(
    [QueueTrigger("start-queue")] string input,
    [DurableClient] IDurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
}
```

> [!NOTE]
> O código C# anterior é para Funções Duráveis 2.x. Para funções duráveis 1.x, você `OrchestrationClient` `DurableClient` deve usar atributo `DurableOrchestrationClient` em vez do `IDurableOrchestrationClient`atributo, e você deve usar o tipo de parâmetro em vez de . Para obter mais informações sobre as diferenças entre as versões, consulte o artigo [de funções duráveis.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

<a name="javascript-function-json"></a>Salvo especificação em contrário, os exemplos nesta página usam o gatilho HTTP com a seguinte função.json.

**function.json**

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": ["post"]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

> [!NOTE]
> Este exemplo tem como alvo a versão 2.x de Funções Duráveis. Na versão 1.x, `orchestrationClient` `durableClient`use em vez de .

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Você também pode iniciar uma instância diretamente usando o comando [Azure Functions Core Tools.](../functions-run-local.md) `durable start-new` Ele usa os seguintes parâmetros:

* (obrigatório) : Nome da função para iniciar. ** `function-name` **
* (opcional) : Entrada na função, inline ou através de um arquivo JSON. ** `input` ** Para arquivos, adicione um prefixo ao `@`caminho `@path/to/file.json`para o arquivo com , como .
* (opcional) : ID da instância de orquestração. ** `id` ** Se você não especificar este parâmetro, o comando usará um GUID aleatório.
* (opcional) : Nome da configuração do aplicativo que contém a seqüência de conexão de armazenamento a ser usada. ** `connection-string-setting` ** O padrão é AzureWebJobsStorage.
* (opcional) : Nome do centro de tarefas Funções Duráveis para usar. ** `task-hub-name` ** O padrão é DurableFunctionsHub. Você também pode definir isso em [host.json](durable-functions-bindings.md#host-json) usando durableTask:HubName.

> [!NOTE]
> Os comandos do Core Tools assumem que você os está executando a partir do diretório raiz de um aplicativo de função. Se você fornecer `connection-string-setting` explicitamente `task-hub-name` os parâmetros e parâmetros, você pode executar os comandos de qualquer diretório. Embora você possa executar esses comandos sem um host de aplicativo de função em execução, você pode descobrir que você não pode observar alguns efeitos a menos que o host esteja sendo executado. Por exemplo, `start-new` o comando enfileira uma mensagem inicial no centro de tarefas de destino, mas a orquestração não é executada a menos que haja um processo de host de aplicativo de função em execução que possa processar a mensagem.

O comando a seguir inicia a função chamada HelloWorld e passa o conteúdo do arquivo `counter-data.json` para ele:

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="query-instances"></a>Instâncias de consulta

Como parte de seu esforço para gerenciar suas orquestrações, você provavelmente precisará coletar informações sobre o status de uma instância de orquestração (por exemplo, se ela foi concluída normalmente ou falhou).

O `GetStatusAsync` método (.NET) ou `getStatus` (JavaScript) no cliente de [orquestração](durable-functions-bindings.md#orchestration-client) consulta o status de uma instância de orquestração.

Ele usa um `instanceId` (obrigatório), `showHistory` (opcional) e `showHistoryOutput` (opcional) e `showInput` (opcional) como parâmetros.

* **`showHistory`**: Se `true`definido como , a resposta contém o histórico de execução.
* **`showHistoryOutput`**: Se `true`definido como , o histórico de execução contém saídas de atividade.
* **`showInput`**: Se `false`estiver definido, a resposta não conterá a entrada da função. O valor padrão é `true`.

O método retorna um objeto com as seguintes propriedades:

* **Name**: o nome da função de orquestrador.
* **InstanceId**: a ID da instância de orquestração (deve ser o mesmo que a entrada `instanceId`).
* **CreatedTime**: a hora em que a função de orquestrador começou a ser executada.
* **LastUpdatedTime**: a hora em que a orquestração passou por uma verificação pontual pela última vez.
* **Input**: a entrada da função como um valor JSON. Este campo não é `showInput` povoado se for falso.
* **CustomStatus**: status de orquestração personalizado no formato JSON.
* **Output**: a saída da função como um valor JSON (se a função tiver sido concluída). Se a função orquestrador falhar, esta propriedade incluirá os detalhes de falha. Se a função orquestradora foi encerrada, esta propriedade inclui o motivo da rescisão (se houver).
* **RuntimeStatus**: um dos seguintes valores:
  * **Pendente**: A instância foi agendada, mas ainda não foi iniciado em execução.
  * **Running**: a instância começou a ser executada.
  * **Completed**: a instância foi concluída normalmente.
  * **ContinuedAsNew**: a instância reiniciou a si mesma com um novo histórico. Este estado é um estado transitório.
  * **Failed**: a instância falhou com um erro.
  * **Terminated**: a instância foi interrompida abruptamente.
* **Histórico**: O histórico de execução de orquestração. Este campo é preenchido somente se `showHistory` é definido como `true`.

Este método `null` retorna (.NET) ou `undefined` (JavaScript) se a instância não existir.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("check-status-queue")] string instanceId)
{
    DurableOrchestrationStatus status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

> [!NOTE]
> O código C# anterior é para Funções Duráveis 2.x. Para funções duráveis 1.x, você `OrchestrationClient` `DurableClient` deve usar atributo `DurableOrchestrationClient` em vez do `IDurableOrchestrationClient`atributo, e você deve usar o tipo de parâmetro em vez de . Para obter mais informações sobre as diferenças entre as versões, consulte o artigo [de funções duráveis.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
}
```

Consulte [Iniciar instâncias](#javascript-function-json) para a configuração function.json.

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Também é possível obter o status de uma instância de orquestração diretamente, usando o comando [Azure Functions Core Tools.](../functions-run-local.md) `durable get-runtime-status` Ele usa os seguintes parâmetros:

* (obrigatório) : ID da instância de orquestração. ** `id` **
* (opcional) : Se `true`definido para , a resposta contém a entrada da função. ** `show-input` ** O valor padrão é `false`.
* (opcional) : Se `true`definido para , a resposta contém a saída da função. ** `show-output` ** O valor padrão é `false`.
* (opcional) : Nome da configuração do aplicativo que contém a seqüência de conexão de armazenamento a ser usada. ** `connection-string-setting` ** O padrão é `AzureWebJobsStorage`.
* (opcional) : Nome do centro de tarefas Funções Duráveis para usar. ** `task-hub-name` ** O padrão é `DurableFunctionsHub`. Ele também pode ser definido em [host.json,](durable-functions-bindings.md#host-json)usando durableTask:HubName.

O comando a seguir recupera o status (incluindo entrada e saída) de uma instância com um ID de ocorrência de orquestração de 0ab8c55a66644d68a3a8b220b12d209c. Ele assume que você `func` está executando o comando a partir do diretório raiz do aplicativo de função:

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

Você pode `durable get-history` usar o comando para recuperar o histórico de uma instância de orquestração. Ele usa os seguintes parâmetros:

* (obrigatório) : ID da instância de orquestração. ** `id` **
* (opcional) : Nome da configuração do aplicativo que contém a seqüência de conexão de armazenamento a ser usada. ** `connection-string-setting` ** O padrão é `AzureWebJobsStorage`.
* (opcional) : Nome do centro de tarefas Funções Duráveis para usar. ** `task-hub-name` ** O padrão é `DurableFunctionsHub`. Ele também pode ser definido em host.json, usando durableTask:HubName.

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="query-all-instances"></a>Consulta todas as instâncias

Em vez de consultar uma instância em sua orquestração de cada vez, você pode achar mais eficiente consultar todos eles de uma vez.

É possível usar o método `GetStatusAsync` (.NET) ou `getStatusAll` (JavaScript) para consultar os status de todas as instâncias de orquestração. Em .NET, você `CancellationToken` pode passar um objeto no caso de você querer cancelá-lo. O método retorna objetos com as mesmas propriedades que o método `GetStatusAsync` com parâmetros.

# <a name="c"></a>[C #](#tab/csharp)

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
> O código C# anterior é para Funções Duráveis 2.x. Para funções duráveis 1.x, você `OrchestrationClient` `DurableClient` deve usar atributo `DurableOrchestrationClient` em vez do `IDurableOrchestrationClient`atributo, e você deve usar o tipo de parâmetro em vez de . Para obter mais informações sobre as diferenças entre as versões, consulte o artigo [de funções duráveis.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

Consulte [Iniciar instâncias](#javascript-function-json) para a configuração function.json.

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Também é possível consultar instâncias diretamente, usando o comando [Azure Functions Core Tools.](../functions-run-local.md) `durable get-instances` Ele usa os seguintes parâmetros:

* (opcional) : Este comando suporta a paginação. ** `top` ** Esse parâmetro corresponde ao número de instâncias recuperadas por solicitação. O padrão é 10.
* (opcional) : Um token para indicar qual página ou seção de instâncias recuperar. ** `continuation-token` ** Cada `get-instances` execução retorna um token para o próximo conjunto de instâncias.
* (opcional) : Nome da configuração do aplicativo que contém a seqüência de conexão de armazenamento a ser usada. ** `connection-string-setting` ** O padrão é `AzureWebJobsStorage`.
* (opcional) : Nome do centro de tarefas Funções Duráveis para usar. ** `task-hub-name` ** O padrão é `DurableFunctionsHub`. Ele também pode ser definido em [host.json,](durable-functions-bindings.md#host-json)usando durableTask:HubName.

```bash
func durable get-instances
```

## <a name="query-instances-with-filters"></a>Casos de consulta com filtros

E se você realmente não precisar de todas as informações que uma consulta de instância padrão pode fornecer? Por exemplo, e se você estiver apenas procurando o tempo de criação da orquestração, ou o status de tempo de execução da orquestração? Você pode restringir sua consulta aplicando filtros.

Use `GetStatusAsync` o método (.NET) ou `getStatusBy` (JavaScript) para obter uma lista de instâncias de orquestração que correspondem a um conjunto de filtros predefinidos.

# <a name="c"></a>[C #](#tab/csharp)

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
> O código C# anterior é para Funções Duráveis 2.x. Para funções duráveis 1.x, você `OrchestrationClient` `DurableClient` deve usar atributo `DurableOrchestrationClient` em vez do `IDurableOrchestrationClient`atributo, e você deve usar o tipo de parâmetro em vez de . Para obter mais informações sobre as diferenças entre as versões, consulte o artigo [de funções duráveis.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

Consulte [Iniciar instâncias](#javascript-function-json) para a configuração function.json.

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Nas ferramentas principais de funções do Azure, você também pode usar o `durable get-instances` comando com filtros. Além dos parâmetros acima `top` `continuation-token` `connection-string-setting`mencionados, `task-hub-name` e de três parâmetros`created-after` `created-before`de `runtime-status`filtro ( , e ).

* (opcional) : Recupere as instâncias criadas após esta data/hora (UTC). ** `created-after` ** Datetimes formato ISO 8601 aceito.
* (opcional) : Recupere as instâncias criadas antes desta data/hora (UTC). ** `created-before` ** Datetimes formato ISO 8601 aceito.
* (opcional) : Recupere as instâncias com um status específico (por exemplo, em execução ou concluída). ** `runtime-status` ** Pode fornecer o status de vários (separado por espaço).
* (opcional) : Número de instâncias recuperadas por solicitação. ** `top` ** O padrão é 10.
* (opcional) : Um token para indicar qual página ou seção de instâncias recuperar. ** `continuation-token` ** Cada `get-instances` execução retorna um token para o próximo conjunto de instâncias.
* (opcional) : Nome da configuração do aplicativo que contém a seqüência de conexão de armazenamento a ser usada. ** `connection-string-setting` ** O padrão é `AzureWebJobsStorage`.
* (opcional) : Nome do centro de tarefas Funções Duráveis para usar. ** `task-hub-name` ** O padrão é `DurableFunctionsHub`. Ele também pode ser definido em [host.json,](durable-functions-bindings.md#host-json)usando durableTask:HubName.

Se você não fornecer filtros`created-after` `created-before`( `runtime-status`ou ), o `top` comando simplesmente recupera instâncias, sem levar em conta o status de tempo de execução ou o tempo de criação.

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminate-instances"></a>Encerrar instâncias

Se você tem uma instância de orquestração que está demorando muito para ser executada, ou você só precisa pará-la antes que ela seja concluída por qualquer motivo, você tem a opção de terminá-la.

Você pode `TerminateAsync` usar o método `terminate` (.NET) ou (JavaScript) do cliente de [orquestração vinculado](durable-functions-bindings.md#orchestration-client) a instâncias de término. Os dois parâmetros são uma `instanceId` seqüência e uma `reason` seqüência, que são escritas para logs e para o status de instância.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("terminate-queue")] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

> [!NOTE]
> O código C# anterior é para Funções Duráveis 2.x. Para funções duráveis 1.x, você `OrchestrationClient` `DurableClient` deve usar atributo `DurableOrchestrationClient` em vez do `IDurableOrchestrationClient`atributo, e você deve usar o tipo de parâmetro em vez de . Para obter mais informações sobre as diferenças entre as versões, consulte o artigo [de funções duráveis.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

Consulte [Iniciar instâncias](#javascript-function-json) para a configuração function.json.

---

Uma instância terminada acabará `Terminated` por passar para o estado. No entanto, essa transição não acontecerá imediatamente. Em vez disso, a operação de término será enfileirada no centro de tarefas, juntamente com outras operações para esse caso. Você pode usar as APIs [de consulta](#query-instances) de instância para `Terminated` saber quando uma instância terminada realmente chegou ao estado.

> [!NOTE]
> A rescisão de instâncias não se propaga atualmente. Funções de atividade e suborquestrações correm até a conclusão, independentemente de você ter encerrado a instância de orquestração que as chamou.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Você também pode encerrar uma instância de orquestração diretamente, usando o comando [Azure Functions Core Tools.](../functions-run-local.md) `durable terminate` Ele usa os seguintes parâmetros:

* (necessário) : ID da instância de orquestração para terminar. ** `id` **
* (opcional) : Motivo para rescisão. ** `reason` **
* (opcional) : Nome da configuração do aplicativo que contém a seqüência de conexão de armazenamento a ser usada. ** `connection-string-setting` ** O padrão é `AzureWebJobsStorage`.
* (opcional) : Nome do centro de tarefas Funções Duráveis para usar. ** `task-hub-name` ** O padrão é `DurableFunctionsHub`. Ele também pode ser definido em [host.json,](durable-functions-bindings.md#host-json)usando durableTask:HubName.

O comando a seguir encerra uma instância de orquestração com um ID de 0ab8c55a6644d68a3a8b220b12d209c:

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="send-events-to-instances"></a>Enviar eventos para instâncias

Em alguns cenários, é importante que suas funções orquestradoras sejam capazes de esperar e ouvir eventos externos. Isso inclui [funções](durable-functions-overview.md#monitoring) e funções de monitor que aguardam a [interação humana.](durable-functions-overview.md#human)

Envie notificações de eventos `RaiseEventAsync` para instâncias em `raiseEvent` execução usando o método (.NET) ou o método (JavaScript) da [vinculação do cliente](durable-functions-bindings.md#orchestration-client)de orquestração . As instâncias que podem lidar com esses `WaitForExternalEvent` eventos são aquelas que aguardam `waitForExternalEvent` uma chamada para (.NET) ou cedendo a uma chamada (JavaScript).

Os parâmetros para `RaiseEventAsync` (.NET) e `raiseEvent` (JavaScript) são os seguintes:

* **InstanceId**: a ID exclusiva da instância.
* **EventName**: o nome do evento a ser enviado.
* **EventData**: uma carga serializável em JSON para enviar para a instância.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("RaiseEvent")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("event-queue")] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

> [!NOTE]
> O código C# anterior é para Funções Duráveis 2.x. Para funções duráveis 1.x, você `OrchestrationClient` `DurableClient` deve usar atributo `DurableOrchestrationClient` em vez do `IDurableOrchestrationClient`atributo, e você deve usar o tipo de parâmetro em vez de . Para obter mais informações sobre as diferenças entre as versões, consulte o artigo [de funções duráveis.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

Consulte [Iniciar instâncias](#javascript-function-json) para a configuração function.json.

---

> [!NOTE]
> Se não houver nenhuma instância de orquestração com a ID da instância especificada, a mensagem de evento é descartada. Se existir uma instância, mas ainda não estiver esperando o evento, o evento será armazenado no estado de ocorrência até que esteja pronto para ser recebido e processado.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Você também pode elevar um evento diretamente a uma instância de orquestração, usando o comando [Azure Functions Core Tools.](../functions-run-local.md) `durable raise-event` Ele usa os seguintes parâmetros:

* (obrigatório) : ID da instância de orquestração. ** `id` **
* **`event-name`**: Nome do evento para levantar.
* (opcional) : Dados para enviar à instância de orquestração. ** `event-data` ** Este pode ser o caminho para um arquivo JSON, ou você pode fornecer os dados diretamente na linha de comando.
* (opcional) : Nome da configuração do aplicativo que contém a seqüência de conexão de armazenamento a ser usada. ** `connection-string-setting` ** O padrão é `AzureWebJobsStorage`.
* (opcional) : Nome do centro de tarefas Funções Duráveis para usar. ** `task-hub-name` ** O padrão é `DurableFunctionsHub`. Ele também pode ser definido em [host.json,](durable-functions-bindings.md#host-json)usando durableTask:HubName.

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>Aguardar a conclusão da orquestração

Em orquestrações de longa duração, você pode querer esperar e obter os resultados de uma orquestração. Nesses casos, também é útil ser capaz de definir um período de tempo na orquestração. Se o tempo for excedido, o estado da orquestração deve ser devolvido em vez dos resultados.

O `WaitForCompletionOrCreateCheckStatusResponseAsync` método (.NET) ou `waitForCompletionOrCreateCheckStatusResponse` (JavaScript) pode ser usado para obter a saída real de uma instância de orquestração sincronizadamente. Por padrão, esses métodos usam um valor `timeout`padrão de `retryInterval`10 segundos para , e 1 segundo para .  

Veja um exemplo de função de gatilho HTTP que demonstra como usar essa API:

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

Consulte [Iniciar instâncias](#javascript-function-json) para a configuração function.json.

---

Ligue para a função com a seguinte linha. Use 2 segundos para o intervalo de tempo e 0,5 segundos para o intervalo de repetição:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

Dependendo do tempo necessário para obter a resposta da instância de orquestração, há dois casos:

* As instâncias de orquestração completam dentro do tempo definido (neste caso 2 segundos), e a resposta é a saída real da instância de orquestração, entregue sincronizadamente:

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

* As instâncias de orquestração não podem ser concluídas dentro do tempo definido, e a resposta é a padrão descrita na [descoberta de URL da API HTTP:](durable-functions-http-api.md)

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
> O formato dos URLs do webhook pode diferir, dependendo da versão do host Funções do Azure que você está executando. O exemplo acima refere-se ao host do Azure Functions 2.0.

## <a name="retrieve-http-management-webhook-urls"></a>Recuperar URLs de webhook de gerenciamento HTTP

Você pode usar um sistema externo para monitorar ou levantar eventos para uma orquestração. Os sistemas externos podem se comunicar com funções duráveis através dos URLs do webhook que fazem parte da resposta padrão descrita na [detecção de URL da API HTTP](durable-functions-http-features.md#http-api-url-discovery). Os URLs do webhook podem ser acessados de forma programática usando a vinculação do [cliente de orquestração](durable-functions-bindings.md#orchestration-client). Os `CreateHttpManagementPayload` métodos (.NET) ou `createHttpManagementPayload` JavaScript podem ser usados para obter um objeto serializável que contenha esses URLs webhook.

Os `CreateHttpManagementPayload` métodos (.NET) e `createHttpManagementPayload` (JavaScript) têm um parâmetro:

* **instanceId**: O ID único da instância.

Os métodos retornam um objeto com as seguintes propriedades de seqüência de string:

* **Id**: a ID da instância da orquestração (deve ser a mesma da entrada `InstanceId`).
* **StatusQueryGetUri**: a URL de status da instância de orquestração.
* **SendEventPostUri**: a URL "raise event" da instância de orquestração.
* **TerminatePostUri**: a URL "terminate" da instância de orquestração.
* **PurgaHistoryDeleteUri**: A URL "purgação" da instância de orquestração.

As funções podem enviar instâncias desses objetos para sistemas externos para monitorar ou levantar eventos nas orquestrações correspondentes, como mostrado nos exemplos a seguir:

# <a name="c"></a>[C #](#tab/csharp)

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
> O código C# anterior é para Funções Duráveis 2.x. Para funções duráveis 1.x, `DurableActivityContext` você deve usar em vez de `IDurableActivityContext`, você deve usar o atributo `OrchestrationClient` em vez do atributo, `DurableClient` e você deve usar o `DurableOrchestrationClient` tipo de parâmetro em vez de `IDurableOrchestrationClient`. Para obter mais informações sobre as diferenças entre as versões, consulte o artigo [de funções duráveis.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

Consulte [Iniciar instâncias](#javascript-function-json) para a configuração function.json.

---

## <a name="rewind-instances-preview"></a>Rebobinar instâncias (visualização)

Se você tiver uma falha de orquestração por uma razão inesperada, você pode *rebobinar* a instância para um estado anteriormente saudável usando uma API construída para esse fim.

> [!NOTE]
> Essa API não pretende ser uma substituição para as políticas de repetição e de tratamento de erros apropriadas. Em vez disso, destina-se a ser usada apenas em casos em que as instâncias de orquestração falhem por motivos inesperados. Para obter mais informações sobre políticas de manipulação e repetição de erros, consulte o artigo [de manipulação de erros.](durable-functions-error-handling.md)

Use `RewindAsync` o método (.NET) ou `rewind` (JavaScript) da vinculação do cliente de [orquestração](durable-functions-bindings.md#orchestration-client) para colocar a orquestração de volta no estado em *execução.* Este método também reexecutará as falhas de execução de atividade ou suborquestração que causaram a falha de orquestração.

Por exemplo, digamos que você tenha um fluxo de trabalho envolvendo uma série de [aprovações humanas.](durable-functions-overview.md#human) Suponha que haja uma série de funções de atividade que notifiquem alguém de que sua aprovação é necessária, e aguardem a resposta em tempo real. Depois de todas as atividades de aprovação terem recebido respostas ou cronometrados, suponha que outra atividade falhe devido a uma configuração errada do aplicativo, como uma seqüência de conexão de banco de dados inválida. O resultado é uma falha de orquestração profundamente no fluxo de trabalho. Com `RewindAsync` a API `rewind` (.NET) ou (JavaScript), um administrador de aplicativo pode corrigir o erro de configuração e rebobinar a orquestração com falha de volta ao estado imediatamente antes da falha. Nenhuma das etapas de interação humana precisa ser reaprovada, e a orquestração agora pode ser concluída com sucesso.

> [!NOTE]
> O recurso *de rebobinar* não suporta rebobinar instâncias de orquestração que usam temporizadores duráveis.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("RewindInstance")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("rewind-queue")] string instanceId)
{
    string reason = "Orchestrator failed and needs to be revived.";
    return client.RewindAsync(instanceId, reason);
}
```

> [!NOTE]
> O código C# anterior é para Funções Duráveis 2.x. Para funções duráveis 1.x, você `OrchestrationClient` `DurableClient` deve usar atributo `DurableOrchestrationClient` em vez do `IDurableOrchestrationClient`atributo, e você deve usar o tipo de parâmetro em vez de . Para obter mais informações sobre as diferenças entre as versões, consulte o artigo [de funções duráveis.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

Consulte [Iniciar instâncias](#javascript-function-json) para a configuração function.json.

---

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Você também pode rebobinar uma instância de orquestração diretamente usando o comando [Azure Functions Core Tools.](../functions-run-local.md) `durable rewind` Ele usa os seguintes parâmetros:

* (obrigatório) : ID da instância de orquestração. ** `id` **
* (opcional) : Razão para rebobinar a instância de orquestração. ** `reason` **
* (opcional) : Nome da configuração do aplicativo que contém a seqüência de conexão de armazenamento a ser usada. ** `connection-string-setting` ** O padrão é `AzureWebJobsStorage`.
* (opcional) : Nome do centro de tarefas Funções Duráveis para usar. ** `task-hub-name` ** Por padrão, o nome do hub de tarefas no arquivo [host.json](durable-functions-bindings.md#host-json) é usado.

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>Histórico de instâncias de expurgo

Para remover todos os dados associados a uma orquestração, você pode limpar o histórico de ocorrências. Por exemplo, você pode querer excluir quaisquer linhas de tabela do Azure e grandes bolhas de mensagem associadas a uma instância concluída. Para isso, use `PurgeInstanceHistoryAsync` o método (.NET) ou `purgeInstanceHistory` (JavaScript) da vinculação do cliente de [orquestração](durable-functions-bindings.md#orchestration-client).

Este método tem duas sobrecargas. A primeira sobrecarga expurga a história pelo ID da instância de orquestração:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [DurableClient] IDurableOrchestrationClient client,
    [QueueTrigger("purge-queue")] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    return client.purgeInstanceHistory(instanceId);
};
```

Consulte [Iniciar instâncias](#javascript-function-json) para a configuração function.json.

---

O exemplo seguinte mostra uma função acionada pelo temporizador que expurga o histórico de todas as instâncias de orquestração concluídas após o intervalo de tempo especificado. Neste caso, ele remove dados para todas as instâncias concluídas há 30 ou mais dias. Está programado para funcionar uma vez por dia, às 12:00:

# <a name="c"></a>[C #](#tab/csharp)

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
> O código C# anterior é para Funções Duráveis 2.x. Para funções duráveis 1.x, você `OrchestrationClient` `DurableClient` deve usar atributo `DurableOrchestrationClient` em vez do `IDurableOrchestrationClient`atributo, e você deve usar o tipo de parâmetro em vez de . Para obter mais informações sobre as diferenças entre as versões, consulte o artigo [de funções duráveis.](durable-functions-versions.md)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O `purgeInstanceHistoryBy` método pode ser usado para expurgar condicionalmente o histórico de instâncias para várias instâncias.

**function.json**

```json
{
  "bindings": [
    {
      "schedule": "0 0 12 * * *",
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

> [!NOTE]
> Este exemplo tem como alvo a versão 2.x de Funções Duráveis. Na versão 1.x, `orchestrationClient` `durableClient`use em vez de .

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function (context, myTimer) {
    const client = df.getClient(context);
    const createdTimeFrom = new Date(0);
    const createdTimeTo = new Date().setDate(today.getDate() - 30);
    const runtimeStatuses = [ df.OrchestrationRuntimeStatus.Completed ];
    return client.purgeInstanceHistoryBy(createdTimeFrom, createdTimeTo, runtimeStatuses);
};
```

---

> [!NOTE]
> Para que a operação de histórico de expurgo seja bem sucedida, o status de tempo de execução da instância de destino deve ser **concluído,** **encerrado**ou **falhado.**

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Você pode limpar o histórico de uma instância de orquestração usando o comando [Azure Functions Core Tools.](../functions-run-local.md) `durable purge-history` Semelhante ao segundo exemplo C# na seção anterior, ele expurga a história de todas as instâncias de orquestração criadas durante um intervalo de tempo especificado. Você pode filtrar ainda mais as instâncias purgadas pelo status de tempo de execução. O comando tem vários parâmetros:

* (opcional) : Expurgar o histórico de instâncias criadas após esta data/hora (UTC). ** `created-after` ** Datetimes formato ISO 8601 aceito.
* (opcional) : Expurgar o histórico de instâncias criadas antes desta data/hora (UTC). ** `created-before` ** Datetimes formato ISO 8601 aceito.
* (opcional) : Expurgar o histórico de instâncias com um status específico (por exemplo, em execução ou concluído). ** `runtime-status` ** Pode fornecer o status de vários (separado por espaço).
* (opcional) : Nome da configuração do aplicativo que contém a seqüência de conexão de armazenamento a ser usada. ** `connection-string-setting` ** O padrão é `AzureWebJobsStorage`.
* (opcional) : Nome do centro de tarefas Funções Duráveis para usar. ** `task-hub-name` ** Por padrão, o nome do hub de tarefas no arquivo [host.json](durable-functions-bindings.md#host-json) é usado.

O comando a seguir exclui o histórico de todas as instâncias com falha criadas antes de 14 de novembro de 2018 às 19h35 (UTC).

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="delete-a-task-hub"></a>Exclua um centro de tarefas

Usando o comando [Azure Functions Core Tools,](../functions-run-local.md) `durable delete-task-hub` você pode excluir todos os artefatos de armazenamento associados a um determinado hub de tarefas, incluindo tabelas de armazenamento do Azure, filas e blobs. O comando tem dois parâmetros:

* (opcional) : Nome da configuração do aplicativo que contém a seqüência de conexão de armazenamento a ser usada. ** `connection-string-setting` ** O padrão é `AzureWebJobsStorage`.
* (opcional) : Nome do centro de tarefas Funções Duráveis para usar. ** `task-hub-name` ** Por padrão, o nome do hub de tarefas no arquivo [host.json](durable-functions-bindings.md#host-json) é usado.

O comando a seguir exclui todos os `UserTest` dados de armazenamento do Azure associados ao centro de tarefas.

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba como lidar com o controle de versão](durable-functions-versioning.md)

> [!div class="nextstepaction"]
> [Referência de API HTTP incorporada para gerenciamento de exemplo](durable-functions-http-api.md)
