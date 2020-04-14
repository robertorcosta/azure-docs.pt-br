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
ms.openlocfilehash: 11469d992e0f5669cd3fc1e3864627dd0b8ae23d
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263329"
---
O seguinte resume os limites-chave da Disponibilidade Geral.

### <a name="sku-ingress-rates-and-capacities"></a>Taxas e capacidades de ingestão de SKU

As taxas e capacidades de ingestão de S1 e S2 SKU fornecem flexibilidade ao configurar um novo ambiente de Insights de Séries Tempo. Sua capacidade de SKU indica sua taxa diária de ingestão com base no número de eventos ou bytes armazenados, o que vier primeiro. Observe que a ingress é medida *por minuto*, e **o estrangulamento** é aplicado usando o algoritmo do balde de token. A entrada é medida em blocos de 1 KB. Por exemplo, um evento real de 0,8-KB seria medido como um evento, e um evento de 2,6 KB é contado como três eventos.

| Capacidade S1 SKU | Taxa de ingressa | Capacidade máxima de armazenamento
| --- | --- | --- |
| 1 | 1 GB (1 milhão de eventos) por dia | 30 GB (30 milhões de eventos) por mês |
| 10 | 10 GB (10 milhões de eventos) por dia | 300 GB (300 milhões de eventos) por mês |

| Capacidade S2 SKU | Taxa de ingressa | Capacidade máxima de armazenamento
| --- | --- | --- |
| 1 | 10 GB (10 milhões de eventos) por dia | 300 GB (300 milhões de eventos) por mês |
| 10 | 100 GB (100 milhões de eventos) por dia | 3 TB (3 bilhões de eventos) por mês |

> [!NOTE]
> As capacidades são dimensionadas linearmente, portanto, uma SKU S1 com capacidade 2 dá suporte a 2 GB (2 milhões) de eventos por taxa de entrada por dia e 60 GB (60 milhões de eventos) por mês.

Os ambientes S2 SKU suportam substancialmente mais eventos por mês e têm uma capacidade de ingestão significativamente maior.

| SKU  | Contagem de eventos por mês  | Contagem de eventos por minuto | Tamanho do evento por minuto  |
|---------|---------|---------|---------|---------|
| S1     |   30 milhões   |  720    |  720 KB   |
 |S2     |   300 milhões   | 7.200   | 7.200 KB  |

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