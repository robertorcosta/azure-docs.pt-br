---
title: Configure um aplicativo web que assina em usuários - plataforma de identidade da Microsoft | Azure
description: Saiba como construir um aplicativo web que faça sinais nos usuários (configuração de código)
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
ms.openlocfilehash: 3cb51a57baa87849e97f7b05762dc4d6eba787a6
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537104"
---
# <a name="web-app-that-signs-in-users-code-configuration"></a>Aplicativo web que assina nos usuários: Configuração de código

Saiba como configurar o código para o seu aplicativo web que assina nos usuários.

## <a name="libraries-for-protecting-web-apps"></a>Bibliotecas para proteger aplicativos web

<!-- This section can be in an include for web app and web APIs -->
As bibliotecas que são usadas para proteger um aplicativo web (e uma API web) são:

| Plataforma | Biblioteca | Descrição |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [Extensões do modelo de identidade para .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Usado diretamente por ASP.NET e ASP.NET Core, o Microsoft Identity Model Extensions for .NET propõe um conjunto de DLLs em execução em ambos .NET Framework e .NET Core. A partir de um ASP.NET ou ASP.NET aplicativo web Core, você pode controlar a validação de tokens usando a classe **TokenValidationParameters** (em particular, em alguns cenários de parceiros). |
| ![Java](media/sample-v2-code/small_logo_java.png) | [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | Suporte para aplicações web Java |
| ![Python](media/sample-v2-code/small_logo_python.png) | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | Suporte para aplicativos web Python |

Selecione a guia que corresponde à plataforma em que você está interessado:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Os trechos de código neste artigo e os seguintes são extraídos do tutorial incremental do [aplicativo web ASP.NET Core, capítulo 1](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg).

Você pode querer consultar este tutorial para obter detalhes completos da implementação.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Os trechos de código neste artigo e os seguintes são extraídos da [amostra do aplicativo web ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect).

Você pode querer consultar esta amostra para obter detalhes completos da implementação.

# <a name="java"></a>[Java](#tab/java)

Os trechos de código neste artigo e os seguintes são extraídos do [aplicativo web Java chamando](https://github.com/Azure-Samples/ms-identity-java-webapp) amostra de gráfico da Microsoft no MSAL Java.

Você pode querer consultar esta amostra para obter detalhes completos da implementação.

# <a name="python"></a>[Python](#tab/python)

Os trechos de código neste artigo e os seguintes são extraídos do [aplicativo web Python chamando](https://github.com/Azure-Samples/ms-identity-python-webapp) a amostra de gráfico da Microsoft no MSAL Python.

Você pode querer consultar esta amostra para obter detalhes completos da implementação.

---

## <a name="configuration-files"></a>Arquivos de configuração

Os aplicativos da Web que fazem login com os usuários usando a plataforma de identidade microsoft geralmente são configurados através de arquivos de configuração. As configurações que você precisa preencher são:

- A instância`Instance`da nuvem ( ) se você quiser que seu aplicativo execute em nuvens nacionais, por exemplo
- O público no iD`TenantId`inquilino ( )
- O ID`ClientId`do cliente ( ) para sua aplicação, conforme copiado do portal Azure

Às vezes, as aplicações podem `Authority`ser parametrizadas `Instance` por `TenantId`, que é uma concatenação de e .

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Em ASP.NET Core, essas configurações estão localizadas no arquivo [appsettings.json,](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8) na seção "AzureAd".

```Json
{
  "AzureAd": {
    // Azure cloud instance among:
    // - "https://login.microsoftonline.com/" for Azure public cloud
    // - "https://login.microsoftonline.us/" for Azure US government
    // - "https://login.microsoftonline.de/" for Azure AD Germany
    // - "https://login.chinacloudapi.cn/" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD audience among:
    // - "TenantId" as a GUID obtained from the Azure portal to sign in users in your organization
    // - "organizations" to sign in users in any work or school account
    // - "common" to sign in users with any work or school account or Microsoft personal account
    // - "consumers" to sign in users with a Microsoft personal account only
    "TenantId": "[Enter the tenantId here]",

    // Client ID (application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc"
  }
}
```

Em ASP.NET Core, outro arquivo[(properties\launchSettings.json](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7)) contém o URL (`applicationUrl`)`sslPort`e a porta TLS/SSL ( ) para seu aplicativo e vários perfis.

```Json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:3110/",
      "sslPort": 44321
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "webApp": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      },
      "applicationUrl": "http://localhost:3110/"
    }
  }
}
```

No portal Azure, as URIs de resposta que você precisa registrar na página **de Autenticação** para sua aplicação precisam corresponder a essas URLs. Para os dois arquivos de `https://localhost:44321/signin-oidc`configuração anteriores, eles seriam . A razão `applicationUrl` é `http://localhost:3110`que `sslPort` é , mas é especificado (44321). `CallbackPath`é, `/signin-oidc`conforme `appsettings.json`definido em .

Da mesma forma, o URI de saída `https://localhost:44321/signout-callback-oidc`seria definido como .

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Em ASP.NET, o aplicativo é configurado através do arquivo [Web.config,](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15) linhas 12 a 15.

```XML
<?xml version="1.0" encoding="utf-8"?>
<!--
  For more information on how to configure your ASP.NET application, visit
  https://go.microsoft.com/fwlink/?LinkId=301880
  -->
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:ClientId" value="[Enter your client ID, as obtained from the app registration portal]" />
    <add key="ida:ClientSecret" value="[Enter your client secret, as obtained from the app registration portal]" />
    <add key="ida:AADInstance" value="https://login.microsoftonline.com/{0}{1}" />
    <add key="ida:RedirectUri" value="https://localhost:44326/" />
    <add key="vs:EnableBrowserLink" value="false" />
  </appSettings>
```

No portal Azure, as URIs de resposta que você precisa registrar na página **de Autenticação** para sua aplicação precisam corresponder a essas URLs. Ou seja, deveriam `https://localhost:44326/`ser.

# <a name="java"></a>[Java](#tab/java)

Em Java, a configuração está localizada no arquivo `src/main/resources` [application.properties](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties) localizado em .

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraph=http://localhost:8080/msal4jsample/graph/me
```

No portal Azure, as URIs de resposta que você precisa registrar na `redirectUri` página **de Autenticação** para sua aplicação precisam corresponder às instâncias que o aplicativo define. Ou seja, eles `http://localhost:8080/msal4jsample/secure/aad` `http://localhost:8080/msal4jsample/graph/me`deveriam ser e.

# <a name="python"></a>[Python](#tab/python)

Aqui está o arquivo de configuração Python em [app_config.py:](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app_config.py)

```Python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/common""
CLIENT_ID = "Enter_the_Application_Id_here"
ENDPOINT = 'https://graph.microsoft.com/v1.0/users'
SCOPE = ["User.ReadBasic.All"]
SESSION_TYPE = "filesystem"  # So the token cache will be stored in a server-side session
```

> [!NOTE]
> Este quickstart propõe armazenar o segredo do cliente no arquivo de configuração para simplificar. Em seu aplicativo de produção, você gostaria de usar outras maneiras de armazenar seu segredo, como um cofre de chaves ou uma variável de ambiente, conforme descrito na [documentação do Flask.](https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables)
>
> ```python
> CLIENT_SECRET = os.getenv("CLIENT_SECRET")
> if not CLIENT_SECRET:
>     raise ValueError("Need to define CLIENT_SECRET environment variable")
> ```

---

## <a name="initialization-code"></a>Código de inicialização

O código de inicialização é diferente dependendo da plataforma. Para ASP.NET Core e ASP.NET, a assinatura de usuários é delegada ao middleware OpenID Connect. O modelo ASP.NET ou ASP.NET Core gera aplicações web para o ponto final do Azure Active Directory (Azure AD). Algumas configurações são necessárias para adaptá-los ao ponto final da plataforma de identidade microsoft (v2.0). No caso de Java, é tratado pela Spring com a cooperação do aplicativo.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Em ASP.NET aplicativos web Core (e APIs web), `[Authorize]` o aplicativo é protegido porque você tem um atributo nos controladores ou nas ações do controlador. Este atributo verifica se o usuário está autenticado. O código que está inicializando o aplicativo está no arquivo Startup.cs.

Para adicionar autenticação com a plataforma de identidade da Microsoft (anteriormente Azure AD v2.0), você precisará adicionar o seguinte código. Os comentários no código devem ser auto-explicativos.

> [!NOTE]
> Se você iniciar seu projeto com o projeto web `dotnet new mvc`padrão ASP.NET `AddAzureAD` Core dentro do estúdio Visual ou usando, o método está disponível por padrão. Isso porque os pacotes relacionados são carregados automaticamente.
>
> Se você constrói um projeto do zero e está tentando usar o seguinte código, sugerimos que você adicione o pacote NuGet **Microsoft.AspNetCore.Authentication.AzureAD.UI** ao seu projeto para disponibilizar o `AddAzureAD` método.

O código a seguir está disponível em [Startup.cs#L33-L34](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/1-WebApp-OIDC/1-1-MyOrg/Startup.cs#L33-L34).

```csharp
public class Startup
{
 ...

  // This method is called by the runtime. Use this method to add services to the container.
  public void ConfigureServices(IServiceCollection services)
  {
    ...
      // Sign in users with the Microsoft identity platform
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration);

      services.AddMvc(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
            options.Filters.Add(new AuthorizeFilter(policy));
            })
        .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
    }
```

O `AddMicrosoftIdentityPlatformAuthentication` método de extensão é definido em [Microsoft.Identity.Web/WebAppServiceCollectionextensions.cs#L23](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L23). E:

- Adiciona o serviço de autenticação.
- Configura opções para ler o arquivo de configuração.
- Configura as opções openid connect para que a autoridade usada seja a plataforma de identidade da Microsoft (anteriormente Azure AD v2.0).
- Valida o emissor do token.
- Garante que as reivindicações correspondentes ao `preferred_username` nome sejam mapeadas a partir da reclamação no token ID.

Além da configuração, você pode especificar o `AddMicrosoftIdentityPlatformAuthentication`nome da seção de configuração ao chamar . Por padrão, é. `AzureAd`

O rastreamento de eventos de middleware OpenId Connect pode ajudá-lo a solucionar problemas do aplicativo web se a autenticação não funcionar. A `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` `true` configuração mostrará como as informações são elaboradas pelo conjunto de middleware ASP.NET Core à `HttpContext.User`medida que progride da resposta HTTP à identidade do usuário em .

```csharp
/// <summary>
/// Add authentication with the Microsoft identity platform.
/// This method expects the configuration file to have a section named "AzureAd" with the necessary settings to initialize authentication options.
/// </summary>
/// <param name="services">Service collection to which to add this authentication scheme</param>
/// <param name="configuration">The Configuration object</param>
/// <param name="subscribeToOpenIdConnectMiddlewareDiagnosticsEvents">
/// Set to true if you want to debug, or just understand the OpenID Connect events.
/// </param>
/// <returns></returns>
public static IServiceCollection AddMicrosoftIdentityPlatformAuthentication(
  this IServiceCollection services,
  IConfiguration configuration,
  string configSectionName = "AzureAd",
  bool subscribeToOpenIdConnectMiddlewareDiagnosticsEvents = false)
{
  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
      .AddAzureAD(options => configuration.Bind(configSectionName, options));
  services.Configure<AzureADOptions>(options => configuration.Bind(configSectionName, options));

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
      // Per the following code, this application signs in users in any work or school
      // accounts and any Microsoft personal accounts.
      // If you want to direct Azure AD to restrict the users who can sign in, change
      // the tenant value of the appsettings.json file in the following way:
      // - Only work or school accounts => 'organizations'
      // - Only Microsoft personal accounts => 'consumers'
      // - Work or school and personal accounts => 'common'
      // If you want to restrict the users who can sign in to only one tenant,
      // set the tenant value in the appsettings.json file to the tenant ID
      // or domain of this organization.
      options.Authority = options.Authority + "/v2.0/";

      // If you want to restrict the users who can sign in to several organizations,
      // set the tenant value in the appsettings.json file to 'organizations', and add the
      // issuers you want to accept to the options.TokenValidationParameters.ValidIssuers collection.
      options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;

      // Set nameClaimType to be preferred_username.
      // This change is needed because certain token claims from the Azure AD v1 endpoint
      // (on which the original .NET Core template is based) are different from the Microsoft identity platform endpoint.
      // For more details, see [ID tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
      // and [Access tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).
      options.TokenValidationParameters.NameClaimType = "preferred_username";

      // ...

      if (subscribeToOpenIdConnectMiddlewareDiagnosticsEvents)
      {
          OpenIdConnectMiddlewareDiagnostics.Subscribe(options.Events);
      }
  });
  return services;
}
  ...
```

A `AadIssuerValidator` classe permite que o emissor do token seja validado em muitos casos. Esta classe funciona com um token v1.0 ou v2.0, um aplicativo de inquilino único ou multilocatário, ou um aplicativo que assina usuários com suas contas pessoais da Microsoft na nuvem pública do Azure ou nuvens nacionais. Está disponível em [Microsoft.Identity.Web/Resource/AadIssuerValidator.cs](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

O código relacionado à autenticação em um aplicativo web ASP.NET e APIs web está localizado no arquivo [App_Start/Startup.Auth.cs.](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs#L17-L61)

```csharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // `Authority` represents the identity platform endpoint - https://login.microsoftonline.com/common/v2.0.
     // `Scope` describes the initial permissions that your app will need.
     //  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/.
     ClientId = clientId,
     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
     RedirectUri = redirectUri,
     Scope = "openid profile",
     PostLogoutRedirectUri = redirectUri,
    });
 }
```

# <a name="java"></a>[Java](#tab/java)

A amostra Java usa a estrutura de Mola. O aplicativo é protegido porque você implementa um filtro, que intercepta cada resposta HTTP. No quickstart para aplicativos web Java, este filtro está `AuthFilter` dentro `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java`.

O filtro processa o fluxo de código de autorização OAuth`isAuthenticated()` 2.0 e verifica se o usuário é autenticado (método). Se o usuário não for autenticado, ele calcula a URL dos pontos finais de autorização do Azure AD e redireciona o navegador para este URI.

Quando a resposta chega, contendo o código de autorização, ele adquire o token usando MSAL Java. Quando ele finalmente recebe o token do ponto final do token (no URI de redirecionamento), o usuário é conectado.

Para obter detalhes, consulte o `doFilter()` método em [AuthFilter.java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java).

> [!NOTE]
> O código `doFilter()` do é escrito em uma ordem ligeiramente diferente, mas o fluxo é o descrito.

Para obter detalhes sobre o fluxo de código de autorização que este método aciona, consulte a [plataforma de identidade Microsoft e o fluxo de código de autorização OAuth 2.0](v2-oauth2-auth-code-flow.md).

# <a name="python"></a>[Python](#tab/python)

A amostra Python usa flask. A inicialização do Flask e do MSAL Python é feita em [app.py#L1-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L1-L28).

```Python
import uuid
import requests
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)
```

---

## <a name="next-steps"></a>Próximas etapas

No próximo artigo, você aprenderá como ativar a entrada e a saída.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Entrar e sair](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Entrar e sair](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Entrar e sair](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Entrar e sair](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=python)

---
