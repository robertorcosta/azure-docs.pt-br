---
title: Erros e exceções (MSAL)
titleSuffix: Microsoft identity platform
description: Saiba como tratar erros e exceções, Acesso Condicional e desafios de declarações em aplicativos da MSAL.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: d65d85d21521a6277a3ea823a8c9e83a34e3f42c
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83772090"
---
# <a name="handle-msal-exceptions-and-errors"></a>Manipular exceções e erros da MSAL

Este artigo fornece uma visão geral dos diferentes tipos de erros e das recomendações para lidar com erros comuns de entrada.

## <a name="msal-error-handling-basics"></a>Noções básicas de tratamento de erros da MSAL

As exceções na MSAL (Biblioteca de Autenticação da Microsoft) são destinadas aos desenvolvedores de aplicativos para solucionar problemas, não para exibição aos usuários finais. Mensagens de exceção não são localizadas.

Ao processar exceções e erros, é possível usar o próprio tipo de exceção e o código de erro para distinguir entre exceções.  Para obter uma lista de códigos de erro, consulte [Códigos de erro de autenticação e autorização](reference-aadsts-error-codes.md).

Durante a experiência de entrada, você pode encontrar erros sobre consentimentos, Acesso Condicional (MFA, Gerenciamento de Dispositivo, Restrições baseadas em local), emissão e resgate de token e propriedades de usuário.

Consulte a seção a seguir, que corresponde ao idioma que você está usando para obter mais detalhes sobre o tratamento de erros para seu aplicativo.

## <a name="net"></a>[.NET](#tab/dotnet)

Ao processar exceções .NET, é possível usar o próprio tipo de exceção e o membro `ErrorCode` para distinguir entre exceções. Os valores `ErrorCode` são constantes do tipo [MsalError](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet).

Você também pode dar uma olhada nos campos [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet), [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) e [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet).

Caso [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) seja gerado, tente [Códigos de erro de autenticação e autorização](reference-aadsts-error-codes.md) para ver se o código está listado lá.

### <a name="common-net-exceptions"></a>Exceções .NET comuns

Aqui são apresentadas as exceções comuns que podem ser geradas e algumas possíveis mitigações:  

| Exceção | Código do erro | Atenuação|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001: O usuário ou administrador não consentiu em usar o aplicativo com a ID '{appId}' nomeada '{appName}'. Envie uma solicitação de autorização interativa para esse usuário e recurso.| É necessário primeiro obter o consentimento do usuário. Caso não esteja usando o .NET Core (que não tem nenhuma interface do usuário da Web), chame (apenas uma vez) `AcquireTokeninteractive`. Se você estiver usando .NET Core ou não quiser fazer um `AcquireTokenInteractive`, o usuário poderá navegar em uma URL para dar o consentimento: `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read`. para chamar `AcquireTokenInteractive`: `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079: O usuário deverá usar a [MFA (autenticação multifator)](../authentication/concept-mfa-howitworks.md).| Não há mitigação. Se a MFA estiver configurada para o locatário e o AAD (Azure Active Directory) decidir impô-lo, será necessário fazer fallback de um fluxo interativo, como `AcquireTokenInteractive` ou `AcquireTokenByDeviceCode`.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) |AADSTS90010: Não há suporte para o tipo de concessão nos pontos de extremidade */common* ou */consumers*. Use o ponto de extremidade */organizations* ou específico do locatário. Você usou */common*.| Conforme explicado na mensagem do Azure AD, a autoridade deve ter um locatário ou, caso contrário, */organizations*.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002: O corpo da solicitação deve conter o seguinte parâmetro: `client_secret or client_assertion`.| Essa exceção poderá ser gerada caso o aplicativo não esteja registrado como um aplicativo cliente público no Azure AD. No portal do Azure, edite o manifesto do aplicativo e defina `allowPublicClient` como `true`. |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| `unknown_user Message`: Não foi possível identificar o usuário conectado| A biblioteca não pôde consultar o usuário atual conectado ao Windows ou esse usuário não entrou no AD ou AAD (não há suporte para usuários ingressados no local de trabalho). Mitigação 1: na Plataforma Universal do Windows, verifique se o aplicativo tem os seguintes recursos: Autenticação Empresarial, Redes Privadas (Cliente e Servidor), Informações sobre a Conta de Usuário. Mitigação 2: Implemente sua própria lógica para buscar o nome de usuário (por exemplo, john@contoso.com) e use o formulário `AcquireTokenByIntegratedWindowsAuth` que recebe o nome de usuário.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| Esse método depende de um protocolo exposto pelo AD (Active Directory). Se um usuário foi criado no Azure Active Directory sem suporte do AD (usuário "gerenciado"), esse método falhará. Usuários criados no AD e com suporte do AAD (usuários "federados") poderão se beneficiar desse método de autenticação não interativo. Mitigação: Use autenticação interativa.|

### `MsalUiRequiredException`

Um código de status comum que retornado do MSAL.NET ao chamar `AcquireTokenSilent()` é `MsalError.InvalidGrantError`. Esse código de status indica que o aplicativo deve chamar a biblioteca de autenticação novamente, mas no modo interativo (AcquireTokenInteractive ou AcquireTokenByDeviceCodeFlow para aplicativos cliente públicos e fazer um desafio em aplicativos Web). Isso acontece porque é preciso ter a interação adicional do usuário antes que o token de autenticação possa ser emitido.

Na maioria das vezes em que `AcquireTokenSilent` falha, deve-se ao fato de o cache de token não ter tokens que correspondam à sua solicitação. Os tokens de acesso expiram em 1 hora e `AcquireTokenSilent` tentará buscar um novo com base em um token de atualização (em termos de OAuth2, esse é o fluxo do "Token de atualização"). Esse fluxo também pode falhar por vários motivos; por exemplo, caso um administrador de locatários configure políticas de logon mais rigorosas. 

A interação tem como objetivo fazer com que o usuário realize uma ação. Algumas dessas condições são de fácil resolução por parte dos usuários (por exemplo, aceitar os Termos de Uso com um único clique), e algumas não podem ser resolvidas com a configuração atual (por exemplo, o computador em questão precisa se conectar a uma rede corporativa específica). Algumas ajudam a configurar o usuário na autenticação multifator ou a instalar o Microsoft Authenticator em seu dispositivo.

### <a name="msaluirequiredexception-classification-enumeration"></a>Enumeração da classificação de `MsalUiRequiredException`

A MSAL expõe um campo `Classification`, o qual você pode ler para fornecer uma experiência de usuário melhor, por exemplo, para informar ao usuário que sua senha expirou ou que eles precisarão fornecer consentimento para usar alguns recursos. Os valores com suporte fazem parte da enumeração de `UiRequiredExceptionClassification`:

| classificação    | Significado           | Ações recomendadas |
|-------------------|-------------------|----------------------|
| BasicAction | A condição pode ser resolvida pela interação do usuário durante o fluxo da autenticação interativa. | Chame AcquireTokenInteractively(). |
| AdditionalAction | A condição pode ser resolvida por uma interação corretiva adicional com o sistema, fora do fluxo da autenticação interativa. | Chame AcquireTokenInteractively() para mostrar uma mensagem que explique a ação corretiva. O aplicativo da chamada pode optar por ocultar fluxos que exigem additional_action caso seja improvável que o usuário conclua a ação corretiva. |
| MessageOnly      | A condição não pode ser resolvida atualmente. Ao iniciar o fluxo da autenticação interativa, será exibida uma mensagem explicando a condição. | Chame AcquireTokenInteractively() para mostrar uma mensagem que explique a condição. AcquireTokenInteractively() retornará um erro UserCanceled depois que o usuário ler a mensagem e fechar a janela. O aplicativo da chamada pode optar por ocultar os fluxos que resultam em message_only caso seja improvável que o usuário se beneficie da mensagem.|
| ConsentRequired  | O consentimento do usuário está ausente ou foi revogado. | Chame AcquireTokenInteractively() para que o usuário dê consentimento. |
| UserPasswordExpired | A senha do usuário expirou. | Chame AcquireTokenInteractively() para que o usuário possa redefinir sua senha. |
| PromptNeverFailed| A autenticação interativa foi chamada com o parâmetro prompt=never, forçando a MSAL a depender de cookies do navegador e não exibir o navegador. Isso falhou. | Chame AcquireTokenInteractively() sem Prompt.None |
| AcquireTokenSilentFailed | O SDK da MSAL não tem informações suficientes para buscar um token do cache. Isso pode ocorrer porque nenhum token está no cache ou porque uma conta não foi encontrada. A mensagem de erro tem mais detalhes.  | Chame AcquireTokenInteractively(). |
| Nenhum    | Nenhum detalhe adicional é fornecido. A condição pode ser resolvida pela interação do usuário durante o fluxo da autenticação interativa. | Chame AcquireTokenInteractively(). |

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

## <a name="javascript"></a>[JavaScript](#tab/javascript)

A MSAL.js fornece objetos de erro que resume e classificam os diferentes tipos de erros comuns. Ele também fornece a interface para acessar detalhes específicos dos erros, como mensagens de erro, para tratá-los adequadamente.

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
- `AuthError.message`:  igual a `errorMessage`.
- `AuthError.stack`: Rastreamento de pilha para erros gerados.

### <a name="error-types"></a>Tipos de erro

Os tipos de erro a seguir estão disponíveis:

- `AuthError`: Classe de erro base para a biblioteca MSAL.js, também usada para erros inesperados.

- `ClientAuthError`: classe de erro, a qual indica um problema com a Autenticação de Cliente. A maioria dos erros originados da biblioteca serão ClientAuthErrors. Esses erros resultam de coisas como chamar um método de logon quando o logon já está em andamento, q o usuário cancela o logon, e assim por diante.

- `ClientConfigurationError`: classe de erro, a qual estende o `ClientAuthError` gerado antes que as solicitações sejam feitas quando os parâmetros de configuração do usuário fornecidos estiverem malformados ou ausentes.

- `ServerError`: classe de erro, a qual representa as cadeias de caracteres de erro enviadas pelo servidor de autenticação. Esses erros poderão ser: parâmetros ou formatos da solicitação inválidos ou qualquer outro erro que impeça o servidor de autenticar ou autorizar o usuário.

- `InteractionRequiredAuthError`: classe de erro, a qual estende o `ServerError` para representar erros do servidor que exigem uma chamada interativa. Esse erro será gerado por `acquireTokenSilent` caso o usuário precise interagir com o servidor para fornecer credenciais ou consentimentos para autenticação/autorização. São exemplos de códigos de erro: `"interaction_required"`, `"login_required"` e `"consent_required"`.

Para tratamento de erro em fluxos de autenticação com métodos de redirecionamento (`loginRedirect`, `acquireTokenRedirect`) você precisará registrar o retorno de chamada, o qual será chamado com êxito ou falha após redirecionamento usando o método `handleRedirectCallback()` conforme a seguir:

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

### <a name="errors-that-require-interaction"></a>Erros que exigem interação

Um erro será retornado quando você tentar usar um método não interativo de aquisição de um token, como `acquireTokenSilent`, sem que a MSAL pudesse fazê-lo silenciosamente.

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

Na MSAL para Python, a maioria dos erros é transmitida como um valor de retorno da chamada à API. O erro é representado como um dicionário contendo a resposta JSON da plataforma de identidade da Microsoft.

* Uma resposta bem-sucedida contém a chave `"access_token"`. O formato da resposta é definido pelo protocolo OAuth2. Para obter mais informações, consulte [5.1 Resposta bem-sucedida](https://tools.ietf.org/html/rfc6749#section-5.1).
* Uma resposta de erro contém `"error"` e, geralmente, `"error_description"`. O formato da resposta é definido pelo protocolo OAuth2. Para obter mais informações, consulte [5.2 Resposta de erro](https://tools.ietf.org/html/rfc6749#section-5.2).

Quando um erro é retornado, a chave `"error_description"` conterá uma mensagem legível, a qual, por sua vez, normalmente contém um código de erro da plataforma de identidade da Microsoft. Para obter detalhes sobre os diversos códigos de erro, consulte [Códigos de erro de autenticação e autorização](https://docs.microsoft.com/azure/active-directory/develop/reference-aadsts-error-codes).

Na MSAL para Python, as exceções são raras, porque a maioria dos erros é tratada retornando um valor de erro. A exceção `ValueError` só é gerada quando houver um problema com o modo como você está tentando usar a biblioteca, por exemplo, quando os parâmetros da API estão malformados.

## <a name="java"></a>[Java](#tab/java)

Na MSAL para Java, há três tipos de exceções: `MsalClientException`, `MsalServiceException`e `MsalInteractionRequiredException`; todas que herdam de `MsalException`.

- `MsalClientException` será gerado quando ocorrer um erro local em relação à biblioteca ou ao dispositivo.
- `MsalServiceException` será gerado quando o STS (serviço de token de segurança) retornar uma resposta de erro ou ocorrer outro erro de rede.
- `MsalInteractionRequiredException` será gerado quando a interação da interface do usuário for necessária para que a autenticação seja realizada com sucesso.

### <a name="msalserviceexception"></a>MsalServiceException

`MsalServiceException` expõe cabeçalhos HTTP retornados nas solicitações para STS. Acesse-os via `MsalServiceException.headers()`.

### <a name="msalinteractionrequiredexception"></a>MsalInteractionRequiredException

Um código de status comum que retornado da MSAL para Java ao chamar `AcquireTokenSilently()` é `InvalidGrantError`. Isso indica que é preciso ter uma interação adicional do usuário antes que um token de autenticação possa ser emitido. Seu aplicativo deve chamar a biblioteca de autenticação novamente, mas no modo interativo, por meio do envio de `AuthorizationCodeParameters` ou `DeviceCodeParameters` para aplicativos cliente públicos.

Na maioria das vezes em que `AcquireTokenSilently` falha, deve-se ao fato de o cache de token não ter um token que correspondam à sua solicitação. Os tokens de acesso expiram em uma hora, e `AcquireTokenSilently` tentará obter um novo com base em um token de atualização. Em termos de OAuth2, esse é o fluxo do Token de atualização. Esse fluxo também pode falhar por vários motivos, como quando um administrador de locatários configurar políticas de logon mais rigorosas.

Algumas condições que resultam nesse erro são de fácil resolução pro parte dos usuários. Por exemplo, pode ser preciso que eles aceitem os Termos de Uso. Ou talvez a solicitação não possa ser cumprida com as configurações atuais porque a máquina precisa se conectar a uma rede corporativa específica.

A MSAL expõe um campo `reason`, que pode ser usado para fornecer uma melhor experiência do usuário. Por exemplo, o campo `reason` pode levar você a informar ao usuário que a senha dele expirou ou que ele precisará fornecer consentimento para usar alguns recursos. Os valores com suporte fazem parte da enumeração de `InteractionRequiredExceptionReason`:

| Motivo | Significado | Ações recomendadas |
|---------|-----------|-----------------------------|
| `BasicAction` | A condição pode ser resolvida pela interação do usuário durante o fluxo da autenticação interativa. | Chame `acquireToken` com parâmetros interativos. |
| `AdditionalAction` | A condição pode ser resolvida por uma interação corretiva adicional com o sistema, fora do fluxo da autenticação interativa. | Chame `acquireToken` com parâmetros interativos para mostrar uma mensagem que explique a ação corretiva a ser tomada. O aplicativo da chamada pode optar por ocultar fluxos que requeiram uma ação adicional caso seja improvável que o usuário conclua a ação corretiva. |
| `MessageOnly` | A condição não pode ser resolvida atualmente. Inicie o fluxo da autenticação interativa para exibir uma mensagem explicando a condição. | Chame `acquireToken` com parâmetros interativos para mostrar uma mensagem que explique a condição. `acquireToken` retornará o erro `UserCanceled` depois que o usuário ler a mensagem e fechar a janela. O aplicativo pode optar por ocultar os fluxos que resultam em mensagem caso seja improvável que o usuário se beneficie dela. |
| `ConsentRequired`| O consentimento do usuário está ausente ou foi revogado. |Chame `acquireToken` com parâmetros interativos para que o usuário possa dar seu consentimento. |
| `UserPasswordExpired` | A senha do usuário expirou. | Chame `acquireToken` com parâmetros interativos para que o usuário possa redefinir sua senha. |
| `None` |  Mais detalhes são fornecidos abaixo. A condição pode ser resolvida pela interação do usuário durante o fluxo da autenticação interativa. | Chame `acquireToken` com parâmetros interativos. |

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

A lista completa de erros da MSAL para iOS e macOS está listada na [enumeração de MSALError](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128).

Todos os erros produzidos pela MSAL são retornados com o domínio `MSALErrorDomain`.

Para erros do sistema, a MSAL retorna o `NSError` original da API do sistema. Por exemplo, caso a aquisição do token falhe devido à falta de conectividade de rede, a MSAL retornará um erro com o domínio `NSURLErrorDomain` e o código `NSURLErrorNotConnectedToInternet`.

Recomendamos que você trate pelo menos dos dois erros da MSAL a seguir no lado do cliente:

- `MSALErrorInteractionRequired`: o usuário deve fazer uma solicitação interativa. Há muitas condições que podem levar a esse erro, como uma sessão de autenticação expirada ou a necessidade de ter requisitos de autenticação adicionais. Chame a API de aquisição de token interativo da MSAL para recuperar. 

- `MSALErrorServerDeclinedScopes`: alguns ou todos os escopos foram recusados. Decida entre continuar com apenas os escopos concedidos ou interromper o processo de entrada.

> [!NOTE]
> A enumeração de `MSALInternalError` só deve ser usada para referência e depuração. Não tente tratar desses erros automaticamente no runtime. Caso seu aplicativo encontre algum dos erros que se enquadrem em `MSALInternalError`, talvez seja interessante mostrar uma mensagem genérica voltada ao usuário que explique o que aconteceu.

Por exemplo, `MSALInternalErrorBrokerResponseNotReceived` significa que o usuário não concluiu a autenticação e voltou manualmente para o aplicativo. Nesse caso, seu aplicativo deve mostrar uma mensagem de erro genérica explicando que a autenticação não foi concluída e sugerindo que o usuário tente autenticar novamente.

O código de exemplo Objective-C a seguir demonstra as melhores práticas para lidar com algumas condições de erro comuns:

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

Ao obter tokens silenciosamente, o aplicativo poderá receber erros quando um [desafio de declarações de acesso condicional](../azuread-dev/conditional-access-dev-guide.md), como a Política de MFA, for exigido por uma API que você está tentando acessar.

O padrão para tratar desse erro é obter um token de forma interativa usando a MSAL. A obtenção interativa de um token solicita a participação do usuário e oferece a oportunidade para atender à política de acesso condicional exigida.

Em determinados casos, ao chamar uma API que exige acesso condicional, você poderá receber um desafio de declarações no erro da API. Por exemplo, se a política de acesso condicional tiver um dispositivo gerenciado (Intune), o erro será semelhante a [AADSTS53000: O dispositivo deverá ser gerenciado para acessar este recurso](reference-aadsts-error-codes.md) ou algo similar. Nesse caso, é possível passar as declarações na chamada do token de aquisição, de modo que o usuário seja solicitado a atender à política apropriada.

### <a name="net"></a>.NET

Ao chamar uma API que exige acesso condicional do MSAL.NET, o aplicativo deverá tratar as exceções de desafio de declaração. Isso aparecerá como [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) em que a propriedade [Declarações](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet) não estará vazia.

Para tratar o desafio de declaração, será necessário usar o método `.WithClaim()` da classe `PublicClientApplicationBuilder`.

### <a name="javascript"></a>JavaScript

Ao obter tokens silenciosamente (com `acquireTokenSilent`) usando a MSAL.js, o aplicativo poderá receber erros quando um [desafio de declarações de acesso condicional](../azuread-dev/conditional-access-dev-guide.md), como a Política de MFA, for exigido por uma API que você está tentando acessar.

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

Ao chamar uma API que exige acesso condicional, você poderá receber um desafio de declarações no erro da API. Nesse caso, você pode passar as declarações retornadas no erro para o campo `claimsRequest` da classe `AuthenticationParameters.ts` de modo a atender à política apropriada. 

Consulte [Solicitar declarações adicionais](active-directory-optional-claims.md) para obter mais detalhes.

### <a name="msal-for-ios-and-macos"></a>MSAL para iOS e macOS

A MSAL para iOS e macOS permite que você solicite declarações específicas em cenários de aquisição de tokens interativos e silenciosos.

Para solicitar declarações personalizadas, especifique `claimsRequest` em `MSALSilentTokenParameters` ou `MSALInteractiveTokenParameters`.

Consulte [Solicitar declarações personalizadas usando a MSAL para iOS e macOS](request-custom-claims.md) para obter mais informações.

## <a name="retrying-after-errors-and-exceptions"></a>Tentar novamente após erros e exceções

Você deve implementar suas próprias políticas de repetição ao chamar a MSAL. A MSAL faz chamadas HTTP para o serviço do AAD, podendo ocorrer falhas ocasionais, como: a rede pode cair ou o servidor pode ficar sobrecarregado.  

### <a name="http-error-codes-500-600"></a>Códigos de erro HTTP 500-600

O MSAL.NET implementa um mecanismo de nova tentativa única simples para erros com códigos de erro HTTP 500-600.

### <a name="http-429"></a>HTTP 429

Quando o STS estiver sobrecarregado com muitas solicitações, ele retornará o erro HTTP 429 com uma dica sobre quanto tempo você deve aguardar até poder tentar novamente no campo de resposta `Retry-After`.

### <a name="net"></a>.NET

[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) é exibida `System.Net.Http.Headers.HttpResponseHeaders` como uma propriedade `namedHeaders`. É possível usar informações adicionais do código de erro para melhorar a confiabilidade dos aplicativos. No caso descrito, é possível usar o `RetryAfterproperty` (do tipo `RetryConditionHeaderValue`) e calcular quando tentar novamente.

Veja um exemplo de um aplicativo daemon usando o fluxo de credenciais do cliente. Você pode adaptá-lo a qualquer um dos métodos para adquirir um token.

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
