---
title: Guia de migração ADAL para MSAL (MSAL4j) | Azure
titleSuffix: Microsoft identity platform
description: Saiba como migrar o aplicativo Java (ADAL) Da Biblioteca de Autenticação de Diretório Ativo do Azure para a Biblioteca de Autenticação microsoft (MSAL).
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: Java
ms.workload: identity
ms.date: 11/04/2019
ms.author: sagonzal
ms.reviewer: nacanuma, twhitney
ms.custom: aaddev
ms.openlocfilehash: 2d85a3a5d876d731655bb30f7d37a6f42fa5c220
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696717"
---
# <a name="adal-to-msal-migration-guide-for-java"></a>Guia de migração ADAL para MSAL para Java

Este artigo destaca as alterações que você precisa fazer para migrar um aplicativo que usa a Adal (Active Directory Authentication Library, biblioteca de autenticação ativa do azure) para usar a Microsoft Authentication Library (MSAL).

Tanto a Biblioteca de Autenticação microsoft para Java (MSAL4J) quanto a Biblioteca de Autenticação Ad do Azure para Java (ADAL4J) são usadas para autenticar entidades Azure AD e solicitar tokens do Azure AD. Até agora, a maioria dos desenvolvedores trabalhou com o Azure AD para a plataforma de desenvolvedores (v1.0) para autenticar identidades Azure AD (contas de trabalho e escola) solicitando tokens usando a Azure AD Authentication Library (ADAL).

A MSAL oferece os seguintes benefícios:

- Como ele usa o ponto final da plataforma de identidade da Microsoft mais nova, você pode autenticar um conjunto mais amplo de identidades da Microsoft, como identidades Azure AD, contas Microsoft e contas sociais e locais através do Azure AD Business to Consumer (B2C).
- Seus usuários terão a melhor experiência de cada sinal único.
- Seu aplicativo pode permitir o consentimento incremental, e o suporte ao acesso condicional é mais fácil.

MSAL for Java é a biblioteca auth que recomendamos que você use com a plataforma de identidade Microsoft. Nenhum novo recursos será implementado no ADAL4J. Todos os esforços daqui para frente estão focados na melhoria do MSAL.

## <a name="differences"></a>Diferenças

Se você tem trabalhado com o Azure AD para desenvolvedores (v1.0) endpoint (e ADAL4J), você pode querer ler [O que há de diferente sobre o ponto final da plataforma de identidade Microsoft (v2.0) ?](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison).

## <a name="scopes-not-resources"></a>Escopos não recursos

A ADAL4J adquire tokens para recursos, enquanto o MSAL for Java adquire tokens para escopos. Um número de classes MSAL para Java requerem um parâmetro de escopos. Este parâmetro é uma lista de strings que declaram as permissões e recursos desejados que são solicitados. Consulte os [escopos do Microsoft Graph](https://docs.microsoft.com/graph/permissions-reference) para ver os escopos de exemplo.

## <a name="core-classes"></a>Classes de núcleo

No ADAL4J, `AuthenticationContext` a classe representa sua conexão com o Serviço de Token de Segurança (STS), ou servidor de autorização, através de uma Autoridade. No entanto, o MSAL for Java foi projetado em torno de aplicativos clientes. Ele fornece duas `PublicClientApplication` classes `ConfidentialClientApplication` separadas: e para representar aplicativos clientes.  Este último, `ConfidentialClientApplication`representa um aplicativo que foi projetado para manter com segurança um segredo, como um identificador de aplicativo para um aplicativo daemon.

A tabela a seguir mostra como o ADAL4J funciona mapa para as novas funções MSAL for Java:

| Método ADAL4J| Método MSAL4J|
|------|-------|
|adquireToken (recurso string, credencial clientcredential, retorno de chamada de authenticationCallback) | adquireToken (ClientCredentialParameters)|
|adquirirToken (recurso string, afirmação do ClientAssertion, autenticaçãochamadachamada chamada de volta)|adquireToken (ClientCredentialParameters)|
|adquireToken (recurso string, credencial AssimétricaKeyCredential, AuthenticationCallback callback)|adquireToken (ClientCredentialParameters)|
|adquireToken (recurso string, String clientId, String username, String password, AuthenticationCallback callback)| adquirirToken (Nome de usuárioSenhaSenha)|
|adquireToken (recurso string, String clientId, String username, String password=null, AuthenticationCallback callback)|adquireToken (IntegratedWindowsAuthenticationParameters)|
|adquirirToken (recurso string, userAssertion userAssertion, clientCredential credencial, AuthenticationCallback callback)| adquireToken (OnBehalfOfParameters)|
|adquirirTokenByAuthorizationCode() | adquirirToken (AuthorizationCodeParameters) |
| adquirirDeviceCode() e adquirirTokenByDeviceCode()| adquirirToken (DeviceCodeParameters)|
|adquireTokenByRefreshToken()| adquireTokenSilently (SilentParameters)|

## <a name="iaccount-instead-of-iuser"></a>IAccount em vez de IUser

ADAL4J manipulou usuários. Embora um usuário represente um único agente humano ou de software, ele pode ter uma ou mais contas no sistema de identidade da Microsoft. Por exemplo, um usuário pode ter várias contas pessoais do Azure AD, Azure AD B2C ou Microsoft.

MSAL for Java define o conceito `IAccount` de Conta através da interface. Esta é uma mudança de quebra do ADAL4J, mas é uma boa porque captura o fato de que o mesmo usuário pode ter várias contas, e talvez até mesmo em diferentes diretórios Azure AD. O MSAL for Java fornece melhores informações em cenários de hóspedes porque as informações da conta doméstica são fornecidas.

## <a name="cache-persistence"></a>Persistência de cache

O ADAL4J não tinha suporte para cache de token.
O MSAL for Java adiciona um [cache de token](msal-acquire-cache-tokens.md) para simplificar o gerenciamento de vidas de tokens atualizando automaticamente tokens expirados quando possível e evitando solicitações desnecessárias para que o usuário forneça credenciais quando possível.

## <a name="common-authority"></a>Autoridade Comum

No v1.0, se `https://login.microsoftonline.com/common` você usar a autoridade, os usuários podem fazer login com qualquer conta azure Active Directory (AAD) (para qualquer organização).

Se você `https://login.microsoftonline.com/common` usar a autoridade em v2.0, os usuários podem fazer login com qualquer organização AAD, ou até mesmo uma conta pessoal da Microsoft (MSA). No MSAL for Java, se você quiser restringir o login a `https://login.microsoftonline.com/organizations` qualquer conta AAD, você precisa usar a autoridade (que é o mesmo comportamento do ADAL4J). Para especificar uma `authority` autoridade, defina o parâmetro no método `PublicClientApplication` [PublicClientApplication.Builder](https://javadoc.io/doc/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.Builder.html) quando criar sua classe.

## <a name="v10-and-v20-tokens"></a>Tokens v1.0 e v2.0

O ponto de extremidade v1.0 (usado pela ADAL) só emite tokens v1.0.

O ponto final v2.0 (usado pela MSAL) pode emitir tokens v1.0 e v2.0. Uma propriedade de manifesto do aplicativo de API Web permite que os desenvolvedores escolham qual versão do token é aceita. Veja `accessTokenAcceptedVersion` na [documentação de](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest) referência do manifesto da aplicação.

Para obter mais informações sobre tokens v1.0 e v2.0, consulte [tokens de acesso do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).

## <a name="adal-to-msal-migration"></a>Migração de ADAL para MSAL

No ADAL4J, os tokens de atualização foram expostos — o que permitiu aos desenvolvedores cachê-los. Eles então `AcquireTokenByRefreshToken()` usariam para habilitar soluções como a implementação de serviços de longa duração que atualizam dashboards em nome do usuário quando o usuário não está mais conectado.

O MSAL for Java não expõe tokens de atualização por razões de segurança. Em vez disso, a MSAL lida com tokens refrescantes para você.

O MSAL for Java possui uma API que permite migrar tokens de atualização que você adquiriu com o ADAL4j para o ClientApplication: [acquireToken (RefreshTokenParameters)](https://javadoc.io/static/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.html#acquireToken-com.microsoft.aad.msal4j.RefreshTokenParameters-). Com esse método, é possível fornecer o token de atualização usado anteriormente junto com todos os escopos (recursos) desejados. O token de atualização será trocado por um novo e armazenado em cache para uso pelo seu aplicativo.

O seguinte trecho de código mostra algum código de migração em um aplicativo cliente confidencial:

```java
String rt = GetCachedRefreshTokenForSIgnedInUser(); // Get refresh token from where you have them stored
Set<String> scopes = Collections.singleton("SCOPE_FOR_REFRESH_TOKEN");

RefreshTokenParameters parameters = RefreshTokenParameters.builder(scopes, rt).build();

PublicClientApplication app = PublicClientApplication.builder(CLIENT_ID) // ClientId for your application
                .authority(AUTHORITY)  //plug in your authority
                .build();

IAuthenticationResult result = app.acquireToken(parameters);
```

O `IAuthenticationResult` retorna um token de acesso e token de ID, enquanto seu novo token de atualização é armazenado no cache. O aplicativo também agora conterá uma IConta:

```java
Set<IAccount> accounts =  app.getAccounts().join();
```

Para usar os tokens que estão agora no cache, ligue para:

```java
SilentParameters parameters = SilentParameters.builder(scope, accounts.iterator().next()).build(); 
IAuthenticationResult result = app.acquireToken(parameters);
```
