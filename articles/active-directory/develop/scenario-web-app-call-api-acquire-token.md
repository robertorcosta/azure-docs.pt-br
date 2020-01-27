---
title: Obter um token em um aplicativo Web que chama APIs da Web-plataforma de identidade da Microsoft | Azure
description: Saiba como adquirir um token para um aplicativo Web que chama APIs da Web
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: abf7d800eda376c21dfdd672032ddb65e27355be
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759067"
---
# <a name="a-web-app-that-calls-web-apis-acquire-a-token-for-the-app"></a>Um aplicativo Web que chama APIs da Web: adquirir um token para o aplicativo

Você criou o objeto de aplicativo cliente. Agora, você o usará para adquirir um token para chamar uma API da Web. No ASP.NET ou ASP.NET Core, a chamada de uma API da Web é feita no controlador:

- Obtenha um token para a API Web usando o cache de token. Para obter esse token, chame o método `AcquireTokenSilent`.
- Chame a API protegida, passando o token de acesso para ele como um parâmetro.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Os métodos do controlador são protegidos por um atributo `[Authorize]` que força os usuários que estão sendo autenticados a usar o aplicativo Web. Este é o código que chama Microsoft Graph:

```csharp
[Authorize]
public class HomeController : Controller
{
 readonly ITokenAcquisition tokenAcquisition;

 public HomeController(ITokenAcquisition tokenAcquisition)
 {
  this.tokenAcquisition = tokenAcquisition;
 }

 // Code for the controller actions (see code below)

}
```

O serviço de `ITokenAcquisition` é injetado por ASP.NET usando injeção de dependência.

Aqui está o código simplificado para a ação do `HomeController`, que obtém um token para chamar Microsoft Graph:

```csharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenOnBehalfOfUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
 string json = await client.GetStringAsync(url);
}
```

Para entender melhor o código necessário para esse cenário, consulte a etapa 2 ([2-1-chamadas de aplicativo Web Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)) do tutorial [MS-Identity-aspnetcore-webapp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) .

Há outras variações complexas, como:

- Chamar várias APIs.
- Processando o consentimento incremental e o acesso condicional.

Essas etapas avançadas são abordadas no capítulo 3 do tutorial [3-webapp-multi-APIs](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/3-WebApp-multi-APIs) .

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

O código para ASP.NET é semelhante ao código mostrado para ASP.NET Core:

- Uma ação do controlador, protegida por um atributo [Authorize], extrai a ID do locatário e a ID de usuário do membro de `ClaimsPrincipal` do controlador. (ASP.NET usa `HttpContext.User`.)
- A partir daí, ele cria um objeto de `IConfidentialClientApplication` MSAL.NET.
- Por fim, ele chama o método `AcquireTokenSilent` do aplicativo cliente confidencial.

# <a name="javatabjava"></a>[Java](#tab/java)

No exemplo de Java, o código que chama uma API está no método getUsersFromGraph em [AuthPageController. java # L62](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L62).

O método tenta chamar `getAuthResultBySilentFlow`. Se o usuário precisar dar consentimento a mais escopos, o código processará o objeto `MsalInteractionRequiredException` para desafiar o usuário.

```java
@RequestMapping("/msal4jsample/graph/me")
public ModelAndView getUserFromGraph(HttpServletRequest httpRequest, HttpServletResponse response)
        throws Throwable {

    IAuthenticationResult result;
    ModelAndView mav;
    try {
        result = authHelper.getAuthResultBySilentFlow(httpRequest, response);
    } catch (ExecutionException e) {
        if (e.getCause() instanceof MsalInteractionRequiredException) {

            // If the silent call returns MsalInteractionRequired, redirect to authorization endpoint
            // so user can consent to new scopes.
            String state = UUID.randomUUID().toString();
            String nonce = UUID.randomUUID().toString();

            SessionManagementHelper.storeStateAndNonceInSession(httpRequest.getSession(), state, nonce);

            String authorizationCodeUrl = authHelper.getAuthorizationCodeUrl(
                    httpRequest.getParameter("claims"),
                    "User.Read",
                    authHelper.getRedirectUriGraph(),
                    state,
                    nonce);

            return new ModelAndView("redirect:" + authorizationCodeUrl);
        } else {

            mav = new ModelAndView("error");
            mav.addObject("error", e);
            return mav;
        }
    }

    if (result == null) {
        mav = new ModelAndView("error");
        mav.addObject("error", new Exception("AuthenticationResult not found in session."));
    } else {
        mav = new ModelAndView("auth_page");
        setAccountInfo(mav, httpRequest);

        try {
            mav.addObject("userInfo", getUserInfoFromGraph(result.accessToken()));

            return mav;
        } catch (Exception e) {
            mav = new ModelAndView("error");
            mav.addObject("error", e);
        }
    }
    return mav;
}
// Code omitted here
```

# <a name="pythontabpython"></a>[Python](#tab/python)

No exemplo de Python, o código que chama Microsoft Graph está em [app. py # L53-L62](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L53-L62).

O código tenta obter um token do cache de token. Em seguida, depois de definir o cabeçalho de autorização, ele chama a API da Web. Se não for possível obter um token, ele será autenticado pelo usuário novamente.

```python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service.
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Chamar uma API da Web](scenario-web-app-call-api-call-api.md)
