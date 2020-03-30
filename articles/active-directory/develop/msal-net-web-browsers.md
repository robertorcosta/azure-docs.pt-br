---
title: Usando navegadores web (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre considerações específicas ao usar o Xamarin Android com a Biblioteca de Autenticação microsoft para .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: ed1f47ae99f6346a932d0fe94be7586dc25a672f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262731"
---
# <a name="using-web-browsers-msalnet"></a>Usando navegadores da Web (MSAL.NET)

Os navegadores da Web são necessários para autenticação interativa. Por padrão, MSAL.NET suporta o [navegador web](#system-web-browser-on-xamarinios-xamarinandroid) do sistema em Xamarin.iOS e Xamarin.Android. Mas [você também pode habilitar o navegador Da Web incorporado](#enable-embedded-webviews-on-ios-and-android) dependendo de suas necessidades (UX, necessidade de único login (SSO), segurança) nos aplicativos [Xamarin.iOS](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios) e [Xamarin.Android.](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) E você pode até [mesmo escolher dinamicamente](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) qual navegador da Web usar com base na presença do Chrome ou de um navegador que suporta guias personalizadas do Chrome no Android. MSAL.NET só suporta o navegador do sistema em aplicativos de desktop .NET Core.

## <a name="web-browsers-in-msalnet"></a>Navegadores da Web em MSAL.NET

### <a name="interaction-happens-in-a-web-browser"></a>A interação acontece em um navegador da Web

É importante entender que ao adquirir um token de forma interativa, o conteúdo da caixa de diálogo não é fornecido pela biblioteca, mas pelo STS (Security Token Service). O ponto final de autenticação envia de volta alguns HTML e JavaScript que controlam a interação, que é renderizada em um navegador da Web ou controle web. Permitir que o STS manuseie a interação HTML tem muitas vantagens:

- A senha (se uma foi digitada) nunca é armazenada pelo aplicativo, nem pela biblioteca de autenticação.
- Habilita redirecionamentos para outros provedores de identidade (por exemplo, login com uma conta de escola de trabalho ou uma conta pessoal com a MSAL, ou com uma conta social com o Azure AD B2C).
- Permite que o STS controle o Acesso Condicional, por exemplo, fazendo com que o usuário faça a autenticação de múltiplos fatores (MFA) durante a fase de autenticação (inserindo um pino do Windows Hello, ou sendo chamado em seu telefone, ou em um aplicativo de autenticação em seu telefone). Nos casos em que a autenticação multifatorial necessária ainda não está configurada, o usuário pode configurá-lo bem a tempo na mesma caixa de diálogo.  O usuário digita seu número de telefone celular e é orientado a instalar um aplicativo de autenticação e escanear uma tag QR para adicionar sua conta. Essa interação orientada pelo servidor é uma ótima experiência!
- Permite que o usuário altere sua senha nesta mesma caixa de diálogo quando a senha expirar (fornecendo campos adicionais para a senha antiga e a nova senha).
- Habilita a marca do inquilino, ou o aplicativo (imagens) controlado pelo administrador/proprietário do inquilino Azure AD.
- Permite que os usuários concordem em permitir que o aplicativo acesse recursos/escopos em seu nome logo após a autenticação.

### <a name="embedded-vs-system-web-ui"></a>Embedded vs System Web UI

MSAL.NET é uma biblioteca multi-framework e tem código específico de estrutura para hospedar um navegador em um controle de interface do usuário (por exemplo, no .Net Classic ele usa WinForms, no Xamarin ele usa controles móveis nativos etc.). Este controle `embedded` é chamado web UI. Alternativamente, MSAL.NET também é capaz de iniciar o navegador do sistema Operacional.

Geralmente, é recomendável que você use o padrão da plataforma, e este é tipicamente o navegador do sistema. O navegador do sistema é melhor em lembrar os usuários que já fizeram login antes. Se você precisa mudar esse comportamento, use`WithUseEmbeddedWebView(bool)`

### <a name="at-a-glance"></a>Visão rápida

| Framework        | Inserida | Sistema | Padrão |
| ------------- |-------------| -----| ----- |
| .NET Classic     | Sim | Sim. | Inserida |
| .NET Core     | Não | Sim. | Sistema |
| .NET Standard | Não | Sim. | Sistema |
| UWP | Sim | Não | Inserida |
| Xamarin.Android | Sim | Sim  | Sistema |
| Xamarin.iOS | Sim | Sim  | Sistema |
| Xamarin.Mac| Sim | Não | Inserida |

^ Requerhttp://localhost" redirecionar uri

## <a name="system-web-browser-on-xamarinios-xamarinandroid"></a>Navegador web do sistema em Xamarin.iOS, Xamarin.Android

Por padrão, MSAL.NET suporta o navegador web do sistema em Xamarin.iOS, Xamarin.Android e .NET Core. Para todas as plataformas que fornecem interface do usuário (ou seja, não .NET Core), uma caixa de diálogo é fornecida pela biblioteca incorporando um controle do navegador da Web. MSAL.NET também usa uma visualização web incorporada para o .NET Desktop e WAB para a plataforma UWP. No entanto, ele aproveita por padrão o **navegador web** do sistema para aplicações Xamarin iOS e Xamarin Android. No iOS, ele ainda escolhe a visualização web para usar dependendo da versão do sistema operacional (iOS12, iOS11 e anterior).

O uso do navegador do sistema tem a vantagem significativa de compartilhar o estado SSO com outras aplicações e com aplicações web sem precisar de um corretor (Portal da Empresa/Autenticador). O navegador do sistema foi usado, por padrão, em MSAL.NET para as plataformas Xamarin iOS e Xamarin Android porque, nessas plataformas, o navegador do sistema ocupa toda a tela, e a experiência do usuário é melhor. A visualização da web do sistema não é distinguível de uma caixa de diálogo. No iOS, porém, o usuário pode ter que dar consentimento para que o navegador ligue de volta para o aplicativo, o que pode ser irritante.

## <a name="system-browser-experience-on-net-core"></a>Experiência do navegador do sistema no .NET Core

No .NET Core, MSAL.NET iniciará o navegador do sistema como um processo separado. MSAL.NET não tem controle sobre este navegador, mas uma vez que o usuário termina a autenticação, a página da Web é redirecionada de tal forma que MSAL.NET pode interceptar o Uri.

Você também pode configurar aplicativos escritos para .NET Classic para usar este navegador, especificando

```csharp
await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
```

MSAL.NET não pode detectar se o usuário navega para longe ou simplesmente fecha o navegador. Os aplicativos que usam essa técnica são `CancellationToken`incentivados a definir um tempo (via ). Recomendamos um intervalo de pelo menos alguns minutos, para levar em conta os casos em que o usuário é solicitado a alterar a senha ou realizar a autenticação multifatorial.

### <a name="how-to-use-the-default-os-browser"></a>Como usar o navegador padrão do SISTEMA OPERACIONAL

MSAL.NET precisa ouvir `http://localhost:port` e interceptar o código que o AAD envia quando o usuário é feito autenticando (Consulte [código de autorização](v2-oauth2-auth-code-flow.md) para detalhes)

Para ativar o navegador do sistema:

1. Durante o registro `http://localhost` do aplicativo, configure-se como um uri de redirecionamento (atualmente não suportado pelo B2C)
2. Quando você constrói seu PublicClientApplication, especifique este uri de redirecionamento:

```csharp
IPublicClientApplication pca = PublicClientApplicationBuilder
                            .Create("<CLIENT_ID>")
                             // or use a known port if you wish "http://localhost:1234"
                            .WithRedirectUri("http://localhost")  
                            .Build();
```

> [!Note]
> Se você `http://localhost`configurar, internamente MSAL.NET encontrará uma porta aberta aleatória e a usará.

### <a name="linux-and-mac"></a>Linux e MAC

No Linux, MSAL.NET abrirá o navegador do Sistema Operacional padrão usando a ferramenta xdg-open. Para solucionar problemas, execute a ferramenta a partir de um terminal, por exemplo,`xdg-open "https://www.bing.com"`  
No Mac, o navegador é aberto invocando`open <url>`

### <a name="customizing-the-experience"></a>Personalizando a experiência

> [!NOTE]
> A personalização está disponível em MSAL.NET 4.1.0 ou posterior.

MSAL.NET é capaz de responder com uma mensagem HTTP quando um token é recebido ou em caso de erro. Você pode exibir uma mensagem HTML ou redirecionar para uma url de sua escolha:

```csharp
var options = new SystemWebViewOptions() 
{
    HtmlMessageError = "<p> An error occured: {0}. Details {1}</p>",
    BrowserRedirectSuccess = new Uri("https://www.microsoft.com");
}

await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
         .WithSystemWebViewOptions(options)
         .ExecuteAsync();
```

### <a name="opening-a-specific-browser-experimental"></a>Abrindo um navegador específico (Experimental)

Você pode personalizar a maneira como MSAL.NET abre o navegador. Por exemplo, em vez de usar qualquer navegador que seja o padrão, você pode forçar a abertura de um navegador específico:

```csharp
var options = new SystemWebViewOptions() 
{
    OpenBrowserAsync = SystemWebViewOptions.OpenWithEdgeBrowserAsync
}
```

### <a name="uwp-doesnt-use-the-system-webview"></a>UWP não usa o Webview do sistema

Para aplicativos de desktop, no entanto, o lançamento de um Webview do sistema leva a uma experiência de usuário subpar, à medida que o usuário vê o navegador, onde eles já podem ter outras guias abertas. E quando a autenticação acontece, os usuários recebem uma página pedindo que fechem essa janela. Se o usuário não prestar atenção, ele pode fechar todo o processo (incluindo outras guias, que não estão relacionadas à autenticação). Aproveitar o navegador do sistema na área de trabalho também exigiria abrir portas locais e ouvi-las, o que pode exigir permissões avançadas para o aplicativo. Você, como desenvolvedor, usuário ou administrador, pode estar relutante com essa exigência.

## <a name="enable-embedded-webviews-on-ios-and-android"></a>Habilite webviews incorporados no iOS e Android

Você também pode habilitar webviews incorporados em aplicativos Xamarin.iOS e Xamarin.Android. A partir MSAL.NET visualização 2.0.0, MSAL.NET também suporta o uso da opção webview **incorporado.** Para ADAL.NET, webview incorporado é a única opção suportada.

Como um desenvolvedor que usa MSAL.NET direcionando xamarin, você pode optar por usar webviews embarcadas ou navegadores de sistema. Essa é a sua escolha, dependendo da experiência do usuário e das preocupações de segurança que você deseja segmentar.

Atualmente, MSAL.NET ainda não suporta as corretoras Android e iOS. Portanto, se você precisar fornecer o soo de assinatura única , o navegador do sistema ainda pode ser uma opção melhor. O suporte aos corretores com o navegador web incorporado está no backlog MSAL.NET.

### <a name="differences-between-embedded-webview-and-system-browser"></a>Diferenças entre webview incorporado e navegador do sistema
Existem algumas diferenças visuais entre webview incorporado e navegador de sistema em MSAL.NET.

**Login interativo com MSAL.NET usando o Webview incorporado:**

![lista](media/msal-net-web-browsers/embedded-webview.png)

**Login interativo com MSAL.NET usando o Navegador do Sistema:**

![Navegador de sistema](media/msal-net-web-browsers/system-browser.png)

### <a name="developer-options"></a>Opções de desenvolvedor

Como desenvolvedor usando MSAL.NET, você tem várias opções para exibir a caixa de diálogo interativa do STS:

- **Navegador do sistema.** O navegador do sistema é definido por padrão na biblioteca. Se usar o Android, leia [os navegadores do sistema](msal-net-system-browser-android-considerations.md) para obter informações específicas sobre quais navegadores são suportados para autenticação. Ao usar o navegador do sistema no Android, recomendamos que o dispositivo tenha um navegador que suporte as guias personalizadas do Chrome.  Caso contrário, a autenticação pode falhar.
- **Webview incorporado.** Para usar apenas webview incorporado `AcquireTokenInteractively` em MSAL.NET, o construtor de parâmetros contém um `WithUseEmbeddedWebView()` método.

    iOS

    ```csharp
    AuthenticationResult authResult;
    authResult = app.AcquireTokenInteractively(scopes)
                    .WithUseEmbeddedWebView(useEmbeddedWebview)
                    .ExecuteAsync();
    ```

    Android:

    ```csharp
    authResult = app.AcquireTokenInteractively(scopes)
                .WithParentActivityOrWindow(activity)
                .WithUseEmbeddedWebView(useEmbeddedWebview)
                .ExecuteAsync();
    ```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinios"></a>Escolhendo entre navegador web incorporado ou navegador de sistema no Xamarin.iOS

No seu aplicativo para `AppDelegate.cs` iOS, `ParentWindow` você `null`pode inicializar o to . Não é usado no iOS

```csharp
App.ParentWindow = null; // no UI parent on iOS
```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid"></a>Escolhendo entre navegador web incorporado ou navegador de sistema no Xamarin.Android

No seu aplicativo `MainActivity.cs` para Android, você pode definir a atividade pai, para que o resultado da autenticação volte a ele:

```csharp
 App.ParentWindow = this;
```

Em seguida, no: `MainPage.xaml.cs`

```csharp
authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(true)
                      .ExecuteAsync();
```

#### <a name="detecting-the-presence-of-custom-tabs-on-xamarinandroid"></a>Detectando a presença de guias personalizadas no Xamarin.Android

Se você quiser usar o navegador web do sistema para ativar o SSO com os aplicativos em execução no navegador, mas está preocupado com `IsSystemWebViewAvailable()` a `IPublicClientApplication`experiência do usuário para dispositivos Android não ter um navegador com suporte a guias personalizadas, você tem a opção de decidir chamando o método em . Esse método `true` retorna se o PackageManager `false` detectar guias personalizadas e se elas não forem detectadas no dispositivo.

Com base no valor devolvido por este método e nos seus requisitos, você pode tomar uma decisão:

- Você pode retornar uma mensagem de erro personalizada para o usuário. Por exemplo: "Por favor, instale o Chrome para continuar com a autenticação" -OR-
- Você pode voltar para a opção webview incorporado e iniciar a ui como uma webview incorporada.

O código abaixo mostra a opção webview incorporado:

```csharp
bool useSystemBrowser = app.IsSystemWebviewAvailable();

authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(!useSystemBrowser)
                      .ExecuteAsync();
```

#### <a name="net-core-doesnt-support-interactive-authentication-with-an-embedded-browser"></a>.NET Core não suporta autenticação interativa com um navegador incorporado

Para o .NET Core, a aquisição de tokens de forma interativa só está disponível através do navegador web do sistema, não com visualizações incorporadas da Web. De fato, o .NET Core ainda não fornece ui.
Se você quiser personalizar a experiência de navegação com o navegador da Web do sistema, você pode implementar a interface [IWithCustomUI](scenario-desktop-acquire-token.md#withcustomwebui) e até mesmo fornecer o seu próprio navegador.
