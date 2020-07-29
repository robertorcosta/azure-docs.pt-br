---
title: incluir arquivo
description: incluir arquivo
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 07/09/2020
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.custom: include file
ms.openlocfilehash: 48080bb4d1e24f7f98d3dfe1fd63b65ba46df35e
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87289894"
---
### <a name="property-limits"></a>Limites de propriedade

Azure Time Series Insights os limites de propriedade aumentaram para 1.000 de um limite máximo de 800 em Gen1. As propriedades de evento fornecidas têm as colunas JSON, CSV e de gráfico correspondentes que você pode exibir dentro do [Azure Time Series insights Gen2 Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).

| SKU | Propriedades máximas |
| --- | --- |
| Gen2 (L1) | Propriedades de 1.000 (colunas) |
| Gen1 (S1) | Propriedades de 600 (colunas) |
| Gen1 (S2) | Propriedades de 800 (colunas) |

### <a name="event-sources"></a>Origens de eventos

Há suporte para no máximo duas origens de evento por instância.

* Saiba como [Adicionar uma origem do hub de eventos](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).
* Configurar [uma origem do Hub IOT](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub).

Por padrão, os [ambientes Gen2 dão suporte a tarifas de entrada](https://docs.microsoft.com/azure/time-series-insights/concepts-streaming-ingress-throughput-limits) de até **1 megabyte por segundo (MB/s) por ambiente**. Os clientes podem dimensionar seus ambientes de até **16 MB/s** de taxa de transferência, se necessário. Também há um limite por partição de **0,5 MB/s**.

### <a name="api-limits"></a>Limites de API

Os limites da API REST para Azure Time Series Insights Gen2 são especificados na [documentação de referência da API REST](https://docs.microsoft.com/rest/api/time-series-insights/preview#limits-1).
