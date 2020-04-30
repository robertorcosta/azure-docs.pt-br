---
title: Configurar um aplicativo Web que chama APIs da Web-plataforma de identidade da Microsoft | Azure
description: Saiba como configurar o código de um aplicativo Web que chama APIs da Web
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
ms.openlocfilehash: 68f6f8ec67aca44c89b338287bdd37b6066992e0
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82207013"
---
# <a name="a-web-app-that-calls-web-apis-code-configuration"></a>Um aplicativo Web que chama APIs da Web: configuração de código

Conforme mostrado no [aplicativo Web que entra no](scenario-web-app-sign-user-overview.md) cenário de usuários, o aplicativo Web usa o [fluxo de código de autorização OAuth 2,0](v2-oauth2-auth-code-flow.md) para conectar o usuário. Esse fluxo tem duas etapas:

1. Solicite um código de autorização. Esta parte Delega um diálogo particular com o usuário para a plataforma de identidade da Microsoft. Durante essa caixa de diálogo, o usuário entra e se dá ao uso de APIs da Web. Quando o diálogo particular termina com êxito, o aplicativo Web recebe um código de autorização em seu URI de redirecionamento.
1. Solicite um token de acesso para a API, resgatando o código de autorização.

O [aplicativo Web que entra em](scenario-web-app-sign-user-overview.md) cenários de usuários abordou apenas a primeira etapa. Aqui você aprende como modificar seu aplicativo Web para que ele não apenas assine os usuários no, mas também chama APIs da Web.

## <a name="libraries-that-support-web-app-scenarios"></a>Bibliotecas que dão suporte a cenários de aplicativo Web

As bibliotecas a seguir na biblioteca de autenticação da Microsoft (MSAL) dão suporte ao fluxo de código de autorização para aplicativos Web:

| Biblioteca MSAL | Descrição |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Suporte para plataformas .NET Framework e .NET Core. Não há suporte para Plataforma Universal do Windows (UWP), Xamarin. iOS e Xamarin. Android, pois essas plataformas são usadas para criar aplicativos cliente públicos. Para ASP.NET Core aplicativos Web e APIs Web, o MSAL.NET é encapsulado em uma biblioteca de nível superior chamada Microsoft. Identity. Web|
| ![MSAL Python](media/sample-v2-code/logo_python.png) <br/> MSAL para Python | Suporte para aplicativos Web do Python. |
| ![MSAL Java](media/sample-v2-code/logo_java.png) <br/> MSAL para Java | Suporte para aplicativos Web Java. |

Selecione a guia para a plataforma em que você está interessado:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Para permitir que seu aplicativo Web chame APIs protegidas ao usar Microsoft. Identity. Web, você precisa apenas chamar `AddWebAppCallsProtectedWebApi` e especificar um formato de serialização de cache de token (por exemplo, cache de token na memória):

```C#
// This method gets called by the runtime. Use this method to add services to the container.
public void ConfigureServices(IServiceCollection services)
{
    // more code here

    services.AddSignIn(Configuration, "AzureAd")
            .AddWebAppCallsProtectedWebApi(Configuration,
                                           initialScopes: new string[] { "user.read" })
            .AddInMemoryTokenCaches();

    // more code here
}
```

Se você estiver interessado em entender mais sobre o cache de token, consulte [Opções de serialização de cache de token](#token-cache)

> [!NOTE]
> Para entender totalmente os exemplos de código aqui, você precisa estar familiarizado com os [conceitos básicos de ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals)e, em particular, com [Opções](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options)e [injeção de dependência](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) .

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Como a entrada do usuário é delegada ao middleware do Open ID Connect (OIDC), você deve interagir com o processo OIDC. A maneira como você interage depende da estrutura usada.

Para ASP.NET, você assinará os eventos do middleware OIDC:

- Você permitirá ASP.NET Core solicitar um código de autorização por meio do middleware do Open ID Connect. ASP.NET ou ASP.NET Core permitirão que o usuário entre e consenti.
- Você assinará o aplicativo Web para receber o código de autorização. Essa assinatura é feita usando um delegado em C#.
- Quando o código de autorização for recebido, você usará bibliotecas MSAL para resgatar. Os tokens de acesso e tokens de atualização resultantes são armazenados no cache de token. O cache pode ser usado em outras partes do aplicativo, como controladores, para adquirir outros tokens silenciosamente.

Os exemplos de código neste artigo e o seguinte são extraídos do [exemplo de aplicativo Web ASP.net](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect). Talvez você queira consultar esse exemplo para obter detalhes completos de implementação.

# <a name="java"></a>[Java](#tab/java)

Os exemplos de código neste artigo e o seguinte são extraídos do [aplicativo Web Java que chama Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp), um exemplo de aplicativo Web que usa MSAL para Java.
O exemplo atualmente permite MSAL para Java produzir a URL de código de autorização e manipula a navegação para o ponto de extremidade de autorização para a plataforma de identidade da Microsoft. Também é possível usar a segurança do Sprint para conectar o usuário. Talvez você queira consultar o exemplo para obter detalhes completos de implementação.

# <a name="python"></a>[Python](#tab/python)

Os exemplos de código neste artigo e o seguinte são extraídos do [aplicativo Web Python chamando Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp), um exemplo de aplicativo Web que usa MSAL. Python.
O exemplo atualmente permite MSAL. O Python produz a URL de código de autorização e manipula a navegação para o ponto de extremidade de autorização para a plataforma de identidade da Microsoft. Talvez você queira consultar o exemplo para obter detalhes completos de implementação.

---

## <a name="code-that-redeems-the-authorization-code"></a>Código que resgata o código de autorização

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

O Microsoft. Identity. Web simplifica seu código definindo as configurações corretas do OpenID Connect, inscrevendo-se no evento de código recebido e resgatando o código. Nenhum código adicional é necessário para resgatar o código de autorização.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

O ASP.NET lida com as coisas de forma semelhante a ASP.NET Core, exceto que a configuração do OpenID Connect `OnAuthorizationCodeReceived` e a assinatura do evento ocorrem no arquivo [App_Start \startup.auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) . Os conceitos também são semelhantes aos da ASP.NET Core, exceto que, em ASP.NET, você deve especificar `RedirectUri` o em [Web. config # L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15). Essa configuração é um pouco menos robusta do que a do ASP.NET Core, pois você precisará alterá-la quando implantar seu aplicativo.

Aqui está o código para Startup.Auth.cs:

```csharp
public partial class Startup
{
  public void ConfigureAuth(IAppBuilder app)
  {
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    // Custom middleware initialization. This is activated when the code obtained from a code_grant is present in the query string (&code=<code>).
    app.UseOAuth2CodeRedeemer(
        new OAuth2CodeRedeemerOptions
        {
            ClientId = AuthenticationConfig.ClientId,
            ClientSecret = AuthenticationConfig.ClientSecret,
            RedirectUri = AuthenticationConfig.RedirectUri
        }
      );

  app.UseOpenIdConnectAuthentication(
      new OpenIdConnectAuthenticationOptions
      {
        // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0.
        Authority = AuthenticationConfig.Authority,
        ClientId = AuthenticationConfig.ClientId,
        RedirectUri = AuthenticationConfig.RedirectUri,
        PostLogoutRedirectUri = AuthenticationConfig.RedirectUri,
        Scope = AuthenticationConfig.BasicSignInScopes + " Mail.Read", // A basic set of permissions for user sign-in and profile access "openid profile offline_access"
        TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = false,
            // In a real application, you would use IssuerValidator for additional checks, such as making sure the user's organization has signed up for your app.
            //     IssuerValidator = (issuer, token, tvp) =>
            //     {
            //        //if(MyCustomTenantValidation(issuer))
            //        return issuer;
            //        //else
            //        //    throw new SecurityTokenInvalidIssuerException("Invalid issuer");
            //    },
            //NameClaimType = "name",
        },
        Notifications = new OpenIdConnectAuthenticationNotifications()
        {
            AuthorizationCodeReceived = OnAuthorizationCodeReceived,
            AuthenticationFailed = OnAuthenticationFailed,
        }
      });
  }

  private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification context)
  {
      // Upon successful sign-in, get the access token and cache it by using MSAL.
      IConfidentialClientApplication clientApp = MsalAppBuilder.BuildConfidentialClientApplication(new ClaimsPrincipal(context.AuthenticationTicket.Identity));
      AuthenticationResult result = await clientApp.AcquireTokenByAuthorizationCode(new[] { "Mail.Read" }, context.Code).ExecuteAsync();
  }

  private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
  {
      notification.HandleResponse();
      notification.Response.Redirect("/Error?message=" + notification.Exception.Message);
      return Task.FromResult(0);
  }
}
```

# <a name="java"></a>[Java](#tab/java)

Consulte [aplicativo Web que conecta usuários: configuração de código](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code) para entender como o exemplo de Java Obtém o código de autorização. Depois que o aplicativo recebe o código, o [AuthFilter. java # L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56):

1. Delega para o `AuthHelper.processAuthenticationCodeRedirect` método em [AuthHelper. java # L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97).
1. Chama `getAuthResultByAuthCode`.

```Java
class AuthHelper {
  // Code omitted
  void processAuthenticationCodeRedirect(HttpServletRequest httpRequest, String currentUri, String fullUrl)
            throws Throwable {

  // Code omitted
  AuthenticationResponse authResponse = AuthenticationResponseParser.parse(new URI(fullUrl), params);

  // Code omitted
  IAuthenticationResult result = getAuthResultByAuthCode(
                    httpRequest,
                    oidcResponse.getAuthorizationCode(),
                    currentUri);

// Code omitted
  }
}
```

O `getAuthResultByAuthCode` método é definido em [AuthHelper. java # L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176). Ele cria um MSAL `ConfidentialClientApplication`e, em seguida `acquireToken()` , `AuthorizationCodeParameters` chama com criado a partir do código de autorização.

```Java
   private IAuthenticationResult getAuthResultByAuthCode(
            HttpServletRequest httpServletRequest,
            AuthorizationCode authorizationCode,
            String currentUri) throws Throwable {

        IAuthenticationResult result;
        ConfidentialClientApplication app;
        try {
            app = createClientApplication();

            String authCode = authorizationCode.getValue();
            AuthorizationCodeParameters parameters = AuthorizationCodeParameters.builder(
                    authCode,
                    new URI(currentUri)).
                    build();

            Future<IAuthenticationResult> future = app.acquireToken(parameters);

            result = future.get();
        } catch (ExecutionException e) {
            throw e.getCause();
        }

        if (result == null) {
            throw new ServiceUnavailableException("authentication result was null");
        }

        SessionManagementHelper.storeTokenCacheInSession(httpServletRequest, app.tokenCache().serialize());

        return result;
    }

    private ConfidentialClientApplication createClientApplication() throws MalformedURLException {
        return ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(clientSecret)).
                authority(authority).
                build();
    }
```

# <a name="python"></a>[Python](#tab/python)

O fluxo do código de autorização é solicitado, conforme mostrado no [aplicativo Web que conecta os usuários: configuração de código](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code). O código é recebido na `authorized` função, que Flask rotas da `/getAToken` URL. Consulte [app. py # L30-l44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44) para obter o contexto completo deste código:

```python
 @app.route("/getAToken")  # Its absolute URL must match your app's redirect_uri set in AAD.
def authorized():
    if request.args['state'] != session.get("state"):
        return redirect(url_for("login"))
    cache = _load_cache()
    result = _build_msal_app(cache).acquire_token_by_authorization_code(
        request.args['code'],
        scopes=app_config.SCOPE,  # Misspelled scope would cause an HTTP 400 error here.
        redirect_uri=url_for("authorized", _external=True))
    if "error" in result:
        return "Login failure: %s, %s" % (
            result["error"], result.get("error_description"))
    session["user"] = result.get("id_token_claims")
    _save_cache(cache)
    return redirect(url_for("index"))
```

---

Em vez de um segredo do cliente, o aplicativo cliente confidencial também pode provar sua identidade usando um certificado de cliente ou uma asserção de cliente.
O uso de asserções de cliente é um cenário avançado, detalhado em [declarações de cliente](msal-net-client-assertions.md).

## <a name="token-cache"></a>Cache de token

> [!IMPORTANT]
> A implementação do cache de tokens para aplicativos Web ou APIs Web é diferente da implementação para aplicativos de área de trabalho, que geralmente é [baseada em arquivo](scenario-desktop-acquire-token.md#file-based-token-cache).
> Por motivos de segurança e desempenho, é importante garantir que, para aplicativos Web e APIs Web, haja um cache de token por conta de usuário. Você deve serializar o cache de token para cada conta.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

O tutorial do ASP.NET Core usa injeção de dependência para permitir que você decida a implementação do cache de token no arquivo Startup.cs para seu aplicativo. Microsoft. Identity. Web vem com serializadores pré-criados de cache de tokens descritos na [serialização de cache de token](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/README.md#token-cache-serialization). Uma possibilidade interessante é escolher ASP.NET Core [caches de memória distribuída](https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache):

```csharp
// Use a distributed token cache by adding:
    services.AddSignIn(Configuration, "AzureAd");
            .AddWebAppCallsProtectedWebApi(Configuration,
                                           initialScopes: new string[] { "user.read" })
            .AddDistributedTokenCaches();

// Then, choose your implementation.
// For instance, the distributed in-memory cache (not cleared when you stop the app):
services.AddDistributedMemoryCache()

// Or a Redis cache:
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache:
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});
```

Para obter detalhes sobre os provedores de cache de token, consulte também os [tutoriais do aplicativo Web ASP.NET Core | Fase de caches de token](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache) do tutorial.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

A implementação do cache de tokens para aplicativos Web ou APIs Web é diferente da implementação para aplicativos de área de trabalho, que geralmente é [baseada em arquivo](scenario-desktop-acquire-token.md#file-based-token-cache).

A implementação do aplicativo Web pode usar a sessão ASP.NET ou a memória do servidor. Por exemplo, veja como a implementação do cache é conectada após a criação do aplicativo MSAL.NET em [MsalAppBuilder. cs # L39-L51](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51):

```csharp
public static class MsalAppBuilder
{
 // Omitted code
    public static IConfidentialClientApplication BuildConfidentialClientApplication(ClaimsPrincipal currentUser)
    {
      IConfidentialClientApplication clientapp = ConfidentialClientApplicationBuilder.Create(AuthenticationConfig.ClientId)
            .WithClientSecret(AuthenticationConfig.ClientSecret)
            .WithRedirectUri(AuthenticationConfig.RedirectUri)
            .WithAuthority(new Uri(AuthenticationConfig.Authority))
            .Build();

      // After the ConfidentialClientApplication is created, we overwrite its default UserTokenCache with our implementation.
      MSALPerUserMemoryTokenCache userTokenCache = new MSALPerUserMemoryTokenCache(clientapp.UserTokenCache, currentUser ?? ClaimsPrincipal.Current);

      return clientapp;
  }
```

# <a name="java"></a>[Java](#tab/java)

O MSAL Java fornece métodos para serializar e desserializar o cache de token. O exemplo de Java manipula a serialização da sessão, conforme mostrado no `getAuthResultBySilentFlow` método em [AuthHelper. java # L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122):

```Java
IAuthenticationResult getAuthResultBySilentFlow(HttpServletRequest httpRequest, HttpServletResponse httpResponse)
      throws Throwable {

  IAuthenticationResult result =  SessionManagementHelper.getAuthSessionObject(httpRequest);

  IConfidentialClientApplication app = createClientApplication();

  Object tokenCache = httpRequest.getSession().getAttribute("token_cache");
  if (tokenCache != null) {
      app.tokenCache().deserialize(tokenCache.toString());
  }

  SilentParameters parameters = SilentParameters.builder(
          Collections.singleton("User.Read"),
          result.account()).build();

  CompletableFuture<IAuthenticationResult> future = app.acquireTokenSilently(parameters);
  IAuthenticationResult updatedResult = future.get();

  // Update session with latest token cache.
  SessionManagementHelper.storeTokenCacheInSession(httpRequest, app.tokenCache().serialize());

  return updatedResult;
}
```

O detalhe da `SessionManagementHelper` classe é fornecido no exemplo de [MSAL para Java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java).

# <a name="python"></a>[Python](#tab/python)

No exemplo de Python, um cache por conta é garantido recriando um aplicativo cliente confidencial para cada solicitação e, em seguida, serializando-o no cache da sessão Flask:

```python
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)

# Code omitted here for simplicity

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
```

---

## <a name="next-steps"></a>Próximas etapas

Neste ponto, quando o usuário faz logon, um token é armazenado no cache de token. Vamos ver como ele é usado em outras partes do aplicativo Web.

> [!div class="nextstepaction"]
> [Um aplicativo Web que chama APIs da Web: remover contas do cache na saída global](scenario-web-app-call-api-sign-in.md)
