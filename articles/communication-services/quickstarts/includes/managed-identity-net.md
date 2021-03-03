---
ms.openlocfilehash: 2c816f005dea452c3ffa2889aa7d2742a5762759
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657600"
---
## <a name="add-managed-identity-to-your-communication-services-solution-net"></a>Adicionar identidade gerenciada à sua solução de serviços de comunicação (.net)

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

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aprender sobre autenticação](../concepts/authentication.md)

Você também pode querer:

- [Saiba mais sobre o controle de acesso baseado em função do Azure](../../../../articles/role-based-access-control/index.yml)
- [Saiba mais sobre a biblioteca de identidades do Azure para .NET](/dotnet/api/overview/azure/identity-readme)
- [Criar tokens de acesso do usuário](../../quickstarts/access-tokens.md)
- [Enviar uma mensagem SMS](../telephony-sms/send.md)
- [Saiba mais sobre SMS](../../concepts/telephony-sms/concepts.md)
