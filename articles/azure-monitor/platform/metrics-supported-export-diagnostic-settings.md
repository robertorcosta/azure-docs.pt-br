---
title: Métricas de plataforma Azure Monitor exportáveis por meio de configurações de diagnóstico
description: Lista de métricas disponíveis para cada tipo de recurso com o Azure Monitor.
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/20/2019
author: rboucher
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: 1bab6a990ba38bc32c2b7350bb1d4eac15a7b95e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75479027"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>Métricas de plataforma Azure Monitor exportáveis por meio de configurações de diagnóstico

O Azure Monitor fornece [métricas de plataforma](data-platform-metrics.md) por padrão sem configuração. Ele fornece várias maneiras de interagir com as métricas de plataforma, incluindo o gráfico delas no portal, acessá-las por meio da API REST ou consultá-las usando o PowerShell ou a CLI. Consulte [métricas – com suporte](metrics-supported.md) para obter uma lista completa de métricas de plataforma disponíveis atualmente com o pipeline de métrica consolidado do Azure monitor. Para consultar e acessar essas métricas, use a [versão de api 2018-01-01](https://docs.microsoft.com/rest/api/monitor/metricdefinitions). Outras métricas podem estar disponíveis no portal ou usando as APIs herdadas.

Você pode exportar as métricas de plataforma do pipeline do Azure monitor para outros locais de uma das duas maneiras.
1. Usando [configurações de diagnóstico](diagnostic-settings.md) para enviar para log Analytics, hubs de eventos ou armazenamento do Azure.
2. Usar a [API REST de métricas](https://docs.microsoft.com/rest/api/monitor/metrics/list)

Devido a complexidades no back-end de Azure Monitor, nem todas as métricas são exportáveis usando configurações de diagnóstico. A tabela a seguir lista os que podem e não podem ser exportados usando as configurações de diagnóstico.

Exportável por meio de configurações de diagnóstico? | ResourceType | Métrica | MetricDisplayName | Unidade | AggregationType
|----|-----|------|----|----|-----|
Sim | Microsoft.AnalysisServices/servers | CleanerCurrentPrice | Memória: preço atual do limpador | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | CleanerMemoryNonshrinkable | Memória: memória do limpador não reduzível | Bytes | Média
Sim | Microsoft.AnalysisServices/servers | CleanerMemoryShrinkable | Memória: memória do limpador reduzível | Bytes | Média
Sim | Microsoft.AnalysisServices/servers | CommandPoolBusyThreads | Threads: threads ocupados do pool comando | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | CommandPoolIdleThreads | Threads: threads ociosos do pool de comandos | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | CommandPoolJobQueueLength | Comprimento da fila de trabalho do pool de comando | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | CurrentConnections | Conexão: conexões atuais | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | CurrentUserSessions | Sessões de usuário atuais | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | LongParsingBusyThreads | Threads: threads ocupados de análise longa | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | LongParsingIdleThreads | Threads: threads ociosos de análise longa | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | LongParsingJobQueueLength | Threads: tamanho da fila de trabalhos de análise longa | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | mashup_engine_memory_metric | Memória do mecanismo M | Bytes | Média
Sim | Microsoft.AnalysisServices/servers | mashup_engine_private_bytes_metric | Bytes privados do mecanismo M | Bytes | Média
Sim | Microsoft.AnalysisServices/servers | mashup_engine_qpu_metric | QPU do mecanismo M | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | mashup_engine_virtual_bytes_metric | Bytes virtuais do mecanismo M | Bytes | Média
Sim | Microsoft.AnalysisServices/servers | memory_metric | Memória | Bytes | Média
Sim | Microsoft.AnalysisServices/servers | memory_thrashing_metric | Sobrecarga de memória | Porcentagem | Média
Sim | Microsoft.AnalysisServices/servers | MemoryLimitHard | Memória: limite de memória física | Bytes | Média
Sim | Microsoft.AnalysisServices/servers | MemoryLimitHigh | Memória: limite de memória superior | Bytes | Média
Sim | Microsoft.AnalysisServices/servers | MemoryLimitLow | Memória: limite de memória inferior | Bytes | Média
Sim | Microsoft.AnalysisServices/servers | MemoryLimitVertiPaq | Memória: VertiPaq do limite de memória | Bytes | Média
Sim | Microsoft.AnalysisServices/servers | MemoryUsage | Memória: uso de memória | Bytes | Média
Sim | Microsoft.AnalysisServices/servers | private_bytes_metric | Bytes Particulares | Bytes | Média
Sim | Microsoft.AnalysisServices/servers | ProcessingPoolBusyIOJobThreads | Threads: threads de trabalho de E/S ocupados do pool de processamento | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | ProcessingPoolBusyNonIOThreads | Threads: threads de trabalho não E/S ocupados do pool de processamento | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | ProcessingPoolIdleIOJobThreads | Threads: threads de trabalho de E/S ociosos do pool de processamento | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | ProcessingPoolIdleNonIOThreads | Threads: threads de trabalho não E/S ociosos do pool de processamento | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | ProcessingPoolIOJobQueueLength | Threads: tamanho da fila de trabalhos de E/S do pool de processamento | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | ProcessingPoolJobQueueLength | Comprimento da fila de trabalho do pool de processamento | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | qpu_metric | QPU | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | QueryPoolBusyThreads | Threads ocupados do pool de consulta | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | QueryPoolIdleThreads | Threads: threads ociosos do pool de consultas | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | QueryPoolJobQueueLength | Threads: tamanho da fila de trabalhos do pool consultas | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | Cota | Memória: cota | Bytes | Média
Sim | Microsoft.AnalysisServices/servers | QuotaBlocked | Memória: cota bloqueada | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | RowsConvertedPerSec | Processamento: linhas convertidas por segundo | CountPerSecond | Média
Sim | Microsoft.AnalysisServices/servers | RowsReadPerSec | Processamento: linhas lidas por segundo | CountPerSecond | Média
Sim | Microsoft.AnalysisServices/servers | RowsWrittenPerSec | Processamento: linhas gravadas por segundo | CountPerSecond | Média
Sim | Microsoft.AnalysisServices/servers | ShortParsingBusyThreads | Threads: threads ocupados de análise resumida | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | ShortParsingIdleThreads | Threads: threads ociosos de análise resumida | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | ShortParsingJobQueueLength | Threads: tamanho da fila de trabalhos de análise resumida | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | SuccessfullConnectionsPerSec | Conexões bem-sucedidas por segundo | CountPerSecond | Média
Sim | Microsoft.AnalysisServices/servers | TotalConnectionFailures | Falhas de conexão totais | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | TotalConnectionRequests | Solicitações de conexão totais | Contagem | Média
Sim | Microsoft.AnalysisServices/servers | VertiPaqNonpaged | Memória: VertiPaq não paginado | Bytes | Média
Sim | Microsoft.AnalysisServices/servers | VertiPaqPaged | Memória: VertiPaq paginado | Bytes | Média
Sim | Microsoft.AnalysisServices/servers | virtual_bytes_metric | Bytes Virtuais | Bytes | Média
Sim | Microsoft.ApiManagement/service | BackendDuration | Duração de solicitações de back-end | Milissegundos | Média
Sim | Microsoft.ApiManagement/service | Capacidade | Capacidade | Porcentagem | Média
Sim | Microsoft.ApiManagement/service | Duração | Duração total de solicitações de gateway | Milissegundos | Média
Sim | Microsoft.ApiManagement/service | EventHubDroppedEvents | Eventos do EventHub ignorados | Contagem | Total
Sim | Microsoft.ApiManagement/service | EventHubRejectedEvents | Eventos de EventHub rejeitados | Contagem | Total
Sim | Microsoft.ApiManagement/service | EventHubSuccessfulEvents | Eventos de EventHub bem-sucedidos | Contagem | Total
Sim | Microsoft.ApiManagement/service | EventHubThrottledEvents | Eventos de EventHub limitados | Contagem | Total
Sim | Microsoft.ApiManagement/service | EventHubTimedoutEvents | Eventos do EventHub com tempo limite excedido | Contagem | Total
Sim | Microsoft.ApiManagement/service | EventHubTotalBytesSent | Tamanho dos eventos do EventHub | Bytes | Total
Sim | Microsoft.ApiManagement/service | EventHubTotalEvents | Total de eventos do EventHub | Contagem | Total
Sim | Microsoft.ApiManagement/service | EventHubTotalFailedEvents | Eventos de EventHub com falha | Contagem | Total
Sim | Microsoft.ApiManagement/service | FailedRequests | Solicitações de gateway com falha (preteridas) | Contagem | Total
Sim | Microsoft.ApiManagement/service | OtherRequests | Outras solicitações de gateway (preteridas) | Contagem | Total
Sim | Microsoft.ApiManagement/service | Solicitações | Solicitações | Contagem | Total
Sim | Microsoft.ApiManagement/service | SuccessfulRequests | Solicitações de gateway com êxito (preteridas) | Contagem | Total
Sim | Microsoft.ApiManagement/service | TotalRequests | Total de solicitações do gateway (preterido) | Contagem | Total
Sim | Microsoft.ApiManagement/service | UnauthorizedRequests | Solicitações de gateway não autorizadas (preteridas) | Contagem | Total
Sim | Microsoft. AppPlatform/Spring | AppCpuUsagePercentage | Porcentagem de uso de CPU do aplicativo | Porcentagem | Média
Sim | Microsoft. AppPlatform/Spring | AppMemoryCommitted | Memória de aplicativo atribuída | Bytes | Média
Sim | Microsoft. AppPlatform/Spring | AppMemoryMax | Memória máxima do aplicativo | Bytes | Máximo
Sim | Microsoft. AppPlatform/Spring | AppMemoryUsed | Memória usada do aplicativo | Bytes | Média
Sim | Microsoft. AppPlatform/Spring | GCPauseTotalCount | Contagem de pausar do GC | Contagem | Total
Sim | Microsoft. AppPlatform/Spring | GCPauseTotalTime | Tempo total de pausa do GC | Milissegundos | Total
Sim | Microsoft. AppPlatform/Spring | MaxOldGenMemoryPoolBytes | Tamanho máximo de dados de geração antiga disponíveis | Bytes | Média
Sim | Microsoft. AppPlatform/Spring | OldGenMemoryPoolBytes | Tamanho de dados de geração antiga | Bytes | Média
Sim | Microsoft. AppPlatform/Spring | OldGenPromotedBytes | Promover para tamanho de dados de geração antiga | Bytes | Máximo
Sim | Microsoft. AppPlatform/Spring | SystemCpuUsagePercentage | Porcentagem de uso da CPU do sistema | Porcentagem | Média
Sim | Microsoft. AppPlatform/Spring | TomcatErrorCount | Erro global do Tomcat | Contagem | Total
Sim | Microsoft. AppPlatform/Spring | TomcatReceivedBytes | Total de bytes recebidos do Tomcat | Bytes | Total
Sim | Microsoft. AppPlatform/Spring | TomcatRequestMaxTime | Tempo máximo de solicitação do Tomcat | Milissegundos | Máximo
Sim | Microsoft. AppPlatform/Spring | TomcatRequestTotalCount | Contagem total de solicitações do Tomcat | Contagem | Total
Sim | Microsoft. AppPlatform/Spring | TomcatRequestTotalTime | Tempos totais da solicitação do Tomcat | Milissegundos | Total
Sim | Microsoft. AppPlatform/Spring | TomcatResponseAvgTime | Tempo médio da solicitação do Tomcat | Milissegundos | Média
Sim | Microsoft. AppPlatform/Spring | TomcatSentBytes | Total de bytes enviados do Tomcat | Bytes | Total
Sim | Microsoft. AppPlatform/Spring | TomcatSessionActiveCurrentCount | Contagem de sessões ativas de Tomcat | Contagem | Total
Sim | Microsoft. AppPlatform/Spring | TomcatSessionActiveMaxCount | Contagem ativa máxima da sessão do Tomcat | Contagem | Total
Sim | Microsoft. AppPlatform/Spring | TomcatSessionAliveMaxTime | Tempo de atividade máx de sessão do Tomcat | Milissegundos | Máximo
Sim | Microsoft. AppPlatform/Spring | TomcatSessionCreatedCount | Contagem de sessões criadas do Tomcat | Contagem | Total
Sim | Microsoft. AppPlatform/Spring | TomcatSessionExpiredCount | Contagem de sessões expiradas do Tomcat | Contagem | Total
Sim | Microsoft. AppPlatform/Spring | TomcatSessionRejectedCount | Contagem rejeitada da sessão Tomcat | Contagem | Total
Sim | Microsoft. AppPlatform/Spring | YoungGenPromotedBytes | Promover para tamanho de dados de geração jovem | Bytes | Máximo
Sim | Microsoft.Automation/automationAccounts | TotalJob | Total de trabalhos | Contagem | Total
Sim | Microsoft.Automation/automationAccounts | TotalUpdateDeploymentMachineRuns | Total de execuções do computador de implantação de atualização | Contagem | Total
Sim | Microsoft.Automation/automationAccounts | TotalUpdateDeploymentRuns | Total de execuções de implantação de atualização | Contagem | Total
Não | Microsoft.Batch/batchAccounts | CoreCount | Contagem de núcleos dedicados | Contagem | Total
Não | Microsoft.Batch/batchAccounts | CreatingNodeCount | Criação de contagem de nós | Contagem | Total
Não | Microsoft.Batch/batchAccounts | IdleNodeCount | Contagem de nós ociosos | Contagem | Total
Sim | Microsoft.Batch/batchAccounts | JobDeleteCompleteEvent | Eventos de conclusão de exclusão do trabalho | Contagem | Total
Sim | Microsoft.Batch/batchAccounts | JobDeleteStartEvent | Eventos de início de exclusão do trabalho | Contagem | Total
Sim | Microsoft.Batch/batchAccounts | JobDisableCompleteEvent | Eventos de conclusão de desabilitação do trabalho | Contagem | Total
Sim | Microsoft.Batch/batchAccounts | JobDisableStartEvent | Eventos de início de desabilitação do trabalho | Contagem | Total
Sim | Microsoft.Batch/batchAccounts | JobStartEvent | Eventos de início do trabalho | Contagem | Total
Sim | Microsoft.Batch/batchAccounts | JobTerminateCompleteEvent | Eventos de conclusão de encerramento do trabalho | Contagem | Total
Sim | Microsoft.Batch/batchAccounts | JobTerminateStartEvent | Eventos de início de encerramento do trabalho | Contagem | Total
Não | Microsoft.Batch/batchAccounts | LeavingPoolNodeCount | Contagem de nós saindo do pool | Contagem | Total
Não | Microsoft.Batch/batchAccounts | LowPriorityCoreCount | Contagem de núcleos LowPriority | Contagem | Total
Não | Microsoft.Batch/batchAccounts | OfflineNodeCount | Contagem de nós off-line | Contagem | Total
Sim | Microsoft.Batch/batchAccounts | PoolCreateEvent | Eventos de criação de pool | Contagem | Total
Sim | Microsoft.Batch/batchAccounts | PoolDeleteCompleteEvent | Eventos de conclusão de exclusão do pool | Contagem | Total
Sim | Microsoft.Batch/batchAccounts | PoolDeleteStartEvent | Eventos de início de exclusão de pool | Contagem | Total
Sim | Microsoft.Batch/batchAccounts | PoolResizeCompleteEvent | Eventos de conclusão de redimensionamento de pool | Contagem | Total
Sim | Microsoft.Batch/batchAccounts | PoolResizeStartEvent | Eventos de início de redimensionamento de pool | Contagem | Total
Não | Microsoft.Batch/batchAccounts | PreemptedNodeCount | Contagem de nós com preempção | Contagem | Total
Não | Microsoft.Batch/batchAccounts | RebootingNodeCount | Contagem de nós de reinicialização | Contagem | Total
Não | Microsoft.Batch/batchAccounts | ReimagingNodeCount | Contagem de nós refazendo a imagem | Contagem | Total
Não | Microsoft.Batch/batchAccounts | RunningNodeCount | Contagem de nós em execução | Contagem | Total
Não | Microsoft.Batch/batchAccounts | StartingNodeCount | Contagem inicial dos nós | Contagem | Total
Não | Microsoft.Batch/batchAccounts | StartTaskFailedNodeCount | Falha na contagem de nós para a tarefa de inicialização | Contagem | Total
Sim | Microsoft.Batch/batchAccounts | TaskCompleteEvent | Eventos de conclusão de tarefa | Contagem | Total
Sim | Microsoft.Batch/batchAccounts | TaskFailEvent | Eventos de falha de tarefa | Contagem | Total
Sim | Microsoft.Batch/batchAccounts | TaskStartEvent | Eventos da tarefa de inicialização | Contagem | Total
Não | Microsoft.Batch/batchAccounts | TotalLowPriorityNodeCount | Contagem de nós de baixa prioridade | Contagem | Total
Não | Microsoft.Batch/batchAccounts | TotalNodeCount | Contagem de nós dedicados | Contagem | Total
Não | Microsoft.Batch/batchAccounts | UnusableNodeCount | Contagem de nós inutilizáveis | Contagem | Total
Não | Microsoft.Batch/batchAccounts | WaitingForStartTaskNodeCount | Contagem de nós para tarefa de inicialização em espera | Contagem | Total
Sim | Microsoft. BatchAI/Workspaces | Núcleos ativos | Núcleos ativos | Contagem | Média
Sim | Microsoft. BatchAI/Workspaces | Nós ativos | Nós ativos | Contagem | Média
Sim | Microsoft. BatchAI/Workspaces | Núcleos ociosos | Núcleos ociosos | Contagem | Média
Sim | Microsoft. BatchAI/Workspaces | Nós ociosos | Nós ociosos | Contagem | Média
Sim | Microsoft. BatchAI/Workspaces | Trabalho concluído | Trabalho concluído | Contagem | Total
Sim | Microsoft. BatchAI/Workspaces | Trabalho enviado | Trabalho enviado | Contagem | Total
Sim | Microsoft. BatchAI/Workspaces | Deixando núcleos | Deixando núcleos | Contagem | Média
Sim | Microsoft. BatchAI/Workspaces | Saindo de nós | Saindo de nós | Contagem | Média
Sim | Microsoft. BatchAI/Workspaces | Núcleos preempçãos | Núcleos preempçãos | Contagem | Média
Sim | Microsoft. BatchAI/Workspaces | Nós admitidos | Nós admitidos | Contagem | Média
Sim | Microsoft. BatchAI/Workspaces | Porcentagem de utilização de cota | Porcentagem de utilização de cota | Contagem | Média
Sim | Microsoft. BatchAI/Workspaces | Total de núcleos | Total de núcleos | Contagem | Média
Sim | Microsoft. BatchAI/Workspaces | Total de nós | Total de nós | Contagem | Média
Sim | Microsoft. BatchAI/Workspaces | Núcleos inutilizáveis | Núcleos inutilizáveis | Contagem | Média
Sim | Microsoft. BatchAI/Workspaces | Nós inutilizáveis | Nós inutilizáveis | Contagem | Média
Sim | Microsoft.Blockchain/blockchainMembers | ConnectionAccepted | Conexões aceitas | Contagem | Total
Sim | Microsoft.Blockchain/blockchainMembers | ConnectionActive | Conexões ativas | Contagem | Média
Sim | Microsoft.Blockchain/blockchainMembers | ConnectionHandled | Conexões manipuladas | Contagem | Total
Sim | Microsoft.Blockchain/blockchainMembers | CpuUsagePercentageInDouble | Porcentagem de uso da CPU | Porcentagem | Máximo
Sim | Microsoft.Blockchain/blockchainMembers | IOReadBytes | Bytes de leitura de e/s | Bytes | Total
Sim | Microsoft.Blockchain/blockchainMembers | IOWriteBytes | Bytes de gravação de e/s | Bytes | Total
Sim | Microsoft.Blockchain/blockchainMembers | MemoryLimit | Limite de memória | Bytes | Média
Sim | Microsoft.Blockchain/blockchainMembers | MemoryUsage | Uso de Memória | Bytes | Média
Sim | Microsoft.Blockchain/blockchainMembers | MemoryUsagePercentageInDouble | Porcentagem de uso da memória | Porcentagem | Média
Sim | Microsoft.Blockchain/blockchainMembers | PendingTransactions | Transações pendentes | Contagem | Média
Sim | Microsoft.Blockchain/blockchainMembers | ProcessedBlocks | Blocos processados | Contagem | Total
Sim | Microsoft.Blockchain/blockchainMembers | ProcessedTransactions | Transações processadas | Contagem | Total
Sim | Microsoft.Blockchain/blockchainMembers | QueuedTransactions | Transações em fila | Contagem | Média
Sim | Microsoft.Blockchain/blockchainMembers | RequestHandled | Solicitações manipuladas | Contagem | Total
Sim | Microsoft.Blockchain/blockchainMembers | StorageUsage | Uso do armazenamento | Bytes | Média
Sim | Microsoft.Cache/redis | cachehits | Acertos do Cache | Contagem | Total
Sim | Microsoft.Cache/redis | cachehits0 | Ocorrências no cache (Fragmento 0) | Contagem | Total
Sim | Microsoft.Cache/redis | cachehits1 | Ocorrências no cache (Fragmento 1) | Contagem | Total
Sim | Microsoft.Cache/redis | cachehits2 | Ocorrências no cache (Fragmento 2) | Contagem | Total
Sim | Microsoft.Cache/redis | cachehits3 | Ocorrências no cache (Fragmento 3) | Contagem | Total
Sim | Microsoft.Cache/redis | cachehits4 | Ocorrências no cache (Fragmento 4) | Contagem | Total
Sim | Microsoft.Cache/redis | cachehits5 | Ocorrências no cache (Fragmento 5) | Contagem | Total
Sim | Microsoft.Cache/redis | cachehits6 | Ocorrências no cache (Fragmento 6) | Contagem | Total
Sim | Microsoft.Cache/redis | cachehits7 | Ocorrências no cache (Fragmento 7) | Contagem | Total
Sim | Microsoft.Cache/redis | cachehits8 | Ocorrências no cache (Fragmento 8) | Contagem | Total
Sim | Microsoft.Cache/redis | cachehits9 | Ocorrências no cache (Fragmento 9) | Contagem | Total
Sim | Microsoft.Cache/redis | cacheLatency | Microssegundos de latência de cache (versão prévia) | Contagem | Média
Sim | Microsoft.Cache/redis | cachemisses | Erros de Cache | Contagem | Total
Sim | Microsoft.Cache/redis | cachemisses0 | Perdas no cache (Fragmento 0) | Contagem | Total
Sim | Microsoft.Cache/redis | cachemisses1 | Perdas no cache (Fragmento 1) | Contagem | Total
Sim | Microsoft.Cache/redis | cachemisses2 | Perdas no cache (Fragmento 2) | Contagem | Total
Sim | Microsoft.Cache/redis | cachemisses3 | Perdas no cache (Fragmento 3) | Contagem | Total
Sim | Microsoft.Cache/redis | cachemisses4 | Perdas no cache (Fragmento 4) | Contagem | Total
Sim | Microsoft.Cache/redis | cachemisses5 | Perdas no cache (Fragmento 5) | Contagem | Total
Sim | Microsoft.Cache/redis | cachemisses6 | Perdas no cache (Fragmento 6) | Contagem | Total
Sim | Microsoft.Cache/redis | cachemisses7 | Perdas no cache (Fragmento 7) | Contagem | Total
Sim | Microsoft.Cache/redis | cachemisses8 | Perdas no cache (Fragmento 8) | Contagem | Total
Sim | Microsoft.Cache/redis | cachemisses9 | Perdas no cache (Fragmento 9) | Contagem | Total
Sim | Microsoft.Cache/redis | cacheRead | Cache Lido | BytesPerSecond | Máximo
Sim | Microsoft.Cache/redis | cacheRead0 | Leitura no cache (Fragmento 0) | BytesPerSecond | Máximo
Sim | Microsoft.Cache/redis | cacheRead1 | Leitura no cache (Fragmento 1) | BytesPerSecond | Máximo
Sim | Microsoft.Cache/redis | cacheRead2 | Leitura no cache (Fragmento 2) | BytesPerSecond | Máximo
Sim | Microsoft.Cache/redis | cacheRead3 | Leitura no cache (Fragmento 3) | BytesPerSecond | Máximo
Sim | Microsoft.Cache/redis | cacheRead4 | Leitura no cache (Fragmento 4) | BytesPerSecond | Máximo
Sim | Microsoft.Cache/redis | cacheRead5 | Leitura no cache (Fragmento 5) | BytesPerSecond | Máximo
Sim | Microsoft.Cache/redis | cacheRead6 | Leitura no cache (Fragmento 6) | BytesPerSecond | Máximo
Sim | Microsoft.Cache/redis | cacheRead7 | Leitura no cache (Fragmento 7) | BytesPerSecond | Máximo
Sim | Microsoft.Cache/redis | cacheRead8 | Leitura no cache (Fragmento 8) | BytesPerSecond | Máximo
Sim | Microsoft.Cache/redis | cacheRead9 | Leitura no cache (Fragmento 9) | BytesPerSecond | Máximo
Sim | Microsoft.Cache/redis | cacheWrite | Gravação no Cache | BytesPerSecond | Máximo
Sim | Microsoft.Cache/redis | cacheWrite0 | Gravação no cache (Fragmento 0) | BytesPerSecond | Máximo
Sim | Microsoft.Cache/redis | cacheWrite1 | Gravação no cache (Fragmento 1) | BytesPerSecond | Máximo
Sim | Microsoft.Cache/redis | cacheWrite2 | Gravação no cache (Fragmento 2) | BytesPerSecond | Máximo
Sim | Microsoft.Cache/redis | cacheWrite3 | Gravação no cache (Fragmento 3) | BytesPerSecond | Máximo
Sim | Microsoft.Cache/redis | cacheWrite4 | Gravação no cache (Fragmento 4) | BytesPerSecond | Máximo
Sim | Microsoft.Cache/redis | cacheWrite5 | Gravação no cache (Fragmento 5) | BytesPerSecond | Máximo
Sim | Microsoft.Cache/redis | cacheWrite6 | Gravação no cache (Fragmento 6) | BytesPerSecond | Máximo
Sim | Microsoft.Cache/redis | cacheWrite7 | Gravação no cache (Fragmento 7) | BytesPerSecond | Máximo
Sim | Microsoft.Cache/redis | cacheWrite8 | Gravação no cache (Fragmento 8) | BytesPerSecond | Máximo
Sim | Microsoft.Cache/redis | cacheWrite9 | Gravação no cache (Fragmento 9) | BytesPerSecond | Máximo
Sim | Microsoft.Cache/redis | connectedclients | Clientes conectados | Contagem | Máximo
Sim | Microsoft.Cache/redis | connectedclients0 | Clientes conectados (Fragmento 0) | Contagem | Máximo
Sim | Microsoft.Cache/redis | connectedclients1 | Clientes conectados (Fragmento 1) | Contagem | Máximo
Sim | Microsoft.Cache/redis | connectedclients2 | Clientes conectados (Fragmento 2) | Contagem | Máximo
Sim | Microsoft.Cache/redis | connectedclients3 | Clientes conectados (Fragmento 3) | Contagem | Máximo
Sim | Microsoft.Cache/redis | connectedclients4 | Clientes conectados (Fragmento 4) | Contagem | Máximo
Sim | Microsoft.Cache/redis | connectedclients5 | Clientes conectados (Fragmento 5) | Contagem | Máximo
Sim | Microsoft.Cache/redis | connectedclients6 | Clientes conectados (Fragmento 6) | Contagem | Máximo
Sim | Microsoft.Cache/redis | connectedclients7 | Clientes conectados (Fragmento 7) | Contagem | Máximo
Sim | Microsoft.Cache/redis | connectedclients8 | Clientes conectados (Fragmento 8) | Contagem | Máximo
Sim | Microsoft.Cache/redis | connectedclients9 | Clientes conectados (Fragmento 9) | Contagem | Máximo
Sim | Microsoft.Cache/redis | erros | Errors | Contagem | Máximo
Sim | Microsoft.Cache/redis | evictedkeys | Chaves removidas | Contagem | Total
Sim | Microsoft.Cache/redis | evictedkeys0 | Chaves removidas (Fragmento 0) | Contagem | Total
Sim | Microsoft.Cache/redis | evictedkeys1 | Chaves removidas (Fragmento 1) | Contagem | Total
Sim | Microsoft.Cache/redis | evictedkeys2 | Chaves removidas (Fragmento 2) | Contagem | Total
Sim | Microsoft.Cache/redis | evictedkeys3 | Chaves removidas (Fragmento 3) | Contagem | Total
Sim | Microsoft.Cache/redis | evictedkeys4 | Chaves removidas (Fragmento 4) | Contagem | Total
Sim | Microsoft.Cache/redis | evictedkeys5 | Chaves removidas (Fragmento 5) | Contagem | Total
Sim | Microsoft.Cache/redis | evictedkeys6 | Chaves removidas (Fragmento 6) | Contagem | Total
Sim | Microsoft.Cache/redis | evictedkeys7 | Chaves removidas (Fragmento 7) | Contagem | Total
Sim | Microsoft.Cache/redis | evictedkeys8 | Chaves removidas (Fragmento 8) | Contagem | Total
Sim | Microsoft.Cache/redis | evictedkeys9 | Chaves removidas (Fragmento 9) | Contagem | Total
Sim | Microsoft.Cache/redis | expiredkeys | Chaves expiradas | Contagem | Total
Sim | Microsoft.Cache/redis | expiredkeys0 | Chaves expiradas (Fragmento 0) | Contagem | Total
Sim | Microsoft.Cache/redis | expiredkeys1 | Chaves expiradas (Fragmento 1) | Contagem | Total
Sim | Microsoft.Cache/redis | expiredkeys2 | Chaves expiradas (Fragmento 2) | Contagem | Total
Sim | Microsoft.Cache/redis | expiredkeys3 | Chaves expiradas (Fragmento 3) | Contagem | Total
Sim | Microsoft.Cache/redis | expiredkeys4 | Chaves expiradas (Fragmento 4) | Contagem | Total
Sim | Microsoft.Cache/redis | expiredkeys5 | Chaves expiradas (Fragmento 5) | Contagem | Total
Sim | Microsoft.Cache/redis | expiredkeys6 | Chaves expiradas (Fragmento 6) | Contagem | Total
Sim | Microsoft.Cache/redis | expiredkeys7 | Chaves expiradas (Fragmento 7) | Contagem | Total
Sim | Microsoft.Cache/redis | expiredkeys8 | Chaves expiradas (Fragmento 8) | Contagem | Total
Sim | Microsoft.Cache/redis | expiredkeys9 | Chaves expiradas (Fragmento 9) | Contagem | Total
Sim | Microsoft.Cache/redis | getcommands | Gets | Contagem | Total
Sim | Microsoft.Cache/redis | getcommands0 | Gets (Fragmento 0) | Contagem | Total
Sim | Microsoft.Cache/redis | getcommands1 | Gets (Fragmento 1) | Contagem | Total
Sim | Microsoft.Cache/redis | getcommands2 | Gets (Fragmento 2) | Contagem | Total
Sim | Microsoft.Cache/redis | getcommands3 | Gets (Fragmento 3) | Contagem | Total
Sim | Microsoft.Cache/redis | getcommands4 | Gets (Fragmento 4) | Contagem | Total
Sim | Microsoft.Cache/redis | getcommands5 | Gets (Fragmento 5) | Contagem | Total
Sim | Microsoft.Cache/redis | getcommands6 | Gets (Fragmento 6) | Contagem | Total
Sim | Microsoft.Cache/redis | getcommands7 | Gets (Fragmento 7) | Contagem | Total
Sim | Microsoft.Cache/redis | getcommands8 | Gets (Fragmento 8) | Contagem | Total
Sim | Microsoft.Cache/redis | getcommands9 | Gets (Fragmento 9) | Contagem | Total
Sim | Microsoft.Cache/redis | operationsPerSecond | Operations por segundo | Contagem | Máximo
Sim | Microsoft.Cache/redis | operationsPerSecond0 | Operações por segundo (fragmento 0) | Contagem | Máximo
Sim | Microsoft.Cache/redis | operationsPerSecond1 | Operações por segundo (fragmento 1) | Contagem | Máximo
Sim | Microsoft.Cache/redis | operationsPerSecond2 | Operações por segundo (fragmento 2) | Contagem | Máximo
Sim | Microsoft.Cache/redis | operationsPerSecond3 | Operações por segundo (fragmento 3) | Contagem | Máximo
Sim | Microsoft.Cache/redis | operationsPerSecond4 | Operações por segundo (fragmento 4) | Contagem | Máximo
Sim | Microsoft.Cache/redis | operationsPerSecond5 | Operações por segundo (fragmento 5) | Contagem | Máximo
Sim | Microsoft.Cache/redis | operationsPerSecond6 | Operações por segundo (fragmento 6) | Contagem | Máximo
Sim | Microsoft.Cache/redis | operationsPerSecond7 | Operações por segundo (fragmento 7) | Contagem | Máximo
Sim | Microsoft.Cache/redis | operationsPerSecond8 | Operações por segundo (fragmento 8) | Contagem | Máximo
Sim | Microsoft.Cache/redis | operationsPerSecond9 | Operações por segundo (fragmento 9) | Contagem | Máximo
Sim | Microsoft.Cache/redis | percentProcessorTime | CPU | Porcentagem | Máximo
Sim | Microsoft.Cache/redis | percentProcessorTime0 | CPU (Fragmento 0) | Porcentagem | Máximo
Sim | Microsoft.Cache/redis | percentProcessorTime1 | CPU (Fragmento 1) | Porcentagem | Máximo
Sim | Microsoft.Cache/redis | percentProcessorTime2 | CPU (Fragmento 2) | Porcentagem | Máximo
Sim | Microsoft.Cache/redis | percentProcessorTime3 | CPU (Fragmento 3) | Porcentagem | Máximo
Sim | Microsoft.Cache/redis | percentProcessorTime4 | CPU (Fragmento 4) | Porcentagem | Máximo
Sim | Microsoft.Cache/redis | percentProcessorTime5 | CPU (Fragmento 5) | Porcentagem | Máximo
Sim | Microsoft.Cache/redis | percentProcessorTime6 | CPU (Fragmento 6) | Porcentagem | Máximo
Sim | Microsoft.Cache/redis | percentProcessorTime7 | CPU (Fragmento 7) | Porcentagem | Máximo
Sim | Microsoft.Cache/redis | percentProcessorTime8 | CPU (Fragmento 8) | Porcentagem | Máximo
Sim | Microsoft.Cache/redis | percentProcessorTime9 | CPU (Fragmento 9) | Porcentagem | Máximo
Sim | Microsoft.Cache/redis | serverLoad | Carga do Servidor | Porcentagem | Máximo
Sim | Microsoft.Cache/redis | serverLoad0 | Carga do servidor (Fragmento 0) | Porcentagem | Máximo
Sim | Microsoft.Cache/redis | serverLoad1 | Carga do servidor (Fragmento 1) | Porcentagem | Máximo
Sim | Microsoft.Cache/redis | serverLoad2 | Carga do servidor (Fragmento 2) | Porcentagem | Máximo
Sim | Microsoft.Cache/redis | serverLoad3 | Carga do servidor (Fragmento 3) | Porcentagem | Máximo
Sim | Microsoft.Cache/redis | serverLoad4 | Carga do servidor (Fragmento 4) | Porcentagem | Máximo
Sim | Microsoft.Cache/redis | serverLoad5 | Carga do servidor (Fragmento 5) | Porcentagem | Máximo
Sim | Microsoft.Cache/redis | serverLoad6 | Carga do servidor (Fragmento 6) | Porcentagem | Máximo
Sim | Microsoft.Cache/redis | serverLoad7 | Carga do servidor (Fragmento 7) | Porcentagem | Máximo
Sim | Microsoft.Cache/redis | serverLoad8 | Carga do servidor (Fragmento 8) | Porcentagem | Máximo
Sim | Microsoft.Cache/redis | serverLoad9 | Carga do servidor (Fragmento 9) | Porcentagem | Máximo
Sim | Microsoft.Cache/redis | setcommands | Conjuntos | Contagem | Total
Sim | Microsoft.Cache/redis | setcommands0 | Sets (Fragmento 0) | Contagem | Total
Sim | Microsoft.Cache/redis | setcommands1 | Sets (Fragmento 1) | Contagem | Total
Sim | Microsoft.Cache/redis | setcommands2 | Sets (Fragmento 2) | Contagem | Total
Sim | Microsoft.Cache/redis | setcommands3 | Sets (Fragmento 3) | Contagem | Total
Sim | Microsoft.Cache/redis | setcommands4 | Sets (Fragmento 4) | Contagem | Total
Sim | Microsoft.Cache/redis | setcommands5 | Sets (Fragmento 5) | Contagem | Total
Sim | Microsoft.Cache/redis | setcommands6 | Sets (Fragmento 6) | Contagem | Total
Sim | Microsoft.Cache/redis | setcommands7 | Sets (Fragmento 7) | Contagem | Total
Sim | Microsoft.Cache/redis | setcommands8 | Sets (Fragmento 8) | Contagem | Total
Sim | Microsoft.Cache/redis | setcommands9 | Sets (Fragmento 9) | Contagem | Total
Sim | Microsoft.Cache/redis | totalcommandsprocessed | Total de Operações | Contagem | Total
Sim | Microsoft.Cache/redis | totalcommandsprocessed0 | Total de operações (Fragmento 0) | Contagem | Total
Sim | Microsoft.Cache/redis | totalcommandsprocessed1 | Total de operações (Fragmento 1) | Contagem | Total
Sim | Microsoft.Cache/redis | totalcommandsprocessed2 | Total de operações (Fragmento 2) | Contagem | Total
Sim | Microsoft.Cache/redis | totalcommandsprocessed3 | Total de operações (Fragmento 3) | Contagem | Total
Sim | Microsoft.Cache/redis | totalcommandsprocessed4 | Total de operações (Fragmento 4) | Contagem | Total
Sim | Microsoft.Cache/redis | totalcommandsprocessed5 | Total de operações (Fragmento 5) | Contagem | Total
Sim | Microsoft.Cache/redis | totalcommandsprocessed6 | Total de operações (Fragmento 6) | Contagem | Total
Sim | Microsoft.Cache/redis | totalcommandsprocessed7 | Total de operações (Fragmento 7) | Contagem | Total
Sim | Microsoft.Cache/redis | totalcommandsprocessed8 | Total de operações (Fragmento 8) | Contagem | Total
Sim | Microsoft.Cache/redis | totalcommandsprocessed9 | Total de operações (Fragmento 9) | Contagem | Total
Sim | Microsoft.Cache/redis | totalkeys | Total de chaves | Contagem | Máximo
Sim | Microsoft.Cache/redis | totalkeys0 | Total de Chaves (Fragmento 0) | Contagem | Máximo
Sim | Microsoft.Cache/redis | totalkeys1 | Total de Chaves (Fragmento 1) | Contagem | Máximo
Sim | Microsoft.Cache/redis | totalkeys2 | Total de Chaves (Fragmento 2) | Contagem | Máximo
Sim | Microsoft.Cache/redis | totalkeys3 | Total de Chaves (Fragmento 3) | Contagem | Máximo
Sim | Microsoft.Cache/redis | totalkeys4 | Total de Chaves (Fragmento 4) | Contagem | Máximo
Sim | Microsoft.Cache/redis | totalkeys5 | Total de Chaves (Fragmento 5) | Contagem | Máximo
Sim | Microsoft.Cache/redis | totalkeys6 | Total de Chaves (Fragmento 6) | Contagem | Máximo
Sim | Microsoft.Cache/redis | totalkeys7 | Total de Chaves (Fragmento 7) | Contagem | Máximo
Sim | Microsoft.Cache/redis | totalkeys8 | Total de Chaves (Fragmento 8) | Contagem | Máximo
Sim | Microsoft.Cache/redis | totalkeys9 | Total de Chaves (Fragmento 9) | Contagem | Máximo
Sim | Microsoft.Cache/redis | usedmemory | Memória Usada | Bytes | Máximo
Sim | Microsoft.Cache/redis | usedmemory0 | Memória usada (Fragmento 0) | Bytes | Máximo
Sim | Microsoft.Cache/redis | usedmemory1 | Memória usada (Fragmento 1) | Bytes | Máximo
Sim | Microsoft.Cache/redis | usedmemory2 | Memória usada (Fragmento 2) | Bytes | Máximo
Sim | Microsoft.Cache/redis | usedmemory3 | Memória usada (Fragmento 3) | Bytes | Máximo
Sim | Microsoft.Cache/redis | usedmemory4 | Memória usada (Fragmento 4) | Bytes | Máximo
Sim | Microsoft.Cache/redis | usedmemory5 | Memória usada (Fragmento 5) | Bytes | Máximo
Sim | Microsoft.Cache/redis | usedmemory6 | Memória usada (Fragmento 6) | Bytes | Máximo
Sim | Microsoft.Cache/redis | usedmemory7 | Memória usada (Fragmento 7) | Bytes | Máximo
Sim | Microsoft.Cache/redis | usedmemory8 | Memória usada (Fragmento 8) | Bytes | Máximo
Sim | Microsoft.Cache/redis | usedmemory9 | Memória usada (Fragmento 9) | Bytes | Máximo
Sim | Microsoft.Cache/redis | usedmemorypercentage | Porcentagem de memória utilizada | Porcentagem | Máximo
Sim | Microsoft.Cache/redis | usedmemoryRss | Memória RSS Usada | Bytes | Máximo
Sim | Microsoft.Cache/redis | usedmemoryRss0 | Memória RSS usada (Fragmento 0) | Bytes | Máximo
Sim | Microsoft.Cache/redis | usedmemoryRss1 | Memória RSS usada (Fragmento 1) | Bytes | Máximo
Sim | Microsoft.Cache/redis | usedmemoryRss2 | Memória RSS usada (Fragmento 2) | Bytes | Máximo
Sim | Microsoft.Cache/redis | usedmemoryRss3 | Memória RSS usada (Fragmento 3) | Bytes | Máximo
Sim | Microsoft.Cache/redis | usedmemoryRss4 | Memória RSS usada (Fragmento 4) | Bytes | Máximo
Sim | Microsoft.Cache/redis | usedmemoryRss5 | Memória RSS usada (Fragmento 5) | Bytes | Máximo
Sim | Microsoft.Cache/redis | usedmemoryRss6 | Memória RSS usada (Fragmento 6) | Bytes | Máximo
Sim | Microsoft.Cache/redis | usedmemoryRss7 | Memória RSS usada (Fragmento 7) | Bytes | Máximo
Sim | Microsoft.Cache/redis | usedmemoryRss8 | Memória RSS usada (Fragmento 8) | Bytes | Máximo
Sim | Microsoft.Cache/redis | usedmemoryRss9 | Memória RSS usada (Fragmento 9) | Bytes | Máximo
Não | Microsoft.ClassicCompute/domainNames/slots/roles | Disk Read Bytes/Sec | Leitura de disco | BytesPerSecond | Média
Sim | Microsoft.ClassicCompute/domainNames/slots/roles | Operações de leitura de disco/Seg | Operações de leitura de disco/Seg | CountPerSecond | Média
Não | Microsoft.ClassicCompute/domainNames/slots/roles | Disk Write Bytes/Sec | Gravação de disco | BytesPerSecond | Média
Sim | Microsoft.ClassicCompute/domainNames/slots/roles | Operações de gravação de disco/Seg | Operações de gravação de disco/Seg | CountPerSecond | Média
Sim | Microsoft.ClassicCompute/domainNames/slots/roles | Entrada na rede | Entrada na rede | Bytes | Total
Sim | Microsoft.ClassicCompute/domainNames/slots/roles | Saída da rede | Saída da rede | Bytes | Total
Sim | Microsoft.ClassicCompute/domainNames/slots/roles | Porcentagem de CPU | Porcentagem de CPU | Porcentagem | Média
Não | Microsoft.ClassicCompute/virtualMachines | Disk Read Bytes/Sec | Leitura de disco | BytesPerSecond | Média
Sim | Microsoft.ClassicCompute/virtualMachines | Operações de leitura de disco/Seg | Operações de leitura de disco/Seg | CountPerSecond | Média
Não | Microsoft.ClassicCompute/virtualMachines | Disk Write Bytes/Sec | Gravação de disco | BytesPerSecond | Média
Sim | Microsoft.ClassicCompute/virtualMachines | Operações de gravação de disco/Seg | Operações de gravação de disco/Seg | CountPerSecond | Média
Sim | Microsoft.ClassicCompute/virtualMachines | Entrada na rede | Entrada na rede | Bytes | Total
Sim | Microsoft.ClassicCompute/virtualMachines | Saída da rede | Saída da rede | Bytes | Total
Sim | Microsoft.ClassicCompute/virtualMachines | Porcentagem de CPU | Porcentagem de CPU | Porcentagem | Média
Sim | Microsoft.ClassicStorage/storageAccounts | Disponibilidade | Disponibilidade | Porcentagem | Média
Sim | Microsoft.ClassicStorage/storageAccounts | Saída | Saída | Bytes | Total
Sim | Microsoft.ClassicStorage/storageAccounts | Entrada | Entrada | Bytes | Total
Sim | Microsoft.ClassicStorage/storageAccounts | SuccessE2ELatency | Latência de E2E com Sucesso | Milissegundos | Média
Sim | Microsoft.ClassicStorage/storageAccounts | SuccessServerLatency | Latência de Servidor Com Sucesso | Milissegundos | Média
Sim | Microsoft.ClassicStorage/storageAccounts | Transações | Transações | Contagem | Total
Não | Microsoft.ClassicStorage/storageAccounts | UsedCapacity | Capacidade utilizada | Bytes | Média
Sim | Microsoft. ClassicStorage/storageAccounts/blobservices | Disponibilidade | Disponibilidade | Porcentagem | Média
Não | Microsoft. ClassicStorage/storageAccounts/blobservices | BlobCapacity | Capacidade do Blob | Bytes | Média
Não | Microsoft. ClassicStorage/storageAccounts/blobservices | BlobCount | Contagem de Blobs | Contagem | Média
Sim | Microsoft. ClassicStorage/storageAccounts/blobservices | ContainerCount | Contagem de Contêineres de Blobs | Contagem | Média
Sim | Microsoft. ClassicStorage/storageAccounts/blobservices | Saída | Saída | Bytes | Total
Não | Microsoft. ClassicStorage/storageAccounts/blobservices | IndexCapacity | Capacidade de Índice | Bytes | Média
Sim | Microsoft. ClassicStorage/storageAccounts/blobservices | Entrada | Entrada | Bytes | Total
Sim | Microsoft. ClassicStorage/storageAccounts/blobservices | SuccessE2ELatency | Latência de E2E com Sucesso | Milissegundos | Média
Sim | Microsoft. ClassicStorage/storageAccounts/blobservices | SuccessServerLatency | Latência de Servidor Com Sucesso | Milissegundos | Média
Sim | Microsoft. ClassicStorage/storageAccounts/blobservices | Transações | Transações | Contagem | Total
Sim | Microsoft. ClassicStorage/storageAccounts/fileservices | Disponibilidade | Disponibilidade | Porcentagem | Média
Sim | Microsoft. ClassicStorage/storageAccounts/fileservices | Saída | Saída | Bytes | Total
Não | Microsoft. ClassicStorage/storageAccounts/fileservices | FileCapacity | Capacidade do Arquivo | Bytes | Média
Não | Microsoft. ClassicStorage/storageAccounts/fileservices | FileCount | Contagem de Arquivos | Contagem | Média
Não | Microsoft. ClassicStorage/storageAccounts/fileservices | FileShareCount | Contagem de Compartilhamentos de Arquivos | Contagem | Média
Não | Microsoft. ClassicStorage/storageAccounts/fileservices | FileShareQuota | Tamanho da cota de compartilhamento de arquivos | Bytes | Média
Não | Microsoft. ClassicStorage/storageAccounts/fileservices | FileShareSnapshotCount | Contagem de instantâneos de compartilhamento de arquivos | Contagem | Média
Não | Microsoft. ClassicStorage/storageAccounts/fileservices | FileShareSnapshotSize | Tamanho do instantâneo de compartilhamento de arquivos | Bytes | Média
Sim | Microsoft. ClassicStorage/storageAccounts/fileservices | Entrada | Entrada | Bytes | Total
Sim | Microsoft. ClassicStorage/storageAccounts/fileservices | SuccessE2ELatency | Latência de E2E com Sucesso | Milissegundos | Média
Sim | Microsoft. ClassicStorage/storageAccounts/fileservices | SuccessServerLatency | Latência de Servidor Com Sucesso | Milissegundos | Média
Sim | Microsoft. ClassicStorage/storageAccounts/fileservices | Transações | Transações | Contagem | Total
Sim | Microsoft. ClassicStorage/storageAccounts/queueservices | Disponibilidade | Disponibilidade | Porcentagem | Média
Sim | Microsoft. ClassicStorage/storageAccounts/queueservices | Saída | Saída | Bytes | Total
Sim | Microsoft. ClassicStorage/storageAccounts/queueservices | Entrada | Entrada | Bytes | Total
Sim | Microsoft. ClassicStorage/storageAccounts/queueservices | QueueCapacity | Capacidade da Fila | Bytes | Média
Sim | Microsoft. ClassicStorage/storageAccounts/queueservices | QueueCount | Contagem de Filas | Contagem | Média
Sim | Microsoft. ClassicStorage/storageAccounts/queueservices | QueueMessageCount | Contagem de Mensagens da Fila | Contagem | Média
Sim | Microsoft. ClassicStorage/storageAccounts/queueservices | SuccessE2ELatency | Latência de E2E com Sucesso | Milissegundos | Média
Sim | Microsoft. ClassicStorage/storageAccounts/queueservices | SuccessServerLatency | Latência de Servidor Com Sucesso | Milissegundos | Média
Sim | Microsoft. ClassicStorage/storageAccounts/queueservices | Transações | Transações | Contagem | Total
Sim | Microsoft. ClassicStorage/storageAccounts/tabelaservices | Disponibilidade | Disponibilidade | Porcentagem | Média
Sim | Microsoft. ClassicStorage/storageAccounts/tabelaservices | Saída | Saída | Bytes | Total
Sim | Microsoft. ClassicStorage/storageAccounts/tabelaservices | Entrada | Entrada | Bytes | Total
Sim | Microsoft. ClassicStorage/storageAccounts/tabelaservices | SuccessE2ELatency | Latência de E2E com Sucesso | Milissegundos | Média
Sim | Microsoft. ClassicStorage/storageAccounts/tabelaservices | SuccessServerLatency | Latência de Servidor Com Sucesso | Milissegundos | Média
Sim | Microsoft. ClassicStorage/storageAccounts/tabelaservices | TableCapacity | Capacidade da Tabela | Bytes | Média
Sim | Microsoft. ClassicStorage/storageAccounts/tabelaservices | TableCount | Contagem de Tabelas | Contagem | Média
Sim | Microsoft. ClassicStorage/storageAccounts/tabelaservices | TableEntityCount | Contagem de Entidades de Tabela | Contagem | Média
Sim | Microsoft. ClassicStorage/storageAccounts/tabelaservices | Transações | Transações | Contagem | Total
Sim | Microsoft.CognitiveServices/accounts | BlockedCalls | Chamadas Bloqueadas | Contagem | Total
Sim | Microsoft.CognitiveServices/accounts | CharactersTrained | Caracteres treinados | Contagem | Total
Sim | Microsoft.CognitiveServices/accounts | CharactersTranslated | Caracteres traduzidos | Contagem | Total
Sim | Microsoft.CognitiveServices/accounts | ClientErrors | Erros do Cliente | Contagem | Total
Sim | Microsoft.CognitiveServices/accounts | DataIn | Entrada de Dados | Bytes | Total
Sim | Microsoft.CognitiveServices/accounts | DataOut | Saída de dados | Bytes | Total
Sim | Microsoft.CognitiveServices/accounts | Latência | Latência | MilliSeconds | Média
Sim | Microsoft.CognitiveServices/accounts | ServerErrors | Erros do Servidor | Contagem | Total
Sim | Microsoft.CognitiveServices/accounts | SpeechSessionDuration | Duração da Sessão de Fala | Segundos | Total
Sim | Microsoft.CognitiveServices/accounts | SuccessfulCalls | Chamadas com Êxito | Contagem | Total
Sim | Microsoft.CognitiveServices/accounts | TotalCalls | Total de Chamadas | Contagem | Total
Sim | Microsoft.CognitiveServices/accounts | TotalErrors | Total de Erros | Contagem | Total
Sim | Microsoft.CognitiveServices/accounts | TotalTokenCalls | Total de chamadas de token | Contagem | Total
Sim | Microsoft.CognitiveServices/accounts | TotalTransactions | Total de Transações | Contagem | Total
Sim | Microsoft.Compute/virtualMachines | Créditos de CPU Consumidos | Créditos de CPU Consumidos | Contagem | Média
Sim | Microsoft.Compute/virtualMachines | Créditos de CPU Restantes | Créditos de CPU Restantes | Contagem | Média
Sim | Microsoft.Compute/virtualMachines | Profundidade da Fila de Disco de Dados | Profundidade da fila do disco de dados (visualização) | Contagem | Média
Sim | Microsoft.Compute/virtualMachines | Bytes de leitura do disco de dados/s | Bytes de Leitura do Disco de Dados/s (Visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachines | Operações de leitura de disco de dados/s | Operações de Leitura do Disco de Dados/s (Visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachines | Bytes de gravação do disco de dados/s | Bytes de Gravação do Disco de Dados/s (Visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachines | Operações de gravação do disco de dados/s | Operações de Gravação do Disco de Dados/s (Visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachines | Bytes de leitura de disco | Bytes de leitura de disco | Bytes | Total
Sim | Microsoft.Compute/virtualMachines | Operações de leitura de disco/Seg | Operações de leitura de disco/Seg | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachines | Bytes de gravação de disco | Bytes de gravação de disco | Bytes | Total
Sim | Microsoft.Compute/virtualMachines | Operações de gravação de disco/Seg | Operações de gravação de disco/Seg | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachines | Fluxos de Entrada | Fluxos de Entrada | Contagem | Média
Sim | Microsoft.Compute/virtualMachines | Taxa máxima de criação de fluxos de entrada | Taxa máxima de criação de fluxos de entrada (visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachines | Entrada na rede | Rede em faturável (preterida) | Bytes | Total
Sim | Microsoft.Compute/virtualMachines | Total de rede | Total de rede | Bytes | Total
Sim | Microsoft.Compute/virtualMachines | Saída da rede | Saída de rede faturável (preterida) | Bytes | Total
Sim | Microsoft.Compute/virtualMachines | Total de saída de rede | Total de saída de rede | Bytes | Total
Sim | Microsoft.Compute/virtualMachines | Profundidade da Fila de Disco do SO | Profundidade da fila de disco do so (visualização) | Contagem | Média
Sim | Microsoft.Compute/virtualMachines | Bytes de leitura do disco do so/s | Bytes de Leitura do Disco do SO/s (Visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachines | Operações de leitura de disco do so/s | Operações de Leitura do Disco do SO/s (Visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachines | Bytes de gravação de disco do sistema operacional/s | Bytes de Gravação do Disco do SO/s (Visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachines | Operações de gravação de disco do so/s | Operações de Gravação do Disco do SO/s (Visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachines | Os Por QD de Disco | Disco do so QD (preterido) | Contagem | Média
Sim | Microsoft.Compute/virtualMachines | SO Por Bytes de Leitura do Disco/s | Bytes de leitura do disco do so/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachines | SO Por Operações de Leitura do Disco/s | Operações de leitura de disco do so/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachines | SO Por Bytes de Gravação do Disco/s | Bytes de gravação de disco do sistema operacional/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachines | SO Por Operações de Gravação do Disco/s | Operações de gravação de disco do sistema operacional/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachines | Fluxos de saída | Fluxos de saída | Contagem | Média
Sim | Microsoft.Compute/virtualMachines | Taxa máxima de criação de fluxos de saída | Taxa máxima de criação de fluxos de saída (versão prévia) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachines | Por QD de Disco | Disco de dados QD (preterido) | Contagem | Média
Sim | Microsoft.Compute/virtualMachines | Por Bytes de Leitura do Disco/s | Bytes de leitura do disco de dados/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachines | Por operações de Leitura de Disco/s | Operações de leitura de disco de dados/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachines | Por Bytes de Gravação de Disco/s | Bytes de gravação do disco de dados/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachines | Por Operações de Gravação de Disco/s | Operações de gravação do disco de dados/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachines | Porcentagem de CPU | Porcentagem de CPU | Porcentagem | Média
Sim | Microsoft.Compute/virtualMachines | Cache do disco de dados Premium-acesso de leitura | Leitura do cache do disco de dados Premium (versão prévia) | Porcentagem | Média
Sim | Microsoft.Compute/virtualMachines | Erro de leitura do cache do disco de dados Premium | Cache do disco de dados Premium-erro de leitura (visualização) | Porcentagem | Média
Sim | Microsoft.Compute/virtualMachines | Cache de disco do sistema operacional Premium-acesso de leitura | Cache de disco do sistema operacional Premium-acesso de leitura (versão prévia) | Porcentagem | Média
Sim | Microsoft.Compute/virtualMachines | Erro de leitura do cache de disco do so premium | Erro de leitura do cache de disco do so premium (visualização) | Porcentagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Créditos de CPU Consumidos | Créditos de CPU Consumidos | Contagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Créditos de CPU Restantes | Créditos de CPU Restantes | Contagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Profundidade da Fila de Disco de Dados | Profundidade da fila do disco de dados (visualização) | Contagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Bytes de leitura do disco de dados/s | Bytes de Leitura do Disco de Dados/s (Visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Operações de leitura de disco de dados/s | Operações de Leitura do Disco de Dados/s (Visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Bytes de gravação do disco de dados/s | Bytes de Gravação do Disco de Dados/s (Visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Operações de gravação do disco de dados/s | Operações de Gravação do Disco de Dados/s (Visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Bytes de leitura de disco | Bytes de leitura de disco | Bytes | Total
Sim | Microsoft.Compute/virtualMachineScaleSets | Operações de leitura de disco/Seg | Operações de leitura de disco/Seg | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Bytes de gravação de disco | Bytes de gravação de disco | Bytes | Total
Sim | Microsoft.Compute/virtualMachineScaleSets | Operações de gravação de disco/Seg | Operações de gravação de disco/Seg | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Fluxos de Entrada | Fluxos de Entrada | Contagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Taxa máxima de criação de fluxos de entrada | Taxa máxima de criação de fluxos de entrada (visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Entrada na rede | Rede em faturável (preterida) | Bytes | Total
Sim | Microsoft.Compute/virtualMachineScaleSets | Total de rede | Total de rede | Bytes | Total
Sim | Microsoft.Compute/virtualMachineScaleSets | Saída da rede | Saída de rede faturável (preterida) | Bytes | Total
Sim | Microsoft.Compute/virtualMachineScaleSets | Total de saída de rede | Total de saída de rede | Bytes | Total
Sim | Microsoft.Compute/virtualMachineScaleSets | Profundidade da Fila de Disco do SO | Profundidade da fila de disco do so (visualização) | Contagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Bytes de leitura do disco do so/s | Bytes de Leitura do Disco do SO/s (Visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Operações de leitura de disco do so/s | Operações de Leitura do Disco do SO/s (Visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Bytes de gravação de disco do sistema operacional/s | Bytes de Gravação do Disco do SO/s (Visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Operações de gravação de disco do so/s | Operações de Gravação do Disco do SO/s (Visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Os Por QD de Disco | Disco do so QD (preterido) | Contagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | SO Por Bytes de Leitura do Disco/s | Bytes de leitura do disco do so/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | SO Por Operações de Leitura do Disco/s | Operações de leitura de disco do so/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | SO Por Bytes de Gravação do Disco/s | Bytes de gravação de disco do sistema operacional/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | SO Por Operações de Gravação do Disco/s | Operações de gravação de disco do sistema operacional/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Fluxos de saída | Fluxos de saída | Contagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Taxa máxima de criação de fluxos de saída | Taxa máxima de criação de fluxos de saída (versão prévia) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Por QD de Disco | Disco de dados QD (preterido) | Contagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Por Bytes de Leitura do Disco/s | Bytes de leitura do disco de dados/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Por operações de Leitura de Disco/s | Operações de leitura de disco de dados/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Por Bytes de Gravação de Disco/s | Bytes de gravação do disco de dados/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Por Operações de Gravação de Disco/s | Operações de gravação do disco de dados/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Porcentagem de CPU | Porcentagem de CPU | Porcentagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Cache do disco de dados Premium-acesso de leitura | Leitura do cache do disco de dados Premium (versão prévia) | Porcentagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Erro de leitura do cache do disco de dados Premium | Cache do disco de dados Premium-erro de leitura (visualização) | Porcentagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Cache de disco do sistema operacional Premium-acesso de leitura | Cache de disco do sistema operacional Premium-acesso de leitura (versão prévia) | Porcentagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets | Erro de leitura do cache de disco do so premium | Erro de leitura do cache de disco do so premium (visualização) | Porcentagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Créditos de CPU Consumidos | Créditos de CPU Consumidos | Contagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Créditos de CPU Restantes | Créditos de CPU Restantes | Contagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Profundidade da Fila de Disco de Dados | Profundidade da fila do disco de dados (visualização) | Contagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Bytes de leitura do disco de dados/s | Bytes de Leitura do Disco de Dados/s (Visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Operações de leitura de disco de dados/s | Operações de Leitura do Disco de Dados/s (Visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Bytes de gravação do disco de dados/s | Bytes de Gravação do Disco de Dados/s (Visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Operações de gravação do disco de dados/s | Operações de Gravação do Disco de Dados/s (Visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Bytes de leitura de disco | Bytes de leitura de disco | Bytes | Total
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Operações de leitura de disco/Seg | Operações de leitura de disco/Seg | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Bytes de gravação de disco | Bytes de gravação de disco | Bytes | Total
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Operações de gravação de disco/Seg | Operações de gravação de disco/Seg | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Fluxos de Entrada | Fluxos de Entrada | Contagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Taxa máxima de criação de fluxos de entrada | Taxa máxima de criação de fluxos de entrada (visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Entrada na rede | Rede em faturável (preterida) | Bytes | Total
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Total de rede | Total de rede | Bytes | Total
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Saída da rede | Saída de rede faturável (preterida) | Bytes | Total
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Total de saída de rede | Total de saída de rede | Bytes | Total
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Profundidade da Fila de Disco do SO | Profundidade da fila de disco do so (visualização) | Contagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Bytes de leitura do disco do so/s | Bytes de Leitura do Disco do SO/s (Visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Operações de leitura de disco do so/s | Operações de Leitura do Disco do SO/s (Visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Bytes de gravação de disco do sistema operacional/s | Bytes de Gravação do Disco do SO/s (Visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Operações de gravação de disco do so/s | Operações de Gravação do Disco do SO/s (Visualização) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Os Por QD de Disco | Disco do so QD (preterido) | Contagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | SO Por Bytes de Leitura do Disco/s | Bytes de leitura do disco do so/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | SO Por Operações de Leitura do Disco/s | Operações de leitura de disco do so/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | SO Por Bytes de Gravação do Disco/s | Bytes de gravação de disco do sistema operacional/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | SO Por Operações de Gravação do Disco/s | Operações de gravação de disco do sistema operacional/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Fluxos de saída | Fluxos de saída | Contagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Taxa máxima de criação de fluxos de saída | Taxa máxima de criação de fluxos de saída (versão prévia) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Por QD de Disco | Disco de dados QD (preterido) | Contagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Por Bytes de Leitura do Disco/s | Bytes de leitura do disco de dados/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Por operações de Leitura de Disco/s | Operações de leitura de disco de dados/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Por Bytes de Gravação de Disco/s | Bytes de gravação do disco de dados/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Por Operações de Gravação de Disco/s | Operações de gravação do disco de dados/s (preterido) | CountPerSecond | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Porcentagem de CPU | Porcentagem de CPU | Porcentagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Cache do disco de dados Premium-acesso de leitura | Leitura do cache do disco de dados Premium (versão prévia) | Porcentagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Erro de leitura do cache do disco de dados Premium | Cache do disco de dados Premium-erro de leitura (visualização) | Porcentagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Cache de disco do sistema operacional Premium-acesso de leitura | Cache de disco do sistema operacional Premium-acesso de leitura (versão prévia) | Porcentagem | Média
Sim | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Erro de leitura do cache de disco do so premium | Erro de leitura do cache de disco do so premium (visualização) | Porcentagem | Média
Sim | Microsoft.ContainerInstance/containerGroups | CpuUsage | Uso da CPU | Contagem | Média
Sim | Microsoft.ContainerInstance/containerGroups | MemoryUsage | Uso de Memória | Bytes | Média
Sim | Microsoft.ContainerInstance/containerGroups | NetworkBytesReceivedPerSecond | Bytes de Rede Recebidos por Segundo | Bytes | Média
Sim | Microsoft.ContainerInstance/containerGroups | NetworkBytesTransmittedPerSecond | Bytes de Rede Transmitidos por Segundo | Bytes | Média
Sim | Microsoft.ContainerRegistry/registries | RunDuration | Duração da execução | Milissegundos | Total
Sim | Microsoft.ContainerRegistry/registries | SuccessfulPullCount | Contagem de pull bem-sucedida | Contagem | Média
Sim | Microsoft.ContainerRegistry/registries | SuccessfulPushCount | Contagem de push bem-sucedida | Contagem | Média
Sim | Microsoft.ContainerRegistry/registries | TotalPullCount | Contagem total de pull | Contagem | Média
Sim | Microsoft.ContainerRegistry/registries | TotalPushCount | Contagem total de push | Contagem | Média
Não | Microsoft.ContainerService/managedClusters | kube_node_status_allocatable_cpu_cores | Número total de núcleos de cpu disponíveis em um cluster gerenciado | Contagem | Média
Não | Microsoft.ContainerService/managedClusters | kube_node_status_allocatable_memory_bytes | Número total de memória disponível em um cluster gerenciado | Bytes | Média
Não | Microsoft.ContainerService/managedClusters | kube_node_status_condition | Status para as várias condições de nó | Contagem | Média
Não | Microsoft.ContainerService/managedClusters | kube_pod_status_phase | Número de pods por fase | Contagem | Média
Não | Microsoft.ContainerService/managedClusters | kube_pod_status_ready | Número de compartimentos no estado Pronto | Contagem | Média
Sim | Microsoft.DataBoxEdge/dataBoxEdgeDevices | AvailableCapacity | Capacidade Disponível | Bytes | Média
Sim | Microsoft.DataBoxEdge/dataBoxEdgeDevices | BytesUploadedToCloud | Bytes de nuvem carregados (dispositivo) | Bytes | Média
Sim | Microsoft.DataBoxEdge/dataBoxEdgeDevices | BytesUploadedToCloudPerShare | Bytes de nuvem carregados (compartilhamento) | Bytes | Média
Sim | Microsoft.DataBoxEdge/dataBoxEdgeDevices | CloudReadThroughput | Taxa de transferência de download da nuvem | BytesPerSecond | Média
Sim | Microsoft.DataBoxEdge/dataBoxEdgeDevices | CloudReadThroughputPerShare | Taxa de transferência de download de nuvem (compartilhamento) | BytesPerSecond | Média
Sim | Microsoft.DataBoxEdge/dataBoxEdgeDevices | CloudUploadThroughput | Taxa de transferência de upload de nuvem | BytesPerSecond | Média
Sim | Microsoft.DataBoxEdge/dataBoxEdgeDevices | CloudUploadThroughputPerShare | Taxa de transferência de upload de nuvem (compartilhamento) | BytesPerSecond | Média
Sim | Microsoft.DataBoxEdge/dataBoxEdgeDevices | HyperVMemoryUtilization | Computação de borda-uso de memória | Porcentagem | Média
Sim | Microsoft.DataBoxEdge/dataBoxEdgeDevices | HyperVVirtualProcessorUtilization | Computação de borda – percentual de CPU | Porcentagem | Média
Sim | Microsoft.DataBoxEdge/dataBoxEdgeDevices | NICReadThroughput | Taxa de transferência de leitura (rede) | BytesPerSecond | Média
Sim | Microsoft.DataBoxEdge/dataBoxEdgeDevices | NICWriteThroughput | Taxa de transferência de gravação (rede) | BytesPerSecond | Média
Sim | Microsoft.DataBoxEdge/dataBoxEdgeDevices | TotalCapacity | Capacidade total | Bytes | Média
Sim | Microsoft.DataFactory/datafactories | FailedRuns | Execuções com falha | Contagem | Total
Sim | Microsoft.DataFactory/datafactories | SuccessfulRuns | Execuções com êxito | Contagem | Total
Sim | Microsoft.DataFactory/factories | ActivityCancelledRuns | Métricas de execuções de atividade canceladas | Contagem | Total
Sim | Microsoft.DataFactory/factories | ActivityFailedRuns | Métricas de execução de atividades com falha | Contagem | Total
Sim | Microsoft.DataFactory/factories | ActivitySucceededRuns | Métricas de execução de atividades bem-sucedidas | Contagem | Total
Sim | Microsoft.DataFactory/factories | FactorySizeInGbUnits | Tamanho total de fábrica (unidade GB) | Contagem | Máximo
Sim | Microsoft.DataFactory/factories | IntegrationRuntimeAvailableMemory | Memória disponível de runtime de integração | Bytes | Média
Sim | Microsoft.DataFactory/factories | IntegrationRuntimeAverageTaskPickupDelay | Duração da fila do Integration Runtime | Segundos | Média
Sim | Microsoft.DataFactory/factories | IntegrationRuntimeCpuPercentage | Utilização de CPU de runtime de integração | Porcentagem | Média
Sim | Microsoft.DataFactory/factories | IntegrationRuntimeQueueLength | Comprimento da fila do Integration Runtime | Contagem | Média
Sim | Microsoft.DataFactory/factories | MaxAllowedFactorySizeInGbUnits | Tamanho máximo de fábrica permitido (unidade GB) | Contagem | Máximo
Sim | Microsoft.DataFactory/factories | MaxAllowedResourceCount | Contagem máxima de entidades permitidas | Contagem | Máximo
Sim | Microsoft.DataFactory/factories | PipelineCancelledRuns | Métricas de execuções de pipeline canceladas | Contagem | Total
Sim | Microsoft.DataFactory/factories | PipelineFailedRuns | Métricas de execução do pipeline com falha | Contagem | Total
Sim | Microsoft.DataFactory/factories | PipelineSucceededRuns | Métricas de execução do pipeline bem-sucedido | Contagem | Total
Sim | Microsoft.DataFactory/factories | ResourceCount | Contagem total de entidades | Contagem | Máximo
Sim | Microsoft.DataFactory/factories | TriggerCancelledRuns | O gatilho cancelado executa métricas | Contagem | Total
Sim | Microsoft.DataFactory/factories | TriggerFailedRuns | Métricas de execuções do gatilho com falha | Contagem | Total
Sim | Microsoft.DataFactory/factories | TriggerSucceededRuns | Métricas de execuções do gatilho bem-sucedidas | Contagem | Total
Sim | Microsoft.DataLakeAnalytics/accounts | JobAUEndedCancelled | Tempo de AU cancelada | Segundos | Total
Sim | Microsoft.DataLakeAnalytics/accounts | JobAUEndedFailure | Tempo de AU com falha | Segundos | Total
Sim | Microsoft.DataLakeAnalytics/accounts | JobAUEndedSuccess | Tempo de AU com êxito | Segundos | Total
Sim | Microsoft.DataLakeAnalytics/accounts | JobEndedCancelled | Trabalhos cancelados | Contagem | Total
Sim | Microsoft.DataLakeAnalytics/accounts | JobEndedFailure | Trabalhos com falha | Contagem | Total
Sim | Microsoft.DataLakeAnalytics/accounts | JobEndedSuccess | Trabalhos com êxito | Contagem | Total
Sim | Microsoft.DataLakeStore/accounts | DataRead | Leitura de dados | Bytes | Total
Sim | Microsoft.DataLakeStore/accounts | DataWritten | Dados gravados | Bytes | Total
Sim | Microsoft.DataLakeStore/accounts | ReadRequests | Solicitações de leitura | Contagem | Total
Sim | Microsoft.DataLakeStore/accounts | TotalStorage | Armazenamento total | Bytes | Máximo
Sim | Microsoft.DataLakeStore/accounts | WriteRequests | Solicitações de gravação | Contagem | Total
Sim | Microsoft.DBforMariaDB/servers | active_connections | Conexões ativas | Contagem | Média
Sim | Microsoft.DBforMariaDB/servers | backup_storage_used | Armazenamento de backup usado | Bytes | Média
Sim | Microsoft.DBforMariaDB/servers | connections_failed | Conexões com falha | Contagem | Total
Sim | Microsoft.DBforMariaDB/servers | cpu_percent | Porcentagem de CPU | Porcentagem | Média
Sim | Microsoft.DBforMariaDB/servers | io_consumption_percent | Porcentagem de E/S | Porcentagem | Média
Sim | Microsoft.DBforMariaDB/servers | memory_percent | Porcentagem de memória | Porcentagem | Média
Sim | Microsoft.DBforMariaDB/servers | network_bytes_egress | Saída da rede | Bytes | Total
Sim | Microsoft.DBforMariaDB/servers | network_bytes_ingress | Entrada na rede | Bytes | Total
Sim | Microsoft.DBforMariaDB/servers | seconds_behind_master | Retardo de replicação em segundos | Contagem | Máximo
Sim | Microsoft.DBforMariaDB/servers | serverlog_storage_limit | Limite de armazenamento do Log do Servidor | Bytes | Média
Sim | Microsoft.DBforMariaDB/servers | serverlog_storage_percent | Porcentagem de armazenamento do Log do Servidor | Porcentagem | Média
Sim | Microsoft.DBforMariaDB/servers | serverlog_storage_usage | Armazenamento do Log do Servidor usado | Bytes | Média
Sim | Microsoft.DBforMariaDB/servers | storage_limit | Limite de armazenamento | Bytes | Máximo
Sim | Microsoft.DBforMariaDB/servers | storage_percent | Porcentagem de armazenamento | Porcentagem | Média
Sim | Microsoft.DBforMariaDB/servers | storage_used | Armazenamento usado | Bytes | Média
Sim | Microsoft.DBforMySQL/servers | active_connections | Conexões ativas | Contagem | Média
Sim | Microsoft.DBforMySQL/servers | backup_storage_used | Armazenamento de backup usado | Bytes | Média
Sim | Microsoft.DBforMySQL/servers | connections_failed | Conexões com falha | Contagem | Total
Sim | Microsoft.DBforMySQL/servers | cpu_percent | Porcentagem de CPU | Porcentagem | Média
Sim | Microsoft.DBforMySQL/servers | io_consumption_percent | Porcentagem de E/S | Porcentagem | Média
Sim | Microsoft.DBforMySQL/servers | memory_percent | Porcentagem de memória | Porcentagem | Média
Sim | Microsoft.DBforMySQL/servers | network_bytes_egress | Saída da rede | Bytes | Total
Sim | Microsoft.DBforMySQL/servers | network_bytes_ingress | Entrada na rede | Bytes | Total
Sim | Microsoft.DBforMySQL/servers | seconds_behind_master | Retardo de replicação em segundos | Contagem | Máximo
Sim | Microsoft.DBforMySQL/servers | serverlog_storage_limit | Limite de armazenamento do Log do Servidor | Bytes | Máximo
Sim | Microsoft.DBforMySQL/servers | serverlog_storage_percent | Porcentagem de armazenamento do Log do Servidor | Porcentagem | Média
Sim | Microsoft.DBforMySQL/servers | serverlog_storage_usage | Armazenamento do Log do Servidor usado | Bytes | Média
Sim | Microsoft.DBforMySQL/servers | storage_limit | Limite de armazenamento | Bytes | Máximo
Sim | Microsoft.DBforMySQL/servers | storage_percent | Porcentagem de armazenamento | Porcentagem | Média
Sim | Microsoft.DBforMySQL/servers | storage_used | Armazenamento usado | Bytes | Média
Sim | Microsoft.DBforPostgreSQL/servers | active_connections | Conexões ativas | Contagem | Média
Sim | Microsoft.DBforPostgreSQL/servers | backup_storage_used | Armazenamento de backup usado | Bytes | Média
Sim | Microsoft.DBforPostgreSQL/servers | connections_failed | Conexões com falha | Contagem | Total
Sim | Microsoft.DBforPostgreSQL/servers | cpu_percent | Porcentagem de CPU | Porcentagem | Média
Sim | Microsoft.DBforPostgreSQL/servers | io_consumption_percent | Porcentagem de E/S | Porcentagem | Média
Sim | Microsoft.DBforPostgreSQL/servers | memory_percent | Porcentagem de memória | Porcentagem | Média
Sim | Microsoft.DBforPostgreSQL/servers | network_bytes_egress | Saída da rede | Bytes | Total
Sim | Microsoft.DBforPostgreSQL/servers | network_bytes_ingress | Entrada na rede | Bytes | Total
Sim | Microsoft.DBforPostgreSQL/servers | pg_replica_log_delay_in_bytes | Retardo Máximo entre Réplicas | Bytes | Máximo
Sim | Microsoft.DBforPostgreSQL/servers | pg_replica_log_delay_in_seconds | Retardo da Réplica | Segundos | Máximo
Sim | Microsoft.DBforPostgreSQL/servers | serverlog_storage_limit | Limite de armazenamento do Log do Servidor | Bytes | Máximo
Sim | Microsoft.DBforPostgreSQL/servers | serverlog_storage_percent | Porcentagem de armazenamento do Log do Servidor | Porcentagem | Média
Sim | Microsoft.DBforPostgreSQL/servers | serverlog_storage_usage | Armazenamento do Log do Servidor usado | Bytes | Média
Sim | Microsoft.DBforPostgreSQL/servers | storage_limit | Limite de armazenamento | Bytes | Máximo
Sim | Microsoft.DBforPostgreSQL/servers | storage_percent | Porcentagem de armazenamento | Porcentagem | Média
Sim | Microsoft.DBforPostgreSQL/servers | storage_used | Armazenamento usado | Bytes | Média
Sim | Microsoft.DBforPostgreSQL/serversv2 | active_connections | Conexões ativas | Contagem | Média
Sim | Microsoft.DBforPostgreSQL/serversv2 | cpu_percent | Porcentagem de CPU | Porcentagem | Média
Sim | Microsoft.DBforPostgreSQL/serversv2 | IOPS | IOPS | Contagem | Média
Sim | Microsoft.DBforPostgreSQL/serversv2 | memory_percent | Porcentagem de memória | Porcentagem | Média
Sim | Microsoft.DBforPostgreSQL/serversv2 | network_bytes_egress | Saída da rede | Bytes | Total
Sim | Microsoft.DBforPostgreSQL/serversv2 | network_bytes_ingress | Entrada na rede | Bytes | Total
Sim | Microsoft.DBforPostgreSQL/serversv2 | storage_percent | Porcentagem de armazenamento | Porcentagem | Média
Sim | Microsoft.DBforPostgreSQL/serversv2 | storage_used | Armazenamento usado | Bytes | Média
Sim | Microsoft. Devices/conta | digitaltwins. telemetria. nós | Espaço reservado para telemetria do nó digital gêmeos | Contagem | Total
Sim | Microsoft.Devices/IotHubs | c2d.commands.egress.abandon.success | C2D mensagens abandonadas | Contagem | Total
Sim | Microsoft.Devices/IotHubs | c2d.commands.egress.complete.success | Entregas de mensagem C2D concluídas | Contagem | Total
Sim | Microsoft.Devices/IotHubs | c2d.commands.egress.reject.success | Mensagens C2D rejeitadas | Contagem | Total
Sim | Microsoft.Devices/IotHubs | c2d.methods.failure | Invocações de método diretas com falhas | Contagem | Total
Sim | Microsoft.Devices/IotHubs | c2d.methods.requestSize | Tamanho da solicitação das invocações de método diretas | Bytes | Média
Sim | Microsoft.Devices/IotHubs | c2d.methods.responseSize | Tamanho da resposta das invocações de método diretas | Bytes | Média
Sim | Microsoft.Devices/IotHubs | c2d.methods.success | Invocações de método diretas bem-sucedidas | Contagem | Total
Sim | Microsoft.Devices/IotHubs | c2d.twin.read.failure | Leituras de gêmeos de back-end com falhas | Contagem | Total
Sim | Microsoft.Devices/IotHubs | c2d.twin.read.size | Tamanho da resposta das leituras de gêmeos de back-end | Bytes | Média
Sim | Microsoft.Devices/IotHubs | c2d.twin.read.success | Leituras de gêmeos de back-end bem-sucedidas | Contagem | Total
Sim | Microsoft.Devices/IotHubs | c2d.twin.update.failure | Atualizações de gêmeos de back-end com falhas | Contagem | Total
Sim | Microsoft.Devices/IotHubs | c2d.twin.update.size | Tamanho das atualizações de gêmeos de back-end | Bytes | Média
Sim | Microsoft.Devices/IotHubs | c2d.twin.update.success | Atualizações de gêmeos de back-end bem-sucedidas | Contagem | Total
Sim | Microsoft.Devices/IotHubs | C2DMessagesExpired | C2D mensagens expiradas (visualização) | Contagem | Total
Sim | Microsoft.Devices/IotHubs | configurações | Métricas de configuração | Contagem | Total
Não | Microsoft.Devices/IotHubs | connectedDeviceCount | Dispositivos conectados (versão prévia) | Contagem | Média
Sim | Microsoft.Devices/IotHubs | d2c.endpoints.egress.builtIn.events | Roteamento: mensagens entregues a mensagens/eventos | Contagem | Total
Sim | Microsoft.Devices/IotHubs | d2c.endpoints.egress.eventHubs | Roteamento: mensagens entregues ao Hub de Eventos | Contagem | Total
Sim | Microsoft.Devices/IotHubs | d2c.endpoints.egress.serviceBusQueues | Roteamento: mensagens entregues à Fila do Barramento de Serviço | Contagem | Total
Sim | Microsoft.Devices/IotHubs | d2c.endpoints.egress.serviceBusTopics | Roteamento: mensagens entregues ao Tópico do Barramento de Serviço | Contagem | Total
Sim | Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage | Roteamento: mensagens entregues ao armazenamento | Contagem | Total
Sim | Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage.blobs | Roteamento: blobs entregues ao armazenamento | Contagem | Total
Sim | Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage.bytes | Roteamento: dados entregues ao armazenamento | Bytes | Total
Sim | Microsoft.Devices/IotHubs | d2c.Endpoints.Latency.builtIn.Events | Roteamento: latência de mensagem para mensagens/eventos | Milissegundos | Média
Sim | Microsoft.Devices/IotHubs | d2c.endpoints.latency.eventHubs | Roteamento: latência de mensagem para o Hub de Eventos | Milissegundos | Média
Sim | Microsoft.Devices/IotHubs | d2c.endpoints.latency.serviceBusQueues | Roteamento: latência de mensagem para a Fila do Barramento de Serviço | Milissegundos | Média
Sim | Microsoft.Devices/IotHubs | d2c.endpoints.latency.serviceBusTopics | Roteamento: latência de mensagem para o Tópico do Barramento de Serviço | Milissegundos | Média
Sim | Microsoft.Devices/IotHubs | d2c.endpoints.latency.storage | Roteamento: latência de mensagem para armazenamento | Milissegundos | Média
Sim | Microsoft.Devices/IotHubs | d2c.telemetry.egress.dropped | Roteamento: mensagens de telemetria eliminadas | Contagem | Total
Sim | Microsoft.Devices/IotHubs | d2c.telemetry.egress.fallback | Roteamento: mensagens entregues ao fallback | Contagem | Total
Sim | Microsoft.Devices/IotHubs | d2c.telemetry.egress.invalid | Roteamento: mensagens de telemetria incompatíveis | Contagem | Total
Sim | Microsoft.Devices/IotHubs | d2c.telemetry.egress.orphaned | Roteamento: mensagens de telemetria órfãs | Contagem | Total
Sim | Microsoft.Devices/IotHubs | d2c.telemetry.egress.success | Roteamento: mensagens de telemetria entregues | Contagem | Total
Sim | Microsoft.Devices/IotHubs | d2c.telemetry.ingress.allProtocol | Tentativas de envio de mensagem de telemetria | Contagem | Total
Sim | Microsoft.Devices/IotHubs | d2c.telemetry.ingress.sendThrottle | Número de erros de limitação | Contagem | Total
Sim | Microsoft.Devices/IotHubs | d2c.telemetry.ingress.success | Mensagens de telemetria enviadas | Contagem | Total
Sim | Microsoft.Devices/IotHubs | d2c.twin.read.failure | Leituras de gêmeos dos dispositivos com falhas | Contagem | Total
Sim | Microsoft.Devices/IotHubs | d2c.twin.read.size | Tamanho da resposta das leituras de gêmeos dos dispositivos | Bytes | Média
Sim | Microsoft.Devices/IotHubs | d2c.twin.read.success | Leituras de gêmeos dos dispositivos bem-sucedidas | Contagem | Total
Sim | Microsoft.Devices/IotHubs | d2c.twin.update.failure | Atualizações de gêmeos dos dispositivos com falhas | Contagem | Total
Sim | Microsoft.Devices/IotHubs | d2c.twin.update.size | Tamanho das atualizações de gêmeos dos dispositivos | Bytes | Média
Sim | Microsoft.Devices/IotHubs | d2c.twin.update.success | Atualizações de gêmeos dos dispositivos bem-sucedidas | Contagem | Total
Sim | Microsoft.Devices/IotHubs | dailyMessageQuotaUsed | Número total de mensagens usadas | Contagem | Média
Sim | Microsoft.Devices/IotHubs | deviceDataUsage | Uso total de dados do dispositivo | Bytes | Total
Sim | Microsoft.Devices/IotHubs | deviceDataUsageV2 | Uso total de dados de dispositivos (versão prévia) | Bytes | Total
Sim | Microsoft.Devices/IotHubs | devices.connectedDevices.allProtocol | Dispositivos conectados (preteridos)  | Contagem | Total
Sim | Microsoft.Devices/IotHubs | devices.totalDevices | Total de dispositivos (preterido) | Contagem | Total
Sim | Microsoft.Devices/IotHubs | EventGridDeliveries | Entregas da grade de eventos (visualização) | Contagem | Total
Sim | Microsoft.Devices/IotHubs | EventGridLatency | Latência da grade de eventos (versão prévia) | Milissegundos | Média
Sim | Microsoft.Devices/IotHubs | jobs.cancelJob.failure | Cancelamentos de trabalho com falhas | Contagem | Total
Sim | Microsoft.Devices/IotHubs | jobs.cancelJob.success | Cancelamentos de trabalho bem-sucedidos | Contagem | Total
Sim | Microsoft.Devices/IotHubs | jobs.completed | Trabalhos concluídos | Contagem | Total
Sim | Microsoft.Devices/IotHubs | jobs.createDirectMethodJob.failure | Criações de trabalhos de invocação de método com falhas | Contagem | Total
Sim | Microsoft.Devices/IotHubs | jobs.createDirectMethodJob.success | Criações de trabalhos de invocação de método bem-sucedidas | Contagem | Total
Sim | Microsoft.Devices/IotHubs | jobs.createTwinUpdateJob.failure | Criações de trabalhos de atualização de gêmeos com falhas | Contagem | Total
Sim | Microsoft.Devices/IotHubs | jobs.createTwinUpdateJob.success | Criações de trabalhos de atualização de gêmeos bem-sucedidas | Contagem | Total
Sim | Microsoft.Devices/IotHubs | jobs.failed | Trabalhos com falha | Contagem | Total
Sim | Microsoft.Devices/IotHubs | jobs.listJobs.failure | Chamadas para listar trabalhos com falhas | Contagem | Total
Sim | Microsoft.Devices/IotHubs | jobs.listJobs.success | Chamadas para listar trabalhos bem-sucedidas | Contagem | Total
Sim | Microsoft.Devices/IotHubs | jobs.queryJobs.failure | Consultas de trabalho com falhas | Contagem | Total
Sim | Microsoft.Devices/IotHubs | jobs.queryJobs.success | Consultas de trabalho bem-sucedidas | Contagem | Total
Não | Microsoft.Devices/IotHubs | totalDeviceCount | Total de dispositivos (versão prévia) | Contagem | Média
Sim | Microsoft.Devices/IotHubs | twinQueries.failure | Consultas de gêmeos com falhas | Contagem | Total
Sim | Microsoft.Devices/IotHubs | twinQueries.resultSize | Tamanho do resultado das consultas de gêmeos | Bytes | Média
Sim | Microsoft.Devices/IotHubs | twinQueries.success | Consultas de gêmeos bem-sucedidas | Contagem | Total
Sim | Microsoft.Devices/provisioningServices | AttestationAttempts | Tentativas de atestado | Contagem | Total
Sim | Microsoft.Devices/provisioningServices | DeviceAssignments | Dispositivos atribuídos | Contagem | Total
Sim | Microsoft.Devices/provisioningServices | RegistrationAttempts | Tentativas de Registro | Contagem | Total
Não | Microsoft.DocumentDB/databaseAccounts | AvailableStorage | Armazenamento disponível | Bytes | Total
Não | Microsoft.DocumentDB/databaseAccounts | CassandraConnectionClosures | Fechamentos de Conexão do Cassandra | Contagem | Total
Não | Microsoft.DocumentDB/databaseAccounts | CassandraRequestCharges | Encargos de Solicitação do Cassandra | Contagem | Total
Não | Microsoft.DocumentDB/databaseAccounts | CassandraRequests | Solicitações do Cassandra | Contagem | Contagem
Não | Microsoft.DocumentDB/databaseAccounts | DataUsage | Uso de dados | Bytes | Total
Sim | Microsoft.DocumentDB/databaseAccounts | DeleteVirtualNetwork | DeleteVirtualNetwork | Contagem | Contagem
Não | Microsoft.DocumentDB/databaseAccounts | DocumentCount | Contagem de documentos | Contagem | Total
Não | Microsoft.DocumentDB/databaseAccounts | DocumentQuota | Cota de documento | Bytes | Total
Não | Microsoft.DocumentDB/databaseAccounts | IndexUsage | Uso do índice | Bytes | Total
Não | Microsoft.DocumentDB/databaseAccounts | MetadataRequests | MetadataRequests | Contagem | Contagem
Sim | Microsoft.DocumentDB/databaseAccounts | MongoRequestCharge | MongoRequestCharge | Contagem | Total
Sim | Microsoft.DocumentDB/databaseAccounts | MongoRequests | Solicitações do Mongo | Contagem | Contagem
Não | Microsoft.DocumentDB/databaseAccounts | MongoRequestsCount | Taxa de solicitação de Mongo | CountPerSecond | Média
Não | Microsoft.DocumentDB/databaseAccounts | MongoRequestsDelete | Taxa de solicitação de exclusão de Mongo | CountPerSecond | Média
Não | Microsoft.DocumentDB/databaseAccounts | MongoRequestsInsert | Taxa de solicitação de inserção de Mongo | CountPerSecond | Média
Não | Microsoft.DocumentDB/databaseAccounts | MongoRequestsQuery | Taxa de solicitação de consulta do Mongo | CountPerSecond | Média
Não | Microsoft.DocumentDB/databaseAccounts | MongoRequestsUpdate | Taxa de solicitação de atualização do Mongo | CountPerSecond | Média
Não | Microsoft.DocumentDB/databaseAccounts | ProvisionedThroughput | Taxa de transferência provisionada | Contagem | Máximo
Sim | Microsoft.DocumentDB/databaseAccounts | ReplicationLatency | Latência de replicação P99 | MilliSeconds | Média
Não | Microsoft.DocumentDB/databaseAccounts | ServiceAvailability | Disponibilidade do serviço | Porcentagem | Média
Sim | Microsoft.DocumentDB/databaseAccounts | TotalRequests | Total de Solicitações | Contagem | Contagem
Sim | Microsoft.DocumentDB/databaseAccounts | TotalRequestUnits | Total de Unidades Solicitadas | Contagem | Total
Não | Microsoft. EnterpriseKnowledgeGraph/serviços | FailureCount | Contagem de Falha | Contagem | Contagem
Não | Microsoft. EnterpriseKnowledgeGraph/serviços | SuccessCount | Contagem Bem-sucedida | Contagem | Contagem
Não | Microsoft. EnterpriseKnowledgeGraph/serviços | SuccessLatency | Latência de êxito | MilliSeconds | Média
Não | Microsoft. EnterpriseKnowledgeGraph/serviços | TransactionCount | Contagem de transações | Contagem | Contagem
Sim | Microsoft.EventGrid/domains | DeadLetteredCount | Eventos de mensagens mortas | Contagem | Total
Não | Microsoft.EventGrid/domains | DeliveryAttemptFailCount | Eventos com falha de entrega | Contagem | Total
Sim | Microsoft.EventGrid/domains | DeliverySuccessCount | Eventos entregues | Contagem | Total
Não | Microsoft.EventGrid/domains | DestinationProcessingDurationInMs | Duração do processamento de destino | Milissegundos | Média
Sim | Microsoft.EventGrid/domains | DroppedEventCount | Eventos removidos | Contagem | Total
Sim | Microsoft.EventGrid/domains | MatchedEventCount | Eventos correspondentes | Contagem | Total
Sim | Microsoft.EventGrid/domains | PublishFailCount | Publicar eventos com falha | Contagem | Total
Sim | Microsoft.EventGrid/domains | PublishSuccessCount | Eventos publicados | Contagem | Total
Sim | Microsoft.EventGrid/domains | PublishSuccessLatencyInMs | Latência de êxito na publicação | Contagem | Total
Sim | Microsoft.EventGrid/eventSubscriptions | DeadLetteredCount | Eventos de mensagens mortas | Contagem | Total
Não | Microsoft.EventGrid/eventSubscriptions | DeliveryAttemptFailCount | Eventos com falha de entrega | Contagem | Total
Sim | Microsoft.EventGrid/eventSubscriptions | DeliverySuccessCount | Eventos entregues | Contagem | Total
Não | Microsoft.EventGrid/eventSubscriptions | DestinationProcessingDurationInMs | Duração do processamento de destino | Milissegundos | Média
Sim | Microsoft.EventGrid/eventSubscriptions | DroppedEventCount | Eventos removidos | Contagem | Total
Sim | Microsoft.EventGrid/eventSubscriptions | MatchedEventCount | Eventos correspondentes | Contagem | Total
Sim | Microsoft.EventGrid/extensionTopics | PublishFailCount | Publicar eventos com falha | Contagem | Total
Sim | Microsoft.EventGrid/extensionTopics | PublishSuccessCount | Eventos publicados | Contagem | Total
Sim | Microsoft.EventGrid/extensionTopics | PublishSuccessLatencyInMs | Latência de êxito na publicação | Contagem | Total
Sim | Microsoft.EventGrid/extensionTopics | UnmatchedEventCount | Eventos sem correspondência | Contagem | Total
Sim | Microsoft.EventGrid/topics | PublishFailCount | Publicar eventos com falha | Contagem | Total
Sim | Microsoft.EventGrid/topics | PublishSuccessCount | Eventos publicados | Contagem | Total
Sim | Microsoft.EventGrid/topics | PublishSuccessLatencyInMs | Latência de êxito na publicação | Contagem | Total
Sim | Microsoft.EventGrid/topics | UnmatchedEventCount | Eventos sem correspondência | Contagem | Total
Não | Microsoft.EventHub/clusters | ActiveConnections | ActiveConnections | Contagem | Média
Não | Microsoft.EventHub/clusters | AvailableMemory | Memória disponível | Porcentagem | Máximo
Não | Microsoft.EventHub/clusters | CaptureBacklog | Lista de Pendências de Captura. | Contagem | Total
Não | Microsoft.EventHub/clusters | CapturedBytes | Bytes Capturados. | Bytes | Total
Não | Microsoft.EventHub/clusters | CapturedMessages | Mensagens Capturadas. | Contagem | Total
Não | Microsoft.EventHub/clusters | ConnectionsClosed | Conexões Fechadas. | Contagem | Média
Não | Microsoft.EventHub/clusters | ConnectionsOpened | Conexões Abertas. | Contagem | Média
Não | Microsoft.EventHub/clusters | CPU | CPU | Porcentagem | Máximo
Sim | Microsoft.EventHub/clusters | IncomingBytes | Bytes de Entrada. | Bytes | Total
Sim | Microsoft.EventHub/clusters | IncomingMessages | Mensagens de entrada | Contagem | Total
Sim | Microsoft.EventHub/clusters | IncomingRequests | Solicitações de entrada | Contagem | Total
Sim | Microsoft.EventHub/clusters | OutgoingBytes | Bytes de Saída. | Bytes | Total
Sim | Microsoft.EventHub/clusters | OutgoingMessages | Mensagens de saída | Contagem | Total
Não | Microsoft.EventHub/clusters | QuotaExceededErrors | Cota de Erros Excedida. | Contagem | Total
Não | Microsoft.EventHub/clusters | ServerErrors | Erros do Servidor. | Contagem | Total
Não | Microsoft.EventHub/clusters | SuccessfulRequests | Solicitações bem sucedidas | Contagem | Total
Não | Microsoft.EventHub/clusters | ThrottledRequests | Solicitações Limitadas. | Contagem | Total
Não | Microsoft.EventHub/clusters | UserErrors | Erros de Usuário. | Contagem | Total
Não | Microsoft.EventHub/namespaces | ActiveConnections | ActiveConnections | Contagem | Média
Não | Microsoft.EventHub/namespaces | CaptureBacklog | Lista de Pendências de Captura. | Contagem | Total
Não | Microsoft.EventHub/namespaces | CapturedBytes | Bytes Capturados. | Bytes | Total
Não | Microsoft.EventHub/namespaces | CapturedMessages | Mensagens Capturadas. | Contagem | Total
Não | Microsoft.EventHub/namespaces | ConnectionsClosed | Conexões Fechadas. | Contagem | Média
Não | Microsoft.EventHub/namespaces | ConnectionsOpened | Conexões Abertas. | Contagem | Média
Sim | Microsoft.EventHub/namespaces | EHABL | Arquivar mensagens de pendências (preterido) | Contagem | Total
Sim | Microsoft.EventHub/namespaces | EHAMBS | Taxa de transferência de mensagem de arquivamento (preterido) | Bytes | Total
Sim | Microsoft.EventHub/namespaces | EHAMSGS | Arquivar mensagens (preterido) | Contagem | Total
Sim | Microsoft.EventHub/namespaces | EHINBYTES | Bytes de entrada (Preterida) | Bytes | Total
Sim | Microsoft.EventHub/namespaces | EHINMBS | Bytes de entrada (obsoletos) (preterido) | Bytes | Total
Sim | Microsoft.EventHub/namespaces | EHINMSGS | Mensagens de Entrada (Preterida) | Contagem | Total
Sim | Microsoft.EventHub/namespaces | EHOUTBYTES | Bytes de saída (Preterida) | Bytes | Total
Sim | Microsoft.EventHub/namespaces | EHOUTMBS | Bytes de saída (obsoletos) (preterido) | Bytes | Total
Sim | Microsoft.EventHub/namespaces | EHOUTMSGS | Mensagens de Saída (Preterida) | Contagem | Total
Sim | Microsoft.EventHub/namespaces | FAILREQ | Solicitações com falha (preteridas) | Contagem | Total
Sim | Microsoft.EventHub/namespaces | IncomingBytes | Bytes de Entrada. | Bytes | Total
Sim | Microsoft.EventHub/namespaces | IncomingMessages | Mensagens de entrada | Contagem | Total
Sim | Microsoft.EventHub/namespaces | IncomingRequests | Solicitações de entrada | Contagem | Total
Sim | Microsoft.EventHub/namespaces | INMSGS | Mensagens de entrada (obsoletas) (preteridas) | Contagem | Total
Sim | Microsoft.EventHub/namespaces | INREQS | Solicitações de entrada (preteridas) | Contagem | Total
Sim | Microsoft.EventHub/namespaces | INTERR | Erros de servidor interno (preterido) | Contagem | Total
Sim | Microsoft.EventHub/namespaces | MISCERR | Outros erros (preterido) | Contagem | Total
Sim | Microsoft.EventHub/namespaces | OutgoingBytes | Bytes de Saída. | Bytes | Total
Sim | Microsoft.EventHub/namespaces | OutgoingMessages | Mensagens de saída | Contagem | Total
Sim | Microsoft.EventHub/namespaces | OUTMSGS | Mensagens de saída (obsoletas) (preteridas) | Contagem | Total
Não | Microsoft.EventHub/namespaces | QuotaExceededErrors | Cota de Erros Excedida. | Contagem | Total
Não | Microsoft.EventHub/namespaces | ServerErrors | Erros do Servidor. | Contagem | Total
Não | Microsoft.EventHub/namespaces | Tamanho | Tamanho | Bytes | Média
Não | Microsoft.EventHub/namespaces | SuccessfulRequests | Solicitações bem sucedidas | Contagem | Total
Sim | Microsoft.EventHub/namespaces | SUCCREQ | Solicitações com êxito (preteridas) | Contagem | Total
Sim | Microsoft.EventHub/namespaces | SVRBSY | Erros do servidor ocupado (preterido) | Contagem | Total
Não | Microsoft.EventHub/namespaces | ThrottledRequests | Solicitações Limitadas. | Contagem | Total
Não | Microsoft.EventHub/namespaces | UserErrors | Erros de Usuário. | Contagem | Total
Sim | Microsoft.HDInsight/clusters | CategorizedGatewayRequests | Solicitações de gateway categorizadas | Contagem | Total
Sim | Microsoft.HDInsight/clusters | GatewayRequests | Solicitações de gateway | Contagem | Total
Sim | Microsoft.HDInsight/clusters | NumActiveWorkers | Número de trabalhadores ativos | Contagem | Máximo
Sim | Microsoft.HDInsight/clusters | ScalingRequests | Solicitações de dimensionamento | Contagem | Máximo
Sim | Microsoft.Insights/AutoscaleSettings | MetricThreshold | Limite da Métrica | Contagem | Média
Sim | Microsoft.Insights/AutoscaleSettings | ObservedCapacity | Capacidade Observada | Contagem | Média
Sim | Microsoft.Insights/AutoscaleSettings | ObservedMetricValue | Valor de Métrica Observado | Contagem | Média
Sim | Microsoft.Insights/AutoscaleSettings | ScaleActionsInitiated | Ações de Escala Iniciadas | Contagem | Total
Sim | Microsoft.Insights/Components | availabilityResults/availabilityPercentage | Disponibilidade | Porcentagem | Média
Não | Microsoft.Insights/Components | availabilityResults/contagem | Testes de disponibilidade | Contagem | Contagem
Sim | Microsoft.Insights/Components | availabilityResults/duration | Duração do teste de disponibilidade | MilliSeconds | Média
Sim | Microsoft.Insights/Components | browserTimings/networkDuration | Tempo de conexão de rede de carregamento de página | MilliSeconds | Média
Sim | Microsoft.Insights/Components | browserTimings/processingDuration | Tempo de processamento do cliente | MilliSeconds | Média
Sim | Microsoft.Insights/Components | browserTimings/receiveDuration | Tempo de resposta de recebimento | MilliSeconds | Média
Sim | Microsoft.Insights/Components | browserTimings/sendDuration | Tempo de solicitação de envio | MilliSeconds | Média
Sim | Microsoft.Insights/Components | browserTimings/totalDuration | Tempo de carregamento de página do navegador | MilliSeconds | Média
Não | Microsoft.Insights/Components | dependencies/count | Chamadas de dependência | Contagem | Contagem
Sim | Microsoft.Insights/Components | dependencies/duration | Duração da dependência | MilliSeconds | Média
Não | Microsoft.Insights/Components | dependencies/failed | Falhas de chamada de dependência | Contagem | Contagem
Não | Microsoft.Insights/Components | exceptions/browser | Exceções de navegador | Contagem | Contagem
Sim | Microsoft.Insights/Components | exceptions/count | Exceções | Contagem | Contagem
Não | Microsoft.Insights/Components | exceptions/server | Exceções do servidor | Contagem | Contagem
Sim | Microsoft.Insights/Components | pageViews/count | Visualizações de página | Contagem | Contagem
Sim | Microsoft.Insights/Components | pageViews/duration | Tempo de carregamento de exibição de página | MilliSeconds | Média
Sim | Microsoft.Insights/Components | performanceCounters/exceptionsPerSecond | Taxa de exceção | CountPerSecond | Média
Sim | Microsoft.Insights/Components | performanceCounters/memoryAvailableBytes | Memória disponível | Bytes | Média
Sim | Microsoft.Insights/Components | performanceCounters/processCpuPercentage | CPU do processo | Porcentagem | Média
Sim | Microsoft.Insights/Components | performanceCounters/processIOBytesPerSecond | Taxa de E/S de processamento | BytesPerSecond | Média
Sim | Microsoft.Insights/Components | performanceCounters/processorCpuPercentage | Tempo do processador | Porcentagem | Média
Sim | Microsoft.Insights/Components | performanceCounters/processPrivateBytes | Processar bytes particulares | Bytes | Média
Sim | Microsoft.Insights/Components | performanceCounters/requestExecutionTime | Tempo de execução de solicitação HTTP | MilliSeconds | Média
Sim | Microsoft.Insights/Components | performanceCounters/requestsInQueue | Solicitações HTTP na fila do aplicativo | Contagem | Média
Sim | Microsoft.Insights/Components | performanceCounters/requestsPerSecond | Taxa de solicitação HTTP | CountPerSecond | Média
Não | Microsoft.Insights/Components | requests/count | Solicitações do servidor | Contagem | Contagem
Sim | Microsoft.Insights/Components | requests/duration | Tempo de resposta do servidor | MilliSeconds | Média
Não | Microsoft.Insights/Components | requests/failed | Solicitações falhas | Contagem | Contagem
Não | Microsoft.Insights/Components | solicitações/taxa | Taxa de solicitações do servidor | CountPerSecond | Média
Sim | Microsoft.Insights/Components | traces/count | Rastreamentos | Contagem | Contagem
Sim | Microsoft.KeyVault/vaults | ServiceApiHit | Total de ocorrências da API de Serviço | Contagem | Contagem
Sim | Microsoft.KeyVault/vaults | ServiceApiLatency | Latência geral da API de Serviço | Milissegundos | Média
Sim | Microsoft.KeyVault/vaults | ServiceApiResult | Total de resultados da API de Serviço | Contagem | Contagem
Sim | Microsoft.Kusto/Clusters | CacheUtilization | Utilização de cache | Porcentagem | Média
Sim | Microsoft.Kusto/Clusters | ContinuousExportMaxLatenessMinutes | Minutos de atraso máximo de exportação contínua | Contagem | Máximo
Sim | Microsoft.Kusto/Clusters | ContinuousExportNumOfRecordsExported | Exportação contínua-num de registros exportados | Contagem | Total
Sim | Microsoft.Kusto/Clusters | ContinuousExportPendingCount | Contagem de exportação contínua pendente | Contagem | Máximo
Sim | Microsoft.Kusto/Clusters | ContinuousExportResult | Resultado da exportação contínua | Contagem | Contagem
Sim | Microsoft.Kusto/Clusters | CPU | CPU | Porcentagem | Média
Sim | Microsoft.Kusto/Clusters | EventsProcessedForEventHubs | Eventos processados (para hubs de evento/IoT) | Contagem | Total
Sim | Microsoft.Kusto/Clusters | ExportUtilization | Utilização da exportação | Porcentagem | Máximo
Sim | Microsoft.Kusto/Clusters | IngestionLatencyInSeconds | Latência de ingestão (em segundos) | Segundos | Média
Sim | Microsoft.Kusto/Clusters | IngestionResult | Resultado da ingestão | Contagem | Contagem
Sim | Microsoft.Kusto/Clusters | IngestionUtilization | Utilização de ingestão | Porcentagem | Média
Sim | Microsoft.Kusto/Clusters | IngestionVolumeInMB | Volume de ingestão (em MB) | Contagem | Total
Sim | Microsoft.Kusto/Clusters | KeepAlive | Keep Alive | Contagem | Média
Sim | Microsoft.Kusto/Clusters | QueryDuration | Duração da consulta | Milissegundos | Média
Sim | Microsoft.Kusto/Clusters | SteamingIngestRequestRate | Taxa de solicitação de ingestão de streaming | Contagem | RateRequestsPerSecond
Sim | Microsoft.Kusto/Clusters | StreamingIngestDataRate | Taxa de dados de ingestão de streaming | Contagem | Média
Sim | Microsoft.Kusto/Clusters | StreamingIngestDuration | Duração da ingestão de streaming | Milissegundos | Média
Sim | Microsoft.Kusto/Clusters | StreamingIngestResults | Resultado de ingestão de streaming | Contagem | Média
Sim | Microsoft.Logic/integrationServiceEnvironments | ActionLatency | Latência da ação  | Segundos | Média
Sim | Microsoft.Logic/integrationServiceEnvironments | ActionsCompleted | Ações concluídas  | Contagem | Total
Sim | Microsoft.Logic/integrationServiceEnvironments | ActionsFailed | Ações com falha  | Contagem | Total
Sim | Microsoft.Logic/integrationServiceEnvironments | ActionsSkipped | Ações ignoradas  | Contagem | Total
Sim | Microsoft.Logic/integrationServiceEnvironments | ActionsStarted | Ações iniciadas  | Contagem | Total
Sim | Microsoft.Logic/integrationServiceEnvironments | ActionsSucceeded | Ações bem sucedidas  | Contagem | Total
Sim | Microsoft.Logic/integrationServiceEnvironments | ActionSuccessLatency | Latência das ações bem sucedidas  | Segundos | Média
Sim | Microsoft.Logic/integrationServiceEnvironments | ActionThrottledEvents | Eventos de restrição de ações | Contagem | Total
Sim | Microsoft.Logic/integrationServiceEnvironments | IntegrationServiceEnvironmentConnectorMemoryUsage | Uso de memória do conector para Ambiente de Serviço de Integração | Porcentagem | Média
Sim | Microsoft.Logic/integrationServiceEnvironments | IntegrationServiceEnvironmentConnectorProcessorUsage | Uso do processador do conector para Ambiente de Serviço de Integração | Porcentagem | Média
Sim | Microsoft.Logic/integrationServiceEnvironments | IntegrationServiceEnvironmentWorkflowMemoryUsage | Uso de memória do fluxo de trabalho para Ambiente de Serviço de Integração | Porcentagem | Média
Sim | Microsoft.Logic/integrationServiceEnvironments | IntegrationServiceEnvironmentWorkflowProcessorUsage | Uso do processador de fluxo de trabalho para Ambiente de Serviço de Integração | Porcentagem | Média
Sim | Microsoft.Logic/integrationServiceEnvironments | RunFailurePercentage | Porcentagem de falha de execução | Porcentagem | Total
Sim | Microsoft.Logic/integrationServiceEnvironments | RunLatency | Latência da execução | Segundos | Média
Sim | Microsoft.Logic/integrationServiceEnvironments | RunsCancelled | Execuções canceladas | Contagem | Total
Sim | Microsoft.Logic/integrationServiceEnvironments | RunsCompleted | Execuções concluídas | Contagem | Total
Sim | Microsoft.Logic/integrationServiceEnvironments | RunsFailed | Execuções com falha | Contagem | Total
Sim | Microsoft.Logic/integrationServiceEnvironments | RunsStarted | Execuções iniciadas | Contagem | Total
Sim | Microsoft.Logic/integrationServiceEnvironments | RunsSucceeded | Execuções bem sucedidas | Contagem | Total
Sim | Microsoft.Logic/integrationServiceEnvironments | RunStartThrottledEvents | Executar eventos restritos de início | Contagem | Total
Sim | Microsoft.Logic/integrationServiceEnvironments | RunSuccessLatency | Latência de execução bem sucedida | Segundos | Média
Sim | Microsoft.Logic/integrationServiceEnvironments | RunThrottledEvents | Eventos de restrição de execução | Contagem | Total
Sim | Microsoft.Logic/integrationServiceEnvironments | TriggerFireLatency | Latência do gatilho acionado  | Segundos | Média
Sim | Microsoft.Logic/integrationServiceEnvironments | TriggerLatency | Latência do gatilho  | Segundos | Média
Sim | Microsoft.Logic/integrationServiceEnvironments | TriggersCompleted | Gatilhos concluídos  | Contagem | Total
Sim | Microsoft.Logic/integrationServiceEnvironments | TriggersFailed | Gatilhos com falha  | Contagem | Total
Sim | Microsoft.Logic/integrationServiceEnvironments | TriggersFired | Gatilhos acionados  | Contagem | Total
Sim | Microsoft.Logic/integrationServiceEnvironments | TriggersSkipped | Gatilhos ignorados | Contagem | Total
Sim | Microsoft.Logic/integrationServiceEnvironments | TriggersStarted | Gatilhos iniciados  | Contagem | Total
Sim | Microsoft.Logic/integrationServiceEnvironments | TriggersSucceeded | Gatilhos bem sucedidos  | Contagem | Total
Sim | Microsoft.Logic/integrationServiceEnvironments | TriggerSuccessLatency | Latência do gatilho bem sucedido  | Segundos | Média
Sim | Microsoft.Logic/integrationServiceEnvironments | TriggerThrottledEvents | Eventos de restrição do gatilho | Contagem | Total
Sim | Microsoft.Logic/workflows | ActionLatency | Latência da ação  | Segundos | Média
Sim | Microsoft.Logic/workflows | ActionsCompleted | Ações concluídas  | Contagem | Total
Sim | Microsoft.Logic/workflows | ActionsFailed | Ações com falha  | Contagem | Total
Sim | Microsoft.Logic/workflows | ActionsSkipped | Ações ignoradas  | Contagem | Total
Sim | Microsoft.Logic/workflows | ActionsStarted | Ações iniciadas  | Contagem | Total
Sim | Microsoft.Logic/workflows | ActionsSucceeded | Ações bem sucedidas  | Contagem | Total
Sim | Microsoft.Logic/workflows | ActionSuccessLatency | Latência das ações bem sucedidas  | Segundos | Média
Sim | Microsoft.Logic/workflows | ActionThrottledEvents | Eventos de restrição de ações | Contagem | Total
Sim | Microsoft.Logic/workflows | BillableActionExecutions | Execuções de ação faturáveis | Contagem | Total
Sim | Microsoft.Logic/workflows | BillableTriggerExecutions | Execuções de gatilho faturáveis | Contagem | Total
Sim | Microsoft.Logic/workflows | BillingUsageNativeOperation | Uso de Cobrança para Execuções de Operação Nativa | Contagem | Total
Sim | Microsoft.Logic/workflows | BillingUsageNativeOperation | Uso de Cobrança para Execuções de Operação Nativa | Contagem | Total
Sim | Microsoft.Logic/workflows | BillingUsageStandardConnector | Uso de Cobrança para Execuções de Conector Padrão | Contagem | Total
Sim | Microsoft.Logic/workflows | BillingUsageStandardConnector | Uso de Cobrança para Execuções de Conector Padrão | Contagem | Total
Sim | Microsoft.Logic/workflows | BillingUsageStorageConsumption | Uso de Cobrança para Execuções de Consumo de Armazenamento | Contagem | Total
Sim | Microsoft.Logic/workflows | BillingUsageStorageConsumption | Uso de Cobrança para Execuções de Consumo de Armazenamento | Contagem | Total
Sim | Microsoft.Logic/workflows | RunFailurePercentage | Porcentagem de falha de execução | Porcentagem | Total
Sim | Microsoft.Logic/workflows | RunLatency | Latência da execução | Segundos | Média
Sim | Microsoft.Logic/workflows | RunsCancelled | Execuções canceladas | Contagem | Total
Sim | Microsoft.Logic/workflows | RunsCompleted | Execuções concluídas | Contagem | Total
Sim | Microsoft.Logic/workflows | RunsFailed | Execuções com falha | Contagem | Total
Sim | Microsoft.Logic/workflows | RunsStarted | Execuções iniciadas | Contagem | Total
Sim | Microsoft.Logic/workflows | RunsSucceeded | Execuções bem sucedidas | Contagem | Total
Sim | Microsoft.Logic/workflows | RunStartThrottledEvents | Executar eventos restritos de início | Contagem | Total
Sim | Microsoft.Logic/workflows | RunSuccessLatency | Latência de execução bem sucedida | Segundos | Média
Sim | Microsoft.Logic/workflows | RunThrottledEvents | Eventos de restrição de execução | Contagem | Total
Sim | Microsoft.Logic/workflows | TotalBillableExecutions | Total de execuções faturáveis | Contagem | Total
Sim | Microsoft.Logic/workflows | TriggerFireLatency | Latência do gatilho acionado  | Segundos | Média
Sim | Microsoft.Logic/workflows | TriggerLatency | Latência do gatilho  | Segundos | Média
Sim | Microsoft.Logic/workflows | TriggersCompleted | Gatilhos concluídos  | Contagem | Total
Sim | Microsoft.Logic/workflows | TriggersFailed | Gatilhos com falha  | Contagem | Total
Sim | Microsoft.Logic/workflows | TriggersFired | Gatilhos acionados  | Contagem | Total
Sim | Microsoft.Logic/workflows | TriggersSkipped | Gatilhos ignorados | Contagem | Total
Sim | Microsoft.Logic/workflows | TriggersStarted | Gatilhos iniciados  | Contagem | Total
Sim | Microsoft.Logic/workflows | TriggersSucceeded | Gatilhos bem sucedidos  | Contagem | Total
Sim | Microsoft.Logic/workflows | TriggerSuccessLatency | Latência do gatilho bem sucedido  | Segundos | Média
Sim | Microsoft.Logic/workflows | TriggerThrottledEvents | Eventos de restrição do gatilho | Contagem | Total
Sim | Microsoft.MachineLearningServices/workspaces | Núcleos ativos | Núcleos ativos | Contagem | Média
Sim | Microsoft.MachineLearningServices/workspaces | Nós ativos | Nós ativos | Contagem | Média
Sim | Microsoft.MachineLearningServices/workspaces | Execuções concluídas | Execuções concluídas | Contagem | Total
Sim | Microsoft.MachineLearningServices/workspaces | Execuções com falha | Execuções com falha | Contagem | Total
Sim | Microsoft.MachineLearningServices/workspaces | Núcleos ociosos | Núcleos ociosos | Contagem | Média
Sim | Microsoft.MachineLearningServices/workspaces | Nós ociosos | Nós ociosos | Contagem | Média
Sim | Microsoft.MachineLearningServices/workspaces | Deixando núcleos | Deixando núcleos | Contagem | Média
Sim | Microsoft.MachineLearningServices/workspaces | Saindo de nós | Saindo de nós | Contagem | Média
Sim | Microsoft.MachineLearningServices/workspaces | Falha na Implantação de Modelo | Falha na Implantação de Modelo | Contagem | Total
Sim | Microsoft.MachineLearningServices/workspaces | Implantação de Modelo iniciado | Implantação de Modelo iniciado | Contagem | Total
Sim | Microsoft.MachineLearningServices/workspaces | Implantação de Modelo com êxito | Implantação de Modelo com êxito | Contagem | Total
Sim | Microsoft.MachineLearningServices/workspaces | Falha no registro do modelo | Falha no registro do modelo | Contagem | Total
Sim | Microsoft.MachineLearningServices/workspaces | Registro de modelo bem-sucedido | Registro de modelo bem-sucedido | Contagem | Total
Sim | Microsoft.MachineLearningServices/workspaces | Núcleos preempçãos | Núcleos preempçãos | Contagem | Média
Sim | Microsoft.MachineLearningServices/workspaces | Nós admitidos | Nós admitidos | Contagem | Média
Sim | Microsoft.MachineLearningServices/workspaces | Porcentagem de utilização de cota | Porcentagem de utilização de cota | Contagem | Média
Sim | Microsoft.MachineLearningServices/workspaces | Execuções iniciadas | Execuções iniciadas | Contagem | Total
Sim | Microsoft.MachineLearningServices/workspaces | Total de núcleos | Total de núcleos | Contagem | Média
Sim | Microsoft.MachineLearningServices/workspaces | Total de nós | Total de nós | Contagem | Média
Sim | Microsoft.MachineLearningServices/workspaces | Núcleos inutilizáveis | Núcleos inutilizáveis | Contagem | Média
Sim | Microsoft.MachineLearningServices/workspaces | Nós inutilizáveis | Nós inutilizáveis | Contagem | Média
Sim | Microsoft.Maps/accounts | Disponibilidade | Disponibilidade | Porcentagem | Média
Não | Microsoft.Maps/accounts | Uso | Uso | Contagem | Contagem
Sim | Microsoft. Media/mediaservices | AssetCount | Contagem de ativos | Contagem | Média
Sim | Microsoft. Media/mediaservices | AssetQuota | Cota de ativos | Contagem | Média
Sim | Microsoft. Media/mediaservices | AssetQuotaUsedPercentage | Porcentagem de cota de ativos usada | Porcentagem | Média
Sim | Microsoft. Media/mediaservices | ContentKeyPolicyCount | Contagem de política de chave de conteúdo | Contagem | Média
Sim | Microsoft. Media/mediaservices | ContentKeyPolicyQuota | Cota de política de chave de conteúdo | Contagem | Média
Sim | Microsoft. Media/mediaservices | ContentKeyPolicyQuotaUsedPercentage | Porcentagem de cota usada da política de chave de conteúdo | Porcentagem | Média
Sim | Microsoft. Media/mediaservices | StreamingPolicyCount | Contagem de políticas de streaming | Contagem | Média
Sim | Microsoft. Media/mediaservices | StreamingPolicyQuota | Cota de política de streaming | Contagem | Média
Sim | Microsoft. Media/mediaservices | StreamingPolicyQuotaUsedPercentage | Porcentagem de cota usada da política de streaming | Porcentagem | Média
Sim | Microsoft. Media/mediaservices/streamingEndpoints | Saída | Saída | Bytes | Total
Sim | Microsoft. Media/mediaservices/streamingEndpoints | Solicitações | Solicitações | Contagem | Total
Sim | Microsoft. Media/mediaservices/streamingEndpoints | SuccessE2ELatency | Latência de ponta a ponta com êxito | Milissegundos | Média
Sim | Microsoft. Microservices4Spring/appClusters | GCPauseTotalCount | Contagem de pausar do GC | Contagem | Total
Sim | Microsoft. Microservices4Spring/appClusters | GCPauseTotalTime | Tempo total de pausa do GC | Milissegundos | Total
Sim | Microsoft. Microservices4Spring/appClusters | MaxOldGenMemoryPoolBytes | Tamanho máximo de dados de geração antiga disponíveis | Bytes | Média
Sim | Microsoft. Microservices4Spring/appClusters | OldGenMemoryPoolBytes | Tamanho de dados de geração antiga | Bytes | Média
Sim | Microsoft. Microservices4Spring/appClusters | OldGenPromotedBytes | Promover para tamanho de dados de geração antiga | Bytes | Máximo
Sim | Microsoft. Microservices4Spring/appClusters | ServiceCpuUsagePercentage | Porcentagem de uso da CPU do serviço | Porcentagem | Média
Sim | Microsoft. Microservices4Spring/appClusters | ServiceMemoryCommitted | Memória de serviço atribuída | Bytes | Média
Sim | Microsoft. Microservices4Spring/appClusters | ServiceMemoryMax | Memória máxima do serviço | Bytes | Máximo
Sim | Microsoft. Microservices4Spring/appClusters | ServiceMemoryUsed | Memória usada pelo serviço | Bytes | Média
Sim | Microsoft. Microservices4Spring/appClusters | SystemCpuUsagePercentage | Porcentagem de uso da CPU do sistema | Porcentagem | Média
Sim | Microsoft. Microservices4Spring/appClusters | TomcatErrorCount | Erro global do Tomcat | Contagem | Total
Sim | Microsoft. Microservices4Spring/appClusters | TomcatReceivedBytes | Total de bytes recebidos do Tomcat | Bytes | Total
Sim | Microsoft. Microservices4Spring/appClusters | TomcatRequestMaxTime | Tempo máximo de solicitação do Tomcat | Milissegundos | Máximo
Sim | Microsoft. Microservices4Spring/appClusters | TomcatRequestTotalCount | Contagem total de solicitações do Tomcat | Contagem | Total
Sim | Microsoft. Microservices4Spring/appClusters | TomcatRequestTotalTime | Tempos totais da solicitação do Tomcat | Milissegundos | Total
Sim | Microsoft. Microservices4Spring/appClusters | TomcatResponseAvgTime | Tempo médio da solicitação do Tomcat | Milissegundos | Média
Sim | Microsoft. Microservices4Spring/appClusters | TomcatSentBytes | Total de bytes enviados do Tomcat | Bytes | Total
Sim | Microsoft. Microservices4Spring/appClusters | TomcatSessionActiveCurrentCount | Contagem de sessões ativas de Tomcat | Contagem | Total
Sim | Microsoft. Microservices4Spring/appClusters | TomcatSessionActiveMaxCount | Contagem ativa máxima da sessão do Tomcat | Contagem | Total
Sim | Microsoft. Microservices4Spring/appClusters | TomcatSessionAliveMaxTime | Tempo de atividade máx de sessão do Tomcat | Milissegundos | Máximo
Sim | Microsoft. Microservices4Spring/appClusters | TomcatSessionCreatedCount | Contagem de sessões criadas do Tomcat | Contagem | Total
Sim | Microsoft. Microservices4Spring/appClusters | TomcatSessionExpiredCount | Contagem de sessões expiradas do Tomcat | Contagem | Total
Sim | Microsoft. Microservices4Spring/appClusters | TomcatSessionRejectedCount | Contagem rejeitada da sessão Tomcat | Contagem | Total
Sim | Microsoft. Microservices4Spring/appClusters | YoungGenPromotedBytes | Promover para tamanho de dados de geração jovem | Bytes | Máximo
Sim | Microsoft.NetApp/netAppAccounts/capacityPools | VolumePoolAllocatedUsed | Pool de volume alocado usado | Bytes | Média
Sim | Microsoft.NetApp/netAppAccounts/capacityPools | VolumePoolTotalLogicalSize | Tamanho lógico total do pool de volume | Bytes | Média
Sim | Microsoft. NetApp/netAppAccounts/capacityPools/volumes | AverageReadLatency | Latência média de leitura | MilliSeconds | Média
Sim | Microsoft. NetApp/netAppAccounts/capacityPools/volumes | AverageWriteLatency | Latência média de gravação | MilliSeconds | Média
Sim | Microsoft. NetApp/netAppAccounts/capacityPools/volumes | ReadIops | IOPS de leitura | CountPerSecond | Média
Sim | Microsoft. NetApp/netAppAccounts/capacityPools/volumes | VolumeLogicalSize | Tamanho do volume lógico | Bytes | Média
Sim | Microsoft. NetApp/netAppAccounts/capacityPools/volumes | VolumeSnapshotSize | Tamanho do instantâneo de volume | Bytes | Média
Sim | Microsoft. NetApp/netAppAccounts/capacityPools/volumes | WriteIops | IOPS de gravação | CountPerSecond | Média
Não | Microsoft.Network/applicationGateways | ApplicationGatewayTotalTime | Tempo total do gateway de aplicativo | MilliSeconds | Média
Não | Microsoft.Network/applicationGateways | AvgRequestCountPerHealthyHost | Solicitações por minuto por host íntegro | Contagem | Média
Não | Microsoft.Network/applicationGateways | BackendConnectTime | Tempo de conexão de back-end | MilliSeconds | Média
Não | Microsoft.Network/applicationGateways | BackendFirstByteResponseTime | Tempo de resposta do primeiro byte do back-end | MilliSeconds | Média
Não | Microsoft.Network/applicationGateways | BackendLastByteResponseTime | Tempo de resposta do último byte do back-end | MilliSeconds | Média
Sim | Microsoft.Network/applicationGateways | BackendResponseStatus | Status da resposta de back-end | Contagem | Total
Sim | Microsoft.Network/applicationGateways | BlockedCount | Distribuição de regra de solicitações bloqueadas do firewall do aplicativo Web | Contagem | Total
Sim | Microsoft.Network/applicationGateways | BlockedReqCount | Contagem de solicitações bloqueadas do firewall do aplicativo Web | Contagem | Total
Sim | Microsoft.Network/applicationGateways | BytesReceived | Bytes Recebidos | Bytes | Total
Sim | Microsoft.Network/applicationGateways | BytesSent | Bytes Enviados | Bytes | Total
Não | Microsoft.Network/applicationGateways | CapacityUnits | Unidades de capacidade atuais | Contagem | Média
Não | Microsoft.Network/applicationGateways | ClientRtt | RTT do cliente | MilliSeconds | Média
Não | Microsoft.Network/applicationGateways | ComputeUnits | Unidades de computação atuais | Contagem | Média
Sim | Microsoft.Network/applicationGateways | CurrentConnections | Conexões atuais | Contagem | Total
Sim | Microsoft.Network/applicationGateways | FailedRequests | Solicitações com falha | Contagem | Total
Sim | Microsoft.Network/applicationGateways | HealthyHostCount | Contagem de hosts íntegros | Contagem | Média
Sim | Microsoft.Network/applicationGateways | MatchedCount | Distribuição da regra total do firewall do aplicativo Web | Contagem | Total
Sim | Microsoft.Network/applicationGateways | ResponseStatus | Status da Resposta | Contagem | Total
Não | Microsoft.Network/applicationGateways | Produtividade | Produtividade | BytesPerSecond | Média
Sim | Microsoft.Network/applicationGateways | TlsProtocol | Protocolo TLS do cliente | Contagem | Total
Sim | Microsoft.Network/applicationGateways | TotalRequests | Total de Solicitações | Contagem | Total
Sim | Microsoft.Network/applicationGateways | UnhealthyHostCount | Contagem de hosts não íntegros | Contagem | Média
Sim | Microsoft.Network/azurefirewalls | ApplicationRuleHit | Contagem de impacto de regras de aplicativo | Contagem | Total
Sim | Microsoft.Network/azurefirewalls | Dataprocessod | Dados processados | Bytes | Total
Sim | Microsoft.Network/azurefirewalls | FirewallHealth | Estado de integridade do firewall | Porcentagem | Média
Sim | Microsoft.Network/azurefirewalls | NetworkRuleHit | Contagem de impacto das regras de rede | Contagem | Total
Sim | Microsoft.Network/azurefirewalls | SNATPortUtilization | Utilização da porta SNAT | Porcentagem | Média
Sim | Microsoft.Network/connections | BitsInPerSecond | BitsInPerSecond | CountPerSecond | Média
Sim | Microsoft.Network/connections | BitsOutPerSecond | BitsOutPerSecond | CountPerSecond | Média
Sim | Microsoft.Network/dnszones | QueryVolume | Volume de consulta | Contagem | Total
Não | Microsoft.Network/dnszones | RecordSetCapacityUtilization | Utilização de capacidade de Conjuntos de Registros | Porcentagem | Máximo
Sim | Microsoft.Network/dnszones | RecordSetCount | Contagem de Conjuntos de Registros | Contagem | Máximo
Sim | Microsoft.Network/expressRouteCircuits | ArpAvailability | Disponibilidade do ARP | Porcentagem | Média
Sim | Microsoft.Network/expressRouteCircuits | BgpAvailability | Disponibilidade de BGP | Porcentagem | Média
Não | Microsoft.Network/expressRouteCircuits | BitsInPerSecond | BitsInPerSecond | CountPerSecond | Média
Não | Microsoft.Network/expressRouteCircuits | BitsOutPerSecond | BitsOutPerSecond | CountPerSecond | Média
Não | Microsoft.Network/expressRouteCircuits | GlobalReachBitsInPerSecond | GlobalReachBitsInPerSecond | CountPerSecond | Média
Não | Microsoft.Network/expressRouteCircuits | GlobalReachBitsOutPerSecond | GlobalReachBitsOutPerSecond | CountPerSecond | Média
Não | Microsoft.Network/expressRouteCircuits | QosDropBitsInPerSecond | DroppedInBitsPerSecond | CountPerSecond | Média
Não | Microsoft.Network/expressRouteCircuits | QosDropBitsOutPerSecond | DroppedOutBitsPerSecond | CountPerSecond | Média
Sim | Microsoft.Network/expressRouteCircuits/peerings | BitsInPerSecond | BitsInPerSecond | CountPerSecond | Média
Sim | Microsoft.Network/expressRouteCircuits/peerings | BitsOutPerSecond | BitsOutPerSecond | CountPerSecond | Média
Não | Microsoft. Network/expressRouteGateways | ErGatewayConnectionBitsInPerSecond | BitsInPerSecond | CountPerSecond | Média
Não | Microsoft. Network/expressRouteGateways | ErGatewayConnectionBitsOutPerSecond | BitsOutPerSecond | CountPerSecond | Média
Sim | Microsoft. Network/expressRoutePorts | Adminstate | Adminstate | Contagem | Média
Sim | Microsoft. Network/expressRoutePorts | LineProtocol | LineProtocol | Contagem | Média
Sim | Microsoft. Network/expressRoutePorts | PortBitsInPerSecond | BitsInPerSecond | CountPerSecond | Média
Sim | Microsoft. Network/expressRoutePorts | PortBitsOutPerSecond | BitsOutPerSecond | CountPerSecond | Média
Sim | Microsoft. Network/expressRoutePorts | RxLightLevel | RxLightLevel | Contagem | Média
Sim | Microsoft. Network/expressRoutePorts | TxLightLevel | TxLightLevel | Contagem | Média
Sim | Microsoft.Network/frontdoors | BackendHealthPercentage | Percentual de integridade do back-end | Porcentagem | Média
Sim | Microsoft.Network/frontdoors | BackendRequestCount | Contagem de solicitações de back-end | Contagem | Total
Sim | Microsoft.Network/frontdoors | BackendRequestLatency | Latência de solicitação de back-end | MilliSeconds | Média
Sim | Microsoft.Network/frontdoors | BillableResponseSize | Tamanho de resposta Faturável | Bytes | Total
Sim | Microsoft.Network/frontdoors | RequestCount | Contagem de solicitações | Contagem | Total
Sim | Microsoft.Network/frontdoors | RequestSize | Tamanho da solicitação | Bytes | Total
Sim | Microsoft.Network/frontdoors | ResponseSize | Tamanho da resposta | Bytes | Total
Sim | Microsoft.Network/frontdoors | TotalLatency | Latência total | MilliSeconds | Média
Sim | Microsoft.Network/frontdoors | WebApplicationFirewallRequestCount | Contagem de solicitações do Firewall de Aplicativo Web | Contagem | Total
Não | Microsoft.Network/loadBalancers | AllocatedSnatPorts | Portas SNAT alocadas (versão prévia) | Contagem | Total
Sim | Microsoft.Network/loadBalancers | ByteCount | Contagem de Bytes | Contagem | Total
Sim | Microsoft.Network/loadBalancers | DipAvailability | Status de investigação de integridade | Contagem | Média
Sim | Microsoft.Network/loadBalancers | PacketCount | Contagem de Pacotes | Contagem | Total
Sim | Microsoft.Network/loadBalancers | SnatConnectionCount | Contagem de Conexões SNAT | Contagem | Total
Sim | Microsoft.Network/loadBalancers | SYNCount | Contagem de SYN | Contagem | Total
Não | Microsoft.Network/loadBalancers | UsedSnatPorts | Portas SNAT usadas (versão prévia) | Contagem | Total
Sim | Microsoft.Network/loadBalancers | VipAvailability | Disponibilidade do Caminho de Dados | Contagem | Média
Sim | Microsoft.Network/networkInterfaces | BytesReceivedRate | Bytes Recebidos | Bytes | Total
Sim | Microsoft.Network/networkInterfaces | BytesSentRate | Bytes Enviados | Bytes | Total
Sim | Microsoft.Network/networkInterfaces | PacketsReceivedRate | Pacotes Recebidos | Contagem | Total
Sim | Microsoft.Network/networkInterfaces | PacketsSentRate | Pacotes Enviados | Contagem | Total
Sim | Microsoft.Network/networkWatchers/connectionMonitors | AverageRoundtripMs | Média de tempo de ida e volta (MS) | MilliSeconds | Média
Sim | Microsoft.Network/networkWatchers/connectionMonitors | ChecksFailedPercent | Verificações por cento com falha (versão prévia) | Porcentagem | Média
Sim | Microsoft.Network/networkWatchers/connectionMonitors | ProbesFailedPercent | Porcentagem de investigações com falha | Porcentagem | Média
Sim | Microsoft.Network/networkWatchers/connectionMonitors | RoundTripTimeMs | Tempo de ida e volta (MS) (visualização) | MilliSeconds | Média
Sim | Microsoft.Network/publicIPAddresses | ByteCount | Contagem de Bytes | Contagem | Total
Sim | Microsoft.Network/publicIPAddresses | BytesDroppedDDoS | DDoS de bytes de entrada removidos | BytesPerSecond | Máximo
Sim | Microsoft.Network/publicIPAddresses | BytesForwardedDDoS | DDoS de bytes de entrada encaminhados | BytesPerSecond | Máximo
Sim | Microsoft.Network/publicIPAddresses | BytesInDDoS | DDoS de bytes de entrada | BytesPerSecond | Máximo
Sim | Microsoft.Network/publicIPAddresses | DDoSTriggerSYNPackets | Pacotes de entrada SYN para disparar a mitigação de DDoS | CountPerSecond | Máximo
Sim | Microsoft.Network/publicIPAddresses | DDoSTriggerTCPPackets | Pacotes TCP de entrada a disparar a mitigação de DDoS | CountPerSecond | Máximo
Sim | Microsoft.Network/publicIPAddresses | DDoSTriggerUDPPackets | Pacotes UDP de entrada a disparar a mitigação de DDoS | CountPerSecond | Máximo
Sim | Microsoft.Network/publicIPAddresses | IfUnderDDoSAttack | Sob ataque DDoS ou não | Contagem | Máximo
Sim | Microsoft.Network/publicIPAddresses | PacketCount | Contagem de Pacotes | Contagem | Total
Sim | Microsoft.Network/publicIPAddresses | PacketsDroppedDDoS | DDoS de pacotes de entrada removidos | CountPerSecond | Máximo
Sim | Microsoft.Network/publicIPAddresses | PacketsForwardedDDoS | DDoS de pacotes de entrada encaminhados | CountPerSecond | Máximo
Sim | Microsoft.Network/publicIPAddresses | PacketsInDDoS | DDoS de pacotes de entrada | CountPerSecond | Máximo
Sim | Microsoft.Network/publicIPAddresses | SynCount | Contagem de SYN | Contagem | Total
Sim | Microsoft.Network/publicIPAddresses | TCPBytesDroppedDDoS | DDoS de bytes de TCP de entrada removidos | BytesPerSecond | Máximo
Sim | Microsoft.Network/publicIPAddresses | TCPBytesForwardedDDoS | DDoS de bytes de TCP de entrada encaminhados | BytesPerSecond | Máximo
Sim | Microsoft.Network/publicIPAddresses | TCPBytesInDDoS | DDoS de bytes de TCP de entrada | BytesPerSecond | Máximo
Sim | Microsoft.Network/publicIPAddresses | TCPPacketsDroppedDDoS | DDoS de pacotes TCP de entrada removidos | CountPerSecond | Máximo
Sim | Microsoft.Network/publicIPAddresses | TCPPacketsForwardedDDoS | DDoS de pacotes TCP de entrada encaminhados | CountPerSecond | Máximo
Sim | Microsoft.Network/publicIPAddresses | TCPPacketsInDDoS | DDoS de pacotes TCP de entrada | CountPerSecond | Máximo
Sim | Microsoft.Network/publicIPAddresses | UDPBytesDroppedDDoS | DDoS de bytes de UDP de entrada removidos | BytesPerSecond | Máximo
Sim | Microsoft.Network/publicIPAddresses | UDPBytesForwardedDDoS | DDoS de bytes de UDP de entrada encaminhados | BytesPerSecond | Máximo
Sim | Microsoft.Network/publicIPAddresses | UDPBytesInDDoS | DDoS de bytes de UDP de entrada | BytesPerSecond | Máximo
Sim | Microsoft.Network/publicIPAddresses | UDPPacketsDroppedDDoS | DDoS de pacotes UDP de entrada removidos | CountPerSecond | Máximo
Sim | Microsoft.Network/publicIPAddresses | UDPPacketsForwardedDDoS | DDoS de pacotes UDP de entrada encaminhados | CountPerSecond | Máximo
Sim | Microsoft.Network/publicIPAddresses | UDPPacketsInDDoS | DDoS de pacotes UDP de entrada | CountPerSecond | Máximo
Sim | Microsoft.Network/publicIPAddresses | VipAvailability | Disponibilidade do Caminho de Dados | Contagem | Média
Sim | Microsoft.Network/trafficManagerProfiles | ProbeAgentCurrentEndpointStateByProfileResourceId | Status do Ponto de Extremidade por Ponto de Extremidade | Contagem | Máximo
Sim | Microsoft.Network/trafficManagerProfiles | QpsByEndpoint | Consultas pelo Ponto de Extremidade Retornado | Contagem | Total
Sim | Microsoft.Network/virtualNetworkGateways | AverageBandwidth | Largura de banda de S2S do gateway | BytesPerSecond | Média
Sim | Microsoft.Network/virtualNetworkGateways | P2SBandwidth | Largura de banda de P2S do gateway | BytesPerSecond | Média
Sim | Microsoft.Network/virtualNetworkGateways | P2SConnectionCount | Contagem de conexões P2S | Contagem | Máximo
Sim | Microsoft.Network/virtualNetworkGateways | TunnelAverageBandwidth | Largura de Banda de Túnel | BytesPerSecond | Média
Sim | Microsoft.Network/virtualNetworkGateways | TunnelEgressBytes | Bytes de Saída de Túnel | Bytes | Total
Sim | Microsoft.Network/virtualNetworkGateways | TunnelEgressPacketDropTSMismatch | Remoção de Pacotes Incompatíveis de TS de Saída de Túnel | Contagem | Total
Sim | Microsoft.Network/virtualNetworkGateways | TunnelEgressPackets | Pacotes de Saída de Túnel | Contagem | Total
Sim | Microsoft.Network/virtualNetworkGateways | TunnelIngressBytes | Bytes de Entrada de Túnel | Bytes | Total
Sim | Microsoft.Network/virtualNetworkGateways | TunnelIngressPacketDropTSMismatch | Remoção de Pacotes Incompatíveis de TS de Entrada de Túnel | Contagem | Total
Sim | Microsoft.Network/virtualNetworkGateways | TunnelIngressPackets | Pacotes de Entrada de Túnel | Contagem | Total
Sim | Microsoft.Network/virtualNetworks | PingMeshAverageRoundtripMs | Tempo de ida e volta para pings em uma VM | MilliSeconds | Média
Sim | Microsoft.Network/virtualNetworks | PingMeshProbesFailedPercent | Pings com falha em uma VM | Porcentagem | Média
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming | Mensagens de entrada | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.all.failedrequests | Todas as solicitações com falha recebidas | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.all.requests | Todas as solicitações recebidas | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.scheduled | Notificações por push agendadas enviadas | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.scheduled.cancel | Notificações por push agendadas canceladas | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.all | Operações de gerenciamento de instalação | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.delete | Excluir operações de instalação | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.get | Obter operações de instalação | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.patch | Operações de instalação de patch | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.upsert | Criar ou atualizar operações de instalação | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | notificationhub.pushes | Todas as notificações de saída | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.badorexpiredchannel | Erros de canal incorreto ou expirado | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.channelerror | Erros de canal | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.invalidpayload | Erros de conteúdo | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.pnserror | Erros de sistema de notificação externa | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.success | Notificações de sucesso | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.badchannel | Erro de canal inválido do APNS | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.expiredchannel | Erro de canal expirado do APNS | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.invalidcredentials | Erros de autorização do APNS | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.invalidnotificationsize | Erro de tamanho de notificação inválido do APNS | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.pnserror | Erros do APNS | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.success | Notificações do APNS bem-sucedidas | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.authenticationerror | Erros de autenticação do GCM | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.badchannel | Erro de canal inválido do GCM | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.expiredchannel | Erro de canal expirado do GCM | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidcredentials | Erros de autorização do GCM (credenciais inválidas) | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidnotificationformat | Formato de notificação inválido do GCM | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidnotificationsize | Erro de tamanho de notificação inválido do GCM | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.pnserror | Erros do GCM | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.success | Notificações do GCM bem-sucedidas | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.throttled | Notificações restritas do GCM | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.wrongchannel | Erro de canal incorreto do GCM | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.authenticationerror | Erros de autenticação do MPNS | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.badchannel | Erro de canal inválido do MPNS | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.channeldisconnected | Canal do MPNS desconectado | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.dropped | Notificações descartadas do MPNS | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.invalidcredentials | Credenciais inválidas do MPNS | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.invalidnotificationformat | Erro de tamanho de notificação inválido do MPNS | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.pnserror | Erros do MPNS | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.success | Notificações de sucesso do MPNS | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.throttled | Notificações restritas do MPNS | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.authenticationerror | Erros de autenticação do WNS | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.badchannel | Erro de canal inválido do WNS | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.channeldisconnected | Canal do WNS desconectado | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.channelthrottled | Canal do WNS restrito | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.dropped | Notificações do WNS descartadas | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.expiredchannel | Erro de canal expirado do WNS | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidcredentials | Erros de autorização do WNS (credenciais inválidas) | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidnotificationformat | Formato de notificação inválido do WNS | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidnotificationsize | Erro de tamanho de notificação inválido do WNS | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidtoken | Erros de autorização do WNS (token inválido) | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.pnserror | Erros do WNS | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.success | Notificações do WNS bem-sucedidas | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.throttled | Notificações restritas do WNS | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.tokenproviderunreachable | Erros de autorização do WNS (inacessível) | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.wrongtoken | Erros de autorização do WNS (token incorreto) | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.all | Operações de Registro | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.create | Operação de criação de registro | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.delete | Operação de exclusão de registro | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.get | Operações de leitura do registro | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.update | Operação de atualização de registro | Contagem | Total
Sim | Microsoft.NotificationHubs/Namespaces/NotificationHubs | scheduled.pending | Notificações agendadas pendentes | Contagem | Total
Sim | Microsoft.OperationalInsights/workspaces | Average_% Available Memory | % de Memória Disponível | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_% Available Swap Space | % de Espaço de Permuta Disponível | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_% Committed Bytes In Use | % de Bytes Confirmados em Uso | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_% DPC Time | % de Tempo de DPC | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_% Free Inodes | % de Inodes livres | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_% Free Space | % de Espaço Livre | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_% Free Space | % de Espaço Livre | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_% Idle Time | % de Tempo Ocioso | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_% Interrupt Time | % de Tempo de Interrupção | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_% IO Wait Time | % de Tempo de Espera de E/S | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_% Nice Time | % de Tempo Adequado | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_% Privileged Time | % de Tempo Privilegiado | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_% Processor Time | % Tempo do Processador | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_% Processor Time | % Tempo do Processador | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_% Used Inodes | % de Inodes Usados | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_% Used Memory | % de Memória Usada | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_% Used Space | % de Espaço Usado | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_% Used Swap Space | % de Espaço de Permuta Usado | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_% User Time | % de Tempo do Usuário | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Available MBytes | MBytes Disponíveis | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Available MBytes Memory | MBytes de Memória Disponíveis | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Available MBytes Swap | MBytes de Espaço de Permuta Disponíveis | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Avg. de segundos/Leitura do Disco | Média de disco s/leitura | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Avg. de segundos/Leitura do Disco | Média de disco s/leitura | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Avg. de segundos/Transferência do Disco | Média de disco s/transferência | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Avg. de segundos/Gravação do Disco | Média de disco s/gravação | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Avg. de segundos/Gravação do Disco | Média de disco s/gravação | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Bytes Received/sec | Bytes Recebidos/s | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Bytes Sent/sec | Bytes Enviados/s | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Bytes Total/sec | Bytes Totais/s | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Current Disk Queue Length | Comprimento da fila atual de disco | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Disk Read Bytes/sec | Bytes Lidos no Disco/s | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Disk Reads/sec | Leituras de Disco/s | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Disk Reads/sec | Leituras de Disco/s | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Disk Transfers/sec | Transferências de Disco/s | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Disk Transfers/sec | Transferências de Disco/s | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Disk Write Bytes/sec | Bytes Gravados no Disco/s | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Disk Writes/sec | Gravações de Disco/s | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Disk Writes/sec | Gravações de Disco/s | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Free Megabytes | Megabytes Livres | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Free Megabytes | Megabytes Livres | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Free Physical Memory | Memória Física Livre | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Free Space in Paging Files | Espaço Livre em Arquivos de Paginação | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Free Virtual Memory | Memória Virtual Livre | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Logical Disk Bytes/sec | Bytes de Disco Lógico/s | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Page Reads/sec | Leituras de Página/s | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Page Writes/sec | Gravações de Página/s | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Pages/sec | Páginas/s | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Pct Privileged Time | % de Tempo Privilegiado | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Pct User Time | % de Tempo do Usuário | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Physical Disk Bytes/sec | Bytes/s do Disco Físico | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Processes | Processos | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Processor Queue Length | Tamanho da fila do processador | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Size Stored In Paging Files | Tamanho Armazenado em Arquivos de Paginação | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Total Bytes | Total de Bytes | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Total Bytes Received | Total de Bytes Recebidos | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Total Bytes Transmitted | Total de Bytes Transmitidos | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Total Collisions | Total de Colisões | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Total Packets Received | Total de Pacotes Recebidos | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Total Packets Transmitted | Total de Pacotes Transmitidos | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Total Rx Errors | Total de Erros de Rx | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Total Tx Errors | Total de Erros de Tx | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Uptime | Tempo de atividade | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Used MBytes Swap Space | MBytes de Espaço de Permuta Usado | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Used Memory kBytes | KBytes de Memória Usada | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Used Memory MBytes | MBytes de Memória Usada | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Users | Usuários | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Average_Virtual Shared Memory | Memória Virtual Compartilhada | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Evento | Evento | Contagem | Média
Sim | Microsoft.OperationalInsights/workspaces | Pulsação | Pulsação | Contagem | Total
Sim | Microsoft.OperationalInsights/workspaces | Atualizar | Atualizar | Contagem | Média
Sim | Microsoft.PowerBIDedicated/capacities | memory_metric | Memória | Bytes | Média
Sim | Microsoft.PowerBIDedicated/capacities | memory_thrashing_metric | Ultrapaginação de memória (conjuntos de valores) | Porcentagem | Média
Sim | Microsoft.PowerBIDedicated/capacities | qpu_high_utilization_metric | Alta utilização de QPU | Contagem | Total
Sim | Microsoft.PowerBIDedicated/capacities | QueryDuration | Duração da consulta (conjuntos de valores) | Milissegundos | Média
Sim | Microsoft.PowerBIDedicated/capacities | QueryPoolJobQueueLength | Comprimento da fila de trabalhos do pool de consultas (conjuntos de os) | Contagem | Média
Não | Microsoft.Relay/namespaces | ActiveConnections | ActiveConnections | Contagem | Total
Não | Microsoft.Relay/namespaces | ActiveListeners | ActiveListeners | Contagem | Total
Sim | Microsoft.Relay/namespaces | BytesTransferred | BytesTransferred | Contagem | Total
Não | Microsoft.Relay/namespaces | ListenerConnections-ClientError | ListenerConnections-ClientError | Contagem | Total
Não | Microsoft.Relay/namespaces | ListenerConnections-ServerError | ListenerConnections-ServerError | Contagem | Total
Não | Microsoft.Relay/namespaces | ListenerConnections-Success | ListenerConnections-Success | Contagem | Total
Não | Microsoft.Relay/namespaces | ListenerConnections-TotalRequests | ListenerConnections-TotalRequests | Contagem | Total
Não | Microsoft.Relay/namespaces | ListenerDisconnects | ListenerDisconnects | Contagem | Total
Não | Microsoft.Relay/namespaces | SenderConnections-ClientError | SenderConnections-ClientError | Contagem | Total
Não | Microsoft.Relay/namespaces | SenderConnections-ServerError | SenderConnections-ServerError | Contagem | Total
Não | Microsoft.Relay/namespaces | SenderConnections-Success | SenderConnections-Success | Contagem | Total
Não | Microsoft.Relay/namespaces | SenderConnections-TotalRequests | SenderConnections-TotalRequests | Contagem | Total
Não | Microsoft.Relay/namespaces | SenderDisconnects | SenderDisconnects | Contagem | Total
Sim | Microsoft.Search/searchServices | SearchLatency | Latência de pesquisa | Segundos | Média
Sim | Microsoft.Search/searchServices | SearchQueriesPerSecond | Consultas de pesquisa por segundo | CountPerSecond | Média
Sim | Microsoft.Search/searchServices | ThrottledSearchQueriesPercentage | Porcentagem das consultas de pesquisa limitadas | Porcentagem | Média
Não | Microsoft.ServiceBus/namespaces | ActiveConnections | ActiveConnections | Contagem | Total
Não | Microsoft.ServiceBus/namespaces | ActiveMessages | Contagem de mensagens em uma fila/tópico. | Contagem | Média
Não | Microsoft.ServiceBus/namespaces | ConnectionsClosed | Conexões Fechadas. | Contagem | Média
Não | Microsoft.ServiceBus/namespaces | ConnectionsOpened | Conexões Abertas. | Contagem | Média
Não | Microsoft.ServiceBus/namespaces | CPUXNS | CPU (preterido) | Porcentagem | Máximo
Não | Microsoft.ServiceBus/namespaces | DeadletteredMessages | Contagem de mensagens mortas em uma fila/tópico. | Contagem | Média
Sim | Microsoft.ServiceBus/namespaces | IncomingMessages | Mensagens de entrada | Contagem | Total
Sim | Microsoft.ServiceBus/namespaces | IncomingRequests | Solicitações de entrada | Contagem | Total
Não | Microsoft.ServiceBus/namespaces | Mensagens | Contagem de mensagens em uma fila/tópico. | Contagem | Média
Não | Microsoft.ServiceBus/namespaces | NamespaceCpuUsage | CPU | Porcentagem | Máximo
Não | Microsoft.ServiceBus/namespaces | NamespaceMemoryUsage | Uso de Memória | Porcentagem | Máximo
Sim | Microsoft.ServiceBus/namespaces | OutgoingMessages | Mensagens de saída | Contagem | Total
Não | Microsoft.ServiceBus/namespaces | ScheduledMessages | Contagem de mensagens agendadas em uma fila/tópico. | Contagem | Média
Não | Microsoft.ServiceBus/namespaces | ServerErrors | Erros do Servidor. | Contagem | Total
Não | Microsoft.ServiceBus/namespaces | Tamanho | Tamanho | Bytes | Média
Não | Microsoft.ServiceBus/namespaces | SuccessfulRequests | Solicitações bem sucedidas | Contagem | Total
Não | Microsoft.ServiceBus/namespaces | ThrottledRequests | Solicitações Limitadas. | Contagem | Total
Não | Microsoft.ServiceBus/namespaces | UserErrors | Erros de Usuário. | Contagem | Total
Não | Microsoft.ServiceBus/namespaces | WSXNS | Uso de memória (preterido) | Porcentagem | Máximo
Não | Microsoft.ServiceFabricMesh/applications | ActualCpu | ActualCpu | Contagem | Média
Não | Microsoft.ServiceFabricMesh/applications | ActualMemory | ActualMemory | Bytes | Média
Não | Microsoft.ServiceFabricMesh/applications | AllocatedCpu | AllocatedCpu | Contagem | Média
Não | Microsoft.ServiceFabricMesh/applications | AllocatedMemory | AllocatedMemory | Bytes | Média
Não | Microsoft.ServiceFabricMesh/applications | ApplicationStatus | ApplicationStatus | Contagem | Média
Não | Microsoft.ServiceFabricMesh/applications | ContainerStatus | ContainerStatus | Contagem | Média
Não | Microsoft.ServiceFabricMesh/applications | CpuUtilization | CpuUtilization | Porcentagem | Média
Não | Microsoft.ServiceFabricMesh/applications | MemoryUtilization | MemoryUtilization | Porcentagem | Média
Não | Microsoft.ServiceFabricMesh/applications | RestartCount | RestartCount | Contagem | Média
Não | Microsoft.ServiceFabricMesh/applications | ServiceReplicaStatus | ServiceReplicaStatus | Contagem | Média
Não | Microsoft.ServiceFabricMesh/applications | ServiceStatus | ServiceStatus | Contagem | Média
Sim | Microsoft.SignalRService/SignalR | ConnectionCount | Contagem de conexão | Contagem | Máximo
Sim | Microsoft.SignalRService/SignalR | InboundTraffic | Tráfego de entrada | Bytes | Total
Sim | Microsoft.SignalRService/SignalR | MessageCount | Contagem de mensagens | Contagem | Total
Sim | Microsoft.SignalRService/SignalR | OutboundTraffic | Tráfego de saída | Bytes | Total
Sim | Microsoft.SignalRService/SignalR | SystemErrors | Erros do sistema | Porcentagem | Máximo
Sim | Microsoft.SignalRService/SignalR | UserErrors | Erros do usuário | Porcentagem | Máximo
Sim | Microsoft.Sql/managedInstances | avg_cpu_percent | Percentual médio de CPU | Porcentagem | Média
Sim | Microsoft.Sql/managedInstances | io_bytes_read | Bytes de E/S lidos | Bytes | Média
Sim | Microsoft.Sql/managedInstances | io_bytes_written | Bytes de E/S gravados | Bytes | Média
Sim | Microsoft.Sql/managedInstances | io_requests | Contagem de solicitações de E/S | Contagem | Média
Sim | Microsoft.Sql/managedInstances | reserved_storage_mb | Espaço de armazenamento reservado | Contagem | Média
Sim | Microsoft.Sql/managedInstances | storage_space_used_mb | Espaço de armazenamento usado | Contagem | Média
Sim | Microsoft.Sql/managedInstances | virtual_core_count | Contagem de núcleos virtuais | Contagem | Média
Não | Microsoft.Sql/servers | database_dtu_consumption_percent | Porcentagem de DTU | Porcentagem | Média
Não | Microsoft.Sql/servers | database_storage_used | Espaço de dados usado | Bytes | Média
Sim | Microsoft.Sql/servers | dtu_consumption_percent | Porcentagem de DTU | Porcentagem | Média
Sim | Microsoft.Sql/servers | dtu_used | DTU usado | Contagem | Média
Sim | Microsoft.Sql/servers | storage_used | Espaço de dados usado | Bytes | Média
Sim | Microsoft.Sql/servers/databases | allocated_data_storage | Espaço de dados alocado | Bytes | Média
Sim | Microsoft.Sql/servers/databases | app_cpu_billed | CPU do aplicativo cobrada | Contagem | Total
Sim | Microsoft.Sql/servers/databases | app_cpu_percent | Porcentagem de CPU do aplicativo | Porcentagem | Média
Sim | Microsoft.Sql/servers/databases | app_memory_percent | Porcentagem de memória do aplicativo | Porcentagem | Média
Sim | Microsoft.Sql/servers/databases | blocked_by_firewall | Bloqueado pelo firewall | Contagem | Total
Sim | Microsoft.Sql/servers/databases | cache_hit_percent | Percentual de ocorrência no cache | Porcentagem | Máximo
Sim | Microsoft.Sql/servers/databases | cache_used_percent | Percentual de cache usado | Porcentagem | Máximo
Sim | Microsoft.Sql/servers/databases | connection_failed | Conexões com falha | Contagem | Total
Sim | Microsoft.Sql/servers/databases | connection_successful | Conexões bem sucedidas | Contagem | Total
Sim | Microsoft.Sql/servers/databases | cpu_limit | Limite de CPU | Contagem | Média
Sim | Microsoft.Sql/servers/databases | cpu_percent | Percentual de CPU | Porcentagem | Média
Sim | Microsoft.Sql/servers/databases | cpu_used | CPU usada | Contagem | Média
Sim | Microsoft.Sql/servers/databases | deadlock | Deadlocks | Contagem | Total
Sim | Microsoft.Sql/servers/databases | dtu_consumption_percent | Porcentagem de DTU | Porcentagem | Média
Sim | Microsoft.Sql/servers/databases | dtu_limit | Limite de DTU | Contagem | Média
Sim | Microsoft.Sql/servers/databases | dtu_used | DTU usado | Contagem | Média
Sim | Microsoft.Sql/servers/databases | dwu_consumption_percent | Porcentagem de DWU | Porcentagem | Máximo
Sim | Microsoft.Sql/servers/databases | dwu_limit | Limite de DWU | Contagem | Máximo
Sim | Microsoft.Sql/servers/databases | dwu_used | DWU usado | Contagem | Máximo
Sim | Microsoft.Sql/servers/databases | local_tempdb_usage_percent | Porcentagem de local de tempdb | Porcentagem | Média
Sim | Microsoft.Sql/servers/databases | log_write_percent | Porcentagem de E/S de log | Porcentagem | Média
Sim | Microsoft.Sql/servers/databases | memory_usage_percent | Porcentagem de memória | Porcentagem | Máximo
Sim | Microsoft.Sql/servers/databases | physical_data_read_percent | Porcentagem de E/S de dados | Porcentagem | Média
Sim | Microsoft.Sql/servers/databases | sessions_percent | Porcentagem de sessões | Porcentagem | Média
Sim | Microsoft.Sql/servers/databases | temporário | Espaço de dados usado | Bytes | Máximo
Sim | Microsoft.Sql/servers/databases | storage_percent | Porcentagem de espaço de dados usada | Porcentagem | Máximo
Sim | Microsoft.Sql/servers/databases | tempdb_data_size | Tamanho do arquivo de dados tempdb em kilobytes | Contagem | Máximo
Sim | Microsoft.Sql/servers/databases | tempdb_log_size | Tamanho do arquivo de log de tempdb em kilobytes | Contagem | Máximo
Sim | Microsoft.Sql/servers/databases | tempdb_log_used_percent | Log de porcentagem de tempdb usado | Porcentagem | Máximo
Sim | Microsoft.Sql/servers/databases | workers_percent | Porcentagem de funcionários | Porcentagem | Média
Sim | Microsoft.Sql/servers/databases | xtp_storage_percent | Percentual de armazenamento do OLTP na memória | Porcentagem | Média
Sim | Microsoft.Sql/servers/elasticPools | allocated_data_storage | Espaço de dados alocado | Bytes | Média
Sim | Microsoft.Sql/servers/elasticPools | allocated_data_storage_percent | Porcentagem alocada de espaço de dados | Porcentagem | Máximo
Sim | Microsoft.Sql/servers/elasticPools | cpu_limit | Limite de CPU | Contagem | Média
Sim | Microsoft.Sql/servers/elasticPools | cpu_percent | Percentual de CPU | Porcentagem | Média
Sim | Microsoft.Sql/servers/elasticPools | cpu_used | CPU usada | Contagem | Média
Não | Microsoft.Sql/servers/elasticPools | database_allocated_data_storage | Espaço de dados alocado | Bytes | Média
Não | Microsoft.Sql/servers/elasticPools | database_cpu_limit | Limite de CPU | Contagem | Média
Não | Microsoft.Sql/servers/elasticPools | database_cpu_percent | Percentual de CPU | Porcentagem | Média
Não | Microsoft.Sql/servers/elasticPools | database_cpu_used | CPU usada | Contagem | Média
Não | Microsoft.Sql/servers/elasticPools | database_dtu_consumption_percent | Porcentagem de DTU | Porcentagem | Média
Não | Microsoft.Sql/servers/elasticPools | database_eDTU_used | eDTU usado | Contagem | Média
Não | Microsoft.Sql/servers/elasticPools | database_log_write_percent | Porcentagem de E/S de log | Porcentagem | Média
Não | Microsoft.Sql/servers/elasticPools | database_physical_data_read_percent | Porcentagem de E/S de dados | Porcentagem | Média
Não | Microsoft.Sql/servers/elasticPools | database_sessions_percent | Porcentagem de sessões | Porcentagem | Média
Não | Microsoft.Sql/servers/elasticPools | database_storage_used | Espaço de dados usado | Bytes | Média
Não | Microsoft.Sql/servers/elasticPools | database_workers_percent | Porcentagem de funcionários | Porcentagem | Média
Sim | Microsoft.Sql/servers/elasticPools | dtu_consumption_percent | Porcentagem de DTU | Porcentagem | Média
Sim | Microsoft.Sql/servers/elasticPools | eDTU_limit | Limite de eDTU | Contagem | Média
Sim | Microsoft.Sql/servers/elasticPools | eDTU_used | eDTU usado | Contagem | Média
Sim | Microsoft.Sql/servers/elasticPools | log_write_percent | Porcentagem de E/S de log | Porcentagem | Média
Sim | Microsoft.Sql/servers/elasticPools | physical_data_read_percent | Porcentagem de E/S de dados | Porcentagem | Média
Sim | Microsoft.Sql/servers/elasticPools | sessions_percent | Porcentagem de sessões | Porcentagem | Média
Sim | Microsoft.Sql/servers/elasticPools | storage_limit | Tamanho máximo de dados | Bytes | Média
Sim | Microsoft.Sql/servers/elasticPools | storage_percent | Porcentagem de espaço de dados usada | Porcentagem | Média
Sim | Microsoft.Sql/servers/elasticPools | storage_used | Espaço de dados usado | Bytes | Média
Sim | Microsoft.Sql/servers/elasticPools | tempdb_data_size | Tamanho do arquivo de dados tempdb em kilobytes | Contagem | Máximo
Sim | Microsoft.Sql/servers/elasticPools | tempdb_log_size | Tamanho do arquivo de log de tempdb em kilobytes | Contagem | Máximo
Sim | Microsoft.Sql/servers/elasticPools | tempdb_log_used_percent | Log de porcentagem de tempdb usado | Porcentagem | Máximo
Sim | Microsoft.Sql/servers/elasticPools | workers_percent | Porcentagem de funcionários | Porcentagem | Média
Sim | Microsoft.Sql/servers/elasticPools | xtp_storage_percent | Percentual de armazenamento do OLTP na memória | Porcentagem | Média
Sim | Microsoft.Storage/storageAccounts | Disponibilidade | Disponibilidade | Porcentagem | Média
Sim | Microsoft.Storage/storageAccounts | Saída | Saída | Bytes | Total
Sim | Microsoft.Storage/storageAccounts | Entrada | Entrada | Bytes | Total
Sim | Microsoft.Storage/storageAccounts | SuccessE2ELatency | Latência de E2E com Sucesso | Milissegundos | Média
Sim | Microsoft.Storage/storageAccounts | SuccessServerLatency | Latência de Servidor Com Sucesso | Milissegundos | Média
Sim | Microsoft.Storage/storageAccounts | Transações | Transações | Contagem | Total
Não | Microsoft.Storage/storageAccounts | UsedCapacity | Capacidade utilizada | Bytes | Média
Sim | Microsoft.Storage/storageAccounts/blobServices | Disponibilidade | Disponibilidade | Porcentagem | Média
Não | Microsoft.Storage/storageAccounts/blobServices | BlobCapacity | Capacidade do Blob | Bytes | Média
Não | Microsoft.Storage/storageAccounts/blobServices | BlobCount | Contagem de Blobs | Contagem | Média
Sim | Microsoft.Storage/storageAccounts/blobServices | ContainerCount | Contagem de Contêineres de Blobs | Contagem | Média
Sim | Microsoft.Storage/storageAccounts/blobServices | Saída | Saída | Bytes | Total
Não | Microsoft.Storage/storageAccounts/blobServices | IndexCapacity | Capacidade de Índice | Bytes | Média
Sim | Microsoft.Storage/storageAccounts/blobServices | Entrada | Entrada | Bytes | Total
Sim | Microsoft.Storage/storageAccounts/blobServices | SuccessE2ELatency | Latência de E2E com Sucesso | Milissegundos | Média
Sim | Microsoft.Storage/storageAccounts/blobServices | SuccessServerLatency | Latência de Servidor Com Sucesso | Milissegundos | Média
Sim | Microsoft.Storage/storageAccounts/blobServices | Transações | Transações | Contagem | Total
Sim | Microsoft.Storage/storageAccounts/fileServices | Disponibilidade | Disponibilidade | Porcentagem | Média
Sim | Microsoft.Storage/storageAccounts/fileServices | Saída | Saída | Bytes | Total
Não | Microsoft.Storage/storageAccounts/fileServices | FileCapacity | Capacidade do Arquivo | Bytes | Média
Não | Microsoft.Storage/storageAccounts/fileServices | FileCount | Contagem de Arquivos | Contagem | Média
Não | Microsoft.Storage/storageAccounts/fileServices | FileShareCount | Contagem de Compartilhamentos de Arquivos | Contagem | Média
Não | Microsoft.Storage/storageAccounts/fileServices | FileShareQuota | Tamanho da cota de compartilhamento de arquivos | Bytes | Média
Não | Microsoft.Storage/storageAccounts/fileServices | FileShareSnapshotCount | Contagem de instantâneos de compartilhamento de arquivos | Contagem | Média
Não | Microsoft.Storage/storageAccounts/fileServices | FileShareSnapshotSize | Tamanho do instantâneo de compartilhamento de arquivos | Bytes | Média
Sim | Microsoft.Storage/storageAccounts/fileServices | Entrada | Entrada | Bytes | Total
Sim | Microsoft.Storage/storageAccounts/fileServices | SuccessE2ELatency | Latência de E2E com Sucesso | Milissegundos | Média
Sim | Microsoft.Storage/storageAccounts/fileServices | SuccessServerLatency | Latência de Servidor Com Sucesso | Milissegundos | Média
Sim | Microsoft.Storage/storageAccounts/fileServices | Transações | Transações | Contagem | Total
Sim | Microsoft.Storage/storageAccounts/queueServices | Disponibilidade | Disponibilidade | Porcentagem | Média
Sim | Microsoft.Storage/storageAccounts/queueServices | Saída | Saída | Bytes | Total
Sim | Microsoft.Storage/storageAccounts/queueServices | Entrada | Entrada | Bytes | Total
Sim | Microsoft.Storage/storageAccounts/queueServices | QueueCapacity | Capacidade da Fila | Bytes | Média
Sim | Microsoft.Storage/storageAccounts/queueServices | QueueCount | Contagem de Filas | Contagem | Média
Sim | Microsoft.Storage/storageAccounts/queueServices | QueueMessageCount | Contagem de Mensagens da Fila | Contagem | Média
Sim | Microsoft.Storage/storageAccounts/queueServices | SuccessE2ELatency | Latência de E2E com Sucesso | Milissegundos | Média
Sim | Microsoft.Storage/storageAccounts/queueServices | SuccessServerLatency | Latência de Servidor Com Sucesso | Milissegundos | Média
Sim | Microsoft.Storage/storageAccounts/queueServices | Transações | Transações | Contagem | Total
Sim | Microsoft.Storage/storageAccounts/tableServices | Disponibilidade | Disponibilidade | Porcentagem | Média
Sim | Microsoft.Storage/storageAccounts/tableServices | Saída | Saída | Bytes | Total
Sim | Microsoft.Storage/storageAccounts/tableServices | Entrada | Entrada | Bytes | Total
Sim | Microsoft.Storage/storageAccounts/tableServices | SuccessE2ELatency | Latência de E2E com Sucesso | Milissegundos | Média
Sim | Microsoft.Storage/storageAccounts/tableServices | SuccessServerLatency | Latência de Servidor Com Sucesso | Milissegundos | Média
Sim | Microsoft.Storage/storageAccounts/tableServices | TableCapacity | Capacidade da Tabela | Bytes | Média
Sim | Microsoft.Storage/storageAccounts/tableServices | TableCount | Contagem de Tabelas | Contagem | Média
Sim | Microsoft.Storage/storageAccounts/tableServices | TableEntityCount | Contagem de Entidades de Tabela | Contagem | Média
Sim | Microsoft.Storage/storageAccounts/tableServices | Transações | Transações | Contagem | Total
Sim | Microsoft. StorageCache/caches | ClientIOPS | IOPS de cliente total | Contagem | Média
Sim | Microsoft. StorageCache/caches | ClientLatency | Latência média do cliente | Milissegundos | Média
Sim | Microsoft. StorageCache/caches | ClientLockIOPS | IOPS de bloqueio de cliente | CountPerSecond | Média
Sim | Microsoft. StorageCache/caches | ClientMetadataReadIOPS | IOPS de leitura de metadados do cliente | CountPerSecond | Média
Sim | Microsoft. StorageCache/caches | ClientMetadataWriteIOPS | IOPS de gravação de metadados do cliente | CountPerSecond | Média
Sim | Microsoft. StorageCache/caches | ClientReadIOPS | IOPS de leitura do cliente | CountPerSecond | Média
Sim | Microsoft. StorageCache/caches | ClientReadThroughput | Média de taxa de transferência de leitura do cache | BytesPerSecond | Média
Sim | Microsoft. StorageCache/caches | ClientWriteIOPS | IOPS de gravação de cliente | CountPerSecond | Média
Sim | Microsoft. StorageCache/caches | ClientWriteThroughput | Taxa de transferência média de gravação no cache | BytesPerSecond | Média
Sim | Microsoft. StorageCache/caches | StorageTargetAsyncWriteThroughput | Taxa de transferência de gravação assíncrona StorageTarget | BytesPerSecond | Média
Sim | Microsoft. StorageCache/caches | StorageTargetFillThroughput | Taxa de transferência de preenchimento do StorageTarget | BytesPerSecond | Média
Sim | Microsoft. StorageCache/caches | StorageTargetHealth | Integridade do destino de armazenamento | Contagem | Média
Sim | Microsoft. StorageCache/caches | StorageTargetIOPS | Total de IOPS de StorageTarget | Contagem | Média
Sim | Microsoft. StorageCache/caches | StorageTargetLatency | Latência de StorageTarget | Milissegundos | Média
Sim | Microsoft. StorageCache/caches | StorageTargetMetadataReadIOPS | IOPS de leitura de metadados StorageTarget | CountPerSecond | Média
Sim | Microsoft. StorageCache/caches | StorageTargetMetadataWriteIOPS | IOPS de gravação de metadados StorageTarget | CountPerSecond | Média
Sim | Microsoft. StorageCache/caches | StorageTargetReadAheadThroughput | Taxa de transferência de leitura antecipada de StorageTarget | BytesPerSecond | Média
Sim | Microsoft. StorageCache/caches | StorageTargetReadIOPS | IOPS de leitura StorageTarget | CountPerSecond | Média
Sim | Microsoft. StorageCache/caches | StorageTargetSyncWriteThroughput | Taxa de transferência de gravação síncrona StorageTarget | BytesPerSecond | Média
Sim | Microsoft. StorageCache/caches | StorageTargetTotalReadThroughput | Taxa de transferência de leitura total de StorageTarget | BytesPerSecond | Média
Sim | Microsoft. StorageCache/caches | StorageTargetTotalWriteThroughput | Taxa de transferência total de gravação de StorageTarget | BytesPerSecond | Média
Sim | Microsoft. StorageCache/caches | StorageTargetWriteIOPS | IOPS de gravação StorageTarget | Contagem | Média
Sim | Microsoft. StorageCache/caches | Tempo de atividade | Tempo de atividade | Contagem | Média
Sim | microsoft.storagesync/storageSyncServices | ServerSyncSessionResult | Resultado da sessão de sincronização | Contagem | Média
Sim | microsoft.storagesync/storageSyncServices | StorageSyncBatchTransferredFileBytes | Bytes sincronizados | Bytes | Total
Sim | microsoft.storagesync/storageSyncServices | StorageSyncRecalledNetworkBytesByApplication | Tamanho de recall de camadas de nuvem por aplicativo | Bytes | Total
Sim | microsoft.storagesync/storageSyncServices | StorageSyncRecalledTotalNetworkBytes | Tamanho de recall em camadas de nuvem | Bytes | Total
Sim | microsoft.storagesync/storageSyncServices | StorageSyncRecallIOTotalSizeBytes | Recall da camada de nuvem | Bytes | Total
Sim | microsoft.storagesync/storageSyncServices | StorageSyncRecallThroughputBytesPerSecond | Taxa de transferência de recall em camadas de nuvem | BytesPerSecond | Média
Sim | microsoft.storagesync/storageSyncServices | StorageSyncServerHeartbeat | Status online do servidor | Contagem | Máximo
Sim | microsoft.storagesync/storageSyncServices | StorageSyncSyncSessionAppliedFilesCount | Arquivos sincronizados | Contagem | Total
Sim | microsoft.storagesync/storageSyncServices | StorageSyncSyncSessionPerItemErrorsCount | Arquivos não sincronizando | Contagem | Total
Sim | Microsoft. storagesync/storageSyncServices/registeredServers | ServerHeartbeat | Status online do servidor | Contagem | Máximo
Sim | Microsoft. storagesync/storageSyncServices/registeredServers | ServerRecallIOTotalSizeBytes | Recall da camada de nuvem | Bytes | Total
Sim | Microsoft. storagesync/storageSyncServices/syncGroups | SyncGroupBatchTransferredFileBytes | Bytes sincronizados | Bytes | Total
Sim | Microsoft. storagesync/storageSyncServices/syncGroups | SyncGroupSyncSessionAppliedFilesCount | Arquivos sincronizados | Contagem | Total
Sim | Microsoft. storagesync/storageSyncServices/syncGroups | SyncGroupSyncSessionPerItemErrorsCount | Arquivos não sincronizando | Contagem | Total
Sim | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints | ServerEndpointBatchTransferredFileBytes | Bytes sincronizados | Bytes | Total
Sim | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints | ServerEndpointSyncSessionAppliedFilesCount | Arquivos sincronizados | Contagem | Total
Sim | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints | ServerEndpointSyncSessionPerItemErrorsCount | Arquivos não sincronizando | Contagem | Total
Sim | Microsoft.StreamAnalytics/streamingjobs | AMLCalloutFailedRequests | Solicitações de função com falha | Contagem | Total
Sim | Microsoft.StreamAnalytics/streamingjobs | AMLCalloutInputEvents | Eventos de função | Contagem | Total
Sim | Microsoft.StreamAnalytics/streamingjobs | AMLCalloutRequests | Solicitações de função | Contagem | Total
Sim | Microsoft.StreamAnalytics/streamingjobs | ConversionErrors | Erros de conversão de dados | Contagem | Total
Sim | Microsoft.StreamAnalytics/streamingjobs | DeserializationError | Erros de desserialização de entrada | Contagem | Total
Sim | Microsoft.StreamAnalytics/streamingjobs | DroppedOrAdjustedEvents | Eventos fora de ordem | Contagem | Total
Sim | Microsoft.StreamAnalytics/streamingjobs | EarlyInputEvents | Eventos de Entrada Antecipados | Contagem | Total
Sim | Microsoft.StreamAnalytics/streamingjobs | Errors | Erros de runtime | Contagem | Total
Sim | Microsoft.StreamAnalytics/streamingjobs | InputEventBytes | Bytes de evento de entrada | Bytes | Total
Sim | Microsoft.StreamAnalytics/streamingjobs | InputEvents | Eventos de entrada | Contagem | Total
Sim | Microsoft.StreamAnalytics/streamingjobs | InputEventsSourcesBacklogged | Eventos de Entrada Acumulados | Contagem | Máximo
Sim | Microsoft.StreamAnalytics/streamingjobs | InputEventsSourcesPerSecond | Fontes de Entrada Recebidas | Contagem | Total
Sim | Microsoft.StreamAnalytics/streamingjobs | LateInputEvents | Eventos de entrada atrasados | Contagem | Total
Sim | Microsoft.StreamAnalytics/streamingjobs | OutputEvents | Eventos de saída | Contagem | Total
Sim | Microsoft.StreamAnalytics/streamingjobs | OutputWatermarkDelaySeconds | Atraso de Marca-d'água | Segundos | Máximo
Sim | Microsoft.StreamAnalytics/streamingjobs | ResourceUtilization | % de utilização do SU | Porcentagem | Máximo
Sim | Microsoft.VMwareCloudSimple/virtualMachines | Bytes de leitura de disco | Bytes de leitura de disco | Bytes | Total
Sim | Microsoft.VMwareCloudSimple/virtualMachines | Operações de leitura de disco/Seg | Operações de leitura de disco/Seg | CountPerSecond | Média
Sim | Microsoft.VMwareCloudSimple/virtualMachines | Bytes de gravação de disco | Bytes de gravação de disco | Bytes | Total
Sim | Microsoft.VMwareCloudSimple/virtualMachines | Operações de gravação de disco/Seg | Operações de gravação de disco/Seg | CountPerSecond | Média
Sim | Microsoft.VMwareCloudSimple/virtualMachines | DiskReadBytesPerSecond | Disk Read Bytes/Sec | BytesPerSecond | Média
Sim | Microsoft.VMwareCloudSimple/virtualMachines | DiskReadLatency | Latência de leitura de disco | Milissegundos | Média
Sim | Microsoft.VMwareCloudSimple/virtualMachines | DiskReadOperations | Operações de leitura de disco | Contagem | Total
Sim | Microsoft.VMwareCloudSimple/virtualMachines | DiskWriteBytesPerSecond | Disk Write Bytes/Sec | BytesPerSecond | Média
Sim | Microsoft.VMwareCloudSimple/virtualMachines | DiskWriteLatency | Latência de Gravação de disco | Milissegundos | Média
Sim | Microsoft.VMwareCloudSimple/virtualMachines | DiskWriteOperations | Operações de gravação de disco | Contagem | Total
Sim | Microsoft.VMwareCloudSimple/virtualMachines | MemoryActive | Memória ativa | Bytes | Média
Sim | Microsoft.VMwareCloudSimple/virtualMachines | MemoryGranted | Memória concedida | Bytes | Média
Sim | Microsoft.VMwareCloudSimple/virtualMachines | MemoryUsed | Memória usada | Bytes | Média
Sim | Microsoft.VMwareCloudSimple/virtualMachines | Entrada na rede | Entrada na rede | Bytes | Total
Sim | Microsoft.VMwareCloudSimple/virtualMachines | Saída da rede | Saída da rede | Bytes | Total
Sim | Microsoft.VMwareCloudSimple/virtualMachines | NetworkInBytesPerSecond | Rede em bytes/s | BytesPerSecond | Média
Sim | Microsoft.VMwareCloudSimple/virtualMachines | NetworkOutBytesPerSecond | Bytes de saída de rede/s | BytesPerSecond | Média
Sim | Microsoft.VMwareCloudSimple/virtualMachines | Porcentagem de CPU | Porcentagem de CPU | Porcentagem | Média
Sim | Microsoft.VMwareCloudSimple/virtualMachines | PercentageCpuReady | Percentual de CPU pronta | Milissegundos | Total
Sim | Microsoft.Web/hostingEnvironments/multiRolePools | ActiveRequests | Solicitações ativas | Contagem | Total
Sim | Microsoft.Web/hostingEnvironments/multiRolePools | AverageResponseTime | Tempo Médio de Resposta | Segundos | Média
Sim | Microsoft.Web/hostingEnvironments/multiRolePools | BytesReceived | Entrada de Dados | Bytes | Total
Sim | Microsoft.Web/hostingEnvironments/multiRolePools | BytesSent | Saída de dados | Bytes | Total
Sim | Microsoft.Web/hostingEnvironments/multiRolePools | CpuPercentage | Percentual de CPU | Porcentagem | Média
Sim | Microsoft.Web/hostingEnvironments/multiRolePools | DiskQueueLength | Tamanho da fila do disco | Contagem | Média
Sim | Microsoft.Web/hostingEnvironments/multiRolePools | Http101 | Http 101 | Contagem | Total
Sim | Microsoft.Web/hostingEnvironments/multiRolePools | Http2xx | Http 2xx | Contagem | Total
Sim | Microsoft.Web/hostingEnvironments/multiRolePools | Http3xx | Http 3xx | Contagem | Total
Sim | Microsoft.Web/hostingEnvironments/multiRolePools | Http401 | Http 401 | Contagem | Total
Sim | Microsoft.Web/hostingEnvironments/multiRolePools | Http403 | Http 403 | Contagem | Total
Sim | Microsoft.Web/hostingEnvironments/multiRolePools | Http404 | Http 404 | Contagem | Total
Sim | Microsoft.Web/hostingEnvironments/multiRolePools | Http406 | Http 406 | Contagem | Total
Sim | Microsoft.Web/hostingEnvironments/multiRolePools | Http4xx | Http 4xx | Contagem | Total
Sim | Microsoft.Web/hostingEnvironments/multiRolePools | Http5xx | Erros do Servidor Http | Contagem | Total
Sim | Microsoft.Web/hostingEnvironments/multiRolePools | HttpQueueLength | Tamanho da Fila de Http | Contagem | Média
Sim | Microsoft.Web/hostingEnvironments/multiRolePools | LargeAppServicePlanInstances | Funções de trabalho grandes do Plano do Serviço de Aplicativo | Contagem | Média
Sim | Microsoft.Web/hostingEnvironments/multiRolePools | MediumAppServicePlanInstances | Funções de trabalho médias do Plano do Serviço de Aplicativo | Contagem | Média
Sim | Microsoft.Web/hostingEnvironments/multiRolePools | MemoryPercentage | Porcentagem de Memória | Porcentagem | Média
Sim | Microsoft.Web/hostingEnvironments/multiRolePools | Solicitações | Solicitações | Contagem | Total
Sim | Microsoft.Web/hostingEnvironments/multiRolePools | SmallAppServicePlanInstances | Funções de trabalho pequenas do Plano do Serviço de Aplicativo | Contagem | Média
Sim | Microsoft.Web/hostingEnvironments/multiRolePools | TotalFrontEnds | Total de front-ends | Contagem | Média
Sim | Microsoft.Web/hostingEnvironments/workerPools | CpuPercentage | Percentual de CPU | Porcentagem | Média
Sim | Microsoft.Web/hostingEnvironments/workerPools | MemoryPercentage | Porcentagem de Memória | Porcentagem | Média
Sim | Microsoft.Web/hostingEnvironments/workerPools | WorkersAvailable | Funções de trabalho disponíveis | Contagem | Média
Sim | Microsoft.Web/hostingEnvironments/workerPools | WorkersTotal | Total de funções de trabalho | Contagem | Média
Sim | Microsoft.Web/hostingEnvironments/workerPools | WorkersUsed | Funções de trabalho usadas | Contagem | Média
Sim | Microsoft.Web/serverfarms | BytesReceived | Entrada de Dados | Bytes | Total
Sim | Microsoft.Web/serverfarms | BytesSent | Saída de dados | Bytes | Total
Sim | Microsoft.Web/serverfarms | CpuPercentage | Percentual de CPU | Porcentagem | Média
Sim | Microsoft.Web/serverfarms | DiskQueueLength | Tamanho da fila do disco | Contagem | Média
Sim | Microsoft.Web/serverfarms | HttpQueueLength | Tamanho da Fila de Http | Contagem | Média
Sim | Microsoft.Web/serverfarms | MemoryPercentage | Porcentagem de Memória | Porcentagem | Média
Sim | Microsoft.Web/serverfarms | TcpCloseWait | Espera TCP de fechamento | Contagem | Média
Sim | Microsoft.Web/serverfarms | TcpClosing | Fechamento de TCP | Contagem | Média
Sim | Microsoft.Web/serverfarms | TcpEstablished | TCP estabelecido | Contagem | Média
Sim | Microsoft.Web/serverfarms | TcpFinWait1 | Espera de TCP fin 1 | Contagem | Média
Sim | Microsoft.Web/serverfarms | TcpFinWait2 | Espera de TCP Fin 2 | Contagem | Média
Sim | Microsoft.Web/serverfarms | TcpLastAck | Última confirmação TCP | Contagem | Média
Sim | Microsoft.Web/serverfarms | TcpSynReceived | SYN TCP recebido | Contagem | Média
Sim | Microsoft.Web/serverfarms | TcpSynSent | SYN TCP enviado | Contagem | Média
Sim | Microsoft.Web/serverfarms | TcpTimeWait | Espera de tempo TCP | Contagem | Média
Sim | Microsoft.Web/sites | AppConnections | Conexões | Contagem | Média
Sim | Microsoft.Web/sites | AverageMemoryWorkingSet | Conjunto de trabalho de memória média | Bytes | Média
Sim | Microsoft.Web/sites | AverageResponseTime | Tempo Médio de Resposta | Segundos | Média
Sim | Microsoft.Web/sites | BytesReceived | Entrada de Dados | Bytes | Total
Sim | Microsoft.Web/sites | BytesSent | Saída de dados | Bytes | Total
Sim | Microsoft.Web/sites | CpuTime | Tempo de CPU | Segundos | Total
Sim | Microsoft.Web/sites | CurrentAssemblies | Assemblies Atuais | Contagem | Média
Sim | Microsoft.Web/sites | FunctionExecutionCount | Contagem de Execução de Função | Contagem | Total
Sim | Microsoft.Web/sites | FunctionExecutionUnits | Unidades de Execução de Função | Contagem | Total
Sim | Microsoft.Web/sites | Gen0Collections | Coletas de lixo da Ger 0 | Contagem | Total
Sim | Microsoft.Web/sites | Gen1Collections | Coletas de lixo da Ger 1 | Contagem | Total
Sim | Microsoft.Web/sites | Gen2Collections | Coletas de lixo da Ger 2 | Contagem | Total
Sim | Microsoft.Web/sites | Alças | Núm. de Identificadores | Contagem | Média
Sim | Microsoft.Web/sites | HealthCheckStatus | Status da verificação de integridade | Contagem | Média
Sim | Microsoft.Web/sites | Http101 | Http 101 | Contagem | Total
Sim | Microsoft.Web/sites | Http2xx | Http 2xx | Contagem | Total
Sim | Microsoft.Web/sites | Http3xx | Http 3xx | Contagem | Total
Sim | Microsoft.Web/sites | Http401 | Http 401 | Contagem | Total
Sim | Microsoft.Web/sites | Http403 | Http 403 | Contagem | Total
Sim | Microsoft.Web/sites | Http404 | Http 404 | Contagem | Total
Sim | Microsoft.Web/sites | Http406 | Http 406 | Contagem | Total
Sim | Microsoft.Web/sites | Http4xx | Http 4xx | Contagem | Total
Sim | Microsoft.Web/sites | Http5xx | Erros do Servidor Http | Contagem | Total
Sim | Microsoft.Web/sites | HttpResponsetime | Tempo de resposta | Segundos | Média
Sim | Microsoft.Web/sites | IoOtherBytesPerSecond | E/S de outros bytes por segundo | BytesPerSecond | Total
Sim | Microsoft.Web/sites | IoOtherOperationsPerSecond | E/S de outras operações por segundo | BytesPerSecond | Total
Sim | Microsoft.Web/sites | IoReadBytesPerSecond | E/S de bytes de leitura por segundo | BytesPerSecond | Total
Sim | Microsoft.Web/sites | IoReadOperationsPerSecond | E/S de operações de leitura por segundo | BytesPerSecond | Total
Sim | Microsoft.Web/sites | IoWriteBytesPerSecond | E/S de bytes de gravação por segundo | BytesPerSecond | Total
Sim | Microsoft.Web/sites | IoWriteOperationsPerSecond | E/S de operações de gravação por segundo | BytesPerSecond | Total
Sim | Microsoft.Web/sites | MemoryWorkingSet | Conjunto de trabalho de memória | Bytes | Média
Sim | Microsoft.Web/sites | PrivateBytes | Bytes Particulares | Bytes | Média
Sim | Microsoft.Web/sites | Solicitações | Solicitações | Contagem | Total
Sim | Microsoft.Web/sites | RequestsInApplicationQueue | Solicitações na fila do aplicativo | Contagem | Média
Sim | Microsoft.Web/sites | Threads | Contagem de Threads | Contagem | Média
Sim | Microsoft.Web/sites | TotalAppDomains | Total de domínios de aplicativo | Contagem | Média
Sim | Microsoft.Web/sites | TotalAppDomainsUnloaded | Total de domínios de aplicativo descarregados | Contagem | Média
Sim | Microsoft.Web/sites/slots | AppConnections | Conexões | Contagem | Média
Sim | Microsoft.Web/sites/slots | AverageMemoryWorkingSet | Conjunto de trabalho de memória média | Bytes | Média
Sim | Microsoft.Web/sites/slots | AverageResponseTime | Tempo Médio de Resposta | Segundos | Média
Sim | Microsoft.Web/sites/slots | BytesReceived | Entrada de Dados | Bytes | Total
Sim | Microsoft.Web/sites/slots | BytesSent | Saída de dados | Bytes | Total
Sim | Microsoft.Web/sites/slots | CpuTime | Tempo de CPU | Segundos | Total
Sim | Microsoft.Web/sites/slots | CurrentAssemblies | Assemblies Atuais | Contagem | Média
Sim | Microsoft.Web/sites/slots | FunctionExecutionCount | Contagem de Execução de Função | Contagem | Total
Sim | Microsoft.Web/sites/slots | FunctionExecutionUnits | Unidades de Execução de Função | Contagem | Total
Sim | Microsoft.Web/sites/slots | Gen0Collections | Coletas de lixo da Ger 0 | Contagem | Total
Sim | Microsoft.Web/sites/slots | Gen1Collections | Coletas de lixo da Ger 1 | Contagem | Total
Sim | Microsoft.Web/sites/slots | Gen2Collections | Coletas de lixo da Ger 2 | Contagem | Total
Sim | Microsoft.Web/sites/slots | Alças | Núm. de Identificadores | Contagem | Média
Sim | Microsoft.Web/sites/slots | HealthCheckStatus | Status da verificação de integridade | Contagem | Média
Sim | Microsoft.Web/sites/slots | Http101 | Http 101 | Contagem | Total
Sim | Microsoft.Web/sites/slots | Http2xx | Http 2xx | Contagem | Total
Sim | Microsoft.Web/sites/slots | Http3xx | Http 3xx | Contagem | Total
Sim | Microsoft.Web/sites/slots | Http401 | Http 401 | Contagem | Total
Sim | Microsoft.Web/sites/slots | Http403 | Http 403 | Contagem | Total
Sim | Microsoft.Web/sites/slots | Http404 | Http 404 | Contagem | Total
Sim | Microsoft.Web/sites/slots | Http406 | Http 406 | Contagem | Total
Sim | Microsoft.Web/sites/slots | Http4xx | Http 4xx | Contagem | Total
Sim | Microsoft.Web/sites/slots | Http5xx | Erros do Servidor Http | Contagem | Total
Sim | Microsoft.Web/sites/slots | HttpResponsetime | Tempo de resposta | Segundos | Média
Sim | Microsoft.Web/sites/slots | IoOtherBytesPerSecond | E/S de outros bytes por segundo | BytesPerSecond | Total
Sim | Microsoft.Web/sites/slots | IoOtherOperationsPerSecond | E/S de outras operações por segundo | BytesPerSecond | Total
Sim | Microsoft.Web/sites/slots | IoReadBytesPerSecond | E/S de bytes de leitura por segundo | BytesPerSecond | Total
Sim | Microsoft.Web/sites/slots | IoReadOperationsPerSecond | E/S de operações de leitura por segundo | BytesPerSecond | Total
Sim | Microsoft.Web/sites/slots | IoWriteBytesPerSecond | E/S de bytes de gravação por segundo | BytesPerSecond | Total
Sim | Microsoft.Web/sites/slots | IoWriteOperationsPerSecond | E/S de operações de gravação por segundo | BytesPerSecond | Total
Sim | Microsoft.Web/sites/slots | MemoryWorkingSet | Conjunto de trabalho de memória | Bytes | Média
Sim | Microsoft.Web/sites/slots | PrivateBytes | Bytes Particulares | Bytes | Média
Sim | Microsoft.Web/sites/slots | Solicitações | Solicitações | Contagem | Total
Sim | Microsoft.Web/sites/slots | RequestsInApplicationQueue | Solicitações na fila do aplicativo | Contagem | Média
Sim | Microsoft.Web/sites/slots | Threads | Contagem de Threads | Contagem | Média
Sim | Microsoft.Web/sites/slots | TotalAppDomains | Total de domínios de aplicativo | Contagem | Média
Sim | Microsoft.Web/sites/slots | TotalAppDomainsUnloaded | Total de domínios de aplicativo descarregados | Contagem | Média
