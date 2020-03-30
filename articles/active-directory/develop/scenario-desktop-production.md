---
title: Mover aplicativo de desktop chamando APIs web para produção - plataforma de identidade Microsoft | Azure
description: Saiba como mover um aplicativo de desktop que chama APIs da Web para a produção
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: c8a9cf0c05d8af14d52bb1efb536dc8bbe7db84d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262562"
---
# <a name="desktop-app-that-calls-web-apis-move-to-production"></a>Aplicativo de desktop que chama APIs da Web: Mude para a produção

Neste artigo, você aprende como mover seu aplicativo de desktop que chama APIs da Web para a produção.

## <a name="handle-errors-in-desktop-applications"></a>Lidar com erros em aplicativos de desktop

Nos diferentes fluxos, você aprendeu a lidar com os erros para os fluxos silenciosos, como mostrado nos trechos de código. Você também viu que há casos em que a interação é necessária, como no consentimento incremental e no acesso condicional.

## <a name="have-the-user-consent-upfront-for-several-resources"></a>Tenha o consentimento do usuário antecipadamente para vários recursos

> [!NOTE]
> Obter consentimento para vários recursos funciona para a plataforma de identidade Microsoft, mas não para o Azure Active Directory (Azure AD) B2C. O Azure AD B2C suporta apenas o consentimento do administrador, não o consentimento do usuário.

Você não pode obter um token para vários recursos ao mesmo tempo com o ponto final da plataforma de identidade Microsoft (v2.0). O `scopes` parâmetro pode conter escopos para apenas um único recurso. Você pode garantir que o usuário pré-consentir `extraScopesToConsent` com vários recursos usando o parâmetro.

Por exemplo, você pode ter dois recursos que têm dois escopos cada:

- `https://mytenant.onmicrosoft.com/customerapi`com os `customer.read` escopos e`customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi`com os `vendor.read` escopos e`vendor.write`

Neste exemplo, use `.WithAdditionalPromptToConsent` o modificador `extraScopesToConsent` que tem o parâmetro.

Por exemplo:

### <a name="in-msalnet"></a>Em MSAL.NET

```csharp
string[] scopesForCustomerApi = new string[]
{
  "https://mytenant.onmicrosoft.com/customerapi/customer.read",
  "https://mytenant.onmicrosoft.com/customerapi/customer.write"
};
string[] scopesForVendorApi = new string[]
{
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"
};

var accounts = await app.GetAccountsAsync();
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithAccount(accounts.FirstOrDefault())
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

### <a name="in-msal-for-ios-and-macos"></a>Em MSAL para iOS e macOS

Objective-C:

```objc
NSArray *scopesForCustomerApi = @[@"https://mytenant.onmicrosoft.com/customerapi/customer.read",
                                @"https://mytenant.onmicrosoft.com/customerapi/customer.write"];

NSArray *scopesForVendorApi = @[@"https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                              @"https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopesForCustomerApi webviewParameters:[MSALWebviewParameters new]];
interactiveParams.extraScopesToConsent = scopesForVendorApi;
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) { /* handle result */ }];
```

Swift:

```swift
let scopesForCustomerApi = ["https://mytenant.onmicrosoft.com/customerapi/customer.read",
                            "https://mytenant.onmicrosoft.com/customerapi/customer.write"]

let scopesForVendorApi = ["https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                          "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopesForCustomerApi, webviewParameters: MSALWebviewParameters())
interactiveParameters.extraScopesToConsent = scopesForVendorApi
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in /* handle result */ })
```

Esta chamada lhe dá um token de acesso para a primeira API web.

Quando precisar chamar a segunda API `AcquireTokenSilent` web, ligue para a API.

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsent-each-time-the-app-runs"></a>A conta pessoal da Microsoft requer reconsentimento cada vez que o aplicativo é executado

Para usuários de conta pessoal da Microsoft, solicitar o consentimento em cada chamada de cliente nativo (desktop ou aplicativo móvel) para autorizar é o comportamento pretendido. A identidade do cliente nativo é inerentemente insegura, o que é contrário à identidade confidencial do aplicativo do cliente. Aplicativos confidenciais de clientes trocam um segredo com a plataforma Microsoft Identity para provar sua identidade. A plataforma de identidade da Microsoft optou por mitigar essa insegurança para os serviços ao consumidor, solicitando ao usuário o consentimento cada vez que o aplicativo é autorizado.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
