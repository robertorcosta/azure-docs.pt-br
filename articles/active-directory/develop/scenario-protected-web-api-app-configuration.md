---
title: Configurar aplicativos de API Web protegidos | Azure
titleSuffix: Microsoft identity platform
description: Saiba como criar uma API Web protegida e configurar o código do aplicativo.
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
ms.openlocfilehash: 3f07105c14d4dafeb689eaaf7d679f93e5f235fe
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77605337"
---
# <a name="protected-web-api-code-configuration"></a>API Web protegida: configuração de código

Para configurar o código para sua API Web protegida, você precisa entender:

- O que define as APIs como protegidas.
- Como configurar um token de portador.
- Como validar o token.

## <a name="what-defines-aspnet-and-aspnet-core-apis-as-protected"></a>O que define as APIs ASP.NET e ASP.NET Core como protegidas?

Assim como os aplicativos Web, as APIs da Web ASP.NET e ASP.NET Core são protegidas porque suas ações de controlador são prefixadas com o atributo **[Authorize]** . As ações do controlador poderão ser chamadas somente se a API for chamada com uma identidade autorizada.

Considere as seguintes perguntas:

- Somente um aplicativo pode chamar uma API da Web. Como a API sabe a identidade do aplicativo que a chama?
- Se o aplicativo chamar a API em nome de um usuário, qual é a identidade do usuário?

## <a name="bearer-token"></a>Token de portador

O token de portador definido no cabeçalho quando o aplicativo é chamado contém informações sobre a identidade do aplicativo. Ele também contém informações sobre o usuário, a menos que o aplicativo Web aceite chamadas de serviço a serviço de um aplicativo daemon.

Aqui está um C# exemplo de código que mostra um cliente que chama a API depois de adquirir um token com a biblioteca de autenticação da Microsoft para .net (MSAL.net):

```csharp
var scopes = new[] {$"api://.../access_as_user"};
var result = await app.AcquireToken(scopes)
                      .ExecuteAsync();

httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
```

> [!IMPORTANT]
> Um aplicativo cliente solicita o token de portador para o ponto de extremidade da plataforma *de identidade da Microsoft para a API da Web*. A API Web é o único aplicativo que deve verificar o token e exibir as declarações que ele contém. Os aplicativos cliente nunca devem tentar inspecionar as declarações em tokens.
>
> No futuro, a API Web pode exigir que o token seja criptografado. Esse requisito impediria o acesso para aplicativos cliente que podem exibir tokens de acesso.

## <a name="jwtbearer-configuration"></a>Configuração do JwtBearer

Esta seção descreve como configurar um token de portador.

### <a name="config-file"></a>Arquivo de configuração

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    /*
      You need specify the TenantId only if you want to accept access tokens from a single tenant
     (line-of-business app).
      Otherwise, you can leave them set to common.
      This can be:
      - A GUID (Tenant ID = Directory ID)
      - 'common' (any organization and personal accounts)
      - 'organizations' (any organization)
      - 'consumers' (Microsoft personal accounts)
    */
    "TenantId": "common"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="code-initialization"></a>Inicialização de código

Quando um aplicativo é chamado em uma ação do controlador que contém um atributo **[Authorize]** , ASP.NET e ASP.NET Core extrai o token de acesso do token de portador do cabeçalho de autorização. O token de acesso é encaminhado para o middleware JwtBearer, que chama as extensões Microsoft IdentityModel para .NET.

Em ASP.NET Core, esse middleware é inicializado no arquivo Startup.cs.

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

O middleware é adicionado à API Web por esta instrução:

```csharp
 services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
         .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

 Atualmente, os modelos de ASP.NET Core criam APIs da Web do Azure Active Directory (Azure AD) que conectam usuários em sua organização ou em qualquer organização. Eles não entram em usuários com contas pessoais. Mas você pode alterar os modelos para usar o ponto de extremidade da plataforma de identidade da Microsoft adicionando este código a Startup.cs:

```csharp
services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
{
    // This is a Microsoft identity platform web API.
    options.Authority += "/v2.0";

    // The web API accepts as audiences both the Client ID (options.Audience) and api://{ClientID}.
    options.TokenValidationParameters.ValidAudiences = new []
    {
     options.Audience,
     $"api://{options.Audience}"
    };

    // Instead of using the default validation (validating against a single tenant,
    // as we do in line-of-business apps),
    // we inject our own multitenant validation logic (which even accepts both v1 and v2 tokens).
    options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;;
});
```

O trecho de código anterior é extraído do ASP.NET Core tutorial incremental da API Web em [Microsoft. Identity. Web/WebApiServiceCollectionExtensions. cs # L50-L63](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/154282843da2fc2958fad151e2a11e521e358d42/Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63). O método **AddProtectedWebApi** , que faz mais do que o trecho de código, é chamado de startup.cs.

## <a name="token-validation"></a>Validação de token

No trecho anterior, o middleware JwtBearer, como o middleware OpenID Connect em aplicativos Web, valida o token com base no valor de `TokenValidationParameters`. O token é descriptografado conforme necessário, as declarações são extraídas e a assinatura é verificada. Em seguida, o middleware valida o token verificando os dados:

- Público-alvo: o token é direcionado para a API Web.
- Sub: ele foi emitido para um aplicativo que tem permissão para chamar a API da Web.
- Emissor: ele foi emitido por um serviço de token de segurança (STS) confiável.
- Expiração: seu tempo de vida está no intervalo.
- Assinatura: ela não foi violada.

Também pode haver validações especiais. Por exemplo, é possível validar que as chaves de assinatura, quando inseridas em um token, sejam confiáveis e que o token não esteja sendo repetido. Por fim, alguns protocolos exigem validações específicas.

### <a name="validators"></a>Validadores

As etapas de validação são capturadas em validadores, que são fornecidas pela biblioteca do [Microsoft IdentityModel Extensions para .net](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) Open-Source. Os validadores são definidos no arquivo de origem da biblioteca [Microsoft. IdentityModel. Tokens/validators. cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs).

Esta tabela descreve os validadores:

| Validator | DESCRIÇÃO |
|---------|---------|
| **ValidateAudience** | Garante que o token seja para o aplicativo que valida o token para você. |
| **ValidateIssuer** | Garante que o token foi emitido por um STS confiável, o que significa que ele é de alguém em que você confia. |
| **ValidateIssuerSigningKey** | Garante que o aplicativo que valida o token confie na chave que foi usada para assinar o token. Há um caso especial em que a chave é inserida no token. Mas esse caso normalmente não ocorre. |
| **ValidateLifetime** | Garante que o token ainda seja válido ou já seja válida. O validador verifica se o tempo de vida do token está no intervalo especificado pelas declarações **nobefore** e **Expires** . |
| **ValidateSignature** | Garante que o token não tenha sido adulterado. |
| **ValidateTokenReplay** | Garante que o token não seja reproduzido. Há um caso especial para alguns protocolos de uso de OneTime. |

Os validadores são associados às propriedades da classe **TokenValidationParameters** . As propriedades são inicializadas a partir do ASP.NET e ASP.NET Core configuração.

Na maioria dos casos, você não precisa alterar os parâmetros. Aplicativos que não são locatários únicos são exceções. Esses aplicativos Web aceitam usuários de qualquer organização ou de contas pessoais da Microsoft. Os emissores nesse caso devem ser validados.

## <a name="token-validation-in-azure-functions"></a>Validação de token no Azure Functions

Você também pode validar tokens de acesso de entrada no Azure Functions. Você pode encontrar exemplos de tal validação em [Microsoft .net](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions), [NodeJS](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)e [python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Verificar escopos e funções de aplicativo em seu código](scenario-protected-web-api-verification-scope-app-roles.md)
