---
title: Associação de saída da grade de eventos do Azure para Azure Functions
description: Saiba como enviar um evento de grade de eventos no Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: e7a2611312ffc33703dd5cc9d0a2d7142ddb0532
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77368942"
---
# <a name="azure-event-grid-output-binding-for-azure-functions"></a>Associação de saída da grade de eventos do Azure para Azure Functions

Use a associação de saída da grade de eventos para gravar eventos em um tópico personalizado. Você deve ter uma [chave de acesso válida para o tópico personalizado](../event-grid/security-authentication.md#custom-topic-publishing).

Para obter informações sobre configuração e detalhes de configuração, consulte a [visão geral](./functions-bindings-event-grid.md).

> [!NOTE]
> A associação de saída da grade de eventos não oferece suporte a assinaturas de acesso compartilhado (tokens SAS). Você deve usar a tecla de acesso do tópico.

> [!IMPORTANT]
> A associação de saída da grade de eventos só está disponível para o Functions 2. x e superior.

## <a name="example"></a>Exemplo

# <a name="c"></a>[C#](#tab/csharp)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que grava uma mensagem em um tópico personalizado da grade de eventos, usando o valor de retorno do método como a saída:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static EventGridEvent Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    return new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

O exemplo a seguir mostra como usar a `IAsyncCollector` interface para enviar um lote de mensagens.

```csharp
[FunctionName("EventGridAsyncOutput")]
public static async Task Run(
    [TimerTrigger("0 */5 * * * *")] TimerInfo myTimer,
    [EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]IAsyncCollector<EventGridEvent> outputEvents,
    ILogger log)
{
    for (var i = 0; i < 3; i++)
    {
        var myEvent = new EventGridEvent("message-id-" + i, "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
        await outputEvents.AddAsync(myEvent);
    }
}
```

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

O exemplo a seguir mostra os dados de associação de saída da grade de eventos no arquivo *Function. JSON* .

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Aqui está o código do script C# que cria um evento:

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out EventGridEvent outputEvent, ILogger log)
{
    outputEvent = new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

Aqui está o código do script C# que cria vários eventos:

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, ICollector<EventGridEvent> outputEvent, ILogger log)
{
    outputEvent.Add(new EventGridEvent("message-id-1", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
    outputEvent.Add(new EventGridEvent("message-id-2", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra os dados de associação de saída da grade de eventos no arquivo *Function. JSON* .

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Este é o código JavaScript que cria um único evento:

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = {
        id: 'message-id',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    };
    context.done();
};
```

Este é o código JavaScript que cria vários eventos:

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = [];

    context.bindings.outputEvent.push({
        id: 'message-id-1',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.bindings.outputEvent.push({
        id: 'message-id-2',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

A associação de saída da grade de eventos não está disponível para Python.

# <a name="java"></a>[Java](#tab/java)

A associação de saída da grade de eventos não está disponível para Java.

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C#](#tab/csharp)

Para [bibliotecas de classes C#](functions-dotnet-class-library.md), use o atributo [EventGridAttribute](https://github.com/Azure/azure-functions-eventgrid-extension/blob/dev/src/EventGridExtension/OutputBinding/EventGridAttribute.cs) .

O construtor do atributo usa o nome de uma configuração de aplicativo que contém o nome do tópico personalizado e o nome de uma configuração de aplicativo que contém a chave do tópico. Para obter mais informações sobre essas configurações, consulte [Saída - configuração](#configuration). Este é um `EventGrid` exemplo de atributo:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Para obter um exemplo completo, consulte [exemplo](#example).

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

Não há suporte para atributos pelo script C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Não há suporte para atributos pelo JavaScript.

# <a name="python"></a>[Python](#tab/python)

A associação de saída da grade de eventos não está disponível para Python.

# <a name="java"></a>[Java](#tab/java)

A associação de saída da grade de eventos não está disponível para Java.

---

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *Function. JSON* e o `EventGrid` atributo.

|Propriedade function.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | N/D | Deve ser definido como "eventGrid". |
|**direção** | N/D | Deve ser definido como "out". Esse parâmetro é definido automaticamente quando você cria a associação no portal do Azure. |
|**name** | N/D | É o nome da variável usada no código da função que representa o evento. |
|**topicEndpointUri** |**TopicEndpointUri** | O nome de uma configuração de aplicativo que contém o URI para o tópico personalizado, como `MyTopicEndpointUri`. |
|**topicKeySetting** |**TopicKeySetting** | O nome de uma configuração de aplicativo que contém uma chave de acesso para o tópico personalizado. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Certifique-se de definir o valor da `TopicEndpointUri` propriedade de configuração como o nome de uma configuração de aplicativo que contém o URI do tópico personalizado. Não especifique o URI do tópico personalizado diretamente nesta propriedade.

## <a name="usage"></a>Uso

# <a name="c"></a>[C#](#tab/csharp)

Envie mensagens usando um parâmetro de método, como `out EventGridEvent paramName`. Para gravar várias mensagens, você pode usar `ICollector<EventGridEvent>` ou `IAsyncCollector<EventGridEvent>` no lugar de `out EventGridEvent`.

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

Envie mensagens usando um parâmetro de método, como `out EventGridEvent paramName`. No script do C#, `paramName` é o valor especificado na propriedade `name` de *function.json*. Para gravar várias mensagens, você pode usar `ICollector<EventGridEvent>` ou `IAsyncCollector<EventGridEvent>` no lugar de `out EventGridEvent`.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Acesse o evento de saída `context.bindings.<name>` usando `<name>` WHERE é o valor especificado na `name` propriedade de *Function. JSON*.

# <a name="python"></a>[Python](#tab/python)

A associação de saída da grade de eventos não está disponível para Python.

# <a name="java"></a>[Java](#tab/java)

A associação de saída da grade de eventos não está disponível para Java.

---

## <a name="next-steps"></a>Próximas etapas

* [Enviar um evento de grade de eventos](./functions-bindings-event-grid-trigger.md)
