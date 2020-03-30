---
title: Reagir aos eventos do Azure SignalR Service
description: Use a Grade de Eventos do Azure para assinar eventos do Azure SignalR Service. Outros serviços a jusante podem ser acionados por esses eventos.
services: azure-signalr,event-grid
author: chenyl
ms.author: chenyl
ms.reviewer: zhshang
ms.date: 11/13/2019
ms.topic: conceptual
ms.service: signalr
ms.openlocfilehash: a8e25907b40b910f2b91884d355b6ac85eeaa250
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74158198"
---
# <a name="reacting-to-azure-signalr-service-events"></a>Reagindo aos eventos do Serviço do Azure SignalR

Os eventos do Azure SignalR Service permitem que os aplicativos reajam às conexões do cliente conectadas ou desconectadas usando arquiteturas modernas sem servidor. Isso é feito sem a necessidade de código complicado ou serviços de sondagem caros e ineficientes.  Em vez disso, os eventos são enviados por push pela [Grade de Eventos do Azure](https://azure.microsoft.com/services/event-grid/) aos assinantes como [Azure Functions](https://azure.microsoft.com/services/functions/), [Aplicativos Lógicos do Azure](https://azure.microsoft.com/services/logic-apps/), ou até mesmo seu próprio ouvinte http personalizado, e você só pague pelo que usa.

Os eventos do Azure SignalR Service são enviados de forma confiável para o serviço Event Grid, que fornece serviços de entrega confiáveis para seus aplicativos por meio de políticas de reavaliação ricas e entrega de cartas mortas. Para saber mais, consulte [a entrega e a tentativa de mensagens event grid](https://docs.microsoft.com/azure/event-grid/delivery-and-retry).

![Modelo da Grade de Eventos](https://docs.microsoft.com/azure/event-grid/media/overview/functional-model.png)

## <a name="serverless-state"></a>Estado sem servidor
Os eventos do Azure SignalR Service só estão ativos quando as conexões do cliente estão em estado sem servidor. De um modo geral, se um cliente não encaminha para um servidor de hub, ele vai para o estado sem servidor. O modo clássico só funciona quando o hub, ao que as conexões do cliente se conectam, não tem um servidor de hub. No entanto, o modo sem servidor é recomendado para evitar algum problema. Para saber mais detalhes sobre o modo de serviço, consulte [Como escolher o modo de serviço](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).

## <a name="available-azure-signalr-service-events"></a>Eventos disponíveis do Azure SignalR Service
A Grade de eventos usa [assinaturas de evento](../event-grid/concepts.md#event-subscriptions) para rotear mensagens de evento para os assinantes. As assinaturas de eventos do Azure SignalR Service suportam dois tipos de eventos:  

|Nome do evento|Descrição|
|----------|-----------|
|`Microsoft.SignalRService.ClientConnectionConnected`|Levantada quando uma conexão com o cliente está conectada.|
|`Microsoft.SignalRService.ClientConnectionDisconnected`|Levantada quando uma conexão com o cliente é desconectada.|

## <a name="event-schema"></a>Esquema do evento
Os eventos do Azure SignalR Service contêm todas as informações necessárias para responder às alterações em seus dados. Você pode identificar um evento do Azure SignalR Service com a propriedade eventType iniciada com "Microsoft.SignalRService". Informações adicionais sobre o uso de propriedades de eventos event grid são documentadas no [esquema de eventos Event Grid](../event-grid/event-schema.md).  

Aqui está um exemplo de um evento conectado à conexão com o cliente:
```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionConnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Para obter mais informações, consulte [o esquema de eventos do SignalR Service](../event-grid/event-schema-azure-signalr.md).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a Event Grid e dê aos eventos do Azure SignalR Service uma tentativa:

> [!div class="nextstepaction"]
> [Experimente uma integração da Grade de Eventos de exemplo com o Azure SignalR Service](./signalr-howto-event-grid-integration.md)
> [Sobre a Grade de Eventos](../event-grid/overview.md)
