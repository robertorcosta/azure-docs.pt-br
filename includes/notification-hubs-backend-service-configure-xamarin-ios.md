---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: c13b7ee8c5c0a0d302e4822047ea60f9df120bf8
ms.sourcegitcommit: e04a66514b21019f117a4ddb23f22c7c016da126
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85112059"
---
### <a name="configure-infoplist-and-entitlementsplist"></a>Configurar Info.plist and Entitlements.plist

1. Verifique se você entrou na **Conta de Desenvolvedor da Apple** em **Visual Studio** > **Preferências...**  > **Publicação** > **Contas de Desenvolvedor da Apple** e se o *Certificado* apropriado e o *Perfil de Provisão* foram baixados. Você deve ter criado esses ativos como parte das etapas anteriores.

1. Em **PushDemo.iOS**, abra **Info.plist** e verifique se o **BundleIdentifier** corresponde ao valor que foi usado para o respectivo perfil de provisão no [Apple Developer Portal](https://developer.apple.com). O **BundleIdentifier** estava no formato ``com.<organization>.PushDemo``.

1. No mesmo arquivo, defina a **Versão mínima do sistema** como **13.0**.

    > [!NOTE]
    > Somente os dispositivos que executam **iOS 13.0 e superior** são compatíveis com os fins deste tutorial, no entanto, você pode estendê-lo para compatibilidade com dispositivos que executam versões mais antigas.

1. Abra as **Opções do Projeto** para **PushDemo.iOS** (clique duas vezes no projeto).

1. Nas **Opções do Projeto**, em **Build > Assinatura de Pacote do iOS**, verifique se a sua conta de desenvolvedor está selecionada em **Equipe**. Em seguida, verifique se "Gerenciar assinatura automaticamente" está selecionado e se seu Certificado de autenticação e Perfil de Provisão são selecionados automaticamente.

    > [!NOTE]
    > Se o *Certificado de autenticação* e o *Perfil de Provisão* não tiverem sido selecionados automaticamente, escolha **Provisionamento Manual** e clique em **Opções de Assinatura de Pacote**. Verifique se sua *Equipe* está selecionada em **Identidade de Assinatura** e seu perfil de provisão específico do *PushDemo* está selecionado para **Perfil de Provisão** para as configurações de **Depuração** e **Versão**, garantindo que **iPhone** esteja selecionado como a **Plataforma** em ambos os casos.

1. Em **PushDemo.iOS**, abra **Entitlements.plist** e verifique se a opção **Habilitar Notificações por Push** está marcada quando exibida na guia **Direitos**. Em seguida, verifique se a configuração **Ambiente da APS** está definida como **desenvolvimento** quando exibida na guia **Origem**.

### <a name="handle-push-notifications-for-ios"></a>Manipular notificações por push para iOS

1. **Control** + **Clique** no projeto **PushDemo.iOS**, escolha **Nova Pasta** no menu **Adicionar** e clique em **Adicionar** usando *Serviços* como o **Nome da Pasta**.

1. **Control** + **Clique** na pasta **Serviços** e escolha **Novo Arquivo...** no menu **Adicionar**.

1. Selecione **Geral** > **Classe Vazia**, digite *DeviceInstallationService.cs* como o **Nome** e clique em **Novo**, adicionando a implementação a seguir.

    ```csharp
    using System;
    using PushDemo.Models;
    using PushDemo.Services;
    using UIKit;

    namespace PushDemo.iOS.Services
    {
        public class DeviceInstallationService : IDeviceInstallationService
        {
            const int SupportedVersionMajor = 13;
            const int SupportedVersionMinor = 0;

            public string Token { get; set; }

            public bool NotificationsSupported
                => UIDevice.CurrentDevice.CheckSystemVersion(SupportedVersionMajor, SupportedVersionMinor);

            public string GetDeviceId()
                => UIDevice.CurrentDevice.IdentifierForVendor.ToString();

            public DeviceInstallation GetDeviceInstallation(params string[] tags)
            {
                if (!NotificationsSupported)
                    throw new Exception(GetNotificationsSupportError());

                var installation = new DeviceInstallation
                {
                    InstallationId = GetDeviceId(),
                    Platform = "apns",
                    PushChannel = Token
                };

                installation.Tags.AddRange(tags);

                return installation;
            }

            string GetNotificationsSupportError()
            {
                if (!NotificationsSupported)
                    return $"This app only supports notifications on iOS {SupportedVersionMajor}.{SupportedVersionMinor} and above. You are running {UIDevice.CurrentDevice.SystemVersion}.";

                if (Token == null)
                    return $"This app can support notifications but you must enable this in your settings.";


                return "An error occurred preventing the use of push notifications";
            }
        }
    }
    ```

    > [!NOTE]
    > Essa classe fornece uma ID exclusiva (usando o valor [UIDevice.IdentifierForVendor](https://docs.microsoft.com/dotnet/api/uikit.uidevice.identifierforvendor?view=xamarin-ios-sdk-12)) e o conteúdo de registro do hub de notificação.

1. Adicione uma nova pasta ao projeto **PushDemo.iOS** chamada *Extensões* e uma **Classe Vazia** a essa pasta, chamada *NSDataExtensions.cs* com a implementação a seguir.

    ```csharp
    using System.Text;
    using Foundation;

    namespace PushDemo.iOS.Extensions
    {
        internal static class NSDataExtensions
        {
            internal static string ToHexString(this NSData data)
            {
                var bytes = data.ToArray();

                if (bytes == null)
                    return null;

                StringBuilder sb = new StringBuilder(bytes.Length * 2);

                foreach (byte b in bytes)
                    sb.AppendFormat("{0:x2}", b);

                return sb.ToString().ToUpperInvariant();
            }
        }
    }
    ```

1. Em **AppDelegate.cs**, verifique se os namespaces a seguir foram adicionados à parte superior do arquivo.

    ```csharp
    using System;
    using System.Diagnostics;
    using System.Threading.Tasks;
    using Foundation;
    using PushDemo.iOS.Extensions;
    using PushDemo.iOS.Services;
    using PushDemo.Services;
    using UIKit;
    using UserNotifications;
    using Xamarin.Essentials;
    ```

1. Adicione uma constante para a chave de cache do token do dispositivo.

    ```csharp
    const string CachedDeviceToken = "cached_device_token";
    ```

1. Adicione propriedades privadas e seus respectivos campos de suporte para armazenar uma referência para as implementações **IPushDemoNotificationActionService**, **INotificationRegistrationService** e **IDeviceInstallationService**.

    ```csharp
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
    ```

1. Adicione o método **RegisterForRemoteNotifications** para registrar configurações de notificação do usuário e para notificações remotas com a **APNs**.

    ```csharp
    void RegisterForRemoteNotifications()
    {
        MainThread.BeginInvokeOnMainThread(() =>
        {
            var pushSettings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert |
                UIUserNotificationType.Badge |
                UIUserNotificationType.Sound,
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(pushSettings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications();
        });
    }
    ```

1. Adicione o método **CompleteRegistrationAsync** para definir o valor da propriedade `IDeviceInstallationService.Token`. Atualize o registro e armazene em cache o token do dispositivo se ele tiver sido atualizado desde a última vez em que foi armazenado.

    ```csharp
    async Task CompleteRegistrationAsync(NSData deviceToken)
    {
        DeviceInstallationService.Token = deviceToken.ToHexString();

        var cachedToken = await SecureStorage.GetAsync(CachedDeviceToken)
            .ConfigureAwait(false);

        if (!string.IsNullOrWhiteSpace(cachedToken) &&
            cachedToken.Equals(DeviceInstallationService.Token))
            return;

        await NotificationRegistrationService.RefreshRegistrationAsync()
            .ConfigureAwait(false);

        await SecureStorage.SetAsync(CachedDeviceToken, DeviceInstallationService.Token)
            .ConfigureAwait(false);
    }
    ```

1. Adicione o método **ProcessNotificationActions** para processar os dados da notificação do **NSDictionary**, chamando condicionalmente **NotificationActionService.TriggerAction**.

    ```csharp
    void ProcessNotificationActions(NSDictionary userInfo)
    {
        if (userInfo == null)
            return;

        try
        {
            var actionValue = userInfo.ObjectForKey(new NSString("action")) as NSString;

            if (!string.IsNullOrWhiteSpace(actionValue?.Description))
                NotificationActionService.TriggerAction(actionValue.Description);
        }
        catch (Exception ex)
        {
            Debug.WriteLine(ex.Message);
        }
    }
    ```

1. Substitua o método **RegisteredForRemoteNotifications** passando o argumento **deviceToken** para o método **CompleteRegistrationAsync**.

    ```csharp
    public override void RegisteredForRemoteNotifications(
        UIApplication application,
        NSData deviceToken)
        => CompleteRegistrationAsync(deviceToken).ContinueWith((task)
            => { if (task.IsFaulted) throw task.Exception; });
    ```

1. Substitua o método **ReceivedRemoteNotification** passando o argumento **userInfo** para o método **ProcessNotificationActions**.

    ```csharp
    public override void ReceivedRemoteNotification(
        UIApplication application,
        NSDictionary userInfo)
        => ProcessNotificationActions(userInfo);
    ```

1. Substitua o método **FailedToRegisterForRemoteNotifications** para registrar o erro.

    ```csharp
    public override void FailedToRegisterForRemoteNotifications(
        UIApplication application,
        NSError error)
        => Debug.WriteLine(error.Description);
    ```

    > [!NOTE]
    > Esse é um espaço reservado. Você vai querer implementar o registro em log e o tratamento de erro apropriados para cenários de produção.

1. Atualize o método **FinishedLaunching** para chamar `Bootstrap.Begin` logo após a chamada para `Forms.Init`, passando a implementação específica da plataforma do **IDeviceInstallationService**.

    ```csharp
    Bootstrap.Begin(() => new DeviceInstallationService());
    ```

1. No mesmo método, solicite a autorização de maneira condicional e registre-se para notificações remotas imediatamente após `Bootstrap.Begin`.

    ```csharp
    if (DeviceInstallationService.NotificationsSupported)
    {
        UNUserNotificationCenter.Current.RequestAuthorization(
                UNAuthorizationOptions.Alert |
                UNAuthorizationOptions.Badge |
                UNAuthorizationOptions.Sound,
                (approvalGranted, error) =>
                {
                    if (approvalGranted && error == null)
                        RegisterForRemoteNotifications();
                });
    }
    ```

1. Ainda em **FinishedLaunching**, chame **ProcessNotificationActions** imediatamente após a chamada para `LoadApplication` se o argumento **options** contiver o **UIApplication.LaunchOptionsRemoteNotificationKey** passando o objeto **userInfo** resultante.

    ```csharp
    using (var userInfo = options?.ObjectForKey(
        UIApplication.LaunchOptionsRemoteNotificationKey) as NSDictionary)
            ProcessNotificationActions(userInfo);
    ```
