---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao SharingCloud  | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Instant Suite.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: jeedes
ms.openlocfilehash: 7ae447a9577feba8b43b5b03a757ec4095ee2cb4
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102177888"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sharingcloud"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao SharingCloud

Neste tutorial, você aprenderá a integrar o SharingCloud ao Azure AD (Azure Active Directory). Ao integrar o SharingCloud ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao SharingCloud.
* Permitir que os usuários sejam conectados automaticamente ao SharingCloud com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Se você quiser saber mais sobre a integração de aplicativo SaaS com o Azure AD, confira [O que é acesso de aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* Assinatura habilitada para SSO (logon único) do SharingCloud.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O SharingCloud é compatível com SSO iniciado por **SP e IDP**
* O SharingCloud é compatível com o provisionamento de usuário **Just-In-Time**

## <a name="adding-sharingcloud-from-the-gallery"></a>Como adicionar o SharingCloud por meio da galeria

Para configurar a integração do SharingCloud ao Azure AD, você precisa adicionar o SharingCloud por meio da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)
    
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)
    
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.

    ![O botão Novo aplicativo](common/add-new-app.png)
    
1. Na seção **Adicionar por meio da galeria**, digite **SharingCloud** na caixa de pesquisa.

    ![SharingCloud na lista de resultados](common/search-new-app.png)
    
1. Selecione **SharingCloud** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sharingcloud"></a>Configurar e testar o logon único do Azure AD para o SharingCloud

Configure e teste o SSO do Azure AD com o SharingCloud usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SharingCloud.

Para configurar e testar o SSO do Azure AD com o SharingCloud, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do SharingCloud](#configure-sharingcloud-sso)** – Para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do SharingCloud](#create-sharingcloud-test-user)** – Para ter um equivalente de B.Fernandes no SharingCloud que esteja vinculado à declaração de usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **SharingCloud**, localize a seção **Gerenciar** e selecione **logon único**.
    
    ![Link Configurar logon único](common/select-sso.png)
    
1. Na página **Selecionar um método de logon único**, escolha **SAML**.

    ![Modo de seleção de logon único](common/select-saml-option.png)

1. Na página **Configurar o logon único com o SAML**, clique no ícone **Editar** para a **Configuração Básica do SAML** a fim de editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    Carregue o arquivo de metadados com o arquivo XML fornecido pelo SharingCloud. Entre em contato com a [equipe de suporte ao cliente do SharingCloud](mailto:support@sharingcloud.com) para obter o arquivo.

    ![image](common/upload-metadata.png)
    
    Selecione o arquivo de metadados fornecido e clique em **Carregar**.

    ![image](common/browse-upload-metadata.png)

1. O aplicativo SharingCloud espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizados à configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/edit_attribute.png)

1. Além do indicado acima, o aplicativo do SharingCloud espera que mais alguns atributos sejam passados novamente na resposta SAML, os quais são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seu requisito.

    | Nome | Atributo de Origem|
    | ---------------| --------- |
    | urn:sharingcloud:sso:firstname | user.givenname |
    | urn:sharingcloud:sso:lastname | user.surname |
    | urn:sharingcloud:sso:email | user.mail |

1. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique no ícone **Copiar** para copiar a **URL de Metadados de Federação** das opções fornecidas conforme seus requisitos.

    ![A URL de metadados a ser copiada](common/copy_metadataurl.png)

## <a name="configure-sharingcloud-sso"></a>Configurar o SSO do SharingCloud

Para configurar logon único no **SharingCloud**, será necessário enviar a **URL de Metadados de Federação** copiada do portal do Azure para a [equipe de suporte do SharingCloud](mailto:support@sharingcloud.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você habilitará que B.Fernandes use o logon único do Azure permitindo que ela tenha acesso ao SharingCloud.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **SharingCloud**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

   ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-sharingcloud-test-user"></a>Criar um usuário de teste do SharingCloud

Nesta seção, uma usuária chamada Brenda Fernandes é criada no SharingCloud. O SharingCloud dá suporte ao provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no SharingCloud, ele será criado após a autenticação.

## <a name="test-sso"></a>Testar o SSO 

* Acesse a URL do SharingCloud diretamente e inicie o fluxo de login nela.

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o SharingCloud, você poderá impor um controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

