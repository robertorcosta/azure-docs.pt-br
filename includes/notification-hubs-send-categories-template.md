---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 03/30/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 299f92484000cb5c59291a5af87f24d89a771fee
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72296778"
---
Nesta seção, você envia as últimas notícias como notificações de modelo marcadas, de um aplicativo de console do .NET.

1. No Visual Studio, crie um novo aplicativo de console em Visual C#:
    1. No menu, selecione **Arquivo** > **Novo** > **Projeto**.
    1. Em **Criar um projeto**, selecione **Aplicativo de Console (.NET Framework)** para C# na lista de modelos e selecione **Avançar**.
    1. Insira um nome do aplicativo.
    1. Para **Solução**, escolha **Adicionar à solução** e selecione **Criar** para criar o projeto.

1. Selecione **Ferramentas** > **Gerenciador de Pacotes NuGet** > **Console do Gerenciador do NuGet** e, em seguida, na janela do console, execute o seguinte comando:

   ```powershell
   Install-Package Microsoft.Azure.NotificationHubs
   ```

   Essa ação adiciona uma referência ao SDK dos Hubs de Notificação do Azure usando o pacote [Microsoft.Azure.NotificationHubs].

1. Abra o arquivo *Program.cs* e adicione a seguinte instrução `using`:

   ```csharp
   using Microsoft.Azure.NotificationHubs;
   ```

1. Na classe `Program` , adicione o seguinte método ou substitua-o, se ele já existir:

    ```csharp
    private static async void SendTemplateNotificationAsync()
    {
        // Define the notification hub.
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");

        // Create an array of breaking news categories.
        var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};

        // Send the notification as a template notification. All template registrations that contain
        // "messageParam" and the proper tags will receive the notifications.
        // This includes APNS, GCM, WNS, and MPNS template registrations.

        Dictionary<string, string> templateParams = new Dictionary<string, string>();

        foreach (var category in categories)
        {
            templateParams["messageParam"] = "Breaking " + category + " News!";
            await hub.SendTemplateNotificationAsync(templateParams, category);
        }
    }
    ```

   Esse código envia uma notificação de modelo para cada uma das seis marcas na matriz de cadeia de caracteres. O uso de marcas garante que os dispositivos recebam notificações somente para as categorias de marcas.

1. No código anterior, substitua os espaços reservados `<hub name>` e `<connection string with full access>` pelo nome do hub de notificação e a cadeia de conexão por *DefaultFullSharedAccessSignature* do painel do hub de notificação.

1. No método `Main()`, adicione as seguintes linhas:

   ```csharp
    SendTemplateNotificationAsync();
    Console.ReadLine();
    ```

1. Compile o aplicativo de console.

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

<!-- URLs. -->
[Get started with Notification Hubs]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Notification Hubs REST interface]: https://msdn.microsoft.com/library/windowsazure/dn223264.aspx
[Add push notifications for Mobile Apps]: ../articles/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md
[How to use Notification Hubs from Java or PHP]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[Microsoft.Azure.NotificationHubs]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/
