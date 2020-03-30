---
title: Associações do Barramento de Serviço para o Azure Functions
description: Aprenda a enviar mensagens de Ônibus de Serviço do Azure das Funções do Azure.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 7e00d03a8b3ec7ef56935ff7714fd932bc343cd3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277434"
---
# <a name="azure-service-bus-output-binding-for-azure-functions"></a>Azure Service Bus vinculação para funções do Azure

Use a associação de saída do barramento de serviço do Azure para enviar mensagens de fila ou de tópico.

Para obter informações sobre detalhes de configuração e configuração, consulte a [visão geral](functions-bindings-service-bus-output.md).

## <a name="example"></a>Exemplo

# <a name="c"></a>[C #](#tab/csharp)

A exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que envia uma mensagem de fila do Barramento de Serviço:

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, ILogger log)
{
    log.LogInformation($"C# function processed: {input.Text}");
    return input.Text;
}
```

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

O exemplo a seguir mostra uma associação de saída de Barramento de Serviço em um arquivo *function.json* e uma [função script C#](functions-reference-csharp.md) que usa a associação. A função usa um gatilho de timer para enviar uma mensagem da fila a cada 15 segundos.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Aqui está o código script C# que cria uma mensagem única:

```cs
public static void Run(TimerInfo myTimer, ILogger log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue = message;
}
```

Este é o código de script C# que cria várias mensagens:

```cs
public static async Task Run(TimerInfo myTimer, ILogger log, IAsyncCollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.LogInformation(message); 
    await outputSbQueue.AddAsync("1 " + message);
    await outputSbQueue.AddAsync("2 " + message);
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

O exemplo a seguir mostra uma associação de saída de Barramento de Serviço em um arquivo *function.json* e uma [função JavaScript C#](functions-reference-node.md) que usa a associação. A função usa um gatilho de timer para enviar uma mensagem da fila a cada 15 segundos.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Aqui está o código de script JavaScript que cria uma mensagem única:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = message;
    context.done();
};
```

Aqui está o código de script JavaScript que cria várias mensagens:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = [];
    context.bindings.outputSbQueue.push("1 " + message);
    context.bindings.outputSbQueue.push("2 " + message);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir demonstra como escrever em uma fila de Ônibus de Serviço em Python.

Uma definição de vinculação do Service Bus é definida `serviceBus`em *function.json* onde o *tipo* é definido como .

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "serviceBus",
      "direction": "out",
      "connection": "AzureServiceBusConnectionString",
      "name": "msg",
      "queueName": "outqueue"
    }
  ]
}
```

Em `set` init *\_.py , você pode escrever uma mensagem para a fila passando um valor para o método. _ \__*

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

O exemplo a seguir mostra uma função Java que `myqueue` envia uma mensagem para uma fila de Barramento de Serviço quando acionada por uma solicitação HTTP.

```java
@FunctionName("httpToServiceBusQueue")
@ServiceBusQueueOutput(name = "message", queueName = "myqueue", connection = "AzureServiceBusConnection")
public String pushToQueue(
  @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
  final String message,
  @HttpOutput(name = "response") final OutputBinding<T> result ) {
      result.setValue(message + " has been sent.");
      return message;
 }
```

 Na [biblioteca de runtime das funções Java](/java/api/overview/azure/functions/runtime), use a anotação `@QueueOutput` nos parâmetros da função cujo valor poderia ser gravado em uma fila do Barramento de Serviço.  O tipo de parâmetro deve ser `OutputBinding<T>`, onde T é qualquer tipo Java nativo de um POJO.

As funções Java também podem escrever para um tópico de Service Bus. O exemplo a `@ServiceBusTopicOutput` seguir usa a anotação para descrever a configuração da vinculação de saída. 

```java
@FunctionName("sbtopicsend")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @ServiceBusTopicOutput(name = "message", topicName = "mytopicname", subscriptionName = "mysubscription", connection = "ServiceBusConnection") OutputBinding<String> message,
            final ExecutionContext context) {
        
        String name = request.getBody().orElse("Azure Functions");

        message.setValue(name);
        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
        
    }
```

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C #](#tab/csharp)

Em [bibliotecas de classes do C#](functions-dotnet-class-library.md), use o [ServiceBusAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs).

O construtor do atributo usa o nome da fila ou o tópico e a assinatura. Você também pode especificar os direitos de acesso da conexão. Há uma explicação sobre como escolher as configuração de direitos de acesso na seção [Saída - Configuração](#configuration). Aqui está um exemplo que mostra o atributo aplicado ao valor retornado da função:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Você pode `Connection` definir a propriedade para especificar o nome de uma configuração de aplicativo que contém a seqüência de conexão Service Bus a ser usada, conforme mostrado no exemplo a seguir:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Para um exemplo completo, consulte [Saída - exemplo](#example).

Você pode usar o atributo `ServiceBusAccount` para especificar a conta do Barramento de Serviço ao nível da classe, do método ou do parâmetro.  Para obter mais informações, consulte [Trigger - atributos](functions-bindings-service-bus-trigger.md#attributes-and-annotations).

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

Os atributos não são suportados pelo script C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="python"></a>[Python](#tab/python)

Os atributos não são suportados pelo Python.

# <a name="java"></a>[Java](#tab/java)

As `ServiceBusQueueOutput` `ServiceBusTopicOutput` anotações estão disponíveis para escrever uma mensagem como saída de função. O parâmetro decorado com essas anotações deve ser `OutputBinding<T>` `T` declarado como um tipo correspondente ao tipo da mensagem.

---

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de `ServiceBus` vinculação que você definiu no arquivo *function.json* e no atributo.

|Propriedade function.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**type** | n/d | Deve ser definido como "serviceBus". Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure.|
|**direction** | n/d | Deve ser definido como "out". Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure. |
|**name** | n/d | O nome da variável que representa a fila ou mensagem de tópico no código de função. Definido como "$return" para referenciar o valor de retorno da função. |
|**queueName**|**Queuename**|Nome da fila.  Defina somente se for enviar mensagens da fila, não para um tópico.
|**topicName**|**Nome do tópico**|Nome do tópico. Defina somente se for enviar mensagens do tópico, não para uma fila.|
|**Conexão**|**Conexão**|O nome de uma configuração de aplicativo que contém uma cadeia de conexão de Barramento de Serviço para usar para essa associação. Se o nome de configuração do aplicativo começar com "AzureWebJobs", você pode especificar apenas o resto do nome. Por exemplo, se `connection` você definir como "MyServiceBus", o tempo de execução functions procurará uma configuração de aplicativo chamada "AzureWebJobsMyServiceBus". Se você deixar `connection` vazio, o runtime de Functions usa a cadeia de caracteres de conexão do Barramento de serviço na configuração de aplicativo chamada "AzureWebJobsServiceBus".<br><br>Para obter uma cadeia de conexão, siga as etapas mostradas em [Obter as credenciais de gerenciamento](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). A cadeia de conexão deve ser voltada para um namespace do Barramento de Serviço, não limitada a uma fila ou tópico específico.|
|**Accessrights**|**Acesso**|Direitos de acesso para a cadeia de caracteres de conexão. Os valores disponíveis são `manage` e `listen`. O padrão é `manage`, que indica que o `connection` tem a permissão **Gerenciar**. Se você usar uma cadeia de conexão que não tenha a permissão **Gerenciar**, defina `accessRights` como "escutar". Caso contrário, o runtime do Functions talvez falhe ao tentar executar operações que exigem o gerenciamento de direitos. Na versão 2.x do Azure Functions ou superior, esta propriedade não está disponível porque a versão mais recente do Service Bus SDK não suporta operações de gerenciamento.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Uso

No Azure Functions 1. x, o runtime criará a fila se ela não existir e você tiver definido `accessRights` como `manage`. Em Funções versão 2.x e superior, a fila ou tópico já deve existir; se você especificar uma fila ou tópico que não existe, a função falhará. 

# <a name="c"></a>[C #](#tab/csharp)

Use os seguintes tipos de parâmetros para a vinculação da saída:

* `out T paramName` - `T` pode ser qualquer tipo serializável em JSON. Se o valor do parâmetro for nulo quando a função existir, o Functions criará a mensagem com um objeto nulo.
* `out string` - Se o valor de parâmetro não for nulo quando a função sair, o Functions criará uma mensagem.
* `out byte[]` - Se o valor de parâmetro não for nulo quando a função sair, o Functions criará uma mensagem.
* `out BrokeredMessage`- Se o valor do parâmetro for nulo quando a função sair, as funções não criarão uma mensagem (para funções 1.x)
* `out Message`- Se o valor do parâmetro for nulo quando a função sair, as funções não criarão uma mensagem (para funções 2.x ou superior)
* `ICollector<T>` ou `IAsyncCollector<T>` - Para a criação de várias mensagens. Uma mensagem é criada quando você chama o método `Add` .

Ao trabalhar com funções C#:

* As funções assincronias precisam de um valor de retorno ou `IAsyncCollector` em vez de um `out` parâmetro.

* Para acessar o ID da [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) sessão, `sessionId` vincule a um tipo e use a propriedade.

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

Use os seguintes tipos de parâmetros para a vinculação da saída:

* `out T paramName` - `T` pode ser qualquer tipo serializável em JSON. Se o valor do parâmetro for nulo quando a função existir, o Functions criará a mensagem com um objeto nulo.
* `out string` - Se o valor de parâmetro não for nulo quando a função sair, o Functions criará uma mensagem.
* `out byte[]` - Se o valor de parâmetro não for nulo quando a função sair, o Functions criará uma mensagem.
* `out BrokeredMessage`- Se o valor do parâmetro for nulo quando a função sair, as funções não criarão uma mensagem (para funções 1.x)
* `out Message`- Se o valor do parâmetro for nulo quando a função sair, as funções não criarão uma mensagem (para funções 2.x ou superior)
* `ICollector<T>` ou `IAsyncCollector<T>` - Para a criação de várias mensagens. Uma mensagem é criada quando você chama o método `Add` .

Ao trabalhar com funções C#:

* As funções assincronias precisam de um valor de retorno ou `IAsyncCollector` em vez de um `out` parâmetro.

* Para acessar o ID da [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) sessão, `sessionId` vincule a um tipo e use a propriedade.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Acesse a fila ou `context.bindings.<name from function.json>`tópico usando . Você pode atribuir uma seqüência, uma matriz de bytes ou um `context.binding.<name>`objeto JavaScript (desserializado em JSON) para .

# <a name="python"></a>[Python](#tab/python)

Use o [SDK do Ônibus de Serviço Do Azure](https://docs.microsoft.com/azure/service-bus-messaging) em vez da vinculação de saída incorporada.

# <a name="java"></a>[Java](#tab/java)

Use o [SDK do Ônibus de Serviço Do Azure](https://docs.microsoft.com/azure/service-bus-messaging) em vez da vinculação de saída incorporada.

---

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de retorno

| Associação | Referência |
|---|---|
| Barramento de Serviço | [Códigos de erro do Barramento de Serviço do Microsoft Azure](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Barramento de Serviço | [Limites do Barramento de Serviço do Microsoft Azure](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>configurações de host.json

Esta seção descreve as configurações globais disponíveis para essa vinculação nas versões 2.x ou superior. O arquivo host.json de exemplo abaixo contém apenas as configurações para esta vinculação. Para obter mais informações sobre as configurações globais, consulte [a referência host.json para a versão Funções do Azure](functions-host-json.md).

> [!NOTE]
> Para obter uma referência de host.json no Functions 1.x, confira [Referência de host.json para o Azure Functions 1.x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "serviceBus": {
            "prefetchCount": 100,
            "messageHandlerOptions": {
                "autoComplete": false,
                "maxConcurrentCalls": 32,
                "maxAutoRenewDuration": "00:55:00"
            },
            "sessionHandlerOptions": {
                "autoComplete": false,
                "messageWaitTimeout": "00:00:30",
                "maxAutoRenewDuration": "00:55:00",
                "maxConcurrentSessions": 16
            }
        }
    }
}
```

|Propriedade  |Padrão | Descrição |
|---------|---------|---------|
|maxAutoRenewDuration|00:05:00|A duração máxima na qual o bloqueio de mensagem será renovado automaticamente.|
|autoComplete|true|Se o gatilho deve marcar imediatamente a mensagem como completa (autocompletar) ou esperar que a função saia com sucesso para chamar concluído.|
|maxConcurrentCalls|16|O número máximo de chamadas simultâneas para o retorno de chamada que a bomba de mensagens deve iniciar. Por padrão, o runtime do Functions processa várias mensagens simultaneamente. Para direcionar o runtime para processar uma única fila ou mensagem de tópico de cada vez, defina `maxConcurrentCalls` como 1. |

## <a name="next-steps"></a>Próximas etapas

- [Execute uma função quando uma fila de barra de serviço ou mensagem de tópico for criada (Trigger)](./functions-bindings-service-bus-trigger.md)
