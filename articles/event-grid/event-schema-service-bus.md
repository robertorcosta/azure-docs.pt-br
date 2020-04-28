---
title: Barramento de serviço do Azure como origem da grade de eventos
description: Descreve as propriedades que são fornecidas para eventos do Barramento de Serviço com a Grade de Eventos do Azure
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: babanisa
ms.openlocfilehash: 141a0e96071014dc3705d30f72b1a9257737298a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81393233"
---
# <a name="azure-service-bus-as-an-event-grid-source"></a>Barramento de serviço do Azure como uma fonte de grade de eventos

Este artigo fornece as propriedades e o esquema para eventos do Barramento de Serviço.Para obter uma introdução a esquemas de evento, consulte [esquema de grade de eventos do Azure](event-schema.md).

## <a name="event-grid-event-schema"></a>Esquema de eventos da Grade de Eventos

### <a name="available-event-types"></a>Tipos de evento disponíveis

Barramento de Serviço emite os seguintes tipos de evento:

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners | Gerado quando há mensagens ativas em uma fila ou assinatura e nenhum receptor escutando. |
| Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener | Gerado quando há mensagens ativas em uma fila de mensagens mortas e nenhum listener ativo. |

### <a name="example-event"></a>Exemplo de evento

O exemplo a seguir mostra o esquema de mensagens ativas sem eventos de ouvintes:

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

O esquema para um evento de fila de mensagens mortas é semelhante:

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
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
| data | objeto | Dados de eventos do armazenamento de blob. |
| dataVersion | cadeia de caracteres | A versão do esquema do objeto de dados. O fornecedor define a versão do esquema. |
| metadataVersion | cadeia de caracteres | A versão do esquema do metadados de evento. Grade de Eventos define o esquema de propriedades de nível superior. Grade de Eventos fornece esse valor. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Type | Descrição |
| -------- | ---- | ----------- |
| namespaceName | cadeia de caracteres | O namespace do Barramento de Serviço onde está o recurso existente. |
| requestUri | cadeia de caracteres | O URI para a fila específica ou a assinatura que emite o evento. |
| entityType | cadeia de caracteres | O tipo de entidade de Barramento de Serviço que emite eventos (fila ou assinatura). |
| queueName | cadeia de caracteres | A fila de mensagens ativas se inscreve-se em uma fila. Valor nulo se usar tópicos / assinaturas. |
| topicName | cadeia de caracteres | O tópico da assinatura do Barramento de Serviço ao qual as mensagens ativas pertencem. Valor nulo se usar uma fila. |
| subscriptionName | cadeia de caracteres | A assinatura do Barramento de Serviço com as mensagens ativas. Valor nulo se usar uma fila. |

## <a name="tutorials-and-how-tos"></a>Tutoriais e instruções
|Title  |Descrição  |
|---------|---------|
| [Tutorial: exemplos do Barramento de Serviço do Azure para a integração da Grade de Eventos do Azure](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | A Grade de Eventos envia mensagens do tópico do Barramento de Serviço para o aplicativo de função e o aplicativo lógico. |
| [Barramento de serviço do Azure para integração da grade de eventos](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | Visão geral da integração do Barramento de Serviço com a Grade de Eventos. |

## <a name="next-steps"></a>Próximas etapas

* Para ver uma introdução à Grade de Eventos do Azure, confira [O que é uma Grade de eventos?](overview.md)
* Para obter mais informações sobre como criar uma assinatura da Grade de Eventos do Azure, confira [Event Grid subscription schema](subscription-creation-schema.md) (Esquema de assinatura da Grade de Eventos).
* Para obter detalhes sobre como usar a grade de eventos do Azure com o Barramento de Serviço, consulte a [visão geral de integração do Barramento de Serviço à Grade de Eventos](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md).
* Tente [receber eventos do Barramento de Serviço com Funções e Aplicativos Lógicos](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json).
