---
title: Configure aplicativos de API web protegidos | Azure
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
ms.openlocfilehash: 3f07105c14d4dafeb689eaaf7d679f93e5f235fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262510"
---
# <a name="protected-web-api-code-configuration"></a>API web protegida: configuração de código

Para configurar o código para sua API web protegida, você precisa entender:

- O que define ASIs como protegidos.
- Como configurar um token portador.
- Como validar o token.

## <a name="what-defines-aspnet-and-aspnet-core-apis-as-protected"></a>O que define ASP.NET e ASP.NET APIs principais como protegidos?

Como aplicativos web, as APIs web ASP.NET e ASP.NET Core são protegidas porque suas ações de controlador são prefixadas com o atributo **[Autorizar].** As ações do controlador só podem ser chamadas se a API for chamada com uma identidade autorizada.

Considere as perguntas a seguir:

- Apenas um aplicativo pode chamar uma API web. Como a API sabe a identidade do aplicativo que o chama?
- Se o aplicativo chamar a API em nome de um usuário, qual é a identidade do usuário?

## <a name="bearer-token"></a>Token de portador

O token do portador definido no cabeçalho quando o aplicativo é chamado contém informações sobre a identidade do aplicativo. Ele também contém informações sobre o usuário, a menos que o aplicativo web aceite chamadas de serviço para serviço de um aplicativo daemon.

Aqui está um exemplo de código C# que mostra um cliente chamando a API depois de adquirir um token com a Microsoft Authentication Library para .NET (MSAL.NET):

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
> Um aplicativo cliente solicita o token bearer para o ponto final da plataforma de identidade Microsoft *para a API web*. A API web é o único aplicativo que deve verificar o token e visualizar as reivindicações que ele contém. Os aplicativos clientes nunca devem tentar inspecionar as reivindicações em tokens.
>
> No futuro, a API da Web pode exigir que o token seja criptografado. Essa exigência impediria o acesso a aplicativos clientes que podem visualizar tokens de acesso.

## <a name="jwtbearer-configuration"></a>Configuração JwtBearer

Esta seção descreve como configurar um token portador.

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

Quando um aplicativo é chamado para uma ação controladora que contém um atributo **[Autorizar],** ASP.NET e ASP.NET Core extraia o token de acesso do token portador do cabeçalho de autorização. O token de acesso é então encaminhado para o middleware JwtBearer, que chama o Microsoft IdentityModel Extensions para .NET.

No ASP.NET Core, esse middleware é inicializado no arquivo Startup.cs.

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

O middleware é adicionado à API da Web por esta instrução:

```csharp
 services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
         .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

 Atualmente, os modelos ASP.NET Core criam APIs da Web Azure Active Directory (Azure AD) que se inscrevam em usuários dentro de sua organização ou de qualquer organização. Eles não fazem login em usuários com contas pessoais. Mas você pode alterar os modelos para usar o ponto final da plataforma de identidade da Microsoft adicionando este código ao Startup.cs:

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

O trecho de código anterior é extraído do tutorial incremental da API da Web ASP.NET no [Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/154282843da2fc2958fad151e2a11e521e358d42/Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63). O método **AddProtectedWebApi,** que faz mais do que o trecho mostra, é chamado de Startup.cs.

## <a name="token-validation"></a>Validação de token

No trecho anterior, o middleware JwtBearer, como o middleware OpenID Connect em aplicativos web, `TokenValidationParameters`valida o token com base no valor de . O token é descriptografado conforme necessário, as reivindicações são extraídas e a assinatura é verificada. O middleware valida o token verificando esses dados:

- Audiência: O token é direcionado para a API web.
- Sub: Foi emitido para um aplicativo que é permitido chamar a API web.
- Emissor: Foi emitido por um serviço de token de segurança confiável (STS).
- Expiração: Sua vida útil está ao alcance.
- Assinatura: Não foi adulterado.

Também pode haver validações especiais. Por exemplo, é possível validar que as teclas de assinatura, quando incorporadas em um token, são confiáveis e que o token não está sendo reproduzido. Finalmente, alguns protocolos exigem validações específicas.

### <a name="validators"></a>Validadores

As etapas de validação são capturadas em validadores, que são fornecidos pela Biblioteca de código aberto [.NET.](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) Os validadores são definidos no arquivo de origem da biblioteca [Microsoft.IdentityModel.Tokens/Validators.cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs).

Esta tabela descreve os validadores:

|  Validator | Descrição |
|---------|---------|
| **ValidarAudiência** | Garante que o token é para o aplicativo que valida o token para você. |
| **Validar Emissor** | Garante que o token foi emitido por um STS confiável, o que significa que é de alguém em quem você confia. |
| **ValidarAChave deassinatura do emissor** | Garante que o aplicativo validando o token confie na chave usada para assinar o token. Há um caso especial onde a chave está embutida no token. Mas este caso não costuma surgir. |
| **ValidarVida** | Garante que o token ainda está ou já é válido. O validador verifica se a vida útil do token está no intervalo especificado pelo **não antes** e expira as **reivindicações.** |
| **ValidarAssinatura** | Garante que o token não foi adulterado. |
| **ValidTokenReplay** | Garante que o token não seja reproduzido. Há um caso especial para alguns protocolos de uso único. |

Os validadores estão associados às propriedades da classe **TokenValidationParameters.** As propriedades são inicializadas a partir da configuração ASP.NET e ASP.NET Core.

Na maioria dos casos, você não precisa mudar os parâmetros. Aplicativos que não são inquilinos solteiros são exceções. Esses aplicativos da Web aceitam usuários de qualquer organização ou de contas pessoais da Microsoft. Os emissores neste caso devem ser validados.

## <a name="token-validation-in-azure-functions"></a>Validação de tokens em funções do Azure

Você também pode validar tokens de acesso de entrada em Funções Azure. Você pode encontrar exemplos de tal validação em [Microsoft .NET,](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions) [NodeJS](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)e [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Verifique escopos e funções de aplicativos em seu código](scenario-protected-web-api-verification-scope-app-roles.md)
