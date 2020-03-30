---
title: Azure Notification Hubs iOS 13 atualizações | Microsoft Docs
description: Saiba mais sobre as mudanças no iOS 13 nos Hubs de Notificação do Azure
author: sethmanheim
ms.author: sethm
ms.date: 10/16/2019
ms.topic: article
ms.service: notification-hubs
ms.reviewer: jowargo
ms.lastreviewed: 10/16/2019
ms.openlocfilehash: 697e8ba9c9f27e8d5644e3a78950ff006290efe7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74228138"
---
# <a name="azure-notification-hubs-updates-for-ios-13"></a>Azure Notification Hubs atualiza ções para iOS 13

A Apple fez recentemente algumas mudanças em seu serviço público de push; as mudanças estão mais alinhadas com as versões do iOS 13 e Xcode. Este artigo descreve o impacto dessas alterações nos Hubs de Notificação do Azure.

## <a name="apns-push-payload-changes"></a>APNS empurram alterações de carga útil

### <a name="apns-push-type"></a>Tipo de push APNS

A Apple agora exige que os desenvolvedores identifiquem `apns-push-type` as notificações como um alerta ou notificações em segundo plano através do novo cabeçalho na API APNS. De acordo com a [documentação da Apple:](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns)"O valor deste cabeçalho deve refletir com precisão o conteúdo da carga útil da sua notificação. Se houver uma incompatibilidade ou se o cabeçalho estiver faltando nos sistemas necessários, as APNs podem retornar um erro, atrasar a entrega da notificação ou solí-la completamente."

Os desenvolvedores agora devem definir esse cabeçalho em aplicativos que enviam notificações através do Azure Notification Hubs. Devido a uma limitação técnica, os clientes devem usar autenticação baseada em token para credenciais APNS com solicitações que incluem esse atributo. Se você estiver usando autenticação baseada em certificado suspirar para suas credenciais APNS, você deve mudar para o uso de autenticação baseada em token.

As amostras de código a seguir mostram como definir esse atributo de cabeçalho nas solicitações de notificação enviadas através dos Hubs de Notificação do Azure.

#### <a name="template-notifications---net-sdk"></a>Notificações de modelo - .NET SDK

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var tempprop = new Dictionary<string, string> {{"message", "value"}};
var notification = new TemplateNotification(tempprop);
notification.Headers = headers;
await hub.SendNotificationAsync(notification);
```

#### <a name="native-notifications---net-sdk"></a>Notificações nativas - .NET SDK

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

#### <a name="direct-rest-calls"></a>Chamadas direct rest

```csharp
var request = new HttpRequestMessage(method, $"<resourceUri>?api-version=2017-04");
request.Headers.Add("Authorization", createToken(resourceUri, KEY_NAME, KEY_VALUE));
request.Headers.Add("ServiceBusNotification-Format", "apple");
request.Headers.Add("apns-push-type", "alert");
```

Para ajudá-lo durante essa transição, quando o Azure Notification `apns-push-type` Hubs detecta uma notificação que não tem o conjunto, o serviço infere o tipo de push da solicitação de notificação e define o valor automaticamente. Lembre-se de que você deve configurar os Hubs de notificação do Azure para usar a autenticação baseada em token satisfatoita para definir o cabeçalho necessário; para obter mais informações, consulte [autenticação baseada em Token (HTTP/2) para APNS](notification-hubs-push-notification-http2-token-authentification.md).

## <a name="apns-priority"></a>Prioridade da APNS

Outra alteração menor, mas que requer uma alteração no aplicativo backend que envia `apns-priority` notificações, é a exigência de que para notificações em segundo plano o cabeçalho deve agora ser definido como 5. Muitos aplicativos definem o `apns-priority` cabeçalho como 10 (indicando entrega imediata), ou não o definem e obtêm o valor padrão (que também é 10).

Definir esse valor como 10 não é mais permitido para notificações em segundo plano, e você deve definir o valor para cada solicitação. A Apple não fornecerá notificações em segundo plano se esse valor estiver faltando. Por exemplo: 

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "background"}, { "apns-priority", "5" }};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

## <a name="sdk-changes"></a>Mudanças no SDK

Durante anos, os desenvolvedores `description` do `deviceToken` iOS usaram o atributo dos dados enviados ao delegado push token para extrair o token push que um aplicativo back-end usa para enviar notificações ao dispositivo. Com o Xcode `description` 11, esse atributo mudou para um formato diferente. O código existente que os desenvolvedores usaram para este atributo está agora quebrado. Atualizamos o SDK do Azure Notification Hubs para acomodar essa alteração, por isso atualize o SDK usado por seus aplicativos para a versão 2.0.4 ou mais recente do [Azure Notification Hubs iOS SDK](https://github.com/Azure/azure-notificationhubs-ios).
