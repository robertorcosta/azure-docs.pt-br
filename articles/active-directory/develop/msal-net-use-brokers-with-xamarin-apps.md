---
title: Usar agentes com o Xamarin iOS & Android | Azure
titleSuffix: Microsoft identity platform
description: Saiba como configurar aplicativos Xamarin iOS que podem usar Microsoft Authenticator e MSAL.NET (biblioteca de autenticação da Microsoft para .NET). Saiba também como migrar da biblioteca de autenticação do AD do Azure para .NET (ADAL.NET) para a biblioteca de autenticação da Microsoft para .NET (MSAL.NET).
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1a57173311278c5e3e0304aeb12d4d6999379eb5
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79262783"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>Usar Microsoft Authenticator ou Portal da Empresa do Intune em aplicativos Xamarin

No Android e iOS, os agentes como Microsoft Authenticator e o Microsoft Intune específico do Android Portal da Empresa habilitam:

- **SSO (logon único)** : os usuários não precisam entrar em cada aplicativo.
- **Identificação do dispositivo**: o agente acessa o certificado do dispositivo. Esse certificado é criado no dispositivo quando ele é ingressado no local de trabalho.
- **Verificação da identificação do aplicativo**: quando um aplicativo chama o agente, ele passa sua URL de redirecionamento. O agente verifica a URL.

Para habilitar um desses recursos, use o parâmetro `WithBroker()` ao chamar o método `PublicClientApplicationBuilder.CreateApplication`. O parâmetro `.WithBroker()` é definido como true por padrão. 

Use também as instruções nas seções a seguir para configurar a autenticação orientada para aplicativos [Ios](#brokered-authentication-for-ios) ou aplicativos [Android](#brokered-authentication-for-android) .

## <a name="brokered-authentication-for-ios"></a>Autenticação orientada para iOS

Use as etapas a seguir para habilitar seu aplicativo Xamarin. iOS para se comunicar com o aplicativo [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) .

### <a name="step-1-enable-broker-support"></a>Etapa 1: habilitar o suporte do Broker
Você deve habilitar o suporte do Broker para instâncias individuais do `PublicClientApplication`. O suporte está desabilitado por padrão. Ao criar `PublicClientApplication` por meio de `PublicClientApplicationBuilder`, use o parâmetro `WithBroker()` como mostra o exemplo a seguir. O parâmetro `WithBroker()` é definido como true por padrão.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>Etapa 2: habilitar o acesso ao conjunto de chaves

Para habilitar o acesso ao conjunto de chaves, você deve ter um grupo de acesso de cadeia de chaves para seu aplicativo. Você pode usar a API `WithIosKeychainSecurityGroup()` para definir o grupo de acesso do conjunto de chaves ao criar seu aplicativo:

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
      
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Para obter mais informações, consulte [habilitar o acesso](msal-net-xamarin-ios-considerations.md#enable-keychain-access)ao conjunto de chaves.

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Etapa 3: atualizar AppDelegate para manipular o retorno de chamada
Quando a MSAL.NET (biblioteca de autenticação da Microsoft para .NET) chama o agente, o agente chama de volta para seu aplicativo por meio do método `OpenUrl` da classe `AppDelegate`. Como MSAL aguarda a resposta do agente, seu aplicativo precisa cooperar para chamar MSAL.NET de volta. Para habilitar essa cooperação, atualize o arquivo de `AppDelegate.cs` para substituir o método a seguir.

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

Esse método é invocado toda vez que o aplicativo é iniciado. Ele é usado como uma oportunidade de processar a resposta do agente e concluir o processo de autenticação que o MSAL.NET iniciou.

### <a name="step-4-set-uiviewcontroller"></a>Etapa 4: definir UIViewController ()
Ainda no arquivo de `AppDelegate.cs`, você precisa definir uma janela de objeto. Normalmente, para o Xamarin iOS, você não precisa definir a janela de objeto. Mas você precisa de uma janela de objeto para enviar e receber respostas do agente. 

Para configurar a janela de objeto: 
1. No arquivo `AppDelegate.cs`, defina `App.RootViewController` como um novo `UIViewController()`. Essa atribuição garante que a chamada para o agente inclui `UIViewController`. Se essa configuração for atribuída incorretamente, você poderá receber esse erro:

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. Na chamada `AcquireTokenInteractive`, use `.WithParentActivityOrWindow(App.RootViewController)` e, em seguida, passe a referência para a janela de objeto que você usará.

    Em `App.cs`:

    ```csharp
       public static object RootViewController { get; set; }
    ```

    Em `AppDelegate.cs`:

    ```csharp
       LoadApplication(new App());
       App.RootViewController = new UIViewController();
    ```

    Na chamada `AcquireToken`:

    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```

### <a name="step-5-register-a-url-scheme"></a>Etapa 5: registrar um esquema de URL
O MSAL.NET usa URLs para invocar o agente e, em seguida, retornar a resposta do agente para seu aplicativo. Para concluir a viagem de ida e volta, registre um esquema de URL para seu aplicativo no arquivo de `Info.plist`.

O nome do `CFBundleURLSchemes` deve incluir `msauth.` como um prefixo. Siga o prefixo com `CFBundleURLName`. 

No esquema de URL, `BundleId` identifica exclusivamente o aplicativo: `$"msauth.(BundleId)"`. Portanto, se `BundleId` for `com.yourcompany.xforms`, o esquema de URL será `msauth.com.yourcompany.xforms`.

> [!NOTE]
> Esse esquema de URL se torna parte do URI de redirecionamento que identifica exclusivamente seu aplicativo quando recebe a resposta do agente.

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

### <a name="step-6-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Etapa 6: Adicionar o identificador do agente à seção LSApplicationQueriesSchemes

O MSAL usa `–canOpenURL:` para verificar se o agente está instalado no dispositivo. No iOS 9, a Apple bloqueou os esquemas que um aplicativo pode consultar. 

Adicione `msauthv2` à seção `LSApplicationQueriesSchemes` do arquivo de `Info.plist`, como no exemplo a seguir:

```XML
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-register-your-redirect-uri-in-the-application-portal"></a>Etapa 7: registrar seu URI de redirecionamento no portal do aplicativo

Quando você usa o agente, seu URI de redirecionamento tem um requisito extra. O URI de redirecionamento _deve_ ter o seguinte formato:

```csharp
$"msauth.{BundleId}://auth"
```

Aqui está um exemplo:

```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```

Observe que o URI de redirecionamento corresponde ao nome de `CFBundleURLSchemes` que você incluiu no arquivo de `Info.plist`.

### <a name="step-8-make-sure-the-redirect-uri-is-registered-with-your-app"></a>Etapa 8: Verifique se o URI de redirecionamento está registrado com seu aplicativo

O URI de redirecionamento precisa ser registrado no [portal de registro de aplicativo](https://portal.azure.com) como um URI de redirecionamento válido para seu aplicativo. 

O portal de registro de aplicativo fornece uma nova experiência para ajudá-lo a calcular o URI de resposta orientado da ID do pacote. 

Para calcular o URI de redirecionamento:

1. No portal de registro de aplicativo, escolha **autenticação** > **experimentar a nova experiência**.

   ![Experimente a nova experiência de registro de aplicativo](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

1. Selecione **Adicionar uma plataforma**.

   ![Adicionar uma plataforma](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

1. Quando houver suporte para a lista de plataformas, selecione **Ios**.

   ![Configurar o iOS](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

1. Insira a ID do pacote conforme solicitado e, em seguida, selecione **Configurar**.

   ![Insira a ID do pacote](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

Quando você terminar as etapas, o URI de redirecionamento será calculado para você.

![Copiar URI de redirecionamento](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Autenticação orientada para Android

### <a name="step-1-enable-broker-support"></a>Etapa 1: habilitar o suporte do Broker

O suporte do Broker é habilitado por PublicClientApplication. Isso está desabilitado por padrão. Use o parâmetro `WithBroker()` (definido como true por padrão) ao criar o `IPublicClientApplication` por meio do `PublicClientApplicationBuilder`.

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid) //(see step 4 below)
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Etapa 2: atualizar AppDelegate para manipular o retorno de chamada

Quando o MSAL.NET chama o agente, o agente, por sua vez, retorna de volta para seu aplicativo com o método OnActivityResult (). Como o MSAL aguardará a resposta do agente, seu aplicativo precisará rotear o resultado para MSAL.NET.
Isso pode ser obtido roteando o resultado para o `SetAuthenticationContinuationEventArgs(int requestCode, Result resultCode, Intent data)` substituindo o método OnActivityResult (), conforme mostrado abaixo

```CSharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
   base.OnActivityResult(requestCode, resultCode, data);
   AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

Esse método é invocado toda vez que o aplicativo agente é iniciado e é usado como uma oportunidade para processar a resposta do agente e concluir o processo de autenticação iniciado pelo MSAL.NET.

### <a name="step-3-set-an-activity"></a>Etapa 3: definir uma atividade

Para que a autenticação orientada funcione, você precisará definir uma atividade para que o MSAL possa enviar e receber a resposta do agente.

Para fazer isso, você precisará fornecer a atividade (geralmente o MainActivity) para a `WithParentActivityOrWindow(object parent)` como o objeto pai. 

**Por exemplo:**

Na chamada de aquisição de token:

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow((Activity)context))
             .ExecuteAsync();
```

### <a name="step-4-register-your-redirecturi-in-the-application-portal"></a>Etapa 4: registrar seu RedirectUri no portal do aplicativo

O MSAL usa URLs para invocar o agente e, em seguida, retornar ao seu aplicativo. Para concluir essa viagem de ida e volta, você precisa registrar um esquema de URL para seu aplicativo. Esse URI de redirecionamento precisa ser registrado no portal de registro de aplicativo do Azure AD como um URI de redirecionamento válido para seu aplicativo.


O URI de redirecionamento necessário para seu aplicativo depende do certificado usado para assinar o APK.

```
Example: msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=
```

A última parte do URI, `hgbUYHVBYUTvuvT&Y6tr554365466=`, é a assinatura com a qual o APK é assinado, codificado em base64.
No entanto, durante a fase de desenvolvimento do seu aplicativo usando o Visual Studio, se você estiver Depurando seu código sem assinar o apk com um certificado específico, o Visual Studio assinará o apk para você para fins de depuração, dando ao APK uma assinatura exclusiva para o computador no qual ele foi criado. Assim, sempre que você criar seu aplicativo em um computador diferente, precisará atualizar o URI de redirecionamento no código do aplicativo e o registro do aplicativo no portal do Azure para autenticar com o MSAL. 

Durante a depuração, você pode encontrar uma exceção MSAL (ou mensagem de log) informando que o URI de redirecionamento fornecido está incorreto. **Essa exceção também fornecerá o URI de redirecionamento que você deve usar** com o computador atual no qual você está depurando. Você pode usar esse URI de redirecionamento para continuar desenvolvendo por enquanto.

Quando você estiver pronto para finalizar seu código, certifique-se de atualizar o URI de redirecionamento no código e no registro do aplicativo no portal do Azure para usar a assinatura do certificado com o qual você estará assinando o APK.

Na prática, isso significa que você precisa registrar um URI de redirecionamento para cada membro da equipe, além de um URI de redirecionamento para a versão de produção assinada do APK.

Você também pode calcular essa assinatura por conta própria, semelhante a como o MSAL faz isso: 

```CSharp
   private string GetRedirectUriForBroker()
   {
      string packageName = Application.Context.PackageName;
      string signatureDigest = this.GetCurrentSignatureForPackage(packageName);
      if (!string.IsNullOrEmpty(signatureDigest))
      {
            return string.Format(CultureInfo.InvariantCulture, "{0}://{1}/{2}", RedirectUriScheme,
               packageName.ToLowerInvariant(), signatureDigest);
      }

      return string.Empty;
   }

   private string GetCurrentSignatureForPackage(string packageName)
   {
            PackageInfo info = Application.Context.PackageManager.GetPackageInfo(packageName,
               PackageInfoFlags.Signatures);
            if (info != null && info.Signatures != null && info.Signatures.Count > 0)
            {
               // First available signature. Applications can be signed with multiple signatures.
               // The order of Signatures is not guaranteed.
               Signature signature = info.Signatures[0];
               MessageDigest md = MessageDigest.GetInstance("SHA");
               md.Update(signature.ToByteArray());
               return Convert.ToBase64String(md.Digest(), Base64FormattingOptions.None);
               // Server side needs to register all other tags. ADAL will
               // send one of them.
            }
   }
```

Você também tem a opção de adquirir a assinatura para seu pacote usando o keytool com os seguintes comandos:

No Windows: `keytool.exe -list -v -keystore "%LocalAppData%\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android`

Para Mac: `keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64`

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as [considerações para usar o plataforma universal do Windows com MSAL.net](msal-net-uwp-considerations.md).
