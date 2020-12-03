---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao ekarda | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o ekarda.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/15/2020
ms.author: jeedes
ms.openlocfilehash: 7bb74732074482c12d3bc760e259bb014ccf006f
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96179207"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ekarda"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao ekarda

Neste tutorial, você aprenderá a integrar o ekarda ao Azure AD (Azure Active Directory). Ao integrar o ekarda ao Azure AD, você poderá:

* Controlar, no Azure AD, quem tem acesso ao ekarda.
* Permitir que os usuários sejam conectados automaticamente ao ekarda usando as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS (software como serviço) ao Azure AD, confira [O que é SSO (logon único)?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do ekarda habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O ekarda é compatível com o SSO iniciado por SP e IDP.
* O ekarda é compatível com o provisionamento de usuário Just-In-Time.
* Depois de configurar o ekarda, você poderá impor o controle de sessão. Essa precaução protege contra exfiltração e infiltração dos dados confidenciais de sua organização em tempo real. O controle de sessão é estendido do Controle de Aplicativos de Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-ekarda-from-the-gallery"></a>Adicionar o ekarda por meio da galeria

Para configurar a integração do ekarda ao Azure AD, adicione o ekarda à sua lista de aplicativos SaaS gerenciados por meio da galeria:

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.

1. No painel esquerdo, selecione o serviço **Microsoft Azure Active Directory**.
1. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os Aplicativos**.
1. Para adicionar o novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **ekarda** na caixa de pesquisa.
1. Selecione **ekarda** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ekarda"></a>Configurar e testar logon único do Azure AD para o ekarda

Configure e teste o SSO do Azure AD com o ekarda usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do ekarda.

Para configurar e testar o SSO do Azure AD com o ekarda, conclua as seguintes etapas:

1. [Configurar o SSO do Azure AD](#configure-azure-ad-sso) – para permitir que os usuários usem esse recurso.

    1. [Crie um usuário de teste do Azure AD](#create-an-azure-ad-test-user) para testar o logon único do Azure AD com B.Fernandes.
    1. [Atribua o usuário de teste do Azure AD](#assign-the-azure-ad-test-user) para permitir que B.Fernandes use o logon único do Azure AD.
1. [Configure o SSO do ekarda](#configure-ekarda-sso) para configurar o logon único no lado do aplicativo.
    * [Crie um usuário de teste do ekarda](#create-an-ekarda-test-user) para ter um equivalente de B.Fernandes no ekarda que esteja vinculado à representação do usuário no Azure AD.
1. [Teste o SSO](#test-sso) para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas no portal do Azure para habilitar o SSO do Microsoft Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com/).
1. Na página de integração de aplicativos do **ekarda**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com o SAML**, selecione o ícone de lápis para editar as configurações da **Configuração Básica de SAML**.

   ![Captura de tela da página Configurar o Logon Único com SAML, com o ícone de lápis realçado.](common/edit-urls.png)

1. Na seção **Configuração Básica de SAML**, se você vir o **arquivo de metadados do Provedor de Serviços**, conclua estas etapas:
    1. Selecione **Carregar o arquivo de metadados**.
    1. Selecione o ícone de pasta para selecionar o arquivo de metadados e, em seguida, selecione **Carregar**.
    1. Quando o arquivo de metadados for carregado com êxito, os valores de **Identificador** e **URL de Resposta** serão exibidos automaticamente na caixa de texto da seção do ekarda.

    > [!Note]
    > Se os valores de **Identificador** e **URL de Resposta** não forem exibidos automaticamente, preencha os valores manualmente de acordo com suas necessidades.

1. Se você não vir o **arquivo de metadados do Provedor de Serviços** na seção **Configuração Básica de SAML** e se desejar configurar o aplicativo no modo iniciado por IDP, insira os valores para os seguintes campos:

    1. Na caixa de texto **Identificador**, digite uma URL que siga este padrão: `https://my.ekarda.com/users/saml_metadata/<COMPANY_ID>`
    1. Na caixa de texto **URL de Resposta**, digite uma URL que siga este padrão: `https://my.ekarda.com/users/saml_acs/<COMPANY_ID>`

1. Caso queira configurar o aplicativo no modo iniciado por SP, selecione **Definir URLs adicionais** e faça isto:

    * Na caixa de texto **URL de Entrada**, digite uma URL que siga este padrão: `https://my.ekarda.com/users/saml_sso/<COMPANY_ID>`

    > [!NOTE]
    > Os valores nas duas etapas anteriores não são reais. Atualize-os com o Identificador, a URL de Resposta e a URL de Logon reais. Entre em contato com a [equipe de suporte ao cliente do ekarda](mailto:contact@ekarda.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, selecione **Baixar** para salvar **Certificado (Base64)** no seu computador.

    ![Captura de tela da seção Certificado de Autenticação SAML da página Configurar Logon Único com SAML, com o link de download realçado para o certificado Base64.](common/certificatebase64.png)

1. Na seção **Configurar ekarda**, copie as URLs apropriadas com base em seus requisitos.

    ![Captura de tela da seção Configurar ekarda da página Configurar Logon Único com SAML, com os links de cópia de URL realçados.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você usará o portal do Azure para criar um usuário de teste chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, selecione **Azure Active Directory** > **Usuários** > **Todos os usuários**.

1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, insira `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e anote o valor que aparece na caixa **Senha**.
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao ekarda.

1. No portal do Azure, selecione **Aplicativos Empresariais** > **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **ekarda**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![Captura de tela da seção Gerenciar, com Usuários e grupos realçados.](common/users-groups-blade.png)

1. Selecione **Adicionar usuário** e, em seguida, selecione **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![Captura de tela da seção Usuários e grupos, com a função Adicionar usuário destacada.](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista de usuários. Em seguida, escolha **Selecionar** na parte inferior da tela.
1. Se você esperar qualquer valor de função na declaração SAML, selecione a função apropriada para o usuário na lista na caixa de diálogo **Selecionar Função**. Em seguida, escolha **Selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.

## <a name="configure-ekarda-sso"></a>Configurar o SSO do ekarda

1. Em outra janela do navegador da Web, entre em seu site de empresa do ekarda como administrador.
1. Selecione **Admin** > **Minha Conta**.

    ![Captura de tela da interface do usuário do site do ekarda com Minha Conta realçado no menu Admin.](./media/ekarda-tutorial/ekarda.png)

1. Na parte inferior da página, localize a seção **CONFIGURAÇÕES DE SAML**. É nesta seção em que você configura a integração SAML.
1. Na seção **CONFIGURAÇÕES DE SAML**, siga estas etapas:

    ![Captura de tela da página CONFIGURAÇÕES DE SAML com os campos de configuração de SAML realçados.](./media/ekarda-tutorial/ekarda1.png)

    1. Selecione o link **Metadados do Provedor de Serviços** e salve-o como arquivo em seu computador.
    1. Marque a caixa de seleção **Habilitar SAML**.
    1. Na caixa de texto **ID de Entidade do IDP**, cole o valor do **Identificador do Azure AD** copiado anteriormente do portal do Azure.
    1. Na caixa de texto **URL de Logon do IDP**, cole o valor da **URL de Logon** copiado anteriormente do portal do Azure.
    1. Na caixa de texto **URL de Logoff do IDP**, cole o valor da **URL de Logoff** copiado anteriormente do portal do Azure.
    1. Use o Bloco de Notas para abrir o arquivo **Certificado (Base64)** baixado do portal do Azure. Cole esse conteúdo na caixa de texto **Certificado x509 do IDP**.
    1. Marque a caixa de seleção **Habilitar o SLO** na seção **OPÇÕES**.
    1. Selecione **Atualização**.

### <a name="create-an-ekarda-test-user"></a>Criar um usuário de teste do ekarda

Nesta seção, um usuário chamado B.Fernandes será criado no ekarda. O ekarda é compatível com o provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há nenhuma ação para você executar nesta seção. Se um usuário chamado B.Fernandes ainda não existir no ekarda, um será criado após a autenticação.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD usando o portal Meus Aplicativos.

Ao selecionar o bloco do ekarda no portal Meus Aplicativos, você deverá ser conectado automaticamente ao site do ekarda para o qual você configurou o SSO. Para obter mais informações sobre o portal Meus Aplicativos, confira [Introdução ao portal Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais de integração de aplicativos SaaS ao Azure Active Directory](./tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [O que é o Acesso Condicional no Azure Active Directory?](../conditional-access/overview.md)
* [Experimente o ekarda com o Azure AD](https://aad.portal.azure.com/)
* Use a [solução de eCard empresarial do ekarda](https://ekarda.com/ecards-ecards-with-logo-for-business-corporate-enterprise) para provisionar qualquer número de pessoal para enviar eCards com o logotipo da sua empresa a clientes e colegas. Saiba mais sobre [como provisionar o ekarda como uma solução de SSO](https://support.ekarda.com/#SSO-Implementation).
* [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)
* [Como proteger o ekarda com visibilidade e controles avançados](/cloud-app-security/proxy-intro-aad)