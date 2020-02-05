---
title: Reagir a eventos de mapeamento usando a grade de eventos | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá a reagir a Microsoft Azure eventos de mapas usando a grade de eventos.
author: walsehgal
ms.author: v-musehg
ms.date: 02/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: a89983a9ae45f21deb7a823de049373b4ff9b935
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989052"
---
# <a name="react-to-azure-maps-events-by-using-event-grid"></a>Reagir a eventos do Azure Mapas usando a Grade de Eventos 

O Azure Maps integra-se à grade de eventos do Azure, para que os usuários possam enviar notificações de eventos para outros serviços e disparar processos downstream. A finalidade deste artigo é ajudá-lo a configurar seus aplicativos de negócios para ouvir os eventos do Azure Maps. Esse serviço permite reagir a eventos críticos de maneira confiável, escalonável e segura. Por exemplo, os usuários podem criar um aplicativo para atualizar um banco de dados, criar um tíquete e entregar uma notificação por email, toda vez que um dispositivo entrar em uma cerca geográfica.

A grade de eventos do Azure é um serviço de roteamento de eventos totalmente gerenciado, que usa um modelo de publicação/assinatura. A grade de eventos tem suporte interno para serviços do Azure como [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) e [aplicativos lógicos do Azure](https://docs.microsoft.com/azure/azure-functions/functions-overview). Ele pode entregar alertas de eventos para serviços que não são do Azure usando WebHooks. Para obter uma lista completa dos manipuladores de eventos que dá suporte a Grade de Eventos, consulte [Uma introdução à Grade de Eventos do Azure](https://docs.microsoft.com/azure/event-grid/overview).


![Modelo funcional da Grade de Eventos do Azure](./media/azure-maps-event-grid-integration/azure-event-grid-functional-model.png)


## <a name="azure-maps-events-types"></a>Tipos de eventos do Azure Mapas

A Grade de eventos usa [assinaturas de evento](https://docs.microsoft.com/azure/event-grid/concepts#event-subscriptions) para rotear mensagens de evento para os assinantes. Uma conta do Azure Mapas emite os seguintes tipos de eventos: 

| Tipo de evento | Description |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | Gerado quando as coordenadas recebidas são movidas de fora de uma determinada cerca geográfica para dentro |
| Microsoft.Maps.GeofenceExited | Gerado quando as coordenadas recebidas são movidas de dentro de uma determinada cerca geográfica para fora |
| Microsoft.Maps.GeofenceResult | Gerado sempre que uma consulta de delimitação geográfica retorna um resultado, independentemente do estado |

## <a name="event-schema"></a>Esquema do evento

O exemplo a seguir mostra o esquema para GeofenceResult:

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

* Várias assinaturas podem ser configuradas para eventos de rota para o mesmo manipulador de eventos. É importante não presumir que os eventos são de uma fonte específica. Sempre verifique o tópico de mensagem para verificar se eles vêm da fonte que você espera.
* As mensagens podem chegar fora de ordem ou após um atraso. Use o campo `X-Correlation-id` no cabeçalho de resposta para entender se suas informações sobre objetos estão atualizadas.
* Quando a API do limite geográfico de Get e POST é chamada com o parâmetro de modo definido como `EnterAndExit`, um evento de entrada ou saída é gerado para cada geometria no limite geográfico para a qual o status foi alterado desde a última chamada à API do limite geográfico.

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre como usar o isolamento geográfico para operações de controle em um local de construção, confira:

> [!div class="nextstepaction"] 
> [Configurar um limite geográfico usando o Azure Mapas](tutorial-geofence.md)
