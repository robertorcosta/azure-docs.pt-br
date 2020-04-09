---
title: Escreva um aplicativo web que inscreve usuários de entrada/saída - plataforma de identidade da Microsoft | Azure
description: Saiba como criar um aplicativo web que faça inscrições para usuários
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 317ca55adb9f680dc93343a185395abad08889da
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881308"
---
# <a name="web-app-that-signs-in-users-sign-in-and-sign-out"></a>Aplicativo web que assina nos usuários: Login e login

Saiba como adicionar login ao código do seu aplicativo web que faz login nos usuários. Então, aprenda a deixá-los sair.

## <a name="sign-in"></a>Conexão

O login consiste em duas partes:

- O botão de login na página HTML
- A ação de login no code-behind no controlador

### <a name="sign-in-button"></a>Botão de login

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Em ASP.NET Core, o botão de `Views\Shared\_LoginPartial.cshtml`login é exposto em . É exibido apenas quando não há conta autenticada. Ou seja, é exibido quando o usuário ainda não fez o assinatura ou assinou a saída.

```html
@using Microsoft.Identity.Web
@if (User.Identity.IsAuthenticated)
{
 // Code omitted code for clarity
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignIn">Sign in</a></li>
    </ul>
}
```

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Em ASP.NET MVC, o botão de `Views\Shared\_LoginPartial.cshtml`saída é exposto em . É exibido apenas quando há uma conta autenticada. Ou seja, é exibido quando o usuário já fez o adesão.

```html
@if (Request.IsAuthenticated)
{
 // Code omitted code for clarity
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

# <a name="java"></a>[Java](#tab/java)

Em nosso quickstart Java, o botão de login está localizado no arquivo [main/resources/templates/index.html.](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/resources/templates/index.html)

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>HomePage</title>
</head>
<body>
<h3>Home Page</h3>

<form action="/msal4jsample/secure/aad">
    <input type="submit" value="Login">
</form>

</body>
</html>
```

# <a name="python"></a>[Python](#tab/python)

No quickstart python, não há botão de login. O code-behind solicita automaticamente ao usuário o login quando ele está chegando à raiz do aplicativo web. Ver [app.py#L14-L18](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app.py#L14-L18).

```Python
@app.route("/")
def index():
    if not session.get("user"):
        return redirect(url_for("login"))
    return render_template('index.html', user=session["user"])
```

---

### <a name="signin-action-of-the-controller"></a>`SignIn`ação do controlador

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Em ASP.NET, selecionar o botão **de login** no `SignIn` aplicativo web `AccountController` aciona a ação no controlador. Nas versões anteriores do `Account` ASP.NET modelos principais, o controlador foi incorporado com o aplicativo web. Isso não é mais o caso porque o controlador agora faz parte da estrutura ASP.NET Core.

O código `AccountController` para está disponível no repositório ASP.NET Core em [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). O controle da conta desafia o usuário redirecionando para o ponto final da plataforma de identidade da Microsoft. Para obter detalhes, consulte o método [SignIn](https://github.com/aspnet/AspNetCore/blob/f3e6b74623d42d5164fd5f97a288792c8ad877b6/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs#L23-L31) fornecido como parte de ASP.NET Core.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Em ASP.NET, a assinatura é `SignOut()` acionada a partir do método em um controlador (por exemplo, [AccountController.cs#L16-L23](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L16-L23)). Este método não faz parte do quadro ASP.NET (ao contrário do que acontece em ASP.NET Núcleo). Ele envia um desafio de login OpenID depois de propor um URI de redirecionamento.

```csharp
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

# <a name="java"></a>[Java](#tab/java)

Em Java, a saída de saída é `logout` feita ligando diretamente `post_logout_redirect_uri` para o ponto final da plataforma de identidade da Microsoft e fornecendo o valor. Para obter detalhes, consulte [AuthPageController.java#L30-L48](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L30-L48).

```Java
@Controller
public class AuthPageController {

    @Autowired
    AuthHelper authHelper;

    @RequestMapping("/msal4jsample")
    public String homepage(){
        return "index";
    }

    @RequestMapping("/msal4jsample/secure/aad")
    public ModelAndView securePage(HttpServletRequest httpRequest) throws ParseException {
        ModelAndView mav = new ModelAndView("auth_page");

        setAccountInfo(mav, httpRequest);

        return mav;
    }

    // More code omitted for simplicity
```

# <a name="python"></a>[Python](#tab/python)

Ao contrário de outras plataformas, o MSAL Python se preocupa em deixar o usuário entrar na página de login. Ver [app.py#L20-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L20-L28).

```Python
@app.route("/login")
def login():
    session["state"] = str(uuid.uuid4())
    auth_url = _build_msal_app().get_authorization_request_url(
        app_config.SCOPE,  # Technically we can use an empty list [] to just sign in
                           # Here we choose to also collect user consent up front
        state=session["state"],
        redirect_uri=url_for("authorized", _external=True))
    return "<a href='%s'>Login with Microsoft Identity</a>" % auth_url
```

O `_build_msal_app()` método é definido em [app.py#L81-L88](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L81-L88) da seguinte forma:

```Python
def _load_cache():
    cache = msal.SerializableTokenCache()
    if session.get("token_cache"):
        cache.deserialize(session["token_cache"])
    return cache

def _save_cache(cache):
    if cache.has_state_changed:
        session["token_cache"] = cache.serialize()

def _build_msal_app(cache=None):
    return msal.ConfidentialClientApplication(
        app_config.CLIENT_ID, authority=app_config.AUTHORITY,
        client_credential=app_config.CLIENT_SECRET, token_cache=cache)

def _get_token_from_cache(scope=None):
    cache = _load_cache()  # This web app maintains one cache per session
    cca = _build_msal_app(cache)
    accounts = cca.get_accounts()
    if accounts:  # So all accounts belong to the current signed-in user
        result = cca.acquire_token_silent(scope, account=accounts[0])
        _save_cache(cache)
        return result

```

---

Depois que o usuário fizer login no seu aplicativo, você vai querer habilitá-lo a sair.

## <a name="sign-out"></a>Sair

Sair de um aplicativo web envolve mais do que remover as informações sobre a conta de login do estado do aplicativo web.
O aplicativo web também deve redirecionar o `logout` usuário para o ponto final da plataforma de identidade da Microsoft para sair. 

Quando seu aplicativo web redireciona `logout` o usuário para o ponto final, este ponto final limpa a sessão do usuário do navegador. Se o seu aplicativo não `logout` for para o ponto final, o usuário irá reautenticar para o seu aplicativo sem inserir suas credenciais novamente. A razão é que eles terão uma sessão de login única válida com o ponto final da plataforma de identidade da Microsoft.

Para saber mais, consulte a seção Enviar uma solicitação [de saída](v2-protocols-oidc.md#send-a-sign-out-request) na plataforma de identidade da Microsoft e na documentação do protocolo [OpenID Connect.](v2-protocols-oidc.md)

### <a name="application-registration"></a>Registro de aplicativo

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Durante o registro da aplicação, você registra um URI pós-logout. Em nosso tutorial, `https://localhost:44321/signout-oidc` você se registrou no campo **URL logout** da seção **Configurações Avançadas** na página **Autenticação.** Para obter detalhes, consulte [Cadastre o aplicativo webApp](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Durante o registro da aplicação, você registra um URI pós-logout. Em nosso tutorial, `https://localhost:44308/Account/EndSession` você se registrou no campo **URL logout** da seção **Configurações Avançadas** na página **Autenticação.** Para obter detalhes, consulte [Cadastre o aplicativo webApp](https://github.com/Azure-Samples/active-directory-dotnet-web-single-sign-out#register-the-service-app-webapp-distributedsignout-dotnet).

# <a name="java"></a>[Java](#tab/java)

Durante o registro da aplicação, você registra um URI pós-logout. Em nosso tutorial, `http://localhost:8080/msal4jsample/sign_out` você se registrou no campo **URL logout** da seção **Configurações Avançadas** na página **Autenticação.**

# <a name="python"></a>[Python](#tab/python)

Durante o registro do aplicativo, você não precisa registrar uma URL de logout extra. O aplicativo será chamado de volta em sua URL principal.

---

### <a name="sign-out-button"></a>Botão de saída

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Em ASP.NET Core, o botão de `Views\Shared\_LoginPartial.cshtml`saída é exposto em . É exibido apenas quando há uma conta autenticada. Ou seja, é exibido quando o usuário já fez o adesão.

```html
@using Microsoft.Identity.Web
@if (User.Identity.IsAuthenticated)
{
    <ul class="nav navbar-nav navbar-right">
        <li class="navbar-text">Hello @User.GetDisplayName()!</li>
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignOut">Sign out</a></li>
    </ul>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignIn">Sign in</a></li>
    </ul>
}
```

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Em ASP.NET MVC, o botão de `Views\Shared\_LoginPartial.cshtml`saída é exposto em . É exibido apenas quando há uma conta autenticada. Ou seja, é exibido quando o usuário já fez o adesão.

```html
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">
                Hello, @User.Identity.Name!
            </li>
            <li>
                @Html.ActionLink("Sign out", "SignOut", "Account")
            </li>
        </ul>
    </text>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

# <a name="java"></a>[Java](#tab/java)

Em nosso quickstart Java, o botão de saída está localizado no arquivo main/resources/templates/auth_page.html.

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<body>

<form action="/msal4jsample/sign_out">
    <input type="submit" value="Sign out">
</form>
...
```

# <a name="python"></a>[Python](#tab/python)

No quickstart python, o botão de saída está localizado no arquivo [templates/index.html#L10.](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/templates/index.html#L10)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
</head>
<body>
    <h1>Microsoft Identity Python Web App</h1>
    Welcome {{ user.get("name") }}!
    <li><a href='/graphcall'>Call Microsoft Graph API</a></li>
    <li><a href="/logout">Logout</a></li>
</body>
</html>
```

---

### <a name="signout-action-of-the-controller"></a>`SignOut`ação do controlador

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Em ASP.NET, selecionar o botão **De sair** no `SignOut` aplicativo web `AccountController` aciona a ação no controlador. Nas versões anteriores dos `Account` modelos ASP.NET Core, o controlador foi incorporado com o aplicativo web. Isso não é mais o caso porque o controlador agora faz parte da estrutura ASP.NET Core.

O código `AccountController` para o está disponível no ASP.NET repositório central em [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs). O controle da conta:

- Define um URI de redirecionamento OpenID para `/Account/SignedOut` que o controlador seja chamado de volta quando o Azure AD tiver concluído a saída de saída.
- Chamadas `Signout()`, que permite que o middleware OpenID Connect entre em contato com o ponto final da plataforma `logout` de identidade da Microsoft. O ponto final, então:

  - Limpa o cookie de sessão do navegador.
  - Chama de volta a URL de logout. Por padrão, a URL de logout exibe a página de exibição de assinatura [SignedOut.html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml). Esta página também é fornecida como parte do ASP.NET Core.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Em ASP.NET, a assinatura é `SignOut()` acionada a partir do método em um controlador (por exemplo, [AccountController.cs#L25-L31](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L25-L31)). Este método não faz parte do quadro ASP.NET, ao contrário do que acontece em ASP.NET Núcleo. E:

- Envia um desafio de saída openid.
- Limpa o cache.
- Redireciona para a página que ele quer.

```csharp
/// <summary>
/// Send an OpenID Connect sign-out request.
/// </summary>
public void SignOut()
{
 HttpContext.GetOwinContext()
            .Authentication
            .SignOut(CookieAuthenticationDefaults.AuthenticationType);
 Response.Redirect("/");
}
```

# <a name="java"></a>[Java](#tab/java)

Em Java, a saída de saída é `logout` feita ligando diretamente `post_logout_redirect_uri` para o ponto final da plataforma de identidade da Microsoft e fornecendo o valor. Para obter detalhes, consulte [AuthPageController.java#L50-L60](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L50-L60).

```Java
@RequestMapping("/msal4jsample/sign_out")
    public void signOut(HttpServletRequest httpRequest, HttpServletResponse response) throws IOException {

        httpRequest.getSession().invalidate();

        String endSessionEndpoint = "https://login.microsoftonline.com/common/oauth2/v2.0/logout";

        String redirectUrl = "http://localhost:8080/msal4jsample/";
        response.sendRedirect(endSessionEndpoint + "?post_logout_redirect_uri=" +
                URLEncoder.encode(redirectUrl, "UTF-8"));
    }
```

# <a name="python"></a>[Python](#tab/python)

O código que assina o usuário está no [app.py#L46-L52](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L47-L48).

```Python
@app.route("/logout")
def logout():
    session.clear()  # Wipe out the user and the token cache from the session
    return redirect(  # Also need to log out from the Microsoft Identity platform
        "https://login.microsoftonline.com/common/oauth2/v2.0/logout"
        "?post_logout_redirect_uri=" + url_for("index", _external=True))
```

---

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>Interceptando a `logout` chamada para o ponto final

O URI pós-logout permite que os aplicativos participem da saída global.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

O middleware ASP.NET Core OpenID Connect permite que seu `logout` aplicativo intercepte a chamada para `OnRedirectToIdentityProviderForSignOut`o ponto final da plataforma de identidade da Microsoft, fornecendo um evento OpenID Connect chamado . Para obter um exemplo de como assinar este evento (para limpar o cache do token), consulte [Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L156](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L156).

```csharp
    // Handling the global sign-out
    options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
    {
        // Forget about the signed-in user
    };
```

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Em ASP.NET, você delega ao middleware para executar a saída de saída, limpando o cookie da sessão:

```csharp
public class AccountController : Controller
{
 ...
 public void EndSession()
 {
  Request.GetOwinContext().Authentication.SignOut();
  Request.GetOwinContext().Authentication.SignOut(Microsoft.AspNet.Identity.DefaultAuthenticationTypes.ApplicationCookie);
  this.HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
 }
}
```

# <a name="java"></a>[Java](#tab/java)

No quickstart Java, o URI de redirecionamento pós-logout exibe apenas a página index.html.

# <a name="python"></a>[Python](#tab/python)

No quickstart python, o URI de redirecionamento pós-logout exibe apenas a página index.html.

---

## <a name="protocol"></a>Protocolo

Se você quiser saber mais sobre a saída de inscrição, leia a documentação do protocolo disponível no [Open ID Connect](./v2-protocols-oidc.md).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Mover para ambiente de produção](scenario-web-app-sign-user-production.md)
