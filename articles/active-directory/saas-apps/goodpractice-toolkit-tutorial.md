---
title: 'Tutorial: Integração do Azure Active Directory ao Mind Tools Toolkit | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Mind Tools Toolkit.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 65b2979d-9e2f-4530-bc08-546975269ebc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/12/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b12bd8ba7998b924035a0946f9e32b88ce206e4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "79476494"
---
# <a name="tutorial-azure-active-directory-integration-with-mind-tools-toolkit"></a>Tutorial: Integração do Azure Active Directory ao Mind Tools Toolkit

Neste tutorial, você aprende a integrar o Mind Tools Toolkit ao Azure AD (Azure Active Directory).
A integração do Mind Tools Toolkit com o Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao Mind Tools Toolkit.
* Você pode permitir que seus usuários entrem automaticamente no Mind Tools Toolkit (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Mind Tools Toolkit, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do Mind Tools Toolkit habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Mind Tools Toolkit dá suporte ao SSO iniciado por **SP**
* O Mind Tools Toolkit dá suporte ao provisionamento de usuário **Just-In-Time**
* Depois de configurar o Mind Tools Toolkit, você poderá impor o controle de sessão, que fornece proteção contra exportação e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-mind-tools-toolkit-from-the-gallery"></a>Como adicionar o Mind Tools Toolkit por meio da galeria

Para configurar a integração do Mind Tools Toolkit ao Azure AD, é necessário adicionar o Mind Tools Toolkit à lista de aplicativos SaaS gerenciados por meio da galeria.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Mind Tools Toolkit** na caixa de pesquisa.
1. Selecione **Mind Tools Toolkit** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você vai configurar e vai testar o logon único do Azure AD com o Mind Tools Toolkit, com base em um usuário de teste chamado **B. Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Mind Tools Toolkit.

Para configurar e testar o logon único do Azure AD com o Mind Tools Toolkit, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Mind Tools Toolkit](#configure-mind-tools-toolkit-sso)** – para definir as configurações de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do Mind Tools Toolkit](#create-mind-tools-toolkit-test-user)** – para ter um equivalente de B.Fernandes no Mind Tools Toolkit que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Mind Tools Toolkit, realize as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativo **Mind Tools Toolkit**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    Na caixa de texto **URL de login**, digite um URL usando o seguinte padrão: `https://app.goodpractice.net/#/<subscriptionUrl>/s/<locationId>`.

    > [!Note]
    > O valor da URL de logon não é real. Atualize o valor com a URL de Logon real. Entre em contato com a [equipe de suporte ao cliente do Mind Tools Toolkit](mailto:support@goodpractice.com) para obter o valor.

5. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar o Mind Tools Toolkit**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

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

Nesta seção, você permitirá que o B.Fernandes use o logon único do Azure, concedendo acesso ao Mind Tools Toolkit.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Mind Tools Toolkit**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-mind-tools-toolkit-sso"></a>Configurar o SSO do Mind Tools Toolkit

Para configurar o logon único no lado do **Mind Tools Toolkit** é necessário enviar o **XML de metadados de federação** baixado e as URLs copiadas apropriadas do portal do Azure para a [equipe de suporte do Mind Tools Toolkit](mailto:support@goodpractice.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-mind-tools-toolkit-test-user"></a>Criar usuário de teste do Mind Tools Toolkit

Nesta seção, um usuário chamado B. Fernandes será criado no Mind Tools Toolkit. O Mind Tools Toolkit dá suporte ao **provisionamento Just-In-Time**, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Mind Tools Toolkit, um novo será criado quando você tentar acessar o Mind Tools Toolkit.

### <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Mind Tools Toolkit no painel de acesso, você será conectado automaticamente ao Mind Tools Toolkit no qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Mind Tools Toolkit com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger o Mind Tools Toolkit com visibilidade e controles avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)