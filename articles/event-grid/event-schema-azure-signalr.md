---
title: Sinalizador do Azure como origem da grade de eventos
description: Descreve as propriedades que são fornecidas para eventos do Signalr do Azure com a grade de eventos do Azure
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: babanisa
ms.openlocfilehash: e4ebae9597d750cea6f292655e9f03dd65ccc3f5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82133711"
---
# <a name="azure-event-grid-event-schema-for-signalr-service"></a>Esquema de evento da grade de eventos do Azure para o serviço Signalr

Este artigo fornece as propriedades e o esquema para eventos do serviço Signalr.Para obter uma introdução a esquemas de evento, consulte [esquema de grade de eventos do Azure](event-schema.md). Ele também fornece uma lista de inícios rápidos e tutoriais para usar o Azure Signalr como uma fonte de eventos.

## <a name="event-grid-event-schema"></a>Esquema de eventos da Grade de Eventos

### <a name="available-event-types"></a>Tipos de evento disponíveis

O serviço signalr emite os seguintes tipos de evento:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft. SignalRService. ClientConnectionConnected | Gerado quando uma conexão de cliente é conectada. |
| Microsoft. SignalRService. ClientConnectionDisconnected | Gerado quando uma conexão de cliente é desconectada. |

### <a name="example-event"></a>Exemplo de evento

O exemplo a seguir mostra o esquema de um evento conectado de conexão de cliente: 

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

O esquema para um evento de conexão de cliente desconectado é semelhante: 

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
| topic | cadeia de caracteres | Caminho de recurso completo para a origem do evento. Esse campo não é gravável. Grade de Eventos fornece esse valor. |
| subject | cadeia de caracteres | Caminho definido pelo fornecedor para o assunto do evento. |
| eventType | cadeia de caracteres | Um dos tipos de evento registrados para a origem do evento. |
| eventTime | cadeia de caracteres | A hora em que o evento é gerado com base na hora UTC do provedor. |
| id | cadeia de caracteres | Identificador exclusivo do evento. |
| data | objeto | Dados de evento do serviço signalr. |
| dataVersion | cadeia de caracteres | A versão do esquema do objeto de dados. O fornecedor define a versão do esquema. |
| metadataVersion | cadeia de caracteres | A versão do esquema do metadados de evento. Grade de Eventos define o esquema de propriedades de nível superior. Grade de Eventos fornece esse valor. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Type | Descrição |
| -------- | ---- | ----------- |
| timestamp | cadeia de caracteres | A hora em que o evento é gerado com base na hora UTC do provedor. |
| hubName | cadeia de caracteres | O Hub ao qual a conexão do cliente pertence. |
| ConnectionId | cadeia de caracteres | O identificador exclusivo para a conexão do cliente. |
| userId | cadeia de caracteres | O identificador de usuário definido na declaração. |
| errorMessage | cadeia de caracteres | O erro que faz com que a conexão seja desconectada. |

## <a name="tutorials-and-how-tos"></a>Tutoriais e instruções
|Title | Descrição |
|---------|---------|
| [Reagir aos eventos do serviço de Signaler do Azure usando a grade de eventos](../azure-signalr/signalr-concept-event-grid-integration.md) | Visão geral da integração do serviço de Signaler do Azure com a grade de eventos. |
| [Como enviar eventos do serviço de Signaler do Azure para a grade de eventos](../azure-signalr/signalr-howto-event-grid-integration.md) | Mostra como enviar eventos do serviço de Signaler do Azure para um aplicativo por meio da grade de eventos. |

## <a name="next-steps"></a>Próximas etapas

* Para ver uma introdução à Grade de Eventos do Azure, confira [O que é uma Grade de eventos?](overview.md)
* Para obter mais informações sobre como criar uma assinatura da Grade de Eventos do Azure, confira [Event Grid subscription schema](subscription-creation-schema.md) (Esquema de assinatura da Grade de Eventos).
