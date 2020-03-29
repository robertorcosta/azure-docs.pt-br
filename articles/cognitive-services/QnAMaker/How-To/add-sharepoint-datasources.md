---
title: Arquivos SharePoint - QnA Maker
description: Adicione fontes de dados sharePoint seguras à sua base de conhecimento para enriquecer a base de conhecimento com perguntas e respostas que podem ser protegidas com o Active Directory.
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 6f05079e39c8afb001bd4ba09d68f435c18efad5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294873"
---
# <a name="add-a-secured-sharepoint-data-source-to-your-knowledge-base"></a>Adicione uma fonte de dados sharePoint segura à sua base de conhecimento

Adicione fontes de dados SharePoint baseadas em nuvem seguras à sua base de conhecimento para enriquecer a base de conhecimento com perguntas e respostas que podem ser protegidas com o Active Directory.

Quando você adiciona um documento sharepoint protegido à sua base de conhecimento, como gerente do QnA Maker, você deve solicitar permissão do Active Directory para o QnA Maker. Uma vez que essa permissão é dada do gerenciador active directory ao QnA Maker para acesso ao SharePoint, ela não precisa ser dada novamente. Cada adição de documento subseqüente à base de conhecimento não precisará de autorização se estiver no mesmo recurso do SharePoint.

Se o gerente da base de conhecimento Do Fabricante de QnA não for o gerente do Active Directory, você precisará se comunicar com o gerente do Active Directory para concluir esse processo.

## <a name="prerequisites"></a>Pré-requisitos

* SharePoint - QnA Maker, baseado em nuvem, usa o Microsoft Graph para permissões. Se o Seu SharePoint estiver no local, você não poderá extrair do SharePoint porque o Microsoft Graph não será capaz de determinar permissões.
* Formato de URL - QnA Maker só suporta urls SharePoint que são gerados para compartilhamento e são de formato`https://\*.sharepoint.com`

## <a name="add-supported-file-types-to-knowledge-base"></a>Adicionar tipos de arquivos suportados à base de conhecimento

Você pode adicionar todos os tipos de [arquivos](../Concepts/content-types.md) suportados pelo QnA Maker a partir de um site do SharePoint à sua base de conhecimento. Você pode ter que conceder [permissões](#permissions) se o recurso de arquivo estiver protegido.

1. Na biblioteca com o site SharePoint, selecione o `...`menu ellipsis do arquivo, .
1. Copie a URL do arquivo.

   ![Obtenha a URL do arquivo SharePoint selecionando o menu ellipsis do arquivo e copiando a URL.](../media/add-sharepoint-datasources/get-sharepoint-file-url.png)

1. No portal QnA Maker, na página **Configurações,** [adicione a URL](manage-knowledge-bases.md#edit-knowledge-base) à base de conhecimento.

### <a name="images-with-sharepoint-files"></a>Imagens com arquivos SharePoint

Se os arquivos incluem imagens, essas não serão extraídas. Você pode adicionar a imagem, a partir do portal QnA Maker, depois que o arquivo é extraído em pares de QnA.

Adicione a imagem com a seguinte sintaxe de marcação:

```markdown
![Explanation or description of image](URL of public image)
```

O texto nos suportes `[]`quadrados, explica a imagem. A URL nos parênteses é `()`o link direto para a imagem.

Quando você testa o par De QnA no painel de teste interativo, no portal QnA Maker, a imagem é exibida, em vez do texto de marcação. Isso valida que a imagem pode ser recuperada publicamente do seu aplicativo cliente.

## <a name="permissions"></a>Permissões

A concessão de permissões acontece quando um arquivo protegido de um servidor SharePoint é adicionado a uma base de conhecimento. Dependendo de como o SharePoint está configurado e as permissões da pessoa adicionando o arquivo, isso pode exigir:

* sem etapas adicionais - a pessoa adicionando o arquivo tem todas as permissões necessárias.
* passos tanto pelo [gerente da base de conhecimento](#knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal) quanto pelo gerente do Active [Directory](#active-directory-manager-grant-file-read-access-to-qna-maker).

Veja as etapas listadas abaixo.

### <a name="knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal"></a>Gerente de base de conhecimento: adicione a fonte de dados SharePoint no portal QnA Maker

Quando o **gerente do QnA Maker** adiciona um documento sharePoint seguro a uma base de conhecimento, o gerente da base de conhecimento inicia um pedido de permissão que o gerente do Active Directory precisa concluir.

A solicitação começa com um pop-up para autenticar em uma conta do Active Directory.

![Autenticar conta de usuário](../media/add-sharepoint-datasources/authenticate-user-account.png)

Uma vez que o gerenciador do QnA Maker selecione a conta, o administrador do Azure Active Directory receberá um aviso de que eles precisam permitir que o aplicativo QnA Maker (não o gerenciador do QnA Maker) acesse o recurso SharePoint. O gerenciador do Azure Active Directory precisará fazer isso para cada recurso do SharePoint, mas não todos os documentos desse recurso.

### <a name="active-directory-manager-grant-file-read-access-to-qna-maker"></a>Gerenciador ativo de diretórios: arquivo de concessão ler acesso ao QnA Maker

O gerente do Active Directory (não o gerente do QnA Maker) precisa conceder acesso ao QnA Maker para acessar o recurso SharePoint, selecionando [este link](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=Files.Read%20Files.Read.All%20Sites.Read.All%20User.Read%20User.ReadBasic.All%20profile%20openid%20email&client_id=c2c11949-e9bb-4035-bda8-59542eb907a6&redirect_uri=https%3A%2F%2Fwww.qnamaker.ai%3A%2FCreate&state=68) para autorizar o aplicativo corporativo QnA Maker Portal SharePoint a ter permissões de leitura de arquivo.

![O gerente do Azure Active Directory concede permissão de forma interativa](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)

<!--
The Active Directory manager must grant QnA Maker access either by application name, `QnAMakerPortalSharePoint`, or by application ID, `c2c11949-e9bb-4035-bda8-59542eb907a6`.
-->
<!--
### Grant access from the interactive pop-up window

The Active Directory manager will get a pop-up window requesting permissions to the `QnAMakerPortalSharePoint` app. The pop-up window includes the QnA Maker Manager email address that initiated the request, an `App Info` link to learn more about **QnAMakerPortalSharePoint**, and a list of permissions requested. Select **Accept** to provide those permissions.

![Azure Active Directory manager grants permission interactively](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)
-->
<!--

### Grant access from the App Registrations list

1. The Active Directory manager signs in to the Azure portal and opens **[App registrations list](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ApplicationsListBlade)**.

1. Search for and select the **QnAMakerPortalSharePoint** app. Change the second filter box from **My apps** to **All apps**. The app information will open on the right side.

    ![Select QnA Maker app in App registrations list](../media/add-sharepoint-datasources/select-qna-maker-app-in-app-registrations.png)

1. Select **Settings**.

    [![Select Settings in the right-side blade](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png)](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png#lightbox)

1. Under **API access**, select **Required permissions**.

    ![Select 'Settings', then under 'API access', select 'Required permission'](../media/add-sharepoint-datasources/select-required-permissions-in-settings-blade.png)

1. Do not change any settings in the **Enable Access** window. Select **Grant Permission**.

    [![Under 'Grant Permission', select 'Yes'](../media/add-sharepoint-datasources/grant-app-required-permissions.png)](../media/add-sharepoint-datasources/grant-app-required-permissions.png#lightbox)

1. Select **YES** in the pop-up confirmation windows.

    ![Grant required permissions](../media/add-sharepoint-datasources/grant-required-permissions.png)
-->
### <a name="grant-access-from-the-azure-active-directory-admin-center"></a>Conceder acesso do centro de administrador esporu do Diretório Ativo do Azure

1. O gerenciador de Diretório Ativo entra no portal Azure e abre **[aplicativos Enterprise](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps)**.

1. Procure `QnAMakerPortalSharePoint` o aplicativo Select The QnA Maker.

    [![Procure por QnAMakerPortalSharePoint na lista de aplicativos corporativos](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png)](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png#lightbox)

1. Em **Segurança,** vá para **Permissões**. Selecione **o consentimento do grant para organização**.

    [![Selecione o usuário autenticado para o Active Directory Admin](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png)](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png#lightbox)

1. Selecione uma conta de Login com permissões para conceder permissões para o Diretório Ativo.



<!--

## Add SharePoint data source with APIs

You need to get the SharePoint file's URI before adding it to QnA Maker.

## Get SharePoint File URI

Use the following steps to transform the SharePoint URL into a sharing token.

1. Encode the URL using [base64](https://en.wikipedia.org/wiki/Base64).

1. Convert the base64-encoded result to an unpadded base64url format with the following character changes.

    * Remove the equal character, `=` from the end of the value.
    * Replace `/` with `_`.
    * Replace `+` with `-`.
    * Append `u!` to be beginning of the string.

1. Sign in to Graph explorer and run the following query, where `sharedURL` is ...:

    ```
    https://graph.microsoft.com/v1.0/shares/<sharedURL>/driveitem
    ```

    Get the **@microsoft.graph.downloadUrl** and use this as `fileuri` in the QnA Maker APIs.

### Add or update a SharePoint File URI to your knowledge base

Use the **@microsoft.graph.downloadUrl** from the previous section as the `fileuri` in the QnA Maker API for [adding a knowledge base](https://go.microsoft.com/fwlink/?linkid=2092179) or [updating a knowledge base](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update). The following fields are mandatory: name, fileuri, filename, source.

```
{
    "name": "Knowledge base name",
    "files": [
        {
            "fileUri": "<@microsoft.graph.downloadURL>",
            "fileName": "filename.xlsx",
            "source": "<SharePoint link>"
        }
    ],
    "urls": [],
    "users": [],
    "hostUrl": "",
    "qnaList": []
}
```



## Remove QnA Maker app from SharePoint authorization

1. Use the steps in the previous section to find the Qna Maker app in the Active Directory admin center.
1. When you select the **QnAMakerPortalSharePoint**, select **Overview**.
1. Select **Delete** to remove permissions.

-->

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Colaborar com sua base de dados de conhecimento](collaborate-knowledge-base.md)
