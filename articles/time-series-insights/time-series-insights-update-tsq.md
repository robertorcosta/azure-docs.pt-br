---
title: Consulta de dados da Versão prévia do Azure Time Series Insights | Microsoft Docs
description: Consulta de dados da Versão Prévia do Azure Time Series Insights.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/07/2019
ms.custom: seodec18
ms.openlocfilehash: 7b7d041b678ccf2a476afc6d0744f6971349677e
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72034871"
---
# <a name="data-querying"></a>Consultas de dados

A Versão Prévia do Azure Time Series Insights permite consultar dados em eventos e metadados armazenados no ambiente por meio de APIs de superfície públicas. Essas APIs também são usadas no [Explorer da Versão Prévia do Time Series Insights](./time-series-insights-update-explorer.md).

Três categorias principais de API estão disponíveis no Time Series Insights:

* **APIs de ambiente**: permitem consultas no próprio ambiente do Time Series Insights. Exemplos de consultas incluem a lista de ambientes a que o chamador tem acesso e metadados do ambiente.

* **APIs TSM-Q (Consulta do modelo do Time Series)** : permitem operações de criação, leitura, atualização e exclusão em metadados armazenados na parte do ambiente do modelo do Time Series. Exemplos incluem hierarquias, tipos e instâncias.

* **APIs TSQ (consulta de série temporal)** : permitem a recuperação de dados de eventos conforme eles são registrados do provedor de origem. Essas APIs podem executar operações para transformar, combinar e executar cálculos em dados de série temporal.

A [linguagem TSX (expressão de série de tempo)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx) é uma quarta categoria poderosa. Ela usa modelos do Time Series para permitir cálculos avançados.

## <a name="azure-time-series-insights-preview-core-apis"></a>Principais APIs da Versão Prévia do Azure Time Series Insights

Há suporte para as APIs principais a seguir.

[Visão geral da consulta da série @no__t 1Time](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>APIs de ambiente

As seguintes APIs de ambiente estão disponíveis:

* [Obter a API de ambiente](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environments-api): retorna a lista de ambientes que o chamador está autorizado a acessar.
* [Obter a API de disponibilidade de ambiente](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environment-availability-api): retorna a distribuição da contagem de eventos de acordo com o carimbo de data/hora do evento`$ts`. Essa API ajuda a determinar se há eventos no carimbo de data/hora retornando a contagem de eventos, se existir.
* [Obter a API de esquema de eventos](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-event-schema-api): retorna os metadados do esquema de eventos para um intervalo de pesquisa fornecido. Essa API ajuda a recuperar todos os metadados e as propriedades disponíveis no esquema para determinado período pesquisado.

## <a name="time-series-model-query-tsm-q-apis"></a>APIs TSM-Q (Consulta do modelo do Time Series)

As seguintes APIs de consulta do modelo do Time Series estão disponíveis:

* [API de configurações de modelo](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api): permite obter e aplicar patch no tipo padrão e no nome do modelo do ambiente.
* [API de tipos](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api): habilita o CRUD em tipos do Time Series e suas variáveis associadas.
* [API de hierarquias](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api): habilita CRUD em hierarquias do Time Series e seus caminhos de campo associados.
* [API de instâncias](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api): habilita CRUD em instâncias do Time Series e seus campos de instância associados.

## <a name="time-series-query-tsq-apis"></a>APIs TSQ (consulta de série temporal)

As seguintes APIs de consulta do Time Series estão disponíveis:

* [API de obtenção de eventos](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-events-api): permite que a consulta e a recuperação de dados de séries temporais de eventos conforme eles são gravados no Time Series Insights do provedor de origem.

* [API de obtenção de série](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-series-api): habilita a consulta e a recuperação de dados do Time Series Insights, de eventos capturados, usando os dados gravados durante a transmissão. Os valores que são retornados se baseiam nas variáveis que foram definidas no modelo ou fornecidas internamente.

    >[!NOTE]
    > A cláusula Aggregation é ignorada, mesmo se estiver especificada em um modelo ou embutida.

  A API de obtenção de série retorna um valor de série temporal para cada variável e intervalo. Um valor de série temporal é um formato que usa o Time Series Insights para produzir JSON de uma consulta. Os valores retornados são com base na ID do Time Series e no conjunto de variáveis que foram fornecidos.

* [API de agregação de série](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#aggregate-series-api): habilita a consulta e a recuperação de dados do Time Series Insights, de eventos capturados, por amostragem e agregando os dados registrados.

  A API de agregação de série retorna um valor de série temporal para cada variável e intervalo. Os valores são com base na ID do Time Series e no conjunto de variáveis que foram fornecidos. A API de agregação de série consegue fazer uma redução usando as variáveis armazenadas no modelo do Time Series ou embutidas para agregar ou coletar exemplo dos dados.

  Tipos de agregação com suporte: `Min`, `Max`, `Sum`, `Count`, `Average`

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [armazenamento e a entrada](./time-series-insights-update-storage-ingress.md) na versão prévia do Azure Time Series insights.

- Leia o artigo Time Series Insights Preview [Data Modeling](./time-series-insights-update-tsm.md) .

- Descubra [as práticas recomendadas ao escolher uma ID de série temporal](./time-series-insights-update-how-to-id.md).
