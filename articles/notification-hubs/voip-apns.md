---
title: Envie notificações VOIP da APNS com hubs de notificação do Azure
description: Saiba como enviar notificações VOIP da APNS através dos Hubs de Notificação do Azure (não suportados oficialmente).
author: sethmanheim
ms.author: sethm
ms.date: 3/23/2020
ms.topic: how-to
ms.service: notification-hubs
ms.openlocfilehash: c99af881b8f93b75633741c2352dc5df17dd2963
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80146882"
---
# <a name="use-apns-voip-through-notification-hubs-not-officially-supported"></a>Use O APNS VOIP através de hubs de notificação (não suportados oficialmente)

É possível usar notificações VOIP APNS através de Hubs de Notificação do Azure; no entanto, não há apoio oficial para este cenário.

## <a name="considerations"></a>Considerações

Se você ainda optar por enviar notificações VOIP APNS através de Hubs de Notificação, esteja ciente das seguintes limitações:

- O envio de uma `apns-topic` notificação VOIP requer que o `.voip` cabeçalho seja definido para o ID do pacote de aplicativos + o sufixo. Por exemplo, para um aplicativo de `com.microsoft.nhubsample`exemplo `apns-topic` com o ID do pacote, o cabeçalho deve ser definido como`com.microsoft.nhubsample.voip.`

   Esse método não funciona bem com os Hubs de Notificação do Azure, porque o ID do pacote do aplicativo deve ser configurado como parte das credenciais APNS do hub e o valor não pode ser alterado. Além disso, o Notification Hubs `apns-topic` não permite que o valor do cabeçalho seja substituído em tempo de execução.

   Para enviar notificações VOIP, você deve configurar `.voip` um hub de notificação separado com o ID do pacote do aplicativo.

- O envio de uma `apns-push-type` notificação VOIP requer `voip`que o cabeçalho seja definido como valor .

   Para ajudar os clientes com a transição para o iOS 13, o Notification Hubs tenta inferir o valor correto para o `apns-push-type` cabeçalho. A lógica de inferência é intencionalmente simples, em um esforço para evitar quebrar notificações padrão. Infelizmente, esse método causa problemas com notificações VOIP, pois a Apple trata as notificações VOIP como um caso especial que não segue as mesmas regras das notificações padrão.

   Para enviar notificações VOIP, você deve `apns-push-type` especificar um valor explícito para o cabeçalho.

- O Notification Hubs limita as cargas de APNS a 4 KB, conforme documentado pela Apple. Para notificações VOIP, a Apple permite cargas de até 5 KB. Os Hubs de Notificação não diferenciam entre notificações padrão e VOIP; portanto, todas as notificações são limitadas a 4 KB.

   Para enviar notificações VOIP, você não deve exceder o limite de tamanho de carga útil de 4 KB.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes links:

- [Documentação `apns-topic` `apns-push-type` para cabeçalhos e valores, incluindo os casos especiais para notificações VOIP](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns).

- [Documentação para limite de tamanho de carga](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification).

- [Atualizações do Notification Hubs para iOS 13](push-notification-updates-ios-13.md#apns-push-type).
