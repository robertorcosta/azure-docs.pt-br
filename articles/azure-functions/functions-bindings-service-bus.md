---
title: Associações do Barramento de Serviço para o Azure Functions
description: Entenda como usar gatilhos e associações do Barramento de Serviço do Azure no Azure Functions.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 04/01/2017
ms.author: cshoe
ms.openlocfilehash: 424d797410c091dc53687284c2b32e2f1f0358e1
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76549063"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Associações do Barramento de Serviço para o Azure Functions

Este artigo explica como trabalhar com associações do Barramento de Serviço do Azure no Azure Functions. O Azure Functions dá suporte a gatilhos e a associações de saída para filas e tópicos do Barramento de Serviço.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pacotes - Functions 1. x

As associações do barramento de serviço são fornecidas no [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) pacote NuGet, versão 2. x. 

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Pacotes-funções 2. x e superior

As associações do Barramento de Serviços são fornecidas no pacote NuGet [Microsoft.Azure.WebJobs.Extensions.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus), versão 3.x. O código-fonte do pacote está no repositório GitHub [Azure-Functions-ServiceBus-Extension](https://github.com/Azure/azure-functions-servicebus-extension) .

> [!NOTE]
> As versões 2. x e superiores não criam o tópico ou a assinatura configurada na instância de `ServiceBusTrigger`. Essas versões são baseadas em [Microsoft. Azure. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) , que não lida com o gerenciamento de filas.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Gatilho

Use o gatilho do Barramento de Serviço para responder às mensagens de uma fila ou tópico do Barramento de Serviço. 

## <a name="trigger---example"></a>Gatilho - exemplo

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

A exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que lê [metadados de mensagem](#trigger---message-metadata) e registra uma mensagem de fila do Barramento de Serviço:

```cs
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
    string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    ILogger log)
{
    log.LogInformation($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"DeliveryCount={deliveryCount}");
    log.LogInformation($"MessageId={messageId}");
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

O exemplo a seguir mostra uma associação de gatilho de Barramento de Serviço em um arquivo *function.json* e uma [função C# script](functions-reference-csharp.md) que usa a associação. A função lê [metadados de mensagem](#trigger---message-metadata) e registra uma mensagem de fila do Barramento de Serviço do Microsoft Azure.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Aqui está o código de script do C#:

```cs
using System;

public static void Run(string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"DeliveryCount={deliveryCount}");
    log.Info($"MessageId={messageId}");
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra uma associação de gatilho de Barramento de Serviço em um arquivo *function.json* e uma [função JavaScript](functions-reference-node.md) que usa a associação. A função lê [metadados de mensagem](#trigger---message-metadata) e registra uma mensagem de fila do Barramento de Serviço do Microsoft Azure. 

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Este é o código do script do JavaScript:

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('DeliveryCount =', context.bindingData.deliveryCount);
    context.log('MessageId =', context.bindingData.messageId);
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

O exemplo a seguir demonstra como ler uma mensagem da fila do barramento de serviço por meio de um gatilho.

Uma associação do barramento de serviço é definida em *Function. JSON* , em que *Type* é definido como `serviceBusTrigger`.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "msg",
      "type": "serviceBusTrigger",
      "direction": "in",
      "queueName": "inputqueue",
      "connection": "AzureServiceBusConnectionString"
    }
  ]
}
```

O código em  *_\_init_\_. py* declara um parâmetro como `func.ServiceBusMessage`, o que permite que você leia a mensagem da fila em sua função.

```python
import azure.functions as func

import logging
import json

def main(msg: func.ServiceBusMessage):
    logging.info('Python ServiceBus queue trigger processed message.')

    result = json.dumps({
        'message_id': msg.message_id,
        'body': msg.get_body().decode('utf-8'),
        'content_type': msg.content_type,
        'expiration_time': msg.expiration_time,
        'label': msg.label,
        'partition_key': msg.partition_key,
        'reply_to': msg.reply_to,
        'reply_to_session_id': msg.reply_to_session_id,
        'scheduled_enqueue_time': msg.scheduled_enqueue_time,
        'session_id': msg.session_id,
        'time_to_live': msg.time_to_live,
        'to': msg.to,
        'user_properties': msg.user_properties,
    })

    logging.info(result)
```

# <a name="javatabjava"></a>[Java](#tab/java)

A função Java a seguir usa a anotação `@ServiceBusQueueTrigger` da [biblioteca de tempo de execução de funções Java](/java/api/overview/azure/functions/runtime) para descrever a configuração de um gatilho de fila do barramento de serviço. A função captura a mensagem colocada na fila e a adiciona aos logs.

```java
@FunctionName("sbprocessor")
 public void serviceBusProcess(
    @ServiceBusQueueTrigger(name = "msg",
                             queueName = "myqueuename",
                             connection = "myconnvarname") String message,
   final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
```

As funções Java também podem ser disparadas quando uma mensagem é adicionada a um tópico do barramento de serviço. O exemplo a seguir usa a anotação `@ServiceBusTopicTrigger` para descrever a configuração do gatilho.

```java
@FunctionName("sbtopicprocessor")
    public void run(
        @ServiceBusTopicTrigger(
            name = "message",
            topicName = "mytopicname",
            subscriptionName = "mysubscription",
            connection = "ServiceBusConnection"
        ) String message,
        final ExecutionContext context
    ) {
        context.getLogger().info(message);
    }
```

---

## <a name="trigger---attributes-and-annotations"></a>Gatilho-atributos e anotações

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Em [bibliotecas de classes C#](functions-dotnet-class-library.md), use os seguintes atributos para configurar um gatilho do Barramento de Serviço:

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  O construtor do atributo usa o nome da fila ou o tópico e a assinatura. In Azure Functions versão 1.x, você também pode especificar os direitos de acesso da conexão. Se você não especificar os direitos de acesso, o padrão é `Manage`. Para obter mais informações, consulte a seção [Gatilho - configuração](#trigger---configuration).

  Aqui está um exemplo que mostra o atributo usado com um parâmetro de cadeia de caracteres:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Você pode definir a propriedade `Connection` para especificar o nome de uma configuração de aplicativo que contém a cadeia de conexão do barramento de serviço a ser usada, conforme mostrado no exemplo a seguir:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Para obter um exemplo completo, consulte [gatilho-exemplo](#trigger---example).

* [ServiceBusAccountAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAccountAttribute.cs)

  Oferece uma maneira de especificar a conta de Barramento de Serviço para usar. O construtor toma o nome de uma configuração de aplicativo que contenha uma cadeia de conexão de Barramento de Serviço. O atributo pode ser aplicado no nível de classe, método ou parâmetro. O exemplo a seguir mostra o nível de classe e método:

  ```csharp
  [ServiceBusAccount("ClassLevelServiceBusAppSetting")]
  public static class AzureFunctions
  {
      [ServiceBusAccount("MethodLevelServiceBusAppSetting")]
      [FunctionName("ServiceBusQueueTriggerCSharp")]
      public static void Run(
          [ServiceBusTrigger("myqueue", AccessRights.Manage)] 
          string myQueueItem, ILogger log)
  {
      ...
  }
  ```

A conta de Barramento de Serviço a ser usada é determinada na seguinte ordem:

* A propriedade `ServiceBusTrigger` do atributo`Connection`.
* O `ServiceBusAccount` atributo aplicado ao mesmo parâmetro do `ServiceBusTrigger` atributo.
* O `ServiceBusAccount` atributo aplicado à função.
* O `ServiceBusAccount` atributo aplicado à classe.
* A configuração de aplicativo "AzureWebJobsServiceBus".

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

O script não dá suporte C# a atributos.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Não há suporte para atributos pelo JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Não há suporte para atributos no Python.

# <a name="javatabjava"></a>[Java](#tab/java)

A anotação `ServiceBusQueueTrigger` permite que você crie uma função que é executada quando uma mensagem de fila do barramento de serviço é criada. As opções de configuração disponíveis incluem nome da fila e nome da cadeia de conexão.

A anotação `ServiceBusTopicTrigger` permite designar um tópico e uma assinatura para direcionar quais dados disparam a função.

Consulte o [exemplo](#trigger---example) de gatilho para obter mais detalhes.

---

## <a name="trigger---configuration"></a>Gatilho – configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no atributo `ServiceBusTrigger`.

|Propriedade function.json | Propriedade de atributo |Description|
|---------|---------|----------------------|
|**tipo** | N/D | Deve ser definido como "serviceBusTrigger". Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure.|
|**direction** | N/D | Deve ser definido como "in". Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure. |
|**name** | N/D | O nome da variável que representa a fila ou mensagem de tópico no código de função. |
|**queueName**|**QueueName**|Nome da fila a ser monitorada.  Defina somente se for monitorar uma fila, não para um tópico.
|**topicName**|**TopicName**|Nome do tópico a ser monitorado. Defina somente se for monitorar um tópico, não uma fila.|
|**subscriptionName**|**SubscriptionName**|Nome da assinatura a ser monitorada. Defina somente se for monitorar um tópico, não uma fila.|
|**conexão**|**Conexão**|O nome de uma configuração de aplicativo que contém uma cadeia de conexão de Barramento de Serviço para usar para essa associação. Se o nome de configuração do aplicativo começar com "AzureWebJobs", você pode especificar apenas o resto do nome. Por exemplo, se você configurar `connection` para "MyServiceBus", o runtime do Functions procura por uma configuração de aplicativo que esteja nomeada "AzureWebJobsMyServiceBus." Se você deixar `connection` vazio, o runtime de Functions usa a cadeia de caracteres de conexão do Barramento de serviço na configuração de aplicativo chamada "AzureWebJobsServiceBus".<br><br>Para obter uma cadeia de conexão, siga as etapas mostradas em [Obter as credenciais de gerenciamento](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). A cadeia de conexão deve ser voltada para um namespace do Barramento de Serviço, não limitada a uma fila ou tópico específico. |
|**accessRights**|**Access**|Direitos de acesso para a cadeia de caracteres de conexão. Os valores disponíveis são `manage` e `listen`. O padrão é `manage`, que indica que o `connection` tem a permissão **Gerenciar**. Se você usar uma cadeia de conexão que não tenha a permissão **Gerenciar**, defina `accessRights` como "escutar". Caso contrário, o runtime do Functions talvez falhe ao tentar executar operações que exigem o gerenciamento de direitos. No Azure Functions versão 2. x e superior, essa propriedade não está disponível porque a versão mais recente do SDK do barramento de serviço não dá suporte a operações de gerenciamento.|
|**isSessionsEnabled**|**IsSessionsEnabled**|`true` se estiver se conectando a uma fila ou assinatura [com reconhecimento de sessão](../service-bus-messaging/message-sessions.md) . `false` caso contrário, que é o valor padrão.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Gatilho - uso

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Os seguintes tipos de parâmetro estão disponíveis para a mensagem de fila ou tópico:

* `string` -Se a mensagem for de texto.
* `byte[]` - Útil para dados binários.
* Um tipo personalizado - Se a mensagem contiver JSON, funções do Azure tentará desserializar os dados JSON.
* `BrokeredMessage` – fornece a você a mensagem desserializada com o método [BrokeredMessage. GetBody\<t > ()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) .

Esses tipos de parâmetro são para Azure Functions versão 1. x; para 2. x e superior, use [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) em vez de `BrokeredMessage`.

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

Os seguintes tipos de parâmetro estão disponíveis para a mensagem de fila ou tópico:

* `string` -Se a mensagem for de texto.
* `byte[]` - Útil para dados binários.
* Um tipo personalizado - Se a mensagem contiver JSON, funções do Azure tentará desserializar os dados JSON.
* `BrokeredMessage` – fornece a você a mensagem desserializada com o método [BrokeredMessage. GetBody\<t > ()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) .

Esses parâmetros são para Azure Functions versão 1. x; para 2. x e superior, use [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) em vez de `BrokeredMessage`.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Acesse a mensagem da fila ou do tópico usando `context.bindings.<name from function.json>`. A mensagem do Barramento de Serviço é passada em uma função como uma cadeia de caracteres ou um objeto JSON.

# <a name="pythontabpython"></a>[Python](#tab/python)

A mensagem da fila está disponível para a função por meio de um parâmetro digitado como `func.ServiceBusMessage`. A mensagem do Barramento de Serviço é passada em uma função como uma cadeia de caracteres ou um objeto JSON.

# <a name="javatabjava"></a>[Java](#tab/java)

A mensagem do barramento de serviço de entrada está disponível por meio de um parâmetro `ServiceBusQueueMessage` ou `ServiceBusTopicMessage`.

[Consulte o exemplo para obter detalhes](#trigger).

---

## <a name="trigger---poison-messages"></a>Gatilho - mensagens suspeitas

A manipulação de mensagens suspeitas não pode ser controlada ou configurada no Azure Functions. O barramento de serviço ele mesmo lida com as mensagens suspeitas.

## <a name="trigger---peeklock-behavior"></a>Gatilho - Comportamento de PeekLock

O runtime do Functions recebe uma mensagem no [Modo PeekLock](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). Ele chama `Complete` na mensagem se a função for concluída com êxito, ou chama `Abandon` se a função falhar. Se a função for executada por mais tempo que o limite `PeekLock`, o bloqueio é renovado automaticamente. 

O `maxAutoRenewDuration` pode ser configurado no *host.json*, que mapeia para [OnMessageOptions.MaxAutoRenewDuration](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet). O máximo permitido para essa configuração é 5 minutos de acordo com a documentação do Barramento de Serviço do Microsoft Azure, enquanto você pode aumentar o limite de tempo de funções do padrão de 5 minutos para 10 minutos. Para funções de Barramento de Serviço você não irá fazer isso, porque excederia o limite de renovação do Barramento de Serviço.

## <a name="trigger---message-metadata"></a>Gatilho - metadados da mensagem

O gatilho Barramento de Serviço fornece várias propriedades de [metadados](./functions-bindings-expressions-patterns.md#trigger-metadata). Essas propriedades podem ser usadas como parte de expressões de associação em outras associações ou como parâmetros em seu código. Essas são propriedades da classe [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).

|Propriedade|Tipo|Description|
|--------|----|-----------|
|`DeliveryCount`|`Int32`|Número total de entregas.|
|`DeadLetterSource`|`string`|A origem de mensagens mortas.|
|`ExpiresAtUtc`|`DateTime`|Tempo de expiração em UTC.|
|`EnqueuedTimeUtc`|`DateTime`|O tempo de enfileiramento no UTC.|
|`MessageId`|`string`|Um valor definido pelo usuário que o Barramento de Serviço pode usar para identificar mensagens duplicadas, se habilitado.|
|`ContentType`|`string`|Um identificador de tipo de conteúdo utilizado pelo remetente e destinatário específico para lógica de aplicativo.|
|`ReplyTo`|`string`|A resposta para o endereço da fila.|
|`SequenceNumber`|`Int64`|Número exclusivo atribuído a uma mensagem pelo Barramento de Serviço.|
|`To`|`string`|Enviar para o endereço.|
|`Label`|`string`|O rótulo específico do aplicativo.|
|`CorrelationId`|`string`|ID de correlação.|

> [!NOTE]
> Atualmente, o gatilho do barramento de serviço que funciona com filas e assinaturas habilitadas para sessão está em versão prévia. Acompanhe [este item](https://github.com/Azure/azure-webjobs-sdk/issues/529#issuecomment-491113458) para obter atualizações adicionais sobre isso. 

Consulte [exemplos de código](#trigger---example) que usam essas propriedades neste artigo.

## <a name="output"></a>Saída

Use a associação de saída do barramento de serviço do Azure para enviar mensagens de fila ou de tópico.

### <a name="output---example"></a>Saída - exemplo

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

# <a name="pythontabpython"></a>[Python](#tab/python)

O exemplo a seguir demonstra como gravar em uma fila do barramento de serviço em Python.

Uma definição de associação do barramento de serviço é definida em *Function. JSON* , em que *Type* é definido como `serviceBus`.

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

Em  *_\_init_\_. py*, você pode gravar uma mensagem na fila passando um valor para o método `set`.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

# <a name="javatabjava"></a>[Java](#tab/java)

O exemplo a seguir mostra uma função Java que envia uma mensagem a uma fila `myqueue` do Barramento de Serviço quando disparada por uma solicitação HTTP.

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

As funções Java também podem gravar em um tópico do barramento de serviço. O exemplo a seguir usa a anotação `@ServiceBusTopicOutput` para descrever a configuração da Associação de saída. 

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

## <a name="output---attributes-and-annotations"></a>Saída-atributos e anotações

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Em [bibliotecas de classes do C#](functions-dotnet-class-library.md), use o [ServiceBusAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs).

O construtor do atributo usa o nome da fila ou o tópico e a assinatura. Você também pode especificar os direitos de acesso da conexão. Há uma explicação sobre como escolher as configuração de direitos de acesso na seção [Saída - Configuração](#output---configuration). Aqui está um exemplo que mostra o atributo aplicado ao valor retornado da função:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Você pode definir a propriedade `Connection` para especificar o nome de uma configuração de aplicativo que contém a cadeia de conexão do barramento de serviço a ser usada, conforme mostrado no exemplo a seguir:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Para obter um exemplo completo, consulte [saída-exemplo](#output---example).

Você pode usar o atributo `ServiceBusAccount` para especificar a conta do Barramento de Serviço ao nível da classe, do método ou do parâmetro.  Para obter mais informações, consulte [Gatilho - atributos](#trigger---attributes-and-annotations).

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

O script não dá suporte C# a atributos.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Não há suporte para atributos pelo JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Não há suporte para atributos no Python.

# <a name="javatabjava"></a>[Java](#tab/java)

As anotações `ServiceBusQueueOutput` e `ServiceBusTopicOutput` estão disponíveis para gravar uma mensagem como uma saída de função. O parâmetro decorado com essas anotações deve ser declarado como um `OutputBinding<T>` onde `T` é o tipo correspondente ao tipo da mensagem.

---

## <a name="output---configuration"></a>Saída - configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no atributo `ServiceBus`.

|Propriedade function.json | Propriedade de atributo |Description|
|---------|---------|----------------------|
|**tipo** | N/D | Deve ser definido como "serviceBus". Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure.|
|**direction** | N/D | Deve ser definido como "out". Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure. |
|**name** | N/D | O nome da variável que representa a fila ou mensagem de tópico no código de função. Definido como "$return" para referenciar o valor de retorno da função. |
|**queueName**|**QueueName**|Nome da fila.  Defina somente se for enviar mensagens da fila, não para um tópico.
|**topicName**|**TopicName**|Nome do tópico. Defina somente se for enviar mensagens do tópico, não para uma fila.|
|**conexão**|**Conexão**|O nome de uma configuração de aplicativo que contém uma cadeia de conexão de Barramento de Serviço para usar para essa associação. Se o nome de configuração do aplicativo começar com "AzureWebJobs", você pode especificar apenas o resto do nome. Por exemplo, se você configurar `connection` para "MyServiceBus", o runtime do Functions procura por uma configuração de aplicativo que esteja nomeada "AzureWebJobsMyServiceBus." Se você deixar `connection` vazio, o runtime de Functions usa a cadeia de caracteres de conexão do Barramento de serviço na configuração de aplicativo chamada "AzureWebJobsServiceBus".<br><br>Para obter uma cadeia de conexão, siga as etapas mostradas em [Obter as credenciais de gerenciamento](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). A cadeia de conexão deve ser voltada para um namespace do Barramento de Serviço, não limitada a uma fila ou tópico específico.|
|**accessRights**|**Access**|Direitos de acesso para a cadeia de caracteres de conexão. Os valores disponíveis são `manage` e `listen`. O padrão é `manage`, que indica que o `connection` tem a permissão **Gerenciar**. Se você usar uma cadeia de conexão que não tenha a permissão **Gerenciar**, defina `accessRights` como "escutar". Caso contrário, o runtime do Functions talvez falhe ao tentar executar operações que exigem o gerenciamento de direitos. No Azure Functions versão 2. x e superior, essa propriedade não está disponível porque a versão mais recente do SDK do barramento de serviço não dá suporte a operações de gerenciamento.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Saída - uso

No Azure Functions 1. x, o runtime criará a fila se ela não existir e você tiver definido `accessRights` como `manage`. Nas funções versão 2. x e superior, a fila ou o tópico já deve existir; Se você especificar uma fila ou um tópico que não existe, a função falhará. 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Use os seguintes tipos de parâmetro para a associação de saída:

* `out T paramName` - `T` pode ser qualquer tipo serializável em JSON. Se o valor do parâmetro for nulo quando a função existir, o Functions criará a mensagem com um objeto nulo.
* `out string` - Se o valor de parâmetro não for nulo quando a função sair, o Functions criará uma mensagem.
* `out byte[]` - Se o valor de parâmetro não for nulo quando a função sair, o Functions criará uma mensagem.
* `out BrokeredMessage`-se o valor do parâmetro for nulo quando a função for encerrada, as funções não criarão uma mensagem (para as funções 1. x)
* `out Message`-se o valor do parâmetro for nulo quando a função for encerrada, as funções não criarão uma mensagem (para as funções 2. x e superior)
* `ICollector<T>` ou `IAsyncCollector<T>` - Para a criação de várias mensagens. Uma mensagem é criada quando você chama o método `Add` .

Ao trabalhar com C# funções:

* As funções assíncronas precisam de um valor de retorno ou `IAsyncCollector` em vez de um parâmetro `out`.

* Para acessar a ID da sessão, associe a um tipo de [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) e use a propriedade `sessionId`.

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

Use os seguintes tipos de parâmetro para a associação de saída:

* `out T paramName` - `T` pode ser qualquer tipo serializável em JSON. Se o valor do parâmetro for nulo quando a função existir, o Functions criará a mensagem com um objeto nulo.
* `out string` - Se o valor de parâmetro não for nulo quando a função sair, o Functions criará uma mensagem.
* `out byte[]` - Se o valor de parâmetro não for nulo quando a função sair, o Functions criará uma mensagem.
* `out BrokeredMessage`-se o valor do parâmetro for nulo quando a função for encerrada, as funções não criarão uma mensagem (para as funções 1. x)
* `out Message`-se o valor do parâmetro for nulo quando a função for encerrada, as funções não criarão uma mensagem (para as funções 2. x e superior)
* `ICollector<T>` ou `IAsyncCollector<T>` - Para a criação de várias mensagens. Uma mensagem é criada quando você chama o método `Add` .

Ao trabalhar com C# funções:

* As funções assíncronas precisam de um valor de retorno ou `IAsyncCollector` em vez de um parâmetro `out`.

* Para acessar a ID da sessão, associe a um tipo de [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) e use a propriedade `sessionId`.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Acesse a fila ou o tópico usando `context.bindings.<name from function.json>`. Você pode atribuir uma cadeia de caracteres, uma matriz de bytes ou um objeto JavaScript (desserializado em JSON) para `context.binding.<name>`.

# <a name="pythontabpython"></a>[Python](#tab/python)

Use o [SDK do barramento de serviço do Azure](https://docs.microsoft.com/azure/service-bus-messaging) em vez da Associação de saída interna.

# <a name="javatabjava"></a>[Java](#tab/java)

Use o [SDK do barramento de serviço do Azure](https://docs.microsoft.com/azure/service-bus-messaging) em vez da Associação de saída interna.

---

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de retorno

| Associação | Referência |
|---|---|
| Service Bus | [Códigos de erro do Barramento de Serviço do Microsoft Azure](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Service Bus | [Limites do Barramento de Serviço do Microsoft Azure](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>configurações de host.json

Esta seção descreve as definições de configuração global disponíveis para essa associação nas versões 2. x e superior. O arquivo host. JSON de exemplo abaixo contém apenas as configurações para essa associação. Para obter mais informações sobre definições de configuração global, consulte [referência de host. JSON para Azure Functions versão](functions-host-json.md).

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

|Propriedade  |Padrão | Description |
|---------|---------|---------|
|maxAutoRenewDuration|00:05:00|A duração máxima na qual o bloqueio de mensagem será renovado automaticamente.|
|autoComplete|true|Se o gatilho deve marcar imediatamente a mensagem como concluída (AutoCompletar) ou aguardar até que a função seja encerrada com êxito para chamar a conclusão.|
|maxConcurrentCalls|16|O número máximo de chamadas simultâneas para o retorno de chamada que a bomba de mensagens deve iniciar. Por padrão, o runtime do Functions processa várias mensagens simultaneamente. Para direcionar o runtime para processar uma única fila ou mensagem de tópico de cada vez, defina `maxConcurrentCalls` como 1. |

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Aprenda mais sobre gatilhos e de associações do Azure Functions](functions-triggers-bindings.md)
