---
title: Adquira um token do cache (MSAL.NET)
titleSuffix: Microsoft identity platform
description: Saiba como adquirir um token de acesso silenciosamente (a partir do cache de token) usando a Biblioteca de Autenticação da Microsoft para .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 90189a1d7fd6421b7a24940e8c6ed615fa0df6d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084843"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>Obtenha um token do cache de tokens usando MSAL.NET

Quando você adquire um token de acesso usando a Microsoft Authentication Library para .NET (MSAL.NET), o token é armazenado em cache. Quando o aplicativo precisa de um token, ele deve primeiro chamar o `AcquireTokenSilent` método para verificar se um token aceitável está no cache. Em muitos casos, é possível adquirir outro token com mais escopos baseados em um token no cache. Também é possível atualizar um token quando ele está chegando perto do vencimento (já que o cache de token também contém um token de atualização).

O padrão recomendado é `AcquireTokenSilent` chamar o método primeiro.  Se `AcquireTokenSilent` falhar, então adquira um token usando outros métodos.

No exemplo a seguir, o aplicativo primeiro tenta adquirir um token do cache de token.  Se `MsalUiRequiredException` uma exceção for lançada, o aplicativo adquire um token interativamente. 

```csharp
AuthenticationResult result = null;
var accounts = await app.GetAccountsAsync();

try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
 // A MsalUiRequiredException happened on AcquireTokenSilent.
 // This indicates you need to call AcquireTokenInteractive to acquire a token
 System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

 try
 {
    result = await app.AcquireTokenInteractive(scopes)
          .ExecuteAsync();
 }
 catch (MsalException msalex)
 {
    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
 }
}
catch (Exception ex)
{
 ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
 return;
}

if (result != null)
{
 string accessToken = result.AccessToken;
 // Use the token
}
```
