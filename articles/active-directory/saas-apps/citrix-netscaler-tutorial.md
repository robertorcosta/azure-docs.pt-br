---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Citrix NetScaler (autenticação baseada em Kerberos) | Microsoft Docs'
description: Saiba como configurar logon único entre Azure Active Directory e Citrix NetScaler.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: af501bd0-8ff5-468f-9b06-21e607ae25de
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75e825f55a890be49000e209859670caa2c1c875
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75431230"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-citrix-netscaler-kerberos-based-authentication"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Citrix NetScaler (autenticação baseada em Kerberos)

Neste tutorial, você aprenderá a integrar o Citrix NetScaler ao Azure AD (Azure Active Directory). Ao integrar o Citrix NetScaler ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Citrix NetScaler.
* Permitir que os usuários sejam conectados automaticamente ao Citrix NetScaler com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisites

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Citrix NetScaler.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Citrix NetScaler é compatível com SSO iniciado por **SP**

* O Citrix NetScaler é compatível com o provisionamento de usuário **Just-In-Time**

- [Configurar o logon único do Citrix NetScaler para autenticação Kerberos](#configure-citrix-netscaler-single-sign-on-for-kerberos-based-authentication)

- [Configurar o logon único do Citrix NetScaler para autenticação baseada em cabeçalho](header-citrix-netscaler-tutorial.md)

## <a name="adding-citrix-netscaler-from-the-gallery"></a>Adicionar o Citrix NetScaler da galeria

Para configurar a integração do Citrix NetScaler com o Azure AD, é preciso adicionar o Citrix NetScaler da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Citrix NetScaler** na caixa de pesquisa.
1. Selecione **Citrix NetScaler** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-citrix-netscaler"></a>Configurar e testar o logon único do Azure AD para o Citrix NetScaler

Configure e teste o SSO do Azure AD com o Citrix NetScaler usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Citrix NetScaler.

Para configurar e testar o SSO do Azure AD com o Citrix NetScaler, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Citrix NetScaler](#configure-citrix-netscaler-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Citrix NetScaler](#create-citrix-netscaler-test-user)** : para ter um equivalente de B.Fernandes no Citrix NetScaler que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Citrix NetScaler**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<<Your FQDN>>`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`

    > [!NOTE]
    > Esses valores não são reais. Você precisa atualizar esses valores com a URL de Logon, o Identificador e a URL de Resposta reais. Contate a [equipe de suporte ao cliente do Citrix NetScaler](https://www.citrix.com/contact/technical-support.html) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

    > [!NOTE]
    > Para que o SSO funcione, essas URLs deverão ser acessíveis a partir de sites públicos. É necessário habilitar o firewall ou outras configurações de segurança do Netscaler para permitir que o Azure AD publique o token na URL de ACS configurado.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, encontre a **URL de Metadados de Federação do Aplicativo**, copie a URL e salve-a no Bloco de notas.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Citrix NetScaler**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Citrix NetScaler.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Citrix NetScaler**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-citrix-netscaler-sso"></a>Configurar o SSO do Citrix NetScaler

- [Configurar o logon único do Citrix NetScaler para autenticação Kerberos](#configure-citrix-netscaler-single-sign-on-for-kerberos-based-authentication)

- [Configurar o logon único do Citrix NetScaler para autenticação baseada em cabeçalho](header-citrix-netscaler-tutorial.md)

### <a name="publishing-web-server"></a>Como publicar o servidor Web 

1. Crie um **servidor virtual**.

    a. Acesse **Gerenciamento de Tráfego > Balanceamento de Carga > Serviços**.
    
    b. Clique em **Adicionar**.

    ![Configuração do Citrix NetScaler](./media/citrix-netscaler-tutorial/web01.png)

    c. Especifique os detalhes do servidor Web que executa os aplicativos abaixo:
    * **Nome do Serviço**
    * **IP do servidor/servidor existente**
    * **Protocolo**
    * **Porta**

     ![Configuração do Citrix NetScaler](./media/citrix-netscaler-tutorial/web01.png)

### <a name="configuring-load-balancer"></a>Como configurar o balanceador de carga

1. Para configurar o balanceador de carga, execute as seguintes etapas:

    ![Configuração do Citrix NetScaler](./media/citrix-netscaler-tutorial/load01.png)

    a. Acesse **Gerenciamento de Tráfego > Balanceamento de Carga > Servidores Virtuais**.

    b. Clique em **Adicionar**.

    c. Especifique os detalhes abaixo:

    * **Nome**
    * **Protocolo**
    * **Endereço IP**
    * **Porta**
    * Clique em **OK**

### <a name="bind-virtual-server"></a>Associar o servidor virtual

Associe o balanceador de carga ao servidor virtual criado anteriormente.

![Configuração do Citrix NetScaler](./media/citrix-netscaler-tutorial/bind01.png)

![Configuração do Citrix NetScaler](./media/citrix-netscaler-tutorial/bind02.png)

### <a name="bind-certificate"></a>Associar o certificado

Já que publicaremos esse serviço como SSL, associe o certificado do servidor e, em seguida, teste o aplicativo.

![Configuração do Citrix NetScaler](./media/citrix-netscaler-tutorial/bind03.png)

![Configuração do Citrix NetScaler](./media/citrix-netscaler-tutorial/bind04.png)

## <a name="citrix-adc-saml-profile"></a>Perfil SAML do Citrix ADC

### <a name="create-authentication-policy"></a>Criar uma política de autenticação

1. Acesse **Segurança > AAA: Tráfego de Aplicativo > Políticas > Autenticação > Políticas de Autenticação**.

2. Clique em **Adicionar** e, em seguida, especifique os detalhes.

    ![Configuração do Citrix NetScaler](./media/citrix-netscaler-tutorial/policy01.png)

    a. Nome da **Política de Autenticação**.

    b. Expressão: **true**.

    c. Tipo de ação **SAML**.

    d. Ação = clique em **Adicionar** (siga o Assistente para Criar Servidor SAML de Autenticação).
    
    e. Clique em Criar na **Política de Autenticação**.

### <a name="create-authentication-saml-server"></a>Criar um servidor SAML de autenticação

1. Execute as seguintes etapas:

    ![Configuração do Citrix NetScaler](./media/citrix-netscaler-tutorial/server01.png)

    a. Especifique o **Nome**.

    b. Importe os metadados (especifique a URL de metadados de federação da interface do usuário do SAML do Azure copiada acima).
    
    c. Especifique o **Nome do Emissor**.

    d. Clique em **Criar**.

### <a name="create-authentication-virtual-server"></a>Criar um servidor virtual de autenticação

1.  Acesse **Segurança > AAA: Tráfego de Aplicativo > > Servidores Virtuais de Autenticação**.

2.  Clique em **Adicionar** e execute as seguintes etapas:

    ![Configuração do Citrix NetScaler](./media/citrix-netscaler-tutorial/server02.png)

    a.  Forneça um **Nome**.

    b.  Escolha **Não Endereçável**.

    c.  Protocolo **SSL**.

    d.  Clique em **OK**.

    e.  Clique em **Continuar**.

### <a name="configure-the-authentication-virtual-server-to-use-azure-ad"></a>Configurar o servidor virtual de autenticação para usar o Azure AD

Você precisará modificar as duas seções do servidor virtual de autenticação.

1.  **Políticas de Autenticação Avançadas**

    ![Configuração do Citrix NetScaler](./media/citrix-netscaler-tutorial/virtual01.png)

    a. Selecione a **Política de Autenticação** criada anteriormente.

    b. Clique em **Associar**.

      ![Configuração do Citrix NetScaler](./media/citrix-netscaler-tutorial/virtual02.png)

2. **Servidores Virtuais Baseados em Formulário**

    ![Configuração do Citrix NetScaler](./media/citrix-netscaler-tutorial/virtual03.png)

    a.  Você precisará fornecer um **FQDN**, pois ele é imposto pela interface do usuário.

    b.  Escolha o **Balanceador de Carga do Servidor Virtual** que deseja proteger com a Autenticação do Azure AD.

    c.  Clique em **Associar**.

    ![Configuração do Citrix NetScaler](./media/citrix-netscaler-tutorial/virtual04.png)

    >[!NOTE]
    >Clique em **Concluído** na página Configuração do Servidor Virtual de Autenticação também.

3. Verifique as alterações. Procure a URL do aplicativo. Você deverá ver a página de logon do locatário em vez do acesso não autenticado anteriormente.

    ![Configuração do Citrix NetScaler](./media/citrix-netscaler-tutorial/virtual05.png)

## <a name="configure-citrix-netscaler-single-sign-on-for-kerberos-based-authentication"></a>Configurar o logon único do Citrix NetScaler para autenticação Kerberos

### <a name="create-a-kerberos-delegation-account-for-citrix-adc"></a>Criar uma Conta de Delegação do Kerberos para o Citrix ADC

1. Crie uma Conta de usuário (neste exemplo AppDelegation).

    ![Configuração do Citrix NetScaler](./media/citrix-netscaler-tutorial/kerberos01.png)

2. Configure um SPN de HOST nessas contas.

    * setspn -S HOST/AppDelegation.IDENTT.WORK identt\appdelegation
    
        No exemplo acima

        a. Identt.work    (FQDN do Domínio)

        b. Identt        (Nome do Netbios do Domínio)

        c. AppDelegation (nome da conta de usuário de delegação)

3. Configurar a delegação para o WebServer 
 
    ![Configuração do Citrix NetScaler](./media/citrix-netscaler-tutorial/kerberos02.png)

    >[!NOTE]
    >No exemplo acima, o nome Webserver Interno que está executando o Site WIA é cweb2

### <a name="citrix-aaa-kcd--kerberos-delegation-accounts"></a>Citrix AAA KCD (Contas de Delegação do Kerberos)

1.  Acesse **Gateway do Citrix > Contas AAA KCD (Delegação Restrita do Kerberos)** .

2.  Clique em Adicionar e especifique os detalhes abaixo:

    a.  Especifique o **Nome**.

    b.  **Realm**.

    c.  **SPN de serviço** `http/<host/fqdn>@DOMAIN.COM`.
    
    >[!NOTE]
    >@DOMAIN.com é obrigatório e em letras maiúsculas.

    d.  Especifique **Conta de usuário delegado**.

    e.  Verifique a Senha do usuário Delegado e Especifique **Senha**.

    f.  Clique em **OK**.
 
    ![Configuração do Citrix NetScaler](./media/citrix-netscaler-tutorial/kerberos03.png)

### <a name="citrix-traffic-policy-and-traffic-profile"></a>Política de Tráfego e Perfil de Tráfego do Citrix

1.  Acesse **Segurança > AAA – Tráfego de Aplicativo > Políticas > Políticas de Tráfego, Perfis e Formulários Perfis de SSO Políticas de Tráfego**.

2.  Selecione **Perfis de Tráfego**.

3.  Clique em **Adicionar**.

4.  Configurar o Perfil de Tráfego.

    a.  Especifique o **Nome**.

    b.  Especifique **Logon Único**.

    c.  Especifique a **Conta KCD** criada na etapa anterior na lista suspensa.

    d.  Clique em **OK**.

    ![Configuração do Citrix NetScaler](./media/citrix-netscaler-tutorial/kerberos04.png)
 
5.  Selecione **Política de Tráfego**.

6.  Clique em **Adicionar**.

7.  Configurar a Política de Tráfego.

    a.  Especifique o **Nome**.

    b.  Escolha o **Perfil de Tráfego** criado anteriormente na lista suspensa.

    c.  Defina a expressão como **true**.

    d.  Clique em **OK**.

    ![Configuração do Citrix NetScaler](./media/citrix-netscaler-tutorial/kerberos05.png)

### <a name="citrix-bind-traffic-policy-to-virtual-servers"></a>Política de Tráfego de Associação do Citrix para Servidores Virtuais

Para associar uma política de Tráfego a um servidor virtual específico usando a GUI.

* Acesse **Gerenciamento de Tráfego > Balanceamento de Carga > Servidores Virtuais**.

* Na lista do painel de detalhes de servidores virtuais, selecione o **servidor virtual** ao qual deseja associar a política de reescrita e, em seguida, clique em **Abrir**.

* Na caixa de diálogo Configurar o Servidor Virtual (Balanceamento de Carga), selecione a guia **Políticas**. Todas as políticas configuradas no NetScaler são exibidas na lista.
 
    ![Configuração do Citrix NetScaler](./media/citrix-netscaler-tutorial/kerberos06.png)

    ![Configuração do Citrix NetScaler](./media/citrix-netscaler-tutorial/kerberos07.png)

1.  Marque a **caixa de seleção** ao lado do nome da política que deseja associar a esse servidor virtual.
 
    ![Configuração do Citrix NetScaler](./media/citrix-netscaler-tutorial/kerberos08.png)

    ![Configuração do Citrix NetScaler](./media/citrix-netscaler-tutorial/kerberos09.png)

1. Somente a política é associada, clique em **Concluído**.
 
    ![Configuração do Citrix NetScaler](./media/citrix-netscaler-tutorial/kerberos10.png)

1. Teste usando o site integrado do Windows.

    ![Configuração do Citrix NetScaler](./media/citrix-netscaler-tutorial/kerberos11.png)    

### <a name="create-citrix-netscaler-test-user"></a>Criar usuário de teste do Citrix NetScaler

Nesta seção, um usuário chamado B.Fernandes é criado no Citrix NetScaler. O Citrix NetScaler é compatível com o provisionamento de usuário just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Citrix NetScaler, um novo será criado após a autenticação.

> [!NOTE]
> Se for necessário criar um usuário manualmente, contate a [equipe de suporte ao Cliente do Citrix NetScaler](https://www.citrix.com/contact/technical-support.html).

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Citrix NetScaler no Painel de Acesso, você deverá ser conectado automaticamente ao Citrix NetScaler para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimentar o Citrix NetScaler com o Azure AD](https://aad.portal.azure.com/)

- [Configurar o logon único do Citrix NetScaler para autenticação baseada em cabeçalho](header-citrix-netscaler-tutorial.md)
