---
title: Como configurar Carteiro - Azure Digital Twins | Microsoft Docs
description: Aprenda a configurar e usar o Carteiro para testar as APIs azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: ffcfb4f6ec5f6c654d0b243af85034ab575e0d88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297164"
---
# <a name="how-to-configure-postman-for-azure-digital-twins"></a>Como configurar o Postman para os Gêmeos Digitais do Azure

Este artigo descreve como configurar o cliente Postman REST para interagir e testar APIs de Gerenciamento dos Gêmeos Digitais do Azure. Especificamente, ele descreve:

* Como configurar um aplicativo do Azure Active Directory para usar o fluxo de concessão implícita do OAuth 2.0.
* Como usar o cliente Postman REST para fazer solicitações HTTP de influência de token para suas APIs de gerenciamento.
* Como usar o Postman para fazer solicitações POST com várias partes para suas APIs de gerenciamento.

## <a name="postman-summary"></a>Resumo do Postman

Começar em nos Gêmeos Digitais do Azure usando uma ferramenta de cliente REST como [Postman](https://www.getpostman.com/) para preparar seu ambiente de teste local. O cliente Postman ajuda a criar rapidamente as solicitações HTTP complexas. Faça o download da versão da área de trabalho do cliente Postman acessando [www.getpostman.com/apps](https://www.getpostman.com/apps).

[Postman](https://www.getpostman.com/) é uma ferramenta de teste REST que localiza as funcionalidades de solicitação HTTP principal na área de trabalho útil e GUI baseado em plugin.

Através do cliente Postman, os desenvolvedores de soluções podem especificar o tipo de solicitação HTTP (*POST*, *GET,* *UPDATE,* *PATCH*e *DELETE*), ponto final da API para chamada e uso de TLS. O Postman também dá suporte para adicionar cabeçalhos de solicitação HTTP, parâmetros, dados de formulário e corpos.

## <a name="configure-azure-active-directory-to-use-the-oauth-20-implicit-grant-flow"></a>Configurar o Azure Active Directory para usar o fluxo de concessão implícita do OAuth 2.0

1. Siga as etapas [do Quickstart](quickstart-view-occupancy-dotnet.md#set-permissions-for-your-app) para criar e configurar um aplicativo do Azure Active Directory. Alternativamente, você pode reutilizar um registro de aplicativo existente.

    [![Configure um novo URI de redirecionamento do Carteiro](media/how-to-configure-postman/authentication-redirect-uri.png)](media/how-to-configure-postman/authentication-redirect-uri.png#lightbox)

1. Agora, adicione um URI `https://www.getpostman.com/oauth2/callback` **redirecionamento** para .

1. Selecione a caixa de seleção de**tokens** **de concessão** > implícita para permitir que o fluxo de subvenção implícita OAuth 2.0 seja usado. Selecione **Configurar** **e**salvar .

1. Copie o **ID** do cliente do seu aplicativo Azure Active Directory.

## <a name="obtain-an-oauth-20-token"></a>Obter um token do OAuth 2.0

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]

Configure e configure o Carteiro para obter um token do Azure Active Directory. Em seguida, faça uma solicitação HTTP autenticada para os Gêmeos Digitais do Azure usando o token adquirido:

1. Verifique se sua **URL de Autorização** está correta. Deve pegar o formato:

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | Nome  | Substitua por | Exemplo |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | O nome do seu inquilino ou organização. Use o nome humano em vez do **ID** de inquilino alfanumérico do seu registro do aplicativo Azure Active Directory. | `microsoft` |

1. Vá para [www.getpostman.com](https://www.getpostman.com/) para baixar o aplicativo.

1. Queremos fazer um pedido get. Selecione a guia **Autorização,** selecione OAuth 2.0 e **selecione Obter novo token de acesso**.

    | Campo  | Valor |
    |---------|---------|
    | Tipo de concessão | `Implicit` |
    | URL de retorno de chamada | `https://www.getpostman.com/oauth2/callback` |
    | URL de autenticação | Use a URL de **autorização** da **etapa 1** |
    | ID do Cliente | Use o **ID do aplicativo** do Azure Active Directory criado ou reutilizado na seção anterior |
    | Escopo | Deixar em branco |
    | Estado | Deixar em branco |
    | Autenticação de cliente | `Send as Basic Auth header` |

1. Agora, o cliente deve se parecer com:

    [![Exemplo de token de cliente carteiro](media/how-to-configure-postman/configure-postman-oauth-token.png)](media/how-to-configure-postman/configure-postman-oauth-token.png#lightbox)

1. Selecione **Solicitação de Token**.
  
1. Role para baixo e selecione **uso Token**.

## <a name="make-a-multipart-post-request"></a>Fazer uma solicitação POST com várias partes

Depois de concluir as etapas anteriores, configure o Postman para fazer uma solicitação POST com várias partes HTTP autenticada:

1. Na guia **Cabeçalhos,** adicione uma chave de `multipart/mixed`cabeçalho de solicitação HTTP Tipo de **conteúdo** com valor .

   [![Especifique o tipo de conteúdo multiparte/misturado](media/how-to-configure-postman/configure-postman-content-type.png)](media/how-to-configure-postman/configure-postman-content-type.png#lightbox)

1. Serialize dados não textuais em arquivos. Os dados JSON seriam salvos como um arquivo JSON.
1. Na guia **Corpo,** selecione `form-data`. 
1. Adicione cada arquivo atribuindo um **nome-chave,** selecionando `File`.
1. Em seguida, selecione cada arquivo por meio do botão **Escolher arquivo**.

   [![Exemplo de corpo de cliente carteiro](media/how-to-configure-postman/configure-postman-form-body.png)](media/how-to-configure-postman/configure-postman-form-body.png#lightbox)

   >[!NOTE]
   > * O cliente Postman não requer que as partes com várias partes tenham um **Content-Type** ou **Content-Disposition** atribuído manualmente.
   > * Você não precisará especificar esses cabeçalhos para cada parte.
   > * É necessário selecionar `multipart/mixed` ou outro **Content-Type** adequado para a solicitação inteira.

1. Por fim, selecione **Enviar** para enviar sua solicitação HTTP POST multiparte. Um código `200` de `201` status ou indica uma solicitação bem sucedida. A mensagem de resposta apropriada aparecerá na interface do cliente.

1. Valide seus dados de solicitação HTTP POST ligando para o ponto final da API: 

   ```URL
   YOUR_MANAGEMENT_API_URL/spaces/blobs?includes=description
   ```

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre as APIs de gerenciamento dos Gêmeos Digitais do Azure e como usá-las, leia [Como usar as APIs de Gerenciamento dos Gêmeos Digitais do Azure](how-to-navigate-apis.md).

- Usar solicitações com várias partes para [adicionar blobs às entidades dos Gêmeos Digitais do Azure](./how-to-add-blobs.md).

- Para saber mais sobre autenticar as APIs de Gerenciamento, leia [Autenticar com APIs](./security-authenticating-apis.md).
