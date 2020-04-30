---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Nitro Productivity Suite | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Nitro Productivity Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 98c96c9f-18a6-4a20-919b-74fb113ee465
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/02/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ca675e43f4d20898d9f97c175da71f8d5c59e4f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676916"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nitro-productivity-suite"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Nitro Productivity Suite

Neste tutorial, você aprenderá a integrar o Nitro Productivity Suite ao Microsoft Azure AD (Azure Active Directory). Ao integrar o Nitro Productivity Suite ao Azure AD, você pode:

* Controlar no Azure AD que tem acesso ao Nitro Productivity Suite.
* Permitir que os usuários sejam conectados automaticamente ao Nitro Productivity Suite com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos de SaaS (software como serviço) ao Azure AD, confira [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa do seguinte:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma [assinatura Enterprise](https://www.gonitro.com/pricing) do Nitro Productivity Suite.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Nitro Productivity Suite é compatível com o SSO iniciado por **SP** e **IDP**.
* O Nitro Productivity Suite é compatível com o provisionamento de usuário **Just-In-Time**.
* Após configurar o Nitro Productivity Suite, você poderá impor o controle de sessão, que protege contra a exportação e infiltração de dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. Para obter mais informações, confira [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-nitro-productivity-suite-from-the-gallery"></a>Adicionar o Nitro Productivity Suite da galeria

Para configurar a integração do Nitro Productivity Suite com o Azure AD, é necessário adicionar o Nitro Productivity Suite da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante, ou uma conta pessoal da Microsoft.
1. No painel esquerdo, selecione **Azure Active Directory**.
1. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, selecione **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Nitro Productivity Suite** na caixa de pesquisa.
1. Selecione **Nitro Productivity Suite** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-nitro-productivity-suite"></a>Configurar e testar o logon único do Azure AD para o Nitro Productivity Suite

Configure e teste o SSO do Azure AD com o Nitro Productivity Suite usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Nitro Productivity Suite.

Para configurar e testar o SSO do Azure AD com o Nitro Productivity Suite, conclua os seguintes blocos de construção:

1. [Configurar o SSO do Azure AD](#configure-azure-ad-sso) – para permitir que os usuários usem esse recurso.
    1. [Crie um usuário de teste do Azure AD](#create-an-azure-ad-test-user) para testar o logon único do Azure AD com B.Fernandes.
    1. [Atribua o usuário de teste do Azure AD](#assign-the-azure-ad-test-user) para permitir que B.Fernandes use o logon único do Azure AD.
1. [Configure o SSO do Nitro Productivity Suite](#configure-nitro-productivity-suite-sso) para definir as configurações de logon único no lado do aplicativo.
    1. [Crie um usuário de teste do Nitro Productivity Suite](#create-a-nitro-productivity-suite-test-user) para ter um equivalente de B.Fernandes no Nitro Productivity Suite que esteja vinculado à representação do usuário no Azure AD.
1. [Teste o SSO](#test-sso) para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Nitro Productivity Suite**, localize a seção **Gerenciar**. Selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na seção **Certificado de autenticação SAML**, localize **Certificados (Base64)** . Selecione **Baixar** para baixar o certificado e salvá-lo em seu computador.

    ![Captura de tela da seção Certificado de Autenticação SAML, com o link Download realçado](common/certificatebase64.png)
    
1. Na seção **Configurar o Nitro Productivity Suite**, selecione o ícone de cópia ao lado da **URL de Logon**.
    
    ![Captura de tela da seção Configurar o Nitro Productivity Suite, com URLs e ícones de cópia realçados](common/copy-configuration-urls.png)
    
1. No [Portal de administração do Nitro](https://admin.gonitro.com/), na página **Configurações do Enterprise**, localize a seção **Logon Único**. Selecione **Configuração de SSO do SAML**.

    a. Cole a **URL de Logon** da etapa anterior no campo **URL de Entrada**.
    
    b. Carregue o **Certificado (Base64)** da etapa anterior no campo **Certificado de Autenticação X509**.
    
    c. Selecione **Enviar**.
    
    d. Selecione **Habilitar Logon Único**.


1. Volte para o [portal do Azure](https://portal.azure.com/). Na página **Configurar o logon único com o SAML**, selecione o ícone de lápis da **Configuração Básica de SAML** para editar as configurações.

   ![Captura de tela da página Configurar o Logon Único com SAML, com o ícone de lápis realçado](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IdP**, insira os valores para os seguintes campos:

    a. Na caixa de texto **Identificador**, copie e cole o campo **ID da Entidade SAML** do [Portal de Administração do Nitro](https://admin.gonitro.com/). Ele deve ter o seguinte padrão: `urn:auth0:gonitro-prod:<ENVIRONMENT>`

    b. Na caixa de texto **URL de Resposta**, copie e cole o campo **URL do ACS** do [Portal de Administração do Nitro](https://admin.gonitro.com/). Ele deve ter o seguinte padrão: `https://gonitro-prod.eu.auth0.com/login/callback?connection=<ENVIRONMENT>`

1. Selecione **Definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado por **SP**:

    Na caixa de texto **URL de Logon**, digite a URL: `https://sso.gonitro.com/login`

1. Clique em **Salvar**.

1. O aplicativo do Nitro Productivity Suite espera que as declarações SAML estejam em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![Captura de tela de atributos padrão](common/default-attributes.png)

1. Além dos atributos anteriores, o aplicativo do Nitro Productivity Suite espera que mais alguns atributos sejam passados novamente na resposta SAML. Esses atributos são pré-populados, mas você pode examiná-los de acordo com seus requisitos.
    
    | Nome  |  Atributo de origem|
    | ---------------| --------------- |
    | employeeNumber |  user.objectid |


### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, selecione **Azure Active Directory** > **Usuários** > **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de seleção **Mostrar senha** e, em seguida, anote a senha.
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá a B.Fernandes acesso ao Nitro Productivity Suite, que permitirá o uso do logon único do Azure.

1. No portal do Azure, selecione **Aplicativos Empresariais** > **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Nitro Productivity Suite**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e selecione **Usuários e grupos**.

   ![Captura de tela da seção Gerenciar, com Usuários e grupos realçados](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**. Em seguida, na caixa de diálogo **Adicionar Atribuição**, selecione **Usuários e grupos**.

    ![Captura de tela da página Usuários e grupos, com a função Adicionar usuário destacada](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista de usuários. Em seguida, escolha **Selecionar** na parte inferior da tela.
1. Se você esperar um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, selecione a função apropriada para o usuário na lista. Em seguida, escolha **Selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.

## <a name="configure-nitro-productivity-suite-sso"></a>Configurar o SSO do Nitro Productivity Suite

Para configurar o logon único no lado do Nitro Productivity Suite, envie o **Certificado (Base64)** baixado e as URLs copiadas apropriadas do portal do Azure para a [equipe de suporte do Nitro Productivity Suite](https://www.gonitro.com/support). Essa equipe garante que a conexão de SSO do SAML seja definida corretamente em ambos os lados.

### <a name="create-a-nitro-productivity-suite-test-user"></a>Criar um usuário de teste do Nitro Productivity Suite

O Nitro Productivity Suite é compatível com o provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há nenhuma ação para você executar. Se um usuário ainda não existir no Nitro Productivity Suite, um será criado após a autenticação.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD usando o Painel de Acesso.

Ao selecionar o bloco do Nitro Productivity Suite no Painel de Acesso, você será conectado automaticamente ao Nitro Productivity Suite para o qual configurou o SSO. Para obter mais informações, confira [Entrar e iniciar aplicativos no portal Meus Aplicativos](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais para a integração de aplicativos SaaS ao Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Nitro Productivity Suite com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Proteger o Nitro Productivity Suite com visibilidade e controles avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

