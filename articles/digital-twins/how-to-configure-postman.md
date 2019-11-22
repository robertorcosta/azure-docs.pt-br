---
title: Como configurar o postmaster – Azure digital gêmeos | Microsoft Docs
description: Saiba como configurar e usar o postmaster para testar as APIs do gêmeos digital do Azure.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: 784e31b823c65c0b908dc07582805e7a69d19563
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74304805"
---
# <a name="how-to-configure-postman-for-azure-digital-twins"></a>Como configurar o Postman para os Gêmeos Digitais do Azure

Este artigo descreve como configurar o cliente Postman REST para interagir e testar APIs de Gerenciamento dos Gêmeos Digitais do Azure. Especificamente, ele descreve:

* Como configurar um aplicativo do Azure Active Directory para usar o fluxo de concessão implícita do OAuth 2.0.
* Como usar o cliente Postman REST para fazer solicitações HTTP de influência de token para suas APIs de gerenciamento.
* Como usar o Postman para fazer solicitações POST com várias partes para suas APIs de gerenciamento.

## <a name="postman-summary"></a>Resumo do Postman

Começar em nos Gêmeos Digitais do Azure usando uma ferramenta de cliente REST como [Postman](https://www.getpostman.com/) para preparar seu ambiente de teste local. O cliente Postman ajuda a criar rapidamente as solicitações HTTP complexas. Faça o download da versão da área de trabalho do cliente Postman acessando [www.getpostman.com/apps](https://www.getpostman.com/apps).

[Postman](https://www.getpostman.com/) é uma ferramenta de teste REST que localiza as funcionalidades de solicitação HTTP principal na área de trabalho útil e GUI baseado em plugin.

Por meio do cliente Postman, os desenvolvedores de soluções podem especificar o tipo de solicitação HTTP (*POST*, *GET*, *UPDATE*, *PATCH* e *DELETE*), ponto de extremidade de API para a chamada e o uso de SSL. O Postman também dá suporte para adicionar cabeçalhos de solicitação HTTP, parâmetros, dados de formulário e corpos.

## <a name="configure-azure-active-directory-to-use-the-oauth-20-implicit-grant-flow"></a>Configurar o Azure Active Directory para usar o fluxo de concessão implícita do OAuth 2.0

Configurar seu aplicativo Azure Active Directory para usar o fluxo de concessão implícita OAuth 2.0.

1. Abra o painel **Permissões de API** para o registro do aplicativo. Selecione o botão **Adicionar uma permissão**. No painel **Solicitar permissões de API**, selecione a guia **APIs que a minha organização usa** e pesquise por:
    
    1. `Azure Digital Twins`. Selecione a **API de Gêmeos Digitais do Azure**.

        [![API de Pesquisa ou Gêmeos Digitais do Azure](../../includes/media/digital-twins-permissions/aad-aap-search-api-dt.png)](../../includes/media/digital-twins-permissions/aad-aap-search-api-dt.png#lightbox)

    1. Como alternativa, pesquise `Azure Smart Spaces Service`. Selecione a API **Azure Smart Spaces Service**.

        [![API de Pesquisa para Espaços Inteligentes do Azure](../../includes/media/digital-twins-permissions/aad-app-search-api.png)](../../includes/media/digital-twins-permissions/aad-app-search-api.png#lightbox)

    > [!IMPORTANT]
    > A ID e o nome da API do Azure AD que serão exibidos dependerão do locatário:
    > * As contas de locatário e cliente de teste devem pesquisar `Azure Digital Twins`.
    > * Outras contas Microsoft devem pesquisar `Azure Smart Spaces Service`.

1. A API selecionada é exibida como **Gêmeos Digitais do Azure** no mesmo painel **Solicitar permissões de API**. Selecione a lista suspensa **Ler (1)** e a caixa de seleção **Read.Write**. Selecione o botão **Adicionar permissões**.

    [![adicionar permissões de API para o Azure digital gêmeos](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png)](../../includes/media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. Dependendo das configurações da sua organização, talvez você precise seguir etapas adicionais para conceder acesso de administrador a essa API. Entre em contato com seu administrador para obter mais informações. Assim que o acesso de administrador for aprovado, a coluna **CONSENTIMENTO DO ADMINISTRADOR NECESSÁRIO** no painel de **Permissões de API** será mostrada da seguinte forma para as suas APIs:

    [![configurar a aprovação de consentimento do administrador](../../includes/media/digital-twins-permissions/aad-app-admin-consent.png)](../../includes/media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)

1. Configure um segundo **URI de redirecionamento** para `https://www.getpostman.com/oauth2/callback`.

    [![configurar um novo URI de redirecionamento do postmaster](media/how-to-configure-postman/authentication-redirect-uri.png)](media/how-to-configure-postman/authentication-redirect-uri.png#lightbox)

1. Para certificar-se [de que o aplicativo está registrado como um **cliente público**](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration), abra o painel de **autenticação** para o registro do aplicativo e role para baixo nesse painel. Na seção **tipo de cliente padrão** , escolha **Sim** para **tratar aplicativo como um cliente público**e clique em **salvar**.

    Verifique os **tokens de acesso** para habilitar a configuração **Oauth2AllowImplicitFlow** em seu manifest. JSON.

    [![definição de configuração de cliente público](../../includes/media/digital-twins-permissions/aad-public-client.png)](../../includes/media/digital-twins-permissions/aad-public-client.png#lightbox)

1. Copie e mantenha a **ID do Aplicativo** do seu aplicativo do Azure Active Directory. Ele é usado nas etapas a seguir.

   [![ID do aplicativo do Azure Active Directory](../../includes/media/digital-twins-permissions/aad-app-reg-app-id.png)](../../includes/media//digital-twins-permissions/aad-app-reg-app-id.png#lightbox)


## <a name="obtain-an-oauth-20-token"></a>Obter um token do OAuth 2.0

[!INCLUDE [digital-twins-management-api](../../includes/digital-twins-management-api.md)]

Configure e configure o postmaster para obter um token de Azure Active Directory. Em seguida, faça uma solicitação HTTP autenticada para os Gêmeos Digitais do Azure usando o token adquirido:

1. Vá para [www.getpostman.com](https://www.getpostman.com/) para baixar o aplicativo.
1. Verifique se sua **URL de Autorização** está correta. Deve pegar o formato:

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | NOME  | Substitua por | Exemplo |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | O nome do seu locatário ou organização | `microsoft` |

1. Selecione a guia **Autorização**, selecione **OAuth 2.0** e, em seguida, selecione **Obter novo token de acesso**.

    | Campo  | Valor |
    |---------|---------|
    | Tipo de concessão | `Implicit` |
    | URL de retorno de chamada | `https://www.getpostman.com/oauth2/callback` |
    | URL de autenticação | Use a **URL de autorização** da **etapa 2** |
    | Id do Cliente | Use a **ID do aplicativo** para o Azure Active Directory aplicativo que foi criado ou reutilizado da seção anterior |
    | Escopo | Deixar em branco |
    | Estado | Deixar em branco |
    | Autenticação de cliente | `Send as Basic Auth header` |

1. Agora, o cliente deve se parecer com:

    [exemplo de token de cliente do ![postmaster](media/how-to-configure-postman/configure-postman-oauth-token.png)](media/how-to-configure-postman/configure-postman-oauth-token.png#lightbox)

1. Selecione **Solicitação de Token**.
  
1. Role para baixo e selecione **uso Token**.

## <a name="make-a-multipart-post-request"></a>Fazer uma solicitação POST com várias partes

Depois de concluir as etapas anteriores, configure o Postman para fazer uma solicitação POST com várias partes HTTP autenticada:

1. Na guia **cabeçalhos** , adicione um **tipo de conteúdo de chave de** cabeçalho de solicitação HTTP com valor `multipart/mixed`.

   [![especificar várias partes do tipo de conteúdo/misto](media/how-to-configure-postman/configure-postman-content-type.png)](media/how-to-configure-postman/configure-postman-content-type.png#lightbox)

1. Serialize dados não textuais em arquivos. Os dados JSON seriam salvos como um arquivo JSON.
1. Na guia **corpo** , selecione `form-data`. 
1. Adicione cada arquivo atribuindo um nome de **chave** , selecionando `File`.
1. Em seguida, selecione cada arquivo por meio do botão **Escolher arquivo**.

   [exemplo de corpo de formulário de cliente do ![post](media/how-to-configure-postman/configure-postman-form-body.png)](media/how-to-configure-postman/configure-postman-form-body.png#lightbox)

   >[!NOTE]
   > * O cliente Postman não requer que as partes com várias partes tenham um **Content-Type** ou **Content-Disposition** atribuído manualmente.
   > * Você não precisará especificar esses cabeçalhos para cada parte.
   > * É necessário selecionar `multipart/mixed` ou outro **Content-Type** adequado para a solicitação inteira.

1. Por fim, selecione **Enviar** para enviar sua solicitação HTTP post de várias partes. Um código de status de `200` ou `201` indica uma solicitação bem-sucedida. Você também verá a mensagem de resposta apropriada.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre as APIs de gerenciamento dos Gêmeos Digitais do Azure e como usá-las, leia [Como usar as APIs de Gerenciamento dos Gêmeos Digitais do Azure](how-to-navigate-apis.md).

- Usar solicitações com várias partes para [adicionar blobs às entidades dos Gêmeos Digitais do Azure](./how-to-add-blobs.md).

- Para saber mais sobre autenticar as APIs de Gerenciamento, leia [Autenticar com APIs](./security-authenticating-apis.md).
