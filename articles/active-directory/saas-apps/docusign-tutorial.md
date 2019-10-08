---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao DocuSign | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o DocuSign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f02492dd7c9563c8c0002a63f0b105bd0be8b14
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71345575"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-docusign"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory com o DocuSign

Neste tutorial, você aprenderá a integrar o DocuSign ao Microsoft Azure AD (Active Directory). Quando integrar o DocuSign ao Azure AD, você poderá:

* Use o Azure AD para controlar quem tem acesso ao DocuSign.
* Habilitar a entrada automática no DocuSign para seus usuários por meio de suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS (software como serviço) ao Azure AD, confira [Logon único em aplicativos no Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do DocuSign habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste para verificar se:

* o DocuSign dá suporte ao SSO iniciado por SP (provedor de serviços).

* o DocuSign dá suporte ao provisionamento de usuário *Just-In-Time*.

* o DocuSign dá suporte a [provisionamento automático de usuários](https://docs.microsoft.com/azure/active-directory/saas-apps/docusign-provisioning-tutorial).

## <a name="adding-docusign-from-the-gallery"></a>Adicionando o DocuSign por meio da galeria

Para configurar a integração do DocuSign ao Azure AD, você precisa adicionar o DocuSign à lista de aplicativos SaaS gerenciados por meio da galeria:

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory**.
1. Acesse **Aplicativos Empresariais** e, em seguida, selecione **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, selecione **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **DocuSign** na caixa de pesquisa.
1. Selecione **DocuSign** no painel de resultados e, depois, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-docusign"></a>Configurar e testar o logon único do Azure AD para o DocuSign

Configure e teste o SSO do Azure AD com o DocuSign usando uma usuária de teste chamada **B.Fernandes**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário correspondente do DocuSign.

Para configurar e testar o SSO do Azure AD com o DocuSign, conclua os seguintes blocos de construção:

1. [Configure o SSO do Azure AD](#configure-azure-ad-sso) para que seus usuários possam usar esse recurso.
    1. [Crie um usuário de teste do Azure AD](#create-an-azure-ad-test-user) para testar o logon único do Azure AD com B.Fernandes.
    1. [Atribua o usuário de teste do Azure AD](#assign-the-azure-ad-test-user) para permitir que B.Fernandes use o logon único do Azure AD.
1. [Configurar o SSO do DocuSign](#configure-docusign-sso) para definir as configurações de logon único no lado do aplicativo.
1. [Criar um usuário de teste do DocuSign](#create-docusign-test-user) – para ter um equivalente de B.Fernandes no DocuSign que esteja vinculado à representação de usuário do Azure AD.
1. [Teste o SSO](#test-sso) para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Para habilitar o SSO do Azure AD no portal do Azure, siga estas etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **DocuSign**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, selecione o ícone de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica de SAML**, siga estas etapas:

    a. Na caixa **URL de Logon**, insira uma URL usando o seguinte padrão: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. Na caixa **Identificador (ID da Entidade)** , insira uma URL usando o seguinte padrão: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    > [!NOTE]
    > Esses valores entre colchetes são espaços reservados. Substitua-os pelos valores na URL e no Identificador de logon reais. Esses detalhes são explicados na seção “Exibir Pontos de Extremidade do SAML 2.0” mais adiante neste tutorial.

1. Na página **Configurar logon único com o SAML**, na seção **Certificado de Autenticação do SAML**, localize **Certificado (Base64)** . Selecione **Baixar** para baixar o certificado e salvá-lo em seu computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o DocuSign**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará uma usuária de teste no portal do Azure chamada B.Fernandes.

1. No painel esquerdo no portal do Azure, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.
1. Na parte superior da tela, selecione **Novo usuário**.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira **B.Fernandes**.  
   1. No campo **Nome de usuário**, insira `<username>@<companydomain>.<extension>`. Por exemplo: `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes acesse o DocuSign para que esse usuário possa usar o logon único do Azure.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **DocuSign**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e selecione **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Selecione **Adicionar usuário** e na caixa de diálogo **Adicionar Atribuição**, selecione **Usuários e grupos**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista **Usuários** e pressione o botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, pressione o botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar Atribuição**, selecione o botão **Atribuir**.

## <a name="configure-docusign-sso"></a>Configurar o SSO do DocuSign

1. Para automatizar a configuração no DocuSign, você deve instalar a extensão do navegador Entrada Segura dos Meus Aplicativos selecionando **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, selecione **Configurar DocuSign**. Você é direcionado ao aplicativo DocuSign. Em seguida, forneça as credenciais de administrador para entrar no DocuSign. A extensão do navegador configura automaticamente o aplicativo e automatiza as etapas de 3 a 5.

    ![Configuração da instalação](common/setup-sso.png)

3. Se desejar configurar o DocuSign manualmente, abra uma nova janela do navegador da Web e entre no site da empresa do DocuSign como administrador.

4. No canto superior direito da página, selecione o logotipo do perfil e selecione **Ir para o Administrador**.
  
    ![Ir para o Administrador em Perfil][51]

5. Em sua página de soluções de domínio, selecione **Domínios**.

    ![Soluções de domínio/Domínios][50]

6. Na seção **Domínios**, selecione **DECLARAR DOMÍNIO**.

    ![Opção Declarar Domínio][52]

7. Na caixa de diálogo **Declarar um Domínio**, na caixa **Nome do Domínio**, digite o domínio de empresa e selecione **DECLARAR**. Verifique o domínio e se o status está ativo.

    ![Caixa de diálogo Declarar um Domínio/Nome de Domínio][53]

8. Na página de soluções de domínio, selecione **Provedores de Identidade**.
  
    ![Opção Provedores de Identidade][54]

9. Na seção **Provedores de Identidade**, selecione **ADICIONAR PROVEDOR DE IDENTIDADE**.

    ![Opção Adicionar Provedor de Identidade][55]

10. Na página **Configurações do Provedor de Identidade**, siga estas etapas:

    ![Campos de configurações do Provedor de Identidade][56]

    a. Na caixa **Nome**, digite um nome exclusivo para sua configuração. Não use espaços.

    b. Na **caixa Emissor do Provedor de Identidade**, cole o valor do **Identificador do Azure AD**, que você copiou do portal do Azure.

    c. Na caixa **URL de Logon do Provedor de Identidade**, cole a **URL de Logon**, que você copiou do portal do Azure.

    d. Na caixa **URL de Logoff do Provedor de Identidade**, cole o valor de **URL de Logoff** que você copiou do portal do Azure.

    e. Selecione **Assinar solicitação AuthN**.

    f. Para **Enviar solicitação AuthN por**, selecione **POST**.

    g. Para **Enviar solicitação de logoff por**, selecione **GET**.

    h. Na seção **Mapeamento de Atributo Personalizado**, selecione **ADICIONAR NOVO MAPEAMENTO**.

       ![Interface do usuário de Mapeamento de Atributo Personalizado][62]

    i. Escolha o campo que você deseja mapear para a declaração do Azure AD. Neste exemplo, a declaração **emailaddress** é mapeada com o valor de `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`. Esse é o nome de declaração padrão do Azure AD para a declaração de email. Selecione **SALVAR**.

       ![Campos de Mapeamento de Atributo Personalizado][57]

       > [!NOTE]
       > Use o **Identificador de usuário** apropriado para mapear o usuário do Azure AD para o mapeamento de usuário do DocuSign. Selecione o campo adequado e insira o valor apropriado com base nas configurações de sua organização.

    j. Na seção **Certificado do Provedor de Identidade**, selecione **ADICIONAR CERTIFICADO**, faça upload do certificado baixado do portal do Azure AD e selecione **SALVAR**.

       ![Certificados do Provedor de Identidade/Adicionar certificado][58]

    k. Na seção **Provedores de Identidade**, selecione **AÇÕES** e, em seguida, **Pontos de Extremidade**.

       ![Provedores de Identidade/Pontos de extremidade][59]

    l. Na seção **Exibir Pontos de Extremidade do SAML 2.0** do portal de administração do DocuSign, siga estas etapas:
       1. Copie a **URL do Emissor de Provedor de Serviços** e, em seguida, cole-a na caixa **Identificador** da seção **Configuração Básica do SAML** no portal do Azure.

       1. Copie a **URL de Logon do Provedor de Serviços** e, em seguida, cole-a na caixa **URL de Logon** da seção **Configuração Básica do SAML** no portal do Azure.

       1. Selecione **Fechar**.

       ![Exibir Pontos de Extremidade do SAML 2.0][60]

### <a name="create-docusign-test-user"></a>Criar um usuário de teste do DocuSign

Nesta seção, uma usuária chamada B.Fernandes será criada no DocuSign. O DocuSign dá suporte ao provisionamento de usuário just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no DocuSign, um novo será criado após a autenticação.

>[!Note]
>Caso precise criar um usuário manualmente, contate a [equipe de suporte do DocuSign](https://support.docusign.com/).

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao selecionar o bloco do DocuSign no Painel de Acesso, você deverá ser conectado automaticamente à instância do DocuSign para a qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais sobre como integrar aplicativos SaaS ao Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único no Azure AD?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso condicional no Azure AD?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimentar o DocuSign com o Azure AD](https://aad.portal.azure.com/)

<!--Image references-->

[50]: ./media/docusign-tutorial/tutorial_docusign_18.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[62]: ./media/docusign-tutorial/tutorial_docusign_30.png
