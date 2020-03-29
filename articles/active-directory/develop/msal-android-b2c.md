---
title: Azure AD B2C (MSAL Android) | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre considerações específicas ao usar o Azure AD B2C com a Microsoft Authentication Library for Android (MSAL). Android)
services: active-directory
author: brianmel
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 9/18/2019
ms.author: brianmel
ms.reviewer: rapong
ms.custom: aaddev
ms.openlocfilehash: 0998bb04b0dfc69db4696f2e390cfe259eba6718
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696514"
---
# <a name="use-msal-for-android-with-b2c"></a>Use MSAL para Android com B2C

A MSAL (Biblioteca de Autenticação da Microsoft) permite que os desenvolvedores de aplicativos autentiquem os usuários com identidades sociais e locais usando o [Azure Active Directory B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/). O Azure AD B2C é um serviço de gerenciamento de identidades. Use-o para personalizar e controlar como os clientes se inscrevem, fazem login e gerenciam seus perfis quando usam seus aplicativos.

## <a name="configure-known-authorities-and-redirect-uri"></a>Configure autoridades conhecidas e redirecione o URI

No MSAL para Android, as políticas B2C (jornadas do usuário) são configuradas como autoridades individuais.

Dado um aplicativo B2C que tem duas políticas:
- Inscrição / Login
    * Chamado`B2C_1_SISOPolicy`
- Editar Perfil
    * Chamado`B2C_1_EditProfile`

O arquivo de configuração `authorities`do aplicativo declararia dois . Um para cada apólice. A `type` propriedade de `B2C`cada autoridade é.

### `app/src/main/res/raw/msal_config.json`
```json
{
    "client_id": "<your_client_id_here>",
    "redirect_uri": "<your_redirect_uri_here>",
    "authorities": [{
            "type": "B2C",
            "authority_url": "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/",
            "default": true
        },
        {
            "type": "B2C",
            "authority_url": "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_EditProfile/"
        }
    ]
}
```

O `redirect_uri` deve ser registrado na configuração `AndroidManifest.xml` do aplicativo, e também para suportar o redirecionamento durante o [fluxo de concessão do código de autorização](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-oauth-code).

## <a name="initialize-ipublicclientapplication"></a>Inicialize iPublicClientApplication

`IPublicClientApplication`é construído por um método de fábrica para permitir que a configuração do aplicativo seja analisado de forma assíncrona.

```java
PublicClientApplication.createMultipleAccountPublicClientApplication(
    context, // Your application Context
    R.raw.msal_config, // Id of app JSON config
    new IPublicClientApplication.ApplicationCreatedListener() {
        @Override
        public void onCreated(IMultipleAccountPublicClientApplication pca) {
            // Application has been initialized.
        }

        @Override
        public void onError(MsalException exception) {
            // Application could not be created.
            // Check Exception message for details.
        }
    }
);
```

## <a name="interactively-acquire-a-token"></a>Adquira interativamente um token

Para adquirir um token interativamente com `AcquireTokenParameters` o MSAL, `acquireToken` construa uma instância e forneça-o ao método. A solicitação de token abaixo usa a `default` autoridade.

```java
IMultipleAccountPublicClientApplication pca = ...; // Initialization not shown

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(new AuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            // Token request was successful, inspect the result
        }

        @Override
        public void onError(MsalException exception) {
            // Token request was unsuccessful, inspect the exception
        }

        @Override
        public void onCancel() {
            // The user cancelled the flow
        }
    }).build();

pca.acquireToken(parameters);
```

## <a name="silently-renew-a-token"></a>Renovar silenciosamente um token

Para adquirir um token silenciosamente com `AcquireTokenSilentParameters` a MSAL, `acquireTokenSilentAsync` construa uma instância e forneça-o ao método. Ao `acquireToken` contrário do `authority` método, deve ser especificado para adquirir um token silenciosamente.

```java
IMultilpeAccountPublicClientApplication pca = ...; // Initialization not shown
AcquireTokenSilentParameters parameters = new AcquireTokenSilentParameters.Builder()
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .forAccount(account)
    // Select a configured authority (policy), mandatory for silent auth requests
    .fromAuthority("https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/")
    .callback(new SilentAuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            // Token request was successful, inspect the result
        }

        @Override
        public void onError(MsalException exception) {
            // Token request was unsuccesful, inspect the exception
        }
    })
    .build();

pca.acquireTokenSilentAsync(parameters);
```

## <a name="specify-a-policy"></a>Especifique uma política

Como as políticas em B2C são representadas como autoridades separadas, invocar `fromAuthority` uma política `acquireToken` `acquireTokenSilent` diferente do padrão é alcançado especificando uma cláusula ao construir ou parâmetros.  Por exemplo: 

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(...) // provide callback here
    .fromAuthority("<url_of_policy_defined_in_configuration_json>")
    .build();
```

## <a name="handle-password-change-policies"></a>Lidar com políticas de mudança de senha

O fluxo de usuário de login de conta local ou de login mostra uma '**senha esquecida?**' . Clicar nesse link não dispara automaticamente um fluxo de usuário de redefinição de senha.

Em vez disso, o código de erro `AADB2C90118` é retornado para seu aplicativo. Seu aplicativo deve lidar com esse código de erro executando um fluxo de usuário específico que redefine a senha.

Para capturar um código de erro de redefinição `AuthenticationCallback`de senha, a implementação a seguir pode ser usada dentro do seu:

```java
new AuthenticationCallback() {

    @Override
    public void onSuccess(IAuthenticationResult authenticationResult) {
        // ..
    }

    @Override
    public void onError(MsalException exception) {
        final String B2C_PASSWORD_CHANGE = "AADB2C90118";

        if (exception.getMessage().contains(B2C_PASSWORD_CHANGE)) {
            // invoke password reset flow
        }
    }

    @Override
    public void onCancel() {
        // ..
    }
}
```

## <a name="use-iauthenticationresult"></a>Use IAuthenticationResult

Uma aquisição de token `IAuthenticationResult` bem sucedida resulta em um objeto. Ele contém o token de acesso, as reivindicações do usuário e os metadados.

### <a name="get-the-access-token-and-related-properties"></a>Obtenha o token de acesso e propriedades relacionadas

```java
// Get the raw bearer token
String accessToken = authenticationResult.getAccessToken();

// Get the scopes included in the access token
String[] accessTokenScopes = authenticationResult.getScope();

// Gets the access token's expiry
Date expiry = authenticationResult.getExpiresOn();

// Get the tenant for which this access token was issued
String tenantId = authenticationResult.getTenantId();
```

### <a name="get-the-authorized-account"></a>Obtenha a conta autorizada

```java
// Get the account from the result
IAccount account = authenticationResult.getAccount();

// Get the id of this account - note for B2C, the policy name is a part of the id
String id = account.getId();

// Get the IdToken Claims
//
// For more information about B2C token claims, see reference documentation
// https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens
Map<String, ?> claims = account.getClaims();

// Get the 'preferred_username' claim through a convenience function
String username = account.getUsername();

// Get the tenant id (tid) claim through a convenience function
String tenantId = account.getTenantId();
```

### <a name="idtoken-claims"></a>Reivindicações do IdToken

As reivindicações retornadas no IdToken são preenchidas pelo Serviço de Token de Segurança (STS), não pela MSAL. Dependendo do provedor de identidade (IdP) usado, algumas reclamações podem estar ausentes. Alguns IDPs não fornecem `preferred_username` atualmente a reivindicação. Como esta reivindicação é usada pela MSAL para `MISSING FROM THE TOKEN RESPONSE`cache, um valor de espaço reservado, é usado em seu lugar. Para obter mais informações sobre as reivindicações do B2C IdToken, consulte [Visão geral dos tokens no Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens#claims).

## <a name="managing-accounts-and-policies"></a>Gerenciamento de contas e políticas

B2C trata cada política como uma autoridade separada. Assim, os tokens de acesso, tokens de atualização e tokens de ID retornados de cada diretiva não são intercambiáveis. Isso significa que cada `IAccount` diretiva retorna um objeto separado cujos tokens não podem ser usados para invocar outras políticas.

Cada diretiva `IAccount` adiciona um ao cache de cada usuário. Se um usuário entrar em um aplicativo e invocar duas `IAccount`políticas, ele terá duas s. Para remover este usuário do cache, você deve chamar `removeAccount()` para cada diretiva.

Quando você renovar tokens `acquireTokenSilent`para uma `IAccount` política com , fornecer o mesmo que `AcquireTokenSilentParameters`foi devolvido de invocações anteriores da política para . Fornecer uma conta retornada por outra política resultará em um erro.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o Azure Active Directory B2C (Azure AD B2C) no [Qual é o Azure Active Directory B2C?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
