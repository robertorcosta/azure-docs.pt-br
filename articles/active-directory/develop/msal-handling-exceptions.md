---
title: Erros e exceções (MSAL)
titleSuffix: Microsoft identity platform
description: Saiba como lidar com erros e exceções, acesso condicional e desafios de reclamações em aplicativos MSAL.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/22/2019
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: f78c64fc0ba25dc3310b24e873dbae266ea2f281
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050329"
---
# <a name="handle-msal-exceptions-and-errors"></a>Lidar com exceções e erros do MSAL

Este artigo fornece uma visão geral dos diferentes tipos de erros e recomendações para lidar com erros comuns de login.

## <a name="msal-error-handling-basics"></a>MSAL erro manipulação básica

Exceções na Microsoft Authentication Library (MSAL) destinam-se aos desenvolvedores de aplicativos para solucionar problemas - não para exibir aos usuários finais. Mensagens de exceção não são localizadas.

Ao processar exceções e erros, é possível usar o próprio tipo de exceção e o código de erro para distinguir entre exceções.  Para obter uma lista de códigos de erro, consulte [Códigos de erro de autenticação e autorização](reference-aadsts-error-codes.md).

Durante a experiência de login, você pode encontrar erros sobre consentimentos, Acesso Condicional (MFA, Gerenciamento de Dispositivos, Restrições baseadas em Localização), emissão e resgate de tokens e propriedades do usuário.

Veja a seção a seguir que corresponde ao idioma que você está usando para obter mais detalhes sobre o manuseio de erros para o seu aplicativo.

## <a name="net"></a>[.NET](#tab/dotnet)

Ao processar exceções .NET, você pode usar `ErrorCode` o próprio tipo de exceção e o membro para distinguir entre exceções. `ErrorCode`valores são constantes do tipo [MsalError](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet).

Você também pode dar uma olhada nos campos de [MsalClientException,](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet) [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)e [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet).

Se [o MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) for lançado, tente [códigos de erro de autenticação e autorização](reference-aadsts-error-codes.md) para ver se o código está listado lá.

### <a name="common-net-exceptions"></a>Exceções comuns .NET

Aqui estão as exceções comuns que podem ser lançadas e algumas possíveis atenuações:  

| Exceção | Código do erro | Atenuação|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001: O usuário ou administrador não consentiu em usar o aplicativo com ID '{appId}' chamado '{appName}'. Envie uma solicitação de autorização interativa para esse usuário e recurso.| É necessário primeiro obter o consentimento do usuário. Se você não estiver usando o .NET Core (que não tem nenhuma `AcquireTokeninteractive`UI da Web), ligue (apenas uma vez) . Se você estiver usando o núcleo .NET `AcquireTokenInteractive`ou não quiser fazer um, o `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read`usuário pode navegar até uma URL para dar consentimento: . para `AcquireTokenInteractive`chamar:`app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079: O usuário é obrigado a usar a autenticação multifatorial (MFA).| Não há atenuação. Se o MFA estiver configurado para o seu inquilino e o AAD (AAD) do Azure Active Directory decidir aplicá-lo, você precisará recuar para um fluxo interativo como `AcquireTokenInteractive` ou `AcquireTokenByDeviceCode`.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) |AADSTS90010: O tipo de subvenção não é suportado sobre os pontos finais */comum* ou */consumers.* Use o ponto de extremidade */organizations* ou específico do locatário. Você usou */common*.| Conforme explicado na mensagem do Azure AD, a autoridade deve ter um locatário ou, caso contrário, */organizations*.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002: O órgão de solicitação deve `client_secret or client_assertion`conter o seguinte parâmetro: .| Essa exceção pode ser lançada se seu aplicativo não foi registrado como um aplicativo de cliente público no Azure AD. No portal Azure, edite o manifesto `allowPublicClient` `true`para sua aplicação e defina como . |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| `unknown_user Message`: Não foi possível identificar o usuário logado| A biblioteca não pôde consultar o usuário de login do Windows atual ou este usuário não está aderido a AD ou AAD (os usuários unidos no local de trabalho não são suportados). Mitigação 1: no UWP, verifique se o aplicativo possui os seguintes recursos: Autenticação Corporativa, Redes Privadas (Cliente e Servidor), Informações da Conta do Usuário. Mitigação 2: Implemente sua própria lógica para john@contoso.combuscar o `AcquireTokenByIntegratedWindowsAuth` nome de usuário (por exemplo, ) e use o formulário que leva no nome de usuário.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| Esse método depende de um protocolo exposto pelo AD (Active Directory). Se um usuário foi criado no Azure Active Directory sem suporte do AD (usuário "gerenciado"), esse método falhará. Usuários criados no AD e com suporte do AAD (usuários "federados") poderão se beneficiar desse método de autenticação não interativo. Mitigação: Use autenticação interativa.|

### `MsalUiRequiredException`

Um dos códigos de status comuns `MsalError.InvalidGrantError`retornados de MSAL.NET quando ligaé `AcquireTokenSilent()` . Esse código de status significa que o aplicativo deve chamar a biblioteca de autenticação novamente, mas no modo interativo (AcquireTokenInteractive ou AcquireTokenByDeviceCodeFlow para aplicativos públicos clientes, e fazer um desafio em aplicativos da Web). Isso porque é necessária uma interação adicional do usuário antes que o token de autenticação possa ser emitido.

Na maioria das `AcquireTokenSilent` vezes, quando falha, é porque o cache de tokens não tem tokens que correspondem à sua solicitação. Os tokens de acesso expiram em 1 hora e `AcquireTokenSilent` tentarão buscar um novo com base em um token de atualização (em termos OAuth2, este é o fluxo "Refresh Token"). Esse fluxo também pode falhar por várias razões, por exemplo, se um admin de inquilino configurar políticas de login mais rigorosas. 

A interação visa fazer com que o usuário faça uma ação. Algumas dessas condições são fáceis de resolver (por exemplo, aceitar Termos de Uso com um único clique), e algumas não podem ser resolvidas com a configuração atual (por exemplo, a máquina em questão precisa se conectar a uma rede corporativa específica). Alguns ajudam o usuário a configurar a autenticação multifatorial ou instalar o Microsoft Authenticator em seu dispositivo.

### <a name="msaluirequiredexception-classification-enumeration"></a>`MsalUiRequiredException`enumeração classificação

A MSAL `Classification` expõe um campo, que você pode ler para fornecer uma melhor experiência ao usuário, por exemplo, para dizer ao usuário que sua senha expirou ou que eles precisarão fornecer consentimento para usar alguns recursos. Os valores suportados `UiRequiredExceptionClassification` fazem parte do enum:

| classificação    | Significado           | Manuseio recomendado |
|-------------------|-------------------|----------------------|
| Ação Básica | A condição pode ser resolvida pela interação do usuário durante o fluxo de autenticação interativo. | Chamada AcquireTokenInteractively(). |
| Ação adicional | A condição pode ser resolvida por interação corretiva adicional com o sistema, fora do fluxo de autenticação interativo. | Chamada AcquireTokenInteractively() para mostrar uma mensagem que explica a ação corretiva. O aplicativo de chamada pode optar por ocultar fluxos que requerem additional_action se o usuário não concluir a ação corretiva. |
| Somente mensagem      | A condição não pode ser resolvida neste momento. O lançamento do fluxo de autenticação interativo mostrará uma mensagem explicando a condição. | Ligue para acquireTokenInteractively() para mostrar uma mensagem que explique a condição. AcquireTokenInteractively() retornará o erro do UsuárioCancelado após o usuário ler a mensagem e fechar a janela. O aplicativo de chamada pode optar por ocultar fluxos que resultem em message_only se o usuário não se beneficiar da mensagem.|
| Consentimentonecessário  | O consentimento do usuário está faltando ou foi revogado. | Chamada AcquireTokenInteractively() para que o usuário dê consentimento. |
| Senha do usuárioExpirada | A senha do usuário expirou. | Chamada AcquireTokenInteractively() para que o usuário possa redefinir sua senha. |
| PromptNeverFailed| A Autenticação Interativa foi chamada com o parâmetro prompt=never, forçando a MSAL a confiar em cookies do navegador e não a exibir o navegador. Isso falhou. | Chamada AcquireTokenInteractively() sem Prompt.None |
| AcquireTokenSilentFailed | O MSAL SDK não tem informações suficientes para buscar um token do cache. Isso pode ser porque nenhum token está no cache ou uma conta não foi encontrada. A mensagem de erro tem mais detalhes.  | Chamada AcquireTokenInteractively(). |
| Nenhum    | Não há mais detalhes. A condição pode ser resolvida pela interação do usuário durante o fluxo de autenticação interativo. | Chamada AcquireTokenInteractively(). |

## <a name="net-code-example"></a>Exemplo de código do .NET

```csharp
AuthenticationResult res;
try
{
 res = await application.AcquireTokenSilent(scopes, account)
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex) when (ex.ErrorCode == MsalError.InvalidGrantError)
{
 switch (ex.Classification)
 {
  case UiRequiredExceptionClassification.None:
   break;
  case UiRequiredExceptionClassification.MessageOnly:
  // You might want to call AcquireTokenInteractive(). Azure AD will show a message
  // that explains the condition. AcquireTokenInteractively() will return UserCanceled error
  // after the user reads the message and closes the window. The calling application may choose
  // to hide features or data that result in message_only if the user is unlikely to benefit 
  // from the message
  try
  {
      res = await application.AcquireTokenInteractive(scopes).ExecuteAsync();
  }
  catch (MsalClientException ex2) when (ex2.ErrorCode == MsalError.AuthenticationCanceledError)
  {
   // Do nothing. The user has seen the message
  }
  break;

  case UiRequiredExceptionClassification.BasicAction:
  // Call AcquireTokenInteractive() so that the user can, for instance accept terms
  // and conditions

  case UiRequiredExceptionClassification.AdditionalAction:
  // You might want to call AcquireTokenInteractive() to show a message that explains the remedial action. 
  // The calling application may choose to hide flows that require additional_action if the user 
  // is unlikely to complete the remedial action (even if this means a degraded experience)

  case UiRequiredExceptionClassification.ConsentRequired:
  // Call AcquireTokenInteractive() for user to give consent.
  
  case UiRequiredExceptionClassification.UserPasswordExpired:
  // Call AcquireTokenInteractive() so that user can reset their password
  
  case UiRequiredExceptionClassification.PromptNeverFailed:
  // You used WithPrompt(Prompt.Never) and this failed
  
  case UiRequiredExceptionClassification.AcquireTokenSilentFailed:
  default:
  // May be resolved by user interaction during the interactive authentication flow.
  res = await application.AcquireTokenInteractive(scopes)
                         .ExecuteAsync(); break;
 }
}
```

## <a name="javascript"></a>[Javascript](#tab/javascript)

O MSAL.js fornece objetos de erro que abstraem e classificam os diferentes tipos de erros comuns. Ele também fornece interface para acessar detalhes específicos dos erros, como mensagens de erro para manuseá-las adequadamente.

### <a name="error-object"></a>Objeto de erro

```javascript
export class AuthError extends Error {
    // This is a short code describing the error
    errorCode: string;
    // This is a descriptive string of the error,
    // and may also contain the mitigation strategy
    errorMessage: string;
    // Name of the error class
    this.name = "AuthError";
}
```

Ao estender a classe do erro, você terá acesso às seguintes propriedades:
- `AuthError.message`: O `errorMessage`mesmo que o .
- `AuthError.stack`: Rastreamento de pilha para erros lançados.

### <a name="error-types"></a>Tipos de erro

Os tipos de erro a seguir estão disponíveis:

- `AuthError`: Classe de erro base para a biblioteca MSAL.js, também usada para erros inesperados.

- `ClientAuthError`: Classe de erro, que denota um problema com a autenticação do Cliente. A maioria dos erros originados da biblioteca serão ClientAuthErrors. Esses erros resultam de coisas como chamar um método de login quando o login já está em andamento, o usuário cancela o login, e assim por diante.

- `ClientConfigurationError`: Classe de `ClientAuthError` erro, estende-se lançada antes que as solicitações sejam feitas quando os parâmetros de configuração do usuário dado estão mal formados ou ausentes.

- `ServerError`: Classe de erro, representa as strings de erro enviadas pelo servidor de autenticação. Esses erros poderão ser: parâmetros ou formatos da solicitação inválidos ou qualquer outro erro que impeça o servidor de autenticar ou autorizar o usuário.

- `InteractionRequiredAuthError`: Classe de `ServerError` erro, estende-se para representar erros do servidor, que requerem uma chamada interativa. Esse erro é `acquireTokenSilent` descartado se o usuário for obrigado a interagir com o servidor para fornecer credenciais ou consentimento para autenticação/autorização. Os códigos `"interaction_required"` `"login_required"`de `"consent_required"`erro incluem , e .

Para o manuseio de erros em fluxos`loginRedirect` `acquireTokenRedirect`de autenticação com métodos de redirecionamento ( ), você precisará `handleRedirectCallback()` registrar o retorno de chamada, que é chamado de sucesso ou falha após o redirecionamento usando o método da seguinte forma:

```javascript
function authCallback(error, response) {
    //handle redirect response
}

var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallback(authCallback);
myMSALObj.acquireTokenRedirect(request);
```

Os métodos para experiência de pop-up (`loginPopup`, `acquireTokenPopup`) retornam promessas, de modo que seja possível usar o padrão de promessa (.then e .catch) para tratá-los, conforme mostrado:

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="errors-that-require-interaction"></a>Erros que requerem interação

Um erro é retornado quando você tenta usar um método não `acquireTokenSilent`interativo de adquirir um token como , mas o MSAL não poderia fazê-lo silenciosamente.

As possíveis razões são:

- é necessário entrar
- é necessário consentir
- é necessário passar por uma experiência de autenticação multifator.

A correção é chamar um método interativo como `acquireTokenPopup` ou `acquireTokenRedirect`:

```javascript
// Request for Access Token
myMSALObj.acquireTokenSilent(request).then(function (response) {
    // call API
}).catch( function (error) {
    // call acquireTokenPopup in case of acquireTokenSilent failure
    // due to consent or interaction required
    if (error.errorCode === "consent_required"
    || error.errorCode === "interaction_required"
    || error.errorCode === "login_required") {
        myMSALObj.acquireTokenPopup(request).then(
            function (response) {
                // call API
            }).catch(function (error) {
                console.log(error);
            });
    }
});
```

## <a name="python"></a>[Python](#tab/python)

No MSAL for Python, a maioria dos erros são transmitidos como um valor de retorno da chamada da API. O erro é representado como um dicionário contendo a resposta JSON da plataforma de identidade Microsoft.

* Uma resposta bem `"access_token"` sucedida contém a chave. O formato da resposta é definido pelo protocolo OAuth2. Para obter mais informações, consulte [5.1 Resposta bem sucedida](https://tools.ietf.org/html/rfc6749#section-5.1)
* Uma resposta `"error"` de `"error_description"`erro contém e geralmente . O formato da resposta é definido pelo protocolo OAuth2. Para obter mais informações, consulte [5.2 Error Response](https://tools.ietf.org/html/rfc6749#section-5.2)

Quando um erro é `"error_description"` retornado, a chave contém uma mensagem legível por humanos; que, por sua vez, normalmente contém um código de erro da plataforma de identidade da Microsoft. Para obter detalhes sobre os vários códigos de erro, consulte [Códigos de erro de autenticação e autorização](https://docs.microsoft.com/azure/active-directory/develop/reference-aadsts-error-codes).

No MSAL for Python, exceções são raras porque a maioria dos erros são manipulados retornando um valor de erro. A `ValueError` exceção só é lançada quando há um problema com a forma como você está tentando usar a biblioteca - como quando os parâmetros de API são malformados.

## <a name="java"></a>[Java](#tab/java)

No MSAL para Java, existem três `MsalClientException` `MsalServiceException`tipos `MsalInteractionRequiredException`de exceções: , e ; todos os `MsalException`que herdam de .

- `MsalClientException`é jogado quando ocorre um erro que é local para a biblioteca ou dispositivo.
- `MsalServiceException`é acionado quando o serviço de token seguro (STS) retorna uma resposta de erro ou outro erro de rede ocorre.
- `MsalInteractionRequiredException`é lançada quando a interação com a UI é necessária para que a autenticação seja bem sucedida.

### <a name="msalserviceexception"></a>MsalServiceException

`MsalServiceException`expõe cabeçalhos HTTP retornados nas solicitações ao STS. Acessá-los via`MsalServiceException.headers()`

### <a name="msalinteractionrequiredexception"></a>MsalInteractionRequiredException

Um dos códigos de status comuns retornados do MSAL para Java ao ligar `AcquireTokenSilently()` é `InvalidGrantError`. Isso significa que é necessária uma interação adicional do usuário antes que um token de autenticação possa ser emitido. Seu aplicativo deve chamar a biblioteca de autenticação `AuthorizationCodeParameters` `DeviceCodeParameters` novamente, mas no modo interativo enviando ou para aplicativos públicos do cliente.

Na maioria das `AcquireTokenSilently` vezes, quando falha, é porque o cache de token não tem um token que corresponda à sua solicitação. Os tokens de acesso expiram em uma hora e `AcquireTokenSilently` tentarão obter um novo com base em um token de atualização. Em termos OAuth2, este é o fluxo do Refresh Token. Esse fluxo também pode falhar por várias razões, como quando um admin inquilino configura políticas de login mais rigorosas.

Algumas condições que resultam neste erro são fáceis de resolver pelos usuários. Por exemplo, eles podem precisar aceitar Termos de Uso. Ou talvez a solicitação não possa ser atendida com a configuração atual porque a máquina precisa se conectar a uma rede corporativa específica.

A MSAL `reason` expõe um campo, que você pode usar para proporcionar uma melhor experiência ao usuário. Por exemplo, `reason` o campo pode levá-lo a dizer ao usuário que sua senha expirou ou que eles precisarão fornecer consentimento para usar alguns recursos. Os valores suportados `InteractionRequiredExceptionReason` fazem parte do enum:

| Motivo | Significado | Manuseio recomendado |
|---------|-----------|-----------------------------|
| `BasicAction` | A condição pode ser resolvida pela interação do usuário durante o fluxo de autenticação interativo | Chamada `acquireToken` com parâmetros interativos |
| `AdditionalAction` | A condição pode ser resolvida por interação corretiva adicional com o sistema fora do fluxo de autenticação interativo. | Ligue `acquireToken` com parâmetros interativos para mostrar uma mensagem que explique a ação corretiva a ser tomada. O aplicativo de chamada pode optar por ocultar fluxos que requerem ação adicional se o usuário não concluir a ação corretiva. |
| `MessageOnly` | A condição não pode ser resolvida neste momento. Inicie o fluxo de autenticação interativo para mostrar uma mensagem explicando a condição. | Ligue `acquireToken` com parâmetros interativos para mostrar uma mensagem que explique a condição. `acquireToken`retornará `UserCanceled` o erro depois que o usuário ler a mensagem e fechar a janela. O aplicativo pode optar por ocultar fluxos que resultem em mensagem se o usuário não se beneficiar da mensagem. |
| `ConsentRequired`| O consentimento do usuário está faltando ou foi revogado. |Ligue `acquireToken` com parâmetros interativos para que o usuário possa dar consentimento. |
| `UserPasswordExpired` | A senha do usuário expirou. | Ligue `acquireToken` com parâmetro interativo para que o usuário possa redefinir sua senha |
| `None` |  Mais detalhes são fornecidos. A condição pode ser resolvida pela interação do usuário durante o fluxo de autenticação interativo. | Chamada `acquireToken` com parâmetros interativos |

### <a name="code-example"></a>Exemplo de código

```java
        IAuthenticationResult result;
        try {
            PublicClientApplication application = PublicClientApplication
                    .builder("clientId")
                    .b2cAuthority("authority")
                    .build();

            SilentParameters parameters = SilentParameters
                    .builder(Collections.singleton("scope"))
                    .build();

            result = application.acquireTokenSilently(parameters).join();
        }
        catch (Exception ex){
            if(ex instanceof MsalInteractionRequiredException){
                // AcquireToken by either AuthorizationCodeParameters or DeviceCodeParameters
            } else{
                // Log and handle exception accordingly
            }
        }
```

## <a name="iosmacos"></a>[iOS/macOS](#tab/iosmacos)

A lista completa de erros de MSAL para iOS e macOS está listada no [enum MSALError](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128).

Todos os erros produzidos `MSALErrorDomain` pela MSAL são devolvidos com domínio.

Para erros do sistema, `NSError` o MSAL retorna o original da API do sistema. Por exemplo, se a aquisição de token falhar devido à falta `NSURLErrorDomain` de `NSURLErrorNotConnectedToInternet` conectividade de rede, a MSAL retorna um erro com o domínio e o código.

Recomendamos que você lide com pelo menos os dois erros de MSAL a seguir no lado do cliente:

- `MSALErrorInteractionRequired`: O usuário deve fazer uma solicitação interativa. Existem muitas condições que podem levar a esse erro, como uma sessão de autenticação expirada ou a necessidade de requisitos adicionais de autenticação. Ligue para a API interativa de aquisição de tokens Da MSAL para recuperar. 

- `MSALErrorServerDeclinedScopes`: Alguns ou todos os escopos foram recusados. Decida se deve continuar apenas com os escopos concedidos ou interromper o processo de login.

> [!NOTE]
> O `MSALInternalError` enum só deve ser usado para referência e depuração. Não tente lidar automaticamente com esses erros em tempo de execução. Se o seu aplicativo encontrar algum `MSALInternalError`dos erros que se enquadram, você pode querer mostrar um usuário genérico enfrentando uma mensagem explicando o que aconteceu.

Por exemplo, `MSALInternalErrorBrokerResponseNotReceived` significa que o usuário não completou a autenticação e retornou manualmente ao aplicativo. Neste caso, seu aplicativo deve mostrar uma mensagem de erro genérica explicando que a autenticação não foi concluída e sugerir que eles tentem autenticar novamente.

O seguinte código de amostra Objective-C demonstra as melhores práticas para lidar com algumas condições comuns de erro:

```objc
    MSALInteractiveTokenParameters *interactiveParameters = ...;
    MSALSilentTokenParameters *silentParameters = ...;
    
    MSALCompletionBlock completionBlock;
    __block __weak MSALCompletionBlock weakCompletionBlock;
    
    weakCompletionBlock = completionBlock = ^(MSALResult *result, NSError *error)
    {
        if (!error)
        {
            // Use result.accessToken
            NSString *accessToken = result.accessToken;
            return;
        }
        
        if ([error.domain isEqualToString:MSALErrorDomain])
        {
            switch (error.code)
            {
                case MSALErrorInteractionRequired:
                {
                    // Interactive auth will be required
                    [application acquireTokenWithParameters:interactiveParameters
                                            completionBlock:weakCompletionBlock];
                    
                    break;
                }
                    
                case MSALErrorServerDeclinedScopes:
                {
                    // These are list of granted and declined scopes.
                    NSArray *grantedScopes = error.userInfo[MSALGrantedScopesKey];
                    NSArray *declinedScopes = error.userInfo[MSALDeclinedScopesKey];
                    
                    // To continue acquiring token for granted scopes only, do the following
                    silentParameters.scopes = grantedScopes;
                    [application acquireTokenSilentWithParameters:silentParameters
                                                  completionBlock:weakCompletionBlock];
                    
                    // Otherwise, instead, handle error fittingly to the application context
                    break;
                }
                    
                case MSALErrorServerProtectionPoliciesRequired:
                {
                    // Integrate the Intune SDK and call the
                    // remediateComplianceForIdentity:silent: API.
                    // Handle this error only if you integrated Intune SDK.
                    // See more info here: https://aka.ms/intuneMAMSDK
                    
                    break;
                }
                    
                case MSALErrorUserCanceled:
                {
                    // The user cancelled the web auth session.
                    // You may want to ask the user to try again.
                    // Handling of this error is optional.
                    
                    break;
                }
                    
                case MSALErrorInternal:
                {
                    // Log the error, then inspect the MSALInternalErrorCodeKey
                    // in the userInfo dictionary.
                    // Display generic error message to the end user
                    // More detailed information about the specific error
                    // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                    NSLog(@"Failed with error %@", error);
                    
                    break;
                }
                    
                default:
                    NSLog(@"Failed with unknown MSAL error %@", error);
                    
                    break;
            }
            
            return;
        }
        
        // Handle no internet connection.
        if ([error.domain isEqualToString:NSURLErrorDomain] && error.code == NSURLErrorNotConnectedToInternet)
        {
            NSLog(@"No internet connection.");
            return;
        }
        
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        NSLog(@"Failed with error %@", error);
    };
    
    // Acquire token silently
    [application acquireTokenSilentWithParameters:silentParameters
                                  completionBlock:completionBlock];

     // or acquire it interactively.
     [application acquireTokenWithParameters:interactiveParameters
                             completionBlock:completionBlock];
```

```swift
    let interactiveParameters: MSALInteractiveTokenParameters = ...
    let silentParameters: MSALSilentTokenParameters = ...
            
    var completionBlock: MSALCompletionBlock!
    completionBlock = { (result: MSALResult?, error: Error?) in
                
        if let result = result
        {
            // Use result.accessToken
            let accessToken = result.accessToken
            return
        }

        guard let error = error as NSError? else { return }

        if error.domain == MSALErrorDomain, let errorCode = MSALError(rawValue: error.code)
        {
            switch errorCode
            {
                case .interactionRequired:
                    // Interactive auth will be required
                    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)

                case .serverDeclinedScopes:
                    let grantedScopes = error.userInfo[MSALGrantedScopesKey]
                    let declinedScopes = error.userInfo[MSALDeclinedScopesKey]

                    if let scopes = grantedScopes as? [String] {
                        silentParameters.scopes = scopes
                        application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
                    }
                        
                    case .serverProtectionPoliciesRequired:
                        // Integrate the Intune SDK and call the
                        // remediateComplianceForIdentity:silent: API.
                        // Handle this error only if you integrated Intune SDK.
                        // See more info here: https://aka.ms/intuneMAMSDK
                        break
                        
                    case .userCanceled:
                       // The user cancelled the web auth session.
                       // You may want to ask the user to try again.
                       // Handling of this error is optional.
                       break
                        
                    case .internal:
                        // Log the error, then inspect the MSALInternalErrorCodeKey
                        // in the userInfo dictionary.
                        // Display generic error message to the end user
                        // More detailed information about the specific error
                        // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                        print("Failed with error \(error)");
                        
                    default:
                        print("Failed with unknown MSAL error \(error)")
            }
        }
                
        // Handle no internet connection.
        if error.domain == NSURLErrorDomain && error.code == NSURLErrorNotConnectedToInternet
        {
            print("No internet connection.")
            return
        }
                
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        print("Failed with error \(error)");    
    }
   
    // Acquire token silently
    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
 
    // or acquire it interactively.
    application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
```

---

## <a name="conditional-access-and-claims-challenges"></a>Acesso condicional e desafio de declarações

Ao obter tokens silenciosamente, seu aplicativo pode receber erros quando um [desafio de reivindicações de acesso condicional,](../azuread-dev/conditional-access-dev-guide.md) como a política MFA, é exigido por uma API que você está tentando acessar.

O padrão para lidar com esse erro é adquirir interativamente um token usando o MSAL. A obtenção interativa de um token solicita a participação do usuário e oferece a oportunidade para atender à política de acesso condicional exigida.

Em determinados casos, ao chamar uma API que exige acesso condicional, você poderá receber um desafio de declarações no erro da API. Por exemplo, se a política de acesso condicional é para ter um dispositivo gerenciado (Intune) o erro será algo como [AADSTS53000: Seu dispositivo é obrigado a ser gerenciado para acessar este recurso](reference-aadsts-error-codes.md) ou algo semelhante. Nesse caso, é possível passar as declarações na chamada do token de aquisição, de modo que o usuário seja solicitado a atender à política apropriada.

### <a name="net"></a>.NET

Ao chamar uma API que exige acesso condicional do MSAL.NET, o aplicativo deverá tratar as exceções de desafio de declaração. Isso aparecerá como [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) em que a propriedade [Declarações](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet) não estará vazia.

Para lidar com o desafio de reivindicação, você precisará usar o `.WithClaim()` método da `PublicClientApplicationBuilder` classe.

### <a name="javascript"></a>JavaScript

Ao obter tokens silenciosamente `acquireTokenSilent`(usando ) usando MSAL.js, seu aplicativo pode receber erros quando um [desafio de reivindicações de acesso condicional,](../azuread-dev/conditional-access-dev-guide.md) como a política MFA, é exigido por uma API que você está tentando acessar.

O padrão para tratar esse erro é fazer uma chamada interativa para obter o token em MSAL.js, como `acquireTokenPopup` ou `acquireTokenRedirect`, conforme no exemplo a seguir:

```javascript
myMSALObj.acquireTokenSilent(accessTokenRequest).then(function (accessTokenResponse) {
    // call API
}).catch( function (error) {
    if (error instanceof InteractionRequiredAuthError) {
        // Extract claims from error message
        accessTokenRequest.claimsRequest = extractClaims(error.errorMessage);
        // call acquireTokenPopup in case of InteractionRequiredAuthError failure
        myMSALObj.acquireTokenPopup(accessTokenRequest).then(function (accessTokenResponse) {
            // call API
        }).catch(function (error) {
            console.log(error);
        });
    }
});
```

A obtenção interativa do token solicita a participação do usuário e oferece a oportunidade para atender à política de acesso condicional exigida.

Ao chamar uma API que exige acesso condicional, você poderá receber um desafio de declarações no erro da API. Neste caso, você pode passar as reivindicações `claimsRequest` devolvidas `AuthenticationParameters.ts` no erro para o campo da classe para satisfazer a política apropriada. 

Consulte [Solicitando reclamações adicionais](active-directory-optional-claims.md) para obter mais detalhes.

### <a name="msal-for-ios-and-macos"></a>MSAL para iOS e macOS

O MSAL para iOS e macOS permite que você solicite reivindicações específicas em cenários de aquisição de tokens interativos e silenciosos.

Para solicitar reclamações `claimsRequest` `MSALSilentTokenParameters` personalizadas, especifique ou `MSALInteractiveTokenParameters`.

Consulte [Solicitar reivindicações personalizadas usando MSAL para iOS e macOS](request-custom-claims.md) para obter mais informações.

## <a name="retrying-after-errors-and-exceptions"></a>Tentar novamente após erros e exceções

Espera-se que você implemente suas próprias políticas de repetição ao ligar para o MSAL. A MSAL faz chamadas HTTP para o serviço AAD, e podem ocorrer falhas ocasionais, por exemplo, a rede pode cair ou o servidor está sobrecarregado.  

### <a name="http-error-codes-500-600"></a>Códigos de erro HTTP 500-600

O MSAL.NET implementa um mecanismo de nova tentativa única simples para erros com códigos de erro HTTP 500-600.

### <a name="http-429"></a>HTTP 429

Quando o Service Token Server (STS) está sobrecarregado com muitas solicitações, ele retorna o erro HTTP 429 com uma dica sobre quanto tempo até que você possa tentar novamente no campo `Retry-After` de resposta.

### <a name="net"></a>.NET

[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) aparece `System.Net.Http.Headers.HttpResponseHeaders` como `namedHeaders`uma propriedade . Você pode usar informações adicionais do código de erro para melhorar a confiabilidade de seus aplicativos. No caso descrito, você `RetryAfterproperty` pode usar `RetryConditionHeaderValue`o (do tipo ) e calcular quando tentar novamente.

Aqui está um exemplo para um aplicativo daemon usando o fluxo de credenciais do cliente. Você pode adaptar isso a qualquer um dos métodos para adquirir um token.

```csharp
do
{
    retry = false;
    TimeSpan? delay;
    try
    {
         result = await publicClientApplication.AcquireTokenForClient(scopes, account)
                                           .ExecuteAsync();
    }
    catch (MsalServiceException serviceException)
    {
         if (ex.ErrorCode == "temporarily_unavailable")
         {
             RetryConditionHeaderValue retryAfter = serviceException.Headers.RetryAfter;
             if (retryAfter.Delta.HasValue)
             {
                 delay = retryAfter.Delta;
             }
             else if (retryAfter.Date.HasValue)
             {
                 delay = retryAfter.Date.Value.Offset;
             }
         }
    }
    …
    if (delay.HasValue)
    {
        Thread.Sleep((int)delay.Value.TotalMilliseconds); // sleep or other
        retry = true;
    }
} while (retry);
```
