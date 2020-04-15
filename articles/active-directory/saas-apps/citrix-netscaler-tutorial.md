---
title: 'Tutorial: Integração do logon único do Azure Active Directory ao Citrix NetScaler (autenticação baseada em Kerberos) | Microsoft Docs'
description: Saiba como configurar SSO (logon único) entre Azure Active Directory e o Citrix NetScaler usando a autenticação baseada em Kerberos.
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
ms.topic: tutorial
ms.date: 03/27/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6771060f05a03c82879738dc5e8caccb67e55abc
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80477992"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-citrix-netscaler-kerberos-based-authentication"></a>Tutorial: Integração do logon único do Azure Active Directory ao Citrix NetScaler (autenticação baseada em Kerberos)

Neste tutorial, você aprenderá a integrar o Citrix NetScaler ao Azure AD (Azure Active Directory). Ao integrar o Citrix NetScaler ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Citrix NetScaler.
* Permitir que os usuários sejam conectados automaticamente ao Citrix NetScaler com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos de SaaS (software como serviço) ao Azure AD, confira [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Citrix NetScaler.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste. Este tutorial inclui os seguintes cenários:

* SSO iniciado por **SP** para o Citrix NetScaler

* Provisionamento de usuário **Just-In-Time** para o Citrix NetScaler

* [Autenticação baseada em Kerberos para Citrix NetScaler](#publish-the-web-server)

* [Autenticação baseada em cabeçalho para Citrix NetScaler](header-citrix-netscaler-tutorial.md#publish-the-web-server)

* Depois de configurar o Citrix NetScaler, você poderá impor o controle de sessão, que fornece proteção contra exportação e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-citrix-netscaler-from-the-gallery"></a>Adicionar o Citrix NetScaler da galeria

Para integrar o Citrix NetScaler ao Azure AD, primeiro adicione o Citrix NetScaler à sua lista de aplicativos SaaS gerenciados da galeria:

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.

1. No menu esquerdo, selecione **Azure Active Directory**.

1. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os Aplicativos**.

1. Para adicionar um novo aplicativo, selecione **Novo aplicativo**.

1. Na seção **Adicionar por meio da galeria**, digite **Citrix NetScaler** na caixa de pesquisa.

1. Nos resultados, selecione **Citrix NetScaler** e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-citrix-netscaler"></a>Configurar e testar o logon único do Azure AD para o Citrix NetScaler

Configure e teste o SSO do Azure AD com o Citrix NetScaler usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Citrix NetScaler.

Para configurar e testar o SSO do Azure AD com o Citrix NetScaler, conclua os seguintes blocos de construção:

1. [Configurar o SSO do Azure AD](#configure-azure-ad-sso) – para permitir que os usuários usem esse recurso.

    1. [Crie um usuário de teste do Azure AD](#create-an-azure-ad-test-user) – para testar o SSO do Azure AD com B.Fernandes.

    1. [Atribua o usuário de teste do Azure AD](#assign-the-azure-ad-test-user) – para permitir que B.Fernandes use o SSO do Azure AD.

1. [Configurar o SSO do Citrix NetScaler](#configure-citrix-netscaler-sso) – para definir as configurações de SSO no lado do aplicativo.

    * [Criar um usuário de teste do Citrix NetScaler](#create-a-citrix-netscaler-test-user) – para ter um equivalente de B.Fernandes no Citrix NetScaler que esteja vinculado à representação de usuário do Azure AD.

1. [Testar o SSO](#test-sso) – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Para habilitar o SSO do Azure AD usando o portal do Azure, siga estas etapas:

1. No [portal do Azure](https://portal.azure.com/), no painel do aplicativo do **Citrix NetScaler**, em **Gerenciar**, selecione **Logon único**.

1. No painel **Selecionar um método de logon único**, selecione **SAML**.

1. No painel **Configurar o logon único com o SAML**, selecione o ícone de caneta **Editar** da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, para configurar o aplicativo no modo **iniciado por IDP**:

    1. Na caixa de texto **Identificador**, digite uma URL com o seguinte padrão: `https://<Your FQDN>`

    1. Na caixa de texto **URL de Resposta**, digite uma URL com o seguinte padrão: `http(s)://<Your FQDN>.of.vserver/cgi/samlauth`

1. Para configurar o aplicativo no modo **Iniciado por SP**, selecione **Definir URLs adicionais** e realize a seguinte etapa:

    * Na caixa de texto **URL de Entrada**, digite uma URL com o seguinte padrão: `https://<Your FQDN>/CitrixAuthService/AuthService.asmx`

    > [!NOTE]
    > * As URLs que são usadas nesta seção não são valores reais. Atualize esses valores com os valores reais do Identificador, da URL de Resposta e da URL de Logon. Contate a [equipe de suporte ao cliente do Citrix NetScaler](https://www.citrix.com/contact/technical-support.html) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.
    > * Para configurar o SSO, as URLs devem ser acessíveis de sites públicos. É necessário habilitar o firewall ou outras configurações de segurança do Citrix NetScaler para permitir que o Azure AD publique o token na URL configurada.

1. No painel **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, encontre a **URL de Metadados de Federação do Aplicativo**, copie a URL e salve-a no Bloco de notas.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Citrix NetScaler**, copie as URLs relevantes de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No menu esquerdo no portal do Azure, selecione **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.

1. Selecione **Novo usuário** na parte superior do painel.

1. Nas propriedades do **Usuário**, conclua estas etapas:

   1. Para **Nome**, insira `B.Simon`.  

   1. Para **Nome de usuário**, insira _username@companydomain.extension_ . Por exemplo, `B.Simon@contoso.com`.

   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote ou copie o valor exibido em **Senha**.

   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B. Fernandes use o SSO do Azure concedendo a ela acesso ao Citrix NetScaler.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.

1. Na lista de aplicativos, selecione **Citrix NetScaler**.

1. Na visão geral do aplicativo, em **Gerenciar**, selecione **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**. Em seguida, na caixa de diálogo **Adicionar Atribuição**, selecione **Usuários e grupos**.

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista **Usuários**. Escolha **Selecionar**.

1. Se você esperar um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, selecione a função relevante para o usuário na lista e, em seguida, escolha **Selecionar**.

1. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.

## <a name="configure-citrix-netscaler-sso"></a>Configurar o SSO do Citrix NetScaler

Selecione um link para as etapas referentes ao tipo de autenticação que você deseja configurar:

- [Configurar o SSO do Citrix NetScaler para autenticação baseada em Kerberos](#publish-the-web-server)

- [Configurar o SSO do Citrix NetScaler para autenticação baseada em cabeçalho](header-citrix-netscaler-tutorial.md#publish-the-web-server)

### <a name="publish-the-web-server"></a>Publicar o servidor Web 

Para criar um servidor virtual:

1. Selecione **Gerenciamento de Tráfego** > **Balanceamento de Carga** > **Serviços**.
    
1. Selecione **Adicionar**.

    ![Configuração do Citrix NetScaler – painel de Serviços](./media/citrix-netscaler-tutorial/web01.png)

1. Defina os seguintes valores para o servidor Web que está executando os aplicativos:

   * **Nome do Serviço**
   * **IP do servidor/servidor existente**
   * **Protocolo**
   * **Porta**

### <a name="configure-the-load-balancer"></a>Configure o balanceador de carga

Para configurar o balanceador de carga:

1. Acesse **Gerenciamento de Tráfego** > **Balanceamento de Carga** > **Servidores Virtuais**.

1. Selecione **Adicionar**.

1. Defina os valores a seguir, conforme descrito na seguinte captura de tela:

    * **Nome**
    * **Protocolo**
    * **Endereço IP**
    * **Porta**

1. Selecione **OK**.

    ![Configuração do Citrix NetScaler – painel de Configurações Básicas](./media/citrix-netscaler-tutorial/load01.png)

### <a name="bind-the-virtual-server"></a>Associar o servidor virtual

Para associar o balanceador de carga ao servidor virtual:

1. No painel **Serviços e Grupos de Serviços**, selecione **Nenhuma Associação de Serviço do Servidor Virtual de Balanceamento de Carga**.

   ![Configuração do Citrix NetScaler – painel Associação de Serviço do Servidor Virtual de Balanceamento de Carga](./media/citrix-netscaler-tutorial/bind01.png)

1. Verifique as configurações conforme mostrado na captura de tela a seguir e, em seguida, selecione **Fechar**.

   ![Configuração do Citrix NetScaler – Verificar a associação de serviço do servidor virtual de balanceamento de carga](./media/citrix-netscaler-tutorial/bind02.png)

### <a name="bind-the-certificate"></a>Associar o certificado

Para publicar esse serviço como TLS, associe o certificado do servidor e, em seguida, teste o aplicativo:

1. Em **Certificado**, selecione **Nenhum Certificado do Servidor**.

   ![Configuração do Citrix NetScaler – painel do Certificado do Servidor](./media/citrix-netscaler-tutorial/bind03.png)

1. Verifique as configurações conforme mostrado na captura de tela a seguir e, em seguida, selecione **Fechar**.

   ![Configuração do Citrix NetScaler – Verificar o certificado](./media/citrix-netscaler-tutorial/bind04.png)

## <a name="citrix-adc-saml-profile"></a>Perfil SAML do Citrix ADC

Para configurar o perfil SAML do Citrix ADC, conclua as seções a seguir.

### <a name="create-an-authentication-policy"></a>Criar uma política de autenticação

Para criar uma política de autenticação:

1. Acesse **Segurança** > **AAA: Tráfego de Aplicativo** > **Políticas** > **Autenticação** > **Políticas de Autenticação**.

1. Selecione **Adicionar**.

1. No painel **Criar Política de Autenticação**, insira ou selecione os seguintes valores:

    * **Name**: insira um nome para a política de autenticação.
    * **Ação**: insira **SAML** e selecione **Adicionar**.
    * **Expressão**:  insira **verdadeiro**.     
    
    ![Configuração do Citrix NetScaler – painel Criar Política de Autenticação](./media/citrix-netscaler-tutorial/policy01.png)

1. Selecione **Criar**.

### <a name="create-an-authentication-saml-server"></a>Criar um servidor SAML de autenticação

Para criar um servidor SAML de autenticação, acesse o painel **Criar Servidor SAML de Autenticação** e, em seguida, conclua as seguintes etapas:

1. Para **Nome**, insira um nome para o servidor SAML de autenticação.

1. Em **Exportar Metadados SAML**:

   1. Marque a caixa de seleção **Importar Metadados**.

   1. Insira a URL de metadados de federação da interface do usuário SAML do Azure que você copiou anteriormente.
    
1. Para **Nome do Emissor**, insira a URL relevante.

1. Selecione **Criar**.

![Configuração do Citrix NetScaler – painel Criar Servidor SAML de Autenticação](./media/citrix-netscaler-tutorial/server01.png)

### <a name="create-an-authentication-virtual-server"></a>Criar um servidor virtual de autenticação

Para criar um servidor virtual de autenticação:

1.  Acesse **Segurança** > **AAA: Tráfego de Aplicativo** > **Políticas** > **Autenticação** > **Servidores Virtuais de Autenticação**.

1.  Selecione **Adicionar** e conclua as etapas a seguir:

    1. Para **Nome**, insira um nome para o servidor virtual de autenticação.

    1. Marque a caixa de seleção **Não endereçável**.

    1. Em **Protocolo**, selecione **SSL**.

    1. Selecione **OK**.
    
1. Selecione **Continuar**.

### <a name="configure-the-authentication-virtual-server-to-use-azure-ad"></a>Configurar o servidor virtual de autenticação para usar o Azure AD

Modifique duas seções para o servidor virtual de autenticação:

1.  No painel **Políticas de Autenticação Avançadas**, selecione **Nenhuma Política de Autenticação**.

    ![Configuração do Citrix NetScaler – painel Políticas de Autenticação Avançadas](./media/citrix-netscaler-tutorial/virtual01.png)

1. No painel **Associação de Política**, selecione a política de autenticação e, em seguida, selecione **Associar**.

    ![Configuração do Citrix NetScaler – painel Associação de Política](./media/citrix-netscaler-tutorial/virtual02.png)

1. No painel **Servidores Virtuais Baseados em Formulário**, selecione **Nenhum Servidor Virtual de Balanceamento de Carga**.

    ![Configuração do Citrix NetScaler – painel Servidores Virtuais Baseados em Formulário](./media/citrix-netscaler-tutorial/virtual03.png)

1. Para **FQDN de Autenticação**, insira um FQDN (nome de domínio totalmente qualificado) (obrigatório).

1. Selecione o servidor virtual de balanceamento de carga que deseja proteger com a Autenticação do Azure AD.

1. Selecione **Associar**.

    ![Configuração do Citrix NetScaler – painel Associação do Servidor Virtual de Balanceamento de Carga](./media/citrix-netscaler-tutorial/virtual04.png)

    > [!NOTE]
    > Selecione **Concluído** no painel **Configuração do Servidor Virtual de Autenticação**.

1. Para verificar as alterações, em um navegador, vá para a URL do aplicativo. Você deve ver sua página de entrada do locatário em vez do acesso não autenticado que você viu anteriormente.

    ![Configuração do Citrix NetScaler – uma página de entrada em um navegador da Web](./media/citrix-netscaler-tutorial/virtual05.png)

## <a name="configure-citrix-netscaler-sso-for-kerberos-based-authentication"></a>Configurar o SSO do Citrix NetScaler para autenticação baseada em Kerberos

### <a name="create-a-kerberos-delegation-account-for-citrix-adc"></a>Criar uma Conta de Delegação do Kerberos para o Citrix ADC

1. Crie uma Conta de usuário (neste exemplo _AppDelegation_).

    ![Configuração do Citrix NetScaler – painel Propriedades](./media/citrix-netscaler-tutorial/kerberos01.png)

1. Configure um SPN de HOST nessas contas. 

    Exemplo: `setspn -S HOST/AppDelegation.IDENTT.WORK identt\appdelegation`
    
    Neste exemplo:

    * `IDENTT.WORK` é o FQDN do domínio.
    * `identt` é o nome NetBIOS do domínio.
    * `appdelegation` é o nome da conta de usuário de delegação.

1. Configure a delegação para o servidor Web, conforme mostrado na seguinte captura de tela:
 
    ![Configuração do Citrix NetScaler – Delegação no painel Propriedades](./media/citrix-netscaler-tutorial/kerberos02.png)

    > [!NOTE]
    > No exemplo da captura de tela, o nome do servidor Web interno que executa o site da WIA (Autenticação Integrada do Windows) é _CWEB2_.

### <a name="citrix-netscaler-aaa-kcd-kerberos-delegation-accounts"></a>Citrix NetScaler AAA KCD (Contas de Delegação do Kerberos)

Para configurar a conta do Citrix NetScaler AAA KCD:

1.  Acesse **Gateway do Citrix** > **Contas AAA KCD (Delegação Restrita do Kerberos)** .

1.  Selecione **Adicionar** e, em seguida, insira ou selecione os seguintes valores:

    * **Name**: insira um nome para a conta da KCD.

    * **Realm**: insira o domínio e a extensão em letras maiúsculas.

    * **SPN de serviço**: `http/<host/fqdn>@<DOMAIN.COM>`.
    
        > [!NOTE]
        > `@DOMAIN.COM` é necessário e deve estar em letras maiúsculas. Exemplo: `http/cweb2@IDENTT.WORK`.

    * **Usuário Delegado**: insira o nome de usuário delegado.

    * Marque a caixa de seleção **Senha para Usuário Delegado** e digite e confirme uma senha.

1. Selecione **OK**.
 
    ![Configuração do Citrix NetScaler – painel Configurar Conta da KCD](./media/citrix-netscaler-tutorial/kerberos03.png)

### <a name="citrix-traffic-policy-and-traffic-profile"></a>Política de tráfego e perfil de tráfego do Citrix

Para configurar a política de tráfego e perfil de tráfego do Citrix:

1.  Acesse **Segurança** > **AAA – Tráfego de Aplicativo** > **Políticas** > **Políticas de Tráfego, Perfis e Formulários Perfis de SSO Políticas de Tráfego**.

1.  Selecione **Perfis de Tráfego**.

1.  Selecione **Adicionar**.

1.  Para configurar um perfil de tráfego, digite ou selecione os valores a seguir.

    * **Name**: insira um nome para o perfil de tráfego.

    * **Logon único**: selecione **ATIVADO**.

    * **Conta da KCD**: selecione a conta da KCD criada na seção anterior.

1. Selecione **OK**.

    ![Configuração do Citrix NetScaler – painel Configurar Perfil de Tráfego](./media/citrix-netscaler-tutorial/kerberos04.png)
 
1.  Selecione **Política de Tráfego**.

1.  Selecione **Adicionar**.

1.  Para configurar uma política de tráfego, digite ou selecione os valores a seguir:

    * **Name**: insira um nome para a política de tráfego.

    * **Perfil**: selecione o perfil de tráfego criado na seção anterior.

    * **Expressão**: insira **verdadeiro**.

1. Selecione **OK**.

    ![Configuração do Citrix NetScaler – painel Configurar Política de Tráfego](./media/citrix-netscaler-tutorial/kerberos05.png)

### <a name="bind-a-traffic-policy-to-a-virtual-server-in-citrix"></a>Associar uma política de tráfego a um servidor virtual no Citrix

Para associar uma política de Tráfego a um servidor virtual usando a GUI:

1. Acesse **Gerenciamento de Tráfego** > **Balanceamento de Carga** > **Servidores Virtuais**.

1. Na lista de servidores virtuais, selecione o servidor virtual ao qual deseja associar a política de reescrita e, em seguida, seleciona **Abrir**.

1. No painel **Servidor Virtual de Balanceamento de Carga**, em **Configurações Avançadas**, selecione **Políticas**. Todas as políticas que são configuradas para sua instância do NetScaler aparecem na lista.
 
    ![Configuração do Citrix NetScaler – painel Servidor Virtual de Balanceamento de Carga](./media/citrix-netscaler-tutorial/kerberos06.png)

    ![Configuração do Citrix NetScaler – caixa de diálogo Políticas](./media/citrix-netscaler-tutorial/kerberos07.png)

1.  Marque a caixa de seleção ao lado do nome da política que deseja associar a esse servidor virtual.
 
    ![Configuração do Citrix NetScaler – painel Associação da Política de Tráfego do Servidor Virtual de Balanceamento de Carga](./media/citrix-netscaler-tutorial/kerberos09.png)

1. Na caixa de diálogo **Escolher Tipo**:

    1. Em **Escolher Política**, selecione **Tráfego**.

    1. Em **Escolher Tipo**, selecione **Solicitação**.

    ![Configuração do Citrix NetScaler – painel Escolher Tipo](./media/citrix-netscaler-tutorial/kerberos08.png)

1. Quando a política estiver associada, selecione **Concluído**.
 
    ![Configuração do Citrix NetScaler – painel Políticas](./media/citrix-netscaler-tutorial/kerberos10.png)

1. Teste a associação usando o site do WIA.

    ![Configuração do Citrix NetScaler – uma página de teste em um navegador da Web](./media/citrix-netscaler-tutorial/kerberos11.png)    

### <a name="create-a-citrix-netscaler-test-user"></a>Criar um usuário de teste do Citrix NetScaler

Nesta seção, um usuário chamado B.Fernandes é criado no Citrix NetScaler. O Citrix NetScaler é compatível com o provisionamento de usuário just-in-time, que está habilitado por padrão. Não há nenhuma ação para você executar nesta seção. Se um usuário ainda não existir no Citrix NetScaler, um novo será criado após a autenticação.

> [!NOTE]
> Se for necessário criar um usuário manualmente, contate a [equipe de suporte ao Cliente do Citrix NetScaler](https://www.citrix.com/contact/technical-support.html).

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de SSO do Azure AD usando o Painel de Acesso.

Ao selecionar o bloco do Citrix NetScaler no Painel de Acesso, você deverá ser conectado automaticamente ao Citrix NetScaler para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS ao Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimentar o Citrix NetScaler com o Azure AD](https://aad.portal.azure.com/)

- [Configurar o logon único do Citrix NetScaler para autenticação baseada em cabeçalho](header-citrix-netscaler-tutorial.md)

- [O que é controle de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger o Citrix NetScaler com visibilidade e controles avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)