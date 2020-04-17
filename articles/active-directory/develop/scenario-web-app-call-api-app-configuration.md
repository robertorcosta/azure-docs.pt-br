---
title: Configure um aplicativo web que chama APIs web - plataforma de identidade da Microsoft | Azure
description: Saiba como configurar o código de um aplicativo web que chama APIs da Web
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
ms.openlocfilehash: 7f05d33b43df85c49a0c92b60157e2a6448325ac
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537127"
---
# <a name="a-web-app-that-calls-web-apis-code-configuration"></a>Um aplicativo web que chama APIs da Web: configuração de código

Como mostrado no aplicativo web que assina no cenário [dos usuários,](scenario-web-app-sign-user-overview.md) o aplicativo web usa o [fluxo de código de autorização OAuth 2.0](v2-oauth2-auth-code-flow.md) para fazer login no usuário. Este fluxo tem dois passos:

1. Solicite um código de autorização. Esta parte delega um diálogo privado com o usuário para a plataforma de identidade Microsoft. Durante esse diálogo, o usuário faz sinais e concorda com o uso de APIs web. Quando o diálogo privado termina com sucesso, o aplicativo web recebe um código de autorização em seu URI de redirecionamento.
1. Solicite um token de acesso para a API resgatando o código de autorização.

O [aplicativo web que assina em](scenario-web-app-sign-user-overview.md) cenários de usuários cobriu apenas o primeiro passo. Aqui você aprende como modificar seu aplicativo web para que ele não só inscisse os usuários, mas também agora chama APIs da Web.

## <a name="libraries-that-support-web-app-scenarios"></a>Bibliotecas que suportam cenários de aplicativos web

As bibliotecas a seguir na Microsoft Authentication Library (MSAL) suportam o fluxo de código de autorização para aplicativos web:

| Biblioteca MSAL | Descrição |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Suporte para plataformas .NET Framework e .NET Core. Não suportados são Universal Windows Platform (UWP), Xamarin.iOS e Xamarin.Android, porque essas plataformas são usadas para criar aplicativos públicos de clientes. |
| ![MSAL Python](media/sample-v2-code/logo_python.png) <br/> MSAL para Python | Suporte para aplicações web Python. |
| ![MSAL Java](media/sample-v2-code/logo_java.png) <br/> MSAL para Java | Suporte para aplicações web Java. |

Selecione a guia para a plataforma em que você está interessado:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Como o login do usuário é delegado ao middleware De conexão Open ID (OIDC), você deve interagir com o processo OIDC. A forma como você interage depende da estrutura que você usa.

Para ASP.NET Core, você assinará eventos OIDC de middleware:

- Você permitirá que ASP.NET Core solicite um código de autorização por meio do middleware Open ID Connect. ASP.NET ou ASP.NET Core permitirá que o usuário faça login e consentie.
- Você vai assinar o aplicativo web para receber o código de autorização. Esta assinatura é feita usando um delegado C#.
- Quando o código de autorização for recebido, você usará bibliotecas MSAL para resgatá-lo. Os tokens de acesso resultantes e tokens de atualização são armazenados no cache de tokens. O cache pode ser usado em outras partes do aplicativo, como controladores, para adquirir outros tokens silenciosamente.

Exemplos de código neste artigo e o seguinte são extraídos do tutorial incremental do [aplicativo web ASP.NET Core, capítulo 2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph). Você pode querer se referir a esse tutorial para obter detalhes completos da implementação.

> [!NOTE]
> Para entender completamente os exemplos de código aqui, você precisa estar familiarizado com [ASP.NET fundamentos do Núcleo](https://docs.microsoft.com/aspnet/core/fundamentals), e em particular com injeção de [dependência](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) e [opções](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Como o login do usuário é delegado ao middleware De conexão Open ID (OIDC), você deve interagir com o processo OIDC. A forma como você interage depende da estrutura que você usa.

Por ASP.NET, você se inscreverá em eventos de Middleware OIDC:

- Você permitirá que ASP.NET Core solicite um código de autorização por meio do middleware Open ID Connect. ASP.NET ou ASP.NET Core permitirá que o usuário faça login e consentie.
- Você vai assinar o aplicativo web para receber o código de autorização. Esta assinatura é feita usando um delegado C#.
- Quando o código de autorização for recebido, você usará bibliotecas MSAL para resgatá-lo. Os tokens de acesso resultantes e tokens de atualização são armazenados no cache de tokens. O cache pode ser usado em outras partes do aplicativo, como controladores, para adquirir outros tokens silenciosamente.

Exemplos de código neste artigo e o seguinte são extraídos da [amostra do aplicativo ASP.NET Web](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect). Você pode querer se referir a essa amostra para obter detalhes completos da implementação.

# <a name="java"></a>[Java](#tab/java)

Exemplos de código neste artigo e no seguinte são extraídos do [aplicativo web Java que chama Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp), uma amostra de aplicativo web que usa MSAL para Java.
Atualmente, a amostra permite que o MSAL for Java produza a URL de código de autorização e lida com a navegação para o ponto final de autorização para a plataforma de identidade Microsoft. Também é possível usar a segurança Sprint para fazer login com o usuário. Você pode querer consultar a amostra para obter detalhes completos da implementação.

# <a name="python"></a>[Python](#tab/python)

Exemplos de código neste artigo e o seguinte são extraídos do [aplicativo web Python chamado Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp), uma amostra de aplicativo web que usa MSAL. Python.
Aamostra atualmente permite msal. O Python produz a URL de código de autorização e lida com a navegação para o ponto final de autorização para a plataforma de identidade Microsoft. Você pode querer consultar a amostra para obter detalhes completos da implementação.

---

## <a name="code-that-redeems-the-authorization-code"></a>Código que resgata o código de autorização

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

### <a name="startupcs"></a>Startup.cs

No ASP.NET Core, `Startup.cs` no arquivo, `OnAuthorizationCodeReceived` você assina o evento OpenID Connect. A partir deste evento, chame o método MSAL.NET. `AcquireTokenFromAuthorizationCode` Este método armazena os seguintes tokens no cache de tokens:

- O *token de* acesso `scopes`para o solicitado .
- Um *token de atualização*. Este token será usado para atualizar o token de acesso quando estiver perto do vencimento, ou para obter outro token em nome do mesmo usuário, mas para um recurso diferente.

O [tutorial do aplicativo Web ASP.NET](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) fornece código reutilizável para seus aplicativos web.

A seguir está o código de [Startup.cs#L40-L42](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Startup.cs#L40-L42). Ele apresenta chamadas para:

- O `AddMicrosoftIdentityPlatformAuthentication` método, que adiciona autenticação ao aplicativo web.
- O `AddMsal` método, que adiciona a capacidade de chamar APIs da Web.
- O `AddInMemoryTokenCaches` método, que é sobre a escolha de uma implementação de cache de token.

```csharp
public class Startup
{
  // Code not shown here

  public void ConfigureServices(IServiceCollection services)
  {
      // Token acquisition service based on MSAL.NET
      // and chosen token-cache implementation
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
          .AddMsal(Configuration, new string[] { Constants.ScopeUserRead })
          .AddInMemoryTokenCaches();
  }

  // Code not shown here
}
```

`Constants.ScopeUserRead`é definido em [Constants.cs#L5](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Infrastructure/Constants.cs#L5):

```csharp
public static class Constants
{
    public const string ScopeUserRead = "User.Read";
}
```

Você já estudou o `AddMicrosoftIdentityPlatformAuthentication` conteúdo do [aplicativo na Web que faz sinais nos usuários - configuração de código](scenario-web-app-sign-user-app-configuration.md?tabs=aspnetcore#initialization-code).

### <a name="the-addmsal-method"></a>O método AddMsal

O código `AddMsal` para está localizado em [Microsoft.Identity.Web/WebAppServiceCollectionextensions.cs#L108-L159](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L108-L159).

```csharp

/// <summary>
/// Extensions for IServiceCollection for startup initialization
/// </summary>
public static class WebAppServiceCollectionExtensions
{
  // Code omitted here

  /// <summary>
  /// Add MSAL support to the web app or web API.
  /// </summary>
  /// <param name="services">Service collection to which to add authentication</param>
  /// <param name="initialScopes">Initial scopes to request at sign-in</param>
  /// <returns></returns>
  public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
  {
      // Ensure that configuration options for MSAL.NET, HttpContext accessor and the Token acquisition service
      // (encapsulating MSAL.NET) are available through dependency injection.
      services.Configure<ConfidentialClientApplicationOptions>(options => configuration.Bind(configSectionName, options));
      services.AddHttpContextAccessor();
      services.AddTokenAcquisition();

      services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
      {
          // Response type
          options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

          // This scope is needed to get a refresh token when users sign in by using their personal Microsoft accounts.
          // (It's required by MSAL.NET and automatically provided when users sign in by using work or school accounts.)
          options.Scope.Add("offline_access");
          if (initialScopes != null)
          {
              foreach (string scope in initialScopes)
              {
                  if (!options.Scope.Contains(scope))
                  {
                      options.Scope.Add(scope);
                  }
              }
          }

          // Handle the auth redemption by MSAL.NET so that a token is available in the token cache,
          // where it will be usable from controllers later (by means of the TokenAcquisition service).
          var handler = options.Events.OnAuthorizationCodeReceived;
          options.Events.OnAuthorizationCodeReceived = async context =>
          {
              var tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
              await tokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync(context, options.Scope).ConfigureAwait(false);
              await handler(context).ConfigureAwait(false);
          };
      });
      return services;
  }
}
```

O `AddMsal` método garante que:

- O aplicativo web ASP.NET Core solicita um token de ID`options.ResponseType = OpenIdConnectResponseType.CodeIdToken`para o usuário e um código de autenticação ( ).
- O `offline_access` escopo é adicionado. Este escopo obtém o consentimento do usuário para que o aplicativo obtenha um token de atualização.
- O aplicativo assina o evento `OnAuthorizationCodeReceived` OIDC e resgata a chamada usando MSAL.NET, que é encapsulada aqui em um componente reutilizável implementando `ITokenAcquisition`.

### <a name="the-tokenacquisitionaddaccounttocachefromauthorizationcodeasync-method"></a>O método TokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync

O `TokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync` método está localizado em [Microsoft.Identity.Web/TokenAcquisition.cs#L101-L145](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L101-L145). Ele garante que:

- ASP.NET não tenta resgatar o código de autenticação`context.HandleCodeRedemption();`em paralelo ao MSAL.NET ( ).
- As reivindicações no token de ID estão disponíveis para a MSAL calcular uma chave de cache de token para a conta do usuário.
- Uma instância do aplicativo MSAL.NET é criada, se necessário.
- O código é resgatado pelo aplicativo MSAL.NET.
- O novo token de ID é compartilhado `context.HandleCodeRedemption(null, result.IdToken);`com ASP.NET Core durante a chamada para . O token de acesso não é compartilhado com ASP.NET Core. Ele permanece no MSAL.NET cache de token associado ao usuário, onde está pronto para ser usado em controladores ASP.NET Core.

Aqui está o código `TokenAcquisition`relevante para:

```csharp
public class TokenAcquisition : ITokenAcquisition
{
  string[] scopesRequestedByMsalNet = new string[]{ "openid", "profile", "offline_access" };

  // Code omitted here for clarity


  public async Task AddAccountToCacheFromAuthorizationCodeAsync(AuthorizationCodeReceivedContext context, IEnumerable<string> scopes)
  {
   // Code omitted here for clarity

    try
    {
      // Because AcquireTokenByAuthorizationCodeAsync is asynchronous, we tell ASP.NET core that we're handing the code
      // even if it's not done yet, so that it doesn't concurrently call the token endpoint. Otherwise, there will be a
      // race condition that causes an Azure AD error message ("code already redeemed").
      context.HandleCodeRedemption();

      // The cache needs the claims from the ID token.
      // If they're not yet in the HttpContext.User's claims, add them here.
      if (!context.HttpContext.User.Claims.Any())
      {
          (context.HttpContext.User.Identity as ClaimsIdentity).AddClaims(context.Principal.Claims);
      }

      var application = GetOrBuildConfidentialClientApplication();

      // Don't share the access token with ASP.NET Core. If we share it, ASP.NET will cache it and won't send the OAuth 2.0 request if
      // a further call to AcquireTokenByAuthorizationCodeAsync is required later for incremental consent (getting a code requesting more scopes).
      // Do share the ID token, however.
      var result = await application
          .AcquireTokenByAuthorizationCode(scopes.Except(_scopesRequestedByMsalNet), context.ProtocolMessage.Code)
          .ExecuteAsync()
          .ConfigureAwait(false);

      context.HandleCodeRedemption(null, result.IdToken);
  }
  catch (MsalException ex)
  {
      Debug.WriteLine(ex.Message);
      throw;
  }
 }
```

### <a name="the-tokenacquisitionbuildconfidentialclientapplication-method"></a>O método TokenAcquisition.BuildConfidentialClientApplication

Em ASP.NET Core, a construção do aplicativo cliente `HttpContext`confidencial usa informações que estão no . O `HttpContext` associado à solicitação é acessado `CurrentHttpContext` usando o imóvel. `HttpContext`tem informações sobre a URL para o aplicativo web e `ClaimsPrincipal`sobre o usuário de entrada (em um ).

O `BuildConfidentialClientApplication` método também usa a configuração ASP.NET Core. A configuração tem uma seção "AzureAD" e também está vinculada a ambos os elementos a seguir:

- A `_applicationOptions` estrutura de dados do tipo [ConfidentialClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationoptions?view=azure-dotnet).
- A `azureAdOptions` instância do tipo [AzureAdOptions,](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/AzureADOptions.cs)definida em ASP.NET Core `Authentication.AzureAD.UI`.

Finalmente, o aplicativo precisa manter caches de token. Você vai aprender mais sobre isso na próxima seção.

O código `GetOrBuildConfidentialClientApplication()` para o método está em [Microsoft.Identity.Web/TokenAcquisition.cs#L290-L333](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L290-L333). Ele usa membros que foram injetados por injeção de `TokenAcquisition` dependência (passado no construtor de em [Microsoft.Identity.Web/TokenAcquisition.cs#L47-L59](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L47-L59)).

Aqui está o `GetOrBuildConfidentialClientApplication`código para:

```csharp
public class TokenAcquisition : ITokenAcquisition
{
  // Code omitted here for clarity

  // Members
  private IConfidentialClientApplication application;
  private HttpContext CurrentHttpContext => _httpContextAccessor.HttpContext;

  // The following members are set by dependency injection in the TokenAcquisition constructor.
  private readonly AzureADOptions _azureAdOptions;
  private readonly ConfidentialClientApplicationOptions _applicationOptions;
  private readonly IMsalAppTokenCacheProvider _appTokenCacheProvider;
  private readonly IMsalUserTokenCacheProvider _userTokenCacheProvider;
  private readonly IHttpContextAccessor _httpContextAccessor;

  /// <summary>
  /// Creates an MSAL confidential client application, if needed.
  /// </summary>
  private IConfidentialClientApplication GetOrBuildConfidentialClientApplication()
  {
    if (application == null)
    {
        application = BuildConfidentialClientApplication();
    }
    return application;
  }

  /// <summary>
  /// Creates an MSAL Confidential client application
  /// </summary>
  /// <param name="claimsPrincipal"></param>
  /// <returns></returns>
  private IConfidentialClientApplication BuildConfidentialClientApplication()
  {
    var request = CurrentHttpContext.Request;
    var azureAdOptions = _azureAdOptions;
    var applicationOptions = _applicationOptions;
    string currentUri = UriHelper.BuildAbsolute(
        request.Scheme,
        request.Host,
        request.PathBase,
        azureAdOptions.CallbackPath ?? string.Empty);

    string authority = $"{applicationOptions.Instance}{applicationOptions.TenantId}/";

    var app = ConfidentialClientApplicationBuilder
        .CreateWithApplicationOptions(applicationOptions)
        .WithRedirectUri(currentUri)
        .WithAuthority(authority)
        .Build();

    // Initialize token cache providers
    _appTokenCacheProvider?.InitializeAsync(app.AppTokenCache);
    _userTokenCacheProvider?.InitializeAsync(app.UserTokenCache);

    return app;
  }

```

### <a name="summary"></a>Resumo

`AcquireTokenByAuthorizationCode`é realmente o método que resgata o código de autorização que ASP.NET solicita, e que recebe os tokens que são adicionados a MSAL.NET cache de token susceptíveis de uso. A partir do cache, os tokens são usados nos controladores ASP.NET Core.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET lida com as coisas de forma semelhante ao ASP.NET Core, exceto que a configuração do OpenID Connect e a assinatura do `OnAuthorizationCodeReceived` evento acontecem no arquivo [App_Start\Startup.Auth.cs.](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) Os conceitos também são semelhantes aos do ASP.NET Core, `RedirectUri` exceto que em ASP.NET você deve especificar o in [Web.config#L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15). Esta configuração é um pouco menos robusta do que a do ASP.NET Core, porque você precisará alterá-la quando implantar seu aplicativo.

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

Consulte [o aplicativo Web que faz sinais nos usuários: Configuração de código](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code) para entender como a amostra Java obtém o código de autorização. Depois que o aplicativo recebe o código, o [AuthFilter.java#L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56):

1. Delega `AuthHelper.processAuthenticationCodeRedirect` ao método em [AuthHelper.java#L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97).
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

O `getAuthResultByAuthCode` método é definido em [AuthHelper.java#L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176). Ele cria um `ConfidentialClientApplication`MSAL `acquireToken()` e, em seguida, chama com `AuthorizationCodeParameters` criado a partir do código de autorização.

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

O fluxo de código de autorização é solicitado como mostrado no [aplicativo Web que assina nos usuários: Configuração de código](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code). O código é então `authorized` recebido na função, `/getAToken` que flask rotas a partir da URL. Consulte [app.py#L30-L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44) para obter o contexto completo deste código:

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

Em vez de um segredo do cliente, o aplicativo de cliente confidencial também pode provar sua identidade usando um certificado de cliente ou uma afirmação do cliente.
O uso de afirmações de clientes é um cenário avançado, detalhado em [afirmações do Cliente.](msal-net-client-assertions.md)

## <a name="token-cache"></a>Cache de token

> [!IMPORTANT]
> A implementação de cache de token para aplicativos web ou APIs da Web é diferente da implementação para aplicativos de desktop, que muitas vezes é [baseada em arquivos](scenario-desktop-acquire-token.md#file-based-token-cache).
> Por razões de segurança e desempenho, é importante garantir que para aplicativos web e APIs da Web haja um cache de token por conta de usuário. Você deve serializar o cache de token para cada conta.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

O tutorial principal ASP.NET usa injeção de dependência para permitir que você decida a implementação do cache de token no arquivo Startup.cs para o seu aplicativo. O Microsoft.Identity.Web vem com serializadores de cache de token pré-construídos descritos na [serialização de cache do Token.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/README.md#token-cache-serialization) Uma possibilidade interessante é escolher [caches de memória distribuída](https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache)ASP.NET Core:

```csharp
// Use a distributed token cache by adding:
    services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
            .AddMsal(new string[] { scopesToRequest })
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

Para obter detalhes sobre os provedores de cache de token, consulte também os [tutoriais do aplicativo Core Web ASP.NET | Fase de caches](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache) de token do tutorial.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

A implementação de cache de token para aplicativos web ou APIs da Web é diferente da implementação para aplicativos de desktop, que muitas vezes é [baseada em arquivos](scenario-desktop-acquire-token.md#file-based-token-cache).

A implementação do aplicativo web pode usar a sessão de ASP.NET ou a memória do servidor. Por exemplo, veja como a implementação do cache é fisgada após a criação do aplicativo MSAL.NET no [MsalAppBuilder.cs#L39-L51](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51):

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

O MSAL Java fornece métodos para serializar e desserializar o cache do token. A amostra Java lida com a serialização da `getAuthResultBySilentFlow` sessão, como mostrado no método em [AuthHelper.java#L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122):

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

O detalhe `SessionManagementHelper` da classe é fornecido na [amostra MSAL para Java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java).

# <a name="python"></a>[Python](#tab/python)

Na amostra Python, um cache por conta é assegurado recriando um aplicativo cliente confidencial para cada solicitação e, em seguida, serializando-o no cache da sessão Flask:

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

Neste ponto, quando o usuário faz o sinal, um token é armazenado no cache do token. Vamos ver como ele é usado em outras partes do aplicativo web.

> [!div class="nextstepaction"]
> [Um aplicativo web que chama APIs da Web: Remova contas do cache na saída global](scenario-web-app-call-api-sign-in.md)
