---
title: incluir arquivo
description: incluir arquivo
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 02/03/2020
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.custom: include file
ms.openlocfilehash: f097479b67a7b48eca4a2710db3bd7eed6ddc982
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77013704"
---
O seguinte resume os limites-chave da Disponibilidade Geral.

### <a name="sku-ingress-rates-and-capacities"></a>Taxas e capacidades de ingestão de SKU

As taxas e capacidades de ingestão de S1 e S2 SKU fornecem flexibilidade ao configurar um novo ambiente de Insights de Séries Tempo.

| Capacidade S1 SKU | Taxa de ingressa | Capacidade máxima de armazenamento
| --- | --- | --- |
| 1 | 1 GB (1 milhão de eventos) | 30 GB (30 milhões de eventos) por mês |
| 10 | 10 GB (10 milhões de eventos) | 300 GB (300 milhões de eventos) por mês |

| Capacidade S2 SKU | Taxa de ingressa | Capacidade máxima de armazenamento
| --- | --- | --- |
| 1 | 10 GB (10 milhões de eventos) | 300 GB (300 milhões de eventos) por mês |
| 10 | 100 GB (100 milhões de eventos) | 3 TB (3 bilhões de eventos) por mês |

> [!NOTE]
> As capacidades são dimensionadas linearmente, portanto, uma SKU S1 com capacidade 2 dá suporte a 2 GB (2 milhões) de eventos por taxa de entrada por dia e 60 GB (60 milhões de eventos) por mês.

Os ambientes S2 SKU suportam substancialmente mais eventos por mês e têm uma capacidade de ingestão significativamente maior.

| SKU  | Contagem de eventos por mês  | Tamanho do evento por mês  | Contagem de eventos por minuto | Tamanho do evento por minuto  |
|---------|---------|---------|---------|---------|
| S1     |   30 milhões     |  30 GB     |  720    |  720 KB   |
 |S2     |   300 milhões    |   300 GB   | 7.200   | 7.200 KB  |

### <a name="property-limits"></a>Limites de propriedade

Os limites de propriedade GA dependem do ambiente SKU selecionado. As propriedades de evento fornecidas têm colunas JSON, CSV e gráficos correspondentes que podem ser visualizadas dentro do [Time Series Insights Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart).

| SKU | Propriedades máximas |
| --- | --- |
| S1 | 600 propriedades (colunas) |
| S2 | 800 propriedades (colunas) |

### <a name="event-sources"></a>Origens de eventos

Um máximo de duas fontes de evento por instância é suportado. 

* Aprenda a [adicionar uma fonte de hub de eventos](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).
* Configure [uma fonte de hub ioT](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub).

### <a name="api-limits"></a>Limites de API

Os limites da API REST para insights de séries de tempo A disponibilidade geral está especificada na [documentação de referência](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#limits)da API REST .