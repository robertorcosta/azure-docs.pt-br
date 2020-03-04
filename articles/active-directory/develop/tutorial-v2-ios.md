---
title: Tutorial do MSAL para iOS e macOS – plataforma de identidade da Microsoft | Azure
description: Saiba como aplicativos iOS e macOS (Swift) podem chamar uma API que exige tokens de acesso usando a plataforma de identidade da Microsoft
services: active-directory
documentationcenter: dev-center-name
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 08/30/2019
ms.author: jmprieur
ms.reviewer: oldalton
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 7704779ff605e28358f6f82c22ccda84d371dba9
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2020
ms.locfileid: "77084378"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-ios-or-macos-app"></a>Conectar usuários e chamar o Microsoft Graph de um aplicativo iOS ou macOS

Neste tutorial, você aprenderá a integrar um aplicativo iOS ou macOS à plataforma de identidade da Microsoft. O aplicativo conectará um usuário, obterá um token de acesso para chamar a API do Microsoft Graph e fará uma solicitação para a API do Microsoft Graph.  

Após concluir este guia, seu aplicativo aceitará conexões de contas Microsoft pessoais (incluindo outlook.com, live.com e outras) e contas corporativas ou de estudante de qualquer empresa ou organização que utilize o Azure Active Directory.

## <a name="how-this-tutorial-works"></a>Como este tutorial funciona

![Mostra como funciona o aplicativo de exemplo gerado por este tutorial](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

O aplicativo deste tutorial conectará usuários e obterá dados no nome deles.  Esses dados serão acessados por meio de uma API protegida (API do Microsoft Graph, nesse caso) que exige autorização e é protegida pela plataforma de identidade da Microsoft.

Mais especificamente:

* Seu aplicativo conectará o usuário por meio de um navegador ou do Microsoft Authenticator.
* O usuário final aceitará as permissões solicitadas por seu aplicativo.
* Será emitido um token de acesso para seu aplicativo para a API do Microsoft Graph.
* O token de acesso será incluído na solicitação HTTP para API Web.
* Processe a resposta do Microsoft Graph.

Esta amostra usa a MSAL (Biblioteca de Autenticação da Microsoft) para implementar a Autenticação. A MSAL automaticamente renovará tokens, fornecerá o SSO (logon único) entre outros aplicativos no dispositivo e ajudará a gerenciar as contas.

Este tutorial é aplicável a aplicativos iOS e macOS. Observe que algumas etapas são diferentes entre essas duas plataformas. 

## <a name="prerequisites"></a>Prerequisites

- O Xcode versão 10.x ou superior é necessário para criar o aplicativo neste guia. Você pode fazer baixar o XCode no [site do iTunes](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "URL de download do XCode").
- Biblioteca de Autenticação da Microsoft ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)). Você pode usar um gerenciador de dependência ou adicionar a biblioteca manualmente. As instruções abaixo mostram como fazer isso.

Este tutorial irá criar um novo projeto. Se, em vez disso, você quiser baixar o tutorial concluído, baixe o código:
- [Código de exemplo do iOS](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)
- [Código de exemplo do macOS](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/archive/master.zip)

## <a name="create-a-new-project"></a>Criar um novo projeto

1. Abra o Xcode e selecione **Crie um novo projeto Xcode**.
2. Para aplicativos iOS, selecione **iOS** > **Aplicativo de Exibição Única** e selecione **Avançar**.
3. Para aplicativos macOS, selecione **macOS** > **Cocoa App** e selecione **Avançar**.
4. Forneça um nome de produto.
5. Defina a **Linguagem** como **Swift** e selecione **Avançar**.
6. Selecione uma pasta para criar seu aplicativo e clique em **Criar**.

## <a name="register-your-application"></a>Registre seu aplicativo

1. Vá para o [Portal do Azure](https://aka.ms/MobileAppReg)
2. Abra a folha [Registros de aplicativo](https://ms.portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) e clique em **+Novo registro**.
3. Insira um **Nome** para seu aplicativo e, em seguida, sem definir um URI de Redirecionamento, clique em **Registrar**.
4. Na seção **Gerenciar** do painel exibido, selecione **Autenticação**.

5. Clique em **Experimentar a nova experiência** ao lado da parte superior da tela para abrir a nova experiência de registro de aplicativo e, em seguida, clique em **+ Novo registro** >  **+ Adicionar uma plataforma** > **iOS**.
    - Insira a ID do Pacote do seu projeto. Se você baixou o código, ele é `com.microsoft.identitysample.MSALiOS`. Se estiver criando seu próprio projeto, selecione seu projeto no Xcode e abra a guia **Geral**. O identificador do pacote será exibido na seção **Identidade**.
    - Observe que, para macOS, você também deve usar a experiência do iOS. 
6. Clique em `Configure` e salve a **Configuração da MSAL** exibida na página **Configuração do iOS** para que você possa inseri-la quando configurar o aplicativo mais tarde.  Clique em **Concluído**.

## <a name="add-msal"></a>Adicionar MSAL

Escolha uma das seguintes maneiras de instalar a biblioteca MSAL em seu aplicativo:

### <a name="cocoapods"></a>CocoaPods

1. Se estiver usando o [CocoaPods](https://cocoapods.org/), instale `MSAL` primeiro criando um arquivo vazio chamado `podfile` na mesma pasta do arquivo `.xcodeproj` do projeto. Adicione o seguinte a `podfile`:

   ```
   use_frameworks!
   
   target '<your-target-here>' do
      pod 'MSAL'
   end
   ```

2. Substitua `<your-target-here>` pelo nome do projeto.
3. Em uma janela do terminal, navegue até a pasta que contém `podfile` que você criou e execute `pod install` para instalar a biblioteca MSAL.
4. Feche o Xcode e abra `<your project name>.xcworkspace` para recarregar o projeto no Xcode.

### <a name="carthage"></a>Carthage

Se estiver usando o [Carthage](https://github.com/Carthage/Carthage), instale `MSAL` adicionando-o ao `Cartfile`:

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

Em uma janela de terminal, no mesmo diretório que o `Cartfile` atualizado, execute o seguinte comando para que o Carthage atualize as dependências em seu projeto.

iOS:

```bash
carthage update --platform iOS
```

macOS:

```bash
carthage update --platform macOS
```

### <a name="manually"></a>Manualmente

Você também pode usar o Submódulo Git ou conferir a última versão e usá-la como uma estrutura em seu aplicativo.

## <a name="add-your-app-registration"></a>Adicionar o registro do seu aplicativo

Em seguida, adicionaremos o registro de aplicativo ao código. 

Primeiro, adicione a seguinte instrução de importação à parte superior dos arquivos `ViewController.swift` e `AppDelegate.swift`:

```swift
import MSAL
```

Em seguida, adicione o seguinte código a `ViewController.swift` antes de `viewDidLoad()`:

```swift
let kClientID = "Your_Application_Id_Here"

// Additional variables for Auth and Graph API
let kGraphURI = "https://graph.microsoft.com/v1.0/me/" // the Microsoft Graph endpoint
let kScopes: [String] = ["https://graph.microsoft.com/user.read"] // request permission to read the profile of the signed-in user
let kAuthority = "https://login.microsoftonline.com/common" // this authority allows a personal Microsoft account and a work or school account in any organization’s Azure AD tenant to sign in
var accessToken = String()
var applicationContext : MSALPublicClientApplication?
var webViewParameters : MSALWebviewParameters?
```

O único valor que você precisa modificar acima é o valor atribuído a `kClientID` para que ele seja a [ID do Aplicativo](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#application-id-client-id). Esse valor faz parte dos dados de Configuração da MSAL que você salvou durante a etapa no início deste tutorial para registrar o aplicativo no portal do Azure.

## <a name="for-ios-only-configure-url-schemes"></a>Somente para o iOS, Configure os esquemas de URL

Nesta etapa, você registrará `CFBundleURLSchemes` para que o usuário possa ser redirecionado novamente para o aplicativo após a entrada. A propósito, `LSApplicationQueriesSchemes` também permite que seu aplicativo faça uso do Microsoft Authenticator.

No Xcode, abra `Info.plist` como um arquivo de código-fonte e adicione o código a seguir dentro da seção `<dict>`. Substitua `[BUNDLE_ID]` pelo valor usado no portal do Azure que, se você baixou o código, é `com.microsoft.identitysample.MSALiOS`. Se estiver criando seu próprio projeto, selecione seu projeto no Xcode e abra a guia **Geral**. O identificador do pacote será exibido na seção **Identidade**.

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msauth.[BUNDLE_ID]</string>
        </array>
    </dict>
</array>
<key>LSApplicationQueriesSchemes</key>
<array>
    <string>msauthv2</string>
    <string>msauthv3</string>
</array>
```

## <a name="for-macos-only-configure-app-sandbox"></a>Somente para macOS, configure a área restrita do aplicativo

1. Acesse as Configurações do Projeto do Xcode > **guia Funcionalidades** > **Área Restrita do Aplicativo**
2. Marque a caixa de seleção **Conexões de Saída (Cliente)** . 

## <a name="create-your-apps-ui"></a>Criar a interface do usuário do seu aplicativo

Agora crie uma interface do usuário que inclui um botão para chamar a API do Microsoft Graph, outra para sair e uma exibição de texto para ver uma saída adicionando o seguinte código à classe `ViewController`:

### <a name="ios-ui"></a>Interface do usuário do iOS

```swift
var loggingText: UITextView!
var signOutButton: UIButton!
var callGraphButton: UIButton!

func initUI() {
        // Add call Graph button
        callGraphButton  = UIButton()
        callGraphButton.translatesAutoresizingMaskIntoConstraints = false
        callGraphButton.setTitle("Call Microsoft Graph API", for: .normal)
        callGraphButton.setTitleColor(.blue, for: .normal)
        callGraphButton.addTarget(self, action: #selector(callGraphAPI(_:)), for: .touchUpInside)
        self.view.addSubview(callGraphButton)
        
        callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 50.0).isActive = true
        callGraphButton.widthAnchor.constraint(equalToConstant: 300.0).isActive = true
        callGraphButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true
        
        // Add sign out button
        signOutButton = UIButton()
        signOutButton.translatesAutoresizingMaskIntoConstraints = false
        signOutButton.setTitle("Sign Out", for: .normal)
        signOutButton.setTitleColor(.blue, for: .normal)
        signOutButton.setTitleColor(.gray, for: .disabled)
        signOutButton.addTarget(self, action: #selector(signOut(_:)), for: .touchUpInside)
        self.view.addSubview(signOutButton)
        
        signOutButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        signOutButton.topAnchor.constraint(equalTo: callGraphButton.bottomAnchor, constant: 10.0).isActive = true
        signOutButton.widthAnchor.constraint(equalToConstant: 150.0).isActive = true
        signOutButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true
        signOutButton.isEnabled = false
        
        // Add logging textfield
        loggingText = UITextView()
        loggingText.isUserInteractionEnabled = false
        loggingText.translatesAutoresizingMaskIntoConstraints = false
        
        self.view.addSubview(loggingText)
        
        loggingText.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
        loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
        loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: 10.0).isActive = true
        loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: 10.0).isActive = true
    }
```

### <a name="macos-ui"></a>Interface do usuário do macOS

```swift

var callGraphButton: NSButton!
var loggingText: NSTextView!
var signOutButton: NSButton!

func initUI() {
        // Add call Graph button
        callGraphButton  = NSButton()
        callGraphButton.translatesAutoresizingMaskIntoConstraints = false
        callGraphButton.title = "Call Microsoft Graph API"
        callGraphButton.target = self
        callGraphButton.action = #selector(callGraphAPI(_:))
        callGraphButton.bezelStyle = .rounded
        self.view.addSubview(callGraphButton)
        
        callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 30.0).isActive = true
        callGraphButton.heightAnchor.constraint(equalToConstant: 34.0).isActive = true
        
        // Add sign out button
        signOutButton = NSButton()
        signOutButton.translatesAutoresizingMaskIntoConstraints = false
        signOutButton.title = "Sign Out"
        signOutButton.target = self
        signOutButton.action = #selector(signOut(_:))
        signOutButton.bezelStyle = .texturedRounded
        self.view.addSubview(signOutButton)
        
        signOutButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        signOutButton.topAnchor.constraint(equalTo: callGraphButton.bottomAnchor, constant: 10.0).isActive = true
        signOutButton.heightAnchor.constraint(equalToConstant: 34.0).isActive = true
        signOutButton.isEnabled = false
        
        // Add logging textfield
        loggingText = NSTextView()
        loggingText.translatesAutoresizingMaskIntoConstraints = false
        
        self.view.addSubview(loggingText)
        
        loggingText.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
        loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
        loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: -10.0).isActive = true
        loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: -10.0).isActive = true
        loggingText.widthAnchor.constraint(equalToConstant: 500.0).isActive = true
        loggingText.heightAnchor.constraint(equalToConstant: 300.0).isActive = true
    }
```

Em seguida, também dentro da classe `ViewController`, substitua o método `viewDidLoad()` por:

```swift
    override func viewDidLoad() {
        super.viewDidLoad()
        initUI()
        do {
            try self.initMSAL()
        } catch let error {
            self.updateLogging(text: "Unable to create Application Context \(error)")
        }
    }
```

## <a name="use-msal"></a>Usar a MSAL

### <a name="initialize-msal"></a>Inicializar a MSAL

Adicione o seguinte método `initMSAL` à classe `ViewController`:

```swift
    func initMSAL() throws {
        
        guard let authorityURL = URL(string: kAuthority) else {
            self.updateLogging(text: "Unable to create authority URL")
            return
        }
        
        let authority = try MSALAADAuthority(url: authorityURL)
        
        let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
        self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
        self.initWebViewParams()
    }
```

Adicione o seguinte após o método `initMSAL` para a classe `ViewController`.

### <a name="ios-code"></a>Código do iOS:

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters(parentViewController: self)
    }
```

### <a name="macos-code"></a>Código do macOS:

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters()
        self.webViewParameters?.webviewType = .wkWebView
    }
```

### <a name="for-ios-only-handle-the-sign-in-callback"></a>Somente para o iOS, manipule o retorno de chamada de entrada

Abra o arquivo `AppDelegate.swift` . Para manipular o retorno de chamada após a entrada, adicione `MSALPublicClientApplication.handleMSALResponse` à classe `appDelegate` desta forma:

```swift
// Inside AppDelegate...
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}

```

**Se estiver usando o Xcode 11**, você deverá colocar o retorno de chamada da MSAL no `SceneDelegate.swift` em vez disso.
Se você der suporte a UISceneDelegate e UIApplicationDelegate para compatibilidade com o iOS mais antigo, o retorno de chamada da MSAL precisará ser colocado nos dois arquivos.

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

#### <a name="acquire-tokens"></a>Adquirir tokens

Agora, podemos implementar a lógica de processamento da interface do usuário do aplicativo e obter tokens de maneira interativa por meio da MSAL.

A MSAL expõe dois métodos principais para obter tokens: `acquireTokenSilently()` e `acquireTokenInteractively()`: 

- `acquireTokenSilently()` tenta conectar um usuário e obter tokens sem nenhuma interação do usuário, desde que uma conta esteja presente.

- `acquireTokenInteractively()` sempre mostra a interface do usuário ao tentar conectar o usuário. Ele pode usar os cookies de sessão no navegador ou uma conta no Microsoft Authenticator para fornecer uma experiência de SSO interativo.

Adicione o código a seguir à classe `ViewController`:

```swift
    @objc func callGraphAPI(_ sender: AnyObject) {
        
        guard let currentAccount = self.currentAccount() else {
            // We check to see if we have a current logged in account.
            // If we don't, then we need to sign someone in.
            acquireTokenInteractively()
            return
        }
        
        acquireTokenSilently(currentAccount)
    }

    func currentAccount() -> MSALAccount? {
        
        guard let applicationContext = self.applicationContext else { return nil }
        
        // We retrieve our current account by getting the first account from cache
        // In multi-account applications, account should be retrieved by home account identifier or username instead
        
        do {
            let cachedAccounts = try applicationContext.allAccounts()
            if !cachedAccounts.isEmpty {
                return cachedAccounts.first
            }
        } catch let error as NSError {
            self.updateLogging(text: "Didn't find any accounts in cache: \(error)")
        }
        
        return nil
    }
```

#### <a name="get-a-token-interactively"></a>Obter um token interativamente

O código abaixo obtém um token pela primeira vez criando um objeto `MSALInteractiveTokenParameters` e chamando `acquireToken`. Em seguida, você adicionará o código que:

1. Cria `MSALInteractiveTokenParameters` com escopos.
2. Chama `acquireToken()` com os parâmetros criados.
3. Trata os erros. Para obter mais detalhes, confira o [Guia de tratamento de erro da MSAL para iOS e macOS](msal-handling-exceptions.md).
4. Trata o caso de sucesso.

Adicione o código a seguir à classe `ViewController` .

```swift
func acquireTokenInteractively() {
        
    guard let applicationContext = self.applicationContext else { return }
    guard let webViewParameters = self.webViewParameters else { return }
        
    // #1
    let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: webViewParameters)
        
    // #2
    applicationContext.acquireToken(with: parameters) { (result, error) in
            
        // #3
        if let error = error {
                
            self.updateLogging(text: "Could not acquire token: \(error)")
            return
        }
            
        guard let result = result else {
                
            self.updateLogging(text: "Could not acquire token: No result returned")
            return
        }
            
        // #4
        self.accessToken = result.accessToken
        self.updateLogging(text: "Access token is \(self.accessToken)")
        self.updateSignOutButton(enabled: true)
        self.getContentWithToken()
    }
}    
```


#### <a name="get-a-token-silently"></a>Obter um token no modo silencioso

Para adquirir um token atualizado silenciosamente, adicione o código a seguir à classe `ViewController`. Ele cria um objeto `MSALSilentTokenParameters` e chama `acquireTokenSilent()`:

```swift
    
    func acquireTokenSilently(_ account : MSALAccount!) {
        guard let applicationContext = self.applicationContext else { return }
        let parameters = MSALSilentTokenParameters(scopes: kScopes, account: account)
        
        applicationContext.acquireTokenSilent(with: parameters) { (result, error) in    
            if let error = error {
                let nsError = error as NSError
                if (nsError.domain == MSALErrorDomain) {
                    if (nsError.code == MSALError.interactionRequired.rawValue) {
                        DispatchQueue.main.async {
                            self.acquireTokenInteractively()
                        }
                        return
                    }
                }
                self.updateLogging(text: "Could not acquire token silently: \(error)")
                return
            }
            
            guard let result = result else {
                self.updateLogging(text: "Could not acquire token: No result returned")
                return
            }
            
            self.accessToken = result.accessToken
            self.updateLogging(text: "Refreshed Access token is \(self.accessToken)")
            self.updateSignOutButton(enabled: true)
            self.getContentWithToken()
        }
    }
```

### <a name="call-the-microsoft-graph-api"></a>Chamar a API do Microsoft Graph 

Depois que você obtiver um token, seu aplicativo poderá usá-lo no cabeçalho HTTP para fazer uma solicitação autorizada ao Microsoft Graph:

| chave de cabeçalho    | value                 |
| ------------- | --------------------- |
| Autorização | Bearer \<access-token> |

Adicione o código a seguir à classe `ViewController`:

```swift
    func getContentWithToken() {        
        // Specify the Graph API endpoint
        let url = URL(string: kGraphURI)
        var request = URLRequest(url: url!)
        
        // Set the Authorization header for the request. We use Bearer tokens, so we specify Bearer + the token we got from the result
        request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")
               
        URLSession.shared.dataTask(with: request) { data, response, error in
               
        if let error = error {
            self.updateLogging(text: "Couldn't get graph result: \(error)")
            return
        }
               
        guard let result = try? JSONSerialization.jsonObject(with: data!, options: []) else {
               
        self.updateLogging(text: "Couldn't deserialize result JSON")
            return
        }
               
        self.updateLogging(text: "Result from Graph: \(result))")
        
        }.resume()
    }
```

Confira [API do Microsoft Graph](https://graph.microsoft.com) para saber mais sobre a API do Microsoft Graph.

### <a name="use-msal-for-sign-out"></a>Usar a MSAL para saída

Em seguida, adicione o suporte para saída.

> [!Important]
> A saída com a MSAL remove todas as informações conhecidas sobre um usuário deste aplicativo, mas o usuário ainda terá uma sessão ativa em seu dispositivo. Se o usuário tentar entrar novamente, ele poderá ver a interface do usuário de entrada, mas talvez não precise inserir novamente suas credenciais, pois a sessão do dispositivo ainda estará ativa.

Para adicionar a funcionalidade de saída, adicione o código a seguir dentro da classe `ViewController`. Esse método percorre todas as contas e as remove:

```swift 
@objc func signOut(_ sender: AnyObject) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        guard let account = self.currentAccount() else { return }
        
        do {
            
            /**
             Removes all tokens from the cache for this application for the provided account
             
             - account:    The account to remove from the cache
             */
            
            try applicationContext.remove(account)
            self.updateLogging(text: "")
            self.updateSignOutButton(enabled: false)
            self.accessToken = ""
            
        } catch let error as NSError {
            
            self.updateLogging(text: "Received error signing account out: \(error)")
        }
    }
```

### <a name="enable-token-caching"></a>Ativar cache de token

Por padrão, a MSAL armazena em cache os tokens do seu aplicativo nos conjuntos de chaves do iOS ou macOS. 

Para habilitar o cache de token:
1. Verifique se seu aplicativo está assinado corretamente
2. Acesse as Configurações do Projeto do Xcode > **guia Funcionalidades** > **Habilitar o Compartilhamento do Conjunto de Chaves**
3. Clique em **+** e insira uma entrada de **Grupos de Conjunto de Chaves** a seguir: 3.a Para iOS, insira `com.microsoft.adalcache` 3.b Para o macOS, insira `com.microsoft.identity.universalstorage`

### <a name="add-helper-methods"></a>Adicionar métodos auxiliares
Adicione os seguintes métodos auxiliares à classe `ViewController` para concluir a amostra.

### <a name="ios-ui"></a>Interface do usuário do iOS:

``` swift
    
    func updateLogging(text : String) {
        
        if Thread.isMainThread {
            self.loggingText.text = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.text = text
            }
        }
    }
    
    func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }
```

### <a name="macos-ui"></a>Interface do usuário do macOS:

```swift
func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }
    
    func updateLogging(text : String) {
        
        if Thread.isMainThread {
            self.loggingText.string = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.string = text
            }
        }
    }
```



### <a name="multi-account-applications"></a>Aplicativos de várias contas

Este aplicativo foi criado para um cenário de conta única. A MSAL também dá suporte a cenários de várias contas, mas requer algum trabalho adicional dos aplicativos. Você precisará criar a interface do usuário para ajudar a selecionar qual conta deseja usar para cada ação que requer tokens do usuário. Como alternativa, seu aplicativo pode implementar uma heurística para selecionar qual conta usar por meio do método `getAccounts()`.

## <a name="test-your-app"></a>Testar seu aplicativo

### <a name="run-locally"></a>Executar localmente

Compilar e implantar o aplicativo em um simulador ou dispositivo de teste. Você deve conseguir conectar e obter tokens para o Azure AD ou contas Microsoft pessoais.

Na primeira vez que um usuário entrar no aplicativo, ele será solicitado pela identidade da Microsoft a fornecer seu consentimento às permissões solicitadas.  Embora a maioria dos usuários consiga fornecer seu consentimento, alguns locatários do Azure AD desabilitaram o consentimento do usuário, o que exige que os administradores forneçam consentimento em nome de todos os usuários. Para dar suporte a esse cenário, registre os escopos do seu aplicativo no portal do Azure.

Após entrar, este aplicativo exibirá os dados retornados do ponto de extremidade `/me` do Microsoft Graph.

## <a name="get-help"></a>Obter ajuda

Visite [Ajuda e suporte](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options) se você tiver algum problema com este tutorial ou com a plataforma de identidade da Microsoft.

Ajude-nos a melhorar a plataforma de identidade da Microsoft. Deixe sua opinião respondendo a uma breve pesquisa de duas perguntas.

> [!div class="nextstepaction"]
> [Pesquisa da plataforma de identidade da Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
