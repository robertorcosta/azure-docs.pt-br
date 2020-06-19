---
title: Métricas de plataforma do Azure Monitor exportáveis por meio das Configurações de Diagnóstico
description: Lista de métricas disponíveis para cada tipo de recurso com o Azure Monitor.
services: azure-monitor
ms.topic: reference
ms.date: 03/30/2020
ms.subservice: metrics
ms.openlocfilehash: 91fc2c4525ee622064520b0098087d54158bbe9e
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680680"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>Métricas de plataforma do Azure Monitor exportáveis por meio das Configurações de Diagnóstico

O Azure Monitor fornece [métricas de plataforma](data-platform-metrics.md) por padrão sem configuração. Ele fornece várias maneiras de interagir com as métricas da plataforma, incluindo a criação de gráficos dessas métricas no portal, acessá-las por meio da API REST ou consultá-las usando o PowerShell ou a CLI. Consulte [Compatibilidade com as métricas](metrics-supported.md) para obter uma lista completa de métricas de plataforma disponíveis atualmente com o pipeline de métrica consolidado do Azure Monitor. Para consultar e acessar essas métricas, use [2018-01-01 api-version](https://docs.microsoft.com/rest/api/monitor/metricdefinitions). Outras métricas podem estar disponíveis no portal ou usando as APIs herdadas.

Você pode exportar as métricas de plataforma do pipeline do Azure Monitor para outros locais de uma das duas maneiras.
1. Use as [configurações de diagnóstico](diagnostic-settings.md) para enviar para o Log Analytics, os Hubs de Eventos ou o Armazenamento do Azure.
2. Usar a [API REST de métricas](https://docs.microsoft.com/rest/api/monitor/metrics/list)

Devido a complexidades no back-end do Azure Monitor, nem todas as métricas são exportáveis usando configurações de diagnóstico. A tabela a seguir lista quais podem e quais não podem ser exportadas usando as configurações de diagnóstico.

## <a name="change-to-behavior-for-nulls-and-zero-values"></a>Alterar para comportamento de valores nulos e zero 
 
Para as métricas de plataforma que podem ser exportadas por meio de configurações de diagnóstico, há algumas métricas para as quais o Azure Monitor interpreta '0' como 'nulo'. Isso causou alguma confusão entre '0' real (emitido pelo recurso) e '0' interpretado (nulo). Em breve, uma alteração ocorrerá e as métricas de plataforma exportadas por meio das configurações de diagnóstico não exportarão mais '0', a menos que tenham sido realmente emitidas pelo recurso subjacente. 

> [!CAUTION]
> A alteração no comportamento descrito acima está agendada para ocorrer em 1º de junho de 2020.

Observação:

1.  Se você excluir um grupo de recursos ou um recurso específico, os dados de métrica dos recursos efetuados não serão mais enviados para os destinos de exportação da configuração de diagnóstico. Ou seja, ele não aparecerá mais nos Hubs de Eventos, nas Contas de Armazenamento e nos Workspaces do Log Analytics.
2.  Esse aprimoramento estará disponível em todas as nuvens públicas e privadas.
3.  Essa alteração não afetará o comportamento das seguintes experiências: 
   - Logs de recursos de plataforma exportados por meio das Configurações de Diagnóstico
   - Criação de gráficos de métricas no Metrics Explorer
   - Alertas sobre métricas de plataforma
 
## <a name="metrics-exportable-table"></a>Tabelas exportáveis de métricas 

A tabela contém as colunas a seguir. 
- Exportável por meio das Configurações de Diagnóstico? 
- Efetuado por NULL/0 
- ResourceType 
- Métrica 
- MetricDisplayName
- Unidade 
- AggregationType


> [!NOTE]
> A tabela a seguir pode ter uma barra de rolagem horizontal na parte inferior. Se você acreditar que está sem informações, verifique se a barra de rolagem está toda para a esquerda.  


| Exportável por meio das Configurações de Diagnóstico?  | Já emite NULLs |  ResourceType  |  Métrica  |  MetricDisplayName  |  Unidade  |  AggregationType | 
|---|---| ---- | ----- | ------ | ---- | ---- | 
| ****Sim****  | Não |  Microsoft.AnalysisServices/servers  |  CleanerCurrentPrice  |  Memória: Preço atual do limpador  |  Contagem  |  Média | 
| ****Sim****  | Não |  Microsoft.AnalysisServices/servers  |  CleanerMemoryNonshrinkable  |  Memória: Memória do limpador não reduzível  |  Bytes  |  Média | 
| ****Sim****  | Não |  Microsoft.AnalysisServices/servers  |  CleanerMemoryShrinkable  |  Memória: Memória do limpador reduzível  |  Bytes  |  Média | 
| ****Sim****  | Não |  Microsoft.AnalysisServices/servers  |  CommandPoolBusyThreads  |  Threads: Threads ocupados do pool comando  |  Contagem  |  Média | 
| ****Sim****  | Não |  Microsoft.AnalysisServices/servers  |  CommandPoolIdleThreads  |  Threads: Threads ociosos do pool comando  |  Contagem  |  Média | 
| ****Sim****  | Não |  Microsoft.AnalysisServices/servers  |  CommandPoolJobQueueLength  |  Comprimento da fila de trabalho do pool de comando  |  Contagem  |  Média | 
| ****Sim****  | Não |  Microsoft.AnalysisServices/servers  |  CurrentConnections  |  Conexão: Conexões atuais  |  Contagem  |  Média | 
| ****Sim****  | Não |  Microsoft.AnalysisServices/servers  |  CurrentUserSessions  |  Sessões de usuário atuais  |  Contagem  |  Média | 
| ****Sim****  | Não |  Microsoft.AnalysisServices/servers  |  LongParsingBusyThreads  |  Threads: Threads ocupados de análise longa  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  LongParsingIdleThreads  |  Threads: Threads ociosos de análise longa  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  LongParsingJobQueueLength  |  Threads: Tamanho da fila de trabalhos de análise longa  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  mashup_engine_memory_metric  |  Memória do mecanismo M  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  mashup_engine_private_bytes_metric  |  Bytes Particulares do Mecanismo M  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  mashup_engine_qpu_metric  |  QPU do mecanismo M  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  mashup_engine_virtual_bytes_metric  |  Bytes Virtuais do Mecanismo M  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  memory_metric  |  Memória  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  memory_thrashing_metric  |  Sobrecarga de memória  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  MemoryLimitHard  |  Memória: Limite de memória física  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  MemoryLimitHigh  |  Memória: Limite de memória superior  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  MemoryLimitLow  |  Memória: Limite de memória inferior  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  MemoryLimitVertiPaq  |  Memória: Limite de memória VertiPaq  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  MemoryUsage  |  Memória: Uso de Memória  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  private_bytes_metric  |  Bytes Particulares  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  ProcessingPoolBusyIOJobThreads  |  Threads: Threads de trabalho de E/S ocupados do pool de processamento  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  ProcessingPoolBusyNonIOThreads  |  Threads: Threads de trabalho não E/S ocupados do pool de processamento  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  ProcessingPoolIdleIOJobThreads  |  Threads: Threads de trabalho de E/S ociosos do pool de processamento  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  ProcessingPoolIdleNonIOThreads  |  Threads: Threads de trabalho não E/S ociosos do pool de processamento  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  ProcessingPoolIOJobQueueLength  |  Threads: Comprimento da fila de trabalho de E/S do pool de processamento  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  ProcessingPoolJobQueueLength  |  Comprimento da fila de trabalho do pool de processamento  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  qpu_metric  |  QPU  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  QueryPoolBusyThreads  |  Threads ocupados do pool de consulta  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  QueryPoolIdleThreads  |  Threads: Threads ociosos do pool de consultas  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  QueryPoolJobQueueLength  |  Threads: Tamanho da fila de trabalhos do pool consultas  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  Quota  |  Memória: Quota  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  QuotaBlocked  |  Memória: Cota bloqueada  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  RowsConvertedPerSec  |  Processamento: Linhas convertidas por segundo  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  RowsReadPerSec  |  Processamento: Linhas lidas por segundo  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  RowsWrittenPerSec  |  Processamento: Linhas gravadas por segundo  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  ShortParsingBusyThreads  |  Threads: Threads ocupados de análise resumida  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  ShortParsingIdleThreads  |  Threads: Threads ociosos de análise resumida  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  ShortParsingJobQueueLength  |  Threads: Tamanho da fila de trabalhos de análise resumida  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  SuccessfullConnectionsPerSec  |  Conexões bem-sucedidas por segundo  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  TotalConnectionFailures  |  Falhas de conexão totais  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  TotalConnectionRequests  |  Solicitações de conexão totais  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  VertiPaqNonpaged  |  Memória: VertiPaq não paginado  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  VertiPaqPaged  |  Memória: VertiPaq paginado  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  virtual_bytes_metric  |  Bytes Virtuais  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  BackendDuration  |  Duração de Solicitações de Back-end  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.ApiManagement/service  |  Capacity  |  Capacity  |  Porcentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  Duration  |  Duração total de solicitações de gateway  |  Milissegundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  EventHubDroppedEvents  |  Eventos do EventHub Ignorados  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  EventHubRejectedEvents  |  Eventos do EventHub Rejeitados  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  EventHubSuccessfulEvents  |  Eventos do EventHub Bem-sucedidos  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  EventHubThrottledEvents  |  Eventos do EventHub Restringidos  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  EventHubTimedoutEvents  |  Eventos do EventHub com Tempo Limite Excedido  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  EventHubTotalBytesSent  |  Tamanho dos Eventos do EventHub  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  EventHubTotalEvents  |  Eventos Totais do EventHub  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  EventHubTotalFailedEvents  |  Eventos do EventHub com Falha  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  FailedRequests  |  Solicitações de Gateway com Falha (Preterido)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  OtherRequests  |  Outras Solicitações de Gateway (Preterido)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  Requests  |  Requests  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  SuccessfulRequests  |  Solicitações de Gateway com Êxito (Preterido)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  TotalRequests  |  Solicitações Totais de Gateway (Preterido)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  UnauthorizedRequests  |  Solicitações de Gateway Não Autorizadas (Preterido)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  AppCpuUsagePercentage  |  Percentual de Uso de CPU do Aplicativo  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  AppMemoryCommitted  |  Memória de Aplicativo Atribuída  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  AppMemoryMax  |  Memória Máxima do Aplicativo  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  AppMemoryUsed  |  Memória Usada do Aplicativo  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  GCPauseTotalCount  |  Contagem de Pausa do GC  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  GCPauseTotalTime  |  Tempo Total de Pausa do GC  |  Milissegundos  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  MaxOldGenMemoryPoolBytes  |  Tamanho Máximo Disponível dos Dados de Geração Anterior  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  OldGenMemoryPoolBytes  |  Tamanho dos Dados da Geração Anterior  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  OldGenPromotedBytes  |  Promover para Tamanho dos Dados da Geração Anterior  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  SystemCpuUsagePercentage  |  Percentual de Uso da CPU do Sistema  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  TomcatErrorCount  |  Erro Global do Tomcat  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  TomcatReceivedBytes  |  Total de Bytes Recebidos do Tomcat  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  TomcatRequestMaxTime  |  Tempo Máximo de Solicitação do Tomcat  |  Milissegundos  |  Máximo | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  TomcatRequestTotalCount  |  Contagem Total de Solicitações do Tomcat  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  TomcatRequestTotalTime  |  Tempos Totais da Solicitação do Tomcat  |  Milissegundos  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  TomcatResponseAvgTime  |  Tempo Médio da Solicitação do Tomcat  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  TomcatSentBytes  |  Total de Bytes Enviados do Tomcat  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  TomcatSessionActiveCurrentCount  |  Contagem de Sessões Ativas do Tomcat  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  TomcatSessionActiveMaxCount  |  Contagem de Sessões Ativas Máximas do Tomcat  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  TomcatSessionAliveMaxTime  |  Tempo de Sessões Ativas Máximas do Tomcat  |  Milissegundos  |  Máximo | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  TomcatSessionCreatedCount  |  Contagem de Sessões Criadas do Tomcat  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  TomcatSessionExpiredCount  |  Contagem de Sessões Expiradas do Tomcat  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  TomcatSessionRejectedCount  |  Contagem de Sessões Rejeitadas do Tomcat  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  YoungGenPromotedBytes  |  Promover para Tamanho dos Dados da Geração Recente  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Automation/automationAccounts  |  TotalJob  |  Total de trabalhos  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Automation/automationAccounts  |  TotalUpdateDeploymentMachineRuns  |  Total de Execuções de Computador da Implantação de Atualização  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Automation/automationAccounts  |  TotalUpdateDeploymentRuns  |  Total de Execuções da Implantação de Atualização  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Batch/batchAccounts  |  CoreCount  |  Contagem de núcleos dedicados  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Batch/batchAccounts  |  CreatingNodeCount  |  Criação de contagem de nós  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Batch/batchAccounts  |  IdleNodeCount  |  Contagem de nós ociosos  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Batch/batchAccounts  |  JobDeleteCompleteEvent  |  Eventos de conclusão de exclusão do trabalho  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Batch/batchAccounts  |  JobDeleteStartEvent  |  Eventos de início de exclusão do trabalho  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Batch/batchAccounts  |  JobDisableCompleteEvent  |  Eventos de conclusão de desabilitação do trabalho  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Batch/batchAccounts  |  JobDisableStartEvent  |  Eventos de início de desabilitação do trabalho  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Batch/batchAccounts  |  JobStartEvent  |  Eventos de início do trabalho  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Batch/batchAccounts  |  JobTerminateCompleteEvent  |  Eventos de conclusão de encerramento do trabalho  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Batch/batchAccounts  |  JobTerminateStartEvent  |  Eventos de início de encerramento do trabalho  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Batch/batchAccounts  |  LeavingPoolNodeCount  |  Contagem de nós saindo do pool  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Batch/batchAccounts  |  LowPriorityCoreCount  |  Contagem de núcleos LowPriority  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Batch/batchAccounts  |  OfflineNodeCount  |  Contagem de nós off-line  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Batch/batchAccounts  |  PoolCreateEvent  |  Eventos de criação de pool  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Batch/batchAccounts  |  PoolDeleteCompleteEvent  |  Eventos de conclusão de exclusão do pool  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Batch/batchAccounts  |  PoolDeleteStartEvent  |  Eventos de início de exclusão de pool  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Batch/batchAccounts  |  PoolResizeCompleteEvent  |  Eventos de conclusão de redimensionamento de pool  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Batch/batchAccounts  |  PoolResizeStartEvent  |  Eventos de início de redimensionamento de pool  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Batch/batchAccounts  |  PreemptedNodeCount  |  Contagem de nós com preempção  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Batch/batchAccounts  |  RebootingNodeCount  |  Contagem de nós de reinicialização  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Batch/batchAccounts  |  ReimagingNodeCount  |  Contagem de nós refazendo a imagem  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Batch/batchAccounts  |  RunningNodeCount  |  Contagem de nós em execução  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Batch/batchAccounts  |  StartingNodeCount  |  Contagem inicial dos nós  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Batch/batchAccounts  |  StartTaskFailedNodeCount  |  Falha na contagem de nós para a tarefa de inicialização  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Batch/batchAccounts  |  TaskCompleteEvent  |  Eventos de conclusão de tarefa  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Batch/batchAccounts  |  TaskFailEvent  |  Eventos de falha de tarefa  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Batch/batchAccounts  |  TaskStartEvent  |  Eventos da tarefa de inicialização  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Batch/batchAccounts  |  TotalLowPriorityNodeCount  |  Contagem de nós de baixa prioridade  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Batch/batchAccounts  |  TotalNodeCount  |  Contagem de nós dedicados  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Batch/batchAccounts  |  UnusableNodeCount  |  Contagem de nós inutilizáveis  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Batch/batchAccounts  |  WaitingForStartTaskNodeCount  |  Contagem de nós para tarefa de inicialização em espera  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.BatchAI/workspaces  |  Núcleos Ativos  |  Núcleos Ativos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/workspaces  |  Nós Ativos  |  Nós Ativos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/workspaces  |  Núcleos Ociosos  |  Núcleos Ociosos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/workspaces  |  Nós Ociosos  |  Nós Ociosos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/workspaces  |  Trabalho Concluído  |  Trabalho Concluído  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.BatchAI/workspaces  |  Trabalho Enviado  |  Trabalho Enviado  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.BatchAI/workspaces  |  Núcleos em Saída  |  Núcleos em Saída  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/workspaces  |  Nós em Saída  |  Nós em Saída  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/workspaces  |  Núcleos com Preempção  |  Núcleos com Preempção  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/workspaces  |  Nós com Preempção  |  Nós com Preempção  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/workspaces  |  Percentual de Utilização de Cota  |  Percentual de Utilização de Cota  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/workspaces  |  Total de Núcleos  |  Total de Núcleos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/workspaces  |  Total de Nós  |  Total de Nós  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/workspaces  |  Núcleos Inutilizáveis  |  Núcleos Inutilizáveis  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/workspaces  |  Nós Inutilizáveis  |  Nós Inutilizáveis  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  ConnectionAccepted  |  Conexões Aceitas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  ConnectionActive  |  Conexões ativas  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  ConnectionHandled  |  Conexões Manipuladas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  CpuUsagePercentageInDouble  |  Percentual de Uso de CPU  |  Porcentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  IOReadBytes  |  Bytes de Leitura de E/S  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  IOWriteBytes  |  Bytes de Gravação de E/S  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  MemoryLimit  |  Limite de Memória  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  MemoryUsage  |  Uso de Memória  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  MemoryUsagePercentageInDouble  |  Percentual de Uso de Memória  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  PendingTransactions  |  Transações Pendentes  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  ProcessedBlocks  |  Blocos Processados  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  ProcessedTransactions  |  Transações Processadas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  QueuedTransactions  |  Transações em Fila  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  RequestHandled  |  Solicitações Manipuladas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  StorageUsage  |  Uso de Armazenamento  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachehits  |  Acertos do Cache  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachehits0  |  Ocorrências no cache (Fragmento 0)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachehits1  |  Ocorrências no cache (Fragmento 1)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachehits2  |  Ocorrências no cache (Fragmento 2)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachehits3  |  Ocorrências no cache (Fragmento 3)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachehits4  |  Ocorrências no cache (Fragmento 4)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachehits5  |  Ocorrências no cache (Fragmento 5)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachehits6  |  Ocorrências no cache (Fragmento 6)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachehits7  |  Ocorrências no cache (Fragmento 7)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachehits8  |  Ocorrências no cache (Fragmento 8)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachehits9  |  Ocorrências no cache (Fragmento 9)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheLatency  |  Microssegundos de latência de cache (versão prévia)  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachemisses  |  Erros de Cache  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachemisses0  |  Perdas no cache (Fragmento 0)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachemisses1  |  Perdas no cache (Fragmento 1)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachemisses2  |  Perdas no cache (Fragmento 2)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachemisses3  |  Perdas no cache (Fragmento 3)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachemisses4  |  Perdas no cache (Fragmento 4)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachemisses5  |  Perdas no cache (Fragmento 5)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachemisses6  |  Perdas no cache (Fragmento 6)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachemisses7  |  Perdas no cache (Fragmento 7)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachemisses8  |  Perdas no cache (Fragmento 8)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cachemisses9  |  Perdas no cache (Fragmento 9)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheRead  |  Cache Lido  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheRead0  |  Leitura no cache (Fragmento 0)  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheRead1  |  Leitura no cache (Fragmento 1)  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheRead2  |  Leitura no cache (Fragmento 2)  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheRead3  |  Leitura no cache (Fragmento 3)  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheRead4  |  Leitura no cache (Fragmento 4)  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheRead5  |  Leitura no cache (Fragmento 5)  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheRead6  |  Leitura no cache (Fragmento 6)  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheRead7  |  Leitura no cache (Fragmento 7)  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheRead8  |  Leitura no cache (Fragmento 8)  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheRead9  |  Leitura no cache (Fragmento 9)  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheWrite  |  Gravação no Cache  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheWrite0  |  Gravação no cache (Fragmento 0)  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheWrite1  |  Gravação no cache (Fragmento 1)  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheWrite2  |  Gravação no cache (Fragmento 2)  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheWrite3  |  Gravação no cache (Fragmento 3)  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheWrite4  |  Gravação no cache (Fragmento 4)  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheWrite5  |  Gravação no cache (Fragmento 5)  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheWrite6  |  Gravação no cache (Fragmento 6)  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheWrite7  |  Gravação no cache (Fragmento 7)  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheWrite8  |  Gravação no cache (Fragmento 8)  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  cacheWrite9  |  Gravação no cache (Fragmento 9)  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  connectedclients  |  Clientes conectados  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  connectedclients0  |  Clientes conectados (Fragmento 0)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  connectedclients1  |  Clientes conectados (Fragmento 1)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  connectedclients2  |  Clientes conectados (Fragmento 2)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  connectedclients3  |  Clientes conectados (Fragmento 3)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  connectedclients4  |  Clientes conectados (Fragmento 4)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  connectedclients5  |  Clientes conectados (Fragmento 5)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  connectedclients6  |  Clientes conectados (Fragmento 6)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  connectedclients7  |  Clientes conectados (Fragmento 7)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  connectedclients8  |  Clientes conectados (Fragmento 8)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  connectedclients9  |  Clientes conectados (Fragmento 9)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  erros  |  Errors  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  evictedkeys  |  Chaves removidas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  evictedkeys0  |  Chaves removidas (Fragmento 0)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  evictedkeys1  |  Chaves removidas (Fragmento 1)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  evictedkeys2  |  Chaves removidas (Fragmento 2)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  evictedkeys3  |  Chaves removidas (Fragmento 3)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  evictedkeys4  |  Chaves removidas (Fragmento 4)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  evictedkeys5  |  Chaves removidas (Fragmento 5)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  evictedkeys6  |  Chaves removidas (Fragmento 6)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  evictedkeys7  |  Chaves removidas (Fragmento 7)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  evictedkeys8  |  Chaves removidas (Fragmento 8)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  evictedkeys9  |  Chaves removidas (Fragmento 9)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  expiredkeys  |  Chaves expiradas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  expiredkeys0  |  Chaves expiradas (Fragmento 0)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  expiredkeys1  |  Chaves expiradas (Fragmento 1)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  expiredkeys2  |  Chaves expiradas (Fragmento 2)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  expiredkeys3  |  Chaves expiradas (Fragmento 3)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  expiredkeys4  |  Chaves expiradas (Fragmento 4)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  expiredkeys5  |  Chaves expiradas (Fragmento 5)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  expiredkeys6  |  Chaves expiradas (Fragmento 6)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  expiredkeys7  |  Chaves expiradas (Fragmento 7)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  expiredkeys8  |  Chaves expiradas (Fragmento 8)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  expiredkeys9  |  Chaves expiradas (Fragmento 9)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  getcommands  |  Gets  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  getcommands0  |  Gets (Fragmento 0)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  getcommands1  |  Gets (Fragmento 1)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  getcommands2  |  Gets (Fragmento 2)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  getcommands3  |  Gets (Fragmento 3)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  getcommands4  |  Gets (Fragmento 4)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  getcommands5  |  Gets (Fragmento 5)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  getcommands6  |  Gets (Fragmento 6)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  getcommands7  |  Gets (Fragmento 7)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  getcommands8  |  Gets (Fragmento 8)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  getcommands9  |  Gets (Fragmento 9)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  operationsPerSecond  |  Operations por segundo  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  operationsPerSecond0  |  Operações por segundo (fragmento 0)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  operationsPerSecond1  |  Operações por segundo (fragmento 1)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  operationsPerSecond2  |  Operações por segundo (fragmento 2)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  operationsPerSecond3  |  Operações por segundo (fragmento 3)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  operationsPerSecond4  |  Operações por segundo (fragmento 4)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  operationsPerSecond5  |  Operações por segundo (fragmento 5)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  operationsPerSecond6  |  Operações por segundo (fragmento 6)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  operationsPerSecond7  |  Operações por segundo (fragmento 7)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  operationsPerSecond8  |  Operações por segundo (fragmento 8)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  operationsPerSecond9  |  Operações por segundo (fragmento 9)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  percentProcessorTime  |  CPU  |  Porcentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  percentProcessorTime0  |  CPU (Fragmento 0)  |  Porcentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  percentProcessorTime1  |  CPU (Fragmento 1)  |  Porcentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  percentProcessorTime2  |  CPU (Fragmento 2)  |  Porcentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  percentProcessorTime3  |  CPU (Fragmento 3)  |  Porcentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  percentProcessorTime4  |  CPU (Fragmento 4)  |  Porcentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  percentProcessorTime5  |  CPU (Fragmento 5)  |  Porcentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  percentProcessorTime6  |  CPU (Fragmento 6)  |  Porcentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  percentProcessorTime7  |  CPU (Fragmento 7)  |  Porcentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  percentProcessorTime8  |  CPU (Fragmento 8)  |  Porcentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  percentProcessorTime9  |  CPU (Fragmento 9)  |  Porcentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  serverLoad  |  Carga do Servidor  |  Porcentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  serverLoad0  |  Carga do servidor (Fragmento 0)  |  Porcentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  serverLoad1  |  Carga do servidor (Fragmento 1)  |  Porcentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  serverLoad2  |  Carga do servidor (Fragmento 2)  |  Porcentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  serverLoad3  |  Carga do servidor (Fragmento 3)  |  Porcentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  serverLoad4  |  Carga do servidor (Fragmento 4)  |  Porcentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  serverLoad5  |  Carga do servidor (Fragmento 5)  |  Porcentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  serverLoad6  |  Carga do servidor (Fragmento 6)  |  Porcentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  serverLoad7  |  Carga do servidor (Fragmento 7)  |  Porcentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  serverLoad8  |  Carga do servidor (Fragmento 8)  |  Porcentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  serverLoad9  |  Carga do servidor (Fragmento 9)  |  Porcentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  setcommands  |  Conjuntos  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  setcommands0  |  Sets (Fragmento 0)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  setcommands1  |  Sets (Fragmento 1)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  setcommands2  |  Sets (Fragmento 2)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  setcommands3  |  Sets (Fragmento 3)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  setcommands4  |  Sets (Fragmento 4)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  setcommands5  |  Sets (Fragmento 5)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  setcommands6  |  Sets (Fragmento 6)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  setcommands7  |  Sets (Fragmento 7)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  setcommands8  |  Sets (Fragmento 8)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  setcommands9  |  Sets (Fragmento 9)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totalcommandsprocessed  |  Total de Operações  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totalcommandsprocessed0  |  Total de operações (Fragmento 0)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totalcommandsprocessed1  |  Total de operações (Fragmento 1)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totalcommandsprocessed2  |  Total de operações (Fragmento 2)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totalcommandsprocessed3  |  Total de operações (Fragmento 3)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totalcommandsprocessed4  |  Total de operações (Fragmento 4)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totalcommandsprocessed5  |  Total de operações (Fragmento 5)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totalcommandsprocessed6  |  Total de operações (Fragmento 6)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totalcommandsprocessed7  |  Total de operações (Fragmento 7)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totalcommandsprocessed8  |  Total de operações (Fragmento 8)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totalcommandsprocessed9  |  Total de operações (Fragmento 9)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totalkeys  |  Total de chaves  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totalkeys0  |  Total de Chaves (Fragmento 0)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totalkeys1  |  Total de Chaves (Fragmento 1)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totalkeys2  |  Total de Chaves (Fragmento 2)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totalkeys3  |  Total de Chaves (Fragmento 3)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totalkeys4  |  Total de Chaves (Fragmento 4)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totalkeys5  |  Total de Chaves (Fragmento 5)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totalkeys6  |  Total de Chaves (Fragmento 6)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totalkeys7  |  Total de Chaves (Fragmento 7)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totalkeys8  |  Total de Chaves (Fragmento 8)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  totalkeys9  |  Total de Chaves (Fragmento 9)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usedmemory  |  Memória Usada  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usedmemory0  |  Memória usada (Fragmento 0)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usedmemory1  |  Memória usada (Fragmento 1)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usedmemory2  |  Memória usada (Fragmento 2)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usedmemory3  |  Memória usada (Fragmento 3)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usedmemory4  |  Memória usada (Fragmento 4)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usedmemory5  |  Memória usada (Fragmento 5)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usedmemory6  |  Memória usada (Fragmento 6)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usedmemory7  |  Memória usada (Fragmento 7)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usedmemory8  |  Memória usada (Fragmento 8)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usedmemory9  |  Memória usada (Fragmento 9)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usedmemorypercentage  |  Porcentagem de memória utilizada  |  Porcentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usedmemoryRss  |  Memória RSS Usada  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usedmemoryRss0  |  Memória RSS usada (Fragmento 0)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usedmemoryRss1  |  Memória RSS usada (Fragmento 1)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usedmemoryRss2  |  Memória RSS usada (Fragmento 2)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usedmemoryRss3  |  Memória RSS usada (Fragmento 3)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usedmemoryRss4  |  Memória RSS usada (Fragmento 4)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usedmemoryRss5  |  Memória RSS usada (Fragmento 5)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usedmemoryRss6  |  Memória RSS usada (Fragmento 6)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usedmemoryRss7  |  Memória RSS usada (Fragmento 7)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usedmemoryRss8  |  Memória RSS usada (Fragmento 8)  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Cache/redis  |  usedmemoryRss9  |  Memória RSS usada (Fragmento 9)  |  Bytes  |  Máximo | 
| Não  | Não |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Disk Read Bytes/Sec  |  Leitura de disco  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Operações de leitura de disco/Seg  |  Operações de leitura de disco/Seg  |  CountPerSecond  |  Média | 
| Não  | Não |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Disk Write Bytes/Sec  |  Gravação de disco  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Operações de gravação de disco/Seg  |  Operações de gravação de disco/Seg  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Entrada na rede  |  Entrada na rede  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Saída da rede  |  Saída da rede  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicCompute/domainNames/slots/roles  |  Porcentagem de CPU  |  Porcentagem de CPU  |  Porcentagem  |  Média | 
| Não  | Não |  Microsoft.ClassicCompute/virtualMachines  |  Disk Read Bytes/Sec  |  Leitura de disco  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicCompute/virtualMachines  |  Operações de leitura de disco/Seg  |  Operações de leitura de disco/Seg  |  CountPerSecond  |  Média | 
| Não  | Não |  Microsoft.ClassicCompute/virtualMachines  |  Disk Write Bytes/Sec  |  Gravação de disco  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicCompute/virtualMachines  |  Operações de gravação de disco/Seg  |  Operações de gravação de disco/Seg  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicCompute/virtualMachines  |  Entrada na rede  |  Entrada na rede  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicCompute/virtualMachines  |  Saída da rede  |  Saída da rede  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicCompute/virtualMachines  |  Porcentagem de CPU  |  Porcentagem de CPU  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts  |  Disponibilidade  |  Disponibilidade  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts  |  Saída  |  Saída  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts  |  Entrada  |  Entrada  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts  |  SuccessE2ELatency  |  Latência de E2E com Sucesso  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts  |  SuccessServerLatency  |  Latência de Servidor Com Sucesso  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts  |  Transactions  |  Transactions  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.ClassicStorage/storageAccounts  |  UsedCapacity  |  Capacidade utilizada  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Disponibilidade  |  Disponibilidade  |  Porcentagem  |  Média | 
| Não  | Não |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  BlobCapacity  |  Capacidade do Blob  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  BlobCount  |  Contagem de Blobs  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  ContainerCount  |  Contagem de Contêineres de Blobs  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Saída  |  Saída  |  Bytes  |  Total | 
| Não  | Não |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  IndexCapacity  |  Capacidade de Índice  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Entrada  |  Entrada  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  SuccessE2ELatency  |  Latência de E2E com Sucesso  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  SuccessServerLatency  |  Latência de Servidor Com Sucesso  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Transactions  |  Transactions  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  Disponibilidade  |  Disponibilidade  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  Saída  |  Saída  |  Bytes  |  Total | 
| Não  | Não |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  FileCapacity  |  Capacidade do Arquivo  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  FileCount  |  Contagem de Arquivos  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  FileShareCount  |  Contagem de Compartilhamentos de Arquivos  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  FileShareQuota  |  Tamanho da cota do compartilhamento de arquivo  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  FileShareSnapshotCount  |  Contagem de Instantâneos do Compartilhamento de Arquivo  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  FileShareSnapshotSize  |  Tamanho do Instantâneo do Compartilhamento de Arquivo  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  Entrada  |  Entrada  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  SuccessE2ELatency  |  Latência de E2E com Sucesso  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  SuccessServerLatency  |  Latência de Servidor Com Sucesso  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  Transactions  |  Transactions  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Disponibilidade  |  Disponibilidade  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Saída  |  Saída  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Entrada  |  Entrada  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  QueueCapacity  |  Capacidade da Fila  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  QueueCount  |  Contagem de Filas  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  QueueMessageCount  |  Contagem de Mensagens da Fila  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  SuccessE2ELatency  |  Latência de E2E com Sucesso  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  SuccessServerLatency  |  Latência de Servidor Com Sucesso  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Transactions  |  Transactions  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Disponibilidade  |  Disponibilidade  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Saída  |  Saída  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Entrada  |  Entrada  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  SuccessE2ELatency  |  Latência de E2E com Sucesso  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  SuccessServerLatency  |  Latência de Servidor Com Sucesso  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  TableCapacity  |  Capacidade da Tabela  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  TableCount  |  Contagem de Tabelas  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  TableEntityCount  |  Contagem de Entidades de Tabela  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Transactions  |  Transactions  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.CognitiveServices/accounts  |  BlockedCalls  |  Chamadas Bloqueadas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.CognitiveServices/accounts  |  CharactersTrained  |  Caracteres Treinados  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.CognitiveServices/accounts  |  CharactersTranslated  |  Caracteres traduzidos  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.CognitiveServices/accounts  |  ClientErrors  |  Erros do Cliente  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.CognitiveServices/accounts  |  DataIn  |  Entrada de Dados  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.CognitiveServices/accounts  |  DataOut  |  Saída de dados  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.CognitiveServices/accounts  |  Latency  |  Latency  |  MilliSeconds  |  Média | 
| **Sim**  | Não |  Microsoft.CognitiveServices/accounts  |  ServerErrors  |  Erros do Servidor  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.CognitiveServices/accounts  |  SpeechSessionDuration  |  Duração da Sessão de Fala  |  Segundos  |  Total | 
| **Sim**  | Não |  Microsoft.CognitiveServices/accounts  |  SuccessfulCalls  |  Chamadas com Êxito  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.CognitiveServices/accounts  |  TotalCalls  |  Total de Chamadas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.CognitiveServices/accounts  |  TotalErrors  |  Total de Erros  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.CognitiveServices/accounts  |  TotalTokenCalls  |  Total de chamadas de token  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.CognitiveServices/accounts  |  TotalTransactions  |  Total de Transações  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Créditos de CPU Consumidos  |  Créditos de CPU Consumidos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Créditos de CPU Restantes  |  Créditos de CPU Restantes  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Profundidade da Fila de Disco de Dados  |  Profundidade da Fila de Disco de Dados (Versão prévia)  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Bytes de Leitura do Disco de Dados/s  |  Bytes de Leitura do Disco de Dados/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Operações de Leitura do Disco de Dados/s  |  Operações de Leitura do Disco de Dados/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Bytes de Gravação do Disco de Dados/s  |  Bytes de Gravação do Disco de Dados/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Operações de Gravação do Disco de Dados/s  |  Operações de Gravação do Disco de Dados/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Bytes de leitura de disco  |  Bytes de leitura de disco  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Operações de leitura de disco/Seg  |  Operações de leitura de disco/Seg  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Bytes de gravação de disco  |  Bytes de gravação de disco  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Operações de gravação de disco/Seg  |  Operações de gravação de disco/Seg  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Fluxos de Entrada  |  Fluxos de Entrada  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Taxa Máxima de Criação de Fluxos de Entrada  |  Taxa Máxima de Criação de Fluxos de Entrada (Versão prévia)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Entrada na rede  |  Rede em Faturável (Preterido)  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Rede no Total  |  Rede no Total  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Saída da rede  |  Rede Fora de Faturável (Preterido)  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Rede Fora do Total  |  Rede Fora do Total  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Profundidade da Fila de Disco do SO  |  Profundidade da Fila de Disco do SO (Versão prévia)  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Bytes de Leitura do Disco do SO/s  |  Bytes de Leitura do Disco do SO/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Operações de Leitura do Disco do SO/s  |  Operações de Leitura do Disco do SO/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Bytes de Gravação do Disco do SO/s  |  Bytes de Gravação do Disco do SO/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Operações de Gravação do Disco do SO/s  |  Operações de Gravação do Disco do SO/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Os Por QD de Disco  |  QD de Disco do SO (Preterido)  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  SO Por Bytes de Leitura do Disco/s  |  Bytes de Leitura do Disco do SO/s (Preterido)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  SO Por Operações de Leitura do Disco/s  |  Operações de Leitura do Disco do SO/s (Preterido)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  SO Por Bytes de Gravação do Disco/s  |  Bytes de Gravação do Disco do SO/s (Preterido)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  SO Por Operações de Gravação do Disco/s  |  Operações de Gravação do Disco do SO/s (Preterido)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Fluxos de Saída  |  Fluxos de Saída  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Taxa Máxima de Criação de Fluxos de Saída  |  Taxa Máxima de Criação de Fluxos de Saída (Versão prévia)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Por QD de Disco  |  QD do Disco de Dados (Preterido)  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Por Bytes de Leitura do Disco/s  |  Bytes de Leitura do Disco de Dados/s (Preterido)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Por operações de Leitura de Disco/s  |  Operações de Leitura do Disco de Dados/s (Preterido)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Por Bytes de Gravação de Disco/s  |  Bytes de Gravação do Disco de Dados/s (Preterido)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Por Operações de Gravação de Disco/s  |  Operações de Gravação do Disco de Dados/s (Preterido)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Porcentagem de CPU  |  Porcentagem de CPU  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Ocorrência de Leitura do Cache de Disco de Dados Premium  |  Ocorrência de Leitura do Cache de Disco de Dados Premium (Versão prévia)  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Ausência de Leitura do Cache de Disco de Dados Premium  |  Ausência de Leitura do Cache de Disco de Dados Premium (Versão prévia)  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Ocorrência de Leitura do Cache de Disco do SO Premium  |  Ocorrência de Leitura do Cache de Disco do SO Premium (Versão prévia)  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Ausência de Leitura do Cache de Disco de SO Premium  |  Ausência de Leitura do Cache de Disco de SO Premium (Versão prévia)  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Créditos de CPU Consumidos  |  Créditos de CPU Consumidos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Créditos de CPU Restantes  |  Créditos de CPU Restantes  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Profundidade da Fila de Disco de Dados  |  Profundidade da Fila de Disco de Dados (Versão prévia)  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Bytes de Leitura do Disco de Dados/s  |  Bytes de Leitura do Disco de Dados/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Operações de Leitura do Disco de Dados/s  |  Operações de Leitura do Disco de Dados/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Bytes de Gravação do Disco de Dados/s  |  Bytes de Gravação do Disco de Dados/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Operações de Gravação do Disco de Dados/s  |  Operações de Gravação do Disco de Dados/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Bytes de leitura de disco  |  Bytes de leitura de disco  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Operações de leitura de disco/Seg  |  Operações de leitura de disco/Seg  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Bytes de gravação de disco  |  Bytes de gravação de disco  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Operações de gravação de disco/Seg  |  Operações de gravação de disco/Seg  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Fluxos de Entrada  |  Fluxos de Entrada  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Taxa Máxima de Criação de Fluxos de Entrada  |  Taxa Máxima de Criação de Fluxos de Entrada (Versão prévia)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Entrada na rede  |  Rede em Faturável (Preterido)  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Rede no Total  |  Rede no Total  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Saída da rede  |  Rede Fora de Faturável (Preterido)  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Rede Fora do Total  |  Rede Fora do Total  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Profundidade da Fila de Disco do SO  |  Profundidade da Fila de Disco do SO (Versão prévia)  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Bytes de Leitura do Disco do SO/s  |  Bytes de Leitura do Disco do SO/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Operações de Leitura do Disco do SO/s  |  Operações de Leitura do Disco do SO/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Bytes de Gravação do Disco do SO/s  |  Bytes de Gravação do Disco do SO/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Operações de Gravação do Disco do SO/s  |  Operações de Gravação do Disco do SO/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Os Por QD de Disco  |  QD de Disco do SO (Preterido)  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  SO Por Bytes de Leitura do Disco/s  |  Bytes de Leitura do Disco do SO/s (Preterido)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  SO Por Operações de Leitura do Disco/s  |  Operações de Leitura do Disco do SO/s (Preterido)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  SO Por Bytes de Gravação do Disco/s  |  Bytes de Gravação do Disco do SO/s (Preterido)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  SO Por Operações de Gravação do Disco/s  |  Operações de Gravação do Disco do SO/s (Preterido)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Fluxos de Saída  |  Fluxos de Saída  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Taxa Máxima de Criação de Fluxos de Saída  |  Taxa Máxima de Criação de Fluxos de Saída (Versão prévia)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Por QD de Disco  |  QD do Disco de Dados (Preterido)  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Por Bytes de Leitura do Disco/s  |  Bytes de Leitura do Disco de Dados/s (Preterido)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Por operações de Leitura de Disco/s  |  Operações de Leitura do Disco de Dados/s (Preterido)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Por Bytes de Gravação de Disco/s  |  Bytes de Gravação do Disco de Dados/s (Preterido)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Por Operações de Gravação de Disco/s  |  Operações de Gravação do Disco de Dados/s (Preterido)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Porcentagem de CPU  |  Porcentagem de CPU  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Ocorrência de Leitura do Cache de Disco de Dados Premium  |  Ocorrência de Leitura do Cache de Disco de Dados Premium (Versão prévia)  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Ausência de Leitura do Cache de Disco de Dados Premium  |  Ausência de Leitura do Cache de Disco de Dados Premium (Versão prévia)  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Ocorrência de Leitura do Cache de Disco do SO Premium  |  Ocorrência de Leitura do Cache de Disco do SO Premium (Versão prévia)  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Ausência de Leitura do Cache de Disco de SO Premium  |  Ausência de Leitura do Cache de Disco de SO Premium (Versão prévia)  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Créditos de CPU Consumidos  |  Créditos de CPU Consumidos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Créditos de CPU Restantes  |  Créditos de CPU Restantes  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Profundidade da Fila de Disco de Dados  |  Profundidade da Fila de Disco de Dados (Versão prévia)  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bytes de Leitura do Disco de Dados/s  |  Bytes de Leitura do Disco de Dados/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operações de Leitura do Disco de Dados/s  |  Operações de Leitura do Disco de Dados/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bytes de Gravação do Disco de Dados/s  |  Bytes de Gravação do Disco de Dados/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operações de Gravação do Disco de Dados/s  |  Operações de Gravação do Disco de Dados/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bytes de leitura de disco  |  Bytes de leitura de disco  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operações de leitura de disco/Seg  |  Operações de leitura de disco/Seg  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bytes de gravação de disco  |  Bytes de gravação de disco  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operações de gravação de disco/Seg  |  Operações de gravação de disco/Seg  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Fluxos de Entrada  |  Fluxos de Entrada  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Taxa Máxima de Criação de Fluxos de Entrada  |  Taxa Máxima de Criação de Fluxos de Entrada (Versão prévia)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Entrada na rede  |  Rede em Faturável (Preterido)  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Rede no Total  |  Rede no Total  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Saída da rede  |  Rede Fora de Faturável (Preterido)  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Rede Fora do Total  |  Rede Fora do Total  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Profundidade da Fila de Disco do SO  |  Profundidade da Fila de Disco do SO (Versão prévia)  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bytes de Leitura do Disco do SO/s  |  Bytes de Leitura do Disco do SO/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operações de Leitura do Disco do SO/s  |  Operações de Leitura do Disco do SO/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bytes de Gravação do Disco do SO/s  |  Bytes de Gravação do Disco do SO/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operações de Gravação do Disco do SO/s  |  Operações de Gravação do Disco do SO/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Os Por QD de Disco  |  QD de Disco do SO (Preterido)  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  SO Por Bytes de Leitura do Disco/s  |  Bytes de Leitura do Disco do SO/s (Preterido)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  SO Por Operações de Leitura do Disco/s  |  Operações de Leitura do Disco do SO/s (Preterido)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  SO Por Bytes de Gravação do Disco/s  |  Bytes de Gravação do Disco do SO/s (Preterido)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  SO Por Operações de Gravação do Disco/s  |  Operações de Gravação do Disco do SO/s (Preterido)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Fluxos de Saída  |  Fluxos de Saída  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Taxa Máxima de Criação de Fluxos de Saída  |  Taxa Máxima de Criação de Fluxos de Saída (Versão prévia)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Por QD de Disco  |  QD do Disco de Dados (Preterido)  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Por Bytes de Leitura do Disco/s  |  Bytes de Leitura do Disco de Dados/s (Preterido)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Por operações de Leitura de Disco/s  |  Operações de Leitura do Disco de Dados/s (Preterido)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Por Bytes de Gravação de Disco/s  |  Bytes de Gravação do Disco de Dados/s (Preterido)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Por Operações de Gravação de Disco/s  |  Operações de Gravação do Disco de Dados/s (Preterido)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Porcentagem de CPU  |  Porcentagem de CPU  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Ocorrência de Leitura do Cache de Disco de Dados Premium  |  Ocorrência de Leitura do Cache de Disco de Dados Premium (Versão prévia)  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Ausência de Leitura do Cache de Disco de Dados Premium  |  Ausência de Leitura do Cache de Disco de Dados Premium (Versão prévia)  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Ocorrência de Leitura do Cache de Disco do SO Premium  |  Ocorrência de Leitura do Cache de Disco do SO Premium (Versão prévia)  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Ausência de Leitura do Cache de Disco de SO Premium  |  Ausência de Leitura do Cache de Disco de SO Premium (Versão prévia)  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.ContainerInstance/containerGroups  |  CpuUsage  |  Uso da CPU  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.ContainerInstance/containerGroups  |  MemoryUsage  |  Uso de Memória  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.ContainerInstance/containerGroups  |  NetworkBytesReceivedPerSecond  |  Bytes de Rede Recebidos por Segundo  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.ContainerInstance/containerGroups  |  NetworkBytesTransmittedPerSecond  |  Bytes de Rede Transmitidos por Segundo  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.ContainerRegistry/registries  |  RunDuration  |  Duração da Execução  |  Milissegundos  |  Total | 
| **Sim**  | Não |  Microsoft.ContainerRegistry/registries  |  SuccessfulPullCount  |  Contagem de Pull com Êxito  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.ContainerRegistry/registries  |  SuccessfulPushCount  |  Contagem de Push com Êxito  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.ContainerRegistry/registries  |  TotalPullCount  |  Contagem Total de Pull  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.ContainerRegistry/registries  |  TotalPushCount  |  Contagem Total de Push  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ContainerService/managedClusters  |  kube_node_status_allocatable_cpu_cores  |  Número total de núcleos de cpu disponíveis em um cluster gerenciado  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ContainerService/managedClusters  |  kube_node_status_allocatable_memory_bytes  |  Número total de memória disponível em um cluster gerenciado  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.ContainerService/managedClusters  |  kube_node_status_condition  |  Status para as várias condições de nó  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ContainerService/managedClusters  |  kube_pod_status_phase  |  Número de pods por fase  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ContainerService/managedClusters  |  kube_pod_status_ready  |  Número de compartimentos no estado Pronto  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  AvailableCapacity  |  Capacidade Disponível  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  BytesUploadedToCloud  |  Bytes Carregados na Nuvem (Dispositivo)  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  BytesUploadedToCloudPerShare  |  Bytes Carregados na Nuvem (Compartilhamento)  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudReadThroughput  |  Taxa de Transferência de Download na Nuvem  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudReadThroughputPerShare  |  Taxa de Transferência de Downloads na Nuvem (Compartilhamento)  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudUploadThroughput  |  Taxa de Transferência de Uploads na Nuvem  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudUploadThroughputPerShare  |  Taxa de Transferência de Uploads na Nuvem (Compartilhamento)  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  HyperVMemoryUtilization  |  Computação de Borda – Uso de Memória  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  HyperVVirtualProcessorUtilization  |  Computação de Borda – Percentual de CPU  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  NICReadThroughput  |  Taxa de Transferência de Leitura (Rede)  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  NICWriteThroughput  |  Taxa de Transferência de Gravações (Rede)  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  TotalCapacity  |  Capacidade Total  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.DataFactory/datafactories  |  FailedRuns  |  Execuções com falha  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.DataFactory/datafactories  |  SuccessfulRuns  |  Execuções com êxito  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.DataFactory/factories  |  ActivityCancelledRuns  |  Métricas de execuções de atividades canceladas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.DataFactory/factories  |  ActivityFailedRuns  |  Métricas de execução de atividades com falha  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.DataFactory/factories  |  ActivitySucceededRuns  |  Métricas de execução de atividades bem-sucedidas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.DataFactory/factories  |  FactorySizeInGbUnits  |  Tamanho total do alocador (unidade GB)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.DataFactory/factories  |  IntegrationRuntimeAvailableMemory  |  Memória disponível de runtime de integração  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.DataFactory/factories  |  IntegrationRuntimeAverageTaskPickupDelay  |  Duração da fila do runtime de integração  |  Segundos  |  Média | 
| **Sim**  | Não |  Microsoft.DataFactory/factories  |  IntegrationRuntimeCpuPercentage  |  Utilização de CPU de runtime de integração  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.DataFactory/factories  |  IntegrationRuntimeQueueLength  |  Tamanho da fila do runtime de integração  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.DataFactory/factories  |  MaxAllowedFactorySizeInGbUnits  |  Tamanho máximo de alocador permitido (unidade GB)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.DataFactory/factories  |  MaxAllowedResourceCount  |  Contagem máxima de entidades permitidas  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.DataFactory/factories  |  PipelineCancelledRuns  |  Métricas de execução do pipeline canceladas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.DataFactory/factories  |  PipelineFailedRuns  |  Métricas de execução do pipeline com falha  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.DataFactory/factories  |  PipelineSucceededRuns  |  Métricas de execução do pipeline bem-sucedido  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.DataFactory/factories  |  ResourceCount  |  Contagem total de entidades  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.DataFactory/factories  |  TriggerCancelledRuns  |  Métricas de execuções do gatilho canceladas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.DataFactory/factories  |  TriggerFailedRuns  |  Métricas de execuções do gatilho com falha  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.DataFactory/factories  |  TriggerSucceededRuns  |  Métricas de execuções do gatilho bem-sucedidas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.DataLakeAnalytics/accounts  |  JobAUEndedCancelled  |  Tempo de AU cancelada  |  Segundos  |  Total | 
| **Sim**  | Não |  Microsoft.DataLakeAnalytics/accounts  |  JobAUEndedFailure  |  Tempo de AU com falha  |  Segundos  |  Total | 
| **Sim**  | Não |  Microsoft.DataLakeAnalytics/accounts  |  JobAUEndedSuccess  |  Tempo de AU com êxito  |  Segundos  |  Total | 
| **Sim**  | Não |  Microsoft.DataLakeAnalytics/accounts  |  JobEndedCancelled  |  Trabalhos cancelados  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.DataLakeAnalytics/accounts  |  JobEndedFailure  |  Trabalhos com falha  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.DataLakeAnalytics/accounts  |  JobEndedSuccess  |  Trabalhos com êxito  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.DataLakeStore/accounts  |  DataRead  |  Leitura de dados  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.DataLakeStore/accounts  |  DataWritten  |  Dados gravados  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.DataLakeStore/accounts  |  ReadRequests  |  Solicitações de leitura  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.DataLakeStore/accounts  |  TotalStorage  |  Armazenamento total  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.DataLakeStore/accounts  |  WriteRequests  |  Solicitações de gravação  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servers  |  active_connections  |  Conexões ativas  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servers  |  backup_storage_used  |  Armazenamento de Backup usado  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servers  |  connections_failed  |  Conexões com falha  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servers  |  cpu_percent  |  Porcentagem de CPU  |  Porcentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servers  |  io_consumption_percent  |  Porcentagem de E/S  |  Porcentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servers  |  memory_percent  |  Porcentagem de memória  |  Porcentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servers  |  network_bytes_egress  |  Saída da rede  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servers  |  network_bytes_ingress  |  Entrada na rede  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servers  |  seconds_behind_master  |  Retardo de replicação em segundos  |  Contagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servers  |  serverlog_storage_limit  |  Limite de armazenamento do Log do Servidor  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servers  |  serverlog_storage_percent  |  Porcentagem de armazenamento do Log do Servidor  |  Porcentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servers  |  serverlog_storage_usage  |  Armazenamento do Log do Servidor usado  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servers  |  storage_limit  |  Limite de armazenamento  |  Bytes  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servers  |  storage_percent  |  Porcentagem de armazenamento  |  Porcentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servers  |  storage_used  |  Armazenamento usado  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servers  |  active_connections  |  Conexões ativas  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servers  |  backup_storage_used  |  Armazenamento de Backup usado  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servers  |  connections_failed  |  Conexões com falha  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servers  |  cpu_percent  |  Porcentagem de CPU  |  Porcentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servers  |  io_consumption_percent  |  Porcentagem de E/S  |  Porcentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servers  |  memory_percent  |  Porcentagem de memória  |  Porcentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servers  |  network_bytes_egress  |  Saída da rede  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servers  |  network_bytes_ingress  |  Entrada na rede  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servers  |  seconds_behind_master  |  Retardo de replicação em segundos  |  Contagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servers  |  serverlog_storage_limit  |  Limite de armazenamento do Log do Servidor  |  Bytes  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servers  |  serverlog_storage_percent  |  Porcentagem de armazenamento do Log do Servidor  |  Porcentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servers  |  serverlog_storage_usage  |  Armazenamento do Log do Servidor usado  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servers  |  storage_limit  |  Limite de armazenamento  |  Bytes  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servers  |  storage_percent  |  Porcentagem de armazenamento  |  Porcentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servers  |  storage_used  |  Armazenamento usado  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servers  |  active_connections  |  Conexões ativas  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servers  |  backup_storage_used  |  Armazenamento de Backup usado  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servers  |  connections_failed  |  Conexões com falha  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servers  |  cpu_percent  |  Porcentagem de CPU  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servers  |  io_consumption_percent  |  Porcentagem de E/S  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servers  |  memory_percent  |  Porcentagem de memória  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servers  |  network_bytes_egress  |  Saída da rede  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servers  |  network_bytes_ingress  |  Entrada na rede  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servers  |  pg_replica_log_delay_in_bytes  |  Retardo Máximo entre Réplicas  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servers  |  pg_replica_log_delay_in_seconds  |  Retardo da Réplica  |  Segundos  |  Máximo | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servers  |  serverlog_storage_limit  |  Limite de armazenamento do Log do Servidor  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servers  |  serverlog_storage_percent  |  Porcentagem de armazenamento do Log do Servidor  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servers  |  serverlog_storage_usage  |  Armazenamento do Log do Servidor usado  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servers  |  storage_limit  |  Limite de armazenamento  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servers  |  storage_percent  |  Porcentagem de armazenamento  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servers  |  storage_used  |  Armazenamento usado  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/serversv2  |  active_connections  |  Conexões ativas  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/serversv2  |  cpu_percent  |  Porcentagem de CPU  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/serversv2  |  iops  |  IOPS  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/serversv2  |  memory_percent  |  Porcentagem de memória  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/serversv2  |  network_bytes_egress  |  Saída da rede  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/serversv2  |  network_bytes_ingress  |  Entrada na rede  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/serversv2  |  storage_percent  |  Porcentagem de armazenamento  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/serversv2  |  storage_used  |  Armazenamento usado  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/Account  |  digitaltwins.telemetry.nodes  |  Espaço Reservado para Telemetria do Nó dos Gêmeos Digitais  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.abandon.success  |  Mensagens C2D abandonadas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.complete.success  |  Entregas de mensagem C2D concluídas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.reject.success  |  Mensagens C2D rejeitadas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  c2d.methods.failure  |  Invocações de método diretas com falhas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  c2d.methods.requestSize  |  Tamanho da solicitação das invocações de método diretas  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  c2d.methods.responseSize  |  Tamanho da resposta das invocações de método diretas  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  c2d.methods.success  |  Invocações de método diretas bem-sucedidas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.failure  |  Leituras de gêmeos de back-end com falhas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.size  |  Tamanho da resposta das leituras de gêmeos de back-end  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.success  |  Leituras de gêmeos de back-end bem-sucedidas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.failure  |  Atualizações de gêmeos de back-end com falhas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.size  |  Tamanho das atualizações de gêmeos de back-end  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.success  |  Atualizações de gêmeos de back-end bem-sucedidas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  C2DMessagesExpired  |  Mensagens C2D expiradas (versão prévia)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  configurações  |  Métricas de configuração  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Devices/IotHubs  |  connectedDeviceCount  |  Dispositivos conectados (versão prévia)  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.builtIn.events  |  Roteamento: mensagens entregues a mensagens/eventos  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.eventHubs  |  Roteamento: mensagens entregues ao Hub de Eventos  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.serviceBusQueues  |  Roteamento: mensagens entregues à Fila do Barramento de Serviço  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.serviceBusTopics  |  Roteamento: mensagens entregues ao Tópico do Barramento de Serviço  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.storage  |  Roteamento: mensagens entregues ao armazenamento  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.storage.blobs  |  Roteamento: blobs entregues ao armazenamento  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.storage.bytes  |  Roteamento: dados entregues ao armazenamento  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.Endpoints.Latency.builtIn.Events  |  Roteamento: latência de mensagem para mensagens/eventos  |  Milissegundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.eventHubs  |  Roteamento: latência de mensagem para o Hub de Eventos  |  Milissegundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.serviceBusQueues  |  Roteamento: latência de mensagem para a Fila do Barramento de Serviço  |  Milissegundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.serviceBusTopics  |  Roteamento: latência de mensagem para o Tópico do Barramento de Serviço  |  Milissegundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.storage  |  Roteamento: latência de mensagem para armazenamento  |  Milissegundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.dropped  |  Roteamento: mensagens de telemetria removidas   |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.fallback  |  Roteamento: mensagens entregues ao fallback  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.invalid  |  Roteamento: mensagens de telemetria incompatíveis  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.orphaned  |  Roteamento: mensagens de telemetria órfãs   |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.success  |  Roteamento: mensagens de telemetria entregues  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.ingress.allProtocol  |  Tentativas de envio de mensagem de telemetria  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.ingress.sendThrottle  |  Número de erros de limitação  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.ingress.success  |  Mensagens de telemetria enviadas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.failure  |  Leituras de gêmeos dos dispositivos com falhas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.size  |  Tamanho da resposta das leituras de gêmeos dos dispositivos  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.success  |  Leituras de gêmeos dos dispositivos bem-sucedidas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.failure  |  Atualizações de gêmeos dos dispositivos com falhas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.size  |  Tamanho das atualizações de gêmeos dos dispositivos  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.success  |  Atualizações de gêmeos dos dispositivos bem-sucedidas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  dailyMessageQuotaUsed  |  Número total de mensagens usadas  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  deviceDataUsage  |  Uso total de dados do dispositivo  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  deviceDataUsageV2  |  Uso total de dados de dispositivos (versão prévia)  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  devices.connectedDevices.allProtocol  |  Dispositivos conectados (preteridos)   |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  devices.totalDevices  |  Total de dispositivos (preterido)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  EventGridDeliveries  |  Entregas da Grade de Eventos (versão prévia)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  EventGridLatency  |  Latência da Grade de Eventos (versão prévia)  |  Milissegundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  jobs.cancelJob.failure  |  Cancelamentos de trabalho com falhas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  jobs.cancelJob.success  |  Cancelamentos de trabalho bem-sucedidos  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  jobs.completed  |  Trabalhos concluídos  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  jobs.createDirectMethodJob.failure  |  Criações de trabalhos de invocação de método com falhas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  jobs.createDirectMethodJob.success  |  Criações de trabalhos de invocação de método bem-sucedidas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  jobs.createTwinUpdateJob.failure  |  Criações de trabalhos de atualização de gêmeos com falhas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  jobs.createTwinUpdateJob.success  |  Criações de trabalhos de atualização de gêmeos bem-sucedidas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  jobs.failed  |  Trabalhos com falha  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  jobs.listJobs.failure  |  Chamadas para listar trabalhos com falhas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  jobs.listJobs.success  |  Chamadas para listar trabalhos bem-sucedidas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  jobs.queryJobs.failure  |  Consultas de trabalho com falhas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  jobs.queryJobs.success  |  Consultas de trabalho bem-sucedidas  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Devices/IotHubs  |  totalDeviceCount  |  Total de dispositivos (versão prévia)  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  twinQueries.failure  |  Consultas de gêmeos com falhas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  twinQueries.resultSize  |  Tamanho do resultado das consultas de gêmeos  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  twinQueries.success  |  Consultas de gêmeos bem-sucedidas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/provisioningServices  |  AttestationAttempts  |  Tentativas de atestado  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/provisioningServices  |  DeviceAssignments  |  Dispositivos atribuídos  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/provisioningServices  |  RegistrationAttempts  |  Tentativas de Registro  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.DocumentDB/databaseAccounts  |  AvailableStorage  |  Armazenamento disponível  |  Bytes  |  Total | 
| Não  | Não |  Microsoft.DocumentDB/databaseAccounts  |  CassandraConnectionClosures  |  Fechamentos de Conexão do Cassandra  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.DocumentDB/databaseAccounts  |  CassandraRequestCharges  |  Encargos de Solicitação do Cassandra  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.DocumentDB/databaseAccounts  |  CassandraRequests  |  Solicitações do Cassandra  |  Contagem  |  Contagem | 
| Não  | Não |  Microsoft.DocumentDB/databaseAccounts  |  DataUsage  |  Uso de dados  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.DocumentDB/databaseAccounts  |  DeleteVirtualNetwork  |  DeleteVirtualNetwork  |  Contagem  |  Contagem | 
| Não  | Não |  Microsoft.DocumentDB/databaseAccounts  |  DocumentCount  |  Contagem de documentos  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.DocumentDB/databaseAccounts  |  DocumentQuota  |  Cota de documento  |  Bytes  |  Total | 
| Não  | Não |  Microsoft.DocumentDB/databaseAccounts  |  IndexUsage  |  Uso do índice  |  Bytes  |  Total | 
| Não  | Não |  Microsoft.DocumentDB/databaseAccounts  |  MetadataRequests  |  MetadataRequests  |  Contagem  |  Contagem | 
| **Sim**  | **Sim** |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestCharge  |  MongoRequestCharge  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequests  |  Solicitações do Mongo  |  Contagem  |  Contagem | 
| Não  | Não |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsCount  |  Taxa de Solicitação do Mongo  |  CountPerSecond  |  Média | 
| Não  | Não |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsDelete  |  Taxa de Solicitação de Exclusão do Mongo  |  CountPerSecond  |  Média | 
| Não  | Não |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsInsert  |  Taxa de Solicitação de Inserção do Mongo  |  CountPerSecond  |  Média | 
| Não  | Não |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsQuery  |  Taxa de Solicitação de Consulta do Mongo  |  CountPerSecond  |  Média | 
| Não  | Não |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsUpdate  |  Taxa de Solicitação de Atualização do Mongo  |  CountPerSecond  |  Média | 
| Não  | Não |  Microsoft.DocumentDB/databaseAccounts  |  ProvisionedThroughput  |  Taxa de transferência provisionada  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.DocumentDB/databaseAccounts  |  ReplicationLatency  |  Latência de Replicação P99  |  MilliSeconds  |  Média | 
| Não  | Não |  Microsoft.DocumentDB/databaseAccounts  |  ServiceAvailability  |  Disponibilidade do serviço  |  Porcentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DocumentDB/databaseAccounts  |  TotalRequests  |  Total de Solicitações  |  Contagem  |  Contagem | 
| **Sim**  | **Sim** |  Microsoft.DocumentDB/databaseAccounts  |  TotalRequestUnits  |  Total de Unidades Solicitadas  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EnterpriseKnowledgeGraph/services  |  FailureCount  |  Falha na Contagem  |  Contagem  |  Contagem | 
| Não  | Não |  Microsoft.EnterpriseKnowledgeGraph/services  |  SuccessCount  |  Contagem Bem-sucedida  |  Contagem  |  Contagem | 
| Não  | Não |  Microsoft.EnterpriseKnowledgeGraph/services  |  SuccessLatency  |  Latência de Êxito  |  MilliSeconds  |  Média | 
| Não  | Não |  Microsoft.EnterpriseKnowledgeGraph/services  |  TransactionCount  |  Contagem de Transações  |  Contagem  |  Contagem | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/domains  |  DeadLetteredCount  |  Eventos de mensagens mortas  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventGrid/domains  |  DeliveryAttemptFailCount  |  Eventos com falha de entrega  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/domains  |  DeliverySuccessCount  |  Eventos entregues  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventGrid/domains  |  DestinationProcessingDurationInMs  |  Duração do processamento de destino  |  Milissegundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/domains  |  DroppedEventCount  |  Eventos removidos  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/domains  |  MatchedEventCount  |  Eventos correspondentes  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/domains  |  PublishFailCount  |  Publicar Eventos com Falha  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/domains  |  PublishSuccessCount  |  Eventos publicados  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/domains  |  PublishSuccessLatencyInMs  |  Publicar Latência de Êxito  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/eventSubscriptions  |  DeadLetteredCount  |  Eventos de mensagens mortas  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventGrid/eventSubscriptions  |  DeliveryAttemptFailCount  |  Eventos com falha de entrega  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/eventSubscriptions  |  DeliverySuccessCount  |  Eventos entregues  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventGrid/eventSubscriptions  |  DestinationProcessingDurationInMs  |  Duração do processamento de destino  |  Milissegundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/eventSubscriptions  |  DroppedEventCount  |  Eventos removidos  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/eventSubscriptions  |  MatchedEventCount  |  Eventos correspondentes  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/extensionTopics  |  PublishFailCount  |  Publicar Eventos com Falha  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/extensionTopics  |  PublishSuccessCount  |  Eventos publicados  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/extensionTopics  |  PublishSuccessLatencyInMs  |  Publicar Latência de Êxito  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/extensionTopics  |  UnmatchedEventCount  |  Eventos sem correspondência  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/topics  |  PublishFailCount  |  Publicar Eventos com Falha  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/topics  |  PublishSuccessCount  |  Eventos publicados  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/topics  |  PublishSuccessLatencyInMs  |  Publicar Latência de Êxito  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/topics  |  UnmatchedEventCount  |  Eventos sem correspondência  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventHub/clusters  |  ActiveConnections  |  ActiveConnections  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.EventHub/clusters  |  AvailableMemory  |  Memória disponível  |  Porcentagem  |  Máximo | 
| Não  | Não |  Microsoft.EventHub/clusters  |  CaptureBacklog  |  Lista de Pendências de Captura.  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventHub/clusters  |  CapturedBytes  |  Bytes Capturados.  |  Bytes  |  Total | 
| Não  | Não |  Microsoft.EventHub/clusters  |  CapturedMessages  |  Mensagens Capturadas.  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventHub/clusters  |  ConnectionsClosed  |  Conexões Fechadas.  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.EventHub/clusters  |  ConnectionsOpened  |  Conexões Abertas.  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.EventHub/clusters  |  CPU  |  CPU  |  Porcentagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.EventHub/clusters  |  IncomingBytes  |  Bytes de Entrada.  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/clusters  |  IncomingMessages  |  Mensagens de entrada  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/clusters  |  IncomingRequests  |  Solicitações de entrada  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/clusters  |  OutgoingBytes  |  Bytes de Saída.  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/clusters  |  OutgoingMessages  |  Mensagens de saída  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventHub/clusters  |  QuotaExceededErrors  |  Cota de Erros Excedida.  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventHub/clusters  |  ServerErrors  |  Erros do Servidor.  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventHub/clusters  |  SuccessfulRequests  |  Solicitações bem sucedidas  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventHub/clusters  |  ThrottledRequests  |  Solicitações Limitadas.  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventHub/clusters  |  UserErrors  |  Erros de Usuário.  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventHub/namespaces  |  ActiveConnections  |  ActiveConnections  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.EventHub/namespaces  |  CaptureBacklog  |  Lista de Pendências de Captura.  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventHub/namespaces  |  CapturedBytes  |  Bytes Capturados.  |  Bytes  |  Total | 
| Não  | Não |  Microsoft.EventHub/namespaces  |  CapturedMessages  |  Mensagens Capturadas.  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventHub/namespaces  |  ConnectionsClosed  |  Conexões Fechadas.  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.EventHub/namespaces  |  ConnectionsOpened  |  Conexões Abertas.  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.EventHub/namespaces  |  EHABL  |  Arquivar mensagens da lista de pendências (Preterido)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/namespaces  |  EHAMBS  |  Arquivar taxa de transferência de mensagem (Preterido)  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/namespaces  |  EHAMSGS  |  Arquivar mensagens (Preterido)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/namespaces  |  EHINBYTES  |  Bytes de entrada (Preterida)  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/namespaces  |  EHINMBS  |  Bytes de entrada (obsoleto) (Preterido)  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/namespaces  |  EHINMSGS  |  Mensagens de Entrada (Preterida)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/namespaces  |  EHOUTBYTES  |  Bytes de saída (Preterida)  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/namespaces  |  EHOUTMBS  |  Bytes de saída (obsoleto) (Preterido)  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/namespaces  |  EHOUTMSGS  |  Mensagens de Saída (Preterida)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/namespaces  |  FAILREQ  |  Solicitações com Falha (Preterido)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/namespaces  |  IncomingBytes  |  Bytes de Entrada.  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/namespaces  |  IncomingMessages  |  Mensagens de entrada  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/namespaces  |  IncomingRequests  |  Solicitações de entrada  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/namespaces  |  INMSGS  |  Mensagens de entrada (obsoleto) (Preterido)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/namespaces  |  INREQS  |  Solicitações de Entrada (Preterido)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/namespaces  |  INTERR  |  Erros Internos do Servidor (Preterido)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/namespaces  |  MISCERR  |  Outros Erros (Preterido)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/namespaces  |  OutgoingBytes  |  Bytes de Saída.  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/namespaces  |  OutgoingMessages  |  Mensagens de saída  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/namespaces  |  OUTMSGS  |  Mensagens de Saída (obsoleto) (Preterido)  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventHub/namespaces  |  QuotaExceededErrors  |  Cota de Erros Excedida.  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventHub/namespaces  |  ServerErrors  |  Erros do Servidor.  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventHub/namespaces  |  Tamanho  |  Tamanho  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.EventHub/namespaces  |  SuccessfulRequests  |  Solicitações bem sucedidas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/namespaces  |  SUCCREQ  |  Solicitações Bem-sucedidas (Preterido)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/namespaces  |  SVRBSY  |  Erros de Servidor Ocupado (Preterido)  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventHub/namespaces  |  ThrottledRequests  |  Solicitações Limitadas.  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventHub/namespaces  |  UserErrors  |  Erros de Usuário.  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.HDInsight/clusters  |  CategorizedGatewayRequests  |  Solicitações de gateway categorizadas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.HDInsight/clusters  |  GatewayRequests  |  Solicitações de gateway  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.HDInsight/clusters  |  NumActiveWorkers  |  Número de Trabalhos Ativos  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.HDInsight/clusters  |  ScalingRequests  |  Solicitações de Escala  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Insights/AutoscaleSettings  |  MetricThreshold  |  Limite da Métrica  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Insights/AutoscaleSettings  |  ObservedCapacity  |  Capacidade Observada  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Insights/AutoscaleSettings  |  ObservedMetricValue  |  Valor de Métrica Observado  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Insights/AutoscaleSettings  |  ScaleActionsInitiated  |  Ações de Escala Iniciadas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Insights/Components  |  availabilityResults/availabilityPercentage  |  Disponibilidade  |  Porcentagem  |  Média | 
| Não  | Não |  Microsoft.Insights/Components  |  availabilityResults/count  |  Testes de disponibilidade  |  Contagem  |  Contagem | 
| **Sim**  | Não |  Microsoft.Insights/Components  |  availabilityResults/duration  |  Duração do teste de disponibilidade  |  MilliSeconds  |  Média | 
| **Sim**  | Não |  Microsoft.Insights/Components  |  browserTimings/networkDuration  |  Tempo de conexão de rede de carregamento de página  |  MilliSeconds  |  Média | 
| **Sim**  | Não |  Microsoft.Insights/Components  |  browserTimings/processingDuration  |  Tempo de processamento do cliente  |  MilliSeconds  |  Média | 
| **Sim**  | Não |  Microsoft.Insights/Components  |  browserTimings/receiveDuration  |  Tempo de resposta de recebimento  |  MilliSeconds  |  Média | 
| **Sim**  | Não |  Microsoft.Insights/Components  |  browserTimings/sendDuration  |  Tempo de solicitação de envio  |  MilliSeconds  |  Média | 
| **Sim**  | Não |  Microsoft.Insights/Components  |  browserTimings/totalDuration  |  Tempo de carregamento de página do navegador  |  MilliSeconds  |  Média | 
| Não  | Não |  Microsoft.Insights/Components  |  dependencies/count  |  Chamadas de dependência  |  Contagem  |  Contagem | 
| **Sim**  | Não |  Microsoft.Insights/Components  |  dependencies/duration  |  Duração da dependência  |  MilliSeconds  |  Média | 
| Não  | Não |  Microsoft.Insights/Components  |  dependencies/failed  |  Falhas de chamada de dependência  |  Contagem  |  Contagem | 
| Não  | Não |  Microsoft.Insights/Components  |  exceptions/browser  |  Exceções de navegador  |  Contagem  |  Contagem | 
| **Sim**  | **Sim** |  Microsoft.Insights/Components  |  exceptions/count  |  Exceções  |  Contagem  |  Contagem | 
| Não  | Não |  Microsoft.Insights/Components  |  exceptions/server  |  Exceções do servidor  |  Contagem  |  Contagem | 
| **Sim**  | **Sim** |  Microsoft.Insights/Components  |  pageViews/count  |  Visualizações de página  |  Contagem  |  Contagem | 
| **Sim**  | Não |  Microsoft.Insights/Components  |  pageViews/duration  |  Tempo de carregamento de exibição de página  |  MilliSeconds  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Insights/Components  |  performanceCounters/exceptionsPerSecond  |  Taxa de exceção  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Insights/Components  |  performanceCounters/memoryAvailableBytes  |  Memória disponível  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Insights/Components  |  performanceCounters/processCpuPercentage  |  CPU do processo  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Insights/Components  |  performanceCounters/processIOBytesPerSecond  |  Taxa de E/S de processamento  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Insights/Components  |  performanceCounters/processorCpuPercentage  |  Tempo do processador  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Insights/Components  |  performanceCounters/processPrivateBytes  |  Processar bytes particulares  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Insights/Components  |  performanceCounters/requestExecutionTime  |  Tempo de execução de solicitação HTTP  |  MilliSeconds  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Insights/Components  |  performanceCounters/requestsInQueue  |  Solicitações HTTP na fila do aplicativo  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Insights/Components  |  performanceCounters/requestsPerSecond  |  Taxa de solicitação HTTP  |  CountPerSecond  |  Média | 
| Não  | Não |  Microsoft.Insights/Components  |  requests/count  |  Solicitações do servidor  |  Contagem  |  Contagem | 
| **Sim**  | Não |  Microsoft.Insights/Components  |  requests/duration  |  Tempo de resposta do servidor  |  MilliSeconds  |  Média | 
| Não  | Não |  Microsoft.Insights/Components  |  requests/failed  |  Solicitações falhas  |  Contagem  |  Contagem | 
| Não  | Não |  Microsoft.Insights/Components  |  solicitações/taxa  |  Taxa de solicitação do servidor  |  CountPerSecond  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Insights/Components  |  traces/count  |  Rastreamentos  |  Contagem  |  Contagem | 
| **Sim**  | Não |  Microsoft.KeyVault/vaults  |  ServiceApiHit  |  Total de ocorrências da API de Serviço  |  Contagem  |  Contagem | 
| **Sim**  | Não |  Microsoft.KeyVault/vaults  |  ServiceApiLatency  |  Latência geral da API de Serviço  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.KeyVault/vaults  |  ServiceApiResult  |  Total de resultados da API de Serviço  |  Contagem  |  Contagem | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  CacheUtilization  |  Utilização do cache  |  Porcentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  ContinuousExportMaxLatenessMinutes  |  Minutos de Atraso Máximo da Exportação Contínua  |  Contagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  ContinuousExportNumOfRecordsExported  |  Exportação Contínua – número de registros exportados  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  ContinuousExportPendingCount  |  Contagem de Exportação Contínua Pendente  |  Contagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  ContinuousExportResult  |  Resultado da Exportação Contínua  |  Contagem  |  Contagem | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  CPU  |  CPU  |  Porcentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  EventsProcessedForEventHubs  |  Eventos processados (para os Hubs IoT/de Eventos)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  ExportUtilization  |  Utilização da Exportação  |  Porcentagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  IngestionLatencyInSeconds  |  Latência de ingestão (em segundos)  |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  IngestionResult  |  Resultados da ingestão  |  Contagem  |  Contagem | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  IngestionUtilization  |  Utilização da ingestão  |  Porcentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  IngestionVolumeInMB  |  Volume de ingestão (em MB)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  KeepAlive  |  Keep alive  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  QueryDuration  |  Duração da consulta  |  Milissegundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  SteamingIngestRequestRate  |  Taxa de Solicitação de Ingestão de Streaming  |  Contagem  |  RateRequestsPerSecond | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  StreamingIngestDataRate  |  Taxa de Dados de Ingestão de Streaming  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  StreamingIngestDuration  |  Duração da Ingestão de Streaming  |  Milissegundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  StreamingIngestResults  |  Resultado da Ingestão de Streaming  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Logic/integrationServiceEnvironments  |  ActionLatency  |  Latência da ação   |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionsCompleted  |  Ações concluídas   |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionsFailed  |  Ações com falha   |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionsSkipped  |  Ações ignoradas   |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionsStarted  |  Ações iniciadas   |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionsSucceeded  |  Ações bem sucedidas   |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Logic/integrationServiceEnvironments  |  ActionSuccessLatency  |  Latência das ações bem sucedidas   |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionThrottledEvents  |  Eventos de restrição de ações  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentConnectorMemoryUsage  |  Uso de Memória do Conector para o Ambiente de Serviço de Integração  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentConnectorProcessorUsage  |  Uso do Processador de Conector para o Ambiente de Serviço de Integração  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentWorkflowMemoryUsage  |  Uso de Memória do Fluxo de Trabalho para o Ambiente de Serviço de Integração  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentWorkflowProcessorUsage  |  Uso do Processador de Fluxo de Trabalho para o Ambiente de Serviço de Integração  |  Porcentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  RunFailurePercentage  |  Porcentagem de falha de execução  |  Porcentagem  |  Total | 
| **Sim**  | Não |  Microsoft.Logic/integrationServiceEnvironments  |  RunLatency  |  Latência da execução  |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsCancelled  |  Execuções Canceladas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsCompleted  |  Execuções concluídas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsFailed  |  Execuções com falha  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsStarted  |  Execuções iniciadas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsSucceeded  |  Execuções bem sucedidas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  RunStartThrottledEvents  |  Executar Eventos Limitados por Início  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Logic/integrationServiceEnvironments  |  RunSuccessLatency  |  Latência de execução bem sucedida  |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  RunThrottledEvents  |  Eventos de restrição de execução  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerFireLatency  |  Latência do gatilho acionado   |  Segundos  |  Média | 
| **Sim**  | Não |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerLatency  |  Latência do gatilho   |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersCompleted  |  Gatilhos concluídos   |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersFailed  |  Gatilhos com falha   |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersFired  |  Gatilhos acionados   |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersSkipped  |  Gatilhos ignorados  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersStarted  |  Gatilhos iniciados   |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersSucceeded  |  Gatilhos bem sucedidos   |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerSuccessLatency  |  Latência do gatilho bem sucedido   |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerThrottledEvents  |  Eventos de restrição do gatilho  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Logic/workflows  |  ActionLatency  |  Latência da ação   |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  ActionsCompleted  |  Ações concluídas   |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  ActionsFailed  |  Ações com falha   |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  ActionsSkipped  |  Ações ignoradas   |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  ActionsStarted  |  Ações iniciadas   |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  ActionsSucceeded  |  Ações bem sucedidas   |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Logic/workflows  |  ActionSuccessLatency  |  Latência das ações bem sucedidas   |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  ActionThrottledEvents  |  Eventos de restrição de ações  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  BillableActionExecutions  |  Execuções de ação faturáveis  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  BillableTriggerExecutions  |  Execuções de gatilho faturáveis  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  BillingUsageNativeOperation  |  Uso de Cobrança para Execuções de Operação Nativa  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  BillingUsageNativeOperation  |  Uso de Cobrança para Execuções de Operação Nativa  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  BillingUsageStandardConnector  |  Uso de Cobrança para Execuções de Conector Padrão  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  BillingUsageStandardConnector  |  Uso de Cobrança para Execuções de Conector Padrão  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  BillingUsageStorageConsumption  |  Uso de Cobrança para Execuções de Consumo de Armazenamento  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  BillingUsageStorageConsumption  |  Uso de Cobrança para Execuções de Consumo de Armazenamento  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  RunFailurePercentage  |  Porcentagem de falha de execução  |  Porcentagem  |  Total | 
| **Sim**  | Não |  Microsoft.Logic/workflows  |  RunLatency  |  Latência da execução  |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  RunsCancelled  |  Execuções Canceladas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  RunsCompleted  |  Execuções concluídas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  RunsFailed  |  Execuções com falha  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  RunsStarted  |  Execuções iniciadas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  RunsSucceeded  |  Execuções bem sucedidas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  RunStartThrottledEvents  |  Executar Eventos Limitados por Início  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Logic/workflows  |  RunSuccessLatency  |  Latência de execução bem sucedida  |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  RunThrottledEvents  |  Eventos de restrição de execução  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  TotalBillableExecutions  |  Total de execuções faturáveis  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Logic/workflows  |  TriggerFireLatency  |  Latência do gatilho acionado   |  Segundos  |  Média | 
| **Sim**  | Não |  Microsoft.Logic/workflows  |  TriggerLatency  |  Latência do gatilho   |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  TriggersCompleted  |  Gatilhos concluídos   |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  TriggersFailed  |  Gatilhos com falha   |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  TriggersFired  |  Gatilhos acionados   |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  TriggersSkipped  |  Gatilhos ignorados  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  TriggersStarted  |  Gatilhos iniciados   |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  TriggersSucceeded  |  Gatilhos bem sucedidos   |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Logic/workflows  |  TriggerSuccessLatency  |  Latência do gatilho bem sucedido   |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  TriggerThrottledEvents  |  Eventos de restrição do gatilho  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Núcleos Ativos  |  Núcleos Ativos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Nós Ativos  |  Nós Ativos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Execuções Concluídas  |  Execuções Concluídas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Execuções com falha  |  Execuções com falha  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Núcleos Ociosos  |  Núcleos Ociosos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Nós Ociosos  |  Nós Ociosos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Núcleos em Saída  |  Núcleos em Saída  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Nós em Saída  |  Nós em Saída  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Implantação de Modelo com Falha  |  Implantação de Modelo com Falha  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Implantação de Modelo Iniciada  |  Implantação de Modelo Iniciada  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Implantação de Modelo Bem-sucedida  |  Implantação de Modelo Bem-sucedida  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Registro de Modelo com Falha  |  Registro de Modelo com Falha  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Registro de Modelo Bem-sucedido  |  Registro de Modelo Bem-sucedido  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Núcleos com Preempção  |  Núcleos com Preempção  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Nós com Preempção  |  Nós com Preempção  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Percentual de Utilização de Cota  |  Percentual de Utilização de Cota  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Execuções Iniciadas  |  Execuções Iniciadas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Total de Núcleos  |  Total de Núcleos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Total de Nós  |  Total de Nós  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Núcleos Inutilizáveis  |  Núcleos Inutilizáveis  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Nós Inutilizáveis  |  Nós Inutilizáveis  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Maps/accounts  |  Disponibilidade  |  Disponibilidade  |  Porcentagem  |  Média | 
| Não  | Não |  Microsoft.Maps/accounts  |  Uso  |  Uso  |  Contagem  |  Contagem | 
| **Sim**  | Não |  Microsoft.Media/mediaservices  |  AssetCount  |  Contagem de ativos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Media/mediaservices  |  AssetQuota  |  Cota de ativos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Media/mediaservices  |  AssetQuotaUsedPercentage  |  Percentual de cota de ativos usado  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Media/mediaservices  |  ContentKeyPolicyCount  |  Contagem de Política de Chave de Conteúdo  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Media/mediaservices  |  ContentKeyPolicyQuota  |  Cota de Política de Chave de Conteúdo  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Media/mediaservices  |  ContentKeyPolicyQuotaUsedPercentage  |  Percentual de Cota Usada da Política de Chave de Conteúdo  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Media/mediaservices  |  StreamingPolicyCount  |  Contagem de Política de Streaming  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Media/mediaservices  |  StreamingPolicyQuota  |  Cota de Política de Streaming  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Media/mediaservices  |  StreamingPolicyQuotaUsedPercentage  |  Percentual de cota usada da Política de Streaming  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Media/mediaservices/streamingEndpoints  |  Saída  |  Saída  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Media/mediaservices/streamingEndpoints  |  Requests  |  Requests  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Media/mediaservices/streamingEndpoints  |  SuccessE2ELatency  |  Latência de ponta a ponta bem-sucedida  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  GCPauseTotalCount  |  Contagem de Pausa do GC  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  GCPauseTotalTime  |  Tempo Total de Pausa do GC  |  Milissegundos  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  MaxOldGenMemoryPoolBytes  |  Tamanho Máximo Disponível dos Dados de Geração Anterior  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  OldGenMemoryPoolBytes  |  Tamanho dos Dados da Geração Anterior  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  OldGenPromotedBytes  |  Promover para Tamanho dos Dados da Geração Anterior  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  ServiceCpuUsagePercentage  |  Percentual de Uso da CPU do Serviço  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  ServiceMemoryCommitted  |  Memória de Serviço Atribuída  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  ServiceMemoryMax  |  Memória Máxima do Serviço  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  ServiceMemoryUsed  |  Memória do Serviço Usada  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  SystemCpuUsagePercentage  |  Percentual de Uso da CPU do Sistema  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatErrorCount  |  Erro Global do Tomcat  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatReceivedBytes  |  Total de Bytes Recebidos do Tomcat  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatRequestMaxTime  |  Tempo Máximo de Solicitação do Tomcat  |  Milissegundos  |  Máximo | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatRequestTotalCount  |  Contagem Total de Solicitações do Tomcat  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatRequestTotalTime  |  Tempos Totais da Solicitação do Tomcat  |  Milissegundos  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatResponseAvgTime  |  Tempo Médio da Solicitação do Tomcat  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatSentBytes  |  Total de Bytes Enviados do Tomcat  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionActiveCurrentCount  |  Contagem de Sessões Ativas do Tomcat  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionActiveMaxCount  |  Contagem de Sessões Ativas Máximas do Tomcat  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionAliveMaxTime  |  Tempo de Sessões Ativas Máximas do Tomcat  |  Milissegundos  |  Máximo | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionCreatedCount  |  Contagem de Sessões Criadas do Tomcat  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionExpiredCount  |  Contagem de Sessões Expiradas do Tomcat  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionRejectedCount  |  Contagem de Sessões Rejeitadas do Tomcat  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  YoungGenPromotedBytes  |  Promover para Tamanho dos Dados da Geração Recente  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.NetApp/netAppAccounts/capacityPools  |  VolumePoolAllocatedUsed  |  Pool de volume alocado usado  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.NetApp/netAppAccounts/capacityPools  |  VolumePoolTotalLogicalSize  |  Tamanho lógico total do pool de volume  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  AverageReadLatency  |  Latência média de leitura  |  MilliSeconds  |  Média | 
| **Sim**  | Não |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  AverageWriteLatency  |  Latência média de gravação  |  MilliSeconds  |  Média | 
| **Sim**  | Não |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  ReadIops  |  IOPS de leitura  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  VolumeLogicalSize  |  Tamanho do volume lógico  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  VolumeSnapshotSize  |  Tamanho do instantâneo de volume  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  WriteIops  |  IOPS de gravação  |  CountPerSecond  |  Média | 
| Não  | Não |  Microsoft.Network/applicationGateways  |  ApplicationGatewayTotalTime  |  Tempo Total do Gateway de Aplicativo  |  MilliSeconds  |  Média | 
| Não  | Não |  Microsoft.Network/applicationGateways  |  AvgRequestCountPerHealthyHost  |  Solicitações por minuto por Host Íntegro  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.Network/applicationGateways  |  BackendConnectTime  |  Tempo de Conexão de Back-end  |  MilliSeconds  |  Média | 
| Não  | Não |  Microsoft.Network/applicationGateways  |  BackendFirstByteResponseTime  |  Tempo de Resposta do Primeiro Byte de Back-end  |  MilliSeconds  |  Média | 
| Não  | Não |  Microsoft.Network/applicationGateways  |  BackendLastByteResponseTime  |  Tempo de Resposta do Último Byte de Back-end  |  MilliSeconds  |  Média | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  BackendResponseStatus  |  Status da Resposta de Back-end  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  BlockedCount  |  Distribuição de Regra de Solicitações Bloqueadas do Firewall do Aplicativo Web  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  BlockedReqCount  |  Contagem de Solicitações Bloqueadas do Firewall do Aplicativo Web  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  BytesReceived  |  Bytes Recebidos  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  BytesSent  |  Bytes Enviados  |  Bytes  |  Total | 
| Não  | Não |  Microsoft.Network/applicationGateways  |  CapacityUnits  |  Unidades de Capacidade Atuais  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.Network/applicationGateways  |  ClientRtt  |  RTT do Cliente  |  MilliSeconds  |  Média | 
| Não  | Não |  Microsoft.Network/applicationGateways  |  ComputeUnits  |  Unidades de Computação Atuais  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  CurrentConnections  |  Conexões atuais  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  FailedRequests  |  Solicitações com falha  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  HealthyHostCount  |  Contagem de hosts íntegros  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  MatchedCount  |  Distribuição de Regra Total do Firewall do Aplicativo Web  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  ResponseStatus  |  Status da Resposta  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Network/applicationGateways  |  Produtividade  |  Produtividade  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  TlsProtocol  |  Protocolo TLS do Cliente  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  TotalRequests  |  Total de Solicitações  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  UnhealthyHostCount  |  Contagem de hosts não íntegros  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Network/azurefirewalls  |  ApplicationRuleHit  |  Contagem de ocorrências de regras de aplicativo  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/azurefirewalls  |  DataProcessed  |  Dados processados  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Network/azurefirewalls  |  FirewallHealth  |  Estado de integridade do firewall  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Network/azurefirewalls  |  NetworkRuleHit  |  Contagem de ocorrências das regras de rede  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/azurefirewalls  |  SNATPortUtilization  |  Utilização da porta SNAT  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Network/connections  |  BitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Network/connections  |  BitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Network/dnszones  |  QueryVolume  |  Volume de consulta  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Network/dnszones  |  RecordSetCapacityUtilization  |  Utilização de capacidade de Conjuntos de Registros  |  Porcentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/dnszones  |  RecordSetCount  |  Contagem de Conjuntos de Registros  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/expressRouteCircuits  |  ArpAvailability  |  Disponibilidade de ARP  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Network/expressRouteCircuits  |  BgpAvailability  |  Disponibilidade de BGP  |  Porcentagem  |  Média | 
| Não  | Não |  Microsoft.Network/expressRouteCircuits  |  BitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Média | 
| Não  | Não |  Microsoft.Network/expressRouteCircuits  |  BitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Média | 
| Não  | Não |  Microsoft.Network/expressRouteCircuits  |  GlobalReachBitsInPerSecond  |  GlobalReachBitsInPerSecond  |  CountPerSecond  |  Média | 
| Não  | Não |  Microsoft.Network/expressRouteCircuits  |  GlobalReachBitsOutPerSecond  |  GlobalReachBitsOutPerSecond  |  CountPerSecond  |  Média | 
| Não  | Não |  Microsoft.Network/expressRouteCircuits  |  QosDropBitsInPerSecond  |  DroppedInBitsPerSecond  |  CountPerSecond  |  Média | 
| Não  | Não |  Microsoft.Network/expressRouteCircuits  |  QosDropBitsOutPerSecond  |  DroppedOutBitsPerSecond  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Network/expressRouteCircuits/peerings  |  BitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Network/expressRouteCircuits/peerings  |  BitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Média | 
| Não  | Não |  Microsoft.Network/expressRouteGateways  |  ErGatewayConnectionBitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Média | 
| Não  | Não |  Microsoft.Network/expressRouteGateways  |  ErGatewayConnectionBitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Network/expressRoutePorts  |  AdminState  |  AdminState  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Network/expressRoutePorts  |  LineProtocol  |  LineProtocol  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Network/expressRoutePorts  |  PortBitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Network/expressRoutePorts  |  PortBitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Network/expressRoutePorts  |  RxLightLevel  |  RxLightLevel  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Network/expressRoutePorts  |  TxLightLevel  |  TxLightLevel  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Network/frontdoors  |  BackendHealthPercentage  |  Percentual de integridade do back-end  |  Porcentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Network/frontdoors  |  BackendRequestCount  |  Contagem de solicitações de back-end  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Network/frontdoors  |  BackendRequestLatency  |  Latência de solicitação de back-end  |  MilliSeconds  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Network/frontdoors  |  BillableResponseSize  |  Tamanho de Resposta Faturável  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Network/frontdoors  |  RequestCount  |  Contagem de solicitações  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Network/frontdoors  |  RequestSize  |  Tamanho da solicitação  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Network/frontdoors  |  ResponseSize  |  Tamanho da resposta  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Network/frontdoors  |  TotalLatency  |  Latência total  |  MilliSeconds  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Network/frontdoors  |  WebApplicationFirewallRequestCount  |  Contagem de solicitações do Firewall de Aplicativo Web  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Network/loadBalancers  |  AllocatedSnatPorts  |  Portas SNAT alocadas (versão prévia)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/loadBalancers  |  ByteCount  |  Contagem de Bytes  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/loadBalancers  |  DipAvailability  |  Status de investigação de integridade  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Network/loadBalancers  |  PacketCount  |  Contagem de Pacotes  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/loadBalancers  |  SnatConnectionCount  |  Contagem de Conexões SNAT  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/loadBalancers  |  SYNCount  |  Contagem de SYN  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Network/loadBalancers  |  UsedSnatPorts  |  Portas SNAT usadas (versão prévia)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/loadBalancers  |  VipAvailability  |  Disponibilidade do Caminho de Dados  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Network/networkInterfaces  |  BytesReceivedRate  |  Bytes Recebidos  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Network/networkInterfaces  |  BytesSentRate  |  Bytes Enviados  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Network/networkInterfaces  |  PacketsReceivedRate  |  Pacotes Recebidos  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/networkInterfaces  |  PacketsSentRate  |  Pacotes Enviados  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/networkWatchers/connectionMonitors  |  AverageRoundtripMs  |  Média Tempo de ida e volta (ms)  |  MilliSeconds  |  Média | 
| **Sim**  | Não |  Microsoft.Network/networkWatchers/connectionMonitors  |  ChecksFailedPercent  |  Porcentagem de Verificações com Falha (Versão prévia)  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Network/networkWatchers/connectionMonitors  |  ProbesFailedPercent  |  Porcentagem de investigações com falha  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Network/networkWatchers/connectionMonitors  |  RoundTripTimeMs  |  Tempo de ida e volta (ms) (Versão prévia)  |  MilliSeconds  |  Média | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  ByteCount  |  Contagem de Bytes  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  BytesDroppedDDoS  |  DDoS de bytes de entrada removidos  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  BytesForwardedDDoS  |  DDoS de bytes de entrada encaminhados  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  BytesInDDoS  |  DDoS de bytes de entrada  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  DDoSTriggerSYNPackets  |  Pacotes de entrada SYN para disparar a mitigação de DDoS  |  CountPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  DDoSTriggerTCPPackets  |  Pacotes TCP de entrada a disparar a mitigação de DDoS  |  CountPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  DDoSTriggerUDPPackets  |  Pacotes UDP de entrada a disparar a mitigação de DDoS  |  CountPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  IfUnderDDoSAttack  |  Sob ataque DDoS ou não  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  PacketCount  |  Contagem de Pacotes  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  PacketsDroppedDDoS  |  DDoS de pacotes de entrada removidos  |  CountPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  PacketsForwardedDDoS  |  DDoS de pacotes de entrada encaminhados  |  CountPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  PacketsInDDoS  |  DDoS de pacotes de entrada  |  CountPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  SynCount  |  Contagem de SYN  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  TCPBytesDroppedDDoS  |  DDoS de bytes de TCP de entrada removidos  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  TCPBytesForwardedDDoS  |  DDoS de bytes de TCP de entrada encaminhados  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  TCPBytesInDDoS  |  DDoS de bytes de TCP de entrada  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  TCPPacketsDroppedDDoS  |  DDoS de pacotes TCP de entrada removidos  |  CountPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  TCPPacketsForwardedDDoS  |  DDoS de pacotes TCP de entrada encaminhados  |  CountPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  TCPPacketsInDDoS  |  DDoS de pacotes TCP de entrada  |  CountPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  UDPBytesDroppedDDoS  |  DDoS de bytes de UDP de entrada removidos  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  UDPBytesForwardedDDoS  |  DDoS de bytes de UDP de entrada encaminhados  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  UDPBytesInDDoS  |  DDoS de bytes de UDP de entrada  |  BytesPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  UDPPacketsDroppedDDoS  |  DDoS de pacotes UDP de entrada removidos  |  CountPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  UDPPacketsForwardedDDoS  |  DDoS de pacotes UDP de entrada encaminhados  |  CountPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  UDPPacketsInDDoS  |  DDoS de pacotes UDP de entrada  |  CountPerSecond  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/publicIPAddresses  |  VipAvailability  |  Disponibilidade do Caminho de Dados  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Network/trafficManagerProfiles  |  ProbeAgentCurrentEndpointStateByProfileResourceId  |  Status do Ponto de Extremidade por Ponto de Extremidade  |  Contagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.Network/trafficManagerProfiles  |  QpsByEndpoint  |  Consultas pelo Ponto de Extremidade Retornado  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/virtualNetworkGateways  |  AverageBandwidth  |  Largura de banda de S2S do gateway  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Network/virtualNetworkGateways  |  P2SBandwidth  |  Largura de banda de P2S do gateway  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Network/virtualNetworkGateways  |  P2SConnectionCount  |  Contagem de conexões P2S  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/virtualNetworkGateways  |  TunnelAverageBandwidth  |  Largura de Banda de Túnel  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressBytes  |  Bytes de Saída de Túnel  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressPacketDropTSMismatch  |  Remoção de Pacotes Incompatíveis de TS de Saída de Túnel  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressPackets  |  Pacotes de Saída de Túnel  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressBytes  |  Bytes de Entrada de Túnel  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressPacketDropTSMismatch  |  Remoção de Pacotes Incompatíveis de TS de Entrada de Túnel  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressPackets  |  Pacotes de Entrada de Túnel  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/virtualNetworks  |  PingMeshAverageRoundtripMs  |  Tempo de ida e volta para Pings em uma VM  |  MilliSeconds  |  Média | 
| **Sim**  | Não |  Microsoft.Network/virtualNetworks  |  PingMeshProbesFailedPercent  |  Pings com Falha em uma VM  |  Porcentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming  |  Mensagens de entrada  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming.all.failedrequests  |  Todas as solicitações com falha recebidas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming.all.requests  |  Todas as solicitações recebidas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming.scheduled  |  Notificações por push agendadas enviadas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  incoming.scheduled.cancel  |  Notificações por push agendadas canceladas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.all  |  Operações de gerenciamento de instalação  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.delete  |  Excluir operações de instalação  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.get  |  Obter operações de instalação  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.patch  |  Operações de instalação de patch  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.upsert  |  Criar ou atualizar operações de instalação  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  notificationhub.pushes  |  Todas as notificações de saída  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.badorexpiredchannel  |  Erros de canal incorreto ou expirado  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.channelerror  |  Erros de canal  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.invalidpayload  |  Erros de conteúdo  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.pnserror  |  Erros de sistema de notificação externa  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.success  |  Notificações de sucesso  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.badchannel  |  Erro de canal inválido do APNS  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.expiredchannel  |  Erro de canal expirado do APNS  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.invalidcredentials  |  Erros de autorização do APNS  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.invalidnotificationsize  |  Erro de tamanho de notificação inválido do APNS  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.pnserror  |  Erros do APNS  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.success  |  Notificações do APNS bem-sucedidas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.authenticationerror  |  Erros de autenticação do GCM  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.badchannel  |  Erro de canal inválido do GCM  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.expiredchannel  |  Erro de canal expirado do GCM  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.invalidcredentials  |  Erros de autorização do GCM (credenciais inválidas)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.invalidnotificationformat  |  Formato de notificação inválido do GCM  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.invalidnotificationsize  |  Erro de tamanho de notificação inválido do GCM  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.pnserror  |  Erros do GCM  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.success  |  Notificações do GCM bem-sucedidas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.throttled  |  Notificações restritas do GCM  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.wrongchannel  |  Erro de canal incorreto do GCM  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.authenticationerror  |  Erros de autenticação do MPNS  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.badchannel  |  Erro de canal inválido do MPNS  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.channeldisconnected  |  Canal do MPNS desconectado  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.dropped  |  Notificações descartadas do MPNS  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.invalidcredentials  |  Credenciais inválidas do MPNS  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.invalidnotificationformat  |  Erro de tamanho de notificação inválido do MPNS  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.pnserror  |  Erros do MPNS  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.success  |  Notificações de sucesso do MPNS  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.throttled  |  Notificações restritas do MPNS  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.authenticationerror  |  Erros de autenticação do WNS  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.badchannel  |  Erro de canal inválido do WNS  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.channeldisconnected  |  Canal do WNS desconectado  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.channelthrottled  |  Canal do WNS restrito  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.dropped  |  Notificações do WNS descartadas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.expiredchannel  |  Erro de canal expirado do WNS  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidcredentials  |  Erros de autorização do WNS (credenciais inválidas)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidnotificationformat  |  Formato de notificação inválido do WNS  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidnotificationsize  |  Erro de tamanho de notificação inválido do WNS  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidtoken  |  Erros de autorização do WNS (token inválido)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.pnserror  |  Erros do WNS  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.success  |  Notificações do WNS bem-sucedidas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.throttled  |  Notificações restritas do WNS  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.tokenproviderunreachable  |  Erros de autorização do WNS (inacessível)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.wrongtoken  |  Erros de autorização do WNS (token incorreto)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.all  |  Operações de Registro  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.create  |  Operação de criação de registro  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.delete  |  Operação de exclusão de registro  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.get  |  Operações de leitura do registro  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.update  |  Operação de atualização de registro  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  scheduled.pending  |  Notificações agendadas pendentes  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_% Available Memory  |  % de Memória Disponível  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_% Available Swap Space  |  % de Espaço de Permuta Disponível  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_% Committed Bytes In Use  |  % de Bytes Confirmados em Uso  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_% DPC Time  |  % de Tempo de DPC  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_% Free Inodes  |  % de Inodes livres  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_% Free Space  |  % de Espaço Livre  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_% Free Space  |  % de Espaço Livre  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_% Idle Time  |  % de Tempo Ocioso  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_% Interrupt Time  |  % de Tempo de Interrupção  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_% IO Wait Time  |  % de Tempo de Espera de E/S  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_% Nice Time  |  % de Tempo Adequado  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_% Privileged Time  |  % de Tempo Privilegiado  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_% Processor Time  |  % Tempo do Processador  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_% Processor Time  |  % Tempo do Processador  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_% Used Inodes  |  % de Inodes Usados  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_% Used Memory  |  % de Memória Usada  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_% Used Space  |  % de Espaço Usado  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_% Used Swap Space  |  % de Espaço de Permuta Usado  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_% User Time  |  % de Tempo do Usuário  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Available MBytes  |  MBytes Disponíveis  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Available MBytes Memory  |  MBytes de Memória Disponíveis  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Available MBytes Swap  |  MBytes de Espaço de Permuta Disponíveis  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. de segundos/Leitura do Disco  |  Média de segundos/Leitura do Disco  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. de segundos/Leitura do Disco  |  Média de segundos/Leitura do Disco  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. de segundos/Transferência do Disco  |  Média de segundos/Transferência do Disco  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. de segundos/Gravação do Disco  |  Média de segundos/Gravação do Disco  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. de segundos/Gravação do Disco  |  Média de segundos/Gravação do Disco  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Bytes Received/sec  |  Bytes Recebidos/s  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Bytes Sent/sec  |  Bytes Enviados/s  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Bytes Total/sec  |  Bytes Totais/s  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Current Disk Queue Length  |  Comprimento da fila atual de disco  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Read Bytes/sec  |  Bytes Lidos no Disco/s  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Reads/sec  |  Leituras de Disco/s  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Reads/sec  |  Leituras de Disco/s  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Transfers/sec  |  Transferências de Disco/s  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Transfers/sec  |  Transferências de Disco/s  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Write Bytes/sec  |  Bytes Gravados no Disco/s  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Writes/sec  |  Gravações de Disco/s  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Writes/sec  |  Gravações de Disco/s  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Free Megabytes  |  Megabytes Livres  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Free Megabytes  |  Megabytes Livres  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Free Physical Memory  |  Memória Física Livre  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Free Space in Paging Files  |  Espaço Livre em Arquivos de Paginação  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Free Virtual Memory  |  Memória Virtual Livre  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Logical Disk Bytes/sec  |  Bytes de Disco Lógico/s  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Page Reads/sec  |  Leituras de Página/s  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Page Writes/sec  |  Gravações de Página/s  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Pages/sec  |  Páginas/s  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Pct Privileged Time  |  % de Tempo Privilegiado  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Pct User Time  |  % de Tempo do Usuário  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Physical Disk Bytes/sec  |  Bytes/s do Disco Físico  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Processes  |  Processos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Processor Queue Length  |  Tamanho da fila do processador  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Size Stored In Paging Files  |  Tamanho Armazenado em Arquivos de Paginação  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Total Bytes  |  Total de Bytes  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Total Bytes Received  |  Total de Bytes Recebidos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Total Bytes Transmitted  |  Total de Bytes Transmitidos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Total Collisions  |  Total de Colisões  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Total Packets Received  |  Total de Pacotes Recebidos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Total Packets Transmitted  |  Total de Pacotes Transmitidos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Total Rx Errors  |  Total de Erros de Rx  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Total Tx Errors  |  Total de Erros de Tx  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Uptime  |  Tempo de atividade  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Used MBytes Swap Space  |  MBytes de Espaço de Permuta Usado  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Used Memory kBytes  |  KBytes de Memória Usada  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Used Memory MBytes  |  MBytes de Memória Usada  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Users  |  Usuários  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Virtual Shared Memory  |  Memória Virtual Compartilhada  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Evento  |  Evento  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.OperationalInsights/workspaces  |  Pulsação  |  Pulsação  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Atualizar  |  Atualizar  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.PowerBIDedicated/capacities  |  memory_metric  |  Memória  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.PowerBIDedicated/capacities  |  memory_thrashing_metric  |  Sobrecarga de Memória (Conjuntos de dados)  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.PowerBIDedicated/capacities  |  qpu_high_utilization_metric  |  Alta utilização de QPU  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.PowerBIDedicated/capacities  |  QueryDuration  |  Duração da Consulta (Conjuntos de dados)  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.PowerBIDedicated/capacities  |  QueryPoolJobQueueLength  |  Tamanho da Fila de Trabalhos do Pool de Consultas (Conjuntos de dados)  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.Relay/namespaces  |  ActiveConnections  |  ActiveConnections  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Relay/namespaces  |  ActiveListeners  |  ActiveListeners  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Relay/namespaces  |  BytesTransferred  |  BytesTransferred  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Relay/namespaces  |  ListenerConnections-ClientError  |  ListenerConnections-ClientError  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Relay/namespaces  |  ListenerConnections-ServerError  |  ListenerConnections-ServerError  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Relay/namespaces  |  ListenerConnections-Success  |  ListenerConnections-Success  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Relay/namespaces  |  ListenerConnections-TotalRequests  |  ListenerConnections-TotalRequests  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Relay/namespaces  |  ListenerDisconnects  |  ListenerDisconnects  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Relay/namespaces  |  SenderConnections-ClientError  |  SenderConnections-ClientError  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Relay/namespaces  |  SenderConnections-ServerError  |  SenderConnections-ServerError  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Relay/namespaces  |  SenderConnections-Success  |  SenderConnections-Success  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Relay/namespaces  |  SenderConnections-TotalRequests  |  SenderConnections-TotalRequests  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Relay/namespaces  |  SenderDisconnects  |  SenderDisconnects  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Search/searchServices  |  SearchLatency  |  Latência de pesquisa  |  Segundos  |  Média | 
| **Sim**  | Não |  Microsoft.Search/searchServices  |  SearchQueriesPerSecond  |  Consultas de pesquisa por segundo  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Search/searchServices  |  ThrottledSearchQueriesPercentage  |  Porcentagem das consultas de pesquisa limitadas  |  Porcentagem  |  Média | 
| Não  | Não |  Microsoft.ServiceBus/namespaces  |  ActiveConnections  |  ActiveConnections  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.ServiceBus/namespaces  |  ActiveMessages  |  Contagem de mensagens em uma fila/tópico.  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ServiceBus/namespaces  |  ConnectionsClosed  |  Conexões Fechadas.  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ServiceBus/namespaces  |  ConnectionsOpened  |  Conexões Abertas.  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ServiceBus/namespaces  |  CPUXNS  |  CPU (Preterido)  |  Porcentagem  |  Máximo | 
| Não  | Não |  Microsoft.ServiceBus/namespaces  |  DeadletteredMessages  |  Contagem de mensagens mortas em uma Fila/Tópico.  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.ServiceBus/namespaces  |  IncomingMessages  |  Mensagens de entrada  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ServiceBus/namespaces  |  IncomingRequests  |  Solicitações de entrada  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.ServiceBus/namespaces  |  Mensagens  |  Contagem de mensagens em uma fila/tópico.  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ServiceBus/namespaces  |  NamespaceCpuUsage  |  CPU  |  Porcentagem  |  Máximo | 
| Não  | Não |  Microsoft.ServiceBus/namespaces  |  NamespaceMemoryUsage  |  Uso de Memória  |  Porcentagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.ServiceBus/namespaces  |  OutgoingMessages  |  Mensagens de saída  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.ServiceBus/namespaces  |  ScheduledMessages  |  Contagem de mensagens agendadas em uma Fila/Tópico.  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ServiceBus/namespaces  |  ServerErrors  |  Erros do Servidor.  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.ServiceBus/namespaces  |  Tamanho  |  Tamanho  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.ServiceBus/namespaces  |  SuccessfulRequests  |  Solicitações bem sucedidas  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.ServiceBus/namespaces  |  ThrottledRequests  |  Solicitações Limitadas.  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.ServiceBus/namespaces  |  UserErrors  |  Erros de Usuário.  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.ServiceBus/namespaces  |  WSXNS  |  Uso de Memória (Preterido)  |  Porcentagem  |  Máximo | 
| Não  | Não |  Microsoft.ServiceFabricMesh/applications  |  ActualCpu  |  ActualCpu  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ServiceFabricMesh/applications  |  ActualMemory  |  ActualMemory  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.ServiceFabricMesh/applications  |  AllocatedCpu  |  AllocatedCpu  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ServiceFabricMesh/applications  |  AllocatedMemory  |  AllocatedMemory  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.ServiceFabricMesh/applications  |  ApplicationStatus  |  ApplicationStatus  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ServiceFabricMesh/applications  |  ContainerStatus  |  ContainerStatus  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ServiceFabricMesh/applications  |  CpuUtilization  |  CpuUtilization  |  Porcentagem  |  Média | 
| Não  | Não |  Microsoft.ServiceFabricMesh/applications  |  MemoryUtilization  |  MemoryUtilization  |  Porcentagem  |  Média | 
| Não  | Não |  Microsoft.ServiceFabricMesh/applications  |  RestartCount  |  RestartCount  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ServiceFabricMesh/applications  |  ServiceReplicaStatus  |  ServiceReplicaStatus  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ServiceFabricMesh/applications  |  ServiceStatus  |  ServiceStatus  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.SignalRService/SignalR  |  ConnectionCount  |  Contagem de conexão  |  Contagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.SignalRService/SignalR  |  InboundTraffic  |  Tráfego de entrada  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.SignalRService/SignalR  |  MessageCount  |  Contagem de mensagens  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.SignalRService/SignalR  |  OutboundTraffic  |  Tráfego de saída  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.SignalRService/SignalR  |  SystemErrors  |  Erros do sistema  |  Porcentagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.SignalRService/SignalR  |  UserErrors  |  Erros do usuário  |  Porcentagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.Sql/managedInstances  |  avg_cpu_percent  |  Percentual médio de CPU  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/managedInstances  |  io_bytes_read  |  Bytes de E/S lidos  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/managedInstances  |  io_bytes_written  |  Bytes de E/S gravados  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/managedInstances  |  io_requests  |  Contagem de solicitações de E/S  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/managedInstances  |  reserved_storage_mb  |  Espaço de armazenamento reservado  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/managedInstances  |  storage_space_used_mb  |  Espaço de armazenamento usado  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/managedInstances  |  virtual_core_count  |  Contagem de núcleos virtuais  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.Sql/servers  |  database_dtu_consumption_percent  |  Porcentagem de DTU  |  Porcentagem  |  Média | 
| Não  | Não |  Microsoft.Sql/servers  |  database_storage_used  |  Espaço de dados usado  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servers  |  dtu_consumption_percent  |  Porcentagem de DTU  |  Porcentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servers  |  dtu_used  |  DTU usado  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servers  |  storage_used  |  Espaço de dados usado  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servers/databases  |  allocated_data_storage  |  Espaço alocado de dados  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servers/databases  |  app_cpu_billed  |  CPU do aplicativo cobrado  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Sql/servers/databases  |  app_cpu_percent  |  Percentual de CPU do aplicativo  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servers/databases  |  app_memory_percent  |  Percentual de memória do aplicativo  |  Porcentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servers/databases  |  blocked_by_firewall  |  Bloqueado pelo firewall  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Sql/servers/databases  |  cache_hit_percent  |  Percentual de ocorrência no cache  |  Porcentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Sql/servers/databases  |  cache_used_percent  |  Percentual de cache usado  |  Porcentagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.Sql/servers/databases  |  connection_failed  |  Conexões com falha  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Sql/servers/databases  |  connection_successful  |  Conexões bem sucedidas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Sql/servers/databases  |  cpu_limit  |  Limite da CPU  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servers/databases  |  cpu_percent  |  Percentual de CPU  |  Porcentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servers/databases  |  cpu_used  |  CPU usada  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servers/databases  |  deadlock  |  Deadlocks  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Sql/servers/databases  |  dtu_consumption_percent  |  Porcentagem de DTU  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servers/databases  |  dtu_limit  |  Limite de DTU  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servers/databases  |  dtu_used  |  DTU usado  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servers/databases  |  dwu_consumption_percent  |  Porcentagem de DWU  |  Porcentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Sql/servers/databases  |  dwu_limit  |  Limite de DWU  |  Contagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.Sql/servers/databases  |  dwu_used  |  DWU usado  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Sql/servers/databases  |  local_tempdb_usage_percent  |  Porcentagem de local de tempdb  |  Porcentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servers/databases  |  log_write_percent  |  Porcentagem de E/S de log  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servers/databases  |  memory_usage_percent  |  Percentual de memória  |  Porcentagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.Sql/servers/databases  |  physical_data_read_percent  |  Porcentagem de E/S de dados  |  Porcentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servers/databases  |  sessions_percent  |  Porcentagem de sessões  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servers/databases  |  armazenamento  |  Espaço de dados usado  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Sql/servers/databases  |  storage_percent  |  Porcentagem de espaço de dados usado  |  Porcentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Sql/servers/databases  |  tempdb_data_size  |  Quilobytes do Tamanho do Arquivo de Dados Tempdb  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Sql/servers/databases  |  tempdb_log_size  |  Quilobytes do Tamanho do Arquivo de Log Tempdb  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Sql/servers/databases  |  tempdb_log_used_percent  |  Porcentagem de Uso do Log Tempdb  |  Porcentagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.Sql/servers/databases  |  workers_percent  |  Porcentagem de funcionários  |  Porcentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servers/databases  |  xtp_storage_percent  |  Percentual de armazenamento do OLTP na memória  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servers/elasticPools  |  allocated_data_storage  |  Espaço alocado de dados  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servers/elasticPools  |  allocated_data_storage_percent  |  Porcentagem do espaço de dados alocado  |  Porcentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Sql/servers/elasticPools  |  cpu_limit  |  Limite da CPU  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servers/elasticPools  |  cpu_percent  |  Percentual de CPU  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servers/elasticPools  |  cpu_used  |  CPU usada  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.Sql/servers/elasticPools  |  database_allocated_data_storage  |  Espaço alocado de dados  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.Sql/servers/elasticPools  |  database_cpu_limit  |  Limite da CPU  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.Sql/servers/elasticPools  |  database_cpu_percent  |  Percentual de CPU  |  Porcentagem  |  Média | 
| Não  | Não |  Microsoft.Sql/servers/elasticPools  |  database_cpu_used  |  CPU usada  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.Sql/servers/elasticPools  |  database_dtu_consumption_percent  |  Porcentagem de DTU  |  Porcentagem  |  Média | 
| Não  | Não |  Microsoft.Sql/servers/elasticPools  |  database_eDTU_used  |  eDTU usado  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.Sql/servers/elasticPools  |  database_log_write_percent  |  Porcentagem de E/S de log  |  Porcentagem  |  Média | 
| Não  | Não |  Microsoft.Sql/servers/elasticPools  |  database_physical_data_read_percent  |  Porcentagem de E/S de dados  |  Porcentagem  |  Média | 
| Não  | Não |  Microsoft.Sql/servers/elasticPools  |  database_sessions_percent  |  Porcentagem de sessões  |  Porcentagem  |  Média | 
| Não  | Não |  Microsoft.Sql/servers/elasticPools  |  database_storage_used  |  Espaço de dados usado  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.Sql/servers/elasticPools  |  database_workers_percent  |  Porcentagem de funcionários  |  Porcentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servers/elasticPools  |  dtu_consumption_percent  |  Porcentagem de DTU  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servers/elasticPools  |  eDTU_limit  |  Limite de eDTU  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servers/elasticPools  |  eDTU_used  |  eDTU usado  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servers/elasticPools  |  log_write_percent  |  Porcentagem de E/S de log  |  Porcentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servers/elasticPools  |  physical_data_read_percent  |  Porcentagem de E/S de dados  |  Porcentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servers/elasticPools  |  sessions_percent  |  Porcentagem de sessões  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servers/elasticPools  |  storage_limit  |  Tamanho máximo dos dados  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servers/elasticPools  |  storage_percent  |  Porcentagem de espaço de dados usado  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servers/elasticPools  |  storage_used  |  Espaço de dados usado  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servers/elasticPools  |  tempdb_data_size  |  Quilobytes do Tamanho do Arquivo de Dados Tempdb  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Sql/servers/elasticPools  |  tempdb_log_size  |  Quilobytes do Tamanho do Arquivo de Log Tempdb  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Sql/servers/elasticPools  |  tempdb_log_used_percent  |  Porcentagem de Uso do Log Tempdb  |  Porcentagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.Sql/servers/elasticPools  |  workers_percent  |  Porcentagem de funcionários  |  Porcentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servers/elasticPools  |  xtp_storage_percent  |  Percentual de armazenamento do OLTP na memória  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts  |  Disponibilidade  |  Disponibilidade  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts  |  Saída  |  Saída  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts  |  Entrada  |  Entrada  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts  |  SuccessE2ELatency  |  Latência de E2E com Sucesso  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts  |  SuccessServerLatency  |  Latência de Servidor Com Sucesso  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts  |  Transactions  |  Transactions  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Storage/storageAccounts  |  UsedCapacity  |  Capacidade utilizada  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts/blobServices  |  Disponibilidade  |  Disponibilidade  |  Porcentagem  |  Média | 
| Não  | Não |  Microsoft.Storage/storageAccounts/blobServices  |  BlobCapacity  |  Capacidade do Blob  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.Storage/storageAccounts/blobServices  |  BlobCount  |  Contagem de Blobs  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts/blobServices  |  ContainerCount  |  Contagem de Contêineres de Blobs  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts/blobServices  |  Saída  |  Saída  |  Bytes  |  Total | 
| Não  | Não |  Microsoft.Storage/storageAccounts/blobServices  |  IndexCapacity  |  Capacidade de Índice  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts/blobServices  |  Entrada  |  Entrada  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts/blobServices  |  SuccessE2ELatency  |  Latência de E2E com Sucesso  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts/blobServices  |  SuccessServerLatency  |  Latência de Servidor Com Sucesso  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts/blobServices  |  Transactions  |  Transactions  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts/fileServices  |  Disponibilidade  |  Disponibilidade  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts/fileServices  |  Saída  |  Saída  |  Bytes  |  Total | 
| Não  | Não |  Microsoft.Storage/storageAccounts/fileServices  |  FileCapacity  |  Capacidade do Arquivo  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.Storage/storageAccounts/fileServices  |  FileCount  |  Contagem de Arquivos  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareCount  |  Contagem de Compartilhamentos de Arquivos  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareQuota  |  Tamanho da cota do compartilhamento de arquivo  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareSnapshotCount  |  Contagem de instantâneos do compartilhamento de arquivo  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareSnapshotSize  |  Tamanho do instantâneo do compartilhamento de arquivo  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts/fileServices  |  Entrada  |  Entrada  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts/fileServices  |  SuccessE2ELatency  |  Latência de E2E com Sucesso  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts/fileServices  |  SuccessServerLatency  |  Latência de Servidor Com Sucesso  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts/fileServices  |  Transactions  |  Transactions  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts/queueServices  |  Disponibilidade  |  Disponibilidade  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts/queueServices  |  Saída  |  Saída  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts/queueServices  |  Entrada  |  Entrada  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts/queueServices  |  QueueCapacity  |  Capacidade da Fila  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts/queueServices  |  QueueCount  |  Contagem de Filas  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts/queueServices  |  QueueMessageCount  |  Contagem de Mensagens da Fila  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts/queueServices  |  SuccessE2ELatency  |  Latência de E2E com Sucesso  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts/queueServices  |  SuccessServerLatency  |  Latência de Servidor Com Sucesso  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts/queueServices  |  Transactions  |  Transactions  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts/tableServices  |  Disponibilidade  |  Disponibilidade  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts/tableServices  |  Saída  |  Saída  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts/tableServices  |  Entrada  |  Entrada  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts/tableServices  |  SuccessE2ELatency  |  Latência de E2E com Sucesso  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts/tableServices  |  SuccessServerLatency  |  Latência de Servidor Com Sucesso  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts/tableServices  |  TableCapacity  |  Capacidade da Tabela  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts/tableServices  |  TableCount  |  Contagem de Tabelas  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts/tableServices  |  TableEntityCount  |  Contagem de Entidades de Tabela  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts/tableServices  |  Transactions  |  Transactions  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ClientIOPS  |  Total de IOPS do Cliente  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ClientLatency  |  Latência Média do Cliente  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ClientLockIOPS  |  IOPS de Bloqueio do Cliente  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ClientMetadataReadIOPS  |  IOPS de Leitura dos Metadados do Cliente  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ClientMetadataWriteIOPS  |  IOPS de Gravação dos Metadados do Cliente  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ClientReadIOPS  |  IOPS de Leitura do Cliente  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ClientReadThroughput  |  Taxa de Transferência Média de Leitura do Cache  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ClientWriteIOPS  |  IOPS de Gravação do Cliente  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ClientWriteThroughput  |  Taxa de Transferência Média de Gravação no Cache  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  StorageTargetAsyncWriteThroughput  |  Taxa de Transferência de Gravação Assíncrona do StorageTarget  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  StorageTargetFillThroughput  |  Taxa de Transferência de Preenchimento do StorageTarget  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  StorageTargetHealth  |  Integridade do Destino de Armazenamento  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  StorageTargetIOPS  |  Total de IOPS do StorageTarget  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  StorageTargetLatency  |  Latência do StorageTarget  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  StorageTargetMetadataReadIOPS  |  IOPS de Leitura de Metadados do StorageTarget  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  StorageTargetMetadataWriteIOPS  |  IOPS de Gravação de Metadados do StorageTarget  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  StorageTargetReadAheadThroughput  |  Taxa de Transferência de Leitura Antecipada do StorageTarget  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  StorageTargetReadIOPS  |  IOPS de Leitura do StorageTarget  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  StorageTargetSyncWriteThroughput  |  Taxa de Transferência de Gravação Síncrona do StorageTarget  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  StorageTargetTotalReadThroughput  |  Taxa de Transferência de Leitura Total do StorageTarget  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  StorageTargetTotalWriteThroughput  |  Taxa de Transferência de Gravação Total do StorageTarget  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  StorageTargetWriteIOPS  |  IOPS de Gravação do StorageTarget  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  Tempo de atividade  |  Tempo de atividade  |  Contagem  |  Média | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices  |  ServerSyncSessionResult  |  Resultado da Sessão de Sincronização  |  Contagem  |  Média | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices  |  StorageSyncBatchTransferredFileBytes  |  Bytes sincronizados  |  Bytes  |  Total | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecalledNetworkBytesByApplication  |  Tamanho de recall da camada de nuvem por aplicativo  |  Bytes  |  Total | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecalledTotalNetworkBytes  |  Tamanho de recall da camada de nuvem  |  Bytes  |  Total | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecallIOTotalSizeBytes  |  Recall da camada de nuvem  |  Bytes  |  Total | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecallThroughputBytesPerSecond  |  Taxa de transferência de recall da camada de nuvem  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices  |  StorageSyncServerHeartbeat  |  Status Online do Servidor  |  Contagem  |  Máximo | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices  |  StorageSyncSyncSessionAppliedFilesCount  |  Arquivos Sincronizados  |  Contagem  |  Total | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices  |  StorageSyncSyncSessionPerItemErrorsCount  |  Arquivos não sincronizando  |  Contagem  |  Total | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices/registeredServers  |  ServerHeartbeat  |  Status Online do Servidor  |  Contagem  |  Máximo | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices/registeredServers  |  ServerRecallIOTotalSizeBytes  |  Recall da camada de nuvem  |  Bytes  |  Total | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices/syncGroups  |  SyncGroupBatchTransferredFileBytes  |  Bytes sincronizados  |  Bytes  |  Total | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices/syncGroups  |  SyncGroupSyncSessionAppliedFilesCount  |  Arquivos Sincronizados  |  Contagem  |  Total | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices/syncGroups  |  SyncGroupSyncSessionPerItemErrorsCount  |  Arquivos não sincronizando  |  Contagem  |  Total | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointBatchTransferredFileBytes  |  Bytes sincronizados  |  Bytes  |  Total | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointSyncSessionAppliedFilesCount  |  Arquivos Sincronizados  |  Contagem  |  Total | 
| **Sim**  | Não |  microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointSyncSessionPerItemErrorsCount  |  Arquivos não sincronizando  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutFailedRequests  |  Solicitações de função com falha  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutInputEvents  |  Eventos de função  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutRequests  |  Solicitações de função  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  ConversionErrors  |  Erros de conversão de dados  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  DeserializationError  |  Erros de desserialização de entrada  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  DroppedOrAdjustedEvents  |  Eventos fora de ordem  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  EarlyInputEvents  |  Eventos de Entrada Antecipados  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  Errors  |  Erros de runtime  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  InputEventBytes  |  Bytes de evento de entrada  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  InputEvents  |  Eventos de entrada  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  InputEventsSourcesBacklogged  |  Eventos de Entrada Acumulados  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  InputEventsSourcesPerSecond  |  Fontes de Entrada Recebidas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  LateInputEvents  |  Eventos de entrada atrasados  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  OutputEvents  |  Eventos de saída  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  OutputWatermarkDelaySeconds  |  Atraso de Marca-d'água  |  Segundos  |  Máximo | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  ResourceUtilization  |  % de utilização do SU  |  Porcentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  Bytes de leitura de disco  |  Bytes de leitura de disco  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  Operações de leitura de disco/Seg  |  Operações de leitura de disco/Seg  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  Bytes de gravação de disco  |  Bytes de gravação de disco  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  Operações de gravação de disco/Seg  |  Operações de gravação de disco/Seg  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskReadBytesPerSecond  |  Disk Read Bytes/Sec  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskReadLatency  |  Latência de leitura de disco  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskReadOperations  |  Operações de Leitura de Disco  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskWriteBytesPerSecond  |  Disk Write Bytes/Sec  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskWriteLatency  |  Latência de Gravação de disco  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskWriteOperations  |  Operações de Gravação em Disco  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  MemoryActive  |  Memória Ativa  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  MemoryGranted  |  Memória Concedida  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  MemoryUsed  |  Memória Usada  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  Entrada na rede  |  Entrada na rede  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  Saída da rede  |  Saída da rede  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  NetworkInBytesPerSecond  |  Bytes/s de Entrada de Rede  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  NetworkOutBytesPerSecond  |  Bytes/s de Saída de Rede  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  Porcentagem de CPU  |  Porcentagem de CPU  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudSimple/virtualMachines  |  PercentageCpuReady  |  Percentual de CPU Pronta  |  Milissegundos  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  ActiveRequests  |  Solicitações ativas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  AverageResponseTime  |  Tempo Médio de Resposta  |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  BytesReceived  |  Entrada de Dados  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  BytesSent  |  Saída de dados  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  CpuPercentage  |  Percentual de CPU  |  Porcentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  DiskQueueLength  |  Tamanho da fila do disco  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http101  |  Http 101  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http2xx  |  Http 2xx  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http3xx  |  Http 3xx  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http401  |  Http 401  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http403  |  Http 403  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http404  |  Http 404  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http406  |  Http 406  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http4xx  |  Http 4xx  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http5xx  |  Erros do Servidor Http  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  HttpQueueLength  |  Tamanho da Fila de Http  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  LargeAppServicePlanInstances  |  Funções de trabalho grandes do Plano do Serviço de Aplicativo  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  MediumAppServicePlanInstances  |  Funções de trabalho médias do Plano do Serviço de Aplicativo  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  MemoryPercentage  |  Porcentagem de Memória  |  Porcentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Requests  |  Requests  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  SmallAppServicePlanInstances  |  Funções de trabalho pequenas do Plano do Serviço de Aplicativo  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  TotalFrontEnds  |  Total de front-ends  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/workerPools  |  CpuPercentage  |  Percentual de CPU  |  Porcentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/workerPools  |  MemoryPercentage  |  Porcentagem de Memória  |  Porcentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/workerPools  |  WorkersAvailable  |  Funções de trabalho disponíveis  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/workerPools  |  WorkersTotal  |  Total de funções de trabalho  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/hostingEnvironments/workerPools  |  WorkersUsed  |  Funções de trabalho usadas  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  BytesReceived  |  Entrada de Dados  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  BytesSent  |  Saída de dados  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  CpuPercentage  |  Percentual de CPU  |  Porcentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  DiskQueueLength  |  Tamanho da fila do disco  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  HttpQueueLength  |  Tamanho da Fila de Http  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  MemoryPercentage  |  Porcentagem de Memória  |  Porcentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  TcpCloseWait  |  Espera de Fechamento de TCP  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  TcpClosing  |  Fechamento de TCP  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  TcpEstablished  |  TCP Estabelecido  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  TcpFinWait1  |  Espera 1 de Fin de TCP  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  TcpFinWait2  |  Espera 2 de Fin de TCP  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  TcpLastAck  |  Última Confirmação de TCP  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  TcpSynReceived  |  Sincronização de TCP Recebida  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  TcpSynSent  |  Sincronização de TCP Enviada  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  TcpTimeWait  |  Espera de Tempo de TCP  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  AppConnections  |  conexões  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  AverageMemoryWorkingSet  |  Conjunto de trabalho de memória média  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  AverageResponseTime  |  Tempo Médio de Resposta  |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  BytesReceived  |  Entrada de Dados  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  BytesSent  |  Saída de dados  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  CpuTime  |  Tempo de CPU  |  Segundos  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  CurrentAssemblies  |  Assemblies Atuais  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  FunctionExecutionCount  |  Contagem de Execução de Função  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  FunctionExecutionUnits  |  Unidades de Execução de Função  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Gen0Collections  |  Coletas de lixo da Ger 0  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Gen1Collections  |  Coletas de lixo da Ger 1  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Gen2Collections  |  Coletas de lixo da Ger 2  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Alças  |  Núm. de Identificadores  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  HealthCheckStatus  |  Status da verificação de integridade  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Http101  |  Http 101  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Http2xx  |  Http 2xx  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Http3xx  |  Http 3xx  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Http401  |  Http 401  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Http403  |  Http 403  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Http404  |  Http 404  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Http406  |  Http 406  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Http4xx  |  Http 4xx  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Http5xx  |  Erros do Servidor Http  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  HttpResponseTime  |  Tempo de Resposta  |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  IoOtherBytesPerSecond  |  E/S de outros bytes por segundo  |  BytesPerSecond  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  IoOtherOperationsPerSecond  |  E/S de outras operações por segundo  |  BytesPerSecond  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  IoReadBytesPerSecond  |  E/S de bytes de leitura por segundo  |  BytesPerSecond  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  IoReadOperationsPerSecond  |  E/S de operações de leitura por segundo  |  BytesPerSecond  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  IoWriteBytesPerSecond  |  E/S de bytes de gravação por segundo  |  BytesPerSecond  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  IoWriteOperationsPerSecond  |  E/S de operações de gravação por segundo  |  BytesPerSecond  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  MemoryWorkingSet  |  Conjunto de trabalho de memória  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  PrivateBytes  |  Bytes Particulares  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Requests  |  Requests  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  RequestsInApplicationQueue  |  Solicitações na fila do aplicativo  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Threads  |  Contagem de Threads  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  TotalAppDomains  |  Total de domínios de aplicativo  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  TotalAppDomainsUnloaded  |  Total de domínios de aplicativo descarregados  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  AppConnections  |  conexões  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  AverageMemoryWorkingSet  |  Conjunto de trabalho de memória média  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  AverageResponseTime  |  Tempo Médio de Resposta  |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  BytesReceived  |  Entrada de Dados  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  BytesSent  |  Saída de dados  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  CpuTime  |  Tempo de CPU  |  Segundos  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  CurrentAssemblies  |  Assemblies Atuais  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  FunctionExecutionCount  |  Contagem de Execução de Função  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  FunctionExecutionUnits  |  Unidades de Execução de Função  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Gen0Collections  |  Coletas de lixo da Ger 0  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Gen1Collections  |  Coletas de lixo da Ger 1  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Gen2Collections  |  Coletas de lixo da Ger 2  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Alças  |  Núm. de Identificadores  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  HealthCheckStatus  |  Status da verificação de integridade  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Http101  |  Http 101  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Http2xx  |  Http 2xx  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Http3xx  |  Http 3xx  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Http401  |  Http 401  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Http403  |  Http 403  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Http404  |  Http 404  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Http406  |  Http 406  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Http4xx  |  Http 4xx  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Http5xx  |  Erros do Servidor Http  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  HttpResponseTime  |  Tempo de Resposta  |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  IoOtherBytesPerSecond  |  E/S de outros bytes por segundo  |  BytesPerSecond  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  IoOtherOperationsPerSecond  |  E/S de outras operações por segundo  |  BytesPerSecond  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  IoReadBytesPerSecond  |  E/S de bytes de leitura por segundo  |  BytesPerSecond  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  IoReadOperationsPerSecond  |  E/S de operações de leitura por segundo  |  BytesPerSecond  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  IoWriteBytesPerSecond  |  E/S de bytes de gravação por segundo  |  BytesPerSecond  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  IoWriteOperationsPerSecond  |  E/S de operações de gravação por segundo  |  BytesPerSecond  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  MemoryWorkingSet  |  Conjunto de trabalho de memória  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  PrivateBytes  |  Bytes Particulares  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Requests  |  Requests  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  RequestsInApplicationQueue  |  Solicitações na fila do aplicativo  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Threads  |  Contagem de Threads  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  TotalAppDomains  |  Total de domínios de aplicativo  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  TotalAppDomainsUnloaded  |  Total de domínios de aplicativo descarregados  |  Contagem  |  Média | 
