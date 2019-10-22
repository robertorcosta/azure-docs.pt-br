---
title: Enviar notificações por push para aplicativos Xamarin.Android usando os Hubs de Notificação do Azure | Microsoft Docs
description: Neste tutorial, você aprende a usar os Hubs de Notificação do Azure para enviar notificações por push a um aplicativo Xamarin Android.
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 08/01/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 08/01/2019
ms.openlocfilehash: 0e4354fa7466efcf27f430bbce7edb30bb9a304c
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387661"
---
# <a name="tutorial-send-push-notifications-to-xamarinandroid-apps-using-notification-hubs"></a>Tutorial: Enviar notificações por push para aplicativos Xamarin.Android usando os Hubs de Notificação

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Visão geral

Este tutorial mostra como usar os Hubs de Notificação do Azure para enviar notificações por push para um aplicativo Xamarin.Android. Você cria um aplicativo Xamarin.Android em branco que recebe notificações por push usando o FCM (Firebase Cloud Messaging). Você usa seu hub de notificação para transmitir notificações por push a todos os dispositivos que executam seu aplicativo. O código concluído está disponível na amostra do [aplicativo NotificationHubs](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/Xamarin/GetStartedXamarinAndroid).

Neste tutorial, você deve executar as seguintes etapas:

> [!div class="checklist"]
> * Criar um projeto de Firebase e habilitar Firebase Cloud Messaging
> * Criar um hub de notificação
> * Criar um aplicativo Xamarin.Android e conectar-se ao hub de notificação
> * Enviar notificações por push de teste do portal do Azure

## <a name="prerequisites"></a>Pré-requisitos

* **Assinatura do Azure**. Caso você não tenha uma assinatura do Azure, [crie uma conta gratuita do Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* [Visual Studio com Xamarin] no Windows ou [Visual Studio para Mac] no OS X.
* Conta ativa do Google

## <a name="create-a-firebase-project-and-enable-firebase-cloud-messaging"></a>Criar um projeto de Firebase e habilitar Firebase Cloud Messaging

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging-xamarin.md)]

## <a name="create-a-notification-hub"></a>Criar um hub de notificação

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-gcmfcm-settings-for-the-notification-hub"></a>Configurar as definições do GCM/FCM para o hub de notificação

1. Selecione **Google (GCM/FCM)** na seção **Configurações** no menu à esquerda.
2. Insira a **chave do servidor** que você anotou do Google Firebase Console.
3. Selecione **Salvar** na barra de ferramentas.

    ![](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

Seu hub de notificação está configurado para funcionar com o FCM e você tem as cadeias de conexão para registrar o aplicativo para receber notificações e enviar notificações por push.

## <a name="create-a-xamarinandroid-app-and-connect-it-to-notification-hub"></a>Criar um aplicativo Xamarin.Android e conectá-lo ao hub de notificação

### <a name="create-visual-studio-project-and-add-nuget-packages"></a>Criar um projeto do Visual Studio e adicionar pacotes NuGet

> [!NOTE]
> As etapas documentadas neste tutorial são para o Visual Studio 2017. 

1. No Visual Studio, abra o menu **Arquivo**, selecione **Novo** e, em seguida, **Projeto**. Na janela **Novo Projeto**, execute estas etapas:
    1. Expanda **Instalado**, **Visual C#** e clique em **Android**.
    2. Selecione **aplicativo Android (Xamarin)** na lista.
    3. Insira um **nome** para o projeto.
    4. Selecione um **local** para o projeto.
    5. Selecione **OK**

        ![Caixa de diálogo Novo Projeto](./media/partner-xamarin-notification-hubs-android-get-started/new-project-dialog-new.png)
2. Na caixa de diálogo **Novo aplicativo Android**, selecione **Aplicativo em branco** e selecione **OK**.

    ![Caixa de diálogo Novo Projeto](./media/partner-xamarin-notification-hubs-android-get-started/new-android-app-dialog.png)
3. Na janela do **Gerenciador de Soluções**, expanda **Propriedades**e clique em **AndroidManifest.xml**. Atualize o nome do pacote para corresponder ao nome do pacote inserido ao adicionar Firebase Cloud Messaging ao seu projeto no Google Firebase Console.

    ![Nome do pacote no GMC](./media/partner-xamarin-notification-hubs-android-get-started/package-name-gcm.png)
4. Defina a versão de destino do Android para o projeto como **Android 9.0 (Pie)** seguindo estas etapas: 
    1. Clique com o botão direito do mouse no projeto e selecione **Propriedades**. 
    1. Para o campo **Compilar usando a versão do Android: (estrutura de destino)** , selecione **Android 9.0 (Pie)** . 
    1. Selecione **Sim** na caixa de mensagem para continuar com a alteração da estrutura de destino.
1. Adicione os pacotes NuGet necessários ao projeto seguindo estas etapas:
    1. Clique o botão direito do mouse no projeto e selecione **Gerenciar Pacotes NuGet...** .
    1. Alterne para a guia **Instalado**, selecione **Xamarin.Android.Support.Design** e selecione **Atualizar** no painel direito para atualizar o pacote para a versão mais recente.
    1. Alterne para a guia **Procurar**. Pesquise por **Xamarin.GooglePlayServices.Base**. Selecione **Xamarin.GooglePlayServices.Base** na lista de resultados. Em seguida, selecione **Instalar**.

        ![Google Play Services NuGet](./media/partner-xamarin-notification-hubs-android-get-started/google-play-services-nuget.png)
    6. Na janela do **Gerenciador de Pacotes do NuGet**, pesquise por **Xamarin.Firebase.Messaging**. Selecione **Xamarin.Firebase.Messaging** na lista de resultados. Em seguida, selecione **Instalar**.
    7. Agora, pesquise por **Xamarin.Azure.NotificationHubs.Android**. Selecione **Xamarin.Azure.NotificationHubs.Android** na lista de resultados. Em seguida, selecione **Instalar**.

### <a name="add-the-google-services-json-file"></a>Adicione o arquivo JSON de Serviços do Google

1. Copie o arquivo `google-services.json` que você baixou do Google Firebase Console para a pasta do projeto.
2. Adicione `google-services.json` ao projeto.
3. Selecione `google-services.json` na janela **Gerenciador de Soluções**.
4. No painel **Propriedades**, defina a ação de compilação como **GoogleServicesJson**. Se você não vir **GoogleServicesJson**, feche o Visual Studio, reinicialize-o, reabra o projeto e tente novamente.

    ![Ação de compilação GoogleServicesJson](./media/partner-xamarin-notification-hubs-android-get-started/google-services-json-build-action.png)

### <a name="set-up-notification-hubs-in-your-project"></a>Configurar hubs de notificação em seu projeto

#### <a name="registering-with-firebase-cloud-messaging"></a>Registrando com o Firebase Cloud Messaging

1. Abra o arquivo `AndroidManifest.xml` e insira os seguintes elementos `<receiver>` no elemento `<application>`:

    ```xml
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
        <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name="${applicationId}" />
        </intent-filter>
    </receiver>
    ```

2. Adicione as seguintes instruções **antes do elemento** do aplicativo.

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    ```

3. Colete as informações a seguir para o aplicativo Android e o hub de notificação:

   * **Escutar cadeia de conexão**: no painel, no [portal do Azure], escolha **Exibir cadeias de conexão**. Copie a cadeia de conexão `DefaultListenSharedAccessSignature` para esse valor.
   * **Nome do hub**: nome do seu hub no [portal do Azure]. Por exemplo, *mynotificationhub2*.
4. Na janela **Gerenciador de Soluções**, clique com o botão direito do mouse no **projeto**, selecione **Adicionar** e, em seguida, **Classe**.
5. Crie uma classe `Constants.cs` para seu projeto Xamarin e defina os valores de constantes a seguir na classe. Substitua os espaços reservados pelos seus valores.

    ```csharp
    public static class Constants
    {
        public const string ListenConnectionString = "<Listen connection string>";
        public const string NotificationHubName = "<hub name>";
    }
    ```

6. Adicione o seguinte usando as instruções para `MainActivity.cs`:

    ```csharp
    using Android.Util;
    using Android.Gms.Common;
    ```

7. Adicione as seguintes propriedades à classe MainActivity. A variável TAG será usada para mostrar uma caixa de diálogo de alerta quando o aplicativo estiver em execução:

    ```csharp
    public const string TAG = "MainActivity";
    internal static readonly string CHANNEL_ID = "my_notification_channel";
    ```

8. Adicione o seguinte método à classe MainActivity. Ele verifica se o **Google Play Services** está disponível no dispositivo.

    ```csharp
    public bool IsPlayServicesAvailable()
    {
        int resultCode = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable(this);
        if (resultCode != ConnectionResult.Success)
        {
            if (GoogleApiAvailability.Instance.IsUserResolvableError(resultCode))
                Log.Debug(TAG, GoogleApiAvailability.Instance.GetErrorString(resultCode));
            else
            {
                Log.Debug(TAG, "This device is not supported");
                Finish();
            }
            return false;
        }

        Log.Debug(TAG, "Google Play Services is available.");
        return true;
    }
    ```

9. Adicione o seguinte método à classe MainActivity que cria um canal de notificação.

    ```csharp
    private void CreateNotificationChannel()
    {
        if (Build.VERSION.SdkInt < BuildVersionCodes.O)
        {
            // Notification channels are new in API 26 (and not a part of the
            // support library). There is no need to create a notification
            // channel on older versions of Android.
            return;
        }

        var channelName = CHANNEL_ID;
        var channelDescription = string.Empty;
        var channel = new NotificationChannel(CHANNEL_ID, channelName, NotificationImportance.Default)
        {
            Description = channelDescription
        };

        var notificationManager = (NotificationManager)GetSystemService(NotificationService);
        notificationManager.CreateNotificationChannel(channel);
    }
    ```

10. No `MainActivity.cs`, adicione o seguinte código a `OnCreate` após `base.OnCreate(savedInstanceState)`:

    ```csharp
    if (Intent.Extras != null)
    {
        foreach (var key in Intent.Extras.KeySet())
        {
            if(key!=null)
            {
                var value = Intent.Extras.GetString(key);
                Log.Debug(TAG, "Key: {0} Value: {1}", key, value);
            }
        }
    }

    IsPlayServicesAvailable();
    CreateNotificationChannel();
    ```

15. Adicione uma classe denominada `MyFirebaseMessagingService` ao projeto. 
16. Adicione o seguinte usando as instruções para `MyFirebaseMessagingService.cs`.

    ```csharp
    using Android.Util;
    using Firebase.Messaging;
    using Android.Support.V4.App;    
    using WindowsAzure.Messaging;
    ```

17. Adicione o seguinte acima da declaração de classe e faça com que a classe herde de `FirebaseMessagingService`:

    ```csharp
    [Service]
    [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
    [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
    public class MyFirebaseMessagingService : FirebaseMessagingService
    ```

18. Adicione o código a seguir ao `MyFirebaseMessagingService.cs` para processar as mensagens que são recebidas. 

    ```csharp
        const string TAG = "MyFirebaseMsgService";
        NotificationHub hub;
    
        public override void OnMessageReceived(RemoteMessage message)
        {
            Log.Debug(TAG, "From: " + message.From);
            if (message.GetNotification() != null)
            {
                //These is how most messages will be received
                Log.Debug(TAG, "Notification Message Body: " + message.GetNotification().Body);
                SendNotification(message.GetNotification().Body);
            }
            else
            {
                //Only used for debugging payloads sent from the Azure portal
                SendNotification(message.Data.Values.First());
    
            }
        }
    
        void SendNotification(string messageBody)
        {
            var intent = new Intent(this, typeof(MainActivity));
            intent.AddFlags(ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);
    
            var notificationBuilder = new NotificationCompat.Builder(this, MainActivity.CHANNEL_ID);
    
            notificationBuilder.SetContentTitle("FCM Message")
                        .SetSmallIcon(Resource.Drawable.ic_launcher)
                        .SetContentText(messageBody)
                        .SetAutoCancel(true)
                        .SetShowWhen(false)
                        .SetContentIntent(pendingIntent);
    
            var notificationManager = NotificationManager.FromContext(this);
    
            notificationManager.Notify(0, notificationBuilder.Build());
        }
    ```

19. Adicione os métodos a seguir à classe MyFirebaseMessagingService para receber o token de registro FCM e enviá-lo para a instância dos Hubs de Notificação (hub). 

    ```csharp
        public override void OnNewToken(string token)
        {
            Log.Debug(TAG, "FCM token: " + token);
            SendRegistrationToServer(token);
        }

        void SendRegistrationToServer(string token)
        {
            // Register with Notification Hubs
            hub = new NotificationHub(Constants.NotificationHubName,
                                        Constants.ListenConnectionString, this);

            var tags = new List<string>() { };
            var regID = hub.Register(token, tags.ToArray()).RegistrationId;

            Log.Debug(TAG, $"Successful registration of ID {regID}");
        }
    ```
1. **Compile** o seu novo projeto.
1. **Executar** aplicativo no dispositivo ou emulador carregado

## <a name="send-test-notification-from-the-azure-portal"></a>Enviar notificação por push de teste do portal do Azure

Você pode testar o recebimento de notificações no aplicativo com a opção **Envio de Teste** no [Portal do Azure]. Isso envia uma notificação por push de teste para seu dispositivo.

![Portal do Azure – Envio de Teste](media/partner-xamarin-notification-hubs-android-get-started/send-test-notification.png)

As notificações por push são geralmente enviadas em um serviço back-end, como Serviços Móveis ou ASP.NET, por meio de uma biblioteca compatível. Se uma biblioteca não estiver disponível para o back-end, você também poderá usar a API REST diretamente para enviar mensagens de notificação.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você envia notificações de transmissão para todos os seus dispositivos Android registrados com o back-end. Para saber como enviar notificações por push para dispositivos Android específicos, vá para o tutorial a seguir:

> [!div class="nextstepaction"]
>[Notificações por push para especificar dispositivos](push-notifications-android-specific-devices-firebase-cloud-messaging.md)

<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next steps]:#next-steps

<!-- Images. -->
[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png
[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png
[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png
[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project2.png
[24]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-xamarin-android-app-options.png
[25]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-google-services-json.png
[30]: ./media/notification-hubs-android-get-started/notification-hubs-test-send.png

<!-- URLs. -->
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[Visual Studio com Xamarin]: https://docs.microsoft.com/visualstudio/install/install-visual-studio
[Visual Studio para Mac]: https://www.visualstudio.com/vs/visual-studio-mac/
[Portal do Azure]: https://portal.azure.com/
[wns object]: https://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Android]: https://msdn.microsoft.com/library/dn282661.aspx
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[GitHub]: https://github.com/Azure/azure-notificationhubs-android
