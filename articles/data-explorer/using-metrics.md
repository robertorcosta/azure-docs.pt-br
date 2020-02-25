---
title: Monitorar o desempenho de Data Explorer do Azure, integridade & uso com métricas
description: Saiba como usar as métricas de Data Explorer do Azure para monitorar o desempenho, a integridade e o uso do cluster.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: 1319b8cd6ac8a0eb83381c24bcde9996458e47a7
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/22/2020
ms.locfileid: "77560297"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>Monitorar o desempenho, a integridade e o uso do Data Explorer do Azure com métricas

O Azure Data Explorer é um serviço de análise de dados rápido e totalmente gerenciado para análise em tempo real de grandes volumes de streaming de dados de aplicativos, sites, dispositivos IoT e muito mais. Para usar o Azure Data Explorer, primeiro crie um cluster e um ou mais bancos de dados nesse cluster. Em seguida, ingira (carregue) dados em um banco de dados para poder executar consultas nele. As métricas de Data Explorer do Azure fornecem indicadores-chave para a integridade e o desempenho dos recursos de cluster. Use as métricas detalhadas neste artigo para monitorar a integridade e o desempenho do cluster Data Explorer do Azure em seu cenário específico como métricas autônomas. Você também pode usar métricas como a base para os [painéis operacionais do Azure](/azure/azure-portal/azure-portal-dashboards) e [alertas do Azure](/azure/azure-monitor/platform/alerts-metric-overview).

## <a name="prerequisites"></a>Prerequisites

* Uma assinatura do Azure. Se você não tiver uma, poderá criar uma [conta gratuita do Azure](https://azure.microsoft.com/free/).
* Um [cluster e um banco de dados](create-cluster-database-portal.md).

## <a name="using-metrics"></a>Usando métricas

1. Entre no [portal do Azure](https://portal.azure.com/).
1. No cluster Data Explorer do Azure, selecione **métricas** para abrir o painel métricas e iniciar a análise no cluster.
    ![selecionar métricas](media/using-metrics/select-metrics.png).
1. No painel métricas: ![painel métricas](media/using-metrics/metrics-pane.png)
    1. Para criar um gráfico de métrica, selecione nome da **métrica** e **agregação** relevante por métrica. Os seletores de **namespace de métrica** e de **recurso** são previamente selecionados para o cluster de data Explorer do Azure. Para obter mais informações sobre métricas diferentes, consulte [métricas de data Explorer do Azure com suporte](#supported-azure-data-explorer-metrics).
    1. Selecione **Adicionar métrica** para ver várias métricas plotadas no mesmo gráfico.
    1. Selecione **+ novo gráfico** para ver vários gráficos em uma exibição.
    1. Use o seletor de tempo para alterar o intervalo de tempo (padrão: últimas 24 horas).
    1. Use [ **Adicionar filtro** e **aplicar divisão** ](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) para métricas que têm dimensões.
    1. Selecione **fixar no painel** para adicionar a configuração do gráfico aos painéis para que você possa exibi-lo novamente.
    1. Defina **nova regra de alerta** para visualizar suas métricas usando os critérios definidos. A nova regra de alerta incluirá seu recurso de destino, métrica, divisões e dimensões de filtro do seu gráfico. Modifique essas configurações no [painel criação de regra de alerta](/azure/azure-monitor/platform/metrics-charts#create-alert-rules).

Informações adicionais sobre como usar o [Metrics Explorer](/azure/azure-monitor/platform/metrics-getting-started).

## <a name="supported-azure-data-explorer-metrics"></a>Métricas de Data Explorer do Azure com suporte

As métricas de Data Explorer do Azure com suporte são separadas em várias categorias de acordo com o uso. 

### <a name="cluster-health-metrics"></a>Métricas de integridade do cluster

As métricas de integridade do cluster rastreiam a integridade geral do cluster. Isso inclui utilização e capacidade de resposta de recursos e ingestão.

**Métrica** | **Unidade** | **Agregação** | **Descrição da métrica** | **Dimensões** |
|---|---|---|---|---|
| Utilização de cache | Porcentagem | AVG, Max, min | Porcentagem de recursos de cache alocados atualmente em uso pelo cluster. Cache é o tamanho do SSD alocado para a atividade do usuário de acordo com a política de cache definida. Uma utilização média de cache de 80% ou menos é um Estado sustentável para um cluster. Se a utilização média do cache estiver acima de 80%, o cluster deverá ser [expandido](manage-cluster-vertical-scaling.md) para um tipo de preço otimizado para armazenamento ou [escalado](manage-cluster-horizontal-scaling.md) horizontalmente para mais instâncias. Como alternativa, adapte a política de cache (menos dias no cache). Se a utilização do cache for superior a 100%, o tamanho dos dados a serem armazenados em cache, de acordo com a política de cache, será maior que o tamanho total do cache no cluster. | Nenhum |
| CPU | Porcentagem | AVG, Max, min | Porcentagem de recursos de computação alocados atualmente em uso por computadores no cluster. Uma CPU média de 80% ou menos é sustentável para um cluster. O valor máximo da CPU é 100%, o que significa que não há recursos de computação adicionais para processar dados. Quando um cluster não estiver com um bom desempenho, verifique o valor máximo da CPU para determinar se há CPUs específicas bloqueadas. | Nenhum |
| Utilização de ingestão | Porcentagem | AVG, Max, min | Porcentagem de recursos reais usados para ingerir dados do total de recursos alocados, na política de capacidade, para executar a ingestão. A política de capacidade padrão não tem mais de 512 operações simultâneas de ingestão ou 75% dos recursos de cluster investidos na ingestão. A utilização média de ingestão de 80% ou menos é um Estado sustentável para um cluster. O valor máximo de utilização de ingestão é 100%, o que significa que toda a capacidade de ingestão de clusters é usada e uma fila de ingestão pode resultar. | Nenhum |
| Keep Alive | Contagem | Avg | Controla a capacidade de resposta do cluster. Um cluster totalmente responsivo retorna o valor 1 e um cluster bloqueado ou desconectado retorna 0. |
| Número total de comandos regulados | Contagem | AVG, Max, min, Sum | O número de comandos restritos (rejeitados) no cluster, pois o número máximo permitido de comandos simultâneos (paralelos) foi atingido. | Nenhum |
| Número total de extensões | Contagem | AVG, Max, min, Sum | Número total de extensões de dados no cluster. As alterações nessa métrica podem implicar grandes alterações na estrutura de dados e alta carga no cluster, uma vez que a mesclagem de extensões de dados é uma atividade pesada para a CPU. | Nenhum |
| | | | |

### <a name="export-health-and-performance-metrics"></a>Exportar métricas de integridade e desempenho

As métricas de desempenho e integridade de exportação controlam a integridade geral e o desempenho de operações de exportação, como atraso, resultados, número de registros e utilização.

**Métrica** | **Unidade** | **Agregação** | **Descrição da métrica** | **Dimensões** |
|---|---|---|---|---|
Número de exportação contínuo de registros exportados    | Contagem | SUM | O número de registros exportados em todos os trabalhos de exportação contínua. | Nenhum |
Atraso máximo de exportação contínua |    Contagem   | Max   | A finalização (em minutos) relatada pelos trabalhos de exportação contínua no cluster. | Nenhum |
Contagem de exportação contínua pendente | Contagem | Max   | O número de trabalhos de exportação contínuos pendentes. Esses trabalhos estão prontos para serem executados, mas aguardando em uma fila, possivelmente devido à capacidade insuficiente). 
Resultado da exportação contínua    | Contagem |   Contagem   | O resultado de falha/êxito de cada execução de exportação contínua. | ContinuousExportName |
Utilização da exportação |    Porcentagem | Max   | Capacidade de exportação usada, fora da capacidade total de exportação no cluster (entre 0 e 100). | Nenhum |
| | | | |

### <a name="ingestion-health-and-performance-metrics"></a>Métricas de desempenho e integridade de ingestão

As métricas de desempenho e integridade de ingestão rastreiam a integridade geral e o desempenho de operações de ingestão, como latência, resultados e volume.

**Métrica** | **Unidade** | **Agregação** | **Descrição da métrica** | **Dimensões** |
|---|---|---|---|---|
| Eventos processados (para hubs de evento/IoT) | Contagem | Max, min, Sum | Número total de eventos lidos dos hubs de eventos e processados pelo cluster. Os eventos são divididos em eventos rejeitados e eventos aceitos pelo mecanismo de cluster. | EventStatus |
| Latência de ingestão | Segundos | AVG, Max, min | Latência de dados ingeridos, desde o momento em que os dados foram recebidos no cluster até que ele esteja pronto para consulta. O período de latência de ingestão depende do cenário de ingestão. | Nenhum |
| Resultado da ingestão | Contagem | Contagem | Número total de operações de ingestão que falharam e tiveram êxito. Use **aplicar divisão** para criar buckets de resultados de êxito e de falha e analisar as dimensões (**valor** > **status**).| IngestionResultDetails |
| Volume de ingestão (em MB) | Contagem | Máximo, soma | O tamanho total dos dados ingeridos para o cluster (em MB) antes da compactação. | Banco de dados |
| | | | |  

### <a name="query-performance"></a>Desempenho de consulta

As métricas de desempenho de consulta rastreiam a duração da consulta e o número total de consultas simultâneas ou limitadas.

**Métrica** | **Unidade** | **Agregação** | **Descrição da métrica** | **Dimensões** |
|---|---|---|---|---|
| Duração da consulta | Milissegundos | AVG, min, Max, Sum | Tempo total até que os resultados da consulta sejam recebidos (não inclui latência de rede). | QueryStatus |
| Número total de consultas simultâneas | Contagem | AVG, Max, min, Sum | O número de consultas executadas em paralelo no cluster. Essa métrica é uma boa maneira de estimar a carga no cluster. | Nenhum |
| Número total de consultas limitadas | Contagem | AVG, Max, min, Sum | O número de consultas limitadas (rejeitadas) no cluster. O número máximo de consultas simultâneas (paralelas) permitidas é definido na política de consulta simultânea. | Nenhum |
| | | | |

### <a name="streaming-ingest-metrics"></a>Métricas de ingestão de streaming

As métricas de ingestão de streaming rastreiam dados de ingestão de streaming e taxa de solicitação, duração e resultados.

**Métrica** | **Unidade** | **Agregação** | **Descrição da métrica** | **Dimensões** |
|---|---|---|---|---|
Taxa de dados de ingestão de streaming |    Contagem   | RateRequestsPerSecond | Volume total de dados ingeridos no cluster. | Nenhum |
Duração da ingestão de streaming   | Milissegundos  | AVG, Max, min | Duração total de todas as solicitações de ingestão de streaming. | Nenhum |
Taxa de solicitação de ingestão de streaming   | Contagem | Count, Méd, Max, min, Sum | Número total de solicitações de ingestão de streaming. | Nenhum |
Resultado de ingestão de streaming | Contagem | Avg   | Número total de solicitações de ingestão de streaming por tipo de resultado. | Result |
| | | | |

Informações adicionais sobre as [métricas de cluster data Explorer do Azure com suporte](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters).


## <a name="next-steps"></a>Próximas etapas

* [Tutorial: ingestão e consulta de dados de monitoramento no Azure Data Explorer](/azure/data-explorer/ingest-data-no-code)
* [Monitorar operações de ingestão do Azure Data Explorer usando logs de diagnóstico](/azure/data-explorer/using-diagnostic-logs)
* [Guia de início rápido: Consultar dados no Data Explorer do Azure](web-query-data.md)
