---
title: Associações para Funções Duráveis – Azure
description: Como usar gatilhos e associações para a extensão de Durable Functions do Azure Functions.
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: 1f42c6c9b0086d49e539040334c83cfc0c6feb42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278214"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Associações para Funções Duráveis (Azure Functions)

A extensão [Funções Duráveis](durable-functions-overview.md) introduz duas novas associações de gatilho que controlam a execução de funções de orquestrador e atividade. Ela também introduz uma associação de saída que atua como um cliente para o runtime das Funções Duráveis.

## <a name="orchestration-trigger"></a>Gatilho de orquestração

O gatilho de orquestração permite que você autor [de funções orquestradoras duráveis](durable-functions-types-features-overview.md#orchestrator-functions). Esse gatilho dá suporte a iniciar novas instâncias de função de orquestrador e a retomar instâncias de função de orquestrador existentes que estão "aguardando" uma tarefa.

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

* `orchestration`é o nome da orquestração que os clientes devem usar quando querem iniciar novas instâncias dessa função orquestradora. Essa propriedade é opcional. Se não for especificada, o nome da função será usado.

Internamente, essa associação de gatilho sonda uma série de filas na conta de armazenamento padrão do aplicativo de funções. Essas filas são detalhes da implementação interna da extensão e, por isso, elas não são configuradas explicitamente nas propriedades de associação.

### <a name="trigger-behavior"></a>Comportamento do gatilho

Veja algumas observações sobre o gatilho de orquestração:

* **Threading único** – um único thread dispatcher é usado para toda a execução de função de orquestrador em uma única instância de host. Por esse motivo, é importante garantir que o código da função de orquestrador seja eficiente e não execute nenhuma E/S. Também é importante garantir que esse thread não faça nenhum trabalho assíncrono, exceto ao aguardar tipos de tarefas específicas das Funções Duráveis.
* **Manipulação de mensagens suspeitas** – não há suporte para mensagens suspeitas nos acionadores de orquestração.
* **Visibilidade da mensagem** – as mensagens do gatilho de orquestração são removidas da fila e mantidas invisíveis por uma duração configurável. A visibilidade dessas mensagens é renovada automaticamente, desde que o aplicativo de funções esteja em execução e íntegro.
* **Valores retornados** – os valores retornados são serializados em JSON e são persistidos na tabela de histórico de orquestração no Armazenamento de Tabelas do Azure. Esse valores retornados podem ser consultados pela associação do cliente de orquestração, descrita posteriormente.

> [!WARNING]
> Funções de orquestrador nunca devem usar nenhuma associação de entrada ou saída que não seja a associação do gatilho de orquestração. Fazer isso tem o potencial de causar problemas com a extensão de Tarefa Durável, pois essas associações podem não cumprir as regras de E/S e de thread único. Se você quiser usar outras vinculações, adicione-as a uma função Atividade chamada a partir de sua função Orquestrador.

> [!WARNING]
> Funções de orquestrador JavaScript nunca devem ser declaradas como `async`.

### <a name="trigger-usage-net"></a>Uso de gatilho (.NET)

A associação de gatilho de orquestração dá suporte a entradas e saídas. Estas são algumas coisas que você precisa saber sobre a manipulação de entradas e saídas:

* **entradas** - as funções de `DurableOrchestrationContext` orquestração .NET suportam apenas como um tipo de parâmetro. Não há suporte para entradas de desserialização diretamente na assinatura da função. O código `GetInput<T>` deve usar o `getInput` método (.NET) ou (JavaScript) para buscar entradas de função orquestradoras. Essas entradas devem ser tipos serializáveis em JSON.
* **saídas** – gatilhos de orquestração dão suporte a valores de saída, bem como entradas. O valor retornado da função é usado para atribuir o valor de saída e deve ser serializável em JSON. Se uma função .NET retornar `Task` ou `void`, um valor `null` será salvo como a saída.

### <a name="trigger-sample"></a>Exemplo de gatilho

O código de exemplo a seguir mostra como a função orquestradora mais simples do "Hello World" pode parecer:

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
> O código anterior é para Funções Duráveis 2.x. Para funções duráveis 1.x, você deve usar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext`. Para obter mais informações sobre as diferenças entre as versões, consulte o artigo [Durable Functions Versions.](durable-functions-versions.md)

#### <a name="javascript-functions-20-only"></a>JavaScript (somente funções 2.0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> O `context` objeto em JavaScript não representa o DurableOrchestrationContext, mas o [contexto de função como um todo](../functions-reference-node.md#context-object). Você pode acessar os métodos de orquestração por meio da propriedade `df` do objeto `context`.

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
> O código anterior é para Funções Duráveis 2.x. Para funções duráveis 1.x, você deve usar `DurableOrchestrationContext` em vez de `IDurableOrchestrationContext`. Para obter mais informações sobre as diferenças entre as versões, consulte o artigo [de funções duráveis.](durable-functions-versions.md)

#### <a name="javascript-functions-20-only"></a>JavaScript (somente funções 2.0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-trigger"></a>Gatilho de atividade

O gatilho de atividade permite que você autorize funções que são chamadas por funções orquestradoras, conhecidas como [funções de atividade](durable-functions-types-features-overview.md#activity-functions).

Se você estiver usando o Visual Studio, o `ActivityTriggerAttribute` gatilho de atividade será configurado usando o atributo .NET.

Se você estiver usando o VS Code ou o Portal do Azure para desenvolvimento, o gatilho de atividade será definido pelo seguinte objeto JSON na matriz `bindings` de *function.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity` é o nome da atividade. Este valor é o nome que as funções do orquestrador usam para invocar essa função de atividade. Essa propriedade é opcional. Se não for especificada, o nome da função será usado.

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

* **entradas** - .NET funções `DurableActivityContext` de atividade nativamente usar como um tipo de parâmetro. Como alternativa, uma função de atividade pode ser declarada com qualquer tipo de parâmetro que possa ser serializado em JSON. Quando você `DurableActivityContext`usar, `GetInput<T>` você pode ligar para buscar e desserializar a entrada da função de atividade.
* **saídas** – Funções de atividade dão suporte a valores de saída, bem como entradas. O valor retornado da função é usado para atribuir o valor de saída e deve ser serializável em JSON. Se uma função .NET retornar `Task` ou `void`, um valor `null` será salvo como a saída.
* **metadados** – funções de atividade .NET podem ser associadas a um parâmetro `string instanceId` para obter a ID da instância da orquestração pai.

### <a name="trigger-sample"></a>Exemplo de gatilho

O código de exemplo a seguir mostra como uma simples função de atividade "Hello World" pode parecer:

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
> O código anterior é para Funções Duráveis 2.x. Para funções duráveis 1.x, você deve usar `DurableActivityContext` em vez de `IDurableActivityContext`. Para obter mais informações sobre as diferenças entre as versões, consulte o artigo [Durable Functions Versions.](durable-functions-versions.md)

O tipo de parâmetro padrão para a associação `ActivityTriggerAttribute` .NET é `IDurableActivityContext`. No entanto, os gatilhos de atividade .NET também dão suporte à associação direta com tipos serializáveis em JSON (incluindo tipos primitivos), de modo que a mesma função poderia ser simplificada da seguinte forma:

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-20-only"></a>JavaScript (somente funções 2.0)

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


### <a name="using-input-and-output-bindings"></a>Usando ligações de entrada e saída

Você pode usar ligações regulares de entrada e saída, além da vinculação do gatilho de atividade. Por exemplo, você pode levar a entrada para a vinculação de atividades e enviar uma mensagem para um EventHub usando a vinculação de saída eventHub:

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

A vinculação do cliente de orquestração permite que você escreva funções que interagem com funções orquestradoras. Essas funções são às vezes referidas como [funções do cliente](durable-functions-types-features-overview.md#client-functions). Por exemplo, você pode atuar em instâncias de orquestração das maneiras a seguir:

* Iniciá-las.
* Consultar seu status.
* Encerrá-las.
* Enviar eventos a elas durante sua execução.
* Limpar o histórico de instância.

Se você estiver usando o Visual Studio, você pode `OrchestrationClientAttribute` vincular-se ao cliente de orquestração usando o atributo .NET para Funções Duráveis 1.0. A partir das Funções Duráveis 2.0, você pode `DurableClientAttribute` vincular-se ao cliente de orquestração usando o atributo .NET.

Se você estiver usando linguagens de script (por exemplo, *arquivos .csx* ou *.js)* para desenvolvimento, `bindings` o gatilho de orquestração é definido pelo seguinte objeto JSON na matriz de *função.json*:

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

Em funções .NET, você `IDurableOrchestrationClient`normalmente se vincula, o que lhe dá acesso total a todas as APIs de cliente de orquestração suportadas por Funções Duráveis. Nas funções duráveis mais antigas 2.x, `DurableOrchestrationClient` você se vincula à classe. Em JavaScript, as mesmas APIs são expostas `getClient`pelo objeto retornado de . APIs no objeto cliente incluem:

* `StartNewAsync`
* `GetStatusAsync`
* `TerminateAsync`
* `RaiseEventAsync`
* `PurgeInstanceHistoryAsync`
* `CreateCheckStatusResponse`
* `CreateHttpManagementPayload`

Alternativamente, as funções .NET `T` `StartOrchestrationArgs` podem `JObject`se ligar a `IAsyncCollector<T>` onde está ou .

Para obter mais informações sobre `IDurableOrchestrationClient` essas operações, consulte a documentação da API.

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
> O código C# anterior é para Funções Duráveis 2.x. Para funções duráveis 1.x, você `OrchestrationClient` `DurableClient` deve usar atributo `DurableOrchestrationClient` em vez do `IDurableOrchestrationClient`atributo, e você deve usar o tipo de parâmetro em vez de . Para obter mais informações sobre as diferenças entre as versões, consulte o artigo [Durable Functions Versions.](durable-functions-versions.md)

### <a name="client-sample-not-visual-studio"></a>Exemplo de cliente (fora do Visual Studio)

Se você não estiver usando o Visual Studio para desenvolvimento, você pode criar o seguinte arquivo *function.json.* Este exemplo mostra como configurar uma função disparada em fila que usa a associação de cliente de orquestração durável:

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
> O JSON anterior é para Funções Duráveis 2.x. Para funções duráveis 1.x, você deve usar `orchestrationClient` em vez do `durableClient` tipo de gatilho. Para obter mais informações sobre as diferenças entre as versões, consulte o artigo [Durable Functions Versions.](durable-functions-versions.md)

A seguir, temos exemplos específico a um idioma que iniciam novas instâncias de função do orquestrador.

#### <a name="c-script-sample"></a>C# Amostra de script

A amostra a seguir mostra como usar a vinculação do cliente de orquestração durável para iniciar uma nova instância de função a partir de uma função C# acionada na fila:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

using Microsoft.Azure.WebJobs.Extensions.DurableTask;

public static Task Run(string input, IDurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> O código anterior é para Funções Duráveis 2.x. Para funções duráveis 1.x, `DurableOrchestrationClient` você deve `IDurableOrchestrationClient`usar o tipo de parâmetro em vez de . Para obter mais informações sobre as diferenças entre as versões, consulte o artigo [Durable Functions Versions.](durable-functions-versions.md)

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

## <a name="entity-trigger"></a>Gatilho de entidade

Os gatilhos de entidade permitem que você seja autor [de funções de entidade.](durable-functions-entities.md) Este gatilho suporta eventos de processamento para uma instância específica da entidade.

Quando você usa as ferramentas do Visual Studio para funções do `EntityTriggerAttribute` Azure, o gatilho da entidade é configurado usando o atributo .NET.

> [!NOTE]
> Os gatilhos da entidade estão disponíveis a partir de Funções Duráveis 2.x.

Internamente, essa associação de gatilho sonda uma série de filas na conta de armazenamento padrão do aplicativo de funções. Essas filas são detalhes da implementação interna da extensão e, por isso, elas não são configuradas explicitamente nas propriedades de associação.

### <a name="trigger-behavior"></a>Comportamento do gatilho

Aqui estão algumas notas sobre o gatilho da entidade:

* **Single-threaded**: Um único segmento de despachante é usado para processar operações para uma entidade específica. Se várias mensagens forem enviadas para uma única entidade simultaneamente, as operações serão processadas uma por vez.
* **Manuseio de mensagens venenosas** - Não há apoio de mensagens venenosas nos gatilhos da entidade.
* **Visibilidade da mensagem** - As mensagens de gatilho da entidade são enfileiradas e mantidas invisíveis por uma duração configurável. A visibilidade dessas mensagens é renovada automaticamente, desde que o aplicativo de funções esteja em execução e íntegro.
* **Valores de devolução** - As funções da entidade não suportam valores de retorno. Existem APIs específicas que podem ser usadas para salvar o estado ou repassar valores para orquestrações.

Quaisquer alterações de Estado feitas a uma entidade durante sua execução serão automaticamente persistidas após a execução ter sido concluída.

### <a name="trigger-usage-net"></a>Uso de gatilho (.NET)

Cada função de entidade tem `IDurableEntityContext`um tipo de parâmetro de , que tem os seguintes membros:

* **EntityName**: o nome da entidade executora atual.
* **EntityKey**: a chave da entidade executora atualmente.
* **EntityId**: o ID da entidade executora.
* **OperationName**: o nome da operação atual.
* **TemEstado**: se a entidade existe, ou seja, tem algum estado. 
* **GetState\<TState>()**: obtém o estado atual da entidade. Se ele ainda não existe, é criado `default<TState>`e inicializado para . O `TState` parâmetro deve ser um tipo primitivo ou serializeable JSON. 
* **GetState\<TState>(initfunction)**: obtém o estado atual da entidade. Se ele ainda não existe, ele é criado `initfunction` chamando o parâmetro fornecido. O `TState` parâmetro deve ser um tipo primitivo ou serializeable JSON. 
* **SetState(arg):** cria ou atualiza o estado da entidade. O `arg` parâmetro deve ser um objeto serializado ré json ou primitivo.
* **DeleteState:exclui**o estado da entidade. 
* **GetInput\<TInput>()**: obtém a entrada para a operação atual. O `TInput` parâmetro de tipo deve ser um tipo primitivo ou serializeable JSON.
* **Return(arg)**: devolve um valor à orquestração que chamou a operação. O `arg` parâmetro deve ser um objeto primitivo ou serializado ré JSON.
* **SignalEntity (EntityId, scheduledTimeUtc, operação, entrada)**: envia uma mensagem unidirecional para uma entidade. O `operation` parâmetro deve ser uma seqüência `scheduledTimeUtc` não nula, o opcional deve ser uma `input` data UTC para invocar a operação, e o parâmetro deve ser um objeto primitivo ou serializeable JSON.
* **CreateNewOrchestration (orchestratorFunctionName, input)**: inicia uma nova orquestração. O `input` parâmetro deve ser um objeto primitivo ou serializado ré JSON.

O `IDurableEntityContext` objeto passado para a função entidade `Entity.Current` pode ser acessado usando a propriedade assíncrona local. Esta abordagem é conveniente ao usar o modelo de programação baseado em classe.

### <a name="trigger-sample-c-function-based-syntax"></a>Amostra de gatilho (sintaxe baseada em função C#)

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

Para obter mais informações sobre a sintaxe baseada em funções e como usá-la, consulte [Sintaxe baseada em funções](durable-functions-dotnet-entities.md#function-based-syntax).

### <a name="trigger-sample-c-class-based-syntax"></a>Amostra de gatilho (sintaxe baseada em classe C#)

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
> O método de ponto de entrada de função com o atributo `[FunctionName]`*deve* ser declarado `static` ao usar classes de entidade. Métodos de ponto de entrada não estáticos podem resultar em várias inicializações de objeto e potencialmente outros comportamentos indefinidos.

As classes de entidade têm mecanismos especiais para interagir com ligações e injeção de dependência .NET. Para obter mais informações, consulte [Entity construction](durable-functions-dotnet-entities.md#entity-construction).

### <a name="trigger-sample-javascript"></a>Amostra de gatilho (JavaScript)

O código a seguir é um exemplo de uma entidade *counter* simples implementada como uma função durável escrita em JavaScript. Essa função define três operações, `add`, `reset` e `get`, cada uma delas opera em um valor de estado inteiro.

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

## <a name="entity-client"></a>Cliente da entidade

A vinculação do cliente da entidade permite que você acione as [funções da entidade](#entity-trigger)de forma assíncrona . Essas funções são às vezes referidas como [funções do cliente](durable-functions-types-features-overview.md#client-functions).

Se você estiver usando o Visual Studio, você pode `DurableClientAttribute` vincular-se ao cliente da entidade usando o atributo .NET.

> [!NOTE]
> O `[DurableClientAttribute]` também pode ser usado para se ligar ao [cliente de orquestração.](#orchestration-client)

Se você estiver usando linguagens de script (por exemplo, *arquivos .csx* ou *.js)* para desenvolvimento, `bindings` o gatilho da entidade é definido pelo seguinte objeto JSON na matriz de *função.json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "in"
}
```

* `taskHub` – usado em cenários em que vários aplicativos de funções compartilham a mesma conta de armazenamento, mas precisam ser isolados uns dos outros. Se não for especificado, o valor padrão de `host.json` será usado. Esse valor deve corresponder ao valor utilizado pelas funções da entidade-alvo.
* `connectionName` – O nome de uma configuração de aplicativo que contém uma cadeia de conexão de armazenamento. A conta de armazenamento representada por essa seqüência de conexões deve ser a mesma usada pelas funções da entidade-alvo. Se não for especificada, a cadeia de conexão da conta de armazenamento padrão do aplicativo de funções será usada.

> [!NOTE]
> Na maioria dos casos, recomendamos que você omita as propriedades opcionais e confie no comportamento padrão.

### <a name="entity-client-usage"></a>Uso do cliente da entidade

Em funções .NET, você `IDurableEntityClient`normalmente se vincula, o que lhe dá acesso total a todas as APIs de cliente suportadas por Entidades Duráveis. Você também pode `IDurableOrchestrationClient` vincular-se à interface, que fornece acesso a APIs de clientes para entidades e orquestrações. APIs no objeto cliente incluem:

* **ReadEntityStateAsync\<T>**: lê o estado de uma entidade. Ele retorna uma resposta que indica se a entidade-alvo existe, e se sim, qual é o seu estado.
* **SignalEntityAsync**: envia uma mensagem unidirecional para uma entidade e aguarda que ela seja enfileirada.
* **ListEntitiesAsync**: consultas para o estado de várias entidades. As entidades podem ser consultadas pelo *nome* e *última hora de operação.*

Não há necessidade de criar a entidade-alvo antes de enviar um sinal - o estado da entidade pode ser criado dentro da função da entidade que lida com o sinal.

> [!NOTE]
> É importante entender que os "sinais" enviados pelo cliente são simplesmente enfileirados, para serem processados assíncronamente posteriormente. Em particular, `SignalEntityAsync` o retorno geralmente antes mesmo da entidade iniciar a operação, e não é possível recuperar o valor de retorno ou observar exceções. Se forem necessárias garantias mais fortes (por exemplo, para fluxos de trabalho), devem ser *utilizadas funções orquestradoras,* que podem aguardar a conclusão das operações da entidade e podem processar valores de retorno e observar exceções.

### <a name="example-client-signals-entity-directly---c"></a>Exemplo: entidade de sinais de cliente diretamente - C #

Aqui está um exemplo de função acionada pela fila que invoca uma entidade "Contador".

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

### <a name="example-client-signals-entity-via-interface---c"></a>Exemplo: entidade de sinais de cliente via interface - C #

Sempre que possível, recomendamos [acessar entidades através de interfaces,](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) pois fornece mais verificação de tipo. Por exemplo, `Counter` suponha que `ICounter` a entidade mencionada anteriormente implementou uma interface, definida da seguinte forma:

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

O código do `SignalEntityAsync<ICounter>` cliente pode, então, ser usado para gerar um proxy seguro de tipo:

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

O `proxy` parâmetro é uma instância `ICounter`dinamicamente gerada, que `Add` traduz internamente a chamada `SignalEntityAsync`para a chamada equivalente (não digitada) para .

> [!NOTE]
> As `SignalEntityAsync` APIs representam operações unidirecionais. Se uma interface `Task<T>`de entidade retornar, o valor `T` do `default`parâmetro será sempre nulo ou .

Em particular, não faz sentido `Get` sinalizar a operação, pois nenhum valor é devolvido. Em vez disso, `ReadStateAsync` os clientes podem usar para acessar o estado `Get` de contador diretamente, ou podem iniciar uma função orquestradora que chama a operação.

### <a name="example-client-signals-entity---javascript"></a>Exemplo: entidade de sinais de cliente - JavaScript

Aqui está um exemplo de função acionada pela fila que sinaliza uma entidade "Contador" em JavaScript.

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

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência de API HTTP incorporada para gerenciamento de exemplo](durable-functions-http-api.md)
