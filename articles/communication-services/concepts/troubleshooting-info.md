---
title: Solução de problemas nos Serviços de Comunicação do Azure
description: Saiba como reunir as informações necessárias para solucionar problemas de sua solução de Serviços de Comunicação.
author: manoskow
manager: jken
services: azure-communication-services
ms.author: manoskow
ms.date: 10/23/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 4921078e9e7b5d9de06fbbc9a97dc4a964569e04
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92754648"
---
# <a name="troubleshooting-in-azure-communication-services"></a>Solução de problemas nos Serviços de Comunicação do Azure

Este documento ajudará você a reunir as informações necessárias para solucionar problemas de sua solução de serviços de comunicação.

## <a name="getting-help"></a>Obtendo ajuda

Incentivamos os desenvolvedores a enviar perguntas, sugerir recursos e relatar problemas como problemas no repositório de Serviços de Comunicação [GitHub](https://github.com/Azure/communication). Outros fóruns incluem:

* [P e R da Microsoft](https://docs.microsoft.com/answers/questions/topics/single/101418.html)
* [StackOverflow](https://stackoverflow.com/questions/tagged/azure+communication)

Dependendo do [plano de suporte](https://azure.microsoft.com/support/plans/) de assinatura do Azure, você pode enviar um tíquete de suporte diretamente por meio do [portal do Azure](https://azure.microsoft.com/support/create-ticket/).

Para ajudá-lo a solucionar determinados tipos de problemas, você pode ser solicitado a fornecer qualquer uma das seguintes informações:

* **ID MS-CV** : Essa ID é usada para solucionar problemas de chamadas e mensagens. 
* **ID da chamada** : Essa ID é usada para identificar chamadas de Serviços de Comunicação.
* **ID da mensagem do SMS** : Essa ID é usada para identificar mensagens SMS.

## <a name="access-your-ms-cv-id"></a>Acessar sua ID do MS-CV

A ID do MS-CV pode ser acessada configurando o diagnóstico na instância do objeto `clientOptions` ao inicializar suas bibliotecas de cliente. O diagnóstico pode ser configurado para qualquer uma das bibliotecas de cliente do Azure, incluindo chat, administração e chamada VoIP.

### <a name="client-options-example"></a>Exemplo das opções do cliente

Os trechos de código a seguir demonstram a configuração de diagnóstico. Quando as bibliotecas de cliente são usadas com o diagnóstico habilitado, os detalhes do diagnóstico serão emitidos para o ouvinte de evento configurado:

# <a name="c"></a>[C#](#tab/csharp)
``` 
// 1. Import Azure.Core.Diagnostics
using Azure.Core.Diagnostics;

// 2. Initialize an event source listener instance
using var listener = AzureEventSourceListener.CreateConsoleLogger();
Uri endpoint = new Uri("https://<RESOURCE-NAME>.communication.azure.net");
var (token, communicationUser) = await GetCommunicationUserAndToken();
CommunicationUserCredential communicationUserCredential = new CommunicationUserCredential(token);

// 3. Setup diagnostic settings
var clientOptions = new ChatClientOptions()
{
    Diagnostics =
    {
        LoggedHeaderNames = { "*" },
        LoggedQueryParameters = { "*" },
        IsLoggingContentEnabled = true,
    }
};

// 4. Initialize the ChatClient instance with the clientOptions 
ChatClient chatClient = new ChatClient(endpoint, communicationUserCredential, clientOptions);
ChatThreadClient chatThreadClient = await chatClient.CreateChatThreadAsync("Thread Topic", new[] { new ChatThreadMember(communicationUser) });
```

# <a name="python"></a>[Python](#tab/python)
``` 
from azure.communication.chat import ChatClient, CommunicationUserCredential
endpoint = "https://communication-services-sdk-live-tests-for-python.communication.azure.com"
chat_client = ChatClient(
    endpoint,
    CommunicationUserCredential(token),
    http_logging_policy=your_logging_policy)
```
---

## <a name="access-your-call-id"></a>Acessar sua ID de chamada

Ao fazer o arquivamento de uma solicitação de suporte por meio do portal do Azure relacionado à chamada de problemas, você pode ser solicitado a fornecer a ID da chamada à qual está se referindo. Isso pode ser acessado por meio da biblioteca de cliente de chamada:

# <a name="javascript"></a>[JavaScript](#tab/javascript)
```javascript
// `call` is an instance of a call created by `callAgent.call` or `callAgent.join` methods 
console.log(call.id)
```

# <a name="ios"></a>[iOS](#tab/ios)
```objc
// The `call id` property can be retrieved by calling the `call.getCallId()` method on a call object after a call ends 
// todo: the code snippet suggests it's a property while the comment suggests it's a method call
print(call.callId) 
```

# <a name="android"></a>[Android](#tab/android)
```java
// The `call id` property can be retrieved by calling the `call.getCallId()` method on a call object after a call ends
// `call` is an instance of a call created by `callAgent.call(…)` or `callAgent.join(…)` methods 
Log.d(call.getCallId()) 
```
---


## <a name="access-your-sms-message-id"></a>Acessar sua ID de mensagem de SMS

Para problemas de SMS, você pode coletar a ID da mensagem do objeto de resposta.

# <a name="net"></a>[.NET](#tab/dotnet)
```
// Instantiate the SMS client
const smsClient = new SmsClient(connectionString);
async function main() {
  const result = await smsClient.send({
    from: "+18445792722",
    to: ["+1972xxxxxxx"],
    message: "Hello World 👋🏻 via Sms"
  }, {
    enableDeliveryReport: true // Optional parameter
  });
console.log(result); // your message ID will be in the result
}
```
---

## <a name="related-information"></a>Informações relacionadas
- [Configurar logs e diagnósticos](logging-and-diagnostics.md)
- [Métricas](metrics.md)
