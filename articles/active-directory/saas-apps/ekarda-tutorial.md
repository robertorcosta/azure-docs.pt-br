---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Ekarda | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Ekarda.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8e2945aa-46fc-41bc-a530-3807a5dcb76a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/15/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: aacaec5ff632385a1f1686610370bb92eb63c349
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87017543"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ekarda"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Ekarda

Neste tutorial, você aprenderá a integrar o Ekarda ao Azure AD (Azure Active Directory). Ao integrar o Ekarda ao Azure AD, você poderá:

* Controlar, no Azure AD, quem tem acesso ao Ekarda.
* Permitir que os usuários sejam conectados automaticamente ao Ekarda com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Ekarda habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Ekarda é compatível com o SSO iniciado por **SP e IDP**
* O Ekarda é compatível com o provisionamento de usuário **Just-In-Time**

* Após configurar o Ekarda, você poderá impor controles de sessão, que protegem contra a exfiltração e infiltração dos dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-ekarda-from-the-gallery"></a>Adicionar o Ekarda por meio da galeria

Para configurar a integração do Ekarda com o Azure AD, é necessário adicionar o Ekarda à sua lista de aplicativos SaaS gerenciados por meio da galeria.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Ekarda** na caixa de pesquisa.
1. Selecione **Ekarda** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-ekarda"></a>Configurar e testar logon único do Azure AD para o Ekarda

Configure e teste o SSO do Azure AD com o Ekarda usando um usuário de teste com o nome **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Ekarda.

Para configurar e testar o SSO do Azure AD com o Ekarda, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Ekarda](#configure-ekarda-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Ekarda](#create-ekarda-test-user)** – para ter um equivalente de B.Fernandes no Ekarda que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Ekarda**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração básica do SAML**, se você tiver um **arquivo de metadados do provedor de serviços**, execute as seguintes etapas:
    
    a. Clique em **Carregar arquivo de metadados**.

    b. Clique no **logotipo da pasta** para selecionar o arquivo de metadados e depois em **Carregar**.

    c. Depois que o arquivo de metadados for carregado com êxito, os valores de **Identificador** e **URL de Resposta** serão preenchidos automaticamente na caixa de texto da seção do Ekarda:

    > [!Note]
    > Se os valores de **Identificador** e **URL de Resposta** não forem populados automaticamente, preencha-os manualmente de acordo com suas necessidades.

1. Se você não tiver o **arquivo de metadados do Provedor de Serviços**, na seção **Configuração SAML Básica**, se desejar configurar o aplicativo no modo iniciado por **IDP**, digite os valores para os seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://my.ekarda.com/users/saml_metadata/<COMPANY_ID>`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://my.ekarda.com/users/saml_acs/<COMPANY_ID>`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://my.ekarda.com/users/saml_sso/<COMPANY_ID>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Entre em contato com a [equipe de suporte ao cliente do Ekarda](mailto:contact@ekarda.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique no botão copiar para copiar o **Certificado(Base64)** e salvá-lo em seu computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Ekarda**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao Ekarda.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Ekarda**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-ekarda-sso"></a>Configurar o SSO do Ekarda

1. Em outra janela do navegador da Web, entre em seu site de empresa do Ekarda como administrador.

1. Clique em **Admin** -> **Minha Conta**.

    ![Configuração do Ekarda](./media/ekarda-tutorial/ekarda.png)    

1. Na parte inferior da página, você encontrará a seção **CONFIGURAÇÕES DE SAML**, em que você configurará essa integração SAML.
1. Na seguinte página, execute as seguintes etapas:

    ![Configuração do Ekarda](./media/ekarda-tutorial/ekarda1.png)

    a. Clique no link **Metadados do Provedor de Serviços** e salve-o como arquivo em seu computador.

    b. Marque a caixa de seleção **Habilitar SAML**.

    c. Na caixa de texto **ID da Entidade de IdP**, cole o valor da **ID da Entidade** copiado do portal do Azure.

    d. Na caixa de texto **URL de Logon de IdP**, cole o valor da **URL de Logon** copiado do portal do Azure.

    e. Na caixa de texto **URL de Logon de IdP**, cole o valor da **URL de Logout** copiado do portal do Azure.

    f. Abra o **Certificado(Base64)** baixado do portal do Azure no Bloco de Notas e cole o conteúdo na caixa de texto **Certificado x509 de IdP**.

    g. Selecione **Habilitar o SLO** na seção **OPÇÕES**.

    h. Clique em **Atualizar**.

### <a name="create-ekarda-test-user"></a>Criar um usuário de teste do Ekarda

Nesta seção, um usuário chamado B.Fernandes será criado no Ekarda. O Ekarda dá suporte ao o provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Ekarda, será criado um após a autenticação.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Ekarda no Painel de Acesso, você deverá ser conectado automaticamente ao Ekarda para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Ekarda com o Azure AD](https://aad.portal.azure.com/)

- Use a [solução de eCard empresarial do Ekarda](https://ekarda.com/ecards-ecards-with-logo-for-business-corporate-enterprise) para provisionar qualquer número de pessoal para enviar eCards com o logo da sua empresa a clientes e colegas. Saiba mais sobre como provisionar o [Ekarda como uma solução de SSO](https://support.ekarda.com/#SSO-Implementation).

- [O que é controle de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger o Ekarda com visibilidade e controles avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
