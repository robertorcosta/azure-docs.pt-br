---
title: Obtenha um token em um aplicativo web que chama APIs web - plataforma de identidade da Microsoft | Azure
description: Saiba como adquirir um token para um aplicativo web que chama APIs da Web
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759067"
---
# <a name="a-web-app-that-calls-web-apis-acquire-a-token-for-the-app"></a>Um aplicativo web que chama APIs da Web: Adquira um token para o aplicativo

Você construiu seu objeto de aplicação cliente. Agora, você vai usá-lo para adquirir um token para chamar uma API web. Em ASP.NET ou ASP.NET Core, chamar uma API web é feito no controlador:

- Obtenha um token para a API da Web usando o cache de token. Para obter este token, `AcquireTokenSilent` você chama o método.
- Ligue para a API protegida, passando o token de acesso para ele como parâmetro.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Os métodos do controlador `[Authorize]` são protegidos por um atributo que força os usuários a serem autenticados para usar o aplicativo web. Aqui está o código que chama Microsoft Graph:

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

O `ITokenAcquisition` serviço é injetado por ASP.NET usando injeção de dependência.

Aqui está o código simplificado `HomeController`para a ação do , que recebe um token para chamar Microsoft Graph:

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

Para entender melhor o código necessário para este cenário, consulte a etapa 2 ([2-1-Web App Calls Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)) do tutorial [ms-identity-aspnetcore-webapp-tutorial.](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial)

Existem outras variações complexas, tais como:

- Chamando várias APIs.
- Processamento de consentimento incremental e acesso condicional.

Essas etapas avançadas são abordadas no capítulo 3 do tutorial [3-WebApp-multi-APIs.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/3-WebApp-multi-APIs)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

O código para ASP.NET é semelhante ao código mostrado para ASP.NET Core:

- Uma ação do controlador, protegida por um atributo [Autorizar], extrai o ID do inquilino e o ID do usuário do `ClaimsPrincipal` membro do controlador. (ASP.NET `HttpContext.User`usa .)
- A partir daí, ele `IConfidentialClientApplication` constrói um objeto MSAL.NET.
- Finalmente, ele `AcquireTokenSilent` chama o método do aplicativo cliente confidencial.

# <a name="java"></a>[Java](#tab/java)

Na amostra Java, o código que chama uma API está no método getUsersFromGraph em [AuthPageController.java#L62](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L62).

O método tenta `getAuthResultBySilentFlow`chamar . Se o usuário precisar consentir com mais `MsalInteractionRequiredException` escopos, o código processa o objeto para desafiar o usuário.

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

# <a name="python"></a>[Python](#tab/python)

Na amostra Python, o código que chama Microsoft Graph está em [app.py#L53-L62](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L53-L62).

O código tenta obter um token do cache de token. Em seguida, depois de definir o cabeçalho de autorização, ele chama a API web. Se ele não conseguir um token, ele assina o usuário novamente.

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

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Chamar uma API da Web](scenario-web-app-call-api-call-api.md)
