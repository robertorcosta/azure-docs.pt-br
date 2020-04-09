---
title: Configure uma API web que chama APIs web | Azure
titleSuffix: Microsoft identity platform
description: Aprenda a construir uma API web que chama APIs da Web (configuração de código do aplicativo)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 4129f1a89575c9a0e7cd6a0090168df659356c1b
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885099"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>Uma API web que chama APIs da Web: configuração de código

Depois de registrar sua API web, você pode configurar o código para o aplicativo.

O código que você usa para configurar sua API web para que ele chame APIs web downstream é construído em cima do código usado para proteger uma API web. Para obter mais informações, consulte [API web protegida: configuração do aplicativo](scenario-protected-web-api-app-configuration.md).

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="code-subscribed-to-ontokenvalidated"></a>Código subscrito ao OnTokenValidado

Além da configuração de código para quaisquer APIs da Web protegidas, você precisa assinar a validação do token bearer que você recebe quando sua API é chamada:

```csharp
/// <summary>
/// Protects the web API with the Microsoft identity platform, or Azure Active Directory (Azure AD) developer platform
/// This supposes that the configuration files have a section named "AzureAD"
/// </summary>
/// <param name="services">The service collection to which to add authentication</param>
/// <param name="configuration">Configuration</param>
/// <returns></returns>
public static IServiceCollection AddProtectedApiCallsWebApis(this IServiceCollection services,
                                                             IConfiguration configuration,
                                                             IEnumerable<string> scopes)
{
    services.AddTokenAcquisition();
    services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        // When an access token for our own web API is validated, we add it
        // to the MSAL.NET cache so that it can be used from the controllers.
        options.Events = new JwtBearerEvents();

        options.Events.OnTokenValidated = async context =>
        {
            context.Success();

            // Adds the token to the cache and handles the incremental consent
            // and claim challenges
            AddAccountToCacheFromJwt(context, scopes);
            await Task.FromResult(0);
        };
    });
    return services;
}
```

## <a name="on-behalf-of-flow"></a>On-Behalf-Of flow

O método AddAccountToCacheFromJwt() precisa:

- Instanciar um aplicativo cliente confidencial da Microsoft Authentication Library (MSAL).
- Chame o método `AcquireTokenOnBehalf` . Esta chamada troca o token do portador que foi adquirido pelo cliente para a API web contra um token portador para o mesmo usuário, mas tem a API chamada uma API downstream.

### <a name="instantiate-a-confidential-client-application"></a>Instanciar um aplicativo de cliente confidencial

Esse fluxo está disponível apenas no fluxo de clienteconfidencial, de modo que a API web protegida forneça `WithClientSecret` credenciais do cliente (segredo ou certificado do cliente) para a [classe ConfidentialClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) através do método ou `WithCertificate` do método.

![Lista de métodos IConfidentialClientApplication](https://user-images.githubusercontent.com/13203188/55967244-3d8e1d00-5c7a-11e9-8285-a54b05597ec9.png)

```csharp
IConfidentialClientApplication app;

#if !VariationWithCertificateCredentials
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .Build();
#else
// Building the client credentials from a certificate
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .Build();
#endif
```

Finalmente, em vez de provar sua identidade através de um segredo de cliente ou um certificado, aplicativos confidenciais do cliente podem provar sua identidade usando afirmações do cliente.
Para obter mais informações sobre este cenário avançado, consulte [Afirmações confidenciais do cliente](msal-net-client-assertions.md).

### <a name="how-to-call-on-behalf-of"></a>Como ligar para o On-Behalf

Você faz a chamada On-Behalf-Of (OBO) chamando o `IConfidentialClientApplication` [método AcquireTokenOnBehalf](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder) na interface.

A `UserAssertion` classe é construída a partir do token bearer que é recebido pela API web de seus próprios clientes. Existem [dois construtores:](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet)
* Um que leva um token de portador JSON Web Token (JWT)
* Um que toma qualquer tipo de afirmação do usuário, outro tipo de token de segurança, cujo tipo é então especificado em um parâmetro adicional chamado`assertionType`

![Propriedades e métodos do UserAssertion](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

Na prática, o fluxo de OBO é frequentemente usado para adquirir um token para uma API a jusante e armazená-lo no cache de token do usuário MSAL.NET. Você faz isso para que outras partes da API ``AcquireTokenOnSilent`` da Web possam mais tarde chamar as [substituições](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet) de chamar as APIs a jusante. Esta chamada tem o efeito de atualizar os tokens, se necessário.

```csharp
private void AddAccountToCacheFromJwt(IEnumerable<string> scopes, JwtSecurityToken jwtToken, ClaimsPrincipal principal, HttpContext httpContext)
{
    try
    {
        UserAssertion userAssertion;
        IEnumerable<string> requestedScopes;
        if (jwtToken != null)
        {
            userAssertion = new UserAssertion(jwtToken.RawData, "urn:ietf:params:oauth:grant-type:jwt-bearer");
            requestedScopes = scopes ?? jwtToken.Audiences.Select(a => $"{a}/.default");
        }
        else
        {
            throw new ArgumentOutOfRangeException("tokenValidationContext.SecurityToken should be a JWT Token");
        }

        // Create the application
        var application = BuildConfidentialClientApplication(httpContext, principal);

        // .Result to make sure that the cache is filled in before the controller tries to get access tokens
        var result = application.AcquireTokenOnBehalfOf(requestedScopes.Except(scopesRequestedByMsalNet),
                                                        userAssertion)
                                .ExecuteAsync()
                                .GetAwaiter().GetResult();
     }
     catch (MsalException ex)
     {
         Debug.WriteLine(ex.Message);
         throw;
     }
}
```
# <a name="java"></a>[Java](#tab/java)

O fluxo on-behalf-of (OBO) é usado para obter um token para chamar a API web downstream. Nesse fluxo, sua API web recebe um token portador com permissões delegadas pelo usuário do aplicativo cliente e, em seguida, troca esse token por outro token de acesso para chamar a API web downstream.

O código abaixo usa a `SecurityContextHolder` estrutura de Segurança de Primavera na API web para obter o token do portador validado. Em seguida, ele usa a biblioteca MSAL Java para `acquireToken` obter `OnBehalfOfParameters`um token para API a jusante usando a chamada com . O MSAL armazena o token para que as `acquireTokenSilently` chamadas subseqüentes para a API possam ser usados para obter o token armazenado em cache.

```Java
@Component
class MsalAuthHelper {

    @Value("${security.oauth2.client.authority}")
    private String authority;

    @Value("${security.oauth2.client.client-id}")
    private String clientId;

    @Value("${security.oauth2.client.client-secret}")
    private String secret;

    @Autowired
    CacheManager cacheManager;

    private String getAuthToken(){
        String res = null;
        Authentication authentication = SecurityContextHolder.getContext().getAuthentication();

        if(authentication != null){
            res = ((OAuth2AuthenticationDetails) authentication.getDetails()).getTokenValue();
        }
        return res;
    }

    String getOboToken(String scope) throws MalformedURLException {
        String authToken = getAuthToken();

        ConfidentialClientApplication application =
                ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(secret))
                        .authority(authority).build();

        String cacheKey = Hashing.sha256()
                .hashString(authToken, StandardCharsets.UTF_8).toString();

        String cachedTokens = cacheManager.getCache("tokens").get(cacheKey, String.class);
        if(cachedTokens != null){
            application.tokenCache().deserialize(cachedTokens);
        }

        IAuthenticationResult auth;
        SilentParameters silentParameters =
                SilentParameters.builder(Collections.singleton(scope))
                        .build();
        auth = application.acquireTokenSilently(silentParameters).join();

        if (auth == null){
            OnBehalfOfParameters parameters =
                    OnBehalfOfParameters.builder(Collections.singleton(scope),
                            new UserAssertion(authToken))
                            .build();

            auth = application.acquireToken(parameters).join();
        }

        cacheManager.getCache("tokens").put(cacheKey, application.tokenCache().serialize());

        return auth.accessToken();
    }
}
```

# <a name="python"></a>[Python](#tab/python)

O fluxo on-behalf-of (OBO) é usado para obter um token para chamar a API web downstream. Nesse fluxo, sua API web recebe um token portador com permissões delegadas pelo usuário do aplicativo cliente e, em seguida, troca esse token por outro token de acesso para chamar a API web downstream.

Uma API web Python precisará usar alguns middleware para validar o token do portador recebido do cliente. A API da Web pode então obter o token de acesso [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) para API a jusante usando a biblioteca MSAL Python chamando o método. Uma amostra demonstrando esse fluxo com o MSAL Python ainda não está disponível.

---

Você também pode ver um exemplo de implementação de fluxo OBO [em Funções Node.js e Azure](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61).

## <a name="protocol"></a>Protocolo

Para obter mais informações sobre o protocolo OBO, consulte a [plataforma de identidade Microsoft e o fluxo On-Behalf-Of do OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Uma API web que chama APIs da Web: Adquira um token para o aplicativo](scenario-web-api-call-api-acquire-token.md)
