---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 1e25656b58fe675cfbe87fef75af4fcb174b7f55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589734"
---
Use a associação de saída dos Hubs de Eventos para gravar eventos em um fluxo de eventos. É necessário ter permissão de envio para um hub de eventos a fim de gravar eventos nele.

Certifique-se de que as referências de pacote necessárias estão no lugar antes de tentar implementar uma vinculação de saída.

<a id="example" name="example"></a>

# <a name="c"></a>[C #](#tab/csharp)

O exemplo a seguir mostra uma [função C#](../articles/azure-functions/functions-dotnet-class-library.md) que grava uma mensagem em um hub de eventos, usando o valor retornado do método como a saída:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

O exemplo a seguir `IAsyncCollector` mostra como usar a interface para enviar um lote de mensagens. Esse cenário é comum quando você está processando mensagens vindas de um Hub de Eventos e enviando o resultado para outro Event Hub.

```csharp
[FunctionName("EH2EH")]
public static async Task Run(
    [EventHubTrigger("source", Connection = "EventHubConnectionAppSetting")] EventData[] events,
    [EventHub("dest", Connection = "EventHubConnectionAppSetting")]IAsyncCollector<string> outputEvents,
    ILogger log)
{
    foreach (EventData eventData in events)
    {
        // do some processing:
        var myProcessedEvent = DoSomething(eventData);

        // then send the message
        await outputEvents.AddAsync(JsonConvert.SerializeObject(myProcessedEvent));
    }
}
```

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

O exemplo a seguir mostra uma associação de gatilho de hub de eventos em um arquivo *function.json* e uma [função C# script](../articles/azure-functions/functions-reference-csharp.md) que usa a associação. A função grava uma mensagem em um hub de eventos.

Os exemplos a seguir mostram dados de associação de Hubs de eventos no *function.json* arquivo. O primeiro exemplo é para Funções 2.x e superior, e o segundo é para Funções 1.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Este é o código C# script que cria uma mensagem:

```cs
using System;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, ILogger log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.LogInformation(msg);   
    outputEventHubMessage = msg;
}
```

Este é o código de script C# que cria várias mensagens:

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, ILogger log)
{
    string message = $"Message created at: {DateTime.Now}";
    log.LogInformation(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

O exemplo a seguir mostra uma associação de gatilho de hub de eventos em um arquivo *function.json* e uma [função JavaScript](../articles/azure-functions/functions-reference-node.md) que usa a associação. A função grava uma mensagem em um hub de eventos.

Os exemplos a seguir mostram dados de associação de Hubs de eventos no *function.json* arquivo. O primeiro exemplo é para Funções 2.x e superior, e o segundo é para Funções 1.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Este é o código JavaScript que envia uma mensagem única:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Message created at: " + timeStamp;
    context.done();
};
```

Este é o código JavaScript que envia várias mensagens:

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();
    var message = 'Message created at: ' + timeStamp;

    context.bindings.outputEventHubMessage = [];

    context.bindings.outputEventHubMessage.push("1 " + message);
    context.bindings.outputEventHubMessage.push("2 " + message);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir mostra uma ligação de acionador do Hub de Eventos em um arquivo *function.json* e uma [função Python](../articles/azure-functions/functions-reference-python.md) que usa a ligação. A função grava uma mensagem em um hub de eventos.

Os exemplos a seguir mostram dados de associação de Hubs de eventos no *function.json* arquivo.

```json
{
    "type": "eventHub",
    "name": "$return",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Este é o código Python que envia uma mensagem única:

```python
import datetime
import logging
import azure.functions as func


def main(timer: func.TimerRequest) -> str:
    timestamp = datetime.datetime.utcnow()
    logging.info('Message created at: %s', timestamp)
    return 'Message created at: {}'.format(timestamp)
```

# <a name="java"></a>[Java](#tab/java)

O exemplo a seguir mostra uma função Java que grava uma mensagem contendo a hora atual para um Event Hub.

```java
@FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 */5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
```

Na [biblioteca de runtime de funções Java](/java/api/overview/azure/functions/runtime), use a anotação `@EventHubOutput` em parâmetros cujo valor seria publicado no Hub de Eventos.  O parâmetro deve ser do tipo `OutputBinding<T>`, em que T é um POJO ou qualquer tipo Java nativo.

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C #](#tab/csharp)

Para [bibliotecas de classes de C#](../articles/azure-functions/functions-dotnet-class-library.md), utilize o atributo [EventHubAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubAttribute.cs).

O construtor do atributo usa os nomes do hub de eventos e de uma configuração de aplicativo que contenham a cadeia de caracteres de conexão. Para obter mais informações sobre essas configurações, consulte [Saída - configuração](#configuration). Este é um `EventHub` exemplo de atributo:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Para ver um exemplo completo, consulte [Saída – exemplo de C#](#example).

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

Os atributos não são suportados pelo script C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="python"></a>[Python](#tab/python)

Os atributos não são suportados pelo Python.

# <a name="java"></a>[Java](#tab/java)

Na [biblioteca de tempo de execução de funções Java,](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)use a anotação [EventHubOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) em parâmetros cujo valor seria publicado no Event Hub. O parâmetro deve ser `OutputBinding<T>` do `T` tipo , onde está um POJO ou qualquer tipo java nativo.

---

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de `EventHub` vinculação que você definiu no arquivo *function.json* e no atributo.

|Propriedade function.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**type** | n/d | Deve ser definido como "eventHub". |
|**direction** | n/d | Deve ser definido como "out". Esse parâmetro é definido automaticamente quando você cria a associação no portal do Azure. |
|**name** | n/d | É o nome da variável usada no código da função que representa o evento. |
|**path** |**EventHubName** | Funciona apenas 1. x. O nome do hub de eventos. Quando o nome do hub de eventos também estiver presente na cadeia de conexão, esse valor substitui essa propriedade em runtime. |
|**eventHubName** |**EventHubName** | Funções 2.x e superior. O nome do hub de eventos. Quando o nome do hub de eventos também estiver presente na cadeia de conexão, esse valor substitui essa propriedade em runtime. |
|**Conexão** |**Conexão** | É o nome de uma configuração de aplicativo que contém a cadeia de conexão para o namespace do hub de eventos. Copie essa cadeia de conexão clicando no botão **Informações de Conexão** do *namespace*, não no próprio hub de eventos. Essa cadeia de conexão deve ter permissões de envio para enviar a mensagem à transmissão do evento.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Uso

# <a name="c"></a>[C #](#tab/csharp)

Envie mensagens usando um parâmetro `out string paramName`de método como . No script do C#, `paramName` é o valor especificado na propriedade `name` de *function.json*. Para gravar várias mensagens, você pode usar `ICollector<string>` ou `IAsyncCollector<string>` no lugar de `out string`.

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

Envie mensagens usando um parâmetro `out string paramName`de método como . No script do C#, `paramName` é o valor especificado na propriedade `name` de *function.json*. Para gravar várias mensagens, você pode usar `ICollector<string>` ou `IAsyncCollector<string>` no lugar de `out string`.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Acesse o evento `context.bindings.<name>` de `<name>` saída usando onde `name` está o valor especificado na propriedade de *function.json*.

# <a name="python"></a>[Python](#tab/python)

Existem duas opções para a saída de uma mensagem do Event Hub de uma função:

- **Valor de**retorno `name` : Defina a `$return`propriedade em *function.json* para . Com essa configuração, o valor de retorno da função é persistido como uma mensagem do Event Hub.

- **Imperativo**: Passe um valor para o método [definido](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) do parâmetro declarado como um tipo [out.](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) O valor `set` passado é persistido como uma mensagem do Event Hub.

# <a name="java"></a>[Java](#tab/java)

Existem duas opções para a saída de uma mensagem do Event Hub de uma função usando a anotação [EventHubOutput:](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhuboutput)

- **Valor**de retorno : Ao aplicar a anotação na função em si, o valor de retorno da função é persistido como uma mensagem do Event Hub.

- **Imperativo**: Para definir explicitamente o valor da mensagem, aplique a [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding)anotação a um parâmetro específico do tipo, onde `T` está um POJO ou qualquer tipo java nativo. Com essa configuração, passar `setValue` um valor para o método persiste o valor como uma mensagem do Event Hub.

---

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de retorno

| Associação | Referência |
|---|---|
| Hub de evento | [Guia de Operações](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>configurações de host.json

Esta seção descreve as configurações globais disponíveis para essa vinculação nas versões 2.x ou superior. O arquivo host.json de exemplo abaixo contém apenas as configurações da versão 2.x+ para esta vinculação. Para obter mais informações sobre configurações globais nas versões 2.x e além, consulte [a referência host.json para Funções Azure](../articles/azure-functions/functions-host-json.md).

> [!NOTE]
> Para obter uma referência de host.json no Functions 1.x, confira [Referência de host.json para o Azure Functions 1.x](../articles/azure-functions/functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "eventHubs": {
            "batchCheckpointFrequency": 5,
            "eventProcessorOptions": {
                "maxBatchSize": 256,
                "prefetchCount": 512
            }
        }
    }
}  
```

|Propriedade  |Padrão | Descrição |
|---------|---------|---------|
|`maxBatchSize`|10|A contagem máxima de eventos recebidos por loop de recebimento.|
|`prefetchCount`|300|A contagem padrão de pré-busca usada pelo subjacente `EventProcessorHost`.|
|`batchCheckpointFrequency`|1|O número de lotes de eventos para processar antes de criar um ponto de verificação do cursor do EventHub.|
