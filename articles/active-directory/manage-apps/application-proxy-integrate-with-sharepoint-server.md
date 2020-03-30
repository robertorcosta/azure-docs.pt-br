---
title: Habilite o acesso remoto ao Proxy de aplicativo SharePoint - Azure AD
description: Aborda os conceitos básicos sobre como integrar um servidor do SharePoint local com o Proxy de Aplicativo do Azure AD.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 983470994c103cb25d0d2aff96ae8544080e6288
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481289"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Habilitar acesso remoto ao SharePoint com o Proxy de Aplicativo do Azure AD

Este guia passo-a-passo explica como integrar uma fazenda sharePoint no local com o Proxy de aplicativo do Azure Active Directory (Azure AD).

## <a name="prerequisites"></a>Pré-requisitos

Para executar a configuração, você precisa dos seguintes recursos:
- Uma fazenda SharePoint 2013 ou mais nova.
- Um inquilino azure AD com um plano que inclui proxy de aplicativo. Saiba mais sobre [os planos e preços do Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).
- Um [domínio personalizado e verificado](../fundamentals/add-custom-domain.md) no inquilino Azure AD.
- O Active Directory no local sincronizado com o Azure AD Connect, através do qual os usuários podem [fazer login no Azure](../hybrid/plan-connect-user-signin.md).
- Um conector proxy de aplicativo instalado e executado em uma máquina dentro do domínio corporativo.

A configuração do SharePoint com o Proxy do aplicativo requer duas URLs:
- Uma URL externa, visível para usuários finais e determinada no Azure AD. Esta URL pode usar um domínio personalizado. Saiba mais sobre [como trabalhar com domínios personalizados no Proxy de aplicativo Azure AD](application-proxy-configure-custom-domain.md).
- Uma URL interna, conhecida apenas dentro do domínio corporativo e nunca usada diretamente.

> [!IMPORTANT]
> Para garantir que os links sejam mapeados corretamente, siga estas recomendações para a URL interna:
> - Use o HTTPS.
> - Não use portas personalizadas.
> - No DNS (Corporate Domain Name System, sistema de nomes de domínio corporativo), crie um host (A) para apontar para o SharePoint WFE (ou balanceador de carga) e não um alias (CName).

Este artigo usa os seguintes valores:
- URL interno:`https://sharepoint`
- URL externo:`https://spsites-demo1984.msappproxy.net/`
- Conta do pool de aplicativos para o aplicativo web SharePoint:`Contoso\spapppool`

## <a name="step-1-configure-an-application-in-azure-ad-that-uses-application-proxy"></a>Passo 1: Configure um aplicativo no Azure AD que usa proxy de aplicativo

Nesta etapa, você cria um aplicativo no seu inquilino do Azure Active Directory que usa o Proxy do aplicativo. Você define a URL externa e especifica a URL interna, que são usadas mais tarde no SharePoint.

1. Crie o aplicativo conforme descrito com as seguintes configurações. Para obter instruções passo a passo, consulte [Publicar aplicativos usando o Proxy de Aplicativo do Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   * **URL interno**: URL interno do SharePoint que será `https://sharepoint`definido mais tarde no SharePoint, como .
   * **Pré-autenticação**: Diretório Ativo do Azure
   * **Traduzir URLs em cabeçalhos**: Não
   * **Traduzir URLs no corpo do aplicativo :** Não

   ![Publicar SharePoint como aplicativo](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

1. Depois que seu aplicativo for publicado, siga estas etapas para configurar as configurações de login único:

   1. Na página de aplicativo no portal, selecione **Logon único**.
   1. Para **o modo de inscrição única,** selecione **Autenticação integrada do Windows**.
   1. Defina **o SPN do aplicativo interno** para o valor definido anteriormente. Para este exemplo, `HTTP/sharepoint`o valor é .
   1. Em **Identidade de login delegada,** selecione a opção mais adequada para a configuração da floresta do Diretório Ativo. Por exemplo, se você tiver um único domínio do Active Directory em sua floresta, selecione **o nome da conta SAM no local** (como mostrado na captura de tela a seguir). Mas se seus usuários não estiverem no mesmo domínio que o SharePoint e os servidores do Conector proxy do aplicativo, selecione o **nome principal do usuário on-premises** (não mostrado na captura de tela).

   ![Configurar a Autenticação Integrada do Windows para SSO](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

1. Para concluir a configuração de seu aplicativo, vá para a seção **Usuários e grupos** e atribua usuários ao acessar este aplicativo. 

## <a name="step-2-configure-the-sharepoint-web-application"></a>Passo 2: Configure o aplicativo web SharePoint

O aplicativo web SharePoint deve ser configurado com o Kerberos e os mapeamentos de acesso alternativos apropriados para funcionar corretamente com o Proxy do Aplicativo Azure AD. Existem duas opções possíveis:

- Crie um novo aplicativo web e use apenas a zona Padrão. Esta é a opção preferida, pois oferece a melhor experiência com o SharePoint (por exemplo, os links nos alertas de e-mail gerados pelo SharePoint sempre apontam para a zona Padrão).
- Estender um aplicativo web existente para configurar o Kerberos em uma região não padrão.

> [!IMPORTANT]
> Independentemente da região usada, a conta do pool de aplicativos do aplicativo web SharePoint deve ser uma conta de domínio para que o Kerberos funcione corretamente.

### <a name="provision-the-sharepoint-web-application"></a>Provisão do aplicativo web SharePoint

- Se você criar um novo aplicativo web e usar apenas a zona Padrão (opção preferida):

    1. Inicie o **SharePoint Management Shell** e execute o seguinte script:

       ```powershell
       # This script creates a web application and configures the Default zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment. Note that the managed account must exist and it must be a domain account
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       $applicationPoolManagedAccount = "Contoso\spapppool"
            
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = New-SPWebApplication -Name "SharePoint - AAD Proxy" -Port 443 -SecureSocketsLayer -URL $externalUrl -ApplicationPool "SharePoint - AAD Proxy" -ApplicationPoolAccount (Get-SPManagedAccount $applicationPoolManagedAccount) -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Default -Internal
       ```

    2. Abra o site da **Administração Central do SharePoint**.
    1. Em **Configurações do Sistema**, selecione **Configurar Mapeamentos Alternativos de Acesso**. A **caixa de coleta de mapeamento de acesso alternativo** é aberta.
    1. Filtre o display com o novo aplicativo web e confirme se você vê algo assim:

       ![Mapeamentos de acesso alternativo de aplicativos web](./media/application-proxy-integrate-with-sharepoint-server/new-webapp-aam.png)

- Se você estender um aplicativo web existente para uma nova região (caso você não possa usar a região Padrão):

    1. Inicie o SharePoint Management Shell e execute o seguinte script:

       ```powershell
       # This script extends an existing web application to Internet zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment
       $webAppUrl = "http://spsites/"
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = Get-SPWebApplication $webAppUrl
       New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -Identity $wa -SecureSocketsLayer -Zone Extranet -Url $externalUrl -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Extranet -Internal
       ```

    2. Abra o site da **Administração Central do SharePoint**.
    1. Em **Configurações do Sistema**, selecione **Configurar Mapeamentos Alternativos de Acesso**. A **caixa de coleta de mapeamento de acesso alternativo** é aberta.
    1. Filtre o display com o aplicativo web estendido e confirme se você vê algo assim:

        ![Mapeamentos de acesso alternativo sustais de aplicação estendida](./media/application-proxy-integrate-with-sharepoint-server/extend-webapp-aam.png)

### <a name="make-sure-the-sharepoint-web-application-is-running-under-a-domain-account"></a>Certifique-se de que o aplicativo web DoSharePoint está sendo executado em uma conta de domínio

Para identificar a conta que executa o pool de aplicativos do aplicativo web SharePoint e ter certeza de que é uma conta de domínio, siga estas etapas:

1. Abra o site da **Administração Central do SharePoint**.
1. Vá para **Segurança** e selecione **Configurar contas de serviço**.
1. Selecione **o Pool de Aplicativos da Web - Seu Nome de Aplicativo web**.

   ![Opções para configurar uma conta de serviço](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

1. Confirme se **Selecionar uma conta para este componente** retorna uma conta de domínio e lembre-se dela, já que ela será necessária na próxima etapa.

### <a name="make-sure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>Certifique-se de que um certificado HTTPS está configurado para o site IIS da região Extranet

Como a URL interna`https://SharePoint/`usa o protocolo HTTPS ( ), um certificado deve ser definido no site do Internet Information Services (IIS).

1. Abra o console Windows PowerShell.
1. Execute o seguinte script para gerar um certificado auto-assinado e adicione-o à loja MY do computador:

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!IMPORTANT]
   > Os certificados autoassinados são adequados somente para fins de teste. Em ambientes de produção, recomendamos fortemente que você use certificados emitidos por uma autoridade de certificados.

1. Abra o console do Gerenciador de Serviços de Informação da Internet.
1. Expanda o servidor na exibição da árvore, expanda **sites,** selecione o **site SharePoint - AAD Proxy** e selecione **Vinculações**.
1. Selecione **https binding** e selecione **Editar**.
1. No campo de certificado TLS/SSL, escolha o certificado **SharePoint** e selecione **OK**.

Agora você pode acessar o site do SharePoint externamente através do Proxy do aplicativo Azure AD.

## <a name="step-3-configure-kerberos-constrained-delegation"></a>Passo 3: Configurar a delegação restrita kerberos

Inicialmente, os usuários autenticarão no Azure AD e, em seguida, no SharePoint usando o Kerberos através do conector Proxy Azure AD. Para permitir que o conector obtenha um token Kerberos em nome do usuário Azure AD, você deve configurar a Delegação Restrita Kerberos (KCD) com a transição de protocolo. Para saber mais sobre o KCD, consulte a visão geral da Delegação Restrita de [Kerberos](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11)).

### <a name="set-the-spn-for-the-sharepoint-service-account"></a>Defina o SPN para a conta de serviço SharePoint

Neste artigo, a URL `https://sharepoint`interna é , e assim o `HTTP/sharepoint`nome principal do serviço (SPN) é . Você deve substituir esses valores pelos valores que correspondem ao seu ambiente.
Para registrar `HTTP/sharepoint` o SPN na `Contoso\spapppool`conta do pool de aplicativos SharePoint, execute o seguinte comando a partir de um prompt de comando, como administrador do domínio:

`setspn -S HTTP/sharepoint Contoso\spapppool`

O `Setspn` comando procura o SPN antes de aadicione. Se o SPN já existir, você verá um erro **de valor de SPN duplicado.** Nesse caso, considere remover o SPN existente se ele não estiver definido na conta de pool de aplicativos correta. Você pode verificar se o SPN `Setspn` foi adicionado com sucesso executando o comando com a opção -L. Para saber mais sobre esse comando, confira [Setspn](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc731241(v=ws.11)).

### <a name="make-sure-the-connector-is-trusted-for-delegation-to-the-spn-that-was-added-to-the-sharepoint-application-pool-account"></a>Certifique-se de que o conector é confiável para a delegação ao SPN que foi adicionado à conta do pool de aplicativos do SharePoint

Configure a KCD para que o serviço Proxy de Aplicativo do Azure AD seja capaz de delegar identidades do usuário à conta do pool de aplicativos do SharePoint. Configure a KCD habilitando o conector Proxy de Aplicativo para recuperar tíquetes Kerberos para seus usuários que foram autenticados no Azure AD. Em seguida, esse servidor passa o contexto para o aplicativo de destino (SharePoint neste caso).

Para configurar o KCD, siga estas etapas para cada máquina conectora:

1. Faça login em um controlador de domínio como administrador de domínio e, em seguida, abra usuários e computadores do Active Directory.
1. Encontre o computador executando o conector Proxy Azure AD. Neste exemplo, é o próprio servidor SharePoint.
1. Clique duas vezes no computador e selecione a guia **Delegação.**
1. Certifique-se de que as opções de delegação estão definidas para confiar neste computador apenas para a delegação para **os serviços especificados**. Então, selecione **Usar qualquer protocolo de autenticação**.
1. Selecione o botão **Adicionar,** selecionar **Usuários ou Computadores**e localize o pool de aplicativos SharePoint. Por exemplo: `Contoso\spapppool`.
1. Na lista de SPNs, selecione aquele que você criou anteriormente para a conta de serviço.
1. Selecione **OK** e selecione **OK** novamente para salvar suas alterações.
  
   ![Configurações de delegação](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

Agora você está pronto para fazer login no SharePoint usando a URL externa e para autenticar com o Azure.

## <a name="troubleshoot-sign-in-errors"></a>Solucionando problemas de login

Se o login no site não estiver funcionando, você poderá obter mais informações sobre o problema nos registros do Conector: Da máquina que executa o conector, abra o visualizador de eventos, vá para **aplicativos e registros de serviços** > **Microsoft** > **AadApplicationProxy** > **Connector**e inspecione o registro de **administração.**

## <a name="next-steps"></a>Próximas etapas

* [Trabalhando com domínios personalizados no Proxy de Aplicativo do AD do Azure](application-proxy-configure-custom-domain.md)
* [Entenda os conectores proxy do aplicativo Azure AD](application-proxy-connectors.md)
