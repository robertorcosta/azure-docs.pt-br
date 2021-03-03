---
ms.openlocfilehash: 9da0cbc3777359994fac3778dcc126c844754861
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657599"
---
## <a name="add-managed-identity-to-your-communication-services-solution"></a>Adicionar identidade gerenciada à sua solução de serviços de comunicação

### <a name="install-the-client-library-packages"></a>Instalar os pacotes de biblioteca de cliente

```console
pip install azure-identity
pip install azure-communication-identity
pip install azure-communication-sms
```

### <a name="use-the-client-library-packages"></a>Usar os pacotes de biblioteca de cliente

Adicione o seguinte `import` ao seu código para usar a identidade do Azure.

```python
from azure.identity import DefaultAzureCredential
```

Os exemplos a seguir estão usando o [DefaultAzureCredential](/python/api/azure.identity.defaultazurecredential). Essa credencial é adequada para ambientes de produção e desenvolvimento.

Para registrar o aplicativo no ambiente de desenvolvimento e configurar variáveis de ambiente, consulte [autorizar o acesso com identidade gerenciada](../managed-identity-from-cli.md)

### <a name="create-an-identity-and-issue-a-token"></a>Criar uma identidade e emitir um token

O exemplo de código a seguir mostra como criar um objeto de cliente de serviço com identidade gerenciada e, em seguida, usar o cliente para emitir um token para um novo usuário:

```python
import azure.communication.identity 

def create_identity_and_get_token(resource_endpoint):
     credential = DefaultAzureCredential()
     client = CommunicationIdentityClient(endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])
     
     return token_response
```

### <a name="send-an-sms-with-azure-managed-identity"></a>Enviar um SMS com a identidade gerenciada do Azure

O exemplo de código a seguir mostra como criar um objeto de cliente de serviço com a identidade gerenciada do Azure e, em seguida, usar o cliente para enviar uma mensagem SMS:

```python
from azure.communication.sms import (
    PhoneNumberIdentifier,
    SendSmsOptions,
    SmsClient
)

def send_sms(resource_endpoint, from_phone_number, to_phone_number, message_content):
     credential = DefaultAzureCredential()
     sms_client = SmsClient(resource_endpoint, credential)

     sms_client.send(
          from_phone_number=PhoneNumberIdentitifier(from_phone_number),
          to_phone_numbers=[PhoneNumberIdentifier(to_phone_number)],
          message=message_content,
          send_sms_options=SendSmsOptions(enable_delivery_report=True))  # optional property
     )
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aprender sobre autenticação](../../concepts/authentication.md)

Você também pode querer:

- [Saiba mais sobre o controle de acesso baseado em função do Azure](../../../../articles/role-based-access-control/index.yml)
- [Saiba mais sobre a biblioteca de identidades do Azure para Python](/net/api/overview/azure/identity-readme)
- [Criar tokens de acesso do usuário](../../quickstarts/access-tokens.md)
- [Enviar uma mensagem SMS](../../quickstarts/telephony-sms/send.md)
- [Saiba mais sobre SMS](../../concepts/telephony-sms/concepts.md)