---
title: Azure SingnalR como fonte da Grade de Eventos
description: Descreve as propriedades fornecidas para eventos Azure SignalR com a Azure Event Grid
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: babanisa
ms.openlocfilehash: 730d1a7a053ab636c45313dd0c35a537434eb782
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393388"
---
# <a name="azure-event-grid-event-schema-for-signalr-service"></a>Esquema de eventos da Azure Event Grid para o Serviço SignalR

Este artigo fornece as propriedades e o esquema para eventos do SignalR Service.Para obter uma introdução a esquemas de evento, consulte [esquema de grade de eventos do Azure](event-schema.md). Ele também oferece uma lista de partidas rápidas e tutoriais para usar o Azure SignalR como fonte de evento.

## <a name="event-grid-event-schema"></a>Esquema de eventos da Grade de Eventos

### <a name="available-event-types"></a>Tipos de evento disponíveis

O SignalR Service emite os seguintes tipos de eventos:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Conexão Microsoft.SignalRService.clientConnected | Levantada quando uma conexão com o cliente conectado. |
| Microsoft.SignalRService.ClientConexão Desconectada | Levantada quando uma conexão com o cliente se desconecta. |

### <a name="example-event"></a>Exemplo de evento

O exemplo a seguir mostra o esquema de um evento conectado à conexão com o cliente: 

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

O esquema para um evento desconectado de conexão do cliente é semelhante: 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionDisconnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23",
    "errorMessage": "Internal server error."
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>Propriedades do evento

Um evento tem os seguintes dados de nível superior:

| Propriedade | Type | Descrição |
| -------- | ---- | ----------- |
| topic | string | Caminho de recurso completo para a origem do evento. Esse campo não é gravável. Grade de Eventos fornece esse valor. |
| subject | string | Caminho definido pelo fornecedor para o assunto do evento. |
| eventType | string | Um dos tipos de evento registrados para a origem do evento. |
| eventTime | string | A hora em que o evento é gerado com base na hora UTC do provedor. |
| id | string | Identificador exclusivo do evento. |
| data | objeto | Dados de eventos do SignalR Service. |
| dataVersion | string | A versão do esquema do objeto de dados. O fornecedor define a versão do esquema. |
| metadataVersion | string | A versão do esquema do metadados de evento. Grade de Eventos define o esquema de propriedades de nível superior. Grade de Eventos fornece esse valor. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Type | Descrição |
| -------- | ---- | ----------- |
| timestamp | string | A hora em que o evento é gerado com base na hora UTC do provedor. |
| hubName | string | O hub ao qual a conexão com o cliente pertence. |
| ConnectionId | string | O identificador exclusivo para a conexão com o cliente. |
| userId | string | O identificador de usuário definido na reclamação. |
| Errormessage | string | O erro que faz com que a conexão seja desconectada. |

## <a name="tutorials-and-how-tos"></a>Tutoriais e como fazer
|Title | Descrição |
|---------|---------|
| [Reaja aos eventos do Azure SignalR Service usando event grid](../azure-signalr/signalr-concept-event-grid-integration.md) | Visão geral da integração do Azure SignalR Service com a Event Grid. |
| [Como enviar eventos do Azure SignalR Service para event grid](../azure-signalr/signalr-howto-event-grid-integration.md) | Mostra como enviar eventos do Azure SignalR Service para um aplicativo através da Event Grid. |

## <a name="next-steps"></a>Próximas etapas

* Para ver uma introdução à Grade de Eventos do Azure, confira [O que é uma Grade de eventos?](overview.md)
* Para obter mais informações sobre como criar uma assinatura da Grade de Eventos do Azure, confira [Event Grid subscription schema](subscription-creation-schema.md) (Esquema de assinatura da Grade de Eventos).
