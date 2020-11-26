---
title: 'Tutorial: Integração do Azure Active Directory ao Dropbox Business | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Dropbox Business.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: jeedes
ms.openlocfilehash: bf86656ff670df19162867d597a869d762a012e8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019337"
---
# <a name="tutorial-integrate-dropbox-business-with-azure-active-directory"></a>Tutorial: Integrar o Dropbox Business ao Azure Active Directory

Neste tutorial, você aprenderá a integrar o Dropbox Business ao Azure AD (Azure Active Directory). Ao integrar o Dropbox Business ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Dropbox Business.
* Permitir que os usuários sejam conectados automaticamente ao Dropbox Business com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* Assinatura habilitada para logon único (SSO) do Dropbox Business.

> [!NOTE]
> Essa integração também está disponível para uso do ambiente de Nuvem do Governo dos EUA do Azure AD. Encontre esse aplicativo na Galeria de Aplicativos de Nuvem do Governo dos EUA do Azure AD e configure-o da mesma forma que na nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

* Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. O Dropbox Business dá suporte ao SSO iniciado por **SP**

* O Dropbox Business é compatível com [provisionamento e desprovisionamento de usuário automatizados](dropboxforbusiness-tutorial.md)
* Depois de configurar o Dropbox, você poderá impor o controle de sessão, que fornece proteção contra exportação e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

## <a name="adding-dropbox-business-from-the-gallery"></a>Como adicionar o Dropbox Business da galeria

Para configurar a integração do Dropbox Business ao Azure AD, é necessário adicionar o Dropbox Business à lista de aplicativos SaaS gerenciados por meio da galeria.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Dropbox Business** na caixa de pesquisa.
1. Escolha **Dropbox Business** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Configure e teste o SSO do Azure AD com o Dropbox Business usando um usuário de teste chamado **Brenda Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Dropbox Business.

Para configurar e testar o SSO do Azure AD com o Dropbox Business, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.    
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Dropbox Business](#configure-dropbox-business-sso)** – para definir as configurações de Logon Único no lado do aplicativo.
    1. **[Criar um usuário de teste do Dropbox Business](#create-dropbox-business-test-user)** – para ter um equivalente de Brenda Fernandes no Dropbox Business que esteja vinculado à declaração de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Dropbox Business**, localize a seção **Gerenciar** e escolha **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na página **Configuração Básica de SAML**, insira os valores nos seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://www.dropbox.com/sso/<id>`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite um valor: `Dropbox`

    > [!NOTE]
    > O valor da URL de entrada anterior não é um valor real. Você atualizará o valor com a URL de entrada real, que é explicada no tutorial posteriormente.

1. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Dropbox Business**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff


### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará a usuária de teste Brenda Fernandes no portal do Azure.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `Britta Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `BrittaSimon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao Dropbox Business.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Dropbox Business**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-dropbox-business-sso"></a>Configurar o SSO do Dropbox Business

1. Para automatizar a configuração no Dropbox Business, é necessário instalar a **extensão do navegador de Entrada Segura dos Meus Aplicativos** clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão ao navegador, clique em **Configurar o Dropbox Business** e será direcionado para o aplicativo Dropbox Business. Forneça as credenciais de administrador para entrar no Dropbox Business. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 8.

    ![Configuração da instalação](common/setup-sso.png)

3. Se você quiser configurar manualmente o Dropbox Business, abra uma nova janela do navegador da Web, acesse o locatário do Dropbox Business e entre no locatário do Dropbox Business. Execute as seguintes etapas:

    ![Captura de tela que mostra a página "Entrada do Dropbox Business".](./media/dropboxforbusiness-tutorial/ic769509.png "Configurar o logon único")

4. Clique na guia **ícone do usuário** e selecione **configurações**.

    ![Captura de tela que mostra a ação "ÍCONE DO USUÁRIO" e "Configurações" selecionado.](./media/dropboxforbusiness-tutorial/configure1.png "Configurar o logon único")

5. No painel de navegação à esquerda, clique em **console de administração**.

    ![Captura de tela que mostra "Console de administração" selecionado.](./media/dropboxforbusiness-tutorial/configure2.png "Configurar o logon único")

6. Sobre o **console de administração**, clique em **configurações** no painel de navegação à esquerda.

    ![Captura de tela que mostra "Configurações" selecionado.](./media/dropboxforbusiness-tutorial/configure3.png "Configurar o logon único")

7. Selecione a opção **Conexão única** na seção **Autenticação**.

    ![Captura de tela que mostra a seção "Autenticação" com "Logon único" selecionado.](./media/dropboxforbusiness-tutorial/configure4.png "Configurar o logon único")

8. Na seção **Conexão única**, execute as seguintes etapas:  

    ![Captura de tela que mostra as definições de configuração de "Logon único".](./media/dropboxforbusiness-tutorial/configure5.png "Configurar o logon único")

    a. Selecione **Obrigatório** como uma opção no menu suspenso para o **Logon único**.

    b. Clique em **Adicionar URL de credenciais** e, na caixa de texto **URL de credenciais do provedor de identidade**, cole o valor da **URL de Logon** copiado no portal do Azure e, em seguida, selecione **Concluído**.

    ![Configurar Logon Único](./media/dropboxforbusiness-tutorial/configure6.png "Configurar o logon único")

    c. Clique em **Carregar certificado** e, em seguida, navegue até o **arquivo de certificado codificado em Base64** que você baixou do portal do Azure.

    d. Clique em **Copiar link** e cole o valor copiado na caixa de texto **URL de logon** da seção **Dropbox Business Domain e URLs** no portal do Azure.

    e. Clique em **Save** (Salvar).

### <a name="create-dropbox-business-test-user"></a>Criar um usuário de teste do Dropbox Business

Nesta seção, será criado um usuário chamado B.Fernandes no Dropbox Business. O Dropbox Business dá suporte ao provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Dropbox Business, um será criado após a autenticação.

>[!Note]
>Se você precisar criar um usuário manualmente, contate a [equipe de suporte ao Cliente do Dropbox Business](https://www.dropbox.com/business/contact)

### <a name="test-sso"></a>Testar o SSO

Quando escolher o bloco do Dropbox Business no Painel de Acesso, você deverá ser conectado automaticamente ao Dropbox Business para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](/cloud-app-security/proxy-intro-aad)

- [Avaliar o Dropbox Business com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)