---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Cloud Academy – SSO | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Cloud Academy – SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 560c86fb-e25c-4428-a1dd-a5711cfece5c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb118e4d2fa811bf88ff13db84848ebb230ed209
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87293239"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cloud-academy---sso"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Cloud Academy – SSO

Neste tutorial, você aprenderá a integrar o Cloud Academy – SSO ao Azure Active Directory (Azure AD). Com a integração do Cloud Academy – SSO ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Cloud Academy – SSO.
* Permitir que os usuários, usando as respectivas contas do Azure AD, sejam conectados automaticamente ao Cloud Academy – SSO.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Cloud Academy – SSO.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Cloud Academy – SSO é compatível com SSO iniciado por **SP**

* Após configurar o Cloud Academy – SSO, você poderá impor o controle de sessão, que protegerá contra o vazamento e a infiltração dos dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-cloud-academy---sso-from-the-gallery"></a>Como adicionar o Cloud Academy – SSO da galeria

Para configurar a integração do Cloud Academy – SSO ao Azure AD, você precisa adicionar o Cloud Academy – SSO por meio da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Cloud Academy – SSO** na caixa de pesquisa.
1. Selecione **Cloud Academy – SSO** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-cloud-academy---sso"></a>Configurar e testar o SSO do Azure AD para o Cloud Academy – SSO

Configure e teste o SSO do Azure AD com o Cloud Academy – SSO usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Cloud Academy – SSO.

Para configurar e testar o SSO do Azure AD com o Cloud Academy – SSO, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Cloud Academy – SSO](#configure-cloud-academy-sso-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do Cloud Academy – SSO](#create-cloud-academy-sso-test-user)** : para ter um equivalente de B.Fernandes no Cloud Academy – SSO que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Cloud Academy – SSO**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    Na caixa de texto **URL de Logon**, digite a URL: `https://cloudacademy.com/login/enterprise/`

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure dando a ela acesso ao Cloud Academy – SSO.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Cloud Academy – SSO**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-cloud-academy-sso-sso"></a>Configurar o SSO do Cloud Academy

1. Em outra janela do navegador, entre no seu site de empresa do Cloud Academy – SSO como administrador.

1. Clique no nome da empresa e selecione **Configurações e Integrações** no menu.

    ![Configuração ](./media/cloud-academy-sso-tutorial/config-1.PNG)

1. Na página **Configurações e Integrações**, vá para a guia **Integrações** e clique no cartão **SSO**.

    ![Configuração ](./media/cloud-academy-sso-tutorial/config-2.PNG)

1. Execute as etapas abaixo na seguinte página:

    ![Configuração ](./media/cloud-academy-sso-tutorial/config-3.PNG)

    a. Na caixa de texto **URL da ID da Entidade**, cole o valor da **ID da Entidade** copiado do portal do Azure.

    b. Na caixa de texto **URL de SSO**, cole o valor da **URL de Logon** copiado do portal do Azure.

    c. Abra o **Certificado (Base64)** baixado do portal do Azure no Bloco de notas e cole o conteúdo na caixa de texto **Certificado**.

    d. Na caixa de texto **Formato da ID de Nome**, use o valor padrão, `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`

1. Clique no botão **Salvar**.

    > [!NOTE]
    > Para obter mais informações sobre como configurar o Cloud Academy – SSO, confira este [artigo de suporte](https://support.cloudacademy.com/hc/articles/360043908452-Setting-Up-Single-Sign-On).

### <a name="create-cloud-academy-sso-test-user"></a>Criar um usuário de teste do Cloud Academy – SSO

1. Faça logon no **Cloud Academy – SSO**.

1. Clique no nome da empresa e selecione **Membros** no menu.

    ![ Criar um usuário de teste ](./media/cloud-academy-sso-tutorial/create-user.PNG)

1. Clique em **Convidar Membros** e selecione **Convidar Um Membro**.

    ![ Criar um usuário de teste ](./media/cloud-academy-sso-tutorial/create-user-1.PNG)

1. Preencha os campos obrigatórios e clique em **Convidar**.

    ![ Criar um usuário de teste ](./media/cloud-academy-sso-tutorial/create-user-2.PNG)

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Cloud Academy – SSO no Painel de Acesso, você deverá ser conectado automaticamente ao Cloud Academy – SSO para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Cloud Academy – SSO com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger o Cloud Academy – SSO com visibilidade e controles avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)