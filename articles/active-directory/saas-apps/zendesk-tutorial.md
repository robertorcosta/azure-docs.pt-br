---
title: 'Tutorial: Integração do Azure Active Directory ao Zendesk | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Zendesk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/13/2020
ms.author: jeedes
ms.openlocfilehash: c7d452803d15bab77df8e85a861de914a5ed08d5
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546046"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zendesk"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Zendesk

Neste tutorial, você aprenderá a integrar o Zendesk ao Azure AD (Azure Active Directory). Ao integrar o Zendesk ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Zendesk.
* Permitir que os usuários sejam conectados automaticamente ao Zendesk com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Zendesk.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Zendesk dá suporte ao SSO iniciado por **SP**
* O Zendesk dá suporte ao [provisionamento de usuário **Automatizado**](zendesk-provisioning-tutorial.md)
* Depois de configurar o Zendesk, você poderá impor um controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-zendesk-from-the-gallery"></a>Adicionar o Zendesk da galeria

Para configurar a integração do Zendesk ao Azure AD, você precisa adicionar o Zendesk por meio da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Zendesk** na caixa de pesquisa.
1. Escolha **Zendesk** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zendesk"></a>Configurar e testar o logon único do Azure AD para o Zendesk

Configure e teste o SSO do Azure AD com o Zendesk usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Zendesk.

Para configurar e testar o SSO do Azure AD com o Zendesk, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Zendesk](#configure-zendesk-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Zendesk](#create-zendesk-test-user)** : para ter um equivalente de B.Fernandes no Zendesk que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Zendesk**, localize a seção **Gerenciar** e escolha **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<subdomain>.zendesk.com`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<subdomain>.zendesk.com`

    c. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<subdomain>.zendesk.com/access/saml`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta, o Identificador e a URL de Logon reais. Entre em contato com a [equipe de atendimento ao cliente do Zendesk](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo Zendesk espera as declarações SAML em um formato específico. Não há nenhum atributo SAML obrigatório, mas, opcionalmente, você pode realizar o gerenciamento na seção **Atributos de Usuário** na página de integração de aplicativos. Na página **Definir Logon Único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Atributos do Usuário**.

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > Você pode usar atributos de extensão para adicionar atributos que não estão no Azure AD por padrão. Clique em [Atributos do usuário que podem ser definidos no SAML](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-) para obter a lista completa de atributos SAML que o **Zendesk** aceita.

1. Na seção **Certificado de Autenticação SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Certificado de Autenticação SAML**.

    ![Editar o Certificado de Autenticação SAML](common/edit-certificate.png)

1. Na seção **Certificado de Autenticação SAML**, copie o **Valor da Impressão Digital** e salve-o em seu computador.

    ![Copiar o valor da Impressão Digital](common/copy-thumbprint.png)

1. Na seção **Configurar o Zendesk**, copie as URLs apropriadas de acordo com as suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Zendesk.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Zendesk**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-zendesk-sso"></a>Configurar SSO do Zendesk

1. Para automatizar a configuração no **Zendesk**, é necessário instalar a **extensão do navegador Entrada Segura dos Meus Aplicativos** clicando em **Instalar a extensão**.

    ![image](./media/target-process-tutorial/install_extension.png)

1. Depois de adicionar a extensão ao navegador, clique em **Configurar o Zendesk** para ser direcionado ao aplicativo Zendesk. Em seguida, forneça as credenciais de administrador para entrar no Zendesk. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 6.

    ![Configuração da instalação](common/setup-sso.png)

1. Caso deseje configurar o Zendesk manualmente, abra uma nova janela do navegador da Web, entre no seu site da empresa do Zendesk como administrador e execute as seguintes etapas:

1. Clique em **Administrador**.

1. No painel de navegação à esquerda, clique em **Configurações** e em **Segurança**.

1. Na página **Segurança**, realize as seguintes etapas:

    ![Segurança](./media/zendesk-tutorial/ic773089.png "Segurança")

    ![Logon Único](./media/zendesk-tutorial/ic773090.png "Logon único")

    a. Clique na guia **Administrador e Agentes**.

    b. Selecione **SSO (logon único) e SAML** e, em seguida, selecione **SAML**.

    c. Na caixa de texto **URL de SSO do SAML**, cole o valor da **URL de Logon** copiado do portal do Azure.

    d. Na caixa de texto **URL de Logoff Remoto**, cole o valor da **URL de Logoff** copiado do portal do Azure.

    e. Na caixa de texto **Impressão Digital do Certificado**, cole o valor de **Impressão Digital** do certificado copiado do Portal do Azure.

    f. Clique em **Save** (Salvar).

### <a name="create-zendesk-test-user"></a>Criar um usuário de teste do Zendesk

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Zendesk. O Zendesk suporta o provisionamento automático de usuários, que é ativado por padrão. Você pode encontrar mais detalhes [ aqui ](Zendesk-provisioning-tutorial.md) sobre como configurar o provisionamento automático de usuários.

**Se você precisar criar um usuário manualmente, siga estas etapas:**

> [!NOTE]
> As contas de **Usuário final** são provisionadas automaticamente ao entrar. Contas de **Agente** e **Admin** precisam ser provisionadas manualmente no **Zendesk** antes de entrar.

1. Entre no seu locatário do **Zendesk**.

2. Selecione a guia **Lista de Clientes** .

3. Selecione a guia **Usuário** e clique em **Adicionar**.

    ![Adicionar usuário](./media/zendesk-tutorial/ic773632.png "Adicionar usuário")
4. Digite o **Nome** e **Email** de uma conta existente do Azure AD que você deseja provisionar e clique em **Salvar**.

    ![Novo usuário](./media/zendesk-tutorial/ic773633.png "Novo usuário")

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do Zendesk ou APIs fornecidas pelo Zendesk para provisionar as contas de usuário do Azure AD.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Zendesk no Painel de Acesso, você deverá ser conectado automaticamente ao Zendesk, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimentar o Zendesk com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger o Zendesk com visibilidade e controles avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Configurar Provisionamento de Usuário](zendesk-provisioning-tutorial.md)