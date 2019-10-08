---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory com o Slack | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Slack.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ffc5e73f-6c38-4bbb-876a-a7dd269d4e1c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b94607691b662dcfcf0e166cd5c339b00ed0573d
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326690"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-slack"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory com o Slack

Neste tutorial, você aprenderá a integrar o Slack com o Azure AD (Azure Active Directory). Ao integrar o Slack no Azure AD, você pode:

* controlar no Azure AD quem tem acesso ao Slack.
* Permitir que os usuários sejam conectados automaticamente ao Slack com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Slack.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Slack dá suporte ao SSO iniciado por **SP**
* O Slack é compatível com o provisionamento de usuário **Just In Time**
* O Slack dá suporte ao [provisionamento **automatizado** de usuários](https://docs.microsoft.com/en-gb/azure/active-directory/saas-apps/slack-provisioning-tutorial)

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="adding-slack-from-the-gallery"></a>Adicionar o Slack da galeria

Para configurar a integração do Slack ao Azure AD, você precisa adicionar o Slack por meio da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Slack** na caixa de pesquisa.
1. Selecione **Slack** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-slack"></a>Configurar e testar o logon único do Azure AD para o Slack

Configure e teste o SSO do Azure AD com o Slack usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Slack.

Para configurar e testar o SSO do Azure AD com o Slack, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Slack](#configure-slack-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Slack](#create-slack-test-user)** – para ter um equivalente de B.Fernandes no Slack que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Slack**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<your Slack company>.slack.com`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL: `https://slack.com`

    > [!NOTE]
    > O valor da URL de Entrada não é real. Atualize o valor com a URL de Entrada real. Contate a [equipe de suporte ao cliente do Slack](https://slack.com/help/contact) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Slack**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao Slack.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **Slack**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-slack-sso"></a>Configurar o SSO do Slack

1. Em outra janela do navegador da Web, entre no site de empresa do Slack como administrador.

2. Navegue até o nome de sua empresa do Slack à esquerda que, em nosso caso foi configurado como **Microsoft Azure AD**, e vá até as **Configurações do Team**, conforme mostrado na seguinte captura de tela.

     ![Configurar o logon único no lado do aplicativo](./media/slack-tutorial/tutorial_slack_001.png)

3. Na seção **Configurações de Equipe**, clique na guia **Autenticação** e em **Alterar Configurações**.

    ![Configurar o logon único no lado do aplicativo](./media/slack-tutorial/tutorial_slack_002.png)

4. No diálogo **Configurações de Autenticação SAML** , realize as seguintes etapas:

    ![Configurar o logon único no lado do aplicativo](./media/slack-tutorial/tutorial_slack_003.png)

    a.  Na caixa de texto **Ponto de Extremidade do SAML 2.0 (HTTP)** , cole o valor da **URL de Logon** copiado do portal do Azure.

    b.  Na caixa de texto **Emissor de provedor de identidade**, cole o valor de **Identificador do Azure AD**, copiado do portal do Azure.

    c.  Abra seu arquivo de certificado baixado no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado Público**.

    d. Defina as três configurações acima conforme apropriado para sua equipe do Slack. Para saber mais sobre as configurações, encontre o **guia de configuração de SSO do Slack** aqui. `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    e.  Clique em **Salvar Configuração**.

### <a name="create-slack-test-user"></a>Criar um usuário de teste do Slack

O objetivo desta seção é criar um usuário chamado B.Fernandes no Slack. O Slack dá suporte ao provisionamento just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Um novo usuário é criado durante uma tentativa de acessar o Slack, caso ele ainda não exista. O Slack também dá suporte ao provisionamento automático de usuário. É possível encontrar [aqui](slack-provisioning-tutorial.md) detalhes de como configurar o provisionamento automático do usuário.

> [!NOTE]
> Se você precisar criar um usuário manualmente, contate a [equipe de suporte do Slack](https://slack.com/help/contact).

> [!NOTE]
> O Azure AD Connect é a ferramenta de sincronização que pode sincronizar Identidades do Active Directory local ao Azure AD e, em seguida, esses usuários sincronizados também poderão usar os aplicativos como outros usuários de nuvem.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Slack no Painel de Acesso, você deverá ser conectado automaticamente ao Slack no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Slack com o Azure AD](https://aad.portal.azure.com/)
