---
author: mikeparker104
ms.author: miparker
ms.date: 07/07/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 9a8b76c0887cff498edd90aedf5cc6eb674fc7e5
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86156259"
---
### <a name="configure-the-runner-target-and-infoplist"></a>Configurar o destino do Executor e Info.plist

1. No **Visual Studio Code**, use **Control** + **Clique** na pasta **ios** e escolha **Abrir no Xcode**.

1. No **Xcode**, clique em **Executor** (**xcodeproj** na parte superior, não na pasta) e selecione o destino **Executor** e **Assinatura e Funcionalidades**. Com a configuração de build **Todos** selecionada, escolha sua conta de desenvolvedor para a **Equipe**. Verifique se a opção "Gerenciar assinatura automaticamente" está selecionada e se seu Certificado de autenticação e Perfil de Provisão são selecionados automaticamente.

    > [!NOTE]
    > Se você não vir o novo valor do perfil de provisão, tente atualizar os perfis para a identidade de assinatura selecionando **Xcode** > **Preferências** > **Conta** e selecione o botão **Baixar Perfis Manuais** para baixar os perfis.

1. Clique em **+ Funcionalidade** e pesquise **Notificações por Push**. **Clique duas vezes** em **Notificações por Push** para adicionar essa funcionalidade.

1. Abra **Info.plist** e defina **Versão mínima do sistema** como **13.0**.

    > [!NOTE]
    > Somente os dispositivos que executam **iOS 13.0 e superior** são compatíveis com os fins deste tutorial, no entanto, você pode estendê-lo para compatibilidade com dispositivos que executam versões mais antigas.

1. Abra **Runner.entitlements** e verifique se a configuração de **Ambiente APS** está definida como **desenvolvimento**.

### <a name="handle-push-notifications-for-ios"></a>Manipular notificações por push para iOS

1. Use **Control** + **Clique** na pasta **Executor** (dentro do projeto do executor) e escolha **Novo Grupo** usando **Serviços** como o nome.

1. Use **Control** + **Clique** na pasta **Serviços** e escolha **Novo Arquivo…** . Em seguida, escolha **Arquivo Swift** e clique em **Avançar**. Especifique **DeviceInstallationService** para o nome e clique em **Criar**.

1. Implemente **DeviceInstallationService.swift** usando o código a seguir.

    ```swift
    import Foundation

    class DeviceInstallationService {

        enum DeviceRegistrationError: Error {
            case notificationSupport(message: String)
        }

        var token : Data? = nil

        let DEVICE_INSTALLATION_CHANNEL = "com.<your_organization>.pushdemo/deviceinstallation"
        let GET_DEVICE_ID = "getDeviceId"
        let GET_DEVICE_TOKEN = "getDeviceToken"
        let GET_DEVICE_PLATFORM = "getDevicePlatform"

        private let deviceInstallationChannel : FlutterMethodChannel

        var notificationsSupported : Bool {
            get {
                if #available(iOS 13.0, *) {
                    return true
                }
                else {
                    return false
                }
            }
        }

        init(withBinaryMessenger binaryMessenger : FlutterBinaryMessenger) {
            deviceInstallationChannel = FlutterMethodChannel(name: DEVICE_INSTALLATION_CHANNEL, binaryMessenger: binaryMessenger)
            deviceInstallationChannel.setMethodCallHandler(handleDeviceInstallationCall)
        }

        func getDeviceId() -> String {
            return UIDevice.current.identifierForVendor!.description
        }

        func getDeviceToken() throws -> String {
            if(!notificationsSupported) {
                let notificationSupportError = getNotificationsSupportError()
                throw DeviceRegistrationError.notificationSupport(message: notificationSupportError)
            }

            if (token == nil) {
                throw DeviceRegistrationError.notificationSupport(message: "Unable to resolve token for APNS.")
            }

            return token!.reduce("", {$0 + String(format: "%02X", $1)})
        }

        func getDevicePlatform() -> String {
            return "apns"
        }

        private func handleDeviceInstallationCall(call: FlutterMethodCall, result: @escaping FlutterResult) {
            switch call.method {
            case GET_DEVICE_ID:
                result(getDeviceId())
            case GET_DEVICE_TOKEN:
                getDeviceToken(result: result)
            case GET_DEVICE_PLATFORM:
                result(getDevicePlatform())
            default:
                result(FlutterMethodNotImplemented)
            }
        }

        private func getDeviceToken(result: @escaping FlutterResult) {
            do {
                let token = try getDeviceToken()
                result(token)
            }
            catch let error {
                result(FlutterError(code: "UNAVAILABLE", message: error.localizedDescription, details: nil))
            }
        }

        private func getNotificationsSupportError() -> String {

            if (!notificationsSupported) {
                return "This app only supports notifications on iOS 13.0 and above. You are running \(UIDevice.current.systemVersion)"
            }

            return "An error occurred preventing the use of push notifications."
        }
    }
    ```

    > [!NOTE]
    > Essa classe implementa a contraparte específica da plataforma para o canal de `com.<your_organization>.pushdemo/deviceinstallation`. Isso foi definido na parte do Flutter do aplicativo em **DeviceInstallationService.dart**. Nesse caso, as chamadas são feitas do código comum para o host nativo. Substitua **<sua_organização>** pela sua organização, sempre que ela for usada.
    >
    > Essa classe fornece uma ID exclusiva (usando o valor [UIDevice.identifierForVendor](https://developer.apple.com/documentation/uikit/uidevice/1620059-identifierforvendor)) como parte do conteúdo de registro do hub de notificação.

1. Adicione outro **Arquivo Swift** à pasta **Serviços** chamada *NotificationRegistrationService* e adicione o código a seguir.

    ```swift
    import Foundation

    class NotificationRegistrationService {

        let NOTIFICATION_REGISTRATION_CHANNEL = "com.<your_organization>.pushdemo/notificationregistration"
        let REFRESH_REGISTRATION = "refreshRegistration"

        private let notificationRegistrationChannel : FlutterMethodChannel

        init(withBinaryMessenger binaryMessenger : FlutterBinaryMessenger) {
           notificationRegistrationChannel = FlutterMethodChannel(name: NOTIFICATION_REGISTRATION_CHANNEL, binaryMessenger: binaryMessenger)
        }

        func refreshRegistration() {
            notificationRegistrationChannel.invokeMethod(REFRESH_REGISTRATION, arguments: nil)
        }
    }
    ```

    > [!NOTE]
    > Essa classe implementa a contraparte específica da plataforma para o canal de `com.<your_organization>.pushdemo/notificationregistration`. Isso foi definido na parte Flutter do aplicativo em **NotificationRegistrationService.dart**. Nesse caso, as chamadas são feitas do host nativo para o código comum. Mais uma vez, substitua **<sua_organização>** pela sua organização sempre que isso for usado.

1. Adicione outro **Arquivo Swift** à pasta **service** chamada *NotificationActionService* e o código a seguir.

    ```swift
    import Foundation

    class NotificationActionService {

        let NOTIFICATION_ACTION_CHANNEL = "com.<your_organization>.pushdemo/notificationaction"
        let TRIGGER_ACTION = "triggerAction"
        let GET_LAUNCH_ACTION = "getLaunchAction"

        private let notificationActionChannel: FlutterMethodChannel

        var launchAction: String? = nil

        init(withBinaryMessenger binaryMessenger: FlutterBinaryMessenger) {
            notificationActionChannel = FlutterMethodChannel(name: NOTIFICATION_ACTION_CHANNEL, binaryMessenger: binaryMessenger)
            notificationActionChannel.setMethodCallHandler(handleNotificationActionCall)
        }

        func triggerAction(action: String) {
           notificationActionChannel.invokeMethod(TRIGGER_ACTION, arguments: action)
        }

        private func handleNotificationActionCall(call: FlutterMethodCall, result: @escaping FlutterResult) {
            switch call.method {
            case GET_LAUNCH_ACTION:
                result(launchAction)
            default:
                result(FlutterMethodNotImplemented)
            }
        }
    }
    ```

    > [!NOTE]
    > Essa classe implementa a contraparte específica da plataforma para o canal de `com.<your_organization>.pushdemo/notificationaction`. Isso foi definido na parte Flutter do aplicativo em **NotificationActionService.dart**. As chamadas podem ser feitas em ambas as direções nesse caso. Substitua **<sua_organização>** pela sua organização, sempre que ela for usada.

1. No **AppDelegate.swift**, adicione variáveis para armazenar uma referência aos serviços que você criou anteriormente.

    ```swift
    var deviceInstallationService : DeviceInstallationService?
    var notificationRegistrationService : NotificationRegistrationService?
    var notificationActionService : NotificationActionService?
    ```

1. Adicione uma função chamada **processNotificationActions** para processar os dados de notificação. Dispare condicionalmente essa ação ou armazene-a para uso posterior se a ação estiver sendo processada durante a inicialização do aplicativo.

    ```swift
    func processNotificationActions(userInfo: [AnyHashable : Any], launchAction: Bool = false) {
        if let action = userInfo["action"] as? String {
            if (launchAction) {
                notificationActionService?.launchAction = action
            }
            else {
                notificationActionService?.triggerAction(action: action)
            }
        }
    }
    ```

1. Substitua a função **didRegisterForRemoteNotificationsWithDeviceToken** definindo o valor do **token** como **DeviceInstallationService**. Em seguida, chame **refreshRegistration** no **NotificationRegistrationService**.

    ```swift
    override func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
      deviceInstallationService?.token = deviceToken
      notificationRegistrationService?.refreshRegistration()
    }
    ```

1. Substitua a função **didReceiveRemoteNotification** passando o argumento **userInfo** para a função **processNotificationActions**.

    ```swift
    override func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any]) {
        processNotificationActions(userInfo: userInfo)
    }
    ```

1. Substitua a função **didFailToRegisterForRemoteNotificationsWithError** para registrar o erro.

    ```swift
    override func application(_ application: UIApplication, didFailToRegisterForRemoteNotificationsWithError error: Error) {
        print(error);
    }
    ```

    > [!NOTE]
    > Esse é um espaço reservado. Você vai querer implementar o registro em log e o tratamento de erro apropriados para cenários de produção.

1. Em **didFinishLaunchingWithOptions**, crie uma instância das variáveis **deviceInstallationService**, **notificationRegistrationService** e **notificationActionService**.

    ```swift
    let controller : FlutterViewController = window?.rootViewController as! FlutterViewController

    deviceInstallationService = DeviceInstallationService(withBinaryMessenger: controller.binaryMessenger)
    notificationRegistrationService = NotificationRegistrationService(withBinaryMessenger: controller.binaryMessenger)
    notificationActionService = NotificationActionService(withBinaryMessenger: controller.binaryMessenger)
    ```

1. Na mesma função, solicite a autorização condicionalmente e registre-se para notificações remotas.

    ```swift
    if #available(iOS 13.0, *) {
      UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .sound, .badge]) {
          (granted, error) in

          if (granted)
          {
              DispatchQueue.main.async {
                  let pushSettings = UIUserNotificationSettings(types: [.alert, .sound, .badge], categories: nil)
                  application.registerUserNotificationSettings(pushSettings)
                  application.registerForRemoteNotifications()
              }
          }
      }
    }
    ```

1. Se **launchOptions** contiver a chave **remoteNotification**, chame **processNotificationActions** ao final da função **didFinishLaunchingWithOptions**. Passe o objeto **userInfo resultante** e use *true* para o argumento *launchAction*. Um valor *true* indica que a ação está sendo processada durante a inicialização do aplicativo.

    ```swift
    if let userInfo = launchOptions?[.remoteNotification] as? [AnyHashable : Any] {
        processNotificationActions(userInfo: userInfo, launchAction: true)
    }
    ```
