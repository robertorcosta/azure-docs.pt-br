---
title: arquivo de inclusão
description: arquivo de inclusão
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 07/09/2020
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.custom: include file
ms.openlocfilehash: 7259e1981f873c8385a02fe4f353dcdda495f823
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91287399"
---
### <a name="property-limits"></a>Limites de propriedade

Azure Time Series Insights os limites de propriedade aumentaram para 1.000 de um limite máximo de 800 em Gen1. As propriedades de evento fornecidas têm as colunas JSON, CSV e de gráfico correspondentes que você pode exibir dentro do [Azure Time Series insights Gen2 Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).

| SKU | Propriedades máximas |
| --- | --- |
| Gen2 (L1) | Propriedades de 1.000 (colunas) |
| Gen1 (S1) | Propriedades de 600 (colunas) |
| Gen1 (S2) | Propriedades de 800 (colunas) |

### <a name="streaming-ingestion"></a>Ingestão de streaming

* Há um máximo de duas [origens de eventos](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md) por ambiente.

* As práticas recomendadas e as diretrizes gerais para fontes de eventos podem ser encontradas [aqui](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices)

* Por padrão, Azure Time Series Insights Gen2 pode ingerir dados de entrada a uma taxa de **até 1 megabyte por segundo (Mbps) por Azure Time Series insights ambiente do Gen2**. Há limitações adicionais [por partição de hub](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md#hub-partitions-and-per-partition-limits). Taxas de até 8 MBps podem ser fornecidas enviando um tíquete de suporte por meio do portal do Azure. Para saber mais, leia [limites de taxa de transferência de ingestão de streaming](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md).

### <a name="api-limits"></a>Limites de API

Os limites da API REST para Azure Time Series Insights Gen2 são especificados na [documentação de referência da API REST](https://docs.microsoft.com/rest/api/time-series-insights/preview#limits-1).
