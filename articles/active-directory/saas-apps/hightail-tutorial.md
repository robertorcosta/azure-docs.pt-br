---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Hightail | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Hightail.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2019
ms.author: jeedes
ms.openlocfilehash: d39f46469d80b3e2d7bf361bd349a39afb16bd2d
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91826184"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hightail"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Hightail

Neste tutorial, você aprenderá a integrar o Hightail ao Azure AD (Azure Active Directory). Ao integrar o Hightail ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Hightail.
* Permitir que os usuários sejam conectados automaticamente ao Hightail com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Hightail.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Hightail dá suporte ao SSO iniciado por **SP e IDP**
* O Hightail dá suporte ao provisionamento de usuário **Just-In-Time**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="adding-hightail-from-the-gallery"></a>Adição do Hightail a partir da galeria

Para configurar a integração do Hightail ao Azure AD, você precisará adicionar o Hightail da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Hightail** na caixa de pesquisa.
1. Selecione **Hightail** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-hightail"></a>Configurar e testar o logon único do Azure AD para o Hightail

Configure e teste o SSO do Azure AD com o Hightail usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Hightail.

Para configurar e testar o SSO do Azure AD com o Hightail, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Hightail](#configure-hightail-sso)** – para definir as configurações de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do Hightail](#create-hightail-test-user)** – para ter um equivalente de B.Fernandes no Hightail que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Hightail**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    Na caixa de texto **URL de Resposta**, digite uma URL: `https://www.hightail.com/samlLogin?phi_action=app/samlLogin&subAction=handleSamlResponse`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de Logon**, digite uma URL: `https://www.hightail.com/loginSSO`

1. O aplicativo Hightail espera as instruções SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizado à configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo Hightail espera que mais alguns atributos sejam passados novamente na resposta SAML, que são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.

    | Nome | Atributo de Origem|
    | -------- |-------- |
    | Nome | user.givenname |
    | LastName | user.surname |
    | Email | user.mail |
    | UserIdentity | user.mail |

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Hightail**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

> [!NOTE]
> Antes de configurar o logon único no aplicativo Hightail, adicione seu domínio de email à lista de permissões com a equipe do Hightail para que todos os usuários que usam esse domínio possam utilizar a funcionalidade de Logon Único.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Hightail.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Hightail**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-hightail-sso"></a>Configurar o SSO do Hightail

1. Para automatizar a configuração no Hightail, é necessário instalar a **extensão do navegador Entrada Segura dos Meus Aplicativos** clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

1. Após a adição da extensão ao navegador, um clique em **Configurar o Hightail** direcionará você ao aplicativo Hightail. Nele, forneça as credenciais de administrador para entrar no Hightail. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 6.

    ![Configuração da instalação](common/setup-sso.png)

1. Caso deseje configurar o Hightail manualmente, em outra janela do navegador, abra o portal de administração do **Hightail**.

1. Clique no **ícone de Usuário** no canto superior direito da página. 

    ![Captura de tela que mostra o ícone do usuário.](./media/hightail-tutorial/configure1.png)

1. Clique na guia **Exibir Console de Administrador**.

    ![Captura de tela que mostra o botão Exibir Console do Administrador para o Usuário.](./media/hightail-tutorial/configure2.png)

1. No menu superior, clique na guia **SAML** e execute estas etapas:

    ![Captura de tela mostra a guia SAML, na qual você pode inserir o logon URL e o certificado SAML.](./media/hightail-tutorial/configure3.png)

    a. Na caixa de texto **URL de Logon**, cole o valor da **URL de Logon** copiado do portal do Azure.

    b. Abra o Certificado codificado em base-64 no bloco de notas baixado do portal do Azure, copie o conteúdo dele para a área de transferência e, depois, cole-o na caixa de texto **Certificado SAML**.

    c. Clique em **COPIAR** para copiar a URL do consumidor SAML para sua instância e cole-a na caixa de texto **URL de Resposta** na seção **Configuração Básica de SAML** no portal do Azure.

    d. Clique em **Salvar Configurações**.

### <a name="create-hightail-test-user"></a>Criar um usuário de teste do Hightail

Nesta seção, um usuário chamado Brenda Fernandes será criado no Hightail. O Hightail dá suporte ao provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Hightail, um novo será criado após a autenticação.

> [!NOTE]
> Se precisar criar um usuário manualmente, você precisará entrar em contato com a [equipe de suporte do Hightail](mailto:support@hightail.com).

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Hightail no Painel de Acesso, você deverá ser conectado automaticamente ao Hightail, para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimentar o Hightail com o Azure AD](https://aad.portal.azure.com/)