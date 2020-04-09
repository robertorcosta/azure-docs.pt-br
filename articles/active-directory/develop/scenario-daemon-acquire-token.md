---
title: Adquira tokens para chamar uma API web (aplicativo daemon) - plataforma de identidade da Microsoft | Azure
description: Aprenda a construir um aplicativo daemon que chama APIs da Web (adquirindo tokens)
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
ms.openlocfilehash: a81f3ffb7ec190943c50127b129523badf0ef0a7
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882974"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>Aplicativo Daemon que chama APIs da Web - adquira um token

Depois de construir um aplicativo de cliente confidencial, você pode adquirir `AcquireTokenForClient`um token para o aplicativo ligando, passando o escopo e, opcionalmente, forçando uma atualização do token.

## <a name="scopes-to-request"></a>Escopos a serem solicitados

O escopo para solicitar um fluxo de credencial do `/.default`cliente é o nome do recurso seguido por . Esta notação diz ao Azure Active Directory (Azure AD) para usar as *permissões de nível de aplicativo declaradas* estáticamente durante o registro do aplicativo. Além disso, essas permissões de API devem ser concedidas por um administrador de inquilinos.

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

# <a name="python"></a>[Python](#tab/python)

No MSAL Python, o arquivo de configuração se parece com este trecho de código:

```Json
{
    "scope": ["https://graph.microsoft.com/.default"],
}
```

# <a name="java"></a>[Java](#tab/java)

```Java
final static String GRAPH_DEFAULT_SCOPE = "https://graph.microsoft.com/.default";
```

---

### <a name="azure-ad-v10-resources"></a>Recursos do Azure AD (v1.0)

O escopo utilizado para credenciais de clientes `/.default`deve ser sempre o ID de recurso seguido por .

> [!IMPORTANT]
> Quando o MSAL solicita um token de acesso para um recurso que aceita um token de acesso versão 1.0, o Azure AD analisa o público desejado do escopo solicitado, tomando tudo antes da última barra e usando-o como identificador de recursos.
> Então, se, como o Azure SQL Database **(https:\//database.windows.net),** o recurso espera um público `https://database.windows.net/`que termine com uma barra `https://database.windows.net//.default`(para o Azure SQL Database), você precisará solicitar um escopo de . (Observe a barra dupla.) Veja também MSAL.NET problema [#747: A barra de arrasto da URL de recurso é omitida, o que causou falha no sql auth](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747).

## <a name="acquiretokenforclient-api"></a>AcquireTokenForClient API

Para adquirir um token para o `AcquireTokenForClient` aplicativo, você usará ou seu equivalente, dependendo da plataforma.

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
using Microsoft.Identity.Client;

// With client credentials flows, the scope is always of the shape "resource/.default" because the
// application permissions need to be set statically (in the portal or by PowerShell), and then granted by
// a tenant administrator.
string[] scopes = new string[] { "https://graph.microsoft.com/.default" };

AuthenticationResult result = null;
try
{
 result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
    // The application doesn't have sufficient permissions.
    // - Did you declare enough app permissions during app creation?
    // - Did the tenant admin grant permissions to the application?
}
catch (MsalServiceException ex) when (ex.Message.Contains("AADSTS70011"))
{
    // Invalid scope. The scope has to be in the form "https://resourceurl/.default"
    // Mitigation: Change the scope to be as expected.
}
```

# <a name="python"></a>[Python](#tab/python)

```Python
# The pattern to acquire a token looks like this.
result = None

# First, the code looks up a token from the cache.
# Because we're looking for a token for the current app, not for a user,
# use None for the account parameter.
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])

if "access_token" in result:
    # Call a protected API with the access token.
    print(result["token_type"])
else:
    print(result.get("error"))
    print(result.get("error_description"))
    print(result.get("correlation_id"))  # You might need this when reporting a bug.
```

# <a name="java"></a>[Java](#tab/java)

Este código é extraído das [amostras de desenvolvimento MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/confidential-client/).

```Java
private static IAuthenticationResult acquireToken() throws Exception {

     // Load token cache from file and initialize token cache aspect. The token cache will have
     // dummy data, so the acquireTokenSilently call will fail.
     TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

     IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);
     ConfidentialClientApplication cca =
             ConfidentialClientApplication
                     .builder(CLIENT_ID, credential)
                     .authority(AUTHORITY)
                     .setTokenCacheAccessAspect(tokenCacheAspect)
                     .build();

     IAuthenticationResult result;
     try {
         SilentParameters silentParameters =
                 SilentParameters
                         .builder(SCOPE)
                         .build();

         // try to acquire token silently. This call will fail since the token cache does not
         // have a token for the application you are requesting an access token for
         result = cca.acquireTokenSilently(silentParameters).join();
     } catch (Exception ex) {
         if (ex.getCause() instanceof MsalException) {

             ClientCredentialParameters parameters =
                     ClientCredentialParameters
                             .builder(SCOPE)
                             .build();

             // Try to acquire a token. If successful, you should see
             // the token information printed out to console
             result = cca.acquireToken(parameters).join();
         } else {
             // Handle other exceptions accordingly
             throw ex;
         }
     }
     return result;
 }
```

---

### <a name="protocol"></a>Protocolo

Se você ainda não tem uma biblioteca para o idioma escolhido, você pode querer usar o protocolo diretamente:

#### <a name="first-case-access-the-token-request-by-using-a-shared-secret"></a>Primeiro caso: Acesse a solicitação de token usando um segredo compartilhado

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-the-token-request-by-using-a-certificate"></a>Segundo caso: Acesse a solicitação de token usando um certificado

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

Para obter mais informações, consulte a documentação do protocolo: [plataforma de identidade Microsoft e o fluxo de credenciais do cliente OAuth 2.0](v2-oauth2-client-creds-grant-flow.md).

## <a name="application-token-cache"></a>Cache de token de aplicativo

Em MSAL.NET, `AcquireTokenForClient` usa o cache de token do aplicativo. (Todos os outros métodos AcquireToken*XX* usam o cache de token do usuário.) Não `AcquireTokenSilent` ligue antes de `AcquireTokenForClient`ligar, porque `AcquireTokenSilent` usa o cache de token do *usuário.* `AcquireTokenForClient`verifica o cache do token do *aplicativo* em si e o atualiza.

## <a name="troubleshooting"></a>Solução de problemas

### <a name="did-you-use-the-resourcedefault-scope"></a>Você usou o escopo resource/.default?

Se você receber uma mensagem de erro dizendo que usou um `resource/.default` escopo inválido, provavelmente não usou o escopo.

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>Esqueceu de dar consentimento ao governo? Os aplicativos Daemon precisam disso!

Se você tiver **privilégios insuficientes para concluir o** erro de operação ao chamar a API, o administrador do inquilino precisa conceder permissões ao aplicativo. Veja a etapa 6 do Registrar o aplicativo do cliente acima.
Você normalmente verá um erro que se parece com este erro:

```JSon
Failed to call the web API: Forbidden
Content: {
  "error": {
    "code": "Authorization_RequestDenied",
    "message": "Insufficient privileges to complete the operation.",
    "innerError": {
      "request-id": "<guid>",
      "date": "<date>"
    }
  }
}
```

## <a name="next-steps"></a>Próximas etapas

# <a name="net"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Aplicativo Daemon - chamando uma API web](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=dotnet)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Aplicativo Daemon - chamando uma API web](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=python)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Aplicativo Daemon - chamando uma API web](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=java)

---
