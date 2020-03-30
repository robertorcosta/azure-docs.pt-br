---
title: Adquira um token para chamar uma API web (aplicativos móveis) | Azure
titleSuffix: Microsoft identity platform
description: Aprenda a criar um aplicativo móvel que chama APIs da Web. (Obtenha um token para o aplicativo.)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 9427235f47a31da75426559a4285634ab2837577
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132456"
---
# <a name="get-a-token-for-a-mobile-app-that-calls-web-apis"></a>Obtenha um token para um aplicativo móvel que chama APIs da Web

Antes que seu aplicativo possa chamar APIs da Web protegidas, ele precisa de um token de acesso. Este artigo orienta você através do processo para obter um token usando a Microsoft Authentication Library (MSAL).

## <a name="define-a-scope"></a>Definir um escopo

Quando você solicita um token, você precisa definir um escopo. O escopo determina quais dados seu aplicativo pode acessar.  

A maneira mais fácil de definir um escopo é combinar `App ID URI` as `.default`API web desejadas com o escopo . Essa definição diz à plataforma de identidade da Microsoft que seu aplicativo requer todos os escopos definidos no portal.

### <a name="android"></a>Android
```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
```

### <a name="ios"></a>iOS
```swift
let scopes = ["https://graph.microsoft.com/.default"]
```

### <a name="xamarin"></a>Xamarin
```csharp 
var scopes = new [] {"https://graph.microsoft.com/.default"};
```

## <a name="get-tokens"></a>Obter tokens

### <a name="acquire-tokens-via-msal"></a>Adquira tokens via MSAL

A MSAL permite que os aplicativos adquiram tokens de forma silenciosa e interativa. Quando você `AcquireTokenSilent()` `AcquireTokenInteractive()`liga ou , O MSAL retorna um token de acesso para os escopos solicitados. O padrão correto é fazer uma solicitação silenciosa e, em seguida, voltar para uma solicitação interativa.

#### <a name="android"></a>Android

```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);

// Check if there are any accounts we can sign in silently.
// Result is in the silent callback (success or error).
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {

        if (accounts.isEmpty() && accounts.size() == 1) {
            // TODO: Create a silent callback to catch successful or failed request.
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts or > 1 account. */
        }
    }
});    

[...]

// No accounts found. Interactively request a token.
// TODO: Create an interactive callback to catch successful or failed requests.
sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());        
```

#### <a name="ios"></a>iOS

Primeiro tente adquirir um token silenciosamente:

```objc

NSArray *scopes = @[@"https://graph.microsoft.com/.default"];
NSString *accountIdentifier = @"my.account.id";
    
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];
    
MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {
        
    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;
            
        // Access token to call the Web API
        NSString *accessToken = result.accessToken;
    }
        
    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
        return;
    }
}];
```
 
```swift

let scopes = ["https://graph.microsoft.com/.default"]
let accountIdentifier = "my.account.id"
        
guard let account = try? application.account(forIdentifier: accountIdentifier) else { return }
let silentParameters = MSALSilentTokenParameters(scopes: scopes, account: account)
application.acquireTokenSilent(with: silentParameters) { (result, error) in
            
    guard let authResult = result, error == nil else {
                
    let nsError = error! as NSError
                
    if (nsError.domain == MSALErrorDomain &&
        nsError.code == MSALError.interactionRequired.rawValue) {
                    
            // Interactive auth will be required, call acquireToken()
            return
         }
         return
     }
            
    // You'll want to get the account identifier to retrieve and reuse the account
    // for later acquireToken calls
    let accountIdentifier = authResult.account.identifier
            
    // Access token to call the Web API
    let accessToken = authResult.accessToken
}
```

Se o `MSALErrorInteractionRequired`MSAL retornar, tente adquirir tokens de forma interativa:

```objc
UIViewController *viewController = ...; // Pass a reference to the view controller that should be used when getting a token interactively
MSALWebviewParameters *webParameters = [[MSALWebviewParameters alloc] initWithParentViewController:viewController];
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:webParameters];
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) {
    if (!error) 
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;
            
        NSString *accessToken = result.accessToken;
    }
}];
```

```swift
let viewController = ... // Pass a reference to the view controller that should be used when getting a token interactively
let webviewParameters = MSALWebviewParameters(parentViewController: viewController)
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: webviewParameters)
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in
                
    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }
                
    // Get access token from result
    let accessToken = authResult.accessToken
})
```

O MSAL para iOS e macOS suporta vários modificadores para obter um token interativa mente ou silenciosamente:
* [Parâmetros comuns para obter um token](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALTokenParameters.html#/Configuration%20parameters)
* [Parâmetros para obter um token interativo](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALInteractiveTokenParameters.html#/Configuring%20MSALInteractiveTokenParameters)
* [Parâmetros para obter um token silencioso](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALSilentTokenParameters.html)

#### <a name="xamarin"></a>Xamarin

O exemplo a seguir mostra o código mínimo para obter um token interativamente. O exemplo usa o Microsoft Graph para ler o perfil do usuário.

```csharp
string[] scopes = new string[] {"user.read"};
var app = PublicClientApplicationBuilder.Create(clientId).Build();
var accounts = await app.GetAccountsAsync();
AuthenticationResult result;
try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
             .ExecuteAsync();
}
catch(MsalUiRequiredException)
{
 result = await app.AcquireTokenInteractive(scopes)
             .ExecuteAsync();
}
```

#### <a name="mandatory-parameters-in-msalnet"></a>Parâmetros obrigatórios em MSAL.NET

`AcquireTokenInteractive`tem apenas um parâmetro `scopes`obrigatório: . O `scopes` parâmetro enumera as strings que definem os escopos para os quais um token é necessário. Se o token for para o Microsoft Graph, você poderá encontrar os escopos necessários na referência da API de cada API do Microsoft Graph. Na referência, vá para a seção "Permissões". 

Por exemplo, para [listar os contatos do usuário,](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts)use o escopo "Usuário.Read", "Contatos.Leia". Para obter mais informações, consulte [a referência de permissões do Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/permissions_reference).

No Android, você pode especificar a atividade `PublicClientApplicationBuilder`dos pais ao criar o aplicativo usando . Se você não especificar a atividade pai nesse momento, `.WithParentActivityOrWindow` mais tarde você poderá especificá-la usando como na seção a seguir. Se você especificar a atividade dos pais, o token volta para essa atividade dos pais após a interação. Se você não especificar, `.ExecuteAsync()` então a chamada abre uma exceção.

#### <a name="specific-optional-parameters-in-msalnet"></a>Parâmetros opcionais específicos em MSAL.NET

As seções a seguir explicam os parâmetros opcionais em MSAL.NET. 

##### <a name="withprompt"></a>WithPrompt

O `WithPrompt()` parâmetro controla a interatividade com o usuário especificando um prompt.

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

A classe define as seguintes constantes:

- `SelectAccount`força o serviço de token de segurança (STS) a apresentar a caixa de diálogo de seleção de conta. A caixa de diálogo contém as contas para as quais o usuário tem uma sessão. Você pode usar essa opção quando quiser deixar o usuário escolher entre diferentes identidades. Essa opção faz com `prompt=select_account` que o MSAL envie para o provedor de identidade. 
    
    A `SelectAccount` constante é o padrão, e ele efetivamente fornece a melhor experiência possível com base nas informações disponíveis. As informações disponíveis podem incluir conta, presença de uma sessão para o usuário, e assim por diante. Não mude esse padrão a menos que você tenha uma boa razão para fazê-lo.
- `Consent`permite que você solicitar o consentimento do usuário, mesmo que o consentimento tenha sido concedido antes. Neste caso, a `prompt=consent` MSAL envia ao provedor de identidade. 

    Você pode querer `Consent` usar a constante em aplicativos focados em segurança, onde a governança da organização exige que os usuários vejam a caixa de diálogo de consentimento cada vez que usam o aplicativo.
- `ForceLogin`permite que o serviço inscisse o usuário para obter credenciais, mesmo que o prompt não seja necessário. 

    Essa opção pode ser útil se a aquisição do token falhar e você quiser deixar o usuário entrar novamente. Neste caso, a `prompt=login` MSAL envia ao provedor de identidade. Você pode querer usar essa opção em aplicativos focados em segurança, onde a governança da organização exige que o usuário faça login cada vez que acessar partes específicas do aplicativo.
- `Never`é apenas para .NET 4.5 e Windows Runtime (WinRT). Essa constante não solicitará ao usuário, mas tentará usar o cookie armazenado na visualização web oculta incorporada. Para obter mais informações, consulte [Usando navegadores da Web com MSAL.NET](https://docs.microsoft.com/azure/active-directory/develop/msal-net-web-browsers).

    Se essa opção `AcquireTokenInteractive` falhar, então lança uma exceção para notificá-lo de que uma interação com a UI é necessária. Então você precisa `Prompt` usar outro parâmetro.
- `NoPrompt`não envia um aviso para o provedor de identidade. 

    Essa opção é útil apenas para políticas de perfil de edição no Azure Active Directory B2C. Para obter mais informações, consulte [as especificidades b2C](https://aka.ms/msal-net-b2c-specificities).

##### <a name="withextrascopetoconsent"></a>com extraescopotoconsent

Use `WithExtraScopeToConsent` o modificador em um cenário avançado onde você deseja que o usuário forneça consentimento antecipado a vários recursos. Você pode usar este modificador quando não quiser usar o consentimento incremental, que normalmente é usado com MSAL.NET ou a plataforma de identidade 2.0 da Microsoft. Para obter mais informações, [consulte Ter o consentimento do usuário antecipadamente para vários recursos](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources).

Este é um exemplo de código: 

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

##### <a name="other-optional-parameters"></a>Outros parâmetros opcionais

Para saber mais sobre `AcquireTokenInteractive`os outros parâmetros opcionais para , consulte a [documentação de referência para AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods).

### <a name="acquire-tokens-via-the-protocol"></a>Adquira tokens através do protocolo

Não recomendamos usar diretamente o protocolo para obter tokens. Se você fizer isso, então o aplicativo não suportará alguns cenários que envolvem logon único (SSO), gerenciamento de dispositivos e acesso condicional.

Quando você usar o protocolo para obter tokens para aplicativos móveis, faça duas solicitações: 

* Pegue um código de autorização.
* Troque o código por um token.

#### <a name="get-an-authorization-code"></a>Obter um código de autorização

```Text
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="get-access-and-refresh-the-token"></a>Obtenha acesso e atualize o token

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=<CLIENT_ID>
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=<ENCODED_REDIRECT_URI>
&grant_type=authorization_code
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Chamar uma API Web](scenario-mobile-call-api.md)
