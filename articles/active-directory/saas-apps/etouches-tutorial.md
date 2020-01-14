---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao etouches | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o etouches.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 76cccaa8-859c-4c16-9d1d-8a6496fc7520
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0774873370d6006e0a1108c4b01c60274d69f0d
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75561177"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-etouches"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao etouches

Neste tutorial, você aprenderá a integrar o etouches ao Azure AD (Azure Active Directory). Quando integrar o etouches ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao etouches.
* Permitir que os usuários sejam conectados automaticamente ao etouches com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisites

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do etouches.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O etouches dá suporte ao SSO iniciado por **SP**





## <a name="adding-etouches-from-the-gallery"></a>Adicionar o etouches da galeria

Para configurar a integração do etouches ao Azure AD, você precisará adicionar o etouches da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **etouches** na caixa de pesquisa.
1. Selecione **etouches** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-etouches"></a>Configurar e testar o logon único do Azure AD para o etouches

Configure e teste o SSO do Azure AD com o etouches usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do etouches.

Para configurar e testar o SSO do Azure AD com o etouches, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do etouches](#configure-etouches-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do etouches](#create-etouches-test-user)** – para ter um equivalente de B.Fernandes no etouches vinculado à declaração de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **etouches**, localize a seção **Gerenciar** e escolha **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://www.eiseverywhere.com/saml/accounts/?sso&accountid=<ACCOUNTID>`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://www.eiseverywhere.com/<instance name>`

    > [!NOTE] 
    > Esses valores não são reais. Você atualiza o valor com a URL de logon real e o identificador, o que é explicado no tutorial posteriormente.

1. O aplicativo etouches espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/edit-attribute.png)

1. Além do indicado acima, o aplicativo etouches espera que mais alguns atributos sejam passados novamente na resposta SAML, que são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seu requisito.

    | Nome | Atributo de Origem|
    | ------------------- | -------------------- |
    | Email | user.mail | 

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o etouches**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao etouches.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **etouches**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-etouches-sso"></a>Configurar o SSO do etouches

1. Para que o SSO seja configurado para o aplicativo, execute as seguintes etapas no aplicativo etouches: 

    ![configuração do etouches](./media/etouches-tutorial/tutorial_etouches_06.png) 

    a. Entre no aplicativo **etouches** usando os direitos de Administrador.
   
    b. Vá para a configuração de **SAML**.

    c. Na seção **Configurações Gerais**, abra seu certificado baixado do Portal do Azure no bloco de notas, copie o conteúdo e, em seguida, cole-o na caixa de texto de metadados IdP. 

    d. Clique no botão **Save & Stay**.
  
    e. Clique no botão **Update Metadata** na seção SAML Metadata. 

    f. Isso abre a página e faz o SSO. Depois que o SSO estiver funcionando, você poderá configurar o nome de usuário.

    g. No campo Nome de usuário, selecione o **endereço de email** conforme mostrado na imagem abaixo. 

    h. Copie o valor da **ID da entidade SP** e cole-o na caixa de texto **Identificador**, que está na seção **Configuração SAML Básica** no portal do Azure.

    i. Copie o valor da **ACS/URL do SSO** e cole-o na caixa de texto **URL de Entrada**, que está na seção **Configuração SAML Básica** no portal do Azure.

### <a name="create-etouches-test-user"></a>Criar um usuário de teste no etouches

Nesta seção, você criará um usuário chamado Brenda Fernandes no etouches. Trabalhe com a [equipe de suporte ao cliente do etouches](https://www.etouches.com/event-software/support/customer-support/) para adicionar usuários na plataforma do etouches.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do etouches no Painel de Acesso, você deve ser conectado automaticamente ao etouches para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimentar o etouches com o Azure AD](https://aad.portal.azure.com/)

