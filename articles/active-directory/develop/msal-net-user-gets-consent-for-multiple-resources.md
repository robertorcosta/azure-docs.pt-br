---
title: Obter consentimento para vários recursos (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Saiba como um usuário pode obter pré-consentimento para vários recursos usando a Biblioteca de Autenticação microsoft para .NET (MSAL.NET).
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
ms.openlocfilehash: 94c9a2b6a46262ad293da9ca3ba493d6f898c870
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085848"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>O usuário obtém consentimento para vários recursos usando MSAL.NET
O ponto final da plataforma de identidade da Microsoft não permite que você obtenha um token para vários recursos ao mesmo tempo. Ao usar a Biblioteca de Autenticação microsoft para .NET (MSAL.NET), o parâmetro de escopos no método de token de aquisição deve conter apenas escopos para um único recurso. No entanto, você pode pré-consentir com vários recursos `.WithExtraScopeToConsent` antecipadamente especificando escopos adicionais usando o método builder.

> [!NOTE]
> Obter consentimento para vários recursos funciona para a plataforma de identidade Microsoft, mas não para o Azure AD B2C. O Azure AD B2C suporta apenas o consentimento do administrador, não o consentimento do usuário.

Por exemplo, se você tem dois recursos que têm 2 escopos cada:

- https:\//mytenant.onmicrosoft.com/customerapi (com `customer.read` 2 `customer.write`escopos e )
- https:\//mytenant.onmicrosoft.com/vendorapi (com `vendor.read` 2 `vendor.write`escopos e )

Você deve `.WithExtraScopeToConsent` usar o modificador que tem o parâmetro *extraScopesToConsent,* conforme mostrado no exemplo a seguir:

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

Isso lhe dará um token de acesso para a primeira API web. Então, quando você precisar acessar a segunda API da Web, você pode adquirir silenciosamente o token do cache de token:

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```
