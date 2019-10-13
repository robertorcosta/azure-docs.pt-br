---
title: Monitorar métricas do servidor do Azure Analysis Services | Microsoft Docs
description: Saiba como monitorar as métricas do servidor do Analysis Services no portal do Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: a66089d01ed9eabdb78975eb1738b87c99fbf311
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72294879"
---
# <a name="monitor-server-metrics"></a>Monitorar métricas do servidor

Analysis Services fornece métricas no Azure Metrics Explorer, uma ferramenta gratuita no portal, para ajudá-lo a monitorar o desempenho e a integridade de seus servidores. Por exemplo, monitore a memória e o uso da CPU, o número de conexões de cliente e o consumo de recursos de consulta. O Analysis Services usa a mesma estrutura de monitoramento que a maioria dos outros serviços do Azure. Para saber mais, confira [introdução ao Azure Metrics Explorer](../azure-monitor/platform/metrics-getting-started.md).

Para executar diagnóstico mais detalhado, rastrear o desempenho e identificar tendências em vários recursos de serviço em um grupo de recursos ou assinatura, use o [Azure Monitor](../azure-monitor/overview.md). Azure Monitor (serviço) pode resultar em um serviço faturável.


## <a name="to-monitor-metrics-for-an-analysis-services-server"></a>Para monitorar as métricas para um servidor do Analysis Services

1. No portal do Azure, selecione **Métricas**.

    ![Monitorar no Portal do Azure](./media/analysis-services-monitor/aas-monitor-portal.png)

2. Na lista de **Métricas disponíveis**, selecione as métricas a incluir no seu gráfico. 

    ![Gráfico do monitor](./media/analysis-services-monitor/aas-monitor-chart.png)

<a id="#server-metrics"></a>

## <a name="server-metrics"></a>Métricas do servidor

Use essa tabela para determinar quais métricas são melhores para o seu cenário de monitoramento. Apenas as métricas da mesma unidade podem ser mostradas no mesmo gráfico.

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|DESCRIÇÃO|
|---|---|---|---|---|
|CommandPoolJobQueueLength|Comprimento da fila de trabalho do pool de comando|Contagem|Average|Número de trabalhos na fila do pool de threads de comando.|
|CurrentConnections|Conexão: Conexões atuais|Contagem|Average|Número atual de conexões de cliente estabelecidas.|
|CurrentUserSessions|Sessões de usuário atuais|Contagem|Average|Número atual de sessões de usuário estabelecidas.|
|mashup_engine_memory_metric|Memória do mecanismo M|Bytes|Average|Uso de memória pelos processos do mecanismo de mashup|
|mashup_engine_qpu_metric|QPU do mecanismo M|Contagem|Average|Uso de QPU por processos de mecanismo de mashup|
|memory_metric|Memória|Bytes|Average|Memória. Intervalo de 0 a 25 GB para S1, 0 a 50 GB para S2 e 0 a 100 GB para S4|
|memory_thrashing_metric|Sobrecarga de memória|Percent|Average|Sobrecarga de memória média.|
|CleanerCurrentPrice|Memória: Preço atual do limpador|Contagem|Average|Preço atual da memória, $/byte/tempo, normalizado em 1000.|
|CleanerMemoryNonshrinkable|Memória: Memória do limpador não reduzível|Bytes|Average|Quantidade de memória, em bytes, não sujeita a eliminação pelo limpador na tela de fundo.|
|CleanerMemoryShrinkable|Memória: Memória do limpador reduzível|Bytes|Average|Quantidade de memória, em bytes, sujeita a eliminação pelo limpador na tela de fundo.|
|MemoryLimitHard|Memória: Limite de memória física|Bytes|Average|Limite de memória física, do arquivo de configuração.|
|MemoryLimitHigh|Memória: Limite de memória superior|Bytes|Average|Limite de memória superior, do arquivo de configuração.|
|MemoryLimitLow|Memória: Limite de memória inferior|Bytes|Average|Limite de memória inferior, do arquivo de configuração.|
|MemoryLimitVertiPaq|Memória: Limite de memória VertiPaq|Bytes|Average|Limite na memória, do arquivo de configuração.|
|MemoryUsage|Memória: Uso de Memória|Bytes|Average|Uso de memória do processo do servidor, como usado no cálculo de preço de memória do limpador. Igual ao contador Process\PrivateBytes mais o tamanho dos dados mapeados em memória, ignorando qualquer memória mapeada ou alocada pelo mecanismo de análise in-memory (VertiPaq), além do Limite de Memória do mecanismo.|
|private_bytes_metric|Bytes Particulares |Bytes|Average|A quantidade total de memória que o processo do mecanismo de Analysis Services e os processos de contêiner do mashup alocaram, não incluindo a memória compartilhada com outros processos.|
|virtual_bytes_metric|Bytes Virtuais |Bytes|Average|O tamanho atual do espaço de endereço virtual que Analysis Services processo do mecanismo e os processos de contêiner do mashup estão usando.|
|mashup_engine_private_bytes_metric|Bytes privados do mecanismo M |Bytes|Average|A quantidade total de processos de contêiner de mashup de memória alocados, não incluindo a memória compartilhada com outros processos.|
|mashup_engine_virtual_bytes_metric|Bytes virtuais do mecanismo M |Bytes|Average|O tamanho atual dos processos de contêiner do mashup de espaço de endereço virtual está usando.|
|Quota|Memória: Quota|Bytes|Average|Cota de memória atual, em bytes. A cota de memória também é conhecida como uma reserva de memória ou concessão de memória.|
|QuotaBlocked|Memória: Cota bloqueada|Contagem|Average|Número atual de solicitações de cota bloqueadas até que outras cotas de memória sejam liberadas.|
|VertiPaqNonpaged|Memória: VertiPaq não paginado|Bytes|Average|Bytes de memória bloqueada no conjunto de trabalho para uso pelo mecanismo na memória.|
|VertiPaqPaged|Memória: VertiPaq paginado|Bytes|Average|Bytes de memória paginada em uso para dados na memória.|
|ProcessingPoolJobQueueLength|Comprimento da fila de trabalho do pool de processamento|Contagem|Average|Número de trabalhos não de E/S na fila do pool de threads de processamento.|
|RowsConvertedPerSec|Processamento: Linhas convertidas por segundo|CountPerSecond|Average|Taxa de linhas convertidas durante o processamento.|
|RowsReadPerSec|Processamento: Linhas lidas por segundo|CountPerSecond|Average|Taxa de linhas lidas de todos os bancos de dados relacionais.|
|RowsWrittenPerSec|Processamento: Linhas gravadas por segundo|CountPerSecond|Average|Taxa de linhas gravadas durante o processamento.|
|qpu_metric|QPU|Contagem|Average|QPU. Intervalo de 0 a 100 para S1, 0 a 200 para S2 e 0 a 400 para S4|
|QueryPoolBusyThreads|Threads ocupados do pool de consulta|Contagem|Average|Número de threads ocupados no pool de threads de consulta.|
|SuccessfullConnectionsPerSec|Conexões bem-sucedidas por segundo|CountPerSecond|Average|Taxa de conclusões de conexão bem-sucedidas.|
|CommandPoolBusyThreads|Threads: Threads ocupados do pool comando|Contagem|Average|Número de threads ocupados no pool de threads de comando.|
|CommandPoolIdleThreads|Threads: Threads ociosos do pool comando|Contagem|Average|Número de threads ociosos no pool de threads de comando.|
|LongParsingBusyThreads|Threads: Threads ocupados de análise longa|Contagem|Average|Número de threads ocupados no pool de threads de análise longa.|
|LongParsingIdleThreads|Threads: Threads ociosos de análise longa|Contagem|Average|Número de threads ociosos no pool de threads de análise longa.|
|LongParsingJobQueueLength|Threads: Tamanho da fila de trabalhos de análise longa|Contagem|Average|Número de trabalhos na fila do pool de threads de análise longa.|
|ProcessingPoolIOJobQueueLength|Threads: Comprimento da fila de trabalho de E/S do pool de processamento|Contagem|Average|Número de trabalhos de E/S na fila do pool de threads de processamento.|
|ProcessingPoolBusyIOJobThreads|Threads: Threads de trabalho de E/S ocupados do pool de processamento|Contagem|Average|Número de threads que executam trabalhos de E/S no pool de threads de processamento.|
|ProcessingPoolBusyNonIOThreads|Threads: Threads de trabalho não E/S ocupados do pool de processamento|Contagem|Average|Número de threads que executam trabalhos não E/S no pool de threads de processamento.|
|ProcessingPoolIdleIOJobThreads|Threads: Threads de trabalho de E/S ociosos do pool de processamento|Contagem|Average|Número de threads ociosos para trabalhos de E/S no pool de threads de processamento.|
|ProcessingPoolIdleNonIOThreads|Threads: Threads de trabalho não E/S ociosos do pool de processamento|Contagem|Average|Número de threads ociosos no pool de threads de processamento dedicado a trabalhos não E/S.|
|QueryPoolIdleThreads|Threads: Threads ociosos do pool de consultas|Contagem|Average|Número de threads ociosos para trabalhos de E/S no pool de threads de processamento.|
|QueryPoolJobQueueLength|Threads: Tamanho da fila de trabalhos do pool consultas|Contagem|Average|Número de trabalhos na fila do pool de threads de consulta.|
|ShortParsingBusyThreads|Threads: Threads ocupados de análise resumida|Contagem|Average|Número de threads ocupados no pool de threads de análise resumida.|
|ShortParsingIdleThreads|Threads: Threads ociosos de análise resumida|Contagem|Average|Número de threads ociosos no pool de threads de análise resumida.|
|ShortParsingJobQueueLength|Threads: Tamanho da fila de trabalhos de análise resumida|Contagem|Average|Número de trabalhos na fila do pool de threads de análise resumida.|
|TotalConnectionFailures|Falhas de conexão totais|Contagem|Average|Total de falhas em tentativas de conexão.|
|TotalConnectionRequests|Solicitações de conexão totais|Contagem|Average|Solicitações de conexão totais. |

## <a name="next-steps"></a>Próximas etapas
[Visão geral de Azure Monitor](../azure-monitor/overview.md)      
[Introdução ao Metrics Explorer do Azure](../azure-monitor/platform/metrics-getting-started.md)      
[Métricas na API REST do Azure Monitor](/rest/api/monitor/metrics)
