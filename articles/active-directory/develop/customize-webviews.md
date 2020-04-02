---
title: Personalizar navegadores & WebViews (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Saiba como personalizar a experiência do navegador MSAL iOS/macOS para fazer login nos usuários.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 8552fc8555207c5b6ca59bbd0da0fdebaae2e87b
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546101"
---
# <a name="how-to-customize-browsers-and-webviews-for-iosmacos"></a>Como: Personalizar navegadores e WebViews para iOS/macOS

Um navegador da Web é necessário para autenticação interativa. No iOS e macOS 10.15+, a Microsoft Authentication Library (MSAL) usa o navegador web do sistema por padrão (que pode aparecer no topo do seu aplicativo) para fazer autenticação interativa para fazer login nos usuários. O uso do navegador do sistema tem a vantagem de compartilhar o estado de SSO (Single Sign On) com outros aplicativos e com aplicações web.

Você pode alterar a experiência personalizando a configuração para outras opções para exibir conteúdo da Web, tais como:

Somente para iOS:

- [Sessão de Autenticação SF](https://developer.apple.com/documentation/safariservices/sfauthenticationsession?language=objc) 
- [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?language=objc)

Para iOS e macOS:

- [Sessão de autenticação asweb](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)
- [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview?language=objc).

O MSAL para macOS só suporta em versões `WKWebView` mais antigas do sistema operacional. `ASWebAuthenticationSession`só é suportado no macOS 10.15 ou superior. 

## <a name="system-browsers"></a>Navegadores do sistema

Para `ASWebAuthenticationSession`iOS, `SFAuthenticationSession`e `SFSafariViewController` são considerados navegadores de sistema. Para macOS, `ASWebAuthenticationSession` só está disponível. Em geral, os navegadores do sistema compartilham cookies e outros dados do site com o aplicativo do navegador Safari.

Por padrão, o MSAL detectará dinamicamente a versão do iOS e selecionará o navegador do sistema recomendado disponível nessa versão. No iOS 12+ `ASWebAuthenticationSession`será . 

### <a name="default-configuration-for-ios"></a>Configuração padrão para iOS

| Versão | Navegador da Web |
|:-------------:|:-------------:|
| iOS 12+ | Sessão de autenticação asweb |
| iOS 11 | Sessão de Autenticação SF |
| iOS 10 | SFSafariViewController |

### <a name="default-configuration-for-macos"></a>Configuração padrão para macOS

| Versão | Navegador da Web |
|:-------------:|:-------------:|
| macOS 10.15+ | Sessão de autenticação asweb |
| outras versões | WKWebView |

Os desenvolvedores também podem selecionar um navegador de sistema diferente para aplicativos MSAL:

- `SFAuthenticationSession`é a versão iOS `ASWebAuthenticationSession`11 de .
- `SFSafariViewController`é um propósito mais geral e fornece uma interface para navegar na Web e pode ser usado para fins de login também. No iOS 9 e 10, cookies e outros dados do site são compartilhados com o Safari — mas não no iOS 11 e posteriores.

## <a name="in-app-browser"></a>Navegador no aplicativo

[WKWebView](https://developer.apple.com/documentation/webkit/wkwebview) é um navegador no aplicativo que exibe conteúdo da Web. Ele não compartilha cookies ou dados de sites com outras instâncias do **WKWebView** ou com o navegador Safari. WKWebView é um navegador multiplataforma que está disponível para iOS e macOS.

## <a name="cookie-sharing-and-single-sign-on-sso-implications"></a>Compartilhamento de cookies e implicações de participação única (SSO)

O navegador que você usa impacta a experiência sso por causa de como eles compartilham cookies. As tabelas a seguir resumem as experiências do SSO por navegador.

| Tecnologia    | Tipo de navegador  | Disponibilidade do iOS | disponibilidade de macOS | Compartilha cookies e outros dados  | Disponibilidade do MSAL | SSO |
|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|-------------:|
| [Sessão de autenticação asweb](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession) | Sistema | iOS12 e acima | macOS 10.15 e até | Sim | iOS e macOS 10.15+ | c/ Instâncias de safari
| [Sessão de Autenticação SF](https://developer.apple.com/documentation/safariservices/sfauthenticationsession) | Sistema | iOS11 e acima | N/D | Sim | Somente iOS |  c/ Instâncias de safari
| [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller) | Sistema | iOS11 e acima | N/D | Não | Somente iOS | Não**
| **SFSafariViewController** | Sistema | iOS10 | N/D | Sim | Somente iOS |  c/ Instâncias de safari
| **WKWebView**  | No aplicativo | iOS8 e acima | macOS 10.10 e acima | Não | iOS e macOS | Não**

** Para que o SSO funcione, os tokens precisam ser compartilhados entre aplicativos. Isso requer um cache de token ou um aplicativo de corretora, como o Microsoft Authenticator para iOS.

## <a name="change-the-default-browser-for-the-request"></a>Alterar o navegador padrão para a solicitação

Você pode usar um navegador no aplicativo ou um navegador de sistema específico, `MSALWebviewParameters`dependendo dos seus requisitos de UX, alterando a seguinte propriedade em :

```objc
@property (nonatomic) MSALWebviewType webviewType;
```

## <a name="change-per-interactive-request"></a>Alterar por solicitação interativa

Cada solicitação pode ser configurada para substituir `MSALInteractiveTokenParameters.webviewParameters.webviewType` o navegador padrão `acquireTokenWithParameters:completionBlock:` alterando a propriedade antes de passá-la para a API.

Além disso, o MSAL suporta `WKWebView` passar `MSALInteractiveTokenParameters.webviewParameters.customWebView` em um costume, definindo a propriedade.

Por exemplo: 

Objective-C
```objc
UIViewController *myParentController = ...;
WKWebView *myCustomWebView = ...;
MSALWebviewParameters *webViewParameters = [[MSALWebviewParameters alloc] initWithParentViewController:myParentController];
webViewParameters.webviewType = MSALWebviewTypeWKWebView;
webViewParameters.customWebview = myCustomWebView;
MSALInteractiveTokenParameters *interactiveParameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"myscope"] webviewParameters:webViewParameters];
    
[app acquireTokenWithParameters:interactiveParameters completionBlock:completionBlock];
```
Swift
```swift
let myParentController: UIViewController = ...
let myCustomWebView: WKWebView = ...
let webViewParameters = MSALWebviewParameters(parentViewController: myParentController)
webViewParameters.webviewType = MSALWebviewType.wkWebView
webViewParameters.customWebview = myCustomWebView
let interactiveParameters = MSALInteractiveTokenParameters(scopes: ["myscope"], webviewParameters: webViewParameters)

app.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
```

Se você usar uma webview personalizada, as notificações serão usadas para indicar o status do conteúdo da Web que está sendo exibido, tais como:

```objc
/*! Fired at the start of a resource load in the webview. The URL of the load, if available, will be in the @"url" key in the userInfo dictionary */
extern NSString *MSALWebAuthDidStartLoadNotification;

/*! Fired when a resource finishes loading in the webview. */
extern NSString *MSALWebAuthDidFinishLoadNotification;

/*! Fired when web authentication fails due to reasons originating from the network. Look at the @"error" key in the userInfo dictionary for more details.*/
extern NSString *MSALWebAuthDidFailNotification;

/*! Fired when authentication finishes */
extern NSString *MSALWebAuthDidCompleteNotification;

/*! Fired before ADAL invokes the broker app */
extern NSString *MSALWebAuthWillSwitchToBrokerApp;
```

### <a name="options"></a>Opções

Todos os tipos de navegador da Web suportados pela MSAL são declarados no [enum MSALWebviewType](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALDefinitions.h#L47)

```objc
typedef NS_ENUM(NSInteger, MSALWebviewType)
{
    /**
     For iOS 11 and up, uses AuthenticationSession (ASWebAuthenticationSession or SFAuthenticationSession).
     For older versions, with AuthenticationSession not being available, uses SafariViewController.
     For macOS 10.15 and above uses ASWebAuthenticationSession
     For older macOS versions uses WKWebView
     */
    MSALWebviewTypeDefault,
    
    /** Use ASWebAuthenticationSession where available.
     On older iOS versions uses SFAuthenticationSession
     Doesn't allow any other webview type, so if either of these are not present, fails the request*/
    MSALWebviewTypeAuthenticationSession,
    
#if TARGET_OS_IPHONE
    
    /** Use SFSafariViewController for all versions. */
    MSALWebviewTypeSafariViewController,
    
#endif
    /** Use WKWebView */
    MSALWebviewTypeWKWebView,
};
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os [Fluxos de autenticação e cenários de aplicativos](authentication-flows-app-scenarios.md)
