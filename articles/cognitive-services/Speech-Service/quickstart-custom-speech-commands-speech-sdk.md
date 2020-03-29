---
title: 'Quickstart: Conecte-se a um aplicativo de comandos personalizados com o Speech SDK - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste artigo, você criará um aplicativo cliente Speech SDK com comandos personalizados.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: 9e324af0b90f595b5b7af2a417a562efb193d854
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76156770"
---
# <a name="quickstart-connect-to-a-custom-commands-application-with-the-speech-sdk-preview"></a>Partida rápida: Conecte-se a um aplicativo de comandos personalizados com o Speech SDK (Preview)

Depois de criar um aplicativo de comandos personalizados hospedados, você pode começar a falar com ele a partir de um dispositivo cliente.

Neste artigo, você:

- Publique um aplicativo de comandos personalizados e obtenha um identificador de aplicativo (ID do aplicativo)
- Crie um aplicativo cliente usando o Speech SDK para permitir que você fale com seu aplicativo de comandos personalizados

## <a name="prerequisites"></a>Pré-requisitos

Um aplicativo de Comandos Personalizados é necessário para concluir este artigo. Se você ainda não criou um aplicativo de comandos personalizados, você pode fazê-lo nessas partidas rápidas anteriores:

- [Quickstart: Crie um comando personalizado (Preview)](./quickstart-custom-speech-commands-create-new.md)
- [Quickstart: Crie um comando personalizado com parâmetros (Visualização)](./quickstart-custom-speech-commands-create-parameters.md)

Você também vai precisar:

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
- Uma chave de assinatura do Azure para os Serviços de Fala. [Obtenha um de graça](get-started.md) ou crie-o no [portal Azure](https://portal.azure.com)

## <a name="optional-get-started-fast"></a>Opcional: Comece rápido

Este quickstart descreve, passo a passo, como fazer um aplicativo cliente para se conectar ao seu aplicativo De comandos personalizados. Se você preferir se aprofundar, o código-fonte completo e pronto para compilação usado neste início rápido estará disponível nos [Exemplos de SDK de Fala](https://aka.ms/csspeech/samples) na pasta `quickstart`.

## <a name="step-1-publish-custom-commands-application"></a>Passo 1: Publicar o aplicativo comandos personalizados

1. Abra seu [aplicativo de comandos personalizados criado anteriormente](./quickstart-custom-speech-commands-create-new.md) e selecione **Publicar**

   > [!div class="mx-imgBorder"]
   > ![aplicativo Publicar](media/custom-speech-commands/fulfill-sdk-publish-application.png)

1. Copie o ID do aplicativo da notificação de publicação para uso posterior

## <a name="step-2-create-a-visual-studio-project"></a>Passo 2: Crie um projeto do Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="step-3-add-sample-code"></a>Passo 3: Adicionar código de amostra

Nesta etapa adicionamos o código XAML que define a interface do usuário do aplicativo e adicionamos a implementação c# de código por trás.

### <a name="xaml-code"></a>Código XAML

Crie a interface de usuário do aplicativo adicionando o código XAML.

1. Em **Solution Explorer,** aberto`MainPage.xaml`

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
               <Button x:Name="ListenButton" Content="Talk"
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

Adicione a fonte de código atrás para que o aplicativo funcione como esperado. A origem code-behind inclui:

- Declarações necessárias `using` para os `Speech` espaços de nome e `Speech.Dialog`
- Uma implementação simples para garantir o acesso ao microfone, conectado a um manipulador de botão
- Auxiliares básicos de interface do usuário para apresentar erros e mensagens no aplicativo
- Um ponto inicial para o caminho do código de inicialização que será populado posteriormente
- Um auxiliar para reproduzir a conversão de texto em fala (sem suporte para streaming)
- Um manipulador de botão vazio para iniciar a escuta que será populado mais tarde

Adicione a fonte de código atrás da seguinte forma:

1. No **Solution Explorer,** abra o `MainPage.xaml.cs` arquivo-fonte `MainPage.xaml`por trás do código (agrupado em )

1. Substitua o conteúdo do arquivo pelo seguinte código:

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

1. Adicione o seguinte código ao corpo do método de`InitializeDialogServiceConnector`

   ```csharp
   // This code creates the `DialogServiceConnector` with your subscription information.
   // create a DialogServiceConfig by providing a Custom Commands application id and Cognitive Services subscription key
   // the RecoLanguage property is optional (default en-US); note that only en-US is supported in Preview
   const string speechCommandsApplicationId = "YourApplicationId"; // Your application id
   const string speechSubscriptionKey = "YourSpeechSubscriptionKey"; // Your subscription key
   const string region = "YourServiceRegion"; // The subscription service region. Note: only 'westus2' is currently supported

   var speechCommandsConfig = CustomCommandsConfig.FromSubscription(speechCommandsApplicationId, speechSubscriptionKey, region);
   speechCommandsConfig.SetProperty(PropertyId.SpeechServiceConnection_RecoLanguage, "en-us");
   connector = new DialogServiceConnector(speechCommandsConfig);
   ```

1. Substitua as `YourApplicationId` `YourSpeechSubscriptionKey`strings `YourServiceRegion` e com seus próprios valores para o seu aplicativo, assinatura de voz e [região](regions.md)

1. Anexar o seguinte trecho de código até o final do corpo do método de`InitializeDialogServiceConnector`

   ```csharp
   //
   // This code sets up handlers for events relied on by `DialogServiceConnector` to communicate its activities,
   // speech recognition results, and other information.
   //
   // ActivityReceived is the main way your client will receive messages, audio, and events
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

1. Adicione o seguinte trecho de código `ListenButton_ButtonClicked` ao corpo `MainPage` do método na classe

   ```csharp
   // This code sets up `DialogServiceConnector` to listen, since you already established the configuration and
   // registered the event handlers.
   if (connector == null)
   {
       InitializeDialogServiceConnector();
       // Optional step to speed up first interaction: if not called,
       // connection happens automatically on first use
       var connectTask = connector.ConnectAsync();
   }

   try
   {
       // Start sending audio
       await connector.ListenOnceAsync();
   }
   catch (Exception ex)
   {
       NotifyUser($"Exception: {ex.ToString()}", NotifyType.ErrorMessage);
   }
   ```

1. Na barra de menu, escolha **'Salvar tudo'** **para** > salvar suas alterações

## <a name="build-and-run-the-application"></a>Compile e execute o aplicativo

1. Na barra de menus, escolha **Build** > **Build Solution** para construir o aplicativo. O código deve compilar sem erros.

1. Escolha **Depurar Depuração** > **Start Debugging** (ou pressione **F5**) para iniciar o aplicativo. A janela **helloworld** é exibida.

   ![Exemplo de aplicativo de assistente virtual UWP em C# – Início rápido](media/sdk/qs-voice-assistant-uwp-helloworld-window.png)

1. Selecione **Habilitar Microfone**. Se a solicitação de permissão de acesso aparecer, selecione **Sim**.

   ![Solicitação de permissão de acesso ao microfone](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Selecione **Falar**e fale uma frase ou frase em inglês no microfone do seu dispositivo. Sua fala será transmitida para o canal de Fala do Direct Line e transcrita em texto, que será exibida na janela.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como: Cumprir comandos no cliente com o Speech SDK (visualização)](./how-to-custom-speech-commands-fulfill-sdk.md)
> [Como: Adicionar validações aos parâmetros de comando personalizado (Visualização)](./how-to-custom-speech-commands-validations.md)
