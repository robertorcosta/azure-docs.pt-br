---
ms.openlocfilehash: 52c5c0d9d13eba29e6f9cb6d50725d51b6877d47
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110709"
---
## <a name="add-managed-identity-to-your-communication-services-solution-net"></a>Adicionar identidade gerenciada à sua solução de serviços de comunicação (.NET)

### <a name="install-the-sdk-packages"></a>Instalar os pacotes do SDK

```console
dotnet add package Azure.Communication.Identity  --version 1.0.0-beta.5
dotnet add package Azure.Communication.Sms  --version 1.0.0-beta.4
dotnet add package Azure.Identity
```

### <a name="use-the-sdk-packages"></a>Usar os pacotes do SDK

Adicione as seguintes `using` diretivas ao seu código para usar a identidade do Azure e os SDKs de armazenamento do Azure.

```csharp
using Azure.Identity;
using Azure.Communication.Identity;
using Azure.Communication.Sms;
using Azure.Core;
```

Os exemplos a seguir estão usando o [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential). Essa credencial é adequada para ambientes de produção e desenvolvimento.

`AZURE_CLIENT_SECRET``AZURE_CLIENT_ID`e as `AZURE_TENANT_ID` variáveis de ambiente são necessárias para criar um `DefaultAzureCredential` objeto. Para criar um aplicativo registrado no ambiente de desenvolvimento e configurar variáveis de ambiente, consulte [autorizar o acesso com identidade gerenciada](../managed-identity-from-cli.md).

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>Criar uma identidade e emitir um token com identidade gerenciada

O exemplo de código a seguir mostra como criar um objeto de cliente de serviço com tokens de Azure Active Directory.

Em seguida, use o cliente para emitir um token para um novo usuário:

```csharp
     public Response<AccessToken> CreateIdentityAndGetTokenAsync(Uri resourceEndpoint)
     {
          TokenCredential credential = new DefaultAzureCredential();

          // You can find your endpoint and access key from your resource in the Azure portal
          // "https://<RESOURCE_NAME>.communication.azure.com";

          var client = new CommunicationIdentityClient(resourceEndpoint, credential);
          var identityResponse = client.CreateUser();
          var identity = identityResponse.Value;

          var tokenResponse = client.GetToken(identity, scopes: new[] { CommunicationTokenScope.VoIP });

          return tokenResponse;
     }
```

### <a name="send-an-sms-with-managed-identity"></a>Enviar um SMS com identidade gerenciada

O exemplo de código a seguir mostra como criar um objeto de cliente de serviço do SMS com identidade gerenciada e, em seguida, usar o cliente para enviar uma mensagem SMS:

```csharp
     public SmsSendResult SendSms(Uri resourceEndpoint, string from, string to, string message)
     {
          TokenCredential credential = new DefaultAzureCredential();
          // You can find your endpoint and access key from your resource in the Azure portal
          // "https://<RESOURCE_NAME>.communication.azure.com";

          SmsClient smsClient = new SmsClient(resourceEndpoint, credential);
          SmsSendResult sendResult = smsClient.Send(
               from: from,
               to: to,
               message: message,
               new SmsSendOptions(enableDeliveryReport: true) // optional
          );

          return sendResult;
      }
```

