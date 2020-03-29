---
title: Migrar APIs web baseadas em OWIN para b2clogin.com
titleSuffix: Azure AD B2C
description: Saiba como ativar uma API web .NET para suportar tokens emitidos por vários emissores de tokens enquanto você migra seus aplicativos para b2clogin.com.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5daf88e746ea803f345c79bd31d656f2615b6754
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184087"
---
# <a name="migrate-an-owin-based-web-api-to-b2clogincom"></a>Migre uma API web baseada em OWIN para b2clogin.com

Este artigo descreve uma técnica para habilitar o suporte para vários emissores de tokens em APIs da Web que implementam a [Interface Web Aberta para .NET (OWIN)](http://owin.org/). O suporte a vários pontos finais de token é útil quando você está migrando APIs do Azure Active Directory B2C (Azure AD B2C) e suas aplicações de *login.microsoftonline.com* a *b2clogin.com*.

Adicionando suporte em sua API para aceitar tokens emitidos por b2clogin.com e login.microsoftonline.com, você pode migrar seus aplicativos da Web de forma em etapa antes de remover o suporte para tokens emitidos por login.microsoftonline.com da API.

As seções a seguir apresentam um exemplo de como ativar vários emissores em uma API web que usa os componentes de middleware [Do Microsoft OWIN][katana] (Katana). Embora os exemplos de código sejam específicos do middleware Microsoft OWIN, a técnica geral deve ser aplicável a outras bibliotecas OWIN.

> [!NOTE]
> Este artigo destina-se aos clientes Azure AD B2C com `login.microsoftonline.com` APIs e aplicativos atualmente implantados que fazem referência e que desejam migrar para o ponto final recomendado. `b2clogin.com` Se você estiver configurando um novo aplicativo, use [b2clogin.com](b2clogin.md) como indicado.

## <a name="prerequisites"></a>Pré-requisitos

Você precisa dos seguintes recursos Azure AD B2C em vigor antes de continuar com as etapas deste artigo:

* [Fluxos de usuários](tutorial-create-user-flows.md) ou [políticas personalizadas](custom-policy-get-started.md) criadas em seu inquilino

## <a name="get-token-issuer-endpoints"></a>Obter pontos finais do emissor de token

Primeiro, você precisa obter uris de ponto final do emissor de token para cada emissor que você deseja suportar em sua API. Para obter o *b2clogin.com* e *login.microsoftonline.com* pontos finais suportados pelo seu inquilino Azure AD B2C, use o seguinte procedimento no portal Azure.

Comece selecionando um de seus fluxos de usuário existentes:

1. Navegue até o seu inquilino Azure AD B2C no [portal Azure](https://portal.azure.com)
1. Em **Políticas,** selecione **fluxos de usuário (políticas)**
1. Selecione uma diretiva existente, por exemplo, *B2C_1_signupsignin1*e selecione **Executar fluxo de usuário**
1. a direção do fluxo de **usuário Executar** perto do topo da página, selecione o hiperlink para navegar até o ponto final de detecção do OpenID Connect para esse fluxo de usuário.

    ![Hiperlink URI bem conhecido na página Executar agora do portal Azure](media/multi-token-endpoints/portal-01-policy-link.png)

1. Na página que abre no seu `issuer` navegador, registre o valor, por exemplo:

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

1. Use o **domínio Select** para seleção do outro domínio e execute `issuer` as duas etapas anteriores mais uma vez e registre seu valor.

Agora você deve ter duas URIs gravadas que são semelhantes a:

```
https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
```

### <a name="custom-policies"></a>Políticas personalizadas

Se você tiver políticas personalizadas em vez de fluxos de usuário, você pode usar um processo semelhante para obter uris do emissor.

1. Navegue até o seu inquilino Azure AD B2C
1. Selecione **o Framework de experiência de identidade**
1. Selecione uma de suas políticas partidárias, por exemplo, *B2C_1A_signup_signin*
1. Use o **domínio Select** para selecionar um domínio, por exemplo, *yourtenant.b2clogin.com*
1. Selecione o hiperlink exibido no **ponto final da detecção do OpenID Connect**
1. Registre `issuer` o valor
1. Executar as etapas 4-6 para o outro domínio, por *exemplo, login.microsoftonline.com*

## <a name="get-the-sample-code"></a>Obter o código de amostra

Agora que você tem ambas as URIs de ponto final do token, você precisa atualizar seu código para especificar que ambos os pontos finais são emissores válidos. Para passar por um exemplo, baixe ou clone o aplicativo de amostra e atualize a amostra para suportar ambos os pontos finais como emissores válidos.

Baixe o arquivo: [active-directory-b2c-dotnet-webapp-and-webapi-master.zip][sample-archive]

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

## <a name="enable-multiple-issuers-in-web-api"></a>Habilitar vários emissores na API web

Nesta seção, você atualiza o código para especificar que ambos os pontos finais do emissor de token são válidos.

1. Abra a solução **B2C-WebAPI-DotNet.sLn** no Visual Studio
1. No projeto **TaskService,** abra o arquivo *\\TaskService App_Start\\**Startup.Auth.cs*** em seu editor
1. Adicione a `using` seguinte diretiva à parte superior do arquivo:

    `using System.Collections.Generic;`
1. Adicione [`ValidIssuers`][validissuers] a propriedade [`TokenValidationParameters`][tokenvalidationparameters] à definição e especifique ambas as URIs que você gravou na seção anterior:

    ```csharp
    TokenValidationParameters tvps = new TokenValidationParameters
    {
        // Accept only those tokens where the audience of the token is equal to the client ID of this app
        ValidAudience = ClientId,
        AuthenticationType = Startup.DefaultPolicy,
        ValidIssuers = new List<string> {
            "https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/",
            "https://{your-b2c-tenant}.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/"
        }
    };
    ```

`TokenValidationParameters`é fornecido por MSAL.NET e é consumido pelo middleware OWIN na próxima seção de código em *Startup.Auth.cs*. Com vários emissores válidos especificados, o pipeline de aplicativos OWIN é informado de que ambos os pontos finais do token são emissores válidos.

```csharp
app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
{
    // This SecurityTokenProvider fetches the Azure AD B2C metadata &  from the OpenID Connect metadata endpoint
    AccessTokenFormat = new JwtFormat(tvps, new tCachingSecurityTokenProvider(String.Format(AadInstance, ultPolicy)))
});
```

Como mencionado anteriormente, outras bibliotecas OWIN normalmente fornecem uma facilidade semelhante para suportar vários emissores. Embora fornecer exemplos para cada biblioteca esteja fora do escopo deste artigo, você pode usar uma técnica semelhante para a maioria das bibliotecas.

## <a name="switch-endpoints-in-web-app"></a>Alternar pontos finais no aplicativo web

Com ambas as URIs agora suportadas pela sua API web, agora você precisa atualizar seu aplicativo web para que ele recupere tokens do ponto final b2clogin.com.

Por exemplo, você pode configurar o aplicativo web de amostra `ida:AadInstance` para usar o novo ponto final modificando o valor no arquivo *TaskWebApp\\**Web.config*** do projeto **TaskWebApp.**

Alterar `ida:AadInstance` o valor na *Web.config* do TaskWebApp `{your-b2c-tenant-name}.b2clogin.com` para `login.microsoftonline.com`que ele faça referências em vez de .

Antes:

```xml
<!-- Old value -->
<add key="ida:AadInstance" value="https://login.microsoftonline.com/tfp/{0}/{1}" />
```

Depois (substitua pelo `{your-b2c-tenant}` nome do seu inquilino B2C):

```xml
<!-- New value -->
<add key="ida:AadInstance" value="https://{your-b2c-tenant}.b2clogin.com/tfp/{0}/{1}" />
```

Quando as strings de ponto final são construídas durante a execução do aplicativo web, os pontos finais baseados em b2clogin.com são usados quando ele solicita tokens.

## <a name="next-steps"></a>Próximas etapas

Este artigo apresentou um método de configuração de uma API web implementando o middleware Microsoft OWIN (Katana) para aceitar tokens de vários pontos finais do emissor. Como você pode notar, existem várias outras strings nos arquivos *Web.Config* de ambos os projetos TaskService e TaskWebApp que precisariam ser alterados se você quiser construir e executar esses projetos contra seu próprio inquilino. Você é bem-vindo para modificar os projetos adequadamente se você quiser vê-los em ação, no entanto, um passo-a-passo completo de fazê-lo está fora do escopo deste artigo.

Para obter mais informações sobre os diferentes tipos de tokens de segurança emitidos pelo Azure AD B2C, consulte [Visão geral dos tokens no Azure Active Directory B2C](tokens-overview.md).

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip
[sample-repo]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi

<!-- LINKS - Internal -->
[katana]: https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/
[validissuers]: https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters.validissuers
[tokenvalidationparameters]: https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters
