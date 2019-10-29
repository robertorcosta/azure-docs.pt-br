---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao BlueJeans para Azure AD | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o BlueJeans para Azure AD.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfc38f63c5b6361122c236543320b91d22faa70a
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595040"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bluejeans-for-azure-ad"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao BlueJeans para Azure AD

Neste tutorial, você aprenderá a integrar o BlueJeans para Azure AD ao Azure AD (Azure Active Directory). Ao integrar o BlueJeans para Azure AD ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao BlueJeans para Azure AD.
* Permitir que os usuários sejam conectados automaticamente ao BlueJeans para Azure AD com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do BlueJeans para Azure AD.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O BlueJeans para Azure AD dá suporte ao SSO iniciado por **SP**

* O BlueJeans para Azure AD dá suporte ao [Provisionamento de Usuário **Automatizado**](bluejeans-provisioning-tutorial.md)

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="adding-bluejeans-for-azure-ad-from-the-gallery"></a>Como adicionar o BlueJeans para Azure AD por meio da galeria

Para configurar a integração do BlueJeans para Azure AD ao Azure AD, você precisará adicionar o BlueJeans para Azure AD por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **BlueJeans para Azure AD** na caixa de pesquisa.
1. Selecione **BlueJeans para Azure AD** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-bluejeans-for-azure-ad"></a>Configurar e testar o logon único do Azure AD para o BlueJeans para Azure AD

Configure e teste o SSO do Azure AD com o BlueJeans para Azure AD usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do BlueJeans para Azure AD.

Para configurar e testar o SSO do Azure AD com o BlueJeans para Azure AD, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do BlueJeans para Azure AD](#configure-bluejeans-for-azure-ad-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do BlueJeans para Azure AD](#create-bluejeans-for-azure-ad-test-user)** – para ter um equivalente de B.Fernandes no BlueJeans para Azure AD que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **BlueJeans para Azure AD**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<companyname>.bluejeans.com`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de Logon real. Contate a [equipe de suporte ao cliente do BlueJeans para Azure AD](https://support.bluejeans.com/contact) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o BlueJeans para Azure AD**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao BlueJeans para Azure AD.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **BlueJeans para Azure AD**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-bluejeans-for-azure-ad-sso"></a>Configurar o SSO do BlueJeans para Azure AD

1. Em outra janela do navegador da Web, entre em seu site de empresa do **BlueJeans para Azure AD** como administrador.

2. Acesse **ADMINISTRADOR \> CONFIGURAÇÕES DE GRUPO \> SEGURANÇA**.

    ![Administrador](./media/bluejeans-tutorial/ic785868.png "Administrador")

3. Na seção **SEGURANÇA**, execute as seguintes etapas:

    ![Logon único do SAML](./media/bluejeans-tutorial/ic785869.png "Logon Único do SAML")

    a. Selecione **Logon Único do SAML**.

    b. Selecione **Habilitar provisionamento automático**.

4. Siga em frente com as seguintes etapas:

    ![Caminho do Certificado](./media/bluejeans-tutorial/ic785870.png "Caminho do Certificado")

    a. Clique em **Escolher um Arquivo** para carregar o certificado codificado em base 64 que você baixou do portal do Azure.

    b. Na caixa de texto **URL de Logon**, cole o valor da **URL de Logon** copiado do portal do Azure.

    c. Na caixa de texto **URL de Alteração de Senha**, cole o valor da **URL de Alteração de Senha** copiado do portal do Azure.

    d. Na caixa de texto **URL de Logoff**, cole o valor da **URL de Logoff** copiado do portal do Azure.

5. Siga em frente com as seguintes etapas:

    ![Save Changes](./media/bluejeans-tutorial/ic785874.png "Salvar Alterações")

    a. Na caixa de texto **ID de usuário**, digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    b. Na caixa de texto **Email**, digite `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    c. Clique em **SALVAR ALTERAÇÕES**.

### <a name="create-bluejeans-for-azure-ad-test-user"></a>Criar um usuário de teste do BlueJeans para Azure AD

O objetivo desta seção é criar um usuário chamado B.Fernandes no BlueJeans para Azure AD. O BlueJeans para Azure AD dá suporte ao Provisionamento de Usuário automático, que está habilitado por padrão. Você pode encontrar mais detalhes [ aqui ](bluejeans-provisioning-tutorial.md) sobre como configurar o provisionamento automático de usuários.

**Se você precisar criar o usuário manualmente, execute as seguintes etapas:**

1. Entre em seu site de empresa do **BlueJeans para Azure AD** como administrador.

2. Acesse **ADMINISTRADOR \> GERENCIAR USUÁRIOS \> ADICIONAR USUÁRIO**.

    ![Administrador](./media/bluejeans-tutorial/ic785877.png "Administrador")

    > [!IMPORTANT]
    > A guia **ADICIONAR USUÁRIO** só estará disponível se, na **guia SEGURANÇA**, a opção **Habilitar provisionamento automático** estiver desmarcada.

3. Na seção **ADICIONAR USUÁRIO**, execute as etapas a seguir:

    ![Adicionar Usuário](./media/bluejeans-tutorial/ic785886.png "Adicionar usuário")

    a. Na caixa de texto **Nome**, insira o nome do usuário como **B**.

    b. Na caixa de texto **Sobrenome**, insira o nome do usuário como **Fernandes**.

    c. Na caixa de texto **Escolher um Nome de Usuário do BlueJeans para Azure AD**, insira o nome do usuário como **Brendafernandes**

    d. Na caixa de texto **Criar uma Senha**, insira sua senha.

    e. Na caixa de texto **Empresa**, insira a sua Empresa.

    f. Na caixa de texto **Endereço de Email**, insira o email do usuário como `b.simon\@contoso.com`.

    g. Na caixa de texto **Criar uma ID de Reunião do BlueJeans para Azure AD**, insira sua ID de reunião.

    h. Na caixa de texto **Escolher uma Senha de Moderador**, insira a sua senha.

    i. Clique em **CONTINUAR**.

    ![Adicionar Usuário](./media/bluejeans-tutorial/ic785887.png "Adicionar usuário")

    J. Clique em **ADICIONAR USUÁRIO**.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do BlueJeans para Azure AD ou as APIs fornecidas pelo BlueJeans para Azure AD para provisionar as contas de usuário do Azure AD.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do BlueJeans para Azure AD no Painel de Acesso, você deverá ser conectado automaticamente ao BlueJeans para Azure AD, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimentar o BlueJeans para Azure AD com o Azure AD](https://aad.portal.azure.com/)

