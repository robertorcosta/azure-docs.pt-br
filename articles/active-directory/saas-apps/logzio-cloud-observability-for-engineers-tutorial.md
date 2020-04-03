---
title: 'Tutorial: Integração de SSO (logon único) do Azure Active Directory com o Logz.io – Cloud Observability for Engineers | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Logz.io – Cloud Observability for Engineers.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b0984471-d12f-4f58-896e-194ea45b01fd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/26/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: de1c929ffa790d2abe3a1922cecc2175cd7a8e12
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385268"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-logzio---cloud-observability-for-engineers"></a>Tutorial: Integração de SSO (logon único) do Azure Active Directory com o Logz.io – Cloud Observability for Engineers

Neste tutorial, você aprenderá a integrar o Logz.io – Cloud Observability for Engineers ao Azure AD (Azure Active Directory). Ao integrar o Logz.io – Cloud Observability for Engineers ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Logz.io – Cloud Observability for Engineers.
* Habilitar seus usuários a entrarem automaticamente no Logz.io – Cloud Observability for Engineers com a conta do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Logz.io – Cloud Observability for Engineers.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Logz.io – Cloud Observability for Engineers é compatível com SSO iniciado por **IDP**
* Depois de configurar o Logz.io – Cloud Observability for Engineers, você poderá impor o controle de sessão, que fornece proteção contra exportação e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-logzio---cloud-observability-for-engineers-from-the-gallery"></a>Como adicionar o Logz.io – Cloud Observability for Engineers da galeria

Para configurar a integração do Logz.io – Cloud Observability for Engineers no Azure AD, você precisará adicionar o Logz.io – Cloud Observability for Engineers da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Logz.io – Cloud Observability for Engineers** na caixa de pesquisa.
1. Selecione **Logz.io – Cloud Observability for Engineers** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-logzio---cloud-observability-for-engineers"></a>Configurar e testar o logon único do Azure AD para o Logz.io – Cloud Observability for Engineers

Configure e teste o SSO do Azure AD com o Logz.io – Cloud Observability for Engineers usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Logz.io – Cloud Observability for Engineers.

Para configurar e testar o SSO do Azure AD com o Logz.io – Cloud Observability for Engineers, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Logz.io – Cloud Observability for Engineers](#configure-logzio-cloud-observability-for-engineers-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do Logz.io – Cloud Observability for Engineers](#create-logzio-cloud-observability-for-engineers-test-user)** – para ter um equivalente de B.Fernandes no Logz.io – Cloud Observability for Engineers vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Logz.io – Cloud Observability for Engineers**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na página **Configurar logon único com SAML**, insira os valores para os seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `urn:auth0:logzio:CONNECTION-NAME`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://logzio.auth0.com/login/callback?connection=CONNECTION-NAME`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Entre em contato com a [equipe de suporte ao cliente do Logz.io – Cloud Observability for Engineers](mailto:help@logz.io) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo Logz.io – Cloud Observability for Engineers espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizados para a configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo Logz.io – Cloud Observability for Engineers espera que mais alguns atributos sejam transmitidos novamente na resposta SAML, os quais são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.
    
    | Nome |  Atributo de Origem|
    | ---------------| --------- |
    | session-expiration | user.session-expiration |
    | email | user.mail |
    | Agrupar | user.groups |

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Logz.io – Cloud Observability for Engineers**, copie as URLs apropriadas com base em seu requisito.

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

Nesta seção, você habilitará B.Fernandes para usar o logon único do Azure concedendo acesso ao Logz.io – Cloud Observability for Engineers.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Logz.io – Cloud Observability for Engineers**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-logzio-cloud-observability-for-engineers-sso"></a>Configurar o Logz.io Cloud Observability for Engineers SSO

Para configurar o logon único no lado do **Logz.io – Cloud Observability for Engineers**, envie o **Certificado (Base64)** baixado e as URLs copiadas apropriadas do portal do Azure para a [equipe do Logz.io – Cloud Observability for Engineers](mailto:help@logz.io). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-logzio-cloud-observability-for-engineers-test-user"></a>Criar o usuário de teste do Logz.io Cloud Observability for Engineers

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Logz.io – Cloud Observability for Engineers. Trabalhe com a  [equipe de suporte do Logz.io – Cloud Observability for Engineers](mailto:help@logz.io) para adicionar os usuários à plataforma do Logz.io – Cloud Observability for Engineers. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clica no bloco Logz.io – Cloud Observability for Engineers no Painel de Acesso, deve entrar automaticamente no Logz.io – Cloud Observability for Engineers para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Avaliar o Logz.io – Cloud Observability for Engineers com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger o Logz.io – Cloud Observability for Engineers com visibilidade e controles avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

