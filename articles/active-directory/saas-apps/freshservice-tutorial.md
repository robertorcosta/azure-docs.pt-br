---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Freshservice | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Freshservice.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3dd22b1f-445d-45c6-8eda-30207eb9a1a8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e08ef72dca09f873ad1cfcc91e132063b88406b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227528"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshservice"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Freshservice

Neste tutorial, você aprenderá a integrar o Freshservice ao Azure AD (Azure Active Directory). Quando integrar o Freshservice ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Freshservice.
* Permitir que os usuários sejam conectados automaticamente ao Freshservice com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para logon único (SSO) do Freshservice.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Freshservice dá suporte ao SSO iniciado por **SP**

## <a name="adding-freshservice-from-the-gallery"></a>Adicionando Freshservice da galeria

Para configurar a integração do Freshservice ao Azure AD, você precisa adicionar o Freshservice da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Freshservice** na caixa de pesquisa.
1. Escolha **Freshservice** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-freshservice"></a>Configurar e testar o logon único do Azure AD para o Freshservice

Configure e teste o SSO do Azure AD com o Freshservice usando um usuário de teste chamado **B. Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Freshservice.

Para configurar e testar o SSO do Azure AD com o Freshservice, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Freshservice](#configure-freshservice-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Freshservice](#create-freshservice-test-user)** – para ter um equivalente de B. Fernandes no Freshservice que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Freshservice**, localize a seção **Gerenciar** e escolha **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<democompany>.freshservice.com`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<democompany>.freshservice.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Entre em contato com [equipe de suporte ao Cliente do Freshservice](https://support.freshservice.com/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o Logon Único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. O Freshservice requer uma impressão digital SHA-256 para que o SSO funcione. Para obter a impressão digital SHA-256, execute as seguintes etapas:

    ![FingerPrint](./media/freshservice-tutorial/ic790821.png "Impressão digital")

    1. Abra o [link](https://www.samltool.com/fingerprint.php) em um navegador da Web diferente.

    1. Abra o arquivo do certificado baixado (Base64) no Bloco de Notas e copie o conteúdo na caixa de texto **Certificado x.509**.

    1. Para o Algoritmo, selecione **SHA256** na lista suspensa.

    1. Clique em **CALCULAR IMPRESSÃO DIGITAL**.

    1. Clique no ícone de cópia para copiar a **Impressão digital** gerada e salve-a em seu computador.

1. Na seção **Configurar o Freshservice** no **portal do Azure**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B. Fernandes use o logon único do Azure concedendo a ela o acesso ao Freshservice.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Freshservice**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-freshservice-sso"></a>Configurar o SSO do Freshservice

1. Para automatizar a configuração no Freshservice, é necessário instalar a **Extensão do navegador de Entrada Segura dos Meus Aplicativos**, clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Após adicionar a extensão ao navegador, clicar em **Configurar o Freshservice** direcionará você ao aplicativo Freshservice. Nele, forneça as credenciais de administrador para entrar no Freshservice. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 6.

    ![Configuração da instalação](common/setup-sso.png)

3. Se desejar configurar o Freshservice manualmente, abra uma nova janela do navegador da Web, entre no site da empresa Freshservice como administrador e execute as seguintes etapas:

4. No menu na parte superior, clique em **Administrador**.

    ![Administrador](./media/freshservice-tutorial/ic790814.png "Administrador")

5. No **Portal do Cliente**, clique em **Segurança**.

    ![Segurança](./media/freshservice-tutorial/ic790815.png "Segurança")

6. Na seção **Segurança** , realize as seguintes etapas:

    ![Logon único](./media/freshservice-tutorial/ic790816.png "Logon Único")

    a. Ative o **Logon Único**.

    b. Selecione **SSO do SAML**.

    c. Na caixa de texto **URL de Logon do SAML**, cole o valor da **URL de Logon** copiado do portal do Azure.

    d. Na caixa de texto **URL de Logoff**, cole o valor da **URL de Logoff** copiado do portal do Azure.

    e. Na caixa de texto **Impressão digital de certificado de segurança**, cole o valor da **Impressão digital** que você gerou anteriormente.

    f. Clique em **Salvar**

### <a name="create-freshservice-test-user"></a>Criar um usuário de teste do Freshservice

Para permitir que os usuários do Azure AD façam logon no FreshService, eles devem ser provisionados no Freshservice. No caso do FreshService, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon no site da empresa **FreshService** como administrador.

2. No menu na parte superior, clique em **Administrador**.

    ![Administrador](./media/freshservice-tutorial/ic790814.png "Administrador")

3. Na seção **Gerenciamento de Usuários**, clique em **Solicitantes**.

    ![Solicitantes](./media/freshservice-tutorial/ic790818.png "Solicitantes")

4. Clique em **Novo Solicitante**.

    ![Novos Solicitantes](./media/freshservice-tutorial/ic790819.png "Novos Solicitantes")

5. Na seção **Novo Solicitante** , realize as seguintes etapas:

    ![Novo Solicitante](./media/freshservice-tutorial/ic790820.png "Novo Solicitante")  

    a. Insira os atributos **Nome** e **Email** de uma conta válida do Azure Active Directory que você deseja provisionar nas caixas de texto relacionadas.

    b. Clique em **Save** (Salvar).

    > [!NOTE]
    > O titular da conta do Azure Active Directory recebe um email que inclui um link para confirmar a conta antes que ela se torne ativa
    >  

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do FreshService ou APIs fornecidas pelo FreshService para provisionar as contas de usuário do Azure AD.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Freshservice no Painel de Acesso, você deverá ser conectado automaticamente ao Freshservice, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimentar o Freshservice com o Azure AD](https://aad.portal.azure.com/)