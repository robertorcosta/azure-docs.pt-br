---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory com o Meraki Dashboard | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Meraki Dashboard.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/20/2020
ms.author: jeedes
ms.openlocfilehash: 84db28348baebc4f6b62f9cacb0035b4df1f6145
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89660772"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-meraki-dashboard"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory com o Meraki Dashboard

Neste tutorial, você aprenderá a integrar o Meraki Dashboard ao Azure AD (Azure Active Directory). Ao integrar o Meraki Dashboard ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Meraki Dashboard.
* Permitir que os usuários sejam conectados automaticamente ao Meraki Dashboard com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Meraki Dashboard.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Meraki Dashboard é compatível com SSO iniciado pelo **IDP**
* Após configurar o Meraki Dashboard, você poderá impor controle de sessão, que protege contra exfiltração e infiltração de dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="adding-meraki-dashboard-from-the-gallery"></a>Como adicionar o Meraki Dashboard da galeria

Para configurar a integração do Meraki Dashboard ao Azure AD, você precisará adicionar o Meraki Dashboard por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Meraki Dashboard** na caixa de pesquisa.
1. Selecione **Meraki Dashboard** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-meraki-dashboard"></a>Configurar e testar o SSO do Azure AD para o Meraki Dashboard

Configure e teste o SSO do Azure AD com o Meraki Dashboard usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Meraki Dashboard.

Para configurar e testar o SSO do Azure AD com o Meraki Dashboard, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Meraki Dashboard](#configure-meraki-dashboard-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Meraki Dashboard](#create-meraki-dashboard-test-user)** – para ter um equivalente de B.Fernandes no Meraki Dashboard que esteja vinculado à declaração de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Meraki Dashboard**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração básica de SAML**, realize as seguintes etapas:
     
    Na caixa de texto **URL de Resposta**, digite uma URL no seguinte padrão: `https://n27.meraki.com/saml/login/m9ZEgb/< UNIQUE ID >`

    > [!NOTE]
    > O valor de URL de Resposta não é real. Atualize o valor com o valor da URL de Resposta real, que é explicada no tutorial posteriormente.

1. Clique no botão **Salvar** .

1. Seu aplicativo Meraki Dashboard espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo Meraki Dashboard espera que mais alguns atributos sejam passados novamente na resposta SAML, que são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.
    
    | Nome | Atributo de Origem|
    | ---------------| --------- |
    | `https://dashboard.meraki.com/saml/attributes/username` | user.userprincipalname |
    | `https://dashboard.meraki.com/saml/attributes/role` | user.assignedroles |

    > [!NOTE]
    > Para entender como configurar funções no Azure AD, confira [aqui](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management).

1. Na seção **Certificado de Autenticação SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Certificado de Autenticação SAML**.

    ![Editar o Certificado de Autenticação SAML](common/edit-certificate.png)

1. Na seção **Certificado de Autenticação SAML**, copie o **Valor da Impressão Digital** e salve-o em seu computador.

    ![Copiar o valor da Impressão Digital](common/copy-thumbprint.png)

1. Na seção **Configurar o Meraki Dashboard**, copie o valor da URL de logout e salve-o em seu computador.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Meraki Dashboard.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Meraki Dashboard**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-meraki-dashboard-sso"></a>Configurar o SSO do Meraki Dashboard

1. Em outra janela do navegador da Web, entre no site do Meraki Dashboard como administrador.

1. Navegue até **Organização** -> **Configurações**.

    ![Guia Configurações do Meraki Dashboard](./media/meraki-dashboard-tutorial/configure1.png)

1. Em Autenticação, altere **SSO do SAML** para **Habilitados para SSO do SAML**.

    ![Autenticação do Meraki Dashboard](./media/meraki-dashboard-tutorial/configure2.png)

1. Clique em **Adicionar um IdP do SAML**.

    ![Adicionar um IdP do SAML no Meraki Dashboard](./media/meraki-dashboard-tutorial/configure3.png)

1. Cole o valor da **Impressão digital** copiado do portal do Azure na caixa de texto **Impressão digital de certificado SHA1 X.590**. Em seguida, clique em **Salvar**. Depois de salvar, a URL do consumidor será exibida. Copie o valor da URL do Consumidor e cole-o na caixa de texto **URL de Resposta** da **Seção de Configuração Básica do SAML** no portal do Azure.

    ![Configuração do Meraki Dashboard](./media/meraki-dashboard-tutorial/configure4.png)

### <a name="create-meraki-dashboard-test-user"></a>Criar usuário de teste do Meraki Dashboard

1. Em outra janela do navegador da Web, entre no site do Meraki Dashboard como administrador.

1. Navegue até **Organização** -> **Administradores**.

    ![Administradores do Meraki Dashboard](./media/meraki-dashboard-tutorial/user1.png)

1. Na seção de funções de administrador do SAML, clique no botão **Adicionar função SAML**.

    ![Botão Adicionar função SAML do Meraki Dashboard](./media/meraki-dashboard-tutorial/user2.png)

1. Insira a função **meraki_full_admin**, marque **Acesso da organização** como **Total** e clique em **Criar função**. Repita o processo para **meraki_readonly_admin**. Desta vez, marque **Acesso da organização** como caixa **Somente leitura**.
 
    ![Criar usuário no Meraki Dashboard](./media/meraki-dashboard-tutorial/user3.png)

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Meraki Dashboard no Painel de Acesso, você deverá ser conectado automaticamente ao Meraki Dashboard, para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Avaliar o Meraki Dashboard com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
