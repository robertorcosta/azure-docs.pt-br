---
ms.openlocfilehash: c5d83cc709c334e15a1c13e64ba17ef24835fed0
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657601"
---
## <a name="add-managed-identity-to-your-communication-services-solution-java"></a>Adicionar identidade gerenciada à sua solução de serviços de comunicação (Java)

### <a name="install-the-client-library-packages"></a>Instalar os pacotes de biblioteca de cliente
No arquivo pom.xml, adicione os seguintes elementos de dependência ao grupo de dependências.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-identity</artifactId>
    <version>1.0.0</version>
</dependency>
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-sms</artifactId>
    <version>1.0.0</version>
</dependency>
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-identity</artifactId>
    <version>1.2.3</version>
</dependency>
```

### <a name="use-the-client-library-packages"></a>Usar os pacotes de biblioteca de cliente

Adicione as seguintes `import` diretivas ao seu código para usar a identidade do Azure e as bibliotecas de cliente de comunicação do Azure.

```java
import com.azure.identity.*;
import com.azure.communication.sms.*;
import com.azure.communication.identity.*;
import com.azure.communication.common.*;
```

Os exemplos a seguir estão usando o [DefaultAzureCredential](/java/api/azure.identity.defaultazurecredential). Essa credencial é adequada para ambientes de produção e desenvolvimento.

`AZURE_CLIENT_SECRET``AZURE_CLIENT_ID`e as `AZURE_TENANT_ID` variáveis de ambiente são necessárias para criar um `DefaultAzureCredential` objeto. Para criar um aplicativo registrado no ambiente de desenvolvimento e configurar variáveis de ambiente, consulte [autorizar o acesso com identidade gerenciada](../managed-identity-from-cli.md).

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>Criar uma identidade e emitir um token com identidade gerenciada

O exemplo de código a seguir mostra como criar um objeto de cliente de serviço com identidade gerenciada.
Em seguida, use o cliente para emitir um token para um novo usuário:

```java
     public AccessToken createIdentityAndGetTokenAsync() {
          // You can find your endpoint and access key from your resource in the Azure portal
          String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

          HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();
          TokenCredential credential = new DefaultAzureCredentialBuilder().build();

          CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
               .endpoint(endpoint)
               .credential(credential)
               .httpClient(httpClient)
               .buildClient();

          CommunicationUserIdentifier user = communicationIdentityClient.createUser();
          AccessToken userToken = communicationIdentityClient.getToken(user, new ArrayList<>(Arrays.asList(CommunicationTokenScope.CHAT)));
          return userToken;
     }
```

### <a name="send-an-sms-with-managed-identity"></a>Enviar um SMS com identidade gerenciada

O exemplo de código a seguir mostra como criar um objeto de cliente de serviço com identidade gerenciada e, em seguida, usar o cliente para enviar uma mensagem SMS:

```java
     public SendSmsResponse sendSms() {
          // You can find your endpoint and access key from your resource in the Azure portal
          String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

          HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();
          TokenCredential credential = new DefaultAzureCredentialBuilder().build();

          SmsClient smsClient = new SmsClientBuilder()
               .endpoint(endpoint)
               .credential(credential)
               .httpClient(httpClient)
               .buildClient();

          // Send the message and check the response for a message id
          SendSmsResponse response = smsClient.sendMessage(
               new PhoneNumberIdentifier("<leased-phone-number>"),
               to,
               "your message",
               options /* Optional */
          );
          return response;
    }
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aprender sobre autenticação](../concepts/authentication.md)

Você também pode querer:

- [Saiba mais sobre o controle de acesso baseado em função do Azure](../../../../articles/role-based-access-control/index.yml)
- [Saiba mais sobre a biblioteca de identidades do Azure para Java](/java/api/overview/azure/identity-readme)
- [Criar tokens de acesso do usuário](../../quickstarts/access-tokens.md)
- [Enviar uma mensagem SMS](../../quickstarts/telephony-sms/send.md)
- [Saiba mais sobre SMS](../../concepts/telephony-sms/concepts.md)
