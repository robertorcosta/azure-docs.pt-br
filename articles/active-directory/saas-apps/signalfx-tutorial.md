---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao SignalFx | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SignalFx.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6d5ab4b0-29bc-4b20-8536-d64db7530f32
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea81f0046d7f73d845ed49325a3d621e6b7735e7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443277"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-signalfx"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory com o SignalFx

Neste tutorial, você aprenderá a integrar o SignalFx ao Azure AD (Active Directory). Ao integrar o SignalFx no Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao SignalFx.
* Permitir que os usuários sejam conectados automaticamente ao SignalFx com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisites

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do SignalFx.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O SignalFx dá suporte ao SSO iniciado por **IdP**
* O SignalFx dá suporte ao provisionamento de usuário **just-in-time**

## <a name="adding-signalfx-from-the-gallery"></a>Adicionar SignalFx da galeria

Para configurar a integração do SignalFx ao Azure Active Directory, será necessário adicionar o SignalFx da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **SignalFx** na caixa de pesquisa.
1. Selecione **SignalFx** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-signalfx"></a>Configurar e testar o logon único do Azure AD para o SignalFx

Configure e teste o SSO do Azure AD com o SignalFx usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SignalFx.

Para configurar e testar o SSO do Azure AD com o SignalFx, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do SignalFx](#configure-signalfx-sso)** – para definir as configurações de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do SignalFx](#create-signalfx-test-user)** – para ter um equivalente de B.Fernandes no SignalFx que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **SignalFx**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na página **Configurar logon único com SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **Identificador**, digite uma URL: `https://api.signalfx.com/v1/saml/metadata`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://api.signalfx.com/v1/saml/acs/<integration ID>`

    > [!NOTE]
    > O valor anterior não é um valor real. Você atualiza o valor com a URL de Resposta real, que é explicada no tutorial posteriormente.

1. Seu aplicativo SignalFx espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo SignalFx espera que mais alguns atributos sejam passados novamente na resposta SAML, os quais são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.

    | Nome |  Atributo de Origem|
    | ------------------- | -------------------- |
    | User.FirstName  | user.givenname |
    | User.email  | user.mail |
    | PersonImmutableID       | user.userprincipalname    |
    | User.LastName       | user.surname    |

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o SignalFx**, copie a(s) URL(s) apropriada(s) de acordo com as suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao SignalFx.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **SignalFx**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-signalfx-sso"></a>Configurar o SSO do SignalFx

1. Entre em seu site de empresa do SignalFx como administrador.

1. Em SignalFx, na parte superior, clique em **Integrações** para abrir a página Integrações.

    ![Integração do SignalFx](./media/signalfx-tutorial/tutorial_signalfx_intg.png)

1. Clique no bloco **Azure Active Directory** na seção **Serviços de Logon**.

    ![SignalFx saml](./media/signalfx-tutorial/tutorial_signalfx_saml.png)

1. Clique em **NOVA INTEGRAÇÃO** e na guia **INSTALAR** execute as seguintes etapas:

    ![SignalFx samlintgpage](./media/signalfx-tutorial/tutorial_signalfx_azure.png)

    a. Na caixa de texto **Nome**, digite um novo nome de integração, como **OurOrgName SAML SSO**.

    b. Copie o valor da **ID de Integração** e acrescente-o à **URL de Resposta** no lugar de `<integration ID>` na caixa de texto **URL de Resposta** da seção **Configuração Básica do SAML** no portal do Azure.

    c. Clique em **Carregar Arquivo** para carregar o **Certificado codificado na Base64** baixado do Portal do Azure na caixa de texto **Certificado**.

    d. Na caixa de texto **URL do Emissor**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    e. Na caixa de texto **URL de Metadados**, cole a **URL de Logon** copiado do portal do Azure.

    f. Clique em **Save** (Salvar).

### <a name="create-signalfx-test-user"></a>Criar um usuário de teste do SignalFx

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no SignalFx. O SignalFx dá suporte ao provisionamento just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Um novo usuário é criado durante uma tentativa de acessar o SignalFx, caso ele ainda não exista.

Quando um usuário iniciar o SignalFx do SSO de SAML pela primeira vez, a [Equipe de suporte do SignalFx](mailto:kmazzola@signalfx.com) enviará um email contendo um link que o usuário deverá clicar para autenticar. Isso acontecerá somente na primeira vez que o usuário entrar. As tentativas subsequentes de logon não exigirão validação de email.

> [!Note]
> Se for necessário criar um usuário manualmente, contate a [Equipe de suporte do SignalFx](mailto:kmazzola@signalfx.com)

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do SignalFx no Painel de Acesso, você deverá ser conectado automaticamente ao SignalFx, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o SignalFx com o Azure AD](https://aad.portal.azure.com/)