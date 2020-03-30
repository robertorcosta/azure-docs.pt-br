---
title: Migrar aplicativos Xamarin usando agentes para o MSAL.NET
titleSuffix: Microsoft identity platform
description: Saiba como migrar aplicativos Xamarin iOS que usam o Microsoft Authenticator de ADAL.NET para MSAL.NET.
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
ms.openlocfilehash: de259daa7fd27cc4f138c294a7f347502ca482a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77185820"
---
# <a name="migrate-ios-applications-that-use-microsoft-authenticator-from-adalnet-to-msalnet"></a>Migre os aplicativos para iOS que usam o Microsoft Authenticator de ADAL.NET para MSAL.NET

Você tem usado a Biblioteca de Autenticação de Diretório Ativo do Azure para o .NET (ADAL.NET) e o corretor do iOS. Agora é hora de migrar para a Biblioteca de [Autenticação da Microsoft](msal-overview.md) para .NET (MSAL.NET), que suporta o corretor no iOS a partir da versão 4.3. 

Por onde você deve começar? Este artigo ajuda você a migrar seu aplicativo Xamarin iOS de ADAL para MSAL.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que você já tem um aplicativo Xamarin iOS integrado com a corretora iOS. Se não, vá diretamente para MSAL.NET e comece a implementação do corretor lá. Para obter informações sobre como invocar o corretor iOS em MSAL.NET com um novo aplicativo, consulte [esta documentação](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS#why-use-brokers-on-xamarinios-and-xamarinandroid-applications).

## <a name="background"></a>Segundo plano

### <a name="what-are-brokers"></a>O que são corretores?

Corretores são aplicativos fornecidos pela Microsoft no Android e iOS. (Veja o aplicativo [Microsoft Authenticator](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) no iOS e Android, e o aplicativo Intune Company Portal no Android.) 

Eles habilitam:

- Logon único.
- Identificação do dispositivo, que é exigida por algumas [políticas de Acesso Condicional](../conditional-access/overview.md). Para obter mais informações, consulte [O gerenciamento do dispositivo](../conditional-access/concept-conditional-access-conditions.md#device-platforms).
- Verificação de identificação de aplicativos, que também é necessária em alguns cenários corporativos. Para obter mais informações, consulte [Intune mobile application management (MAM)](https://docs.microsoft.com/intune/mam-faq).

## <a name="migrate-from-adal-to-msal"></a>Migrar de ADAL para MSAL

### <a name="step-1-enable-the-broker"></a>Passo 1: Habilite o corretor

<table>
<tr><td>Código ADAL atual:</td><td>Contrapartida da MSAL:</td></tr>
<tr><td>
Em ADAL.NET, o suporte ao corretor foi habilitado em um contexto por autenticação. Isso está desabilitado por padrão. Você tinha que definir um 

`useBroker`bandeira para verdade `PlatformParameters` na construtora para chamar o corretor:

```csharp
public PlatformParameters(
        UIViewController callerViewController, 
        bool useBroker)
```
Além disso, no código específico da plataforma, neste exemplo, no renderizador de página para iOS, defina o`useBroker` 
bandeira para a verdade:
```csharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```

Em seguida, inclua os parâmetros na chamada de token de aquisição:
```csharp
 AuthenticationResult result =
                    await
                        AuthContext.AcquireTokenAsync(
                              Resource, 
                              ClientId, 
                              new Uri(RedirectURI), 
                              platformParameters)
                              .ConfigureAwait(false);
```

</td><td>
Em MSAL.NET, o suporte ao corretor é habilitado por publicclientapplication. Isso está desabilitado por padrão. Para habilitá-lo, use o 

`WithBroker()`parâmetro (definido como verdadeiro por padrão) a fim de chamar o corretor:

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos)
                .Build();
```
Na chamada de token de aquisição:
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

### <a name="step-2-set-a-uiviewcontroller"></a>Passo 2: Definir um UIViewController()
Em ADAL.NET, você passou em um UIViewController como parte de `PlatformParameters`. (Veja o exemplo no Passo 1.) Em MSAL.NET, para dar aos desenvolvedores mais flexibilidade, uma janela de objeto é usada, mas não é necessária no uso regular do iOS. Para usar o corretor, defina a janela do objeto para enviar e receber respostas do corretor. 
<table>
<tr><td>Código ADAL atual:</td><td>Contrapartida da MSAL:</td></tr>
<tr><td>
Um UIViewController é transmitido para 

`PlatformParameters`na plataforma específica para iOS.

```csharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```
</td><td>
Em MSAL.NET, você faz duas coisas para definir a janela do objeto para iOS:

1. Em `AppDelegate.cs`, `App.RootViewController` definido `UIViewController()`para um novo . Esta atribuição garante que haja um UIViewController com a chamada para o corretor. Se não estiver definido corretamente, você pode obter este erro:`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
1. Na chamada AcquireTokenInteractive, `.WithParentActivityOrWindow(App.RootViewController)`use e passe na referência à janela do objeto que você usará.

**Por exemplo:**

Em `App.cs`:
```csharp
   public static object RootViewController { get; set; }
```
Em `AppDelegate.cs`:
```csharp
   LoadApplication(new App());
   App.RootViewController = new UIViewController();
```
Na chamada de token de aquisição:
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

</table>

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Passo 3: Atualize o AppDelegate para lidar com o retorno de chamada
Tanto a ADAL quanto a MSAL ligam para o corretor, e o corretor, por sua vez, liga de volta para sua aplicação através do `OpenUrl` método da `AppDelegate` classe. Para obter mais informações, consulte [esta documentação](msal-net-use-brokers-with-xamarin-apps.md#step-3-update-appdelegate-to-handle-the-callback).

Não há mudanças aqui entre ADAL.NET e MSAL.NET.

### <a name="step-4-register-a-url-scheme"></a>Passo 4: Registre um esquema de URL
ADAL.NET e MSAL.NET usar URLs para invocar o corretor e devolver a resposta do corretor de volta ao aplicativo. Registre o esquema `Info.plist` de URL no arquivo do seu aplicativo da seguinte forma:

<table>
<tr><td>Código ADAL atual:</td><td>Contrapartida da MSAL:</td></tr>
<tr><td>
O esquema de URL é exclusivo do seu aplicativo.
</td><td>
O 

`CFBundleURLSchemes`nome deve incluir 

`msauth.`

como um prefixo, seguido pelo seu`CFBundleURLName`

Por exemplo: `$"msauth.(BundleId")`

```csharp
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

> [!NOTE]
> Esse esquema de URL torna-se parte do URI de redirecionamento que é usado para identificar exclusivamente o aplicativo quando ele recebe a resposta do corretor.

</table>

### <a name="step-5-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Passo 5: Adicione o identificador de corretor à seção LSApplicationQueriesSchemes

ADAL.NET e MSAL.NET `-canOpenURL:` ambos usam para verificar se o corretor está instalado no dispositivo. Adicione o identificador correto para a corretora iOS à seção LSApplicationQueriesSchemes do arquivo info.plist da seguinte forma:

<table>
<tr><td>Código ADAL atual:</td><td>Contrapartida da MSAL:</td></tr>
<tr><td>
Usos 

`msauth`


```csharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauth</string>
</array>
```
</td><td>
Usos 

`msauthv2`


```csharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
     <string>msauthv3</string>
</array>
```
</table>

### <a name="step-6-register-your-redirect-uri-in-the-portal"></a>Passo 6: Registre seu URI redirecionado no portal

ADAL.NET e MSAL.NET ambos adicionam um requisito extra no URI de redirecionamento quando ele visa o corretor. Registre o URI redirecionado com sua aplicação no portal.
<table>
<tr><td>Código ADAL atual:</td><td>Contrapartida da MSAL:</td></tr>
<tr><td>

`"<app-scheme>://<your.bundle.id>"`

Exemplo: 

`mytestiosapp://com.mycompany.myapp`
</td><td>

`$"msauth.{BundleId}://auth"`

Exemplo:

`public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; `

</table>

Para obter mais informações sobre como registrar o URI redirecionado no portal, consulte [Aproveitar a corretora em aplicativos Xamarin.iOS](msal-net-use-brokers-with-xamarin-apps.md#step-8-make-sure-the-redirect-uri-is-registered-with-your-app).

## <a name="next-steps"></a>Próximas etapas

Conheça [as considerações específicas do IOS xamarin com MSAL.NET](msal-net-xamarin-ios-considerations.md). 
