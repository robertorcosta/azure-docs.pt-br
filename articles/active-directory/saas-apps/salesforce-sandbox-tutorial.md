---
title: 'Tutorial: Integração do Azure Active Directory com a Área Restrita do Salesforce | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Salesforce Sandbox.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9d6d11b6b56483f954049fdc1858db31f35c14a
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2020
ms.locfileid: "76289996"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce-sandbox"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Salesforce Sandbox

Neste tutorial, você aprenderá como integrar o Salesforce Sandbox ao Azure AD (Azure Active Directory). Ao integrar o Salesforce Sandbox ao Azure AD, você poderá:

* Controlar quem tem acesso ao Salesforce Sandbox no Azure AD.
* Permitir que os usuários sejam conectados automaticamente ao Salesforce Sandbox com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisites

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Salesforce Sandbox.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Salesforce Sandbox dá suporte ao SSO iniciado por **SP e IDP**
* O Salesforce Sandbox é compatível com o provisionamento de usuário **Just In Time**
* O Salesforce Sandbox é compatível com o [provisionamento de usuário **Automatizado**](salesforce-sandbox-provisioning-tutorial.md)
* Após configurar o Salesforce Sandbox, você poderá impor controles de sessão, que protegem o vazamento e a infiltração de dados confidenciais de sua organização em tempo real. Os controles da sessão são estendidos do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Adicionando o Salesforce Sandbox por meio da galeria

Para configurar a integração do Salesforce Sandbox ao Azure AD, é necessário adicionar o Salesforce Sandbox à lista de aplicativos SaaS gerenciados por meio da galeria.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Salesforce Sandbox** na caixa de pesquisa.
1. Selecione **Salesforce Sandbox** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-salesforce-sandbox"></a>Configurar e testar o logon único do Azure AD para o Salesforce Sandbox

Configure e teste o SSO do Azure AD com o Salesforce Sandbox usando uma usuária de teste chamada **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Salesforce Sandbox.

Para configurar e testar o SSO do Azure AD com o Salesforce Sandbox, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Sandbox](#configure-salesforce-sandbox-sso)** – para configurar o logon único no lado do aplicativo.
    * **[Criar um usuário de teste do Salesforce Sandbox](#create-salesforce-sandbox-test-user)** – para ter um equivalente de B.Fernandes no Salesforce Sandbox que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Salesforce Sandbox**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica de SAML**, se você tiver um **Arquivo de metadados do Provedor de Serviço** e quiser configurar o modo iniciado por **IDP**, execute as etapas a seguir:

    a. Clique em **Carregar arquivo de metadados**.

    ![Carregar arquivo de metadados](common/upload-metadata.png)

    b. Clique no **logotipo da pasta** para selecionar o arquivo de metadados e depois em **Carregar**.

    ![Escolha o arquivo de metadados](common/browse-upload-metadata.png)

    > [!NOTE]
    > Você obterá o arquivo de metadados do provedor de serviços no portal de administração do Salesforce Sandbox, explicado posteriormente no tutorial.

    c. Depois que o arquivo de metadados for enviado, o **URL de resposta** será preenchido automaticamente na caixa de texto **URL de resposta**.

    ![image](common/both-replyurl.png)

    > [!Note]
    > Se o valor da **URL de Resposta** não for preenchido automaticamente, preencha-o manualmente de acordo com seus requisitos.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Download** para baixar o **XML de metadados** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar o Salesforce Sandbox**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Salesforce Sandbox.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Salesforce Sandbox**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-salesforce-sandbox-sso"></a>Configurar o SSO do Salesforce Sandbox

1. Abra uma nova guia no navegador e entre em sua conta Administrador do Salesforce Sandbox.

2. Clique na **Configuração** no **ícone de configurações**, no canto superior direito da página.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/configure1.png)

3. Role para baixo até **CONFIGURAÇÕES** no painel de navegação esquerdo e clique em **Identidade** para expandir a seção correspondente. Em seguida, clique em **Configurações de Logon Único**.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

4. Na página **Configurações de Logon Único**, clique no botão **Editar**.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/configure3.png)

5. Selecione **SAML Habilitado** e, em seguida, clique em **Salvar**.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

6. Para configurar as configurações de logon único do SAML, clique em **Novo do arquivo de metadados**.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

7. Clique em **Escolher Arquivo** para carregar o arquivo XML de metadados que você baixou do portal do Azure e clique em **Criar**.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/xmlchoose.png)

8. Na página **Configurações de logon único do SAML**, os campos são preenchidos automaticamente, clique em Salvar.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/salesforcexml.png)

9. Na página **Configurações de Logon Único**, clique no botão **Baixar Metadados** para baixar o arquivo de metadados do provedor de serviços. Usar esse arquivo a **básicas de configuração de SAML** seção no portal do Azure para configurar as URLs necessárias, conforme explicado acima.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/configure4.png)

10. Se você desejar configurar o aplicativo no modo iniciado **SP**, os pré-requisitos disso estão a seguir:

    a. Você deve ter um domínio verificado.

    b. Você precisa configurar e habilitar seu domínio na Área Restrita do Salesforce, as etapas para isso serão explicadas posteriormente neste tutorial.

    c. No portal do Azure, sobre o **básicas de configuração de SAML** seção, clique em **definir URLs adicionais** e execute a seguinte etapa:
  
    ![Informações sobre logon único de URLs e Domínio da a Área Restrita Salesforce](common/both-signonurl.png)

    Na caixa de texto **URL de Logon**, digite o valor usando o seguinte padrão: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    > [!NOTE]
    > Esse valor deve ser copiado do portal da Área Restrita Salesforce depois que você tiver habilitado o domínio.

11. Sobre o **certificado de autenticação SAML** seção, clique em **XML de metadados de Federação** e, em seguida, salve o arquivo xml em seu computador.

    ![O link de download do Certificado](common/metadataxml.png)

12. Abra uma nova guia no navegador e entre em sua conta Administrador do Salesforce Sandbox.

13. Clique na **Configuração** no **ícone de configurações**, no canto superior direito da página.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/configure1.png)

14. Role para baixo até **CONFIGURAÇÕES** no painel de navegação esquerdo e clique em **Identidade** para expandir a seção correspondente. Em seguida, clique em **Configurações de Logon Único**.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

15. Na página **Configurações de Logon Único**, clique no botão **Editar**.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/configure3.png)

16. Selecione **SAML Habilitado** e, em seguida, clique em **Salvar**.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

17. Para configurar as configurações de logon único do SAML, clique em **Novo do arquivo de metadados**.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

18. Clique em **Escolher arquivo** para carregar o arquivo XML de metadados e clique em **criar**.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/xmlchoose.png)

19. Na página **Configurações do SAML Single Sign-On**, em que os campos são preenchidos automaticamente, digite o nome da configuração (por exemplo: *SPSSOWAAD_Test*), na caixa de texto **Nome** e clique em salvar.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

20. Para habilitar seu domínio na Área Restrita Salesforce, execute as etapas a seguir:

    > [!NOTE]
    > Antes de habilitar o domínio, você precisará criar o mesmo na Área Restrita Salesforce. Para obter mais informações, consulte [Definindo o nome de domínio](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US). Depois que o domínio for criado, certifique-se de que ele esteja configurado corretamente.

21. No painel de navegação à esquerda na Área Restrita Salesforce, clique em **Configurações da Empresa** para expandir a seção correspondente e clique em **Meu Domínio**.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

22. Na seção **Configuração de Autenticação**, clique em **Editar**.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

23. Na seção **Configuração de Autenticação**, como **Serviço de Autenticação**, selecione o nome da Configuração de Logon Único do SAML que você definiu durante a configuração de SSO na Área Restrita Salesforce e clique em **Salvar**.

    ![Configurar o logon único](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-salesforce-sandbox-test-user"></a>Criar um usuário de teste do Salesforce Sandbox

Nesta seção, um usuário chamado Brenda Fernandes é criado no Salesforce Sandbox. O Salesforce Sandbox dá suporte ao provisionamento Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Salesforce Sandbox, um novo será criado quando você tentar acessar o Salesforce Sandbox. A área restrita do Salesforce também dá suporte ao provisionamento automático de usuário. É possível encontrar [aqui](salesforce-sandbox-provisioning-tutorial.md) detalhes de como configurar o provisionamento automático de usuário.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Salesforce Sandbox no Painel de Acesso, você deverá ser conectado automaticamente ao Salesforce Sandbox no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Salesforce Sandbox com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/protect-salesforce)

- [Configurar Provisionamento de Usuário](salesforce-sandbox-provisioning-tutorial.md)

- [Como proteger o Salesforce Sandbox com visibilidade e controles avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
