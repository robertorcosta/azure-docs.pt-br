---
title: Configure SSO no macOS e iOS
titleSuffix: Microsoft identity platform
description: Saiba como configurar o single sign on (SSO) no macOS e iOS.
services: active-directory
documentationcenter: dev-center-name
author: mmacy
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/03/2020
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 91a55520b37c549c8f1d94ba6cf08ecd24db85b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262445"
---
# <a name="how-to-configure-sso-on-macos-and-ios"></a>Como: Configurar sSO no macOS e iOS

A Microsoft Authentication Library (MSAL) para macOS e iOS suporta O SSO (Single Sign-on) entre aplicativos macOS/iOS e navegadores. Este artigo abrange os seguintes cenários do SSO:

- [SSO silencioso entre vários aplicativos](#silent-sso-between-apps)

Este tipo de SSO funciona entre vários aplicativos distribuídos pelo mesmo Desenvolvedor Apple. Ele fornece SSO silencioso (ou seja, o usuário não é solicitado para credenciais) lendo tokens de atualização escritos por outros aplicativos do chaveiro e trocando-os por tokens de acesso silenciosamente.  

- [SSO através do corretor de autenticação](#sso-through-authentication-broker-on-ios)

> [!IMPORTANT]
> Esse fluxo não está disponível no macOS.

A Microsoft fornece aplicativos, chamados de corretores, que permitem o SSO entre aplicativos de diferentes fornecedores, desde que o dispositivo móvel esteja registrado no Azure Active Directory (AAD). Este tipo de SSO requer que um aplicativo de corretor seja instalado no dispositivo do usuário.

- **SSO entre MSAL e Safari**

O SSO é alcançado através da classe [ASWebAuthenticationSession.](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc) Ele usa o estado de login existente de outros aplicativos e do navegador Safari. Não se limita a aplicativos distribuídos pelo mesmo Apple Developer, mas requer alguma interação do usuário.

Se você usar a visualização web padrão em seu aplicativo para fazer login nos usuários, você receberá SSO automático entre aplicativos baseados em MSAL e Safari. Para saber mais sobre as visualizações da Web que o MSAL suporta, visite [Personalizar navegadores e WebViews](customize-webviews.md).

> [!IMPORTANT]
> Este tipo de SSO não está disponível no macOS. O MSAL no macOS só suporta o WKWebView, que não tem suporte ao SSO com o Safari. 

- **SSO silencioso entre aplicativos MacOS/iOS MSAL e MSAL**

O MSAL Objective-C suporta migração e SSO com aplicativos baseados em Objetivo ADAL. Os aplicativos devem ser distribuídos pelo mesmo Desenvolvedor Apple.

Consulte [sso entre aplicativos ADAL e MSAL no macOS e iOS](sso-between-adal-msal-apps-macos-ios.md) para obter instruções para SSO cross-app entre aplicativos baseados em ADAL e MSAL.

## <a name="silent-sso-between-apps"></a>SSO silencioso entre aplicativos

A MSAL suporta o compartilhamento de SSO através de grupos de acesso a chaveiros iOS.

Para habilitar o SSO em seus aplicativos, você precisará fazer as seguintes etapas, que são explicadas com mais detalhes abaixo:

1. Certifique-se de que todos os seus aplicativos usem o mesmo ID do cliente ou id do aplicativo.
1. Certifique-se de que todos os aplicativos compartilhem o mesmo certificado de assinatura da Apple para que você possa compartilhar os conjuntos de chaves.
1. Solicite o mesmo direito de conjunto de chaves para cada um dos seus aplicativos.
1. Informe os SDKs do MSAL sobre o chaveiro compartilhado que você quer que usemos se for diferente do padrão.

### <a name="use-the-same-client-id-and-application-id"></a>Use o mesmo ID do cliente e id de aplicativo

Para que a plataforma de identidade da Microsoft saiba quais aplicativos podem compartilhar tokens, esses aplicativos precisam compartilhar o mesmo ID do Cliente ou ID do aplicativo. Esse é o identificador exclusivo fornecido para você quando você registrou seu primeiro aplicativo no portal.

A maneira como a plataforma de identidade da Microsoft informa aos aplicativos que usam o mesmo ID de aplicativo à parte é por suas **URIs redirecionadas**. Cada aplicativo pode ter vários URIs de Redirecionamento registrados no portal de integração. Cada aplicativo em seu pacote terá um URI de redirecionamento diferente. Por exemplo: 

URI de Redirecionamento do App1: `msauth.com.contoso.mytestapp1://auth`  
URI de Redirecionamento do App2: `msauth.com.contoso.mytestapp2://auth`  
URI de Redirecionamento do App3: `msauth.com.contoso.mytestapp3://auth`  

> [!IMPORTANT]
> O formato de uris de redirecionamento deve ser compatível com o formato msal suporta, que está documentado nos requisitos de [formato URI de redirecionamento MSAL](redirect-uris-ios.md#msal-redirect-uri-format-requirements).

### <a name="setup-keychain-sharing-between-applications"></a>Compartilhamento de chaveiros de configuração entre aplicativos

Consulte o artigo ['Recursos de adição'](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html) da Apple para permitir o compartilhamento de chaveiros. O importante é que você decida o que quer que seu chaveiro seja chamado e adicione esse recurso a todos os seus aplicativos que estarão envolvidos no SSO.

Quando você tiver os direitos configurados corretamente, `entitlements.plist` você verá um arquivo no diretório do projeto que contém algo como este exemplo:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>keychain-access-groups</key>
    <array>
        <string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
        <string>$(AppIdentifierPrefix)com.myapp.mycache</string>
    </array>
</dict>
</plist>
```

#### <a name="add-a-new-keychain-group"></a>Adicionar um novo grupo de chaveiros

Adicione um novo grupo de chaveiros ao seu projeto **Recursos**. O grupo chaveiro deve ser:
* `com.microsoft.adalcache`no iOS 
* `com.microsoft.identity.universalstorage`no macOS.

![exemplo de chaveiro](media/single-sign-on-macos-ios/keychain-example.png)

Para obter mais informações, consulte [grupos de chaveiros](howto-v2-keychain-objc.md).

## <a name="configure-the-application-object"></a>Configure o objeto do aplicativo

Uma vez que você tenha o direito do chaveiro ativado em cada um de `MSALPublicClientApplication` seus aplicativos e esteja pronto para usar o SSO, configure com o seu grupo de acesso de chaveiro como no exemplo a seguir:

Objective-C:

```objc
NSError *error = nil;
MSALPublicClientApplicationConfig *configuration = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<my-client-id>"];
configuration.cacheConfig.keychainSharingGroup = @"my.keychain.group";
    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:configuration error:&error];
```

Swift:

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<my-client-id>")
config.cacheConfig.keychainSharingGroup = "my.keychain.group"

do {
   let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application
} catch let error as NSError {
  // handle error here
}
```

> [!WARNING]
> Quando você compartilha um chaveiro em seus aplicativos, qualquer aplicativo pode excluir usuários ou até mesmo todos os tokens em seu aplicativo.
> Isso é particularmente impactante se você tiver aplicativos que dependem de tokens para fazer o trabalho de fundo.
> Compartilhar um chaveiro significa que você deve ter muito cuidado quando seu aplicativo usa o SDK de identidade da Microsoft remover operações.

É isso! O SDK de identidade da Microsoft agora compartilhará credenciais em todos os seus aplicativos. A lista de contas também será compartilhada entre as instâncias do aplicativo.

## <a name="sso-through-authentication-broker-on-ios"></a>SSO através de corretor de autenticação no iOS

A MSAL oferece suporte para autenticação intermediada com o Microsoft Authenticator. O Microsoft Authenticator fornece SSO para dispositivos registrados em AAD e também ajuda seu aplicativo a seguir as políticas de Acesso Condicional.

As etapas a seguir são como você habilita o SSO usando um corretor de autenticação para o seu aplicativo:

1. Registre um formato URI compatível com corretor para o aplicativo no Info.plist do seu aplicativo. O formato URI de `msauth.<app.bundle.id>://auth`redirecionamento compatível com corretor é . Substitua '<app.bundle.id>'' pelo ID do pacote do seu aplicativo. Por exemplo: 

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

1. Adicione os seguintes esquemas à Info.plist do seu aplicativo em `LSApplicationQueriesSchemes`:

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

1. Adicione o seguinte `AppDelegate.m` ao seu arquivo para lidar com retornos de chamadas:

    Objective-C:
    
    ```objc
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options
    {
        return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
    }
    ```
    
    Swift:
    
    ```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }
    ```
    
**Se você estiver usando xcode 11,** você deve `SceneDelegate` colocar a chamada msal de volta no arquivo em vez disso.
Se você der suporte a UISceneDelegate e UIApplicationDelegate para compatibilidade com o iOS mais antigo, o retorno de chamada da MSAL precisará ser colocado nos dois arquivos.

Objective-C:

```objc
 - (void)scene:(UIScene *)scene openURLContexts:(NSSet<UIOpenURLContext *> *)URLContexts
 {
     UIOpenURLContext *context = URLContexts.anyObject;
     NSURL *url = context.URL;
     NSString *sourceApplication = context.options.sourceApplication;
     
     [MSALPublicClientApplication handleMSALResponse:url sourceApplication:sourceApplication];
 }
```

Swift:

```swift
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
        
        guard let urlContext = URLContexts.first else {
            return
        }
        
        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication
        
        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os [Fluxos de autenticação e cenários de aplicativos](authentication-flows-app-scenarios.md)