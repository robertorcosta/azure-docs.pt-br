---
title: Associações do Barramento de Serviço para o Azure Functions
description: Aprenda a executar uma função Azure quando as mensagens de barramento de serviço do Azure forem criadas.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 1ead7fcd9d474369e3a62e372a971d88d26f4e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273561"
---
# <a name="azure-service-bus-trigger-for-azure-functions"></a>Gatilho do ônibus de serviço do Azure para funções do Azure

Use o gatilho do Barramento de Serviço para responder às mensagens de uma fila ou tópico do Barramento de Serviço.

Para obter informações sobre detalhes de configuração e configuração, consulte a [visão geral](functions-bindings-service-bus-output.md).

## <a name="example"></a>Exemplo

# <a name="c"></a>[C #](#tab/csharp)

A exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que lê [metadados de mensagem](#message-metadata) e registra uma mensagem de fila do Barramento de Serviço:

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

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

O exemplo a seguir mostra uma associação de gatilho de Barramento de Serviço em um arquivo *function.json* e uma [função C# script](functions-reference-csharp.md) que usa a associação. A função lê [metadados de mensagem](#message-metadata) e registra uma mensagem de fila do Barramento de Serviço do Microsoft Azure.

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

O exemplo a seguir mostra uma associação de gatilho de Barramento de Serviço em um arquivo *function.json* e uma [função JavaScript](functions-reference-node.md) que usa a associação. A função lê [metadados de mensagem](#message-metadata) e registra uma mensagem de fila do Barramento de Serviço do Microsoft Azure. 

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

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir demonstra como ler uma mensagem de fila de ônibus de serviço através de um gatilho.

Uma vinculação de barramento de serviço é definida `serviceBusTrigger`em *function.json* onde o *tipo* é definido como .

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

O código * _ \_init_\_.py* declara um `func.ServiceBusMessage`parâmetro como , o que permite que você leia a mensagem de fila em sua função.

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

# <a name="java"></a>[Java](#tab/java)

A função Java `@ServiceBusQueueTrigger` a seguir usa a anotação da biblioteca de [tempo de execução das funções Java](/java/api/overview/azure/functions/runtime) para descrever a configuração de um gatilho de fila de barramento de serviço. A função pega a mensagem colocada na fila e a adiciona aos registros.

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

As funções Java também podem ser acionadas quando uma mensagem é adicionada a um tópico de Barra de Serviço. O exemplo a `@ServiceBusTopicTrigger` seguir usa a anotação para descrever a configuração do gatilho.

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

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C #](#tab/csharp)

Em [bibliotecas de classes C#](functions-dotnet-class-library.md), use os seguintes atributos para configurar um gatilho do Barramento de Serviço:

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  O construtor do atributo usa o nome da fila ou o tópico e a assinatura. In Azure Functions versão 1.x, você também pode especificar os direitos de acesso da conexão. Se você não especificar os direitos de acesso, o padrão é `Manage`. Para obter mais informações, consulte a seção [Gatilho - configuração](#configuration).

  Aqui está um exemplo que mostra o atributo usado com um parâmetro de cadeia de caracteres:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Você pode `Connection` definir a propriedade para especificar o nome de uma configuração de aplicativo que contém a seqüência de conexão Service Bus a ser usada, conforme mostrado no exemplo a seguir:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Para um exemplo completo, consulte [Trigger - exemplo](#example).

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

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

Os atributos não são suportados pelo script C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="python"></a>[Python](#tab/python)

Os atributos não são suportados pelo Python.

# <a name="java"></a>[Java](#tab/java)

A `ServiceBusQueueTrigger` anotação permite criar uma função que é executada quando uma mensagem de fila de Barramento de Serviço é criada. As opções de configuração disponíveis incluem nome da fila e nome da seqüência de seqüência de conexão.

A `ServiceBusTopicTrigger` anotação permite designar um tópico e uma assinatura para direcionar quais dados desencadeiam a função.

Consulte o [exemplo](#example) do gatilho para obter mais detalhes.

---

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de `ServiceBusTrigger` vinculação que você definiu no arquivo *function.json* e no atributo.

|Propriedade function.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**type** | n/d | Deve ser definido como "serviceBusTrigger". Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure.|
|**direction** | n/d | Deve ser definido como "in". Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure. |
|**name** | n/d | O nome da variável que representa a fila ou mensagem de tópico no código de função. |
|**queueName**|**Queuename**|Nome da fila a ser monitorada.  Defina somente se for monitorar uma fila, não para um tópico.
|**topicName**|**Nome do tópico**|Nome do tópico a ser monitorado. Defina somente se for monitorar um tópico, não uma fila.|
|**nome de assinatura**|**Nome da assinatura**|Nome da assinatura a ser monitorada. Defina somente se for monitorar um tópico, não uma fila.|
|**Conexão**|**Conexão**|O nome de uma configuração de aplicativo que contém uma cadeia de conexão de Barramento de Serviço para usar para essa associação. Se o nome de configuração do aplicativo começar com "AzureWebJobs", você pode especificar apenas o resto do nome. Por exemplo, se `connection` você definir como "MyServiceBus", o tempo de execução functions procurará uma configuração de aplicativo chamada "AzureWebJobsMyServiceBus". Se você deixar `connection` vazio, o runtime de Functions usa a cadeia de caracteres de conexão do Barramento de serviço na configuração de aplicativo chamada "AzureWebJobsServiceBus".<br><br>Para obter uma cadeia de conexão, siga as etapas mostradas em [Obter as credenciais de gerenciamento](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). A cadeia de conexão deve ser voltada para um namespace do Barramento de Serviço, não limitada a uma fila ou tópico específico. |
|**Accessrights**|**Acesso**|Direitos de acesso para a cadeia de caracteres de conexão. Os valores disponíveis são `manage` e `listen`. O padrão é `manage`, que indica que o `connection` tem a permissão **Gerenciar**. Se você usar uma cadeia de conexão que não tenha a permissão **Gerenciar**, defina `accessRights` como "escutar". Caso contrário, o runtime do Functions talvez falhe ao tentar executar operações que exigem o gerenciamento de direitos. Na versão 2.x do Azure Functions ou superior, esta propriedade não está disponível porque a versão mais recente do Service Bus SDK não suporta operações de gerenciamento.|
|**isSessionsEnabled**|**IsSessionsEnabled**|`true`se estiver se conectando a uma fila ou assinatura [com reconhecimento de sessão.](../service-bus-messaging/message-sessions.md) `false`caso contrário, que é o valor padrão.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Uso

# <a name="c"></a>[C #](#tab/csharp)

Os seguintes tipos de parâmetros estão disponíveis para a mensagem de fila ou tópico:

* `string` -Se a mensagem for de texto.
* `byte[]` - Útil para dados binários.
* Um tipo personalizado - Se a mensagem contiver JSON, funções do Azure tentará desserializar os dados JSON.
* `BrokeredMessage`- Dá-lhe a mensagem desserializada com o método [BrokeredMessage.GetBody\<T>()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1)

Esses tipos de parâmetros são para funções do Azure versão 1.x; para 2,x ou [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) mais, `BrokeredMessage`use em vez de .

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

Os seguintes tipos de parâmetros estão disponíveis para a mensagem de fila ou tópico:

* `string` -Se a mensagem for de texto.
* `byte[]` - Útil para dados binários.
* Um tipo personalizado - Se a mensagem contiver JSON, funções do Azure tentará desserializar os dados JSON.
* `BrokeredMessage`- Dá-lhe a mensagem desserializada com o método [BrokeredMessage.GetBody\<T>()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1)

Esses parâmetros são para funções do Azure versão 1.x; para 2,x ou [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) mais, `BrokeredMessage`use em vez de .

# <a name="javascript"></a>[Javascript](#tab/javascript)

Acesse a fila ou a `context.bindings.<name from function.json>`mensagem de tópico usando . A mensagem do Barramento de Serviço é passada em uma função como uma cadeia de caracteres ou um objeto JSON.

# <a name="python"></a>[Python](#tab/python)

A mensagem de fila está disponível para `func.ServiceBusMessage`a função através de um parâmetro digitado como . A mensagem do Barramento de Serviço é passada em uma função como uma cadeia de caracteres ou um objeto JSON.

# <a name="java"></a>[Java](#tab/java)

A mensagem de Ônibus de `ServiceBusQueueMessage` `ServiceBusTopicMessage` Serviço de entrada está disponível através de um ou parâmetro.

[Veja o exemplo para detalhes](#example).

---

## <a name="poison-messages"></a>Mensagens venenosas

A manipulação de mensagens suspeitas não pode ser controlada ou configurada no Azure Functions. O barramento de serviço ele mesmo lida com as mensagens suspeitas.

## <a name="peeklock-behavior"></a> Comportamento de PeekLock

O runtime do Functions recebe uma mensagem no [Modo PeekLock](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). Ele chama `Complete` na mensagem se a função for concluída com êxito, ou chama `Abandon` se a função falhar. Se a função for executada por mais tempo que o limite `PeekLock`, o bloqueio é renovado automaticamente. 

O `maxAutoRenewDuration` pode ser configurado no *host.json*, que mapeia para [OnMessageOptions.MaxAutoRenewDuration](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet). O máximo permitido para essa configuração é 5 minutos de acordo com a documentação do Barramento de Serviço do Microsoft Azure, enquanto você pode aumentar o limite de tempo de funções do padrão de 5 minutos para 10 minutos. Para funções de Barramento de Serviço você não irá fazer isso, porque excederia o limite de renovação do Barramento de Serviço.

## <a name="message-metadata"></a>Metadados de mensagem

O gatilho Barramento de Serviço fornece várias propriedades de [metadados](./functions-bindings-expressions-patterns.md#trigger-metadata). Essas propriedades podem ser usadas como parte de expressões de associação em outras associações ou como parâmetros em seu código. Essas propriedades são membros da classe [BrokeredMessage.](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)

|Propriedade|Type|Descrição|
|--------|----|-----------|
|`DeliveryCount`|`Int32`|Número total de entregas.|
|`DeadLetterSource`|`string`|A origem de mensagens mortas.|
|`ExpiresAtUtc`|`DateTime`|Tempo de expiração em UTC.|
|`EnqueuedTimeUtc`|`DateTime`|O tempo de enfileiramento no UTC.|
|`MessageId`|`string`|Um valor definido pelo usuário que o Barramento de Serviço pode usar para identificar mensagens duplicadas, se habilitado.|
|`ContentType`|`string`|Um identificador de tipo de conteúdo utilizado pelo remetente e receptor para uma lógica específica do aplicativo.|
|`ReplyTo`|`string`|A resposta para o endereço da fila.|
|`SequenceNumber`|`Int64`|Número exclusivo atribuído a uma mensagem pelo Barramento de Serviço.|
|`To`|`string`|Enviar para o endereço.|
|`Label`|`string`|O rótulo específico da aplicação.|
|`CorrelationId`|`string`|ID de correlação.|

Consulte [exemplos de código](#example) que usam essas propriedades neste artigo.

## <a name="next-steps"></a>Próximas etapas

- [Envie mensagens de barramento de serviço do Azure das funções do Azure (vinculação de saída)](./functions-bindings-service-bus-output.md)
