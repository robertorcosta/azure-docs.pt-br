---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Akamai | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Akamai.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1b7e0d7a-e78f-43a5-af93-b626186e2376
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/03/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb9135873b61abf5a5ebd0d9c4d7f52ae314ee12
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/10/2020
ms.locfileid: "84674859"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-akamai"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Akamai

Neste tutorial, você aprenderá a integrar o Akamai ao Azure AD (Azure Active Directory). Quando integrar o Akamai ao Azure AD, será possível:

* Controlar no Azure AD quem tem acesso ao Akamai.
* Permitir que seus usuários entrem automaticamente no Akamai com as contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

A integração de acesso a aplicativos empresariais do Azure Active Directory e do Akamai permite acesso contínuo a aplicativos herdados hospedados na nuvem ou localmente. A solução integrada aproveita as vantagens de todos os recursos modernos do Azure Active Directory, como [Acesso condicional do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal), [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) e [Azure AD Identity Governance](https://docs.microsoft.com/azure/active-directory/governance/identity-governance-overview) para acesso de aplicativos herdados sem modificações de aplicativo ou instalação de agentes.

A imagem abaixo descreve como o EAA do Akamai se encaixa no cenário de Acesso Seguro Híbrido mais amplo

![O EAA do Akamai se encaixa no cenário de Acesso Seguro Híbrido mais amplo](./media/header-akamai-tutorial/introduction01.png)

### <a name="key-authentication-scenarios"></a>Cenários de autenticação de chaves

Além do suporte à integração nativa do Azure Active Directory para protocolos de autenticação modernos, como Open ID Connect, SAML e WS-Fed, o EAA do Akamai estende o acesso seguro para aplicativos de autenticação herdados para acesso interno e externo com o Azure AD, habilitando cenários modernos (por exemplo, acesso sem senha) a esses aplicativos. Isso inclui:

* Aplicativos de autenticação baseada em cabeçalho
* Área de Trabalho Remota
* SSH (Secure Shell)
* Aplicativos de autenticação Kerberos
* VNC (Computação de Rede Virtual)
* Autenticação anônima ou nenhum aplicativo de autenticação interno
* Aplicativos de autenticação NTLM (proteção com prompts duplos para o usuário)
* Aplicativo baseado em formulários (proteção com prompts duplos para o usuário)

### <a name="integration-scenarios"></a>Cenários de integração

A parceria entre a Microsoft e o EAA do Akamai permite a flexibilidade de atender aos seus requisitos de negócios, dando suporte a vários cenários de integração com base em seu requisito de negócios. Isso pode ser usado para fornecer cobertura, desde o início, para todos os aplicativos, bem como classificar e configurar gradualmente as classificações de política apropriadas.

#### <a name="integration-scenario-1"></a>Cenário de integração 1

O EAA do Akamai é configurado como um único aplicativo no Azure AD. O administrador pode configurar a Política de AC no aplicativo e, depois que as condições forem satisfeitas, os usuários poderão obter acesso ao portal do EAA do Akamai.

**Prós**:

• Você precisa configurar o IDP apenas uma vez

**Contras**:

• Os usuários acabam tendo dois portais de aplicativos

• Cobertura da Política de AC única comum para todos os aplicativos.

![Cenário de integração 1](./media/header-akamai-tutorial/scenario1.png)

#### <a name="integration-scenario-2"></a>Cenário de integração 2

O aplicativo EAA do Akamai é configurado individualmente no portal do Azure AD. O administrador pode configurar a Política de AC individual nos aplicativos e, depois que as condições são atendidas, os usuários poderão ser redirecionados diretamente para o aplicativo específico.

**Prós**:

• Você pode definir Políticas de AC individuais

• Todos os aplicativos são representados no 0365 Waffle e no painel myApps.microsoft.com.

**Contras**:

• Você precisa configurar vários IDPs.

![Cenário de integração 2](./media/header-akamai-tutorial/scenario2.png)

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do Akamai habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

- O Akamai é compatível com SSO iniciado por IDP

#### <a name="important"></a>Importante

Todas as configurações listadas abaixo são as mesmas para o **Cenário de integração 1** e para o **Cenário 2**. Para o **Cenário de integração 2**, você terá configurado o IDP individual no EAA do Akamai e a propriedade de URL precisará ser modificada para apontar para a URL do aplicativo.

![Importante](./media/header-akamai-tutorial/important.png)

## <a name="adding-akamai-from-the-gallery"></a>Adicionando o Akamai por meio da galeria

Para configurar a integração do Akamai ao Azure AD, você precisa adicioná-lo por meio da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Akamai** na caixa de pesquisa.
1. Selecione **Akamai** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-akamai"></a>Configurar e testar logon único do Azure AD para o Akamai

Configure e teste o SSO do Azure AD com o Akamai usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Akamai.

Para configurar e testar o SSO do Azure AD com o Akamai, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Akamai](#configure-akamai-sso)** – para definir as configurações de logon único no lado do aplicativo.
    * **[Como configurar o IDP](#setting-up-idp)**
    * **[Autenticação baseada em cabeçalho](#header-based-authentication)**
    * **[Área de trabalho remota](#remote-desktop)**
    * **[SSH](#ssh)**
    * **[Autenticação Kerberos](#kerberos-authentication)**
    * **[Criar um usuário de teste do Akamai](#create-akamai-test-user)** – para ter um equivalente de B. Fernandes no Akamai que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Akamai**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<Yourapp>.login.go.akamai-access.com/saml/sp/response`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https:// <Yourapp>.login.go.akamai-access.com/saml/sp/response`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Contate a [equipe de suporte ao Cliente do Akamai](https://www.akamai.com/us/en/contact-us/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o Akamai**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo-lhe acesso ao Akamai.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Akamai**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-akamai-sso"></a>Configurar o SSO do Akamai

### <a name="setting-up-idp"></a>Como configurar o IDP

**Configuração de IDP do EAA do AKAMAI**

1. Entre no console de **Acesso ao aplicativo empresarial do Akamai**.
1. No **Console EAA do Akamai**, selecione **Identidade** > **Provedores de Identidade** e clique em **Adicionar Provedor de Identidade**.

    ![Configurando o Akamai](./media/header-akamai-tutorial/configure01.png)

1. Em **Criar Provedor de Identidade**, execute as seguintes etapas:

    ![Configurando o Akamai](./media/header-akamai-tutorial/configure02.png)

    a. Especifique o **Nome Exclusivo**.

    b. Escolha **SAML de Terceiros** e clique em **Criar Provedor de Identidade e Configurar**.

### <a name="general-settings"></a>Configurações gerais

1. **Interceptação de identidade** – especifique o nome do (URL base do SP – será usado para a configuração do Azure AD)

    > [!NOTE]
    > Você pode optar por ter seu próprio domínio personalizado (exigirá uma entrada DNS e um Certificado). Neste exemplo, vamos usar o domínio do Akamai.

1. **Zona de nuvem do Akamai** – selecione a zona de nuvem apropriada.
1. **Validação de certificado** – Verifique a documentação do Akamai (opcional)

    ![Configurando o Akamai](./media/header-akamai-tutorial/configure03.png)

### <a name="authentication-configuration"></a>Configuração de autenticação

1. URL – especifique a URL igual à sua interceptação de identidade (é aqui que os usuários são redirecionados após a autenticação).
2. URL de Logoff: Atualize a URL de logoff.
3. Assinar solicitação SAML: padrão desmarcado.
4. Para o arquivo de metadados de IDP, adicione o aplicativo no console do Azure AD.

    ![Configurando o Akamai](./media/header-akamai-tutorial/configure04.png)

### <a name="session-settings"></a>Configurações da sessão

Deixe as configurações como padrão.

![Configurando o Akamai](./media/header-akamai-tutorial/sessionsettings.png)

### <a name="directories"></a>Diretórios

Ignore a configuração de diretório.

![Configurando o Akamai](./media/header-akamai-tutorial/directories.png)

### <a name="customization-ui"></a>UI de personalização

Você pode adicionar a personalização ao IDP.

![Configurando o Akamai](./media/header-akamai-tutorial/customizationui.png)

### <a name="advanced-settings"></a>Configurações avançadas

Ignore as configurações avançadas/consulte a documentação do Akamai para obter mais detalhes.

![Configurando o Akamai](./media/header-akamai-tutorial/advancesettings.png)

### <a name="deployment"></a>Implantação

1. Clique em Implantar Provedor de Identidade.

    ![Configurando o Akamai](./media/header-akamai-tutorial/deployment.png)

2. Verifique se a implantação foi bem-sucedida

### <a name="header-based-authentication"></a>Autenticação baseada em cabeçalho

Autenticação baseada em cabeçalho do Akamai

1. Escolha **HTTP personalizado** para o Assistente para adicionar aplicativos.

    ![Configurando o Akamai](./media/header-akamai-tutorial/configure05.png)

2. Insira o **Nome do Aplicativo** e a **Descrição**.

    ![Configurando o Akamai](./media/header-akamai-tutorial/configure06.png)

    ![Configurando o Akamai](./media/header-akamai-tutorial/configure07.png)

    ![Configurando o Akamai](./media/header-akamai-tutorial/configure08.png)

#### <a name="authentication"></a>Autenticação

1. Selecione a guia **Autenticação**.

    ![Configurando o Akamai](./media/header-akamai-tutorial/configure09.png)

2. Atribua o **Provedor de identidade**

    ![Configurando o Akamai](./media/header-akamai-tutorial/configure10.png)

#### <a name="services"></a>Serviços

Clique em Salvar e Ir para Autenticação.

![Configurando o Akamai](./media/header-akamai-tutorial/configure11.png)

#### <a name="advanced-settings"></a>Configurações avançadas

1. Em **Cabeçalhos HTTP do Cliente**, especifique **CustomerHeader** e **Atributo SAML**.

    ![Configurando o Akamai](./media/header-akamai-tutorial/configure12.png)

1. Clique no botão **Salvar e ir para Implantação**.

    ![Configurando o Akamai](./media/header-akamai-tutorial/configure13.png)

#### <a name="deploy-the-application"></a>Implantar o aplicativo

1. Clique no botão **Implantar Aplicativo**.

    ![Configurando o Akamai](./media/header-akamai-tutorial/configure14.png)

1. Verifique se o aplicativo foi implantado com êxito.

    ![Configurando o Akamai](./media/header-akamai-tutorial/configure15.png)

1. Experiência do usuário final.

    ![Configurando o Akamai](./media/header-akamai-tutorial/enduser01.png)

    ![Configurando o Akamai](./media/header-akamai-tutorial/enduser02.png)

1. Acesso Condicional.

    ![Configurando o Akamai](./media/header-akamai-tutorial/conditionalaccess01.png)

    ![Configurando o Akamai](./media/header-akamai-tutorial/conditionalaccess02.png)

#### <a name="remote-desktop"></a>Área de Trabalho Remota

1. Escolha **RDP** no Assistente para adicionar aplicativos.

    ![Configurando o Akamai](./media/header-akamai-tutorial/configure16.png)

1. Insira o **Nome do Aplicativo** e a **Descrição**.

    ![Configurando o Akamai](./media/header-akamai-tutorial/configure17.png)

    ![Configurando o Akamai](./media/header-akamai-tutorial/configure18.png)

1. Especifique o Conector que atenderá a ele.

    ![Configurando o Akamai](./media/header-akamai-tutorial/configure19.png)

#### <a name="authentication"></a>Autenticação

Clique em **Salvar e ir para Serviços**.

![Configurando o Akamai](./media/header-akamai-tutorial/configure20.png)

#### <a name="services"></a>Serviços

Clique em **Salvar e ir para Configurações Avançadas**.

![Configurando o Akamai](./media/header-akamai-tutorial/configure21.png)

#### <a name="advanced-settings"></a>Configurações avançadas

1. Clique em **Salvar e ir para Implantação**.

    ![Configurando o Akamai](./media/header-akamai-tutorial/configure22.png)

    ![Configurando o Akamai](./media/header-akamai-tutorial/configure23.png)

    ![Configurando o Akamai](./media/header-akamai-tutorial/configure24.png)

1. Experiência do usuário final

    ![Configurando o Akamai](./media/header-akamai-tutorial/enduser03.png)

    ![Configurando o Akamai](./media/header-akamai-tutorial/enduser02.png)

1. Acesso Condicional

    ![Configurando o Akamai](./media/header-akamai-tutorial/conditionalaccess04.png)

    ![Configurando o Akamai](./media/header-akamai-tutorial/conditionalaccess05.png)

    ![Configurando o Akamai](./media/header-akamai-tutorial/conditionalaccess06.png)

1. Como alternativa, você também pode digitar diretamente a URL do aplicativo RDP.

#### <a name="ssh"></a>SSH

1. Vá para Adicionar Aplicativos e escolha **SSH**.

    ![Configurando o Akamai](./media/header-akamai-tutorial/configure25.png)

1. Insira o **Nome do Aplicativo** e a **Descrição**.

    ![Configurando o Akamai](./media/header-akamai-tutorial/configure26.png)

1. Configurar a identidade do aplicativo.

    ![Configurando o Akamai](./media/header-akamai-tutorial/configure27.png)

    a. Especifique nome/descrição.

    b. Especifique o IP/FQDN do servidor de aplicativos e a porta para SSH.

    c. Especifique o nome de usuário/senha de SSH *Verifique a EAA do Akamai.

    d. Especifique o nome do host externo.

    e. Especifique o local para o conector e escolha o conector.

#### <a name="authentication"></a>Autenticação

Clique em **Salvar e ir para Serviços**.

![Configurando o Akamai](./media/header-akamai-tutorial/configure28.png)

#### <a name="services"></a>Serviços

Clique em **Salvar e ir para Configurações Avançadas**.

![Configurando o Akamai](./media/header-akamai-tutorial/configure29.png)

#### <a name="advanced-settings"></a>Configurações avançadas

Clique em Salvar e ir para a Implantação

![Configurando o Akamai](./media/header-akamai-tutorial/configure30.png)

![Configurando o Akamai](./media/header-akamai-tutorial/configure31.png)

#### <a name="deployment"></a>Implantação

1. Clique em **Implantar aplicativo**.

    ![Configurando o Akamai](./media/header-akamai-tutorial/configure32.png)

1. Experiência do usuário final

    ![Configurando o Akamai](./media/header-akamai-tutorial/enduser03.png)

    ![Configurando o Akamai](./media/header-akamai-tutorial/enduser04.png)

1. Acesso Condicional

    ![Configurando o Akamai](./media/header-akamai-tutorial/conditionalaccess04.png)

    ![Configurando o Akamai](./media/header-akamai-tutorial/conditionalaccess07.png)

    ![Configurando o Akamai](./media/header-akamai-tutorial/conditionalaccess08.png)

    ![Configurando o Akamai](./media/header-akamai-tutorial/conditionalaccess09.png)

### <a name="kerberos-authentication"></a>Autenticação Kerberos

No exemplo abaixo, publicaremos um servidor Web interno [http://frp-app1.superdemo.live](http://frp-app1.superdemo.live/) e habilitaremos o SSO usando o KCD

#### <a name="general-tab"></a>Guia Geral

![Configurando o Akamai](./media/header-akamai-tutorial/generaltab.png)

#### <a name="authentication-tab"></a>Guia Autenticação

Atribua o Provedor de Identidade

![Configurando o Akamai](./media/header-akamai-tutorial/authenticationtab.png)

#### <a name="services-tab"></a>Guia Serviços

![Configurando o Akamai](./media/header-akamai-tutorial/servicestab.png)

#### <a name="advanced-settings"></a>Configurações avançadas

![Configurando o Akamai](./media/header-akamai-tutorial/advancesettings02.png)

> [!NOTE]
> O SPN para o servidor Web deve estar no formato SPN@Domain, por exemplo, `HTTP/frp-app1.superdemo.live@SUPERDEMO.LIVE` para esta demonstração. Deixe o restante das configurações como padrão.

#### <a name="deployment-tab"></a>Guia Implantação

![Configurando o Akamai](./media/header-akamai-tutorial/deploymenttab.png)

#### <a name="adding-directory"></a>Adicionando diretório

1. Selecione **AD** no menu suspenso.

    ![Configurando o Akamai](./media/header-akamai-tutorial/configure33.png)

1. Forneça os dados necessários.

    ![Configurando o Akamai](./media/header-akamai-tutorial/configure34.png)

1. Verifique a criação do diretório.

    ![Configurando o Akamai](./media/header-akamai-tutorial/directorydomain.png)

1. Adicione os grupos/UOs que precisariam de acesso.

    ![Configurando o Akamai](./media/header-akamai-tutorial/addgroup.png)

1. Abaixo, o grupo é chamado de EAAGroup e tem um membro.

    ![Configurando o Akamai](./media/header-akamai-tutorial/eaagroup.png)

1. Adicione o diretório ao provedor de identidade clicando em **Identidade** > **Provedores de Identidade** e clique na guia **Diretórios** e em **Atribuir diretório**.

    ![Configurando o Akamai](./media/header-akamai-tutorial/assigndirectory.png)

### <a name="configure-kcd-delegation-for-eaa-walkthrough"></a>Passo a passo da configuração da delegação de KCD para EAA

#### <a name="step-1-create-an-account"></a>Etapa 1: Criar uma conta 

1. No exemplo, usaremos uma conta chamada **EAADelegation**. Execute isso usando o snap-in **Usuários e computadores do Active Directory**.

    ![Configurando o Akamai](./media/header-akamai-tutorial/assigndirectory.png)

    > [!NOTE]
    > O nome de usuário deve estar em um formato específico com base no **Nome da Interceptação de Identidade**. Na Figura 1, vemos que é **corpapps.login.go.akamai-access.com**

1. O nome de logon do usuário será:`HTTP/corpapps.login.go.akamai-access.com`

    ![Configurando o Akamai](./media/header-akamai-tutorial/eaadelegation.png)

#### <a name="step-2-configure-the-spn-for-this-account"></a>Etapa 2: configurar o SPN para essa conta

1. Com base neste exemplo, o SPN será o seguinte.

2. setspn -s **Http/corpapps.login.go.akamai-access.com eaadelegation**

    ![Configurando o Akamai](./media/header-akamai-tutorial/spn.png)

#### <a name="step-3-configure-delegation"></a>Etapa 3: configurar a delegação

1. Para a conta do EAADelegation, clique na guia Delegação.

    ![Configurando o Akamai](./media/header-akamai-tutorial/spn.png)

    * Especifique o uso de qualquer protocolo de autenticação
    * Clique em Adicionar e Adicionar Conta do Pool de Aplicativos ao site do Kerberos. Isso deve ser resolvido automaticamente para o SPN correto, se configurado corretamente.

#### <a name="step-4-create-a-keytab-file-for-akamai-eaa"></a>Etapa 4: criar um arquivo Keytab para o EAA do AKAMAI

1. Aqui está a sintaxe genérica.

1. ktpass /out ActiveDirectorydomain.keytab  /princ `HTTP/yourloginportalurl@ADDomain.com`  /mapuser serviceaccount@ADdomain.com /pass +rdnPass  /crypto All /ptype KRB5_NT_PRINCIPAL

1. Exemplo explicado

    | Snippet | Explicação |
    | - | - |
    | Ktpass /out EAADemo.keytab | // O nome do arquivo Keytab de saída |
    | /princ HTTP/corpapps.login.go.akamai-access.com@superdemo.live | // HTTP/yourIDPName@YourdomainName |
    | /mapuser eaadelegation@superdemo.live | // Conta de delegação do EAA |
    | /pass RANDOMPASS | // Conta de delegação do EAA Senha |
    | /crypto All ptype KRB5_NT_PRINCIPAL | // Documentação do EAA do Akamai de consultoria |
    | | |

1. Ktpass /out EAADemo.keytab  /princ HTTP/corpapps.login.go.akamai-access.com@superdemo.live /mapuser eaadelegation@superdemo.live /pass RANDOMPASS /crypto All ptype KRB5_NT_PRINCIPAL

    ![Configurando o Akamai](./media/header-akamai-tutorial/administrator.png)

#### <a name="step-5-import-keytab-in-the-akamai-eaa-console"></a>Etapa 5: importar Keytab no console do EAA do AKAMAI

1. Clique em **Sistema** > **Keytabs**.

    ![Configurando o Akamai](./media/header-akamai-tutorial/keytabs.png)

1. Em Tipo de Keytab, escolha **Delegação Kerberos**.

    ![Configurando o Akamai](./media/header-akamai-tutorial/keytabdelegation.png)

1. Verifique se Keytab aparece como Implantado e Verificado.

    ![Configurando o Akamai](./media/header-akamai-tutorial/keytabs02.png)

1. Experiência do usuário

    ![Configurando o Akamai](./media/header-akamai-tutorial/enduser03.png)

    ![Configurando o Akamai](./media/header-akamai-tutorial/enduser04.png)

1. Acesso Condicional

    ![Configurando o Akamai](./media/header-akamai-tutorial/conditionalaccess04.png)

    ![Configurando o Akamai](./media/header-akamai-tutorial/conditionalaccess10.png)

    ![Configurando o Akamai](./media/header-akamai-tutorial/conditionalaccess11.png)

### <a name="create-akamai-test-user"></a>Criar usuário de teste do Akamai

Nesta seção, você criará um usuário chamado B.Fernandes no Akamai. Trabalhe com a [equipe de suporte ao Cliente do Akamai](https://www.akamai.com/us/en/contact-us/) para adicionar os usuários à plataforma Akamai. Os usuários devem ser criados e ativados antes de usar o logon único. 

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Akamai no Painel de Acesso, você deverá ser conectado automaticamente ao Akamai no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimentar o Akamai com o Azure AD](https://aad.portal.azure.com/)