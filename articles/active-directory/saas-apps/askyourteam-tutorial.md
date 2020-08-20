---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao AskYourTeam | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o AskYourTeam.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/28/2020
ms.author: jeedes
ms.openlocfilehash: ad68208f8993ce84c71c97d97de7e2b090f1e7da
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549406"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-askyourteam"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao AskYourTeam

Neste tutorial, você aprenderá a integrar o AskYourTeam ao Azure AD (Azure Active Directory). Ao integrar o AskYourTeam ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao AskYourTeam.
* Permitir que os usuários sejam conectados automaticamente ao AskYourTeam com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do AskYourTeam habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O AskYourTeam dá suporte ao SSO iniciado por **SP e IdP**.
* Depois de configurar o AskYourTeam, você poderá impor um controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-askyourteam-from-the-gallery"></a>Como adicionar o AskYourTeam por meio da galeria

Para configurar a integração do AskYourTeam ao Azure AD, você precisará adicionar o AskYourTeam por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **AskYourTeam** na caixa de pesquisa.
1. Selecione **AskYourTeam** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-askyourteam"></a>Configurar e testar o logon único do Azure AD para o AskYourTeam

Configure e teste o SSO do Azure AD com o AskYourTeam usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no AskYourTeam.

Para configurar e testar o SSO do Azure AD com o AskYourTeam, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do AskYourTeam](#configure-askyourteam-sso)** : para definir as configurações de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do AskYourTeam](#create-askyourteam-test-user)** : para ter um equivalente de B.Fernandes no AskYourTeam que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **AskYourTeam**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    Na caixa de texto **URL de Resposta**, digite uma URL usando o seguinte padrão: `https://<COMPANY>.app.askyourteam.com/users/auth/saml/callback`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<COMPANY>.app.askyourteam.com/login`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com os valores reais da URL de Resposta e da URL de Logon, que serão explicados mais adiante no tutorial.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o AskYourTeam**, copie as URLs apropriadas de acordo com as suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao AskYourTeam.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **AskYourTeam**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-askyourteam-sso"></a>Configurar o SSO do AskYourTeam

1. Em outra janela do navegador da Web, entre no site do AskYourTeam como administrador.

1. Clique em **Minha Organização**.

    ![Configuração do AskYourTeam](./media/askyourteam-tutorial/user1.png)

1. Clique em **Integrações**.

    ![Configuração do AskYourTeam](./media/askyourteam-tutorial/configure1.png)

1. Clique em **Editar Configurações**.

    ![Configuração do AskYourTeam](./media/askyourteam-tutorial/configure2.png)

1. Na página **Editar a Integração de Logon Único**, execute as seguintes etapas: 

    ![Configuração do AskYourTeam](./media/askyourteam-tutorial/configure3.png)

    a. Na caixa de texto **URL de Serviço de Logon Único do SAML**, cole o valor da **URL do Logon** copiado do portal do Azure.

    b. Na caixa de texto **ID da Entidade do SAML**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    c. Na caixa de texto **URL de Logoff**, cole o valor da **URL de Logoff** copiado do portal do Azure.

    d. Abra o **Certificado (Base64)** baixado do portal do Azure no Bloco de notas e cole o conteúdo na caixa de texto **Certificado de Autenticação SAML – Base64**.

    > [!NOTE]
    > Como alternativa, você também pode carregar o arquivo **XML de Metadados de Federação** clicando na opção **Escolher Arquivo**.

    e. Copie o valor da **URL de Resposta (URL do Serviço do Consumidor de Declaração)** e cole esse valor na caixa de texto **URL de Resposta** na seção **Configuração Básica do SAML** no portal do Azure.

    f. Copie a **URL de Logon**, cole esse valor na caixa de texto **URL de Logon** na seção **Configuração Básica de SAML** no portal do Azure.

    g. Clique em **Save** (Salvar).

### <a name="create-askyourteam-test-user"></a>Criar um usuário de teste do AskYourTeam

1. Em outra janela do navegador da Web, entre no site do AskYourTeam como administrador.

1. Clique em **Minha Organização**.

    ![Configuração do AskYourTeam](./media/askyourteam-tutorial/user1.png)

1. Clique em **Usuários** e selecione **Novo Usuário**.

    ![Configuração do AskYourTeam](./media/askyourteam-tutorial/user2.png)

1. Na seção **Novo Usuário**, execute as seguintes etapas:

    ![Configuração do AskYourTeam](./media/askyourteam-tutorial/user3.png)

    1. Na caixa de texto **Nome**, insira o nome do usuário.

    1. Na caixa de texto **Sobrenome**, insira o sobrenome do usuário.

    1. Na caixa de texto **Email**, insira o endereço de email do usuário como B.Simon@contoso.com.

    1. Selecione a **Função** para o usuário de acordo com o requisito da sua organização.

    1. Clique em **Save** (Salvar).

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do AskYourTeam no Painel de Acesso, você deverá ser conectado automaticamente ao AskYourTeam, para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimentar o AskYourTeam com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
