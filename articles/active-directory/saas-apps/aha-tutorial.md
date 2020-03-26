---
title: 'Tutorial: Integração do Azure Active Directory ao Aha! | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Aha!.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ad955d3d-896a-41bb-800d-68e8cb5ff48d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/09/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a63872680d28664c6d5a7ff109f6de72817173d5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "68989658"
---
# <a name="tutorial-integrate-aha-with-azure-active-directory"></a>Tutorial: Integre o Aha! ao Azure Active Directory

Neste tutorial, você aprende a integrar o Aha! ao Azure AD (Azure Active Directory). Quando você integra o Aha! ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Aha!.
* Permitir que os usuários se conectem automaticamente ao Aha! com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Aha! assinatura habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* Aha! é compatível com o SSO iniciado por **SP**
* Aha! é compatível com o provisionamento de usuário **Just In Time**

## <a name="adding-aha-from-the-gallery"></a>Adicionando o Aha! por meio da galeria

Para configurar a integração do Aha! ao Azure AD, você precisa adicionar o Aha! à lista de aplicativos SaaS gerenciados por meio da galeria.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Aha!** na caixa de pesquisa.
1. Selecione **Aha!** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-aha"></a>Configurar e testar logon único do Azure AD para o Aha!

Configurar e testar o SSO do Azure AD com o Aha! usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Aha!.

Para configurar e testar o SSO do Azure AD com o Aha!, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
2. **[Configurar o SSO do Aha!](#configure-aha-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Aha!](#create-aha-test-user)** – para ter um equivalente de B.Fernandes no Aha! que esteja vinculado à representação de usuário do Azure AD.
3. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Aha!** , localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<companyname>.aha.io/session/new`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<companyname>.aha.io`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao Cliente do Aha!](https://www.aha.io/company/contact) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

4. Na página **Configurar o Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e clique em **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar o Aha!** , copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao Aha!.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Aha!** .
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-aha-sso"></a>Configurar o Aha! SSO

1. Para automatizar a configuração no Aha!, é necessário instalar a **Extensão do navegador de Entrada Segura dos Meus Aplicativos**, clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, clicar em **Configurar o Aha!** o direcionará para o Aha! . De lá, forneça as credenciais de administrador para entrar no Aha!. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 8.

    ![Configuração da instalação](common/setup-sso.png)

3. Se você quiser configurar o Aha! manualmente, abra uma nova janela do navegador da Web e entre no site da empresa do Aha! como administrador e execute as seguintes etapas:

4. No menu na parte superior, clique em **Configurações**.

    ![Configurações](./media/aha-tutorial/IC798950.png "Configurações")

5. Clique em **Conta**.
  
    ![Perfil](./media/aha-tutorial/IC798951.png "Perfil")

6. Clique em **Segurança e logon único**.

    ![Segurança e logon único](./media/aha-tutorial/IC798952.png "Segurança e logon único")

7. Na seção **Logon Único**, como **Provedor de Identidade**, selecione **SAML2.0**.

    ![Segurança e logon único](./media/aha-tutorial/IC798953.png "Segurança e logon único")

8. Na página de configuração **Logon Único** , realize as seguintes etapas:

    ![Logon único](./media/aha-tutorial/IC798954.png "Logon Único")

    a. Na caixa de texto **Nome** , digite um nome para a sua configuração.

    b. Para **Configurar usando**, selecione **Arquivo de Metadados**.

    c. Para carregar seu arquivo de metadados baixado, clique em **Procurar**.

    d. Clique em **Atualizar**.

### <a name="create-aha-test-user"></a>Criar teste de usuário do Aha!

Nesta seção, um usuário chamado B.Fernandes será criado no Aha!. Aha! dá suporte ao provisionamento do usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Aha!, será criado um novo usuário após a autenticação.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando clicar no bloco Aha! no Painel de Acesso, você deverá ser conectado automaticamente ao Aha! para o qual configurou o logon único. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

