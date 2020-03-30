---
title: Vinculação de saída do Azure Event Grid para funções do Azure
description: Aprenda a enviar um evento da Event Grid em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: e7a2611312ffc33703dd5cc9d0a2d7142ddb0532
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368942"
---
# <a name="azure-event-grid-output-binding-for-azure-functions"></a>Vinculação de saída do Azure Event Grid para funções do Azure

Use a vinculação de saída da Grade de Eventos para escrever eventos em um tópico personalizado. Você deve ter uma chave de acesso válida [para o tópico personalizado](../event-grid/security-authentication.md#custom-topic-publishing).

Para obter informações sobre detalhes de configuração e configuração, consulte a [visão geral](./functions-bindings-event-grid.md).

> [!NOTE]
> A vinculação de saída do Event Grid não suporta assinaturas de acesso compartilhada (tokens SAS). Você deve usar a chave de acesso do tópico.

> [!IMPORTANT]
> A vinculação de saída da Grade de Eventos só está disponível para funções 2.x ou superior.

## <a name="example"></a>Exemplo

# <a name="c"></a>[C #](#tab/csharp)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que grava uma mensagem para um tópico personalizado da Grade de Eventos, usando o valor de retorno do método como saída:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static EventGridEvent Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    return new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

O exemplo a seguir `IAsyncCollector` mostra como usar a interface para enviar um lote de mensagens.

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

O exemplo a seguir mostra os dados de vinculação de saída da Event Grid no arquivo *function.json.*

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Aqui está o código de script C# que cria um evento:

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

Aqui está o código de script C# que cria vários eventos:

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

O exemplo a seguir mostra os dados de vinculação de saída da Event Grid no arquivo *function.json.*

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Aqui está o código JavaScript que cria um único evento:

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

Aqui está o código JavaScript que cria vários eventos:

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

A vinculação de saída do Event Grid não está disponível para Python.

# <a name="java"></a>[Java](#tab/java)

A vinculação de saída event grid não está disponível para Java.

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C #](#tab/csharp)

Para [bibliotecas de classe C#,](functions-dotnet-class-library.md)use o atributo [EventGridAttribute.](https://github.com/Azure/azure-functions-eventgrid-extension/blob/dev/src/EventGridExtension/OutputBinding/EventGridAttribute.cs)

O construtor do atributo leva o nome de uma configuração de aplicativo que contém o nome do tópico personalizado e o nome de uma configuração de aplicativo que contém a chave de tópico. Para obter mais informações sobre essas configurações, consulte [Saída - configuração](#configuration). Este é um `EventGrid` exemplo de atributo:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Para um exemplo completo, veja [o exemplo](#example).

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

Os atributos não são suportados pelo script C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="python"></a>[Python](#tab/python)

A vinculação de saída do Event Grid não está disponível para Python.

# <a name="java"></a>[Java](#tab/java)

A vinculação de saída event grid não está disponível para Java.

---

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de `EventGrid` vinculação que você definiu no arquivo *function.json* e no atributo.

|Propriedade function.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**type** | n/d | Deve ser definido como "eventGrid". |
|**direction** | n/d | Deve ser definido como "out". Esse parâmetro é definido automaticamente quando você cria a associação no portal do Azure. |
|**name** | n/d | É o nome da variável usada no código da função que representa o evento. |
|**tópicoEndpointUri** |**TópicoEndpointUri** | O nome de uma configuração de aplicativo que `MyTopicEndpointUri`contém o URI para o tópico personalizado, como . |
|**topicKeySetting** |**Configuração de tecla tópicos** | O nome de uma configuração de aplicativo que contém uma chave de acesso para o tópico personalizado. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Certifique-se de definir `TopicEndpointUri` o valor da propriedade de configuração para o nome de uma configuração de aplicativo que contém o URI do tópico personalizado. Não especifique o URI do tópico personalizado diretamente nesta propriedade.

## <a name="usage"></a>Uso

# <a name="c"></a>[C #](#tab/csharp)

Envie mensagens usando um parâmetro `out EventGridEvent paramName`de método como . Para gravar várias mensagens, você pode usar `ICollector<EventGridEvent>` ou `IAsyncCollector<EventGridEvent>` no lugar de `out EventGridEvent`.

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

Envie mensagens usando um parâmetro `out EventGridEvent paramName`de método como . No script do C#, `paramName` é o valor especificado na propriedade `name` de *function.json*. Para gravar várias mensagens, você pode usar `ICollector<EventGridEvent>` ou `IAsyncCollector<EventGridEvent>` no lugar de `out EventGridEvent`.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Acesse o evento `context.bindings.<name>` de `<name>` saída usando onde `name` está o valor especificado na propriedade de *function.json*.

# <a name="python"></a>[Python](#tab/python)

A vinculação de saída do Event Grid não está disponível para Python.

# <a name="java"></a>[Java](#tab/java)

A vinculação de saída event grid não está disponível para Java.

---

## <a name="next-steps"></a>Próximas etapas

* [Despachar um evento da Grade de Eventos](./functions-bindings-event-grid-trigger.md)
