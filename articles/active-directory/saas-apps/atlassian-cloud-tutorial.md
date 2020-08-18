---
title: 'Tutorial: Integração do Azure Active Directory ao Atlassian Cloud | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Atlassian Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: celested
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 08/04/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dcb3522898a40dc79e8465af813633015568f1c8
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88033641"
---
# <a name="tutorial-integrate-atlassian-cloud-with-azure-active-directory"></a>Tutorial: Integração do Atlassian Cloud com o Azure Active Directory

Neste tutorial, você aprenderá a integrar o Atlassian Cloud ao Azure Active Directory (Azure AD). Ao integrar o Atlassian Cloud com o Azure AD, você poderá:

* Controlar, no Azure AD, quem tem acesso ao Atlassian Cloud.
* Permitir que os usuários sejam conectados automaticamente ao Atlassian Cloud com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* Assinatura do Atlassian Cloud habilitada para logon único (SSO).
* Para habilitar logon único do SAML (Security Assertion Markup Language) para produtos Atlassian Cloud, é necessário configurar o Atlassian Access. Saiba mais sobre o [Atlassian Access]( https://www.atlassian.com/enterprise/cloud/identity-manager).

> [!NOTE]
> Essa integração também está disponível para uso do ambiente de Nuvem do Governo dos EUA do Azure AD. Encontre esse aplicativo na Galeria de Aplicativos de Nuvem do Governo dos EUA do Azure AD e configure-o da mesma forma que na nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. 

* O Atlassian Cloud é compatível com SSO iniciado por **SP e IDP**
* O Atlassian Cloud é compatível com [provisionamento e desprovisionamento de usuário automatizados](atlassian-cloud-provisioning-tutorial.md)
* Após configurar o Atlassian Cloud, você poderá impor controles de sessão, que protegem contra exfiltração e infiltração de dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-atlassian-cloud-from-the-gallery"></a>Adição do Atlassian Cloud da galeria

Para configurar a integração do Atlassian Cloud ao Azure AD, você precisará adicionar o Atlassian Cloud da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Atlassian Cloud** na caixa de pesquisa.
1. Selecione **Atlassian Cloud** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar o SSO do Azure AD

Configure e teste o SSO do Azure AD com o Atlassian Cloud usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Atlassian Cloud.

Para configurar e testar o SSO do Azure AD com o Atlassian Cloud, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD com o Atlassian Cloud](#configure-azure-ad-sso)** – para permitir que os usuários usem o SSO do SAML baseado no Azure AD com o Atlassian Cloud.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Criar um usuário de teste do Atlassian Cloud](#create-atlassian-cloud-test-user)** – para ter um equivalente de B.Fernandes no Atlassian Cloud que esteja vinculado à sua representação no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. Antes de começar, vá para a sua instância de produto do Atlassian e copie/salve a URL da Instância
   > [!NOTE]
   > A URL deve se ajustar ao padrão `https://<instancename>.atlassian.net`

   ![image](./media/atlassian-cloud-tutorial/get-atlassian-instance-name.png)

1. Abra o [Portal de Administração do Atlassian](https://admin.atlassian.com/) e clique no nome da sua organização

   ![image](./media/atlassian-cloud-tutorial/click-on-organization-in-atlassian-access.png)

1. É necessário verificar o domínio antes de configurar logon único. Para obter mais informações, consulte o documento de [verificação de domínio do Atlassian](https://confluence.atlassian.com/cloud/domain-verification-873871234.html).
1. Na Tela do Portal de Administração do Atlassian, selecione **Segurança** na gaveta esquerda

   ![image](./media/atlassian-cloud-tutorial/click-on-security-in-atlassian-access.png)

1. Na Tela de Segurança do Portal de Administração do Atlassian, selecione **Logon único do SAML** na gaveta esquerda

   ![image](./media/atlassian-cloud-tutorial/click-on-saml-sso-in-atlassian-access-security.png)

1. Clique em **Adicionar Configuração do SAML** e mantenha a página aberta

   ![image](./media/atlassian-cloud-tutorial/saml-configuration-in-atlassian-access-security-saml-sso.png)

   ![image](./media/atlassian-cloud-tutorial/add-saml-configuration.png)

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Atlassian Cloud**, localize a seção **Gerenciar** e selecione **Configurar logon único**.

   ![image](./media/atlassian-cloud-tutorial/set-up-sso.png)

1. Na página **Escolher um método de logon único**, escolha **SAML**.

   ![image](./media/atlassian-cloud-tutorial/saml-in-azure.png)

1. Na página **Configurar logon único com SAML**, role para baixo até **Configurar Atlassian Cloud**
   
   a. Clique em **URLs de Configuração**

   ![image](./media/atlassian-cloud-tutorial/configuration-urls.png)
   
   b. Copie o valor **Identificador do Azure AD** do portal do Azure, cole-o na caixa de texto **ID da Entidade do Provedor de Identidade** em Atlassian
   
   c. Copie o valor **URL de Logon** do portal do Azure, cole-o na caixa de texto **URL de SSO do Provedor de Identidade** em Atlassian

   ![image](./media/atlassian-cloud-tutorial/configuration-urls-azure.png)

   ![image](./media/atlassian-cloud-tutorial/entity-id-and-ss.png)

1. Na página **Configurar o Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e escolha **Baixar** para fazer o download do certificado e salvá-lo no computador.

   ![image](./media/atlassian-cloud-tutorial/certificate.png)

   ![image](./media/atlassian-cloud-tutorial/certificate-1.png)

1. **Adicionar/Salvar** a configuração de SAML no Atlassian

1. Se você quiser configurar o aplicativo no modo iniciado por **IDP**, edite a seção **Configuração de SAML Básica** da página **Configurar Logon Único com SAML** no Azure e abra a **página de logon único SAML** no portal de administração do Atlassian

   a. Copie o valor **ID da Entidade do SP** do Atlassian, cole-o na caixa **Identificador (ID da Entidade)** no Azure e defina-a como padrão
   
   b. Copie o valor **URL do Serviço do Consumidor de Declaração do SP** do Atlassian, cole-o na caixa **URL de Resposta (URL do Serviço do Consumidor de Asserção)** no Azure e defina-o como padrão
   
   c. Cole o valor **URL de Instância** que você copiou na etapa 1 na caixa **Estado de Retransmissão** no Azure

   ![image](./media/atlassian-cloud-tutorial/copy-urls.png)

   ![image](./media/atlassian-cloud-tutorial/edit-button.png)

   ![image](./media/atlassian-cloud-tutorial/urls.png)
   
1. Se você quiser configurar o aplicativo no modo iniciado por **SP**, edite a seção **Configuração de SAML Básica** da página **Configurar Logon Único com SAML** no Azure. Copie a **URL da Instância** (da etapa 1) e cole-a na caixa **URL de Logon** no Azure

   ![image](./media/atlassian-cloud-tutorial/edit-button.png)

   ![image](./media/atlassian-cloud-tutorial/sign-on-URL.png)
   
1. O aplicativo Atlassian Cloud espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizados para a configuração de atributos do token SAML. Você pode editar o mapeamento de atributo clicando no ícone **Editar**. 

   ![image](./media/atlassian-cloud-tutorial/default-attributes.png)
   
   1. Mapeamento de atributo para um locatário do Azure AD com uma licença do Office 365
      
      a. Clique na declaração do **Identificador de Usuário Único (ID de Nome)**

      ![image](./media/atlassian-cloud-tutorial/user-attributes-and-claims.png)
      
      b. O Atlassian Cloud espera que o **nameidentifier** (**Identificador de Usuário Único**) seja mapeado para o email do usuário (**user.email**). Edite o **Atributo de origem** e altere-o para **user.mail**. Salve as alterações à declaração.

      ![image](./media/atlassian-cloud-tutorial/unique-user-identifier.png)
      
      c. Os mapeamentos de atributos finais devem ter a seguinte aparência.

      ![image](common/default-attributes.png)
      
   1. Mapeamento de atributo para um locatário do Azure AD sem uma licença do Office 365 

      a. Clique na declaração de **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** .

      ![image](./media/atlassian-cloud-tutorial/email-address.png)
         
      b. O Azure não preenche o atributo **user.mail** para usuários criados em locatários do Azure AD sem licenças do Office 365 e armazena o email para esses usuários no atributo **userPrincipalName**. O Atlassian Cloud espera que o **nameidentifier** (**Identificador de Usuário Único**) seja mapeado para o email do usuário (**user.userprincipalname**).  Edite o **Atributo de origem** e altere-o para **user.userprincipalname**. Salve as alterações à declaração.

      ![image](./media/atlassian-cloud-tutorial/set-email.png)
         
      c. Os mapeamentos de atributos finais devem ter a seguinte aparência.

      ![image](common/default-attributes.png)
     
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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ele acesso ao Atlassian Cloud.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Atlassian Cloud**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](./media/atlassian-cloud-tutorial/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-atlassian-cloud-test-user"></a>Criar um usuário de teste do Atlassian Cloud

Para permitir que os usuários do Azure AD entrar no Atlassian Cloud, provisione as contas de usuário manualmente no Atlassian Cloud fazendo o seguinte:

1. No painel **Administração**, selecione **Usuários**.

    ![O link de Usuários do Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-14.png)

1. Para criar um usuário no Atlassian Cloud, selecione **Convidar usuário**.

    ![Criar um usuário do Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-15.png)

1. Na caixa **Endereço de email**, insira o endereço de email do usuário e atribua o acesso ao aplicativo.

    ![Criar um usuário do Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-16.png)

1. Para enviar um convite por email para o usuário, selecione **Convidar usuários**. Um convite por email é enviado ao usuário e, depois de aceitar o convite, o usuário está ativo no sistema.

> [!NOTE]
> Você também pode criar usuários em massa clicando no botão **Criar em Massa** na seção **Usuários**.

### <a name="test-sso"></a>Testar o SSO

Ao selecionar o bloco do Atlassian Cloud no Painel de Acesso, você deverá ser conectado automaticamente ao Atlassian Cloud para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Atlassian Cloud com o Azure AD](https://aad.portal.azure.com/)

- [Como proteger o Atlassian Cloud com visibilidade e controles avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)