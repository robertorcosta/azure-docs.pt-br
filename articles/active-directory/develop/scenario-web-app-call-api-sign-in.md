---
title: Remover contas do cache de token na saída-plataforma de identidade da Microsoft | Azure
description: Saiba como remover uma conta do cache de token ao sair
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: ea18538662dc63876a50f52e9e6a8b3fffb3b35a
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2020
ms.locfileid: "76758863"
---
# <a name="a-web-app-that-calls-web-apis-remove-accounts-from-the-token-cache-on-global-sign-out"></a>Um aplicativo Web que chama APIs da Web: remover contas do cache de token na saída global

Você aprendeu a adicionar a entrada ao seu aplicativo Web no [aplicativo Web que conecta os usuários: entrar e sair](scenario-web-app-sign-user-sign-in.md).

A saída é diferente para um aplicativo Web que chama APIs da Web. Quando o usuário sai do seu aplicativo ou de qualquer aplicativo, você deve remover os tokens associados a esse usuário do cache de token.

## <a name="intercept-the-callback-after-single-sign-out"></a>Interceptar o retorno de chamada após a saída única

Para limpar a entrada de cache de token associada à conta que foi desconectada, seu aplicativo pode interceptar o evento de `logout` após. Os aplicativos Web armazenam tokens de acesso para cada usuário em um cache de token. Ao interceptar o retorno de chamada após `logout`, seu aplicativo Web pode remover o usuário do cache.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Por ASP.NET Core, o mecanismo de interceptação é ilustrado no método `AddMsal()` de [WebAppServiceCollectionExtensions. cs # L151-L157](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L157).

A URL de logout que você registrou anteriormente para seu aplicativo permite que você implemente o logout único. O ponto de extremidade `logout` da plataforma de identidade da Microsoft chama a URL de logout. Essa chamada ocorrerá se a saída for iniciada a partir de seu aplicativo Web ou de outro aplicativo Web ou navegador. Para obter mais informações, consulte [logout único](v2-protocols-oidc.md#single-sign-out).

```csharp
public static class WebAppServiceCollectionExtensions
{
 public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
 {
  // Code omitted here

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Code omitted here

   // Handling the sign-out: Remove the account from MSAL.NET cache.
   options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
   {
    // Remove the account from MSAL.NET token cache.
    var tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
    await tokenAcquisition.RemoveAccountAsync(context).ConfigureAwait(false);
   };
  });
  return services;
 }
}
```

O código para `RemoveAccountAsync` está disponível em [Microsoft. Identity. Web/TokenAcquisition. cs # L264-L288](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288).

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

O exemplo ASP.NET não remove contas do cache na saída global.

# <a name="javatabjava"></a>[Java](#tab/java)

O exemplo de Java não remove contas do cache na saída global.

# <a name="pythontabpython"></a>[Python](#tab/python)

O exemplo de Python não remove contas do cache na saída global.

---

## <a name="next-steps"></a>Próximos passos

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Adquirir um token para o aplicativo Web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnetcore)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Adquirir um token para o aplicativo Web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnet)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Adquirir um token para o aplicativo Web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=java)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Adquirir um token para o aplicativo Web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=python)

---
