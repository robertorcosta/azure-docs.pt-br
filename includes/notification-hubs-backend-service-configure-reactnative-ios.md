---
author: alexeystrakh
ms.author: alstrakh
ms.date: 06/11/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: e67f53e26f2081e1aa12f7c0e6a219c240f5027a
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060396"
---
### <a name="configure-required-ios-packages"></a>Configurar os pacotes necessários do iOS

O pacote é [vinculado automaticamente](https://github.com/react-native-community/cli/blob/master/docs/autolinking.md) durante o build do aplicativo. Basta instalar os pods nativos:

```bash
npx pod-install
```

### <a name="configure-infoplist-and-entitlementsplist"></a>Configurar Info.plist and Entitlements.plist

1. Acesse a pasta "PushDemo/ios" e abra o workspace "PushDemo.xcworkspace", selecione o projeto superior "PushDemo" e a guia "Assinatura e Funcionalidades".

1. Atualize o identificador de pacote para que ele corresponda ao valor usado no perfil de provisão.

1. Adicione duas novas funcionalidades usando o botão "+":

    - Funcionalidade de Modo em Segundo Plano e seleção de Notificações Remotas.
    - Funcionalidade de Notificações por Push

### <a name="handle-push-notifications-for-ios"></a>Manipular notificações por push para iOS

1. Abra "AppDelegate.h" e adicione a seguinte importação:

    ```objective-c
    #import <UserNotifications/UNUserNotificationCenter.h>
    ```

1. Atualize a lista de protocolos, com suporte do "AppDelegate", adicionando `UNUserNotificationCenterDelegate`:

    ```objective-c
    @interface AppDelegate : UIResponder <UIApplicationDelegate, RCTBridgeDelegate, UNUserNotificationCenterDelegate>
    ```

1. Abra "AppDelegate.m" e configure todos os retornos de chamada do iOS necessários:

    ```objective-c
    #import <UserNotifications/UserNotifications.h>
    #import <RNCPushNotificationIOS.h>

    ...

    // Required to register for notifications
    - (void)application:(UIApplication *)application didRegisterUserNotificationSettings:(UIUserNotificationSettings *)notificationSettings
    {
     [RNCPushNotificationIOS didRegisterUserNotificationSettings:notificationSettings];
    }

    // Required for the register event.
    - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
    {
     [RNCPushNotificationIOS didRegisterForRemoteNotificationsWithDeviceToken:deviceToken];
    }

    // Required for the notification event. You must call the completion handler after handling the remote notification.
    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo
    fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
    {
      [RNCPushNotificationIOS didReceiveRemoteNotification:userInfo fetchCompletionHandler:completionHandler];
    }

    // Required for the registrationError event.
    - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:(NSError *)error
    {
     [RNCPushNotificationIOS didFailToRegisterForRemoteNotificationsWithError:error];
    }

    // IOS 10+ Required for localNotification event
    - (void)userNotificationCenter:(UNUserNotificationCenter *)center
    didReceiveNotificationResponse:(UNNotificationResponse *)response
             withCompletionHandler:(void (^)(void))completionHandler
    {
      [RNCPushNotificationIOS didReceiveNotificationResponse:response];
      completionHandler();
    }

    // IOS 4-10 Required for the localNotification event.
    - (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification
    {
     [RNCPushNotificationIOS didReceiveLocalNotification:notification];
    }

    //Called when a notification is delivered to a foreground app.
    -(void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      completionHandler(UNAuthorizationOptionSound | UNAuthorizationOptionAlert | UNAuthorizationOptionBadge);
    }
    ```
