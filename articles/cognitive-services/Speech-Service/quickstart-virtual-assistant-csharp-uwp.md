---
title: 'Início Rápido: Assistente virtual personalizado que tem como prioridade o uso de voz (versão prévia), C# (UWP) – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: Neste artigo, você criará um aplicativo UWP (Plataforma Universal do Windows) em C# usando o SDK (Software Development Kit) de Fala dos Serviços Cognitivos. Você conectará seu aplicativo cliente a um bot do Bot Framework criado anteriormente, configurado para usar o canal de Fala do Direct Line. O aplicativo é criado com o Pacote NuGet do SDK de Fala e o Microsoft Visual Studio 2019.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/19/2019
ms.author: travisw
ms.openlocfilehash: c676e98eb812a31d6fb8d7cc0f58929f803c868e
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/07/2019
ms.locfileid: "70382141"
---
# <a name="quickstart-create-a-voice-first-virtual-assistant-with-the-speech-sdk-uwp"></a>Início Rápido: Criar um assistente virtual que tem como prioridade o uso de voz com o SDK de Fala, UWP

Também há inícios rápidos disponíveis para [reconhecimento de fala](quickstart-csharp-uwp.md), [síntese de fala](quickstart-text-to-speech-csharp-uwp.md) e [tradução de fala](quickstart-translate-speech-uwp.md).

Neste artigo, você desenvolverá um aplicativo UWP (Plataforma Universal do Windows) em C# usando o [SDK de Fala](speech-sdk.md). O programa se conectará a um bot criado e configurado anteriormente para proporcionar uma experiência de assistente virtual que tem como prioridade o uso de voz no aplicativo cliente. O aplicativo é criado com o [Pacote NuGet do SDK de Fala](https://aka.ms/csspeech/nuget) e o Microsoft Visual Studio 2019 (qualquer edição).

> [!NOTE]
> A Plataforma Universal do Windows permite desenvolver aplicativos que executam em qualquer dispositivo com suporte para Windows 10, incluindo PCs, Xbox, Surface Hub e outros dispositivos.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Uma chave de assinatura do Azure para os Serviços de Fala. [Obtenha uma gratuitamente](get-started.md) ou crie-a no [portal do Azure](https://portal.azure.com).
* Um bot criado anteriormente, configurado com o [canal de Fala do Direct Line](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

  > [!NOTE]
  > A Direct Line Speech (Versão prévia) está disponível atualmente em um subconjunto de regiões dos Serviços de Fala. Consulte [a lista de regiões com suporte a assistentes virtuais com prioridade no uso de voz](regions.md#voice-first-virtual-assistants) e implante seus recursos em uma dessas regiões.

## <a name="optional-get-started-fast"></a>Opcional: Introdução rápida

Este Início Rápido descreverá, passo a passo, como criar um aplicativo cliente para conectá-lo ao bot habilitado para fala. Se você preferir se aprofundar, o código-fonte completo e pronto para compilação usado neste início rápido estará disponível nos [Exemplos de SDK de Fala](https://aka.ms/csspeech/samples) na pasta `quickstart`.

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

Agora, adicione o código XAML que define a interface do usuário do aplicativo e adicione a implementação code-behind em C#.

### <a name="xaml-code"></a>Código XAML

Primeiro, você criará a interface do usuário do aplicativo adicionando o código XAML:

1. No **Gerenciador de Soluções**, abra `MainPage.xaml`.

1. Na exibição XAML do designer, substitua todo o conteúdo pelo seguinte snippet de código:

    ```xml
    <Page
        x:Class="helloworld.MainPage"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:local="using:helloworld"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        mc:Ignorable="d"
        Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

        <Grid>
            <StackPanel Orientation="Vertical" HorizontalAlignment="Center"  
                        Margin="20,50,0,0" VerticalAlignment="Center" Width="800">
                <Button x:Name="EnableMicrophoneButton" Content="Enable Microphone"  
                        Margin="0,0,10,0" Click="EnableMicrophone_ButtonClicked" 
                        Height="35"/>
                <Button x:Name="ListenButton" Content="Talk to your bot" 
                        Margin="0,10,10,0" Click="ListenButton_ButtonClicked" 
                        Height="35"/>
                <StackPanel x:Name="StatusPanel" Orientation="Vertical" 
                            RelativePanel.AlignBottomWithPanel="True" 
                            RelativePanel.AlignRightWithPanel="True" 
                            RelativePanel.AlignLeftWithPanel="True">
                    <TextBlock x:Name="StatusLabel" Margin="0,10,10,0" 
                               TextWrapping="Wrap" Text="Status:" FontSize="20"/>
                    <Border x:Name="StatusBorder" Margin="0,0,0,0">
                        <ScrollViewer VerticalScrollMode="Auto"  
                                      VerticalScrollBarVisibility="Auto" MaxHeight="200">
                            <!-- Use LiveSetting to enable screen readers to announce 
                                 the status update. -->
                            <TextBlock 
                                x:Name="StatusBlock" FontWeight="Bold" 
                                AutomationProperties.LiveSetting="Assertive"
                                MaxWidth="{Binding ElementName=Splitter, Path=ActualWidth}" 
                                Margin="10,10,10,20" TextWrapping="Wrap"  />
                        </ScrollViewer>
                    </Border>
                </StackPanel>
            </StackPanel>
            <MediaElement x:Name="mediaElement"/>
        </Grid>
    </Page>
    ```

O modo de exibição de Design é atualizado para mostrar a interface do usuário do aplicativo.

### <a name="c-code-behind-source"></a>Origem code-behind em C#

Em seguida, você adiciona a origem code-behind para que o aplicativo funcione conforme o esperado. A origem code-behind inclui:

- `using` instruções para os namespaces `Speech` e `Speech.Dialog`
- Uma implementação simples para garantir o acesso ao microfone, conectado a um manipulador de botão
- Auxiliares básicos de interface do usuário para apresentar erros e mensagens no aplicativo
- Um ponto inicial para o caminho do código de inicialização que será populado posteriormente
- Um auxiliar para reproduzir a conversão de texto em fala (sem suporte para streaming)
- Um manipulador de botão vazio para iniciar a escuta que será populado mais tarde

Para adicionar a origem code-behind, execute as seguintes etapas:

1. No **Gerenciador de Soluções**, abra o arquivo de origem code-behind `MainPage.xaml.cs`. (Ele está agrupado em `MainPage.xaml`.)

1. Substitua o conteúdo do arquivo pelo snippet de código a seguir:

    ```csharp
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.CognitiveServices.Speech.Dialog;
    using System;
    using System.Diagnostics;
    using System.IO;
    using System.Text;
    using Windows.Foundation;
    using Windows.Storage.Streams;
    using Windows.UI.Xaml;
    using Windows.UI.Xaml.Controls;
    using Windows.UI.Xaml.Media;

    namespace helloworld
    {
        public sealed partial class MainPage : Page
        {
            private DialogServiceConnector connector;

            private enum NotifyType
            {
                StatusMessage,
                ErrorMessage
            };

            public MainPage()
            {
                this.InitializeComponent();
            }

            private async void EnableMicrophone_ButtonClicked(
                object sender, RoutedEventArgs e)
            {
                bool isMicAvailable = true;
                try
                {
                    var mediaCapture = new Windows.Media.Capture.MediaCapture();
                    var settings = 
                        new Windows.Media.Capture.MediaCaptureInitializationSettings();
                    settings.StreamingCaptureMode = 
                        Windows.Media.Capture.StreamingCaptureMode.Audio;
                    await mediaCapture.InitializeAsync(settings);
                }
                catch (Exception)
                {
                    isMicAvailable = false;
                }
                if (!isMicAvailable)
                {
                    await Windows.System.Launcher.LaunchUriAsync(
                        new Uri("ms-settings:privacy-microphone"));
                }
                else
                {
                    NotifyUser("Microphone was enabled", NotifyType.StatusMessage);
                }
            }

            private void NotifyUser(
                string strMessage, NotifyType type = NotifyType.StatusMessage)
            {
                // If called from the UI thread, then update immediately.
                // Otherwise, schedule a task on the UI thread to perform the update.
                if (Dispatcher.HasThreadAccess)
                {
                    UpdateStatus(strMessage, type);
                }
                else
                {
                    var task = Dispatcher.RunAsync(
                        Windows.UI.Core.CoreDispatcherPriority.Normal, 
                        () => UpdateStatus(strMessage, type));
                }
            }

            private void UpdateStatus(string strMessage, NotifyType type)
            {
                switch (type)
                {
                    case NotifyType.StatusMessage:
                        StatusBorder.Background = new SolidColorBrush(
                            Windows.UI.Colors.Green);
                        break;
                    case NotifyType.ErrorMessage:
                        StatusBorder.Background = new SolidColorBrush(
                            Windows.UI.Colors.Red);
                        break;
                }
                StatusBlock.Text += string.IsNullOrEmpty(StatusBlock.Text) 
                    ? strMessage : "\n" + strMessage;

                if (!string.IsNullOrEmpty(StatusBlock.Text))
                {
                    StatusBorder.Visibility = Visibility.Visible;
                    StatusPanel.Visibility = Visibility.Visible;
                }
                else
                {
                    StatusBorder.Visibility = Visibility.Collapsed;
                    StatusPanel.Visibility = Visibility.Collapsed;
                }
                // Raise an event if necessary to enable a screen reader 
                // to announce the status update.
                var peer = Windows.UI.Xaml.Automation.Peers.FrameworkElementAutomationPeer.FromElement(StatusBlock);
                if (peer != null)
                {
                    peer.RaiseAutomationEvent(
                        Windows.UI.Xaml.Automation.Peers.AutomationEvents.LiveRegionChanged);
                }
            }

            // Waits for and accumulates all audio associated with a given 
            // PullAudioOutputStream and then plays it to the MediaElement. Long spoken 
            // audio will create extra latency and a streaming playback solution 
            // (that plays audio while it continues to be received) should be used -- 
            // see the samples for examples of this.
            private void SynchronouslyPlayActivityAudio(
                PullAudioOutputStream activityAudio)
            {
                var playbackStreamWithHeader = new MemoryStream();
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("RIFF"), 0, 4); // ChunkID
                playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // ChunkSize: max
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("WAVE"), 0, 4); // Format
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("fmt "), 0, 4); // Subchunk1ID
                playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 4); // Subchunk1Size: PCM
                playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // AudioFormat: PCM
                playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // NumChannels: mono
                playbackStreamWithHeader.Write(BitConverter.GetBytes(16000), 0, 4); // SampleRate: 16kHz
                playbackStreamWithHeader.Write(BitConverter.GetBytes(32000), 0, 4); // ByteRate
                playbackStreamWithHeader.Write(BitConverter.GetBytes(2), 0, 2); // BlockAlign
                playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 2); // BitsPerSample: 16-bit
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("data"), 0, 4); // Subchunk2ID
                playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // Subchunk2Size

                byte[] pullBuffer = new byte[2056];

                uint lastRead = 0;
                do
                {
                    lastRead = activityAudio.Read(pullBuffer);
                    playbackStreamWithHeader.Write(pullBuffer, 0, (int)lastRead);
                }
                while (lastRead == pullBuffer.Length);

                var task = Dispatcher.RunAsync(
                    Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
                {
                    mediaElement.SetSource(
                        playbackStreamWithHeader.AsRandomAccessStream(), "audio/wav");
                    mediaElement.Play();
                });
            }

            private void InitializeDialogServiceConnector()
            {
                // New code will go here
            }

            private async void ListenButton_ButtonClicked(
                object sender, RoutedEventArgs e)
            {
                // New code will go here
            }
        }
    }
    ```

1. Adicione o snippet de código a seguir ao corpo do método de `InitializeDialogServiceConnector`. Esse código criar o `DialogServiceConnector` com as informações de assinatura.

    ```csharp
    // create a DialogServiceConfig by providing a bot secret key 
    // and Cognitive Services subscription key
    // the RecoLanguage property is optional (default en-US); 
    // note that only en-US is supported in Preview
    const string channelSecret = "YourChannelSecret"; // Your channel secret
    const string speechSubscriptionKey = "YourSpeechSubscriptionKey"; // Your subscription key

    // Your subscription service region. 
    // Note: only a subset of regions are currently supported
    const string region = "YourServiceRegion"; 

    var botConfig = DialogServiceConfig.FromBotSecret(
        channelSecret, speechSubscriptionKey, region);
    botConfig.SetProperty(PropertyId.SpeechServiceConnection_RecoLanguage, "en-US");
    connector = new DialogServiceConnector(botConfig);
    ```

   > [!NOTE]
   > A Direct Line Speech (Versão prévia) está disponível atualmente em um subconjunto de regiões dos Serviços de Fala. Consulte [a lista de regiões com suporte a assistentes virtuais com prioridade no uso de voz](regions.md#voice-first-virtual-assistants) e implante seus recursos em uma dessas regiões.

   > [!NOTE]
   > Para obter informações sobre como configurar o bot e recuperar um segredo do canal, confira a documentação do Bot Framework para [o canal de Fala do Direct Line](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

1. Substitua as cadeias de caracteres `YourChannelSecret`, `YourSpeechSubscriptionKey` e `YourServiceRegion` por seus próprios valores para o bot, pela assinatura de fala e pela [região](regions.md).

1. Acrescente o snippet de código a seguir ao final do corpo do método de `InitializeDialogServiceConnector`. Esse código define manipuladores de eventos utilizados por `DialogServiceConnector` para comunicar suas atividades de bot, resultados de reconhecimento de fala e outras informações.

    ```csharp
    // ActivityReceived is the main way your bot will communicate with the client 
    // and uses bot framework activities
    connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
    {
        NotifyUser(
            $"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

        if (activityReceivedEventArgs.HasAudio)
        {
            SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
        }
    };

    // Canceled will be signaled when a turn is aborted or experiences an error condition
    connector.Canceled += (sender, canceledEventArgs) =>
    {
        NotifyUser($"Canceled, reason={canceledEventArgs.Reason}");
        if (canceledEventArgs.Reason == CancellationReason.Error)
        {
            NotifyUser(
                $"Error: code={canceledEventArgs.ErrorCode}, details={canceledEventArgs.ErrorDetails}");
        }
    };

    // Recognizing (not 'Recognized') will provide the intermediate recognized text 
    // while an audio stream is being processed
    connector.Recognizing += (sender, recognitionEventArgs) =>
    {
        NotifyUser($"Recognizing! in-progress text={recognitionEventArgs.Result.Text}");
    };

    // Recognized (not 'Recognizing') will provide the final recognized text 
    // once audio capture is completed
    connector.Recognized += (sender, recognitionEventArgs) =>
    {
        NotifyUser($"Final speech-to-text result: '{recognitionEventArgs.Result.Text}'");
    };

    // SessionStarted will notify when audio begins flowing to the service for a turn
    connector.SessionStarted += (sender, sessionEventArgs) =>
    {
        NotifyUser($"Now Listening! Session started, id={sessionEventArgs.SessionId}");
    };

    // SessionStopped will notify when a turn is complete and 
    // it's safe to begin listening again
    connector.SessionStopped += (sender, sessionEventArgs) =>
    {
        NotifyUser($"Listening complete. Session ended, id={sessionEventArgs.SessionId}");
    };
    ```

1. Adicione o snippet de código a seguir ao corpo do método `ListenButton_ButtonClicked` na classe `MainPage`. Esse código configura `DialogServiceConnector` para escutar, pois você já estabeleceu a configuração e registrou os manipuladores de eventos.

    ```csharp
    if (connector == null)
    {
        InitializeDialogServiceConnector();
        // Optional step to speed up first interaction: if not called, 
        // connection happens automatically on first use
        var connectTask = connector.ConnectAsync();
    }

    try
    {
        // Start sending audio to your speech-enabled bot
        var listenTask = connector.ListenOnceAsync();

        // You can also send activities to your bot as JSON strings -- 
        // Microsoft.Bot.Schema can simplify this
        string speakActivity = 
            @"{""type"":""message"",""text"":""Greeting Message"", ""speak"":""Hello there!""}";
        await connector.SendActivityAsync(speakActivity);

    }
    catch (Exception ex)
    {
        NotifyUser($"Exception: {ex.ToString()}", NotifyType.ErrorMessage);
    }
    ```

1. Na barra de menus, escolha **Arquivo** > **Salvar Tudo** para salvar suas alterações.

## <a name="build-and-run-the-application"></a>Compile e execute o aplicativo

Agora, você está pronto para criar e testar seu aplicativo.

1. Na barra de menus, escolha **Compilar** > **Compilar Solução** para compilar o aplicativo. Agora, o código deve compilar sem erros.

1. Escolha **Depurar** > **Iniciar Depuração** (ou pressione **F5**) para iniciar o aplicativo. A janela **helloworld** é exibida.

   ![Exemplo de aplicativo de assistente virtual UWP em C# – Início rápido](media/sdk/qs-virtual-assistant-uwp-helloworld-window.png)

1. Selecione **Habilitar Microfone** e, quando a solicitação de permissão de acesso for exibida, selecione **Sim**.

   ![Solicitação de permissão de acesso ao microfone](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Selecione **Falar com o bot** e fale uma frase ou uma sentença em inglês no microfone do dispositivo. Sua fala será transmitida para o canal de Fala do Direct Line e transcrita em texto, que será exibida na janela.
<!--
    ![Successful bot response](media/voice-first-virtual-assistants/quickstart-cs-uwp-bot-successful-turn.png)
-->
## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar e implantar um bot básico](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)

## <a name="see-also"></a>Consulte também

- [Sobre assistentes virtuais com prioridade no uso de voz](voice-first-virtual-assistants.md)
- [Obter gratuitamente uma chave de assinatura do Serviço de Fala](get-started.md)
- [Palavras de ativação personalizadas](speech-devices-sdk-create-kws.md)
- [Conectar a Fala do Direct Line ao seu bot](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [Explorar amostras de C# no GitHub](https://aka.ms/csspeech/samples)
