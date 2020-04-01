---
title: Métricas da plataforma do Azure Monitor exportáveis através de Configurações de Diagnóstico
description: Lista de métricas disponíveis para cada tipo de recurso com o Azure Monitor.
services: azure-monitor
ms.topic: reference
ms.date: 03/30/2020
ms.subservice: metrics
ms.openlocfilehash: 6be8cb1b7e74301d16a1174f5ca2b774334dac3f
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422120"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>Métricas da plataforma do Azure Monitor exportáveis através de Configurações de Diagnóstico

O Azure Monitor fornece [métricas da plataforma](data-platform-metrics.md) por padrão sem configuração. Ele fornece várias maneiras de interagir com métricas da plataforma, incluindo mapeá-las no portal, acessá-las através da API REST ou quescá-las usando PowerShell ou CLI. Consulte [métricas suportadas](metrics-supported.md) para obter uma lista completa de métricas de plataforma disponíveis atualmente com o pipeline métrico consolidado do Azure Monitor. Para consultar e acessar essas métricas, use a [versão api 2018-01-01](https://docs.microsoft.com/rest/api/monitor/metricdefinitions). Outras métricas podem estar disponíveis no portal ou usando as APIs herdadas.

Você pode exportar as métricas da plataforma do pipeline de monitor do Azure para outros locais de duas maneiras.
1. Usando [configurações de diagnóstico](diagnostic-settings.md) para enviar ao Log Analytics, Event Hubs ou Azure Storage.
2. Use as [métricas REST API](https://docs.microsoft.com/rest/api/monitor/metrics/list)

Devido aos meandros no backend do Monitor Azure, nem todas as métricas são exportáveis usando configurações de diagnóstico. A tabela abaixo lista quais podem e não podem ser exportadas usando configurações de diagnóstico.

## <a name="change-to-behavior-for-nulls-and-zero-values"></a>Mudança de comportamento para NULLs e valores zero 
 
Para as métricas da plataforma que podem ser exportadas através de configurações de diagnóstico, existem algumas métricas para as quais o Azure Monitor interpreta '0s' como 'Nulos'. Isso causou alguma confusão entre os '0s' reais (emitidos por recurso) e os '0s' (Nulos). Em breve ocorrerá uma alteração e as métricas da plataforma exportadas através de configurações de diagnóstico não exportarão mais '0s' a menos que tenham sido realmente emitidas pelo recurso subjacente. A mudança estava prevista para 1º de abril de 2020, mas foi adiada devido a mudanças prioritárias devido ao COVID-19. 

Observação:

1.  Se você excluir um grupo de recursos ou um recurso específico, os dados métricos dos recursos efetivados não serão mais enviados para os destinos de exportação de configuração de diagnóstico. Ou seja, ele não aparecerá mais em Hubs de Eventos, Contas de Armazenamento e Espaços de Trabalho do Log Analytics.
2.  Essa melhoria estará disponível em todas as nuvens públicas e privadas.
3.  Essa mudança não afetará o comportamento de nenhuma das seguintes experiências: 
   - Logs de recursos da plataforma exportados via Configurações de Diagnóstico
   - Gráfico de métricas no Metrics Explorer
   - Alertando sobre métricas da plataforma
 
## <a name="metrics-exportable-table"></a>Tabela de métricas exportáveis 

A tabela contém as seguintes colunas. 
- Exportável através de configurações de diagnóstico? 
- Efetuado por NULL /0 
- ResourceType 
- Métrica 
- Nome de exibição métrica
- Unidade 
- AggregationType


> [!NOTE]
> A tabela abaixo pode ter uma barra de rolagem horizontal na parte inferior. Se você acredita que está perdendo informações, verifique se a barra de rolagem está todo à esquerda.  


| Exportável através de configurações de diagnóstico?  | Já emitem NULLs |  ResourceType  |  Métrica  |  Nome de exibição métrica  |  Unidade  |  AggregationType | 
|---|---| ---- | ----- | ------ | ---- | ---- | 
| Sim****  | Não |  Microsoft.AnalysisServices/servers  |  CleanerCurrentPrice  |  Memória: preço atual do limpador  |  Contagem  |  Média | 
| Sim****  | Não |  Microsoft.AnalysisServices/servers  |  CleanerMemoryNonshrinkable  |  Memória: memória do limpador não reduzível  |  Bytes  |  Média | 
| Sim****  | Não |  Microsoft.AnalysisServices/servers  |  CleanerMemoryShrinkable  |  Memória: memória do limpador reduzível  |  Bytes  |  Média | 
| Sim****  | Não |  Microsoft.AnalysisServices/servers  |  CommandPoolBusyThreads  |  Threads: threads ocupados do pool comando  |  Contagem  |  Média | 
| Sim****  | Não |  Microsoft.AnalysisServices/servers  |  CommandPoolIdleThreads  |  Threads: threads ociosos do pool de comandos  |  Contagem  |  Média | 
| Sim****  | Não |  Microsoft.AnalysisServices/servers  |  CommandPoolJobQueueLength  |  Comprimento da fila de trabalho do pool de comando  |  Contagem  |  Média | 
| Sim****  | Não |  Microsoft.AnalysisServices/servers  |  CurrentConnections  |  Conexão: conexões atuais  |  Contagem  |  Média | 
| Sim****  | Não |  Microsoft.AnalysisServices/servers  |  CurrentUserSessions  |  Sessões de usuário atuais  |  Contagem  |  Média | 
| Sim****  | Não |  Microsoft.AnalysisServices/servers  |  LongParsingBusyThreads  |  Threads: threads ocupados de análise longa  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  LongParsingIdleThreads  |  Threads: threads ociosos de análise longa  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  LongParsingJobQueueLength  |  Threads: tamanho da fila de trabalhos de análise longa  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  mashup_engine_memory_metric  |  Memória do mecanismo M  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  mashup_engine_private_bytes_metric  |  M Engine Private Bytes  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  mashup_engine_qpu_metric  |  QPU do mecanismo M  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  mashup_engine_virtual_bytes_metric  |  M Engine Virtual Bytes  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  memory_metric  |  Memória  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  memory_thrashing_metric  |  Sobrecarga de memória  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  MemoryLimitHard  |  Memória: limite de memória física  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  MemoryLimitHigh  |  Memória: limite de memória superior  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  MemoryLimitLow  |  Memória: limite de memória inferior  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  MemoryLimitVertiPaq  |  Memória: VertiPaq do limite de memória  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  MemoryUsage  |  Memória: uso de memória  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  private_bytes_metric  |  Bytes Particulares  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  ProcessingPoolBusyIOJobThreads  |  Threads: threads de trabalho de E/S ocupados do pool de processamento  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  ProcessingPoolBusyNonIOThreads  |  Threads: threads de trabalho não E/S ocupados do pool de processamento  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  ProcessingPoolIdleIOJobThreads  |  Threads: threads de trabalho de E/S ociosos do pool de processamento  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  ProcessingPoolIdleNonIOThreads  |  Threads: threads de trabalho não E/S ociosos do pool de processamento  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  ProcessingPoolIOJobQueueLength  |  Threads: tamanho da fila de trabalhos de E/S do pool de processamento  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  ProcessingPoolJobQueueLength  |  Comprimento da fila de trabalho do pool de processamento  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  qpu_metric  |  QPU  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  QueryPoolBusyThreads  |  Threads ocupados do pool de consulta  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  QueryPoolIdleThreads  |  Threads: threads ociosos do pool de consultas  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  QueryPoolJobQueueLength  |  Threads: tamanho da fila de trabalhos do pool consultas  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  Quota  |  Memória: cota  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  QuotaBlocked  |  Memória: cota bloqueada  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  RowsConvertedPerSec  |  Processamento: linhas convertidas por segundo  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  RowsReadPerSec  |  Processamento: linhas lidas por segundo  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  RowsWrittenPerSec  |  Processamento: linhas gravadas por segundo  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  ShortParsingBusyThreads  |  Threads: threads ocupados de análise resumida  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  ShortParsingIdleThreads  |  Threads: threads ociosos de análise resumida  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  ShortParsingJobQueueLength  |  Threads: tamanho da fila de trabalhos de análise resumida  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  SuccessfullConnectionsPerSec  |  Conexões bem-sucedidas por segundo  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  TotalConnectionFailures  |  Falhas de conexão totais  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  TotalConnectionRequests  |  Solicitações de conexão totais  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  VertiPaqNonpaged  |  Memória: VertiPaq não paginado  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  VertiPaqPaged  |  Memória: VertiPaq paginado  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AnalysisServices/servers  |  virtual_bytes_metric  |  Bytes Virtuais  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  Duração do backend  |  Duração das solicitações de backend  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.ApiManagement/service  |  Capacity  |  Capacity  |  Porcentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  Duration  |  Duração total de solicitações de gateway  |  Milissegundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  EventHubDroppedEvents  |  Eventos EventHub descartados  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  EventHubRejectedEvents  |  Eventos eventhub rejeitados  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  EventHubEventos bem-sucedidos  |  Eventos de sucesso do EventHub  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  Eventos EventHubThrottledEvents  |  Eventos de EventHub estrangulados  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  EventHubTimedoutEvents  |  Eventos EventHub com data esgotado  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  EventHubTotalBytesSent  |  Tamanho dos eventos eventhub  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  EventHubTotalEvents  |  Total de Eventos EventHub  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  EventHubTotalFailedEvents  |  Eventos eventhub com falha  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  FailedRequests  |  Solicitações de gateway com falha (depreciadas)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  OtherRequests  |  Outras solicitações de gateway (depreciadas)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  Requests  |  Requests  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  SuccessfulRequests  |  Solicitações de gateway bem-sucedidas (depreciadas)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  TotalRequests  |  Total de solicitações de gateway (depreciadas)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ApiManagement/service  |  UnauthorizedRequests  |  Solicitações de gateway não autorizadas (depreciadas)  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  AppCpuUsagePercentage  |  Porcentagem de uso da CPU do aplicativo  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  Memória de aplicativocomprometida  |  Memória do aplicativo atribuída  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  AppMemoryMax  |  Memória do aplicativo Max  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  AppMemoryUsed  |  Memória do aplicativo usada  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  Contagem total de GCPause  |  Contagem de pausa sinuosa gc  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  GCPauseTotalTimeTime  |  TEMPO total de pausa do GC  |  Milissegundos  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  MaxOldGenMemoryPoolBytes  |  Tamanho máximo de dados da geração antiga disponível  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  OldGenMemoryPoolBytes  |  Tamanho de dados da geração antiga  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  OldGenPromotedBytes  |  Promover ao tamanho de dados da geração antiga  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  SistemaCpuUsagePercentage  |  Porcentagem de uso da CPU do sistema  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  TomcatErrorCount  |  Erro global da Tomcat  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  TomcatReceivedBytes  |  Bytes recebidos no total de tomcat  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  TomcatRequestMaxTime  |  Tomcat Solicitar Tempo Máximo  |  Milissegundos  |  Máximo | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  TomcatRequestTotalCount  |  Contagem total de solicitações de tomcat  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  TomcatRequestTotalTime  |  Tomcat Request Total Times  |  Milissegundos  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  TomcatResponseAvgTime  |  Tempo médio de solicitação de tomcat  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  TomcatSentBytes  |  Tomcat Total Sent Bytes  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  TomcatSessionActiveCurrentCount  |  Tomcat Session Alive Count  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  TomcatSessionActiveMaxCount  |  Contagem ativa da sessão Tomcat  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  TomcatSessionAliveMaxTime  |  Tomcat Session Max Tempo Vivo  |  Milissegundos  |  Máximo | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  TomcatSessionCrioucontagem  |  Contagem criada da sessão tomcat  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  Contagem expirada do TomcatSession  |  Contagem expirada da sessão tomcat  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  Contagem rejeitada de TomcatSession  |  Contagem rejeitada da sessão de Tomcat  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.AppPlatform/Spring  |  YoungGenPromotedBytes  |  Promover ao Tamanho de Dados da Geração Jovem  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Automation/automationAccounts  |  TotalJob  |  Total de trabalhos  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Automation/automationAccounts  |  TotalUpdateDeploymentMachineExecut  |  Total de execuções de computador da implantação de atualização  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Automation/automationAccounts  |  TotalUpdateDeploymentExecutado  |  Total de execuções da implantação de atualização  |  Contagem  |  Total | 
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
| **Sim**  | Não |  Microsoft.BatchAI/espaços de trabalho  |  Núcleos ativos  |  Núcleos ativos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/espaços de trabalho  |  Nódulos ativos  |  Nódulos ativos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/espaços de trabalho  |  Núcleos ociosos  |  Núcleos ociosos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/espaços de trabalho  |  Nódulos ociosos  |  Nódulos ociosos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/espaços de trabalho  |  Trabalho concluído  |  Trabalho concluído  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.BatchAI/espaços de trabalho  |  Trabalho submetido  |  Trabalho submetido  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.BatchAI/espaços de trabalho  |  Deixando núcleos  |  Deixando núcleos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/espaços de trabalho  |  Deixando nódulos  |  Deixando nódulos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/espaços de trabalho  |  Núcleos pré-empáveis  |  Núcleos pré-empáveis  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/espaços de trabalho  |  Lemos pré-empáticos  |  Lemos pré-empáticos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/espaços de trabalho  |  Percentual de Utilização de Cotas  |  Percentual de Utilização de Cotas  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/espaços de trabalho  |  Núcleos Totais  |  Núcleos Totais  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/espaços de trabalho  |  Nodes Totais  |  Nodes Totais  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/espaços de trabalho  |  Núcleos inutilizáveis  |  Núcleos inutilizáveis  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.BatchAI/espaços de trabalho  |  Nós inutilizáveis  |  Nós inutilizáveis  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  ConexãoAceita  |  Conexões aceitas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  ConexãoAtiva  |  Conexões ativas  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  ConexãoManipulada  |  Conexões manuseadas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  CpuUsagePercentageInDouble  |  Porcentagem de uso da CPU  |  Porcentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  IOReadBytes  |  IO Ler Bytes  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  IOWriteBytes  |  IO Write Bytes  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  MemoryLimit  |  Limite de memória  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  MemoryUsage  |  Uso de Memória  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  Memóriautilizaçãoporcentagememdobrar  |  Porcentagem de uso da memória  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  Transações pendentes  |  Transações pendentes  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  Blocos processados  |  Blocos processados  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  Transações processadas  |  Transações processadas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  Transações enfileiradas  |  Transações enfileiradas  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  RequestHandle  |  Solicitações manipuladas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Blockchain/blockchainMembers  |  ArmazenamentoUso  |  Uso de armazenamento  |  Bytes  |  Média | 
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
| **Sim**  | Não |  Microsoft.ClassicArmazenamento/armazenamentoContas/blobServices  |  Disponibilidade  |  Disponibilidade  |  Porcentagem  |  Média | 
| Não  | Não |  Microsoft.ClassicArmazenamento/armazenamentoContas/blobServices  |  BlobCapacity  |  Capacidade do Blob  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.ClassicArmazenamento/armazenamentoContas/blobServices  |  BlobCount  |  Contagem de Blobs  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicArmazenamento/armazenamentoContas/blobServices  |  ContainerCount  |  Contagem de Contêineres de Blobs  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicArmazenamento/armazenamentoContas/blobServices  |  Saída  |  Saída  |  Bytes  |  Total | 
| Não  | Não |  Microsoft.ClassicArmazenamento/armazenamentoContas/blobServices  |  IndexCapacity  |  Capacidade de índice  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicArmazenamento/armazenamentoContas/blobServices  |  Entrada  |  Entrada  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicArmazenamento/armazenamentoContas/blobServices  |  SuccessE2ELatency  |  Latência de E2E com Sucesso  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicArmazenamento/armazenamentoContas/blobServices  |  SuccessServerLatency  |  Latência de Servidor Com Sucesso  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicArmazenamento/armazenamentoContas/blobServices  |  Transactions  |  Transactions  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicArmazenamento/armazenamentoContas/arquivosServiços  |  Disponibilidade  |  Disponibilidade  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicArmazenamento/armazenamentoContas/arquivosServiços  |  Saída  |  Saída  |  Bytes  |  Total | 
| Não  | Não |  Microsoft.ClassicArmazenamento/armazenamentoContas/arquivosServiços  |  FileCapacity  |  Capacidade do Arquivo  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.ClassicArmazenamento/armazenamentoContas/arquivosServiços  |  FileCount  |  Contagem de Arquivos  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ClassicArmazenamento/armazenamentoContas/arquivosServiços  |  FileShareCount  |  Contagem de Compartilhamentos de Arquivos  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ClassicArmazenamento/armazenamentoContas/arquivosServiços  |  FileShareQuota  |  Tamanho da cota de compartilhamento de arquivos  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.ClassicArmazenamento/armazenamentoContas/arquivosServiços  |  FileShareSnapshotContag  |  Contagem de instantâneos de compartilhamento de arquivos  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ClassicArmazenamento/armazenamentoContas/arquivosServiços  |  FileShareSnapshotSize  |  Tamanho do instantâneo do compartilhamento de arquivos  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicArmazenamento/armazenamentoContas/arquivosServiços  |  Entrada  |  Entrada  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicArmazenamento/armazenamentoContas/arquivosServiços  |  SuccessE2ELatency  |  Latência de E2E com Sucesso  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicArmazenamento/armazenamentoContas/arquivosServiços  |  SuccessServerLatency  |  Latência de Servidor Com Sucesso  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicArmazenamento/armazenamentoContas/arquivosServiços  |  Transactions  |  Transactions  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicArmazenamento/armazenamentoContas/queueServices  |  Disponibilidade  |  Disponibilidade  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicArmazenamento/armazenamentoContas/queueServices  |  Saída  |  Saída  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicArmazenamento/armazenamentoContas/queueServices  |  Entrada  |  Entrada  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicArmazenamento/armazenamentoContas/queueServices  |  QueueCapacity  |  Capacidade da Fila  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicArmazenamento/armazenamentoContas/queueServices  |  QueueCount  |  Contagem de Filas  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicArmazenamento/armazenamentoContas/queueServices  |  QueueMessageCount  |  Contagem de Mensagens da Fila  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicArmazenamento/armazenamentoContas/queueServices  |  SuccessE2ELatency  |  Latência de E2E com Sucesso  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicArmazenamento/armazenamentoContas/queueServices  |  SuccessServerLatency  |  Latência de Servidor Com Sucesso  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicArmazenamento/armazenamentoContas/queueServices  |  Transactions  |  Transactions  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicArmazenamento/armazenamentoContas/tabelaServiços  |  Disponibilidade  |  Disponibilidade  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicArmazenamento/armazenamentoContas/tabelaServiços  |  Saída  |  Saída  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicArmazenamento/armazenamentoContas/tabelaServiços  |  Entrada  |  Entrada  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.ClassicArmazenamento/armazenamentoContas/tabelaServiços  |  SuccessE2ELatency  |  Latência de E2E com Sucesso  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicArmazenamento/armazenamentoContas/tabelaServiços  |  SuccessServerLatency  |  Latência de Servidor Com Sucesso  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicArmazenamento/armazenamentoContas/tabelaServiços  |  TableCapacity  |  Capacidade da Tabela  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicArmazenamento/armazenamentoContas/tabelaServiços  |  TableCount  |  Contagem de Tabelas  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicArmazenamento/armazenamentoContas/tabelaServiços  |  TableEntityCount  |  Contagem de Entidades de Tabela  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.ClassicArmazenamento/armazenamentoContas/tabelaServiços  |  Transactions  |  Transactions  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.CognitiveServices/accounts  |  BlockedCalls  |  Chamadas Bloqueadas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.CognitiveServices/accounts  |  Personagenstreinados  |  Personagens treinados  |  Contagem  |  Total | 
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
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Profundidade da Fila de Disco de Dados  |  Profundidade da fila de discos de dados (visualização)  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Leitura de disco de dados Bytes/seg  |  Bytes de Leitura do Disco de Dados/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Operações de leitura de disco de dados/seg  |  Operações de Leitura do Disco de Dados/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Bytes de gravação de disco de dados/seg  |  Bytes de Gravação do Disco de Dados/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Operações de gravação de disco de dados/seg  |  Operações de Gravação do Disco de Dados/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Bytes de leitura de disco  |  Bytes de leitura de disco  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Operações de leitura de disco/Seg  |  Operações de leitura de disco/Seg  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Bytes de gravação de disco  |  Bytes de gravação de disco  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Operações de gravação de disco/Seg  |  Operações de gravação de disco/Seg  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Fluxos de Entrada  |  Fluxos de Entrada  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Taxa de criação máxima dos fluxos de entrada  |  Taxa de criação máxima de fluxos de entrada (visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Entrada na rede  |  Rede Em Billable (Depreciada)  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Rede no total  |  Rede no total  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Saída da rede  |  Rede Out Billable (Depreciada)  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Rede Out Total  |  Rede Out Total  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Profundidade da Fila de Disco do SO  |  Profundidade da fila do disco do SISTEMA OPERACIONAL (Visualização)  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Leitura de disco do SISTEMA OPERACIONAL Bytes/seg  |  Bytes de Leitura do Disco do SO/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Operações de leitura de disco do SISTEMA OPERACIONAL/Seg  |  Operações de Leitura do Disco do SO/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Bytes de gravação de disco do SISTEMA OPERACIONAL/seg  |  Bytes de Gravação do Disco do SO/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Operações de gravação de disco do SISTEMA OPERACIONAL/Seg  |  Operações de Gravação do Disco do SO/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Os Por QD de Disco  |  Disco do SISTEMA OPERACIONAL QD (Preterido)  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  SO Por Bytes de Leitura do Disco/s  |  Leitura de disco do SISTEMA OPERACIONAL Bytes/Seg (preterida)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  SO Por Operações de Leitura do Disco/s  |  Operações de leitura de disco do SISTEMA OPERACIONAL/Seg (preteridas)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  SO Por Bytes de Gravação do Disco/s  |  Bytes de gravação de disco do SISTEMA OPERACIONAL/Seg (preteridos)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  SO Por Operações de Gravação do Disco/s  |  Operações de gravação de disco do SISTEMA OPERACIONAL/Seg (preteridas)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Fluxos de saída  |  Fluxos de saída  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Taxa de criação máxima dos fluxos de saída  |  Taxa de criação máxima de fluxos de saída (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Por QD de Disco  |  QD do disco de dados (preterido)  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Por Bytes de Leitura do Disco/s  |  Leitura de disco de dados Bytes/Seg (preterido)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Por operações de Leitura de Disco/s  |  Operações de leitura de disco de dados/seg (preterido)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Por Bytes de Gravação de Disco/s  |  Bytes de gravação de disco de dados/seg (preteridos)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Por Operações de Gravação de Disco/s  |  Operações de gravação de disco de dados/seg (preterido)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Porcentagem de CPU  |  Porcentagem de CPU  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Impacto de leitura de leitura de armazenamento de disco de dados premium  |  Impacto de leitura de leitura de armazenamento de dados premium (visualização)  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Cache de disco de dados premium Leia Miss  |  Cache de disco de dados premium Ler falta (visualização)  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Impacto de leitura de leitura de cache do sistema operacional premium  |  A cessação de leitura de leitura do cache do sistema operacional premium (visualização)  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachines  |  Cache de disco premium do SISTEMA OPERACIONAL Ler Miss  |  Cache de disco premium do Sistema Operacional Read Miss (Visualização)  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Créditos de CPU Consumidos  |  Créditos de CPU Consumidos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Créditos de CPU Restantes  |  Créditos de CPU Restantes  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Profundidade da Fila de Disco de Dados  |  Profundidade da fila de discos de dados (visualização)  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Leitura de disco de dados Bytes/seg  |  Bytes de Leitura do Disco de Dados/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Operações de leitura de disco de dados/seg  |  Operações de Leitura do Disco de Dados/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Bytes de gravação de disco de dados/seg  |  Bytes de Gravação do Disco de Dados/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Operações de gravação de disco de dados/seg  |  Operações de Gravação do Disco de Dados/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Bytes de leitura de disco  |  Bytes de leitura de disco  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Operações de leitura de disco/Seg  |  Operações de leitura de disco/Seg  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Bytes de gravação de disco  |  Bytes de gravação de disco  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Operações de gravação de disco/Seg  |  Operações de gravação de disco/Seg  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Fluxos de Entrada  |  Fluxos de Entrada  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Taxa de criação máxima dos fluxos de entrada  |  Taxa de criação máxima de fluxos de entrada (visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Entrada na rede  |  Rede Em Billable (Depreciada)  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Rede no total  |  Rede no total  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Saída da rede  |  Rede Out Billable (Depreciada)  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Rede Out Total  |  Rede Out Total  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Profundidade da Fila de Disco do SO  |  Profundidade da fila do disco do SISTEMA OPERACIONAL (Visualização)  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Leitura de disco do SISTEMA OPERACIONAL Bytes/seg  |  Bytes de Leitura do Disco do SO/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Operações de leitura de disco do SISTEMA OPERACIONAL/Seg  |  Operações de Leitura do Disco do SO/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Bytes de gravação de disco do SISTEMA OPERACIONAL/seg  |  Bytes de Gravação do Disco do SO/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Operações de gravação de disco do SISTEMA OPERACIONAL/Seg  |  Operações de Gravação do Disco do SO/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Os Por QD de Disco  |  Disco do SISTEMA OPERACIONAL QD (Preterido)  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  SO Por Bytes de Leitura do Disco/s  |  Leitura de disco do SISTEMA OPERACIONAL Bytes/Seg (preterida)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  SO Por Operações de Leitura do Disco/s  |  Operações de leitura de disco do SISTEMA OPERACIONAL/Seg (preteridas)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  SO Por Bytes de Gravação do Disco/s  |  Bytes de gravação de disco do SISTEMA OPERACIONAL/Seg (preteridos)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  SO Por Operações de Gravação do Disco/s  |  Operações de gravação de disco do SISTEMA OPERACIONAL/Seg (preteridas)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Fluxos de saída  |  Fluxos de saída  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Taxa de criação máxima dos fluxos de saída  |  Taxa de criação máxima de fluxos de saída (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Por QD de Disco  |  QD do disco de dados (preterido)  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Por Bytes de Leitura do Disco/s  |  Leitura de disco de dados Bytes/Seg (preterido)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Por operações de Leitura de Disco/s  |  Operações de leitura de disco de dados/seg (preterido)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Por Bytes de Gravação de Disco/s  |  Bytes de gravação de disco de dados/seg (preteridos)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Por Operações de Gravação de Disco/s  |  Operações de gravação de disco de dados/seg (preterido)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Porcentagem de CPU  |  Porcentagem de CPU  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Impacto de leitura de leitura de armazenamento de disco de dados premium  |  Impacto de leitura de leitura de armazenamento de dados premium (visualização)  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Cache de disco de dados premium Leia Miss  |  Cache de disco de dados premium Ler falta (visualização)  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Impacto de leitura de leitura de cache do sistema operacional premium  |  A cessação de leitura de leitura do cache do sistema operacional premium (visualização)  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets  |  Cache de disco premium do SISTEMA OPERACIONAL Ler Miss  |  Cache de disco premium do Sistema Operacional Read Miss (Visualização)  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Créditos de CPU Consumidos  |  Créditos de CPU Consumidos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Créditos de CPU Restantes  |  Créditos de CPU Restantes  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Profundidade da Fila de Disco de Dados  |  Profundidade da fila de discos de dados (visualização)  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Leitura de disco de dados Bytes/seg  |  Bytes de Leitura do Disco de Dados/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operações de leitura de disco de dados/seg  |  Operações de Leitura do Disco de Dados/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bytes de gravação de disco de dados/seg  |  Bytes de Gravação do Disco de Dados/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operações de gravação de disco de dados/seg  |  Operações de Gravação do Disco de Dados/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bytes de leitura de disco  |  Bytes de leitura de disco  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operações de leitura de disco/Seg  |  Operações de leitura de disco/Seg  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bytes de gravação de disco  |  Bytes de gravação de disco  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operações de gravação de disco/Seg  |  Operações de gravação de disco/Seg  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Fluxos de Entrada  |  Fluxos de Entrada  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Taxa de criação máxima dos fluxos de entrada  |  Taxa de criação máxima de fluxos de entrada (visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Entrada na rede  |  Rede Em Billable (Depreciada)  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Rede no total  |  Rede no total  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Saída da rede  |  Rede Out Billable (Depreciada)  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Rede Out Total  |  Rede Out Total  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Profundidade da Fila de Disco do SO  |  Profundidade da fila do disco do SISTEMA OPERACIONAL (Visualização)  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Leitura de disco do SISTEMA OPERACIONAL Bytes/seg  |  Bytes de Leitura do Disco do SO/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operações de leitura de disco do SISTEMA OPERACIONAL/Seg  |  Operações de Leitura do Disco do SO/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bytes de gravação de disco do SISTEMA OPERACIONAL/seg  |  Bytes de Gravação do Disco do SO/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Operações de gravação de disco do SISTEMA OPERACIONAL/Seg  |  Operações de Gravação do Disco do SO/s (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Os Por QD de Disco  |  Disco do SISTEMA OPERACIONAL QD (Preterido)  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  SO Por Bytes de Leitura do Disco/s  |  Leitura de disco do SISTEMA OPERACIONAL Bytes/Seg (preterida)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  SO Por Operações de Leitura do Disco/s  |  Operações de leitura de disco do SISTEMA OPERACIONAL/Seg (preteridas)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  SO Por Bytes de Gravação do Disco/s  |  Bytes de gravação de disco do SISTEMA OPERACIONAL/Seg (preteridos)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  SO Por Operações de Gravação do Disco/s  |  Operações de gravação de disco do SISTEMA OPERACIONAL/Seg (preteridas)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Fluxos de saída  |  Fluxos de saída  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Taxa de criação máxima dos fluxos de saída  |  Taxa de criação máxima de fluxos de saída (Visualização)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Por QD de Disco  |  QD do disco de dados (preterido)  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Por Bytes de Leitura do Disco/s  |  Leitura de disco de dados Bytes/Seg (preterido)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Por operações de Leitura de Disco/s  |  Operações de leitura de disco de dados/seg (preterido)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Por Bytes de Gravação de Disco/s  |  Bytes de gravação de disco de dados/seg (preteridos)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Por Operações de Gravação de Disco/s  |  Operações de gravação de disco de dados/seg (preterido)  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Porcentagem de CPU  |  Porcentagem de CPU  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Impacto de leitura de leitura de armazenamento de disco de dados premium  |  Impacto de leitura de leitura de armazenamento de dados premium (visualização)  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Cache de disco de dados premium Leia Miss  |  Cache de disco de dados premium Ler falta (visualização)  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Impacto de leitura de leitura de cache do sistema operacional premium  |  A cessação de leitura de leitura do cache do sistema operacional premium (visualização)  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Cache de disco premium do SISTEMA OPERACIONAL Ler Miss  |  Cache de disco premium do Sistema Operacional Read Miss (Visualização)  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.ContainerInstance/containerGroups  |  CpuUsage  |  Uso da CPU  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.ContainerInstance/containerGroups  |  MemoryUsage  |  Uso de Memória  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.ContainerInstance/containerGroups  |  NetworkBytesReceivedPerSecond  |  Bytes de Rede Recebidos por Segundo  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.ContainerInstance/containerGroups  |  NetworkBytesTransmittedPerSecond  |  Bytes de Rede Transmitidos por Segundo  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.ContainerRegistry/registries  |  Duração do execução  |  Duração da execução  |  Milissegundos  |  Total | 
| **Sim**  | Não |  Microsoft.ContainerRegistry/registries  |  SucessoPullCount  |  Contagem de tração bem sucedida  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.ContainerRegistry/registries  |  SucessopushCount  |  Contagem de push bem sucedida  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.ContainerRegistry/registries  |  TotalPullCount  |  Contagem total de puxadas  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.ContainerRegistry/registries  |  TotalPushCount  |  Contagem total de push  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ContainerService/managedClusters  |  kube_node_status_allocatable_cpu_cores  |  Número total de núcleos de cpu disponíveis em um cluster gerenciado  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ContainerService/managedClusters  |  kube_node_status_allocatable_memory_bytes  |  Número total de memória disponível em um cluster gerenciado  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.ContainerService/managedClusters  |  kube_node_status_condition  |  Status para as várias condições de nó  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ContainerService/managedClusters  |  kube_pod_status_phase  |  Número de pods por fase  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ContainerService/managedClusters  |  kube_pod_status_ready  |  Número de compartimentos no estado Pronto  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDispositivos  |  Capacidade disponível  |  Capacidade Disponível  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDispositivos  |  BytesUploadedToCloud  |  Cloud Bytes Carregado (Dispositivo)  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDispositivos  |  BytesUploadedToCloudPerShare  |  Cloud Bytes Carregado (Compartilhar)  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDispositivos  |  CloudReadThroughput  |  Throughput de download na nuvem  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDispositivos  |  CloudReadthroughputPerShare  |  Throughput de download na nuvem (Compartilhar)  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDispositivos  |  CloudUploadThroughput  |  Throughput de upload de nuvem  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDispositivos  |  CloudUploadthroughputperShare  |  Throughput de upload de nuvem (Compartilhar)  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDispositivos  |  HyperVMemoryUtilização  |  Edge Compute - Uso da memória  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDispositivos  |  HyperVVirtualProcessorUtilização  |  Edge Compute - CPU percentual  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDispositivos  |  NICReadThroughput  |  Ler throughput (rede)  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDispositivos  |  NICWriteThroughput  |  Gravar throughput (rede)  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.DataBoxEdge/dataBoxEdgeDispositivos  |  Capacidade Total  |  Capacidade total  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.DataFactory/datafactories  |  FailedRuns  |  Execuções com falha  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.DataFactory/datafactories  |  SuccessfulRuns  |  Execuções com êxito  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.DataFactory/factories  |  ActivityCancelledRuns  |  Atividade cancelada executa métricas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.DataFactory/factories  |  ActivityFailedRuns  |  Métricas de execução de atividades com falha  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.DataFactory/factories  |  ActivitySucceededRuns  |  Métricas de execução de atividades bem-sucedidas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.DataFactory/factories  |  FactorySizeInGbUnits  |  Tamanho total da fábrica (unidade GB)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.DataFactory/factories  |  IntegrationRuntimeAvailableMemory  |  Memória disponível de runtime de integração  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.DataFactory/factories  |  IntegraçãoExecutartempoAverageTaskPickupAtraso  |  Duração da fila de tempo de execução de integração  |  Segundos  |  Média | 
| **Sim**  | Não |  Microsoft.DataFactory/factories  |  IntegrationRuntimeCpuPercentage  |  Utilização de CPU de runtime de integração  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.DataFactory/factories  |  IntegraçãoRuntimeQueueLength  |  Comprimento da fila de tempo de execução de integração  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.DataFactory/factories  |  MaxallowedfactorySizeingbunits  |  Tamanho máximo permitido de fábrica (unidade GB)  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.DataFactory/factories  |  Contagem de recursos máximas permitidas  |  Contagem máxima de entidades permitidas  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.DataFactory/factories  |  PipelineCanceledRuns  |  Pipeline cancelado executa métricas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.DataFactory/factories  |  PipelineFailedRuns  |  Métricas de execução do pipeline com falha  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.DataFactory/factories  |  PipelineSucceededRuns  |  Métricas de execução do pipeline bem-sucedido  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.DataFactory/factories  |  Contagem de recursos  |  Contagem total de entidades  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.DataFactory/factories  |  TriggerCanceledRuns  |  Gatilho cancelado executa métricas  |  Contagem  |  Total | 
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
| **Sim**  | **Sim** |  Microsoft.DBforMariaDB/servers  |  backup_storage_used  |  Armazenamento de backup usado  |  Bytes  |  Média | 
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
| **Sim**  | **Sim** |  Microsoft.DBforMySQL/servers  |  backup_storage_used  |  Armazenamento de backup usado  |  Bytes  |  Média | 
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
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/servers  |  backup_storage_used  |  Armazenamento de backup usado  |  Bytes  |  Média | 
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
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/serversv2  |  Iops  |  IOPS  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/serversv2  |  memory_percent  |  Porcentagem de memória  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/serversv2  |  network_bytes_egress  |  Saída da rede  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/serversv2  |  network_bytes_ingress  |  Entrada na rede  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/serversv2  |  storage_percent  |  Porcentagem de armazenamento  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.DBforPostgreSQL/serversv2  |  storage_used  |  Armazenamento usado  |  Bytes  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Devices/Conta  |  digitaltwins.telemetria.nodes  |  Espaço reservado para telemetria de nó de gêmeos digitais  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.abandon.success  |  Mensagens C2D abandonadas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.complete.success  |  Entregas de mensagens C2D concluídas  |  Contagem  |  Total | 
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
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  C2DMessagesExpirado  |  Mensagens C2D expiradas (visualização)  |  Contagem  |  Total | 
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
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  EventGridEntregas  |  Entregas da Event Grid (pré-visualização)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Devices/IotHubs  |  EventGridLatency  |  Latência do Grid de Eventos (visualização)  |  Milissegundos  |  Média | 
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
| **Sim**  | Não |  Microsoft.DocumentDB/databaseAccounts  |  ExcluirVirtualNetwork  |  ExcluirVirtualNetwork  |  Contagem  |  Contagem | 
| Não  | Não |  Microsoft.DocumentDB/databaseAccounts  |  DocumentCount  |  Contagem de documentos  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.DocumentDB/databaseAccounts  |  DocumentQuota  |  Cota de documento  |  Bytes  |  Total | 
| Não  | Não |  Microsoft.DocumentDB/databaseAccounts  |  IndexUsage  |  Uso do índice  |  Bytes  |  Total | 
| Não  | Não |  Microsoft.DocumentDB/databaseAccounts  |  MetadataRequests  |  MetadataRequests  |  Contagem  |  Contagem | 
| **Sim**  | **Sim** |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestCharge  |  MongoRequestCharge  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequests  |  Solicitações do Mongo  |  Contagem  |  Contagem | 
| Não  | Não |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsCount  |  Taxa de Solicitação de Mongo  |  CountPerSecond  |  Média | 
| Não  | Não |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsDelete  |  Taxa de solicitação de exclusão de Mongo  |  CountPerSecond  |  Média | 
| Não  | Não |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsInsert  |  Taxa de solicitação de inserção de Mongo  |  CountPerSecond  |  Média | 
| Não  | Não |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsQuery  |  Taxa de solicitação de consulta de Mongo  |  CountPerSecond  |  Média | 
| Não  | Não |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsUpdate  |  Taxa de solicitação de atualização do Mongo  |  CountPerSecond  |  Média | 
| Não  | Não |  Microsoft.DocumentDB/databaseAccounts  |  ProvisionedThroughput  |  Taxa de transferência provisionada  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.DocumentDB/databaseAccounts  |  ReplicationLatency  |  Latência de replicação P99  |  MilliSeconds  |  Média | 
| Não  | Não |  Microsoft.DocumentDB/databaseAccounts  |  ServiceAvailability  |  Disponibilidade do serviço  |  Porcentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.DocumentDB/databaseAccounts  |  TotalRequests  |  Total de Solicitações  |  Contagem  |  Contagem | 
| **Sim**  | **Sim** |  Microsoft.DocumentDB/databaseAccounts  |  TotalRequestUnits  |  Total de Unidades Solicitadas  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EnterpriseKnowledgeGraph/services  |  Contagem de falhas  |  Contagem de Falha  |  Contagem  |  Contagem | 
| Não  | Não |  Microsoft.EnterpriseKnowledgeGraph/services  |  Contagem de sucessos  |  Contagem de Êxito  |  Contagem  |  Contagem | 
| Não  | Não |  Microsoft.EnterpriseKnowledgeGraph/services  |  SuccessLatency  |  Latência de sucesso  |  MilliSeconds  |  Média | 
| Não  | Não |  Microsoft.EnterpriseKnowledgeGraph/services  |  Contagem de transações  |  Contagem de transações  |  Contagem  |  Contagem | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/domínios  |  DeadLetteredCount  |  Eventos de mensagens mortas  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventGrid/domínios  |  DeliveryAttemptFailCount  |  Eventos com falha de entrega  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/domínios  |  DeliverySuccessCount  |  Eventos entregues  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventGrid/domínios  |  DestinationProcessingDurationInMs  |  Duração do processamento de destino  |  Milissegundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/domínios  |  DroppedEventCount  |  Eventos removidos  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/domínios  |  MatchedEventCount  |  Eventos correspondentes  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/domínios  |  PublishFailCount  |  Publicar eventos com falha  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/domínios  |  PublishSuccessCount  |  Eventos publicados  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/domínios  |  PublicarSucessoLatencyInms  |  Publicar latência de sucesso  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/eventSubscriptions  |  DeadLetteredCount  |  Eventos de mensagens mortas  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventGrid/eventSubscriptions  |  DeliveryAttemptFailCount  |  Eventos com falha de entrega  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/eventSubscriptions  |  DeliverySuccessCount  |  Eventos entregues  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventGrid/eventSubscriptions  |  DestinationProcessingDurationInMs  |  Duração do processamento de destino  |  Milissegundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/eventSubscriptions  |  DroppedEventCount  |  Eventos removidos  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/eventSubscriptions  |  MatchedEventCount  |  Eventos correspondentes  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/extensionTopics  |  PublishFailCount  |  Publicar eventos com falha  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/extensionTopics  |  PublishSuccessCount  |  Eventos publicados  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/extensionTopics  |  PublicarSucessoLatencyInms  |  Publicar latência de sucesso  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/extensionTopics  |  UnmatchedEventCount  |  Eventos sem correspondência  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/topics  |  PublishFailCount  |  Publicar eventos com falha  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/topics  |  PublishSuccessCount  |  Eventos publicados  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventGrid/topics  |  PublicarSucessoLatencyInms  |  Publicar latência de sucesso  |  Contagem  |  Total | 
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
| **Sim**  | **Sim** |  Microsoft.EventHub/namespaces  |  EHABL  |  Arquivar mensagens de atraso (depreciadas)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/namespaces  |  EHAMBS  |  Throughput de mensagem de arquivamento (Preterido)  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/namespaces  |  EHAMSGS  |  Arquivar mensagens (Depreciadas)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/namespaces  |  EHINBYTES  |  Bytes de entrada (Preterida)  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/namespaces  |  EHINMBS  |  Bytes de entrada (obsoletos) (Depreciados)  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/namespaces  |  EHINMSGS  |  Mensagens de Entrada (Preterida)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/namespaces  |  EHOUTBYTES  |  Bytes de saída (Preterida)  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/namespaces  |  EHOUTMBS  |  Bytes de saída (obsoletos) (Depreciados)  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/namespaces  |  EHOUTMSGS  |  Mensagens de Saída (Preterida)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/namespaces  |  FAILREQ  |  Solicitações com falha (depreciadas)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/namespaces  |  IncomingBytes  |  Bytes de Entrada.  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/namespaces  |  IncomingMessages  |  Mensagens de entrada  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/namespaces  |  IncomingRequests  |  Solicitações de entrada  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/namespaces  |  INMSGS  |  Mensagens recebidas (obsoletas) (Depreciadas)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/namespaces  |  INREQS  |  Pedidos de entrada (preteridos)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/namespaces  |  INTERR  |  Erros internos do servidor (preteridos)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/namespaces  |  MISCERR  |  Outros erros (preteridos)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/namespaces  |  OutgoingBytes  |  Bytes de Saída.  |  Bytes  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/namespaces  |  OutgoingMessages  |  Mensagens de saída  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/namespaces  |  OUTMSGS  |  Mensagens de saída (obsoletas) (Depreciadas)  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventHub/namespaces  |  QuotaExceededErrors  |  Cota de Erros Excedida.  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventHub/namespaces  |  ServerErrors  |  Erros do Servidor.  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventHub/namespaces  |  Tamanho  |  Tamanho  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.EventHub/namespaces  |  SuccessfulRequests  |  Solicitações bem sucedidas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/namespaces  |  SUCCREQ  |  Solicitações bem sucedidas (depreciadas)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.EventHub/namespaces  |  SVRBSY  |  Erros ocupados do servidor (preteridos)  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventHub/namespaces  |  ThrottledRequests  |  Solicitações Limitadas.  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.EventHub/namespaces  |  UserErrors  |  Erros de Usuário.  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.HDInsight/clusters  |  CategorizedGatewayRequests  |  Solicitações de gateway categorizadas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.HDInsight/clusters  |  GatewayRequests  |  Solicitações de gateway  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.HDInsight/clusters  |  NumActiveWorkers  |  Número de trabalhadores ativos  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.HDInsight/clusters  |  Solicitações de dimensionamento  |  Solicitações de dimensionamento  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Insights/AutoscaleSettings  |  MetricThreshold  |  Limite da Métrica  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Insights/AutoscaleSettings  |  ObservedCapacity  |  Capacidade Observada  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Insights/AutoscaleSettings  |  ObservedMetricValue  |  Valor de Métrica Observado  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Insights/AutoscaleSettings  |  ScaleActionsInitiated  |  Ações de Escala Iniciadas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Insights/Components  |  disponibilidadeResultados/disponibilidadePorcentagem  |  Disponibilidade  |  Porcentagem  |  Média | 
| Não  | Não |  Microsoft.Insights/Components  |  disponibilidadeResultados/contagem  |  Testes de disponibilidade  |  Contagem  |  Contagem | 
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
| Não  | Não |  Microsoft.Insights/Components  |  solicitações/taxa  |  Taxa de solicitação de servidor  |  CountPerSecond  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Insights/Components  |  traces/count  |  Rastreamentos  |  Contagem  |  Contagem | 
| **Sim**  | Não |  Microsoft.KeyVault/vaults  |  ServiceApiHit  |  Total de ocorrências da API de Serviço  |  Contagem  |  Contagem | 
| **Sim**  | Não |  Microsoft.KeyVault/vaults  |  ServiceApiLatency  |  Latência geral da API de Serviço  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.KeyVault/vaults  |  ServiceApiResult  |  Total de resultados da API de Serviço  |  Contagem  |  Contagem | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  CacheUtilização  |  Utilização do cache  |  Porcentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  ContinuousExportMaxLatenessMinutes  |  Atas de atraso máximas de exportação contínua  |  Contagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  ContinuousExportNumOfRecordsExported  |  Exportação contínua - num de registros exportados  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  Contagem contínuadeexportações  |  Contagem contínua pendente de exportação  |  Contagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  Resultado de exportação contínua  |  Resultado contínuo da exportação  |  Contagem  |  Contagem | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  CPU  |  CPU  |  Porcentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  EventosProcessadosParaEventosHubs  |  Eventos processados (para Hubs de Eventos/IoT)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  ExportAçãoUtilização  |  Utilização da exportação  |  Porcentagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  InstionLatencyInSeconds  |  Latência de ingestão (em segundos)  |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  IngestionResult  |  Resultado da ingestão  |  Contagem  |  Contagem | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  IngestãoUtilização  |  Utilização da ingestão  |  Porcentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  IngestionVolumeInMB  |  Volume de ingestão (em MB)  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  Keepalive  |  Mantenha-se vivo  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  QueryDuration  |  Duração da consulta  |  Milissegundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  Taxa de solicitação mais vaporosa  |  Taxa de solicitação de ingestão de streaming  |  Contagem  |  RateRequestsPerSegundo | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  StreamingIngestDataRate  |  Taxa de dados de ingestão de streaming  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  StreamingIngestDuration  |  Streaming de duração ingerida  |  Milissegundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Kusto/Clusters  |  StreamingingestResultados  |  Resultado de Streaming Ingest  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Logic/integrationServiceEnvironments  |  ActionLatency  |  Latência da ação   |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionsCompleted  |  Ações concluídas   |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionsFailed  |  Ações com falha   |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionsSkipped  |  Ações ignoradas   |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionsStarted  |  Ações iniciadas   |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionsSucceeded  |  Ações bem sucedidas   |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Logic/integrationServiceEnvironments  |  ActionSuccessLatency  |  Latência das ações bem sucedidas   |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionThrottledEvents  |  Eventos de restrição de ações  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Logic/integrationServiceEnvironments  |  IntegraçãoServiceEnvironmentConectorMemoryUseUso  |  Uso da memória do conector para o ambiente de serviço de integração  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Logic/integrationServiceEnvironments  |  IntegraçãoServiceEnvironmentConectorProcessorUseusouso  |  Uso do processador conector para ambiente de serviço de integração  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Logic/integrationServiceEnvironments  |  IntegraçãoServiçoAmbientede trabalhoTrabalhotrabalhoTrabalhosmaisbem-ministroUsode trabalho  |  Uso da memória do fluxo de trabalho para o ambiente de serviço de integração  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Logic/integrationServiceEnvironments  |  IntegraçãoServiçoAmbienteambienteTrabalhotrabalhoControlede utilizaçãodotrabalho  |  Uso do processador de fluxo de trabalho para o ambiente de serviço de integração  |  Porcentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  RunFailurePercentage  |  Porcentagem de falha de execução  |  Porcentagem  |  Total | 
| **Sim**  | Não |  Microsoft.Logic/integrationServiceEnvironments  |  RunLatency  |  Latência da execução  |  Segundos  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsCancelled  |  Executa cancelado  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsCompleted  |  Execuções concluídas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsFailed  |  Execuções com falha  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsStarted  |  Execuções iniciadas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsSucceeded  |  Execuções bem sucedidas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/integrationServiceEnvironments  |  RunStartThrottledEvents  |  Executar eventos estrangulados  |  Contagem  |  Total | 
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
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  RunsCancelled  |  Executa cancelado  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  RunsCompleted  |  Execuções concluídas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  RunsFailed  |  Execuções com falha  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  RunsStarted  |  Execuções iniciadas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  RunsSucceeded  |  Execuções bem sucedidas  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Logic/workflows  |  RunStartThrottledEvents  |  Executar eventos estrangulados  |  Contagem  |  Total | 
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
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Núcleos ativos  |  Núcleos ativos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Nódulos ativos  |  Nódulos ativos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Executa concluído  |  Executa concluído  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Execuções com falha  |  Execuções com falha  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Núcleos ociosos  |  Núcleos ociosos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Nódulos ociosos  |  Nódulos ociosos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Deixando núcleos  |  Deixando núcleos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Deixando nódulos  |  Deixando nódulos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Falha na implantação do modelo  |  Falha na implantação do modelo  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Implantação do modelo iniciado  |  Implantação do modelo iniciado  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Modelo Implantar Bem Sucedido  |  Modelo Implantar Bem Sucedido  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Falha no registro do modelo  |  Falha no registro do modelo  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Registro de modelo sucedendo  |  Registro de modelo sucedendo  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Núcleos pré-empáveis  |  Núcleos pré-empáveis  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Lemos pré-empáticos  |  Lemos pré-empáticos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Percentual de Utilização de Cotas  |  Percentual de Utilização de Cotas  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Started Runs  |  Started Runs  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Núcleos Totais  |  Núcleos Totais  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Nodes Totais  |  Nodes Totais  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Núcleos inutilizáveis  |  Núcleos inutilizáveis  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.MachineLearningServices/workspaces  |  Nós inutilizáveis  |  Nós inutilizáveis  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Maps/accounts  |  Disponibilidade  |  Disponibilidade  |  Porcentagem  |  Média | 
| Não  | Não |  Microsoft.Maps/accounts  |  Uso  |  Uso  |  Contagem  |  Contagem | 
| **Sim**  | Não |  Microsoft.Media/mediaservices  |  Contagem de ativos  |  Contagem de ativos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Media/mediaservices  |  Cotação patrimonial  |  Cota de ativos  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Media/mediaservices  |  AssetQuotaUsedPercentage  |  Cota de ativos utilizada  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Media/mediaservices  |  Contagem de políticas de conteúdo  |  Contagem de políticas de chave de conteúdo  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Media/mediaservices  |  Cota de política de conteúdo  |  Cota de política de chave de conteúdo  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Media/mediaservices  |  ConteúdochavePolicyQuotaUsedPercentage  |  Cota de política de chave de conteúdo usada  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Media/mediaservices  |  StreamingPolicyCount  |  Contagem de políticas de streaming  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Media/mediaservices  |  StreamingPolicyQuota  |  Cota de Política de Streaming  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Media/mediaservices  |  StreamingPolicyQuotaUsedPercentage  |  Cota de Política de Streaming usada  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Media/mediaservices/streamingEndpoints  |  Saída  |  Saída  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Media/mediaservices/streamingEndpoints  |  Requests  |  Requests  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Media/mediaservices/streamingEndpoints  |  SuccessE2ELatency  |  Sucesso de ponta a ponta Latência  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  Contagem total de GCPause  |  Contagem de pausa sinuosa gc  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  GCPauseTotalTimeTime  |  TEMPO total de pausa do GC  |  Milissegundos  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  MaxOldGenMemoryPoolBytes  |  Tamanho máximo de dados da geração antiga disponível  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  OldGenMemoryPoolBytes  |  Tamanho de dados da geração antiga  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  OldGenPromotedBytes  |  Promover ao tamanho de dados da geração antiga  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  ServiceCpuUsagePercentage  |  Porcentagem de uso da CPU do serviço  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  Memória de serviçocomprometida  |  Memória de serviço atribuída  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  ServiceMemoryMax  |  Memória de Serviço Max  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  ServiceMemoryUsed  |  Memória de serviço usada  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  SistemaCpuUsagePercentage  |  Porcentagem de uso da CPU do sistema  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatErrorCount  |  Erro global da Tomcat  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatReceivedBytes  |  Bytes recebidos no total de tomcat  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatRequestMaxTime  |  Tomcat Solicitar Tempo Máximo  |  Milissegundos  |  Máximo | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatRequestTotalCount  |  Contagem total de solicitações de tomcat  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatRequestTotalTime  |  Tomcat Request Total Times  |  Milissegundos  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatResponseAvgTime  |  Tempo médio de solicitação de tomcat  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatSentBytes  |  Tomcat Total Sent Bytes  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionActiveCurrentCount  |  Tomcat Session Alive Count  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionActiveMaxCount  |  Contagem ativa da sessão Tomcat  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionAliveMaxTime  |  Tomcat Session Max Tempo Vivo  |  Milissegundos  |  Máximo | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionCrioucontagem  |  Contagem criada da sessão tomcat  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  Contagem expirada do TomcatSession  |  Contagem expirada da sessão tomcat  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  Contagem rejeitada de TomcatSession  |  Contagem rejeitada da sessão de Tomcat  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Microservices4Spring/appClusters  |  YoungGenPromotedBytes  |  Promover ao Tamanho de Dados da Geração Jovem  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.NetApp/netAppAccounts/capacityPools  |  VolumePoolAllocatedUsed  |  Pool de volume alocado usado  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.NetApp/netAppAccounts/capacityPools  |  VolumePoolTotalLogicalSize  |  Tamanho lógico total do pool de volume  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.NetApp/netAppContas/capacityPools/volumes  |  AverageReadLatency  |  Latência média de leitura  |  MilliSeconds  |  Média | 
| **Sim**  | Não |  Microsoft.NetApp/netAppContas/capacityPools/volumes  |  AverageWriteLatency  |  Latência média de gravação  |  MilliSeconds  |  Média | 
| **Sim**  | Não |  Microsoft.NetApp/netAppContas/capacityPools/volumes  |  ReadIops  |  IOPS de leitura  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.NetApp/netAppContas/capacityPools/volumes  |  VolumeLogicalSize  |  Tamanho do volume lógico  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.NetApp/netAppContas/capacityPools/volumes  |  VolumeSnapshotSize  |  Tamanho do instantâneo de volume  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.NetApp/netAppContas/capacityPools/volumes  |  WriteIops  |  IOPS de gravação  |  CountPerSecond  |  Média | 
| Não  | Não |  Microsoft.Network/applicationGateways  |  ApplicationGatewayTotalTime  |  Tempo total do gateway de aplicativo  |  MilliSeconds  |  Média | 
| Não  | Não |  Microsoft.Network/applicationGateways  |  AvgRequestCountPerHealthyHost  |  Solicitações por minuto por Host Saudável  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.Network/applicationGateways  |  BackendConnectTime  |  Tempo de conexão backend  |  MilliSeconds  |  Média | 
| Não  | Não |  Microsoft.Network/applicationGateways  |  BackendFirstByteResponseTime  |  Backend First Byte Response Time  |  MilliSeconds  |  Média | 
| Não  | Não |  Microsoft.Network/applicationGateways  |  BackendLastByteResponseTime  |  Backend Last Byte Response Time  |  MilliSeconds  |  Média | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  BackendResponseStatus  |  Status de resposta backend  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  Contagem bloqueada  |  Firewall de aplicativos da Web bloqueou a distribuição de regras de solicitações  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  BloqueadoReqCount  |  Contagem de solicitações bloqueadas do Firewall de Aplicativos da Web  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  BytesReceived  |  Bytes Recebidos  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  BytesSent  |  Bytes Enviados  |  Bytes  |  Total | 
| Não  | Não |  Microsoft.Network/applicationGateways  |  Unidades de capacitação  |  Unidades de capacidade atuais  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.Network/applicationGateways  |  ClienteRtt  |  Cliente RTT  |  MilliSeconds  |  Média | 
| Não  | Não |  Microsoft.Network/applicationGateways  |  ComputaçãoUnidades  |  Unidades atuais de computação  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  CurrentConnections  |  Conexões atuais  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  FailedRequests  |  Solicitações com falha  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  HealthyHostCount  |  Contagem de hosts íntegros  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  Contagem combinada  |  Distribuição total de regras do Firewall de Aplicativos da Web  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  ResponseStatus  |  Status da Resposta  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Network/applicationGateways  |  Produtividade  |  Produtividade  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  TlsProtocol  |  Protocolo TLS do cliente  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  TotalRequests  |  Total de Solicitações  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/applicationGateways  |  UnhealthyHostCount  |  Contagem de hosts não íntegros  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Network/azurefirewalls  |  ApplicationRuleHit  |  Contagem de acerto de regras de aplicação  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/azurefirewalls  |  DataProcessado  |  Dados processados  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Network/azurefirewalls  |  FirewallHealth  |  Estado de saúde do firewall  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Network/azurefirewalls  |  NetworkRuleHit  |  Contagem de acerto de regras de rede  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Network/azurefirewalls  |  SNATPortUtilization  |  Utilização da porta SNAT  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Network/connections  |  BitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Network/connections  |  BitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Network/dnszones  |  QueryVolume  |  Volume de consulta  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.Network/dnszones  |  RecordSetCapacityUtilization  |  Utilização de capacidade de Conjuntos de Registros  |  Porcentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/dnszones  |  RecordSetCount  |  Contagem de Conjuntos de Registros  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Network/expressRouteCircuits  |  ArpAvailability  |  Disponibilidade de Arp  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Network/expressRouteCircuits  |  BgpAvailability  |  Disponibilidade do BGP  |  Porcentagem  |  Média | 
| Não  | Não |  Microsoft.Network/expressRouteCircuits  |  BitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Média | 
| Não  | Não |  Microsoft.Network/expressRouteCircuits  |  BitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Média | 
| Não  | Não |  Microsoft.Network/expressRouteCircuits  |  GlobalReachBitsPerSegundo  |  GlobalReachBitsPerSegundo  |  CountPerSecond  |  Média | 
| Não  | Não |  Microsoft.Network/expressRouteCircuits  |  GlobalReachBitsOutSegundo  |  GlobalReachBitsOutSegundo  |  CountPerSecond  |  Média | 
| Não  | Não |  Microsoft.Network/expressRouteCircuits  |  QosDropBitsInPerSegundo  |  DroppedInBitsPerSecond  |  CountPerSecond  |  Média | 
| Não  | Não |  Microsoft.Network/expressRouteCircuits  |  QosDropBitsOutPerSegundo  |  DroppedOutBitsPerSecond  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Network/expressRouteCircuits/peerings  |  BitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Network/expressRouteCircuits/peerings  |  BitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Média | 
| Não  | Não |  Microsoft.Network/expressRouteGateways  |  ErGatewayConnectionBitsPerSegundo  |  BitsInPerSecond  |  CountPerSecond  |  Média | 
| Não  | Não |  Microsoft.Network/expressRouteGateways  |  ErGatewayConnectionBitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Network/expressRoutePorts  |  AdminState  |  AdminState  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Network/expressRoutePorts  |  Protocolo de linha  |  Protocolo de linha  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Network/expressRoutePorts  |  PortBitsInPerSegundo  |  BitsInPerSecond  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Network/expressRoutePorts  |  PortBitsOutPerSegundo  |  BitsOutPerSecond  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.Network/expressRoutePorts  |  RxLightLevel  |  RxLightLevel  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.Network/expressRoutePorts  |  TxLightLevel  |  TxLightLevel  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Network/frontdoors  |  BackendHealthPercentage  |  Percentual de integridade do back-end  |  Porcentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Network/frontdoors  |  BackendRequestCount  |  Contagem de solicitações de back-end  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Network/frontdoors  |  BackendRequestLatency  |  Latência de solicitação de back-end  |  MilliSeconds  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Network/frontdoors  |  Tamanho da resposta de Billable  |  Tamanho da resposta billable  |  Bytes  |  Total | 
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
| **Sim**  | Não |  Microsoft.Network/networkWatchers/connectionMonitors  |  AverageRoundtripMs  |  Avg. Tempo de ida e volta (ms)  |  MilliSeconds  |  Média | 
| **Sim**  | Não |  Microsoft.Network/networkWatchers/connectionMonitors  |  ChecksFailedPercent  |  Verifica a porcentagem de falha (visualização)  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Network/networkWatchers/connectionMonitors  |  ProbesFailedPercent  |  Porcentagem de investigações com falha  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Network/networkWatchers/connectionMonitors  |  RoundTripTimeMs  |  Tempo de ida e volta (ms) (Visualização)  |  MilliSeconds  |  Média | 
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
| **Sim**  | Não |  Microsoft.Network/virtualNetworks  |  PingMeshAverageRoundtripMs  |  Tempo de ida e volta para Pings para uma VM  |  MilliSeconds  |  Média | 
| **Sim**  | Não |  Microsoft.Network/virtualNetworks  |  PingMeshProbesFailedPercent  |  Pings com falha em uma VM  |  Porcentagem  |  Média | 
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
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. de segundos/Leitura do Disco  |  Avg. Disk sec/Read  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. de segundos/Leitura do Disco  |  Avg. Disk sec/Read  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. de segundos/Transferência do Disco  |  Avg. Disk sec/Transfer  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. de segundos/Gravação do Disco  |  Avg. Disk sec/Write  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. de segundos/Gravação do Disco  |  Avg. Disk sec/Write  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Bytes Received/sec  |  Bytes Recebidos/s  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Bytes Sent/sec  |  Bytes Enviados/s  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Bytes Total/sec  |  Bytes Totais/s  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Current Disk Queue Length  |  Comprimento da fila atual de disco  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Read Bytes/sec  |  Bytes Lidos no Disco/s   |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Reads/sec  |  Leituras de Disco/s   |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Reads/sec  |  Leituras de Disco/s   |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Transfers/sec  |  Transferências de Disco/s  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Transfers/sec  |  Transferências de Disco/s  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Write Bytes/sec  |   Bytes Gravados no Disco/s  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Writes/sec  |   Gravações de Disco/s  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Writes/sec  |   Gravações de Disco/s  |  Contagem  |  Média | 
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
| **Sim**  | Não |  Microsoft.PowerBIDedicated/capacities  |  memory_thrashing_metric  |  Bater na memória (conjuntos de dados)  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.PowerBIDedicated/capacities  |  qpu_high_utilization_metric  |  Alta utilização de QPU  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.PowerBIDedicated/capacities  |  QueryDuration  |  Duração da consulta (Conjuntos de dados)  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.PowerBIDedicated/capacities  |  QueryPoolJobQueueLength  |  Comprimento da fila de trabalho do pool de consulta (Conjuntos de dados)  |  Contagem  |  Média | 
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
| Não  | Não |  Microsoft.ServiceBus/namespaces  |  CPUXNS  |  CPU (Preterida)  |  Porcentagem  |  Máximo | 
| Não  | Não |  Microsoft.ServiceBus/namespaces  |  DeadletteredMessages  |  Contagem de mensagens com letras mortas em uma fila/tópico.  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.ServiceBus/namespaces  |  IncomingMessages  |  Mensagens de entrada  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.ServiceBus/namespaces  |  IncomingRequests  |  Solicitações de entrada  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.ServiceBus/namespaces  |  Mensagens  |  Contagem de mensagens em uma fila/tópico.  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ServiceBus/namespaces  |  NamespaceCpuUso  |  CPU  |  Porcentagem  |  Máximo | 
| Não  | Não |  Microsoft.ServiceBus/namespaces  |  NamespaceMemoryUso  |  Uso de Memória  |  Porcentagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.ServiceBus/namespaces  |  OutgoingMessages  |  Mensagens de saída  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.ServiceBus/namespaces  |  Mensagens agendadas  |  Contagem de mensagens agendadas em uma fila/tópico.  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ServiceBus/namespaces  |  ServerErrors  |  Erros do Servidor.  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.ServiceBus/namespaces  |  Tamanho  |  Tamanho  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.ServiceBus/namespaces  |  SuccessfulRequests  |  Solicitações bem sucedidas  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.ServiceBus/namespaces  |  ThrottledRequests  |  Solicitações Limitadas.  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.ServiceBus/namespaces  |  UserErrors  |  Erros de Usuário.  |  Contagem  |  Total | 
| Não  | Não |  Microsoft.ServiceBus/namespaces  |  WSXNS  |  Uso da memória (preterido)  |  Porcentagem  |  Máximo | 
| Não  | Não |  Microsoft.ServiceFabricMesh/aplicativos  |  Cpu real  |  Cpu real  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ServiceFabricMesh/aplicativos  |  Memória real  |  Memória real  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.ServiceFabricMesh/aplicativos  |  Cpu alocada  |  Cpu alocada  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ServiceFabricMesh/aplicativos  |  Memória alocada  |  Memória alocada  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.ServiceFabricMesh/aplicativos  |  Status do aplicativo  |  Status do aplicativo  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ServiceFabricMesh/aplicativos  |  Status do contêiner  |  Status do contêiner  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ServiceFabricMesh/aplicativos  |  CpuUtilização  |  CpuUtilização  |  Porcentagem  |  Média | 
| Não  | Não |  Microsoft.ServiceFabricMesh/aplicativos  |  MemóriaUtilização  |  MemóriaUtilização  |  Porcentagem  |  Média | 
| Não  | Não |  Microsoft.ServiceFabricMesh/aplicativos  |  Contagem de reinicialização  |  Contagem de reinicialização  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ServiceFabricMesh/aplicativos  |  ServiceReplicaStatus  |  ServiceReplicaStatus  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.ServiceFabricMesh/aplicativos  |  Status de serviço  |  Status de serviço  |  Contagem  |  Média | 
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
| **Sim**  | Não |  Microsoft.Sql/servers/databases  |  app_cpu_percent  |  Porcentagem da CPU do aplicativo  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servers/databases  |  app_memory_percent  |  Porcentagem de memória do aplicativo  |  Porcentagem  |  Média | 
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
| **Sim**  | Não |  Microsoft.Sql/servers/databases  |  memory_usage_percent  |  Porcentagem de memória  |  Porcentagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.Sql/servers/databases  |  physical_data_read_percent  |  Porcentagem de E/S de dados  |  Porcentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servers/databases  |  sessions_percent  |  Porcentagem de sessões  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servers/databases  |  armazenamento  |  Espaço de dados usado  |  Bytes  |  Máximo | 
| **Sim**  | Não |  Microsoft.Sql/servers/databases  |  storage_percent  |  Espaço de dados usado por cento  |  Porcentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Sql/servers/databases  |  tempdb_data_size  |  Tamanho do arquivo de dados tempdb Kilobytes  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Sql/servers/databases  |  tempdb_log_size  |  Tamanho do arquivo de log tempdb Kilobytes  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Sql/servers/databases  |  tempdb_log_used_percent  |  Registro percentual de tempdb usado  |  Porcentagem  |  Máximo | 
| **Sim**  | **Sim** |  Microsoft.Sql/servers/databases  |  workers_percent  |  Porcentagem de funcionários  |  Porcentagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Sql/servers/databases  |  xtp_storage_percent  |  Percentual de armazenamento do OLTP na memória  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servers/elasticPools  |  allocated_data_storage  |  Espaço alocado de dados  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servers/elasticPools  |  allocated_data_storage_percent  |  Espaço de dados alocado por cento  |  Porcentagem  |  Máximo | 
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
| **Sim**  | Não |  Microsoft.Sql/servers/elasticPools  |  storage_percent  |  Espaço de dados usado por cento  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servers/elasticPools  |  storage_used  |  Espaço de dados usado  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Sql/servers/elasticPools  |  tempdb_data_size  |  Tamanho do arquivo de dados tempdb Kilobytes  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Sql/servers/elasticPools  |  tempdb_log_size  |  Tamanho do arquivo de log tempdb Kilobytes  |  Contagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.Sql/servers/elasticPools  |  tempdb_log_used_percent  |  Registro percentual de tempdb usado  |  Porcentagem  |  Máximo | 
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
| Não  | Não |  Microsoft.Storage/storageAccounts/blobServices  |  IndexCapacity  |  Capacidade de índice  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts/blobServices  |  Entrada  |  Entrada  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts/blobServices  |  SuccessE2ELatency  |  Latência de E2E com Sucesso  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts/blobServices  |  SuccessServerLatency  |  Latência de Servidor Com Sucesso  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts/blobServices  |  Transactions  |  Transactions  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts/fileServices  |  Disponibilidade  |  Disponibilidade  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.Storage/storageAccounts/fileServices  |  Saída  |  Saída  |  Bytes  |  Total | 
| Não  | Não |  Microsoft.Storage/storageAccounts/fileServices  |  FileCapacity  |  Capacidade do Arquivo  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.Storage/storageAccounts/fileServices  |  FileCount  |  Contagem de Arquivos  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareCount  |  Contagem de Compartilhamentos de Arquivos  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareQuota  |  Tamanho da cota de compartilhamento de arquivos  |  Bytes  |  Média | 
| Não  | Não |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareSnapshotContag  |  Contagem de instantâneos de compartilhamento de arquivos  |  Contagem  |  Média | 
| Não  | Não |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareSnapshotSize  |  Tamanho do instantâneo do compartilhamento de arquivos  |  Bytes  |  Média | 
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
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ClientIOPS  |  IOPS de cliente total  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  Perfilabilidade do cliente  |  Latência média do cliente  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ClientLockIOPS  |  IOPS de bloqueio de cliente  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ClienteMetadataReadIOPS  |  Metadados do cliente ler IOPS  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ClienteMetadataWriteIOPS  |  IOPS de gravação de metadados do cliente  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ClientReadIOPS  |  Cliente ler IOPS  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ClientReadThroughput  |  Throughput de leitura média de cache  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ClientWriteIOPS  |  IOPS de gravação de cliente  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ClientWriteThroughput  |  Throughput médio de gravação de cache  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ArmazenamentoTargetAsyncWriteThroughputput  |  Throughput de gravação assíncrona de alvo de armazenamento  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ArmazenamentoTargetPreenchimentodepreenchimentoput  |  Throughput de preenchimento de destino de armazenamento  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ArmazenamentoTargetHealth  |  Saúde alvo de armazenamento  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  TargetIOPS de armazenamento  |  IOPS de alvo de armazenamento total  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  Gerenciamento de segmentação de armazenamento  |  Latência de destino de armazenamento  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ArmazenamentoTargetMetadataReadIOPS  |  ArmazenamentoMetadados target ler IOPS  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ArmazenamentoTargetMetadataWriteIOPS  |  ArmazenamentoTarget Metadata Write IOPS  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ArmazenamentoTargetReadAheadThroughput  |  ArmazenamentoTarget Ler throughput à frente  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  Alvo de armazenamentoReadIOPS  |  Alvo de armazenamento Ler IOPS  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ArmazenamentoTargetSyncWriteThroughputput  |  Throughput de gravação síncrona de alvo de armazenamento  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ArmazenamentoTargetTotalReadThroughput  |  Throughput total de leitura do alvo de armazenamento  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ArmazenamentoTargetTotalWriteWriteput  |  Throughput total de gravação de armazenamentotarget  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  ArmazenamentoTargetWriteIOPS  |  IOPS de gravação de alvo de armazenamento  |  Contagem  |  Média | 
| **Sim**  | Não |  Microsoft.StorageCache/caches  |  Tempo de atividade  |  Tempo de atividade  |  Contagem  |  Média | 
| **Sim**  | Não |  microsoft.storageSync/storageSyncServices  |  ServerSyncSessionResult  |  Resultado da sessão de sincronização  |  Contagem  |  Média | 
| **Sim**  | Não |  microsoft.storageSync/storageSyncServices  |  ArmazenamentoSyncBatchTransferredFileBytes  |  Bytes sincronizados  |  Bytes  |  Total | 
| **Sim**  | Não |  microsoft.storageSync/storageSyncServices  |  ArmazenamentoSyncRecalledNetworkBytesByApplication  |  Tamanho de recall de hierarquiagem em nuvem por aplicativo  |  Bytes  |  Total | 
| **Sim**  | Não |  microsoft.storageSync/storageSyncServices  |  ArmazenamentoSyncRecalledTotalNetworkBytes  |  Tamanho de recall de hierarquiagem de nuvem  |  Bytes  |  Total | 
| **Sim**  | Não |  microsoft.storageSync/storageSyncServices  |  ArmazenamentoSyncRecallIOTotalSizeBytes  |  Recall da camada de nuvem  |  Bytes  |  Total | 
| **Sim**  | Não |  microsoft.storageSync/storageSyncServices  |  ArmazenamentoSyncRecallThroughputBytesPerSecond  |  Throughput de recall de hierarquidade na nuvem  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  microsoft.storageSync/storageSyncServices  |  ArmazenamentoSyncServerHeartbeat  |  Status on-line do servidor  |  Contagem  |  Máximo | 
| **Sim**  | Não |  microsoft.storageSync/storageSyncServices  |  ArmazenamentoSincronizaSCSOSistemaDecandidatar-SEAContagemdesCtivaSContagem regressiva  |  Arquivos Sincronizados  |  Contagem  |  Total | 
| **Sim**  | Não |  microsoft.storageSync/storageSyncServices  |  ArmazenamentoSincronizasOSincronizaÇãoDeitensdoper-erroContagemdectosContagem regressiva  |  Arquivos não sincronizando  |  Contagem  |  Total | 
| **Sim**  | Não |  microsoft.storageSync/storageSyncServices/registeredServers  |  Heartbeat do servidor  |  Status on-line do servidor  |  Contagem  |  Máximo | 
| **Sim**  | Não |  microsoft.storageSync/storageSyncServices/registeredServers  |  ServerRecallIOTotalSizeBytes  |  Recall da camada de nuvem  |  Bytes  |  Total | 
| **Sim**  | Não |  microsoft.storageSync/storageSyncServices/syncGroups  |  SyncGroupBatchTransferredFileBytes  |  Bytes sincronizados  |  Bytes  |  Total | 
| **Sim**  | Não |  microsoft.storageSync/storageSyncServices/syncGroups  |  SincronizaçãoGrupoSincronizaçãoSessãoAplicadacontaminaçãoArquivosContagemde  |  Arquivos Sincronizados  |  Contagem  |  Total | 
| **Sim**  | Não |  microsoft.storageSync/storageSyncServices/syncGroups  |  SincronizaçãoGrupoSincronizaçãoConsultaPer-errocontagemContagemdecência  |  Arquivos não sincronizando  |  Contagem  |  Total | 
| **Sim**  | Não |  microsoft.storageSync/storageSyncS/syncGroups/serverEndpoints  |  ServerEndpointBatchTransferBytes  |  Bytes sincronizados  |  Bytes  |  Total | 
| **Sim**  | Não |  microsoft.storageSync/storageSyncS/syncGroups/serverEndpoints  |  ServidorEndpointSyncSessãoderecursosaplicadoSContagemregressiva  |  Arquivos Sincronizados  |  Contagem  |  Total | 
| **Sim**  | Não |  microsoft.storageSync/storageSyncS/syncGroups/serverEndpoints  |  ServidorEndpointSyncConsultaPer-erroproblemaContagemdectosContagem regressiva  |  Arquivos não sincronizando  |  Contagem  |  Total | 
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
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  InputEventsSourcesPerSegundo  |  Fontes de Entrada Recebidas  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  LateInputEvents  |  Eventos de entrada atrasados  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  OutputEvents  |  Eventos de saída  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  OutputWatermarkDelaySeconds  |  Atraso de Marca-d'água  |  Segundos  |  Máximo | 
| **Sim**  | Não |  Microsoft.StreamAnalytics/streamingjobs  |  ResourceUtilization  |  % de utilização do SU  |  Porcentagem  |  Máximo | 
| **Sim**  | Não |  Microsoft.VMwareCloudMáquinas simples/virtuais  |  Bytes de leitura de disco  |  Bytes de leitura de disco  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.VMwareCloudMáquinas simples/virtuais  |  Operações de leitura de disco/Seg  |  Operações de leitura de disco/Seg  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudMáquinas simples/virtuais  |  Bytes de gravação de disco  |  Bytes de gravação de disco  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.VMwareCloudMáquinas simples/virtuais  |  Operações de gravação de disco/Seg  |  Operações de gravação de disco/Seg  |  CountPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudMáquinas simples/virtuais  |  DiskReadBytesPerSecond  |  Disk Read Bytes/Sec  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudMáquinas simples/virtuais  |  DiskReadLatency  |  Latência de leitura de disco  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudMáquinas simples/virtuais  |  Operações de leitura de disco  |  Operações de leitura de disco  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.VMwareCloudMáquinas simples/virtuais  |  DiskWriteBytesPerSecond  |  Disk Write Bytes/Sec  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudMáquinas simples/virtuais  |  Gerenciamento de disco  |  Latência de Gravação de disco  |  Milissegundos  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudMáquinas simples/virtuais  |  Operações de gravação de disco  |  Operações de gravação de disco  |  Contagem  |  Total | 
| **Sim**  | Não |  Microsoft.VMwareCloudMáquinas simples/virtuais  |  MemóriaAtiva  |  Memória Ativa  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudMáquinas simples/virtuais  |  Memóriaconcedida  |  Memória concedida  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudMáquinas simples/virtuais  |  Memóriausada  |  Memória usada  |  Bytes  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudMáquinas simples/virtuais  |  Entrada na rede  |  Entrada na rede  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.VMwareCloudMáquinas simples/virtuais  |  Saída da rede  |  Saída da rede  |  Bytes  |  Total | 
| **Sim**  | Não |  Microsoft.VMwareCloudMáquinas simples/virtuais  |  NetworkInBytesPerSecond  |  Rede em Bytes/Sec  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudMáquinas simples/virtuais  |  NetworkOutBytesPerSecond  |  Rede Out Bytes/Sec  |  BytesPerSecond  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudMáquinas simples/virtuais  |  Porcentagem de CPU  |  Porcentagem de CPU  |  Porcentagem  |  Média | 
| **Sim**  | Não |  Microsoft.VMwareCloudMáquinas simples/virtuais  |  PorcentagemCpuReady  |  Porcentagem de CPU pronta  |  Milissegundos  |  Total | 
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
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  TcpCloseWait  |  TCP Fechar Espera  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  TcpClosing  |  Fechamento do TCP  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  TcpEstabelecido  |  TCP Estabelecido  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  TcpFinWait1  |  TCP Fin Wait 1  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  TcpFinWait2  |  TCP Fin Wait 2  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  TcpLastAck  |  TCP Último Ack  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  TcpSynReceived  |  TCP Syn Recebido  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  TcpSynSent  |  TCP Syn Enviado  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/serverfarms  |  TcpTimeWait  |  Espera por tempo tcp  |  Contagem  |  Média | 
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
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  HealthCheckStatus  |  Situação de verificação de saúde  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Http101  |  Http 101  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Http2xx  |  Http 2xx  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Http3xx  |  Http 3xx  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Http401  |  Http 401  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Http403  |  Http 403  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Http404  |  Http 404  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Http406  |  Http 406  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Http4xx  |  Http 4xx  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  Http5xx  |  Erros do Servidor Http  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites  |  HttpResponseTime  |  Tempo de resposta  |  Segundos  |  Média | 
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
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  HealthCheckStatus  |  Situação de verificação de saúde  |  Contagem  |  Média | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Http101  |  Http 101  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Http2xx  |  Http 2xx  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Http3xx  |  Http 3xx  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Http401  |  Http 401  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Http403  |  Http 403  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Http404  |  Http 404  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Http406  |  Http 406  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Http4xx  |  Http 4xx  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  Http5xx  |  Erros do Servidor Http  |  Contagem  |  Total | 
| **Sim**  | **Sim** |  Microsoft.Web/sites/slots  |  HttpResponseTime  |  Tempo de resposta  |  Segundos  |  Média | 
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
