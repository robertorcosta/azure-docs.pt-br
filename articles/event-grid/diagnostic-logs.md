---
title: Grade de eventos do Azure-logs de diagnóstico para tópicos ou domínios
description: Este artigo fornece informações conceituais sobre os logs de diagnóstico para um tópico ou um domínio da grade de eventos do Azure.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: spelluru
ms.openlocfilehash: 93e7e47cbcc1ab9542ba333b89f7dd655a412489
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82630246"
---
#  <a name="diagnostic-logs-for-azure-event-grid-topicsdomains"></a>Logs de diagnóstico para tópicos/domínios da grade de eventos do Azure
As configurações de diagnóstico permitem que os usuários da grade de eventos capturem e exibam logs de **falha de publicação e entrega** em uma conta de armazenamento, em um hub de eventos ou em um espaço de trabalho log Analytics. Este artigo fornece o esquema para os logs e um exemplo de entrada de log.


## <a name="schema-for-publishdelivery-failure-logs"></a>Esquema para logs de falha de publicação/entrega

| Nome da propriedade | Tipo de dados | Descrição |
| ------------- | --------- | ----------- | 
| Hora | Datetime | A hora em que a entrada de log foi gerada <p>**Valor de exemplo:** 01-29-2020 09:52:02.700</p> |
| EventSubscriptionName | String | O nome da assinatura do evento <p>**Valor de exemplo:** "EVENTSUB1"</p> <p>Essa propriedade existe somente para logs de falha de entrega.</p>  |
| Categoria | String | O nome da categoria de log. <p>**Valores de exemplo:** "DeliveryFailures" ou "PublishFailures" | 
| OperationName | String | O nome da operação executada ao encontrar a falha.<p>**Valores de exemplo:** "Entregar" para falhas de entrega. |
| Mensagem | String | A mensagem de log para o usuário que explica o motivo da falha e outros detalhes adicionais. |
| ResourceId | String | A ID de recurso para o recurso de tópico/domínio<p>**Valores de exemplo:**`/SUBSCRIPTIONS/SAMPLE-SUBSCRIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/TOPIC1` |

## <a name="example"></a>Exemplo

```json
{
    "time": "2019-11-01T00:17:13.4389048Z",
    "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID /RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME ",
    "eventSubscriptionName": "SAMPLEDESTINATION",
    "category": "DeliveryFailures",
    "operationName": "Deliver",
    "message": "Message:outcome=NotFound, latencyInMs=2635, systemId=17284f7c-0044-46fb-84b7-59fda5776017, state=FilteredFailingDelivery, deliveryTime=11/1/2019 12:17:10 AM, deliveryCount=0, probationCount=0, deliverySchema=EventGridEvent, eventSubscriptionDeliverySchema=EventGridEvent, fields=InputEvent, EventSubscriptionId, DeliveryTime, State, Id, DeliverySchema, LastDeliveryAttemptTime, SystemId, fieldCount=, requestExpiration=1/1/0001 12:00:00 AM, delivered=False publishTime=11/1/2019 12:17:10 AM, eventTime=11/1/2019 12:17:09 AM, eventType=Type, deliveryTime=11/1/2019 12:17:10 AM, filteringState=FilteredWithRpc, inputSchema=EventGridEvent, publisher=DIAGNOSTICLOGSTEST-EASTUS.EASTUS-1.EVENTGRID.AZURE.NET, size=363, fields=Id, PublishTime, SerializedBody, EventType, Topic, Subject, FilteringHashCode, SystemId, Publisher, FilteringTopic, TopicCategory, DataVersion, MetadataVersion, InputSchema, EventTime, fieldCount=15, url=sb://diagnosticlogstesting-eastus.servicebus.windows.net/, deliveryResponse=NotFound: The messaging entity 'sb://diagnosticlogstesting-eastus.servicebus.windows.net/eh-diagnosticlogstest' could not be found. TrackingId:c98c5af6-11f0-400b-8f56-c605662fb849_G14, SystemTracker:diagnosticlogstesting-eastus.servicebus.windows.net:eh-diagnosticlogstest, Timestamp:2019-11-01T00:17:13, referenceId: ac141738a9a54451b12b4cc31a10dedc_G14:"
}
```

## <a name="next-steps"></a>Próximas etapas
Para saber como habilitar os logs de diagnóstico para tópicos ou domínios, consulte [Habilitar logs de diagnóstico](enable-diagnostic-logs-topic.md).
