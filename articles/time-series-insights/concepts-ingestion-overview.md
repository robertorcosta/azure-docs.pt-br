---
title: Visão geral da ingestão-Azure Time Series Insights Gen2 | Microsoft Docs
description: Saiba mais sobre a ingestão de dados no Azure Time Series Insights Gen2.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 07/07/2020
ms.custom: seodec18
ms.openlocfilehash: 33cafd058e55951f7da4e925a603c2c442d4aed1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87077661"
---
# <a name="azure-time-series-insights-gen2-data-ingestion-overview"></a>Visão geral de ingestão de dados do Azure Time Series Insights Gen2

Seu ambiente de Azure Time Series Insights Gen2 contém um *mecanismo de ingestão* para coletar, processar e armazenar dados de série temporal de streaming. À medida que os dados chegam às suas origens de eventos, Azure Time Series Insights Gen2 consumirá e armazenará seus dados quase em tempo real.

[![Visão geral da ingestão](media/concepts-ingress-overview/ingress-overview.png)](media/concepts-ingress-overview/ingress-overview.png#lightbox)

## <a name="ingestion-topics"></a>Tópicos de ingestão

Os artigos a seguir abordam o processamento de dados em detalhes, incluindo as práticas recomendadas a seguir:

* Leia sobre [fontes de eventos](./concepts-streaming-ingestion-event-sources.md) e orientações sobre como selecionar um carimbo de data/hora de origem do evento.

* Examinar os [tipos de dados](./concepts-supported-data-types.md) com suporte

* Entenda como o mecanismo de ingestão aplicará um conjunto de [regras](./concepts-json-flattening-escaping-rules.md) às suas propriedades JSON para criar suas colunas de conta de armazenamento.

* Examine as [limitações de taxa de transferência](./concepts-streaming-ingress-throughput-limits.md) do seu ambiente para planejar suas necessidades de dimensionamento.

## <a name="next-steps"></a>Próximas etapas

* Continue em para saber mais sobre as [fontes de evento](./concepts-streaming-ingestion-event-sources.md) para seu ambiente de Azure Time Series insights Gen2. 
