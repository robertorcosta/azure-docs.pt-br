---
title: 'Tutorial: integração do Azure Active Directory ao FreshDesk | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o FreshDesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b7188708c44d00d075a02d54b0a3b10cbc81954
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2020
ms.locfileid: "87513499"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Tutorial: integração do Azure Active Directory ao FreshDesk

Neste tutorial, você aprenderá a integrar o FreshDesk ao Azure AD (Azure Active Directory).
A integração do FreshDesk ao Azure AD oferece os seguintes benefícios:

* No Microsoft Azure AD, é possível controlar quem tem acesso ao FreshDesk.
* Você pode permitir que os usuários sejam conectados automaticamente ao FreshDesk (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Prerequisites

Para configurar a integração do Azure AD ao FreshDesk, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do FreshDesk

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O FreshDesk dá suporte ao SSO iniciado por **SP**
* Após configurar o FreshDesk, você poderá impor o controle de sessão, que protege contra a exportação e infiltração de dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-freshdesk-from-the-gallery"></a>Adicionar o FreshDesk da galeria

Para configurar a integração do FreshDesk ao Azure AD, você precisa adicionar o FreshDesk da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **FreshDesk** na caixa de pesquisa.
1. Escolha **FreshDesk** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-freshdesk"></a>Configurar e testar logon único do Azure AD para o FreshDesk

Configure e teste o SSO do Azure AD com o FreshDesk usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do FreshDesk.

Para configurar e testar o SSO do Azure AD com o FreshDesk, conclua os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Configurar o Logon Único do FreshDesk](#configure-freshdesk-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
    1. **[Criar um usuário de teste do FreshDesk](#create-freshdesk-test-user)** – para ter um equivalente de Brenda Fernandes no FreshDesk que esteja vinculado à representação de usuário do Azure AD.
1. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **FreshDesk**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o padrão `https://<tenant-name>.freshdesk.com` ou qualquer outro valor sugerido pelo FreshDesk.

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o padrão `https://<tenant-name>.freshdesk.com` ou qualquer outro valor sugerido pelo FreshDesk.

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Entre em contato com [equipe de suporte do cliente FreshDesk](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo FreshDesk espera as asserções de SAML em um formato específico, que exige a inclusão de mapeamentos de atributos personalizados na configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão em que **Identificador de Usuário Único** é mapeado com **user.userprincipalname**, mas o FreshDesk espera que essa declaração seja mapeada com **user.mail**, de modo que você precisa editar o mapeamento de atributos clicando no ícone Editar para então alterá-lo.

    ![image](common/edit-attribute.png)

1. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o FreshDesk**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brendafernandes\@dominiodaempresa.extensao**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.


### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você deve habilitar Britta Simon usar logon único do Azure, concedendo acesso ao FreshDesk.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **FreshDesk**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, digite e selecione **FreshDesk**.

    ![O link do FreshDesk na lista Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-freshdesk-single-sign-on"></a>Configurar o Logon Único do FreshDesk

1. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa do Freshdesk como administrador.

2. Selecione o **Ícone de Segurança** e, na seção **Segurança**, realize as seguintes etapas:

    ![Logon único](./media/freshdesk-tutorial/configure-1.png "Logon Único")
  
    a. Para **Logon Único**, selecione **Ativado**.

    b. No **Método de Logon**, selecione **SSO SAML**.

    c. Na caixa de texto **ID da Entidade fornecida pelo IdP**, cole o valor da **ID da Entidade** copiado do portal do Azure.

    d. Na caixa de texto **URL de SSO SAML**, cole o valor da **URL de Logon** copiado do portal do Azure.

    e. Nas **Opções de Assinatura**, selecione **Apenas Asserções Assinadas** na lista suspensa.

    f. Na caixa de texto **URL de logoff**, cole o **valor do URL de logout**, copiado do portal do Azure.

    g. Na caixa de texto **Certificado de Segurança**, cole o valor **Certificado (Base64)** que você obteve anteriormente.
  
    h. Clique em **Save** (Salvar).

## <a name="create-freshdesk-test-user"></a>Criar um usuário de teste do FreshDesk

Para permitir que os usuários do Azure AD façam logon no FreshDesk, eles devem ser provisionados no FreshDesk.  
No caso do FreshDesk, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu locatário do **Freshdesk** .

1. No menu à esquerda, clique em **Administração** e, na guia **Configurações Gerais**, clique em **Agentes**.
  
    ![Agentes](./media/freshdesk-tutorial/create-user-1.png "Agentes")

1. Clique em **Novo Agente**.

    ![Novo Agente](./media/freshdesk-tutorial/create-user-2.png "Novo Agente")

1. Na caixa de diálogo de Informações do Agente, insira os dados nos campos obrigatórios e clique em **Criar agente**.

    ![Informações sobre o Agente](./media/freshdesk-tutorial/create-user-3.png "Informações sobre o Agente")

    >[!NOTE]
    >O titular da conta do Azure AD receberá um email que inclui um link para confirmar a conta antes que ela se torne ativa.
    >
    >[!NOTE]
    >É possível usar qualquer outra ferramenta de criação da conta de usuário do Freshdesk ou APIs fornecidas pelo Freshdesk para provisionar as contas de usuário do Azure AD para o FreshDesk.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do FreshDesk no Painel de Acesso, você deverá ser conectado automaticamente ao FreshDesk, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

