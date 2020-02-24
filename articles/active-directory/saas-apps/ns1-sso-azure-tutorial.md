---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao NS1 SSO for Azure | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o NS1 SSO for Azure.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 24a1afb6-b8b6-4787-bd4b-8fe3a32f8def
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/12/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5ba34aeb97808c19c78f187dcc68309893d1d1b
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77429187"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ns1-sso-for-azure"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao NS1 SSO for Azure

Neste tutorial, você aprenderá a integrar o NS1 SSO ao Azure AD (Azure Active Directory). Ao integrar o NS1 SSO for Azure ao Azure AD, você pode:

* Controlar quem tem acesso ao NS1 SSO for Azure no Azure AD.
* Permitir que os usuários sejam conectados automaticamente ao NS1 SSO for Azure com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do NS1 SSO for Azure.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O NS1 SSO for Azure dá suporte ao SSO iniciado por **SP e IDP**
* Depois de configurar o NS1 SSO for Azure, você poderá impor o controle de sessão, que fornece proteção contra exportação e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


## <a name="adding-ns1-sso-for-azure-from-the-gallery"></a>Adicionar o NS1 SSO for Azure da galeria

Para configurar a integração do NS1 SSO for Azure ao Azure AD, é necessário adicionar o NS1 SSO for Azure da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **NS1 SSO for Azure** na caixa de pesquisa.
1. Selecione **NS1 SSO for Azure** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-ns1-sso-for-azure"></a>Configurar e testar o logon único do Azure AD para o NS1 SSO for Azure

Configure e teste o SSO do Azure AD com o NS1 SSO for Azure usando uma usuária de teste chamada **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do NS1 SSO for Azure.

Para configurar e testar o SSO do Azure AD com o NS1 SSO, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do NS1 SSO for Azure](#configure-ns1-sso-for-azure-sso)** – para configurar o logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do NS1 SSO for Azure](#create-ns1-sso-for-azure-test-user)** – para ter um equivalente de B.Fernandes no NS1 SSO for Azure que esteja vinculado à representação do usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **NS1 SSO for Azure**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. Na caixa de texto **Identificador**, digite a URL `https://api.nsone.net/saml/metadata`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://api.nsone.net/saml/sso/<ssoid>`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de Logon**, digite a URL: `https://my.nsone.net/#/login/sso`

    > [!NOTE]
    > O valor de URL de Resposta não é real. Atualize o valor da URL de Resposta com a URL de Resposta real. Entre em contato com a [equipe de suporte ao cliente do NS1 SSO for Azure](mailto:techops@nsone.net) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo NS1 SSO for Azure espera que as declarações SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. Na página **Definir Logon Único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Atributos do Usuário**.

    ![Seção Atributo](./media/ns1-sso-for-azure-tutorial/attribute-edit-option.png)

1. Clique no nome do atributo para editar a declaração.

    ![Seção Atributo](./media/ns1-sso-for-azure-tutorial/attribute-claim-edit.png)

1. Selecione **Transformação**.

    ![Seção Atributo](./media/ns1-sso-for-azure-tutorial/prefix-edit.png)

1. Na seção **Gerenciar transformação**, execute as seguintes etapas:

    ![Seção Atributo](./media/ns1-sso-for-azure-tutorial/prefix-added.png)

    1. Selecione **ExactMailPrefix()** como **Transformação**.

    1. Selecione **user.userprincipalname** como **Parâmetro 1**.

    1. Clique em **Adicionar**.

    1. Clique em **Salvar**

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao NS1 SSO for Azure.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **NS1 SSO for Azure**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-ns1-sso-for-azure-sso"></a>Configurar o SSO do NS1 SSO for Azure

Para configurar logon único no lado do **NS1 SSO for Azure**, você precisa enviar a **URL de Metadados de Federação do Aplicativo** para a [equipe de suporte do NS1 SSO for Azure](mailto:techops@nsone.net). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-ns1-sso-for-azure-test-user"></a>Criar um usuário de teste do NS1 SSO for Azure

Nesta seção, você criará um usuário com o nome B.Fernandes no NS1 SSO for Azure. Trabalhe com a equipe de suporte do NS1 SSO for Azure para adicionar os usuários na plataforma do NS1 SSO for Azure. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do NS1 SSO for Azure no Painel de Acesso, você deverá ser conectado automaticamente ao NS1 SSO for Azure, para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o NS1 SSO for Azure com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)