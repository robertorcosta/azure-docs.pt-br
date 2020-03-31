---
title: Verifique escopos e funções de aplicativos protegidos da API da Web | Azure
titleSuffix: Microsoft identity platform
description: Aprenda a construir uma API web protegida e configure o código do seu aplicativo.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 816a9620a3486b534f9293084b7c4f5b4f748033
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76768126"
---
# <a name="protected-web-api-verify-scopes-and-app-roles"></a>API web protegida: verifique escopos e funções de aplicativos

Este artigo descreve como você pode adicionar autorização à sua API web. Essa proteção garante que a API seja chamada apenas por:

- Aplicativos em nome de usuários que têm os escopos certos.
- Aplicativos Daemon que têm as funções de aplicativo certas.

> [!NOTE]
> Os trechos de código deste artigo são extraídos das seguintes amostras, que são totalmente funcionais:
>
> - [ASP.NET tutorial incremental da API da Web](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs#L37) no GitHub
> - [ASP.NET amostra de API da Web](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/dfd0115533d5a230baff6a3259c76cf117568bd9/TodoListService/Controllers/TodoListController.cs#L48)

Para proteger uma API web ASP.NET ou `[Authorize]` ASP.NET Core, você deve adicionar o atributo a um dos seguintes itens:

- O próprio controlador se você quiser que todas as ações do controlador sejam protegidas
- A ação do controlador individual para sua API

```csharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

Mas essa proteção não é suficiente. Ele garante apenas que ASP.NET e ASP.NET Core validem o token. Sua API precisa verificar se o token usado para chamar a API é solicitado com as reivindicações esperadas. Essas alegações, em particular, precisam de verificação:

- Os *escopos* se a API for chamada em nome de um usuário.
- O *aplicativo funde* se a API puder ser chamada de um aplicativo daemon.

## <a name="verify-scopes-in-apis-called-on-behalf-of-users"></a>Verificar escopos em APIs chamadas em nome dos usuários

Se um aplicativo cliente chamar sua API em nome de um usuário, a API precisa solicitar um token portador que tenha escopos específicos para a API. Para obter mais informações, consulte [configuração de código | Token do portador](scenario-protected-web-api-app-configuration.md#bearer-token).

```csharp
[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    const string scopeRequiredByAPI = "access_as_user";

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
        VerifyUserHasAnyAcceptedScope(scopeRequiredByAPI);
        // Do the work and return the result.
        ...
    }
...
}
```

O `VerifyUserHasAnyAcceptedScope` método faz algo como as seguintes etapas:

- Verifique se há uma `http://schemas.microsoft.com/identity/claims/scope` `scp`reclamação nomeada ou .
- Verifique se a reivindicação tem um valor que contém o escopo esperado pela API.

```csharp
    /// <summary>
    /// When applied to a <see cref="HttpContext"/>, verifies that the user authenticated in the
    /// web API has any of the accepted scopes.
    /// If the authenticated user doesn't have any of these <paramref name="acceptedScopes"/>, the
    /// method throws an HTTP Unauthorized error with a message noting which scopes are expected in the token.
    /// </summary>
    /// <param name="acceptedScopes">Scopes accepted by this API</param>
    /// <exception cref="HttpRequestException"/> with a <see cref="HttpResponse.StatusCode"/> set to 
    /// <see cref="HttpStatusCode.Unauthorized"/>
    public static void VerifyUserHasAnyAcceptedScope(this HttpContext context,
                                                     params string[] acceptedScopes)
    {
        if (acceptedScopes == null)
        {
            throw new ArgumentNullException(nameof(acceptedScopes));
        }
        Claim scopeClaim = HttpContext?.User
                                      ?.FindFirst("http://schemas.microsoft.com/identity/claims/scope");
        if (scopeClaim == null || !scopeClaim.Value.Split(' ').Intersect(acceptedScopes).Any())
        {
            context.Response.StatusCode = (int)HttpStatusCode.Unauthorized;
            string message = $"The 'scope' claim does not contain scopes '{string.Join(",", acceptedScopes)}' or was not found";
            throw new HttpRequestException(message);
        }
    }
```

O [código de amostra](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/Microsoft.Identity.Web/Resource/ScopesRequiredByWebAPIExtension.cs#L47) anterior é para ASP.NET Core. Para ASP.NET, `HttpContext.User` basta `ClaimsPrincipal.Current`substituir e substituir `"http://schemas.microsoft.com/identity/claims/scope"` `"scp"`o tipo de solicitação por . Veja também o trecho de código mais tarde neste artigo.

## <a name="verify-app-roles-in-apis-called-by-daemon-apps"></a>Verifique as funções do aplicativo em APIs chamadas por aplicativos daemon

Se sua API web for chamada por um [aplicativo daemon,](scenario-daemon-overview.md)esse aplicativo deve exigir uma permissão de aplicativo para sua API web. Como mostrado em [Expor permissões de aplicativos (funções de aplicativo)](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#exposing-application-permissions-app-roles), sua API expõe tais permissões. Um exemplo `access_as_application` é a função do aplicativo.

Agora você precisa que sua API verifique se `roles` o token que recebe contém a reclamação e que essa reclamação tem o valor esperado. O código de verificação é semelhante ao código que verifica permissões delegadas, exceto que o controlador testa as funções em vez de escopos:

```csharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
        ValidateAppRole("access_as_application");
        ...
    }
```

O `ValidateAppRole` método pode ser mais ou menos assim:

```csharp
private void ValidateAppRole(string appRole)
{
    //
    // The `role` claim tells you what permissions the client application has in the service.
    // In this case, we look for a `role` value of `access_as_application`.
    //
    Claim roleClaim = ClaimsPrincipal.Current.FindFirst("roles");
    if (roleClaim == null || !roleClaim.Value.Split(' ').Contains(appRole))
    {
        throw new HttpResponseException(new HttpResponseMessage
        { StatusCode = HttpStatusCode.Unauthorized,
            ReasonPhrase = $"The 'roles' claim does not contain '{appRole}' or was not found"
        });
    }
}
}
```

Desta vez, o trecho do código é para ASP.NET. Para ASP.NET Core, `ClaimsPrincipal.Current` `HttpContext.User`basta substituir `"roles"` e substituir `"http://schemas.microsoft.com/identity/claims/roles"`o nome da reivindicação por . Veja também o trecho de código no início deste artigo.

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Aceitar tokens somente de aplicativos se a API web deve ser chamada apenas por aplicativos daemon

Os usuários também podem usar reivindicações de funções em padrões de atribuição de usuário, como mostrado em [Como: Adicionar funções de aplicativo em seu aplicativo e recebê-las no token](howto-add-app-roles-in-azure-ad-apps.md). Se as funções forem atribuídas a ambos, a verificação de funções permitirá que os aplicativos entrem como usuários e usuários para fazer login como aplicativos. Recomendamos que você declare diferentes funções para usuários e aplicativos para evitar essa confusão.

Se você quiser apenas aplicativos daemon para chamar sua API web, adicione a condição de que o token seja um token somente para aplicativos quando você validar a função do aplicativo.

```csharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

Verificar a condição inversa permite que apenas aplicativos que assinam um usuário liguem para sua API.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Mover para ambiente de produção](scenario-protected-web-api-production.md)
