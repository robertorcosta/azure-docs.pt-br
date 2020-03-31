---
title: incluir arquivo
description: incluir arquivo
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 02/07/2020
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.custom: include file
ms.openlocfilehash: 7bc6938523a6d66a2bc20b37c659568fc5ca494d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77123247"
---
### <a name="general-availability-and-preview-comparison"></a>Disponibilidade geral e comparação de visualização

A tabela a seguir resume várias diferenças importantes entre a disponibilidade geral (GA) do Azure Time Series Insights e as instâncias de visualização.

| | GA | Visualização |
| --- | --- | ---|
| Cidadão de primeira classe | Centrado em eventos | Centrado em séries temporicas |
| Raciocínio semântico | Baixo nível (dados de referência) | Alto nível (modelos) |
| Contextualização de dados | Nível não-dispositivo | Nível do dispositivo e do dispositivo |
| Armazenamento lógico de computação | Não | Armazenado em variáveis de tipo parte do modelo |
| Controle de armazenamento e acesso | Não | Ativado via modelo |
| Agregações/Amostragem | Não | Evento ponderado e tempo ponderado |
| Reconstrução de sinal | Não | Interpolação |
| Produção de séries tempois derivadas | Não | Sim, se funde e se junta |
| Flexibilidade do idioma | Não compostável | Compostos |
| Linguagem de expressão | Corda de predicado | Expressões de séries tempois (seqüências predicadas, valores, expressões e funções) |

### <a name="property-limits"></a>Limites de propriedade

Os limites de propriedade do Time Series Insights aumentaram para 1.000 de um limite máximo de 800 em GA. As propriedades de evento fornecidas têm colunas JSON, CSV e gráficos correspondentes que você pode visualizar dentro do [explorador de visualização de visualizações da série temporal](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).

| SKU | Propriedades máximas |
| --- | --- |
| Visualizar PAYG | 1.000 propriedades (colunas) |
| GA S1 | 600 propriedades (colunas) |
| GA S2 | 800 propriedades (colunas) |

### <a name="event-sources"></a>Origens de eventos

Um máximo de duas fontes de evento por instância é suportado. 

* Aprenda a [adicionar uma fonte de hub de eventos](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).
* Configure [uma fonte de hub ioT](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub).

Por padrão, [os ambientes de visualização suportam taxas de ingestão](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-storage-ingress#ingress-scale-and-preview-limitations) de até **1 megabyte por segundo (MB/s) por ambiente**. Os clientes podem dimensionar seus ambientes de visualização até **16 MB/s** de throughput, se necessário. Há também um limite por partição de **0,5 MB/s**. 

### <a name="api-limits"></a>Limites de API

Os limites da API REST para a visualização de insights da série de tempo estão especificados na [documentação de referência](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#limits)da API REST .
