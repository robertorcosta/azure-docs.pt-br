---
title: Métricas compatíveis por tipo de recurso do Azure Monitor
description: Lista de métricas disponíveis para cada tipo de recurso com o Azure Monitor.
author: anirudhcavale
services: azure-monitor
ms.topic: reference
ms.date: 12/18/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 3e43d2baf4337e7a986d59c47f805183a920c7a1
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77659672"
---
# <a name="supported-metrics-with-azure-monitor"></a>Métricas compatíveis com o Azure Monitor

O Azure Monitor fornece várias maneiras de interagir com as métricas, incluindo a criação de gráficos para os mesmos no portal, acessá-las por meio da API REST ou consultá-las usando o PowerShell ou a CLI. Abaixo está uma lista completa de todas as métricas atualmente disponíveis do pipeline de métrica do Azure Monitor. Outras métricas podem estar disponíveis no portal ou usando as APIs herdadas. Essa lista abaixo inclui apenas as métricas disponíveis usando o pipeline de métrica consolidado do Azure Monitor. As métricas são organizadas por namespace. Para obter uma lista de serviços e os namespaces que pertencem a eles, consulte [provedores de recursos para serviços do Azure](../../azure-resource-manager/management/azure-services-resource-providers.md). Para consultar e acessar essas métricas programaticamente, use a [versão de api 2018-01-01](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)

> [!NOTE]
> Atualmente, não há suporte para o envio da métrica multidimensional por meio das configurações de diagnóstico. As métricas com dimensões são exportadas como métricas dimensionais simples, agregadas nos valores da dimensão.
>
> *Por exemplo*: a métrica “Mensagens de Entrada” em um Hub de Eventos pode ser explorada e mapeada por nível da fila. No entanto, quando exportada por meio das configurações de diagnóstico, a métrica será representada como todas as mensagens de entrada em todas as filas no Hub de Eventos.
>
> Para obter uma lista de métricas de plataforma exportáveis por meio de configurações de diagnóstico, consulte [Este artigo](metrics-supported-export-diagnostic-settings.md).



## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|qpu_metric|QPU|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|QPU. Intervalo de 0 a 100 para S1, 0 a 200 para S2 e 0 a 400 para S4|ServerResourceType|
|memory_metric|Memória|Bytes|Média|Memória. Intervalo de 0 a 25 GB para S1, 0 a 50 GB para S2 e 0 a 100 GB para S4|ServerResourceType|
|private_bytes_metric|Bytes Particulares|Bytes|Média|Bytes privados.|ServerResourceType|
|virtual_bytes_metric|Bytes Virtuais|Bytes|Média|Bytes virtuais.|ServerResourceType|
|TotalConnectionRequests|Solicitações de conexão totais|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Solicitações de conexão totais. Estas são chegadas.|ServerResourceType|
|SuccessfullConnectionsPerSec|Conexões bem-sucedidas por segundo|CountPerSecond|Média|Taxa de conclusões de conexão bem-sucedidas.|ServerResourceType|
|TotalConnectionFailures|Falhas de conexão totais|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Total de falhas em tentativas de conexão.|ServerResourceType|
|CurrentUserSessions|Sessões de usuário atuais|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número atual de sessões de usuário estabelecidas.|ServerResourceType|
|QueryPoolBusyThreads|Threads ocupados do pool de consulta|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de threads ocupados no pool de threads de consulta.|ServerResourceType|
|CommandPoolJobQueueLength|Comprimento da fila de trabalho do pool de comando|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de trabalhos na fila do pool de threads de comando.|ServerResourceType|
|ProcessingPoolJobQueueLength|Comprimento da fila de trabalho do pool de processamento|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de trabalhos não de E/S na fila do pool de threads de processamento.|ServerResourceType|
|CurrentConnections|Conexão: conexões atuais|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|O número atual de conexões de cliente estabelecidas.|ServerResourceType|
|CleanerCurrentPrice|Memória: preço atual do limpador|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Preço atual da memória, $/byte/tempo, normalizado em 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Memória: memória do limpador reduzível|Bytes|Média|Quantidade de memória, em bytes, sujeita a eliminação pelo limpador na tela de fundo.|ServerResourceType|
|CleanerMemoryNonshrinkable|Memória: memória do limpador não reduzível|Bytes|Média|Quantidade de memória, em bytes, não sujeita a eliminação pelo limpador na tela de fundo.|ServerResourceType|
|MemoryUsage|Memória: uso de memória|Bytes|Média|Uso de memória do processo do servidor, como usado no cálculo de preço de memória do limpador. Igual ao contador Processo\PrivateBytes mais o tamanho dos dados mapeados em memória, ignorando qualquer memória mapeada ou alocada pelo mecanismo de análise de memória xVelocity (VertiPaq), além do limite de memória do mecanismo xVelocity.|ServerResourceType|
|MemoryLimitHard|Memória: limite de memória física|Bytes|Média|Limite de memória física, do arquivo de configuração.|ServerResourceType|
|MemoryLimitHigh|Memória: limite de memória superior|Bytes|Média|Limite de memória superior, do arquivo de configuração.|ServerResourceType|
|MemoryLimitLow|Memória: limite de memória inferior|Bytes|Média|Limite de memória inferior, do arquivo de configuração.|ServerResourceType|
|MemoryLimitVertiPaq|Memória: VertiPaq do limite de memória|Bytes|Média|Limite na memória, do arquivo de configuração.|ServerResourceType|
|Cota|Memória: cota|Bytes|Média|Cota de memória atual, em bytes. A cota de memória também é conhecida como uma reserva de memória ou concessão de memória.|ServerResourceType|
|QuotaBlocked|Memória: cota bloqueada|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número atual de solicitações de cota bloqueadas até que outras cotas de memória sejam liberadas.|ServerResourceType|
|VertiPaqNonpaged|Memória: VertiPaq não paginado|Bytes|Média|Bytes de memória bloqueada no conjunto de trabalho para uso pelo mecanismo na memória.|ServerResourceType|
|VertiPaqPaged|Memória: VertiPaq paginado|Bytes|Média|Bytes de memória paginada em uso para dados na memória.|ServerResourceType|
|RowsReadPerSec|Processamento: linhas lidas por segundo|CountPerSecond|Média|Taxa de linhas lidas de todos os bancos de dados relacionais.|ServerResourceType|
|RowsConvertedPerSec|Processamento: linhas convertidas por segundo|CountPerSecond|Média|Taxa de linhas convertidas durante o processamento.|ServerResourceType|
|RowsWrittenPerSec|Processamento: linhas gravadas por segundo|CountPerSecond|Média|Taxa de linhas gravadas durante o processamento.|ServerResourceType|
|CommandPoolBusyThreads|Threads: threads ocupados do pool comando|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de threads ocupados no pool de threads de comando.|ServerResourceType|
|CommandPoolIdleThreads|Threads: threads ociosos do pool de comandos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de threads ociosos no pool de threads de comando.|ServerResourceType|
|LongParsingBusyThreads|Threads: threads ocupados de análise longa|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de threads ocupados no pool de threads de análise longa.|ServerResourceType|
|LongParsingIdleThreads|Threads: threads ociosos de análise longa|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de threads ociosos no pool de threads de análise longa.|ServerResourceType|
|LongParsingJobQueueLength|Threads: tamanho da fila de trabalhos de análise longa|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de trabalhos na fila do pool de threads de análise longa.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Threads: threads de trabalho de E/S ocupados do pool de processamento|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de threads que executam trabalhos de E/S no pool de threads de processamento.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Threads: threads de trabalho não E/S ocupados do pool de processamento|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de threads que executam trabalhos não E/S no pool de threads de processamento.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Threads: tamanho da fila de trabalhos de E/S do pool de processamento|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de trabalhos de E/S na fila do pool de threads de processamento.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Threads: threads de trabalho de E/S ociosos do pool de processamento|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de threads ociosos para trabalhos de E/S no pool de threads de processamento.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Threads: threads de trabalho não E/S ociosos do pool de processamento|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de threads ociosos no pool de threads de processamento dedicado a trabalhos não E/S.|ServerResourceType|
|QueryPoolIdleThreads|Threads: threads ociosos do pool de consultas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de threads ociosos para trabalhos de E/S no pool de threads de processamento.|ServerResourceType|
|QueryPoolJobQueueLength|Threads: tamanho da fila de trabalhos do pool consultas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de trabalhos na fila do pool de threads de consulta.|ServerResourceType|
|ShortParsingBusyThreads|Threads: threads ocupados de análise resumida|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de threads ocupados no pool de threads de análise resumida.|ServerResourceType|
|ShortParsingIdleThreads|Threads: threads ociosos de análise resumida|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de threads ociosos no pool de threads de análise resumida.|ServerResourceType|
|ShortParsingJobQueueLength|Threads: tamanho da fila de trabalhos de análise resumida|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de trabalhos na fila do pool de threads de análise resumida.|ServerResourceType|
|memory_thrashing_metric|Sobrecarga de memória|Porcentagem|Média|Sobrecarga de memória média.|ServerResourceType|
|mashup_engine_qpu_metric|QPU do mecanismo M|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Uso de QPU por processos de mecanismo de mashup|ServerResourceType|
|mashup_engine_memory_metric|Memória do mecanismo M|Bytes|Média|Uso de memória pelos processos do mecanismo de mashup|ServerResourceType|
|mashup_engine_private_bytes_metric|Bytes privados do mecanismo M|Bytes|Média|Uso de bytes privados por processos do mecanismo mashup.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|Bytes virtuais do mecanismo M|Bytes|Média|Uso de bytes virtuais por processos do mecanismo mashup.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|TotalRequests|Total de solicitações do gateway (preterido)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de solicitações de gateway-use a métrica de solicitação de várias dimensões com a dimensão GatewayResponseCodeCategory em vez disso|Localização, nome do host|
|SuccessfulRequests|Solicitações de gateway com êxito (preteridas)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de solicitações de gateway bem-sucedidas-use a métrica de solicitação de várias dimensões com a dimensão GatewayResponseCodeCategory em vez disso|Localização, nome do host|
|UnauthorizedRequests|Solicitações de gateway não autorizadas (preteridas)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de solicitações de gateway não autorizadas-use a métrica de solicitação de várias dimensões com a dimensão GatewayResponseCodeCategory em vez disso|Localização, nome do host|
|FailedRequests|Solicitações de gateway com falha (preteridas)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de falhas em solicitações de gateway-use a métrica de solicitação de várias dimensões com a dimensão GatewayResponseCodeCategory em vez disso|Localização, nome do host|
|OtherRequests|Outras solicitações de gateway (preteridas)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de outras solicitações de gateway – use a métrica de solicitação de várias dimensões com a dimensão GatewayResponseCodeCategory em vez disso|Localização, nome do host|
|Duration|Duração total de solicitações de gateway|Milissegundos|Média|Duração total de solicitações de gateway em milissegundos|Localização, nome do host|
|BackendDuration|Duração de solicitações de back-end|Milissegundos|Média|Duração de solicitações de back-end em milissegundos|Localização, nome do host|
|Capacity|Capacity|Porcentagem|Média|Métrica de utilização para o serviço ApiManagement|Local|
|EventHubTotalEvents|Total de eventos do EventHub|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de eventos enviados ao EventHub|Local|
|EventHubSuccessfulEvents|Eventos de EventHub bem-sucedidos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de eventos de EventHub bem-sucedidos|Local|
|EventHubTotalFailedEvents|Eventos de EventHub com falha|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de eventos de EventHub com falha|Local|
|EventHubRejectedEvents|Eventos de EventHub rejeitados|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de eventos do EventHub rejeitados (configuração incorreta ou não autorizada)|Local|
|EventHubThrottledEvents|Eventos de EventHub limitados|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de eventos regulados do EventHub|Local|
|EventHubTimedoutEvents|Eventos do EventHub com tempo limite excedido|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de eventos do EventHub com tempo limite excedido|Local|
|EventHubDroppedEvents|Eventos do EventHub ignorados|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de eventos ignorados devido ao limite de tamanho de fila atingido|Local|
|EventHubTotalBytesSent|Tamanho dos eventos do EventHub|Bytes|Total|Tamanho total dos eventos do EventHub em bytes|Local|
|Solicitações|Solicitações|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Métricas de solicitação de gateway com várias dimensões|Local, nome do host, LastErrorReason, BackendResponseCode, GatewayResponseCode, BackendResponseCodeCategory, GatewayResponseCodeCategory|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|HttpIncomingRequestCount|HttpIncomingRequestCount|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Número total de solicitações HTTP de entrada.|Nenhum|
|FailedHttpRequestCount|FailedHttpRequestCount|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Solicitações HTTP com falha.|Nenhum|
|HttpIncomingRequestDuration|HttpIncomingRequestDuration|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Latência em uma solicitação HTTP.|Nenhum|


## <a name="microsoftappplatformspring"></a>Microsoft. AppPlatform/Spring

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|SystemCpuUsagePercentage|Porcentagem de uso da CPU do sistema|Porcentagem|Média|O uso recente da CPU para todo o sistema|AppName, Pod|
|AppCpuUsagePercentage|Porcentagem de uso de CPU do aplicativo|Porcentagem|Média|Porcentagem de uso da CPU da JVM do aplicativo|AppName, Pod|
|AppMemoryCommitted|Memória de aplicativo atribuída|Bytes|Média|Memória atribuída à JVM em bytes|AppName, Pod|
|AppMemoryUsed|Memória usada do aplicativo|Bytes|Média|Memória do aplicativo usada em bytes|AppName, Pod|
|AppMemoryMax|Memória máxima do aplicativo|Bytes|Máximo|A quantidade máxima de memória, em bytes, que pode ser usada para gerenciamento de memória|AppName, Pod|
|MaxOldGenMemoryPoolBytes|Tamanho máximo de dados de geração antiga disponíveis|Bytes|Média|Tamanho máximo do pool de memória de geração antiga|AppName, Pod|
|OldGenMemoryPoolBytes|Tamanho de dados de geração antiga|Bytes|Média|Tamanho do pool de memória de geração antiga após um GC completo|AppName, Pod|
|OldGenPromotedBytes|Promover para tamanho de dados de geração antiga|Bytes|Máximo|Contagem de aumentos positivos no tamanho do pool de memória de geração antiga antes do GC ser após o GC|AppName, Pod|
|YoungGenPromotedBytes|Promover para tamanho de dados de geração jovem|Bytes|Máximo|Incrementado para um aumento no tamanho do pool de memória de geração jovem após um GC antes do próximo|AppName, Pod|
|GCPauseTotalCount|Contagem de pausar do GC|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Contagem de pausar do GC|AppName, Pod|
|GCPauseTotalTime|Tempo total de pausa do GC|Milissegundos|Total|Tempo total de pausa do GC|AppName, Pod|
|TomcatSentBytes|Total de bytes enviados do Tomcat|Bytes|Total|Total de bytes enviados do Tomcat|AppName, Pod|
|TomcatReceivedBytes|Total de bytes recebidos do Tomcat|Bytes|Total|Total de bytes recebidos do Tomcat|AppName, Pod|
|TomcatRequestTotalTime|Tempos totais da solicitação do Tomcat|Milissegundos|Total|Tempos totais da solicitação do Tomcat|AppName, Pod|
|TomcatRequestTotalCount|Contagem total de solicitações do Tomcat|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Contagem total de solicitações do Tomcat|AppName, Pod|
|TomcatResponseAvgTime|Tempo médio da solicitação do Tomcat|Milissegundos|Média|Tempo médio da solicitação do Tomcat|AppName, Pod|
|TomcatRequestMaxTime|Tempo máximo de solicitação do Tomcat|Milissegundos|Máximo|Tempo máximo de solicitação do Tomcat|AppName, Pod|
|TomcatErrorCount|Erro global do Tomcat|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Erro global do Tomcat|AppName, Pod|
|TomcatSessionActiveMaxCount|Contagem ativa máxima da sessão do Tomcat|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Contagem ativa máxima da sessão do Tomcat|AppName, Pod|
|TomcatSessionAliveMaxTime|Tempo de atividade máx de sessão do Tomcat|Milissegundos|Máximo|Tempo de atividade máx de sessão do Tomcat|AppName, Pod|
|TomcatSessionActiveCurrentCount|Contagem de sessões ativas de Tomcat|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Contagem de sessões ativas de Tomcat|AppName, Pod|
|TomcatSessionCreatedCount|Contagem de sessões criadas do Tomcat|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Contagem de sessões criadas do Tomcat|AppName, Pod|
|TomcatSessionExpiredCount|Contagem de sessões expiradas do Tomcat|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Contagem de sessões expiradas do Tomcat|AppName, Pod|
|TomcatSessionRejectedCount|Contagem rejeitada da sessão Tomcat|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Contagem rejeitada da sessão Tomcat|AppName, Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|TotalJob|Total de trabalhos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|O número total de trabalhos|Runbook, status|
|TotalUpdateDeploymentRuns|Total de execuções de implantação de atualização|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Total de execuções de implantação de atualização de software|SoftwareUpdateConfigurationName, status|
|TotalUpdateDeploymentMachineRuns|Total de execuções do computador de implantação de atualização|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|O computador de implantação de atualização de software total é executado em uma execução de implantação de atualização de software|SoftwareUpdateConfigurationName, status, Computadordedestino, SoftwareUpdateConfigurationRunId|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|CoreCount|Contagem de núcleos dedicados|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número total de núcleos dedicados na conta do lote|Nenhum|
|TotalNodeCount|Contagem de nós dedicados|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número total de nós dedicados na conta do lote|Nenhum|
|LowPriorityCoreCount|Contagem de núcleos LowPriority|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número total de núcleos de baixa prioridade na conta do lote|Nenhum|
|TotalLowPriorityNodeCount|Contagem de nós de baixa prioridade|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número total de nós de baixa prioridade na conta do lote|Nenhum|
|CreatingNodeCount|Criação de contagem de nós|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de nós sendo criados|Nenhum|
|StartingNodeCount|Contagem inicial dos nós|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de nós iniciais|Nenhum|
|WaitingForStartTaskNodeCount|Contagem de nós para tarefa de inicialização em espera|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de nós aguardando a conclusão da Tarefa de Inicialização|Nenhum|
|StartTaskFailedNodeCount|Falha na contagem de nós para a tarefa de inicialização|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de nós com falha na tarefa de inicialização|Nenhum|
|IdleNodeCount|Contagem de nós ociosos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de nós ociosos|Nenhum|
|OfflineNodeCount|Contagem de nós off-line|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de nós off-line|Nenhum|
|RebootingNodeCount|Contagem de nós de reinicialização|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de nós de reinicialização|Nenhum|
|ReimagingNodeCount|Contagem de nós refazendo a imagem|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de nós refazendo a imagem|Nenhum|
|RunningNodeCount|Contagem de nós em execução|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de nós em execução|Nenhum|
|LeavingPoolNodeCount|Contagem de nós saindo do pool|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de nós saindo do pool|Nenhum|
|UnusableNodeCount|Contagem de nós inutilizáveis|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de nós inutilizáveis|Nenhum|
|PreemptedNodeCount|Contagem de nós com preempção|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de nós com preempção|Nenhum|
|TaskStartEvent|Eventos da tarefa de inicialização|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número total de tarefas que iniciaram|Nenhum|
|TaskCompleteEvent|Eventos de conclusão de tarefa|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número total de tarefas concluídas|Nenhum|
|TaskFailEvent|Eventos de falha de tarefa|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número total de tarefas que foram concluídas em um estado com falha|Nenhum|
|PoolCreateEvent|Eventos de criação de pool|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número total de pools criados|Nenhum|
|PoolResizeStartEvent|Eventos de início de redimensionamento de pool|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número total de tarefas de redimensionamento de pool que iniciaram|Nenhum|
|PoolResizeCompleteEvent|Eventos de conclusão de redimensionamento de pool|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número total de redimensionamentos de pool concluídos|Nenhum|
|PoolDeleteStartEvent|Eventos de início de exclusão de pool|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número total de exclusões de pool iniciados|Nenhum|
|PoolDeleteCompleteEvent|Eventos de conclusão de exclusão do pool|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número total de exclusões de pool concluídas|Nenhum|
|JobDeleteCompleteEvent|Eventos de conclusão de exclusão do trabalho|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número total de trabalhos que foram excluídos com êxito.|Nenhum|
|JobDeleteStartEvent|Eventos de início de exclusão do trabalho|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número total de trabalhos que foram solicitados a serem excluídos.|Nenhum|
|JobDisableCompleteEvent|Eventos de conclusão de desabilitação do trabalho|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número total de trabalhos que foram desabilitados com êxito.|Nenhum|
|JobDisableStartEvent|Eventos de início de desabilitação do trabalho|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número total de trabalhos que foram solicitados a serem desabilitados.|Nenhum|
|JobStartEvent|Eventos de início do trabalho|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número total de trabalhos que foram iniciados com êxito.|Nenhum|
|JobTerminateCompleteEvent|Eventos de conclusão de encerramento do trabalho|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número total de trabalhos que foram terminados com êxito.|Nenhum|
|JobTerminateStartEvent|Eventos de início de encerramento do trabalho|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número total de trabalhos que foram solicitados a serem encerrados.|Nenhum|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft. BatchAI/Workspaces

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|Trabalho enviado|Trabalho enviado|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de trabalhos enviados|Cenário, ClusterName|
|Trabalho concluído|Trabalho concluído|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de trabalhos concluídos|Cenário, ClusterName, Resultadotype|
|Total de nós|Total de nós|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número total de nós|Cenário, ClusterName|
|Nós ativos|Nós ativos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de nós em execução|Cenário, ClusterName|
|Nós ociosos|Nós ociosos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de nós ociosos|Cenário, ClusterName|
|Nós inutilizáveis|Nós inutilizáveis|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de nós inutilizáveis|Cenário, ClusterName|
|Nós admitidos|Nós admitidos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de nós com preempção|Cenário, ClusterName|
|Saindo de nós|Saindo de nós|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de nós de saída|Cenário, ClusterName|
|Total de núcleos|Total de núcleos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de núcleos totais|Cenário, ClusterName|
|Núcleos ativos|Núcleos ativos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de núcleos ativos|Cenário, ClusterName|
|Núcleos ociosos|Núcleos ociosos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de núcleos ociosos|Cenário, ClusterName|
|Núcleos inutilizáveis|Núcleos inutilizáveis|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de núcleos inutilizáveis|Cenário, ClusterName|
|Núcleos preempçãos|Núcleos preempçãos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de núcleos admitidos|Cenário, ClusterName|
|Deixando núcleos|Deixando núcleos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de núcleos de saída|Cenário, ClusterName|
|Porcentagem de utilização de cota|Porcentagem de utilização de cota|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Porcentagem de cota utilizada|Cenário, ClusterName, VmFamilyName, VmPriority|

## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|CpuUsagePercentageInDouble|Porcentagem de uso da CPU|Porcentagem|Máximo|Porcentagem de uso da CPU|Nó|
|MemoryUsage|Utilização de Memória|Bytes|Média|Utilização de Memória|Nó|
|MemoryLimit|Limite de memória|Bytes|Média|Limite de memória|Nó|
|MemoryUsagePercentageInDouble|Porcentagem de uso da memória|Porcentagem|Média|Porcentagem de uso da memória|Nó|
|StorageUsage|Uso do armazenamento|Bytes|Média|Uso do armazenamento|Nó|
|IOReadBytes|Bytes de leitura de e/s|Bytes|Total|Bytes de leitura de e/s|Nó|
|IOWriteBytes|Bytes de gravação de e/s|Bytes|Total|Bytes de gravação de e/s|Nó|
|ConnectionAccepted|Conexões aceitas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Conexões aceitas|Nó|
|ConnectionHandled|Conexões manipuladas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Conexões manipuladas|Nó|
|ConnectionActive|Conexões ativas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Conexões ativas|Nó|
|RequestHandled|Solicitações manipuladas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Solicitações manipuladas|Nó|
|ProcessedBlocks|Blocos processados|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Blocos processados|Nó|
|ProcessedTransactions|Transações processadas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Transações processadas|Nó|
|PendingTransactions|Transações pendentes|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Transações pendentes|Nó|
|QueuedTransactions|Transações em fila|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Transações em fila|Nó|



## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|connectedclients|Clientes Conectados|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo||ShardId|
|totalcommandsprocessed|Total de Operações|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||ShardId|
|cachehits|Acertos do Cache|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||ShardId|
|cachemisses|Erros de Cache|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||ShardId|
|cachemissrate|Taxa de erros de cache|Porcentagem|cachemissrate||ShardId|
|getcommands|Obtém|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||ShardId|
|setcommands|Conjuntos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||ShardId|
|operationsPerSecond|Operations por segundo|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo||ShardId|
|evictedkeys|Chaves removidas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||ShardId|
|totalkeys|Total de chaves|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo||ShardId|
|expiredkeys|Chaves expiradas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||ShardId|
|usedmemory|Memória utilizada|Bytes|Máximo||ShardId|
|usedmemorypercentage|Porcentagem de memória utilizada|Porcentagem|Máximo||ShardId|
|usedmemoryRss|Memória RSS Usada|Bytes|Máximo||ShardId|
|serverLoad|Carga do Servidor|Porcentagem|Máximo||ShardId|
|cacheWrite|Gravação no Cache|BytesPerSecond|Máximo||ShardId|
|cacheRead|Cache Lido|BytesPerSecond|Máximo||ShardId|
|percentProcessorTime|CPU|Porcentagem|Máximo||ShardId|
|cacheLatency|Microssegundos de latência de cache (versão prévia)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média||ShardId|
|erros|Erros|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo||Fragmentid, ErrorType|
|connectedclients0|Clientes conectados (Fragmento 0)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo||Nenhum|
|totalcommandsprocessed0|Total de operações (Fragmento 0)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|cachehits0|Ocorrências no cache (Fragmento 0)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|cachemisses0|Perdas no cache (Fragmento 0)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|getcommands0|Gets (Fragmento 0)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|setcommands0|Sets (Fragmento 0)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|operationsPerSecond0|Operações por segundo (fragmento 0)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo||Nenhum|
|evictedkeys0|Chaves removidas (Fragmento 0)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|totalkeys0|Total de Chaves (Fragmento 0)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo||Nenhum|
|expiredkeys0|Chaves expiradas (Fragmento 0)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|usedmemory0|Memória usada (Fragmento 0)|Bytes|Máximo||Nenhum|
|usedmemoryRss0|Memória RSS usada (Fragmento 0)|Bytes|Máximo||Nenhum|
|serverLoad0|Carga do servidor (Fragmento 0)|Porcentagem|Máximo||Nenhum|
|cacheWrite0|Gravação no cache (Fragmento 0)|BytesPerSecond|Máximo||Nenhum|
|cacheRead0|Leitura no cache (Fragmento 0)|BytesPerSecond|Máximo||Nenhum|
|percentProcessorTime0|CPU (Fragmento 0)|Porcentagem|Máximo||Nenhum|
|connectedclients1|Clientes conectados (Fragmento 1)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo||Nenhum|
|totalcommandsprocessed1|Total de operações (Fragmento 1)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|cachehits1|Ocorrências no cache (Fragmento 1)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|cachemisses1|Perdas no cache (Fragmento 1)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|getcommands1|Gets (Fragmento 1)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|setcommands1|Sets (Fragmento 1)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|operationsPerSecond1|Operações por segundo (fragmento 1)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo||Nenhum|
|evictedkeys1|Chaves removidas (Fragmento 1)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|totalkeys1|Total de Chaves (Fragmento 1)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo||Nenhum|
|expiredkeys1|Chaves expiradas (Fragmento 1)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|usedmemory1|Memória usada (Fragmento 1)|Bytes|Máximo||Nenhum|
|usedmemoryRss1|Memória RSS usada (Fragmento 1)|Bytes|Máximo||Nenhum|
|serverLoad1|Carga do servidor (Fragmento 1)|Porcentagem|Máximo||Nenhum|
|cacheWrite1|Gravação no cache (Fragmento 1)|BytesPerSecond|Máximo||Nenhum|
|cacheRead1|Leitura no cache (Fragmento 1)|BytesPerSecond|Máximo||Nenhum|
|percentProcessorTime1|CPU (Fragmento 1)|Porcentagem|Máximo||Nenhum|
|connectedclients2|Clientes conectados (Fragmento 2)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo||Nenhum|
|totalcommandsprocessed2|Total de operações (Fragmento 2)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|cachehits2|Ocorrências no cache (Fragmento 2)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|cachemisses2|Perdas no cache (Fragmento 2)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|getcommands2|Gets (Fragmento 2)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|setcommands2|Sets (Fragmento 2)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|operationsPerSecond2|Operações por segundo (fragmento 2)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo||Nenhum|
|evictedkeys2|Chaves removidas (Fragmento 2)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|totalkeys2|Total de Chaves (Fragmento 2)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo||Nenhum|
|expiredkeys2|Chaves expiradas (Fragmento 2)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|usedmemory2|Memória usada (Fragmento 2)|Bytes|Máximo||Nenhum|
|usedmemoryRss2|Memória RSS usada (Fragmento 2)|Bytes|Máximo||Nenhum|
|serverLoad2|Carga do servidor (Fragmento 2)|Porcentagem|Máximo||Nenhum|
|cacheWrite2|Gravação no cache (Fragmento 2)|BytesPerSecond|Máximo||Nenhum|
|cacheRead2|Leitura no cache (Fragmento 2)|BytesPerSecond|Máximo||Nenhum|
|percentProcessorTime2|CPU (Fragmento 2)|Porcentagem|Máximo||Nenhum|
|connectedclients3|Clientes conectados (Fragmento 3)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo||Nenhum|
|totalcommandsprocessed3|Total de operações (Fragmento 3)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|cachehits3|Ocorrências no cache (Fragmento 3)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|cachemisses3|Perdas no cache (Fragmento 3)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|getcommands3|Gets (Fragmento 3)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|setcommands3|Sets (Fragmento 3)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|operationsPerSecond3|Operações por segundo (fragmento 3)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo||Nenhum|
|evictedkeys3|Chaves removidas (Fragmento 3)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|totalkeys3|Total de Chaves (Fragmento 3)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo||Nenhum|
|expiredkeys3|Chaves expiradas (Fragmento 3)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|usedmemory3|Memória usada (Fragmento 3)|Bytes|Máximo||Nenhum|
|usedmemoryRss3|Memória RSS usada (Fragmento 3)|Bytes|Máximo||Nenhum|
|serverLoad3|Carga do servidor (Fragmento 3)|Porcentagem|Máximo||Nenhum|
|cacheWrite3|Gravação no cache (Fragmento 3)|BytesPerSecond|Máximo||Nenhum|
|cacheRead3|Leitura no cache (Fragmento 3)|BytesPerSecond|Máximo||Nenhum|
|percentProcessorTime3|CPU (Fragmento 3)|Porcentagem|Máximo||Nenhum|
|connectedclients4|Clientes conectados (Fragmento 4)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo||Nenhum|
|totalcommandsprocessed4|Total de operações (Fragmento 4)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|cachehits4|Ocorrências no cache (Fragmento 4)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|cachemisses4|Perdas no cache (Fragmento 4)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|getcommands4|Gets (Fragmento 4)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|setcommands4|Sets (Fragmento 4)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|operationsPerSecond4|Operações por segundo (fragmento 4)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo||Nenhum|
|evictedkeys4|Chaves removidas (Fragmento 4)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|totalkeys4|Total de Chaves (Fragmento 4)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo||Nenhum|
|expiredkeys4|Chaves expiradas (Fragmento 4)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|usedmemory4|Memória usada (Fragmento 4)|Bytes|Máximo||Nenhum|
|usedmemoryRss4|Memória RSS usada (Fragmento 4)|Bytes|Máximo||Nenhum|
|serverLoad4|Carga do servidor (Fragmento 4)|Porcentagem|Máximo||Nenhum|
|cacheWrite4|Gravação no cache (Fragmento 4)|BytesPerSecond|Máximo||Nenhum|
|cacheRead4|Leitura no cache (Fragmento 4)|BytesPerSecond|Máximo||Nenhum|
|percentProcessorTime4|CPU (Fragmento 4)|Porcentagem|Máximo||Nenhum|
|connectedclients5|Clientes conectados (Fragmento 5)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo||Nenhum|
|totalcommandsprocessed5|Total de operações (Fragmento 5)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|cachehits5|Ocorrências no cache (Fragmento 5)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|cachemisses5|Perdas no cache (Fragmento 5)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|getcommands5|Gets (Fragmento 5)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|setcommands5|Sets (Fragmento 5)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|operationsPerSecond5|Operações por segundo (fragmento 5)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo||Nenhum|
|evictedkeys5|Chaves removidas (Fragmento 5)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|totalkeys5|Total de Chaves (Fragmento 5)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo||Nenhum|
|expiredkeys5|Chaves expiradas (Fragmento 5)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|usedmemory5|Memória usada (Fragmento 5)|Bytes|Máximo||Nenhum|
|usedmemoryRss5|Memória RSS usada (Fragmento 5)|Bytes|Máximo||Nenhum|
|serverLoad5|Carga do servidor (Fragmento 5)|Porcentagem|Máximo||Nenhum|
|cacheWrite5|Gravação no cache (Fragmento 5)|BytesPerSecond|Máximo||Nenhum|
|cacheRead5|Leitura no cache (Fragmento 5)|BytesPerSecond|Máximo||Nenhum|
|percentProcessorTime5|CPU (Fragmento 5)|Porcentagem|Máximo||Nenhum|
|connectedclients6|Clientes conectados (Fragmento 6)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo||Nenhum|
|totalcommandsprocessed6|Total de operações (Fragmento 6)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|cachehits6|Ocorrências no cache (Fragmento 6)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|cachemisses6|Perdas no cache (Fragmento 6)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|getcommands6|Gets (Fragmento 6)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|setcommands6|Sets (Fragmento 6)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|operationsPerSecond6|Operações por segundo (fragmento 6)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo||Nenhum|
|evictedkeys6|Chaves removidas (Fragmento 6)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|totalkeys6|Total de Chaves (Fragmento 6)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo||Nenhum|
|expiredkeys6|Chaves expiradas (Fragmento 6)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|usedmemory6|Memória usada (Fragmento 6)|Bytes|Máximo||Nenhum|
|usedmemoryRss6|Memória RSS usada (Fragmento 6)|Bytes|Máximo||Nenhum|
|serverLoad6|Carga do servidor (Fragmento 6)|Porcentagem|Máximo||Nenhum|
|cacheWrite6|Gravação no cache (Fragmento 6)|BytesPerSecond|Máximo||Nenhum|
|cacheRead6|Leitura no cache (Fragmento 6)|BytesPerSecond|Máximo||Nenhum|
|percentProcessorTime6|CPU (Fragmento 6)|Porcentagem|Máximo||Nenhum|
|connectedclients7|Clientes conectados (Fragmento 7)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo||Nenhum|
|totalcommandsprocessed7|Total de operações (Fragmento 7)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|cachehits7|Ocorrências no cache (Fragmento 7)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|cachemisses7|Perdas no cache (Fragmento 7)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|getcommands7|Gets (Fragmento 7)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|setcommands7|Sets (Fragmento 7)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|operationsPerSecond7|Operações por segundo (fragmento 7)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo||Nenhum|
|evictedkeys7|Chaves removidas (Fragmento 7)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|totalkeys7|Total de Chaves (Fragmento 7)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo||Nenhum|
|expiredkeys7|Chaves expiradas (Fragmento 7)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|usedmemory7|Memória usada (Fragmento 7)|Bytes|Máximo||Nenhum|
|usedmemoryRss7|Memória RSS usada (Fragmento 7)|Bytes|Máximo||Nenhum|
|serverLoad7|Carga do servidor (Fragmento 7)|Porcentagem|Máximo||Nenhum|
|cacheWrite7|Gravação no cache (Fragmento 7)|BytesPerSecond|Máximo||Nenhum|
|cacheRead7|Leitura no cache (Fragmento 7)|BytesPerSecond|Máximo||Nenhum|
|percentProcessorTime7|CPU (Fragmento 7)|Porcentagem|Máximo||Nenhum|
|connectedclients8|Clientes conectados (Fragmento 8)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo||Nenhum|
|totalcommandsprocessed8|Total de operações (Fragmento 8)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|cachehits8|Ocorrências no cache (Fragmento 8)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|cachemisses8|Perdas no cache (Fragmento 8)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|getcommands8|Gets (Fragmento 8)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|setcommands8|Sets (Fragmento 8)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|operationsPerSecond8|Operações por segundo (fragmento 8)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo||Nenhum|
|evictedkeys8|Chaves removidas (Fragmento 8)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|totalkeys8|Total de Chaves (Fragmento 8)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo||Nenhum|
|expiredkeys8|Chaves expiradas (Fragmento 8)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|usedmemory8|Memória usada (Fragmento 8)|Bytes|Máximo||Nenhum|
|usedmemoryRss8|Memória RSS usada (Fragmento 8)|Bytes|Máximo||Nenhum|
|serverLoad8|Carga do servidor (Fragmento 8)|Porcentagem|Máximo||Nenhum|
|cacheWrite8|Gravação no cache (Fragmento 8)|BytesPerSecond|Máximo||Nenhum|
|cacheRead8|Leitura no cache (Fragmento 8)|BytesPerSecond|Máximo||Nenhum|
|percentProcessorTime8|CPU (Fragmento 8)|Porcentagem|Máximo||Nenhum|
|connectedclients9|Clientes conectados (Fragmento 9)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo||Nenhum|
|totalcommandsprocessed9|Total de operações (Fragmento 9)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|cachehits9|Ocorrências no cache (Fragmento 9)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|cachemisses9|Perdas no cache (Fragmento 9)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|getcommands9|Gets (Fragmento 9)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|setcommands9|Sets (Fragmento 9)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|operationsPerSecond9|Operações por segundo (fragmento 9)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo||Nenhum|
|evictedkeys9|Chaves removidas (Fragmento 9)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|totalkeys9|Total de Chaves (Fragmento 9)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo||Nenhum|
|expiredkeys9|Chaves expiradas (Fragmento 9)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nenhum|
|usedmemory9|Memória usada (Fragmento 9)|Bytes|Máximo||Nenhum|
|usedmemoryRss9|Memória RSS usada (Fragmento 9)|Bytes|Máximo||Nenhum|
|serverLoad9|Carga do servidor (Fragmento 9)|Porcentagem|Máximo||Nenhum|
|cacheWrite9|Gravação no cache (Fragmento 9)|BytesPerSecond|Máximo||Nenhum|
|cacheRead9|Leitura no cache (Fragmento 9)|BytesPerSecond|Máximo||Nenhum|
|percentProcessorTime9|CPU (Fragmento 9)|Porcentagem|Máximo||Nenhum|



## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/cdnwebapplicationfirewallpolicies

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Contagem de solicitações do Firewall de Aplicativo Web|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|O número de solicitações de cliente processadas pelo Firewall do Aplicativo Web|PolicyName, RuleName, ação|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|Porcentagem de CPU|Porcentagem de CPU|Porcentagem|Média|O percentual das unidades de computação alocadas que estão atualmente em uso pelas Máquinas Virtuais.|Nenhum|
|Entrada na rede|Entrada na rede|Bytes|Total|O número de bytes recebidos em todos os adaptadores de rede pelas Máquinas Virtuais (Tráfego de Entrada).|Nenhum|
|Saída da rede|Saída da rede|Bytes|Total|O número de bytes de saída em todos os adaptadores de rede pelas Máquinas Virtuais (Tráfego de Saída).|Nenhum|
|Disk Read Bytes/Sec|Leitura de disco|BytesPerSecond|Média|Média de bytes lidos do disco durante o período de monitoramento.|Nenhum|
|Disk Write Bytes/Sec|Gravação de disco|BytesPerSecond|Média|Média de bytes gravados em disco durante o período de monitoramento.|Nenhum|
|Operações de leitura de disco/Seg|Operações de leitura de disco/Seg|CountPerSecond|Média|IOPS de leitura de disco.|Nenhum|
|Operações de gravação de disco/Seg|Operações de gravação de disco/Seg|CountPerSecond|Média|IOPS de gravação de disco.|Nenhum|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|Porcentagem de CPU|Porcentagem de CPU|Porcentagem|Média|O percentual das unidades de computação alocadas que estão atualmente em uso pelas Máquinas Virtuais.|RoleInstanceId|
|Entrada na rede|Entrada na rede|Bytes|Total|O número de bytes recebidos em todos os adaptadores de rede pelas Máquinas Virtuais (Tráfego de Entrada).|RoleInstanceId|
|Saída da rede|Saída da rede|Bytes|Total|O número de bytes de saída em todos os adaptadores de rede pelas Máquinas Virtuais (Tráfego de Saída).|RoleInstanceId|
|Disk Read Bytes/Sec|Leitura de disco|BytesPerSecond|Média|Média de bytes lidos do disco durante o período de monitoramento.|RoleInstanceId|
|Disk Write Bytes/Sec|Gravação de disco|BytesPerSecond|Média|Média de bytes gravados em disco durante o período de monitoramento.|RoleInstanceId|
|Operações de leitura de disco/Seg|Operações de leitura de disco/Seg|CountPerSecond|Média|IOPS de leitura de disco.|RoleInstanceId|
|Operações de gravação de disco/Seg|Operações de gravação de disco/Seg|CountPerSecond|Média|IOPS de gravação de disco.|RoleInstanceId|



## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft.ClassicStorage/storageAccounts

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|UsedCapacity|Capacidade utilizada|Bytes|Média|Capacidade utilizada pela conta|Nenhum|
|Transações|Transações|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|ResponseType, GeoType, ApiName, autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|Geotipo, ApiName, autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável.|Geotipo, ApiName, autenticação|
|SuccessServerLatency|Latência de Servidor Com Sucesso|Milissegundos|Média|A latência usada pelo armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Esse valor não inclui a latência de rede especificada em SuccessE2ELatency.|Geotipo, ApiName, autenticação|
|SuccessE2ELatency|Latência de E2E com Sucesso|Milissegundos|Média|A latência de ponta a ponta de solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|Geotipo, ApiName, autenticação|
|Availability|Availability|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|Geotipo, ApiName, autenticação|

## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft. ClassicStorage/storageAccounts/blobservices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|BlobCapacity|Capacidade do Blob|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço Blob da conta de armazenamento em bytes.|BlobType, camada|
|BlobCount|Contagem de Blobs|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|O número de Blobs no serviço Blob da conta de armazenamento.|BlobType, camada|
|ContainerCount|Contagem de Contêineres de Blobs|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|O número de contêineres no serviço Blob da conta de armazenamento.|Nenhum|
|IndexCapacity|Capacidade do índice|Bytes|Média|A quantidade de armazenamento usada pelo índice ADLS Gen2 (hierárquico) em bytes.|Nenhum|
|Transações|Transações|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|ResponseType, GeoType, ApiName, autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|Geotipo, ApiName, autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável.|Geotipo, ApiName, autenticação|
|SuccessServerLatency|Latência de Servidor Com Sucesso|Milissegundos|Média|A latência usada pelo armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Esse valor não inclui a latência de rede especificada em SuccessE2ELatency.|Geotipo, ApiName, autenticação|
|SuccessE2ELatency|Latência de E2E com Sucesso|Milissegundos|Média|A latência de ponta a ponta de solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|Geotipo, ApiName, autenticação|
|Availability|Availability|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|Geotipo, ApiName, autenticação|

## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft. ClassicStorage/storageAccounts/tabelaservices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|TableCapacity|Capacidade da Tabela|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço Tabela da conta de armazenamento em bytes.|Nenhum|
|TableCount|Contagem de Tabelas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|O número de tabelas no serviço Tabela da conta de armazenamento.|Nenhum|
|TableEntityCount|Contagem de Entidades de Tabela|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|O número de entidades de tabelas no serviço Tabela da conta de armazenamento.|Nenhum|
|Transações|Transações|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|ResponseType, GeoType, ApiName, autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|Geotipo, ApiName, autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável.|Geotipo, ApiName, autenticação|
|SuccessServerLatency|Latência de Servidor Com Sucesso|Milissegundos|Média|A latência usada pelo armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Esse valor não inclui a latência de rede especificada em SuccessE2ELatency.|Geotipo, ApiName, autenticação|
|SuccessE2ELatency|Latência de E2E com Sucesso|Milissegundos|Média|A latência de ponta a ponta de solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|Geotipo, ApiName, autenticação|
|Availability|Availability|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|Geotipo, ApiName, autenticação|

## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft. ClassicStorage/storageAccounts/fileservices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|FileCapacity|Capacidade do Arquivo|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço Arquivo da conta de armazenamento em bytes.|FileShare|
|FileCount|Contagem de Arquivos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|O número de arquivos no serviço Arquivo da conta de armazenamento.|FileShare|
|FileShareCount|Contagem de Compartilhamentos de Arquivos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|O número de compartilhamentos de arquivos no serviço Arquivo da conta de armazenamento.|Nenhum|
|FileShareSnapshotCount|Contagem de instantâneos de compartilhamento de arquivos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|O número de instantâneos presentes no serviço compartilhamento de arquivos da conta de armazenamento.|FileShare|
|FileShareSnapshotSize|Tamanho do instantâneo de compartilhamento de arquivos|Bytes|Média|A quantidade de armazenamento usada pelos instantâneos no serviço de arquivo da conta de armazenamento, em bytes.|FileShare|
|FileShareQuota|Tamanho da cota de compartilhamento de arquivos|Bytes|Média|O limite superior da quantidade de armazenamento que pode ser usada pelo serviço de arquivos do Azure em bytes.|FileShare|
|Transações|Transações|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|ResponseType, GeoType, ApiName, autenticação, FileShare|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|Geotipo, ApiName, autenticação, FileShare|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável.|Geotipo, ApiName, autenticação, FileShare|
|SuccessServerLatency|Latência de Servidor Com Sucesso|Milissegundos|Média|A latência usada pelo armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Esse valor não inclui a latência de rede especificada em SuccessE2ELatency.|Geotipo, ApiName, autenticação, FileShare|
|SuccessE2ELatency|Latência de E2E com Sucesso|Milissegundos|Média|A latência de ponta a ponta de solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|Geotipo, ApiName, autenticação, FileShare|
|Availability|Availability|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|Geotipo, ApiName, autenticação, FileShare|

## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft. ClassicStorage/storageAccounts/queueservices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|QueueCapacity|Capacidade da Fila|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço Fila da conta de armazenamento em bytes.|Nenhum|
|QueueCount|Contagem de Filas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|O número de filas no serviço Fila da conta de armazenamento.|Nenhum|
|QueueMessageCount|Contagem de Mensagens da Fila|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|O número aproximado de mensagens da fila no serviço Fila da conta de armazenamento.|Nenhum|
|Transações|Transações|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|ResponseType, GeoType, ApiName, autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|Geotipo, ApiName, autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável.|Geotipo, ApiName, autenticação|
|SuccessServerLatency|Latência de Servidor Com Sucesso|Milissegundos|Média|A latência usada pelo armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Esse valor não inclui a latência de rede especificada em SuccessE2ELatency.|Geotipo, ApiName, autenticação|
|SuccessE2ELatency|Latência de E2E com Sucesso|Milissegundos|Média|A latência de ponta a ponta de solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|Geotipo, ApiName, autenticação|
|Availability|Availability|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|Geotipo, ApiName, autenticação|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|TotalCalls|Total de Chamadas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número total de chamadas.|ApiName, OperationName, região|
|SuccessfulCalls|Chamadas com Êxito|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de chamadas com êxito.|ApiName, OperationName, região|
|TotalErrors|Total de Erros|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número total de chamadas com resposta de erro (código de resposta HTTP 4xx ou 5xx).|ApiName, OperationName, região|
|BlockedCalls|Chamadas Bloqueadas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de chamadas que excederam a taxa ou o limite de cota.|ApiName, OperationName, região|
|ServerErrors|Erros do Servidor|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de chamadas com erro interno do serviço (código de resposta HTTP 5xx).|ApiName, OperationName, região|
|ClientErrors|Erros do Cliente|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de chamadas com erro do lado do cliente (código de resposta HTTP 4xx).|ApiName, OperationName, região|
|DataIn|Entrada de Dados|Bytes|Total|Tamanho dos dados de entrada em bytes.|ApiName, OperationName, região|
|DataOut|Saída de dados|Bytes|Total|Tamanho dos dados de saída em bytes.|ApiName, OperationName, região|
|Latência|Latência|MilliSeconds|Média|Latência em milissegundos.|ApiName, OperationName, região|
|CharactersTranslated|Caracteres traduzidos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número total de caracteres na solicitação de texto de entrada.|ApiName, OperationName, região|
|CharactersTrained|Caracteres treinados|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número total de caracteres treinados.|ApiName, OperationName, região|
|SpeechSessionDuration|Duração da Sessão de Fala|Segundos|Total|Duração total da sessão de fala em segundos.|ApiName, OperationName, região|
|TotalTransactions|Total de Transações|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número total de transações.|Nenhum|
|TotalTokenCalls|Total de chamadas de token|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número total de chamadas de token.|ApiName, OperationName, região|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|Porcentagem de CPU|Porcentagem de CPU|Porcentagem|Média|A porcentagem das unidades de computação alocadas que estão atualmente em uso pela máquina virtual ou pelas máquinas virtuais|Nenhum|
|Entrada na rede|Rede em faturável (preterida)|Bytes|Total|O número de bytes faturáveis recebidos em todas as interfaces de rede pelas máquinas virtuais (tráfego de entrada) (preterido)|Nenhum|
|Saída da rede|Saída de rede faturável (preterida)|Bytes|Total|O número de bytes faturáveis de saída em todas as interfaces de rede pelas máquinas virtuais (tráfego de saída) (preterido)|Nenhum|
|Bytes de leitura de disco|Bytes de leitura de disco|Bytes|Total|Bytes lidos do disco durante o período de monitoramento|Nenhum|
|Bytes de gravação de disco|Bytes de gravação de disco|Bytes|Total|Bytes gravados no disco durante o período de monitoramento|Nenhum|
|Operações de leitura de disco/Seg|Operações de leitura de disco/Seg|CountPerSecond|Média|IOPS de leitura de disco|Nenhum|
|Operações de gravação de disco/Seg|Operações de gravação de disco/Seg|CountPerSecond|Média|IOPS de gravação de disco|Nenhum|
|Créditos de CPU Restantes|Créditos de CPU Restantes|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número total de créditos disponíveis para intermitência|Nenhum|
|Créditos de CPU Consumidos|Créditos de CPU Consumidos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número total de créditos consumido pela Máquina Virtual|Nenhum|
|Por Bytes de Leitura do Disco/s|Bytes de leitura do disco de dados/s (preterido)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento|SlotId|
|Por Bytes de Gravação de Disco/s|Bytes de gravação do disco de dados/s (preterido)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento|SlotId|
|Por operações de Leitura de Disco/s|Operações de leitura de disco de dados/s (preterido)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento|SlotId|
|Por Operações de Gravação de Disco/s|Operações de gravação do disco de dados/s (preterido)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento|SlotId|
|Por QD de Disco|Disco de dados QD (preterido)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Profundidade da Fila do Disco de Dados (ou Tamanho da Fila)|SlotId|
|SO Por Bytes de Leitura do Disco/s|Bytes de leitura do disco do so/s (preterido)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhum|
|SO Por Bytes de Gravação do Disco/s|Bytes de gravação de disco do sistema operacional/s (preterido)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento do disco do sistema operacional|Nenhum|
|SO Por Operações de Leitura do Disco/s|Operações de leitura de disco do so/s (preterido)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhum|
|SO Por Operações de Gravação do Disco/s|Operações de gravação de disco do sistema operacional/s (preterido)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhum|
|Os Por QD de Disco|Disco do so QD (preterido)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Profundidade da Fila do Disco do SO (ou Tamanho da Fila)|Nenhum|
|Bytes de leitura do disco de dados/s|Bytes de Leitura do Disco de Dados/s (Visualização)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento|LUN|
|Bytes de gravação do disco de dados/s|Bytes de Gravação do Disco de Dados/s (Visualização)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento|LUN|
|Operações de leitura de disco de dados/s|Operações de Leitura do Disco de Dados/s (Visualização)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento|LUN|
|Operações de gravação do disco de dados/s|Operações de Gravação do Disco de Dados/s (Visualização)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento|LUN|
|Profundidade da fila do disco de dados|Profundidade da fila do disco de dados (visualização)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Profundidade da Fila do Disco de Dados (ou Tamanho da Fila)|LUN|
|Bytes de leitura do disco do so/s|Bytes de Leitura do Disco do SO/s (Visualização)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhum|
|Bytes de gravação de disco do sistema operacional/s|Bytes de Gravação do Disco do SO/s (Visualização)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento do disco do sistema operacional|Nenhum|
|Operações de leitura de disco do so/s|Operações de Leitura do Disco do SO/s (Visualização)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhum|
|Operações de gravação de disco do so/s|Operações de Gravação do Disco do SO/s (Visualização)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhum|
|Profundidade da fila de disco do so|Profundidade da fila de disco do so (visualização)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Profundidade da Fila do Disco do SO (ou Tamanho da Fila)|Nenhum|
|Fluxos de entrada|Fluxos de entrada|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Os fluxos de entrada são o número de fluxos atuais na direção de entrada (tráfego entrando na VM)|Nenhum|
|Fluxos de saída|Fluxos de saída|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Fluxos de saída são o número de fluxos atuais na direção de saída (tráfego saindo da VM)|Nenhum|
|Taxa máxima de criação de fluxos de entrada|Taxa máxima de criação de fluxos de entrada (visualização)|CountPerSecond|Média|A taxa de criação máxima de fluxos de entrada (tráfego entrando na VM)|Nenhum|
|Taxa máxima de criação de fluxos de saída|Taxa máxima de criação de fluxos de saída (versão prévia)|CountPerSecond|Média|A taxa de criação máxima de fluxos de saída (tráfego saindo da VM)|Nenhum|
|Cache do disco de dados Premium-acesso de leitura|Leitura do cache do disco de dados Premium (versão prévia)|Porcentagem|Média|Cache do disco de dados Premium-acesso de leitura|LUN|
|Erro de leitura do cache do disco de dados Premium|Cache do disco de dados Premium-erro de leitura (visualização)|Porcentagem|Média|Erro de leitura do cache do disco de dados Premium|LUN|
|Cache de disco do sistema operacional Premium-acesso de leitura|Cache de disco do sistema operacional Premium-acesso de leitura (versão prévia)|Porcentagem|Média|Cache de disco do sistema operacional Premium-acesso de leitura|Nenhum|
|Erro de leitura do cache de disco do so premium|Erro de leitura do cache de disco do so premium (visualização)|Porcentagem|Média|Erro de leitura do cache de disco do so premium|Nenhum|
|Total de rede|Total de rede|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela máquina virtual ou pelas máquinas virtuais (tráfego de entrada)|Nenhum|
|Total de saída de rede|Total de saída de rede|Bytes|Total|O número de bytes de saída em todas as interfaces de rede pela máquina virtual ou pelas máquinas virtuais (tráfego de saída)|Nenhum|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|Porcentagem de CPU|Porcentagem de CPU|Porcentagem|Média|A porcentagem das unidades de computação alocadas que estão atualmente em uso pela máquina virtual ou pelas máquinas virtuais|VMName|
|Entrada na rede|Rede em faturável (preterida)|Bytes|Total|O número de bytes faturáveis recebidos em todas as interfaces de rede pelas máquinas virtuais (tráfego de entrada) (preterido)|VMName|
|Saída da rede|Saída de rede faturável (preterida)|Bytes|Total|O número de bytes faturáveis de saída em todas as interfaces de rede pelas máquinas virtuais (tráfego de saída) (preterido)|VMName|
|Bytes de leitura de disco|Bytes de leitura de disco|Bytes|Total|Bytes lidos do disco durante o período de monitoramento|VMName|
|Bytes de gravação de disco|Bytes de gravação de disco|Bytes|Total|Bytes gravados no disco durante o período de monitoramento|VMName|
|Operações de leitura de disco/Seg|Operações de leitura de disco/Seg|CountPerSecond|Média|IOPS de leitura de disco|VMName|
|Operações de gravação de disco/Seg|Operações de gravação de disco/Seg|CountPerSecond|Média|IOPS de gravação de disco|VMName|
|Créditos de CPU Restantes|Créditos de CPU Restantes|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número total de créditos disponíveis para intermitência|Nenhum|
|Créditos de CPU Consumidos|Créditos de CPU Consumidos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número total de créditos consumido pela Máquina Virtual|Nenhum|
|Por Bytes de Leitura do Disco/s|Bytes de leitura do disco de dados/s (preterido)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento|SlotId|
|Por Bytes de Gravação de Disco/s|Bytes de gravação do disco de dados/s (preterido)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento|SlotId|
|Por operações de Leitura de Disco/s|Operações de leitura de disco de dados/s (preterido)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento|SlotId|
|Por Operações de Gravação de Disco/s|Operações de gravação do disco de dados/s (preterido)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento|SlotId|
|Por QD de Disco|Disco de dados QD (preterido)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Profundidade da Fila do Disco de Dados (ou Tamanho da Fila)|SlotId|
|SO Por Bytes de Leitura do Disco/s|Bytes de leitura do disco do so/s (preterido)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhum|
|SO Por Bytes de Gravação do Disco/s|Bytes de gravação de disco do sistema operacional/s (preterido)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento do disco do sistema operacional|Nenhum|
|SO Por Operações de Leitura do Disco/s|Operações de leitura de disco do so/s (preterido)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhum|
|SO Por Operações de Gravação do Disco/s|Operações de gravação de disco do sistema operacional/s (preterido)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhum|
|Os Por QD de Disco|Disco do so QD (preterido)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Profundidade da Fila do Disco do SO (ou Tamanho da Fila)|Nenhum|
|Bytes de leitura do disco de dados/s|Bytes de Leitura do Disco de Dados/s (Visualização)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento|LUN, VMName|
|Bytes de gravação do disco de dados/s|Bytes de Gravação do Disco de Dados/s (Visualização)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento|LUN, VMName|
|Operações de leitura de disco de dados/s|Operações de Leitura do Disco de Dados/s (Visualização)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento|LUN, VMName|
|Operações de gravação do disco de dados/s|Operações de Gravação do Disco de Dados/s (Visualização)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento|LUN, VMName|
|Profundidade da fila do disco de dados|Profundidade da fila do disco de dados (visualização)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Profundidade da Fila do Disco de Dados (ou Tamanho da Fila)|LUN, VMName|
|Bytes de leitura do disco do so/s|Bytes de Leitura do Disco do SO/s (Visualização)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento para o disco do sistema operacional|VMName|
|Bytes de gravação de disco do sistema operacional/s|Bytes de Gravação do Disco do SO/s (Visualização)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento do disco do sistema operacional|VMName|
|Operações de leitura de disco do so/s|Operações de Leitura do Disco do SO/s (Visualização)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|VMName|
|Operações de gravação de disco do so/s|Operações de Gravação do Disco do SO/s (Visualização)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|VMName|
|Profundidade da fila de disco do so|Profundidade da fila de disco do so (visualização)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Profundidade da Fila do Disco do SO (ou Tamanho da Fila)|VMName|
|Fluxos de entrada|Fluxos de entrada|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Os fluxos de entrada são o número de fluxos atuais na direção de entrada (tráfego entrando na VM)|VMName|
|Fluxos de saída|Fluxos de saída|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Fluxos de saída são o número de fluxos atuais na direção de saída (tráfego saindo da VM)|VMName|
|Taxa máxima de criação de fluxos de entrada|Taxa máxima de criação de fluxos de entrada (visualização)|CountPerSecond|Média|A taxa de criação máxima de fluxos de entrada (tráfego entrando na VM)|VMName|
|Taxa máxima de criação de fluxos de saída|Taxa máxima de criação de fluxos de saída (versão prévia)|CountPerSecond|Média|A taxa de criação máxima de fluxos de saída (tráfego saindo da VM)|VMName|
|Cache do disco de dados Premium-acesso de leitura|Leitura do cache do disco de dados Premium (versão prévia)|Porcentagem|Média|Cache do disco de dados Premium-acesso de leitura|LUN, VMName|
|Erro de leitura do cache do disco de dados Premium|Cache do disco de dados Premium-erro de leitura (visualização)|Porcentagem|Média|Erro de leitura do cache do disco de dados Premium|LUN, VMName|
|Cache de disco do sistema operacional Premium-acesso de leitura|Cache de disco do sistema operacional Premium-acesso de leitura (versão prévia)|Porcentagem|Média|Cache de disco do sistema operacional Premium-acesso de leitura|VMName|
|Erro de leitura do cache de disco do so premium|Erro de leitura do cache de disco do so premium (visualização)|Porcentagem|Média|Erro de leitura do cache de disco do so premium|VMName|
|Total de rede|Total de rede|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela máquina virtual ou pelas máquinas virtuais (tráfego de entrada)|VMName|
|Total de saída de rede|Total de saída de rede|Bytes|Total|O número de bytes de saída em todas as interfaces de rede pela máquina virtual ou pelas máquinas virtuais (tráfego de saída)|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|Porcentagem de CPU|Porcentagem de CPU|Porcentagem|Média|A porcentagem das unidades de computação alocadas que estão atualmente em uso pela máquina virtual ou pelas máquinas virtuais|Nenhum|
|Entrada na rede|Rede em faturável (preterida)|Bytes|Total|O número de bytes faturáveis recebidos em todas as interfaces de rede pelas máquinas virtuais (tráfego de entrada) (preterido)|Nenhum|
|Saída da rede|Saída de rede faturável (preterida)|Bytes|Total|O número de bytes faturáveis de saída em todas as interfaces de rede pelas máquinas virtuais (tráfego de saída) (preterido)|Nenhum|
|Bytes de leitura de disco|Bytes de leitura de disco|Bytes|Total|Bytes lidos do disco durante o período de monitoramento|Nenhum|
|Bytes de gravação de disco|Bytes de gravação de disco|Bytes|Total|Bytes gravados no disco durante o período de monitoramento|Nenhum|
|Operações de leitura de disco/Seg|Operações de leitura de disco/Seg|CountPerSecond|Média|IOPS de leitura de disco|Nenhum|
|Operações de gravação de disco/Seg|Operações de gravação de disco/Seg|CountPerSecond|Média|IOPS de gravação de disco|Nenhum|
|Créditos de CPU Restantes|Créditos de CPU Restantes|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número total de créditos disponíveis para intermitência|Nenhum|
|Créditos de CPU Consumidos|Créditos de CPU Consumidos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número total de créditos consumido pela Máquina Virtual|Nenhum|
|Por Bytes de Leitura do Disco/s|Bytes de leitura do disco de dados/s (preterido)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento|SlotId|
|Por Bytes de Gravação de Disco/s|Bytes de gravação do disco de dados/s (preterido)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento|SlotId|
|Por operações de Leitura de Disco/s|Operações de leitura de disco de dados/s (preterido)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento|SlotId|
|Por Operações de Gravação de Disco/s|Operações de gravação do disco de dados/s (preterido)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento|SlotId|
|Por QD de Disco|Disco de dados QD (preterido)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Profundidade da Fila do Disco de Dados (ou Tamanho da Fila)|SlotId|
|SO Por Bytes de Leitura do Disco/s|Bytes de leitura do disco do so/s (preterido)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhum|
|SO Por Bytes de Gravação do Disco/s|Bytes de gravação de disco do sistema operacional/s (preterido)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento do disco do sistema operacional|Nenhum|
|SO Por Operações de Leitura do Disco/s|Operações de leitura de disco do so/s (preterido)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhum|
|SO Por Operações de Gravação do Disco/s|Operações de gravação de disco do sistema operacional/s (preterido)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhum|
|Os Por QD de Disco|Disco do so QD (preterido)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Profundidade da Fila do Disco do SO (ou Tamanho da Fila)|Nenhum|
|Bytes de leitura do disco de dados/s|Bytes de Leitura do Disco de Dados/s (Visualização)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento|LUN|
|Bytes de gravação do disco de dados/s|Bytes de Gravação do Disco de Dados/s (Visualização)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento|LUN|
|Operações de leitura de disco de dados/s|Operações de Leitura do Disco de Dados/s (Visualização)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento|LUN|
|Operações de gravação do disco de dados/s|Operações de Gravação do Disco de Dados/s (Visualização)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento|LUN|
|Profundidade da fila do disco de dados|Profundidade da fila do disco de dados (visualização)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Profundidade da Fila do Disco de Dados (ou Tamanho da Fila)|LUN|
|Bytes de leitura do disco do so/s|Bytes de Leitura do Disco do SO/s (Visualização)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhum|
|Bytes de gravação de disco do sistema operacional/s|Bytes de Gravação do Disco do SO/s (Visualização)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento do disco do sistema operacional|Nenhum|
|Operações de leitura de disco do so/s|Operações de Leitura do Disco do SO/s (Visualização)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhum|
|Operações de gravação de disco do so/s|Operações de Gravação do Disco do SO/s (Visualização)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhum|
|Profundidade da fila de disco do so|Profundidade da fila de disco do so (visualização)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Profundidade da Fila do Disco do SO (ou Tamanho da Fila)|Nenhum|
|Fluxos de entrada|Fluxos de entrada|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Os fluxos de entrada são o número de fluxos atuais na direção de entrada (tráfego entrando na VM)|Nenhum|
|Fluxos de saída|Fluxos de saída|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Fluxos de saída são o número de fluxos atuais na direção de saída (tráfego saindo da VM)|Nenhum|
|Taxa máxima de criação de fluxos de entrada|Taxa máxima de criação de fluxos de entrada (visualização)|CountPerSecond|Média|A taxa de criação máxima de fluxos de entrada (tráfego entrando na VM)|Nenhum|
|Taxa máxima de criação de fluxos de saída|Taxa máxima de criação de fluxos de saída (versão prévia)|CountPerSecond|Média|A taxa de criação máxima de fluxos de saída (tráfego saindo da VM)|Nenhum|
|Cache do disco de dados Premium-acesso de leitura|Leitura do cache do disco de dados Premium (versão prévia)|Porcentagem|Média|Cache do disco de dados Premium-acesso de leitura|LUN|
|Erro de leitura do cache do disco de dados Premium|Cache do disco de dados Premium-erro de leitura (visualização)|Porcentagem|Média|Erro de leitura do cache do disco de dados Premium|LUN|
|Cache de disco do sistema operacional Premium-acesso de leitura|Cache de disco do sistema operacional Premium-acesso de leitura (versão prévia)|Porcentagem|Média|Cache de disco do sistema operacional Premium-acesso de leitura|Nenhum|
|Erro de leitura do cache de disco do so premium|Erro de leitura do cache de disco do so premium (visualização)|Porcentagem|Média|Erro de leitura do cache de disco do so premium|Nenhum|
|Total de rede|Total de rede|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela máquina virtual ou pelas máquinas virtuais (tráfego de entrada)|Nenhum|
|Total de saída de rede|Total de saída de rede|Bytes|Total|O número de bytes de saída em todas as interfaces de rede pela máquina virtual ou pelas máquinas virtuais (tráfego de saída)|Nenhum|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|CpuUsage|Uso da CPU|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Uso de CPU em todos os núcleos em milicores.|containerName|
|MemoryUsage|Utilização de Memória|Bytes|Média|Uso de memória total em bytes.|containerName|
|NetworkBytesReceivedPerSecond|Bytes de Rede Recebidos por Segundo|Bytes|Média|Os bytes de rede recebidos por segundo.|Nenhum|
|NetworkBytesTransmittedPerSecond|Bytes de Rede Transmitidos por Segundo|Bytes|Média|Os bytes de rede transmitidos por segundo.|Nenhum|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|TotalPullCount|Contagem total de pull|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de pulls de imagem no total|Nenhum|
|SuccessfulPullCount|Contagem de pull bem-sucedida|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de pulls de imagem bem-sucedidos|Nenhum|
|TotalPushCount|Contagem total de push|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de envios por push de imagem no total|Nenhum|
|SuccessfulPushCount|Contagem de push bem-sucedida|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de Pushes de imagem bem-sucedidos|Nenhum|
|RunDuration|Duração da execução|Milissegundos|Total|Duração da execução em milissegundos|Nenhum|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Número total de núcleos de cpu disponíveis em um cluster gerenciado|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número total de núcleos de cpu disponíveis em um cluster gerenciado|Nenhum|
|kube_node_status_allocatable_memory_bytes|Número total de memória disponível em um cluster gerenciado|Bytes|Média|Número total de memória disponível em um cluster gerenciado|Nenhum|
|kube_pod_status_ready|Número de compartimentos no estado Pronto|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de compartimentos no estado Pronto|namespace, Pod|
|kube_node_status_condition|Status para as várias condições de nó|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Status para as várias condições de nó|condição, status, status2, nó|
|kube_pod_status_phase|Número de pods por fase|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de pods por fase|fase, namespace, Pod|



## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|SuccessfullRequests|Solicitações bem sucedidas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Solicitações bem-sucedidas feitas pelo provedor personalizado|HttpMethod, CallPath, StatusCode|
|FailedRequests|Solicitações com falha|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Obtém os logs disponíveis para provedores de recursos personalizados|HttpMethod, CallPath, StatusCode|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|NICReadThroughput|Taxa de transferência de leitura (rede)|BytesPerSecond|Média|A taxa de transferência de leitura da interface de rede no dispositivo no período de relatório para todos os volumes no gateway.|InstanceName|
|NICWriteThroughput|Taxa de transferência de gravação (rede)|BytesPerSecond|Média|A taxa de transferência de gravação da interface de rede no dispositivo no período de relatório para todos os volumes no gateway.|InstanceName|
|CloudReadThroughputPerShare|Taxa de transferência de download de nuvem (compartilhamento)|BytesPerSecond|Média|A taxa de transferência de download para o Azure de um compartilhamento durante o período de relatório.|Compartilhar|
|CloudUploadThroughputPerShare|Taxa de transferência de upload de nuvem (compartilhamento)|BytesPerSecond|Média|A taxa de transferência de upload para o Azure de um compartilhamento durante o período de relatório.|Compartilhar|
|BytesUploadedToCloudPerShare|Bytes de nuvem carregados (compartilhamento)|Bytes|Média|O número total de bytes que são carregados no Azure de um compartilhamento durante o período de relatório.|Compartilhar|
|TotalCapacity|Capacidade total|Bytes|Média|Capacidade total|Nenhum|
|AvailableCapacity|Capacidade Disponível|Bytes|Média|A capacidade disponível em bytes durante o período de relatório.|Nenhum|
|CloudUploadThroughput|Taxa de transferência de upload de nuvem|BytesPerSecond|Média|A taxa de transferência de upload de nuvem para o Azure durante o período de relatório.|Nenhum|
|CloudReadThroughput|Taxa de transferência de download da nuvem|BytesPerSecond|Média|A taxa de transferência de download de nuvem para o Azure durante o período de relatório.|Nenhum|
|BytesUploadedToCloud|Bytes de nuvem carregados (dispositivo)|Bytes|Média|O número total de bytes que são carregados no Azure de um dispositivo durante o período de relatório.|Nenhum|
|HyperVVirtualProcessorUtilization|Computação de borda – percentual de CPU|Porcentagem|Média|Porcentagem de uso da CPU|InstanceName|
|HyperVMemoryUtilization|Computação de borda-uso de memória|Porcentagem|Média|Quantidade de RAM em uso|InstanceName|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|FailedRuns|Execuções com falha|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||pipelinename, ActivityName|
|SuccessfulRuns|Execuções com êxito|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||pipelinename, ActivityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|PipelineFailedRuns|Métricas de execução do pipeline com falha|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||FailureType, nome|
|PipelineSucceededRuns|Métricas de execução do pipeline bem-sucedido|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||FailureType, nome|
|PipelineCancelledRuns|Métricas de execuções de pipeline canceladas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||FailureType, nome|
|ActivityFailedRuns|Métricas de execução de atividades com falha|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||ActivityType, Pipelinename, Falhatype, nome|
|ActivitySucceededRuns|Métricas de execução de atividades bem-sucedidas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||ActivityType, Pipelinename, Falhatype, nome|
|ActivityCancelledRuns|Métricas de execuções de atividade canceladas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||ActivityType, Pipelinename, Falhatype, nome|
|TriggerFailedRuns|Métricas de execuções do gatilho com falha|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nome, Falhatype|
|TriggerSucceededRuns|Métricas de execuções do gatilho bem-sucedidas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nome, Falhatype|
|TriggerCancelledRuns|O gatilho cancelado executa métricas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total||Nome, Falhatype|
|IntegrationRuntimeCpuPercentage|Utilização de CPU de runtime de integração|Porcentagem|Média||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAvailableMemory|Memória disponível de runtime de integração|Bytes|Média||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAverageTaskPickupDelay|Duração da fila do Integration Runtime|Segundos|Média||IntegrationRuntimeName|
|IntegrationRuntimeQueueLength|Comprimento da fila do Integration Runtime|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média||IntegrationRuntimeName|
|IntegrationRuntimeAvailableNodeNumber|Contagem de nós disponíveis do Integration Runtime|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média||IntegrationRuntimeName|
|MaxAllowedResourceCount|Contagem máxima de entidades permitidas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo||Nenhum|
|MaxAllowedFactorySizeInGbUnits|Tamanho máximo de fábrica permitido (unidade GB)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo||Nenhum|
|ResourceCount|Contagem total de entidades|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo||Nenhum|
|FactorySizeInGbUnits|Tamanho total de fábrica (unidade GB)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo||Nenhum|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|JobEndedSuccess|Trabalhos com êxito|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Contagem de trabalhos com êxito.|Nenhum|
|JobEndedFailure|Trabalhos com falha|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Contagem de trabalhos com falha.|Nenhum|
|JobEndedCancelled|Trabalhos cancelados|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Contagem de trabalhos cancelados.|Nenhum|
|JobAUEndedSuccess|Tempo de AU com êxito|Segundos|Total|Tempo total de AU dos trabalhos com êxito.|Nenhum|
|JobAUEndedFailure|Tempo de AU com falha|Segundos|Total|Tempo total de AU dos trabalhos com falha.|Nenhum|
|JobAUEndedCancelled|Tempo de AU cancelada|Segundos|Total|Tempo total de AU dos trabalhos cancelados.|Nenhum|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|TotalStorage|Armazenamento total|Bytes|Máximo|Quantidade total de dados armazenados na conta.|Nenhum|
|DataWritten|Dados gravados|Bytes|Total|Quantidade total de dados gravados na conta.|Nenhum|
|DataRead|Leitura de dados|Bytes|Total|Quantidade total de dados lidos na conta.|Nenhum|
|WriteRequests|Solicitações de gravação|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Contagem de solicitações de gravação de dados para a conta.|Nenhum|
|ReadRequests|Solicitações de leitura|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Contagem solicitações de leitura de dados para a conta.|Nenhum|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|cpu_percent|Porcentagem de CPU|Porcentagem|Média|Porcentagem de CPU|Nenhum|
|memory_percent|Porcentagem de memória|Porcentagem|Média|Porcentagem de memória|Nenhum|
|io_consumption_percent|Porcentagem de E/S|Porcentagem|Média|Porcentagem de E/S|Nenhum|
|storage_percent|Porcentagem de armazenamento|Porcentagem|Média|Porcentagem de armazenamento|Nenhum|
|storage_used|Armazenamento usado|Bytes|Média|Armazenamento usado|Nenhum|
|storage_limit|Limite de armazenamento|Bytes|Máximo|Limite de armazenamento|Nenhum|
|serverlog_storage_percent|Porcentagem de armazenamento do Log do Servidor|Porcentagem|Média|Porcentagem de armazenamento do Log do Servidor|Nenhum|
|serverlog_storage_usage|Armazenamento do Log do Servidor usado|Bytes|Média|Armazenamento do Log do Servidor usado|Nenhum|
|serverlog_storage_limit|Limite de armazenamento do Log do Servidor|Bytes|Média|Limite de armazenamento do Log do Servidor|Nenhum|
|active_connections|Conexões ativas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Conexões ativas|Nenhum|
|connections_failed|Conexões com falha|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Conexões com falha|Nenhum|
|seconds_behind_master|Retardo de replicação em segundos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo|Retardo de replicação em segundos|Nenhum|
|backup_storage_used|Armazenamento de backup usado|Bytes|Média|Armazenamento de backup usado|Nenhum|
|network_bytes_egress|Saída da rede|Bytes|Total|Saída de Rede em conexões ativas|Nenhum|
|network_bytes_ingress|Entrada na rede|Bytes|Total|Entrada de Rede em conexões ativas|Nenhum|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|cpu_percent|Porcentagem de CPU|Porcentagem|Média|Porcentagem de CPU|Nenhum|
|memory_percent|Porcentagem de memória|Porcentagem|Média|Porcentagem de memória|Nenhum|
|io_consumption_percent|Porcentagem de E/S|Porcentagem|Média|Porcentagem de E/S|Nenhum|
|storage_percent|Porcentagem de armazenamento|Porcentagem|Média|Porcentagem de armazenamento|Nenhum|
|storage_used|Armazenamento usado|Bytes|Média|Armazenamento usado|Nenhum|
|storage_limit|Limite de armazenamento|Bytes|Máximo|Limite de armazenamento|Nenhum|
|serverlog_storage_percent|Porcentagem de armazenamento do Log do Servidor|Porcentagem|Média|Porcentagem de armazenamento do Log do Servidor|Nenhum|
|serverlog_storage_usage|Armazenamento do Log do Servidor usado|Bytes|Média|Armazenamento do Log do Servidor usado|Nenhum|
|serverlog_storage_limit|Limite de armazenamento do Log do Servidor|Bytes|Máximo|Limite de armazenamento do Log do Servidor|Nenhum|
|active_connections|Conexões ativas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Conexões ativas|Nenhum|
|connections_failed|Conexões com falha|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Conexões com falha|Nenhum|
|seconds_behind_master|Retardo de replicação em segundos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo|Retardo de replicação em segundos|Nenhum|
|backup_storage_used|Armazenamento de backup usado|Bytes|Média|Armazenamento de backup usado|Nenhum|
|network_bytes_egress|Saída da rede|Bytes|Total|Saída de Rede em conexões ativas|Nenhum|
|network_bytes_ingress|Entrada na rede|Bytes|Total|Entrada de Rede em conexões ativas|Nenhum|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|cpu_percent|Porcentagem de CPU|Porcentagem|Média|Porcentagem de CPU|Nenhum|
|memory_percent|Porcentagem de memória|Porcentagem|Média|Porcentagem de memória|Nenhum|
|io_consumption_percent|Porcentagem de E/S|Porcentagem|Média|Porcentagem de E/S|Nenhum|
|storage_percent|Porcentagem de armazenamento|Porcentagem|Média|Porcentagem de armazenamento|Nenhum|
|storage_used|Armazenamento usado|Bytes|Média|Armazenamento usado|Nenhum|
|storage_limit|Limite de armazenamento|Bytes|Máximo|Limite de armazenamento|Nenhum|
|serverlog_storage_percent|Porcentagem de armazenamento do Log do Servidor|Porcentagem|Média|Porcentagem de armazenamento do Log do Servidor|Nenhum|
|serverlog_storage_usage|Armazenamento do Log do Servidor usado|Bytes|Média|Armazenamento do Log do Servidor usado|Nenhum|
|serverlog_storage_limit|Limite de armazenamento do Log do Servidor|Bytes|Máximo|Limite de armazenamento do Log do Servidor|Nenhum|
|active_connections|Conexões ativas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Conexões ativas|Nenhum|
|connections_failed|Conexões com falha|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Conexões com falha|Nenhum|
|backup_storage_used|Armazenamento de backup usado|Bytes|Média|Armazenamento de backup usado|Nenhum|
|network_bytes_egress|Saída da rede|Bytes|Total|Saída de Rede em conexões ativas|Nenhum|
|network_bytes_ingress|Entrada na rede|Bytes|Total|Entrada de Rede em conexões ativas|Nenhum|
|pg_replica_log_delay_in_seconds|Retardo da Réplica|Segundos|Máximo|Atraso de réplica em segundos|Nenhum|
|pg_replica_log_delay_in_bytes|Retardo Máximo entre Réplicas|Bytes|Máximo|Atraso em bytes da réplica mais defasante|Nenhum|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|cpu_percent|Porcentagem de CPU|Porcentagem|Média|Porcentagem de CPU|Nenhum|
|memory_percent|Porcentagem de memória|Porcentagem|Média|Porcentagem de memória|Nenhum|
|IOPS|IOPS|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Operações de e/s por segundo|Nenhum|
|storage_percent|Porcentagem de armazenamento|Porcentagem|Média|Porcentagem de armazenamento|Nenhum|
|storage_used|Armazenamento usado|Bytes|Média|Armazenamento usado|Nenhum|
|active_connections|Conexões ativas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Conexões ativas|Nenhum|
|network_bytes_egress|Saída da rede|Bytes|Total|Saída de Rede em conexões ativas|Nenhum|
|network_bytes_ingress|Entrada na rede|Bytes|Total|Entrada de Rede em conexões ativas|Nenhum|





## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Tentativas de envio de mensagem de telemetria|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de tentativas de envio de mensagens de telemetria do dispositivo para nuvem para o hub IoT|Nenhum|
|d2c.telemetry.ingress.success|Mensagens de telemetria enviadas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de mensagens de telemetria do dispositivo para nuvem enviadas com êxito para o hub IoT|Nenhum|
|c2d.commands.egress.complete.success|Entregas de mensagem C2D concluídas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de entregas de mensagem da nuvem para o dispositivo concluídas com êxito pelo dispositivo|Nenhum|
|c2d.commands.egress.abandon.success|C2D mensagens abandonadas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de mensagens da nuvem para o dispositivo abandonadas pelo dispositivo|Nenhum|
|c2d.commands.egress.reject.success|Mensagens C2D rejeitadas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de mensagens da nuvem para o dispositivo rejeitadas pelo dispositivo|Nenhum|
|C2DMessagesExpired|C2D mensagens expiradas (visualização)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de mensagens de nuvem para o dispositivo expiradas|Nenhum|
|devices.totalDevices|Total de dispositivos (preterido)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de dispositivos registrados para o seu hub IoT|Nenhum|
|devices.connectedDevices.allProtocol|Dispositivos conectados (preteridos) |{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de dispositivos registrados ao seu hub IoT|Nenhum|
|d2c.telemetry.egress.success|Roteamento: mensagens de telemetria entregues|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|O número de vezes que as mensagens foram entregues com êxito a todos os pontos de extremidade usando o roteamento do Hub IoT. Se uma mensagem é encaminhada para vários pontos de extremidade, esse valor aumenta em um para cada entrega bem-sucedida. Se uma mensagem é entregue ao mesmo ponto de extremidade várias vezes, esse valor aumenta em um para cada entrega bem-sucedida.|Nenhum|
|d2c.telemetry.egress.dropped|Roteamento: mensagens de telemetria removidas |{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|O número de vezes que as mensagens foram removidas pelo roteamento do Hub IoT devido à inatividade dos pontos de extremidade. Esse valor não conta as mensagens entregues à rota de fallback, pois as mensagens removidas não são entregues.|Nenhum|
|d2c.telemetry.egress.orphaned|Roteamento: mensagens de telemetria órfãs |{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|O número de vezes que as mensagens ficaram órfãos do roteamento do Hub IoT porque não correspondiam a nenhuma regra de roteamento (incluindo a regra de fallback). |Nenhum|
|d2c.telemetry.egress.invalid|Roteamento: mensagens de telemetria incompatíveis|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|O número de vezes que o roteamento do Hub IoT não conseguiu entregar mensagens devido a uma incompatibilidade com o ponto de extremidade. Esse valor não inclui novas tentativas.|Nenhum|
|d2c.telemetry.egress.fallback|Roteamento: mensagens entregues ao fallback|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|O número de vezes que o roteamento do Hub IoT entregou mensagens ao ponto de extremidade associado à rota de fallback.|Nenhum|
|d2c.endpoints.egress.eventHubs|Roteamento: mensagens entregues ao Hub de Eventos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|O número de vezes que o roteamento do Hub IoT entregou com êxito mensagens aos pontos de extremidade do Hub de Eventos.|Nenhum|
|d2c.endpoints.latency.eventHubs|Roteamento: latência de mensagem para o Hub de Eventos|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem no Hub IoT e a entrada da mensagem em um ponto de extremidade do Hub de Eventos.|Nenhum|
|d2c.endpoints.egress.serviceBusQueues|Roteamento: mensagens entregues à Fila do Barramento de Serviço|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|O número de vezes que o roteamento do Hub IoT entregou com êxito mensagens aos pontos de extremidade da fila do Barramento de Serviço.|Nenhum|
|d2c.endpoints.latency.serviceBusQueues|Roteamento: latência de mensagem para a Fila do Barramento de Serviço|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem no Hub IoT e a entrada da mensagem de telemetria em um ponto de extremidade da fila do Barramento de Serviço.|Nenhum|
|d2c.endpoints.egress.serviceBusTopics|Roteamento: mensagens entregues ao Tópico do Barramento de Serviço|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|O número de vezes que o roteamento do Hub IoT entregou com êxito mensagens aos pontos de extremidade do tópico do Barramento de Serviço.|Nenhum|
|d2c.endpoints.latency.serviceBusTopics|Roteamento: latência de mensagem para o Tópico do Barramento de Serviço|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem no Hub IoT e a entrada da mensagem de telemetria em um ponto de extremidade do tópico do Barramento de Serviço.|Nenhum|
|d2c.endpoints.egress.builtIn.events|Roteamento: mensagens entregues a mensagens/eventos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|O número de vezes que o roteamento do Hub IoT entregou com êxito mensagens ao ponto de extremidade interno (mensagens/eventos).|Nenhum|
|d2c.Endpoints.Latency.builtIn.Events|Roteamento: latência de mensagem para mensagens/eventos|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem no Hub IoT e a entrada da mensagem de telemetria no ponto de extremidade interno (mensagens/eventos).|Nenhum|
|d2c.endpoints.egress.storage|Roteamento: mensagens entregues ao armazenamento|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|O número de vezes que o roteamento do Hub IoT entregou com êxito mensagens aos pontos de extremidade de armazenamento.|Nenhum|
|d2c.endpoints.latency.storage|Roteamento: latência de mensagem para armazenamento|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem no Hub IoT e a entrada da mensagem de telemetria em um ponto de extremidade de armazenamento.|Nenhum|
|d2c.endpoints.egress.storage.bytes|Roteamento: dados entregues ao armazenamento|Bytes|Total|A quantidade de dados (bytes) de roteamento do Hub IoT entregue aos pontos de extremidade de armazenamento.|Nenhum|
|d2c.endpoints.egress.storage.blobs|Roteamento: blobs entregues ao armazenamento|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|O número de vezes que o roteamento do Hub IoT entregou blobs aos pontos de extremidade de armazenamento.|Nenhum|
|EventGridDeliveries|Entregas da grade de eventos (visualização)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|O número de eventos do Hub IoT publicados na grade de eventos. Use a dimensão de resultado para o número de solicitações bem-sucedidas e com falha. A dimensão EventType mostra o tipo de evento (https://aka.ms/ioteventgrid).|ResourceId, resultado, EventType|
|EventGridLatency|Latência da grade de eventos (versão prévia)|Milissegundos|Média|A latência média (milissegundos) de quando o evento do Hub IOT foi gerado quando o evento foi publicado na grade de eventos. Esse número é uma média entre todos os tipos de evento. Use a dimensão EventType para ver a latência de um tipo específico de evento.|ResourceId, EventType|
|d2c.twin.read.success|Leituras de gêmeos dos dispositivos bem-sucedidas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de todas as leituras de gêmeos iniciadas pelo dispositivo bem-sucedidas.|Nenhum|
|d2c.twin.read.failure|Leituras de gêmeos dos dispositivos com falhas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de todas as leituras de gêmeos iniciadas pelo dispositivo com falhas.|Nenhum|
|d2c.twin.read.size|Tamanho da resposta das leituras de gêmeos dos dispositivos|Bytes|Média|A média, o mínimo e o máximo de todas as leituras de gêmeos iniciadas pelo dispositivo bem-sucedidas.|Nenhum|
|d2c.twin.update.success|Atualizações de gêmeos dos dispositivos bem-sucedidas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de todas as atualizações de gêmeos iniciadas pelo dispositivo bem-sucedidas.|Nenhum|
|d2c.twin.update.failure|Atualizações de gêmeos dos dispositivos com falhas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de todas as atualizações de gêmeos iniciadas pelo dispositivo.|Nenhum|
|d2c.twin.update.size|Tamanho das atualizações de gêmeos dos dispositivos|Bytes|Média|A média, o mínimo e o máximo de todas as atualizações de gêmeos iniciadas pelo dispositivo bem-sucedidas.|Nenhum|
|c2d.methods.success|Invocações de método diretas bem-sucedidas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de todas as chamadas de método diretas bem-sucedidas.|Nenhum|
|c2d.methods.failure|Invocações de método diretas com falhas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de todas as chamadas de método diretas com falhas.|Nenhum|
|c2d.methods.requestSize|Tamanho da solicitação das invocações de método diretas|Bytes|Média|A média, o mínimo e o máximo de todas as solicitações de método diretas bem-sucedidas.|Nenhum|
|c2d.methods.responseSize|Tamanho da resposta das invocações de método diretas|Bytes|Média|A média, o mínimo e o máximo de todas as respostas de método diretas bem-sucedidas.|Nenhum|
|c2d.twin.read.success|Leituras de gêmeos de back-end bem-sucedidas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de todas as leituras de gêmeos bem-sucedidas iniciadas pelo back-end.|Nenhum|
|c2d.twin.read.failure|Leituras de gêmeos de back-end com falhas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de todas as falhas de leituras de gêmeos iniciadas pelo back-end.|Nenhum|
|c2d.twin.read.size|Tamanho da resposta das leituras de gêmeos de back-end|Bytes|Média|A média, o mínimo e o máximo de todas as leituras de gêmeos bem-sucedidas iniciadas pelo back-end.|Nenhum|
|c2d.twin.update.success|Atualizações de gêmeos de back-end bem-sucedidas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de todas as atualizações de gêmeos iniciadas pelo back-end bem-sucedidas.|Nenhum|
|c2d.twin.update.failure|Atualizações de gêmeos de back-end com falhas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de todas as falhas de atualizações de gêmeos iniciadas pelo back-end.|Nenhum|
|c2d.twin.update.size|Tamanho das atualizações de gêmeos de back-end|Bytes|Média|A média, o mínimo e o máximo de todas as atualizações de gêmeos iniciadas pelo back-end bem-sucedidas.|Nenhum|
|twinQueries.success|Consultas de gêmeos bem-sucedidas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de todas as consultas de gêmeos bem-sucedidas.|Nenhum|
|twinQueries.failure|Consultas de gêmeos com falhas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de todas as consultas de gêmeos com falhas.|Nenhum|
|twinQueries.resultSize|Tamanho do resultado das consultas de gêmeos|Bytes|Média|A média, o mínimo e o máximo do tamanho do resultado de todas as consultas de gêmeos bem-sucedidas.|Nenhum|
|jobs.createTwinUpdateJob.success|Criações de trabalhos de atualização de gêmeos bem-sucedidas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de todos os trabalhos de criação de atualização de gêmeos bem-sucedidos.|Nenhum|
|jobs.createTwinUpdateJob.failure|Criações de trabalhos de atualização de gêmeos com falhas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de todos os trabalhos de criação de atualização de gêmeos com falhas.|Nenhum|
|jobs.createDirectMethodJob.success|Criações de trabalhos de invocação de método bem-sucedidas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de todas as criações de trabalhos de invocação de método direto bem-sucedidas.|Nenhum|
|jobs.createDirectMethodJob.failure|Criações de trabalhos de invocação de método com falhas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de todas as criações de trabalhos de invocação de método direto com falhas.|Nenhum|
|jobs.listJobs.success|Chamadas para listar trabalhos bem-sucedidas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de todas as chamadas para listar trabalhos bem-sucedidas.|Nenhum|
|jobs.listJobs.failure|Chamadas para listar trabalhos com falhas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de todas as chamadas para listar trabalhos com falhas.|Nenhum|
|jobs.cancelJob.success|Cancelamentos de trabalho bem-sucedidos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de todas as chamadas para cancelar um trabalho bem-sucedidas.|Nenhum|
|jobs.cancelJob.failure|Cancelamentos de trabalho com falhas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de todas as chamadas para cancelar um trabalho com falhas.|Nenhum|
|jobs.queryJobs.success|Consultas de trabalho bem-sucedidas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de todas as chamadas para consultas de trabalho bem-sucedidas.|Nenhum|
|jobs.queryJobs.failure|Consultas de trabalho com falhas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de todas as chamadas para consultas de trabalho com falhas.|Nenhum|
|jobs.completed|Trabalhos concluídos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de todos os trabalhos concluídos.|Nenhum|
|jobs.failed|Trabalhos com falha|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de todos os trabalhos com falha.|Nenhum|
|d2c.telemetry.ingress.sendThrottle|Número de erros de limitação|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de erros de limitação devido a limitações da taxa de transferência do dispositivo|Nenhum|
|dailyMessageQuotaUsed|Número total de mensagens usadas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número total de mensagens usadas hoje|Nenhum|
|deviceDataUsage|Uso total de dados do dispositivo|Bytes|Total|Bytes transferidos entre todos os dispositivos conectados ao lotHub|Nenhum|
|deviceDataUsageV2|Uso total de dados de dispositivos (versão prévia)|Bytes|Total|Bytes transferidos entre todos os dispositivos conectados ao lotHub|Nenhum|
|totalDeviceCount|Total de dispositivos (versão prévia)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de dispositivos registrados para o seu hub IoT|Nenhum|
|connectedDeviceCount|Dispositivos conectados (versão prévia)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de dispositivos registrados ao seu hub IoT|Nenhum|
|configurações|Métricas de configuração|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Métricas para operações de configuração|Nenhum|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|RegistrationAttempts|Tentativas de Registro|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de tentativas de registros do dispositivo|ProvisioningServiceName, IotHubName, status|
|DeviceAssignments|Dispositivos atribuídos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de dispositivos atribuídos a um hub IoT|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Tentativas de atestado|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de tentativas de atestado do dispositivo|ProvisioningServiceName, status, protocolo|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|Addregion|Região adicionada|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Região adicionada|Região|
|AvailableStorage|Armazenamento Disponível|Bytes|Total|Armazenamento total disponível relatado à granularidade de 5 minutos|CollectionName, DatabaseName, região|
|CassandraConnectionClosures|Fechamentos de Conexão do Cassandra|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de conexões Cassandra que foram fechadas, relatadas a uma granularidade de 1 minuto|APIType, região, ClosureReason|
|CassandraRequestCharges|Encargos de Solicitação do Cassandra|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|RUs consumido para solicitações Cassandra feitas|APIType, DatabaseName, CollectionName, região, OperationType, ResourceType|
|CassandraRequests|Solicitações do Cassandra|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Número de solicitações Cassandra feitas|APIType, DatabaseName, CollectionName, região, OperationType, ResourceType, ErrorCode|
|CreateAccount|Conta criada|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Conta criada|Nenhum|
|DataUsage|Uso de dados|Bytes|Total|Uso total de dados relatados à granularidade de 5 minutos|CollectionName, DatabaseName, região|
|DeleteAccount|Conta excluída|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Conta excluída|Nenhum|
|DocumentCount|Contagem de documentos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Contagem total de documentos relatados à granularidade de 5 minutos|CollectionName, DatabaseName, região|
|DocumentQuota|Cota de documento|Bytes|Total|Cota de armazenamento total relatada à granularidade de 5 minutos|CollectionName, DatabaseName, região|
|IndexUsage|Uso do índice|Bytes|Total|Uso total do índice relatado à granularidade de 5 minutos|CollectionName, DatabaseName, região|
|MetadataRequests|MetadataRequests|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Contagem de solicitações de metadados. O Cosmos DB mantém uma coleção de metadados do sistema para cada conta, o que permite que você enumere coleções, bancos de dados, etc, e suas configurações, gratuitamente.|DatabaseName, CollectionName, região, StatusCode, função|
|MongoRequestCharge|MongoRequestCharge|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Unidades Solicitadas do Mongo Consumidas|DatabaseName, CollectionName, região, CommandName, ErrorCode|
|MongoRequests|Solicitações do Mongo|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Número de Solicitações do Mongo Feitas|DatabaseName, CollectionName, região, CommandName, ErrorCode|
|MongoRequestsCount|Taxa de solicitação de Mongo|CountPerSecond|Média|Contagem de solicitações Mongo por segundo|DatabaseName, CollectionName, região, CommandName, ErrorCode|
|MongoRequestsDelete|Taxa de solicitação de exclusão de Mongo|CountPerSecond|Média|Solicitação de exclusão de Mongo por segundo|DatabaseName, CollectionName, região, CommandName, ErrorCode|
|MongoRequestsInsert|Taxa de solicitação de inserção de Mongo|CountPerSecond|Média|Contagem de inserção de Mongo por segundo|DatabaseName, CollectionName, região, CommandName, ErrorCode|
|MongoRequestsQuery|Taxa de solicitação de consulta do Mongo|CountPerSecond|Média|Solicitação de consulta Mongo por segundo|DatabaseName, CollectionName, região, CommandName, ErrorCode|
|MongoRequestsUpdate|Taxa de solicitação de atualização do Mongo|CountPerSecond|Média|Solicitação de atualização de Mongo por segundo|DatabaseName, CollectionName, região, CommandName, ErrorCode|
|ProvisionedThroughput|Taxa de transferência provisionada|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo|Taxa de transferência provisionada|DatabaseName, CollectionName|
|RegionFailover|Falha na região|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Falha na região|Nenhum|
|RemoveRegion|Região removida|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Região removida|Região|
|ReplicationLatency|Latência de replicação P99|MilliSeconds|Média|Latência de replicação P99 nas regiões de origem e de destino para conta habilitada geograficamente|SourceRegion,TargetRegion|
|ServiceAvailability|Disponibilidade de serviços|Porcentagem|Média|Disponibilidade de solicitações de conta em uma hora, granularidade de dia ou mês|Nenhum|
|TotalRequestUnits|Total de Unidades Solicitadas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Unidades Solicitadas Consumidas|DatabaseName, CollectionName, região, StatusCode, OperationType|
|TotalRequests|Total de Solicitações|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Número de solicitações feitas|DatabaseName, CollectionName, região, StatusCode, OperationType|
|UpdateAccountKeys|Chaves de conta atualizadas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Chaves de conta atualizadas|KeyType|
|UpdateAccountNetworkSettings|Configurações de rede da conta atualizadas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Configurações de rede da conta atualizadas|Nenhum|
|UpdateAccountReplicationSettings|Configurações de replicação de conta atualizadas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Configurações de replicação de conta atualizadas|Nenhum|



## <a name="microsoftenterpriseknowledgegraphservices"></a>Microsoft. EnterpriseKnowledgeGraph/serviços

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|TransactionCount|Contagem de transações|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Contagem total de transações|TransactionCount|
|SuccessCount|Contagem Bem-sucedida|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Contagem de transações com êxito|SuccessCount|
|FailureCount|Contagem de falhas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Contagem de transações com falha|FailureCount|
|SuccessLatency|Latência de êxito|MilliSeconds|Média|Latência de transações bem-sucedidas|SuccessCount|

## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventos publicados|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Total de eventos publicados neste tópico|Tópico|
|PublishFailCount|Publicar eventos com falha|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Total de eventos com falha ao publicar neste tópico|Tópico, ErrorType, erro|
|PublishSuccessLatencyInMs|Latência de êxito na publicação|Milissegundos|Total|Latência de êxito de publicação em milissegundos|Nenhum|
|MatchedEventCount|Eventos correspondentes|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Total de eventos correspondentes a essa assinatura de evento|Tópico, EventSubscriptionName, DomainEventSubscriptionName|
|DeliveryAttemptFailCount|Eventos com falha de entrega|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Total de eventos não entregues a essa assinatura de evento|Tópico, EventSubscriptionName, DomainEventSubscriptionName, erro, ErrorType|
|DeliverySuccessCount|Eventos entregues|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Total de eventos entregues a essa assinatura de evento|Tópico, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|Duração do processamento de destino|Milissegundos|Média|Duração do processamento de destino em milissegundos|Tópico, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Eventos removidos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Total de eventos removidos correspondentes a essa assinatura de evento|Tópico, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|DeadLetteredCount|Eventos de mensagens mortas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Total de eventos de mensagens mortas correspondentes a essa assinatura de evento|Tópico, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventos publicados|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Total de eventos publicados neste tópico|Nenhum|
|PublishFailCount|Publicar eventos com falha|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Total de eventos com falha ao publicar neste tópico|ErrorType, erro|
|UnmatchedEventCount|Eventos sem correspondência|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Total de eventos sem correspondência com nenhuma das assinaturas de evento para este tópico|Nenhum|
|PublishSuccessLatencyInMs|Latência de êxito na publicação|Milissegundos|Total|Latência de êxito de publicação em milissegundos|Nenhum|
|MatchedEventCount|Eventos correspondentes|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Total de eventos correspondentes a essa assinatura de evento|EventSubscriptionName|
|DeliveryAttemptFailCount|Eventos com falha de entrega|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Total de eventos não entregues a essa assinatura de evento|Erro, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Eventos entregues|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Total de eventos entregues a essa assinatura de evento|EventSubscriptionName|
|DestinationProcessingDurationInMs|Duração do processamento de destino|Milissegundos|Média|Duração do processamento de destino em milissegundos|EventSubscriptionName|
|DroppedEventCount|Eventos removidos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Total de eventos removidos correspondentes a essa assinatura de evento|DropReason,EventSubscriptionName|
|DeadLetteredCount|Eventos de mensagens mortas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Total de eventos de mensagens mortas correspondentes a essa assinatura de evento|DeadLetterReason,EventSubscriptionName|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|MatchedEventCount|Eventos correspondentes|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Total de eventos correspondentes a essa assinatura de evento|Nenhum|
|DeliveryAttemptFailCount|Eventos com falha de entrega|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Total de eventos não entregues a essa assinatura de evento|Erro, ErrorType|
|DeliverySuccessCount|Eventos entregues|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Total de eventos entregues a essa assinatura de evento|Nenhum|
|DestinationProcessingDurationInMs|Duração do processamento de destino|Milissegundos|Média|Duração do processamento de destino em milissegundos|Nenhum|
|DroppedEventCount|Eventos removidos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Total de eventos removidos correspondentes a essa assinatura de evento|DropReason|
|DeadLetteredCount|Eventos de mensagens mortas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Total de eventos de mensagens mortas correspondentes a essa assinatura de evento|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventos publicados|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Total de eventos publicados neste tópico|Nenhum|
|PublishFailCount|Publicar eventos com falha|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Total de eventos com falha ao publicar neste tópico|ErrorType, erro|
|UnmatchedEventCount|Eventos sem correspondência|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Total de eventos sem correspondência com nenhuma das assinaturas de evento para este tópico|Nenhum|
|PublishSuccessLatencyInMs|Latência de êxito na publicação|Milissegundos|Total|Latência de êxito de publicação em milissegundos|Nenhum|



## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|SuccessfulRequests|Solicitações bem sucedidas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Solicitações Bem-sucedidas para Microsoft.EventHub.|EntityName, OperationResult|
|ServerErrors|Erros de servidor.|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Erros do Servidor para o Microsoft.EventHub.|EntityName, OperationResult|
|UserErrors|Erros de Usuário.|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Erros de Usuário para o Microsoft.EventHub.|EntityName, OperationResult|
|QuotaExceededErrors|Cota de Erros Excedida.|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Cota de Erros Excedida para o Microsoft.EventHub.|EntityName, OperationResult|
|ThrottledRequests|Solicitações Limitadas.|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Solicitações Limitadas para o Microsoft.EventHub.|EntityName, OperationResult|
|IncomingRequests|Solicitações de entrada|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Solicitações de Entrada para o Microsoft.EventHub.|EntityName|
|IncomingMessages|Mensagens de entrada|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Mensagens de Entrada para o Microsoft.EventHub.|EntityName|
|OutgoingMessages|Mensagens de saída|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Mensagens de Saída para o Microsoft.EventHub.|EntityName|
|IncomingBytes|Bytes de Entrada.|Bytes|Total|Bytes de Entrada para o Microsoft.EventHub.|EntityName|
|OutgoingBytes|Bytes de Saída.|Bytes|Total|Bytes de Saída para o Microsoft.EventHub.|EntityName|
|ActiveConnections|ActiveConnections|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Total de Conexões Ativas para o Microsoft.EventHub.|Nenhum|
|ConnectionsOpened|Conexões Abertas.|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Conexões Abertas para o Microsoft.EventHub.|EntityName|
|ConnectionsClosed|Conexões Fechadas.|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Conexões Fechadas para o Microsoft.EventHub.|EntityName|
|CaptureBacklog|Lista de Pendências de Captura.|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Lista de Pendências de Captura para o Microsoft.EventHub.|EntityName|
|CapturedMessages|Mensagens Capturadas.|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Mensagens Capturadas para o Microsoft.EventHub.|EntityName|
|CapturedBytes|Bytes Capturados.|Bytes|Total|Bytes Capturados para o Microsoft.EventHub.|EntityName|
|Size|Size|Bytes|Média|Tamanho de um EventHub em Bytes.|EntityName|
|INREQS|Solicitações de entrada (preteridas)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Total de solicitações de envio de entrada para um namespace (preterido)|Nenhum|
|SUCCREQ|Solicitações com êxito (preteridas)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Total de solicitações bem-sucedidas para um namespace (preterido)|Nenhum|
|FAILREQ|Solicitações com falha (preteridas)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Total de solicitações com falha para um namespace (preterido)|Nenhum|
|SVRBSY|Erros do servidor ocupado (preterido)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Total de erros do servidor ocupado para um namespace (preterido)|Nenhum|
|INTERR|Erros de servidor interno (preterido)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Total de erros internos do servidor para um namespace (preterido)|Nenhum|
|MISCERR|Outros erros (preterido)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Total de solicitações com falha para um namespace (preterido)|Nenhum|
|INMSGS|Mensagens de entrada (obsoletas) (preteridas)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Total de mensagens de entrada para um namespace. Essa métrica foi preterida. Use a métrica de mensagens de entrada em vez disso (preterido)|Nenhum|
|EHINMSGS|Mensagens de Entrada (Preterida)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Total de mensagens de entrada para um namespace (preterido)|Nenhum|
|OUTMSGS|Mensagens de saída (obsoletas) (preteridas)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Total de mensagens de saída para um namespace. Essa métrica foi preterida. Use a métrica de mensagens de saída em vez disso (preterido)|Nenhum|
|EHOUTMSGS|Mensagens de Saída (Preterida)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Total de mensagens de saída para um namespace (preterido)|Nenhum|
|EHINMBS|Bytes de entrada (obsoletos) (preterido)|Bytes|Total|Taxa de transferência de mensagem de entrada do Hub de Eventos para um namespace. Essa métrica foi preterida. Use a métrica de bytes de entrada em vez disso (preterido)|Nenhum|
|EHINBYTES|Bytes de entrada (Preterida)|Bytes|Total|Taxa de transferência de mensagem de entrada do hub de eventos para um namespace (preterido)|Nenhum|
|EHOUTMBS|Bytes de saída (obsoletos) (preterido)|Bytes|Total|Taxa de transferência de mensagem de saída do Hub de Eventos para um namespace. Essa métrica foi preterida. Use a métrica de bytes de saída em vez disso (preterido)|Nenhum|
|EHOUTBYTES|Bytes de saída (Preterida)|Bytes|Total|Taxa de transferência de mensagem de saída do hub de eventos para um namespace (preterido)|Nenhum|
|EHABL|Arquivar mensagens de pendências (preterido)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Mensagens de arquivamento do hub de eventos no backlog para um namespace (preterido)|Nenhum|
|EHAMSGS|Arquivar mensagens (preterido)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Mensagens arquivadas do hub de eventos em um namespace (preterido)|Nenhum|
|EHAMBS|Taxa de transferência de mensagem de arquivamento (preterido)|Bytes|Total|Taxa de transferência de mensagem arquivada do hub de eventos em um namespace (preterido)|Nenhum|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|SuccessfulRequests|Solicitações bem sucedidas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Solicitações Bem-sucedidas para Microsoft.EventHub.|OperationResult|
|ServerErrors|Erros de servidor.|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Erros do Servidor para o Microsoft.EventHub.|OperationResult|
|UserErrors|Erros de Usuário.|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Erros de Usuário para o Microsoft.EventHub.|OperationResult|
|QuotaExceededErrors|Cota de Erros Excedida.|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Cota de Erros Excedida para o Microsoft.EventHub.|OperationResult|
|ThrottledRequests|Solicitações Limitadas.|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Solicitações Limitadas para o Microsoft.EventHub.|OperationResult|
|IncomingRequests|Solicitações de entrada|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Solicitações de Entrada para o Microsoft.EventHub.|Nenhum|
|IncomingMessages|Mensagens de entrada|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Mensagens de Entrada para o Microsoft.EventHub.|Nenhum|
|OutgoingMessages|Mensagens de saída|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Mensagens de Saída para o Microsoft.EventHub.|Nenhum|
|IncomingBytes|Bytes de Entrada.|Bytes|Total|Bytes de Entrada para o Microsoft.EventHub.|Nenhum|
|OutgoingBytes|Bytes de Saída.|Bytes|Total|Bytes de Saída para o Microsoft.EventHub.|Nenhum|
|ActiveConnections|ActiveConnections|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Total de Conexões Ativas para o Microsoft.EventHub.|Nenhum|
|ConnectionsOpened|Conexões Abertas.|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Conexões Abertas para o Microsoft.EventHub.|Nenhum|
|ConnectionsClosed|Conexões Fechadas.|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Conexões Fechadas para o Microsoft.EventHub.|Nenhum|
|CaptureBacklog|Lista de Pendências de Captura.|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Lista de Pendências de Captura para o Microsoft.EventHub.|Nenhum|
|CapturedMessages|Mensagens Capturadas.|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Mensagens Capturadas para o Microsoft.EventHub.|Nenhum|
|CapturedBytes|Bytes Capturados.|Bytes|Total|Bytes Capturados para o Microsoft.EventHub.|Nenhum|
|CPU|CPU|Porcentagem|Máximo|Utilização da CPU para o Cluster do Hub de Eventos como um percentual|Função|
|AvailableMemory|Memória disponível|Porcentagem|Máximo|Memória disponível para o cluster do hub de eventos como uma porcentagem da memória total.|Função|


## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|GatewayRequests|Solicitações de gateway|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de solicitações de gateway|HttpStatus|
|CategorizedGatewayRequests|Solicitações de gateway categorizadas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de solicitações de gateway por categorias (1xx/2xx/3xx/4xx/5xx)|HttpStatus|
|NumActiveWorkers|Número de trabalhadores ativos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo|Número de trabalhadores ativos|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|ObservedMetricValue|Valor de Métrica Observado|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|O valor calculado pelo dimensionamento automático quando executado|MetricTriggerSource|
|MetricThreshold|Limite da Métrica|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|O limite do dimensionamento automático configurado quando o dimensionamento automático foi executado.|MetricTriggerRule|
|ObservedCapacity|Capacidade Observada|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|A capacidade relatada para o dimensionamento automático quando ele foi executado.|Nenhum|
|ScaleActionsInitiated|Ações de Escala Iniciadas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A direção da operação de escala.|ScaleDirection|




## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Availability|Porcentagem|Média|Percentual de testes de disponibilidade concluídos com êxito|availabilityResult/nome, availabilityResult/local|
|availabilityResults/contagem|Testes de disponibilidade|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Contagem de testes de disponibilidade|availabilityResult/nome, availabilityResult/local, availabilityResult/êxito|
|availabilityResults/duration|Duração do teste de disponibilidade|MilliSeconds|Média|Duração do teste de disponibilidade|availabilityResult/nome, availabilityResult/local, availabilityResult/êxito|
|browserTimings/networkDuration|Tempo de conexão de rede de carregamento de página|MilliSeconds|Média|Tempo entre a solicitação do usuário e a conexão de rede. Inclui a pesquisa DNS e a conexão de transporte.|Nenhum|
|browserTimings/processingDuration|Tempo de processamento do cliente|MilliSeconds|Média|Tempo entre o recebimento do último byte de um documento até que o DOM seja carregado. As solicitações assíncronas ainda podem estar sendo processadas.|Nenhum|
|browserTimings/receiveDuration|Tempo de resposta de recebimento|MilliSeconds|Média|Tempo entre o primeiro e o último byte, ou até a desconexão.|Nenhum|
|browserTimings/sendDuration|Tempo de solicitação de envio|MilliSeconds|Média|Tempo entre a conexão de rede e o recebimento do primeiro byte.|Nenhum|
|browserTimings/totalDuration|Tempo de carregamento de página do navegador|MilliSeconds|Média|Tempo de solicitação do usuário até que o DOM, as imagens, os scripts e as folhas de estilo sejam carregados.|Nenhum|
|dependencies/count|Chamadas de dependência|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Contagem de chamadas feitas pelo aplicativo a recursos externos.|dependência/tipo, dependência/performanceBucket, dependência/êxito, dependência/destino, operação/sintética, nuvem/roleInstance, nuvem/roleName|
|dependencies/duration|Duração da dependência|MilliSeconds|Média|Duração das chamadas feitas pelo aplicativo a recursos externos.|dependência/tipo, dependência/performanceBucket, dependência/êxito, dependência/destino, operação/sintética, nuvem/roleInstance, nuvem/roleName|
|dependencies/failed|Falhas de chamada de dependência|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Contagem de chamadas de dependência com falha feitas pelo aplicativo a recursos externos.|dependência/tipo, dependência/performanceBucket, dependência/êxito, dependência/destino, operação/sintética, nuvem/roleInstance, nuvem/roleName|
|pageViews/count|Visualizações de página|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Contagem de exibições de página.|operação/sintética, nuvem/roleName|
|pageViews/duration|Tempo de carregamento de exibição de página|MilliSeconds|Média|Tempo de carregamento de exibição de página|operação/sintética, nuvem/roleName|
|performanceCounters/requestExecutionTime|Tempo de execução de solicitação HTTP|MilliSeconds|Média|Tempo de execução da solicitação mais recente.|cloud/roleInstance|
|performanceCounters/requestsInQueue|Solicitações HTTP na fila do aplicativo|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Comprimento da fila de solicitação de aplicativo.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|Taxa de solicitação HTTP|CountPerSecond|Média|Taxa de todas as solicitações para o aplicativo por segundo do ASP.NET.|cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|Taxa de exceção|CountPerSecond|Média|Contagem de exceções com e sem tratamento relatadas para o Windows, incluindo exceções do .NET e exceções não gerenciadas convertidas em exceções do .NET.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Taxa de E/S de processamento|BytesPerSecond|Média|Total de bytes por segundo lidos e gravados em arquivos, rede e dispositivos.|cloud/roleInstance|
|performanceCounters/processCpuPercentage|CPU do processo|Porcentagem|Média|O percentual de tempo decorrido que todos os threads do processo usaram o processador para executar instruções. Isso pode variar entre 0 e 100. Essa métrica indica o desempenho do processo de w3wp isoladamente.|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Tempo do processador|Porcentagem|Média|O percentual de tempo que o processador gasta em threads não ociosos.|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Memória disponível|Bytes|Média|Memória física disponível imediatamente para alocação a um processo ou para uso do sistema.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|Processar bytes particulares|Bytes|Média|Memória atribuída exclusivamente aos processos do aplicativo monitorado.|cloud/roleInstance|
|requests/duration|Tempo de resposta do servidor|MilliSeconds|Média|Tempo entre o recebimento de uma solicitação HTTP e a finalização do envio da resposta.|solicitação/performanceBucket, solicitação/resultCode, operação/sintética, nuvem/roleInstance, solicitação/êxito, nuvem/roleName|
|requests/count|Solicitações do servidor|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Contagem de solicitações HTTP concluídas.|solicitação/performanceBucket, solicitação/resultCode, operação/sintética, nuvem/roleInstance, solicitação/êxito, nuvem/roleName|
|requests/failed|Solicitações falhas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Contagem de solicitações HTTP marcadas como com falha. Na maioria dos casos, essas são solicitações com um código de resposta >= 400 e diferente de 401.|solicitação/performanceBucket, solicitação/resultCode, solicitação/êxito, operação/sintética, nuvem/roleInstance, nuvem/roleName|
|solicitações/taxa|Taxa de solicitações do servidor|CountPerSecond|Média|Taxa de solicitações de servidor por segundo|solicitação/performanceBucket, solicitação/resultCode, operação/sintética, nuvem/roleInstance, solicitação/êxito, nuvem/roleName|
|exceptions/count|Exceções|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Contagem combinada de todas as exceções não capturadas.|Cloud/roleName, nuvem/roleInstance, cliente/tipo|
|exceptions/browser|Exceções de navegador|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Contagem de exceções não identificadas lançadas no navegador.|cliente/IsServer, nuvem/roleName|
|exceptions/server|Exceções do servidor|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Contagem de exceções não capturadas geradas no aplicativo para servidores.|cliente/IsServer, nuvem/roleName, nuvem/roleInstance|
|traces/count|Traces|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Contagem de documentos de rastreamento|Trace/nível, operação/sintético, nuvem/roleName, nuvem/roleInstance|



## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|ServiceApiHit|Total de ocorrências da API de Serviço|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Número total de ocorrências da API de serviço|ActivityType, ActivityName|
|ServiceApiLatency|Latência geral da API de Serviço|Milissegundos|Média|Latência geral das solicitações da API de serviço|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|Total de resultados da API de Serviço|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Número total de resultados da API de serviço|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|SaturationShoebox|Saturação do cofre geral|Porcentagem|Média|Capacidade do cofre usada|Activitytable, ActivityName, transactionType|
|Availability|Disponibilidade geral do cofre|Porcentagem|Média|Disponibilidade de solicitações do cofre|ActivityType, ActivityName, StatusCode, StatusCodeClass|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|CacheUtilization|Utilização de cache|Porcentagem|Média|Nível de utilização no escopo do cluster|Nenhum|
|QueryDuration|Duração da consulta|Milissegundos|Média|Duração das consultas em segundos|QueryStatus|
|IngestionUtilization|Utilização de ingestão|Porcentagem|Média|Taxa de slots de ingestão usados no cluster|Nenhum|
|KeepAlive|Keep Alive|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|A verificação de integridade indica que o cluster responde às consultas|Nenhum|
|IngestionVolumeInMB|Volume de ingestão (em MB)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Volume total de dados ingeridos no cluster (em MB)|Database|
|IngestionLatencyInSeconds|Latência de ingestão (em segundos)|Segundos|Média|Tempo de ingestão da origem (por exemplo, mensagem no Hub de Eventos) para o cluster em segundos|Nenhum|
|EventsProcessedForEventHubs|Eventos processados (para hubs de evento/IoT)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de eventos processados pelo cluster ao ingerir de evento/Hub IoT|EventStatus|
|IngestionResult|Resultado da ingestão|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Número de operações de ingestão|IngestionResultDetails|
|CPU|CPU|Porcentagem|Média|Nível de utilização da CPU|Nenhum|
|ContinuousExportNumOfRecordsExported|Exportação contínua – núm de registros exportados|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de registros exportados, acionados para cada artefato de armazenamento gravado durante a operação de exportação|Nenhum|
|ExportUtilization|Utilização da exportação|Porcentagem|Máximo|Utilização da exportação|Nenhum|
|ContinuousExportPendingCount|Contagem de exportação contínua pendente|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo|O número de trabalhos de exportação contínuos pendentes prontos para execução|Nenhum|
|ContinuousExportMaxLatenessMinutes|Minutos de atraso máximo de exportação contínua|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo|A finalização máxima em minutos de todas as exportações contínuas pendentes e prontas para execução|Nenhum|
|ContinuousExportResult|Resultado da exportação contínua|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Indica se a exportação contínua foi bem-sucedida ou falhou|ContinuousExportName, resultado, banco de dados|
|StreamingIngestDuration|Duração da ingestão de streaming|Milissegundos|Média|Duração da ingestão de streaming em milissegundos|Nenhum|
|StreamingIngestDataRate|Taxa de dados de ingestão de streaming|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Taxa de dados de ingestão de streaming (MB por segundo)|Nenhum|
|SteamingIngestRequestRate|Taxa de solicitação de ingestão de streaming|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|RateRequestsPerSecond|Taxa de solicitação de ingestão de streaming (solicitações por segundo)|Nenhum|
|StreamingIngestResults|Resultado de ingestão de streaming|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Resultado de ingestão de streaming|Resultado|
|TotalNumberOfConcurrentQueries|Número total de consultas simultâneas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número total de consultas simultâneas|Nenhum|
|TotalNumberOfThrottledQueries|Número total de consultas limitadas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número total de consultas limitadas|Nenhum|
|TotalNumberOfThrottledCommands|Número total de comandos regulados|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número total de comandos regulados|CommandType|
|TotalNumberOfExtents|Número total de extensões|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número total de extensões de dados|Nenhum|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|RunsStarted|Execuções iniciadas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de execuções fluxo de trabalho iniciadas.|Nenhum|
|RunsCompleted|Execuções concluídas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de execuções de fluxo de trabalho concluídas.|Nenhum|
|RunsSucceeded|Execuções bem sucedidas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de execuções de fluxo de trabalho bem sucedidas.|Nenhum|
|RunsFailed|Execuções com falha|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de execuções de fluxo de trabalho com falha.|Nenhum|
|RunsCancelled|Execuções canceladas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de execuções de fluxo de trabalho canceladas.|Nenhum|
|RunLatency|Latência da execução|Segundos|Média|Latência das execuções de fluxo de trabalho concluídas.|Nenhum|
|RunSuccessLatency|Latência de execução bem sucedida|Segundos|Média|Latência de execuções de fluxo de trabalho bem sucedidas.|Nenhum|
|RunThrottledEvents|Eventos de restrição de execução|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de eventos de restrição de ações ou gatilhos de fluxo de trabalho.|Nenhum|
|RunStartThrottledEvents|Executar eventos restritos de início|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de eventos restritos de início de fluxo de trabalho.|Nenhum|
|RunFailurePercentage|Porcentagem de falha de execução|Porcentagem|Total|Porcentagem de execuções do fluxo de trabalho com falha.|Nenhum|
|ActionsStarted|Ações iniciadas |{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de ações de fluxo de trabalho iniciadas.|Nenhum|
|ActionsCompleted|Ações concluídas |{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de ações de fluxo de trabalho concluídas.|Nenhum|
|ActionsSucceeded|Ações bem sucedidas |{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de ações de fluxo de trabalho bem sucedidas.|Nenhum|
|ActionsFailed|Ações com falha |{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de ações de fluxo de trabalho com falha.|Nenhum|
|ActionsSkipped|Ações ignoradas |{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de ações de fluxo de trabalho ignoradas.|Nenhum|
|ActionLatency|Latência da ação |Segundos|Média|Latência das ações de fluxo de trabalho concluídas.|Nenhum|
|ActionSuccessLatency|Latência das ações bem sucedidas |Segundos|Média|Latência das ações de fluxo de trabalho bem sucedidas.|Nenhum|
|ActionThrottledEvents|Eventos de restrição de ações|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de eventos de restrição de ações de fluxo de trabalho.|Nenhum|
|TriggersStarted|Gatilhos iniciados |{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de gatilhos de fluxo de trabalho iniciadas.|Nenhum|
|TriggersCompleted|Gatilhos concluídos |{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de gatilhos de fluxo de trabalho concluídos.|Nenhum|
|TriggersSucceeded|Gatilhos bem sucedidos |{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de gatilhos de fluxo de trabalho bem sucedidos.|Nenhum|
|TriggersFailed|Gatilhos com falha |{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de gatilhos de fluxo de trabalho com falha.|Nenhum|
|TriggersSkipped|Gatilhos ignorados|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de gatilhos de fluxo de trabalho ignorados.|Nenhum|
|TriggersFired|Gatilhos acionados |{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de gatilhos de fluxo de trabalho acionados.|Nenhum|
|TriggerLatency|Latência do gatilho |Segundos|Média|Latência dos gatilhos de fluxo de trabalho concluídos.|Nenhum|
|TriggerFireLatency|Latência do gatilho acionado |Segundos|Média|Latência dos gatilhos de fluxo de trabalho acionados.|Nenhum|
|TriggerSuccessLatency|Latência do gatilho bem sucedido |Segundos|Média|Latência dos gatilhos de fluxo de trabalho bem sucedidos.|Nenhum|
|TriggerThrottledEvents|Eventos de restrição do gatilho|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de eventos de restrição do gatilho de fluxo de trabalho.|Nenhum|
|BillableActionExecutions|Execuções de ação faturáveis|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de execuções de ação de fluxo de trabalho sendo faturadas.|Nenhum|
|BillableTriggerExecutions|Execuções de gatilho faturáveis|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de execuções de gatilho do fluxo de trabalho sendo cobradas.|Nenhum|
|TotalBillableExecutions|Total de execuções faturáveis|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de execuções de fluxo de trabalho sendo faturadas.|Nenhum|
|BillingUsageNativeOperation|Uso de Cobrança para Execuções de Operação Nativa|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de execuções de operação nativa sendo cobradas.|Nenhum|
|BillingUsageStandardConnector|Uso de Cobrança para Execuções de Conector Padrão|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de execuções do conector padrão sendo cobradas.|Nenhum|
|BillingUsageStorageConsumption|Uso de Cobrança para Execuções de Consumo de Armazenamento|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de execuções de consumo de armazenamento sendo cobradas.|Nenhum|
|BillingUsageNativeOperation|Uso de Cobrança para Execuções de Operação Nativa|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de execuções de operação nativa sendo cobradas.|Nenhum|
|BillingUsageStandardConnector|Uso de Cobrança para Execuções de Conector Padrão|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de execuções do conector padrão sendo cobradas.|Nenhum|
|BillingUsageStorageConsumption|Uso de Cobrança para Execuções de Consumo de Armazenamento|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de execuções de consumo de armazenamento sendo cobradas.|Nenhum|



## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|RunsStarted|Execuções iniciadas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de execuções fluxo de trabalho iniciadas.|Nenhum|
|RunsCompleted|Execuções concluídas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de execuções de fluxo de trabalho concluídas.|Nenhum|
|RunsSucceeded|Execuções bem sucedidas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de execuções de fluxo de trabalho bem sucedidas.|Nenhum|
|RunsFailed|Execuções com falha|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de execuções de fluxo de trabalho com falha.|Nenhum|
|RunsCancelled|Execuções canceladas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de execuções de fluxo de trabalho canceladas.|Nenhum|
|RunLatency|Latência da execução|Segundos|Média|Latência das execuções de fluxo de trabalho concluídas.|Nenhum|
|RunSuccessLatency|Latência de execução bem sucedida|Segundos|Média|Latência de execuções de fluxo de trabalho bem sucedidas.|Nenhum|
|RunThrottledEvents|Eventos de restrição de execução|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de eventos de restrição de ações ou gatilhos de fluxo de trabalho.|Nenhum|
|RunStartThrottledEvents|Executar eventos restritos de início|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de eventos restritos de início de fluxo de trabalho.|Nenhum|
|RunFailurePercentage|Porcentagem de falha de execução|Porcentagem|Total|Porcentagem de execuções do fluxo de trabalho com falha.|Nenhum|
|ActionsStarted|Ações iniciadas |{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de ações de fluxo de trabalho iniciadas.|Nenhum|
|ActionsCompleted|Ações concluídas |{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de ações de fluxo de trabalho concluídas.|Nenhum|
|ActionsSucceeded|Ações bem sucedidas |{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de ações de fluxo de trabalho bem sucedidas.|Nenhum|
|ActionsFailed|Ações com falha |{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de ações de fluxo de trabalho com falha.|Nenhum|
|ActionsSkipped|Ações ignoradas |{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de ações de fluxo de trabalho ignoradas.|Nenhum|
|ActionLatency|Latência da ação |Segundos|Média|Latência das ações de fluxo de trabalho concluídas.|Nenhum|
|ActionSuccessLatency|Latência das ações bem sucedidas |Segundos|Média|Latência das ações de fluxo de trabalho bem sucedidas.|Nenhum|
|ActionThrottledEvents|Eventos de restrição de ações|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de eventos de restrição de ações de fluxo de trabalho.|Nenhum|
|TriggersStarted|Gatilhos iniciados |{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de gatilhos de fluxo de trabalho iniciadas.|Nenhum|
|TriggersCompleted|Gatilhos concluídos |{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de gatilhos de fluxo de trabalho concluídos.|Nenhum|
|TriggersSucceeded|Gatilhos bem sucedidos |{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de gatilhos de fluxo de trabalho bem sucedidos.|Nenhum|
|TriggersFailed|Gatilhos com falha |{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de gatilhos de fluxo de trabalho com falha.|Nenhum|
|TriggersSkipped|Gatilhos ignorados|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de gatilhos de fluxo de trabalho ignorados.|Nenhum|
|TriggersFired|Gatilhos acionados |{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de gatilhos de fluxo de trabalho acionados.|Nenhum|
|TriggerLatency|Latência do gatilho |Segundos|Média|Latência dos gatilhos de fluxo de trabalho concluídos.|Nenhum|
|TriggerFireLatency|Latência do gatilho acionado |Segundos|Média|Latência dos gatilhos de fluxo de trabalho acionados.|Nenhum|
|TriggerSuccessLatency|Latência do gatilho bem sucedido |Segundos|Média|Latência dos gatilhos de fluxo de trabalho bem sucedidos.|Nenhum|
|TriggerThrottledEvents|Eventos de restrição do gatilho|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de eventos de restrição do gatilho de fluxo de trabalho.|Nenhum|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Uso do processador de fluxo de trabalho para Ambiente de Serviço de Integração|Porcentagem|Média|Uso do processador do fluxo de trabalho para o ambiente do serviço de integração.|Nenhum|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Uso de memória do fluxo de trabalho para Ambiente de Serviço de Integração|Porcentagem|Média|Uso de memória do fluxo de trabalho para o ambiente do serviço de integração.|Nenhum|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Uso do processador do conector para Ambiente de Serviço de Integração|Porcentagem|Média|Uso do processador do conector para o ambiente do serviço de integração.|Nenhum|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Uso de memória do conector para Ambiente de Serviço de Integração|Porcentagem|Média|Uso de memória do conector para o ambiente do serviço de integração.|Nenhum|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|Execuções concluídas|Execuções concluídas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de execuções concluídas com êxito para este espaço de trabalho|Cenário, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Execuções iniciadas|Execuções iniciadas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de execuções iniciadas para este espaço de trabalho|Cenário, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Execuções com falha|Execuções com falha|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de execuções com falha para este espaço de trabalho|Cenário, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Registro de modelo bem-sucedido|Registro de modelo bem-sucedido|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de registros de modelo que foram bem-sucedidos neste espaço de trabalho|Cenário|
|Falha no registro do modelo|Falha no registro do modelo|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de registros de modelo que falharam neste espaço de trabalho|Cenário, StatusCode|
|Implantação de Modelo iniciado|Implantação de Modelo iniciado|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de implantações de modelo iniciadas neste espaço de trabalho|Cenário|
|Implantação de Modelo com êxito|Implantação de Modelo com êxito|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de implantações de modelo que foram bem-sucedidas neste espaço de trabalho|Cenário|
|Falha na Implantação de Modelo|Falha na Implantação de Modelo|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de implantações de modelo que falharam neste espaço de trabalho|Cenário, StatusCode|
|Total de nós|Total de nós|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número total de nós. Esse total inclui alguns nós ativos, nós ociosos, nós inutilizáveis, nós admitidos, deixando nós|Cenário, ClusterName|
|Nós ativos|Nós ativos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de nós active. Esses são os nós que estão executando ativamente um trabalho.|Cenário, ClusterName|
|Nós ociosos|Nós ociosos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de nós ociosos. Os nós ociosos são os nós que não estão executando nenhum trabalho, mas podem aceitar novos trabalhos, se disponíveis.|Cenário, ClusterName|
|Nós inutilizáveis|Nós inutilizáveis|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de nós inutilizáveis. Os nós inutilizáveis não são funcionais devido a algum problema não resolvido. O Azure reciclará esses nós.|Cenário, ClusterName|
|Nós admitidos|Nós admitidos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de nós admitidos. Esses nós são os nós de baixa prioridade que são desfeitos do pool de nós disponível.|Cenário, ClusterName|
|Saindo de nós|Saindo de nós|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de nós de saída. A saída de nós são os nós que acabaram de processar um trabalho e vão para o estado ocioso.|Cenário, ClusterName|
|Total de núcleos|Total de núcleos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de núcleos totais|Cenário, ClusterName|
|Núcleos ativos|Núcleos ativos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de núcleos ativos|Cenário, ClusterName|
|Núcleos ociosos|Núcleos ociosos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de núcleos ociosos|Cenário, ClusterName|
|Núcleos inutilizáveis|Núcleos inutilizáveis|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de núcleos inutilizáveis|Cenário, ClusterName|
|Núcleos preempçãos|Núcleos preempçãos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de núcleos admitidos|Cenário, ClusterName|
|Deixando núcleos|Deixando núcleos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de núcleos de saída|Cenário, ClusterName|
|Porcentagem de utilização de cota|Porcentagem de utilização de cota|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Porcentagem de cota utilizada|Cenário, ClusterName, VmFamilyName, VmPriority|
|CpuUtilization|CpuUtilization|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|CPU (versão prévia)|Cenário, runId, NodeId, Createdtime|
|GpuUtilization|GpuUtilization|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|GPU (visualização)|Cenário, runId, NodeId, Createdtime, DeviceID|


## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|Uso|Uso|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Contagem de chamadas à API|ApiCategory, ApiName, ResultType, ResponseCode|
|Availability|Availability|Porcentagem|Média|Disponibilidade das APIs|ApiCategory, ApiName|

## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft. Media/mediaservices/streamingEndpoints

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|Saída|Saída|Bytes|Total|A quantidade de dados de egresso, em bytes.|OutputFormat|
|SuccessE2ELatency|Latência de ponta a ponta com êxito|Milissegundos|Média|A latência média de solicitações bem-sucedidas em milissegundos.|OutputFormat|
|Solicitações|Solicitações|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Solicitações para um ponto de extremidade de streaming.|OutputFormat, HttpStatusCode, ErrorCode|


## <a name="microsoftmediamediaservices"></a>Microsoft. Media/mediaservices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|AssetQuota|Cota de ativos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Quantos ativos são permitidos para a conta de serviço de mídia atual|Nenhum|
|AssetCount|Contagem de ativos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Quantos ativos já foram criados na conta de serviço de mídia atual|Nenhum|
|AssetQuotaUsedPercentage|Porcentagem de cota de ativos usada|Porcentagem|Média|Porcentagem de ativos usados na conta de serviço de mídia atual|Nenhum|
|ContentKeyPolicyQuota|Cota de política de chave de conteúdo|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Quantas políticas de chave de conteúdo são permitidas para a conta de serviço de mídia atual|Nenhum|
|ContentKeyPolicyCount|Contagem de política de chave de conteúdo|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Quantas políticas de chave de conteúdo já foram criadas na conta de serviço de mídia atual|Nenhum|
|ContentKeyPolicyQuotaUsedPercentage|Porcentagem de cota usada da política de chave de conteúdo|Porcentagem|Média|Porcentagem usada da política de chave de conteúdo na conta de serviço de mídia atual|Nenhum|
|StreamingPolicyQuota|Cota de política de streaming|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Quantas políticas de streaming são permitidas para a conta de serviço de mídia atual|Nenhum|
|StreamingPolicyCount|Contagem de políticas de streaming|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Quantas políticas de streaming já foram criadas na conta de serviço de mídia atual|Nenhum|
|StreamingPolicyQuotaUsedPercentage|Porcentagem de cota usada da política de streaming|Porcentagem|Média|Percentual usado pela política de streaming na conta de serviço de mídia atual|Nenhum|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft. NetApp/netAppAccounts/capacityPools/volumes

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|AverageReadLatency|Latência média de leitura|MilliSeconds|Média|Latência média de leitura em milissegundos por operação|Nenhum|
|AverageWriteLatency|Latência média de gravação|MilliSeconds|Média|Latência média de gravação em milissegundos por operação|Nenhum|
|VolumeLogicalSize|Tamanho do volume lógico|Bytes|Média|Tamanho lógico do volume (bytes usados)|Nenhum|
|VolumeSnapshotSize|Tamanho do instantâneo de volume|Bytes|Média|Tamanho de todos os instantâneos no volume|Nenhum|
|ReadIops|IOPS de leitura|CountPerSecond|Média|Operações de entrada/saída de leitura por segundo|Nenhum|
|WriteIops|IOPS de gravação|CountPerSecond|Média|Operações de entrada/saída de gravação por segundo|Nenhum|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|VolumePoolAllocatedUsed|Pool de volume alocado usado|Bytes|Média|Tamanho usado alocado do pool|Nenhum|
|VolumePoolTotalLogicalSize|Tamanho lógico total do pool de volume|Bytes|Média|Soma do tamanho lógico de todos os volumes que pertencem ao pool|Nenhum|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|BytesSentRate|Bytes Enviados|Bytes|Total|Número de bytes enviados pela Interface de Rede|Nenhum|
|BytesReceivedRate|Bytes recebidos|Bytes|Total|Número de bytes recebidos pela Interface de Rede|Nenhum|
|PacketsSentRate|Pacotes Enviados|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de bytes recebidos pela Interface de Rede|Nenhum|
|PacketsReceivedRate|Pacotes Recebidos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de pacotes recebidos pela Interface de Rede|Nenhum|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|VipAvailability|Disponibilidade do Caminho de Dados|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Disponibilidade média do caminho de dados do Balanceador de Carga por duração de tempo|FrontendIPAddress, FrontendPort|
|DipAvailability|Status de investigação de integridade|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Status médio de investigação de integridade do Balanceador de Carga por duração de tempo|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Contagem de Bytes|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número total de Bytes transmitidos no período|FrontendIPAddress, FrontendPort, direção|
|PacketCount|Contagem de Pacotes|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número total de Pacotes transmitidos no período|FrontendIPAddress, FrontendPort, direção|
|SYNCount|Contagem de SYN|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número total de Pacotes de SYN transmitidos no período|FrontendIPAddress, FrontendPort, direção|
|SnatConnectionCount|Contagem de Conexões SNAT|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número total de novas conexões SNAT criadas no período|FrontendIPAddress, BackendIPAddress, ConnectionState|
|AllocatedSnatPorts|Portas SNAT alocadas (versão prévia)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número total de portas SNAT alocadas no período|FrontendIPAddress, BackendIPAddress, ProtocolType, IsAwaitingRemoval|
|UsedSnatPorts|Portas SNAT usadas (versão prévia)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número total de portas SNAT usadas no período|FrontendIPAddress, BackendIPAddress, ProtocolType, IsAwaitingRemoval|


## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|QueryVolume|Volume de consulta|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de consultas atendidas para uma zona DNS|Nenhum|
|RecordSetCount|Contagem de Conjuntos de Registros|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo|Número de Conjuntos de Registros em uma zona DNS|Nenhum|
|RecordSetCapacityUtilization|Utilização de capacidade de Conjuntos de Registros|Porcentagem|Máximo|Porcentagem de capacidade de Conjuntos de Registros utilizada por uma zona DNS|Nenhum|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|PacketsInDDoS|DDoS de pacotes de entrada|CountPerSecond|Máximo|DDoS de pacotes de entrada|Nenhum|
|PacketsDroppedDDoS|DDoS de pacotes de entrada removidos|CountPerSecond|Máximo|DDoS de pacotes de entrada removidos|Nenhum|
|PacketsForwardedDDoS|DDoS de pacotes de entrada encaminhados|CountPerSecond|Máximo|DDoS de pacotes de entrada encaminhados|Nenhum|
|TCPPacketsInDDoS|DDoS de pacotes TCP de entrada|CountPerSecond|Máximo|DDoS de pacotes TCP de entrada|Nenhum|
|TCPPacketsDroppedDDoS|DDoS de pacotes TCP de entrada removidos|CountPerSecond|Máximo|DDoS de pacotes TCP de entrada removidos|Nenhum|
|TCPPacketsForwardedDDoS|DDoS de pacotes TCP de entrada encaminhados|CountPerSecond|Máximo|DDoS de pacotes TCP de entrada encaminhados|Nenhum|
|UDPPacketsInDDoS|DDoS de pacotes UDP de entrada|CountPerSecond|Máximo|DDoS de pacotes UDP de entrada|Nenhum|
|UDPPacketsDroppedDDoS|DDoS de pacotes UDP de entrada removidos|CountPerSecond|Máximo|DDoS de pacotes UDP de entrada removidos|Nenhum|
|UDPPacketsForwardedDDoS|DDoS de pacotes UDP de entrada encaminhados|CountPerSecond|Máximo|DDoS de pacotes UDP de entrada encaminhados|Nenhum|
|BytesInDDoS|DDoS de bytes de entrada|BytesPerSecond|Máximo|DDoS de bytes de entrada|Nenhum|
|BytesDroppedDDoS|DDoS de bytes de entrada removidos|BytesPerSecond|Máximo|DDoS de bytes de entrada removidos|Nenhum|
|BytesForwardedDDoS|DDoS de bytes de entrada encaminhados|BytesPerSecond|Máximo|DDoS de bytes de entrada encaminhados|Nenhum|
|TCPBytesInDDoS|DDoS de bytes de TCP de entrada|BytesPerSecond|Máximo|DDoS de bytes de TCP de entrada|Nenhum|
|TCPBytesDroppedDDoS|DDoS de bytes de TCP de entrada removidos|BytesPerSecond|Máximo|DDoS de bytes de TCP de entrada removidos|Nenhum|
|TCPBytesForwardedDDoS|DDoS de bytes de TCP de entrada encaminhados|BytesPerSecond|Máximo|DDoS de bytes de TCP de entrada encaminhados|Nenhum|
|UDPBytesInDDoS|DDoS de bytes de UDP de entrada|BytesPerSecond|Máximo|DDoS de bytes de UDP de entrada|Nenhum|
|UDPBytesDroppedDDoS|DDoS de bytes de UDP de entrada removidos|BytesPerSecond|Máximo|DDoS de bytes de UDP de entrada removidos|Nenhum|
|UDPBytesForwardedDDoS|DDoS de bytes de UDP de entrada encaminhados|BytesPerSecond|Máximo|DDoS de bytes de UDP de entrada encaminhados|Nenhum|
|IfUnderDDoSAttack|Sob ataque DDoS ou não|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo|Sob ataque DDoS ou não|Nenhum|
|DDoSTriggerTCPPackets|Pacotes TCP de entrada a disparar a mitigação de DDoS|CountPerSecond|Máximo|Pacotes TCP de entrada a disparar a mitigação de DDoS|Nenhum|
|DDoSTriggerUDPPackets|Pacotes UDP de entrada a disparar a mitigação de DDoS|CountPerSecond|Máximo|Pacotes UDP de entrada a disparar a mitigação de DDoS|Nenhum|
|DDoSTriggerSYNPackets|Pacotes de entrada SYN para disparar a mitigação de DDoS|CountPerSecond|Máximo|Pacotes de entrada SYN para disparar a mitigação de DDoS|Nenhum|
|VipAvailability|Disponibilidade do Caminho de Dados|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Disponibilidade média de Endereço IP por duração de tempo|Porta|
|ByteCount|Contagem de Bytes|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número total de Bytes transmitidos no período|Porta, direção|
|PacketCount|Contagem de Pacotes|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número total de Pacotes transmitidos no período|Porta, direção|
|SynCount|Contagem de SYN|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número total de Pacotes de SYN transmitidos no período|Porta, direção|



## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Tempo de ida e volta para pings em uma VM|MilliSeconds|Média|Tempo de ida e volta para pings enviados para uma VM de destino|SourceCustomerAddress,DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Pings com falha em uma VM|Porcentagem|Média|Porcentagem do número de pings com falha para o total de pings enviados de uma VM de destino|SourceCustomerAddress,DestinationCustomerAddress|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|ApplicationRuleHit|Contagem de impacto de regras de aplicativo|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de vezes que as regras de aplicativo foram atingidas|Status, motivo, protocolo|
|NetworkRuleHit|Contagem de impacto das regras de rede|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de vezes que as regras de rede foram atingidas|Status, motivo, protocolo|
|FirewallHealth|Estado de integridade do firewall|Porcentagem|Média|Estado de integridade do firewall|Status, motivo|
|Dataprocessod|Dados processados|Bytes|Total|Quantidade total de dados processados pelo firewall|Nenhum|
|SNATPortUtilization|Utilização da porta SNAT|Porcentagem|Média|Utilização da porta SNAT|Nenhum|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|Produtividade|Produtividade|BytesPerSecond|Média|Número de bytes por segundo atendidos pelo Gateway de Aplicativo|Nenhum|
|UnhealthyHostCount|Contagem de hosts não íntegros|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de hosts de back-end não íntegros|BackendSettingsPool|
|HealthyHostCount|Contagem de hosts íntegros|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de hosts de back-end íntegros|BackendSettingsPool|
|TotalRequests|Total de Solicitações|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Contagem de solicitações bem sucedidas atendidas pelo Gateway de Aplicativo|BackendSettingsPool|
|AvgRequestCountPerHealthyHost|Solicitações por minuto por host íntegro|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Contagem média de solicitações por minuto por host de back-end íntegro em um pool|BackendSettingsPool|
|FailedRequests|Solicitações com falha|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Contagem de solicitações com falha atendidas pelo Gateway de Aplicativo|BackendSettingsPool|
|ResponseStatus|Status da Resposta|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Status de resposta HTTP retornado pelo Gateway de Aplicativo|HttpStatusGroup|
|CurrentConnections|Conexões Atuais|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Contagem de conexões atuais estabelecidas com o Gateway de Aplicativo|Nenhum|
|CpuUtilization|Utilização da CPU|Porcentagem|Média|Utilização atual da CPU do gateway de aplicativo|Nenhum|
|CapacityUnits|Unidades de capacidade atuais|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Unidades de capacidade consumidas|Nenhum|
|ComputeUnits|Unidades de computação atuais|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Unidades de computação consumidas|Nenhum|
|BackendResponseStatus|Status da resposta de back-end|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|O número de códigos de resposta HTTP gerados pelos membros de back-end. Isso não inclui nenhum código de resposta gerado pelo gateway de aplicativo.|BackendServer, Httpsettings, BackendHttpSetting, HttpStatusGroup|
|TlsProtocol|Protocolo TLS do cliente|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|O número de solicitações TLS e não TLS iniciadas pelo cliente que estabeleceram a conexão com o gateway de aplicativo. Para exibir a distribuição de protocolo TLS, filtre pelo protocolo TLS de dimensão.|Ouvinte, TlsProtocol|
|BytesSent|Bytes Enviados|Bytes|Total|O número total de bytes enviados pelo gateway de aplicativo para os clientes|Ouvinte|
|BytesReceived|Bytes recebidos|Bytes|Total|O número total de bytes recebidos pelo gateway de aplicativo dos clientes|Ouvinte|
|ClientRtt|RTT do cliente|MilliSeconds|Média|Tempo médio de ida e volta entre clientes e o gateway de aplicativo. Essa métrica indica quanto tempo leva para estabelecer conexões e retornar confirmações|Ouvinte|
|ApplicationGatewayTotalTime|Tempo total do gateway de aplicativo|MilliSeconds|Média|Tempo médio que leva para que uma solicitação seja processada e sua resposta seja enviada. Isso é calculado como a média do intervalo desde o momento em que o gateway de aplicativo recebe o primeiro byte de uma solicitação HTTP até o momento em que a operação de envio de resposta é concluída. É importante observar que isso geralmente inclui o tempo de processamento do gateway de aplicativo, o tempo que os pacotes de solicitação e resposta estão viajando pela rede e o tempo que o servidor back-end levou para responder.|Ouvinte|
|BackendConnectTime|Tempo de conexão de back-end|MilliSeconds|Média|Tempo gasto estabelecendo uma conexão com um servidor de back-end|Listener, BackendServer, Httpsettings, BackendHttpSetting|
|BackendFirstByteResponseTime|Tempo de resposta do primeiro byte do back-end|MilliSeconds|Média|Intervalo de tempo entre o início do estabelecimento de uma conexão com o servidor back-end e o recebimento do primeiro byte do cabeçalho de resposta, aproximar tempo de processamento do servidor back-end|Listener, BackendServer, Httpsettings, BackendHttpSetting|
|BackendLastByteResponseTime|Tempo de resposta do último byte do back-end|MilliSeconds|Média|Intervalo de tempo entre o início do estabelecimento de uma conexão com o servidor de back-end e o recebimento do último byte do corpo da resposta|Listener, BackendServer, Httpsettings, BackendHttpSetting|
|MatchedCount|Distribuição da regra total do firewall do aplicativo Web|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Distribuição da regra total do firewall do aplicativo Web para o tráfego de entrada|Ruler, RuleId|
|BlockedCount|Distribuição de regra de solicitações bloqueadas do firewall do aplicativo Web|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Distribuição de regra de solicitações bloqueadas do firewall do aplicativo Web|Ruler, RuleId|
|BlockedReqCount|Contagem de solicitações bloqueadas do firewall do aplicativo Web|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Contagem de solicitações bloqueadas do firewall do aplicativo Web|Nenhum|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|AverageBandwidth|Largura de banda de S2S do gateway|BytesPerSecond|Média|Largura de banda média site a site de um gateway em bytes por segundo|Nenhum|
|P2SBandwidth|Largura de banda de P2S do gateway|BytesPerSecond|Média|Largura de banda média ponto a site de um gateway em bytes por segundo|Nenhum|
|P2SConnectionCount|Contagem de conexões P2S|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo|Contagem de conexões ponto a site de um gateway|Protocolo|
|TunnelAverageBandwidth|Largura de Banda de Túnel|BytesPerSecond|Média|Largura de banda média de um túnel em bytes por segundo|ConnectionName, RemoteIP|
|TunnelEgressBytes|Bytes de Saída de Túnel|Bytes|Total|Bytes de saída de um túnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Bytes de Entrada de Túnel|Bytes|Total|Bytes de entrada de um túnel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Pacotes de Saída de Túnel|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Contagem de pacotes de saída de um túnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|Pacotes de Entrada de Túnel|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Contagem de pacotes de entrada de um túnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Remoção de Pacotes Incompatíveis de TS de Saída de Túnel|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Contagem de remoção de pacotes de saída da incompatibilidade do seletor de tráfego de um túnel|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Remoção de Pacotes Incompatíveis de TS de Entrada de Túnel|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Contagem de remoção de pacotes de entrada da incompatibilidade do seletor de tráfego de um túnel|ConnectionName, RemoteIP|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft. Network/expressRoutePorts

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|RxLightLevel|RxLightLevel|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Nível de luz Rx em dBm|Link, Lane|
|TxLightLevel|TxLightLevel|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Nível de luz TX em dBm|Link, Lane|
|Adminstate|Adminstate|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Estado do administrador da porta|Link|
|LineProtocol|LineProtocol|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Status do protocolo de linha da porta|Link|
|PortBitsInPerSecond|BitsInPerSecond|CountPerSecond|Média|Bits de entrada no Azure por segundo|Link|
|PortBitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Média|Bits de saída no Azure por segundo|Link|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Média|Bits de entrada no Azure por segundo|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Média|Bits de saída no Azure por segundo|PeeringType|
|GlobalReachBitsInPerSecond|GlobalReachBitsInPerSecond|CountPerSecond|Média|Bits de entrada no Azure por segundo|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|GlobalReachBitsOutPerSecond|CountPerSecond|Média|Bits de saída no Azure por segundo|PeeredCircuitSKey|
|BgpAvailability|Disponibilidade de BGP|Porcentagem|Média|Disponibilidade de BGP de MSEE para todos os pares.|PeeringType, par|
|ArpAvailability|Disponibilidade do ARP|Porcentagem|Média|Disponibilidade ARP de MSEE para todos os pares.|PeeringType, par|
|QosDropBitsInPerSecond|DroppedInBitsPerSecond|CountPerSecond|Média|Bits de entrada de dados eliminados por segundo|Nenhum|
|QosDropBitsOutPerSecond|DroppedOutBitsPerSecond|CountPerSecond|Média|Bits de saída de dados eliminados por segundo|Nenhum|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Média|Bits de entrada no Azure por segundo|Nenhum|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Média|Bits de saída no Azure por segundo|Nenhum|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Média|Bits de entrada no Azure por segundo|Nenhum|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Média|Bits de saída no Azure por segundo|Nenhum|

## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft. Network/expressRouteGateways

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|BitsInPerSecond|CountPerSecond|Média|Bits de entrada no Azure por segundo|ConnectionName|
|ErGatewayConnectionBitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Média|Bits de saída no Azure por segundo|ConnectionName|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|QpsByEndpoint|Consultas pelo Ponto de Extremidade Retornado|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Número de vezes que um ponto de extremidade do Gerenciador de Tráfego foi retornado no período determinado|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Status do Ponto de Extremidade por Ponto de Extremidade|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo|1 se o status de investigação de um ponto de extremidade for “Habilitado”; caso contrário, 0.|EndpointName|



## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|ProbesFailedPercent|Porcentagem de investigações com falha|Porcentagem|Média|Porcentagem de investigações de monitoramento de conectividade com falha|Nenhum|
|AverageRoundtripMs|Média de tempo de ida e volta (MS)|MilliSeconds|Média|Tempo ida e volta de rede médio (ms) para investigações de monitoramento de conectividade enviadas entre a origem e o destino|Nenhum|
|ChecksFailedPercent|Verificações por cento com falha (versão prévia)|Porcentagem|Média|% de verificações de monitoramento de conectividade com falha|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, Destinationname, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName|
|RoundTripTimeMs|Tempo de ida e volta (MS) (visualização)|MilliSeconds|Média|Tempo de ida e volta em milissegundos para as verificações de monitoramento de conectividade|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, Destinationname, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|RequestCount|Contagem de solicitações|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|O número de solicitações de cliente atendidas pelo proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Tamanho da solicitação|Bytes|Total|O número de bytes enviados como solicitações de clientes ao proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Tamanho da resposta|Bytes|Total|O número de bytes enviados como respostas do proxy HTTP/S para clientes|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BillableResponseSize|Tamanho de resposta Faturável|Bytes|Total|O número de bytes faturáveis (mínimo de 2 KB por solicitação) enviados como respostas do proxy HTTP/S para os clientes.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Contagem de solicitações de back-end|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|O número de solicitações enviadas do proxy HTTP/S aos back-ends|HttpStatus, HttpStatusGroup, back-end|
|BackendRequestLatency|Latência de solicitação de back-end|MilliSeconds|Média|O tempo calculado de quando a solicitação foi enviada pelo proxy HTTP/S ao back-end até o proxy HTTP/S receber o último byte de resposta do back-end|Back-end|
|TotalLatency|Latência Total|MilliSeconds|Média|O tempo calculado de quando a solicitação do cliente foi recebida pelo proxy HTTP/S até o cliente confirmar o último byte de resposta do proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Percentual de integridade do back-end|Porcentagem|Média|O percentual de investigações de integridade bem-sucedidas do proxy HTTP/S aos back-ends|Back-end, Httpsettings|
|WebApplicationFirewallRequestCount|Contagem de solicitações do Firewall de Aplicativo Web|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|O número de solicitações de cliente processadas pelo Firewall do Aplicativo Web|PolicyName, RuleName, ação|


## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|registration.all|Operações de Registro|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de todas as operações de registro bem-sucedidas (consultas de atualizações de criações e exclusões). |Nenhum|
|registration.create|Operação de criação de registro|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de todas as criações de registro bem-sucedidas.|Nenhum|
|registration.update|Operação de atualização de registro|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de todas as atualizações de registro bem-sucedidas.|Nenhum|
|registration.get|Operações de leitura do registro|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de todas as consultas de registro bem-sucedidas.|Nenhum|
|registration.delete|Operação de exclusão de registro|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de todas as exclusões de registro bem-sucedidas.|Nenhum|
|incoming|Mensagens de entrada|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de todas as chamadas de API enviadas com sucesso. |Nenhum|
|incoming.scheduled|Notificações por push agendadas enviadas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Notificações por push agendadas canceladas|Nenhum|
|incoming.scheduled.cancel|Notificações por push agendadas canceladas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Notificações por push agendadas canceladas|Nenhum|
|scheduled.pending|Notificações agendadas pendentes|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Notificações agendadas pendentes|Nenhum|
|installation.all|Operações de gerenciamento de instalação|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Operações de gerenciamento de instalação|Nenhum|
|installation.get|Obter operações de instalação|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Obter operações de instalação|Nenhum|
|installation.upsert|Criar ou atualizar operações de instalação|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Criar ou atualizar operações de instalação|Nenhum|
|installation.patch|Operações de instalação de patch|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Operações de instalação de patch|Nenhum|
|installation.delete|Excluir operações de instalação|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Excluir operações de instalação|Nenhum|
|outgoing.allpns.success|Notificações de sucesso|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de todas as notificações bem-sucedidas.|Nenhum|
|outgoing.allpns.invalidpayload|Erros de conteúdo|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de pushes que falharam porque o PNS retornou um erro de conteúdo inválido.|Nenhum|
|outgoing.allpns.pnserror|Erros de sistema de notificação externa|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de pushes que falharam porque houve um problema na comunicação com o PNS (exclui problemas de autenticação).|Nenhum|
|outgoing.allpns.channelerror|Erros de canal|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de pushes que falharam porque o canal era inválido, não associado com o aplicativo correto, restrito ou expirado.|Nenhum|
|outgoing.allpns.badorexpiredchannel|Erros de canal incorreto ou expirado|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de pushes que falharam porque o canal/token/registrationId do registro estava expirado ou inválido.|Nenhum|
|outgoing.wns.success|Notificações do WNS bem-sucedidas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de todas as notificações bem-sucedidas.|Nenhum|
|outgoing.wns.invalidcredentials|Erros de autorização do WNS (credenciais inválidas)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de pushes que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas. (O Windows Live não reconhece as credenciais).|Nenhum|
|outgoing.wns.badchannel|Erro de canal inválido do WNS|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de pushes que falharam porque o ChannelURI do registro não foi reconhecido (status do WNS: 404 não encontrado).|Nenhum|
|outgoing.wns.expiredchannel|Erro de canal expirado do WNS|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de pushes que falharam porque o ChannelURI está expirado (status do WNS: 410 perdido).|Nenhum|
|outgoing.wns.throttled|Notificações restritas do WNS|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de pushes que falharam porque o WNS está limitando este aplicativo (status do WNS: 406 não aceitável).|Nenhum|
|outgoing.wns.tokenproviderunreachable|Erros de autorização do WNS (inacessível)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|O Windows Live não está acessível.|Nenhum|
|outgoing.wns.invalidtoken|Erros de autorização do WNS (token inválido)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|O token fornecido ao WNS não é válido (status do WNS: 401 não autorizado).|Nenhum|
|outgoing.wns.wrongtoken|Erros de autorização do WNS (token incorreto)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|O token fornecido ao WNS é válido, mas para outro aplicativo (status do WNS: 403 proibido). Isso pode acontecer se o ChannelURI do registro estiver associado a outro aplicativo. Verifique se o aplicativo cliente está associado ao mesmo aplicativo cujas credenciais estão no hub de notificação.|Nenhum|
|outgoing.wns.invalidnotificationformat|Formato de notificação inválido do WNS|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|O formato da notificação é inválido (status do WNS: 400). Observe que o WNS não rejeita todos os conteúdos inválidos.|Nenhum|
|outgoing.wns.invalidnotificationsize|Erro de tamanho de notificação inválido do WNS|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|O conteúdo de notificação é muito grande (status do WNS: 413).|Nenhum|
|outgoing.wns.channelthrottled|Canal do WNS restrito|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A notificação foi descartada porque o ChannelURI do registro está restrito (cabeçalho de resposta do WNS: X-WNS-NotificationStatus:channelThrottled).|Nenhum|
|outgoing.wns.channeldisconnected|Canal do WNS desconectado|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A notificação foi descartada porque o ChannelURI do registro está restrito (cabeçalho de resposta do WNS: X-WNS-DeviceConnectionStatus: disconnected).|Nenhum|
|outgoing.wns.dropped|Notificações do WNS descartadas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A notificação foi descartada porque o ChannelURI do registro está restrito (X-WNS-NotificationStatus: dropped but not X-WNS-DeviceConnectionStatus: disconnected).|Nenhum|
|outgoing.wns.pnserror|Erros do WNS|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Notificação não entregue devido a erros de comunicação com o WNS.|Nenhum|
|outgoing.wns.authenticationerror|Erros de autenticação do WNS|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Notificação não entregue devido a erros de comunicação com o Windows Live, credenciais inválidas ou token incorreto.|Nenhum|
|outgoing.apns.success|Notificações do APNS bem-sucedidas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de todas as notificações bem-sucedidas.|Nenhum|
|outgoing.apns.invalidcredentials|Erros de autorização do APNS|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de pushes que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Nenhum|
|outgoing.apns.badchannel|Erro de canal inválido do APNS|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de pushes que falharam porque o token é inválido (código de status do APNS: 8).|Nenhum|
|outgoing.apns.expiredchannel|Erro de canal expirado do APNS|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de tokens que foram invalidados pelo canal de comentários do APNS.|Nenhum|
|outgoing.apns.invalidnotificationsize|Erro de tamanho de notificação inválido do APNS|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de pushes que falharam porque o conteúdo era muito grande (código de status do APNS: 7).|Nenhum|
|outgoing.apns.pnserror|Erros do APNS|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de pushes que falharam devido a erros de comunicação com o APNS.|Nenhum|
|outgoing.gcm.success|Notificações do GCM bem-sucedidas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de todas as notificações bem-sucedidas.|Nenhum|
|outgoing.gcm.invalidcredentials|Erros de autorização do GCM (credenciais inválidas)|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de pushes que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Nenhum|
|outgoing.gcm.badchannel|Erro de canal inválido do GCM|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de pushes que falharam porque o registrationId do registro estava expirado ou inválido (resultado GCM: registo inválido).|Nenhum|
|outgoing.gcm.expiredchannel|Erro de canal expirado do GCM|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de pushes que falharam porque o registrationId do registro estava expirado (resultado do GCM: NotRegistered).|Nenhum|
|outgoing.gcm.throttled|Notificações restritas do GCM|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de pushes que falharam porque o GCM restringiu este aplicativo (código de status do GCM: 501 a 599 ou resultado: indisponível).|Nenhum|
|outgoing.gcm.invalidnotificationformat|Formato de notificação inválido do GCM|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de pushes que falharam porque o conteúdo não foi formatado corretamente (resultado do GCM: InvalidDataKey ou InvalidTtl).|Nenhum|
|outgoing.gcm.invalidnotificationsize|Erro de tamanho de notificação inválido do GCM|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de pushes que falharam porque o conteúdo era muito grande (resultado do GCM: MessageTooBig).|Nenhum|
|outgoing.gcm.wrongchannel|Erro de canal incorreto do GCM|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de pushes que falharam porque o registrationId do registro não está associado ao aplicativo atual (resultado do GCM: InvalidPackageName).|Nenhum|
|outgoing.gcm.pnserror|Erros do GCM|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de pushes que falharam devido a erros de comunicação com o GCM.|Nenhum|
|outgoing.gcm.authenticationerror|Erros de autenticação do GCM|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de pushes que falharam porque o PNS não aceitou as credenciais fornecidas, as credenciais estão bloqueadas ou o SenderId não está configurado corretamente no aplicativo (resultado do GCM: MismatchedSenderId).|Nenhum|
|outgoing.mpns.success|Notificações de sucesso do MPNS|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de todas as notificações bem-sucedidas.|Nenhum|
|outgoing.mpns.invalidcredentials|Credenciais inválidas do MPNS|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de pushes que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Nenhum|
|outgoing.mpns.badchannel|Erro de canal inválido do MPNS|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de pushes que falharam porque o ChannelURI do registro não foi reconhecido (status do MPNS: 404 não encontrado).|Nenhum|
|outgoing.mpns.throttled|Notificações restritas do MPNS|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de pushes que falharam porque o MPNS está limitando este aplicativo (WNS MPNS: 406 não aceitável).|Nenhum|
|outgoing.mpns.invalidnotificationformat|Erro de tamanho de notificação inválido do MPNS|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de pushes que falharam porque o conteúdo da notificação era muito grande.|Nenhum|
|outgoing.mpns.channeldisconnected|Canal do MPNS desconectado|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de pushes que falharam porque o ChannelURI do registro foi desconectado (status do MPNS: 412 não encontrado).|Nenhum|
|outgoing.mpns.dropped|Notificações descartadas do MPNS|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de pushes que foram descartados pelo MPNS (cabeçalho de resposta do MPNS: X-NotificationStatus: QueueFull ou Suppressed).|Nenhum|
|outgoing.mpns.pnserror|Erros do MPNS|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de pushes que falharam devido a erros de comunicação com o MPNS.|Nenhum|
|outgoing.mpns.authenticationerror|Erros de autenticação do MPNS|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A contagem de pushes que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Nenhum|
|notificationhub.pushes|Todas as notificações de saída|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Todas as notificações de saída do hub de notificação|Nenhum|
|incoming.all.requests|Todas as solicitações recebidas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Total de solicitações recebidas para um hub de notificação|Nenhum|
|incoming.all.failedrequests|Todas as solicitações com falha recebidas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Total de solicitações com falha recebidas para um hub de notificação|Nenhum|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|Average_% Free Inodes|% de inodes livres|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_% Free Inodes|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% Free Space|% de espaço livre|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_% Free Space|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% Used Inodes|% de inodes usados|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_% Used Inodes|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% Used Space|% de espaço utilizado|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_% Used Space|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Disk Read Bytes/sec|Bytes de Leitura de Disco/s|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Disk Read Bytes/sec|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Disk Reads/sec|Leituras de Disco/s|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Disk Reads/sec|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Disk Transfers/sec|Transferências do disco/s|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Disk Transfers/sec|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Disk Write Bytes/sec|Bytes de Gravação de Disco/s|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Disk Write Bytes/sec|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Disk Writes/sec|Gravações de Disco/s|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Disk Writes/sec|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Free Megabytes|Megabytes livres|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Free Megabytes|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Logical Disk Bytes/sec|Bytes de disco lógico/s|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Logical Disk Bytes/sec|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% Available Memory|% de memória disponível|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_% Available Memory|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% Available Swap Space|% de espaço de permuta disponível|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_% Available Swap Space|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% Used Memory|% de memória utilizada|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_% Used Memory|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% Used Swap Space|% de espaço de permuta utilizado|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_% Used Swap Space|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Available MBytes Memory|Memória de mBytes disponível|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Available MBytes Memory|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Available MBytes Swap|Permuta de mBytes disponível|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Available MBytes Swap|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Page Reads/sec|Leituras de Página/s|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Page Reads/sec|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Page Writes/sec|Gravações de Página/s|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Page Writes/sec|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Pages/sec|Páginas/segundo|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Pages/sec|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Used MBytes Swap Space|Espaço de permuta de megabytes usado|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Used MBytes Swap Space|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Used Memory MBytes|Megabytes de memória usados|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Used Memory MBytes|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Total Bytes Transmitted|Total de Bytes Transmitidos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Total Bytes Transmitted|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Total Bytes Received|Total de Bytes Recebidos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Total Bytes Received|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Total Bytes|Total de bytes|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Total Bytes|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Total Packets Transmitted|Total de Pacotes Transmitidos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Total Packets Transmitted|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Total Packets Received|Total de Pacotes Recebidos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Total Packets Received|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Total Rx Errors|Total de Erros de Rx|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Total Rx Errors|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Total Tx Errors|Total de Erros de Tx|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Total Tx Errors|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Total Collisions|Total de Colisões|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Total Collisions|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Avg. Segundos de Disco/Leitura|Média de disco s/leitura|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Avg. Segundos de Disco/Leitura|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Avg. no Disco Lógico|Média de disco s/transferência|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Avg. no Disco Lógico|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Avg. Média de Gravações/Disco s|Média de disco s/gravação|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Avg. Média de Gravações/Disco s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Physical Disk Bytes/sec|Bytes de disco físico/s|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Physical Disk Bytes/sec|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Pct Privileged Time|Pct de tempo de privilégio|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Pct Privileged Time|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Pct User Time|Pct de tempo do usuário|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Pct User Time|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Used Memory kBytes|KBytes de Memória Usada|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Used Memory kBytes|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Virtual Shared Memory|Memória compartilhada virtual|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Virtual Shared Memory|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% DPC Time|% de tempo de DPC|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_% DPC Time|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% Idle Time|% de tempo ocioso|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_% Idle Time|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% Interrupt Time|% de tempo de interrupção|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_% Interrupt Time|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% IO Wait Time|% de tempo de espera de ES|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_% IO Wait Time|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% Nice Time|% tempo adequado|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_% Nice Time|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% Privileged Time|% de tempo de privilégio|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_% Privileged Time|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% Processor Time|% do tempo do processador|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_% Processor Time|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% User Time|% de tempo do usuário|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_% User Time|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Free Physical Memory|Memória física livre|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Free Physical Memory|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Free Space in Paging Files|Espaço livre em arquivos de paginação|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Free Space in Paging Files|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Free Virtual Memory|Memória virtual livre|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Free Virtual Memory|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Processes|Processos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Processes|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Size Stored In Paging Files|Tamanho armazenado em arquivos de paginação|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Size Stored In Paging Files|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Uptime|Atividade|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Uptime|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Users|Usuários|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Users|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Avg. Segundos de Disco/Leitura|Média de disco s/leitura|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Avg. Segundos de Disco/Leitura|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Avg. Média de Gravações/Disco s|Média de disco s/gravação|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Avg. Média de Gravações/Disco s|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Current Disk Queue Length|Comprimento da Fila de Disco Atual|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Current Disk Queue Length|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Disk Reads/sec|Leituras de Disco/s|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Disk Reads/sec|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Disk Transfers/sec|Transferências do disco/s|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Disk Transfers/sec|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Disk Writes/sec|Gravações de Disco/s|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Disk Writes/sec|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Free Megabytes|Megabytes livres|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Free Megabytes|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% Free Space|% de espaço livre|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_% Free Space|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Available MBytes|MBytes Disponíveis|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Available MBytes|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% Committed Bytes In Use|% de Bytes Confirmados em Uso|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_% Committed Bytes In Use|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Bytes Received/sec|Bytes Recebidos/s|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Bytes Received/sec|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Bytes Sent/sec|Bytes Enviados/s|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Bytes Sent/sec|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Bytes Total/sec|Bytes Totais/s|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Bytes Total/sec|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% Processor Time|% do tempo do processador|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_% Processor Time|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Processor Queue Length|Comprimento da Fila do Processador|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Average_Processor Queue Length|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Pulsação|Pulsação|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Pulsação|Computador, OSType, versão, SourceComputerId|
|Atualizar|Atualizar|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Atualizar|Computador, produto, classificação, UpdateState, opcional, aprovado|
|{1&gt;Evento&lt;1}|{1&gt;Evento&lt;1}|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|{1&gt;Evento&lt;1}|Origem, EventLog, computador, EventCategory, EventLevel, EventLevelName, EventID|

## <a name="microsoftpeeringpeeringservices"></a>Microsoft. emparelhamento/peeringServices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|PrefixLatency|Latência de prefixo|Milissegundos|Média|Latência de prefixo mediana|Prefixoname|

## <a name="microsoftpeeringpeerings"></a>Microsoft. emparelhamento/emparelhamentos

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|SessionAvailabilityV4|Disponibilidade de sessão v4|Porcentagem|Média|Disponibilidade da sessão v4|ConnectionId|
|SessionAvailabilityV6|Disponibilidade de sessão V6|Porcentagem|Média|Disponibilidade da sessão V6|ConnectionId|
|IngressTrafficRate|Taxa de tráfego de entrada|BitsPerSecond|Média|Taxa de tráfego de entrada em bits por segundo|ConnectionId|
|EgressTrafficRate|Taxa de tráfego de saída|BitsPerSecond|Média|Taxa de tráfego de saída em bits por segundo|ConnectionId|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|QueryDuration|Duração da consulta|Milissegundos|Média|Duração da consulta DAX no último intervalo|Sem dimensões|
|QueryPoolJobQueueLength|Threads: tamanho da fila de trabalhos do pool consultas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Número de trabalhos na fila do pool de threads de consulta.|Sem dimensões|
|qpu_high_utilization_metric|Alta utilização de QPU|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|QPU de alta utilização no último minuto, 1 para QPU de alta utilização, caso contrário, 0|Sem dimensões|
|memory_metric|Memória|Bytes|Média|Memória. Intervalo de 0 a 3 GB para A1, 0 a 5 GB para A2, 0 a 10 GB para A3, 0 a 25 GB para A4, 0 a 50 GB para A5 e 0 a 100 GB para A6|Sem dimensões|
|memory_thrashing_metric|Sobrecarga de memória|Porcentagem|Média|Sobrecarga de memória média.|Sem dimensões|



## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|ListenerConnections bem-sucedido para o Microsoft.Relay.|EntityName, OperationResult|
|ListenerConnections-ClientError|ListenerConnections-ClientError|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|ClientError no ListenerConnections para o Microsoft.Relay.|EntityName, OperationResult|
|ListenerConnections-ServerError|ListenerConnections-ServerError|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|ServerError no ListenerConnections para o Microsoft.Relay.|EntityName, OperationResult|
|SenderConnections-Success|SenderConnections-Success|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|SenderConnections bem-sucedido para o Microsoft.Relay.|EntityName, OperationResult|
|SenderConnections-ClientError|SenderConnections-ClientError|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|ClientError no SenderConnections para o Microsoft.Relay.|EntityName, OperationResult|
|SenderConnections-ServerError|SenderConnections-ServerError|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|ServerError no SenderConnections para o Microsoft.Relay.|EntityName, OperationResult|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Total de ListenerConnections para o  Microsoft.Relay.|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Total de solicitações SenderConnections para o Microsoft.Relay.|EntityName|
|ActiveConnections|ActiveConnections|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Total de ActiveConnections para o Microsoft.Relay.|EntityName|
|ActiveListeners|ActiveListeners|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Total de ActiveListeners para o Microsoft.Relay.|EntityName|
|BytesTransferred|BytesTransferred|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Total de BytesTransferred para o Microsoft.Relay.|EntityName|
|ListenerDisconnects|ListenerDisconnects|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Total de ListenerDisconnects para o Microsoft.Relay.|EntityName|
|SenderDisconnects|SenderDisconnects|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Total de SenderDisconnects para o Microsoft.Relay.|EntityName|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|SearchLatency|Latência de pesquisa|Segundos|Média|Latência média de pesquisa do serviço de pesquisa|Nenhum|
|SearchQueriesPerSecond|Consultas de pesquisa por segundo|CountPerSecond|Média|Consultas de pesquisa por segundo para o serviço de pesquisa|Nenhum|
|ThrottledSearchQueriesPercentage|Porcentagem das consultas de pesquisa limitadas|Porcentagem|Média|Porcentagem de consultas de pesquisa que eram limitadas para o serviço de pesquisa|Nenhum|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|SuccessfulRequests|Solicitações bem sucedidas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Total de solicitações bem-sucedidas para um namespace|EntityName, OperationResult|
|ServerErrors|Erros de servidor.|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Erros do Servidor para o Microsoft.ServiceBus.|EntityName, OperationResult|
|UserErrors|Erros de Usuário.|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Erros de Usuário para o Microsoft.ServiceBus.|EntityName, OperationResult|
|ThrottledRequests|Solicitações Limitadas.|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Solicitações Limitadas para o Microsoft.ServiceBus.|EntityName, OperationResult|
|IncomingRequests|Solicitações de entrada|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Solicitações de Entrada para o Microsoft.ServiceBus.|EntityName|
|IncomingMessages|Mensagens de entrada|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Mensagens de Entrada para Microsoft.ServiceBus.|EntityName|
|OutgoingMessages|Mensagens de saída|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Mensagens de Saída para o Microsoft.ServiceBus.|EntityName|
|ActiveConnections|ActiveConnections|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Conexões Totalmente Ativas para o Microsoft.ServiceBus.|Nenhum|
|ConnectionsOpened|Conexões Abertas.|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Conexões Abertas para o Microsoft.ServiceBus.|EntityName|
|ConnectionsClosed|Conexões Fechadas.|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Conexões Fechadas para o Microsoft.ServiceBus.|EntityName|
|Size|Size|Bytes|Média|Tamanho de uma fila/tópico em bytes.|EntityName|
|Messages|Contagem de mensagens em uma fila/tópico.|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Contagem de mensagens em uma fila/tópico.|EntityName|
|ActiveMessages|Contagem de mensagens em uma fila/tópico.|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Contagem de mensagens em uma fila/tópico.|EntityName|
|DeadletteredMessages|Contagem de mensagens mortas em uma fila/tópico.|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Contagem de mensagens mortas em uma fila/tópico.|EntityName|
|ScheduledMessages|Contagem de mensagens agendadas em uma fila/tópico.|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Contagem de mensagens agendadas em uma fila/tópico.|EntityName|
|NamespaceCpuUsage|CPU|Porcentagem|Máximo|Métrica de uso de CPU do namespace Premium do barramento de serviço.|Réplica|
|NamespaceMemoryUsage|Utilização de Memória|Porcentagem|Máximo|Métrica de uso de memória do namespace Premium do barramento de serviço.|Réplica|
|CPUXNS|CPU (preterido)|Porcentagem|Máximo|Métrica de uso de CPU do namespace Premium do barramento de serviço. Essa métrica é preterida. Em vez disso, use a métrica de CPU (NamespaceCpuUsage).|Réplica|
|WSXNS|Uso de memória (preterido)|Porcentagem|Máximo|Métrica de uso de memória do namespace Premium do barramento de serviço. Essa métrica foi preterida. Use a métrica de uso de memória (NamespaceMemoryUsage) em vez disso.|Réplica|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|CPU alocada para este contêiner em núcleos de mili|ApplicationName, ServiceName, CodePackageName, ServiceId|
|AllocatedMemory|AllocatedMemory|Bytes|Média|Memória alocada para este contêiner em MB|ApplicationName, ServiceName, CodePackageName, ServiceId|
|ActualCpu|ActualCpu|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Uso real da CPU em núcleos de mili|ApplicationName, ServiceName, CodePackageName, ServiceId|
|ActualMemory|ActualMemory|Bytes|Média|Uso real da memória em MB|ApplicationName, ServiceName, CodePackageName, ServiceId|
|CpuUtilization|CpuUtilization|Porcentagem|Média|Utilização da CPU para este contêiner como porcentagem de AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceId|
|MemoryUtilization|MemoryUtilization|Porcentagem|Média|Utilização da CPU para este contêiner como porcentagem de AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceId|
|ApplicationStatus|ApplicationStatus|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Status do aplicativo de malha Service Fabric|ApplicationName, status|
|ServiceStatus|ServiceStatus|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Status de integridade de um serviço no aplicativo de malha Service Fabric|ApplicationName, status, ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Status de integridade de uma réplica de serviço no aplicativo de malha Service Fabric|ApplicationName, status, ServiceName, nome de réplica|
|ContainerStatus|ContainerStatus|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Status do contêiner no aplicativo de malha Service Fabric|ApplicationName, ServiceName, CodePackageName, ServiceId, status|
|RestartCount|RestartCount|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Reiniciar a contagem de um contêiner no aplicativo de malha Service Fabric|ApplicationName, status, ServiceName, nome de réplica, CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|ConnectionCount|Contagem de conexão|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo|A quantidade de conexões do usuário.|Ponto de extremidade|
|MessageCount|Contagem de mensagens|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|A quantidade total de mensagens.|Nenhum|
|InboundTraffic|Tráfego de entrada|Bytes|Total|O tráfego de entrada do serviço|Nenhum|
|OutboundTraffic|Tráfego de saída|Bytes|Total|O tráfego de saída do serviço|Nenhum|
|UserErrors|Erros de Usuário|Porcentagem|Máximo|A porcentagem de erros do usuário|Nenhum|
|SystemErrors|Erros do sistema|Porcentagem|Máximo|A porcentagem de erros do sistema|Nenhum|





## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|cpu_percent|Percentual de CPU|Porcentagem|Média|Percentual de CPU|Nenhum|
|physical_data_read_percent|Porcentagem de E/S de dados|Porcentagem|Média|Porcentagem de E/S de dados|Nenhum|
|log_write_percent|Porcentagem de E/S de log|Porcentagem|Média|Percentual de e/s de log. Não aplicável a data warehouses.|Nenhum|
|dtu_consumption_percent|Porcentagem de DTU|Porcentagem|Média|Porcentagem de DTU. Aplica-se a bancos de dados baseados em DTU.|Nenhum|
|armazenamento|Espaço de dados usado|Bytes|Máximo|Espaço de dados usado. Não aplicável a data warehouses.|Nenhum|
|connection_successful|Conexões bem sucedidas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Conexões bem sucedidas|Nenhum|
|connection_failed|Conexões com falha|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Conexões com falha|Nenhum|
|blocked_by_firewall|Bloqueado pelo firewall|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Bloqueado pelo firewall|Nenhum|
|deadlock|Deadlocks|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Deadlocks. Não aplicável a data warehouses.|Nenhum|
|storage_percent|Porcentagem de espaço de dados usada|Porcentagem|Máximo|Porcentagem de espaço de dados usada. Não aplicável a data warehouses ou a bancos de dados de hiperescala.|Nenhum|
|xtp_storage_percent|Percentual de armazenamento do OLTP na memória|Porcentagem|Média|Porcentagem de armazenamento OLTP na memória. Não aplicável a data warehouses.|Nenhum|
|workers_percent|Porcentagem de funcionários|Porcentagem|Média|Porcentagem de trabalhadores. Não aplicável a data warehouses.|Nenhum|
|sessions_percent|Porcentagem de sessões|Porcentagem|Média|Porcentagem de sessões. Não aplicável a data warehouses.|Nenhum|
|dtu_limit|Limite de DTU|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Limite de DTU. Aplica-se a bancos de dados baseados em DTU.|Nenhum|
|dtu_used|DTU usado|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|DTU usado. Aplica-se a bancos de dados baseados em DTU.|Nenhum|
|cpu_limit|Limite de CPU|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Limite de CPU. Aplica-se a bancos de dados baseados em vCore.|Nenhum|
|cpu_used|CPU usada|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|CPU usada. Aplica-se a bancos de dados baseados em vCore.|Nenhum|
|dwu_limit|Limite de DWU|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo|Limite de DWU. Aplica-se somente a data warehouses.|Nenhum|
|dwu_consumption_percent|Porcentagem de DWU|Porcentagem|Máximo|Porcentagem de DWU. Aplica-se somente a data warehouses.|Nenhum|
|dwu_used|DWU usado|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo|DWU usado. Aplica-se somente a data warehouses.|Nenhum|
|cache_hit_percent|Percentual de ocorrência no cache|Porcentagem|Máximo|Porcentagem de acesso ao cache. Aplica-se somente a data warehouses.|Nenhum|
|cache_used_percent|Percentual de cache usado|Porcentagem|Máximo|Percentual de cache usado. Aplica-se somente a data warehouses.|Nenhum|
|sqlserver_process_core_percent|Porcentagem de núcleo do processo de SQL Server|Porcentagem|Máximo|Percentual de uso da CPU para o processo de SQL Server, conforme medido pelo sistema operacional. Atualmente disponível somente para bancos de dados sem servidor.|Nenhum|
|sqlserver_process_memory_percent|Porcentagem de memória de processo SQL Server|Porcentagem|Máximo|Porcentagem de uso de memória para o processo de SQL Server, conforme medido pelo sistema operacional. Atualmente disponível somente para bancos de dados sem servidor.|Nenhum|
|tempdb_data_size|Tamanho do arquivo de dados tempdb em kilobytes|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo|Tamanho do arquivo de dados tempdb em kilobytes. Não aplicável a data warehouses.|Nenhum|
|tempdb_log_size|Tamanho do arquivo de log de tempdb em kilobytes|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo|Tamanho do arquivo de log de tempdb em kilobytes. Não aplicável a data warehouses.|Nenhum|
|tempdb_log_used_percent|Log de porcentagem de tempdb usado|Porcentagem|Máximo|Log de porcentagem de tempdb usado. Não aplicável a data warehouses.|Nenhum|
|local_tempdb_usage_percent|Porcentagem de local de tempdb|Porcentagem|Média|Porcentagem de tempdb local. Aplica-se somente a data warehouses.|Nenhum|
|app_cpu_billed|CPU do aplicativo cobrada|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|CPU do aplicativo cobrada. Aplica-se a bancos de dados sem servidor.|Nenhum|
|app_cpu_percent|Porcentagem de CPU do aplicativo|Porcentagem|Média|Porcentagem de CPU do aplicativo. Aplica-se a bancos de dados sem servidor.|Nenhum|
|app_memory_percent|Porcentagem de memória do aplicativo|Porcentagem|Média|Porcentagem de memória do aplicativo. Aplica-se a bancos de dados sem servidor.|Nenhum|
|allocated_data_storage|Espaço de dados alocado|Bytes|Média|Armazenamento de dados alocado. Não aplicável a data warehouses.|Nenhum|
|memory_usage_percent|Porcentagem de memória|Porcentagem|Máximo|Porcentagem de memória. Aplica-se somente a data warehouses.|Nenhum|
|full_backup_size_bytes|Tamanho de armazenamento de backup completo|Bytes|Máximo|Tamanho de armazenamento de backup completo cumulativo. Aplica-se a bancos de dados baseados em vCore. Não aplicável a bancos de dados de hiperescala.|Nenhum|
|diff_backup_size_bytes|Tamanho de armazenamento diferencial de backup|Bytes|Máximo|Tamanho do armazenamento de backup diferencial cumulativo. Aplica-se a bancos de dados baseados em vCore. Não aplicável a bancos de dados de hiperescala.|Nenhum|
|log_backup_size_bytes|Tamanho do armazenamento de backup de log|Bytes|Máximo|Tamanho de armazenamento de backup de log cumulativo. Aplica-se a bancos de dados baseados em vCore. Não aplicável a bancos de dados de hiperescala.|Nenhum|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|cpu_percent|Percentual de CPU|Porcentagem|Média|Percentual de CPU|Nenhum|
|database_cpu_percent|Percentual de CPU|Porcentagem|Média|Percentual de CPU|DatabaseResourceId|
|physical_data_read_percent|Porcentagem de E/S de dados|Porcentagem|Média|Porcentagem de E/S de dados|Nenhum|
|database_physical_data_read_percent|Porcentagem de E/S de dados|Porcentagem|Média|Porcentagem de E/S de dados|DatabaseResourceId|
|log_write_percent|Porcentagem de E/S de log|Porcentagem|Média|Porcentagem de E/S de log|Nenhum|
|database_log_write_percent|Porcentagem de E/S de log|Porcentagem|Média|Porcentagem de E/S de log|DatabaseResourceId|
|dtu_consumption_percent|Porcentagem de DTU|Porcentagem|Média|Porcentagem de DTU. Aplica-se a pools elásticos baseados em DTU.|Nenhum|
|database_dtu_consumption_percent|Porcentagem de DTU|Porcentagem|Média|Porcentagem de DTU|DatabaseResourceId|
|storage_percent|Porcentagem de espaço de dados usada|Porcentagem|Média|Porcentagem de espaço de dados usada|Nenhum|
|workers_percent|Porcentagem de funcionários|Porcentagem|Média|Porcentagem de funcionários|Nenhum|
|database_workers_percent|Porcentagem de funcionários|Porcentagem|Média|Porcentagem de funcionários|DatabaseResourceId|
|sessions_percent|Porcentagem de sessões|Porcentagem|Média|Porcentagem de sessões|Nenhum|
|database_sessions_percent|Porcentagem de sessões|Porcentagem|Média|Porcentagem de sessões|DatabaseResourceId|
|eDTU_limit|Limite de eDTU|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|limite de eDTU. Aplica-se a pools elásticos baseados em DTU.|Nenhum|
|storage_limit|Tamanho máximo de dados|Bytes|Média|Tamanho máximo de dados|Nenhum|
|eDTU_used|eDTU usado|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|eDTU usado. Aplica-se a pools elásticos baseados em DTU.|Nenhum|
|database_eDTU_used|eDTU usado|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|eDTU usado|DatabaseResourceId|
|storage_used|Espaço de dados usado|Bytes|Média|Espaço de dados usado|Nenhum|
|database_storage_used|Espaço de dados usado|Bytes|Média|Espaço de dados usado|DatabaseResourceId|
|xtp_storage_percent|Percentual de armazenamento do OLTP na memória|Porcentagem|Média|Percentual de armazenamento do OLTP na memória|Nenhum|
|cpu_limit|Limite de CPU|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Limite de CPU. Aplica-se a pools elásticos baseados em vCore.|Nenhum|
|database_cpu_limit|Limite de CPU|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Limite de CPU|DatabaseResourceId|
|cpu_used|CPU usada|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|CPU usada. Aplica-se a pools elásticos baseados em vCore.|Nenhum|
|database_cpu_used|CPU usada|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|CPU usada|DatabaseResourceId|
|sqlserver_process_core_percent|Porcentagem de núcleo do processo de SQL Server|Porcentagem|Máximo|Uso da CPU como uma porcentagem do processo do BD SQL. Aplica-se a pools elásticos.|Nenhum|
|sqlserver_process_memory_percent|Porcentagem de memória de processo SQL Server|Porcentagem|Máximo|Uso de memória como uma porcentagem do processo do BD SQL. Aplica-se a pools elásticos.|Nenhum|
|tempdb_data_size|Tamanho do arquivo de dados tempdb em kilobytes|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo|Tamanho do arquivo de dados tempdb em kilobytes|Nenhum|
|tempdb_log_size|Tamanho do arquivo de log de tempdb em kilobytes|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo|Tamanho do arquivo de log de tempdb em kilobytes|Nenhum|
|tempdb_log_used_percent|Log de porcentagem de tempdb usado|Porcentagem|Máximo|Log de porcentagem de tempdb usado|Nenhum|
|allocated_data_storage|Espaço de dados alocado|Bytes|Média|Espaço de dados alocado|Nenhum|
|database_allocated_data_storage|Espaço de dados alocado|Bytes|Média|Espaço de dados alocado|DatabaseResourceId|
|allocated_data_storage_percent|Porcentagem alocada de espaço de dados|Porcentagem|Máximo|Porcentagem alocada de espaço de dados|Nenhum|

## <a name="microsoftsqlservers"></a>Microsoft.Sql/servers

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|dtu_consumption_percent|Porcentagem de DTU|Porcentagem|Média|Porcentagem de DTU|ElasticPoolResourceId|
|database_dtu_consumption_percent|Porcentagem de DTU|Porcentagem|Média|Porcentagem de DTU|DatabaseResourceId,ElasticPoolResourceId|
|storage_used|Espaço de dados usado|Bytes|Média|Espaço de dados usado|ElasticPoolResourceId|
|database_storage_used|Espaço de dados usado|Bytes|Média|Espaço de dados usado|DatabaseResourceId,ElasticPoolResourceId|
|dtu_used|DTU usado|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|DTU usado|DatabaseResourceId|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|virtual_core_count|Contagem de núcleos virtuais|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Contagem de núcleos virtuais|Nenhum|
|avg_cpu_percent|Percentual médio de CPU|Porcentagem|Média|Percentual médio de CPU|Nenhum|
|reserved_storage_mb|Espaço de armazenamento reservado|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Espaço de armazenamento reservado|Nenhum|
|storage_space_used_mb|Espaço de armazenamento usado|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Espaço de armazenamento usado|Nenhum|
|io_requests|Contagem de solicitações de E/S|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Contagem de solicitações de E/S|Nenhum|
|io_bytes_read|Bytes de E/S lidos|Bytes|Média|Bytes de E/S lidos|Nenhum|
|io_bytes_written|Bytes de E/S gravados|Bytes|Média|Bytes de E/S gravados|Nenhum|



## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|UsedCapacity|Capacidade utilizada|Bytes|Média|Capacidade utilizada pela conta|Nenhum|
|Transações|Transações|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|ResponseType, GeoType, ApiName, autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|Geotipo, ApiName, autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável.|Geotipo, ApiName, autenticação|
|SuccessServerLatency|Latência de Servidor Com Sucesso|Milissegundos|Média|A latência média usada pelo Armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Esse valor não inclui a latência de rede especificada em AverageE2ELatency.|Geotipo, ApiName, autenticação|
|SuccessE2ELatency|Latência de E2E com Sucesso|Milissegundos|Média|A latência média de ponta a ponta das solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|Geotipo, ApiName, autenticação|
|Availability|Availability|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|Geotipo, ApiName, autenticação|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|BlobCapacity|Capacidade do Blob|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço Blob da conta de armazenamento em bytes.|BlobType, camada|
|BlobCount|Contagem de Blobs|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|O número de Blobs no serviço Blob da conta de armazenamento.|BlobType, camada|
|ContainerCount|Contagem de Contêineres de Blobs|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|O número de contêineres no serviço Blob da conta de armazenamento.|Nenhum|
|IndexCapacity|Capacidade do índice|Bytes|Média|A quantidade de armazenamento usada pelo índice ADLS Gen2 (hierárquico) em bytes.|Nenhum|
|Transações|Transações|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|ResponseType, GeoType, ApiName, autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|Geotipo, ApiName, autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável.|Geotipo, ApiName, autenticação|
|SuccessServerLatency|Latência de Servidor Com Sucesso|Milissegundos|Média|A latência média usada pelo Armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Esse valor não inclui a latência de rede especificada em AverageE2ELatency.|Geotipo, ApiName, autenticação|
|SuccessE2ELatency|Latência de E2E com Sucesso|Milissegundos|Média|A latência média de ponta a ponta das solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|Geotipo, ApiName, autenticação|
|Availability|Availability|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|Geotipo, ApiName, autenticação|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|TableCapacity|Capacidade da Tabela|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço Tabela da conta de armazenamento em bytes.|Nenhum|
|TableCount|Contagem de Tabelas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|O número de tabelas no serviço Tabela da conta de armazenamento.|Nenhum|
|TableEntityCount|Contagem de Entidades de Tabela|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|O número de entidades de tabelas no serviço Tabela da conta de armazenamento.|Nenhum|
|Transações|Transações|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|ResponseType, GeoType, ApiName, autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|Geotipo, ApiName, autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável.|Geotipo, ApiName, autenticação|
|SuccessServerLatency|Latência de Servidor Com Sucesso|Milissegundos|Média|A latência média usada pelo Armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Esse valor não inclui a latência de rede especificada em AverageE2ELatency.|Geotipo, ApiName, autenticação|
|SuccessE2ELatency|Latência de E2E com Sucesso|Milissegundos|Média|A latência média de ponta a ponta das solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|Geotipo, ApiName, autenticação|
|Availability|Availability|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|Geotipo, ApiName, autenticação|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|FileCapacity|Capacidade do Arquivo|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço Arquivo da conta de armazenamento em bytes.|FileShare|
|FileCount|Contagem de Arquivos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|O número de arquivos no serviço Arquivo da conta de armazenamento.|FileShare|
|FileShareCount|Contagem de Compartilhamentos de Arquivos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|O número de compartilhamentos de arquivos no serviço Arquivo da conta de armazenamento.|Nenhum|
|FileShareSnapshotCount|Contagem de instantâneos de compartilhamento de arquivos|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|O número de instantâneos presentes no serviço compartilhamento de arquivos da conta de armazenamento.|FileShare|
|FileShareSnapshotSize|Tamanho do instantâneo de compartilhamento de arquivos|Bytes|Média|A quantidade de armazenamento usada pelos instantâneos no serviço de arquivo da conta de armazenamento, em bytes.|FileShare|
|FileShareQuota|Tamanho da cota de compartilhamento de arquivos|Bytes|Média|O limite superior da quantidade de armazenamento que pode ser usada pelo serviço de arquivos do Azure em bytes.|FileShare|
|Transações|Transações|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|ResponseType, GeoType, ApiName, autenticação, FileShare|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|Geotipo, ApiName, autenticação, FileShare|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável.|Geotipo, ApiName, autenticação, FileShare|
|SuccessServerLatency|Latência de Servidor Com Sucesso|Milissegundos|Média|A latência média usada pelo Armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Esse valor não inclui a latência de rede especificada em AverageE2ELatency.|Geotipo, ApiName, autenticação, FileShare|
|SuccessE2ELatency|Latência de E2E com Sucesso|Milissegundos|Média|A latência média de ponta a ponta das solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|Geotipo, ApiName, autenticação, FileShare|
|Availability|Availability|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|Geotipo, ApiName, autenticação, FileShare|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|QueueCapacity|Capacidade da Fila|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço Fila da conta de armazenamento em bytes.|Nenhum|
|QueueCount|Contagem de Filas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|O número de filas no serviço Fila da conta de armazenamento.|Nenhum|
|QueueMessageCount|Contagem de Mensagens da Fila|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|O número aproximado de mensagens da fila no serviço Fila da conta de armazenamento.|Nenhum|
|Transações|Transações|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|ResponseType, GeoType, ApiName, autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|Geotipo, ApiName, autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável.|Geotipo, ApiName, autenticação|
|SuccessServerLatency|Latência de Servidor Com Sucesso|Milissegundos|Média|A latência média usada pelo Armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Esse valor não inclui a latência de rede especificada em AverageE2ELatency.|Geotipo, ApiName, autenticação|
|SuccessE2ELatency|Latência de E2E com Sucesso|Milissegundos|Média|A latência média de ponta a ponta das solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|Geotipo, ApiName, autenticação|
|Availability|Availability|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|Geotipo, ApiName, autenticação|





## <a name="microsoftstoragecachecaches"></a>Microsoft. StorageCache/caches

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|ClientIOPS|IOPS de cliente total|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|A taxa de operações de arquivos do cliente processadas pelo cache.|Nenhum|
|ClientLatency|Latência média do cliente|Milissegundos|Média|Latência média de operações de arquivo do cliente para o cache de armazenamento.|Nenhum|
|ClientReadIOPS|IOPS de leitura do cliente|CountPerSecond|Média|Operações de leitura de cliente por segundo.|Nenhum|
|ClientReadThroughput|Média de taxa de transferência de leitura do cache|BytesPerSecond|Média|Taxa de transferência de dados de leitura de cliente.|Nenhum|
|ClientWriteIOPS|IOPS de gravação de cliente|CountPerSecond|Média|Operações de gravação de cliente por segundo.|Nenhum|
|ClientWriteThroughput|Taxa de transferência média de gravação no cache|BytesPerSecond|Média|Taxa de transferência de dados de gravação de cliente.|Nenhum|
|ClientMetadataReadIOPS|IOPS de leitura de metadados do cliente|CountPerSecond|Média|A taxa de operações de arquivo do cliente enviadas ao cache, excluindo leituras de dados, que não modificam o estado persistente.|Nenhum|
|ClientMetadataWriteIOPS|IOPS de gravação de metadados do cliente|CountPerSecond|Média|A taxa de operações de arquivo do cliente enviadas ao cache, excluindo gravações de dados, que modificam o estado persistente.|Nenhum|
|ClientLockIOPS|IOPS de bloqueio de cliente|CountPerSecond|Média|Operações de bloqueio de arquivo do cliente por segundo.|Nenhum|
|StorageTargetHealth|Integridade do destino de armazenamento|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Resultados boolianos de teste de conectividade entre o cache e os destinos de armazenamento.|Nenhum|
|Atividade|Atividade|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Resultados boolianos de teste de conectividade entre o cache e o sistema de monitoramento.|Nenhum|
|StorageTargetIOPS|Total de IOPS de StorageTarget|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|A taxa de todas as operações de arquivo que o cache envia para um determinado StorageTarget.|StorageTarget|
|StorageTargetWriteIOPS|IOPS de gravação StorageTarget|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|A taxa das operações de gravação de arquivo que o cache envia para um determinado StorageTarget.|StorageTarget|
|StorageTargetAsyncWriteThroughput|Taxa de transferência de gravação assíncrona StorageTarget|BytesPerSecond|Média|A taxa em que o cache grava dados de forma assíncrona em um determinado StorageTarget. Essas são gravações oportunistas que não fazem com que os clientes bloqueiem.|StorageTarget|
|StorageTargetSyncWriteThroughput|Taxa de transferência de gravação síncrona StorageTarget|BytesPerSecond|Média|A taxa em que o cache grava dados de forma síncrona em um determinado StorageTarget. Essas são gravações que fazem com que os clientes bloqueiem.|StorageTarget|
|StorageTargetTotalWriteThroughput|Taxa de transferência total de gravação de StorageTarget|BytesPerSecond|Média|A taxa total em que o cache grava dados em um determinado StorageTarget.|StorageTarget|
|StorageTargetLatency|Latência de StorageTarget|Milissegundos|Média|A latência média de ida e volta de todas as operações de arquivo que o cache envia para um partricular StorageTarget.|StorageTarget|
|StorageTargetMetadataReadIOPS|IOPS de leitura de metadados StorageTarget|CountPerSecond|Média|A taxa de operações de arquivo que não modificam o estado persistente e excluindo a operação de leitura, que o cache envia para um determinado StorageTarget.|StorageTarget|
|StorageTargetMetadataWriteIOPS|IOPS de gravação de metadados StorageTarget|CountPerSecond|Média|A taxa de operações de arquivo que modificam o estado persistente e excluindo a operação de gravação, que o cache envia para um determinado StorageTarget.|StorageTarget|
|StorageTargetReadIOPS|IOPS de leitura StorageTarget|CountPerSecond|Média|A taxa de operações de leitura de arquivo que o cache envia para um determinado StorageTarget.|StorageTarget|
|StorageTargetReadAheadThroughput|Taxa de transferência de leitura antecipada de StorageTarget|BytesPerSecond|Média|A taxa em que o cache lê de forma oportunista os dados do StorageTarget.|StorageTarget|
|StorageTargetFillThroughput|Taxa de transferência de preenchimento do StorageTarget|BytesPerSecond|Média|A taxa em que o cache lê os dados do StorageTarget para lidar com um erro de cache.|StorageTarget|
|StorageTargetTotalReadThroughput|Taxa de transferência de leitura total de StorageTarget|BytesPerSecond|Média|A taxa total em que o cache lê dados de um determinado StorageTarget.|StorageTarget|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Resultado da sessão de sincronização|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Métrica que registra um valor de 1 cada vez que o ponto de extremidade do servidor conclui com êxito uma sessão de sincronização com o ponto de extremidade de nuvem|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Arquivos sincronizados|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Contagem de arquivos sincronizados|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Arquivos não sincronizando|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Falha na sincronização da contagem de arquivos|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncBatchTransferredFileBytes|Bytes sincronizados|Bytes|Total|Tamanho total do arquivo transferido para sessões de sincronização|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncServerHeartbeat|Status online do servidor|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo|Métrica que registra um valor de 1 cada vez que o servidor resigtered registra com êxito uma pulsação com o ponto de extremidade de nuvem|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Recall da camada de nuvem|Bytes|Total|Tamanho total dos dados recuperados pelo servidor|ServerName|
|StorageSyncRecalledTotalNetworkBytes|Tamanho de recall em camadas de nuvem|Bytes|Total|Tamanho dos dados em recall|SyncGroupName, servername|
|StorageSyncRecallThroughputBytesPerSecond|Taxa de transferência de recall em camadas de nuvem|BytesPerSecond|Média|Tamanho da taxa de transferência de recall de dados|SyncGroupName, servername|
|StorageSyncRecalledNetworkBytesByApplication|Tamanho de recall de camadas de nuvem por aplicativo|Bytes|Total|Tamanho dos dados recuperados pelo aplicativo|SyncGroupName, servername, ApplicationName|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>Microsoft. storagesync/storageSyncServices/syncGroups

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|SyncGroupSyncSessionAppliedFilesCount|Arquivos sincronizados|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Contagem de arquivos sincronizados|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|Arquivos não sincronizando|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Falha na sincronização da contagem de arquivos|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncGroupBatchTransferredFileBytes|Bytes sincronizados|Bytes|Total|Tamanho total do arquivo transferido para sessões de sincronização|SyncGroupName,ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|ServerEndpointSyncSessionAppliedFilesCount|Arquivos sincronizados|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Contagem de arquivos sincronizados|ServerEndpointName,SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|Arquivos não sincronizando|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Falha na sincronização da contagem de arquivos|ServerEndpointName,SyncDirection|
|ServerEndpointBatchTransferredFileBytes|Bytes sincronizados|Bytes|Total|Tamanho total do arquivo transferido para sessões de sincronização|ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>Microsoft. storagesync/storageSyncServices/registeredServers

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|ServerHeartbeat|Status online do servidor|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo|Métrica que registra um valor de 1 cada vez que o servidor resigtered registra com êxito uma pulsação com o ponto de extremidade de nuvem|ServerResourceId, servername|
|ServerRecallIOTotalSizeBytes|Recall da camada de nuvem|Bytes|Total|Tamanho total dos dados recuperados pelo servidor|ServerResourceId, servername|



## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|ResourceUtilization|% de utilização do SU|Porcentagem|Máximo|% de utilização do SU|LogicalName, PartitionID|
|InputEvents|Eventos de entrada|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Eventos de entrada|LogicalName, PartitionID|
|InputEventBytes|Bytes de evento de entrada|Bytes|Total|Bytes de evento de entrada|LogicalName, PartitionID|
|LateInputEvents|Eventos de entrada atrasados|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Eventos de entrada atrasados|LogicalName, PartitionID|
|OutputEvents|Eventos de saída|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Eventos de saída|LogicalName, PartitionID|
|ConversionErrors|Erros de conversão de dados|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Erros de conversão de dados|LogicalName, PartitionID|
|Erros|Erros de runtime|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Erros de runtime|LogicalName, PartitionID|
|DroppedOrAdjustedEvents|Eventos fora de ordem|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Eventos fora de ordem|LogicalName, PartitionID|
|AMLCalloutRequests|Solicitações de função|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Solicitações de função|LogicalName, PartitionID|
|AMLCalloutFailedRequests|Solicitações de função com falha|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Solicitações de função com falha|LogicalName, PartitionID|
|AMLCalloutInputEvents|Eventos de função|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Eventos de função|LogicalName, PartitionID|
|DeserializationError|Erros de desserialização de entrada|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Erros de desserialização de entrada|LogicalName, PartitionID|
|EarlyInputEvents|Eventos de Entrada Antecipados|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Eventos de Entrada Antecipados|LogicalName, PartitionID|
|OutputWatermarkDelaySeconds|Atraso de Marca-d'água|Segundos|Máximo|Atraso de Marca-d'água|LogicalName, PartitionID|
|InputEventsSourcesBacklogged|Eventos de Entrada Acumulados|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo|Eventos de Entrada Acumulados|LogicalName, PartitionID|
|InputEventsSourcesPerSecond|Fontes de Entrada Recebidas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Fontes de Entrada Recebidas|LogicalName, PartitionID|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|IngressReceivedMessages|Mensagens de entrada recebidas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Contagem de mensagens lidas de todos os Hubs de Eventos ou origens de evento do Hub IoT|Nenhum|
|IngressReceivedInvalidMessages|Mensagens de entrada inválidas recebidas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Contagem de mensagens inválidas lidas de todos os Hubs de Eventos ou origens de evento do Hub IoT|Nenhum|
|IngressReceivedBytes|Bytes de entrada recebidos|Bytes|Total|Contagem de bytes lidos de todas as fontes de evento|Nenhum|
|IngressStoredBytes|Bytes de entrada armazenados|Bytes|Total|Tamanho total de eventos processados com êxito e disponíveis para consulta|Nenhum|
|IngressStoredEvents|Eventos de entrada armazenados|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Contagem de eventos nivelados processados com êxito e disponíveis para consulta|Nenhum|
|IngressReceivedMessagesTimeLag|Tempo de atraso das mensagens de entrada recebidas|Segundos|Máximo|Diferença entre a hora em que a mensagem é enfileirada na origem do evento e a hora em que ela é processada na entrada|Nenhum|
|IngressReceivedMessagesCountLag|Atraso na contagem das mensagens de entrada recebidas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Diferença entre o número de sequência da última mensagem enfileirada na partição de origem do evento e o número de sequência de mensagens sendo processadas na entrada|Nenhum|
|WarmStorageMaxProperties|Propriedades máximas de armazenamento quente|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo|Número máximo de propriedades usadas permitidas pelo ambiente para SKU S1/S2 e número máximo de propriedades permitidas pelo armazenamento quente para o SKU PAYG|Nenhum|
|WarmStorageUsedProperties|Propriedades usadas de armazenamento quente |{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo|Número de propriedades usadas pelo ambiente para SKU S1/S2 e número de propriedades usadas pelo armazenamento quente para SKU PAYG|Nenhum|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|IngressReceivedMessages|Mensagens de entrada recebidas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Contagem de mensagens lidas na origem do evento|Nenhum|
|IngressReceivedInvalidMessages|Mensagens de entrada inválidas recebidas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Contagem de mensagens inválidas lidas na origem do evento|Nenhum|
|IngressReceivedBytes|Bytes de entrada recebidos|Bytes|Total|Contagem de bytes lidos da fonte de evento|Nenhum|
|IngressStoredBytes|Bytes de entrada armazenados|Bytes|Total|Tamanho total de eventos processados com êxito e disponíveis para consulta|Nenhum|
|IngressStoredEvents|Eventos de entrada armazenados|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Contagem de eventos nivelados processados com êxito e disponíveis para consulta|Nenhum|
|IngressReceivedMessagesTimeLag|Tempo de atraso das mensagens de entrada recebidas|Segundos|Máximo|Diferença entre a hora em que a mensagem é enfileirada na origem do evento e a hora em que ela é processada na entrada|Nenhum|
|IngressReceivedMessagesCountLag|Atraso na contagem das mensagens de entrada recebidas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Diferença entre o número de sequência da última mensagem enfileirada na partição de origem do evento e o número de sequência de mensagens sendo processadas na entrada|Nenhum|
|WarmStorageMaxProperties|Propriedades máximas de armazenamento quente|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo|Número máximo de propriedades usadas permitidas pelo ambiente para SKU S1/S2 e número máximo de propriedades permitidas pelo armazenamento quente para o SKU PAYG|Nenhum|
|WarmStorageUsedProperties|Propriedades usadas de armazenamento quente |{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Máximo|Número de propriedades usadas pelo ambiente para SKU S1/S2 e número de propriedades usadas pelo armazenamento quente para SKU PAYG|Nenhum|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Disk Read Bytes/Sec|BytesPerSecond|Média|Taxa de transferência média do disco devido a operações de leitura no período de exemplo.|Nenhum|
|DiskWriteBytesPerSecond|Disk Write Bytes/Sec|BytesPerSecond|Média|Taxa de transferência média do disco devido a operações de gravação no período de exemplo.|Nenhum|
|Bytes de leitura de disco|Bytes de leitura de disco|Bytes|Total|Taxa de transferência total do disco devido a operações de leitura no período de exemplo.|Nenhum|
|Bytes de gravação de disco|Bytes de gravação de disco|Bytes|Total|Taxa de transferência total do disco devido a operações de gravação no período de exemplo.|Nenhum|
|DiskReadOperations|Operações de leitura de disco|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|O número de operações de leitura de e/s no período de exemplo anterior. Observe que essas operações podem ser de tamanho variável.|Nenhum|
|DiskWriteOperations|Operações de gravação de disco|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|O número de operações de gravação de e/s no período de exemplo anterior. Observe que essas operações podem ser de tamanho variável.|Nenhum|
|Operações de leitura de disco/Seg|Operações de leitura de disco/Seg|CountPerSecond|Média|O número médio de operações de leitura de e/s no período de exemplo anterior. Observe que essas operações podem ser de tamanho variável.|Nenhum|
|Operações de gravação de disco/Seg|Operações de gravação de disco/Seg|CountPerSecond|Média|O número médio de operações de gravação de e/s no período de exemplo anterior. Observe que essas operações podem ser de tamanho variável.|Nenhum|
|DiskReadLatency|Latência de leitura de disco|Milissegundos|Média|Latência de leitura total. A soma das latências de leitura do dispositivo e do kernel.|Nenhum|
|DiskWriteLatency|Latência de Gravação de disco|Milissegundos|Média|Latência de gravação total. A soma das latências de gravação do kernel e do dispositivo.|Nenhum|
|NetworkInBytesPerSecond|Rede em bytes/s|BytesPerSecond|Média|Taxa de transferência média de rede para o tráfego recebido.|Nenhum|
|NetworkOutBytesPerSecond|Bytes de saída de rede/s|BytesPerSecond|Média|Taxa de transferência média de rede para o tráfego transmitido.|Nenhum|
|Entrada na rede|Entrada na rede|Bytes|Total|Taxa de transferência total de rede para o tráfego recebido.|Nenhum|
|Saída da rede|Saída da rede|Bytes|Total|Taxa de transferência total de rede para o tráfego transmitido.|Nenhum|
|MemoryUsed|Memória usada|Bytes|Média|A quantidade de memória da máquina que está sendo usada pela VM.|Nenhum|
|MemoryGranted|Memória concedida|Bytes|Média|A quantidade de memória que foi concedida à VM pelo host. A memória não é concedida ao host até que ele seja tocado uma vez e a memória concedida possa ser trocada ou bloqueada se o VMkernel precisar da memória.|Nenhum|
|MemoryActive|Memória ativa|Bytes|Média|A quantidade de memória usada pela VM na última pequena janela de tempo. Esse é o número "verdadeiro" da quantidade de memória necessária para a VM no momento. A memória adicional não utilizada pode ser trocada ou por balão sem impacto no desempenho do convidado.|Nenhum|
|Porcentagem de CPU|Porcentagem de CPU|Porcentagem|Média|A utilização da CPU. Esse valor é relatado com 100% que representa todos os núcleos de processador no sistema. Por exemplo, uma VM de 2 vias usando 50% de um sistema de quatro núcleos está usando completamente dois núcleos.|Nenhum|
|PercentageCpuReady|Percentual de CPU pronta|Milissegundos|Total|O tempo de preparação é o tempo gasto aguardando que as CPUs fiquem disponíveis no intervalo de atualização anterior.|Nenhum|










## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|CpuPercentage|Percentual de CPU|Porcentagem|Média|Percentual de CPU|Instância|
|MemoryPercentage|Porcentagem de Memória|Porcentagem|Média|Porcentagem de Memória|Instância|
|DiskQueueLength|Comprimento da fila de disco|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Comprimento da fila de disco|Instância|
|HttpQueueLength|Tamanho da Fila de Http|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Tamanho da Fila de Http|Instância|
|BytesReceived|Entrada de Dados|Bytes|Total|Entrada de Dados|Instância|
|BytesSent|Saída de dados|Bytes|Total|Saída de dados|Instância|
|TcpSynSent|SYN TCP enviado|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|SYN TCP enviado|Instância|
|TcpSynReceived|SYN TCP recebido|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|SYN TCP recebido|Instância|
|TcpEstablished|TCP estabelecido|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|TCP estabelecido|Instância|
|TcpFinWait1|Espera de TCP fin 1|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Espera de TCP fin 1|Instância|
|TcpFinWait2|Espera de TCP Fin 2|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Espera de TCP Fin 2|Instância|
|TcpClosing|Fechamento de TCP|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Fechamento de TCP|Instância|
|TcpCloseWait|Espera TCP de fechamento|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Espera TCP de fechamento|Instância|
|TcpLastAck|Última confirmação TCP|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Última confirmação TCP|Instância|
|TcpTimeWait|Espera de tempo TCP|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Espera de tempo TCP|Instância|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (excluindo funções)

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|CpuTime|Tempo de CPU|Segundos|Total|Tempo de CPU|Instância|
|Solicitações|Solicitações|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Solicitações|Instância|
|BytesReceived|Entrada de Dados|Bytes|Total|Entrada de Dados|Instância|
|BytesSent|Saída de dados|Bytes|Total|Saída de dados|Instância|
|Http101|Http 101|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Http 101|Instância|
|Http2xx|Http 2xx|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Http 2xx|Instância|
|Http3xx|Http 3xx|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Http 3xx|Instância|
|Http401|Http 401|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Http 401|Instância|
|Http403|Http 403|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Http 403|Instância|
|Http404|Http 404|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Http 404|Instância|
|Http406|Http 406|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Http 406|Instância|
|Http4xx|Http 4xx|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Http 4xx|Instância|
|Http5xx|Erros do Servidor Http|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Erros do Servidor Http|Instância|
|MemoryWorkingSet|Conjunto de trabalho de memória|Bytes|Média|Conjunto de trabalho de memória|Instância|
|AverageMemoryWorkingSet|Conjunto de trabalho de memória média|Bytes|Média|Conjunto de trabalho de memória média|Instância|
|AverageResponseTime|Tempo Médio de Resposta|Segundos|Média|Tempo Médio de Resposta|Instância|
|AppConnections|Conexões|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Conexões|Instância|
|Alças|Contagem de Identificadores|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Contagem de Identificadores|Instância|
|Threads|Contagem de Threads|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Contagem de Threads|Instância|
|PrivateBytes|Bytes Particulares|Bytes|Média|Bytes Particulares|Instância|
|IoReadBytesPerSecond|E/S de bytes de leitura por segundo|BytesPerSecond|Total|E/S de bytes de leitura por segundo|Instância|
|IoWriteBytesPerSecond|E/S de bytes de gravação por segundo|BytesPerSecond|Total|E/S de bytes de gravação por segundo|Instância|
|IoOtherBytesPerSecond|E/S de outros bytes por segundo|BytesPerSecond|Total|E/S de outros bytes por segundo|Instância|
|IoReadOperationsPerSecond|E/S de operações de leitura por segundo|BytesPerSecond|Total|E/S de operações de leitura por segundo|Instância|
|IoWriteOperationsPerSecond|E/S de operações de gravação por segundo|BytesPerSecond|Total|E/S de operações de gravação por segundo|Instância|
|IoOtherOperationsPerSecond|E/S de outras operações por segundo|BytesPerSecond|Total|E/S de outras operações por segundo|Instância|
|RequestsInApplicationQueue|Solicitações na fila do aplicativo|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Solicitações na fila do aplicativo|Instância|
|CurrentAssemblies|Assemblies Atuais|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Assemblies Atuais|Instância|
|TotalAppDomains|Total de domínios de aplicativo|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Total de domínios de aplicativo|Instância|
|TotalAppDomainsUnloaded|Total de domínios de aplicativo descarregados|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Total de domínios de aplicativo descarregados|Instância|
|Gen0Collections|Coletas de lixo da Ger 0|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Coletas de lixo da Ger 0|Instância|
|Gen1Collections|Coletas de lixo da Ger 1|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Coletas de lixo da Ger 1|Instância|
|Gen2Collections|Coletas de lixo da Ger 2|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Coletas de lixo da Ger 2|Instância|
|HealthCheckStatus|Status da verificação de integridade|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Status da verificação de integridade|Instância|
|FileSystemUsage|Uso do sistema de arquivos|Bytes|Média|Uso do sistema de arquivos|Nenhum|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (funções)

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|BytesReceived|Entrada de Dados|Bytes|Total|Entrada de Dados|Instância|
|BytesSent|Saída de dados|Bytes|Total|Saída de dados|Instância|
|Http5xx|Erros do Servidor Http|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Erros do Servidor Http|Instância|
|MemoryWorkingSet|Conjunto de trabalho de memória|Bytes|Média|Conjunto de trabalho de memória|Instância|
|AverageMemoryWorkingSet|Conjunto de trabalho de memória média|Bytes|Média|Conjunto de trabalho de memória média|Instância|
|FunctionExecutionUnits|Unidades de Execução de Função|MB/milissegundos|Total|[Unidades de execução de função](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Instância|
|FunctionExecutionCount|Contagem de Execução de Função|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Contagem de Execução de Função|Instância|
|PrivateBytes|Bytes Particulares|Bytes|Média|Bytes Particulares|Instância|
|IoReadBytesPerSecond|E/S de bytes de leitura por segundo|BytesPerSecond|Total|E/S de bytes de leitura por segundo|Instância|
|IoWriteBytesPerSecond|E/S de bytes de gravação por segundo|BytesPerSecond|Total|E/S de bytes de gravação por segundo|Instância|
|IoOtherBytesPerSecond|E/S de outros bytes por segundo|BytesPerSecond|Total|E/S de outros bytes por segundo|Instância|
|IoReadOperationsPerSecond|E/S de operações de leitura por segundo|BytesPerSecond|Total|E/S de operações de leitura por segundo|Instância|
|IoWriteOperationsPerSecond|E/S de operações de gravação por segundo|BytesPerSecond|Total|E/S de operações de gravação por segundo|Instância|
|IoOtherOperationsPerSecond|E/S de outras operações por segundo|BytesPerSecond|Total|E/S de outras operações por segundo|Instância|
|RequestsInApplicationQueue|Solicitações na fila do aplicativo|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Solicitações na fila do aplicativo|Instância|
|CurrentAssemblies|Assemblies Atuais|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Assemblies Atuais|Instância|
|TotalAppDomains|Total de domínios de aplicativo|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Total de domínios de aplicativo|Instância|
|TotalAppDomainsUnloaded|Total de domínios de aplicativo descarregados|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Total de domínios de aplicativo descarregados|Instância|
|Gen0Collections|Coletas de lixo da Ger 0|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Coletas de lixo da Ger 0|Instância|
|Gen1Collections|Coletas de lixo da Ger 1|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Coletas de lixo da Ger 1|Instância|
|Gen2Collections|Coletas de lixo da Ger 2|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Coletas de lixo da Ger 2|Instância|
|HealthCheckStatus|Status da verificação de integridade|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Status da verificação de integridade|Instância|
|FileSystemUsage|Uso do sistema de arquivos|Bytes|Média|Uso do sistema de arquivos|Nenhum|


## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|CpuTime|Tempo de CPU|Segundos|Total|Tempo de CPU|Instância|
|Solicitações|Solicitações|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Solicitações|Instância|
|BytesReceived|Entrada de Dados|Bytes|Total|Entrada de Dados|Instância|
|BytesSent|Saída de dados|Bytes|Total|Saída de dados|Instância|
|Http101|Http 101|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Http 101|Instância|
|Http2xx|Http 2xx|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Http 2xx|Instância|
|Http3xx|Http 3xx|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Http 3xx|Instância|
|Http401|Http 401|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Http 401|Instância|
|Http403|Http 403|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Http 403|Instância|
|Http404|Http 404|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Http 404|Instância|
|Http406|Http 406|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Http 406|Instância|
|Http4xx|Http 4xx|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Http 4xx|Instância|
|Http5xx|Erros do Servidor Http|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Erros do Servidor Http|Instância|
|MemoryWorkingSet|Conjunto de trabalho de memória|Bytes|Média|Conjunto de trabalho de memória|Instância|
|AverageMemoryWorkingSet|Conjunto de trabalho de memória média|Bytes|Média|Conjunto de trabalho de memória média|Instância|
|AverageResponseTime|Tempo Médio de Resposta|Segundos|Média|Tempo Médio de Resposta|Instância|
|HttpResponsetime|Tempo de resposta|Segundos|Média|Tempo de resposta|Instância|
|FunctionExecutionUnits|Unidades de Execução de Função|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Unidades de Execução de Função|Instância|
|FunctionExecutionCount|Contagem de Execução de Função|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Contagem de Execução de Função|Instância|
|AppConnections|Conexões|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Conexões|Instância|
|Alças|Contagem de Identificadores|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Contagem de Identificadores|Instância|
|Threads|Contagem de Threads|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Contagem de Threads|Instância|
|PrivateBytes|Bytes Particulares|Bytes|Média|Bytes Particulares|Instância|
|IoReadBytesPerSecond|E/S de bytes de leitura por segundo|BytesPerSecond|Total|E/S de bytes de leitura por segundo|Instância|
|IoWriteBytesPerSecond|E/S de bytes de gravação por segundo|BytesPerSecond|Total|E/S de bytes de gravação por segundo|Instância|
|IoOtherBytesPerSecond|E/S de outros bytes por segundo|BytesPerSecond|Total|E/S de outros bytes por segundo|Instância|
|IoReadOperationsPerSecond|E/S de operações de leitura por segundo|BytesPerSecond|Total|E/S de operações de leitura por segundo|Instância|
|IoWriteOperationsPerSecond|E/S de operações de gravação por segundo|BytesPerSecond|Total|E/S de operações de gravação por segundo|Instância|
|IoOtherOperationsPerSecond|E/S de outras operações por segundo|BytesPerSecond|Total|E/S de outras operações por segundo|Instância|
|RequestsInApplicationQueue|Solicitações na fila do aplicativo|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Solicitações na fila do aplicativo|Instância|
|CurrentAssemblies|Assemblies Atuais|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Assemblies Atuais|Instância|
|TotalAppDomains|Total de domínios de aplicativo|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Total de domínios de aplicativo|Instância|
|TotalAppDomainsUnloaded|Total de domínios de aplicativo descarregados|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Total de domínios de aplicativo descarregados|Instância|
|Gen0Collections|Coletas de lixo da Ger 0|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Coletas de lixo da Ger 0|Instância|
|Gen1Collections|Coletas de lixo da Ger 1|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Coletas de lixo da Ger 1|Instância|
|Gen2Collections|Coletas de lixo da Ger 2|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Coletas de lixo da Ger 2|Instância|
|HealthCheckStatus|Status da verificação de integridade|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Status da verificação de integridade|Instância|
|FileSystemUsage|Uso do sistema de arquivos|Bytes|Média|Uso do sistema de arquivos|Nenhum|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|Solicitações|Solicitações|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Solicitações|Instância|
|BytesReceived|Entrada de Dados|Bytes|Total|Entrada de Dados|Instância|
|BytesSent|Saída de dados|Bytes|Total|Saída de dados|Instância|
|Http101|Http 101|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Http 101|Instância|
|Http2xx|Http 2xx|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Http 2xx|Instância|
|Http3xx|Http 3xx|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Http 3xx|Instância|
|Http401|Http 401|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Http 401|Instância|
|Http403|Http 403|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Http 403|Instância|
|Http404|Http 404|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Http 404|Instância|
|Http406|Http 406|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Http 406|Instância|
|Http4xx|Http 4xx|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Http 4xx|Instância|
|Http5xx|Erros do Servidor Http|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Erros do Servidor Http|Instância|
|AverageResponseTime|Tempo Médio de Resposta|Segundos|Média|Tempo Médio de Resposta|Instância|
|CpuPercentage|Percentual de CPU|Porcentagem|Média|Percentual de CPU|Instância|
|MemoryPercentage|Porcentagem de Memória|Porcentagem|Média|Porcentagem de Memória|Instância|
|DiskQueueLength|Comprimento da fila de disco|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Comprimento da fila de disco|Instância|
|HttpQueueLength|Tamanho da Fila de Http|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Tamanho da Fila de Http|Instância|
|ActiveRequests|Solicitações ativas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Total|Solicitações ativas|Instância|
|TotalFrontEnds|Total de front-ends|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Total de front-ends|Nenhum|
|SmallAppServicePlanInstances|Funções de trabalho pequenas do Plano do Serviço de Aplicativo|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Funções de trabalho pequenas do Plano do Serviço de Aplicativo|Nenhum|
|MediumAppServicePlanInstances|Funções de trabalho médias do Plano do Serviço de Aplicativo|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Funções de trabalho médias do Plano do Serviço de Aplicativo|Nenhum|
|LargeAppServicePlanInstances|Funções de trabalho grandes do Plano do Serviço de Aplicativo|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Funções de trabalho grandes do Plano do Serviço de Aplicativo|Nenhum|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensions|
|---|---|---|---|---|---|
|WorkersTotal|Total de funções de trabalho|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Total de funções de trabalho|Nenhum|
|WorkersAvailable|Funções de trabalho disponíveis|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Funções de trabalho disponíveis|Nenhum|
|WorkersUsed|Funções de trabalho usadas|{1&gt;{2&gt;Contagem&lt;2}&lt;1}|Média|Funções de trabalho usadas|Nenhum|
|CpuPercentage|Percentual de CPU|Porcentagem|Média|Percentual de CPU|Instância|
|MemoryPercentage|Porcentagem de Memória|Porcentagem|Média|Porcentagem de Memória|Instância|
## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}
* [Leia sobre as métricas no Azure Monitor](data-platform.md)
* [Criar alertas para métricas](alerts-overview.md)
* [Exportar as métricas de armazenamento, Hub de eventos ou Log Analytics](platform-logs-overview.md)
