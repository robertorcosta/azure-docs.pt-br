---
title: Consulta de dados no Preview - Azure Time Series Insights | Microsoft Docs
description: Conceitos de consulta de dados e visão geral da API HTTP REST na visualização do Azure Time Series Insights.
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/25/2020
ms.custom: seodec18
ms.openlocfilehash: 23094ec71dac5780def10e16b90de0b818ef3c68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284884"
---
# <a name="data-querying-in-azure-time-series-insights-preview"></a>Consulta de dados na visualização de insights da série do tempo do Azure

O Azure Time Series Insights permite a consulta de dados sobre eventos e metadados armazenados no ambiente por meio de APIs de superfície pública. Essas APIs também são usadas pelo [Time Series Insights Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer).

Três categorias principais de API estão disponíveis no Time Series Insights:

* **APIs do ambiente**: Essas APIs permitem consultas no próprio ambiente Time Series Insights. Estes podem ser usados para reunir a lista de ambientes aos que o chamador tem acesso e metadados do ambiente.
* **APIs de modelo de modelo de série de tempo (TSM-Q):** permite criar, ler, atualizar e excluir operações (CRUD) em metadados armazenados no Modelo de Série soda do ambiente. Eles podem ser usados para acessar e editar as instâncias, tipos e hierarquias.
* **APIs de Consulta de Série sportiva (TSQ):** Permite a recuperação de dados de telemetria ou eventos como é registrado a partir do provedor de origem e permite cálculos e agregações performáticas nos dados usando funções escalares e agregadas avançadas.

O Time Series Insights usa uma linguagem de expressão baseada em strings rica, [Time Series Expression (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax), para expressar cálculos.

## <a name="azure-time-series-insights-core-apis"></a>APIs centrais da série de tempo do Azure

Há suporte para as APIs principais a seguir.

[![Visão geral da consulta da série temporal](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>APIs de ambiente

* [Obter API de ambientes](https://docs.microsoft.com/rest/api/time-series-insights/management/environments/get): Retorna a lista de ambientes que o chamador está autorizado a acessar.
* [Obter API de disponibilidade de ambientes](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability): Retorna `$ts`a distribuição da contagem de eventos sobre o carimbo de data-hora do evento . Esta API ajuda a determinar se há algum evento no ambiente, retornando a contagem de eventos divididos em intervalos de tempo, se houver algum.
* [Obtenha a API do Esquema de Eventos](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema): Retorna os metadados do esquema de evento para um determinado período de pesquisa. Essa API ajuda a recuperar todos os metadados e as propriedades disponíveis no esquema para determinado período pesquisado.

## <a name="time-series-model-query-tsm-q-apis"></a>APIs TSM-Q (Consulta do modelo do Time Series)

A maioria dessas APIs suporta a operação de execução em lote para habilitar operações CRUD em lote em várias entidades do Modelo de Séries de Tempo:

* [API de configurações do modelo :](https://docs.microsoft.com/rest/api/time-series-insights/preview#model-settings-api)Habilita *get* e *PATCH* no tipo padrão e no nome do modelo do ambiente.
* [API tipos](https://docs.microsoft.com/rest/api/time-series-insights/preview#types-api): Habilita crud em tipos de séries tempontos e suas variáveis associadas.
* [Hierarquias API](https://docs.microsoft.com/rest/api/time-series-insights/preview#hierarchies-api): Habilita o CRUD nas hierarquias da série temporal e seus caminhos de campo associados.
* [API instâncias](https://docs.microsoft.com/rest/api/time-series-insights/preview#instances-api): Habilita o CRUD em instâncias de séries tempontos e seus campos de instância saqueados. Além disso, a API Instâncias suporta as seguintes operações:
  * [Pesquisa](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/search): Recupera uma lista parcial de acessos na busca por instâncias de séries tempontos com base em atributos de ocorrência.
  * [Sugerir](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/suggest): Pesquisas e sugere uma lista parcial de acessos na busca de instâncias de séries tempocom base em atributos de ocorrência.

## <a name="time-series-query-tsq-apis"></a>APIs TSQ (consulta de série temporal)

Essas APIs estão disponíveis em todas as duas lojas em nossa solução de armazenamento multicamadas no Time Series Insights. Os parâmetros de URL da consulta são usados para especificar o [tipo de armazenamento](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#uri-parameters) que a consulta deve executar em:

* [Obter API eventos](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents): Permite consulta e recuperação de eventos brutos e os carimbos de tempo associados do evento, pois são registrados no Time Series Insights do provedor de origem. Esta API permite a recuperação de eventos brutos para um dado ID de série temporal e período de pesquisa. Esta API suporta paginação para recuperar o conjunto de dados de resposta completa para a entrada selecionada. 

* [Obter API série](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries): Permite consulta e recuperação de valores computados e os carimbos de tempo do evento associado, aplicando cálculos definidos por variáveis em eventos brutos. Essas variáveis podem ser definidas no Modelo de Séries Tempo ou fornecidas inline na consulta. Esta API suporta paginação para recuperar o conjunto de dados de resposta completa para a entrada selecionada. 

* [AAPI série agregada](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries): Permite a consulta e a recuperação de valores agregados e os carimbos de tempo de intervalo associados, aplicando cálculos definidos por variáveis em eventos brutos. Essas variáveis podem ser definidas no Modelo de Séries Tempo ou fornecidas inline na consulta. Esta API suporta paginação para recuperar o conjunto de dados de resposta completa para a entrada selecionada. 
  
  Para um intervalo e intervalo de pesquisa especificado, esta API retorna uma resposta agregada por variável por intervalo para um ID de série de tempo. O número de intervalos no conjunto de dados de resposta é calculado contando carrapatos de época (o número de milissegundos que se passaram desde a época de Unix - 1de janeiro de 1970) e dividindo os carrapatos pelo tamanho de intervalo especificado na consulta.

  Os carimbos de tempo retornados no conjunto de resposta são dos limites do intervalo esquerdo, não dos eventos amostrados do intervalo. 

## <a name="next-steps"></a>Próximas etapas

- Leia mais sobre diferentes variáveis que podem ser definidas no [Modelo série soda](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-tsm).
- Leia mais sobre como consultar dados do [Time Series Insights Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer).
