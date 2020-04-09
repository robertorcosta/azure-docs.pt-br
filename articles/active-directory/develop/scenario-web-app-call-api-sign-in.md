---
title: Remover contas do cache de token na saída de saída - plataforma de identidade da Microsoft | Azure
description: Saiba como remover uma conta do cache de token na saída de saída
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 934b756329065c466f21fca1480247065bdea28b
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881605"
---
# <a name="a-web-app-that-calls-web-apis-remove-accounts-from-the-token-cache-on-global-sign-out"></a>Um aplicativo web que chama APIs da Web: Remova contas do cache de token na saída global

Você aprendeu como adicionar login ao seu aplicativo web no [aplicativo web que faz login nos usuários: Login e login](scenario-web-app-sign-user-sign-in.md).

A saída é diferente para um aplicativo web que chama apis web. Quando o usuário sai do seu aplicativo ou de qualquer aplicativo, você deve remover os tokens associados a esse usuário do cache de tokens.

## <a name="intercept-the-callback-after-single-sign-out"></a>Intercepte a chamada de volta após a única saída

Para limpar a entrada de cache de token associada à conta `logout` que saiu, seu aplicativo pode interceptar o evento após o evento. Os aplicativos da Web armazenam tokens de acesso para cada usuário em um cache de token. Ao interceptar o `logout` retorno pós-chamada, seu aplicativo web pode remover o usuário do cache.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Para ASP.NET Core, o mecanismo de `AddMsal()` interceptação é ilustrado no método [webAppServiceCollectionExtensions.cs#L151-L157](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L157).

A URL de logout que você registrou anteriormente para o seu aplicativo permite que você implemente a saída única. O ponto `logout` final da plataforma de identidade da Microsoft chama sua URL de logout. Essa chamada acontece se a saída de saída começou a partir do seu aplicativo web, ou de outro aplicativo web ou do navegador. Para obter mais informações, consulte [A saída única](v2-protocols-oidc.md#single-sign-out).

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

O código `RemoveAccountAsync` para está disponível em [Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

A amostra ASP.NET não remove contas do cache na saída global.

# <a name="java"></a>[Java](#tab/java)

A amostra Java não remove contas do cache na saída global.

# <a name="python"></a>[Python](#tab/python)

A amostra Python não remove contas do cache na saída global.

---

## <a name="next-steps"></a>Próximas etapas

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Adquira um token para o aplicativo web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Adquira um token para o aplicativo web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Adquira um token para o aplicativo web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Adquira um token para o aplicativo web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=python)

---
