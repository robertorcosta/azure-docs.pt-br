---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Veracode | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Veracode.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/10/2019
ms.author: jeedes
ms.openlocfilehash: a8c7c70e7956868bf069704eb5fe34db014dd135
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88532459"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-veracode"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Veracode

Neste tutorial, você aprenderá a integrar o Veracode ao Azure AD (Azure Active Directory). Ao integrar o Veracode ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Veracode.
* Permitir que os usuários sejam conectados automaticamente ao Veracode com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos de SaaS (software como serviço) ao Azure AD, confira [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do Veracode habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. O Veracode dá suporte ao SSO iniciado pelo provedor de identidade e ao provisionamento do usuário Just-in-Time.

## <a name="add-veracode-from-the-gallery"></a>Adicionar o Veracode da galeria

Para configurar a integração do Veracode ao Azure AD, adicione o Veracode da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante, ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, selecione **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite "Veracode" na caixa de pesquisa.
1. Selecione **Veracode** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-veracode"></a>Configurar e testar o logon único do Azure AD para o Veracode

Configure e teste o SSO do Azure AD com o Veracode usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer um vínculo entre um usuário do Azure AD e o usuário relacionado do Veracode.

Para configurar e testar o SSO do Azure AD com o Veracode, conclua os seguintes blocos de construção:

1. **[Configure o SSO do Azure AD](#configure-azure-ad-sso)** para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B. Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para permitir que B. Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Veracode](#configure-veracode-sso)** – para definir as configurações de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do Veracode](#create-veracode-test-user)** – para ter um equivalente de B.Fernandes no Veracode que esteja vinculado à representação de usuário do Azure AD.
1. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Veracode**, localize a seção **Gerenciar**. Selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, selecione o ícone de lápis da **Configuração Básica de SAML** para editar as configurações.

   ![Captura de tela da página Configurar o logon único com o SAML, com o ícone de lápis realçado](common/edit-urls.png)

1. Na seção **Configuração Básica de SAML**, o aplicativo é pré-configurado e as URLs necessárias já são preenchidas previamente com o Azure. Clique em **Salvar**.

1. Na página **Configurar logon único com o SAML**, na seção **Certificado de Autenticação do SAML**, localize **Certificado (Base64)** . Selecione **Baixar** para baixar o certificado e salvá-lo em seu computador.

    ![Captura de tela da seção Certificado de Autenticação SAML, com o link Download realçado](common/certificatebase64.png)

1. O Veracode espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![Captura de tela da seção Atributos do Usuário e Declarações](common/default-attributes.png)

1. O Veracode também espera que mais alguns atributos sejam passados de volta na resposta SAML. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.

    | Nome | Atributo de origem|
    | ---------------| --------------- |
    | nome |User.givenname |
    | sobrenome |User.surname |
    | email |User.mail |

1. Na seção **Configurar o Veracode**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Captura de tela da seção Configurar Veracode, com URLs de configuração realçadas](common/copy-configuration-urls.png)

## <a name="configure-veracode-sso"></a>Configurar o SSO do Veracode

1. Em uma janela diferente do navegador da Web, entre no site da sua empresa do Veracode como administrador.

1. No menu na parte superior, selecione **Configurações** > **Administrador**.
   
    ![Captura de tela da Administração do Veracode, com o ícone de Configurações e o Administrador realçados](./media/veracode-tutorial/ic802911.png "Administração")

1. Selecione a guia **SAML**.

1. Na seção **Configurações do SAML da Organização** , realize as seguintes etapas:

    ![Captura de tela da seção Configurações de SAML da Organização](./media/veracode-tutorial/ic802912.png "Administração")

    a.  Para o **Emissor**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    b. Para **Certificado de Autenticação de Declaração**, selecione **Escolher Arquivo** para carregar o certificado baixado do portal do Azure.

    c. Para **Autoregistro**, selecione **Habilitar Autoregistro**.

1. Na seção **Configurações de Autorregistro**, realize as seguintes etapas e selecione **Salvar**:

    ![Captura de tela da seção de Configurações de Autorregistro, com várias opções realçadas](./media/veracode-tutorial/ic802913.png "Administração")

    a. Para **Ativação de Novo Usuário**, selecione **Sem Ativação Necessária**.

    b. Para **Atualizações de Dados do Usuário**, selecione **Dados do Usuário de Preferência do Veracode**.

    c. Para **Detalhes de Atributos do SAML**, selecione o seguinte:
      * **Funções de usuário**
      * **Administrador de políticas**
      * **Revisor**
      * **Orientações de Segurança**
      * **Executivo**
      * **Emissor**
      * **Criador**
      * **Todos os Tipos de Verificação**
      * **Associações de Equipe**
      * **Equipe Padrão**

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, selecione **Azure Active Directory** >**Usuários** > **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:

   1. Para **Nome**, insira `B.Simon`.  
   1. Para **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Selecione **Mostrar senha** e anote o valor da senha mostrada.
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Veracode.

1. No portal do Azure, selecione **Aplicativos Empresariais** > **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Veracode**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e selecione **Usuários e grupos**.

   ![Captura de tela da seção Gerenciar, com Usuários e grupos realçados](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**. Na caixa de diálogo **Adicionar Atribuição**, selecione **Usuários e grupos**.

    ![Captura de tela da página Usuários e grupos, com a função Adicionar usuário destacada](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, em **Usuários**, selecione **B.Fernandes**. Em seguida, escolha **Selecionar** na parte inferior da tela.
1. Se você esperar um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, selecione a função apropriada para o usuário na lista. Em seguida, escolha **Selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.

### <a name="create-veracode-test-user"></a>Criar usuário de teste do Veracode

Para entrar no Veracode, os usuários do Azure AD precisam ser provisionados no Veracode. Essa tarefa é automatizada e você não precisa fazer nada manualmente. Os usuários são criados automaticamente, se necessário, durante a primeira tentativa de logon único.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do Veracode ou APIs fornecidas pelo Veracode para provisionar as contas de usuário do Azure AD.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao selecionar o **Veracode** no Painel de Acesso, você deverá ser conectado automaticamente ao Veracode no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS ao Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimentar o Veracode com o Azure AD](https://aad.portal.azure.com/)