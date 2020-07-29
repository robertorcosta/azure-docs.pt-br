---
title: Pré-requisitos de API-Azure Marketplace
description: Pré-requisitos para usar as APIs de Portal do Cloud Partner.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 2cce5d3463ced126a3e6e77323110e4a70024acb
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87292910"
---
<a name="api-prerequisites"></a>Pré-requisitos de API
================

> [!NOTE]
> As APIs de Portal do Cloud Partner são integradas ao e continuarão funcionando no Partner Center. A transição apresenta pequenas alterações. Examine as alterações listadas em [portal do Cloud Partner referência de API](./cloud-partner-portal-api-overview.md) para garantir que seu código continue funcionando após a transição para o Partner Center. As APIs de CPP só devem ser usadas para produtos existentes que já foram integrados antes da transição para o Partner Center; os novos produtos devem usar as APIs de envio do Partner Center.

Há dois ativos programático necessários para usar as APIs do Portal do Cloud Partner: uma entidade de serviço e um token de acesso do Azure AD (Azure Active Directory).


<a name="create-a-service-principal-in-your-azure-active-directory-tenant"></a>Criar uma entidade de serviço em seu locatário do Azure Active Directory
----------------------------------------------------------------

Primeiro, você precisa criar uma entidade de serviço no locatário do Azure AD. Este locatário será atribuído a seu próprio conjunto de permissões no Portal do Cloud Partner. O código chamará APIs usando esse locatário em vez de usar suas credenciais pessoais.  Para obter uma explicação completa da criação de uma entidade de serviço, consulte [como: usar o portal para criar um aplicativo do Azure AD e uma entidade de serviço que pode acessar recursos](../active-directory/develop/howto-create-service-principal-portal.md).


<a name="add-the-service-principal-to-your-account"></a>Adicionar a entidade de serviço à sua conta
-----------------------------------------

Agora que você criou a entidade de serviço em seu locatário, você poderá adicioná-la como um usuário à sua conta do Portal do Cloud Partner. Assim como um usuário, a entidade de serviço pode ser um proprietário ou um colaborador no portal.

Siga as etapas a seguir para adicionar a entidade de serviço:

1. Faça logon no Portal do Cloud Partner. 
2. Clique em **Usuários** na barra de menus à esquerda e escolha **Adicionar Usuário**.

   ![Adicionar um usuário ao portal](./media/cloud-partner-portal-api-prerequisites/add-user.jpg)

3. Na lista suspensa **Tipo**, selecione **Entidade de Serviço** e adicione os seguintes detalhes:

-   Um **Nome Amigável** para a entidade de serviço, por exemplo `spAccount`.
-   A **ID do Aplicativo**. Para localizar esse identificador, vá para o [portal do Azure](https://portal.azure.com), clique em **Azure Active Directory**, escolha **registros de aplicativo**e clique em seu aplicativo.
-   A **ID do locatário**, também conhecida como a **ID do diretório**, do seu locatário do Azure AD. Você pode encontrar esse identificador na página do Azure Active Directory no [portal do Azure](https://portal.azure.com), em **Propriedades**.
-   A **ID do Objeto** do objeto de entidade de serviço. Você pode obter esse identificador no portal do Azure. Acesse **Azure Active Directory**, escolha **Registros de aplicativo**, clique em seu aplicativo e clique no nome do aplicativo em **Aplicativo gerenciado no diretório local**. Em seguida, acesse a página **Propriedades**, para localizar a ID do objeto. Não pegue a ID do objeto inicial que está no aplicativo, mas sim a ID do objeto que está no aplicativo gerenciado.
-   A **função** associada à conta, que será usada para o RBAC.

     ![Adicionar um aplicativo gerenciado ao portal](./media/cloud-partner-portal-api-prerequisites/managed-app.png)

1. Clique em **Adicionar** para adicionar a entidade de serviço à sua conta.

   ![Adicionar uma entidade de serviço](./media/cloud-partner-portal-api-prerequisites/add-service-principal.jpg)


<a name="get-an-azure-ad-access-token"></a>Obter um token de acesso do Azure AD
----------------------------

As APIs do Portal do Microsoft Cloud Partner usam os seguintes protocolos e ativos durante a autenticação:

- Um token de portador JWT (Token Web JSON) para solicitar acesso a recursos
- O protocolo [OIDC](https://openid.net/connect/) (OpenID Connect) para verificar a identidade
- O [Azure AD (Azure Active Directory)](../active-directory/fundamentals/active-directory-whatis.md) como a autoridade de identidade

Há duas abordagens de princípio para adquirir um token JWT de forma programática:

- Usar a biblioteca de autenticação da Microsoft para .NET ([MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)).  Essa abordagem de nível superior é recomendada para os desenvolvedores do .NET. 
- Fazer uma solicitação **HTTP POST** para o ponto de extremidade do **token** OAuth do Azure AD, que usa o formato:

``` HTTP
POST https://login.microsoftonline.com/<tenant-id>/oauth2/token
    client_id: <application-id>
    client_secret:<application-secret>
    grant_type: client_credentials
    resource: https://cloudpartner.azure.com
```

Agora, você pode passar esse token como parte do cabeçalho de autorização para solicitações de API.

``` HTTP
GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview 
    Accept: application/json
    Authorization: Bearer <access-token>
```
> [!NOTE]
> Para todas as APIs nesta referência, o cabeçalho de autorização é sempre considerado como passado, portanto, ele não foi mencionado explicitamente.

Se você encontrar erros de autenticação em sua solicitação, confira [Solução de erros de autenticação](./cloud-partner-portal-api-troubleshooting-authentication-errors.md).
