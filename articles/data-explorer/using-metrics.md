---
title: Monitore o desempenho do Azure Data Explorer, o uso de & de saúde com métricas
description: Saiba como usar as métricas do Azure Data Explorer para monitorar o desempenho, a saúde e o uso do cluster.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: 1319b8cd6ac8a0eb83381c24bcde9996458e47a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560297"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>Monitore o desempenho, a saúde e o uso do Azure Data Explorer com métricas

O Azure Data Explorer é um serviço de análise de dados rápido e totalmente gerenciado para análise em tempo real de grandes volumes de streaming de dados de aplicativos, sites, dispositivos IoT e muito mais. Para usar o Azure Data Explorer, primeiro crie um cluster e um ou mais bancos de dados nesse cluster. Em seguida, ingira (carregue) dados em um banco de dados para poder executar consultas nele. As métricas do Azure Data Explorer fornecem indicadores-chave quanto à saúde e desempenho dos recursos do cluster. Use as métricas detalhadas neste artigo para monitorar a saúde e o desempenho do cluster Azure Data Explorer em seu cenário específico como métricas autônomas. Você também pode usar métricas como base para [painéis azure](/azure/azure-portal/azure-portal-dashboards) operacionais e [alertas azure.](/azure/azure-monitor/platform/alerts-metric-overview)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma, você pode criar uma [conta gratuita do Azure](https://azure.microsoft.com/free/).
* Um [cluster e um banco de dados.](create-cluster-database-portal.md)

## <a name="using-metrics"></a>Usando métricas

1. Faça login no [portal Azure](https://portal.azure.com/).
1. No cluster Do Azure Data Explorer, selecione **Métricas** para abrir o painel de métricas e iniciar a análise em seu cluster.
    ![Selecione](media/using-metrics/select-metrics.png)Métricas .
1. No painel Métricas: ![Painel de métricas](media/using-metrics/metrics-pane.png)
    1. Para criar um gráfico métrico, selecione **Nome métrico** e **agregação** relevante por métrica. Os seletoreres **De recursos** e **espaço-nome métrico** são pré-selecionados para o cluster Azure Data Explorer. Para obter mais informações sobre diferentes métricas, consulte [as métricas suportadas do Azure Data Explorer](#supported-azure-data-explorer-metrics).
    1. Selecione **Adicionar métrica** para ver várias métricas plotadas no mesmo gráfico.
    1. Selecione **+ Novo gráfico** para ver vários gráficos em uma exibição.
    1. Use o seletor de tempo para alterar o intervalo de tempo (padrão: passados 24 horas).
    1. Use [ **Adicionar filtro** e **Aplicar a divisão** ](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) para métricas que tenham dimensões.
    1. Selecione **Pin para painel de instrumentos** para adicionar sua configuração de gráfico aos painéis para que você possa visualizá-lo novamente.
    1. Defina **nova regra de alerta** para visualizar suas métricas usando os critérios definidos. A nova regra de alerta incluirá seu recurso de destino, métrica, divisões e dimensões de filtro do seu gráfico. Modifique essas configurações no [painel de criação de regras de alerta](/azure/azure-monitor/platform/metrics-charts#create-alert-rules).

Informações adicionais sobre o uso do [Metrics Explorer](/azure/azure-monitor/platform/metrics-getting-started).

## <a name="supported-azure-data-explorer-metrics"></a>Métricas suportadas do Azure Data Explorer

As métricas suportadas do Azure Data Explorer São separadas em várias categorias de acordo com o uso. 

### <a name="cluster-health-metrics"></a>Métricas de saúde de cluster

As métricas de saúde do cluster acompanham a saúde geral do cluster. Isso inclui utilização de recursos e ingestão e capacidade de resposta.

**Métrica** | **Unidade** | **Agregação** | **Descrição da métrica** | **Dimensões** |
|---|---|---|---|---|
| Utilização do cache | Porcentagem | Avg | Porcentagem dos recursos de cache alocados atualmente em uso pelo cluster. Cache é o tamanho do SSD alocado para a atividade do usuário de acordo com a política de cache definida. Uma utilização média de cache de 80% ou menos é um estado sustentável para um cluster. Se a utilização média do cache estiver acima de 80%, o cluster deve ser [dimensionado](manage-cluster-vertical-scaling.md) para um nível de preço otimizado de armazenamento ou [dimensionado](manage-cluster-horizontal-scaling.md) para mais instâncias. Alternativamente, adapte a política de cache (menos dias no cache). Se a utilização do cache for superior a 100%, o tamanho dos dados a serem armazenados em cache, de acordo com a política de cache, é maior que o tamanho total do cache no cluster. | Nenhum |
| CPU | Porcentagem | Avg | Percentual de recursos computacionais alocados atualmente em uso por máquinas no cluster. Uma CPU média de 80% ou menos é sustentável para um cluster. O valor máximo da CPU é de 100%, o que significa que não há recursos adicionais de computação para processar dados. Quando um cluster não estiver funcionando bem, verifique o valor máximo da CPU para determinar se há CPUs específicas bloqueadas. | Nenhum |
| Utilização da ingestão | Porcentagem | Avg | Percentual de recursos reais utilizados para ingerir dados do total de recursos alocados, na política de capacidade, para a realização da ingestão. A política de capacidade padrão não é superior a 512 operações simultâneas de ingestão ou 75% dos recursos de cluster investidos na ingestão. A utilização média da ingestão de 80% ou menos é um estado sustentável para um cluster. O valor máximo da utilização da ingestão é de 100%, o que significa que toda a capacidade de ingestão de cluster é usada e uma fila de ingestão pode resultar. | Nenhum |
| Mantenha-se vivo | Contagem | Avg | Rastreia a capacidade de resposta do cluster. Um cluster totalmente responsivo retorna o valor 1 e um cluster bloqueado ou desconectado retorna 0. |
| Número total de comandos estrangulados | Contagem | Avg, Max, Min, Sum | O número de comandos estrangulados (rejeitados) no cluster, uma vez que o número máximo permitido de comandos simultâneos (paralelos) foi atingido. | Nenhum |
| Número total de extensões | Contagem | Avg, Max, Min, Sum | Número total de extensões de dados no cluster. Mudanças nesta métrica podem implicar mudanças maciças na estrutura de dados e alta carga no cluster, uma vez que a fusão de extensões de dados é uma atividade pesada da CPU. | Nenhum |
| | | | |

### <a name="export-health-and-performance-metrics"></a>Exportar métricas de saúde e desempenho

As métricas de saúde e desempenho das exportações acompanham a saúde geral e o desempenho das operações de exportação, como atraso, resultados, número de registros e utilização.

**Métrica** | **Unidade** | **Agregação** | **Descrição da métrica** | **Dimensões** |
|---|---|---|---|---|
Número de exportação contínua de registros exportados    | Contagem | SUM | O número de registros exportados em todos os empregos de exportação contínua. | Nenhum |
Atraso máximo da exportação contínua |    Contagem   | Max   | O atraso (em minutos) relatado pelos empregos de exportação contínua no cluster. | Nenhum |
Contagem contínua de exportação pendente | Contagem | Max   | O número de empregos pendentes de exportação contínua. Esses trabalhos estão prontos para serem executados, mas esperando em uma fila, possivelmente devido à capacidade insuficiente). 
Resultado contínuo da exportação    | Contagem |   Contagem   | O resultado de Falha/Sucesso de cada execução contínua de exportação. | Nome de exportação contínua |
Utilização da exportação |    Porcentagem | Max   | Capacidade de exportação utilizada, fora da capacidade total de exportação no cluster (entre 0 e 100). | Nenhum |
| | | | |

### <a name="ingestion-health-and-performance-metrics"></a>Métricas de saúde e desempenho da ingestão

As métricas de saúde e desempenho da ingestão acompanham a saúde geral e o desempenho das operações de ingestão, como latência, resultados e volume.

**Métrica** | **Unidade** | **Agregação** | **Descrição da métrica** | **Dimensões** |
|---|---|---|---|---|
| Eventos processados (para Hubs de Eventos/IoT) | Contagem | Max, Min, Sum | Número total de eventos lidos a partir de hubs de eventos e processados pelo cluster. Os eventos são divididos em eventos rejeitados e eventos aceitos pelo mecanismo de cluster. | EventStatus |
| Latência da ingestão | Segundos | Avg | Latência de dados ingeridos, desde o momento em que os dados foram recebidos no cluster até que esteja pronto para consulta. O período de latência da ingestão depende do cenário de ingestão. | Nenhum |
| Resultado da ingestão | Contagem | Contagem | Número total de operações de ingestão que falharam e tiveram sucesso. Use **aplicar divisão** para criar baldes de sucesso e resultados de falha e analisar as dimensões **(Status de Valor).** > **Status**| IngestionResultDetails |
| Volume de ingestão (em MB) | Contagem | Max | O tamanho total dos dados ingeridos para o cluster (em MB) antes da compressão. | Banco de dados |
| | | | |  

### <a name="query-performance"></a>Desempenho de consulta

As métricas de desempenho de consulta acompanham a duração da consulta e o número total de consultas simultâneas ou estranguladas.

**Métrica** | **Unidade** | **Agregação** | **Descrição da métrica** | **Dimensões** |
|---|---|---|---|---|
| Duração da consulta | Milissegundos | Avg, Min, Max, Sum | Tempo total até que os resultados da consulta sejam recebidos (não inclui latência da rede). | QueryStatus |
| Número total de consultas simultâneas | Contagem | Avg, Max, Min, Sum | O número de consultas é executado em paralelo no cluster. Esta métrica é uma boa maneira de estimar a carga no cluster. | Nenhum |
| Número total de consultas estranguladas | Contagem | Avg, Max, Min, Sum | O número de consultas estranguladas (rejeitadas) no cluster. O número máximo de consultas simultâneas (paralelas) permitidas é definido na política de consulta simultânea. | Nenhum |
| | | | |

### <a name="streaming-ingest-metrics"></a>Streaming de métricas de ingestão

As métricas de streaming rastreiam os dados de ingestão de streaming e a taxa de solicitação, duração e resultados.

**Métrica** | **Unidade** | **Agregação** | **Descrição da métrica** | **Dimensões** |
|---|---|---|---|---|
Taxa de dados de ingestão de streaming |    Contagem   | RateRequestsPerSegundo | Volume total de dados ingeridos para o cluster. | Nenhum |
Streaming de duração ingerida   | Milissegundos  | Avg | Duração total de todas as solicitações de ingestão de streaming. | Nenhum |
Taxa de solicitação de ingestão de streaming   | Contagem | Conde, Avg, Max, Min, Sum | Número total de solicitações de ingestão de streaming. | Nenhum |
Resultado de Streaming Ingest | Contagem | Avg   | Número total de solicitações de ingestão de streaming por tipo de resultado. | Result |
| | | | |

Informações adicionais sobre [métricas de cluster do Azure Data Explorer suportadas](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters).


## <a name="next-steps"></a>Próximas etapas

* [Tutorial: Inserie e consulte dados de monitoramento no Azure Data Explorer](/azure/data-explorer/ingest-data-no-code)
* [Monitorar operações de ingestão do Azure Data Explorer usando logs de diagnóstico](/azure/data-explorer/using-diagnostic-logs)
* [Guia de início rápido: Consultar dados no Data Explorer do Azure](web-query-data.md)
