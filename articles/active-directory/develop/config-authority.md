---
title: Configure provedores de identidade (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Aprenda a usar diferentes autoridades, como B2C, nuvens soberanas e usuários convidados, com MSAL para iOS e macOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 4810de772e44be22ee5bd4a9fb6ef0ef756e62f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085217"
---
# <a name="how-to-configure-msal-for-ios-and-macos-to-use-different-identity-providers"></a>Como: Configurar msal para iOS e macOS para usar diferentes provedores de identidade

Este artigo mostrará como configurar seu aplicativo de biblioteca de autenticação da Microsoft para iOS e macOS (MSAL) para diferentes autoridades, como Azure Active Directory (Azure AD), Business-to-Consumer (B2C), nuvens soberanas e usuários convidados.  Ao longo deste artigo, você geralmente pode pensar em uma autoridade como um provedor de identidade.

## <a name="default-authority-configuration"></a>Configuração de autoridade padrão

`MSALPublicClientApplication`é configurado com uma `https://login.microsoftonline.com/common`URL de autoridade padrão de , que é adequada para a maioria dos cenários do Azure Active Directory (AAD). A menos que você esteja implementando cenários avançados como nuvens nacionais, ou trabalhando com B2C, você não precisará mudá-lo.

> [!NOTE]
> A autenticação moderna com os Serviços ativos da Federação de Diretórios como provedor de identidade (ADFS) não é suportada (consulte [ADFS para desenvolvedores](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios) para obter detalhes). A ADFS é apoiada através da federação.

## <a name="change-the-default-authority"></a>Alterar a autoridade padrão

Em alguns cenários, como o business-to-consumer (B2C), você pode precisar mudar a autoridade padrão.

### <a name="b2c"></a>B2C

Para trabalhar com o B2C, a [Microsoft Authentication Library (MSAL)](reference-v2-libraries.md) requer uma configuração de autoridade diferente. A MSAL reconhece o formato de URL de uma autoridade como B2C por si só. O formato de autoridade `https://<host>/tfp/<tenant>/<policy>`B2C `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy`reconhecido é, por exemplo. No entanto, você também pode usar quaisquer outras URLs de autoridade B2C suportadas declarando autoridade como autoridade B2C explicitamente.

Para suportar um formato de URL `MSALB2CAuthority` arbitrário para B2C, pode ser definido com uma URL arbitrária, como esta:

Objective-C
```objc
NSURL *authorityURL = [NSURL URLWithString:@"arbitrary URL"];
MSALB2CAuthority *b2cAuthority = [[MSALB2CAuthority alloc] initWithURL:authorityURL
                                                                     error:&b2cAuthorityError];
```
Swift
```swift
guard let authorityURL = URL(string: "arbitrary URL") else {
    // Handle error
    return
}
let b2cAuthority = try MSALB2CAuthority(url: authorityURL)
```

Todas as autoridades B2C que não usam o formato padrão de autoridade B2C devem ser declaradas como autoridades conhecidas.

Adicione cada autoridade B2C diferente à lista de autoridades conhecidas, mesmo que as autoridades só diferam na política.

Objective-C
```objc
MSALPublicClientApplicationConfig *b2cApplicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:b2cAuthority];
b2cApplicationConfig.knownAuthorities = @[b2cAuthority];
```
Swift
```swift
let b2cApplicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: b2cAuthority)
b2cApplicationConfig.knownAuthorities = [b2cAuthority]
```

Quando seu aplicativo solicita uma nova diretiva, a URL de autoridade precisa ser alterada porque a URL da autoridade é diferente para cada política. 

Para `@property MSALAuthority *authority` configurar um aplicativo B2C, defina com uma ocorrência de in `MSALB2CAuthority` `MSALPublicClientApplicationConfig` antes de criar, `MSALPublicClientApplication`como esta:

Objective-C
```ObjC
    // Create B2C authority URL
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy"];
    
    MSALB2CAuthority *b2cAuthority = [[MSALB2CAuthority alloc] initWithURL:authorityURL
                                                                     error:&b2cAuthorityError];
    if (!b2cAuthority)
    {
        // Handle error
        return;
    }
    
    // Create MSALPublicClientApplication configuration
    MSALPublicClientApplicationConfig *b2cApplicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                                   initWithClientId:@"your-client-id"
                                                                   redirectUri:@"your-redirect-uri"
                                                                   authority:b2cAuthority];

    // Initialize MSALPublicClientApplication
    MSALPublicClientApplication *b2cApplication =
    [[MSALPublicClientApplication alloc] initWithConfiguration:b2cApplicationConfig error:&error];
    
    if (!b2cApplication)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    // Create B2C authority URL
    guard let authorityURL = URL(string: "https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy") else {
        // Handle error
        return
    }
    let b2cAuthority = try MSALB2CAuthority(url: authorityURL)

    // Create MSALPublicClientApplication configuration
    let b2cApplicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: b2cAuthority)

    // Initialize MSALPublicClientApplication
    let b2cApplication = try MSALPublicClientApplication(configuration: b2cApplicationConfig)
} catch {
    // Handle error
}
```

### <a name="sovereign-clouds"></a>Nuvens soberanas

Se o aplicativo for executado em uma nuvem soberana, `MSALPublicClientApplication`talvez seja necessário alterar a URL de autoridade no . O exemplo a seguir define a URL de autoridade para trabalhar com a nuvem AAD alemã:

Objective-C
```objc
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.de/common"];
    MSALAuthority *sovereignAuthority = [MSALAuthority authorityWithURL:authorityURL error:&authorityError];
    
    if (!sovereignAuthority)
    {
        // Handle error
        return;
    }
    
    MSALPublicClientApplicationConfig *applicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:sovereignAuthority];
    
    
    MSALPublicClientApplication *sovereignApplication = [[MSALPublicClientApplication alloc] initWithConfiguration:applicationConfig error:&error];
    
    
    if (!sovereignApplication)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    guard let authorityURL = URL(string: "https://login.microsoftonline.de/common") else {
        //Handle error
        return
    }
    let sovereignAuthority = try MSALAuthority(url: authorityURL)
            
    let applicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: sovereignAuthority)
            
    let sovereignApplication = try MSALPublicClientApplication(configuration: applicationConfig)
} catch {
    // Handle error
}
```

Você pode precisar passar diferentes escopos para cada nuvem soberana. Quais escopos enviar depende do recurso que você está usando. Por exemplo, você `"https://graph.microsoft.com/user.read"` pode usar `"https://graph.microsoft.de/user.read"` em nuvem mundial, e em nuvem alemã.

### <a name="signing-a-user-into-a-specific-tenant"></a>Assinar um usuário em um inquilino específico

Quando a URL de `"login.microsoftonline.com/common"`autoridade estiver definida para, o usuário será conectado ao seu inquilino doméstico. No entanto, alguns aplicativos podem precisar assinar com o usuário em um inquilino diferente e alguns aplicativos só funcionam com um único inquilino.

Para assinar com o usuário um `MSALPublicClientApplication` inquilino específico, configure-o com uma autoridade específica. Por exemplo: 

`https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4`

A seguir, mostra como assinar um usuário em um inquilino específico:

Objective-C
```objc
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4"];
    MSALAADAuthority *tenantedAuthority = [[MSALAADAuthority alloc] initWithURL:authorityURL error:&authorityError];
    
    if (!tenantedAuthority)
    {
        // Handle error
        return;
    }
    
    MSALPublicClientApplicationConfig *applicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:tenantedAuthority];
    
    MSALPublicClientApplication *application =
    [[MSALPublicClientApplication alloc] initWithConfiguration:applicationConfig error:&error];
    
    if (!application)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    guard let authorityURL = URL(string: "https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4") else {
        //Handle error
        return
    }    
    let tenantedAuthority = try MSALAADAuthority(url: authorityURL)
            
    let applicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: tenantedAuthority)
            
    let application = try MSALPublicClientApplication(configuration: applicationConfig)
} catch {
    // Handle error
}
```

## <a name="supported-authorities"></a>Autoridades apoiadas

### <a name="msalauthority"></a>MSALAuthority

A `MSALAuthority` classe é a classe abstrata base para as classes de autoridade msal. Não tente criar uma instância `alloc` dele `new`usando ou . Em vez disso, crie uma`MSALAADAuthority`de `MSALB2CAuthority`suas subclasses `authorityWithURL:error:` diretamente () ou use o método de fábrica para criar subclasses usando uma URL de autoridade.

Use `url` a propriedade para obter uma URL de autoridade normalizada. Parâmetros extras e componentes de caminho ou fragmentos que não fazem parte da autoridade não estarão na URL de autoridade normalizada devolvida.

As seguintes subclasses que `MSALAuthority` você pode instanciar dependendo da autoridade deseja usar.

### <a name="msalaadauthority"></a>MSALAADAuthority

`MSALAADAuthority`representa uma autoridade AAD. A url de autoridade deve estar `<port>` no seguinte formato, onde é opcional:`https://<host>:<port>/<tenant>`

### <a name="msalb2cauthority"></a>MSALB2CAuthority

`MSALB2CAuthority`representa uma autoridade B2C. Por padrão, a url de autoridade B2C `<port>` deve estar `https://<host>:<port>/tfp/<tenant>/<policy>`no seguinte formato, onde é opcional: . No entanto, a MSAL também suporta outros formatos arbitrários de autoridade B2C.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os [Fluxos de autenticação e cenários de aplicativos](authentication-flows-app-scenarios.md)
