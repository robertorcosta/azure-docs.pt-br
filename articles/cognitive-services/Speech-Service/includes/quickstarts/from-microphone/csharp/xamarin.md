---
title: 'Início Rápido: Reconhecer a fala de um microfone, C# (Xamarin) – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: Neste artigo, você criará um aplicativo C# Xamarin multiplataforma para o UWP (Plataforma Universal do Windows), o Android e o iOS usando o SDK de Fala dos Serviços Cognitivos. Você converterá a fala em texto em tempo real por meio do microfone do dispositivo ou do simulador. O aplicativo é criado com o Pacote NuGet do SDK de Fala e o Microsoft Visual Studio 2019.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: ecfdc74c72284da97ebf3107140911e666a3a12d
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818849"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

> [!div class="checklist"]
> * [Criar um Recurso de Fala do Azure](../../../../get-started.md)
> * [Configurar seu ambiente de desenvolvimento](../../../../quickstarts/setup-platform.md?tabs=xamarin)
> * [Criar um projeto de amostra vazio](../../../../quickstarts/create-project.md?tabs=xamarin)
> * Verificar se você tem acesso a um microfone para captura de áudio

Se você já fez isso, ótimo. Vamos continuar.

## <a name="add-sample-code-for-the-common-helloworld-project"></a>Adicionar um código de exemplo para o projeto helloworld comum

O projeto helloworld comum contém implementações independentes de plataforma para o aplicativo multiplataforma. Agora adicione o código XAML que define a interface do usuário do aplicativo e adicione a implementação code-behind do C#.

1. No **Gerenciador de Soluções**, no projeto helloworld comum, abra `MainPage.xaml`.

1. Na exibição XAML do designer, insira o seguinte snippet XAML na marca **Grade** entre `<StackLayout>` e `</StackLayout>`:

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml)]

1. No **Gerenciador de Soluções**, abra o arquivo de origem code-behind `MainPage.xaml.cs`. Ele está agrupado em `MainPage.xaml`.

1. Substitua todo o código nele pelo snippet a seguir:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml.cs)]

1. No manipulador `OnRecognitionButtonClicked` do arquivo de origem, localize a cadeia de caracteres `YourSubscriptionKey` e substitua-a por sua chave de assinatura.


1. No manipulador `OnRecognitionButtonClicked`, localize a cadeia de caracteres `YourServiceRegion` e substitua-a pela [região](~/articles/cognitive-services/Speech-Service/regions.md) associada à assinatura. (Por exemplo, use `westus` para a assinatura de avaliação gratuita.)

1. Em seguida, você precisará criar um [Serviço do Xamarin](https://docs.microsoft.com/xamarin/android/app-fundamentals/services/creating-a-service/), que é usado para consultar permissões do microfone de projetos de diferentes plataformas, como UWP, Android e iOS. Para fazer isso, adicione uma nova pasta chamada *Serviços* ao projeto helloworld e crie um arquivo de origem C# nele. Clique com o botão direito do mouse na pasta *Serviços* e selecione **Adicionar** > **Novo Item** > **Arquivo de Código**. Renomeie o arquivo `IMicrophoneService.cs` e coloque todo o código do seguinte snippet nesse arquivo:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/Services/IMicrophoneService.cs)]

#### <a name="androidtabx-android"></a>[Android](#tab/x-android)
## <a name="add-sample-code-for-the-helloworldandroid-project"></a>Adicionar código de exemplo para o projeto `helloworld.Android`

Agora adicione o código C# que define a parte do aplicativo específica do Android.

1. No **Gerenciador de Soluções**, no projeto helloworld.Android, abra `MainActivity.cs`.

1. Substitua todo o código nele pelo snippet a seguir:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/MainActivity.cs)]

1. Em seguida, adicione a implementação específica do Android para `MicrophoneService` criando a pasta *Serviços* no projeto helloworld.Android. Depois disso, crie um arquivo de origem C# nele. Renomeie o arquivo `MicrophoneService.cs`. Copie e cole o seguinte snippet de código nesse arquivo:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/Services/MicrophoneService.cs)]

1. Depois disso, abra `AndroidManifest.xml` na pasta *Propriedades*. Adicione a seguinte configuração de permissão de usos para o microfone entre `<manifest>` e `</manifest>`:

   ```xml
   <uses-permission android:name="android.permission.RECORD_AUDIO" />
   ```
   
#### <a name="iostabios"></a>[iOS](#tab/ios)
## <a name="add-sample-code-for-the-helloworldios-project"></a>Adicionar código de exemplo para o projeto `helloworld.iOS`

Agora adicione o código C# que define a parte do aplicativo específica do iOS. Além disso, crie configurações específicas do dispositivo Apple para o projeto helloworld.iOS.

1. No **Gerenciador de Soluções**, no projeto helloworld.iOS, abra `AppDelegate.cs`.

1. Substitua todo o código nele pelo snippet a seguir:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/AppDelegate.cs)]

1. Em seguida, adicione a implementação específica do iOS para `MicrophoneService` criando a pasta *Serviços* no projeto helloworld.iOS. Depois disso, crie um arquivo de origem C# nele. Renomeie o arquivo `MicrophoneService.cs`. Copie e cole o seguinte snippet de código nesse arquivo:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/Services/MicrophoneService.cs)]

1. Abra `Info.plist` no projeto helloworld.iOS no editor de texto. Adicione o seguinte par chave-valor na seção dict:

   <key>NSMicrophoneUsageDescription</key>
   <string>Este aplicativo de exemplo exige acesso ao microfone</string>

   > [!NOTE]
   > Se estiver criando o aplicativo para um dispositivo iPhone, verifique se `Bundle Identifier` corresponde à ID do aplicativo do perfil de provisionamento do dispositivo. Caso contrário, o build falhará. Com o iPhoneSimulator, você pode mantê-lo como está.

1. Se estiver criando um aplicativo em um computador Windows, estabeleça uma conexão com o dispositivo Mac para a criação por meio de **Ferramentas** > **iOS** > **Emparelhar com Mac**. Siga o assistente de instruções fornecido pelo Visual Studio para habilitar a conexão com o dispositivo Mac.

#### <a name="uwptabhelloworlduwp"></a>[UWP (Plataforma Universal do Windows)](#tab/helloworlduwp)
## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Adicionar código de exemplo para o projeto `helloworld.UWP`

## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Adicionar um código de exemplo ao projeto helloworld.UWP

Agora adicione o código C# que define a parte do aplicativo específica do UWP.

1. No **Gerenciador de Soluções**, no projeto helloworld.UWP, abra `MainPage.xaml.cs`.

1. Substitua todo o código nele pelo snippet a seguir:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/MainPage.xaml.cs)]

1. Em seguida, adicione a implementação específica do UWP para `MicrophoneService` criando a pasta *Serviços* no projeto helloworld.UWP. Depois disso, crie um arquivo de origem C# nele. Renomeie o arquivo `MicrophoneService.cs`. Copie e cole o seguinte snippet de código nesse arquivo:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/Services/MicrophoneService.cs)]

1. Em seguida, clique duas vezes no arquivo `Package.appxmanifest` no projeto helloworld.UWP dentro do Visual Studio. Em **Funcionalidades**, verifique se **Microfone** está selecionado e salve o arquivo.

1. Em seguida, clique duas vezes no arquivo `Package.appxmanifest` no projeto `helloworld.UWP` dentro do Visual Studio, marque a opção **Funcionalidades** > **Microfone** e salve o arquivo.
   > Observação: Caso você veja o aviso: O arquivo de certificado não existe: helloworld.UWP_TemporaryKey.pfx, verifique o exemplo de [conversão de fala em texto](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp) para obter mais informações.

1. Na barra de menus, selecione **Arquivo** > **Salvar Tudo** para salvar as alterações.

## <a name="build-and-run-the-uwp-application"></a>Compilar e executar o aplicativo UWP

1. Defina helloworld.UWP como um projeto de inicialização. Clique com o botão direito do mouse no projeto helloworld.UWP e selecione **Compilar** para compilar o aplicativo.

1. Selecione **Depurar** > **Iniciar Depuração** (ou selecione **F5**) para iniciar o aplicativo. A janela **helloworld** é exibida.

   ![Exemplo de aplicativo de reconhecimento de fala da UWP em C# – início rápido](../../../../media/sdk/qs-csharp-xamarin-helloworld-uwp-window.png)

1. Selecione **Habilitar Microfone**. Quando a solicitação de permissão de acesso for exibida, selecione **Sim**.

   ![Solicitação de permissão de acesso ao microfone](../../../../media/sdk/qs-csharp-xamarin-uwp-access-prompt.png)

1. Selecione **Iniciar reconhecimento de fala** e fale uma frase ou uma oração em inglês no microfone do dispositivo. Sua fala será transmitida para o serviço de Fala e transcrita para texto, que será exibida na janela.

   ![Interface do usuário do reconhecimento de fala](../../../../media/sdk/qs-csharp-xamarin-uwp-ui-result.png)
* * *

## <a name="build-and-run-the-android-and-ios-applications"></a>Criar e executar os aplicativos Android e iOS

A criação e a execução de aplicativos Android e iOS no dispositivo ou no simulador ocorrem de forma semelhante àquelas do UWP. Verifique se todos os SDKs estão instalados corretamente conforme exigido na seção "Pré-requisitos" deste artigo.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [footer](./footer.md)]
