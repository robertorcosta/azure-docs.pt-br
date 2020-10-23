---
title: 'Tutorial: integração do Azure Active Directory com o Jobscience | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Jobscience.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 8dc4087d1a10b4c4af7477a02f397c5a2bc547c2
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92459383"
---
# <a name="tutorial-azure-active-directory-integration-with-jobscience"></a>Tutorial: Integração do Active Directory do Azure com o Jobscience

Neste tutorial, você aprende a integrar o Jobscience ao Azure AD (Azure Active Directory).

A integração do Jobscience ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Jobscience
- É possível permitir que os usuários se conectem automaticamente ao Jobscience (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um única localização: o Portal do Azure

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisites

Para configurar a integração do Azure AD ao Jobscience, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura habilitada para logon único do Jobscience

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, será possível obter uma versão de avaliação de um mês aqui: [Oferta de avaliação](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Jobscience por meio da galeria
1. configurar e testar o logon único do Azure AD

## <a name="adding-jobscience-from-the-gallery"></a>Adicionando o Jobscience por meio da galeria
Para configurar a integração do Jobscience ao Azure AD, é necessário adicionar o Jobscience à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o Jobscience por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

1. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Captura de tela mostra a portal do Azure aplicativos empresariais selecionados em gerenciar, com todos os aplicativos selecionados.][2]
    
1. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![Captura de tela mostra novo aplicativo selecionado.][3]

1. Na caixa de pesquisa, digite **Jobscience**.

    ![A captura de tela mostra adicionar da galeria com o jobscience inserido.](./media/jobscience-tutorial/tutorial_jobscience_search.png)

1. No painel de resultados, selecione **Jobscience** e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Captura de tela mostra os resultados que incluíam Jobscience.](./media/jobscience-tutorial/tutorial_jobscience_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>configurar e testar o logon único do Azure AD
Nesta seção, você configura e testa o logon único do Azure AD com o Jobscience, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Jobscience é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Jobscience.

No Jobscience, atribua o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Jobscience, você precisa concluir os seguintes blocos de construção:

1. **[Configuração do logon único do Azure ad](#configuring-azure-ad-single-sign-on)** -para habilitar seus usuários a usar esse recurso.
1. **[Criação de um usuário de teste do Azure ad](#creating-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
1. **[Criando um usuário de teste do Jobscience](#creating-a-jobscience-test-user)** – para ter um equivalente de Brenda Fernandes no Jobscience que esteja vinculado à representação de usuário do Azure AD.
1. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do AD do Azure.
1. **[Testando o logon único](#testing-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure e configura o logon único no aplicativo Jobscience.

**Para configurar o logon único do Azure AD com o Jobscience, realize as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **Jobscience**, clique em **Logon único**.

    ![Captura de tela mostra o logon único selecionado em gerenciar na portal do Azure.][4]

1. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Captura de tela mostra o modo de logon baseado em SAML selecionado.](./media/jobscience-tutorial/tutorial_jobscience_samlbase.png)

1. Na seção **Domínio e URLs do Jobscience**, realize as seguintes etapas:

    ![Captura de tela mostra o U R L de logon.](./media/jobscience-tutorial/tutorial_jobscience_url.png)

    Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão:  `http://<company name>.my.salesforce.com`
    
    > [!NOTE] 
    > Esse valor não é real. Atualize esse valor com a URL de Logon real. Obtenha esse valor com a [equipe de suporte ao Cliente do Jobscience](http://www.jobscience.com/support) ou no perfil de SSO que você criará, explicado adiante no tutorial. 
 
1. Na seção **certificado de autenticação SAML** , clique em **certificado (Base64)** e, em seguida, salve o arquivo de certificado em seu computador.

    ![A captura de tela mostra o painel Certificado de Autenticação SAML em que você pode baixar um certificado.](./media/jobscience-tutorial/tutorial_jobscience_certificate.png) 

1. Clique no botão **Salvar** .

    ![Captura de tela mostra o botão salvar.](./media/jobscience-tutorial/tutorial_general_400.png)

1. Na seção **Configuração do Jobscience**, clique em **Configurar o Jobscience** para abrir a janela **Configurar logon**. Copie a **URL de saída, a ID da entidade SAML e a URL do serviço de Sign-On único SAML** da **seção de referência rápida.**

    ![Captura de tela mostra a janela de configuração do Jobscience.](./media/jobscience-tutorial/tutorial_jobscience_configure.png) 

1. Faça logon no site da sua empresa Jobscience como um administrador.

1. Vá para **Configuração**.
   
   ![Captura de tela mostra o item de instalação da sua empresa.](./media/jobscience-tutorial/IC784358.png "Configuração")

1. No painel de navegação à esquerda, na seção **Administrador**, clique em **Gerenciamento de Domínio** para expandir a seção correspondente e clique em **Meu Domínio** para abrir a página **Meu Domínio**. 
   
   ![Meu domínio](./media/jobscience-tutorial/ic767825.png "Meu domínio")

1. Para verificar se o seu domínio foi configurado corretamente, verifique se ele está em "**etapa 4 implantada para usuários**" e examine "**minhas configurações de domínio**".

    ![Domínio implantado para o usuário](./media/jobscience-tutorial/ic784377.png "Domínio implantado para o usuário")

1. No site da empresa do Jobscience, clique em **Controles de Segurança** e, em seguida, clique em **Configurações de Logon Único**.
    
    ![Captura de tela mostra as configurações de Sign-On único selecionadas em controles de segurança.](./media/jobscience-tutorial/ic784364.png "Controles de Segurança")

1. Na seção **Configurações de Logon Único**, faça o seguinte:
    
    ![Configurações de logon único](./media/jobscience-tutorial/ic781026.png "Configurações de logon único")
    
    a. Selecione **SAML Habilitado**.

    b. Clique em **Nova**.

1. Na caixa de diálogo **Editar Configuração de Logon Único do SAML**, execute as etapas a seguir:
    
    ![Configuração de Logon Único do SAML](./media/jobscience-tutorial/ic784365.png "Configuração de Logon Único do SAML")
    
    a. Na caixa de texto **Nome** , digite um nome para a sua configuração.

    b. Na caixa de texto **Emissor**, cole o valor da **ID da Entidade SAML** copiado do portal do Azure.

    c. Na caixa de texto **ID da Entidade**, digite `https://salesforce-jobscience.com`

    d. Clique em **Procurar** para carregar seu certificado do AD do Azure.

    e. Como **Tipo de Identidade SAML**, selecione **A declaração contém a ID de Federação do objeto de usuário**.

    f. Selecione **A identidade está no elemento NameIdentifier da declaração do Assunto** como **Local da Identidade do SAML**.

    g. Na caixa de texto **URL de Logon do Provedor de Identidade**, cole o valor da **URL do Serviço de Logon Único SAML** copiado do portal do Azure.

    h. Na caixa de texto **URL de Logoff do Provedor de Identidade**, cole o valor da **URL de Saída** copiado do portal do Azure.

    i. Clique em **Save** (Salvar).

1. No painel de navegação à esquerda, na seção **Administrador**, clique em **Gerenciamento de Domínio** para expandir a seção correspondente e clique em **Meu Domínio** para abrir a página **Meu Domínio**. 
    
    ![Meu domínio](./media/jobscience-tutorial/ic767825.png "Meu domínio")

1. Na página **Meu Domínio**, na seção **Identidade Visual da Página de Logon**, clique em **Editar**.
    
    ![Captura de tela mostra a seção identidade visual da página de logon com o botão Editar.](./media/jobscience-tutorial/ic767826.png "Identidade visual da página de logon")

1. Na página **Identidade Visual da Página de Logon**, na seção **Serviço de Autenticação**, o nome das **Configurações de SSO do SAML** é exibido. Selecione-o e, em seguida, clique em **Salvar**.
    
    ![Captura de tela mostra a seção identidade visual da página de logon com PPE e salvar selecionado.](./media/jobscience-tutorial/ic784366.png "Identidade visual da página de logon")

1. Para obter o logon único iniciado pelo SP, clique nas **Configurações de Logon Único** na seção do menu **Controles de Segurança**.

    ![A captura de tela mostra administrar controles de segurança com configurações de Sign-On único selecionadas.](./media/jobscience-tutorial/ic784368.png "Controles de Segurança")
    
    Clique no perfil SSO que você criou na etapa anterior. Esta página mostra o logon único na URL para a sua empresa (por exemplo, `https://companyname.my.salesforce.com?so=companyid`.    

> [!TIP]
> Agora você pode ler uma versão concisa dessas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto você está configurando o aplicativo!  Depois de adicionar esse aplicativo da seção **Active Directory > Aplicativos Empresariais**, basta clicar na guia **Logon Único** e acessar a documentação inserida por meio da seção **Configuração** na parte inferior. Saiba mais sobre a funcionalidade de documentação inserida aqui: [Documentação inserida do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal do Azure**, no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![Captura de tela mostra o ícone do Azure A D no portal do Azure.](./media/jobscience-tutorial/create_aaduser_01.png) 

1. Para exibir a lista de usuários, vá para **usuários e grupos** e clique em **todos os usuários**.
    
    ![Captura de tela mostra os usuários e grupos selecionados no menu gerenciar, com todos os usuários selecionados.](./media/jobscience-tutorial/create_aaduser_02.png) 

1. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo.
 
    ![Captura de tela mostra o botão Adicionar para abrir a caixa de diálogo usuário.](./media/jobscience-tutorial/create_aaduser_03.png) 

1. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Captura de tela mostra a caixa de diálogo de usuário onde você pode inserir os valores nesta etapa.](./media/jobscience-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor da **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-jobscience-test-user"></a>Criando um usuário de teste do Jobscience

Para permitir que os usuários do Azure AD façam logon no Jobscience, eles devem ser provisionados no Jobscience. No caso do Jobscience, o provisionamento é uma tarefa manual.

>[!NOTE]
>Você pode usar qualquer outra ferramenta de criação de conta de usuário do Jobscience ou as APIs fornecidas pelo Jobscience para provisionar contas de usuário do Azure Active Directory.
>  
        
**Para configurar o provisionamento de usuários, execute as seguintes etapas:**

1. Faça logon no site da empresa do **Jobscience** como administrador.

1. Vá para a Configuração.
   
   ![Captura de tela mostra o item de instalação.](./media/jobscience-tutorial/ic784358.png "Instalação")
1. Vá para **Gerenciar Usuários \> Usuários**.
   
   ![Usuários](./media/jobscience-tutorial/ic784369.png "Usuários")
1. Clique em **Novo usuário**.
   
   ![Todos os Usuários](./media/jobscience-tutorial/ic784370.png "Todos os Usuários")
1. Na caixa de diálogo **Editar Usuário**, execute as seguintes etapas:
   
   ![Editar usuário](./media/jobscience-tutorial/ic784371.png "Editar usuário")
   
   a. Na caixa de texto **Nome**, digite um nome do usuário, como Brenda.
   
   b. Na caixa de texto **Sobrenome**, digite um sobrenome do usuário, como Fernandes.
   
   c. Na caixa de texto **Alias**, digite um nome de alias do usuário, como brendaf.

   d. Na caixa de texto **Email**, digite o endereço de email do usuário, como Brittasimon@contoso.com.

   e. Na caixa de texto **Nome de Usuário**, digite um nome de usuário do usuário, como Brittasimon@contoso.com.

   f. Na caixa de texto **Apelido**, digite um apelido do usuário, como Fernandes.

   g. Clique em **Save** (Salvar).

    
> [!NOTE]
> O titular da conta do Azure Active Directory recebe um email e segue um link para confirmar sua conta antes que ela se torne ativa.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Jobscience.

![Captura de tela mostra um nome de exibição da conta.][200] 

**Para atribuir Brenda Fernandes ao Jobscience, realize as seguintes etapas:**

1. Na portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **aplicativos empresariais** e clique em **todos os aplicativos**.

    ![Captura de tela mostra aplicativos empresariais no menu portal do Azure com todos os aplicativos selecionados.][201] 

1. Na lista de aplicativos, selecione **Jobscience**.

    ![A captura de tela mostra o Jobscience selecionado.](./media/jobscience-tutorial/tutorial_jobscience_app.png) 

1. No menu à esquerda, clique em **usuários e grupos**.

    ![Captura de tela mostra os usuários e grupos selecionados no menu portal do Azure.][202] 

1. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Captura de tela mostra o botão Adicionar, usado para adicionar atribuições.][203]

1. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

1. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

1. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco do Jobscience no Painel de Acesso, deverá ser conectado automaticamente ao aplicativo Jobscience.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/jobscience-tutorial/tutorial_general_01.png
[2]: ./media/jobscience-tutorial/tutorial_general_02.png
[3]: ./media/jobscience-tutorial/tutorial_general_03.png
[4]: ./media/jobscience-tutorial/tutorial_general_04.png

[100]: ./media/jobscience-tutorial/tutorial_general_100.png

[200]: ./media/jobscience-tutorial/tutorial_general_200.png
[201]: ./media/jobscience-tutorial/tutorial_general_201.png
[202]: ./media/jobscience-tutorial/tutorial_general_202.png
[203]: ./media/jobscience-tutorial/tutorial_general_203.png