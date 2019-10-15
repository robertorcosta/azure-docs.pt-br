---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Salesforce | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63cc4b902c0bd0281228e23076be6e0a18461597
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241429"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Salesforce

Neste tutorial, você aprenderá a integrar o Salesforce ao Azure AD (Azure Active Directory). Ao integrar o Salesforce ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Salesforce.
* Permitir que os usuários sejam conectados automaticamente ao Salesforce com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do Salesforce habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Salesforce dá suporte ao SSO iniciado por **SP**

* O Salesforce é compatível com o provisionamento de usuário **Just In Time**

* O Salesforce é compatível com o [provisionamento de usuário **Automatizado**](salesforce-provisioning-tutorial.md)

* Agora, o aplicativo móvel do Salesforce pode ser configurado com o Azure AD para habilitar o SSO. Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

## <a name="adding-salesforce-from-the-gallery"></a>Adicionar Salesforce da galeria

Para configurar a integração do Salesforce ao Azure AD, você precisará adicionar o Salesforce da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Salesforce** na caixa de pesquisa.
1. Selecione **Salesforce** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-salesforce"></a>Configurar e testar o logon único do Azure AD para o Salesforce

Configure e teste o SSO do Azure AD com o Salesforce usando um usuário de teste chamado **B. Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Salesforce.

Para configurar e testar o SSO do Azure AD com o Salesforce, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
2. **[Configurar o SSO do Salesforce](#configure-salesforce-sso)** – para definir as configurações de Logon Único no lado do aplicativo.
    1. **[Criar um usuário de teste do Salesforce](#create-salesforce-test-user)** – para ter um equivalente de B. Fernandes no Salesforce que esteja vinculado à representação do usuário no Azure AD.
3. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Salesforce, execute as seguintes etapas:

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Salesforce**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    a. Na caixa de texto **URL de Logon**, digite o valor usando o seguinte padrão:

    Conta empresarial: `https://<subdomain>.my.salesforce.com`

    Conta de desenvolvedor: `https://<subdomain>-dev-ed.my.salesforce.com`

    b. Na caixa de texto **Identificador**, digite o valor usando o seguinte padrão:

    Conta empresarial: `https://<subdomain>.my.salesforce.com`

    Conta de desenvolvedor: `https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte do Cliente Salesforce](https://help.salesforce.com/support) para obter esses valores.

1. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com suas necessidades e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o Salesforce**, copie as URLs apropriadas de acordo com suas necessidades.

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
   
    > [!NOTE]
    > Os atributos de usuário do Salesforce diferenciam maiúsculas de minúsculas para validação de SAML.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B. Fernandes use o logon único do Azure ao conceder a ela o acesso ao Salesforce.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Salesforce**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-salesforce-sso"></a>Configurar o SSO do Salesforce

1. Abra uma nova guia no navegador e entre em sua conta de administrador do Salesforce.

2. Clique na **Configuração** no **ícone de configurações**, no canto superior direito da página.

    ![Configurar o logon único](./media/salesforce-tutorial/configure1.png)

3. Role para baixo até **CONFIGURAÇÕES** no painel de navegação e clique em **Identidade** para expandir a seção correspondente. Em seguida, clique em **Configurações de Logon Único**.

    ![Configurar o logon único](./media/salesforce-tutorial/sf-admin-sso.png)

4. Na página **Configurações de Logon Único**, clique no botão **Editar**.

    ![Configurar o logon único](./media/salesforce-tutorial/sf-admin-sso-edit.png)

    > [!NOTE]
    > Se não for possível habilitar as configurações de Logon Único para a conta do Salesforce, talvez seja necessário entrar em contato com a [equipe de suporte do Cliente Salesforce](https://help.salesforce.com/support).

5. Selecione **SAML Habilitado** e, em seguida, clique em **Salvar**.

      ![Configurar o logon único](./media/salesforce-tutorial/sf-enable-saml.png)

6. Para configurar as configurações de logon único do SAML, clique em **Novo do arquivo de metadados**.

    ![Configurar o logon único](./media/salesforce-tutorial/sf-admin-sso-new.png)

7. Clique em **Escolher Arquivo** para carregar o arquivo XML de metadados que você baixou do portal do Azure e clique em **Criar**.

    ![Configurar o logon único](./media/salesforce-tutorial/xmlchoose.png)

8. Na página **Configurações de logon único do SAML**, os campos são preenchidos automaticamente, clique em Salvar.

    ![Configurar o logon único](./media/salesforce-tutorial/salesforcexml.png)

9. No painel de navegação à esquerda no Salesforce, clique em **Configurações da Empresa** para expandir a seção correspondente e clique em **Meu Domínio**.

    ![Configurar o logon único](./media/salesforce-tutorial/sf-my-domain.png)

10. Role para baixo até a seção **Configuração de Autenticação** e, em seguida, clique no botão **Editar**.

    ![Configurar o logon único](./media/salesforce-tutorial/sf-edit-auth-config.png)

11. Na seção **Configuração de Autenticação**, marque **AzureSSO** como **Serviço de Autenticação** da configuração de SSO do SAML e, em seguida, clique em **Salvar**.

    ![Configurar o logon único](./media/salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > Se mais de um serviço de autenticação estiver selecionado, quando os usuários tentarem iniciar o logon único em seu ambiente do Salesforce, serão solicitados a selecionar o serviço de autenticação com o qual desejam entrar. Se você não quiser que isso aconteça, deverá **deixar todos os outros serviços de autenticação desmarcados**.

### <a name="create-salesforce-test-user"></a>Criar um usuário de teste do Salesforce

Nesta seção, é criado um usuário denominado B. Fernandes no Salesforce. O Salesforce dá suporte ao provisionamento Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Salesforce, um novo será criado quando você tentar acessar o Salesforce. O Salesforce também dá suporte ao provisionamento automático de usuário. É possível encontrar [aqui](salesforce-provisioning-tutorial.md) detalhes de como configurar o provisionamento automático do usuário.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Salesforce no Painel de Acesso, você deverá ser conectado automaticamente ao Salesforce no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="test-sso-for-salesforce-mobile"></a>Testar o SSO para Salesforce (móvel)

1. Abra o aplicativo móvel do Salesforce. Na página de entrada, clique em **Usar Domínio Personalizado**.

    ![Aplicativo móvel do Salesforce](media/salesforce-tutorial/mobile-app1.png)

1. Na caixa de texto **Domínio Personalizado**, insira o nome de domínio personalizado registrado e clique em **Continuar**.

    ![Aplicativo móvel do Salesforce](media/salesforce-tutorial/mobile-app2.png)

1. Insira suas credenciais do Azure AD para entrar no aplicativo do Salesforce e clique em **Avançar**.

    ![Aplicativo móvel do Salesforce](media/salesforce-tutorial/mobile-app3.png)

1. Na página **Permitir Acesso**, conforme mostrado abaixo, clique em **Permitir** para conceder acesso ao aplicativo do Salesforce.

    ![Aplicativo móvel do Salesforce](media/salesforce-tutorial/mobile-app4.png)

1. Por fim, após entrar com sucesso, a home page do aplicativo será exibida.

    ![Aplicativo móvel do Salesforce](media/salesforce-tutorial/mobile-app5.png) ![Aplicativo móvel do Salesforce](media/salesforce-tutorial/mobile-app6.png)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configurar Provisionamento de Usuário](salesforce-provisioning-tutorial.md)

- [Experimentar o Salesforce com o Azure AD](https://aad.portal.azure.com)
