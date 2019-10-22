---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Concur Travel and Expense | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Concur Travel and Expense.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6401ace4-71c0-4778-8b15-900f2f5f0c4c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9dddd9f6904aa5ef7840850792aeabf04666dddc
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72373435"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-concur-travel-and-expense"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Concur Travel and Expense

Neste tutorial, você aprenderá a integrar o Concur Travel and Expense ao Azure AD (Azure Active Directory). Com a integração do Concur Travel and Expense ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Concur Travel and Expense.
* Permitir que os usuários sejam conectados automaticamente ao Concur Travel and Expense usando suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do Concur Travel and Expense.
* Uma função "Administrador da empresa" em sua conta de usuário do Concur. Teste se você tem o acesso correto acessando a [Ferramenta de Autoatendimento de SSO do Concur](https://www.concursolutions.com/nui/authadmin/ssoadmin). Caso não tenha o acesso, contate o suporte do Concur ou o gerente de projetos de implementação. 

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD.

* O Concur Travel and Expense dá suporte ao SSO iniciado por **IdP** e **SP**
* O Concur Travel and Expense dá suporte ao SSO de teste no ambiente de produção e de implementação 

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo para cada uma das três regiões: EUA, EMEA e China. Portanto, só uma instância pode ser configurada para cada região em um locatário. 

## <a name="adding-concur-travel-and-expense-from-the-gallery"></a>Adicionando o Concur Travel and Expense da galeria

Para configurar a integração do Concur Travel and Expense ao Azure AD, você precisará adicionar o Concur Travel and Expense por meio da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Concur Travel and Expense** na caixa de pesquisa.
1. Selecione **Concur Travel and Expense** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-concur-travel-and-expense"></a>Configurar e testar o logon único do Azure AD para o Concur Travel and Expense

Configure e teste o SSO do Azure AD com o Concur Travel and Expense usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Concur Travel and Expense.

Para configurar e testar o SSO do Azure AD com o Concur Travel and Expense, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Concur Travel and Expense](#configure-concur-travel-and-expense-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Concur Travel and Expense](#create-concur-travel-and-expense-test-user)** – para ter um equivalente de B. Fernandes no Concur Travel and Expense que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Concur Travel and Expense**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica de SAML**, o aplicativo é pré-configurado no modo iniciado do **IDP** e as URLs necessárias já são preenchidas previamente com o Azure. É necessário que o usuário salve a configuração, clicando no botão **Salvar**.

    > [!NOTE]
    > O Identificador (ID da Entidade) e a URL de Resposta (URL do Serviço do Consumidor de Declaração) são específicos da região. Selecione uma opção com base no datacenter de sua entidade do Concur. Caso não saiba o datacenter de sua entidade do Concur, contate o suporte do Concur. 

5. Na página **Configurar o Logon Único com o SAML**, clique no ícone de edição/caneta de **Atributo de Usuário** para editar as configurações. O Identificador de Usuário Exclusivo precisa corresponder à login_id do usuário do Concur. Normalmente, é necessário alterar **user.userprincipalname** para **user.mail**.

    ![Editar Atributo de Usuário](common/edit-attribute.png)

6. Na página **Configurar logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar os metadados e salvá-los no computador.

    ![O link de download do Certificado](common/metadataxml.png)

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao Concur Travel and Expense.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Concur Travel and Expense**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-concur-travel-and-expense-sso"></a>Configurar o SSO do Concur Travel and Expense

1. Para configurar o logon único no lado do **Concur Travel and Expense**, é necessário fazer upload do **XML dos Metadados de Federação** baixado na [Ferramenta de Autoatendimento de SSO do Concur](https://www.concursolutions.com/nui/authadmin/ssoadmin) e fazer logon com uma conta com a função "Administrador da empresa". 

1. Clique em **Adicionar**.
1. Insira um nome personalizado para o IdP, por exemplo, "Azure AD (EUA)". 
1. Clique em **Carregar Arquivo XML** e anexe **XML de Metadados de Federação** baixado anteriormente.
1. Clique em **Adicionar Metadados** para salvar a alteração.

    ![Captura de tela da Ferramenta de Autoatendimento de SSO do Concur](./media/concur-travel-and-expense-tutorial/add-metadata-concur-self-service-tool.png)

### <a name="create-concur-travel-and-expense-test-user"></a>Criar usuário de teste do Concur Travel and Expense

Nesta seção, você criará um usuário chamado B.Fernandes no Concur Travel and Expense. Trabalhe com a equipe de suporte do Concur para adicionar os usuários à plataforma Concur Travel and Expense. Os usuários devem ser criados e ativados antes de usar o logon único. 

> [!NOTE]
> A ID de logon do Concur de B.Fernandes precisa corresponder ao identificador exclusivo de B.Fernandes no Azure AD. Por exemplo, se o identificador exclusivo do Azure AD de B.Fernandes for `B.Simon@contoso.com`. A ID de logon do Concur de B.Fernandes precisará ser `B.Simon@contoso.com` também. 

## <a name="configure-concur-mobile-sso"></a>Configurar o SSO móvel do Concur
Para habilitar o SSO móvel do Concur, você precisa fornecer à equipe de suporte do Concur a **URL de acesso do usuário**. Siga as etapas abaixo para obter a **URL de acesso do usuário** do Azure AD:
1. Acesse **Aplicativos empresariais**
1. Clique em **Concur Travel and Expense**
1. Clique em **Propriedades**
1. Copie a **URL de acesso do usuário** e forneça essa URL ao suporte do Concur

> [!NOTE]
> A opção Autoatendimento para configurar o SSO não está disponível, portanto, trabalhe com a equipe de suporte do Concur para habilitar o SSO móvel. 

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Concur Travel and Expense no Painel de Acesso, você deverá ser conectado automaticamente ao Concur Travel and Expense no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimentar o Concur Travel and Expense com o Azure AD](https://aad.portal.azure.com/)

