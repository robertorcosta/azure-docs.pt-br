---
title: Instalação silenciosa do conector de Proxy do Aplicativo Azure AD | Microsoft Docs
description: Aborda como realizar a instalação autônoma do Conector de roxy de Aplicativo do Azure AD para fornecer acesso remoto seguro aos seus aplicativos locais.
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
ms.date: 01/24/2020
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ae3cd491db03fd036869a8d86aeb646e3175b59
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82609962"
---
# <a name="create-an-unattended-installation-script-for-the-azure-ad-application-proxy-connector"></a>Criar um script de instalação autônoma para o conector de Proxy de Aplicativo do Azure AD

Este tópico ajuda você a criar um script do Windows PowerShell que permite a instalação autônoma e registro para o conector de Proxy de Aplicativo do Azure AD.

Esse recurso é útil quando você deseja:

* Instale o conector em servidores Windows que não têm uma interface do usuário habilitada ou que você não pode acessar com a Área de Trabalho Remota.
* Instalar e registrar vários conectores ao mesmo tempo.
* Integrar a instalação e o registro do conector como parte de outro procedimento.
* Criar uma imagem de servidor padrão que contém bits do conector, mas que não está registrada.

Para que o [conector de proxy de aplicativo](application-proxy-connectors.md) funcione, ele precisa ser registrado com seu diretório do AD do Azure usando um administrador de aplicativos e uma senha. Normalmente, essa informação é inserida durante a instalação do Conector em uma caixa de diálogo pop-up, mas em vez disso, você pode usar o PowerShell para automatizar esse processo.

Há duas etapas para uma instalação autônoma. Primeiro, instale o conector. Segundo, registre o conector com o Azure AD.

## <a name="install-the-connector"></a>Instalar o conector
Use as seguintes etapas para instalar o conector sem registrá-lo:

1. Abra um prompt de comando.
2. Execute o comando a seguir, no qual o /q significa instalação silenciosa. Uma instalação silenciona não solicita que você aceite os termos de licença.

        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="register-the-connector-with-azure-ad"></a>Registrar o conector com o Azure AD
Há dois métodos que você pode usar para registrar o conector:

* Registrar o conector usando um objeto de credencial do Windows PowerShell
* Registrar o conector usando um token criado offline

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Registrar o conector usando um objeto de credencial do Windows PowerShell
1. Crie um objeto de credenciais do Windows PowerShell `$cred` que contém um nome de usuário administrativo e uma senha para seu diretório. Execute o comando a seguir, substituindo * \<nome de usuário\> * e * \<senha\>*:

        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
2. Vá para **C:\Program Files\Microsoft AAD App Proxy Connector** e execute o script a seguir usando o objeto `$cred` que você criou:

        .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature ApplicationProxy

### <a name="register-the-connector-using-a-token-created-offline"></a>Registrar o conector usando um token criado offline
1. Crie um token offline usando a classe AuthenticationContext, usando os valores nesse snippet de código ou cmdlets do PowerShell abaixo:

    **Usando C#:**

        using System;
        using System.Diagnostics;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

        class Program
        {
        #region constants
        /// <summary>
        /// The AAD authentication endpoint uri
        /// </summary>
        static readonly Uri AadAuthenticationEndpoint = new Uri("https://login.microsoftonline.com/common/oauth2/token?api-version=1.0");

        /// <summary>
        /// The application ID of the connector in AAD
        /// </summary>
        static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

        /// <summary>
        /// The reply address of the connector application in AAD
        /// </summary>
        static readonly Uri ConnectorRedirectAddress = new Uri("urn:ietf:wg:oauth:2.0:oob");

        /// <summary>
        /// The AppIdUri of the registration service in AAD
        /// </summary>
        static readonly Uri RegistrationServiceAppIdUri = new Uri("https://proxy.cloudwebappproxy.net/registerapp");

        #endregion

        #region private members
        private string token;
        private string tenantID;
        #endregion

        public void GetAuthenticationToken()
        {
            AuthenticationContext authContext = new AuthenticationContext(AadAuthenticationEndpoint.AbsoluteUri);

            AuthenticationResult authResult = authContext.AcquireToken(RegistrationServiceAppIdUri.AbsoluteUri,
                ConnectorAppId,
                ConnectorRedirectAddress,
                PromptBehavior.Always);

            if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
            {
                Trace.TraceError("Authentication result, token or tenant id returned are null");
                throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
            }

            token = authResult.AccessToken;
            tenantID = authResult.TenantId;
        }

    **Usando o PowerShell:**

        # Locate AzureAD PowerShell Module
        # Change Name of Module to AzureAD after what you have installed
        $AADPoshPath = (Get-InstalledModule -Name AzureAD).InstalledLocation
        # Set Location for ADAL Helper Library
        $ADALPath = $(Get-ChildItem -Path $($AADPoshPath) -Filter Microsoft.IdentityModel.Clients.ActiveDirectory.dll -Recurse ).FullName | Select-Object -Last 1

        # Add ADAL Helper Library
        Add-Type -Path $ADALPath

        #region constants

        # The AAD authentication endpoint uri
        [uri]$AadAuthenticationEndpoint = "https://login.microsoftonline.com/common/oauth2/token?api-version=1.0/"

        # The application ID of the connector in AAD
        [string]$ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489"

        # The reply address of the connector application in AAD
        [uri]$ConnectorRedirectAddress = "urn:ietf:wg:oauth:2.0:oob"

        # The AppIdUri of the registration service in AAD
        [uri]$RegistrationServiceAppIdUri = "https://proxy.cloudwebappproxy.net/registerapp"

        #endregion

        #region GetAuthenticationToken

        # Set AuthN context
        $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $AadAuthenticationEndpoint

        # Build platform parameters
        $promptBehavior = [Microsoft.IdentityModel.Clients.ActiveDirectory.PromptBehavior]::Always
        $platformParam = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList $promptBehavior

        # Do AuthN and get token
        $authResult = $authContext.AcquireTokenAsync($RegistrationServiceAppIdUri.AbsoluteUri, $ConnectorAppId, $ConnectorRedirectAddress, $platformParam).Result

        # Check AuthN result
        If (($authResult) -and ($authResult.AccessToken) -and ($authResult.TenantId) ) {
        $token = $authResult.AccessToken
        $tenantId = $authResult.TenantId
        }
        Else {
        Write-Output "Authentication result, token or tenant id returned are null"
        }

        #endregion

2. Quando tiver o token, crie uma SecureString usando o token:

   `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`

3. Execute o seguinte comando do Windows PowerShell, substituindo \<GUID de locatário\> com sua ID de diretório:

   `.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID> -Feature ApplicationProxy`

## <a name="next-steps"></a>Próximas etapas
* [Publicar aplicativos usando seu próprio nome de domínio](application-proxy-configure-custom-domain.md)
* [Habilitar o logon único](application-proxy-configure-single-sign-on-with-kcd.md)
* [Solucionar problemas que surgirem com o Proxy de Aplicativo](application-proxy-troubleshoot.md)
