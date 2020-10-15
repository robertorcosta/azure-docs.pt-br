---
title: 'Tutorial: Integração do Azure Active Directory ao PagerDuty | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o PagerDuty.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 2bc486987dd25d899728af4861c0b71ef14d9f4a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88554072"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pagerduty"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao PagerDuty

Neste tutorial, você aprenderá a integrar o PagerDuty ao Azure AD (Azure Active Directory). Com a integração do PagerDuty ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao PagerDuty.
* Permitir que os usuários entrem automaticamente no PagerDuty com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do PagerDuty.

> [!NOTE]
> Se você estiver usando a autenticação sem senha ou MFA com o Azure AD, desative o valor AuthnContext na solicitação SAML. Caso contrário, o Azure AD vai gerar o erro na incompatibilidade de AuthnContext e não enviará o token de volta para o aplicativo.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O PagerDuty dá suporte ao SSO iniciado por **SP**
* Depois de configurar o PagerDuty, você poderá impor o controle de sessão, que fornece proteção contra exportação e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-pagerduty-from-the-gallery"></a>Adicionar o PagerDuty da galeria

Para configurar a integração do PagerDuty ao Azure AD, você precisa adicionar o PagerDuty da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **PagerDuty** na caixa de pesquisa.
1. Escolha **PagerDuty** no painel de resultados e, depois, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-pagerduty"></a>Configurar e testar logon único do Azure AD para o PagerDuty

Configure e teste o SSO do Azure AD com o PagerDuty usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do PagerDuty.

Para configurar e testar o SSO do Azure AD com o PagerDuty, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do PagerDuty](#configure-pagerduty-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do PagerDuty](#create-pagerduty-test-user)** – para ter um equivalente de B.Fernandes no PagerDuty que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **PagerDuty**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<tenant-name>.pagerduty.com`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<tenant-name>.pagerduty.com`

    c. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<tenant-name>.pagerduty.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta, o Identificador e a URL de Logon reais. Contate a [equipe de suporte ao cliente do PagerDuty](https://www.pagerduty.com/support/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o PagerDuty**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá a B.Fernandes acesso ao PagerDuty, que permitirá o uso do logon único do Azure.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **PagerDuty**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-pagerduty-sso"></a>Configurar o SSO do PagerDuty

1. Em outra janela do navegador da Web, entre em seu site de empresa do PagerDuty como administrador.

2. No menu na parte superior, clique em **Configurações de Conta**.

    ![Configurações da Conta](./media/pagerduty-tutorial/ic778535.png "Configurações da Conta")

3. Clique em **Logon Único**.

    ![Logon Único](./media/pagerduty-tutorial/ic778536.png "Logon único")

4. Na página **Habilitar Logon Único (SSO)** , execute as seguintes etapas:

    ![Habilitar logon único](./media/pagerduty-tutorial/ic778537.png "Habilitar logon único")

    a. Abra o certificado codificado em Base 64 baixado no Portal do Azure no bloco de notas, copie o conteúdo dele para a área de transferência e, depois, cole-o na caixa de texto **Certificado X.509**
  
    b. Na caixa de texto **URL de Logon**, cole a **URL de Logon** copiada no portal do Azure.
  
    c. Na caixa de texto **URL de Logoff**, cole a **URL de Logoff** copiada no portal do Azure.

    d. Selecione **Permitir logon com nome de usuário/senha**.

    e. Marque a caixa de seleção **Exigir comparação de contexto de autenticação EXACT**.

    f. Clique em **Salvar Alterações**.

### <a name="create-pagerduty-test-user"></a>Criar um usuário de teste do PagerDuty

Para que os usuários do Azure AD possam entrar no PagerDuty, eles precisam primeiro ser provisionados nele. No caso do PagerDuty, o provisionamento é uma tarefa manual.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do PagerDuty ou as APIs fornecidas pelo PagerDuty para provisionar as contas de usuário do Azure Active Directory.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre em seu locatário do **PagerDuty**.

2. No menu na parte superior, clique em **Usuários**.

3. Clique em **Adicionar Usuários**.
   
    ![Adicionar Usuários](./media/pagerduty-tutorial/ic778539.png "Adicionar Usuários")

4.  No diálogo **Convidar sua equipe**, execute as seguintes etapas:
   
    ![Convidar sua equipe](./media/pagerduty-tutorial/ic778540.png "Convidar sua equipe")

    a. Digite o **Nome e Sobrenome** do usuário, por exemplo, **B.Fernandes**. 
   
    b. Insira o endereço de **Email** do usuário, como **b.fernandes\@contoso.com**.
   
    c. Clique em **Adicionar** e depois em **Enviar Convites**.
   
    > [!NOTE]
    > Todos os usuários adicionados receberão um convite para criar uma conta do PagerDuty.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do PagerDuty no Painel de Acesso, você deverá ser conectado automaticamente ao PagerDuty, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o PagerDuty com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger o PagerDuty com visibilidade e controles avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

