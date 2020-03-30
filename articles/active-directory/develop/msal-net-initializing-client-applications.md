---
title: Inicialize MSAL.NET aplicativos clientes | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre a inicialização de aplicativos públicos de clientes e clientes confidenciais usando a Biblioteca de Autenticação Microsoft para .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/12/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 57ce6ab31421cd4016f7e204eeabce82f2f7e6a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084006"
---
# <a name="initialize-client-applications-using-msalnet"></a>Inicialize os aplicativos clientes usando MSAL.NET
Este artigo descreve a inicialização de aplicativos públicos de clientes e clientes confidenciais usando a Microsoft Authentication Library for .NET (MSAL.NET).  Para saber mais sobre os tipos de aplicativos clientes e as opções de configuração do aplicativo, leia a [visão geral](msal-client-applications.md).

Com MSAL.NET 3.x, a maneira recomendada de instanciar `PublicClientApplicationBuilder` um `ConfidentialClientApplicationBuilder`aplicativo é usando os construtores de aplicativos: e . Eles oferecem um mecanismo poderoso para configurar o aplicativo a partir do código, ou a partir de um arquivo de configuração, ou mesmo misturando ambas as abordagens.

## <a name="prerequisites"></a>Pré-requisitos
Antes de inicializar um aplicativo, primeiro você precisa [registrá-lo](quickstart-register-app.md) para que seu aplicativo possa ser integrado à plataforma de identidade da Microsoft.  Após o cadastro, você pode precisar das seguintes informações (que podem ser encontradas no portal azure):

- O ID do cliente (uma string representando um GUID)
- O URL do provedor de identidade (chamado de instância) e o público de login do seu aplicativo. Esses dois parâmetros são coletivamente conhecidos como autoridade.
- O ID do inquilino se você estiver escrevendo uma linha de aplicativo de negócios exclusivamente para sua organização (também chamado de aplicativo de inquilino único).
- O segredo do aplicativo (seqüência secreta do cliente) ou certificado (do tipo X509Certificate2) se for um aplicativo cliente confidencial.
- Para aplicativos web e, às vezes, para aplicativos públicos de clientes (em particular quando seu aplicativo precisa usar uma corretora), você também terá definido o redirectUri onde o provedor de identidade entrará em contato com seu aplicativo com os tokens de segurança.

## <a name="ways-to-initialize-applications"></a>Formas de inicializar aplicativos
Existem muitas maneiras diferentes de instanciar aplicativos de clientes.

### <a name="initializing-a-public-client-application-from-code"></a>Inicializando um aplicativo de cliente público a partir do código

O código a seguir instancia um aplicativo cliente público, usuários de login na nuvem pública do Microsoft Azure, com suas contas de trabalho e escola, ou suas contas pessoais da Microsoft.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-code"></a>Inicializando um aplicativo cliente confidencial a partir do código

Da mesma forma, o código a seguir instancia um `https://myapp.azurewebsites.net`aplicativo confidencial (um aplicativo da Web localizado em ) manipulação de tokens de usuários na nuvem pública do Microsoft Azure, com suas contas de trabalho e escola, ou suas contas pessoais da Microsoft. O aplicativo é identificado com o provedor de identidade compartilhando um segredo do cliente:

```csharp
string redirectUri = "https://myapp.azurewebsites.net";
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithClientSecret(clientSecret)
    .WithRedirectUri(redirectUri )
    .Build();
```

Como você deve saber, na produção, em vez de usar um segredo de cliente, você pode querer compartilhar com o Azure AD um certificado. O código seria então o seguinte:

```csharp
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithCertificate(certificate)
    .WithRedirectUri(redirectUri )
    .Build();
```

### <a name="initializing-a-public-client-application-from-configuration-options"></a>Inicializando um aplicativo cliente público a partir de opções de configuração

O código a seguir instancia um aplicativo cliente público a partir de um objeto de configuração, que pode ser preenchido programáticamente ou lido a partir de um arquivo de configuração:

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-configuration-options"></a>Inicializando um aplicativo cliente confidencial a partir de opções de configuração

O mesmo tipo de padrão se aplica a aplicativos confidenciais de clientes. Você também pode adicionar `.WithXXX` outros parâmetros usando modificadores (aqui um certificado).

```csharp
ConfidentialClientApplicationOptions options = GetOptions(); // your own method
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(options)
    .WithCertificate(certificate)
    .Build();
```

## <a name="builder-modifiers"></a>Modificadores de construtor

Nos trechos de código usando os `.With` construtores de aplicativos, uma série de `.WithCertificate` métodos podem ser aplicados como modificadores (por exemplo, e `.WithRedirectUri`). 

### <a name="modifiers-common-to-public-and-confidential-client-applications"></a>Modificadores comuns a aplicativos clientes públicos e confidenciais

Os modificadores que você pode definir em um cliente público ou construtor de aplicativos de cliente confidencial são:

|Modificador | Descrição|
|--------- | --------- |
|`.WithAuthority()`7 substituições | Define a autoridade padrão do aplicativo para uma autoridade Ad do Azure, com a possibilidade de escolher o Azure Cloud, o público, o inquilino (ID do inquilino ou nome de domínio) ou fornecer diretamente a autoridade URI.|
|`.WithAdfsAuthority(string)` | Define a autoridade padrão do aplicativo como uma autoridade ADFS.|
|`.WithB2CAuthority(string)` | Define a autoridade padrão do aplicativo como uma autoridade Azure AD B2C.|
|`.WithClientId(string)` | Substitui a iD do cliente.|
|`.WithComponent(string)` | Define o nome da biblioteca usando MSAL.NET (por razões de telemetria). |
|`.WithDebugLoggingCallback()` | Se chamado, o `Debug.Write` aplicativo ligará simplesmente para ativar rastreamentos de depuração. Consulte [Log](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) para obter mais informações.|
|`.WithExtraQueryParameters(IDictionary<string,string> eqp)` | Defina os parâmetros extras de consulta de nível de aplicativo que serão enviados em todas as solicitações de autenticação. Isso é superridicularizado em cada nível de método `.WithExtraQueryParameters pattern`de aquisição de tokens (com o mesmo ).|
|`.WithHttpClientFactory(IMsalHttpClientFactory httpClientFactory)` | Habilita cenários avançados, como a configuração de um proxy HTTP ou para forçar o MSAL a usar um HttpClient específico (por exemplo, em ASP.NET aplicativos web/APIs do Core).|
|`.WithLogging()` | Se chamado, o aplicativo chamará um callback com traços de depuração. Consulte [Log](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) para obter mais informações.|
|`.WithRedirectUri(string redirectUri)` | Substitui o URI de redirecionamento padrão. No caso de aplicações públicas de clientes, isso será útil para cenários envolvendo o corretor.|
|`.WithTelemetry(TelemetryCallback telemetryCallback)` | Define o delegado usado para enviar telemetria.|
|`.WithTenantId(string tenantId)` | Substitui a id do inquilino, ou a descrição do inquilino.|

### <a name="modifiers-specific-to-xamarinios-applications"></a>Modificadores específicos para aplicativos Xamarin.iOS

Os modificadores que você pode definir em um construtor de aplicativos cliente público no Xamarin.iOS são:

|Modificador | Descrição|
|--------- | --------- |
|`.WithIosKeychainSecurityGroup()` | **Somente Xamarin.iOS**: Define o grupo de segurança da cadeia de chaves do iOS (para a persistência do cache).|

### <a name="modifiers-specific-to-confidential-client-applications"></a>Modificadores específicos para aplicativos clienteconfidenciais

Os modificadores que você pode definir em um construtor de aplicativos cliente confidencial são:

|Modificador | Descrição|
|--------- | --------- |
|`.WithCertificate(X509Certificate2 certificate)` | Define o certificado que identifica o aplicativo com o Azure AD.|
|`.WithClientSecret(string clientSecret)` | Define o segredo do cliente (senha do aplicativo) identificando o aplicativo com o Azure AD.|

Estes modificadores são mutuamente exclusivos. Se você fornecer ambos, a MSAL lançará uma exceção significativa.

### <a name="example-of-usage-of-modifiers"></a>Exemplo de uso de modificadores

Vamos supor que sua aplicação seja uma linha de negócios, que é apenas para sua organização.  Então você pode escrever:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzurePublic, tenantId)
        .Build();
```

Onde se torna interessante é que a programação para nuvens nacionais agora foi simplificada. Se você quiser que seu aplicativo seja um aplicativo multi-inquilino em uma nuvem nacional, você pode escrever, por exemplo:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment, AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

Há também uma substituição para o ADFS (ADFS 2019 não é suportado no momento):
```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Finalmente, se você é um desenvolvedor Azure AD B2C, você pode especificar seu inquilino assim:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```
