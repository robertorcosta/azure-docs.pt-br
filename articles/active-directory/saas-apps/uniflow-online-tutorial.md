---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao uniFLOW Online | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o uniFLOW Online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28313d27-638c-4d50-92ad-d093f2ae9ecf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9eb369047574ef76dd31996fd16399380ea027c8
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74823148"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-uniflow-online"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao uniFLOW Online

Neste tutorial, você aprenderá a integrar o uniFLOW Online ao Azure AD (Azure Active Directory). Quando integrar o uniFLOW Online ao Azure AD, será possível:

* Controlar no Azure AD quem tem acesso ao uniFLOW Online.
* Permitir que os usuários sejam conectados automaticamente ao uniFLOW Online com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do uniFLOW Online habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O uniFLOW Online é compatível com o SSO iniciado por **SP**

## <a name="adding-uniflow-online-from-the-gallery"></a>Adicionar o uniFLOW Online da galeria

Para configurar a integração do uniFLOW Online ao Azure AD, você precisa adicionar o uniFLOW Online da galeria à sua lista de aplicativos de SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **uniFLOW Online** na caixa de pesquisa.
1. Selecione **uniFLOW Online** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-uniflow-online"></a>Configurar e testar o logon único do Azure AD para o uniFLOW Online

Configure e teste o SSO do Azure AD com o uniFLOW Online usando uma usuária de teste chamada **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do uniFLOW Online.

Para configurar e testar o SSO do Azure AD com o uniFLOW Online, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do uniFLOW Online](#configure-uniflow-online-sso)** – para definir as configurações de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do uniFLOW Online](#create-uniflow-online-test-user)** – para ter um equivalente de Brenda Fernandes no uniFLOW Online que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **uniFLOW Online**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão:

    | | |
    |-|-|
    | `https://<tenant_domain_name>.eu.uniFLOWonline.com`|
    | `https://<tenant_domain_name>.us.uniFLOWonline.com`|
    | `https://<tenant_domain_name>.sg.uniFLOWonline.com`|
    | `https://<tenant_domain_name>.jp.uniFLOWonline.com`|
    | `https://<tenant_domain_name>.au.uniFLOWonline.com`|

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão:

    | | |
    |-|-|
    | `https://<tenant_domain_name>.eu.uniFLOWonline.com`|
    | `https://<tenant_domain_name>.us.uniFLOWonline.com`|
    | `https://<tenant_domain_name>.sg.uniFLOWonline.com`|
    | `https://<tenant_domain_name>.jp.uniFLOWonline.com`|
    | `https://<tenant_domain_name>.au.uniFLOWonline.com`|

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Entre com contato com a [equipe de suporte ao Cliente do uniFLOW Online](mailto:support@nt-ware.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Seu aplicativo uniFLOW Online espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo uniFLOW Online espera que mais alguns atributos sejam passados novamente na resposta SAML, que são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.

    | NOME |  Atributo de Origem|
    | -----------| --------------- |
    | displayname | user.displayname |
    | apelido | user.onpremisessamaccountname |

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, clique no botão Copiar para copiar a **URL de Metadados de Federação do Aplicativo** e salve-a no computador.

    ![O link de download do Certificado](common/copy-metadataurl.png)

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

Nesta seção, você permitirá que B. Fernandes use o logon único do Azure permitindo a ela acesso ao uniFLOW Online.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **uniFLOW Online**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-uniflow-online-sso"></a>Configurar o SSO do uniFLOW Online

1. Em uma janela do navegador da Web diferente, entre no site uniFLOW Online como administrador.

1. No painel de navegação esquerdo, selecione a guia **Usuário**.

    ![Configuração do uniFLOW Online](./media/uniflow-online-tutorial/configure1.png)

1. Clique em **Provedores de Identidade**.

    ![Configuração do uniFLOW Online](./media/uniflow-online-tutorial/configure2.png)

1. Clique em **Adicionar provedor de identidade**.

    ![Configuração do uniFLOW Online](./media/uniflow-online-tutorial/configure3.png)

1. Na seção **ADICIONAR PROVEDOR DE IDENTIDADE**, execute as seguintes etapas:


    ![Configuração do uniFLOW Online](./media/uniflow-online-tutorial/configure4.png)

    a. Insira o nome de exibição Por ex: *SSO do AzureAD*.

    b. Para **Tipo de provedor**, selecione a opção **WS-Fed** no menu suspenso.

    c. Para **Tipo WS-Fed**, selecione a opção **Azure Active Directory** no menu suspenso.

    d. Clique em **Save** (Salvar).

1. Na guia **Geral**, execute as seguintes etapas:

    ![Configuração do uniFLOW Online](./media/uniflow-online-tutorial/configure5.png)

    a. Insira o nome de exibição Por ex: *SSO do AzureAD*.

    b. Selecione a opção **Da URL** do **Metadados de Federação do ADGS**.

    c. Na caixa de texto **URL de Metadados de Federação**, cole o valor da **URL de metadados de federação do aplicativo** que você copiou do portal do Azure.

    d. Selecione **Provedor de identidade** como **Habilitado**.

    e. Selecione **Registro de usuário automático** como **Ativado**.

    f. Clique em **Save** (Salvar).

### <a name="create-uniflow-online-test-user"></a>Criar um usuário de teste do uniFLOW Online

1. Em uma janela do navegador da Web diferente, entre no site uniFLOW Online como administrador.

1. No painel de navegação esquerdo, selecione a guia **Usuário**.

    ![Configuração do uniFLOW Online](./media/uniflow-online-tutorial/configure1.png)

1. Clique em **Adicionar usuário**.

    ![Configuração do uniFLOW Online](./media/uniflow-online-tutorial/user1.png)

1. Clique em **Criar usuário manualmente**.

    ![Configuração do uniFLOW Online](./media/uniflow-online-tutorial/user2.png)

1. Na guia **CRIAR USUÁRIO MANUALMENTE**, forneça os valores necessários de acordo com os requisitos de sua organização.

    ![Configuração do uniFLOW Online](./media/uniflow-online-tutorial/user3.png)

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do uniFLOW Online no Painel de Acesso, você deverá ser conectado automaticamente ao uniFLOW Online, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o uniFLOW Online com o Azure AD](https://aad.portal.azure.com/)