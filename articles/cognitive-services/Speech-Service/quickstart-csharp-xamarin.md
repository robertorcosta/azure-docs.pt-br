---
title: 'Início Rápido: Reconhecer fala, C# (Xamarin) – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: Neste artigo, você criará um aplicativo C# Xamarin multiplataforma para o UWP (Plataforma Universal do Windows), o Android e o iOS usando o SDK de Fala dos Serviços Cognitivos. Você converterá a fala em texto em tempo real por meio do microfone do dispositivo ou do simulador. O aplicativo é criado com o Pacote NuGet do SDK de Fala e o Microsoft Visual Studio 2019.
services: cognitive-services
author: jhakulin
manager: robch
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: jhakulin
ms.custom: ''
ms.openlocfilehash: ad1c6f7d17d1e04fcfa5b187a29dadd2fa61edd8
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675617"
---
# <a name="quickstart-recognize-speech-by-using-a-cross-platform-xamarin-app-that-uses-the-speech-sdk"></a>Início Rápido: Reconhecer uma fala usando um aplicativo Xamarin multiplataforma que usa o SDK de Fala

Também há inícios rápidos disponíveis para [conversão de fala em texto](quickstart-csharp-uwp.md), [conversão de texto em fala](quickstart-text-to-speech-csharp-uwp.md) e [tradução de fala](quickstart-translate-speech-uwp.md).

Neste artigo, você desenvolverá um aplicativo C# multiplataforma usando o Xamarin para o UWP (Plataforma Universal do Windows), o Android e o iOS que usa o [SDK de Fala](speech-sdk.md) dos Serviços Cognitivos do Azure. O programa transcreve a fala em texto em tempo real por meio do microfone do dispositivo. O aplicativo é criado com o [pacote NuGet do SDK de Fala](https://aka.ms/csspeech/nuget) e qualquer edição do Microsoft Visual Studio 2019.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Uma chave de assinatura do Azure para o Serviço de Fala. [Obtenha uma gratuitamente](get-started.md).
* Um PC Windows com Windows 10 Fall Creators Update 10.0, build 16299 ou posterior e com um microfone funcionando.
* Uma [instalação do Xamarin no Visual Studio](https://docs.microsoft.com/xamarin/get-started/installation/?pivots=windows).
* Uma [instalação do Xamarin Android no Windows](https://docs.microsoft.com/xamarin/android/get-started/installation/windows).
* Uma [instalação do Xamarin iOS no Windows](https://docs.microsoft.com/xamarin/ios/get-started/installation/windows/?pivots=windows).
* Para direcionar ao Android: 
   * Um dispositivo Android (ARM32/64, x86; API 23: Android 6.0 Marshmallow ou superior) [habilitado para desenvolvimento](https://developer.android.com/studio/debug/dev-options) com um microfone funcionando.
* Para direcionamento ao iOS: 
    * Um dispositivo iOS (ARM64) ou um simulador de iOS (x64) [habilitado para desenvolvimento](https://docs.microsoft.com/xamarin/ios/get-started/installation/device-provisioning/) com um microfone funcional.
* Para obter suporte ao build do Windows ARM64, instale as [ferramentas de build opcionais e o SDK do Windows 10 para ARM/ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/).

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-xamarin-create-proj.md)]

## <a name="add-sample-code-for-the-common-helloworld-project"></a>Adicionar um código de exemplo para o projeto helloworld comum

O projeto helloworld comum contém implementações independentes de plataforma para o aplicativo multiplataforma. Agora adicione o código XAML que define a interface do usuário do aplicativo e adicione a implementação code-behind do C#.

1. No **Gerenciador de Soluções**, no projeto helloworld comum, abra `MainPage.xaml`.

1. Na exibição XAML do designer, insira o seguinte snippet XAML na marca **Grade** entre `<StackLayout>` e `</StackLayout>`:

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/MainPage.xaml)]

1. No **Gerenciador de Soluções**, abra o arquivo de origem code-behind `MainPage.xaml.cs`. Ele está agrupado em `MainPage.xaml`.

1. Substitua todo o código nele pelo snippet a seguir:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/MainPage.xaml.cs)]

1. No manipulador `OnRecognitionButtonClicked` do arquivo de origem, localize a cadeia de caracteres `YourSubscriptionKey` e substitua-a por sua chave de assinatura.

1. No manipulador `OnRecognitionButtonClicked`, localize a cadeia de caracteres `YourServiceRegion` e substitua-a pela [região](regions.md) associada à assinatura. Por exemplo, use `westus` para a assinatura de avaliação gratuita.

1. Em seguida, você precisará criar um [Serviço do Xamarin](https://docs.microsoft.com/xamarin/android/app-fundamentals/services/creating-a-service/), que é usado para consultar permissões do microfone de projetos de diferentes plataformas, como UWP, Android e iOS. Para fazer isso, adicione uma nova pasta chamada *Serviços* ao projeto helloworld e crie um arquivo de origem C# nele. Clique com o botão direito do mouse na pasta *Serviços* e selecione **Adicionar** > **Novo Item** > **Arquivo de Código**. Renomeie o arquivo `IMicrophoneService.cs` e coloque todo o código do seguinte snippet nesse arquivo:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/Services/IMicrophoneService.cs)]

## <a name="add-sample-code-for-the-helloworldandroid-project"></a>Adicionar um código de exemplo ao projeto helloworld.Android

Agora adicione o código C# que define a parte do aplicativo específica do Android.

1. No **Gerenciador de Soluções**, no projeto helloworld.Android, abra `MainActivity.cs`.

1. Substitua todo o código nele pelo snippet a seguir:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.Android/MainActivity.cs)]

1. Em seguida, adicione a implementação específica do Android para `MicrophoneService` criando a pasta *Serviços* no projeto helloworld.Android. Depois disso, crie um arquivo de origem C# nele. Renomeie o arquivo `MicrophoneService.cs`. Copie e cole o seguinte snippet de código nesse arquivo:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.Android/Services/MicrophoneService.cs)]

1. Depois disso, abra `AndroidManifest.xml` na pasta *Propriedades*. Adicione a seguinte configuração de permissão de usos para o microfone entre `<manifest>` e `</manifest>`:

   ```xml
   <uses-permission android:name="android.permission.RECORD_AUDIO" />
   ```

## <a name="add-sample-code-for-the-helloworldios-project"></a>Adicionar um código de exemplo ao projeto helloworld.iOS

Agora adicione o código C# que define a parte do aplicativo específica do iOS. Além disso, crie configurações específicas do dispositivo Apple para o projeto helloworld.iOS.

1. No **Gerenciador de Soluções**, no projeto helloworld.iOS, abra `AppDelegate.cs`.

1. Substitua todo o código nele pelo snippet a seguir:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.iOS/AppDelegate.cs)]

1. Em seguida, adicione a implementação específica do iOS para `MicrophoneService` criando a pasta *Serviços* no projeto helloworld.iOS. Depois disso, crie um arquivo de origem C# nele. Renomeie o arquivo `MicrophoneService.cs`. Copie e cole o seguinte snippet de código nesse arquivo:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.iOS/Services/MicrophoneService.cs)]

1. Abra `Info.plist` no projeto helloworld.iOS no editor de texto. Adicione o seguinte par chave-valor na seção dict:

   <key>NSMicrophoneUsageDescription</key>
   <string>Este aplicativo de exemplo exige acesso ao microfone</string>

   > [!NOTE] 
   > Se estiver criando o aplicativo para um dispositivo iPhone, verifique se `Bundle Identifier` corresponde à ID do aplicativo do perfil de provisionamento do dispositivo. Caso contrário, o build falhará. Com o iPhoneSimulator, você pode mantê-lo como está.

1. Se estiver criando um aplicativo em um computador Windows, estabeleça uma conexão com o dispositivo Mac para a criação por meio de **Ferramentas** > **iOS** > **Emparelhar com Mac**. Siga o assistente de instruções fornecido pelo Visual Studio para habilitar a conexão com o dispositivo Mac.

## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Adicionar um código de exemplo ao projeto helloworld.UWP

Agora adicione o código C# que define a parte do aplicativo específica do UWP.

1. No **Gerenciador de Soluções**, no projeto helloworld.UWP, abra `MainPage.xaml.cs`.

1. Substitua todo o código nele pelo snippet a seguir:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.UWP/MainPage.xaml.cs)]

1. Em seguida, adicione a implementação específica do UWP para `MicrophoneService` criando a pasta *Serviços* no projeto helloworld.UWP. Depois disso, crie um arquivo de origem C# nele. Renomeie o arquivo `MicrophoneService.cs`. Copie e cole o seguinte snippet de código nesse arquivo:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.UWP/Services/MicrophoneService.cs)]

1. Em seguida, clique duas vezes no arquivo `Package.appxmanifest` no projeto helloworld.UWP dentro do Visual Studio. Em **Funcionalidades**, verifique se **Microfone** está selecionado e salve o arquivo.

   > [!NOTE] 
   > Se o aviso "O arquivo de certificado não existe: helloworld.UWP_TemporaryKey.pfx" for exibido, verifique a amostra de [conversão de fala em texto](quickstart-csharp-uwp.md) para obter mais informações.

1. Na barra de menus, selecione **Arquivo** > **Salvar Tudo** para salvar as alterações.

## <a name="build-and-run-the-uwp-application"></a>Compilar e executar o aplicativo UWP

1. Defina helloworld.UWP como um projeto de inicialização. Clique com o botão direito do mouse no projeto helloworld.UWP e selecione **Compilar** para compilar o aplicativo. 

1. Selecione **Depurar** > **Iniciar Depuração** (ou selecione **F5**) para iniciar o aplicativo. A janela **helloworld** é exibida.

   ![Exemplo de aplicativo de reconhecimento de fala da UWP em C# – início rápido](media/sdk/qs-csharp-xamarin-helloworld-uwp-window.png)

1. Selecione **Habilitar Microfone**. Quando a solicitação de permissão de acesso for exibida, selecione **Sim**.

   ![Solicitação de permissão de acesso ao microfone](media/sdk/qs-csharp-xamarin-uwp-access-prompt.png)

1. Selecione **Iniciar reconhecimento de fala** e fale uma frase ou uma oração em inglês no microfone do dispositivo. Sua fala será transmitida para o Serviço de Fala e transcrita em um texto, que será exibido na janela.

   ![Interface do usuário do reconhecimento de fala](media/sdk/qs-csharp-xamarin-uwp-ui-result.png)

## <a name="build-and-run-the-android-and-ios-applications"></a>Criar e executar os aplicativos Android e iOS

A criação e a execução de aplicativos Android e iOS no dispositivo ou no simulador ocorrem de forma semelhante àquelas do UWP. Verifique se todos os SDKs estão instalados corretamente conforme exigido na seção "Pré-requisitos" deste artigo.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar amostras de C# no GitHub](https://aka.ms/csspeech/samples)
