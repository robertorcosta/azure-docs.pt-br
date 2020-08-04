---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory com o Lenses.io | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Lenses.io.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2a0d4a7c-a171-48c6-b1c1-f2bd728fb37f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 07/02/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c2b630111261be8e3615ab45e95633040e799551
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87050998"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-the-lensesio-dataops-portal"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao portal de DataOps do Lenses.io.

Neste tutorial, você aprenderá a integrar o portal de DataOps do [Lenses.io](https://lenses.io/) ao Azure AD (Azure Active Directory). Ao integrar o Lenses.io ao Azure AD, você pode:

* Controlar, no Azure AD, quem tem acesso ao portal do Lenses.io.
* Permitir que os usuários sejam conectados automaticamente ao Lenses com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma instância de um portal do Lenses. É possível implantar um portal do Lenses de [várias maneiras](https://lenses.io/product/deployment/).
* Uma [licença](https://lenses.io/product/pricing/) do Lenses.io compatível com SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Lenses.io é compatível com SSO iniciado por **SP**

* Depois de configurar o Lenses.io, você poderá impor o controle de sessão, que fornece proteção contra exportação e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-lensesio-from-the-gallery"></a>Como adicionar o Lenses.io da galeria

Para configurar a integração do Lenses.io ao Azure AD, você precisará adicionar o Lenses.io da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Lenses.io** na caixa de pesquisa.
1. Selecione **Lenses.io** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-lensesio"></a>Configurar e testar o SSO do Azure AD para o Lenses.io

Configure e teste o SSO do Azure AD com o portal do Lenses.io com um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Lenses.io.

Para configurar e testar o SSO do Azure AD com o Lenses.io, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um grupo e usuário de teste do Azure AD](#create-an-azure-ad-test-user-and-group)** – para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Lenses.io](#configure-lensesio-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar permissões de grupo de teste do Lenses.io](#create-lensesio-test-group-permissions)** – para controlar o que B.Fernandes deve acessar no Lenses.io (autorização).
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Lenses.io**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<CUSTOMER_LENSES_BASE_URL>`, por exemplo, `https://lenses.my.company.com`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<CUSTOMER_LENSES_BASE_URL>`, por exemplo, `https://lenses.my.company.com`

    c. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<CUSTOMER_LENSES_BASE_URL>/api/v2/auth/saml/callback?client_name=SAML2Client`
    Por exemplo: `https://lenses.my.company.com/api/v2/auth/saml/callback?client_name=SAML2Client`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Logon, a URL de Resposta e o Identificador reais, com base na URL base da instância do portal do Lenses. É possível encontrar mais informações na [documentação de SSO do Lenses.io](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0).

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o Lenses.io**, use o arquivo XML acima para configurar o Lenses em relação ao SSO do Azure.

### <a name="create-an-azure-ad-test-user-and-group"></a>Criar um grupo e usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes. Você também criará um grupo de teste para B.Fernandes, que será usado para controlar que tipo de acesso B.Fernandes tem no Lenses.
Você pode descobrir como o Lenses usa o mapeamento de associação de grupo para autorização na [documentação do SSO do Lenses](https://docs.lenses.io/install_setup/configuration/security.html#id3)

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

Para criar o grupo:
1. Volte para o **Azure Active Directory** e selecione **Grupos**
1. Selecione **Novo grupo** na parte superior da tela.
1. Nas **Propriedades do grupo**, siga estas etapas:
   1. No campo **Tipo de grupo**, selecione `Security`.
   1. No campo **Nome do Grupo**, insira `LensesUsers`
   1. Clique em **Criar**.
1. Selecione o grupo `LensesUsers` e anote a **ID do Objeto** (por exemplo, `f8b5c1ec-45de-4abd-af5c-e874091fb5f7`). Essa ID será usada no Lenses para mapear usuários desse grupo para as [permissões corretas](https://docs.lenses.io/install_setup/configuration/security.html#id3).  
   
Para atribuir o grupo ao usuário de teste: 
1. Volte para o **Azure Active Directory** e selecione **Usuários**.
1. Selecione o usuário de teste `B.Simon`.
1. Selecione **Grupos**.
1. Selecione **Adicionar associações** na parte superior da tela.
1. Pesquise `LensesUsers` e selecione-o.
1. Clique em **Selecionar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Lenses.io.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Lenses.io**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-lensesio-sso"></a>Configurar o SSO do Lenses.io

Para configurar o logon único no portal do **Lenses.io**, instale o **XML de Metadados de Federação** baixado em sua instância do Lenses e [configure o Lenses para habilitar o SSO](https://docs.lenses.io/install_setup/configuration/security.html#configure-lenses). 

### <a name="create-lensesio-test-group-permissions"></a>Criar permissões de grupo de teste do Lenses.io

Nesta seção, você criará um grupo no Lenses usando a **ID de Objeto** do grupo `LensesUsers` que anotamos na [seção de criação](#create-an-azure-ad-test-user-and-group) do usuário.
Atribua as permissões desejadas que `B.Simon` deve ter no Lenses.
Você pode encontrar mais informações sobre o [Azure – Mapeamento de grupo do Lenses](https://docs.lenses.io/install_setup/configuration/security.html#azure-groups).

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Lenses.io no Painel de Acesso, você deverá ser conectado automaticamente ao portal do Lenses.io para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Configurar o SSO em sua instância do Lenses.io ](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0)

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Lenses.io com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger o Lenses.io com visibilidade e controles avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
