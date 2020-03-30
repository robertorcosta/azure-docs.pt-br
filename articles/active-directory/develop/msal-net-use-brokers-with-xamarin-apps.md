---
title: Use corretores com Xamarin iOS & Android | Azure
titleSuffix: Microsoft identity platform
description: Saiba como configurar aplicativos Xamarin iOS que podem usar o Microsoft Authenticator e a Microsoft Authentication Library para .NET (MSAL.NET). Saiba também como migrar da Biblioteca de Autenticação Ad do Azure para .NET (ADAL.NET) para a Biblioteca de Autenticação Microsoft para .NET (MSAL.NET).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262783"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>Use o Microsoft Authenticator ou o Portal da Empresa Intune em aplicativos Xamarin

No Android e iOS, corretores como o Microsoft Authenticator e o Portal da Empresa Microsoft Intune específico para Android permitem:

- **Único login (SSO)**: Os usuários não precisam fazer login em cada aplicativo.
- **Identificação do dispositivo**: O corretor acessa o certificado do dispositivo. Este certificado é criado no dispositivo quando é juntado ao local de trabalho.
- **Verificação de identificação do aplicativo**: Quando um aplicativo chama o corretor, ele passa sua URL de redirecionamento. O corretor verifica a URL.

Para habilitar um desses `WithBroker()` recursos, use o `PublicClientApplicationBuilder.CreateApplication` parâmetro quando chamar o método. O `.WithBroker()` parâmetro é definido como verdadeiro por padrão. 

Use também as instruções nas seções a seguir para configurar autenticação intermediada para aplicativos [iOS](#brokered-authentication-for-ios) ou [Android.](#brokered-authentication-for-android)

## <a name="brokered-authentication-for-ios"></a>Autenticação intermediada para iOS

Use as seguintes etapas para habilitar seu aplicativo Xamarin.iOS para falar com o aplicativo [Microsoft Authenticator.](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458)

### <a name="step-1-enable-broker-support"></a>Passo 1: Habilite o suporte ao corretor
Você deve habilitar o `PublicClientApplication`suporte ao corretor para instâncias individuais de . O suporte é desativado por padrão. Quando você `PublicClientApplication` `PublicClientApplicationBuilder`criar através, use o `WithBroker()` parâmetro como o exemplo a seguir mostra. O `WithBroker()` parâmetro é definido como verdadeiro por padrão.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>Passo 2: Habilite o acesso ao chaveiro

Para habilitar o acesso ao chaveiro, você deve ter um grupo de acesso a chaveiros para sua aplicação. Você pode `WithIosKeychainSecurityGroup()` usar a API para definir seu grupo de acesso ao chaveiro ao criar seu aplicativo:

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
      
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Para obter mais informações, consulte [Habilitar acesso a chaveiros](msal-net-xamarin-ios-considerations.md#enable-keychain-access).

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Passo 3: Atualize o AppDelegate para lidar com o retorno de chamada
Quando a Biblioteca de Autenticação microsoft para .NET (MSAL.NET) chama `OpenUrl` o corretor, o corretor liga de volta para o seu aplicativo através do método da `AppDelegate` classe. Como a MSAL aguarda a resposta do corretor, sua solicitação precisa cooperar para chamar MSAL.NET de volta. Para habilitar essa `AppDelegate.cs` cooperação, atualize o arquivo para substituir o seguinte método.

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

Este método é invocado toda vez que a aplicação é iniciada. É usado como uma oportunidade para processar a resposta do corretor e completar o processo de autenticação que MSAL.NET iniciado.

### <a name="step-4-set-uiviewcontroller"></a>Passo 4: Definir uiViewcontroller()
Ainda no `AppDelegate.cs` arquivo, você precisa definir uma janela de objeto. Normalmente, para xamarin iOS você não precisa definir a janela do objeto. Mas você precisa de uma janela de objeto para enviar e receber respostas do corretor. 

Para configurar a janela do objeto: 
1. No `AppDelegate.cs` arquivo, `App.RootViewController` definido como `UIViewController()`um novo . Esta atribuição garante que a chamada `UIViewController`para o corretor inclua . Se esta configuração for atribuída incorretamente, você pode obter esse erro:

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. Na `AcquireTokenInteractive` chamada, `.WithParentActivityOrWindow(App.RootViewController)` use e passe na referência à janela do objeto que você usará.

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

### <a name="step-5-register-a-url-scheme"></a>Passo 5: Registre um esquema de URL
MSAL.NET usa URLs para invocar o corretor e, em seguida, retornar a resposta do corretor ao seu aplicativo. Para concluir a ida e volta, registre `Info.plist` um esquema de URL para o seu aplicativo no arquivo.

O `CFBundleURLSchemes` nome `msauth.` deve incluir como prefixo. Siga o `CFBundleURLName`prefixo com . 

No esquema de `BundleId` URL, identifica exclusivamente o aplicativo: `$"msauth.(BundleId)"`. Então, `BundleId` se `com.yourcompany.xforms`for, então `msauth.com.yourcompany.xforms`o esquema de URL é .

> [!NOTE]
> Esse esquema de URL torna-se parte do URI de redirecionamento que identifica exclusivamente seu aplicativo quando recebe a resposta do corretor.

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

### <a name="step-6-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Passo 6: Adicione o identificador do corretor à seção LSApplicationQueriesSchemes

A MSAL usa `–canOpenURL:` para verificar se o corretor está instalado no dispositivo. No iOS 9, a Apple bloqueou os esquemas que um aplicativo pode consultar. 

Adicione `msauthv2` à `LSApplicationQueriesSchemes` seção `Info.plist` do arquivo, como no exemplo a seguir:

```XML
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-register-your-redirect-uri-in-the-application-portal"></a>Passo 7: Registre seu URI redirecionado no portal de aplicativos

Quando você usa o corretor, seu URI de redirecionamento tem um requisito extra. O URI de redirecionamento _deve_ ter o seguinte formato:

```csharp
$"msauth.{BundleId}://auth"
```

Aqui está um exemplo:

```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```

Observe que o URI `CFBundleURLSchemes` redirecionamento corresponde `Info.plist` ao nome que você incluiu no arquivo.

### <a name="step-8-make-sure-the-redirect-uri-is-registered-with-your-app"></a>Passo 8: Certifique-se de que o URI redirecionamento esteja registrado no seu aplicativo

O URI de redirecionamento precisa ser registrado no portal de registro do [aplicativo](https://portal.azure.com) como um URI de redirecionamento válido para sua aplicação. 

O portal de registro de aplicativos fornece uma nova experiência para ajudá-lo a calcular o URI de resposta intermediado a partir do ID do pacote. 

Para calcular o URI de redirecionamento:

1. No portal de registro do aplicativo, escolha **Autenticação** > **Experimente a nova experiência.**

   ![Experimente a nova experiência de registro de aplicativos](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

1. Selecione **Adicionar uma plataforma**.

   ![Adicionar uma plataforma](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

1. Quando a lista de plataformas for suportada, selecione **o iOS**.

   ![Configurar iOS](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

1. Digite seu ID de pacote conforme solicitado e, em seguida, **selecione Configurar**.

   ![Digite o ID do pacote](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

Quando você terminar com as etapas, o URI de redirecionamento é computado para você.

![Copiar redirecionar uri](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Autenticação intermediada para Android

### <a name="step-1-enable-broker-support"></a>Passo 1: Habilite o suporte ao corretor

O suporte ao corretor é habilitado por publicclientApplication. Isso está desabilitado por padrão. Use `WithBroker()` o parâmetro (definido como verdadeiro por `IPublicClientApplication` padrão) ao criar o através do `PublicClientApplicationBuilder`.

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid) //(see step 4 below)
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Passo 2: Atualize o AppDelegate para lidar com o retorno de chamada

Quando MSAL.NET chama o corretor, o corretor, por sua vez, ligará de volta para sua aplicação com o método OnActivityResult(). Como a MSAL aguardará a resposta do corretor, sua solicitação precisa encaminhar o resultado para MSAL.NET.
Isso pode ser alcançado direcionando `SetAuthenticationContinuationEventArgs(int requestCode, Result resultCode, Intent data)` o resultado para o método OnActivityResult() como mostrado abaixo

```CSharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
   base.OnActivityResult(requestCode, resultCode, data);
   AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

Este método é invocado toda vez que o aplicativo corretor é lançado e é usado como uma oportunidade para processar a resposta do corretor e completar o processo de autenticação iniciado por MSAL.NET.

### <a name="step-3-set-an-activity"></a>Passo 3: Definir uma atividade

Para que a autenticação intermediada funcione, você precisará definir uma atividade para que a MSAL possa enviar e receber a resposta do corretor.

Para fazer isso, você precisará fornecer a atividade (geralmente `WithParentActivityOrWindow(object parent)` o MainActivity) para o objeto pai. 

**Por exemplo:**

Na chamada Acquire Token:

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow((Activity)context))
             .ExecuteAsync();
```

### <a name="step-4-register-your-redirecturi-in-the-application-portal"></a>Passo 4: Registre seu RedirectUri no portal de aplicativos

A MSAL usa URLs para invocar o corretor e, em seguida, retornar ao seu aplicativo. Para completar essa ida e volta, você precisa registrar um esquema de URL para o seu aplicativo. Este URI redirecionado precisa ser registrado no portal de registro do aplicativo Azure AD como um URI de redirecionamento válido para sua aplicação.


O URI de redirecionamento necessário para sua aplicação depende do certificado usado para assinar o APK.

```
Example: msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=
```

A última parte do `hgbUYHVBYUTvuvT&Y6tr554365466=`URI, é a assinatura com a assinatura que o APK está assinado, base64 codificado.
No entanto, durante a fase de desenvolvimento do seu aplicativo usando o Visual Studio, se você estiver depurando seu código sem assinar o apk com um certificado específico, o Visual Studio assinará o apk para fins de depuração, dando ao APK uma assinatura exclusiva para o máquina em que é construído. Assim, cada vez que você constrói seu aplicativo em uma máquina diferente, você precisará atualizar o URI redirecionado no código do aplicativo e o registro do aplicativo no portal Azure, a fim de autenticar com o MSAL. 

Durante a depuração, você pode encontrar uma exceção MSAL (ou mensagem de log) informando que o URI de redirecionamento fornecido está incorreto. **Esta exceção também fornecerá o URI de redirecionamento que você deve usar** com a máquina atual em que você está depurando. Você pode usar este URI de redirecionamento para continuar se desenvolvendo por enquanto.

Uma vez que você esteja pronto para finalizar seu código, certifique-se de atualizar o URI redirecionamento no código e no registro do aplicativo no portal Azure para usar a assinatura do certificado com o qual você estará assinando o APK.

Na prática, isso significa que você tem que registrar um URI de redirecionamento para cada membro da equipe, além de um URI redirecionado para a versão assinada de produção do APK.

Você também pode calcular essa assinatura você mesmo, semelhante a como a MSAL faz: 

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

Você também tem a opção de adquirir a assinatura do seu pacote usando a ferramenta de chave com os seguintes comandos:

No Windows: `keytool.exe -list -v -keystore "%LocalAppData%\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android`

Para Mac:`keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64`

## <a name="next-steps"></a>Próximas etapas

Conheça [considerações para usar a Plataforma Universal Windows com MSAL.NET](msal-net-uwp-considerations.md).
