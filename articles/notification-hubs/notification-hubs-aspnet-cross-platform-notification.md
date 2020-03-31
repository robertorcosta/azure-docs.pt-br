---
title: Enviar notificações de multiplaforma para os usuários com os Hubs de Notificação do Azure (ASP.NET)
description: Saiba como usar modelos de Hubs de notificação para enviar, em uma única solicitação, uma notificação independente de plataforma que se destina a todas as plataformas.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 11d2131b-f683-47fd-a691-4cdfc696f62b
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: multiple
ms.topic: article
ms.date: 09/30/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: fc3cec348517244c8a7f54d2d3d17298c58e4262
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127039"
---
# <a name="send-cross-platform-notifications-with-azure-notification-hubs"></a>Envie notificações entre plataformas com hubs de notificação do Azure

Este tutorial se baseia no tutorial anterior, [enviar notificações para usuários específicos usando o Azure Notification Hubs]. Esse tutorial descreve como empurrar notificações para todos os dispositivos que estão registrados para um usuário autenticado específico. Essa abordagem exigia várias solicitações para enviar uma notificação a cada plataforma de cliente suportada. Os Hubs de Notificação do Azure dão suporte a modelos, com os quais é possível especificar como um dispositivo específico deseja receber notificações. Esse método simplifica o envio de notificações de multiplaforma.

Este artigo demonstra como aproveitar os modelos para enviar uma notificação que visa todas as plataformas. Este artigo usa uma única solicitação para enviar uma notificação neutra da plataforma. Para obter informações mais detalhadas sobre modelos, consulte [Visão geral dos Hubs de Notificação][Templates].

> [!IMPORTANT]
> Os projetos do Windows Phone 8.1 e anteriores não são suportados no Visual Studio 2019. Para saber mais, confira [Direcionamento e Compatibilidade da Plataforma Visual Studio 2019](/visualstudio/releases/2019/compatibility).

> [!NOTE]
> Com os Hubs de Notificação, um dispositivo pode registrar vários modelos usando a mesma tag. Nesse caso, uma mensagem de entrada direcionada à tag resulta na entrega de várias notificações ao dispositivo, uma para cada modelo. Esse processo permite exibir a mesma mensagem em várias notificações visuais, como uma notificação e como uma notificação do sistema em um aplicativo da Windows Store.

## <a name="send-cross-platform-notifications-using-templates"></a>Enviar notificações multiplataforma usando modelos

Esta seção usa o código de exemplo que você construiu nas notificações enviar para usuários específicos usando o tutorial [do Azure Notification Hubs.] É possível baixar o exemplo do [GitHub](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/NotifyUsers).

Para enviar notificações entre plataformas usando modelos, faça as seguintes etapas:

1. No Visual Studio in **Solution Explorer,** expanda a pasta **Controladores** e abra o *arquivo RegisterController.cs.*

1. Localize o bloco do código no método `Put` que cria um novo registro e, em seguida, substitua o conteúdo `switch` pelo seguinte código:

    ```csharp
    switch (deviceUpdate.Platform)
    {
        case "mpns":
            var toastTemplate = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                    "<wp:Toast>" +
                        "<wp:Text1>$(message)</wp:Text1>" +
                    "</wp:Toast> " +
                "</wp:Notification>";
            registration = new MpnsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
            break;
        case "wns":
            toastTemplate = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
            registration = new WindowsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
            break;
        case "apns":
            var alertTemplate = "{\"aps\":{\"alert\":\"$(message)\"}}";
            registration = new AppleTemplateRegistrationDescription(deviceUpdate.Handle, alertTemplate);
            break;
        case "fcm":
            var messageTemplate = "{\"data\":{\"message\":\"$(message)\"}}";
            registration = new FcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
            break;
        default:
            throw new HttpResponseException(HttpStatusCode.BadRequest);
    }
    ```

    Esse código chama o método da plataforma específica para criar um registro de modelo em vez de um registro nativo. Como os registros de modelo são derivados de registros nativos, você não precisa modificar os registros existentes.

1. No **Solution Explorer,** na pasta **Controladores,** abra o *arquivo NotificationsController.cs.* Substitua o método `Post` pelo seguinte código:

    ```csharp
    public async Task<HttpResponseMessage> Post()
    {
        var user = HttpContext.Current.User.Identity.Name;
        var userTag = "username:" + user;

        var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
        await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);

        return Request.CreateResponse(HttpStatusCode.OK);
    }
    ```

    Este código envia uma notificação para todas as plataformas ao mesmo tempo. Você não especifica uma carga nativa. Os Hubs de Notificação desenvolve e fornece a carga correta para cada dispositivo com o valor marca fornecido, conforme especificado nos modelos registrados.

1. Republique seu projeto de API web.

1. Execute o aplicativo do cliente novamente para verificar se o registro foi bem sucedido.

1. Opcionalmente, implante o aplicativo cliente em um segundo dispositivo e execute o aplicativo. Uma notificação é exibida em cada dispositivo.

## <a name="next-steps"></a>Próximas etapas

Agora que você completou este tutorial, saiba mais sobre Hubs de Notificação e modelos nestes artigos:

* Para obter um cenário diferente para o uso de modelos, consulte as [notificações push para dispositivos Windows específicos executando][Use Notification Hubs to send breaking news] o tutorial de aplicativos do Universal Windows Platform.
* Para obter informações mais detalhadas sobre modelos, consulte [Visão geral dos Hubs de Notificação][Templates].

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Push to users ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Push to users Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Visual Studio 2012 Express for Windows 8]: https://visualstudio.microsoft.com/downloads/

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: https://go.microsoft.com/fwlink/p/?LinkId=314257
[Enviar notificações para usuários específicos usando o Hubs de Notificação do Microsoft Azure]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: https://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: https://msdn.microsoft.com/library/windowsazure/jj927172.aspx
