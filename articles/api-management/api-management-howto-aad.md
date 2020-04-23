---
title: Autorize contas de desenvolvedores usando o Azure Active Directory
titleSuffix: Azure API Management
description: Como autorizar usuários usando o Active Directory do Azure no Gerenciamento de API.
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 41f9f267880d199d2e221453eea5c3584ce96881
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868403"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Autorizar contas de desenvolvedor usando o Active Directory do Azure no Gerenciamento de API do Azure

Este artigo mostra como habilitar o acesso ao portal do desenvolvedor para os usuários do Azure Active Directory (Azure AD). Este guia também mostra como gerenciar grupos de usuários do Azure AD, adicionando grupos externos que contêm os usuários.

## <a name="prerequisites"></a>Pré-requisitos

- Conclua o início rápido a seguir: [Criar uma instância do Gerenciamento de API do Azure](get-started-create-service-instance.md).
- Importar e publicar uma instância de gerenciamento de API do Azure. Para obter mais informações, consulte [Importar e publicar](import-and-publish.md).

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Autorizar contas de desenvolvedor usando o Azure AD

1. Entre no [portal do Azure](https://portal.azure.com). 
2. Selecionar ![seta](./media/api-management-howto-aad/arrow.png).
3. Digite **api** na caixa de pesquisa.
4. Selecione **Serviços de Gerenciamento de API**.
5. Selecione uma instância do Serviço de Gerenciamento de API.
6. Em **Portal desenvolvedor,** **selecione Identidades**.
7. Selecione **+Adicionar** na parte superior.

    O painel **Adicionar provedor de identidade** aparece à direita.
8. Em **Tipo de provedor**, selecione **Azure Active Directory**.

    Os controles que permitem que você insira outras informações necessárias aparecem no painel. Os controles incluem **ID do cliente** e **Segredo do cliente**. (Você obterá informações sobre esses controles posteriormente neste artigo.)
9. Anote o conteúdo da **URL redirecionar**.
    
   ![Etapas para adicionar um provedor de identidade no portal do Azure](./media/api-management-howto-aad/api-management-with-aad001.png)  
10. No navegador, abra uma guia diferente. 
11. Navegue até o [portal Azure - Inscrições de aplicativos](https://go.microsoft.com/fwlink/?linkid=2083908) para registrar um aplicativo no Active Directory.
12. Em **Gerenciar**, selecione **Registros de aplicativo**.
13. Selecione **Novo registro**. Na **página Registrar uma página de aplicativo,** defina os valores da seguinte forma:
    
    * Defina **nome** para um nome significativo. por exemplo, *desenvolvedor-portal*
    * Defina **os tipos de conta suportados** apenas para contas neste diretório **organizacional**. 
    * Defina **o URI de redirecionamento** para o valor que você tem da etapa 9. 
    * Escolha **Registrar**. 

14.  Depois que o aplicativo estiver registrado, copie o ID do **aplicativo (cliente)** da página **Visão Geral.** 
15. Volte para a sua instância de gerenciamento de API. Na janela **Adicionar provedor de identidade,** cole o valor **de Identificação do aplicativo (cliente)** na caixa ID do **cliente.**
16. Volte para a configuração do Azure AD, Selecione **Certificados & segredos** em **Gerenciar**. Selecione o botão **Novo segredo do cliente**. Insira um valor em **Descrição**, escolha uma opção para **Expira** e escolha **Adicionar**. Copie o valor secreto do cliente antes de sair da página. Isso será necessário na próxima etapa. 
17. Em **Gerenciar,** selecione **Autenticação** e selecione **tokens de ID** em **Implicit Grant**
18. Volte para sua instância de gerenciamento de API, cole o segredo na caixa secreta do **Cliente.**

    > [!IMPORTANT]
    > Certifique-se de atualizar o **Segredo do cliente** antes de a chave expirar. 
    >  
    >

19. A janela **Adicionar provedor de identidade** também contém a caixa de texto **Locatários Permitidos**. Lá, especifique os domínios das instâncias do Azure AD para os quais você deseja conceder acesso às APIs da instância de serviço Gerenciamento da API. Você pode separar múltiplos domínios com novas linhas, espaços ou vírgulas.

    > [!NOTE]
    > Você pode especificar vários domínios na seção **Locatários Permitidos**. Antes que qualquer usuário possa entrar por meio de um domínio diferente do domínio original em que o aplicativo foi registrado, um administrador global do domínio diferente deve conceder permissão para o aplicativo para acessar dados de diretório. Para conceder permissão, o administrador global deve: a. Vá para `https://<URL of your developer portal>/aadadminconsent` (por exemplo, https://contoso.portal.azure-api.net/aadadminconsent).
    > b. Digite o nome de domínio do locatário do Azure AD ao qual você deseja conceder acesso.
    > c. Selecione **Enviar**. 

20.  Depois de especificar a configuração desejada, selecione **Adicionar**.

Depois que as alterações forem salvas, os usuários na instância do Azure AD poderão entrar no portal do desenvolvedor seguindo as etapas em [Entrar no portal do desenvolvedor usando uma conta do Azure AD](#log_in_to_dev_portal).

## <a name="add-an-external-azure-ad-group"></a>Adicionar um grupo do Azure AD externo

Depois de habilitar o acesso para usuários em um inquilino Azure AD, você pode adicionar grupos Azure AD no Gerenciamento de API. Como resultado, você pode controlar a visibilidade do produto usando grupos Azure AD.

Para adicionar um grupo Ad externo do Azure no APIM, você deve primeiro concluir a seção anterior. Além disso, o aplicativo registrado deve ter acesso à `Directory.Read.All` API do Microsoft Graph com permissão seguindo estas etapas: 

1. Volte ao seu Registro de Aplicativos que foi criado na seção anterior.
2. Selecione **permissões de API**e clique **em +Adicionar uma permissão**. 
3. No painel Solicitar permissões de **API,** selecione a guia **APIs** da Microsoft e selecione o bloco de azulejo **sino** do Microsoft Graph. Selecione **permissões de aplicativo,** procure **diretório**e selecione a permissão **Directory.Read.All.** 
4. Clique em **Adicionar permissões** na parte inferior do painel e, em seguida, clique em **Conceder consentimento de administrador para {tenantname} para** que você conceda acesso a todos os usuários neste diretório. 

Agora você pode adicionar grupos Ad externos do Azure na guia **Grupos** da instância De Gerenciamento de API.

1. Selecione a guia **Grupos** .
2. Selecione o botão **Adicionar grupo do AAD**.
    ![Botão “Adicionar grupo do AAD”](./media/api-management-howto-aad/api-management-with-aad008.png)
3. Selecione o grupo que deseja adicionar.
4. Pressione o botão **Selecionar**.

Depois de adicionar um grupo do Azure AD externo, você poderá revisar e configurar as propriedades. Selecione o nome do grupo na guia **Grupos.** A partir daqui, você pode editar informações **de nome** e **descrição** para o grupo.
 
Os usuários da instância do Azure AD configurada já podem entrar no portal do desenvolvedor. Eles podem exibir e se inscrever em todos os grupos para os quais têm visibilidade.

## <a name="developer-portal---add-azure-ad-account-authentication"></a><a id="log_in_to_dev_portal"/>Portal do desenvolvedor - adicione a autenticação da conta Azure AD

No portal do desenvolvedor, o login com a AAD é possível com o **botão de login: widget OAuth.** O widget já está incluído na página de login do conteúdo padrão do portal do desenvolvedor.

Embora uma nova conta seja criada automaticamente sempre que um novo usuário entrar com o AAD, você pode considerar adicionar o mesmo widget à página de inscrição.

O **formulário de inscrição: Owidget OAuth** representa um formulário usado para se inscrever com o OAuth.

> [!IMPORTANT]
> Você precisa [republicar o portal](api-management-howto-developer-portal-customize.md#publish) para que as alterações do AAD entrem em vigor.

## <a name="legacy-developer-portal---how-to-sign-in-with-azure-ad"></a>Portal de desenvolvedores legados - como fazer login com o Azure AD

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

Para entrar no portal do desenvolvedor usando uma conta do Azure AD que você configurou nas seções anteriores:

1. Abra uma nova janela do navegador usando a URL de entrada na configuração do aplicativo do Active Directory e selecione **Azure Active Directory**.

   ![Página de entrada][api-management-dev-portal-signin]

1. Insira as credenciais de um dos usuários no Azure AD e selecione **Entrar**.

   ![Entrada com nome de usuário e senha][api-management-aad-signin]

1. Pode ser solicitado que você preencha um formulário de registro se qualquer informação adicional for necessária. Preencha o formulário de registro e selecione **Inscrever-se**.

   ![Botão "Inscrever-se" no formulário de registro][api-management-complete-registration]

Agora o usuário está conectado no portal do desenvolvedor para a instância de serviço de Gerenciamento de API.

![Portal do desenvolvedor após a conclusão do registro][api-management-registration-complete]

[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
