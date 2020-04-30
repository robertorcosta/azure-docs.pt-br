---
title: 'Tutorial: Integração do Azure Active Directory com SharePoint no local | Microsoft Docs'
description: Aprenda como configurar o logon único entre o Active Directory do Azure e o SharePoint local.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 85b8d4d0-3f6a-4913-b9d3-8cc327d8280d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/15/2020
ms.author: miguego
ms.openlocfilehash: 7e47891a74feb60b0f3e4594bd1621e8b62d64d1
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81866973"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sharepoint-on-premises"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao SharePoint local

Neste tutorial, você aprenderá como integrar o SharePoint local ao Azure AD (Azure Active Directory). Ao integrar o SharePoint local ao Azure AD, você pode:

* Você pode controlar no Azure AD quem tem acesso ao SharePoint local.
* Permitir que os usuários sejam conectados automaticamente ao SharePoint local com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure Active Directory com o SharePoint local, você precisa dos seguintes itens:

* Uma assinatura do Azure Active Directory. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Um farm do SharePoint 2013 ou mais recente.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você vai configurar e testar o logon único do Azure Active Directory em um ambiente de teste. Os usuários de Azure Active Directory seriam capazes de acessar seu SharePoint local.

## <a name="create-the-enterprise-applications-in-azure-portal"></a>Criar os aplicativos empresariais no portal do Azure

Para configurar a integração do SharePoint local ao Azure AD, você precisa adicionar o SharePoint local da galeria à sua lista de aplicativos SaaS gerenciados.

Para adicionar o SharePoint local da galeria, execute as etapas a seguir:

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

 > [!NOTE]
 > Se o elemento não estiverem disponíveis, também é possível abrir pelo link fixo **Todos os serviços** na parte superior do painel de navegação à esquerda. Na visão geral a seguir, o link do **Azure Active Directory** está localizado na seção **Identidade** ou ele pode ser pesquisado usando a caixa de texto de filtro.

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

4. Na caixa de pesquisa, digite **SharePoint Local**, selecione **SharePoint local** no painel de resultados.

    <kbd>![SharePoint local na lista de resultados](./media/sharepoint-on-premises-tutorial/search-new-app.png)</kbd>

1. Especifique um nome para o SharePoint Local e clique no botão **Adicionar** para adicionar o aplicativo.

1. No novo aplicativo empresarial, clique em Propriedades e verifique o valor de **Atribuição de usuário necessária**

   <kbd>![SharePoint local na lista de resultados](./media/sharepoint-on-premises-tutorial/user-assignment-required.png)</kbd>

em nosso cenário, esse valor é definido como **Não**.

## <a name="configure-and-test-azure-ad"></a>Configurar e testar o Azure AD

Nesta seção, você vai configurar o logon único do Azure AD com o SharePoint local.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do SharePoint local.

Para configurar e testar o logon único do Azure Active Directory com o SharePoint local, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
1. **[Configurar o SharePoint local](#configure-sharepoint-on-premises)** – para definir as configurações de logon único no lado do aplicativo.
1. **[Criar um usuário de teste do Azure AD no portal do Azure](#create-an-azure-ad-test-user-in-the-azure-portal)** – crie um usuário no Azure AD para logon único.
1. **[Criar um grupo de segurança do Microsoft Azure AD no portal do Azure](#create-an-azure-ad-security-group-in-the-azure-portal)** – crie um grupo de segurança para logon único no Microsoft Azure AD.
1. **[Conceder permissões para a Conta do Azure Active Directory no SharePoint local](#grant-permissions-to-azure-active-directory-account-in-sharepoint-on-premises)** – conceder permissões ao usuário do Azure AD.
1. **[Conceder permissões ao grupo do Azure AD no SharePoint local](#grant-permissions-to-azure-ad-group-in-sharepoint-on-premises)** – conceder permissões ao grupo do Azure AD.
1. **[Permitir acesso a uma Conta de Convidado para o SharePoint local no portal do Azure](#grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal)** – conceder permissões para a conta de convidado no Azure AD para SharePoint local.
1. **[Como configurar o provedor de identidade confiável para vários aplicativos Web](#configuring-the-trusted-identity-provider-for-multiple-web-applications)** – como usar o mesmo provedor de identidade confiável para vários aplicativos Web

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o SharePoint local, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), abra o diretório do Azure AD, clique em **Aplicativos empresariais**, clique no **nome do Aplicativo Empresarial criado anteriormente** e clique em **Logon único**.

2. Na caixa de diálogo **Selecionar um método de logon único**, clique no modo **SAML** para habilitar o logon único.
 
3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do SharePoint local](./media/sharepoint-on-premises-tutorial/sp-identifier-reply.png)

    1. Na caixa **Identificador**, digite uma URL usando o seguinte padrão: `urn:<sharepointFarmName>:<federationName>`

    1. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<YourSharePointSiteURL>/_trust/`

    1. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://<YourSharePointSiteURL>/`
    1. clique em Salvar.

    > [!NOTE]
    > Esses valores não são reais. Você precisa atualizar esses valores com a URL de Logon, o Identificador e a URL de Resposta reais.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](./media/sharepoint-on-premises-tutorial/certificatebase64.png)

6. Na seção **Configurar o SharePoint local**, copie as URLs apropriadas de acordo com suas necessidades.
    
    1. **URL de logon**
    
        Copiar URL de logon substituir **/saml2** no final por **/wsfed**. A aparência seria **https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed** (essa URL não é precisa)

    1. **Identificador do Azure AD**
    1. **URL de logoff**
    > [!NOTE]
    > Essa URL não pode ser usada como está no SharePoint: você deve substituir **/saml2** por **/wsfed**. O aplicativo SharePoint local usa SAML 1.1 token, portanto o Azure AD espera a solicitação WS Fed de servidor do SharePoint e após a autenticação, ele emite SAML 1.1. token.

### <a name="configure-sharepoint-on-premises"></a>Configurar o SharePoint local

1. **Criar um provedor de identidade confiável no SharePoint Server 2016**

    Entre no SharePoint Server e abra o Shell de gerenciamento do SharePoint. Preencha os valores:
    1. **$realm** (valor do identificador da seção Domínio e URLs do SharePoint local no portal do Azure).
    1. **$wsfedurl** (URL de logon único).
   1. **$filepath** (caminho de arquivo para o qual você baixou o arquivo de certificado) do portal do Azure.

    Execute os comandos a seguir para configurar um novo provedor de identidade confiável.

    > [!TIP]
    > Se você for novo no uso do PowerShell ou se quiser saber mais sobre como funciona o PowerShell, veja [PowerShell do SharePoint](https://docs.microsoft.com/powershell/sharepoint/overview?view=sharepoint-ps).


    ```
    $realm = "urn:sharepoint:sps201x"
    $wsfedurl="https://login.microsoftonline.com/2c4f1a9f-be5f-10ee-327d-a95dac567e4f/wsfed"
    $filepath="C:\temp\SharePoint 2019 OnPrem.cer"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map1 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.microsoft.com/ws/2008/06/identity/claims/role" -IncomingClaimTypeDisplayName "Role" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "Azure AD SharePoint server 201x" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map1,$map2 -SignInUrl $wsfedurl -IdentifierClaim $map1.InputClaimType
    ```
1. **Habilitar o provedor de identidade confiável para seu aplicativo**

    a. Na Administração Central, navegue até **Gerenciar Aplicativo de Web** e selecione o aplicativo Web que você deseja proteger com o Azure AD.

    b. Na faixa de opções, clique em **Provedores de Autenticação** e escolha a região que você deseja usar.

    c. Selecione **Provedor de Identidade Confiável** e selecione o provedor de identidade *AzureAD* que você acabou de registrar.

    d. Clique em **OK**.

    ![Como configurar seu provedor de autenticação](./media/sharepoint-on-premises-tutorial/config-auth-provider.png)

### <a name="create-an-azure-ad-test-user-in-the-azure-portal"></a>Criar um usuário de teste do Azure AD no portal do Azure

O objetivo desta seção é criar um usuário de teste no portal do Azure.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory** e, no painel **Gerenciar**, selecione **Usuários**.

2. Em seguida, selecione **Todos os usuários** seguido por **Novo usuário** na parte superior da tela.

3. Selecione a opção **Criar Usuário** e, nas propriedades do usuário, execute as etapas a seguir.  
   Você pode criar usuários em seu Azure AD usando seu sufixo de locatário ou qualquer domínio verificado. 

    a. No campo **Nome**, Insira o nome de usuário. Nós usamos **TestUser**.
  
    b. No campo **Nome de usuário**, digite `TestUser@yourcompanydomain.extension`  
    Por exemplo, TestUser@contoso.com

    ![A caixa de diálogo Usuário](./media/sharepoint-on-premises-tutorial/user-properties.png)

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

    e. Agora você pode compartilhar o site com TestUser@contoso.com e permitir que esse usuário o acesse.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Criar um grupo de segurança do Microsoft Azure Active Directory no portal do Azure

1. Clique em **Azure Active Directory > Grupos**.

2. Clique em **Novo grupo**:

3. Preencha **Tipo de grupo**, **Nome do grupo**, **Descrição do grupo**, **Tipo de associação**. Clique na seta para selecionar membros; em seguida, pesquise ou clique no membro que deseja adicionar ao grupo. Clique em **Selecionar** para adicionar os membros selecionados e, em seguida, clique em **Criar**.

![Criar um grupo de segurança do Azure AD](./media/sharepoint-on-premises-tutorial/new-group.png)

### <a name="grant-permissions-to-azure-active-directory-account-in-sharepoint-on-premises"></a>Conceder permissões para o Azure Active Directory conta no SharePoint local

Para permitir acesso ao usuário do Azure Active Directory no SharePoint local, você precisa compartilhar o conjunto de sites ou adicionar o usuário do Azure Active Directory a um dos grupos do conjunto de sites. Agora, os usuários podem entrar no SharePoint 201x usando identidades do Azure AD, mas ainda há oportunidades de aprimoramento para a experiência do usuário. Por exemplo, a pesquisa por um usuário apresenta vários resultados de pesquisa no seletor de pessoas. Há um resultado de pesquisa para cada um dos tipos de declarações criados no mapeamento de declarações. Para escolher um usuário usando o seletor de pessoas, você deve digitar o nome de usuário dele exatamente e escolher o resultado da reivindicação **nome**.

![Reivindica resultados da pesquisa](./media/sharepoint-on-premises-tutorial/claims-search-results.png)

Não há validação nos valores que você pesquisa, o que pode levar a erros de ortografia ou a usuários que escolherem acidentalmente o tipo de declaração errado. Isso pode impedir que os usuários acessem recursos com êxito.

**Para corrigir o seletor de pessoas** com esse cenário, há uma solução de software livre chamada [AzureCP](https://yvand.github.io/AzureCP/) que fornece um provedor de declarações personalizado para o SharePoint 2013, 2016 e 2019. Ele usará a API do Microsoft Graph para resolver o que os usuários inserem e realizará a validação. Saiba mais em [ AzureCP ](https://yvand.github.io/AzureCP/).

  > [!NOTE]
  > Sem o AzureCP, você pode adicionar grupos adicionando a ID do grupo do Azure AD, mas isso não é amigável nem confiável para o usuário. A aparência é esta:  
  >   
  >![Adicionar o grupo do Azure AD ao grupo do SharePoint](./media/sharepoint-on-premises-tutorial/adding-group-by-id.png)
  
### <a name="grant-permissions-to-azure-ad-group-in-sharepoint-on-premises"></a>Conceder permissões ao grupo do Azure AD no SharePoint local

Para atribuir grupos de segurança do Azure Active Directory ao SharePoint local, será necessário usar um provedor de declarações personalizado para o SharePoint Server. Em nosso exemplo, usamos o AzureCP.

 > [!NOTE]
 > O AzureCP não é um produto da Microsoft ou com suporte pelo suporte técnico da Microsoft. Baixar, instalar e configurar o AzureCP no farm do SharePoint local de acordo com https://yvand.github.io/AzureCP/. 

1. Configure o AzureCP no farm do SharePoint local ou uma solução alternativa de provedor de declarações personalizadas. as etapas da configuração do AzureCP estão disponíveis em https://yvand.github.io/AzureCP/Register-App-In-AAD.html

1. No portal do Azure, abra o diretório do Microsoft Azure Active Directory. Clique em **Aplicativos empresariais**, clique no **nome do aplicativo empresarial criado anteriormente** e clique em **Logon único**.

1. Na página **Configurar o Logon Único com o SAM**, edite a seção **Atributos do Usuário e Declarações**.

1. Clique em **Adicionar uma declaração de grupo**.

1. Selecione quais grupos associados ao usuário devem ser retornados na declaração. Em nosso caso, selecione **Todos os grupos** e, na seção Atributo de origem, selecione **ID do Grupo** e clique em **Salvar**.

Para permitir acesso ao grupo de segurança Azure Active Directory no SharePoint local, você precisa compartilhar o conjunto de sites ou adicionar o grupo de segurança do Azure Active Directory a um dos grupos do conjunto de sites.

1. Navegue até o conjunto de sites do SharePoint e, em Configurações do Site para o Conjunto de Sites, clique em "Pessoas e grupos". Selecione o grupo do SharePoint e clique em Novo, "Adicionar Usuários a este Grupo" e comece a digitar o nome do grupo que o Seletor de Pessoas exibirá o Grupo de Segurança do Azure Active Directory.

    ![Adicionar o grupo do Azure AD ao grupo do SharePoint](./media/sharepoint-on-premises-tutorial/permission-azure-ad-group.png)

### <a name="grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal"></a>Permitir acesso a uma conta de convidado para o SharePoint local no portal do Azure

Agora é possível permitir acesso ao seu site do SharePoint para uma conta de convidado de maneira consistente. Acontece que o UPN é modificado. O usuário com jdoe@outlook.com será representado como `jdoe_outlook.com#ext#@TENANT.onmicrosoft.com`. É possível obter uma experiência simplificada ao compartilhar seu site com usuários externos. Seria necessário adicionar algumas modificações à seção **Atributos de Usuário e Declarações** no portal do Azure.

1. No portal do Azure, abra o diretório do Microsoft Azure Active Directory. Clique em **Aplicativos empresariais**, clique no **nome do aplicativo empresarial criado anteriormente** e clique em **Logon único**.

1. Na página **Configurar o Logon Único com o SAM**, edite a seção **Atributos do Usuário e Declarações**.

1. Na zona **Declaração necessária**, clique em **Identificador de Usuário Único (ID de Nome)** .

1. Altere a propriedade **Atributo de Origem** para o valor **user.localuserprincipalname** e **salve**.

    ![Atributos de usuário e declarações iniciais](./media/sharepoint-on-premises-tutorial/manage-claim.png)

1. Usando a faixa de opções, volte para **Logon baseado em SAML**. Agora, a seção **Atributos de Usuário e Declarações** teria a seguinte aparência: 

    ![Atributos de usuário e declarações finais](./media/sharepoint-on-premises-tutorial/user-attributes-claims-final.png)  

    > [!NOTE]
    > O sobrenome e o nome fornecido não são necessários nesta configuração.

1. No portal do Azure, no painel esquerdo, selecione **Azure Active Directory** e selecione **Usuários**.

1. Clique em **Novo Usuário Convidado**

1. Selecione a opção **Convidar Usuário**, preencha as propriedades do usuário e clique em **Convidar**.

1. Agora você pode compartilhar o site com MyGuestAccount@outlook.com e permitir que esse usuário o acesse.

    ![Como compartilhar o site com a conta do convidado](./media/sharepoint-on-premises-tutorial/sharing-guest-account.png)

### <a name="configuring-the-trusted-identity-provider-for-multiple-web-applications"></a>Configurando o provedor de identidade confiável para vários aplicativos Web

A configuração funciona para um único aplicativo Web, mas precisa de configuração adicional se você pretende usar o mesmo provedor de identidade confiável para vários aplicativos Web. Por exemplo, imagine que nós estendemos um aplicativo Web para usar a URL `https://sales.contoso.com` e agora queremos autenticar os usuários em `https://marketing.contoso.com` também. Para fazer isso, precisamos atualizar o provedor de identidade para respeitar o parâmetro WReply e atualizar o registro do aplicativo no Azure AD para adicionar uma URL de resposta.

1. No portal do Azure, abra o diretório do Microsoft Azure Active Directory. Clique em **Aplicativos empresariais**, clique no **nome do aplicativo empresarial criado anteriormente** e clique em **Logon único**.

2. Na página **Configurar Logon Único com SAM**, edite a **Configuração Básica de SAML**.

    ![editar a Configuração Básica de SAML](./media/sharepoint-on-premises-tutorial/add-reply-url.png)

3. Em **URL de Resposta (URL do Serviço do Consumidor de Declaração)** , adicione a URL para os aplicativo Web adicionais e clique em **Salvar**.

    ![Editar a Configuração Básica de SAML](./media/sharepoint-on-premises-tutorial/reply-url-for-web-application.png)

4. No servidor do SharePoint, abra o **Shell de Gerenciamento do SharePoint 201x** e execute os comandos a seguir, usando o nome do emissor do token de identidade confiável que você usou anteriormente.
    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
5. Em Administração Central, vá para o aplicativo Web e habilite o provedor de identidade confiável existente.

Você pode ter outro cenário em que deseja conceder acesso ao SharePoint local para seus usuários internos. Para esse cenário, você precisaria implantar o Microsoft Azure Active Directory Connect, que permitirá sincronizar seus usuários locais com o Azure Active Directory. Essa configuração fará parte de outro artigo. 

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [O que é a identidade híbrida com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity)
