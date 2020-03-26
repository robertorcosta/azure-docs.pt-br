---
title: 'Tutorial: Integração do Azure Active Directory com o Tableau Online | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Tableau Online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1d4b1149-ba3b-4f4e-8bce-9791316b730d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.openlocfilehash: 187600edb599f5a5775e1b847ed1cb3a49f3b827
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "76985554"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-online"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Tableau Online

Neste tutorial, você aprenderá a integrar o Tableau Online ao Azure AD (Azure Active Directory). Ao integrar o Tableau Online ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Tableau Online.
* Permitir que os usuários sejam conectados automaticamente ao Tableau Online com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Tableau Online.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Tableau Online é compatível com o SSO iniciado por **SP**
* Depois de configurar o Tableau Online, você poderá impor controles de sessão, que fornecem proteção contra exportação e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-tableau-online-from-the-gallery"></a>Adição do Tableau Online da galeria

Para configurar a integração do Tableau Online ao Azure AD, você precisa adicionar o Tableau Online da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Tableau Online** na caixa de pesquisa.
1. Selecione **Tableau Online** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Tableau Online baseado em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Tableau Online.

Para configurar e testar o SSO do Azure AD com o Tableau Online, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Tableau Online](#configure-tableau-online-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Tableau Online](#create-tableau-online-test-user)** : para ter um equivalente de B.Fernandes no Tableau Online que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Tableau Online, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Tableau Online**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Tableau Online](common/sp-identifier.png)

    a. Na caixa de texto **URL de Logon**, digite a URL: `https://sso.online.tableau.com/public/sp/login?alias=<entityid>`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite a URL: `https://sso.online.tableau.com/public/sp/metadata?alias=<entityid>`

    > [!NOTE]
    > Você obterá o valor `<entityid>` na seção **Configurar o Tableau Online** neste tutorial. O valor de ID de entidade será o valor **Identificador do Azure AD** na seção **Configurar o Tableau Online**.

5. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar o Tableau Online**, copie a URL apropriada, de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brendafernandes\@dominiodaempresa.extensao**  
    Por exemplo, BrendaFernandes\@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Tableau Online.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Tableau Online**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Tableau Online**.

    ![O link do Tableau Online na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-tableau-online-sso"></a>Configurar o SSO do Tableau Online

1. Em uma janela de navegador diferente, entre no seu aplicativo Tableau Online. Vá para **Settings** e para **Authentication**.

    ![Configurar o logon único](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)

2. Para habilitar o SAML, na seção **Tipos de Autenticação**. Marque **Habilitar um método de autenticação adicional** e também a caixa de seleção **SAML**.

    ![Configurar o logon único](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

3. Role para baixo até a seção **Importar arquivo de metadados para o Tableau Online**.  Clique em Procurar e importe o arquivo de metadados, que você baixou do Azure AD. Em seguida, clique em **Apply**.

   ![Configurar o logon único](./media/tableauonline-tutorial/tutorial_tableauonline_13.png)

4. Na seção **Corresponder Instruções de Declaração**, insira o nome de instrução de declaração do Provedor de Identidade correspondente para **endereço de email**, **nome** e **sobrenome**. Para obter essas informações do Azure AD: 
  
    a. No portal do Azure, acesse a página de integração de aplicativos do **Tableau Online**.

    b. Na seção **Atributos e Declarações de Usuário**, clique no ícone Editar.

   ![Configurar o logon único](./media/tableauonline-tutorial/attributesection.png)

    c. Copie o valor de namespace para esses atributos: nome, email e sobrenome usando as seguintes etapas:

   ![Logon Único do AD do Azure](./media/tableauonline-tutorial/tutorial_tableauonline_10.png)

    d. Clique no valor **user.givenname**

    e. Copie o valor da caixa de texto **Namespace**.

    ![Configurar o logon único](./media/tableauonline-tutorial/attributesection2.png)

    f. Para copiar os valores de namespace para o email e o sobrenome, repita as etapas acima.

    g. Alterne para o aplicativo Tableau Online e defina a seção **Atributos do usuário e declarações** da seguinte maneira:

    * Email: **mail** ou **userprincipalname**

    * Nome: **givenname**

    * Sobrenome: **surname**

    ![Configurar o logon único](./media/tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="create-tableau-online-test-user"></a>Criar usuário de teste do Tableau Online

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Tableau Online.

1. No **Tableau Online**, clique em **Configurações** e na seção **Autenticação**. Role para baixo até a seção **Gerenciar Usuários**. Clique em **Adicionar Usuários** e, em seguida, clique em **Inserir Endereços de Email**.
  
    ![Criação de um usuário de teste do AD do Azure](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)

2. Selecione **Adicionar usuários para autenticação (de SAML)** . Na caixa de texto **Inserir endereços de email**, adicione brenda.fernandes\@contoso.com
  
    ![Criação de um usuário de teste do AD do Azure](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)

3. Clique em **Adicionar Usuários**.

### <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Tableau Online no Painel de Acesso, você deverá entrar automaticamente no Tableau Online para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [O que é controle de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)