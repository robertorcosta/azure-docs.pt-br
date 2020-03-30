---
title: Adquira um token para chamar uma API web (desktop app) | Azure
titleSuffix: Microsoft identity platform
description: Saiba como criar um aplicativo de desktop que chama APIs da Web para adquirir um token para o aplicativo
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 636c7c654b98ced5f93c3ace0e4a99bfc9bf7def
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262601"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token"></a>Aplicativo de desktop que chama APIs da Web: Adquira um token

Depois de construir uma instância do aplicativo cliente público, você o usará para adquirir um token que você usará para chamar uma API web.

## <a name="recommended-pattern"></a>Padrão recomendado

A API web é `scopes`definida por sua . Qualquer que seja a experiência que você oferece em sua aplicação, o padrão a ser usado é:

- Tenta sistematicamente obter um token do cache `AcquireTokenSilent`de token chamando .
- Se esta chamada falhar, use o `AcquireToken` fluxo que deseja `AcquireTokenXX`usar, que é representado aqui por .

# <a name="net"></a>[.NET](#tab/dotnet)

### <a name="in-msalnet"></a>Em MSAL.NET

```csharp
AuthenticationResult result;
var accounts = await app.GetAccountsAsync();
IAccount account = ChooseAccount(accounts); // for instance accounts.FirstOrDefault
                                            // if the app manages is at most one account  
try
{
 result = await app.AcquireTokenSilent(scopes, account)
                   .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
  result = await app.AcquireTokenXX(scopes, account)
                    .WithOptionalParameterXXX(parameter)
                    .ExecuteAsync();
}
```

# <a name="java"></a>[Java](#tab/java)

```java

Set<IAccount> accountsInCache = pca.getAccounts().join();
// Take first account in the cache. In a production application, you would filter
// accountsInCache to get the right account for the user authenticating.
IAccount account = accountsInCache.iterator().next();

IAuthenticationResult result;
try {
    SilentParameters silentParameters =
            SilentParameters
                    .builder(SCOPE, account)
                    .build();

    // try to acquire token silently. This call will fail since the token cache
    // does not have any data for the user you are trying to acquire a token for
    result = pca.acquireTokenSilently(silentParameters).join();
} catch (Exception ex) {
    if (ex.getCause() instanceof MsalException) {

        InteractiveRequestParameters parameters = InteractiveRequestParameters
                .builder(new URI("http://localhost"))
                .scopes(SCOPE)
                .build();

        // Try to acquire a token interactively with system browser. If successful, you should see
        // the token and account information printed out to console
        result = pca.acquireToken(parameters).join();
    } else {
        // Handle other exceptions accordingly
        throw ex;
    }
}
return result;

```

# <a name="python"></a>[Python](#tab/python)

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_xxx(scopes=config["scope"])
```

# <a name="macos"></a>[Macos](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>Em MSAL para iOS e macOS

Objective-C:

```objc
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];

MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {

    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
    }
}];
```
Swift:

```swift
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
}
```
---

Aqui estão as várias maneiras de adquirir tokens em um aplicativo de desktop.

## <a name="acquire-a-token-interactively"></a>Adquira um token interativamente

O exemplo a seguir mostra código mínimo para obter um token interativamente para ler o perfil do usuário com o Microsoft Graph.

# <a name="net"></a>[.NET](#tab/dotnet)
### <a name="in-msalnet"></a>Em MSAL.NET

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

### <a name="mandatory-parameters"></a>Parâmetros obrigatórios

`AcquireTokenInteractive`tem apenas um parâmetro ``scopes``obrigatório, que contém uma enumeração de strings que definem os escopos para os quais um token é necessário. Se o token for do Microsoft Graph, os escopos necessários podem ser encontrados na referência de API de cada API do Microsoft Graph na seção denominada "Permissões". Por exemplo, para [listar os contatos do usuário,](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts)o escopo "Usuário.Read", "Contacts.Read" deve ser usado. Para obter mais informações, consulte [a referência de permissões do Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/permissions_reference).

No Android, você também precisa especificar `.WithParentActivityOrWindow`a atividade dos pais usando , como mostrado, para que o token volte a essa atividade pai após a interação. Se você não especificar, uma exceção `.ExecuteAsync()`é lançada ao ligar .

### <a name="specific-optional-parameters-in-msalnet"></a>Parâmetros opcionais específicos em MSAL.NET

#### <a name="withparentactivityorwindow"></a>janela de atividade de compais

A ui é importante porque é interativa. `AcquireTokenInteractive`tem um parâmetro opcional específico que pode especificar, para plataformas que o suportam, a ui pai. Quando usado em um `.WithParentActivityOrWindow` aplicativo de desktop, tem um tipo diferente, que depende da plataforma.

```csharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .Net Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

Comentários:

- No .NET Standard, `object` `Activity` o esperado `UIViewController` é no `NSWindow` Android, `IWin32Window` no `IntPr` iOS, no MAC e ou no Windows.
- No Windows, você `AcquireTokenInteractive` deve ligar do segmento de Interface do Usuário para que o navegador incorporado obtenha o contexto de sincronização de interface do usuário apropriado. Não ligar do segmento de ui pode fazer com que as mensagens não bombeiem adequadamente e impasse cenários com a ui. Uma maneira de chamar as Bibliotecas de Autenticação microsoft (MSALs) do segmento de IA `Dispatcher` se você não estiver no segmento de IA já é usar o no WPF.
- Se você estiver usando o WPF, para obter uma janela `WindowInteropHelper.Handle` de um controle WPF, você pode usar a classe. Em seguida, a chamada é`this`de um controle WPF ( ):

  ```csharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()`é usado para controlar a interatividade com o usuário especificando um prompt.

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

A classe define as seguintes constantes:

- ``SelectAccount``força o STS a apresentar a caixa de diálogo de seleção de conta que contém contas para as quais o usuário tem uma sessão. Essa opção é útil quando os desenvolvedores de aplicativos querem permitir que os usuários escolham entre diferentes identidades. Essa opção faz com ``prompt=select_account`` que o MSAL envie para o provedor de identidade. Essa é a opção padrão. Ele faz um bom trabalho de fornecer a melhor experiência possível com base nas informações disponíveis, como conta e presença de uma sessão para o usuário. Não mude a menos que tenha uma boa razão para fazê-lo.
- ``Consent``permite que o desenvolvedor do aplicativo force o usuário a ser solicitado para o consentimento, mesmo que o consentimento tenha sido concedido antes. Neste caso, a `prompt=consent` MSAL envia ao provedor de identidade. Essa opção pode ser usada em alguns aplicativos focados em segurança, onde a governança da organização exige que o usuário seja apresentado com a caixa de diálogo de consentimento cada vez que o aplicativo é usado.
- ``ForceLogin``permite que o desenvolvedor do aplicativo tenha o usuário solicitado para credenciais pelo serviço, mesmo que esse prompt de usuário não seja necessário. Essa opção pode ser útil para permitir que o usuário faça login novamente se a aquisição de um token falhar. Neste caso, a `prompt=login` MSAL envia ao provedor de identidade. Às vezes, ele é usado em aplicativos focados em segurança, onde a governança da organização exige que o usuário refaça os sinais em cada vez que acessa partes específicas de um aplicativo.
- ``Never``(apenas para .NET 4.5 e WinRT) não solicitará ao usuário, mas tentará usar o cookie armazenado na visualização web oculta incorporada. Para obter mais informações, consulte visualizações na web em MSAL.NET. Usar essa opção pode falhar. Nesse caso, `AcquireTokenInteractive` abre uma exceção para notificar que uma interação com a UI é necessária. Você precisa usar outro `Prompt` parâmetro.
- ``NoPrompt``não enviará nenhum aviso ao provedor de identidade. Essa opção é útil apenas para políticas de perfil de edição do Azure Active Directory (Azure AD) B2C. Para obter mais informações, consulte [as especificidades do Azure AD B2C](https://aka.ms/msal-net-b2c-specificities).

#### <a name="withextrascopetoconsent"></a>com extraescopotoconsent

Este modificador é usado em um cenário avançado onde você deseja que o usuário pré-consentimento para vários recursos antecipadamente, e você não quer usar o consentimento incremental, que normalmente é usado com MSAL.NET/the plataforma de identidade Microsoft. Para obter mais informações, [consulte Ter o consentimento do usuário antecipadamente para vários recursos](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources).

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>ComCustomWebUi

Uma interface web é um mecanismo para invocar um navegador. Este mecanismo pode ser um controle dedicado do WebBrowser de Interface do Usuário ou uma maneira de delegar a abertura do navegador.
A MSAL fornece implementações de interface do usuário para a maioria das plataformas, mas há casos em que você mesmo pode querer hospedar o navegador:

- Plataformas que não são explicitamente cobertas pela MSAL, por exemplo, Blazor, Unity e Mono em desktops.
- Você deseja testar sua aplicação e usar um navegador automatizado que pode ser usado com selenium.
- O navegador e o aplicativo que executam o MSAL estão em processos separados.

##### <a name="at-a-glance"></a>Visão rápida

Para isso, você dá `start Url`ao MSAL , que precisa ser exibido em um navegador de escolha para que o usuário final possa inserir itens como seu nome de usuário.
Após o término da autenticação, seu aplicativo `end Url`precisa passar de volta para o MSAL , que contém um código fornecido pelo Azure AD.
O anfitrião `end Url` é `redirectUri`sempre. Para `end Url`interceptar, faça uma das seguintes coisas:

- Monitore os redirecionamentos do navegador até `redirect Url` ser atingido.
- Faça com que o navegador seja redirecionado para uma URL, que você monitora.

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi é um ponto de extensibilidade

`WithCustomWebUi`é um ponto de extensibilidade que você pode usar para fornecer sua própria interface do usuário em aplicativos de clientes públicos. Você também pode deixar o usuário passar pelo ponto final /Autorizar o provedor de identidade e deixá-lo entrar e consentir. MSAL.NET pode então resgatar o código de autenticação e obter um token. Por exemplo, ele é usado no Visual Studio para ter aplicações de elétrons (por exemplo, Visual Studio Feedback) fornecer a interação web, mas deixá-lo para MSAL.NET fazer a maior parte do trabalho. Você também pode usá-lo se quiser fornecer automação de iu. Em aplicativos públicos de clientes, MSAL.NET usa o padrão De Chave de Prova para Troca de Código (PKCE) para garantir que a segurança seja respeitada. Só MSAL.NET pode resgatar o código. Para obter mais informações, consulte [RFC 7636 - Proof Key for Code Exchange por Clientes Públicos OAuth](https://tools.ietf.org/html/rfc7636).

  ```csharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="use-withcustomwebui"></a>Use WithCustomWebUi

Para `.WithCustomWebUI`usar, siga estes passos.

  1. Implemente a interface `ICustomWebUi`. Para obter mais informações, consulte [este site](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70). Implementar `AcquireAuthorizationCodeAsync`um método e aceitar o URL de código de autorização computado por MSAL.NET. Em seguida, deixe o usuário passar pela interação com o provedor de identidade e retorne a URL pela qual o provedor de identidade teria chamado sua implementação de volta junto com o código de autorização. Se você tiver problemas, sua `MsalExtensionException` implementação deve abrir uma exceção para cooperar bem com o MSAL.
  2. Em `AcquireTokenInteractive` sua chamada, `.WithCustomUI()` use o modificador que passa na instância de sua ui web personalizada.

     ```csharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation-seleniumwebui"></a>Exemplos de implementação do ICustomWebUi na automação de testes: SeleniumWebUI

A equipe MSAL.NET reescreveu os testes de interface do usuário para usar este mecanismo de extensibilidade. Se você estiver interessado, olhe para a classe [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) no código-fonte MSAL.NET.

##### <a name="provide-a-great-experience-with-systemwebviewoptions"></a>Forneça uma ótima experiência com o SystemWebViewOptions

A partir MSAL.NET [`SystemWebViewOptions`](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.systemwebviewoptions?view=azure-dotnet)4.1, você pode especificar:

- O URI para`BrowserRedirectError`ir ( ) ou`HtmlMessageError`o fragmento HTML para exibir ( ) em caso de erros de login ou consentimento no navegador web do sistema.
- O URI para`BrowserRedirectSuccess`ir ( ) ou`HtmlMessageSuccess`o fragmento HTML para exibir ( ) em caso de login ou consentimento bem sucedido.
- A ação para executar para iniciar o navegador do sistema. Você pode fornecer sua própria `OpenBrowserAsync` implementação definindo o delegado. A classe também fornece uma implementação `OpenWithEdgeBrowserAsync` `OpenWithChromeEdgeBrowserAsync` padrão para dois navegadores: e para o Microsoft Edge e [o Microsoft Edge no Chromium,](https://www.windowscentral.com/faq-edge-chromium)respectivamente.

Para usar esta estrutura, escreva algo como o seguinte exemplo:

```csharp
IPublicClientApplication app;
...

options = new SystemWebViewOptions
{
 HtmlMessageError = "<b>Sign-in failed. You can close this tab ...</b>",
 BrowserRedirectSuccess = "https://contoso.com/help-for-my-awesome-commandline-tool.html"
};

var result = app.AcquireTokenInteractive(scopes)
                .WithEmbeddedWebView(false)       // The default in .NET Core
                .WithSystemWebViewOptions(options)
                .Build();
```

#### <a name="other-optional-parameters"></a>Outros parâmetros opcionais

Para saber mais sobre todos `AcquireTokenInteractive`os outros parâmetros opcionais para , consulte [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods).

# <a name="java"></a>[Java](#tab/java)

```java
private static IAuthenticationResult acquireTokenInteractive() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            InteractiveRequestParameters parameters = InteractiveRequestParameters
                    .builder(new URI("http://localhost"))
                    .scopes(SCOPE)
                    .build();

            // Try to acquire a token interactively with system browser. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

O MSAL Python não fornece um método de token de adquira interativo diretamente. Em vez disso, requer que o aplicativo envie uma solicitação de autorização em sua implementação do fluxo de interação do usuário para obter um código de autorização. Este código pode então `acquire_token_by_authorization_code` ser passado para o método para obter o token.

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_authorization_code(
         request.args['code'],
         scopes=config["scope"])    

```

# <a name="macos"></a>[Macos](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>Em MSAL para iOS e macOS

Objective-C:

```objc
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:[MSALWebviewParameters new]];
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

Swift:

```swift
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: MSALWebviewParameters())
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in

    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }

    // Get access token from result
    let accessToken = authResult.accessToken
})
```
---

## <a name="integrated-windows-authentication"></a>Autenticação Integrada do Windows

Para fazer login em um usuário de domínio em um domínio ou a máquina aderida ao Azure AD, use a IWA (Integrated Windows Authentication).

### <a name="constraints"></a>Restrições

- A autenticação integrada do Windows é utilizável apenas para usuários *federados,* ou seja, usuários criados no Active Directory e apoiados pelo Azure AD. Os usuários criados diretamente no Azure AD sem o backup do Active Directory, conhecidos como usuários *gerenciados,* não podem usar esse fluxo de autenticação. Essa limitação não afeta o nome de usuário e o fluxo de senha.
- O IWA é para aplicativos escritos para plataformas .NET Framework, .NET Core e Universal Windows Platform (UWP).
- O IWA não contorna a autenticação multifatorial (MFA). Se o MFA estiver configurado, o IWA pode falhar se um desafio mfa for necessário, porque o MFA requer interação do usuário.
  > [!NOTE]
  > Este é complicado. O IWA não é interativo, mas o MFA requer interatividade do usuário. Você não controla quando o provedor de identidade solicita que o MFA seja executado, o inquilino faz. A partir de nossas observações, o MFA é necessário quando você faz login de um país diferente, quando não está conectado via VPN a uma rede corporativa, e às vezes até mesmo quando conectado via VPN. Não espere um conjunto determinista de regras. O Azure AD usa AI para aprender continuamente se o MFA é necessário. Volte a um prompt de usuário como autenticação interativa ou fluxo de código do dispositivo se o IWA falhar.

- A autoridade `PublicClientApplicationBuilder` aprovada precisa ser:
  - Inquilino do formulário `https://login.microsoftonline.com/{tenant}/`, `tenant` onde está o GUID que representa o ID do inquilino ou um domínio associado ao inquilino.
  - Para qualquer trabalho e `https://login.microsoftonline.com/organizations/`contas escolares: .
  - As contas pessoais da Microsoft não são suportadas. Você não pode usar inquilinos /comuns ou //consumidores.

- Porque a autenticação integrada do Windows é um fluxo silencioso:
  - O usuário do seu aplicativo deve ter consentido anteriormente em usar o aplicativo.
  - Ou, o inquilino deve ter consentido previamente com todos os usuários do inquilino para usar o aplicativo.
  - Em outras palavras:
    - Você, como desenvolvedor, selecionou o botão **Grant** no portal Azure para você mesmo.
    - Ou, um admin inquilino selecionou o **consentimento de grant/revoke admin para {domínio de inquilino}** na guia **de permissões aPI** do registro para o aplicativo. Para obter mais informações, consulte [Adicionar permissões para acessar APIs da Web](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis#add-permissions-to-access-web-apis).
    - Ou, você forneceu uma maneira para os usuários consentirem com o aplicativo. Para obter mais informações, consulte [Solicitar o consentimento individual do usuário](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-individual-user-consent).
    - Ou, você forneceu uma maneira para o inquilino do dinamismo consentir com o pedido. Para obter mais informações, consulte [o consentimento do admin](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-consent-for-an-entire-tenant).

- Esse fluxo está habilitado para aplicativos de desktop .NET, .NET Core e UWP.

Para obter mais informações sobre consentimento, consulte as permissões e o consentimento da [plataforma de identidade da Microsoft.](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)

### <a name="learn-how-to-use-it"></a>Saiba como usá-lo

# <a name="net"></a>[.NET](#tab/dotnet)

Em MSAL.NET, você precisa usar:

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

Você normalmente precisa de`scopes`apenas um parâmetro ( . Dependendo da maneira como o administrador do Windows configurou as políticas, os aplicativos na sua máquina Windows podem não ser permitidos procurar o usuário inscrito. Nesse caso, use um `.WithUsername()`segundo método e passe no nome de usuário do usuário inscrito `joe@contoso.com`como um formato UPN, por exemplo, . No .NET Core, apenas a sobrecarga que leva o nome de usuário está disponível porque a plataforma .NET Core não pode pedir o nome de usuário para o SO.

A amostra a seguir apresenta o caso mais atual, com explicações do tipo de exceções que você pode obter e suas atenuações.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
      .ExecuteAsync();
 }
 else
 {
  try
  {
   result = await app.AcquireTokenByIntegratedWindowsAuth(scopes)
      .ExecuteAsync(CancellationToken.None);
  }
  catch (MsalUiRequiredException ex)
  {
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'.Send an interactive authorization request for this user and resource.

   // you need to get user consent first. This can be done, if you are not using .NET Core (which does not have any Web UI)
   // by doing (once only) an AcquireToken interactive.

   // If you are using .NET core or don't want to do an AcquireTokenInteractive, you might want to suggest the user to navigate
   // to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read

   // AADSTS50079: The user is required to use multi-factor authentication.
   // There is no mitigation - if MFA is configured for your tenant and AAD decides to enforce it,
   // you need to fallback to an interactive flows such as AcquireTokenInteractive or AcquireTokenByDeviceCode
   }
   catch (MsalServiceException ex)
   {
    // Kind of errors you could have (in ex.Message)

    // MsalServiceException: AADSTS90010: The grant type is not supported over the /common or /consumers endpoints. Please use the /organizations or tenant-specific endpoint.
    // you used common.
    // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted or otherwise organizations

    // MsalServiceException: AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
    // Explanation: this can happen if your application was not registered as a public client application in Azure AD
    // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   }
   catch (MsalClientException ex)
   {
      // Error Code: unknown_user Message: Could not identify logged in user
      // Explanation: the library was unable to query the current Windows logged-in user or this user is not AD or AAD
      // joined (work-place joined users are not supported).

      // Mitigation 1: on UWP, check that the application has the following capabilities: Enterprise Authentication,
      // Private Networks (Client and Server), User Account Information

      // Mitigation 2: Implement your own logic to fetch the username (e.g. john@contoso.com) and use the
      // AcquireTokenByIntegratedWindowsAuth form that takes in the username

      // Error Code: integrated_windows_auth_not_supported_managed_user
      // Explanation: This method relies on an a protocol exposed by Active Directory (AD). If a user was created in Azure
      // Active Directory without AD backing ("managed" user), this method will fail. Users created in AD and backed by
      // AAD ("federated" users) can benefit from this non-interactive method of authentication.
      // Mitigation: Use interactive authentication
   }
 }


 Console.WriteLine(result.Account.Username);
}
```

Para obter a lista de possíveis modificadores no AcquireTokenByIntegratedWindowsAuthentication, consulte [AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder?view=azure-dotnet-preview#methods).

# <a name="java"></a>[Java](#tab/java)

Este extrato é das [amostras de desenvolvimento MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```Java
private static IAuthenticationResult acquireTokenIwa() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            IntegratedWindowsAuthenticationParameters parameters =
                    IntegratedWindowsAuthenticationParameters
                            .builder(SCOPE, USER_NAME)
                            .build();

            // Try to acquire a IWA. You will need to generate a Kerberos ticket.
            // If successful, you should see the token and account information printed out to
            // console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

Esse fluxo ainda não é suportado no MSAL Python.

# <a name="macos"></a>[Macos](#tab/macOS)

Esse fluxo não se aplica ao MacOS.

---

## <a name="username-and-password"></a>Nome de usuário e senha

Você também pode adquirir um token fornecendo o nome de usuário e senha. Esse fluxo é limitado e não recomendado, mas ainda há casos de uso em que é necessário.

### <a name="this-flow-isnt-recommended"></a>Este fluxo não é recomendado

Esse fluxo não é *recomendado* porque ter seu aplicativo pedindo a um usuário sua senha não é seguro. Para obter mais informações, [veja qual é a solução para o crescente problema das senhas?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). O fluxo preferido para adquirir um token silenciosamente em máquinas unidas de domínio do Windows é [a Autenticação Integrada do Windows](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication). Você também pode usar [o fluxo de código do dispositivo](https://aka.ms/msal-net-device-code-flow).

> [!NOTE]
> O uso de um nome de usuário e senha é útil em alguns casos, como cenários de DevOps. Mas se você quiser usar um nome de usuário e senha em cenários interativos onde você fornece sua própria ui, pense em como se afastar dela. Usando um nome de usuário e senha, você está abrindo mão de uma série de coisas:
>
> - Princípios fundamentais da identidade moderna. Uma senha pode ser phished e reproduzida porque um segredo compartilhado pode ser interceptado. É incompatível com sem senha.
> - Os usuários que precisam fazer MFA não podem fazer login porque não há interação.
> - Os usuários não podem fazer o único login (SSO).

### <a name="constraints"></a>Restrições

As seguintes restrições também se aplicam:

- O fluxo de nome de usuário e senha não é compatível com acesso condicional e autenticação multifatorial. Como conseqüência, se o seu aplicativo for executado em um inquilino Azure AD onde o administrador do inquilino requer autenticação multifatorial, você não poderá usar esse fluxo. Muitas organizações fazem isso.
- Funciona apenas para contas de trabalho e escola (não MSA).
- O fluxo está disponível na área de trabalho .NET e no .NET Core, mas não no UWP.

### <a name="b2c-specifics"></a>Especificidades b2C

Para obter mais informações, consulte [OCPC (Resource Owner Password Credentials, credenciais de senha de proprietário de recursos) com B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c).

### <a name="use-it"></a>Use-o

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`contém o `AcquireTokenByUsernamePassword`método .

A amostra a seguir apresenta um caso simplificado.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
       .WithAuthority(authority)
       .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAsync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password
    securePassword.AppendChar(c);  // keystroke by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                     securePassword)
                      .ExecuteAsync();
  }
  catch(MsalException)
  {
   // See details below
  }
 }
 Console.WriteLine(result.Account.Username);
}
```

A amostra a seguir apresenta o caso mais atual, com explicações do tipo de exceções que você pode obter e suas atenuações.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
                                   .WithAuthority(authority)
                                   .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password keystroke
    securePassword.AppendChar(c);  // by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                    securePassword)
                    .ExecuteAsync();
  }
  catch (MsalUiRequiredException ex) when (ex.Message.Contains("AADSTS65001"))
  {
   // Here are the kind of error messages you could have, and possible mitigations

   // ------------------------------------------------------------------------
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'. Send an interactive authorization request for this user and resource.

   // Mitigation: you need to get user consent first. This can be done either statically (through the portal),
   /// or dynamically (but this requires an interaction with Azure AD, which is not possible with
   // the username/password flow)
   // Statically: in the portal by doing the following in the "API permissions" tab of the application registration:
   // 1. Click "Add a permission" and add all the delegated permissions corresponding to the scopes you want (for instance
   // User.Read and User.ReadBasic.All)
   // 2. Click "Grant/revoke admin consent for <tenant>") and click "yes".
   // Dynamically, if you are not using .NET Core (which does not have any Web UI) by
   // calling (once only) AcquireTokenInteractive.
   // remember that Username/password is for public client applications that is desktop/mobile applications.
   // If you are using .NET core or don't want to call AcquireTokenInteractive, you might want to:
   // - use device code flow (See https://aka.ms/msal-net-device-code-flow)
   // - or suggest the user to navigate to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ErrorCode: invalid_grant
   // SubError: basic_action
   // MsalUiRequiredException: AADSTS50079: The user is required to use multi-factor authentication.
   // The tenant admin for your organization has chosen to oblige users to perform multi-factor authentication.
   // Mitigation: none for this flow
   // Your application cannot use the Username/Password grant.
   // Like in the previous case, you might want to use an interactive flow (AcquireTokenInteractive()),
   // or Device Code Flow instead.
   // Note this is one of the reason why using username/password is not recommended;
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // Message = "AADSTS70002: Error validating credentials.
   // AADSTS50126: Invalid username or password
   // In the case of a managed user (user from an Azure AD tenant opposed to a
   // federated user, which would be owned
   // in another IdP through ADFS), the user has entered the wrong password
   // Mitigation: ask the user to re-enter the password
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // MsalServiceException: ADSTS50034: To sign into this application the account must be added to
   // the {domainName} directory.
   // or The user account does not exist in the {domainName} directory. To sign into this application,
   // the account must be added to the directory.
   // The user was not found in the directory
   // Explanation: wrong username
   // Mitigation: ask the user to re-enter the username.
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_request")
  {
   // ------------------------------------------------------------------------
   // AADSTS90010: The grant type is not supported over the /common or /consumers endpoints.
   // Please use the /organizations or tenant-specific endpoint.
   // you used common.
   // Mitigation: as explained in the message from Azure AD, the authority you use in the application needs
   // to be tenanted or otherwise "organizations". change the
   // "Tenant": property in the appsettings.json to be a GUID (tenant Id), or domain name (contoso.com)
   // if such a domain is registered with your tenant
   // or "organizations", if you want this application to sign-in users in any Work and School accounts.
   // ------------------------------------------------------------------------

  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "unauthorized_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS700016: Application with identifier '{clientId}' was not found in the directory '{domain}'.
   // This can happen if the application has not been installed by the administrator of the tenant or consented
   // to by any user in the tenant.
   // You may have sent your authentication request to the wrong tenant
   // Cause: The clientId in the appsettings.json might be wrong
   // Mitigation: check the clientId and the app registration
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
   // Explanation: this can happen if your application was not registered as a public client application in Azure AD
   // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException)
  {
   throw;
  }

  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user_type")
  {
   // Message = "Unsupported User Type 'Unknown'. Please see https://aka.ms/msal-net-up"
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "user_realm_discovery_failed")
  {
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user. That's for instance the case
   // if you use a phone number
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user")
  {
   // the username was probably empty
   // ex.Message = "Could not identify the user logged into the OS. See https://aka.ms/msal-net-iwa for details."
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "parsing_wstrust_response_failed")
  {
   // ------------------------------------------------------------------------
   // In the case of a Federated user (that is owned by a federated IdP, as opposed to a managed user owned in an Azure AD tenant)
   // ID3242: The security token could not be authenticated or authorized.
   // The user does not exist or has entered the wrong password
   // ------------------------------------------------------------------------
  }
 }

 Console.WriteLine(result.Account.Username);
}
```

Para obter mais informações sobre todos os modificadores que podem ser `AcquireTokenByUsernamePassword`aplicados, consulte [AcquireTokenByUsernamePasswordParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder?view=azure-dotnet-preview#methods).

# <a name="java"></a>[Java](#tab/java)

O extrato a seguir é das [amostras de desenvolvimento MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```Java
private static IAuthenticationResult acquireTokenUsernamePassword() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();
        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            UserNamePasswordParameters parameters =
                    UserNamePasswordParameters
                            .builder(SCOPE, USER_NAME, USER_PASSWORD.toCharArray())
                            .build();
            // Try to acquire a token via username/password. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

Este extrato é das [amostras de desenvolvimento MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/).

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    # See this page for constraints of Username Password Flow.
    # https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Username-Password-Authentication
    result = app.acquire_token_by_username_password(
        config["username"], config["password"], scopes=config["scope"])
```

# <a name="macos"></a>[Macos](#tab/macOS)

Esse fluxo não é suportado no MSAL para macOS.

---

## <a name="command-line-tool-without-a-web-browser"></a>Ferramenta de linha de comando sem navegador web

### <a name="device-code-flow"></a>Fluxo de código do dispositivo

Se você está escrevendo uma ferramenta de linha de comando que não tem controles web, e você não pode ou não quer usar os fluxos anteriores, você precisa usar o fluxo de código do dispositivo.

A autenticação interativa com o Azure AD requer um navegador da Web. Para obter mais informações, consulte [O uso de navegadores da Web](https://aka.ms/msal-net-uses-web-browser). Para autenticar usuários em dispositivos ou sistemas operacionais que não fornecem um navegador da Web, o fluxo de código do dispositivo permite que o usuário use outro dispositivo, como um computador ou um telefone celular, para fazer login interativamente. Usando o fluxo de código do dispositivo, o aplicativo obtém tokens através de um processo de duas etapas projetado para esses dispositivos ou OSes. Exemplos desses aplicativos são aplicativos executados em iOT ou ferramentas de linha de comando (CLI). A idéia é que:

1. Sempre que a autenticação do usuário é necessária, o aplicativo fornece um código para o usuário. O usuário é solicitado a usar outro dispositivo, como um smartphone conectado à `https://microsoft.com/devicelogin`internet, para ir a uma URL, por exemplo, . Em seguida, o usuário é solicitado a inserir o código. Feito isso, a página da Web conduz o usuário através de uma experiência normal de autenticação, que inclui solicitações de consentimento e autenticação multifatorial, se necessário.

2. Após a autenticação bem-sucedida, o aplicativo de linha de comando recebe os tokens necessários através de um canal de trás e os usa para executar as chamadas de API da Web de que precisa.

### <a name="use-it"></a>Use-o

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`contém um `AcquireTokenWithDeviceCode`método chamado .

```csharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

Este método toma como parâmetros:

- Solicitar `scopes` um token de acesso para.
- Um retorno de `DeviceCodeResult`chamada que recebe o .

  ![Propriedades DeviceCodeResult](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

O seguinte código amostral apresenta o caso mais atual, com explicações do tipo de exceções que você pode obter e sua mitigação.

```csharp
private const string ClientId = "<client_guid>";
private const string Authority = "https://login.microsoftonline.com/contoso.com";
private readonly string[] Scopes = new string[] { "user.read" };

static async Task<AuthenticationResult> GetATokenForGraph()
{
    IPublicClientApplication pca = PublicClientApplicationBuilder
            .Create(ClientId)
            .WithAuthority(Authority)
            .WithDefaultRedirectUri()
            .Build();

    var accounts = await pca.GetAccountsAsync();

    // All AcquireToken* methods store the tokens in the cache, so check the cache first
    try
    {
        return await pca.AcquireTokenSilent(Scopes, accounts.FirstOrDefault())
            .ExecuteAsync();
    }
    catch (MsalUiRequiredException ex)
    {
        // No token found in the cache or AAD insists that a form interactive auth is required (e.g. the tenant admin turned on MFA)
        // If you want to provide a more complex user experience, check out ex.Classification

        return await AcquireByDeviceCodeAsync(pca);
    }         
}

private async Task<AuthenticationResult> AcquireByDeviceCodeAsync(IPublicClientApplication pca)
{
    try
    {
        var result = await pca.AcquireTokenWithDeviceCode(scopes,
            deviceCodeResult =>
            {
                    // This will print the message on the console which tells the user where to go sign-in using
                    // a separate browser and the code to enter once they sign in.
                    // The AcquireTokenWithDeviceCode() method will poll the server after firing this
                    // device code callback to look for the successful login of the user via that browser.
                    // This background polling (whose interval and timeout data is also provided as fields in the
                    // deviceCodeCallback class) will occur until:
                    // * The user has successfully logged in via browser and entered the proper code
                    // * The timeout specified by the server for the lifetime of this code (typically ~15 minutes) has been reached
                    // * The developing application calls the Cancel() method on a CancellationToken sent into the method.
                    //   If this occurs, an OperationCanceledException will be thrown (see catch below for more details).
                    Console.WriteLine(deviceCodeResult.Message);
                return Task.FromResult(0);
            }).ExecuteAsync();

        Console.WriteLine(result.Account.Username);
        return result;
    }
    // TODO: handle or throw all these exceptions depending on your app
    catch (MsalServiceException ex)
    {
        // Kind of errors you could have (in ex.Message)

        // AADSTS50059: No tenant-identifying information found in either the request or implied by any provided credentials.
        // Mitigation: as explained in the message from Azure AD, the authoriy needs to be tenanted. you have probably created
        // your public client application with the following authorities:
        // https://login.microsoftonline.com/common or https://login.microsoftonline.com/organizations

        // AADSTS90133: Device Code flow is not supported under /common or /consumers endpoint.
        // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted

        // AADSTS90002: Tenant <tenantId or domain you used in the authority> not found. This may happen if there are
        // no active subscriptions for the tenant. Check with your subscription administrator.
        // Mitigation: if you have an active subscription for the tenant this might be that you have a typo in the
        // tenantId (GUID) or tenant domain name.
    }
    catch (OperationCanceledException ex)
    {
        // If you use a CancellationToken, and call the Cancel() method on it, then this *may* be triggered
        // to indicate that the operation was cancelled.
        // See https://docs.microsoft.com/dotnet/standard/threading/cancellation-in-managed-threads
        // for more detailed information on how C# supports cancellation in managed threads.
    }
    catch (MsalClientException ex)
    {
        // Possible cause - verification code expired before contacting the server
        // This exception will occur if the user does not manage to sign-in before a time out (15 mins) and the
        // call to `AcquireTokenWithDeviceCode` is not cancelled in between
    }
}
```
# <a name="java"></a>[Java](#tab/java)

Este extrato é das [amostras de desenvolvimento MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```java
private static IAuthenticationResult acquireTokenDeviceCode() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            Consumer<DeviceCode> deviceCodeConsumer = (DeviceCode deviceCode) ->
                    System.out.println(deviceCode.message());

            DeviceCodeFlowParameters parameters =
                    DeviceCodeFlowParameters
                            .builder(SCOPE, deviceCodeConsumer)
                            .build();

            // Try to acquire a token via device code flow. If successful, you should see
            // the token and account information printed out to console, and the sample_cache.json
            // file should have been updated with the latest tokens.
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

Este extrato é das [amostras de desenvolvimento MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/).

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Note: If your device-flow app does not have any interactive ability, you can
#   completely skip the following cache part. But here we demonstrate it anyway.
# We now check the cache to see if we have some end users signed in before.
accounts = app.get_accounts()
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    print("Pick the account you want to use to proceed:")
    for a in accounts:
        print(a["username"])
    # Assuming the end user chose this one
    chosen = accounts[0]
    # Now let's try to find a token in cache for this account
    result = app.acquire_token_silent(config["scope"], account=chosen)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")

    flow = app.initiate_device_flow(scopes=config["scope"])
    if "user_code" not in flow:
        raise ValueError(
            "Fail to create device flow. Err: %s" % json.dumps(flow, indent=4))

    print(flow["message"])
    sys.stdout.flush()  # Some terminal needs this to ensure the message is shown

    # Ideally you should wait here, in order to save some unnecessary polling
    # input("Press Enter after signing in from another device to proceed, CTRL+C to abort.")

    result = app.acquire_token_by_device_flow(flow)  # By default it will block
        # You can follow this instruction to shorten the block time
        #    https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow
        # or you may even turn off the blocking behavior,
        # and then keep calling acquire_token_by_device_flow(flow) in your own customized loop
```

# <a name="macos"></a>[Macos](#tab/macOS)

Esse fluxo não se aplica ao MacOS.

---

## <a name="file-based-token-cache"></a>Cache de token baseado em arquivos

Na MSAL.NET, um cache de token na memória é fornecido por padrão.

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-apps-or-web-apis"></a>A serialização é personalizável em aplicativos de desktop do Windows e aplicativos web ou APIs web

No caso do .NET Framework e do .NET Core, se você não fizer nada extra, o cache de token na memória dura durante a duração do aplicativo. Para entender por que a serialização não é fornecida fora da caixa, lembre-se que os aplicativos MSAL .NET desktop ou .NET Core podem ser aplicativos de console ou Windows (que teriam acesso ao sistema de arquivos), *mas também* aplicativos web ou APIs web. Esses aplicativos da Web e APIs da Web podem usar alguns mecanismos específicos de cache, como bancos de dados, caches distribuídos e caches Redis. Para ter um aplicativo de cache de token persistente na área de trabalho .NET ou no .NET Core, você precisará personalizar a serialização.

Classes e interfaces envolvidas na serialização de cache de token são os seguintes tipos:

- ``ITokenCache``, que define eventos para subscrever solicitações de serialização de cache de token e métodos para serializar ou desserializar o cache em vários formatos (ADAL v3.0, MSAL 2.x e MSAL 3.x = ADAL v5.0).
- ``TokenCacheCallback`` é um retorno de chamada passado aos eventos para permitir que você controle a serialização. Eles serão chamados com argumentos do tipo ``TokenCacheNotificationArgs``.
- ``TokenCacheNotificationArgs``apenas fornece ``ClientId`` o aplicativo e uma referência ao usuário para o qual o token está disponível.

  ![Diagrama de serialização de cache de token](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> A MSAL.NET cria os caches de token para você e fornece o cache `IToken` quando você chama as propriedades `UserTokenCache` e `AppTokenCache` de um aplicativo. Você não deveria implementar a interface você mesmo. Ao implementar uma serialização do cache de token personalizada, sua responsabilidade é:
>
> - Reagir `BeforeAccess` e `AfterAccess` eventos, ou sua contraparte *Assíncrona.* O`BeforeAccess` delegado é responsável por desserializar o cache. O `AfterAccess` delegado é responsável por serializar o cache.
> - Entenda que parte desses eventos armazenam ou carregam bolhas, que são passadas através do argumento do evento para o armazenamento que você quiser.

As estratégias são diferentes dependendo se você estiver escrevendo uma serialização de cache de token para um aplicativo cliente público, como um desktop ou um aplicativo cliente confidencial, como um aplicativo web ou API web ou um aplicativo daemon.

Desde msal v2.x, você tem várias opções. Sua escolha depende se você deseja serializar o cache apenas para o formato MSAL.NET, que é um cache de formato unificado que é comum com o MSAL, mas também em todas as plataformas. Ou, você também pode querer suportar a serialização de cache de token [legado](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) do ADAL v3.

A personalização da serialização de cache de token para compartilhar o estado SSO entre ADAL.NET 3.x, ADAL.NET 5.x e MSAL.NET é explicada em parte da amostra [ativo-diretório-dotnet-v1-para-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2).

### <a name="simple-token-cache-serialization-msal-only"></a>Serialização do cache de token simples (somente MSAL)

O exemplo a seguir é uma implementação ingênua da serialização personalizada de um cache de token para aplicativos de desktop. Aqui, o cache do token do usuário está em um arquivo na mesma pasta que o aplicativo.

Depois de construir o aplicativo, você ``TokenCacheHelper.EnableSerialization()`` habilita a `UserTokenCache`serialização ligando e passando o aplicativo .

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

Esta classe auxiliar parece o seguinte trecho de código:

```csharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Path to the token cache
  /// </summary>
  public static readonly string CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin3";

  private static readonly object FileLock = new object();


  private static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeMsalV3(File.Exists(CacheFilePath)
            ? ProtectedData.Unprotect(File.ReadAllBytes(CacheFilePath),
                                      null,
                                      DataProtectionScope.CurrentUser)
            : null);
   }
  }

  private static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     // reflect changesgs in the persistent store
     File.WriteAllBytes(CacheFilePath,
                         ProtectedData.Protect(args.TokenCache.SerializeMsalV3(),
                                                 null,
                                                 DataProtectionScope.CurrentUser)
                         );
    }
   }
  }
 }
```

Uma visualização de um serializador de cache de cache de token de qualidade de produto para aplicativos clientes públicos para aplicativos de desktop em execução no Windows, Mac e Linux está disponível na biblioteca de código aberto [Microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) open-source. Você pode incluí-lo em seus aplicativos a partir do seguinte pacote NuGet: [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

> [!NOTE]
> Aviso: A biblioteca Microsoft.Identity.Client.Extensions.Msal é uma extensão sobre MSAL.NET. As aulas nessas bibliotecas podem entrar em MSAL.NET no futuro, como é ou com mudanças.

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>Serialização de cache de token duplo (cache unificado MSAL + ADAL v3)

Você pode querer implementar a serialização do cache de token com o formato de cache Unificado. Esse formato é comum a ADAL.NET 4.x e MSAL.NET 2.x, e com outros MSALs da mesma geração ou mais antigos, na mesma plataforma. Inspire-se no seguinte código:

```csharp
string appLocation = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location;
string cacheFolder = Path.GetFullPath(appLocation) + @"..\..\..\..");
string adalV3cacheFileName = Path.Combine(cacheFolder, "cacheAdalV3.bin");
string unifiedCacheFileName = Path.Combine(cacheFolder, "unifiedCache.bin");

IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
                                    .Build();
FilesBasedTokenCacheHelper.EnableSerialization(app.UserTokenCache,
                                               unifiedCacheFileName,
                                               adalV3cacheFileName);

```

Desta vez, a classe auxiliar parece o seguinte código:

```csharp
using System;
using System.IO;
using System.Security.Cryptography;
using Microsoft.Identity.Client;

namespace CommonCacheMsalV3
{
 /// <summary>
 /// Simple persistent cache implementation of the dual cache serialization (ADAL V3 legacy
 /// and unified cache format) for a desktop applications (from MSAL 2.x)
 /// </summary>
 static class FilesBasedTokenCacheHelper
 {
  /// <summary>
  /// Get the user token cache
  /// </summary>
  /// <param name="adalV3CacheFileName">File name where the cache is serialized with the
  /// ADAL V3 token cache format. Can
  /// be <c>null</c> if you don't want to implement the legacy ADAL V3 token cache
  /// serialization in your MSAL 2.x+ application</param>
  /// <param name="unifiedCacheFileName">File name where the cache is serialized
  /// with the Unified cache format, common to
  /// ADAL V4 and MSAL V2 and above, and also across ADAL/MSAL on the same platform.
  ///  Should not be <c>null</c></param>
  /// <returns></returns>
  public static void EnableSerialization(ITokenCache cache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   cache.SetBeforeAccess(BeforeAccessNotification);
   cache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// File path where the token cache is serialized with the unified cache format
  /// (ADAL.NET V4, MSAL.NET V3)
  /// </summary>
  public static string UnifiedCacheFileName { get; private set; }

  /// <summary>
  /// File path where the token cache is serialized with the legacy ADAL V3 format
  /// </summary>
  public static string AdalV3CacheFileName { get; private set; }

  private static readonly object FileLock = new object();

  public static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeAdalV3(ReadFromFileIfExists(AdalV3CacheFileName));
    try
    {
     args.TokenCache.DeserializeMsalV3(ReadFromFileIfExists(UnifiedCacheFileName));
    }
    catch(Exception ex)
    {
     // Compatibility with the MSAL v2 cache if you used one
     args.TokenCache.DeserializeMsalV2(ReadFromFileIfExists(UnifiedCacheFileName));
    }
   }
  }

  public static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     WriteToFileIfNotNull(UnifiedCacheFileName, args.TokenCache.SerializeMsalV3());
     if (!string.IsNullOrWhiteSpace(AdalV3CacheFileName))
     {
      WriteToFileIfNotNull(AdalV3CacheFileName, args.TokenCache.SerializeAdalV3());
     }
    }
   }
  }

  /// <summary>
  /// Read the content of a file if it exists
  /// </summary>
  /// <param name="path">File path</param>
  /// <returns>Content of the file (in bytes)</returns>
  private static byte[] ReadFromFileIfExists(string path)
  {
   byte[] protectedBytes = (!string.IsNullOrEmpty(path) && File.Exists(path))
       ? File.ReadAllBytes(path) : null;
   byte[] unprotectedBytes = encrypt ?
       ((protectedBytes != null) ? ProtectedData.Unprotect(protectedBytes, null, DataProtectionScope.CurrentUser) : null)
       : protectedBytes;
   return unprotectedBytes;
  }

  /// <summary>
  /// Writes a blob of bytes to a file. If the blob is <c>null</c>, deletes the file
  /// </summary>
  /// <param name="path">path to the file to write</param>
  /// <param name="blob">Blob of bytes to write</param>
  private static void WriteToFileIfNotNull(string path, byte[] blob)
  {
   if (blob != null)
   {
    byte[] protectedBytes = encrypt
      ? ProtectedData.Protect(blob, null, DataProtectionScope.CurrentUser)
      : blob;
    File.WriteAllBytes(path, protectedBytes);
   }
   else
   {
    File.Delete(path);
   }
  }

  // Change if you want to test with an un-encrypted blob (this is a json format)
  private static bool encrypt = true;
 }
}
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Chame uma API web a partir do aplicativo de desktop](scenario-desktop-call-api.md)
