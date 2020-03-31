---
title: Considerações xamarin Android (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre considerações para usar o Xamarin Android com a Microsoft Authentication Library para .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81b55253d757f641979c6f72001803d7d38d9af3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132510"
---
# <a name="considerations-for-using-xamarin-android-with-msalnet"></a>Considerações para usar Xamarin Android com MSAL.NET
Este artigo discute o que você deve considerar quando você usa o Xamarin Android com a Microsoft Authentication Library para .NET (MSAL.NET).

## <a name="set-the-parent-activity"></a>Defina a atividade dos pais

No Xamarin Android, defina a atividade pai para que o token retorne após a interação. Este é um exemplo de código:

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

No MSAL 4.2 e posterior, você também pode `PublicClientApplication`definir essa funcionalidade no nível de . Para isso, use um retorno de chamada:

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

Se você usar [CurrentActivityPlugin,](https://github.com/jamesmontemagno/CurrentActivityPlugin)então o código do `PublicClientApplication` construtor será o seguinte exemplo.

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

## <a name="ensure-that-control-returns-to-msal"></a>Certifique-se de que o controle retorne ao MSAL 
Quando a parte interativa do fluxo de autenticação terminar, certifique-se de que o controle volte para o MSAL. No Android, anular `OnActivityResult` o `Activity`método de . Em seguida, chame o `SetAuthenticationContinuationEventArgs` método da `AuthenticationContinuationHelper` classe MSAL. 

Aqui está um exemplo:

```csharp
protected override void OnActivityResult(int requestCode, 
                                         Result resultCode, Intent data)
{
 base.OnActivityResult(requestCode, resultCode, data);
 AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode,
                                                                         resultCode,
                                                                         data);
}

```

Esta linha garante que o controle retorne ao MSAL no final da parte interativa do fluxo de autenticação.

## <a name="update-the-android-manifest"></a>Atualize o manifesto do Android
O arquivo *AndroidManifest.xml* deve conter os seguintes valores:

<!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
```
  <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
     <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" />
     </intent-filter>
 </activity>
```

Substitua o nome do pacote que você registrou `android:host=` no portal Azure pelo valor. Substitua o hash de chave que você registrou `android:path=` no portal Azure pelo valor. O hash de assinatura *não* deve ser codificado por URL. Certifique-se de que`/`uma barra de avanço () apareça no início do seu hash de assinatura.

Alternativamente, [crie a atividade em código em](https://docs.microsoft.com/xamarin/android/platform/android-manifest#the-basics) vez de editar manualmente o *AndroidManifest.xml*. Para criar a atividade em código, primeiro `Activity` crie uma `IntentFilter` classe que inclua o atributo e o atributo. 

Aqui está um exemplo de uma classe que representa os valores do arquivo XML:

```csharp
  [Activity]
  [IntentFilter(new[] { Intent.ActionView },
        Categories = new[] { Intent.CategoryBrowsable, Intent.CategoryDefault },
        DataHost = "auth",
        DataScheme = "msal{client_id}")]
  public class MsalActivity : BrowserTabActivity
  {
  }
```

### <a name="xamarinforms-43x-manifest"></a>Xamarin.Forms 4.3.X manifesto

Xamarin.Forms 4.3.x gera código `package` que `com.companyname.{appName}` define o atributo para no *AndroidManifest.xml*. Se você `DataScheme` `msal{client_id}`usar como , então você pode querer alterar `MainActivity.cs` o valor para corresponder ao valor do namespace.

## <a name="use-the-embedded-web-view-optional"></a>Use a visualização web incorporada (opcional)

Por padrão, MSAL.NET usa o navegador da Web do sistema. Este navegador permite que você obtenha o soo de login único usando aplicativos da Web e outros aplicativos. Em alguns casos raros, você pode querer que seu sistema use uma visualização web incorporada. 

Este exemplo de código mostra como configurar uma visualização web incorporada:

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

Para obter mais informações, consulte [Usar navegadores da Web para MSAL.NET](msal-net-web-browsers.md) e [considerações do navegador do sistema Android Xamarin](msal-net-system-browser-android-considerations.md).


## <a name="troubleshoot"></a>Solução de problemas
Você pode criar um novo aplicativo Xamarin.Forms e adicionar uma referência ao pacote MSAL.NET NuGet.
Mas você pode ter problemas de compilação se atualizar um aplicativo Xamarin.Forms existente para MSAL.NET visualização 1.1.2 ou posterior.

Para solucionar problemas de construção:

- Atualize o pacote nuGet MSAL.NET existente para MSAL.NET visualização 1.1.2 ou posterior.
- Verifique se o Xamarin.Forms é atualizado automaticamente para a versão 2.5.0.122203. Se necessário, atualize Xamarin.Forms para esta versão.
- Verifique que Xamarin.Android.Support.v4 atualizado automaticamente para a versão 25.4.0.2. Se necessário, atualize para a versão 25.4.0.2.
- Certifique-se de que todos os pacotes Xamarin.Android.Support são a versão alvo 25.4.0.2.
- Limpe ou reconstrua o aplicativo.
- No Visual Studio, tente definir o número máximo de compilações de projetos paralelos para 1. Para isso, selecione **Projetos de Opções** > **e Soluções** > **Build and Run** > **Número máximo de projetos paralelos construídos**.
- Se você estiver construindo a partir da `/m`linha de comando e seu comando usa, tente remover este elemento do comando.

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>Erro: O nome AutenticaçãoContinuação não existe no contexto atual

Se um erro `AuthenticationContinuationHelper` indicar que não existe no contexto atual, o Visual Studio pode ter atualizado incorretamente o arquivo Android.csproj*. Às * \<vezes,* o caminho do arquivo HintPath>contém incorretamente *netstandard13* em vez de *monoandroid90*.

Este exemplo contém um caminho de arquivo correto:

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte a amostra de um [aplicativo móvel Xamarin que usa a plataforma de identidade Microsoft](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations). A tabela a seguir resume as informações relevantes no arquivo README.

| Amostra | Plataforma | Descrição |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin.iOS, Android, UWP | Um simples aplicativo Xamarin.Forms que mostra como usar o MSAL para autenticar contas pessoais da Microsoft e a Azure AD através do ponto final do Azure AD 2.0. O aplicativo também mostra como acessar o Microsoft Graph e mostra o token resultante. <br>![Topologia](media/msal-net-xamarin-android-considerations/topology.png) |
