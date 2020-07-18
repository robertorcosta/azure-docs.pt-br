---
title: Enviar notificações por push para aplicativos React Native usando os Hubs de Notificação do Azure por meio de um serviço de back-end | Microsoft Docs
description: Saiba como enviar notificações por push para aplicativos React Native que usam os Hubs de Notificação do Azure por meio de um serviço de back-end.
author: alexeystrakh
ms.service: notification-hubs
ms.topic: tutorial
ms.date: 06/11/2020
ms.author: alstrakh
ms.openlocfilehash: 007be386b7b64fd3461fa508d35a4ef9be377c1f
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86170896"
---
# <a name="tutorial-send-push-notifications-to-react-native-apps-using-azure-notification-hubs-via-a-backend-service"></a>Tutorial: Enviar notificações por push para aplicativos React Native usando os Hubs de Notificação do Azure por meio de um serviço de back-end  

[![Baixar Exemplo](./media/notification-hubs-backend-service-react-native/download.png) Baixar o exemplo](https://github.com/xamcat/mobcat-samples/tree/master/notification_hub_backend_service)  

> [!div class="op_single_selector"]
>
> * [Xamarin.Forms](notification-hubs-backend-service-xamarin-forms.md)
> * [Flutter](notification-hubs-backend-service-flutter.md)
> * [React Native](notification-hubs-backend-service-react-native.md)

Neste tutorial, você usará os [Hubs de Notificação do Azure](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-overview) para enviar notificações por push a um aplicativo [React Native](https://reactnative.dev/) direcionado ao **Android** e ao **iOS**.  

[!INCLUDE [Notification Hubs Backend Service Introduction](../../includes/notification-hubs-backend-service-introduction.md)]

Este tutorial conduz você pelas seguintes etapas:

> [!div class="checklist"]
>
> * [Configurar os Serviços de Notificação por Push nos Hubs de Notificação do Azure.](#set-up-push-notification-services-and-azure-notification-hub)
> * [Criar um aplicativo de back-end de API Web do ASP.NET Core.](#create-an-aspnet-core-web-api-backend-application)
> * [Criar um aplicativo React Native multiplataforma.](#create-a-cross-platform-react-native-application)
> * [Configurar o projeto nativo do Android para notificações por push.](#configure-the-native-android-project-for-push-notifications)
> * [Configurar o projeto nativo do iOS para notificações por push.](#configure-the-native-ios-project-for-push-notifications)
> * [Testar a solução.](#test-the-solution)

## <a name="prerequisites"></a>Pré-requisitos

Para acompanhar, você precisa de:

* Uma [assinatura do Azure](https://portal.azure.com) na qual você possa criar e gerenciar recursos.
* Um Mac com [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) instalado (ou um PC executando o [Visual Studio 2019](https://visualstudio.microsoft.com/vs) com a carga de trabalho **Desenvolvimento Móvel com o .NET**).
* A capacidade de executar o aplicativo tanto no **Android** (dispositivos físicos ou de emulador) quanto no **iOS** (somente em dispositivos físicos).

Para o Android, é preciso ter:

* Um dispositivo físico desbloqueado para desenvolvedor ou um emulador *(executando a API 26 e posterior com o Google Play Services instalado)* .

Para o iOS, é preciso ter:

* Uma [Conta de Desenvolvedor da Apple](https://developer.apple.com) ativa.
* Um dispositivo iOS físico [registrado na sua conta de desenvolvedor](https://help.apple.com/developer-account/#/dev40df0d9fa) *(executando o iOS 13.0 ou posterior)* .
* Um [certificado de desenvolvimento](https://help.apple.com/developer-account/#/dev04fd06d56) **.p12** instalado em seu **conjunto de chaves**, permitindo que você [execute um aplicativo em um dispositivo físico](https://help.apple.com/xcode/mac/current/#/dev5a825a1ca).

> [!NOTE]
> O simulador do iOS não é compatível com notificações remotas e, portanto, um dispositivo físico é necessário ao explorar este exemplo no iOS. No entanto, não é necessário executar o aplicativo tanto no **Android** quanto no **iOS** para concluir este tutorial.

As etapas neste exemplo de princípios essenciais não requerem experiência prévia. No entanto, você se beneficiará caso tenha familiaridade com os aspectos a seguir.

* [Apple Developer Portal](https://developer.apple.com)
* [ASP.NET Core](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-3.1)
* [Hubs de Notificação do Azure](notification-hubs-push-notification-overview.md)
* [Console do Google Firebase](https://console.firebase.google.com/u/0/)
* [React Native](https://reactnative.dev/docs/getting-started)

As etapas fornecidas destinam-se ao [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) e ao [Visual Studio Code](https://code.visualstudio.com/download), mas é possível segui-las usando o [Visual Studio 2019](https://visualstudio.microsoft.com/vs).

## <a name="set-up-push-notification-services-and-azure-notification-hub"></a>Configurar os Serviços de Notificação por Push nos Hubs de Notificação do Azure

Nesta seção, você configura o **[FCM (Firebase Cloud Messaging)](https://firebase.google.com/docs/cloud-messaging)** e o **[APNS (Apple Push Notification Service)](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html)** . Em seguida, você cria e configura um hub de notificação para funcionar com esses serviços.

[!INCLUDE [Create a Firebase project and enable Firebase Cloud Messaging](../../includes/notification-hubs-common-enable-firebase-cloud-messaging.md)]

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-common-enable-apple-push-notifications.md)]

[!INCLUDE [Create a notification hub](../../includes/notification-hubs-common-create-notification-hub.md)]

[!INCLUDE [Configure your notification hub with APNs information](../../includes/notification-hubs-common-configure-with-apns-information.md)]

[!INCLUDE [Configure your notification hub with FCM information](../../includes/notification-hubs-common-configure-with-fcm-information.md)]

## <a name="create-an-aspnet-core-web-api-backend-application"></a>Criar um aplicativo de back-end de API Web do ASP.NET Core

Nesta seção, você criará o back-end da [API Web do ASP.NET Core](https://dotnet.microsoft.com/apps/aspnet/apis) para processar o [registro de dispositivos](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#what-is-device-registration) e o envio de notificações ao aplicativo móvel React Native.

[!INCLUDE [Create an ASP.NET Core Web API backend application](../../includes/notification-hubs-backend-service-web-api.md)]

## <a name="create-a-cross-platform-react-native-application"></a>Criar um aplicativo React Native multiplataforma

Nesta seção, você criará um aplicativo móvel [React Native](https://reactnative.dev/) implementando notificações por push no modo multiplataforma.

[!INCLUDE [Sample application generic overview](../../includes/notification-hubs-backend-service-sample-app-overview.md)]

[!INCLUDE [Create React Native application](../../includes/notification-hubs-backend-service-sample-app-reactnative.md)]

## <a name="configure-the-native-android-project-for-push-notifications"></a>Configurar o projeto nativo do Android para notificações por push

[!INCLUDE [Configure the native Android project](../../includes/notification-hubs-backend-service-configure-reactnative-android.md)]

## <a name="configure-the-native-ios-project-for-push-notifications"></a>Configurar o projeto nativo do iOS para notificações por push

[!INCLUDE [Configure the native iOS project](../../includes/notification-hubs-backend-service-configure-reactnative-ios.md)]

## <a name="test-the-solution"></a>Testar a solução

Agora é possível testar o envio de notificações por meio do serviço de back-end.

[!INCLUDE [Testing the solution](../../includes/notification-hubs-backend-service-testing.md)]

## <a name="next-steps"></a>Próximas etapas

Agora você tem um aplicativo React Native básico conectado a um hub de notificação por meio de um serviço de back-end e pode enviar e receber notificações.

[!INCLUDE [Next steps](../../includes/notification-hubs-backend-service-next-steps.md)]

## <a name="troubleshooting"></a>Solução de problemas

[!INCLUDE [Troubleshooting](../../includes/notification-hubs-backend-service-troubleshooting.md)]

## <a name="related-links"></a>Links relacionados

* [Visão geral dos Hubs de Notificação do Azure](notification-hubs-push-notification-overview.md)
* [Como instalar o Visual Studio para Mac](https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019)
* [Como instalar o Visual Studio Code](https://code.visualstudio.com/download)
* [Como configurar o ambiente de desenvolvimento React Native](https://reactnative.dev/docs/environment-setup)
* [SDK dos Hubs de Notificação para operações de back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
* [SDKs dos Hubs de Notificação do GitHub](https://github.com/Azure/azure-notificationhubs)
* [Registrar com o back-end de aplicativo](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
* [Gerenciamento de registros](notification-hubs-push-notification-registration-management.md)
* [Como trabalhar com marcas](notification-hubs-tags-segment-push-message.md)
* [Como trabalhar com modelos personalizados](notification-hubs-templates-cross-platform-push-messages.md)
