---
title: 'Tutorial: integração do SSO (logon único) do Azure Active Directory ao NetSuite | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o NetSuite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/10/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad7065ba6378bcb383e67b4a58d7c195e88679ca
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75890677"
---
# <a name="tutorial-integrate-azure-ad-single-sign-on-sso-with-netsuite"></a>Tutorial: Integrar o SSO (logon único) do Azure AD ao NetSuite

Neste tutorial, você aprenderá a integrar o NetSuite ao Azure AD (Azure Active Directory). Ao integrar o NetSuite ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao NetSuite.
* Permitir que os usuários sejam conectados automaticamente ao NetSuite com as contas do Azure AD deles.
* gerenciar suas contas em um local central, o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, consulte [O que é o acesso de aplicativos e o logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisites

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura habilitada para SSO (logon único) do NetSuite.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. 

O NetSuite dá suporte ao:

* SSO iniciado por IdP.
* Provisionamento de Usuário just-in-time (JIT).
* [Provisionamento de Usuário automatizado](NetSuite-provisioning-tutorial.md).

> [!NOTE]
> Como o identificador desse aplicativo é um valor de cadeia de caracteres fixo, apenas uma instância pode ser configurada em um locatário.

## <a name="add-netsuite-from-the-gallery"></a>Adicionar o NetSuite por meio da galeria

Para configurar a integração do NetSuite ao Azure AD, adicione o NetSuite por meio da galeria à lista de aplicativos SaaS gerenciados fazendo o seguinte:

1. Entre no [portal do Azure](https://portal.azure.com) com uma conta corporativa ou de estudante ou uma conta pessoal Microsoft.
1. No painel esquerdo, selecione o serviço **Azure Active Directory**.
1. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, selecione **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **NetSuite** na caixa de pesquisa.
1. No painel de resultados, selecione **NetSuite** e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netsuite"></a>Configurar e testar o logon único do Azure AD para o NetSuite

Configure e teste o SSO do Azure AD com o NetSuite usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do NetSuite.

Para configurar e testar o SSO do Azure AD com o NetSuite, conclua os seguintes blocos de construção:

1. [Configurar o SSO do Azure AD](#configure-azure-ad-sso) – para permitir que os usuários usem esse recurso.
    * [Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user) para testar o logon único do Azure AD com o usuário B.Fernandes.  
    * [Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user) para permitir que o usuário B.Fernandes use o logon único do Azure AD.
1. [Configurar o SSO do NetSuite](#configure-netsuite-sso) para definir as configurações de logon único no lado do aplicativo.
    * [Criar um usuário de teste do NetSuite](#create-the-netsuite-test-user) para ter um equivalente do usuário B.Fernandes no NetSuite que esteja vinculado à representação do usuário do Azure AD.
1. [Teste o SSO](#test-sso) para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Para habilitar o SSO do Azure AD no portal do Azure, faça o seguinte:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **NetSuite**, procure a seção **Gerenciar** e, em seguida, selecione **Logon único**.
1. No painel **Selecionar um método de logon único**, selecione **SAML**.
1. No painel **Configurar o Logon Único com o SAML**, selecione o ícone **Editar** ("lápis") ao lado de **Configuração Básica do SAML**.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, na caixa de texto **URL de Resposta**, digite uma URL em um dos seguintes formatos:

    ||
    |-|
    | `https://<Account ID>.NetSuite.com/saml2/acs`|
    | `https://<Account ID>.na1.NetSuite.com/saml2/acs`|
    | `https://<Account ID>.na2.NetSuite.com/saml2/acs`|
    | `https://<Account ID>.sandbox.NetSuite.com/saml2/acs`|
    | `https://<Account ID>.na1.sandbox.NetSuite.com/saml2/acs`|
    | `https://<Account ID>.na2.sandbox.NetSuite.com/saml2/acs`|

    > [!NOTE]
    > Os valores nas URLs anteriores não são reais. Atualize-os com a URL de Resposta real. Para obter o valor, contate a [equipe de suporte ao cliente do NetSuite](http://www.netsuite.com/portal/services/support-services/suitesupport.shtml). Veja também os formatos mostrados na seção **Configuração Básica do SAML** no portal do Azure.

1. O aplicativo NetSuite espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo NetSuite espera que mais alguns atributos sejam passados novamente na resposta SAML, que são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.

    | Nome | Atributo de origem |
    | ---------------| --------------- |
    | account  | `account id` |

    > [!NOTE]
    > O valor do atributo da conta não é real. Você atualizará esse valor, conforme explicado mais adiante neste tutorial.

1. Na página Configurar o logon único com o SAML, na seção Certificado de Autenticação SAML, localize XML de Metadados de Federação e selecione Baixar para baixar o certificado e salvá-lo no computador.

    ![O link Download do certificado](common/metadataxml.png)

1. Na seção **Configurar o NetSuite**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, selecione **Azure Active Directory** > **Usuários** > **Todos os usuários**.

1. Selecione **Novo usuário** na parte superior da tela.

1. No painel de propriedades **Usuário**, siga estas etapas:

   a. Na caixa **Nome**, insira **B.Fernandes**.  
   b. Na caixa **Nome de usuário**, insira o username@companydomain.extension (por exemplo, B.Simon@contoso.com).  
   c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.  
   d. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que o usuário B.Fernandes use o logon único do Azure permitindo acesso ao NetSuite.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **NetSuite**.
1. No painel de visão geral, procure a seção **Gerenciar** e, em seguida, selecione o link **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Selecione **Adicionar usuário** e, em seguida, no painel **Adicionar Atribuição**, selecione **Usuários e grupos**.

    ![O botão "Adicionar usuário"](common/add-assign-user.png)

1. No painel **Usuários e grupos**, na lista suspensa **Usuários**, selecione **B.Fernandes** e, em seguida, escolha o botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando qualquer valor de função na declaração SAML, faça o seguinte:

   a. No painel **Selecionar Função**, na lista suspensa, selecione a função apropriada para o usuário.  
   b. Na parte inferior da tela, escolha o botão **Selecionar**.
1. No painel **Adicionar Atribuição**, selecione o botão **Atribuir**.

## <a name="configure-netsuite-sso"></a>Configurar o SSO do NetSuite

1. Abra uma nova guia no navegador e entre em seu site de empresa do NetSuite como administrador.

2. Na barra de navegação superior, selecione **Configuração** e, em seguida, **Empresa** > **Habilitar Recursos**.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-setupsaml.png)

3. Na barra de ferramentas no meio da página, selecione **SuiteCloud**.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-suitecloud.png)

4. Em **Gerenciar Autenticação**, marque a caixa de seleção **Logon Único do SAML** para habilitar a opção de logon único do SAML no NetSuite.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-ticksaml.png)

5. Na barra de navegação superior, selecione **Configuração**.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-setup.png)

6. Na lista **Tarefas de Configuração**, selecione **Integração**.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-integration.png)

7. Em **Gerenciar Autenticação**, selecione **Logon Único do SAML**.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-saml.png)

8. No painel **Configuração do SAML**, em **Configuração do NetSuite**, faça o seguinte:

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. Marque a caixa de seleção **Método de Autenticação Principal**.

    b. Em **Metadados do Provedor de Identidade SAMLV2**, selecione **Carregar Arquivo de Metadados do IdP** e, em seguida, selecione **Procurar** para carregar o arquivo de metadados que você baixou no portal do Azure.

    c. Selecione **Enviar**.

9. Na barra de navegação superior do NetSuite, selecione **Configuração** e, em seguida, selecione **Empresa** > **Dados da Empresa**.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-com.png)

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-account-id.png)

    b. No painel **Dados da Empresa**, na coluna à direita, copie o valor da **ID da Conta**.

    c. Cole a **ID da Conta** que você copiou da conta do NetSuite na caixa **Valor do Atributo** no Azure AD. 

10. Antes que os usuários possam realizar logon único no NetSuite, eles devem primeiro ter as permissões apropriadas no NetSuite. Para atribuir essas permissões, faça o seguinte:

    a. Na barra de navegação superior, selecione **Configuração**.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-setup.png)

    b. No painel esquerdo, selecione **Usuários/Funções** e, em seguida, **Gerenciar Funções**.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. Selecione **Nova Função**.

    d. Insira um **Nome** para a nova função.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-new-role.png)

    e. Clique em **Salvar**.

    f. Na barra de navegação superior, selecione **Permissões**. Em seguida, selecione **Configuração**.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-sso.png)

    g. Selecione **Logon Único do SAML** e, em seguida, **Adicionar**.

    h. Clique em **Salvar**.

    i. Na barra de navegação superior, selecione **Configuração** e, em seguida, **Gerenciador de Configuração**.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-setup.png)

    j. No painel esquerdo, selecione **Usuários/Funções** e, em seguida, **Gerenciar Usuários**.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Selecione um usuário de teste, selecione **Editar** e, em seguida, a guia **Acesso**.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-edit-user.png)

    l. No painel **Funções**, atribua a função apropriada que você criou.

    ![Configurar o logon único](./media/NetSuite-tutorial/ns-add-role.png)

    m. Clique em **Salvar**.

### <a name="create-the-netsuite-test-user"></a>Criar um usuário de teste do NetSuite

Nesta seção, um usuário chamado B.Fernandes será criado no NetSuite. O NetSuite dá suporte ao provisionamento Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no NetSuite, um novo será criado após a autenticação.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao selecionar o bloco do NetSuite no Painel de Acesso, você deverá ser conectado automaticamente ao NetSuite, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Experimente o NetSuite com o Azure AD](https://aad.portal.azure.com/)
