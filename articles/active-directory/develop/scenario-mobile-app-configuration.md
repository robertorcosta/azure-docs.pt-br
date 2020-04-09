---
title: Configure aplicativos móveis que chamam APIs web | Azure
titleSuffix: Microsoft identity platform
description: Saiba como criar um aplicativo móvel que chame APIs web (configuração de código do aplicativo)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/23/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 06475b53acf6c9161888e29723feab9cdc4336d5
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882719"
---
# <a name="configure-a-mobile-app-that-calls-web-apis"></a>Configure um aplicativo móvel que chama APIs da Web

Depois de criar seu aplicativo, você aprenderá a configurar o código usando os parâmetros de registro do aplicativo. Os aplicativos móveis apresentam algumas complexidades relacionadas ao encaixe em sua estrutura de criação.

## <a name="find-msal-support-for-mobile-apps"></a>Encontre suporte à MSAL para aplicativos móveis

Os seguintes tipos de Biblioteca de Autenticação da Microsoft (MSAL) suportam aplicativos móveis.

MSAL | Descrição
------------ | ----------
![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Usado para desenvolver aplicações portáteis. MSAL.NET suporta as seguintes plataformas para a construção de um aplicativo móvel: Universal Windows Platform (UWP), Xamarin.iOS e Xamarin.Android.
![MSAL.iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL.iOS | Usado para desenvolver aplicativos nativos para iOS usando Objective-C ou Swift.
![MSAL.Android](media/sample-v2-code/logo_android.png) <br/> MSAL.Android | Usado para desenvolver aplicativos android nativos em Java para Android.

## <a name="instantiate-the-application"></a>Instanciar o aplicativo

### <a name="android"></a>Android

Aplicativos móveis usam `PublicClientApplication` a classe. Veja como instancia-lo:

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

Aplicativos móveis no iOS precisam `MSALPublicClientApplication` instanciar a classe. Para instanciar a classe, use o seguinte código. 

```objc
NSError *msalError = nil;
     
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

[Outras propriedades MSALPublicClientApplicationConfig](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplicationConfig.html#/Configuration%20options) podem substituir a autoridade padrão, especificar um URI de redirecionamento ou alterar o comportamento do cache de token MSAL. 

### <a name="xamarin-or-uwp"></a>Xamarin ou UWP

Esta seção explica como instanciar o aplicativo para aplicativos Xamarin.iOS, Xamarin.Android e UWP.

#### <a name="instantiate-the-application"></a>Instanciar o aplicativo

Em Xamarin ou UWP, a maneira mais simples de instanciar o aplicativo é usando o seguinte código. Neste código, `ClientId` está o GUID do seu aplicativo registrado.

```csharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

Os `With<Parameter>` métodos adicionais definem o pai da UI, anulam a autoridade padrão, especificam um nome e uma versão do cliente para telemetria, especificam um URI redirecionado e especificam a fábrica HTTP para usar. A fábrica HTTP pode ser usada, por exemplo, para lidar com proxies e para especificar telemetria e registro. 

As seções a seguir fornecem mais informações sobre a instanciação do aplicativo.

##### <a name="specify-the-parent-ui-window-or-activity"></a>Especifique a ui, janela ou atividade do pai

No Android, você precisa passar a atividade dos pais antes de fazer a autenticação interativa. No iOS, quando você usa um corretor, `ViewController`você precisa passar . Da mesma forma no UWP, você pode querer passar na janela dos pais. Você passa quando você adquire o token. Mas quando você está criando o aplicativo, você também pode `UIParent`especificar um retorno de chamada como um delegado que retorna .

```csharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

No Android, recomendamos [`CurrentActivityPlugin`](https://github.com/jamesmontemagno/CurrentActivityPlugin)que você use . O código `PublicClientApplication` de construtor resultante se parece com este exemplo:

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="find-more-app-building-parameters"></a>Encontre mais parâmetros de construção de aplicativos

Para obter uma lista de todos `PublicClientApplicationBuilder`os métodos disponíveis, consulte a [lista Métodos](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods).

Para obter uma descrição de todas `PublicClientApplicationOptions`as opções expostas, consulte a [documentação de referência](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions).

## <a name="tasks-for-xamarin-ios"></a>Tarefas para Xamarin iOS

Se você usar MSAL.NET no IOS Xamarin, faça as seguintes tarefas.

* [Anular e implementar `OpenUrl` a função em`AppDelegate`](msal-net-xamarin-ios-considerations.md#implement-openurl)
* [Habilite grupos de chaveiros](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
* [Habilitar o compartilhamento de cache de token](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
* [Habilite o acesso a chaveiros](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

Para obter mais informações, consulte [considerações do IOS Xamarin](msal-net-xamarin-ios-considerations.md).

## <a name="tasks-for-msal-for-ios-and-macos"></a>Tarefas para MSAL para iOS e macOS

Essas tarefas são necessárias quando você usa MSAL para iOS e macOS:

* [Implementar `openURL` o retorno de chamada](#brokered-authentication-for-msal-for-ios-and-macos)
* [Habilite grupos de acesso a chaveiros](howto-v2-keychain-objc.md)
* [Personalizar navegadores e WebViews](customize-webviews.md)

## <a name="tasks-for-xamarinandroid"></a>Tarefas para Xamarin.Android

Se você usa Xamarin.Android, faça as seguintes tarefas:

- [Certifique-se de que o controle volte ao MSAL após o término da parte interativa do fluxo de autenticação](msal-net-xamarin-android-considerations.md#ensure-that-control-returns-to-msal)
- [Atualize o manifesto do Android](msal-net-xamarin-android-considerations.md#update-the-android-manifest)
- [Use a visualização web incorporada (opcional)](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [Solução de problemas conforme necessário](msal-net-xamarin-android-considerations.md#troubleshoot)

Para obter mais informações, consulte [considerações de Xamarin.Android](msal-net-xamarin-android-considerations.md).

Para obter considerações sobre os navegadores no Android, consulte [considerações específicas do Xamarin.Android com MSAL.NET](msal-net-system-browser-android-considerations.md).

#### <a name="tasks-for-uwp"></a>Tarefas para UWP

No UWP, você pode usar redes corporativas. As seções a seguir explicam as tarefas que você deve completar no cenário corporativo.

Para obter mais informações, consulte [considerações específicas da UWP com MSAL.NET](msal-net-uwp-considerations.md).

## <a name="configure-the-application-to-use-the-broker"></a>Configure o aplicativo para usar o corretor

No Android e iOS, os corretores habilitam:

- **Único login (SSO)**: Você pode usar SSO para dispositivos registrados no Azure Active Directory (Azure AD). Quando você usa SSO, seus usuários não precisam fazer login em cada aplicativo.
- **Identificação do dispositivo**: Esta configuração permite políticas de acesso condicional relacionadas a dispositivos AD do Azure. O processo de autenticação utiliza o certificado de dispositivo que foi criado quando o dispositivo foi juntado ao local de trabalho.
- **Verificação de identificação do aplicativo**: Quando um aplicativo chama o corretor, ele passa sua URL de redirecionamento. Então o corretor verifica.

### <a name="enable-the-broker-on-xamarin"></a>Habilite o corretor em Xamarin

Para habilitar o corretor em `WithBroker()` Xamarin, use `PublicClientApplicationBuilder.CreateApplication` o parâmetro quando chamar o método. Por padrão, `.WithBroker()` é definido como verdadeiro. 

Para habilitar a autenticação intermediada para Xamarin.iOS, siga os passos na [seção Xamarin.iOS](#enable-brokered-authentication-for-xamarin-ios) neste artigo.

### <a name="enable-the-broker-for-msal-for-android"></a>Habilite o corretor para MSAL para Android

Para obter informações sobre como ativar um corretor no Android, consulte [Autenticação Intermediada no Android](brokered-auth.md). 

### <a name="enable-the-broker-for-msal-for-ios-and-macos"></a>Habilite o corretor para MSAL para iOS e macOS

A autenticação intermediada é habilitada por padrão para cenários AD do Azure no MSAL para iOS e macOS. 

As seções a seguir fornecem instruções para configurar seu aplicativo para suporte de autenticação intermediada para MSAL para Xamarin.iOS ou MSAL para iOS e macOS. Nos dois conjuntos de instruções, algumas das etapas diferem.

### <a name="enable-brokered-authentication-for-xamarin-ios"></a>Habilitar autenticação intermediada para IOS Xamarin

Siga os passos nesta seção para permitir que seu aplicativo Xamarin.iOS converse com o aplicativo [Microsoft Authenticator.](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458)

#### <a name="step-1-enable-broker-support"></a>Passo 1: Habilite o suporte ao corretor

O suporte ao corretor é desativado por padrão. Você habilita para `PublicClientApplication` uma classe individual. Use `WithBroker()` o parâmetro quando `PublicClientApplication` criar `PublicClientApplicationBuilder`a classe através de . O `WithBroker()` parâmetro é definido como verdadeiro por padrão.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Passo 2: Atualize o AppDelegate para lidar com o retorno de chamada

Quando MSAL.NET liga para o corretor, o corretor liga de volta para sua solicitação. Ele chama de `AppDelegate.OpenUrl` volta usando o método. Como a MSAL aguarda a resposta do corretor, sua solicitação precisa cooperar para chamar MSAL.NET de volta. Você configura esse comportamento `AppDelegate.cs` atualizando o arquivo para substituir o método, como mostra o código a seguir.

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url,
                             string sourceApplication,
                             NSObject annotation)
{
 if (AuthenticationContinuationHelper.IsBrokerResponse(sourceApplication))
 {
  AuthenticationContinuationHelper.SetBrokerContinuationEventArgs(url);
  return true;
 }
 else if (!AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url))
 {
  return false;
 }
 return true;
}
```

Este método é invocado toda vez que o aplicativo é lançado. É uma oportunidade de processar a resposta do corretor e concluir o processo de autenticação que MSAL.NET iniciado.

#### <a name="step-3-set-a-uiviewcontroller"></a>Passo 3: Defina um UIViewController()

Para xamarin iOS, você normalmente não precisa definir uma janela de objeto. Mas, neste caso, você deve configurá-lo para que você possa enviar e receber respostas de um corretor. Para definir uma janela `AppDelegate.cs`de objeto, em , você definir um `ViewController`.

Para definir a janela do objeto, siga estas etapas:

1. Em `AppDelegate.cs`, `App.RootViewController` definir o `UIViewController()`para um novo . Esta configuração garante que a chamada `UIViewController`para o corretor inclua . Se não estiver definido corretamente, você pode obter este erro:

    `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker."`

1. Na `AcquireTokenInteractive` chamada, `.WithParentActivityOrWindow(App.RootViewController)`use. Passe na referência à janela do objeto que você usará. Aqui está um exemplo:

    Em `App.cs`:
    ```csharp
       public static object RootViewController { get; set; }
    ```
    Em `AppDelegate.cs`:
    ```csharp
       LoadApplication(new App());
       App.RootViewController = new UIViewController();
    ```
    Na `AcquireToken` chamada:
    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```
    
#### <a name="step-4-register-a-url-scheme"></a>Passo 4: Registre um esquema de URL

MSAL.NET usa URLs para invocar o corretor e, em seguida, retornar a resposta do corretor de volta ao seu aplicativo. Para terminar a ida e volta, registre `Info.plist` o esquema de URL do seu aplicativo no arquivo. 

Para registrar o esquema de URL do seu aplicativo, siga estas etapas:

1. `CFBundleURLSchemes` Prefixo `msauth`com . 
1. Adicione `CFBundleURLName` até o final. Siga este padrão: 

   `$"msauth.(BundleId)"`

   Aqui, `BundleId` identifica exclusivamente o seu dispositivo. Por exemplo, `BundleId` `yourcompany.xforms`se for, `msauth.com.yourcompany.xforms`seu esquema de URL é .
    
   > [!NOTE]
   > Esse esquema de URL se tornará parte do URI de redirecionamento que identifica exclusivamente seu aplicativo quando ele recebe a resposta do corretor.
    
   ```XML
    <key>CFBundleURLTypes</key>
       <array>
         <dict>
           <key>CFBundleTypeRole</key>
           <string>Editor</string>
           <key>CFBundleURLName</key>
           <string>com.yourcompany.xforms</string>
           <key>CFBundleURLSchemes</key>
           <array>
             <string>msauth.com.yourcompany.xforms</string>
           </array>
         </dict>
       </array>
   ```
    
#### <a name="step-5-add-to-the-lsapplicationqueriesschemes-section"></a>Passo 5: Adicionar à seção LSApplicationQueriesSchemes

A MSAL usa `–canOpenURL:` para verificar se o corretor está instalado no dispositivo. No iOS 9, a Apple bloqueou os esquemas que um aplicativo pode consultar.

Adicione `msauthv2` à `LSApplicationQueriesSchemes` seção `Info.plist` do arquivo, como no exemplo de código a seguir:

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-msal-for-ios-and-macos"></a>Autenticação intermediada para MSAL para iOS e macOS

A autenticação intermediada é habilitada por padrão para cenários Azure AD.

#### <a name="step-1-update-appdelegate-to-handle-the-callback"></a>Passo 1: Atualize o AppDelegate para lidar com o retorno de chamada

Quando o MSAL para iOS e macOS chama o corretor, `openURL` o corretor liga de volta para o seu aplicativo usando o método. Como a MSAL aguarda a resposta do corretor, sua solicitação precisa cooperar para chamar de volta a MSAL. Configure esse recurso `AppDelegate.m` atualizando o arquivo para substituir o método, como mostram os exemplos de código a seguir.

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url 
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

> [!NOTE]
> Se você `UISceneDelegate` adotou no iOS 13 ou posterior, coloque `scene:openURLContexts:` `UISceneDelegate` o retorno de chamada do MSAL no de seu lugar. O `handleMSALResponse:sourceApplication:` MSAL deve ser chamado apenas uma vez para cada URL.
>
> Para obter mais informações, consulte a [documentação](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)da Apple .

#### <a name="step-2-register-a-url-scheme"></a>Passo 2: Registre um esquema de URL

MSAL para iOS e macOS usa URLs para invocar o corretor e, em seguida, retornar a resposta do corretor para o seu aplicativo. Para terminar a ida e volta, registre `Info.plist` um esquema de URL para o seu aplicativo no arquivo.

Para registrar um esquema para o seu aplicativo: 

1. Prefixe seu esquema `msauth`de URL personalizado com . 

1. Adicione seu identificador de pacote ao final do seu esquema. Siga este padrão: 

   `$"msauth.(BundleId)"`

   Aqui, `BundleId` identifica exclusivamente o seu dispositivo. Por exemplo, `BundleId` `yourcompany.xforms`se for, `msauth.com.yourcompany.xforms`seu esquema de URL é .
  
   > [!NOTE]
   > Esse esquema de URL se tornará parte do URI de redirecionamento que identifica exclusivamente seu aplicativo quando ele recebe a resposta do corretor. Certifique-se de que o `msauth.(BundleId)://auth` URI redirecionante no formato esteja registrado para sua aplicação no [portal Azure](https://portal.azure.com).
  
   ```XML
   <key>CFBundleURLTypes</key>
   <array>
       <dict>
           <key>CFBundleURLSchemes</key>
           <array>
               <string>msauth.[BUNDLE_ID]</string>
           </array>
       </dict>
   </array>
   ```

#### <a name="step-3-add-lsapplicationqueriesschemes"></a>Passo 3: Adicionar esquemas de consultas de aplicativos lS

Adicione `LSApplicationQueriesSchemes` para permitir chamadas ao aplicativo Microsoft Authenticator, se estiver instalado.

> [!NOTE]
> O `msauthv3` esquema é necessário quando seu aplicativo é compilado usando Xcode 11 e posterior. 

Aqui está um exemplo de `LSApplicationQueriesSchemes`como adicionar:

```XML 
<key>LSApplicationQueriesSchemes</key>
<array>
  <string>msauthv2</string>
  <string>msauthv3</string>
</array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>Autenticação intermediada para Xamarin.Android

MSAL.NET não suporta corretores para Android.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aquisição de um token](scenario-mobile-acquire-token.md)
