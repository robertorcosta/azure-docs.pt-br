---
title: Associações para Funções Duráveis – Azure
description: Como usar gatilhos e associações para a extensão de Durable Functions do Azure Functions.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 40b5f0f17cbb6867a6ef293a485d728141a012ef
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233035"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Associações para Funções Duráveis (Azure Functions)

A extensão [Funções Duráveis](durable-functions-overview.md) introduz duas novas associações de gatilho que controlam a execução de funções de orquestrador e atividade. Ela também introduz uma associação de saída que atua como um cliente para o runtime das Funções Duráveis.

## <a name="orchestration-trigger"></a>Orchestration trigger

The orchestration trigger enables you to author [durable orchestrator functions](durable-functions-types-features-overview.md#orchestrator-functions). Esse gatilho dá suporte a iniciar novas instâncias de função de orquestrador e a retomar instâncias de função de orquestrador existentes que estão "aguardando" uma tarefa.

Quando você usa as ferramentas do Visual Studio para o Azure Functions, o gatilho de orquestração é configurado usando o atributo .NET [OrchestrationTriggerAttribute](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.OrchestrationTriggerAttribute?view=azure-dotnet).

Quando você escreve funções de orquestrador em linguagens de script (por exemplo, script em JavaScript ou C#), o gatilho de orquestração é definido pelo seguinte objeto JSON na matriz `bindings` do arquivo *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration` is the name of the orchestration that clients must use when they want to start new instances of this orchestrator function. Essa propriedade é opcional. Se não for especificada, o nome da função será usado.

Internamente, essa associação de gatilho sonda uma série de filas na conta de armazenamento padrão do aplicativo de funções. Essas filas são detalhes da implementação interna da extensão e, por isso, elas não são configuradas explicitamente nas propriedades de associação.

### <a name="trigger-behavior"></a>Comportamento do gatilho

Veja algumas observações sobre o gatilho de orquestração:

* **Threading único** – um único thread dispatcher é usado para toda a execução de função de orquestrador em uma única instância de host. Por esse motivo, é importante garantir que o código da função de orquestrador seja eficiente e não execute nenhuma E/S. Também é importante garantir que esse thread não faça nenhum trabalho assíncrono, exceto ao aguardar tipos de tarefas específicas das Funções Duráveis.
* **Manipulação de mensagens suspeitas** – não há suporte para mensagens suspeitas nos acionadores de orquestração.
* **Visibilidade da mensagem** – as mensagens do gatilho de orquestração são removidas da fila e mantidas invisíveis por uma duração configurável. A visibilidade dessas mensagens é renovada automaticamente, desde que o aplicativo de funções esteja em execução e íntegro.
* **Valores retornados** – os valores retornados são serializados em JSON e são persistidos na tabela de histórico de orquestração no Armazenamento de Tabelas do Azure. Esse valores retornados podem ser consultados pela associação do cliente de orquestração, descrita posteriormente.

> [!WARNING]
> Funções de orquestrador nunca devem usar nenhuma associação de entrada ou saída que não seja a associação do gatilho de orquestração. Fazer isso tem o potencial de causar problemas com a extensão de Tarefa Durável, pois essas associações podem não cumprir as regras de E/S e de thread único. If you'd like to use other bindings, add them to an Activity function called from your Orchestrator function.

> [!WARNING]
> Funções de orquestrador JavaScript nunca devem ser declaradas como `async`.

### <a name="trigger-usage-net"></a>Uso de gatilho (.NET)

A associação de gatilho de orquestração dá suporte a entradas e saídas. Estas são algumas coisas que você precisa saber sobre a manipulação de entradas e saídas:

* **inputs** - .NET orchestration functions support only `DurableOrchestrationContext` as a parameter type. Não há suporte para entradas de desserialização diretamente na assinatura da função. Code must use the `GetInput<T>` (.NET) or `getInput` (JavaScript) method to fetch orchestrator function inputs. Essas entradas devem ser tipos serializáveis em JSON.
* **saídas** – gatilhos de orquestração dão suporte a valores de saída, bem como entradas. O valor retornado da função é usado para atribuir o valor de saída e deve ser serializável em JSON. Se uma função .NET retornar `Task` ou `void`, um valor `null` será salvo como a saída.

### <a name="trigger-sample"></a>Exemplo de gatilho

The following example code shows what the simplest "Hello World" orchestrator function might look like:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```
> [!NOTE]
> The previous code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions Versions](durable-functions-versions.md) article.

#### <a name="javascript-functions-20-only"></a>Javascript (somente funções 2.0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> The `context` object in JavaScript does not represent the DurableOrchestrationContext, but the [function context as a whole](../functions-reference-node.md#context-object). Você pode acessar os métodos de orquestração por meio da propriedade `df` do objeto `context`.

> [!NOTE]
> Os orquestradores JavaScript devem usar `return`. A biblioteca `durable-functions` é responsável por chamar o método `context.done`.

A maioria das funções de orquestrador chamam funções de atividade, sendo assim, este é um exemplo de "Olá, Mundo" que demonstra como chamar uma função de atividade:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

> [!NOTE]
> The previous code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableOrchestrationContext` instead of `IDurableOrchestrationContext`. For more information about the differences between versions, see the [Durable Functions versions](durable-functions-versions.md) article.

#### <a name="javascript-functions-20-only"></a>Javascript (somente funções 2.0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-trigger"></a>Activity trigger

The activity trigger enables you to author functions that are called by orchestrator functions, known as [activity functions](durable-functions-types-features-overview.md#activity-functions).

If you're using Visual Studio, the activity trigger is configured using the `ActivityTriggerAttribute` .NET attribute.

Se você estiver usando o VS Code ou o Portal do Azure para desenvolvimento, o gatilho de atividade será definido pelo seguinte objeto JSON na matriz `bindings` de *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity` é o nome da atividade. This value is the name that orchestrator functions use to invoke this activity function. Essa propriedade é opcional. Se não for especificada, o nome da função será usado.

Internamente, essa associação de gatilho sonda uma fila na conta de armazenamento padrão para o aplicativo de funções. Essa fila é um detalhe da implementação interna da extensão e, por isso, ela não é configurada explicitamente nas propriedades da associação.

### <a name="trigger-behavior"></a>Comportamento do gatilho

Veja algumas observações sobre o gatilho de atividade:

* **Threading** – diferente do gatilho de orquestração, os gatilhos de atividade não têm restrições quanto a threading ou E/S. Eles podem ser tratados como funções regulares.
* **Manipulação de mensagens suspeitas** – não há suporte para mensagens suspeitas nos gatilhos de atividade.
* **Visibilidade da mensagem** – as mensagens do gatilho de atividade são removidas da fila e mantidas invisíveis por uma duração configurável. A visibilidade dessas mensagens é renovada automaticamente, desde que o aplicativo de funções esteja em execução e íntegro.
* **Valores retornados** – os valores retornados são serializados em JSON e são persistidos na tabela de histórico de orquestração no Armazenamento de Tabelas do Azure.

> [!WARNING]
> O back-end de armazenamento das funções de atividade é um detalhe da implementação e o código do usuário não deve interagir diretamente com essas entidades de armazenamento.

### <a name="trigger-usage-net"></a>Uso de gatilho (.NET)

A associação de gatilho de atividade dá suporte a entradas e saídas, assim como no caso do gatilho de orquestração. Estas são algumas coisas que você precisa saber sobre a manipulação de entradas e saídas:

* **inputs** - .NET activity functions natively use `DurableActivityContext` as a parameter type. Como alternativa, uma função de atividade pode ser declarada com qualquer tipo de parâmetro que possa ser serializado em JSON. When you use `DurableActivityContext`, you can call `GetInput<T>` to fetch and deserialize the activity function input.
* **saídas** – Funções de atividade dão suporte a valores de saída, bem como entradas. O valor retornado da função é usado para atribuir o valor de saída e deve ser serializável em JSON. Se uma função .NET retornar `Task` ou `void`, um valor `null` será salvo como a saída.
* **metadados** – funções de atividade .NET podem ser associadas a um parâmetro `string instanceId` para obter a ID da instância da orquestração pai.

### <a name="trigger-sample"></a>Exemplo de gatilho

The following example code shows what a simple "Hello World" activity function might look like:

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] IDurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

> [!NOTE]
> The previous code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `DurableActivityContext` instead of `IDurableActivityContext`. For more information about the differences between versions, see the [Durable Functions Versions](durable-functions-versions.md) article.

O tipo de parâmetro padrão para a associação `ActivityTriggerAttribute` .NET é `IDurableActivityContext`. No entanto, os gatilhos de atividade .NET também dão suporte à associação direta com tipos serializáveis em JSON (incluindo tipos primitivos), de modo que a mesma função poderia ser simplificada da seguinte forma:

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-20-only"></a>Javascript (somente funções 2.0)

```javascript
module.exports = async function(context) {
    return `Hello ${context.bindings.name}!`;
};
```

Associações de JavaScript também podem ser passadas como parâmetros adicionais para que a mesma função possa ser simplificada da seguinte maneira:

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```


### <a name="using-input-and-output-bindings"></a>Using input and output bindings

You can use regular input and output bindings in addition to the activity trigger binding. For example, you can take the input to your activity binding, and send a message to an EventHub using the EventHub output binding:

```json
{
  "bindings": [
    {
      "name": "message",
      "type": "activityTrigger",
      "direction": "in"
    },
    {
      "type": "eventHub",
      "name": "outputEventHubMessage",
      "connection": "EventhubConnectionSetting",
      "eventHubName": "eh_messages",
      "direction": "out"
  }
  ]
}
```

```javascript
module.exports = async function (context) {
    context.bindings.outputEventHubMessage = context.bindings.message;
};
```

## <a name="orchestration-client"></a>Cliente de orquestração

The orchestration client binding enables you to write functions that interact with orchestrator functions. These functions are sometimes referred to as [client functions](durable-functions-types-features-overview.md#client-functions). Por exemplo, você pode atuar em instâncias de orquestração das maneiras a seguir:

* Iniciá-las.
* Consultar seu status.
* Encerrá-las.
* Enviar eventos a elas durante sua execução.
* Limpar o histórico de instância.

If you're using Visual Studio, you can bind to the orchestration client by using the `OrchestrationClientAttribute` .NET attribute for Durable Functions 1.0. Starting in the Durable Functions 2.0, you can bind to the orchestration client by using the `DurableClientAttribute` .NET attribute.

If you're using scripting languages (for example, *.csx* or *.js* files) for development, the orchestration trigger is defined by the following JSON object in the `bindings` array of *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "in"
}
```

* `taskHub` – usado em cenários em que vários aplicativos de funções compartilham a mesma conta de armazenamento, mas precisam ser isolados uns dos outros. Se não for especificado, o valor padrão de `host.json` será usado. Esse valor deve corresponder ao valor usado pelas funções de orquestrador de destino.
* `connectionName` – O nome de uma configuração de aplicativo que contém uma cadeia de conexão de armazenamento. A conta de armazenamento representada por essa cadeia de conexão deve ser a mesma usada pelas funções de orquestrador de destino. Se não for especificada, a cadeia de conexão da conta de armazenamento padrão do aplicativo de funções será usada.

> [!NOTE]
> Na maioria dos casos, é recomendável omitir essas propriedades e contar com o comportamento padrão.

### <a name="client-usage"></a>Uso do cliente

In .NET functions, you typically bind to `IDurableOrchestrationClient`, which gives you full access to all orchestration client APIs supported by Durable Functions. In the older Durable Functions 2.x releases, you instead bind to the `DurableOrchestrationClient` class. In JavaScript, the same APIs are exposed by the object returned from `getClient`. APIs no objeto cliente incluem:

* `StartNewAsync`
* `GetStatusAsync`
* `TerminateAsync`
* `RaiseEventAsync`
* `PurgeInstanceHistoryAsync`
* `CreateCheckStatusResponse`
* `CreateHttpManagementPayload`

Alternatively, .NET functions can bind to `IAsyncCollector<T>` where `T` is `StartOrchestrationArgs` or `JObject`.

For more information on these operations, see the `IDurableOrchestrationClient` API documentation.

### <a name="client-sample-visual-studio-development"></a>Exemplo de cliente (desenvolvimento no Visual Studio)

Veja um exemplo de função disparada em fila que é iniciada com uma orquestração "Olá, Mundo".

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> The previous C# code is for Durable Functions 2.x. For Durable Functions 1.x, you must use `OrchestrationClient` attribute instead of the `DurableClient` attribute, and you must use the `DurableOrchestrationClient` parameter type instead of `IDurableOrchestrationClient`. For more information about the differences between versions, see the [Durable Functions Versions](durable-functions-versions.md) article.

### <a name="client-sample-not-visual-studio"></a>Exemplo de cliente (fora do Visual Studio)

Se não estiver usando o Visual Studio para desenvolvimento, você poderá criar o seguinte arquivo *function.json*. Este exemplo mostra como configurar uma função disparada em fila que usa a associação de cliente de orquestração durável:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "queueTrigger",
      "queueName": "durable-function-trigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ]
}
```

> [!NOTE]
> The previous JSON is for Durable Functions 2.x. For Durable Functions 1.x, you must use `orchestrationClient` instead of the `durableClient` as the trigger type. For more information about the differences between versions, see the [Durable Functions Versions](durable-functions-versions.md) article.

A seguir, temos exemplos específico a um idioma que iniciam novas instâncias de função do orquestrador.

#### <a name="c-script-sample"></a>C# Script Sample

The following sample shows how to use the durable orchestration client binding to start a new function instance from a queue-triggered C# function:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

using Microsoft.Azure.WebJobs.Extensions.DurableTask;

public static Task Run(string input, IDurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> The previous code is for Durable Functions 2.x. For Durable Functions 1.x, you must use the `DurableOrchestrationClient` parameter type instead of `IDurableOrchestrationClient`. For more information about the differences between versions, see the [Durable Functions Versions](durable-functions-versions.md) article.

#### <a name="javascript-sample"></a>Exemplo de JavaScript

O exemplo a seguir mostra como usar a associação de cliente de orquestração durável para iniciar uma nova instância de função de uma função JavaScript:

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    return instanceId = await client.startNew("HelloWorld", undefined, context.bindings.input);
};
```

Mais detalhes sobre como iniciar instâncias podem ser encontrados em [Gerenciamento de instâncias](durable-functions-instance-management.md).

## <a name="entity-trigger"></a>Entity trigger

Entity triggers allow you to author [entity functions](durable-functions-entities.md). This trigger supports processing events for a specific entity instance.

When you use the Visual Studio tools for Azure Functions, the entity trigger is configured using the `EntityTriggerAttribute` .NET attribute.

> [!NOTE]
> Entity triggers are available starting in Durable Functions 2.x.

Internamente, essa associação de gatilho sonda uma série de filas na conta de armazenamento padrão do aplicativo de funções. Essas filas são detalhes da implementação interna da extensão e, por isso, elas não são configuradas explicitamente nas propriedades de associação.

### <a name="trigger-behavior"></a>Comportamento do gatilho

Here are some notes about the entity trigger:

* **Single-threaded**: A single dispatcher thread is used to process operations for a particular entity. If multiple messages are sent to a single entity concurrently, the operations will be processed one-at-a-time.
* **Poison-message handling** - There is no poison message support in entity triggers.
* **Message visibility** - Entity trigger messages are dequeued and kept invisible for a configurable duration. A visibilidade dessas mensagens é renovada automaticamente, desde que o aplicativo de funções esteja em execução e íntegro.
* **Return values** - Entity functions do not support return values. There are specific APIs that can be used to save state or pass values back to orchestrations.

Any state changes made to an entity during its execution will be automatically persisted after execution has completed.

### <a name="trigger-usage-net"></a>Uso de gatilho (.NET)

Every entity function has a parameter type of `IDurableEntityContext`, which has the following members:

* **EntityName**: the name of the currently executing entity.
* **EntityKey**: the key of the currently executing entity.
* **EntityId**: the ID of the currently executing entity.
* **OperationName**: the name of the current operation.
* **HasState**: whether the entity exists, that is, has some state. 
* **GetState\<TState>()** : gets the current state of the entity. If it does not already exist, it is created and initialized to `default<TState>`. The `TState` parameter must be a primitive or JSON-serializeable type. 
* **GetState\<TState>(initfunction)** : gets the current state of the entity. If it does not already exist, it is created by calling the provided `initfunction` parameter. The `TState` parameter must be a primitive or JSON-serializeable type. 
* **SetState(arg)** : creates or updates the state of the entity. The `arg` parameter must be a JSON-serializeable object or primitive.
* **DeleteState()** : deletes the state of the entity. 
* **GetInput\<TInput>()** : gets the input for the current operation. The `TInput` type parameter must be a primitive or JSON-serializeable type.
* **Return(arg)** : returns a value to the orchestration that called the operation. The `arg` parameter must be a primitive or JSON-serializeable object.
* **SignalEntity(EntityId, operation, input)** : sends a one-way message to an entity. The `operation` parameter must be a non-null string, and the `input` parameter must be a primitive or JSON-serializeable object.
* **CreateNewOrchestration(orchestratorFunctionName, input)** : starts a new orchestration. The `input` parameter must be a primitive or JSON-serializeable object.

The `IDurableEntityContext` object passed to the entity function can be accessed using the `Entity.Current` async-local property. This approach is convenient when using the class-based programming model.

### <a name="trigger-sample-c-function-based-syntax"></a>Trigger sample (C# function-based syntax)

O código a seguir é um exemplo de uma entidade *Counter* simples implementada como uma função durável. Essa função define três operações, `add`, `reset` e `get`, cada uma delas opera em um valor de estado inteiro.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            ctx.SetState(ctx.GetState<int>() + ctx.GetInput<int>());
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(ctx.GetState<int>()));
            break;
    }
}
```

Para obter mais informações sobre a sintaxe baseada em função e como usá-la, confira [Sintaxe baseada em função](durable-functions-dotnet-entities.md#function-based-syntax).

### <a name="trigger-sample-c-class-based-syntax"></a>Trigger sample (C# class-based syntax)

O exemplo a seguir é uma implementação equivalente da entidade `Counter` usando métodos e classes.

```csharp
[JsonObject(MemberSerialization.OptIn)]
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;

    public void Reset() => this.CurrentValue = 0;

    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

O estado dessa entidade é um objeto do tipo `Counter`, que contém um campo que armazena o valor atual do contador. Para persistir esse objeto no armazenamento, ele é serializado e desserializado pela biblioteca [Json.NET](https://www.newtonsoft.com/json). 

Para obter mais informações sobre a sintaxe baseada em classe e como usá-la, confira [Definir classes de entidade](durable-functions-dotnet-entities.md#defining-entity-classes).

> [!NOTE]
> O método de ponto de entrada de função com o atributo `[FunctionName]` *deve* ser declarado `static` ao usar classes de entidade. Métodos de ponto de entrada não estáticos podem resultar em várias inicializações de objeto e potencialmente outros comportamentos indefinidos.

Entity classes have special mechanisms for interacting with bindings and .NET dependency injection. For more information, see [Entity construction](durable-functions-dotnet-entities.md#entity-construction).

### <a name="trigger-sample-javascript"></a>Trigger sample (JavaScript)

The following code is an example of a simple *Counter* entity implemented as a durable function written in JavaScript. Essa função define três operações, `add`, `reset` e `get`, cada uma delas opera em um valor de estado inteiro.

**function.json**
```json
{
  "bindings": [
    {
      "name": "context",
      "type": "entityTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

**index.js**
```javascript
const df = require("durable-functions");

module.exports = df.entity(function(context) {
    const currentValue = context.df.getState(() => 0);
    switch (context.df.operationName) {
        case "add":
            const amount = context.df.getInput();
            context.df.setState(currentValue + amount);
            break;
        case "reset":
            context.df.setState(0);
            break;
        case "get":
            context.df.return(currentValue);
            break;
    }
});
```

> [!NOTE]
> As entidades duráveis estão disponíveis em JavaScript a partir da versão **1.3.0** do pacote npm `durable-functions`.

## <a name="entity-client"></a>Entity client

The entity client binding enables you to asynchronously trigger [entity functions](#entity-trigger). These functions are sometimes referred to as [client functions](durable-functions-types-features-overview.md#client-functions).

If you're using Visual Studio, you can bind to the entity client by using the `DurableClientAttribute` .NET attribute.

> [!NOTE]
> The `[DurableClientAttribute]` can also be used to bind to the [orchestration client](#orchestration-client).

If you're using scripting languages (for example, *.csx* or *.js* files) for development, the entity trigger is defined by the following JSON object in the `bindings` array of *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "out"
}
```

* `taskHub` – usado em cenários em que vários aplicativos de funções compartilham a mesma conta de armazenamento, mas precisam ser isolados uns dos outros. Se não for especificado, o valor padrão de `host.json` será usado. This value must match the value used by the target entity functions.
* `connectionName` – O nome de uma configuração de aplicativo que contém uma cadeia de conexão de armazenamento. The storage account represented by this connection string must be the same one used by the target entity functions. Se não for especificada, a cadeia de conexão da conta de armazenamento padrão do aplicativo de funções será usada.

> [!NOTE]
> In most cases, we recommend that you omit the optional properties and rely on the default behavior.

### <a name="entity-client-usage"></a>Entity client usage

In .NET functions, you typically bind to `IDurableEntityClient`, which gives you full access to all client APIs supported by Durable Entities. You can also bind to the `IDurableOrchestrationClient` interface, which provides access to client APIs for both entities and orchestrations. APIs no objeto cliente incluem:

* **ReadEntityStateAsync\<T>** : reads the state of an entity. It returns a response that indicates whether the target entity exists, and if so, what its state is.
* **SignalEntityAsync**: sends a one-way message to an entity, and waits for it to be enqueued.

There is no need to create the target entity before sending a signal - the entity state can be created from within the entity function that handles the signal.

> [!NOTE]
> It's important to understand that the "signals" sent from the client are simply enqueued, to be processed asynchronously at a later time. In particular, the `SignalEntityAsync` usually returns before the entity even starts the operation, and it is not possible to get back the return value or observe exceptions. If stronger guarantees are required (e.g. for workflows), *orchestrator functions* should be used, which can wait for entity operations to complete, and can process return values and observe exceptions.

### <a name="example-client-signals-entity-directly---c"></a>Example: client signals entity directly - C#

Here is an example queue-triggered function that invokes a "Counter" entity.

```csharp
[FunctionName("AddFromQueue")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableEntityClient client)
{
    // Entity operation input comes from the queue message content.
    var entityId = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(input);
    return client.SignalEntityAsync(entityId, "Add", amount);
}
```

### <a name="example-client-signals-entity-via-interface---c"></a>Example: client signals entity via interface - C#

Where possible, we recommend [accessing entities through interfaces](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) because it provides more type checking. For example, suppose the `Counter` entity mentioned earlier implemented an `ICounter` interface, defined as follows:

```csharp
public interface ICounter
{
    void Add(int amount);
    void Reset();
    Task<int> Get();
}

public class Counter : ICounter
{
    // ...
}
```

Client code can then use `SignalEntityAsync<ICounter>` to generate a type-safe proxy:

```csharp
[FunctionName("UserDeleteAvailable")]
public static async Task AddValueClient(
    [QueueTrigger("my-queue")] string message,
    [DurableClient] IDurableEntityClient client)
{
    var target = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(message);
    await client.SignalEntityAsync<ICounter>(target, proxy => proxy.Add(amount));
}
```

The `proxy` parameter is a dynamically generated instance of `ICounter`, which internally translates the call to `Add` into the equivalent (untyped) call to `SignalEntityAsync`.

> [!NOTE]
> The `SignalEntityAsync` APIs represent one-way operations. If an entity interfaces returns `Task<T>`, the value of the `T` parameter will always be null or `default`.

In particular, it does not make sense to signal the `Get` operation, as no value is returned. Instead, clients can use either `ReadStateAsync` to access the counter state directly, or can start an orchestrator function that calls the `Get` operation.

### <a name="example-client-signals-entity---javascript"></a>Example: client signals entity - JavaScript

Here is an example queue-triggered function that signals a "Counter" entity in JavaScript.

**function.json**
```json
{
    "bindings": [
      {
        "name": "input",
        "type": "queueTrigger",
        "queueName": "durable-entity-trigger",
        "direction": "in",
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

**index.js**
```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

> [!NOTE]
> As entidades duráveis estão disponíveis em JavaScript a partir da versão **1.3.0** do pacote npm `durable-functions`.

<a name="host-json"></a>
## <a name="hostjson-settings"></a>configurações de host.json

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Built-in HTTP API reference for instance management](durable-functions-http-api.md)
