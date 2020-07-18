---
title: Enviar notificações por push para aplicativos Flutter usando os Hubs de Notificação do Azure por meio de um serviço de back-end | Microsoft Docs
description: Saiba como enviar notificações por push para aplicativos Flutter que usam os Hubs de Notificação do Azure por meio de um serviço de back-end.
author: mikeparker104
ms.service: notification-hubs
ms.topic: tutorial
ms.date: 07/07/2020
ms.author: miparker
ms.openlocfilehash: 5fa753a6b8b1284c4f8fcd046f74fabcbae3f8fb
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86170999"
---
# <a name="tutorial-send-push-notifications-to-flutter-apps-using-azure-notification-hubs-via-a-backend-service"></a>Tutorial: Enviar notificações por push para aplicativos Flutter usando os Hubs de Notificação do Azure por meio de um serviço de back-end  

[![Baixar Exemplo](./media/notification-hubs-backend-service-flutter/download.png) Baixar o exemplo](https://github.com/xamcat/mobcat-samples/tree/master/notification_hub_backend_service)  

> [!div class="op_single_selector"]
>
> * [Xamarin.Forms](notification-hubs-backend-service-xamarin-forms.md)
> * [Flutter](notification-hubs-backend-service-flutter.md)
> * [React Native](notification-hubs-backend-service-react-native.md)

Neste tutorial, você usará os [Hubs de Notificação do Azure](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-overview) para enviar notificações por push a um aplicativo [Flutter](https://flutter.dev) direcionado ao **Android** e ao **iOS**.  

[!INCLUDE [Notification Hubs Backend Service Introduction](../../includes/notification-hubs-backend-service-introduction.md)]

Este tutorial conduz você pelas seguintes etapas:

> [!div class="checklist"]
>
> * [Configurar os Serviços de Notificação por Push nos Hubs de Notificação do Azure.](#set-up-push-notification-services-and-azure-notification-hub)
> * [Criar um aplicativo de back-end de API Web do ASP.NET Core.](#create-an-aspnet-core-web-api-backend-application)
> * [Criar um aplicativo Flutter multiplataforma.](#create-a-cross-platform-flutter-application)
> * [Configurar o projeto nativo do Android para notificações por push.](#configure-the-native-android-project-for-push-notifications)
> * [Configurar o projeto nativo do iOS para notificações por push.](#configure-the-native-ios-project-for-push-notifications)
> * [Testar a solução.](#test-the-solution)

## <a name="prerequisites"></a>Pré-requisitos

Para acompanhar, você precisa de:

* Uma [assinatura do Azure](https://portal.azure.com) na qual você possa criar e gerenciar recursos.
* O kit de ferramentas do [Flutter](https://flutter.dev/docs/get-started/install) (junto com seus pré-requisitos).
* [Visual Studio Code](https://code.visualstudio.com) com os plug-ins [Flutter e Dart](https://flutter.dev/docs/get-started/editor?tab=vscode) instalados.
* A capacidade de executar o aplicativo tanto no **Android** (dispositivos físicos ou de emulador) quanto no **iOS** (somente em dispositivos físicos).

Para o Android, é preciso ter:

* Um dispositivo físico desbloqueado para desenvolvedor ou um emulador *(executando a API 26 e posterior com o Google Play Services instalado)* .

Para o iOS, é preciso ter:

* Uma [Conta de Desenvolvedor da Apple](https://developer.apple.com) ativa.
* Um dispositivo iOS físico [registrado na sua conta de desenvolvedor](https://help.apple.com/developer-account/#/dev40df0d9fa) *(executando o iOS 13.0 ou posterior)* .
* Um [certificado de desenvolvimento](https://help.apple.com/developer-account/#/dev04fd06d56) **.p12** instalado em seu **conjunto de chaves**, permitindo que você [execute um aplicativo em um dispositivo físico](https://help.apple.com/xcode/mac/current/#/dev5a825a1ca).
* [CocoaPods](https://guides.cocoapods.org/using/getting-started.html#installation) instalado para gerenciar dependências de biblioteca.

> [!NOTE]
> O simulador do iOS não é compatível com notificações remotas e, portanto, um dispositivo físico é necessário ao explorar este exemplo no iOS. No entanto, não é necessário executar o aplicativo tanto no **Android** quanto no **iOS** para concluir este tutorial.

As etapas neste exemplo de princípios essenciais não requerem experiência prévia. No entanto, você se beneficiará caso tenha familiaridade com os aspectos a seguir.

* [Apple Developer Portal](https://developer.apple.com)
* [ASP.NET Core](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-3.1)
* [Hubs de Notificação do Azure](notification-hubs-push-notification-overview.md)
* [Console do Google Firebase](https://console.firebase.google.com/u/0/)
* [Flutter](https://flutter.dev) e [Dart](https://dart.dev) para desenvolvimento multiplataforma
* [Kotlin](https://kotlinlang.org) e [Swift](https://developer.apple.com/swift) para desenvolvimento nativo para Android e iOS

As etapas fornecidas são específicas para o [macOS](https://developer.apple.com/macos). É possível acompanhar no [Windows](https://www.microsoft.com/windows) ignorando os aspectos do **iOS**.

## <a name="set-up-push-notification-services-and-azure-notification-hub"></a>Configurar os Serviços de Notificação por Push nos Hubs de Notificação do Azure

Nesta seção, você configura o **[FCM (Firebase Cloud Messaging)](https://firebase.google.com/docs/cloud-messaging)** e o **[APNS (Apple Push Notification Service)](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html)** . Em seguida, você cria e configura um hub de notificação para funcionar com esses serviços.

[!INCLUDE [Create a Firebase project and enable Firebase Cloud Messaging](../../includes/notification-hubs-common-enable-firebase-cloud-messaging.md)]

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-common-enable-apple-push-notifications.md)]

[!INCLUDE [Create a notification hub](../../includes/notification-hubs-common-create-notification-hub.md)]

[!INCLUDE [Configure your notification hub with APNs information](../../includes/notification-hubs-common-configure-with-apns-information.md)]

[!INCLUDE [Configure your notification hub with FCM information](../../includes/notification-hubs-common-configure-with-fcm-information.md)]

## <a name="create-an-aspnet-core-web-api-backend-application"></a>Criar um aplicativo de back-end de API Web do ASP.NET Core

Nesta seção, você criará o back-end da [API Web do ASP.NET Core](https://dotnet.microsoft.com/apps/aspnet/apis) para processar o [registro de dispositivos](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#what-is-device-registration) e o envio de notificações ao aplicativo móvel Flutter.

[!INCLUDE [Create an ASP.NET Core Web API backend application](../../includes/notification-hubs-backend-service-web-api.md)]

## <a name="create-a-cross-platform-flutter-application"></a>Criar um aplicativo Flutter multiplataforma

Nesta seção, você criará um aplicativo móvel [Flutter](https://flutter.dev) implementando notificações por push no modo multiplataforma.

[!INCLUDE [Sample application generic overview](../../includes/notification-hubs-backend-service-sample-app-overview.md)]

[!INCLUDE [Create Flutter application](../../includes/notification-hubs-backend-service-sample-app-flutter.md)]

## <a name="configure-the-native-android-project-for-push-notifications"></a>Configurar o projeto nativo do Android para notificações por push

[!INCLUDE [Configure the native Android project](../../includes/notification-hubs-backend-service-configure-flutter-android.md)]

## <a name="configure-the-native-ios-project-for-push-notifications"></a>Configurar o projeto nativo do iOS para notificações por push

[!INCLUDE [Configure the native iOS project](../../includes/notification-hubs-backend-service-configure-flutter-ios.md)]

## <a name="test-the-solution"></a>Testar a solução

Agora é possível testar o envio de notificações por meio do serviço de back-end.

[!INCLUDE [Testing the solution](../../includes/notification-hubs-backend-service-testing.md)]

## <a name="next-steps"></a>Próximas etapas

Agora você tem um aplicativo Flutter básico conectado a um hub de notificação por meio de um serviço de back-end e pode enviar e receber notificações.

[!INCLUDE [Next steps](../../includes/notification-hubs-backend-service-next-steps.md)]

## <a name="troubleshooting"></a>Solução de problemas

[!INCLUDE [Troubleshooting](../../includes/notification-hubs-backend-service-troubleshooting.md)]

## <a name="related-links"></a>Links relacionados

* [Visão geral dos Hubs de Notificação do Azure](notification-hubs-push-notification-overview.md)
* [Como instalar o Flutter no macOS](https://flutter.dev/docs/get-started/install/macos)
* [Como instalar o Flutter no Windows](https://flutter.dev/docs/get-started/install/windows)
* [SDK dos Hubs de Notificação para operações de back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
* [SDKs dos Hubs de Notificação do GitHub](https://github.com/Azure/azure-notificationhubs)
* [Registrar com o back-end de aplicativo](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
* [Gerenciamento de registros](notification-hubs-push-notification-registration-management.md)
* [Como trabalhar com marcas](notification-hubs-tags-segment-push-message.md)
* [Como trabalhar com modelos personalizados](notification-hubs-templates-cross-platform-push-messages.md)
