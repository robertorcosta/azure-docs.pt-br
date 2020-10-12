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
ms.date: 09/28/2020
ms.custom: seodec18
ms.openlocfilehash: 57c23ba4acdbde1a5dfac39d89a09dfcef6b25a1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91460893"
---
# <a name="azure-time-series-insights-gen2-data-ingestion-overview"></a>Visão geral da ingestão de dados do Azure Time Series Insights Gen2

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
