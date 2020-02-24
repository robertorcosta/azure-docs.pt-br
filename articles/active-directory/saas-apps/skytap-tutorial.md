---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Single Sign-on for Skytap | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Single Sign-on for Skytap.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d6cb7ab2-da1a-4015-8e6f-c0c47bb6210f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/13/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 847534ee28880cd2206c6abef605e695cc968f80
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2020
ms.locfileid: "77370703"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-single-sign-on-for-skytap"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Single Sign-on for Skytap

Neste tutorial, você aprenderá a integrar o Single Sign-on for Skytap ao Azure AD (Azure Active Directory). Ao integrar o Single Sign-on for Skytap ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Single Sign-on for Skytap.
* Você pode permitir que os usuários sejam conectados automaticamente ao Single Sign-on for Skytap com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Single Sign-on for Skytap.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Single Sign-on for Skytap dá suporte ao SSO iniciado por **SP e IdP**
* Depois de configurar o Single Sign-on for Skytap, você poderá impor o controle de sessão, que fornece proteção contra exportação e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-single-sign-on-for-skytap-from-the-gallery"></a>Como adicionar o Single Sign-on for Skytap por meio da galeria

Para configurar a integração do Single Sign-on for Skytap ao Azure AD, você precisará adicionar o Single Sign-on for Skytap por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Single Sign-on for Skytap** na caixa de pesquisa.
1. Selecione **Single Sign-on for Skytap** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-single-sign-on-for-skytap"></a>Configurar e testar o logon único do Azure AD para o Single Sign-on for Skytap

Configure e teste o SSO do Azure AD com o Single Sign-on for Skytap usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Single Sign-on for Skytap.

Para configurar e testar o SSO do Azure AD com o Single Sign-on for Skytap, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Single Sign-on for Skytap](#configure-single-sign-on-for-skytap-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Single Sign-on for Skytap](#create-single-sign-on-for-skytap-test-user)** : para ter um equivalente de B.Fernandes no Single Sign-on for Skytap que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Single Sign-on for Skytap**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP** execute as seguintes etapas:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `http://pingone.com/<custom EntityID>`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://sso.connect.pingidentity.com/sso/sp/ACS.saml2`

1. Clique em **Definir URLs adicionais** e execute as seguintes etapas caso deseje configurar o aplicativo no modo iniciado por **SP**:

    d. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://sso.connect.pingidentity.com/sso/sp/initsso?saasid=<saasid>&idpid=<idpid>`

    
    e. Na caixa de texto **Estado de Retransmissão**, digite uma URL usando o seguinte padrão: `https://pingone.com/1.0/<custom ID>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta, a URL de Logon e o Estado de retransmissão reais. Contate a [equipe de suporte ao cliente do Single Sign-on for Skytap](mailto:support@skytap.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o arquivo de metadados e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o Single Sign-on for Skytap**, copie as URLs apropriadas de acordo com as suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Single Sign-on for Skytap.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Single Sign-on for Skytap**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-single-sign-on-for-skytap-sso"></a>Configurar o SSO do Single Sign-on for Skytap

Para configurar o logon único no lado do **Single Sign-on for Skytap**, é necessário enviar o **XML de Metadados de Federação** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte ao cliente do Single Sign-on for Skytap](mailto:support@skytap.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.


### <a name="create-single-sign-on-for-skytap-test-user"></a>Criar um usuário de teste do Single Sign-on for Skytap

Nesta seção, você criará um usuário chamado Brenda Fernandes no Single Sign-on for Skytap. Trabalhe com a [equipe de suporte ao cliente do Single Sign-on for Skytap](mailto:support@skytap.com) para adicionar os usuários à plataforma Single Sign-on for Skytap. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Single Sign-on for Skytap no Painel de Acesso, você deverá ser conectado automaticamente ao Single Sign-on for Skytap, para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Slack com o Azure AD](https://aad.portal.azure.com/)

