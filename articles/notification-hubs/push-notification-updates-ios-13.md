---
title: Hubs de notificação do Azure-atualizações do iOS Microsoft Docs
description: Saiba mais sobre as alterações significativas do iOS em hubs de notificação do Azure
author: sethmanheim
ms.author: sethm
ms.date: 10/16/2019
ms.topic: article
ms.service: notification-hubs
ms.reviewer: jowargo
ms.lastreviewed: 10/16/2019
ms.openlocfilehash: 697e8ba9c9f27e8d5644e3a78950ff006290efe7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "74228138"
---
# <a name="azure-notification-hubs-updates-for-ios-13"></a>Atualizações dos hubs de notificação do Azure para iOS 13

A Apple fez recentemente algumas alterações no serviço de envio público; as alterações alinhadas principalmente com as versões do iOS 13 e do Xcode. Este artigo descreve o impacto dessas alterações nos hubs de notificação do Azure.

## <a name="apns-push-payload-changes"></a>Alterações de conteúdo de push de APNS

### <a name="apns-push-type"></a>Tipo de push APNS

Agora, a Apple exige que os desenvolvedores identifiquem notificações como um alerta ou notificações `apns-push-type` de segundo plano por meio do novo cabeçalho na API do APNS. De acordo com a [documentação da Apple](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns): "o valor desse cabeçalho deve refletir com precisão o conteúdo da carga da notificação. Se houver uma incompatibilidade ou se o cabeçalho estiver ausente nos sistemas necessários, o APNs poderá retornar um erro, atrasar a entrega da notificação ou soltá-la completamente. "

Os desenvolvedores agora devem definir esse cabeçalho em aplicativos que enviam notificações por meio dos hubs de notificação do Azure. Devido a uma limitação técnica, os clientes devem usar a autenticação baseada em token para credenciais de APNS com solicitações que incluem esse atributo. Se você estiver usando a autenticação baseada em certificado para suas credenciais de APNS, será necessário alternar para o uso da autenticação baseada em token.

Os exemplos de código a seguir mostram como definir esse atributo de cabeçalho em solicitações de notificação enviadas por meio dos hubs de notificação do Azure.

#### <a name="template-notifications---net-sdk"></a>Notificações de modelo-SDK do .NET

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var tempprop = new Dictionary<string, string> {{"message", "value"}};
var notification = new TemplateNotification(tempprop);
notification.Headers = headers;
await hub.SendNotificationAsync(notification);
```

#### <a name="native-notifications---net-sdk"></a>Notificações nativas-SDK do .NET

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

#### <a name="direct-rest-calls"></a>Chamadas REST diretas

```csharp
var request = new HttpRequestMessage(method, $"<resourceUri>?api-version=2017-04");
request.Headers.Add("Authorization", createToken(resourceUri, KEY_NAME, KEY_VALUE));
request.Headers.Add("ServiceBusNotification-Format", "apple");
request.Headers.Add("apns-push-type", "alert");
```

Para ajudá-lo durante essa transição, quando os hubs de notificação do Azure detectam uma `apns-push-type` notificação que não tem o conjunto, o serviço infere o tipo de push da solicitação de notificação e define o valor automaticamente. Lembre-se de que você deve configurar os hubs de notificação do Azure para usar a autenticação baseada em token para definir o cabeçalho necessário; para obter mais informações, consulte [autenticação baseada em token (http/2) para APNS](notification-hubs-push-notification-http2-token-authentification.md).

## <a name="apns-priority"></a>Prioridade de APNS

Outra alteração secundária, mas uma que requer uma alteração no aplicativo de back-end que envia notificações, é o requisito de notificações em `apns-priority` segundo plano em que o cabeçalho agora deve ser definido como 5. Muitos aplicativos definem `apns-priority` o cabeçalho como 10 (indicando entrega imediata) ou não os definem e obtêm o valor padrão (que também é 10).

Definir esse valor como 10 não é mais permitido para notificações em segundo plano e você deve definir o valor para cada solicitação. A Apple não fornecerá notificações em segundo plano se esse valor estiver ausente. Por exemplo:

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "background"}, { "apns-priority", "5" }};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

## <a name="sdk-changes"></a>Alterações do SDK

Durante anos, os desenvolvedores do iOS `description` usaram o atributo `deviceToken` dos dados enviados ao delegado do token de push para extrair o token de push usado por um aplicativo de back-end para enviar notificações ao dispositivo. Com o Xcode 11, `description` esse atributo foi alterado para um formato diferente. O código existente que os desenvolvedores usavam para este atributo agora está quebrado. Atualizamos o SDK dos hubs de notificação do Azure para acomodar essa alteração. portanto, atualize o SDK usado por seus aplicativos para a versão 2.0.4 ou mais recente do [SDK do IOS dos hubs de notificação do Azure](https://github.com/Azure/azure-notificationhubs-ios).
