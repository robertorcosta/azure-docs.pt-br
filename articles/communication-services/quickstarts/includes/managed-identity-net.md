---
ms.openlocfilehash: 11b10817959a390b4ea0215d72f97513a6b23345
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102486566"
---
## <a name="add-managed-identity-to-your-communication-services-solution-net"></a>Adicionar identidade gerenciada à sua solução de serviços de comunicação (.NET)

### <a name="install-the-client-library-packages"></a>Instalar os pacotes de biblioteca de cliente

```console
dotnet add package Azure.Communication.Identity
dotnet add package Azure.Communication.Sms
dotnet add package Azure.Identity
```

### <a name="use-the-client-library-packages"></a>Usar os pacotes de biblioteca de cliente

Adicione as seguintes `using` diretivas ao seu código para usar as bibliotecas de cliente de armazenamento do Azure e identidade do Azure.

```csharp
using Azure.Identity;
using Azure.Communication.Identity;
using Azure.Communication.Sms;
```

Os exemplos a seguir estão usando o [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential). Essa credencial é adequada para ambientes de produção e desenvolvimento.

`AZURE_CLIENT_SECRET``AZURE_CLIENT_ID`e as `AZURE_TENANT_ID` variáveis de ambiente são necessárias para criar um `DefaultAzureCredential` objeto. Para criar um aplicativo registrado no ambiente de desenvolvimento e configurar variáveis de ambiente, consulte [autorizar o acesso com identidade gerenciada](../managed-identity-from-cli.md).

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>Criar uma identidade e emitir um token com identidade gerenciada

O exemplo de código a seguir mostra como criar um objeto de cliente de serviço com tokens de Azure Active Directory.

Em seguida, use o cliente para emitir um token para um novo usuário:

```csharp
     public async Task<Response<CommunicationUserToken>> CreateIdentityAndGetTokenAsync(Uri resourceEdnpoint)
     {
          TokenCredential credential = new DefaultAzureCredential();
          // You can find your endpoint and access key from your resource in the Azure portal
          String resourceEndpoint = "https://<RESOURCE_NAME>.communication.azure.com";

          var client = new CommunicationIdentityClient(resourceEndpoint, credential);
          var identityResponse = await client.CreateUserAsync();

          var tokenResponse = await client.GetTokenAsync(identity, scopes: new [] { CommunicationTokenScope.VoIP });

          return tokenResponse;
     }
```

### <a name="send-an-sms-with-managed-identity"></a>Enviar um SMS com identidade gerenciada

O exemplo de código a seguir mostra como criar um objeto de cliente de serviço do SMS com identidade gerenciada e, em seguida, usar o cliente para enviar uma mensagem SMS:

```csharp
     public async Task SendSms(Uri resourceEndpoint, PhoneNumber from, PhoneNumber to, string message)
     {
          TokenCredential credential = new DefaultAzureCredential();
          // You can find your endpoint and access key from your resource in the Azure portal
          String resourceEndpoint = "https://<RESOURCE_NAME>.communication.azure.com";

          SmsClient smsClient = new SmsClient(resourceEndpoint, credential);
          smsClient.Send(
               from: from,
               to: to,
               message: message,
               new SendSmsOptions { EnableDeliveryReport = true } // optional
          );
     }
```

