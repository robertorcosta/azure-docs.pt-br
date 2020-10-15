---
title: 'Tutorial: Integração do Azure Active Directory ao Gerenciador de Senhas Protetor e Cofre Digital | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Gerenciador de Senhas Protetor e Cofre Digital.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/07/2020
ms.author: jeedes
ms.openlocfilehash: c6bd0c130e860a5700256a54c081cc046219b41a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88546739"
---
# <a name="tutorial-azure-active-directory-integration-with-keeper-password-manager--digital-vault"></a>Tutorial: integração do Azure Active Directory ao Gerenciador de Senhas Protetor e Cofre Digital

Neste tutorial, você aprenderá a integrar o Gerenciador de Senhas Protetor e o Cofre Digital ao Azure Active Directory (Azure AD).
Integrar do Gerenciador de Senhas Protetor e do Cofre Digital ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao Keeper Password Manager & Digital Vault.
* Você pode habilitar os usuários a serem conectados automaticamente com o Keeper Password Manager & Digital Vault (Logon Único) usando suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Prerequisites

Para configurar a integração do Azure AD ao Gerenciador de Senhas Protetor e Cofre Digital, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Uma assinatura habilitada para logon único do Keeper Password Manager & Digital Vault

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Keeper Password Manager & Digital Vault é compatível com o SSO iniciado por **SP**

* O Keeper Password Manager & Digital Vault é compatível com o provisionamento do usuário **Just-In-Time**

* Depois de configurar o Keeper Password Manager & Digital Vault, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-keeper-password-manager--digital-vault-from-the-gallery"></a>Adicionando Gerenciador de Senhas Protetor e Cofre Digital da galeria

Para configurar a integração do Gerenciador de Senhas Protetor e Cofre Digital no Azure AD, você precisa adicionar o Gerenciador de Senhas Protetor e Cofre Digital da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Keeper Password Manager & Digital Vault** na caixa de pesquisa.
1. Selecione **Keeper Password Manager & Digital Vault** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-keeper-password-manager--digital-vault"></a>Configurar e testar o SSO do Azure AD para o Keeper Password Manager & Digital Vault

Configure e teste o SSO do Azure AD com o Keeper Password Manager & Digital Vault usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Keeper Password Manager & Digital Vault.

Para configurar e testar o SSO do Azure AD com o Keeper Password Manager & Digital Vault, você precisará concluir os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.

    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.

1. **[Configurar o SSO do Keeper Password Manager & Digital Vault](#configure-keeper-password-manager--digital-vault-sso)** : para definir as configurações de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do Keeper Password Manager & Digital Vault](#create-keeper-password-manager--digital-vault-test-user)** – para ter um equivalente de Brenda Fernandes no Keeper Password Manager & Digital Vault vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Keeper Password Manager & Digital Vault**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: 
    * Para o **SSO de nuvem**: `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * Para o **SSO local**: `https://<KEEPER_FQDN>/sso-connect/saml/login`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão:
    * Para o **SSO de nuvem**: `https://keepersecurity.com/api/rest/sso/saml/<CLOUD_INSTANCE_ID>`
    * Para o **SSO local**: `https://<KEEPER_FQDN>/sso-connect`

    c. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: 
    * Para o **SSO de nuvem**: `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * Para o **SSO local**: `https://<KEEPER_FQDN>/sso-connect/saml/sso`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta, o Identificador e a URL de Logon reais. Entre em contato com a [equipe de suporte do Gerenciador de Senhas Protetor e Cliente do Cofre Digital](https://keepersecurity.com/contact.html) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar o Keeper Password Manager & Digital Vault**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Keeper Password Manager & Digital Vault.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Gerenciador de Senhas Protetor e Cofre Digital**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.


## <a name="configure-keeper-password-manager--digital-vault-sso"></a>Configurar o SSO do Keeper Password Manager & Digital Vault

Para configurar o logon único no lado da **Configuração do Gerenciador de Senhas Protetor e Cofre Digital**, siga as orientações fornecidas no [Guia de Suporte do Protetor](https://docs.keeper.io/sso-connect-guide/).

### <a name="create-keeper-password-manager--digital-vault-test-user"></a>Criar um usuário de teste do Keeper Password Manager & Digital Vault

Para permitir que os usuários do Azure AD fazer logon no Gerenciador de Senhas Protetor e Cofre Digital, eles devem ser provisionados no Gerenciador de Senhas Protetor e Cofre Digital. O aplicativo dá suporte ao provisionamento de usuário just-in-time e, após a autenticação, os usuários serão automaticamente criados no aplicativo. Você poderá contatar [Suporte Protetor](https://keepersecurity.com/contact.html) se quiser configurar usuários manualmente.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando clicar no bloco do Keeper Password Manager & Digital Vault no Painel de Acesso, você deverá se conectar automaticamente ao Keeper Password Manager & Digital Vault para o qual o SSO foi configurado. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimentar o Keeper Password Manager & Digital Vault com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
