---
title: Instanciar um aplicativo cliente confidencial (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Aprenda a instanciar um aplicativo cliente confidencial com opções de configuração usando a Biblioteca de Autenticação Microsoft para .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1a520c5a1002e401f880fba84f8fc02a0a678133
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084743"
---
# <a name="instantiate-a-confidential-client-application-with-configuration-options-using-msalnet"></a>Instanciar um aplicativo cliente confidencial com opções de configuração usando MSAL.NET

Este artigo descreve como instanciar um [aplicativo cliente confidencial](msal-client-applications.md) usando a Microsoft Authentication Library for .NET (MSAL.NET).  O aplicativo é instanciado com opções de configuração definidas em um arquivo de configurações.

Antes de inicializar um aplicativo, primeiro você precisa [registrá-lo](quickstart-register-app.md) para que seu aplicativo possa ser integrado à plataforma de identidade da Microsoft. Após o cadastro, você pode precisar das seguintes informações (que podem ser encontradas no portal azure):

- O ID do cliente (uma string representando um GUID)
- O URL do provedor de identidade (chamado de instância) e o público de login do seu aplicativo. Esses dois parâmetros são coletivamente conhecidos como autoridade.
- O ID do inquilino se você estiver escrevendo uma linha de aplicativo de negócios exclusivamente para sua organização (também chamado de aplicativo de inquilino único).
- O segredo do aplicativo (seqüência secreta do cliente) ou certificado (do tipo X509Certificate2) se for um aplicativo cliente confidencial.
- Para aplicativos web e, às vezes, para aplicativos públicos de clientes (em particular quando seu aplicativo precisa usar uma corretora), você também terá definido o redirectUri onde o provedor de identidade entrará em contato com seu aplicativo com os tokens de segurança.

## <a name="configure-the-application-from-the-config-file"></a>Configure o aplicativo a partir do arquivo de configuração
O nome das propriedades das opções em MSAL.NET corresponde `AzureADOptions` ao nome das propriedades do in ASP.NET Core, então você não precisa escrever nenhum código de cola.

Uma configuração de aplicativo ASP.NET Core é descrita em um arquivo *appsettings.json:*

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "[Enter the domain of your tenant, e.g. contoso.onmicrosoft.com]",
    "TenantId": "[Enter 'common', or 'organizations' or the Tenant Id (Obtained from the Azure portal. Select 'Endpoints' from the 'App registrations' blade and use the GUID in any of the URLs), e.g. da41245a5-11b3-996c-00a8-4d99re19f292]",
    "ClientId": "[Enter the Client Id (Application ID obtained from the Azure portal), e.g. ba74781c2-53c2-442a-97c2-3d60re42f403]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc",

    "ClientSecret": "[Copy the client secret added to the app from the Azure portal]"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

A partir de MSAL.NET v3.x, você pode configurar seu aplicativo cliente confidencial a partir do arquivo config.

Na classe em que você deseja configurar e instanciar sua aplicação, você precisa declarar um `ConfidentialClientApplicationOptions` objeto.  Vincule a configuração lida da origem (incluindo o arquivo appconfig.json) `IConfigurationRoot.Bind()` à instância das opções de aplicativo, usando o método do [pacote microsoft.extensions.configuration.Binder nuget:](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder)

```csharp
using Microsoft.Identity.Client;

private ConfidentialClientApplicationOptions _applicationOptions;
_applicationOptions = new ConfidentialClientApplicationOptions();
configuration.Bind("AzureAD", _applicationOptions);
```

Isso permite que o conteúdo da seção "AzureAD" do arquivo *appsettings.json* seja vinculado às propriedades correspondentes do `ConfidentialClientApplicationOptions` objeto.  Em seguida, `ConfidentialClientApplication` construa um objeto:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
        .Build();
```

## <a name="add-runtime-configuration"></a>Adicionar configuração de tempo de execução
Em um aplicativo cliente confidencial, você geralmente tem um cache por usuário. Portanto, você precisará obter o cache associado ao usuário e informar o construtor do aplicativo que deseja usá-lo. Da mesma forma, você pode ter um URI de redirecionamento dinamicamente computado. Neste caso, o código é o seguinte:

```csharp
IConfidentialClientApplication app;
var request = httpContext.Request;
var currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, _azureAdOptions.CallbackPath ?? string.Empty);
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
       .WithRedirectUri(currentUri)
       .Build();
TokenCache userTokenCache = _tokenCacheProvider.SerializeCache(app.UserTokenCache,httpContext, claimsPrincipal);
```

