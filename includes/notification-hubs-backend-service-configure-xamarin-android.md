---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: c919cfce3d868a81f28eb8172314e9639387e933
ms.sourcegitcommit: e04a66514b21019f117a4ddb23f22c7c016da126
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85112024"
---
### <a name="validate-package-name-and-permissions"></a>Validar o nome e as permissões do pacote

1. No **PushDemo.Android**, abra as **Opções do Projeto** e **Aplicativo Android** na seção **Build**.

1. Verifique se o **Nome do pacote** corresponde ao valor usado no projeto **PushDemo** do [Console do Firebase](https://console.firebase.google.com). O **Nome do pacote** estava no formato ``com.<organization>.pushdemo``.

1. Defina a **Versão Mínima do Android** como **Android 8.0 (nível da API 26)** e a **Versão de Destino do Android** para o **nível da API** mais recente.

    > [!NOTE]
    > Somente os dispositivos que executam **Nível da API 26 e superior** são compatíveis com os fins deste tutorial, no entanto, você pode estendê-lo para compatibilidade com dispositivos que executam versões mais antigas.

1. Verifique se as permissões **INTERNET** e **READ_PHONE_STATE** estão habilitadas em **Permissões necessárias**.

1. Clique em **OK**

### <a name="add-the-xamarin-google-play-services-base-and-xamarinfirebasemessaging-packages"></a>Adicionar os pacotes Xamarin Google Play Services Base e Xamarin.Firebase.Messaging

1. Em **PushDemo.Android**, **Control** + **Clique** na pasta **Pacotes** e escolha **Gerenciar Pacotes NuGet...** .

1. Pesquise **Xamarin.GooglePlayServices.Base** (não **Basement**) e verifique se ele está marcado.

1. Pesquise **Xamarin.Firebase.Messaging** e verifique se ele está marcado.

1. Clique em **Adicionar Pacotes** e em **Aceitar** quando solicitado a aceitar os **termos de licença**.

### <a name="add-the-google-services-json-file"></a>Adicione o arquivo JSON de Serviços do Google

1. **Control** + **Clique** no projeto `PushDemo.Android` e escolha **Arquivo existente...** no menu **Adicionar**.

1. Escolha o arquivo *google-services.json* que você baixou anteriormente ao configurar o projeto **PushDemo** no [Console do Firebase](https://console.firebase.google.com) e clique em **Abrir**.

1. Quando solicitado, escolha **Copiar o arquivo no diretório**.

1. **Control** + **Clique** no arquivo *google-services.json* dentro do projeto `PushDemo.Android` e verifique se **GoogleServicesJson** está definido como **Ação de Build**.

### <a name="handle-push-notifications-for-android"></a>Manipular notificações por push para o Android

1. **Control** + **Clique** no projeto `PushDemo.Android`, escolha **Nova Pasta** no menu **Adicionar** e clique em **Adicionar** usando *Serviços* como o **Nome da Pasta**.

1. **Control** + **Clique** na pasta **Serviços** e escolha **Novo Arquivo...** no menu **Adicionar**.

1. Selecione **Geral** > **Classe Vazia**, digite *DeviceInstallationService.cs* como o **Nome** e clique em **Novo**, adicionando a implementação a seguir.

    ```csharp
    using System;
    using Android.App;
    using Android.Gms.Common;
    using PushDemo.Models;
    using PushDemo.Services;
    using static Android.Provider.Settings;

    namespace PushDemo.Droid.Services
    {
        public class DeviceInstallationService : IDeviceInstallationService
        {
            public string Token { get; set; }

            public bool NotificationsSupported
                => GoogleApiAvailability.Instance
                    .IsGooglePlayServicesAvailable(Application.Context) == ConnectionResult.Success;

            public string GetDeviceId()
                => Secure.GetString(Application.Context.ContentResolver, Secure.AndroidId);

            public DeviceInstallation GetDeviceInstallation(params string[] tags)
            {
                if (!NotificationsSupported)
                    throw new Exception(GetPlayServicesError());

                var installation = new DeviceInstallation
                {
                    InstallationId = GetDeviceId(),
                    Platform = "fcm",
                    PushChannel = Token
                };

                installation.Tags.AddRange(tags);

                return installation;
            }

            string GetPlayServicesError()
            {
                int resultCode = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable(Application.Context);

                if (resultCode != ConnectionResult.Success)
                    return GoogleApiAvailability.Instance.IsUserResolvableError(resultCode) ?
                               GoogleApiAvailability.Instance.GetErrorString(resultCode) :
                               "This device is not supported";

                return "An error occurred preventing the use of push notifications";
            }
        }
    }
    ```

    > [!NOTE]
    > Essa classe fornece uma ID exclusiva (usando [Secure.AndroidId](https://docs.microsoft.com/dotnet/api/android.provider.settings.secure.androidid?view=xamarin-android-sdk-9)) como parte do conteúdo de registro do hub de notificação.

1. Adicione outra **Classe Vazia** à pasta **Serviços** chamada *PushNotificationFirebaseMessagingService.cs* e adicione a implementação a seguir.

    ```csharp
    using Android.App;
    using Android.Content;
    using Firebase.Messaging;
    using PushDemo.Services;

    namespace PushDemo.Droid.Services
    {
        [Service]
        [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
        public class PushNotificationFirebaseMessagingService : FirebaseMessagingService
        {
            IPushDemoNotificationActionService _notificationActionService;
            INotificationRegistrationService _notificationRegistrationService;
            IDeviceInstallationService _deviceInstallationService;

            IPushDemoNotificationActionService NotificationActionService
                => _notificationActionService ??
                    (_notificationActionService =
                    ServiceContainer.Resolve<IPushDemoNotificationActionService>());

            INotificationRegistrationService NotificationRegistrationService
                => _notificationRegistrationService ??
                    (_notificationRegistrationService =
                    ServiceContainer.Resolve<INotificationRegistrationService>());

            IDeviceInstallationService DeviceInstallationService
                => _deviceInstallationService ??
                    (_deviceInstallationService =
                    ServiceContainer.Resolve<IDeviceInstallationService>());

            public override void OnNewToken(string token)
            {
                DeviceInstallationService.Token = token;

                NotificationRegistrationService.RefreshRegistrationAsync()
                    .ContinueWith((task) => { if (task.IsFaulted) throw task.Exception; });
            }

            public override void OnMessageReceived(RemoteMessage message)
            {
                if(message.Data.TryGetValue("action", out var messageAction))
                    NotificationActionService.TriggerAction(messageAction);
            }
        }
    }
    ```

1. Em **MainActivity.cs**, verifique se os namespaces a seguir foram adicionados à parte superior do arquivo.

    ```csharp
    using System;
    using Android.App;
    using Android.Content;
    using Android.Content.PM;
    using Android.OS;
    using Android.Runtime;
    using Firebase.Iid;
    using PushDemo.Droid.Services;
    using PushDemo.Services;
    ```

1. Em **MainActivity.cs**, defina a **LaunchMode** como **SingleTop** para que **MainActivity** não seja criada novamente quando aberta.

    ```csharp
    [Activity(
        Label = "PushDemo",
        LaunchMode = LaunchMode.SingleTop,
        Icon = "@mipmap/icon",
        Theme = "@style/MainTheme",
        MainLauncher = true,
        ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
    ```

1. Adicione propriedades privadas e os campos de suporte correspondentes para armazenar uma referência para as implementações **IPushNotificationActionService** e **IDeviceInstallationService**.

    ```csharp
    IPushDemoNotificationActionService _notificationActionService;
    IDeviceInstallationService _deviceInstallationService;

    IPushDemoNotificationActionService NotificationActionService
        => _notificationActionService ??
            (_notificationActionService =
            ServiceContainer.Resolve<IPushDemoNotificationActionService>());

    IDeviceInstallationService DeviceInstallationService
        => _deviceInstallationService ??
            (_deviceInstallationService =
            ServiceContainer.Resolve<IDeviceInstallationService>());
    ```

1. Implemente a interface **IOnSuccessListener** para recuperar e armazenar o token do **Firebase**.

    ```csharp
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity, Android.Gms.Tasks.IOnSuccessListener
    {
        ...

        public void OnSuccess(Java.Lang.Object result)
            => DeviceInstallationService.Token =
                result.Class.GetMethod("getToken").Invoke(result).ToString();
    }
    ```

1. Adicione um novo método chamado **ProcessNotificationActions** que verificará se uma determinada **Intenção** tem um valor extra chamado *ação*. Dispare de maneira condicional essa ação usando a implementação **IPushDemoNotificationActionService**.

    ```csharp
    void ProcessNotificationActions(Intent intent)
    {
        try
        {
            if (intent?.HasExtra("action") == true)
            {
                var action = intent.GetStringExtra("action");

                if (!string.IsNullOrEmpty(action))
                    NotificationActionService.TriggerAction(action);
            }
        }
        catch (Exception ex)
        {
            System.Diagnostics.Debug.WriteLine(ex.Message);
        }
    }
    ```

1. Substitua o método **OnNewIntent** para chamar o método de **CheckIntentForNotificationActions**.

    ```csharp
    protected override void OnNewIntent(Intent intent)
    {
        base.OnNewIntent(intent);
        ProcessNotificationActions(intent);
    }
    ```

    > [!NOTE]
    > Como **LaunchMode** para a **Atividade** está definida como **SingleTop**, uma **Intenção** será enviada para a instância existente da **Atividade** por meio do método **OnNewIntent** em vez do método **OnCreate** e, portanto, você deverá manipular uma intenção de entrada nos métodos **OnCreate** e **OnNewIntent**.

1. Atualize o método **OnCreate** para chamar `Bootstrap.Begin` logo após a chamada para `base.OnCreate`, passando a implementação específica da plataforma do **IDeviceInstallationService**.

    ```csharp
    Bootstrap.Begin(() => new DeviceInstallationService());
    ```

1. No mesmo método, chame de maneira condicional **GetInstanceId** na instância do **FirebaseApp**, logo após a chamada para `Bootstrap.Begin`, adicionando **MainActivity** como **IOnSuccessListener**.

    ```csharp
    if (DeviceInstallationService.NotificationsSupported)
    {
        FirebaseInstanceId.GetInstance(Firebase.FirebaseApp.Instance)
            .GetInstanceId()
            .AddOnSuccessListener(this);
    }
    ```

1. Ainda em **OnCreate**, chame **ProcessNotificationActions** imediatamente após a chamada para `LoadApplication`, passando a **Intenção** atual.

    ```cs
    ...

    LoadApplication(new App());

    ProcessNotificationActions(Intent);
    ```

> [!NOTE]
> Você deve registrar novamente o aplicativo sempre que executá-lo e interrompê-lo em uma sessão de depuração para continuar a receber notificações por push.
