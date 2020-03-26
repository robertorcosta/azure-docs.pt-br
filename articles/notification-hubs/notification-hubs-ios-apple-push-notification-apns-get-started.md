---
title: Enviar notificações por push para aplicativos iOS usando Hubs de Notificação do Azure | Microsoft Docs
description: Neste tutorial, você aprenderá a usar os Hubs de Notificação do Azure para enviar notificações por push a um aplicativo do iOS.
services: notification-hubs
documentationcenter: ios
keywords: notificação por push, notificações por push, notificações por push do ios
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: b7fcd916-8db8-41a6-ae88-fc02d57cb914
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: tutorial
ms.custom: mvc
ms.date: 11/21/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: 032ca8d4ecbcf1fc7f3c22cbe5a0ee934fc5e17c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "74407368"
---
# <a name="tutorial-send-push-notifications-to-ios-apps-using-azure-notification-hubs"></a>Tutorial: Enviar notificações por push para aplicativos iOS usando Hubs de Notificação do Microsoft Azure

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

Neste tutorial, você usará os Hubs de Notificação do Azure para enviar notificações por push a um aplicativo do iOS. Você cria um aplicativo do iOS em branco que recebe notificações por push usando o [APNs (Apple Push Notification Service)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1).

Neste tutorial, você deve executar as seguintes etapas:

> [!div class="checklist"]
> * Gerar o arquivo de solicitação de assinatura de certificado
> * Solicitar seu aplicativo para notificações por push
> * Criar um perfil de provisionamento para o aplicativo
> * Configurar o hub de notificação para notificações por push do iOS
> * Conectar seu aplicativo do iOS aos hubs de notificação
> * Enviar notificações por push de teste
> * Verifique se seu aplicativo recebe notificações

O código completo deste tutorial pode ser encontrado [no GitHub](https://github.com/Azure/azure-notificationhubs-ios/tree/master/Samples).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes pré-requisitos:

* Uma conta ativa do Azure. Caso ainda não tenha uma conta, [crie uma conta gratuita do Azure](https://azure.microsoft.com/free).
* [Estrutura de Mensagens do Microsoft Azure]
* Versão mais recente do [Xcode]
* Um dispositivo compatível com iOS versão 10 (ou posterior)
* [Programa de Desenvolvedores de iOS](https://developer.apple.com/programs/)
  
  > [!NOTE]
  > Devido aos requisitos de configuração das notificações por push, você deve implantá-las e testá-las em um dispositivo iOS físico (iPhone ou iPad), em vez de usar o Simulador de iOS.
  
A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais sobre os Hubs de Notificação para aplicativos do iOS.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-notification-hubs"></a>Conectar seu aplicativo do iOS aos Hubs de Notificação

1. No Xcode, crie um novo projeto do iOS e selecione o modelo **Aplicativo de Modo de Exibição Único** .

    ![Xcode - aplicativo de modo de exibição único][8]

2. Ao definir as opções para o novo projeto, lembre-se de usar os mesmos **Nome do Produto** e **Identificador Organizacional** que você usou quando configurou o identificador do pacote no Portal do Desenvolvedor da Apple.

3. Em Navegador de Projeto, selecione o nome do projeto em **Destinos** e selecione a guia **Assinatura e Funcionalidades**. Verifique se você selecionou a **Equipe** apropriada para sua conta de Desenvolvedor da Apple. O deverá automaticamente efetuar pull do perfil de provisionamento que você criou anteriormente com base em seu identificador de pacote.

    Se você não vir o novo perfil de provisionamento que você criou no Xcode, tente atualizar os perfis da sua identidade de assinatura. Clique em **Xcode** na barra de menus, em **Preferências**, na guia **Conta**, no botão **Exibir Detalhes**, em sua identidade de assinatura e depois clique no botão Atualizar no canto inferior direito.

    ![Xcode - perfil de provisionamento][9]

4. Na guia **Assinatura e Funcionalidades**, selecione **+ Funcionalidade**.  Clique duas vezes em **Notificações por Push** para habilitá-la.

    ![Xcode – funcionalidades de push][12]

5. Adicione os módulos do SDK dos Hubs de Notificação do Microsoft Azure.

   É possível integrar o SDK dos Hubs de Notificação do Microsoft Azure ao seu aplicativo usando [Cocoapods](https://cocoapods.org) ou adicionando manualmente os binários ao seu projeto.

   - Integração por meio de Cocoapods

     Adicione as dependências a seguir ao seu `podfile` para incluir o SDK dos Hubs de Notificação do Microsoft Azure em seu aplicativo.

     ```ruby
     pod 'AzureNotificationHubs-iOS'
     ```

     Execute `pod install` para instalar seu pod recém-definido e abrir seu `.xcworkspace`.

     > [!NOTE]
     > Se um erro como **[!] Não é possível encontrar uma especificação para AzureNotificationHubs-iOS** for exibido durante a execução de `pod install`, execute `pod repo update` para obter os pods mais recentes do repositório Cocoapods e, em seguida, execute `pod install`.

   - Integração por meio do Carthage

     Adicione as dependências a seguir ao seu `Cartfile` para incluir o SDK dos Hubs de Notificação do Microsoft Azure em seu aplicativo.

     ```ruby
     github "Azure/azure-notificationhubs-ios"
     ```

     Em seguida, atualize e compile as dependências:

     ```shell
     $ carthage update
     ```

     Para obter mais informações sobre como usar o Carthage, confira o [repositório GitHub do Carthage](https://github.com/Carthage/Carthage).

   - Integração copiando os binários em seu projeto

     1. Faça o download da estrutura do [SDK dos Hubs de Notificação do Microsoft Azure](https://github.com/Azure/azure-notificationhubs-ios/releases) fornecido como arquivo zip e descompacte-o.

     2. No Xcode, clique com o botão direito do mouse no projeto e clique na opção **Adicionar Arquivos a** para adicionar a pasta **WindowsAzureMessaging.framework** ao seu projeto do Xcode. Selecione **Opções** e verifique se a opção **Copiar itens se necessário** está selecionada e, em seguida, clique em **Adicionar**.

        ![Descompactar o SDK do Azure][10]

6. Adicione um novo arquivo de cabeçalho a seu projeto chamado **Constants.h**. Para fazer isso, clique com o botão direito do mouse no nome do projeto e selecione **Novo Arquivo…** . Em seguida, selecione **Arquivo de Cabeçalho**. Esse arquivo conterá as constantes para o hub de notificação. Em seguida, selecione **Avançar**. Dê ao arquivo o nome **Constants.h**.

7. Adicione o seguinte código ao arquivo Constants.h:

    ```objc
    #ifndef Constants_h
    #define Constants_h

    extern NSString* const NHInfoConnectionString;
    extern NSString* const NHInfoHubName;
    extern NSString* const NHUserDefaultTags;

    #endif /* Constants_h */
    ```

8. Adicione o arquivo de implementação para Constants.h. Para fazer isso, clique com o botão direito do mouse no nome do projeto e selecione **Novo Arquivo…** . Selecione **Arquivo Objective-C** e, em seguida, selecione **Próximo**. Dê ao arquivo o nome **Constants.m**.

    ![Adicionar o arquivo .m](media/notification-hubs-ios-get-started/new-file-objc.png)

9. Abra o arquivo **Constants.m** e substitua o conteúdo pelo código a seguir. Substitua os espaços reservados de literal de cadeia de caracteres `NotificationHubConnectionString` e `NotificationHubConnectionString` pelo nome do hub e a **DefaultListenSharedAccessSignature**, respectivamente, como você obteve anteriormente do portal:

    ```objc
    #import <Foundation/Foundation.h>
    #import "Constants.h"

    NSString* const NHInfoConnectionString = @"NotificationHubConnectionString";
    NSString* const NHInfoHubName = @"NotificationHubName";
    NSString* const NHUserDefaultTags = @"notification_tags";
    ```

10. Abra o arquivo **AppDelegate.h** do projeto e substitua o conteúdo pelo código a seguir:

    ```objc
    #import <UIKit/UIKit.h>
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    #import <UserNotifications/UserNotifications.h> 

    @interface AppDelegate : UIResponder <UIApplicationDelegate,UNUserNotificationCenterDelegate>

    @property (strong, nonatomic) UIWindow *window;

    - (void)handleRegister;
    - (void)handleUnregister;

    @end

    ```

11. No arquivo **AppDelegate.m** do projeto, adicione as seguintes instruções `import`:

    ```objc
    #import "Constants.h"
    #import "NotificationDetailViewController.h"
    ```

12. Ainda no arquivo **AppDelegate.m**, adicione a seguinte linha de código ao método `didFinishLaunchingWithOptions` com base na sua versão do iOS. Esse código registra seu identificador de dispositivo nas APNs:

    ```objc
    [[UNUserNotificationCenter currentNotificationCenter] setDelegate:self];
    ```

13. No mesmo arquivo **AppDelegate.m**, substitua todo o código após `didFinishLaunchingWithOptions` pelo código a seguir:

    ```objc
    // Tells the app that a remote notification arrived that indicates there is data to be fetched.

    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))completionHandler {
        NSLog(@"Received remote (silent) notification");
        [self logNotificationDetails:userInfo];

        //
        // Let the system know the silent notification has been processed.
        //
        completionHandler(UIBackgroundFetchResultNoData);
    }

    // Tells the delegate that the app successfully registered with Apple Push Notification service (APNs).

    - (void) application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
        NSMutableSet *tags = [[NSMutableSet alloc] init];

        // Load and parse stored tags
        NSString *unparsedTags = [[NSUserDefaults standardUserDefaults] valueForKey:NHUserDefaultTags];
        if (unparsedTags.length > 0) {
            NSArray *tagsArray = [unparsedTags componentsSeparatedByString: @","];
            [tags addObjectsFromArray:tagsArray];
        }

        // Register the device with the Notification Hub.
        // If the device has not already been registered, this will create the registration.
        // If the device has already been registered, this will update the existing registration.
        //
        SBNotificationHub* hub = [self getNotificationHub];
        [hub registerNativeWithDeviceToken:deviceToken tags:tags completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            } else {
                [self showAlert:@"Registered" withTitle:@"Registration Status"];
            }
        }];
    }

    // UNUserNotificationCenterDelegate methods
    //
    // Asks the delegate how to handle a notification that arrived while the app was running in the  foreground.

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler {
        NSLog(@"Received notification while the application is in the foreground");

        // The system calls this delegate method when the app is in the foreground. This allows the app to handle the notification
        // itself (and potentially modify the default system behavior).

        // Handle the notification by displaying custom UI.
        //
        [self showNotification:notification.request.content.userInfo];

        // Use 'options' to specify which default behaviors to enable.
        // - UNAuthorizationOptionBadge: Apply the notification's badge value to the app’s icon.
        // - UNAuthorizationOptionSound: Play the sound associated with the notification.
        // - UNAuthorizationOptionAlert: Display the alert using the content provided by the notification.
        //
        // In this case, do not pass UNAuthorizationOptionAlert because the notification was handled by the app.
        //
        completionHandler(UNAuthorizationOptionBadge | UNAuthorizationOptionSound);
    }

    // Asks the delegate to process the user's response to a delivered notification.
    //

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)(void))completionHandler {
        NSLog(@"Received notification while the application is in the background");

        // The system calls this delegate method when the user taps or responds to the system notification.

        // Handle the notification response by displaying custom UI
        //
        [self showNotification:response.notification.request.content.userInfo];

        // Let the system know the response has been processed.
        //
        completionHandler();
    }

    // App logic and helpers

    - (SBNotificationHub *)getNotificationHub {
        NSString *hubName = [[NSBundle mainBundle] objectForInfoDictionaryKey:NHInfoHubName];
        NSString *connectionString = [[NSBundle mainBundle] objectForInfoDictionaryKey:NHInfoConnectionString];

        return [[SBNotificationHub alloc] initWithConnectionString:connectionString notificationHubPath:hubName];
    }

    - (void)handleRegister {
        UNUserNotificationCenter *center = [UNUserNotificationCenter currentNotificationCenter];

        UNAuthorizationOptions options =  UNAuthorizationOptionAlert | UNAuthorizationOptionSound | UNAuthorizationOptionBadge;
        [center requestAuthorizationWithOptions:(options) completionHandler:^(BOOL granted, NSError * _Nullable error) {
            if (error != nil) {
                NSLog(@"Error requesting for authorization: %@", error);
            }
        }];
        [[UIApplication sharedApplication] registerForRemoteNotifications];
    }

    - (void)handleUnregister {
        //
        // Unregister the device with the Notification Hub.
        //
        SBNotificationHub *hub = [self getNotificationHub];
        [hub unregisterNativeWithCompletion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error unregistering for push: %@", error);
            } else {
                [self showAlert:@"Unregistered" withTitle:@"Registration Status"];
            }
        }];
    }

    - (void)logNotificationDetails:(NSDictionary *)userInfo {
        if (userInfo != nil) {
            UIApplicationState state = [UIApplication sharedApplication].applicationState;
            BOOL background = state != UIApplicationStateActive;
            NSLog(@"Received %@notification: \n%@", background ? @"(background) " : @"", userInfo);
        }
    }

    - (void)showAlert:(NSString *)message withTitle:(NSString *)title {
        if (title == nil) {
            title = @"Alert";
        }
        UIAlertController *alert = [UIAlertController alertControllerWithTitle:title message:message preferredStyle:UIAlertControllerStyleAlert];
        [alert addAction:[UIAlertAction actionWithTitle:@"OK" style:UIAlertActionStyleDefault handler:nil]];
        [[[[UIApplication sharedApplication] keyWindow] rootViewController] presentViewController:alert animated:YES completion:nil];
    }

    - (void)showNotification:(NSDictionary *)userInfo {
        [self logNotificationDetails:userInfo];

        NotificationDetailViewController *notificationDetail = [[NotificationDetailViewController alloc] initWithUserInfo:userInfo];
        [[[[UIApplication sharedApplication] keyWindow] rootViewController] presentViewController:notificationDetail animated:YES completion:nil];
    }

    @end
    ```

    Esse código conecta-se ao hub de notificação usando as informações de conexão que você especificou em **Constants.h**. Esse código fornece o token do dispositivo ao hub de notificação para que o hub de notificação possa enviar notificações.

### <a name="notificationdetailviewcontroller"></a>NotificationDetailViewController

1. Assim como as instruções anteriores, adicione outro arquivo de cabeçalho chamado **NotificationDetailViewController.h**. Substitua o conteúdo do novo arquivo de cabeçalho pelo seguinte código:

    ```objc
    #import <UIKit/UIKit.h>

    NS_ASSUME_NONNULL_BEGIN

    @interface NotificationDetailViewController : UIViewController

    @property (strong, nonatomic) IBOutlet UILabel *titleLabel;
    @property (strong, nonatomic) IBOutlet UILabel *bodyLabel;
    @property (strong, nonatomic) IBOutlet UIButton *dismissButton;

    @property (strong, nonatomic) NSDictionary *userInfo;

    - (id)initWithUserInfo:(NSDictionary *)userInfo;

    @end

    NS_ASSUME_NONNULL_END
    ```

2. Adicione o arquivo de implementação **NotificationDetailViewController.m**. Substitua o conteúdo do arquivo pelo código a seguir, que implementa os métodos `UIViewController`:

    ```objc
    #import "NotificationDetailViewController.h"

    @interface NotificationDetailViewController ()

    @end

    @implementation NotificationDetailViewController

    - (id)initWithUserInfo:(NSDictionary *)userInfo {
        self = [super initWithNibName:@"NotificationDetail" bundle:nil];
        if (self) {
            _userInfo = userInfo;
        }
        return self;
    }

    - (void)viewDidLayoutSubviews {
        [self.titleLabel sizeToFit];
        [self.bodyLabel sizeToFit];
    }

    - (void)viewDidLoad {
        [super viewDidLoad];

        NSString *title = nil;
        NSString *body = nil;

        NSDictionary *aps = [_userInfo valueForKey:@"aps"];
        NSObject *alertObject = [aps valueForKey:@"alert"];
        if (alertObject != nil) {
            if ([alertObject isKindOfClass:[NSDictionary class]]) {
                NSDictionary *alertDict = (NSDictionary *)alertObject;
                title = [alertDict valueForKey:@"title"];
                body = [alertObject valueForKey:@"body"];
            } else if ([alertObject isKindOfClass:[NSString class]]) {
                body = (NSString *)alertObject;
            } else {
                NSLog(@"Unable to parse notification content. Unexpected format: %@", alertObject);
            }
        }

        if (title == nil) {
            title = @"<unset>";
        }

        if (body == nil) {
            body = @"<unset>";
        }

        self.titleLabel.text = title;
        self.bodyLabel.text = body;
    }

    - (IBAction)handleDismiss:(id)sender {
        [self dismissViewControllerAnimated:YES completion:nil];
    }

    @end
    ```

### <a name="viewcontroller"></a>ViewController

1. No arquivo **ViewController.h** do projeto, adicione as seguintes instruções `import`:

    ```objc
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    #import <UserNotifications/UserNotifications.h>
    ```

2. Também em **ViewController.h**, adicione as seguintes declarações de propriedade após a declaração de `@interface`:

    ```objc
    @property (strong, nonatomic) IBOutlet UITextField *tagsTextField;
    @property (strong, nonatomic) IBOutlet UIButton *registerButton;
    @property (strong, nonatomic) IBOutlet UIButton *unregisterButton;
    ```

3. No arquivo de implementação **ViewController.m** do projeto, substitua o conteúdo do arquivo pelo código a seguir:

    ```objc
    #import "ViewController.h"
    #import "Constants.h"
    #import "AppDelegate.h"

    @interface ViewController ()

    @end

    @implementation ViewController

    // UIViewController methods

    - (void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event {
        // Simple method to dismiss keyboard when user taps outside of the UITextField.
        [self.view endEditing:YES];
    }

    - (void)viewDidLoad {
        [super viewDidLoad];

        // Load raw tags text from storage and initialize the text field
        self.tagsTextField.text = [[NSUserDefaults standardUserDefaults] valueForKey:NHUserDefaultTags];
    }

    - (IBAction)handleRegister:(id)sender {
        // Save raw tags text in storage
        [[NSUserDefaults standardUserDefaults] setValue:self.tagsTextField.text forKey:NHUserDefaultTags];

    // Delegate processing the register action to the app delegate.
    [[[UIApplication sharedApplication] delegate] performSelector:@selector(handleRegister)];
    }

    - (IBAction)handleUnregister:(id)sender {
        [[[UIApplication sharedApplication] delegate] performSelector:@selector(handleUnregister)];
    }

    @end
    ```

4. Compile e execute o aplicativo no dispositivo para verificar se não há falhas.

## <a name="send-test-push-notifications"></a>Enviar notificações por push de teste

Você pode testar o recebimento de notificações no aplicativo com a opção *Envio de Teste* no [Azure portal]. Isso envia uma notificação por push de teste para seu dispositivo.

![Portal do Azure – Envio de Teste][30]

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

## <a name="verify-that-your-app-receives-push-notifications"></a>Verifique se seu aplicativo recebe notificações por push

Para testar as notificações por push no iOS, você deve implantar o aplicativo em um dispositivo iOS físico. Não é possível enviar notificações por push da Apple com o Simulador do iOS.

1. Execute o aplicativo e verifique se o registro foi bem-sucedido e pressione **OK**.

    ![Teste de registro de notificação por push de aplicativo do iOS][33]

2. Em seguida, você pode enviar uma notificação por push de teste do [Azure portal], conforme descrito na seção anterior.

3. A notificação por push é enviada a todos os dispositivos que estão registrados para receber as notificações do Hub de Notificação específico.

    ![Teste de recebimento de notificação por push de aplicativo do iOS][35]

## <a name="next-steps"></a>Próximas etapas

Neste exemplo simples, você enviou notificações por push a todos os seus dispositivos iOS registrados. Para saber como enviar notificações por push para dispositivos iOS específicos, vá para o tutorial a seguir:

> [!div class="nextstepaction"]
>[Notificações por push para especificar dispositivos](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

<!-- Images. -->
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png
[12]: ./media/notification-hubs-ios-get-started/notification-hubs-enable-push.png
[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png

<!-- URLs. -->
[Estrutura de Mensagens do Microsoft Azure]: https://go.microsoft.com/fwlink/?LinkID=799698&clcid=0x409
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: https://go.microsoft.com/fwlink/p/?LinkId=272456
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Azure Notification Hubs Notify Users for iOS with .NET backend]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-ios-xplat-segmented-apns-push-notification.md
[Local and Push Notification Programming Guide]: https://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Azure portal]: https://portal.azure.com
