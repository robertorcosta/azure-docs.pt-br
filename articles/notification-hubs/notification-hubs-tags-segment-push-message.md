---
title: Expressões de roteamento e tag em Hubs de Notificação do Azure
description: Saiba como fazer rotas e tag expressões para os Hubs de Notificação do Azure.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 0fffb3bb-8ed8-4e0f-89e8-0de24a47f644
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 12/09/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 12/04/2019
ms.openlocfilehash: 2432ac41645e373ea3a87ff7e69ef02a4e30c81d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062304"
---
# <a name="routing-and-tag-expressions"></a>Expressões de marca e roteamento

## <a name="overview"></a>Visão geral

As expressões de marcas permitem que você direcione conjuntos específicos de dispositivos, ou mais especificamente, registros, ao enviar uma notificação por push por meio de Hubs de notificação.

## <a name="targeting-specific-registrations"></a>Como direcionar registros específicos

A única maneira de direcionar registros de notificações específicos é associar marcas a eles e, então, direcionar essas marcas. Conforme discutido no [Gerenciamento de Registro,](notification-hubs-push-notification-registration-management.md)para receber notificações push, um aplicativo deve registrar uma alça de dispositivo em um hub de notificação. Uma vez que o aplicativo crie um registro em um hub de notificação, o backend do aplicativo pode enviar notificações push para ele. O back-end do aplicativo pode escolher os registros para direcionar uma notificação específica das seguintes maneiras:

1. **Difusão**: todos os registros no hub de notificação recebem a notificação.
2. **Marca**: todos os registros que contêm a marca especificada recebem a notificação.
3. **Expressão de marca**: todos os registros cujos conjuntos de marcas correspondem à expressão especificada recebem a notificação.

## <a name="tags"></a>Marcas

Uma tag pode ser qualquer string, até 120 caracteres, contendo alfanuméricas e os`_`seguintes`@`caracteres`#`não`.`alfanuméricos:`-`' '' '' '', ''' ''' ''' '''''''''''''''.`:` O exemplo a seguir mostra um aplicativo do qual você pode receber notificações de aviso sobre grupos musicais específicos. Nesse cenário, uma maneira simples de encaminhar notificações é rotular registros com tags que representam as diferentes bandas, como na seguinte figura:

![Visão geral das tags](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags.png)

Na figura, a mensagem marcada com **os Beatles** atinge apenas o tablet que registrou com a tag **Beatles**.

Para mais informações sobre a criação de registros para marcas, consulte [Gerenciamento de registro](notification-hubs-push-notification-registration-management.md).

Você pode enviar notificações para marcas usando os métodos de notificações de envio da classe `Microsoft.Azure.NotificationHubs.NotificationHubClient` no SDK dos [Hubs de Notificação do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). Você também pode usar o Node. js ou as APIs de REST de notificações por push.  Aqui está um exemplo usando o SDK.

```csharp
Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

// Windows 8.1 / Windows Phone 8.1
var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
"You requested a Beatles notification</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Beatles");

// Windows 10
toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
"You requested a Wailers notification</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Wailers");
```

As tags não devem ser pré-provisionadas e podem se referir a vários conceitos específicos do aplicativo. Por exemplo, os usuários deste aplicativo de exemplo podem comentar sobre as bandas e desejar receber notificações do sistema, não apenas dos comentários sobre suas bandas favoritas, mas também de todos os comentários dos seus amigos, independentemente da banda sobre a qual eles estão comentando. A figura a seguir destaca um exemplo deste cenário:

![Tags amigos](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags2.png)

Neste exemplo, Alice está interessada em atualizações para os Beatles, e Bob está interessado em atualizações para os Wailers. Bob também está interessado nos comentários de Charlie, e Charlie está interessado nos Wailers. Quando uma notificação é enviada para o comentário de Charlie sobre os Beatles, o Notification Hubs envia-o tanto para Alice quanto para Bob.

Embora você possa codificar várias preocupações em tags (por exemplo, `band_Beatles` ou `follows_Charlie`), as tags são strings simples e não propriedades com valores. Um registro corresponde apenas à presença ou ausência de uma tag específica.

Para obter um tutorial passo a passo completo sobre como usar marcas para enviar para grupos de interesse, consulte as [Últimas notícias](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md).

> [!NOTE]
> Os Hubs de Notificação do Azure dão suporte a um máximo de 60 marcas por registro.

## <a name="using-tags-to-target-users"></a>Como usar marcas para usuários de destino

Outra maneira de usar tags é identificar todos os dispositivos associados a um determinado usuário. Você pode marcar um Registro com uma tag que contém o ID do usuário, como na seguinte figura:

![Usuários de tag](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags3.png)

Na figura, a mensagem marcada `user_Alice` atinge todos `user_Alice`os dispositivos marcados com .

## <a name="tag-expressions"></a>Expressões de marca

Há casos em que as notificações devem ter como alvo um conjunto de registros identificados não por uma única tag, mas por uma expressão booleana usando tags.

Considere um aplicativo de esportes que envia um lembrete para todos em Boston sobre um jogo entre o Red Sox e o Cardinals. Se o aplicativo cliente registrar marcas sobre interesse em times e localização, a notificação deverá ser direcionada para todos em Boston interessados no Red Sox ou no Cardinals. Essa condição pode ser expressada com a seguinte expressão booliana:

```csharp
(follows_RedSox || follows_Cardinals) && location_Boston
```

![Expressões de marca](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags4.png)

As expressões de tag suportam `AND` `&&`operadores booleanos comuns, tais `OR` como (`||`), e `NOT` ( );`!` eles também podem conter parênteses. As expressões `OR` de tags usando apenas operadores podem referenciar 20 tags; expressão `AND` com operadores, mas nenhum `OR` operador pode referenciar 10 tags; caso contrário, as expressões de marcação são limitadas a 6 tags.

Aqui está um exemplo para enviar notificações com expressões de tag usando o SDK:

```csharp
Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

String userTag = "(location_Boston && !follows_Cardinals)";

// Windows 8.1 / Windows Phone 8.1
var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
"You want info on the Red Sox</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

// Windows 10
toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
"You want info on the Red Sox</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
```
