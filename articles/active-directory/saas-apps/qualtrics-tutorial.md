---
title: 'Tutorial: Integração do Azure Active Directory ao SAP Qualtrics | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SAP Qualtrics.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4df889ab-2685-4d15-a163-1ba26567eeda
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/03/2020
ms.author: jeedes
ms.openlocfilehash: d7a35f9f366f45a111aa7111501c9261ecbfa87e
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2020
ms.locfileid: "80667513"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-qualtrics"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao SAP Qualtrics

Neste tutorial, você aprenderá a integrar o SAP Qualtrics ao Azure AD (Azure Active Directory). Ao integrar o SAP Qualtrics ao Azure AD, você pode:

* Controlar no Azure AD que tem acesso ao SAP Qualtrics.
* Permitir que os usuários sejam conectados automaticamente ao SAP Qualtrics com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do SAP Qualtrics habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O SP Qualtrics é compatível com o SSO iniciado por **SP** e **IDP**
* O SAP Qualtrics é compatível com o provisionamento de usuário **Just In Time**
* Após configurar o SAP Qualtrics, você poderá impor o controle de sessão, que protege contra a exportação e infiltração de dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-sap-qualtrics-from-the-gallery"></a>Adicionar o Qualtrics da galeria

Para configurar a integração do SAP Qualtrics ao Azure AD, é necessário adicionar o SAP Qualtrics da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **SAP Qualtrics** na caixa de pesquisa.
1. Selecione **SAP Qualtrics** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-qualtrics"></a>Configurar e testar o logon único do Azure AD para o SAP Qualtrics

Configure e teste o SSO do Azure AD com o SAP Qualtrics usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SAP Qualtrics.

Para configurar e testar o SSO do Azure AD com o SAP Qualtrics, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do SAP Qualtrics](#configure-sap-qualtrics-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do SAP Qualtrics](#create-sap-qualtrics-test-user)** – para ter um equivalente de B.Fernandes no SAP Qualtrics que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **SAP Qualtrics**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na página **Configurar logon único com SAML**, caso queira configurar o aplicativo no modo iniciado por **IDP**, insira os valores nos seguintes campos:
    
    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão:

    `https://< DATACENTER >.qualtrics.com`
   
    b. Na caixa de texto **URL de Resposta**, digite uma URL usando o seguinte padrão:

    `https://< DATACENTER >.qualtrics.com/login/v1/sso/saml2/default-sp`

    c. Na caixa de texto **Estado de Retransmissão**, digite uma URL usando o seguinte padrão:

    `https://< brandID >.< DATACENTER >.qualtrics.com`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de Entrada**, digite uma URL usando o padrão a seguir:

    `https://< brandID >.< DATACENTER >.qualtrics.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Logon, o Identificador, a URL de Resposta e o Estado de Retransmissão reais. Contate a [equipe de suporte do cliente do Qualtrics](https://www.qualtrics.com/support/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao SAP Qualtrics.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **SAP Qualtrics**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-sap-qualtrics-sso"></a>Configurar o SSO do SAP Qualtrics

Para configurar o logon único no lado do **SAP Qualtrics**, é necessário enviar a **URL de Metadados de Federação do Aplicativo** copiada do portal do Azure para a [equipe de suporte do SAP Qualtrics](https://www.qualtrics.com/support/). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-sap-qualtrics-test-user"></a>Criar um usuário de teste do SAP Qualtrics

Nesta seção, uma usuária chamada Brenda Fernandes será criada no SAP Qualtrics. O SAP Qualtrics é compatível com o provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no SAP Qualtrics, um será criado após a autenticação.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do SAP Qualtrics no Painel de Acesso, você deverá ser conectado automaticamente ao SAP Qualtrics para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o SAP Qualtrics com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger o SAP Qualtrics com visibilidade e controles avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

