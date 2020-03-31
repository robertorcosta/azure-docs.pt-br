---
title: Reagir ao mapear eventos usando event grid | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá como reagir aos eventos do Microsoft Azure Maps usando event grid.
author: philmea
ms.author: philmea
ms.date: 02/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 9c9483af191e5439af0c0b5e433187d6475c178c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335727"
---
# <a name="react-to-azure-maps-events-by-using-event-grid"></a>Reagir a eventos do Azure Mapas usando a Grade de Eventos 

O Azure Maps integra-se ao Azure Event Grid, para que os usuários possam enviar notificações de eventos para outros serviços e desencadear processos a jusante. O objetivo deste artigo é ajudá-lo a configurar seus aplicativos de negócios para ouvir os eventos do Azure Maps. Isso permite que os usuários reajam a eventos críticos de forma confiável, escalável e segura. Por exemplo, os usuários podem criar um aplicativo para atualizar um banco de dados, criar um ticket e entregar uma notificação por e-mail, sempre que um dispositivo entra em uma geocerca.

O Azure Event Grid é um serviço de roteamento de eventos totalmente gerenciado, que usa um modelo de assinatura de publicação. Event Grid tem suporte integrado para serviços do Azure, como [Funções Do Azure](https://docs.microsoft.com/azure/azure-functions/functions-overview) e [Aplicativos azure Logic](https://docs.microsoft.com/azure/azure-functions/functions-overview). Ele pode fornecer alertas de eventos para serviços não-Azure usando webhooks. Para obter uma lista completa dos manipuladores de eventos que dá suporte a Grade de Eventos, consulte [Uma introdução à Grade de Eventos do Azure](https://docs.microsoft.com/azure/event-grid/overview).


![Modelo funcional da Grade de Eventos do Azure](./media/azure-maps-event-grid-integration/azure-event-grid-functional-model.png)


## <a name="azure-maps-events-types"></a>Tipos de eventos do Azure Mapas

A Grade de eventos usa [assinaturas de evento](https://docs.microsoft.com/azure/event-grid/concepts#event-subscriptions) para rotear mensagens de evento para os assinantes. Uma conta do Azure Mapas emite os seguintes tipos de eventos: 

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | Levantadas quando as coordenadas recebidas mudaram-se de fora de uma determinada geocerca para dentro |
| Microsoft.Maps.GeofenceExited | Levantadas quando as coordenadas recebidas mudaram de dentro de uma determinada geocerca para fora |
| Microsoft.Maps.GeofenceResult | Gerado sempre que uma consulta de delimitação geográfica retorna um resultado, independentemente do estado |

## <a name="event-schema"></a>Esquema do evento

O exemplo a seguir mostra o esquema de GeofenceResult:

```JSON
{   
   "id":"451675de-a67d-4929-876c-5c2bf0b2c000", 
   "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"1", 
            "distance":999.0, 
            "nearestLat":47.609833, 
            "nearestLon":-122.148274 
         }, 
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":999.0, 
            "nearestLat":47.621954, 
            "nearestLon":-122.131841 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "eventType":"Microsoft.Maps.GeofenceResult", 
   "eventTime":"2018-11-08T00:52:08.0954283Z", 
   "metadataVersion":"1", 
   "dataVersion":"1.0" 
}
```

## <a name="tips-for-consuming-events"></a>Dicas para o consumo de eventos

Aplicativos que manipulam eventos de limite geográfico do Azure Mapas devem seguir algumas práticas recomendadas:

* Configure várias assinaturas para direcionar eventos para o mesmo manipulador de eventos. É importante não presumir que os eventos são de uma fonte específica. Verifique sempre o tópico da mensagem para garantir que a mensagem veio da fonte que você espera.
* Use `X-Correlation-id` o campo no cabeçalho de resposta para entender se suas informações sobre objetos estão atualizadas. As mensagens podem chegar fora de ordem ou após um atraso.
* Quando uma solicitação GET ou POST na API geofence é `EnterAndExit`chamada com o parâmetro de modo definido para , em seguida, um evento Enter ou Exit é gerado para cada geometria na geocerca para a qual o status foi alterado da chamada API geofence anterior.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como usar o isolamento geográfico para operações de controle em um local de construção, confira:

> [!div class="nextstepaction"] 
> [Configurar um limite geográfico usando o Azure Mapas](tutorial-geofence.md)
