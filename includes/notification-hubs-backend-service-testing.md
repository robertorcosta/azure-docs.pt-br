---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 72e4318bea7245e440db4c7d95bf7f1f38bbe268
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095104"
---
### <a name="send-a-test-notification"></a>Enviar uma notificação de teste

1. Abra uma nova guia no [Postman](https://www.postman.com/downloads/).

1. Defina a solicitação como **POST** e insira o seguinte endereço:

    ```xml
    https://<app_name>.azurewebsites.net/api/notifications/requests
    ```

1. Se você optou por concluir a seção [Autenticar clientes usando uma chave de API](#authenticate-clients-using-an-api-key-optional), configure os cabeçalhos de solicitação para incluir o valor de **apikey**.

   | Chave                            | Valor                          |
   | ------------------------------ | ------------------------------ |
   | apikey                         | <your_api_key>                 |

   > [!NOTE]
   > Isso pode ser encontrado em **Config.local_secrets.cs** dentro do projeto **PushDemo**.

1. Escolha a opção **bruto** para o **Corpo**, escolha **JSON** na lista de opções de formato e inclua algum conteúdo de espaço reservado **JSON**:

    ```json
    {
        "text": "Message from Postman!",
        "action": "action_a"
    }
    ```

1. Selecione o botão **Código**, que está abaixo do botão **Salvar** no canto superior direito da janela. A solicitação deve ser semelhante ao seguinte exemplo quando exibida para **HTML** (dependendo se você incluiu um cabeçalho **apiKey**):

    ```html
    POST /api/notifications/requests HTTP/1.1
    Host: https://<app_name>.azurewebsites.net
    apikey: <your_api_key>
    Content-Type: application/json

    {
        "text": "Message from backend service",
        "action": "action_a"
    }
    ```

1. Execute o aplicativo **PushDemo** em uma ou ambas as plataformas de destino (**Android** e **iOS**).

    > [!NOTE]
    > Se você estiver testando no **Android** não execute em **Depuração** ou, se o aplicativo tiver sido implantado executando o aplicativo, force o fechamento do aplicativo e inicie-o novamente com o inicializador.

1. No aplicativo **PushDemo**, toque no botão **Registrar**.

1. De volta ao **[Postman](https://www.postman.com/downloads)** , feche a janela **Gerar Trechos de Código** (se você ainda não tiver feito isso), e clique no botão **Enviar**.

1. Valide se você recebeu uma resposta **200 OK** no **[Postman](https://www.postman.com/downloads)** e se o alerta aparece no aplicativo mostrando **Ação ActionA recebida**.  

1. Feche o aplicativo **PushDemo** e clique no botão **Enviar** novamente no **[Postman](https://www.postman.com/downloads)** .

1. Valide se você recebe uma resposta **200 OK** no **[Postman](https://www.postman.com/downloads)** novamente. Verifique se aparece uma notificação na área de notificação do aplicativo **PushDemo** com a mensagem correta.

1. Toque na notificação para confirmar que ela abre o aplicativo e exibe o alerta **Ação ActionA recebida**.

1. De volta ao **[Postman](https://www.postman.com/downloads)** , modifique o corpo da solicitação anterior para enviar uma notificação silenciosa especificando *action_b* em vez de *action_a* como o valor da **ação**.

    ```json
    {
        "action": "action_b",
        "silent": true
    }
    ```

1. Com o aplicativo ainda aberto, clique no botão **Enviar** no **[Postman](https://www.postman.com/downloads)** .

1. Valide se você recebeu uma resposta **200 OK** no **[Postman](https://www.postman.com/downloads)** e se o alerta aparece no aplicativo mostrando **Ação ActionB recebida** em vez de **Ação ActionA recebida**.

1. Feche o aplicativo **PushDemo** e clique no botão **Enviar** novamente no **[Postman](https://www.postman.com/downloads)** .

1. Valide se você obtém uma resposta **200 OK** no **[Postman](https://www.postman.com/downloads)** e se a notificação silenciosa não aparece na área de notificação.
