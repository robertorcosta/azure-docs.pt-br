---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao New Relic | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o New Relic.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: jeedes
ms.openlocfilehash: 4cf3f9d0ae23bab4d2412b47e5841d6b8a56b65a
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327059"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao New Relic

Neste tutorial, você aprenderá a integrar o New Relic ao Azure AD (Azure Active Directory). Ao integrar o New Relic ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao New Relic.
* Permitir que os usuários sejam conectados automaticamente ao New Relic com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do New Relic.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O New Relic dá suporte ao SSO iniciado por **SP e IdP**.
* Depois de configurar o New Relic, você poderá impor um controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-new-relic-application-from-the-gallery"></a>Adicionar o aplicativo New Relic por meio da galeria

Para configurar a integração do New Relic ao Azure AD, você precisará adicionar o **New Relic (By Organization)** por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. Selecione o serviço **Azure Active Directory**.
1. Selecione **Aplicativos empresariais**.
1. Para adicionar um novo aplicativo, selecione **Novo aplicativo**.
1. Na página **Procurar na Galeria do Azure AD**, digite **New Relic (By Organization)** na caixa de pesquisa.
1. Selecione **New Relic (By Organization)** no painel de resultados e escolha **Criar**. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-new-relic"></a>Configurar e testar o SSO do Azure AD para o New Relic

Configure e teste o SSO do Azure AD com o New Relic usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do New Relic.

Para configurar e testar o SSO do Azure AD com o New Relic, conclua as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
   1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
   1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do New Relic](#configure-new-relic-sso)** : para definir as configurações de logon único no lado do New Relic.
   1. **[Criar um usuário de teste do New Relic](#create-a-new-relic-test-user)** : para ter um equivalente para B.Fernandes no New Relic que esteja vinculado ao usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **New Relic by Organization**, localize a seção **Gerenciar** e selecione **Logon único**.

1. Na página **Selecionar um método de logon único**, escolha **SAML**.

1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, preencha os valores para **Identificador** e **URL de Resposta**.

   * Esses valores podem ser recuperados por meio do aplicativo New Relic **Minha Organização**. Para usar esse aplicativo, execute estas etapas:
      1. [Faça logon](https://login.newrelic.com/) no New Relic.
      1. No menu superior, selecione **Aplicativos**.
      1. Na seção **Seus aplicativos**, escolha **Minha Organização**.
      1. Clique em **Domínios de autenticação**.
      1. Escolha o domínio de autenticação ao qual deseja conectar o SSO do Azure AD (caso você tenha mais de um domínio de autenticação). A maioria das empresas tem apenas um domínio de autenticação chamado **Padrão**. Com um só domínio de autenticação, não é necessário selecioná-lo.
      1. Na seção **Autenticação**, a opção **URL do Consumidor de Declaração** contém o valor a ser usado em **URL de Resposta**.
      1. Na seção **Autenticação**, a opção **Nossa ID da entidade** contém o valor a ser usado em **Identificador**.

1. Na seção **Atributos e Declarações de Usuário**, verifique se **Identificador de Usuário Exclusivo** é mapeado para um campo que contém o endereço de email que está sendo usado no New Relic.

   * O campo padrão **user.userprincipalname** funcionará para você se os valores forem iguais aos endereços de email do New Relic.
   * O campo **user.mail** poderá funcionar melhor se **user.userprincipalname** não for o endereço de email do New Relic.

1. Na seção **Certificado de Autenticação SAML**, copie a **URL de Metadados de Federação do Aplicativo** e salve o valor para uso posterior.

1. Na seção **Configurar o New Relic by Organization**, copie a **URL de Logon** e salve o valor para uso posterior.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No portal do Azure, selecione o serviço **Azure Active Directory**
1. Selecione **Usuários**.
1. Para adicionar um novo usuário, selecione **Novo usuário** na parte superior da tela.
1. Na página **Novo usuário**, siga estas etapas:
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `b.simon@contoso.com`. Isso deve corresponder ao endereço de email que será usado no lado do New Relic.
   1. No campo **Nome**, insira `B.Simon`.  
   1. Marque a caixa de seleção **Mostrar senha** e salve o valor exibido no campo **Senha inicial**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure AD permitindo acesso ao aplicativo **New Relic by Organization**.

1. No portal do Azure, selecione o serviço **Azure Active Directory**
1. Selecione **Aplicativos empresariais**.
1. Na lista de aplicativos, selecione **New Relic by Organization**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Selecione **Adicionar usuário** e **Usuários e grupos** (ou **Usuários**, dependendo do nível do seu plano) na caixa de diálogo **Adicionar Atribuição**.

   ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos** (ou **Usuários**), selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-new-relic-sso"></a>Configurar o SSO do New Relic

Siga estas etapas para configurar o SSO no New Relic.

1. [Faça logon](https://login.newrelic.com/) no New Relic.

1. No menu superior, selecione **Aplicativos**.

1. Na seção **Seus aplicativos**, escolha **Minha Organização**.

1. Clique em **Domínios de autenticação**.

1. Escolha o domínio de autenticação ao qual deseja conectar o SSO do Azure AD (caso você tenha mais de um domínio de autenticação). A maioria das empresas tem apenas um domínio de autenticação chamado **Padrão**. Com um só domínio de autenticação, não é necessário selecioná-lo.

1. Na seção **Autenticação**, clique em **Configurar**.

   1. No campo **Origem dos metadados do SAML**, insira o valor salvo anteriormente do campo **URL de Metadados de Federação do Aplicativo** no lado do Azure AD.

   1. No campo **URL de destino do SSO**, insira o valor salvo anteriormente do campo **URL de Logon** no lado do Azure AD.

   1. Clique em **Salvar** depois de verificar se as configurações estão corretas no lado do Azure AD e do New Relic. Se os dois lados não estiverem configurados corretamente, os usuários não poderão fazer logon no New Relic.

### <a name="create-a-new-relic-test-user"></a>Criar um usuário de teste do New Relic

Nesta seção, você criará um usuário chamado B.Fernandes no New Relic. Siga estas etapas para criar o usuário.

1. [Faça logon](https://login.newrelic.com/) no New Relic.

1. No menu superior, selecione **Aplicativos**.

1. Na seção **Seus aplicativos**, selecione **Gerenciamento de Usuários**.

1. Clique no botão **Adicionar usuário**.

   1. No campo **Nome**, insira **B.Fernandes**.
   
   1. No campo **Email**, insira o valor que será enviado pelo SSO do Azure AD.
   
   1. Escolha um **Tipo** e um **Grupo** de usuário para o usuário. Para um usuário de teste, **Usuário Básico** em Tipo e **Usuário** em Grupo são opções razoáveis.
   
   1. Clique em **Adicionar Usuário** para salvar o usuário.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do **New Relic by Organization** no Painel de Acesso, você deverá ser conectado automaticamente ao New Relic. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o New Relic com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)
