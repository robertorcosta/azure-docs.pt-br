---
title: Considerações sobre o Xamarin Android (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre as considerações para usar o Xamarin Android com a MSAL.NET (biblioteca de autenticação da Microsoft para .NET).
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
ms.openlocfilehash: d244bbbe96bcea45da5c0860e4af52409123fb7f
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88118681"
---
# <a name="considerations-for-using-xamarin-android-with-msalnet"></a>Considerações sobre o uso do Xamarin Android com MSAL.NET
Este artigo discute o que você deve considerar ao usar o Xamarin Android com a MSAL.NET (biblioteca de autenticação da Microsoft para .NET).

## <a name="set-the-parent-activity"></a>Definir a atividade pai

No Xamarin Android, defina a atividade pai para que o token seja retornado após a interação. Este é um exemplo de código:

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

No MSAL 4,2 e posterior, você também pode definir essa funcionalidade no nível de `PublicClientApplication` . Para fazer isso, use um retorno de chamada:

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

Se você usar [CurrentActivityPlugin](https://github.com/jamesmontemagno/CurrentActivityPlugin), o `PublicClientApplication` código do Construtor será parecido com o exemplo a seguir.

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

## <a name="ensure-that-control-returns-to-msal"></a>Verifique se o controle retorna ao MSAL 
Quando a parte interativa do fluxo de autenticação terminar, verifique se o controle volta para MSAL. No Android, substitua o `OnActivityResult` método de `Activity` . Em seguida, chame o `SetAuthenticationContinuationEventArgs` método da `AuthenticationContinuationHelper` classe MSAL. 

Veja um exemplo:

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

Essa linha garante que o controle retorne ao MSAL no final da parte interativa do fluxo de autenticação.

## <a name="update-the-android-manifest"></a>Atualizar o manifesto do Android
O arquivo de *AndroidManifest.xml* deve conter os seguintes valores:

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

Substitua o nome do pacote que você registrou no portal do Azure para o `android:host=` valor. Substitua o hash de chave que você registrou no portal do Azure para o `android:path=` valor. O hash de assinatura *não* deve ser codificado em URL. Verifique se uma barra () à esquerda `/` é exibida no início do hash de assinatura.

Como alternativa, [crie a atividade em código](/xamarin/android/platform/android-manifest#the-basics) em vez de editar manualmente *AndroidManifest.xml*. Para criar a atividade no código, primeiro crie uma classe que inclua o `Activity` atributo e o `IntentFilter` atributo. 

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

### <a name="xamarinforms-43x-manifest"></a>Manifesto do Xamarin. Forms 4.3. X

O Xamarin. Forms 4.3. x gera código que define o `package` atributo como `com.companyname.{appName}` em *AndroidManifest.xml*. Se você usar `DataScheme` as `msal{client_id}` , talvez queira alterar o valor para corresponder ao valor do `MainActivity.cs` namespace.

## <a name="use-the-embedded-web-view-optional"></a>Usar a exibição da Web inserida (opcional)

Por padrão, o MSAL.NET usa o navegador da Web do sistema. Este navegador permite que você obtenha SSO (logon único) usando aplicativos Web e outros aplicativos. Em alguns casos raros, talvez você queira que o seu sistema use uma exibição da Web inserida. 

Este exemplo de código mostra como configurar uma exibição da Web inserida:

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

Para obter mais informações, consulte [usar navegadores da Web para](msal-net-web-browsers.md) [Considerações sobre o navegador do sistema MSAL.net e Xamarin Android](msal-net-system-browser-android-considerations.md).


## <a name="troubleshoot"></a>Solucionar problemas
Você pode criar um novo aplicativo Xamarin. Forms e adicionar uma referência ao pacote NuGet MSAL.NET.
Mas você poderá ter problemas de compilação se atualizar um aplicativo Xamarin. Forms existente para o MSAL.NET Preview 1.1.2 ou posterior.

Para solucionar problemas de compilação:

- Atualize o pacote NuGet do MSAL.NET existente para o MSAL.NET Preview 1.1.2 ou posterior.
- Verifique se o Xamarin. Forms foi atualizado automaticamente para a versão 2.5.0.122203. Se necessário, atualize Xamarin. Forms para esta versão.
- Verifique se o Xamarin. Android. support. v4 foi atualizado automaticamente para a versão 25.4.0.2. Se necessário, atualize para a versão 25.4.0.2.
- Verifique se todos os pacotes Xamarin. Android. support são de destino da versão 25.4.0.2.
- Limpar ou recompilar o aplicativo.
- No Visual Studio, tente definir o número máximo de compilações de projetos paralelas como 1. Para fazer isso, selecione **Opções**  >  **projetos e soluções**  >  **Compilar e executar**o  >  **número máximo de compilações de projetos paralelos**.
- Se você estiver criando a partir da linha de comando e o comando usar `/m` , tente remover esse elemento do comando.

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>Erro: o nome AuthenticationContinuationHelper não existe no contexto atual

Se um erro indicar que `AuthenticationContinuationHelper` não existe no contexto atual, o Visual Studio poderá ter atualizado incorretamente o arquivo Android. csproj *. Às vezes, o *\<HintPath>* caminho do arquivo contém incorretamente *netstandard13* em vez de *monoandroid90*.

Este exemplo contém um caminho de arquivo correto:

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte o exemplo de um [aplicativo do Xamarin Mobile que usa a plataforma de identidade da Microsoft](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations). A tabela a seguir resume as informações relevantes no arquivo LEIAme.

| Amostra | Plataforma | Descrição |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin. iOS, Android, UWP | Um aplicativo Xamarin. Forms simples que mostra como usar o MSAL para autenticar contas pessoais da Microsoft e o Azure AD por meio do ponto de extremidade 2,0 do Azure AD. O aplicativo também mostra como acessar Microsoft Graph e mostra o token resultante. <br>![Topologia](media/msal-net-xamarin-android-considerations/topology.png) |