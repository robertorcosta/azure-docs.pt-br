---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Sonarqube | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Sonarqube.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b01665e7-c3d0-4393-9286-d5bcf8cf6add
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 571d8849fd7cae5c872a56182858848dbb43ef42
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026696"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sonarqube"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Sonarqube

Neste tutorial, você aprenderá a integrar o Sonarqube ao Azure AD (Azure Active Directory). Ao integrar o Sonarqube ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Sonarqube.
* Permitir que os usuários sejam conectados automaticamente ao Sonarqube com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do Sonarqube habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Sonarqube dá suporte ao SSO iniciado por **SP**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="adding-sonarqube-from-the-gallery"></a>Como adicionar o Sonarqube por meio da galeria

Para configurar a integração do Sonarqube ao Azure AD, você precisará adicionar o Sonarqube por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Sonarqube** na caixa de pesquisa.
1. Selecione **Sonarqube** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sonarqube"></a>Configurar e testar o logon único do Azure AD para o Sonarqube

Configure e teste o SSO do Azure AD com o Sonarqube usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Sonarqube.

Para configurar e testar o SSO do Azure AD com o Sonarqube, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Sonarqube](#configure-sonarqube-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Sonarqube](#create-sonarqube-test-user)** – para ter um equivalente de B.Fernandes no Sonarqube que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Sonarqube**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    Na caixa de texto **URL de Logon**, digite uma URL:

    * **Para o ambiente de produção**

        `https://servicessonar.corp.microsoft.com/`

    * **Para o ambiente de desenvolvimento**

        `https://servicescode-dev.westus.cloudapp.azure.com`

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Sonarqube**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Sonarqube.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Sonarqube**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-sonarqube-sso"></a>Configurar o SSO do Sonarqube

1. Abra uma nova janela do navegador da Web e entre em seu site de empresa do Sonarqube como administrador.

2. Instale o plug-in do SAML por meio do marketplace do Sonarqube.

3. No canto superior esquerdo da página, clique em **ADMIN** e, em seguida, navegue até **SAML**.

4. Na página **SAML** , execute as seguintes etapas:

    ![Configuração do Sonarqube](./media/sonarqube-tutorial/config01.png)

    a. Alterne a opção **Habilitado** para **sim**.

    b. Na caixa de texto **ID do Aplicativo**, insira o nome como **sonarqube**.

    c. Na caixa de texto **Nome do Provedor**, insira o nome como **SAML**.

    d. Na caixa de texto **ID do Provedor**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    e. Na caixa de texto **URL de Logon SAML**, cole o valor da **URL de Logon** copiado do portal do Azure.

    f. Abra o certificado codificado em Base64 no Bloco de notas, copie o conteúdo e cole-o na caixa de texto **Certificado do provedor**.

    g. Na caixa de texto **Atributo de logon de usuário do SAML**, insira o valor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    h. Na caixa de texto **Atributo de nome de usuário do SAML**, insira o valor `http://schemas.microsoft.com/identity/claims/displayname`.

    i. Na caixa de texto **Atributo de email de usuário do SAML**, insira o valor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    j. Clique em **Save** (Salvar).

### <a name="create-sonarqube-test-user"></a>Criar um usuário de teste do Sonarqube

Nesta seção, você criará um usuário chamado B.Fernandes no Sonarqube. Trabalhe com a [equipe de suporte ao cliente do Sonarqube](https://www.sonarsource.com/support/) para adicionar os usuários à plataforma Sonarqube. Os usuários devem ser criados e ativados antes de usar o logon único. 

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Sonarqube no Painel de Acesso, você deverá ser conectado automaticamente ao Sonarqube, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimentar o Sonarqube com o Azure AD](https://aad.portal.azure.com/)

