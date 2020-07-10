---
title: Métricas compatíveis por tipo de recurso do Azure Monitor
description: Lista de métricas disponíveis para cada tipo de recurso com o Azure Monitor.
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 06/16/2020
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: ffdfeacad2fcfa7f77f3bcb55e8b1edaea865202
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86145167"
---
# <a name="supported-metrics-with-azure-monitor"></a>Métricas compatíveis com o Azure Monitor

> [!NOTE]
> Essa lista é amplamente gerada automaticamente a partir da API REST de métricas Azure Monitor. Qualquer modificação feita nessa lista por meio do GitHub pode ser gravada sem aviso. Contate o autor deste artigo para obter detalhes sobre como fazer atualizações permanentes.

O Azure Monitor fornece várias maneiras de interagir com as métricas, incluindo a criação de gráficos para os mesmos no portal, acessá-las por meio da API REST ou consultá-las usando o PowerShell ou a CLI. 

Este artigo é uma lista completa de todas as métricas de plataforma (ou seja, coletadas automaticamente) atualmente disponíveis com o pipeline de métrica consolidado do Azure Monitor. A lista foi atualizada pela última vez em 27 de março de 2020. As métricas alteradas ou adicionadas após essa data podem não aparecer abaixo. Para consultar e acessar a lista de métricas programaticamente, use a [versão de api 2018-01-01](https://docs.microsoft.com/rest/api/monitor/metricdefinitions). Outras métricas que não estão nessa lista podem estar disponíveis no portal ou usando APIs herdadas.

As métricas são organizadas por provedores de recursos e tipo de recurso. Para obter uma lista de serviços e os provedores de recursos que pertencem a eles, consulte [provedores de recursos para serviços do Azure](../../azure-resource-manager/management/azure-services-resource-providers.md). 


## <a name="guest-os-metrics"></a>Métricas do SO convidado

As métricas para o sistema operacional convidado (SO convidado) que é executado em máquinas virtuais do Azure, Service Fabric e serviços de nuvem **não** estão listadas aqui. Em vez disso, as métricas de desempenho do SO convidado devem ser coletadas por meio de um ou mais agentes que são executados no ou como parte do sistema operacional convidado.  As métricas do sistema operacional convidado incluem contadores de desempenho que acompanham o percentual de CPU ou o uso de memória do convidado, que são frequentemente usados para o dimensionamento automático ou o alerta.  Usando a [extensão diagnóstico do Azure](diagnostics-extension-overview.md), você pode enviar métricas de desempenho do sistema operacional convidado para o mesmo banco de dados em que as métricas de plataforma são armazenadas. Ele roteia as métricas do sistema operacional convidado por meio da API de [métricas personalizada](metrics-custom-overview.md) . Em seguida, você pode enviar um gráfico, alertar e, de outra forma, usar métricas de SO convidado como métricas de plataforma Para obter mais informações, consulte [visão geral dos agentes de monitoramento](agents-overview.md).    

## <a name="routing-platform-metrics-to-other-locations"></a>Métricas da plataforma de roteamento para outros locais

Você pode usar [as configurações de diagnóstico](diagnostic-settings.md) para rotear métricas de plataforma para o armazenamento do Azure, Azure monitor logs (e, portanto, log Analytics) e hubs de eventos.  

Há algumas limitações no que pode ser roteado e no formulário em que eles são armazenados. 
- Nem todas as métricas são exportáveis para outros locais. Para obter uma lista de métricas de plataforma exportáveis por meio de configurações de diagnóstico, consulte [Este artigo](metrics-supported-export-diagnostic-settings.md).

- No momento, não há suporte para o envio de métricas multidimensionais para outros locais por meio de configurações de diagnóstico. As métricas com dimensões são exportadas como métricas dimensionais simples, agregadas nos valores da dimensão.
*Por exemplo*: a métrica “Mensagens de Entrada” em um Hub de Eventos pode ser explorada e mapeada por nível da fila. No entanto, quando exportada por meio das configurações de diagnóstico, a métrica será representada como todas as mensagens de entrada em todas as filas no Hub de Eventos.


## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|qpu_metric|QPU|Contagem|Média|QPU. Intervalo de 0 a 100 para S1, 0 a 200 para S2 e 0 a 400 para S4|ServerResourceType|
|memory_metric|Memória|Bytes|Média|Memória. Intervalo de 0 a 25 GB para S1, 0 a 50 GB para S2 e 0 a 100 GB para S4|ServerResourceType|
|private_bytes_metric|Bytes Particulares|Bytes|Média|Bytes privados.|ServerResourceType|
|virtual_bytes_metric|Bytes Virtuais|Bytes|Média|Bytes virtuais.|ServerResourceType|
|TotalConnectionRequests|Solicitações de conexão totais|Contagem|Média|Solicitações de conexão totais. Estas são chegadas.|ServerResourceType|
|SuccessfullConnectionsPerSec|Conexões bem-sucedidas por segundo|CountPerSecond|Média|Taxa de conclusões de conexão bem-sucedidas.|ServerResourceType|
|TotalConnectionFailures|Falhas de conexão totais|Contagem|Média|Total de falhas em tentativas de conexão.|ServerResourceType|
|CurrentUserSessions|Sessões de usuário atuais|Contagem|Média|Número atual de sessões de usuário estabelecidas.|ServerResourceType|
|QueryPoolBusyThreads|Threads ocupados do pool de consulta|Contagem|Média|Número de threads ocupados no pool de threads de consulta.|ServerResourceType|
|CommandPoolJobQueueLength|Comprimento da fila de trabalho do pool de comando|Contagem|Média|Número de trabalhos na fila do pool de threads de comando.|ServerResourceType|
|ProcessingPoolJobQueueLength|Comprimento da fila de trabalho do pool de processamento|Contagem|Média|Número de trabalhos não de E/S na fila do pool de threads de processamento.|ServerResourceType|
|CurrentConnections|Conexão: Conexões atuais|Contagem|Média|Número atual de conexões de cliente estabelecidas.|ServerResourceType|
|CleanerCurrentPrice|Memória: Preço atual do limpador|Contagem|Média|Preço atual da memória, $/byte/tempo, normalizado em 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Memória: Memória do limpador reduzível|Bytes|Média|Quantidade de memória, em bytes, sujeita a eliminação pelo limpador na tela de fundo.|ServerResourceType|
|CleanerMemoryNonshrinkable|Memória: Memória do limpador não reduzível|Bytes|Média|Quantidade de memória, em bytes, não sujeita a eliminação pelo limpador na tela de fundo.|ServerResourceType|
|MemoryUsage|Memória: Uso de Memória|Bytes|Média|Uso de memória do processo do servidor, como usado no cálculo de preço de memória do limpador. Igual ao contador Processo\PrivateBytes mais o tamanho dos dados mapeados em memória, ignorando qualquer memória mapeada ou alocada pelo mecanismo de análise de memória xVelocity (VertiPaq), além do limite de memória do mecanismo xVelocity.|ServerResourceType|
|MemoryLimitHard|Memória: Limite de memória física|Bytes|Média|Limite de memória física, do arquivo de configuração.|ServerResourceType|
|MemoryLimitHigh|Memória: Limite de memória superior|Bytes|Média|Limite de memória superior, do arquivo de configuração.|ServerResourceType|
|MemoryLimitLow|Memória: Limite de memória inferior|Bytes|Média|Limite de memória inferior, do arquivo de configuração.|ServerResourceType|
|MemoryLimitVertiPaq|Memória: Limite de memória VertiPaq|Bytes|Média|Limite na memória, do arquivo de configuração.|ServerResourceType|
|Quota|Memória: Quota|Bytes|Média|Cota de memória atual, em bytes. A cota de memória também é conhecida como uma reserva de memória ou concessão de memória.|ServerResourceType|
|QuotaBlocked|Memória: Cota bloqueada|Contagem|Média|Número atual de solicitações de cota bloqueadas até que outras cotas de memória sejam liberadas.|ServerResourceType|
|VertiPaqNonpaged|Memória: VertiPaq não paginado|Bytes|Média|Bytes de memória bloqueada no conjunto de trabalho para uso pelo mecanismo na memória.|ServerResourceType|
|VertiPaqPaged|Memória: VertiPaq paginado|Bytes|Média|Bytes de memória paginada em uso para dados na memória.|ServerResourceType|
|RowsReadPerSec|Processamento: Linhas lidas por segundo|CountPerSecond|Média|Taxa de linhas lidas de todos os bancos de dados relacionais.|ServerResourceType|
|RowsConvertedPerSec|Processamento: Linhas convertidas por segundo|CountPerSecond|Média|Taxa de linhas convertidas durante o processamento.|ServerResourceType|
|RowsWrittenPerSec|Processamento: Linhas gravadas por segundo|CountPerSecond|Média|Taxa de linhas gravadas durante o processamento.|ServerResourceType|
|CommandPoolBusyThreads|Threads: Threads ocupados do pool comando|Contagem|Média|Número de threads ocupados no pool de threads de comando.|ServerResourceType|
|CommandPoolIdleThreads|Threads: Threads ociosos do pool comando|Contagem|Média|Número de threads ociosos no pool de threads de comando.|ServerResourceType|
|LongParsingBusyThreads|Threads: Threads ocupados de análise longa|Contagem|Média|Número de threads ocupados no pool de threads de análise longa.|ServerResourceType|
|LongParsingIdleThreads|Threads: Threads ociosos de análise longa|Contagem|Média|Número de threads ociosos no pool de threads de análise longa.|ServerResourceType|
|LongParsingJobQueueLength|Threads: Tamanho da fila de trabalhos de análise longa|Contagem|Média|Número de trabalhos na fila do pool de threads de análise longa.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Threads: Threads de trabalho de E/S ocupados do pool de processamento|Contagem|Média|Número de threads que executam trabalhos de E/S no pool de threads de processamento.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Threads: Threads de trabalho não E/S ocupados do pool de processamento|Contagem|Média|Número de threads que executam trabalhos não E/S no pool de threads de processamento.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Threads: Comprimento da fila de trabalho de E/S do pool de processamento|Contagem|Média|Número de trabalhos de E/S na fila do pool de threads de processamento.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Threads: Threads de trabalho de E/S ociosos do pool de processamento|Contagem|Média|Número de threads ociosos para trabalhos de E/S no pool de threads de processamento.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Threads: Threads de trabalho não E/S ociosos do pool de processamento|Contagem|Média|Número de threads ociosos no pool de threads de processamento dedicado a trabalhos não E/S.|ServerResourceType|
|QueryPoolIdleThreads|Threads: Threads ociosos do pool de consultas|Contagem|Média|Número de threads ociosos para trabalhos de E/S no pool de threads de processamento.|ServerResourceType|
|QueryPoolJobQueueLength|Threads: tamanho da fila de trabalhos do pool consultas|Contagem|Média|Número de trabalhos na fila do pool de threads de consulta.|ServerResourceType|
|ShortParsingBusyThreads|Threads: Threads ocupados de análise resumida|Contagem|Média|Número de threads ocupados no pool de threads de análise resumida.|ServerResourceType|
|ShortParsingIdleThreads|Threads: Threads ociosos de análise resumida|Contagem|Média|Número de threads ociosos no pool de threads de análise resumida.|ServerResourceType|
|ShortParsingJobQueueLength|Threads: Tamanho da fila de trabalhos de análise resumida|Contagem|Média|Número de trabalhos na fila do pool de threads de análise resumida.|ServerResourceType|
|memory_thrashing_metric|Sobrecarga de memória|Porcentagem|Média|Sobrecarga de memória média.|ServerResourceType|
|mashup_engine_qpu_metric|QPU do mecanismo M|Contagem|Média|Uso de QPU por processos de mecanismo de mashup|ServerResourceType|
|mashup_engine_memory_metric|Memória do mecanismo M|Bytes|Média|Uso de memória pelos processos do mecanismo de mashup|ServerResourceType|
|mashup_engine_private_bytes_metric|Bytes Particulares do Mecanismo M|Bytes|Média|Uso de bytes privados por processos do mecanismo mashup.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|Bytes Virtuais do Mecanismo M|Bytes|Média|Uso de bytes virtuais por processos do mecanismo mashup.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TotalRequests|Solicitações Totais de Gateway (Preterido)|Contagem|Total|Número de solicitações de gateway-use a métrica de solicitação de várias dimensões com a dimensão GatewayResponseCodeCategory em vez disso|Localização, nome do host|
|SuccessfulRequests|Solicitações de Gateway com Êxito (Preterido)|Contagem|Total|Número de solicitações de gateway bem-sucedidas-use a métrica de solicitação de várias dimensões com a dimensão GatewayResponseCodeCategory em vez disso|Localização, nome do host|
|UnauthorizedRequests|Solicitações de Gateway Não Autorizadas (Preterido)|Contagem|Total|Número de solicitações de gateway não autorizadas-use a métrica de solicitação de várias dimensões com a dimensão GatewayResponseCodeCategory em vez disso|Localização, nome do host|
|FailedRequests|Solicitações de Gateway com Falha (Preterido)|Contagem|Total|Número de falhas em solicitações de gateway-use a métrica de solicitação de várias dimensões com a dimensão GatewayResponseCodeCategory em vez disso|Localização, nome do host|
|OtherRequests|Outras Solicitações de Gateway (Preterido)|Contagem|Total|Número de outras solicitações de gateway – use a métrica de solicitação de várias dimensões com a dimensão GatewayResponseCodeCategory em vez disso|Localização, nome do host|
|Duration|Duração total de solicitações de gateway|Milissegundos|Média|Duração total de solicitações de gateway em milissegundos|Localização, nome do host|
|BackendDuration|Duração de Solicitações de Back-end|Milissegundos|Média|Duração de solicitações de back-end em milissegundos|Localização, nome do host|
|Capacity|Capacity|Porcentagem|Média|Métrica de utilização para o serviço ApiManagement|Localização|
|EventHubTotalEvents|Eventos Totais do EventHub|Contagem|Total|Número de eventos enviados ao EventHub|Localização|
|EventHubSuccessfulEvents|Eventos do EventHub Bem-sucedidos|Contagem|Total|Número de eventos de EventHub bem-sucedidos|Localização|
|EventHubTotalFailedEvents|Eventos do EventHub com Falha|Contagem|Total|Número de eventos de EventHub com falha|Localização|
|EventHubRejectedEvents|Eventos do EventHub Rejeitados|Contagem|Total|Número de eventos do EventHub rejeitados (configuração incorreta ou não autorizada)|Localização|
|EventHubThrottledEvents|Eventos do EventHub Restringidos|Contagem|Total|Número de eventos regulados do EventHub|Localização|
|EventHubTimedoutEvents|Eventos do EventHub com Tempo Limite Excedido|Contagem|Total|Número de eventos do EventHub com tempo limite excedido|Localização|
|EventHubDroppedEvents|Eventos do EventHub Ignorados|Contagem|Total|Número de eventos ignorados devido ao limite de tamanho de fila atingido|Localização|
|EventHubTotalBytesSent|Tamanho dos Eventos do EventHub|Bytes|Total|Tamanho total dos eventos do EventHub em bytes|Localização|
|Requests|Requests|Contagem|Total|Métricas de solicitação de gateway com várias dimensões|Local, nome do host, LastErrorReason, BackendResponseCode, GatewayResponseCode, BackendResponseCodeCategory, GatewayResponseCodeCategory|
|Conectividade|Status de conectividade de rede de recursos (versão prévia)|Contagem|Total|Status de conectividade de rede de tipos de recursos dependentes do serviço de gerenciamento de API|Local, ResourceType|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|HttpIncomingRequestCount|HttpIncomingRequestCount|Contagem|Contagem|Número total de solicitações HTTP de entrada.|StatusCode|
|HttpIncomingRequestDuration|HttpIncomingRequestDuration|Contagem|Média|Latência em uma solicitação HTTP.|StatusCode|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|SystemCpuUsagePercentage|Percentual de Uso da CPU do Sistema|Porcentagem|Média|O uso recente da CPU para todo o sistema|AppName, Pod|
|AppCpuUsagePercentage|Percentual de Uso de CPU do Aplicativo|Porcentagem|Média|Porcentagem de uso da CPU da JVM do aplicativo|AppName, Pod|
|AppMemoryCommitted|Memória de Aplicativo Atribuída|Bytes|Média|Memória atribuída à JVM em bytes|AppName, Pod|
|AppMemoryUsed|Memória Usada do Aplicativo|Bytes|Média|Memória do aplicativo usada em bytes|AppName, Pod|
|AppMemoryMax|Memória Máxima do Aplicativo|Bytes|Máximo|A quantidade máxima de memória, em bytes, que pode ser usada para gerenciamento de memória|AppName, Pod|
|MaxOldGenMemoryPoolBytes|Tamanho Máximo Disponível dos Dados de Geração Anterior|Bytes|Média|Tamanho máximo do pool de memória de geração antiga|AppName, Pod|
|OldGenMemoryPoolBytes|Tamanho dos Dados da Geração Anterior|Bytes|Média|Tamanho do pool de memória de geração antiga após um GC completo|AppName, Pod|
|OldGenPromotedBytes|Promover para Tamanho dos Dados da Geração Anterior|Bytes|Máximo|Contagem de aumentos positivos no tamanho do pool de memória de geração antiga antes do GC ser após o GC|AppName, Pod|
|YoungGenPromotedBytes|Promover para Tamanho dos Dados da Geração Recente|Bytes|Máximo|Incrementado para um aumento no tamanho do pool de memória de geração jovem após um GC antes do próximo|AppName, Pod|
|GCPauseTotalCount|Contagem de Pausa do GC|Contagem|Total|Contagem de Pausa do GC|AppName, Pod|
|GCPauseTotalTime|Tempo Total de Pausa do GC|Milissegundos|Total|Tempo Total de Pausa do GC|AppName, Pod|
|TomcatSentBytes|Total de Bytes Enviados do Tomcat|Bytes|Total|Total de Bytes Enviados do Tomcat|AppName, Pod|
|TomcatReceivedBytes|Total de Bytes Recebidos do Tomcat|Bytes|Total|Total de Bytes Recebidos do Tomcat|AppName, Pod|
|TomcatRequestTotalTime|Tempos Totais da Solicitação do Tomcat|Milissegundos|Total|Tempos Totais da Solicitação do Tomcat|AppName, Pod|
|TomcatRequestTotalCount|Contagem Total de Solicitações do Tomcat|Contagem|Total|Contagem Total de Solicitações do Tomcat|AppName, Pod|
|TomcatResponseAvgTime|Tempo Médio da Solicitação do Tomcat|Milissegundos|Média|Tempo Médio da Solicitação do Tomcat|AppName, Pod|
|TomcatRequestMaxTime|Tempo Máximo de Solicitação do Tomcat|Milissegundos|Máximo|Tempo Máximo de Solicitação do Tomcat|AppName, Pod|
|TomcatErrorCount|Erro Global do Tomcat|Contagem|Total|Erro Global do Tomcat|AppName, Pod|
|TomcatSessionActiveMaxCount|Contagem de Sessões Ativas Máximas do Tomcat|Contagem|Total|Contagem de Sessões Ativas Máximas do Tomcat|AppName, Pod|
|TomcatSessionAliveMaxTime|Tempo de Sessões Ativas Máximas do Tomcat|Milissegundos|Máximo|Tempo de Sessões Ativas Máximas do Tomcat|AppName, Pod|
|TomcatSessionActiveCurrentCount|Contagem de Sessões Ativas do Tomcat|Contagem|Total|Contagem de Sessões Ativas do Tomcat|AppName, Pod|
|TomcatSessionCreatedCount|Contagem de Sessões Criadas do Tomcat|Contagem|Total|Contagem de Sessões Criadas do Tomcat|AppName, Pod|
|TomcatSessionExpiredCount|Contagem de Sessões Expiradas do Tomcat|Contagem|Total|Contagem de Sessões Expiradas do Tomcat|AppName, Pod|
|TomcatSessionRejectedCount|Contagem de Sessões Rejeitadas do Tomcat|Contagem|Total|Contagem de Sessões Rejeitadas do Tomcat|AppName, Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TotalJob|Total de trabalhos|Contagem|Total|O número total de trabalhos|Runbook, status|
|TotalUpdateDeploymentRuns|Total de Execuções da Implantação de Atualização|Contagem|Total|Total de execuções de implantação de atualização de software|SoftwareUpdateConfigurationName, status|
|TotalUpdateDeploymentMachineRuns|Total de Execuções de Computador da Implantação de Atualização|Contagem|Total|O computador de implantação de atualização de software total é executado em uma execução de implantação de atualização de software|SoftwareUpdateConfigurationName, status, Computadordedestino, SoftwareUpdateConfigurationRunId|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|CoreCount|Contagem de núcleos dedicados|Contagem|Total|Número total de núcleos dedicados na conta do lote|Nenhum|
|TotalNodeCount|Contagem de nós dedicados|Contagem|Total|Número total de nós dedicados na conta do lote|Nenhum|
|LowPriorityCoreCount|Contagem de núcleos LowPriority|Contagem|Total|Número total de núcleos de baixa prioridade na conta do lote|Nenhum|
|TotalLowPriorityNodeCount|Contagem de nós de baixa prioridade|Contagem|Total|Número total de nós de baixa prioridade na conta do lote|Nenhum|
|CreatingNodeCount|Criação de contagem de nós|Contagem|Total|Número de nós sendo criados|Nenhum|
|StartingNodeCount|Contagem inicial dos nós|Contagem|Total|Número de nós iniciais|Nenhum|
|WaitingForStartTaskNodeCount|Contagem de nós para tarefa de inicialização em espera|Contagem|Total|Número de nós aguardando a conclusão da Tarefa de Inicialização|Nenhum|
|StartTaskFailedNodeCount|Falha na contagem de nós para a tarefa de inicialização|Contagem|Total|Número de nós com falha na tarefa de inicialização|Nenhum|
|IdleNodeCount|Contagem de nós ociosos|Contagem|Total|Número de nós ociosos|Nenhum|
|OfflineNodeCount|Contagem de nós off-line|Contagem|Total|Número de nós off-line|Nenhum|
|RebootingNodeCount|Contagem de nós de reinicialização|Contagem|Total|Número de nós de reinicialização|Nenhum|
|ReimagingNodeCount|Contagem de nós refazendo a imagem|Contagem|Total|Número de nós refazendo a imagem|Nenhum|
|RunningNodeCount|Contagem de nós em execução|Contagem|Total|Número de nós em execução|Nenhum|
|LeavingPoolNodeCount|Contagem de nós saindo do pool|Contagem|Total|Número de nós saindo do pool|Nenhum|
|UnusableNodeCount|Contagem de nós inutilizáveis|Contagem|Total|Número de nós inutilizáveis|Nenhum|
|PreemptedNodeCount|Contagem de nós com preempção|Contagem|Total|Número de nós com preempção|Nenhum|
|TaskStartEvent|Eventos da tarefa de inicialização|Contagem|Total|Número total de tarefas que iniciaram|poolid, jobId|
|TaskCompleteEvent|Eventos de conclusão de tarefa|Contagem|Total|Número total de tarefas concluídas|poolid, jobId|
|TaskFailEvent|Eventos de falha de tarefa|Contagem|Total|Número total de tarefas que foram concluídas em um estado com falha|poolid, jobId|
|PoolCreateEvent|Eventos de criação de pool|Contagem|Total|Número total de pools criados|poolId|
|PoolResizeStartEvent|Eventos de início de redimensionamento de pool|Contagem|Total|Número total de tarefas de redimensionamento de pool que iniciaram|poolId|
|PoolResizeCompleteEvent|Eventos de conclusão de redimensionamento de pool|Contagem|Total|Número total de redimensionamentos de pool concluídos|poolId|
|PoolDeleteStartEvent|Eventos de início de exclusão de pool|Contagem|Total|Número total de exclusões de pool iniciados|poolId|
|PoolDeleteCompleteEvent|Eventos de conclusão de exclusão do pool|Contagem|Total|Número total de exclusões de pool concluídas|poolId|
|JobDeleteCompleteEvent|Eventos de conclusão de exclusão do trabalho|Contagem|Total|Número total de trabalhos que foram excluídos com êxito.|jobId|
|JobDeleteStartEvent|Eventos de início de exclusão do trabalho|Contagem|Total|Número total de trabalhos que foram solicitados a serem excluídos.|jobId|
|JobDisableCompleteEvent|Eventos de conclusão de desabilitação do trabalho|Contagem|Total|Número total de trabalhos que foram desabilitados com êxito.|jobId|
|JobDisableStartEvent|Eventos de início de desabilitação do trabalho|Contagem|Total|Número total de trabalhos que foram solicitados a serem desabilitados.|jobId|
|JobStartEvent|Eventos de início do trabalho|Contagem|Total|Número total de trabalhos que foram iniciados com êxito.|jobId|
|JobTerminateCompleteEvent|Eventos de conclusão de encerramento do trabalho|Contagem|Total|Número total de trabalhos que foram terminados com êxito.|jobId|
|JobTerminateStartEvent|Eventos de início de encerramento do trabalho|Contagem|Total|Número total de trabalhos que foram solicitados a serem encerrados.|jobId|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/workspaces

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Trabalho Enviado|Trabalho Enviado|Contagem|Total|Número de trabalhos enviados|Cenário, ClusterName|
|Trabalho Concluído|Trabalho Concluído|Contagem|Total|Número de trabalhos concluídos|Cenário, ClusterName, Resultadotype|
|Total de Nós|Total de Nós|Contagem|Média|Número total de nós|Cenário, ClusterName|
|Nós Ativos|Nós Ativos|Contagem|Média|Número de nós em execução|Cenário, ClusterName|
|Nós Ociosos|Nós Ociosos|Contagem|Média|Número de nós ociosos|Cenário, ClusterName|
|Nós Inutilizáveis|Nós Inutilizáveis|Contagem|Média|Número de nós inutilizáveis|Cenário, ClusterName|
|Nós com Preempção|Nós com Preempção|Contagem|Média|Número de nós com preempção|Cenário, ClusterName|
|Nós em Saída|Nós em Saída|Contagem|Média|Número de nós de saída|Cenário, ClusterName|
|Total de Núcleos|Total de Núcleos|Contagem|Média|Número de núcleos totais|Cenário, ClusterName|
|Núcleos Ativos|Núcleos Ativos|Contagem|Média|Número de núcleos ativos|Cenário, ClusterName|
|Núcleos Ociosos|Núcleos Ociosos|Contagem|Média|Número de núcleos ociosos|Cenário, ClusterName|
|Núcleos Inutilizáveis|Núcleos Inutilizáveis|Contagem|Média|Número de núcleos inutilizáveis|Cenário, ClusterName|
|Núcleos com Preempção|Núcleos com Preempção|Contagem|Média|Número de núcleos admitidos|Cenário, ClusterName|
|Núcleos em Saída|Núcleos em Saída|Contagem|Média|Número de núcleos de saída|Cenário, ClusterName|
|Percentual de Utilização de Cota|Percentual de Utilização de Cota|Contagem|Média|Porcentagem de cota utilizada|Cenário, ClusterName, VmFamilyName, VmPriority|

## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|CpuUsagePercentageInDouble|Percentual de Uso de CPU|Porcentagem|Máximo|Percentual de Uso de CPU|Nó|
|MemoryUsage|Uso de Memória|Bytes|Média|Uso de Memória|Nó|
|MemoryLimit|Limite de Memória|Bytes|Média|Limite de Memória|Nó|
|MemoryUsagePercentageInDouble|Percentual de Uso de Memória|Porcentagem|Média|Percentual de Uso de Memória|Nó|
|StorageUsage|Uso de Armazenamento|Bytes|Média|Uso de Armazenamento|Nó|
|IOReadBytes|Bytes de Leitura de E/S|Bytes|Total|Bytes de Leitura de E/S|Nó|
|IOWriteBytes|Bytes de Gravação de E/S|Bytes|Total|Bytes de Gravação de E/S|Nó|
|ConnectionAccepted|Conexões Aceitas|Contagem|Total|Conexões Aceitas|Nó|
|ConnectionHandled|Conexões Manipuladas|Contagem|Total|Conexões Manipuladas|Nó|
|ConnectionActive|Conexões ativas|Contagem|Média|Conexões ativas|Nó|
|RequestHandled|Solicitações Manipuladas|Contagem|Total|Solicitações Manipuladas|Nó|
|ProcessedBlocks|Blocos Processados|Contagem|Total|Blocos Processados|Nó|
|ProcessedTransactions|Transações Processadas|Contagem|Total|Transações Processadas|Nó|
|PendingTransactions|Transações Pendentes|Contagem|Média|Transações Pendentes|Nó|
|QueuedTransactions|Transações em Fila|Contagem|Média|Transações em Fila|Nó|



## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|connectedclients|Clientes conectados|Contagem|Máximo||ShardId|
|totalcommandsprocessed|Total de Operações|Contagem|Total||ShardId|
|cachehits|Acertos do Cache|Contagem|Total||ShardId|
|cachemisses|Erros de Cache|Contagem|Total||ShardId|
|cachemissrate|Taxa de erros de cache|Porcentagem|cachemissrate||ShardId|
|getcommands|Gets|Contagem|Total||ShardId|
|setcommands|Conjuntos|Contagem|Total||ShardId|
|operationsPerSecond|Operations por segundo|Contagem|Máximo||ShardId|
|evictedkeys|Chaves removidas|Contagem|Total||ShardId|
|totalkeys|Total de chaves|Contagem|Máximo||ShardId|
|expiredkeys|Chaves expiradas|Contagem|Total||ShardId|
|usedmemory|Memória Usada|Bytes|Máximo||ShardId|
|usedmemorypercentage|Porcentagem de memória utilizada|Porcentagem|Máximo||ShardId|
|usedmemoryRss|Memória RSS Usada|Bytes|Máximo||ShardId|
|serverLoad|Carga do Servidor|Porcentagem|Máximo||ShardId|
|cacheWrite|Gravação no Cache|BytesPerSecond|Máximo||ShardId|
|cacheRead|Cache Lido|BytesPerSecond|Máximo||ShardId|
|percentProcessorTime|CPU|Porcentagem|Máximo||ShardId|
|cacheLatency|Microssegundos de latência de cache (versão prévia)|Contagem|Média||ShardId|
|erros|Errors|Contagem|Máximo||Fragmentid, ErrorType|
|connectedclients0|Clientes conectados (Fragmento 0)|Contagem|Máximo||Nenhum|
|totalcommandsprocessed0|Total de operações (Fragmento 0)|Contagem|Total||Nenhum|
|cachehits0|Ocorrências no cache (Fragmento 0)|Contagem|Total||Nenhum|
|cachemisses0|Perdas no cache (Fragmento 0)|Contagem|Total||Nenhum|
|getcommands0|Gets (Fragmento 0)|Contagem|Total||Nenhum|
|setcommands0|Sets (Fragmento 0)|Contagem|Total||Nenhum|
|operationsPerSecond0|Operações por segundo (fragmento 0)|Contagem|Máximo||Nenhum|
|evictedkeys0|Chaves removidas (Fragmento 0)|Contagem|Total||Nenhum|
|totalkeys0|Total de Chaves (Fragmento 0)|Contagem|Máximo||Nenhum|
|expiredkeys0|Chaves expiradas (Fragmento 0)|Contagem|Total||Nenhum|
|usedmemory0|Memória usada (Fragmento 0)|Bytes|Máximo||Nenhum|
|usedmemoryRss0|Memória RSS usada (Fragmento 0)|Bytes|Máximo||Nenhum|
|serverLoad0|Carga do servidor (Fragmento 0)|Porcentagem|Máximo||Nenhum|
|cacheWrite0|Gravação no cache (Fragmento 0)|BytesPerSecond|Máximo||Nenhum|
|cacheRead0|Leitura no cache (Fragmento 0)|BytesPerSecond|Máximo||Nenhum|
|percentProcessorTime0|CPU (Fragmento 0)|Porcentagem|Máximo||Nenhum|
|connectedclients1|Clientes conectados (Fragmento 1)|Contagem|Máximo||Nenhum|
|totalcommandsprocessed1|Total de operações (Fragmento 1)|Contagem|Total||Nenhum|
|cachehits1|Ocorrências no cache (Fragmento 1)|Contagem|Total||Nenhum|
|cachemisses1|Perdas no cache (Fragmento 1)|Contagem|Total||Nenhum|
|getcommands1|Gets (Fragmento 1)|Contagem|Total||Nenhum|
|setcommands1|Sets (Fragmento 1)|Contagem|Total||Nenhum|
|operationsPerSecond1|Operações por segundo (fragmento 1)|Contagem|Máximo||Nenhum|
|evictedkeys1|Chaves removidas (Fragmento 1)|Contagem|Total||Nenhum|
|totalkeys1|Total de Chaves (Fragmento 1)|Contagem|Máximo||Nenhum|
|expiredkeys1|Chaves expiradas (Fragmento 1)|Contagem|Total||Nenhum|
|usedmemory1|Memória usada (Fragmento 1)|Bytes|Máximo||Nenhum|
|usedmemoryRss1|Memória RSS usada (Fragmento 1)|Bytes|Máximo||Nenhum|
|serverLoad1|Carga do servidor (Fragmento 1)|Porcentagem|Máximo||Nenhum|
|cacheWrite1|Gravação no cache (Fragmento 1)|BytesPerSecond|Máximo||Nenhum|
|cacheRead1|Leitura no cache (Fragmento 1)|BytesPerSecond|Máximo||Nenhum|
|percentProcessorTime1|CPU (Fragmento 1)|Porcentagem|Máximo||Nenhum|
|connectedclients2|Clientes conectados (Fragmento 2)|Contagem|Máximo||Nenhum|
|totalcommandsprocessed2|Total de operações (Fragmento 2)|Contagem|Total||Nenhum|
|cachehits2|Ocorrências no cache (Fragmento 2)|Contagem|Total||Nenhum|
|cachemisses2|Perdas no cache (Fragmento 2)|Contagem|Total||Nenhum|
|getcommands2|Gets (Fragmento 2)|Contagem|Total||Nenhum|
|setcommands2|Sets (Fragmento 2)|Contagem|Total||Nenhum|
|operationsPerSecond2|Operações por segundo (fragmento 2)|Contagem|Máximo||Nenhum|
|evictedkeys2|Chaves removidas (Fragmento 2)|Contagem|Total||Nenhum|
|totalkeys2|Total de Chaves (Fragmento 2)|Contagem|Máximo||Nenhum|
|expiredkeys2|Chaves expiradas (Fragmento 2)|Contagem|Total||Nenhum|
|usedmemory2|Memória usada (Fragmento 2)|Bytes|Máximo||Nenhum|
|usedmemoryRss2|Memória RSS usada (Fragmento 2)|Bytes|Máximo||Nenhum|
|serverLoad2|Carga do servidor (Fragmento 2)|Porcentagem|Máximo||Nenhum|
|cacheWrite2|Gravação no cache (Fragmento 2)|BytesPerSecond|Máximo||Nenhum|
|cacheRead2|Leitura no cache (Fragmento 2)|BytesPerSecond|Máximo||Nenhum|
|percentProcessorTime2|CPU (Fragmento 2)|Porcentagem|Máximo||Nenhum|
|connectedclients3|Clientes conectados (Fragmento 3)|Contagem|Máximo||Nenhum|
|totalcommandsprocessed3|Total de operações (Fragmento 3)|Contagem|Total||Nenhum|
|cachehits3|Ocorrências no cache (Fragmento 3)|Contagem|Total||Nenhum|
|cachemisses3|Perdas no cache (Fragmento 3)|Contagem|Total||Nenhum|
|getcommands3|Gets (Fragmento 3)|Contagem|Total||Nenhum|
|setcommands3|Sets (Fragmento 3)|Contagem|Total||Nenhum|
|operationsPerSecond3|Operações por segundo (fragmento 3)|Contagem|Máximo||Nenhum|
|evictedkeys3|Chaves removidas (Fragmento 3)|Contagem|Total||Nenhum|
|totalkeys3|Total de Chaves (Fragmento 3)|Contagem|Máximo||Nenhum|
|expiredkeys3|Chaves expiradas (Fragmento 3)|Contagem|Total||Nenhum|
|usedmemory3|Memória usada (Fragmento 3)|Bytes|Máximo||Nenhum|
|usedmemoryRss3|Memória RSS usada (Fragmento 3)|Bytes|Máximo||Nenhum|
|serverLoad3|Carga do servidor (Fragmento 3)|Porcentagem|Máximo||Nenhum|
|cacheWrite3|Gravação no cache (Fragmento 3)|BytesPerSecond|Máximo||Nenhum|
|cacheRead3|Leitura no cache (Fragmento 3)|BytesPerSecond|Máximo||Nenhum|
|percentProcessorTime3|CPU (Fragmento 3)|Porcentagem|Máximo||Nenhum|
|connectedclients4|Clientes conectados (Fragmento 4)|Contagem|Máximo||Nenhum|
|totalcommandsprocessed4|Total de operações (Fragmento 4)|Contagem|Total||Nenhum|
|cachehits4|Ocorrências no cache (Fragmento 4)|Contagem|Total||Nenhum|
|cachemisses4|Perdas no cache (Fragmento 4)|Contagem|Total||Nenhum|
|getcommands4|Gets (Fragmento 4)|Contagem|Total||Nenhum|
|setcommands4|Sets (Fragmento 4)|Contagem|Total||Nenhum|
|operationsPerSecond4|Operações por segundo (fragmento 4)|Contagem|Máximo||Nenhum|
|evictedkeys4|Chaves removidas (Fragmento 4)|Contagem|Total||Nenhum|
|totalkeys4|Total de Chaves (Fragmento 4)|Contagem|Máximo||Nenhum|
|expiredkeys4|Chaves expiradas (Fragmento 4)|Contagem|Total||Nenhum|
|usedmemory4|Memória usada (Fragmento 4)|Bytes|Máximo||Nenhum|
|usedmemoryRss4|Memória RSS usada (Fragmento 4)|Bytes|Máximo||Nenhum|
|serverLoad4|Carga do servidor (Fragmento 4)|Porcentagem|Máximo||Nenhum|
|cacheWrite4|Gravação no cache (Fragmento 4)|BytesPerSecond|Máximo||Nenhum|
|cacheRead4|Leitura no cache (Fragmento 4)|BytesPerSecond|Máximo||Nenhum|
|percentProcessorTime4|CPU (Fragmento 4)|Porcentagem|Máximo||Nenhum|
|connectedclients5|Clientes conectados (Fragmento 5)|Contagem|Máximo||Nenhum|
|totalcommandsprocessed5|Total de operações (Fragmento 5)|Contagem|Total||Nenhum|
|cachehits5|Ocorrências no cache (Fragmento 5)|Contagem|Total||Nenhum|
|cachemisses5|Perdas no cache (Fragmento 5)|Contagem|Total||Nenhum|
|getcommands5|Gets (Fragmento 5)|Contagem|Total||Nenhum|
|setcommands5|Sets (Fragmento 5)|Contagem|Total||Nenhum|
|operationsPerSecond5|Operações por segundo (fragmento 5)|Contagem|Máximo||Nenhum|
|evictedkeys5|Chaves removidas (Fragmento 5)|Contagem|Total||Nenhum|
|totalkeys5|Total de Chaves (Fragmento 5)|Contagem|Máximo||Nenhum|
|expiredkeys5|Chaves expiradas (Fragmento 5)|Contagem|Total||Nenhum|
|usedmemory5|Memória usada (Fragmento 5)|Bytes|Máximo||Nenhum|
|usedmemoryRss5|Memória RSS usada (Fragmento 5)|Bytes|Máximo||Nenhum|
|serverLoad5|Carga do servidor (Fragmento 5)|Porcentagem|Máximo||Nenhum|
|cacheWrite5|Gravação no cache (Fragmento 5)|BytesPerSecond|Máximo||Nenhum|
|cacheRead5|Leitura no cache (Fragmento 5)|BytesPerSecond|Máximo||Nenhum|
|percentProcessorTime5|CPU (Fragmento 5)|Porcentagem|Máximo||Nenhum|
|connectedclients6|Clientes conectados (Fragmento 6)|Contagem|Máximo||Nenhum|
|totalcommandsprocessed6|Total de operações (Fragmento 6)|Contagem|Total||Nenhum|
|cachehits6|Ocorrências no cache (Fragmento 6)|Contagem|Total||Nenhum|
|cachemisses6|Perdas no cache (Fragmento 6)|Contagem|Total||Nenhum|
|getcommands6|Gets (Fragmento 6)|Contagem|Total||Nenhum|
|setcommands6|Sets (Fragmento 6)|Contagem|Total||Nenhum|
|operationsPerSecond6|Operações por segundo (fragmento 6)|Contagem|Máximo||Nenhum|
|evictedkeys6|Chaves removidas (Fragmento 6)|Contagem|Total||Nenhum|
|totalkeys6|Total de Chaves (Fragmento 6)|Contagem|Máximo||Nenhum|
|expiredkeys6|Chaves expiradas (Fragmento 6)|Contagem|Total||Nenhum|
|usedmemory6|Memória usada (Fragmento 6)|Bytes|Máximo||Nenhum|
|usedmemoryRss6|Memória RSS usada (Fragmento 6)|Bytes|Máximo||Nenhum|
|serverLoad6|Carga do servidor (Fragmento 6)|Porcentagem|Máximo||Nenhum|
|cacheWrite6|Gravação no cache (Fragmento 6)|BytesPerSecond|Máximo||Nenhum|
|cacheRead6|Leitura no cache (Fragmento 6)|BytesPerSecond|Máximo||Nenhum|
|percentProcessorTime6|CPU (Fragmento 6)|Porcentagem|Máximo||Nenhum|
|connectedclients7|Clientes conectados (Fragmento 7)|Contagem|Máximo||Nenhum|
|totalcommandsprocessed7|Total de operações (Fragmento 7)|Contagem|Total||Nenhum|
|cachehits7|Ocorrências no cache (Fragmento 7)|Contagem|Total||Nenhum|
|cachemisses7|Perdas no cache (Fragmento 7)|Contagem|Total||Nenhum|
|getcommands7|Gets (Fragmento 7)|Contagem|Total||Nenhum|
|setcommands7|Sets (Fragmento 7)|Contagem|Total||Nenhum|
|operationsPerSecond7|Operações por segundo (fragmento 7)|Contagem|Máximo||Nenhum|
|evictedkeys7|Chaves removidas (Fragmento 7)|Contagem|Total||Nenhum|
|totalkeys7|Total de Chaves (Fragmento 7)|Contagem|Máximo||Nenhum|
|expiredkeys7|Chaves expiradas (Fragmento 7)|Contagem|Total||Nenhum|
|usedmemory7|Memória usada (Fragmento 7)|Bytes|Máximo||Nenhum|
|usedmemoryRss7|Memória RSS usada (Fragmento 7)|Bytes|Máximo||Nenhum|
|serverLoad7|Carga do servidor (Fragmento 7)|Porcentagem|Máximo||Nenhum|
|cacheWrite7|Gravação no cache (Fragmento 7)|BytesPerSecond|Máximo||Nenhum|
|cacheRead7|Leitura no cache (Fragmento 7)|BytesPerSecond|Máximo||Nenhum|
|percentProcessorTime7|CPU (Fragmento 7)|Porcentagem|Máximo||Nenhum|
|connectedclients8|Clientes conectados (Fragmento 8)|Contagem|Máximo||Nenhum|
|totalcommandsprocessed8|Total de operações (Fragmento 8)|Contagem|Total||Nenhum|
|cachehits8|Ocorrências no cache (Fragmento 8)|Contagem|Total||Nenhum|
|cachemisses8|Perdas no cache (Fragmento 8)|Contagem|Total||Nenhum|
|getcommands8|Gets (Fragmento 8)|Contagem|Total||Nenhum|
|setcommands8|Sets (Fragmento 8)|Contagem|Total||Nenhum|
|operationsPerSecond8|Operações por segundo (fragmento 8)|Contagem|Máximo||Nenhum|
|evictedkeys8|Chaves removidas (Fragmento 8)|Contagem|Total||Nenhum|
|totalkeys8|Total de Chaves (Fragmento 8)|Contagem|Máximo||Nenhum|
|expiredkeys8|Chaves expiradas (Fragmento 8)|Contagem|Total||Nenhum|
|usedmemory8|Memória usada (Fragmento 8)|Bytes|Máximo||Nenhum|
|usedmemoryRss8|Memória RSS usada (Fragmento 8)|Bytes|Máximo||Nenhum|
|serverLoad8|Carga do servidor (Fragmento 8)|Porcentagem|Máximo||Nenhum|
|cacheWrite8|Gravação no cache (Fragmento 8)|BytesPerSecond|Máximo||Nenhum|
|cacheRead8|Leitura no cache (Fragmento 8)|BytesPerSecond|Máximo||Nenhum|
|percentProcessorTime8|CPU (Fragmento 8)|Porcentagem|Máximo||Nenhum|
|connectedclients9|Clientes conectados (Fragmento 9)|Contagem|Máximo||Nenhum|
|totalcommandsprocessed9|Total de operações (Fragmento 9)|Contagem|Total||Nenhum|
|cachehits9|Ocorrências no cache (Fragmento 9)|Contagem|Total||Nenhum|
|cachemisses9|Perdas no cache (Fragmento 9)|Contagem|Total||Nenhum|
|getcommands9|Gets (Fragmento 9)|Contagem|Total||Nenhum|
|setcommands9|Sets (Fragmento 9)|Contagem|Total||Nenhum|
|operationsPerSecond9|Operações por segundo (fragmento 9)|Contagem|Máximo||Nenhum|
|evictedkeys9|Chaves removidas (Fragmento 9)|Contagem|Total||Nenhum|
|totalkeys9|Total de Chaves (Fragmento 9)|Contagem|Máximo||Nenhum|
|expiredkeys9|Chaves expiradas (Fragmento 9)|Contagem|Total||Nenhum|
|usedmemory9|Memória usada (Fragmento 9)|Bytes|Máximo||Nenhum|
|usedmemoryRss9|Memória RSS usada (Fragmento 9)|Bytes|Máximo||Nenhum|
|serverLoad9|Carga do servidor (Fragmento 9)|Porcentagem|Máximo||Nenhum|
|cacheWrite9|Gravação no cache (Fragmento 9)|BytesPerSecond|Máximo||Nenhum|
|cacheRead9|Leitura no cache (Fragmento 9)|BytesPerSecond|Máximo||Nenhum|
|percentProcessorTime9|CPU (Fragmento 9)|Porcentagem|Máximo||Nenhum|




## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/cdnwebapplicationfirewallpolicies

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Contagem de solicitações do Firewall de Aplicativo Web|Contagem|Total|O número de solicitações de cliente processadas pelo Firewall do Aplicativo Web|PolicyName, RuleName, ação|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Percentual de CPU|Porcentagem de CPU|Porcentagem|Média|O percentual das unidades de computação alocadas que estão atualmente em uso pelas Máquinas Virtuais.|Nenhum|
|Entrada na rede|Entrada na rede|Bytes|Total|O número de bytes recebidos em todos os adaptadores de rede pelas Máquinas Virtuais (Tráfego de Entrada).|Nenhum|
|Saída da rede|Saída da rede|Bytes|Total|O número de bytes de saída em todos os adaptadores de rede pelas Máquinas Virtuais (Tráfego de Saída).|Nenhum|
|Disk Read Bytes/Sec|Leitura de disco|BytesPerSecond|Média|Média de bytes lidos do disco durante o período de monitoramento.|Nenhum|
|Disk Write Bytes/Sec|Gravação de disco|BytesPerSecond|Média|Média de bytes gravados em disco durante o período de monitoramento.|Nenhum|
|Operações de leitura de disco/Seg|Operações de leitura de disco/Seg|CountPerSecond|Média|IOPS de leitura de disco.|Nenhum|
|Operações de gravação de disco/Seg|Operações de gravação de disco/Seg|CountPerSecond|Média|IOPS de gravação de disco.|Nenhum|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Percentual de CPU|Porcentagem de CPU|Porcentagem|Média|O percentual das unidades de computação alocadas que estão atualmente em uso pelas Máquinas Virtuais.|RoleInstanceId|
|Entrada na rede|Entrada na rede|Bytes|Total|O número de bytes recebidos em todos os adaptadores de rede pelas Máquinas Virtuais (Tráfego de Entrada).|RoleInstanceId|
|Saída da rede|Saída da rede|Bytes|Total|O número de bytes de saída em todos os adaptadores de rede pelas Máquinas Virtuais (Tráfego de Saída).|RoleInstanceId|
|Disk Read Bytes/Sec|Leitura de disco|BytesPerSecond|Média|Média de bytes lidos do disco durante o período de monitoramento.|RoleInstanceId|
|Disk Write Bytes/Sec|Gravação de disco|BytesPerSecond|Média|Média de bytes gravados em disco durante o período de monitoramento.|RoleInstanceId|
|Operações de leitura de disco/Seg|Operações de leitura de disco/Seg|CountPerSecond|Média|IOPS de leitura de disco.|RoleInstanceId|
|Operações de gravação de disco/Seg|Operações de gravação de disco/Seg|CountPerSecond|Média|IOPS de gravação de disco.|RoleInstanceId|



## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft.ClassicStorage/storageAccounts

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|UsedCapacity|Capacidade utilizada|Bytes|Média|Capacidade utilizada pela conta|Nenhum|
|Transações|Transactions|Contagem|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|ResponseType, GeoType, ApiName, autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|Geotipo, ApiName, autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável.|Geotipo, ApiName, autenticação|
|SuccessServerLatency|Latência de Servidor Com Sucesso|Milissegundos|Média|A latência usada pelo armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Esse valor não inclui a latência de rede especificada em SuccessE2ELatency.|Geotipo, ApiName, autenticação|
|SuccessE2ELatency|Latência de E2E com Sucesso|Milissegundos|Média|A latência de ponta a ponta de solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|Geotipo, ApiName, autenticação|
|Disponibilidade|Disponibilidade|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|Geotipo, ApiName, autenticação|

## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft.ClassicStorage/storageAccounts/blobServices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BlobCapacity|Capacidade do Blob|Bytes|Média|A quantidade de armazenamento usada pelo serviço BLOB da conta de armazenamento em bytes.|BlobType, camada|
|BlobCount|Contagem de Blobs|Contagem|Média|O número de blob no serviço BLOB da conta de armazenamento.|BlobType, camada|
|ContainerCount|Contagem de Contêineres de Blobs|Contagem|Média|O número de contêineres no serviço BLOB da conta de armazenamento.|Nenhum|
|IndexCapacity|Capacidade de Índice|Bytes|Média|A quantidade de armazenamento usada pelo índice ADLS Gen2 (hierárquico) em bytes.|Nenhum|
|Transações|Transactions|Contagem|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|ResponseType, GeoType, ApiName, autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|Geotipo, ApiName, autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável.|Geotipo, ApiName, autenticação|
|SuccessServerLatency|Latência de Servidor Com Sucesso|Milissegundos|Média|A latência usada pelo armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Esse valor não inclui a latência de rede especificada em SuccessE2ELatency.|Geotipo, ApiName, autenticação|
|SuccessE2ELatency|Latência de E2E com Sucesso|Milissegundos|Média|A latência de ponta a ponta de solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|Geotipo, ApiName, autenticação|
|Disponibilidade|Disponibilidade|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|Geotipo, ApiName, autenticação|

## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft.ClassicStorage/storageAccounts/tableServices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TableCapacity|Capacidade da Tabela|Bytes|Média|A quantidade de armazenamento usada pelo serviço tabela da conta de armazenamento em bytes.|Nenhum|
|TableCount|Contagem de Tabelas|Contagem|Média|O número de tabelas no serviço tabela da conta de armazenamento.|Nenhum|
|TableEntityCount|Contagem de Entidades de Tabela|Contagem|Média|O número de entidades de tabela no serviço tabela da conta de armazenamento.|Nenhum|
|Transações|Transactions|Contagem|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|ResponseType, GeoType, ApiName, autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|Geotipo, ApiName, autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável.|Geotipo, ApiName, autenticação|
|SuccessServerLatency|Latência de Servidor Com Sucesso|Milissegundos|Média|A latência usada pelo armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Esse valor não inclui a latência de rede especificada em SuccessE2ELatency.|Geotipo, ApiName, autenticação|
|SuccessE2ELatency|Latência de E2E com Sucesso|Milissegundos|Média|A latência de ponta a ponta de solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|Geotipo, ApiName, autenticação|
|Disponibilidade|Disponibilidade|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|Geotipo, ApiName, autenticação|

## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft.ClassicStorage/storageAccounts/fileServices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|FileCapacity|Capacidade do Arquivo|Bytes|Média|A quantidade de armazenamento usada pelo serviço de arquivo da conta de armazenamento em bytes.|FileShare|
|FileCount|Contagem de Arquivos|Contagem|Média|O número de arquivos no serviço de arquivo da conta de armazenamento.|FileShare|
|FileShareCount|Contagem de Compartilhamentos de Arquivos|Contagem|Média|O número de compartilhamentos de arquivos no serviço de arquivo da conta de armazenamento.|Nenhum|
|FileShareSnapshotCount|Contagem de Instantâneos do Compartilhamento de Arquivo|Contagem|Média|O número de instantâneos presentes no serviço compartilhamento de arquivos da conta de armazenamento.|FileShare|
|FileShareSnapshotSize|Tamanho do Instantâneo do Compartilhamento de Arquivo|Bytes|Média|A quantidade de armazenamento usada pelos instantâneos no serviço de arquivo da conta de armazenamento, em bytes.|FileShare|
|FileShareQuota|Tamanho da cota do compartilhamento de arquivo|Bytes|Média|O limite superior da quantidade de armazenamento que pode ser usada pelo serviço de arquivos do Azure em bytes.|FileShare|
|Transações|Transactions|Contagem|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|ResponseType, GeoType, ApiName, autenticação, FileShare|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|Geotipo, ApiName, autenticação, FileShare|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável.|Geotipo, ApiName, autenticação, FileShare|
|SuccessServerLatency|Latência de Servidor Com Sucesso|Milissegundos|Média|A latência usada pelo armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Esse valor não inclui a latência de rede especificada em SuccessE2ELatency.|Geotipo, ApiName, autenticação, FileShare|
|SuccessE2ELatency|Latência de E2E com Sucesso|Milissegundos|Média|A latência de ponta a ponta de solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|Geotipo, ApiName, autenticação, FileShare|
|Disponibilidade|Disponibilidade|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|Geotipo, ApiName, autenticação, FileShare|

## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft.ClassicStorage/storageAccounts/queueServices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|QueueCapacity|Capacidade da Fila|Bytes|Média|A quantidade de armazenamento usada pela serviço Fila da conta de armazenamento em bytes.|Nenhum|
|QueueCount|Contagem de Filas|Contagem|Média|O número de filas na serviço Fila da conta de armazenamento.|Nenhum|
|QueueMessageCount|Contagem de Mensagens da Fila|Contagem|Média|O número aproximado de mensagens de fila na serviço Fila da conta de armazenamento.|Nenhum|
|Transações|Transactions|Contagem|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|ResponseType, GeoType, ApiName, autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|Geotipo, ApiName, autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável.|Geotipo, ApiName, autenticação|
|SuccessServerLatency|Latência de Servidor Com Sucesso|Milissegundos|Média|A latência usada pelo armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Esse valor não inclui a latência de rede especificada em SuccessE2ELatency.|Geotipo, ApiName, autenticação|
|SuccessE2ELatency|Latência de E2E com Sucesso|Milissegundos|Média|A latência de ponta a ponta de solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|Geotipo, ApiName, autenticação|
|Disponibilidade|Disponibilidade|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|Geotipo, ApiName, autenticação|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TotalCalls|Total de Chamadas|Contagem|Total|Número total de chamadas.|ApiName, OperationName, região|
|SuccessfulCalls|Chamadas com Êxito|Contagem|Total|Número de chamadas com êxito.|ApiName, OperationName, região|
|TotalErrors|Total de Erros|Contagem|Total|Número total de chamadas com resposta de erro (código de resposta HTTP 4xx ou 5xx).|ApiName, OperationName, região|
|BlockedCalls|Chamadas Bloqueadas|Contagem|Total|Número de chamadas que excederam a taxa ou o limite de cota.|ApiName, OperationName, região|
|ServerErrors|Erros do Servidor|Contagem|Total|Número de chamadas com erro interno do serviço (código de resposta HTTP 5xx).|ApiName, OperationName, região|
|ClientErrors|Erros do Cliente|Contagem|Total|Número de chamadas com erro do lado do cliente (código de resposta HTTP 4xx).|ApiName, OperationName, região|
|DataIn|Entrada de Dados|Bytes|Total|Tamanho dos dados de entrada em bytes.|ApiName, OperationName, região|
|DataOut|Saída de dados|Bytes|Total|Tamanho dos dados de saída em bytes.|ApiName, OperationName, região|
|Latência|Latency|MilliSeconds|Média|Latência em milissegundos.|ApiName, OperationName, região|
|TotalTokenCalls|Total de chamadas de token|Contagem|Total|Número total de chamadas de token.|ApiName, OperationName, região|
|CharactersTranslated|Caracteres traduzidos|Contagem|Total|Número total de caracteres na solicitação de texto de entrada.|ApiName, OperationName, região|
|CharactersTrained|Caracteres Treinados|Contagem|Total|Número total de caracteres treinados.|ApiName, OperationName, região|
|SpeechSessionDuration|Duração da Sessão de Fala|Segundos|Total|Duração total da sessão de fala em segundos.|ApiName, OperationName, região|
|TotalTransactions|Total de Transações|Contagem|Total|Número total de transações.|Nenhum|
|ProcessedImages|Imagens processadas|Contagem|Total| Número de transações para processamento de imagem.|ApiName, FeatureName, canal, região|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Percentual de CPU|Porcentagem de CPU|Porcentagem|Média|A porcentagem das unidades de computação alocadas que estão atualmente em uso pela máquina virtual ou pelas máquinas virtuais|Nenhum|
|Entrada na rede|Rede em Faturável (Preterido)|Bytes|Total|O número de bytes faturáveis recebidos em todas as interfaces de rede pelas máquinas virtuais (tráfego de entrada) (preterido)|Nenhum|
|Saída da rede|Rede Fora de Faturável (Preterido)|Bytes|Total|O número de bytes faturáveis de saída em todas as interfaces de rede pelas máquinas virtuais (tráfego de saída) (preterido)|Nenhum|
|Bytes de leitura de disco|Bytes de leitura de disco|Bytes|Total|Bytes lidos do disco durante o período de monitoramento|Nenhum|
|Bytes de gravação de disco|Bytes de gravação de disco|Bytes|Total|Bytes gravados no disco durante o período de monitoramento|Nenhum|
|Operações de leitura de disco/Seg|Operações de leitura de disco/Seg|CountPerSecond|Média|IOPS de leitura de disco|Nenhum|
|Operações de gravação de disco/Seg|Operações de gravação de disco/Seg|CountPerSecond|Média|IOPS de gravação de disco|Nenhum|
|Créditos de CPU Restantes|Créditos de CPU Restantes|Contagem|Média|Número total de créditos disponíveis para intermitência|Nenhum|
|Créditos de CPU Consumidos|Créditos de CPU Consumidos|Contagem|Média|Número total de créditos consumido pela Máquina Virtual|Nenhum|
|Por Bytes de Leitura do Disco/s|Bytes de leitura do disco de dados/s [(preterido)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento|SlotId|
|Por Bytes de Gravação de Disco/s|Bytes de gravação do disco de dados/s [(preterido)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento|SlotId|
|Por operações de Leitura de Disco/s|Operações de leitura de disco de dados/s [(preterido)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento|SlotId|
|Por Operações de Gravação de Disco/s|Operações de gravação do disco de dados/s [(preterido)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento|SlotId|
|Por QD de Disco|Disco de dados QD [(preterido)](portal-disk-metrics-deprecation.md)] (Portal-Disk-Metrics-deprecation.MD)|Contagem|Média|Profundidade da Fila do Disco de Dados (ou Tamanho da Fila)|SlotId|
|SO Por Bytes de Leitura do Disco/s|Bytes de leitura do disco do so/s [(preterido)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhum|
|SO Por Bytes de Gravação do Disco/s|Bytes de gravação de disco do sistema operacional/s [(preterido)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento do disco do sistema operacional|Nenhum|
|SO Por Operações de Leitura do Disco/s|Operações de leitura de disco do so/s [(preterido)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhum|
|SO Por Operações de Gravação do Disco/s|Operações de gravação de disco do sistema operacional/s [(preterido)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhum|
|Os Por QD de Disco|Disco do so QD [(preterido)](portal-disk-metrics-deprecation.md)|Contagem|Média|Profundidade da Fila do Disco do SO (ou Tamanho da Fila)|Nenhum|
|Bytes de Leitura do Disco de Dados/s|Bytes de Leitura do Disco de Dados/s (Visualização)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento|LUN|
|Bytes de Gravação do Disco de Dados/s|Bytes de Gravação do Disco de Dados/s (Visualização)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento|LUN|
|Operações de Leitura do Disco de Dados/s|Operações de Leitura do Disco de Dados/s (Visualização)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento|LUN|
|Operações de Gravação do Disco de Dados/s|Operações de Gravação do Disco de Dados/s (Visualização)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento|LUN|
|Profundidade da Fila de Disco de Dados|Profundidade da Fila de Disco de Dados (Versão prévia)|Contagem|Média|Profundidade da Fila do Disco de Dados (ou Tamanho da Fila)|LUN|
|Bytes de Leitura do Disco do SO/s|Bytes de Leitura do Disco do SO/s (Visualização)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhum|
|Bytes de Gravação do Disco do SO/s|Bytes de Gravação do Disco do SO/s (Visualização)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento do disco do sistema operacional|Nenhum|
|Operações de Leitura do Disco do SO/s|Operações de Leitura do Disco do SO/s (Visualização)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhum|
|Operações de Gravação do Disco do SO/s|Operações de Gravação do Disco do SO/s (Visualização)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhum|
|Profundidade da Fila de Disco do SO|Profundidade da Fila de Disco do SO (Versão prévia)|Contagem|Média|Profundidade da Fila do Disco do SO (ou Tamanho da Fila)|Nenhum|
|Fluxos de Entrada|Fluxos de Entrada|Contagem|Média|Os fluxos de entrada são o número de fluxos atuais na direção de entrada (tráfego entrando na VM)|Nenhum|
|Fluxos de Saída|Fluxos de Saída|Contagem|Média|Fluxos de saída são o número de fluxos atuais na direção de saída (tráfego saindo da VM)|Nenhum|
|Taxa Máxima de Criação de Fluxos de Entrada|Taxa Máxima de Criação de Fluxos de Entrada|CountPerSecond|Média|A taxa de criação máxima de fluxos de entrada (tráfego entrando na VM)|Nenhum|
|Taxa Máxima de Criação de Fluxos de Saída|Taxa Máxima de Criação de Fluxos de Saída|CountPerSecond|Média|A taxa de criação máxima de fluxos de saída (tráfego saindo da VM)|Nenhum|
|Ocorrência de Leitura do Cache de Disco de Dados Premium|Ocorrência de Leitura do Cache de Disco de Dados Premium (Versão prévia)|Porcentagem|Média|Ocorrência de Leitura do Cache de Disco de Dados Premium|LUN|
|Ausência de Leitura do Cache de Disco de Dados Premium|Ausência de Leitura do Cache de Disco de Dados Premium (Versão prévia)|Porcentagem|Média|Ausência de Leitura do Cache de Disco de Dados Premium|LUN|
|Ocorrência de Leitura do Cache de Disco do SO Premium|Ocorrência de Leitura do Cache de Disco do SO Premium (Versão prévia)|Porcentagem|Média|Ocorrência de Leitura do Cache de Disco do SO Premium|Nenhum|
|Ausência de Leitura do Cache de Disco de SO Premium|Ausência de Leitura do Cache de Disco de SO Premium (Versão prévia)|Porcentagem|Média|Ausência de Leitura do Cache de Disco de SO Premium|Nenhum|
|Rede no Total|Rede no Total|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela máquina virtual ou pelas máquinas virtuais (tráfego de entrada)|Nenhum|
|Rede Fora do Total|Rede Fora do Total|Bytes|Total|O número de bytes de saída em todas as interfaces de rede pela máquina virtual ou pelas máquinas virtuais (tráfego de saída)|Nenhum|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Percentual de CPU|Porcentagem de CPU|Porcentagem|Média|A porcentagem das unidades de computação alocadas que estão atualmente em uso pela máquina virtual ou pelas máquinas virtuais|VMName|
|Entrada na rede|Rede em Faturável (Preterido)|Bytes|Total|O número de bytes faturáveis recebidos em todas as interfaces de rede pelas máquinas virtuais (tráfego de entrada) (preterido)|VMName|
|Saída da rede|Rede Fora de Faturável (Preterido)|Bytes|Total|O número de bytes faturáveis de saída em todas as interfaces de rede pelas máquinas virtuais (tráfego de saída) (preterido)|VMName|
|Bytes de leitura de disco|Bytes de leitura de disco|Bytes|Total|Bytes lidos do disco durante o período de monitoramento|VMName|
|Bytes de gravação de disco|Bytes de gravação de disco|Bytes|Total|Bytes gravados no disco durante o período de monitoramento|VMName|
|Operações de leitura de disco/Seg|Operações de leitura de disco/Seg|CountPerSecond|Média|IOPS de leitura de disco|VMName|
|Operações de gravação de disco/Seg|Operações de gravação de disco/Seg|CountPerSecond|Média|IOPS de gravação de disco|VMName|
|Créditos de CPU Restantes|Créditos de CPU Restantes|Contagem|Média|Número total de créditos disponíveis para intermitência|Nenhum|
|Créditos de CPU Consumidos|Créditos de CPU Consumidos|Contagem|Média|Número total de créditos consumido pela Máquina Virtual|Nenhum|
|Por Bytes de Leitura do Disco/s|Bytes de leitura do disco de dados/s [(preterido)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento|SlotId|
|Por Bytes de Gravação de Disco/s|Bytes de gravação do disco de dados/s [(preterido)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento|SlotId|
|Por operações de Leitura de Disco/s|Operações de leitura de disco de dados/s [(preterido)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento|SlotId|
|Por Operações de Gravação de Disco/s|Operações de gravação do disco de dados/s [(preterido)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento|SlotId|
|Por QD de Disco|Disco de dados QD [(preterido)](portal-disk-metrics-deprecation.md)|Contagem|Média|Profundidade da Fila do Disco de Dados (ou Tamanho da Fila)|SlotId|
|SO Por Bytes de Leitura do Disco/s|Bytes de leitura do disco do so/s [(preterido)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhum|
|SO Por Bytes de Gravação do Disco/s|Bytes de gravação de disco do sistema operacional/s [(preterido)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento do disco do sistema operacional|Nenhum|
|SO Por Operações de Leitura do Disco/s|Operações de leitura de disco do so/s [(preterido)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhum|
|SO Por Operações de Gravação do Disco/s|Operações de gravação de disco do sistema operacional/s [(preterido)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhum|
|Os Por QD de Disco|Disco do so QD [(preterido)](portal-disk-metrics-deprecation.md)|Contagem|Média|Profundidade da Fila do Disco do SO (ou Tamanho da Fila)|Nenhum|
|Bytes de Leitura do Disco de Dados/s|Bytes de Leitura do Disco de Dados/s (Visualização)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento|LUN, VMName|
|Bytes de Gravação do Disco de Dados/s|Bytes de Gravação do Disco de Dados/s (Visualização)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento|LUN, VMName|
|Operações de Leitura do Disco de Dados/s|Operações de Leitura do Disco de Dados/s (Visualização)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento|LUN, VMName|
|Operações de Gravação do Disco de Dados/s|Operações de Gravação do Disco de Dados/s (Visualização)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento|LUN, VMName|
|Profundidade da Fila de Disco de Dados|Profundidade da Fila de Disco de Dados (Versão prévia)|Contagem|Média|Profundidade da Fila do Disco de Dados (ou Tamanho da Fila)|LUN, VMName|
|Bytes de Leitura do Disco do SO/s|Bytes de Leitura do Disco do SO/s (Visualização)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento para o disco do sistema operacional|VMName|
|Bytes de Gravação do Disco do SO/s|Bytes de Gravação do Disco do SO/s (Visualização)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento do disco do sistema operacional|VMName|
|Operações de Leitura do Disco do SO/s|Operações de Leitura do Disco do SO/s (Visualização)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|VMName|
|Operações de Gravação do Disco do SO/s|Operações de Gravação do Disco do SO/s (Visualização)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|VMName|
|Profundidade da Fila de Disco do SO|Profundidade da Fila de Disco do SO (Versão prévia)|Contagem|Média|Profundidade da Fila do Disco do SO (ou Tamanho da Fila)|VMName|
|Fluxos de Entrada|Fluxos de Entrada|Contagem|Média|Os fluxos de entrada são o número de fluxos atuais na direção de entrada (tráfego entrando na VM)|VMName|
|Fluxos de Saída|Fluxos de Saída|Contagem|Média|Fluxos de saída são o número de fluxos atuais na direção de saída (tráfego saindo da VM)|VMName|
|Taxa Máxima de Criação de Fluxos de Entrada|Taxa Máxima de Criação de Fluxos de Entrada|CountPerSecond|Média|A taxa de criação máxima de fluxos de entrada (tráfego entrando na VM)|VMName|
|Taxa Máxima de Criação de Fluxos de Saída|Taxa Máxima de Criação de Fluxos de Saída|CountPerSecond|Média|A taxa de criação máxima de fluxos de saída (tráfego saindo da VM)|VMName|
|Ocorrência de Leitura do Cache de Disco de Dados Premium|Ocorrência de Leitura do Cache de Disco de Dados Premium (Versão prévia)|Porcentagem|Média|Ocorrência de Leitura do Cache de Disco de Dados Premium|LUN, VMName|
|Ausência de Leitura do Cache de Disco de Dados Premium|Ausência de Leitura do Cache de Disco de Dados Premium (Versão prévia)|Porcentagem|Média|Ausência de Leitura do Cache de Disco de Dados Premium|LUN, VMName|
|Ocorrência de Leitura do Cache de Disco do SO Premium|Ocorrência de Leitura do Cache de Disco do SO Premium (Versão prévia)|Porcentagem|Média|Ocorrência de Leitura do Cache de Disco do SO Premium|VMName|
|Ausência de Leitura do Cache de Disco de SO Premium|Ausência de Leitura do Cache de Disco de SO Premium (Versão prévia)|Porcentagem|Média|Ausência de Leitura do Cache de Disco de SO Premium|VMName|
|Rede no Total|Rede no Total|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela máquina virtual ou pelas máquinas virtuais (tráfego de entrada)|VMName|
|Rede Fora do Total|Rede Fora do Total|Bytes|Total|O número de bytes de saída em todas as interfaces de rede pela máquina virtual ou pelas máquinas virtuais (tráfego de saída)|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Percentual de CPU|Porcentagem de CPU|Porcentagem|Média|A porcentagem das unidades de computação alocadas que estão atualmente em uso pela máquina virtual ou pelas máquinas virtuais|Nenhum|
|Entrada na rede|Rede em Faturável (Preterido)|Bytes|Total|O número de bytes faturáveis recebidos em todas as interfaces de rede pelas máquinas virtuais (tráfego de entrada) (preterido)|Nenhum|
|Saída da rede|Rede Fora de Faturável (Preterido)|Bytes|Total|O número de bytes faturáveis de saída em todas as interfaces de rede pelas máquinas virtuais (tráfego de saída) (preterido)|Nenhum|
|Bytes de leitura de disco|Bytes de leitura de disco|Bytes|Total|Bytes lidos do disco durante o período de monitoramento|Nenhum|
|Bytes de gravação de disco|Bytes de gravação de disco|Bytes|Total|Bytes gravados no disco durante o período de monitoramento|Nenhum|
|Operações de leitura de disco/Seg|Operações de leitura de disco/Seg|CountPerSecond|Média|IOPS de leitura de disco|Nenhum|
|Operações de gravação de disco/Seg|Operações de gravação de disco/Seg|CountPerSecond|Média|IOPS de gravação de disco|Nenhum|
|Créditos de CPU Restantes|Créditos de CPU Restantes|Contagem|Média|Número total de créditos disponíveis para intermitência|Nenhum|
|Créditos de CPU Consumidos|Créditos de CPU Consumidos|Contagem|Média|Número total de créditos consumido pela Máquina Virtual|Nenhum|
|Por Bytes de Leitura do Disco/s|Bytes de leitura do disco de dados/s [(preterido)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento|SlotId|
|Por Bytes de Gravação de Disco/s|Bytes de gravação do disco de dados/s [(preterido)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento|SlotId|
|Por operações de Leitura de Disco/s|Operações de leitura de disco de dados/s [(preterido)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento|SlotId|
|Por Operações de Gravação de Disco/s|Operações de gravação do disco de dados/s [(preterido)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento|SlotId|
|Por QD de Disco|Disco de dados QD [(preterido)](portal-disk-metrics-deprecation.md)|Contagem|Média|Profundidade da Fila do Disco de Dados (ou Tamanho da Fila)|SlotId|
|SO Por Bytes de Leitura do Disco/s|Bytes de leitura do disco do so/s [(preterido)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhum|
|SO Por Bytes de Gravação do Disco/s|Bytes de gravação de disco do sistema operacional/s [(preterido)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento do disco do sistema operacional|Nenhum|
|SO Por Operações de Leitura do Disco/s|Operações de leitura de disco do so/s [(preterido)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhum|
|SO Por Operações de Gravação do Disco/s|Operações de gravação de disco do sistema operacional/s [(preterido)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhum|
|Os Por QD de Disco|Disco do so QD [(preterido)](portal-disk-metrics-deprecation.md)|Contagem|Média|Profundidade da Fila do Disco do SO (ou Tamanho da Fila)|Nenhum|
|Bytes de Leitura do Disco de Dados/s|Bytes de Leitura do Disco de Dados/s (Visualização)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento|LUN|
|Bytes de Gravação do Disco de Dados/s|Bytes de Gravação do Disco de Dados/s (Visualização)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento|LUN|
|Operações de Leitura do Disco de Dados/s|Operações de Leitura do Disco de Dados/s (Visualização)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento|LUN|
|Operações de Gravação do Disco de Dados/s|Operações de Gravação do Disco de Dados/s (Visualização)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento|LUN|
|Profundidade da Fila de Disco de Dados|Profundidade da Fila de Disco de Dados (Versão prévia)|Contagem|Média|Profundidade da Fila do Disco de Dados (ou Tamanho da Fila)|LUN|
|Bytes de Leitura do Disco do SO/s|Bytes de Leitura do Disco do SO/s (Visualização)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhum|
|Bytes de Gravação do Disco do SO/s|Bytes de Gravação do Disco do SO/s (Visualização)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento do disco do sistema operacional|Nenhum|
|Operações de Leitura do Disco do SO/s|Operações de Leitura do Disco do SO/s (Visualização)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhum|
|Operações de Gravação do Disco do SO/s|Operações de Gravação do Disco do SO/s (Visualização)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Nenhum|
|Profundidade da Fila de Disco do SO|Profundidade da Fila de Disco do SO (Versão prévia)|Contagem|Média|Profundidade da Fila do Disco do SO (ou Tamanho da Fila)|Nenhum|
|Fluxos de Entrada|Fluxos de Entrada|Contagem|Média|Os fluxos de entrada são o número de fluxos atuais na direção de entrada (tráfego entrando na VM)|Nenhum|
|Fluxos de Saída|Fluxos de Saída|Contagem|Média|Fluxos de saída são o número de fluxos atuais na direção de saída (tráfego saindo da VM)|Nenhum|
|Taxa Máxima de Criação de Fluxos de Entrada|Taxa Máxima de Criação de Fluxos de Entrada|CountPerSecond|Média|A taxa de criação máxima de fluxos de entrada (tráfego entrando na VM)|Nenhum|
|Taxa Máxima de Criação de Fluxos de Saída|Taxa Máxima de Criação de Fluxos de Saída|CountPerSecond|Média|A taxa de criação máxima de fluxos de saída (tráfego saindo da VM)|Nenhum|
|Ocorrência de Leitura do Cache de Disco de Dados Premium|Ocorrência de Leitura do Cache de Disco de Dados Premium (Versão prévia)|Porcentagem|Média|Ocorrência de Leitura do Cache de Disco de Dados Premium|LUN|
|Ausência de Leitura do Cache de Disco de Dados Premium|Ausência de Leitura do Cache de Disco de Dados Premium (Versão prévia)|Porcentagem|Média|Ausência de Leitura do Cache de Disco de Dados Premium|LUN|
|Ocorrência de Leitura do Cache de Disco do SO Premium|Ocorrência de Leitura do Cache de Disco do SO Premium (Versão prévia)|Porcentagem|Média|Ocorrência de Leitura do Cache de Disco do SO Premium|Nenhum|
|Ausência de Leitura do Cache de Disco de SO Premium|Ausência de Leitura do Cache de Disco de SO Premium (Versão prévia)|Porcentagem|Média|Ausência de Leitura do Cache de Disco de SO Premium|Nenhum|
|Rede no Total|Rede no Total|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela máquina virtual ou pelas máquinas virtuais (tráfego de entrada)|Nenhum|
|Rede Fora do Total|Rede Fora do Total|Bytes|Total|O número de bytes de saída em todas as interfaces de rede pela máquina virtual ou pelas máquinas virtuais (tráfego de saída)|Nenhum|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|CpuUsage|Uso da CPU|Contagem|Média|Uso de CPU em todos os núcleos em milicores.|containerName|
|MemoryUsage|Uso de Memória|Bytes|Média|Uso de memória total em bytes.|containerName|
|NetworkBytesReceivedPerSecond|Bytes de Rede Recebidos por Segundo|Bytes|Média|Os bytes de rede recebidos por segundo.|Nenhum|
|NetworkBytesTransmittedPerSecond|Bytes de Rede Transmitidos por Segundo|Bytes|Média|Os bytes de rede transmitidos por segundo.|Nenhum|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TotalPullCount|Contagem Total de Pull|Contagem|Média|Número de pulls de imagem no total|Nenhum|
|SuccessfulPullCount|Contagem de Pull com Êxito|Contagem|Média|Número de pulls de imagem bem-sucedidos|Nenhum|
|TotalPushCount|Contagem Total de Push|Contagem|Média|Número de envios por push de imagem no total|Nenhum|
|SuccessfulPushCount|Contagem de Push com Êxito|Contagem|Média|Número de Pushes de imagem bem-sucedidos|Nenhum|
|RunDuration|Duração da Execução|Milissegundos|Total|Duração da execução em milissegundos|Nenhum|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Número total de núcleos de cpu disponíveis em um cluster gerenciado|Contagem|Média|Número total de núcleos de cpu disponíveis em um cluster gerenciado|Nenhum|
|kube_node_status_allocatable_memory_bytes|Número total de memória disponível em um cluster gerenciado|Bytes|Média|Número total de memória disponível em um cluster gerenciado|Nenhum|
|kube_pod_status_ready|Número de compartimentos no estado Pronto|Contagem|Média|Número de compartimentos no estado Pronto|namespace, Pod|
|kube_node_status_condition|Status para as várias condições de nó|Contagem|Média|Status para as várias condições de nó|condição, status, status2, nó|
|kube_pod_status_phase|Número de pods por fase|Contagem|Média|Número de pods por fase|fase, namespace, Pod|



## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|SuccessfullRequests|Solicitações bem sucedidas|Contagem|Total|Solicitações bem-sucedidas feitas pelo provedor personalizado|HttpMethod, CallPath, StatusCode|
|FailedRequests|Solicitações com falha|Contagem|Total|Obtém os logs disponíveis para provedores de recursos personalizados|HttpMethod, CallPath, StatusCode|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|NICReadThroughput|Taxa de Transferência de Leitura (Rede)|BytesPerSecond|Média|A taxa de transferência de leitura da interface de rede no dispositivo no período de relatório para todos os volumes no gateway.|InstanceName|
|NICWriteThroughput|Taxa de Transferência de Gravações (Rede)|BytesPerSecond|Média|A taxa de transferência de gravação da interface de rede no dispositivo no período de relatório para todos os volumes no gateway.|InstanceName|
|CloudReadThroughputPerShare|Taxa de Transferência de Downloads na Nuvem (Compartilhamento)|BytesPerSecond|Média|A taxa de transferência de download para o Azure de um compartilhamento durante o período de relatório.|Compartilhar|
|CloudUploadThroughputPerShare|Taxa de Transferência de Uploads na Nuvem (Compartilhamento)|BytesPerSecond|Média|A taxa de transferência de upload para o Azure de um compartilhamento durante o período de relatório.|Compartilhar|
|BytesUploadedToCloudPerShare|Bytes Carregados na Nuvem (Compartilhamento)|Bytes|Média|O número total de bytes que são carregados no Azure de um compartilhamento durante o período de relatório.|Compartilhar|
|TotalCapacity|Capacidade Total|Bytes|Média|Capacidade Total|Nenhum|
|AvailableCapacity|Capacidade Disponível|Bytes|Média|A capacidade disponível em bytes durante o período de relatório.|Nenhum|
|CloudUploadThroughput|Taxa de Transferência de Uploads na Nuvem|BytesPerSecond|Média|A taxa de transferência de upload de nuvem para o Azure durante o período de relatório.|Nenhum|
|CloudReadThroughput|Taxa de Transferência de Download na Nuvem|BytesPerSecond|Média|A taxa de transferência de download de nuvem para o Azure durante o período de relatório.|Nenhum|
|BytesUploadedToCloud|Bytes Carregados na Nuvem (Dispositivo)|Bytes|Média|O número total de bytes que são carregados no Azure de um dispositivo durante o período de relatório.|Nenhum|
|HyperVVirtualProcessorUtilization|Computação de Borda – Percentual de CPU|Porcentagem|Média|Porcentagem de uso da CPU|InstanceName|
|HyperVMemoryUtilization|Computação de Borda – Uso de Memória|Porcentagem|Média|Quantidade de RAM em uso|InstanceName|


## <a name="microsoftdatacatalogdatacatalogs"></a>Microsoft. datacatalog/datacatalogs

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|AssetDistributionByClassification|Distribuição de ativos por classificação|Contagem|Total|Indica o número de ativos com uma determinada classificação atribuída, ou seja, eles são classificados com esse rótulo.|Classificação, origem|
|AssetDistributionByStorageType|Distribuição de ativos por tipo de armazenamento|Contagem|Total|Indica o número de ativos com um determinado tipo de armazenamento.|StorageType|
|NumberOfAssetsWithClassifications|Número de ativos com pelo menos uma classificação|Contagem|Média|Indica o número de ativos com pelo menos uma classificação de marca.|Nenhum|
|ScanCancelled|Verificação cancelada|Contagem|Total|Indica o número de verificações canceladas.|Nenhum|
|ScanCompleted|Verificação concluída|Contagem|Total|Indica o número de verificações concluídas com êxito.|Nenhum|
|ScanFailed|Falha na verificação|Contagem|Total|Indica o número de verificações que falharam.|Nenhum|
|ScanTimeTaken|Tempo de verificação tomado|Segundos|Total|Indica o tempo total de verificação em segundos.|Nenhum|
|CatalogActiveUsers|Usuários ativos diariamente|Contagem|Total|Número de usuários ativos diariamente|Nenhum|
|CatalogUsage|Distribuição de uso por operação|Contagem|Total|Indique o número de operações que o usuário faz no catálogo, ou seja, acessar, Pesquisar, Glossário.|Operação|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|FailedRuns|Execuções com falha|Contagem|Total||pipelinename, ActivityName|
|SuccessfulRuns|Execuções com êxito|Contagem|Total||pipelinename, ActivityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PipelineFailedRuns|Métricas de execução do pipeline com falha|Contagem|Total||FailureType, nome|
|PipelineSucceededRuns|Métricas de execução do pipeline bem-sucedido|Contagem|Total||FailureType, nome|
|PipelineCancelledRuns|Métricas de execução do pipeline canceladas|Contagem|Total||FailureType, nome|
|ActivityFailedRuns|Métricas de execução de atividades com falha|Contagem|Total||ActivityType, Pipelinename, Falhatype, nome|
|ActivitySucceededRuns|Métricas de execução de atividades bem-sucedidas|Contagem|Total||ActivityType, Pipelinename, Falhatype, nome|
|ActivityCancelledRuns|Métricas de execuções de atividades canceladas|Contagem|Total||ActivityType, Pipelinename, Falhatype, nome|
|TriggerFailedRuns|Métricas de execuções do gatilho com falha|Contagem|Total||Nome, Falhatype|
|TriggerSucceededRuns|Métricas de execuções do gatilho bem-sucedidas|Contagem|Total||Nome, Falhatype|
|TriggerCancelledRuns|Métricas de execuções do gatilho canceladas|Contagem|Total||Nome, Falhatype|
|IntegrationRuntimeCpuPercentage|Utilização de CPU de runtime de integração|Porcentagem|Média||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAvailableMemory|Memória disponível de runtime de integração|Bytes|Média||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAverageTaskPickupDelay|Duração da fila do runtime de integração|Segundos|Média||IntegrationRuntimeName|
|IntegrationRuntimeQueueLength|Tamanho da fila do runtime de integração|Contagem|Média||IntegrationRuntimeName|
|IntegrationRuntimeAvailableNodeNumber|Contagem de nós disponíveis do Integration Runtime|Contagem|Média||IntegrationRuntimeName|
|MaxAllowedResourceCount|Contagem máxima de entidades permitidas|Contagem|Máximo||Nenhum|
|MaxAllowedFactorySizeInGbUnits|Tamanho máximo de alocador permitido (unidade GB)|Contagem|Máximo||Nenhum|
|ResourceCount|Contagem total de entidades|Contagem|Máximo||Nenhum|
|FactorySizeInGbUnits|Tamanho total do alocador (unidade GB)|Contagem|Máximo||Nenhum|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|JobEndedSuccess|Trabalhos com êxito|Contagem|Total|Contagem de trabalhos com êxito.|Nenhum|
|JobEndedFailure|Trabalhos com falha|Contagem|Total|Contagem de trabalhos com falha.|Nenhum|
|JobEndedCancelled|Trabalhos cancelados|Contagem|Total|Contagem de trabalhos cancelados.|Nenhum|
|JobAUEndedSuccess|Tempo de AU com êxito|Segundos|Total|Tempo total de AU dos trabalhos com êxito.|Nenhum|
|JobAUEndedFailure|Tempo de AU com falha|Segundos|Total|Tempo total de AU dos trabalhos com falha.|Nenhum|
|JobAUEndedCancelled|Tempo de AU cancelada|Segundos|Total|Tempo total de AU dos trabalhos cancelados.|Nenhum|
|JobStage|Trabalhos no estágio|Contagem|Total|Número de trabalhos em cada estágio.|Nenhum|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TotalStorage|Armazenamento total|Bytes|Máximo|Quantidade total de dados armazenados na conta.|Nenhum|
|DataWritten|Dados gravados|Bytes|Total|Quantidade total de dados gravados na conta.|Nenhum|
|DataRead|Leitura de dados|Bytes|Total|Quantidade total de dados lidos na conta.|Nenhum|
|WriteRequests|Solicitações de gravação|Contagem|Total|Contagem de solicitações de gravação de dados para a conta.|Nenhum|
|ReadRequests|Solicitações de leitura|Contagem|Total|Contagem solicitações de leitura de dados para a conta.|Nenhum|


## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/accounts

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ShareCount|Compartilhamentos enviados|Contagem|Máximo|Número de compartilhamentos enviados na conta|ShareName|
|ShareSubscriptionCount|Compartilhamentos recebidos|Contagem|Máximo|Número de compartilhamentos recebidos na conta|ShareSubscriptionName|
|SucceededShareSynchronizations|Instantâneos de compartilhamento enviados com êxito|Contagem|Contagem|Número de instantâneos de compartilhamento enviados com êxito na conta|Nenhum|
|FailedShareSynchronizations|Instantâneos com falha no compartilhamento enviado|Contagem|Contagem|Número de instantâneos de compartilhamento enviados com falha na conta|Nenhum|
|SucceededShareSubscriptionSynchronizations|Instantâneos recebidos com êxito do compartilhamento|Contagem|Contagem|Número de instantâneos recebidos com êxito no compartilhamento na conta|Nenhum|
|FailedShareSubscriptionSynchronizations|Instantâneos de falha de compartilhamento recebidos|Contagem|Contagem|Número de instantâneos de compartilhamento recebidos com falha na conta|Nenhum|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
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
|active_connections|Conexões ativas|Contagem|Média|Conexões ativas|Nenhum|
|connections_failed|Conexões com falha|Contagem|Total|Conexões com falha|Nenhum|
|seconds_behind_master|Retardo de replicação em segundos|Contagem|Máximo|Retardo de replicação em segundos|Nenhum|
|backup_storage_used|Armazenamento de Backup usado|Bytes|Média|Armazenamento de Backup usado|Nenhum|
|network_bytes_egress|Saída da rede|Bytes|Total|Saída de Rede em conexões ativas|Nenhum|
|network_bytes_ingress|Entrada na rede|Bytes|Total|Entrada de Rede em conexões ativas|Nenhum|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
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
|active_connections|Conexões ativas|Contagem|Média|Conexões ativas|Nenhum|
|connections_failed|Conexões com falha|Contagem|Total|Conexões com falha|Nenhum|
|seconds_behind_master|Retardo de replicação em segundos|Contagem|Máximo|Retardo de replicação em segundos|Nenhum|
|backup_storage_used|Armazenamento de Backup usado|Bytes|Média|Armazenamento de Backup usado|Nenhum|
|network_bytes_egress|Saída da rede|Bytes|Total|Saída de Rede em conexões ativas|Nenhum|
|network_bytes_ingress|Entrada na rede|Bytes|Total|Entrada de Rede em conexões ativas|Nenhum|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
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
|active_connections|Conexões ativas|Contagem|Média|Conexões ativas|Nenhum|
|connections_failed|Conexões com falha|Contagem|Total|Conexões com falha|Nenhum|
|backup_storage_used|Armazenamento de Backup usado|Bytes|Média|Armazenamento de Backup usado|Nenhum|
|network_bytes_egress|Saída da rede|Bytes|Total|Saída de Rede em conexões ativas|Nenhum|
|network_bytes_ingress|Entrada na rede|Bytes|Total|Entrada de Rede em conexões ativas|Nenhum|
|pg_replica_log_delay_in_seconds|Retardo da Réplica|Segundos|Máximo|Atraso de réplica em segundos|Nenhum|
|pg_replica_log_delay_in_bytes|Retardo Máximo entre Réplicas|Bytes|Máximo|Atraso em bytes da réplica mais defasante|Nenhum|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Porcentagem de CPU|Porcentagem|Média|Porcentagem de CPU|Nenhum|
|memory_percent|Porcentagem de memória|Porcentagem|Média|Porcentagem de memória|Nenhum|
|iops|IOPS|Contagem|Média|Operações de e/s por segundo|Nenhum|
|storage_percent|Porcentagem de armazenamento|Porcentagem|Média|Porcentagem de armazenamento|Nenhum|
|storage_used|Armazenamento usado|Bytes|Média|Armazenamento usado|Nenhum|
|active_connections|Conexões ativas|Contagem|Média|Conexões ativas|Nenhum|
|network_bytes_egress|Saída da rede|Bytes|Total|Saída de Rede em conexões ativas|Nenhum|
|network_bytes_ingress|Entrada na rede|Bytes|Total|Entrada de Rede em conexões ativas|Nenhum|


## <a name="microsoftdbforpostgresqlsingleservers"></a>Microsoft. DBforPostgreSQL/singleservers

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Porcentagem de CPU|Porcentagem|Média|Porcentagem de CPU|Nenhum|
|memory_percent|Porcentagem de memória|Porcentagem|Média|Porcentagem de memória|Nenhum|
|iops|IOPS|Contagem|Média|Operações de e/s por segundo|Nenhum|
|storage_percent|Porcentagem de armazenamento|Porcentagem|Média|Porcentagem de armazenamento|Nenhum|
|storage_used|Armazenamento usado|Bytes|Média|Armazenamento usado|Nenhum|
|active_connections|Conexões ativas|Contagem|Média|Conexões ativas|Nenhum|
|network_bytes_egress|Saída da rede|Bytes|Total|Saída de Rede em conexões ativas|Nenhum|
|network_bytes_ingress|Entrada na rede|Bytes|Total|Entrada de Rede em conexões ativas|Nenhum|
|connections_failed|Conexões com falha|Contagem|Total|Conexões com falha|Nenhum|
|connections_succeeded|Conexões com êxito|Contagem|Total|Conexões com êxito|Nenhum|
|maximum_used_transactionIDs|Máximo de IDs de transação usadas|Contagem|Média|Máximo de IDs de transação usadas|Nenhum|





## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Tentativas de envio de mensagem de telemetria|Contagem|Total|Número de tentativas de envio de mensagens de telemetria do dispositivo para nuvem para o hub IoT|Nenhum|
|d2c.telemetry.ingress.success|Mensagens de telemetria enviadas|Contagem|Total|Número de mensagens de telemetria do dispositivo para nuvem enviadas com êxito para o hub IoT|Nenhum|
|c2d.commands.egress.complete.success|Entregas de mensagem C2D concluídas|Contagem|Total|Número de entregas de mensagem da nuvem para o dispositivo concluídas com êxito pelo dispositivo|Nenhum|
|c2d.commands.egress.abandon.success|Mensagens C2D abandonadas|Contagem|Total|Número de mensagens da nuvem para o dispositivo abandonadas pelo dispositivo|Nenhum|
|c2d.commands.egress.reject.success|Mensagens C2D rejeitadas|Contagem|Total|Número de mensagens da nuvem para o dispositivo rejeitadas pelo dispositivo|Nenhum|
|C2DMessagesExpired|Mensagens C2D expiradas (versão prévia)|Contagem|Total|Número de mensagens de nuvem para o dispositivo expiradas|Nenhum|
|devices.totalDevices|Total de dispositivos (preterido)|Contagem|Total|Número de dispositivos registrados para o seu hub IoT|Nenhum|
|devices.connectedDevices.allProtocol|Dispositivos conectados (preteridos) |Contagem|Total|Número de dispositivos registrados ao seu hub IoT|Nenhum|
|d2c.telemetry.egress.success|Roteamento: mensagens de telemetria entregues|Contagem|Total|O número de vezes que as mensagens foram entregues com êxito a todos os pontos de extremidade usando o roteamento do Hub IoT. Se uma mensagem é encaminhada para vários pontos de extremidade, esse valor aumenta em um para cada entrega bem-sucedida. Se uma mensagem é entregue ao mesmo ponto de extremidade várias vezes, esse valor aumenta em um para cada entrega bem-sucedida.|Nenhum|
|d2c.telemetry.egress.dropped|Roteamento: mensagens de telemetria removidas |Contagem|Total|O número de vezes que as mensagens foram removidas pelo roteamento do Hub IoT devido à inatividade dos pontos de extremidade. Esse valor não conta as mensagens entregues à rota de fallback, pois as mensagens removidas não são entregues.|Nenhum|
|d2c.telemetry.egress.orphaned|Roteamento: mensagens de telemetria órfãs |Contagem|Total|O número de vezes que as mensagens ficaram órfãos do roteamento do Hub IoT porque não correspondiam a nenhuma regra de roteamento (incluindo a regra de fallback). |Nenhum|
|d2c.telemetry.egress.invalid|Roteamento: mensagens de telemetria incompatíveis|Contagem|Total|O número de vezes que o roteamento do Hub IoT não conseguiu entregar mensagens devido a uma incompatibilidade com o ponto de extremidade. Esse valor não inclui novas tentativas.|Nenhum|
|d2c.telemetry.egress.fallback|Roteamento: mensagens entregues ao fallback|Contagem|Total|O número de vezes que o roteamento do Hub IoT entregou mensagens ao ponto de extremidade associado à rota de fallback.|Nenhum|
|d2c.endpoints.egress.eventHubs|Roteamento: mensagens entregues ao Hub de Eventos|Contagem|Total|O número de vezes que o roteamento do Hub IoT entregou com êxito mensagens aos pontos de extremidade do Hub de Eventos.|Nenhum|
|d2c.endpoints.latency.eventHubs|Roteamento: latência de mensagem para o Hub de Eventos|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem no Hub IoT e a entrada da mensagem em um ponto de extremidade do Hub de Eventos.|Nenhum|
|d2c.endpoints.egress.serviceBusQueues|Roteamento: mensagens entregues à Fila do Barramento de Serviço|Contagem|Total|O número de vezes que o roteamento do Hub IoT entregou com êxito mensagens aos pontos de extremidade da fila do Barramento de Serviço.|Nenhum|
|d2c.endpoints.latency.serviceBusQueues|Roteamento: latência de mensagem para a Fila do Barramento de Serviço|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem no Hub IoT e a entrada da mensagem de telemetria em um ponto de extremidade da fila do Barramento de Serviço.|Nenhum|
|d2c.endpoints.egress.serviceBusTopics|Roteamento: mensagens entregues ao Tópico do Barramento de Serviço|Contagem|Total|O número de vezes que o roteamento do Hub IoT entregou com êxito mensagens aos pontos de extremidade do tópico do Barramento de Serviço.|Nenhum|
|d2c.endpoints.latency.serviceBusTopics|Roteamento: latência de mensagem para o Tópico do Barramento de Serviço|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem no Hub IoT e a entrada da mensagem de telemetria em um ponto de extremidade do tópico do Barramento de Serviço.|Nenhum|
|d2c.endpoints.egress.builtIn.events|Roteamento: mensagens entregues a mensagens/eventos|Contagem|Total|O número de vezes que o roteamento do Hub IoT entregou com êxito mensagens ao ponto de extremidade interno (mensagens/eventos).|Nenhum|
|d2c.Endpoints.Latency.builtIn.Events|Roteamento: latência de mensagem para mensagens/eventos|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem no Hub IoT e a entrada da mensagem de telemetria no ponto de extremidade interno (mensagens/eventos).|Nenhum|
|d2c.endpoints.egress.storage|Roteamento: mensagens entregues ao armazenamento|Contagem|Total|O número de vezes que o roteamento do Hub IoT entregou com êxito mensagens aos pontos de extremidade de armazenamento.|Nenhum|
|d2c.endpoints.latency.storage|Roteamento: latência de mensagem para armazenamento|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem no Hub IoT e a entrada da mensagem de telemetria em um ponto de extremidade de armazenamento.|Nenhum|
|d2c.endpoints.egress.storage.bytes|Roteamento: dados entregues ao armazenamento|Bytes|Total|A quantidade de dados (bytes) de roteamento do Hub IoT entregue aos pontos de extremidade de armazenamento.|Nenhum|
|d2c.endpoints.egress.storage.blobs|Roteamento: blobs entregues ao armazenamento|Contagem|Total|O número de vezes que o roteamento do Hub IoT entregou blobs aos pontos de extremidade de armazenamento.|Nenhum|
|EventGridDeliveries|Entregas da grade de eventos (visualização)|Contagem|Total|O número de eventos do Hub IoT publicados na grade de eventos. Use a dimensão de resultado para o número de solicitações bem-sucedidas e com falha. A dimensão EventType mostra o tipo de evento ( https://aka.ms/ioteventgrid) .|ResourceId, resultado, EventType|
|EventGridLatency|Latência da Grade de Eventos (versão prévia)|Milissegundos|Média|A latência média (milissegundos) de quando o evento do Hub IOT foi gerado quando o evento foi publicado na grade de eventos. Esse número é uma média entre todos os tipos de evento. Use a dimensão EventType para ver a latência de um tipo específico de evento.|ResourceId, EventType|
|RoutingDeliveries|Entregas de roteamento (visualização)|Milissegundos|Total|O número de vezes que o Hub IoT tentou entregar mensagens a todos os pontos de extremidade usando o roteamento. Para ver o número de tentativas bem-sucedidas ou com falha, use a dimensão de resultado. Para ver o motivo da falha, como inválida, descartada ou órfã, use a dimensão FailureReasonCategory. Você também pode usar as dimensões EndpointName e EndpointType para entender quantas mensagens foram entregues aos diferentes pontos de extremidade. O valor da métrica aumenta em uma para cada tentativa de entrega, incluindo se a mensagem é entregue a vários pontos de extremidade ou se a mensagem é entregue ao mesmo ponto de extremidades várias vezes.|ResourceId, EndpointType, EndpointName, FailureReasonCategory, resultado, roteamento|
|RoutingDeliveryLatency|Latência de entrega de roteamento (visualização)|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem e o Hub IoT e a entrada da mensagem de telemetria em um ponto de extremidade. Você pode usar as dimensões EndpointName e EndpointType para entender a latência para seus pontos de extremidade diferentes.|ResourceId, EndpointType, EndpointName, roteamento|
|d2c.twin.read.success|Leituras de gêmeos dos dispositivos bem-sucedidas|Contagem|Total|A contagem de todas as leituras de gêmeos iniciadas pelo dispositivo bem-sucedidas.|Nenhum|
|d2c.twin.read.failure|Leituras de gêmeos dos dispositivos com falhas|Contagem|Total|A contagem de todas as leituras de gêmeos iniciadas pelo dispositivo com falhas.|Nenhum|
|d2c.twin.read.size|Tamanho da resposta das leituras de gêmeos dos dispositivos|Bytes|Média|A média, o mínimo e o máximo de todas as leituras de gêmeos iniciadas pelo dispositivo bem-sucedidas.|Nenhum|
|d2c.twin.update.success|Atualizações de gêmeos dos dispositivos bem-sucedidas|Contagem|Total|A contagem de todas as atualizações de gêmeos iniciadas pelo dispositivo bem-sucedidas.|Nenhum|
|d2c.twin.update.failure|Atualizações de gêmeos dos dispositivos com falhas|Contagem|Total|A contagem de todas as atualizações de gêmeos iniciadas pelo dispositivo.|Nenhum|
|d2c.twin.update.size|Tamanho das atualizações de gêmeos dos dispositivos|Bytes|Média|A média, o mínimo e o máximo de todas as atualizações de gêmeos iniciadas pelo dispositivo bem-sucedidas.|Nenhum|
|c2d.methods.success|Invocações de método diretas bem-sucedidas|Contagem|Total|A contagem de todas as chamadas de método diretas bem-sucedidas.|Nenhum|
|c2d.methods.failure|Invocações de método diretas com falhas|Contagem|Total|A contagem de todas as chamadas de método diretas com falhas.|Nenhum|
|c2d.methods.requestSize|Tamanho da solicitação das invocações de método diretas|Bytes|Média|A média, o mínimo e o máximo de todas as solicitações de método diretas bem-sucedidas.|Nenhum|
|c2d.methods.responseSize|Tamanho da resposta das invocações de método diretas|Bytes|Média|A média, o mínimo e o máximo de todas as respostas de método diretas bem-sucedidas.|Nenhum|
|c2d.twin.read.success|Leituras de gêmeos de back-end bem-sucedidas|Contagem|Total|A contagem de todas as leituras de gêmeos bem-sucedidas iniciadas pelo back-end.|Nenhum|
|c2d.twin.read.failure|Leituras de gêmeos de back-end com falhas|Contagem|Total|A contagem de todas as falhas de leituras de gêmeos iniciadas pelo back-end.|Nenhum|
|c2d.twin.read.size|Tamanho da resposta das leituras de gêmeos de back-end|Bytes|Média|A média, o mínimo e o máximo de todas as leituras de gêmeos bem-sucedidas iniciadas pelo back-end.|Nenhum|
|c2d.twin.update.success|Atualizações de gêmeos de back-end bem-sucedidas|Contagem|Total|A contagem de todas as atualizações de gêmeos iniciadas pelo back-end bem-sucedidas.|Nenhum|
|c2d.twin.update.failure|Atualizações de gêmeos de back-end com falhas|Contagem|Total|A contagem de todas as falhas de atualizações de gêmeos iniciadas pelo back-end.|Nenhum|
|c2d.twin.update.size|Tamanho das atualizações de gêmeos de back-end|Bytes|Média|A média, o mínimo e o máximo de todas as atualizações de gêmeos iniciadas pelo back-end bem-sucedidas.|Nenhum|
|twinQueries.success|Consultas de gêmeos bem-sucedidas|Contagem|Total|A contagem de todas as consultas de gêmeos bem-sucedidas.|Nenhum|
|twinQueries.failure|Consultas de gêmeos com falhas|Contagem|Total|A contagem de todas as consultas de gêmeos com falhas.|Nenhum|
|twinQueries.resultSize|Tamanho do resultado das consultas de gêmeos|Bytes|Média|A média, o mínimo e o máximo do tamanho do resultado de todas as consultas de gêmeos bem-sucedidas.|Nenhum|
|jobs.createTwinUpdateJob.success|Criações de trabalhos de atualização de gêmeos bem-sucedidas|Contagem|Total|A contagem de todos os trabalhos de criação de atualização de gêmeos bem-sucedidos.|Nenhum|
|jobs.createTwinUpdateJob.failure|Criações de trabalhos de atualização de gêmeos com falhas|Contagem|Total|A contagem de todos os trabalhos de criação de atualização de gêmeos com falhas.|Nenhum|
|jobs.createDirectMethodJob.success|Criações de trabalhos de invocação de método bem-sucedidas|Contagem|Total|A contagem de todas as criações de trabalhos de invocação de método direto bem-sucedidas.|Nenhum|
|jobs.createDirectMethodJob.failure|Criações de trabalhos de invocação de método com falhas|Contagem|Total|A contagem de todas as criações de trabalhos de invocação de método direto com falhas.|Nenhum|
|jobs.listJobs.success|Chamadas para listar trabalhos bem-sucedidas|Contagem|Total|A contagem de todas as chamadas para listar trabalhos bem-sucedidas.|Nenhum|
|jobs.listJobs.failure|Chamadas para listar trabalhos com falhas|Contagem|Total|A contagem de todas as chamadas para listar trabalhos com falhas.|Nenhum|
|jobs.cancelJob.success|Cancelamentos de trabalho bem-sucedidos|Contagem|Total|A contagem de todas as chamadas para cancelar um trabalho bem-sucedidas.|Nenhum|
|jobs.cancelJob.failure|Cancelamentos de trabalho com falhas|Contagem|Total|A contagem de todas as chamadas para cancelar um trabalho com falhas.|Nenhum|
|jobs.queryJobs.success|Consultas de trabalho bem-sucedidas|Contagem|Total|A contagem de todas as chamadas para consultas de trabalho bem-sucedidas.|Nenhum|
|jobs.queryJobs.failure|Consultas de trabalho com falhas|Contagem|Total|A contagem de todas as chamadas para consultas de trabalho com falhas.|Nenhum|
|jobs.completed|Trabalhos concluídos|Contagem|Total|A contagem de todos os trabalhos concluídos.|Nenhum|
|jobs.failed|Trabalhos com falha|Contagem|Total|A contagem de todos os trabalhos com falha.|Nenhum|
|d2c.telemetry.ingress.sendThrottle|Número de erros de limitação|Contagem|Total|Número de erros de limitação devido a limitações da taxa de transferência do dispositivo|Nenhum|
|dailyMessageQuotaUsed|Número total de mensagens usadas|Contagem|Média|Número total de mensagens usadas hoje|Nenhum|
|deviceDataUsage|Uso total de dados do dispositivo|Bytes|Total|Bytes transferidos entre todos os dispositivos conectados ao lotHub|Nenhum|
|deviceDataUsageV2|Uso total de dados de dispositivos (versão prévia)|Bytes|Total|Bytes transferidos entre todos os dispositivos conectados ao lotHub|Nenhum|
|totalDeviceCount|Total de dispositivos (versão prévia)|Contagem|Média|Número de dispositivos registrados para o seu hub IoT|Nenhum|
|connectedDeviceCount|Dispositivos conectados (versão prévia)|Contagem|Média|Número de dispositivos registrados ao seu hub IoT|Nenhum|
|configurações|Métricas de configuração|Contagem|Total|Métricas para operações de configuração|Nenhum|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|RegistrationAttempts|Tentativas de Registro|Contagem|Total|Número de tentativas de registros do dispositivo|ProvisioningServiceName, IotHubName, status|
|DeviceAssignments|Dispositivos atribuídos|Contagem|Total|Número de dispositivos atribuídos a um hub IoT|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Tentativas de atestado|Contagem|Total|Número de tentativas de atestado do dispositivo|ProvisioningServiceName, status, protocolo|




## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Addregion|Região adicionada|Contagem|Contagem|Região adicionada|Região|
|AvailableStorage|Armazenamento disponível|Bytes|Total|Armazenamento total disponível relatado à granularidade de 5 minutos|CollectionName, DatabaseName, região|
|CassandraConnectionClosures|Fechamentos de Conexão do Cassandra|Contagem|Total|Número de conexões Cassandra que foram fechadas, relatadas a uma granularidade de 1 minuto|APIType, região, ClosureReason|
|CassandraKeyspaceDelete|Cassandra keyspace excluído|Contagem|Contagem|Cassandra keyspace excluído|ResourceName, ApiKind, ApiKindResourceType, OperationType|
|CassandraKeyspaceThroughputUpdate|Taxa de transferência de keyspace Cassandra atualizada|Contagem|Contagem|Taxa de transferência de keyspace Cassandra atualizada|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|CassandraKeyspaceUpdate|Cassandra keyspace atualizado|Contagem|Contagem|Cassandra keyspace atualizado|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|CassandraRequestCharges|Encargos de Solicitação do Cassandra|Contagem|Total|RUs consumido para solicitações Cassandra feitas|APIType, DatabaseName, CollectionName, região, OperationType, ResourceType|
|CassandraRequests|Solicitações do Cassandra|Contagem|Contagem|Número de solicitações Cassandra feitas|APIType, DatabaseName, CollectionName, região, OperationType, ResourceType, ErrorCode|
|CassandraTableDelete|Tabela Cassandra excluída|Contagem|Contagem|Tabela Cassandra excluída|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, OperationType|
|CassandraTableThroughputUpdate|Taxa de transferência de tabela Cassandra atualizada|Contagem|Contagem|Taxa de transferência de tabela Cassandra atualizada|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|CassandraTableUpdate|Tabela Cassandra atualizada|Contagem|Contagem|Tabela Cassandra atualizada|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|CreateAccount|Conta criada|Contagem|Contagem|Conta criada|Nenhum|
|DataUsage|Uso de dados|Bytes|Total|Uso total de dados relatados à granularidade de 5 minutos|CollectionName, DatabaseName, região|
|DeleteAccount|Conta excluída|Contagem|Contagem|Conta excluída|Nenhum|
|DocumentCount|Contagem de documentos|Contagem|Total|Contagem total de documentos relatados à granularidade de 5 minutos|CollectionName, DatabaseName, região|
|DocumentQuota|Cota de documento|Bytes|Total|Cota de armazenamento total relatada à granularidade de 5 minutos|CollectionName, DatabaseName, região|
|GremlinDatabaseDelete|Banco de dados Gremlin excluído|Contagem|Contagem|Banco de dados Gremlin excluído|ResourceName, ApiKind, ApiKindResourceType, OperationType|
|GremlinDatabaseThroughputUpdate|Taxa de transferência do banco de dados Gremlin atualizada|Contagem|Contagem|Taxa de transferência do banco de dados Gremlin atualizada|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|GremlinDatabaseUpdate|Banco de dados Gremlin atualizado|Contagem|Contagem|Banco de dados Gremlin atualizado|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|GremlinGraphDelete|Grafo Gremlin excluído|Contagem|Contagem|Grafo Gremlin excluído|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, OperationType|
|GremlinGraphThroughputUpdate|Taxa de transferência do grafo Gremlin atualizada|Contagem|Contagem|Taxa de transferência do grafo Gremlin atualizada|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|GremlinGraphUpdate|Grafo Gremlin atualizado|Contagem|Contagem|Grafo Gremlin atualizado|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|IndexUsage|Uso do índice|Bytes|Total|Uso total do índice relatado à granularidade de 5 minutos|CollectionName, DatabaseName, região|
|MetadataRequests|MetadataRequests|Contagem|Contagem|Contagem de solicitações de metadados. O Cosmos DB mantém uma coleção de metadados do sistema para cada conta, o que permite que você enumere coleções, bancos de dados, etc, e suas configurações, gratuitamente.|DatabaseName, CollectionName, região, StatusCode, função|
|MongoCollectionDelete|Coleção Mongo excluída|Contagem|Contagem|Coleção Mongo excluída|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, OperationType|
|MongoCollectionThroughputUpdate|Taxa de transferência de coleção Mongo atualizada|Contagem|Contagem|Taxa de transferência de coleção Mongo atualizada|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|MongoCollectionUpdate|Coleção Mongo atualizada|Contagem|Contagem|Coleção Mongo atualizada|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|MongoDBDatabaseUpdate|Banco de dados Mongo atualizado|Contagem|Contagem|Banco de dados Mongo atualizado|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|MongoDatabaseDelete|Banco de dados Mongo excluído|Contagem|Contagem|Banco de dados Mongo excluído|ResourceName, ApiKind, ApiKindResourceType, OperationType|
|MongoDatabaseThroughputUpdate|Taxa de transferência do banco de dados Mongo atualizada|Contagem|Contagem|Taxa de transferência do banco de dados Mongo atualizada|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|MongoRequestCharge|MongoRequestCharge|Contagem|Total|Unidades Solicitadas do Mongo Consumidas|DatabaseName, CollectionName, região, CommandName, ErrorCode, status|
|MongoRequests|Solicitações do Mongo|Contagem|Contagem|Número de Solicitações do Mongo Feitas|DatabaseName, CollectionName, região, CommandName, ErrorCode, status|
|MongoRequestsCount|Taxa de Solicitação do Mongo|CountPerSecond|Média|Contagem de solicitações Mongo por segundo|DatabaseName, CollectionName, região, CommandName, ErrorCode|
|MongoRequestsDelete|Taxa de Solicitação de Exclusão do Mongo|CountPerSecond|Média|Solicitação de exclusão de Mongo por segundo|DatabaseName, CollectionName, região, CommandName, ErrorCode|
|MongoRequestsInsert|Taxa de Solicitação de Inserção do Mongo|CountPerSecond|Média|Contagem de inserção de Mongo por segundo|DatabaseName, CollectionName, região, CommandName, ErrorCode|
|MongoRequestsQuery|Taxa de Solicitação de Consulta do Mongo|CountPerSecond|Média|Solicitação de consulta Mongo por segundo|DatabaseName, CollectionName, região, CommandName, ErrorCode|
|MongoRequestsUpdate|Taxa de Solicitação de Atualização do Mongo|CountPerSecond|Média|Solicitação de atualização de Mongo por segundo|DatabaseName, CollectionName, região, CommandName, ErrorCode|
|NormalizedRUConsumption|Consumo de RU normalizado|Porcentagem|Máximo|Porcentagem máxima de consumo de RU por minuto|CollectionName, DatabaseName, região|
|ProvisionedThroughput|Taxa de transferência provisionada|Contagem|Máximo|Taxa de transferência provisionada|DatabaseName, CollectionName|
|RegionFailover|Falha na região|Contagem|Contagem|Falha na região|Nenhum|
|RemoveRegion|Região removida|Contagem|Contagem|Região removida|Região|
|ReplicationLatency|Latência de Replicação P99|MilliSeconds|Média|Latência de replicação P99 nas regiões de origem e de destino para conta habilitada geograficamente|SourceRegion,TargetRegion|
|ServerSideLatency|Latência do lado do servidor|MilliSeconds|Média|Latência do lado do servidor|DatabaseName, CollectionName, região, ConnectionMode, OperationType, PublicAPIType|
|ServiceAvailability|Disponibilidade do serviço|Porcentagem|Média|Disponibilidade de solicitações de conta em uma hora, granularidade de dia ou mês|Nenhum|
|SqlContainerDelete|Contêiner SQL excluído|Contagem|Contagem|Contêiner SQL excluído|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, OperationType|
|SqlContainerThroughputUpdate|Produtividade do contêiner SQL atualizada|Contagem|Contagem|Produtividade do contêiner SQL atualizada|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|SqlContainerUpdate|Contêiner SQL atualizado|Contagem|Contagem|Contêiner SQL atualizado|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|SqlDatabaseDelete|Banco de dados SQL excluído|Contagem|Contagem|Banco de dados SQL excluído|ResourceName, ApiKind, ApiKindResourceType, OperationType|
|SqlDatabaseThroughputUpdate|Produtividade do banco de dados SQL atualizada|Contagem|Contagem|Produtividade do banco de dados SQL atualizada|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|SqlDatabaseUpdate|Banco de dados SQL atualizado|Contagem|Contagem|Banco de dados SQL atualizado|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|TableTableDelete|Tabela do azuretable excluída|Contagem|Contagem|Tabela do azuretable excluída|ResourceName, ApiKind, ApiKindResourceType, OperationType|
|TableTableThroughputUpdate|Taxa de transferência de tabela do azuretable atualizada|Contagem|Contagem|Taxa de transferência de tabela do azuretable atualizada|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|TableTableUpdate|Tabela do azuretable atualizada|Contagem|Contagem|Tabela do azuretable atualizada|ResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|TotalRequestUnits|Total de Unidades Solicitadas|Contagem|Total|Unidades Solicitadas Consumidas|DatabaseName, CollectionName, região, StatusCode, OperationType, status|
|TotalRequests|Total de Solicitações|Contagem|Contagem|Número de solicitações feitas|DatabaseName, CollectionName, região, StatusCode, OperationType, status|
|UpdateAccountKeys|Chaves de conta atualizadas|Contagem|Contagem|Chaves de conta atualizadas|KeyType|
|UpdateAccountNetworkSettings|Configurações de rede da conta atualizadas|Contagem|Contagem|Configurações de rede da conta atualizadas|Nenhum|
|UpdateAccountReplicationSettings|Configurações de replicação de conta atualizadas|Contagem|Contagem|Configurações de replicação de conta atualizadas|Nenhum|
|UpdateDiagnosticsSettings|Configurações de diagnóstico da conta atualizadas|Contagem|Contagem|Configurações de diagnóstico da conta atualizadas|DiagnosticSettingsName, ResourceGroupName|



## <a name="microsoftenterpriseknowledgegraphservices"></a>Microsoft.EnterpriseKnowledgeGraph/services

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TransactionCount|Contagem de Transações|Contagem|Contagem|Contagem total de transações|TransactionCount|
|SuccessCount|Contagem Bem-sucedida|Contagem|Contagem|Contagem de transações com êxito|SuccessCount|
|FailureCount|Falha na Contagem|Contagem|Contagem|Contagem de transações com falha|FailureCount|
|SuccessLatency|Latência de Êxito|MilliSeconds|Média|Latência de transações bem-sucedidas|SuccessCount|

## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventos publicados|Contagem|Total|Total de eventos publicados neste tópico|Tópico|
|PublishFailCount|Publicar Eventos com Falha|Contagem|Total|Total de eventos com falha ao publicar neste tópico|Tópico, ErrorType, erro|
|PublishSuccessLatencyInMs|Publicar Latência de Êxito|Milissegundos|Total|Latência de êxito de publicação em milissegundos|Nenhum|
|MatchedEventCount|Eventos correspondentes|Contagem|Total|Total de eventos correspondentes a essa assinatura de evento|Tópico, EventSubscriptionName, DomainEventSubscriptionName|
|DeliveryAttemptFailCount|Eventos com falha de entrega|Contagem|Total|Total de eventos não entregues a essa assinatura de evento|Tópico, EventSubscriptionName, DomainEventSubscriptionName, erro, ErrorType|
|DeliverySuccessCount|Eventos entregues|Contagem|Total|Total de eventos entregues a essa assinatura de evento|Tópico, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|Duração do processamento de destino|Milissegundos|Média|Duração do processamento de destino em milissegundos|Tópico, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Eventos removidos|Contagem|Total|Total de eventos removidos correspondentes a essa assinatura de evento|Tópico, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|DeadLetteredCount|Eventos de mensagens mortas|Contagem|Total|Total de eventos de mensagens mortas correspondentes a essa assinatura de evento|Tópico, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventos publicados|Contagem|Total|Total de eventos publicados neste tópico|Nenhum|
|PublishFailCount|Publicar Eventos com Falha|Contagem|Total|Total de eventos com falha ao publicar neste tópico|ErrorType, erro|
|UnmatchedEventCount|Eventos sem correspondência|Contagem|Total|Total de eventos sem correspondência com nenhuma das assinaturas de evento para este tópico|Nenhum|
|PublishSuccessLatencyInMs|Publicar Latência de Êxito|Milissegundos|Total|Latência de êxito de publicação em milissegundos|Nenhum|
|MatchedEventCount|Eventos correspondentes|Contagem|Total|Total de eventos correspondentes a essa assinatura de evento|EventSubscriptionName|
|DeliveryAttemptFailCount|Eventos com falha de entrega|Contagem|Total|Total de eventos não entregues a essa assinatura de evento|Erro, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Eventos entregues|Contagem|Total|Total de eventos entregues a essa assinatura de evento|EventSubscriptionName|
|DestinationProcessingDurationInMs|Duração do processamento de destino|Milissegundos|Média|Duração do processamento de destino em milissegundos|EventSubscriptionName|
|DroppedEventCount|Eventos removidos|Contagem|Total|Total de eventos removidos correspondentes a essa assinatura de evento|DropReason,EventSubscriptionName|
|DeadLetteredCount|Eventos de mensagens mortas|Contagem|Total|Total de eventos de mensagens mortas correspondentes a essa assinatura de evento|DeadLetterReason,EventSubscriptionName|

## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventos publicados|Contagem|Total|Total de eventos publicados neste tópico|Nenhum|
|PublishFailCount|Publicar Eventos com Falha|Contagem|Total|Total de eventos com falha ao publicar neste tópico|ErrorType, erro|
|UnmatchedEventCount|Eventos sem correspondência|Contagem|Total|Total de eventos sem correspondência com nenhuma das assinaturas de evento para este tópico|Nenhum|
|PublishSuccessLatencyInMs|Publicar Latência de Êxito|Milissegundos|Total|Latência de êxito de publicação em milissegundos|Nenhum|
|MatchedEventCount|Eventos correspondentes|Contagem|Total|Total de eventos correspondentes a essa assinatura de evento|EventSubscriptionName|
|DeliveryAttemptFailCount|Eventos com falha de entrega|Contagem|Total|Total de eventos não entregues a essa assinatura de evento|Erro, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Eventos entregues|Contagem|Total|Total de eventos entregues a essa assinatura de evento|EventSubscriptionName|
|DestinationProcessingDurationInMs|Duração do processamento de destino|Milissegundos|Média|Duração do processamento de destino em milissegundos|EventSubscriptionName|
|DroppedEventCount|Eventos removidos|Contagem|Total|Total de eventos removidos correspondentes a essa assinatura de evento|DropReason,EventSubscriptionName|
|DeadLetteredCount|Eventos de mensagens mortas|Contagem|Total|Total de eventos de mensagens mortas correspondentes a essa assinatura de evento|DeadLetterReason,EventSubscriptionName|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|MatchedEventCount|Eventos correspondentes|Contagem|Total|Total de eventos correspondentes a essa assinatura de evento|Nenhum|
|DeliveryAttemptFailCount|Eventos com falha de entrega|Contagem|Total|Total de eventos não entregues a essa assinatura de evento|Erro, ErrorType|
|DeliverySuccessCount|Eventos entregues|Contagem|Total|Total de eventos entregues a essa assinatura de evento|Nenhum|
|DestinationProcessingDurationInMs|Duração do processamento de destino|Milissegundos|Média|Duração do processamento de destino em milissegundos|Nenhum|
|DroppedEventCount|Eventos removidos|Contagem|Total|Total de eventos removidos correspondentes a essa assinatura de evento|DropReason|
|DeadLetteredCount|Eventos de mensagens mortas|Contagem|Total|Total de eventos de mensagens mortas correspondentes a essa assinatura de evento|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventos publicados|Contagem|Total|Total de eventos publicados neste tópico|Nenhum|
|PublishFailCount|Publicar Eventos com Falha|Contagem|Total|Total de eventos com falha ao publicar neste tópico|ErrorType, erro|
|UnmatchedEventCount|Eventos sem correspondência|Contagem|Total|Total de eventos sem correspondência com nenhuma das assinaturas de evento para este tópico|Nenhum|
|PublishSuccessLatencyInMs|Publicar Latência de Êxito|Milissegundos|Total|Latência de êxito de publicação em milissegundos|Nenhum|




## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|SuccessfulRequests|Solicitações bem sucedidas|Contagem|Total|Solicitações Bem-sucedidas para Microsoft.EventHub.|EntityName, OperationResult|
|ServerErrors|Erros do Servidor.|Contagem|Total|Erros do Servidor para o Microsoft.EventHub.|EntityName, OperationResult|
|UserErrors|Erros de Usuário.|Contagem|Total|Erros de Usuário para o Microsoft.EventHub.|EntityName, OperationResult|
|QuotaExceededErrors|Cota de Erros Excedida.|Contagem|Total|Cota de Erros Excedida para o Microsoft.EventHub.|EntityName, OperationResult|
|ThrottledRequests|Solicitações Limitadas.|Contagem|Total|Solicitações Limitadas para o Microsoft.EventHub.|EntityName, OperationResult|
|IncomingRequests|Solicitações de entrada|Contagem|Total|Solicitações de Entrada para o Microsoft.EventHub.|EntityName|
|IncomingMessages|Mensagens de entrada|Contagem|Total|Mensagens de Entrada para o Microsoft.EventHub.|EntityName|
|OutgoingMessages|Mensagens de saída|Contagem|Total|Mensagens de Saída para o Microsoft.EventHub.|EntityName|
|IncomingBytes|Bytes de Entrada.|Bytes|Total|Bytes de Entrada para o Microsoft.EventHub.|EntityName|
|OutgoingBytes|Bytes de Saída.|Bytes|Total|Bytes de Saída para o Microsoft.EventHub.|EntityName|
|ActiveConnections|ActiveConnections|Contagem|Média|Total de Conexões Ativas para o Microsoft.EventHub.|Nenhum|
|ConnectionsOpened|Conexões Abertas.|Contagem|Média|Conexões Abertas para o Microsoft.EventHub.|EntityName|
|ConnectionsClosed|Conexões Fechadas.|Contagem|Média|Conexões Fechadas para o Microsoft.EventHub.|EntityName|
|CaptureBacklog|Lista de Pendências de Captura.|Contagem|Total|Lista de Pendências de Captura para o Microsoft.EventHub.|EntityName|
|CapturedMessages|Mensagens Capturadas.|Contagem|Total|Mensagens Capturadas para o Microsoft.EventHub.|EntityName|
|CapturedBytes|Bytes Capturados.|Bytes|Total|Bytes Capturados para o Microsoft.EventHub.|EntityName|
|Tamanho|Tamanho|Bytes|Média|Tamanho de um EventHub em Bytes.|EntityName|
|INREQS|Solicitações de Entrada (Preterido)|Contagem|Total|Total de solicitações de envio de entrada para um namespace (preterido)|Nenhum|
|SUCCREQ|Solicitações Bem-sucedidas (Preterido)|Contagem|Total|Total de solicitações bem-sucedidas para um namespace (preterido)|Nenhum|
|FAILREQ|Solicitações com Falha (Preterido)|Contagem|Total|Total de solicitações com falha para um namespace (preterido)|Nenhum|
|SVRBSY|Erros de Servidor Ocupado (Preterido)|Contagem|Total|Total de erros do servidor ocupado para um namespace (preterido)|Nenhum|
|INTERR|Erros Internos do Servidor (Preterido)|Contagem|Total|Total de erros internos do servidor para um namespace (preterido)|Nenhum|
|MISCERR|Outros Erros (Preterido)|Contagem|Total|Total de solicitações com falha para um namespace (preterido)|Nenhum|
|INMSGS|Mensagens de entrada (obsoleto) (Preterido)|Contagem|Total|Total de mensagens de entrada para um namespace. Essa métrica foi preterida. Use a métrica de mensagens de entrada em vez disso (preterido)|Nenhum|
|EHINMSGS|Mensagens de Entrada (Preterida)|Contagem|Total|Total de mensagens de entrada para um namespace (preterido)|Nenhum|
|OUTMSGS|Mensagens de Saída (obsoleto) (Preterido)|Contagem|Total|Total de mensagens de saída para um namespace. Essa métrica foi preterida. Use a métrica de mensagens de saída em vez disso (preterido)|Nenhum|
|EHOUTMSGS|Mensagens de Saída (Preterida)|Contagem|Total|Total de mensagens de saída para um namespace (preterido)|Nenhum|
|EHINMBS|Bytes de entrada (obsoleto) (Preterido)|Bytes|Total|Taxa de transferência de mensagem de entrada do Hub de Eventos para um namespace. Essa métrica foi preterida. Use a métrica de bytes de entrada em vez disso (preterido)|Nenhum|
|EHINBYTES|Bytes de entrada (Preterida)|Bytes|Total|Taxa de transferência de mensagem de entrada do hub de eventos para um namespace (preterido)|Nenhum|
|EHOUTMBS|Bytes de saída (obsoleto) (Preterido)|Bytes|Total|Taxa de transferência de mensagem de saída do Hub de Eventos para um namespace. Essa métrica foi preterida. Use a métrica de bytes de saída em vez disso (preterido)|Nenhum|
|EHOUTBYTES|Bytes de saída (Preterida)|Bytes|Total|Taxa de transferência de mensagem de saída do hub de eventos para um namespace (preterido)|Nenhum|
|EHABL|Arquivar mensagens da lista de pendências (Preterido)|Contagem|Total|Mensagens de arquivamento do hub de eventos no backlog para um namespace (preterido)|Nenhum|
|EHAMSGS|Arquivar mensagens (Preterido)|Contagem|Total|Mensagens arquivadas do hub de eventos em um namespace (preterido)|Nenhum|
|EHAMBS|Arquivar taxa de transferência de mensagem (Preterido)|Bytes|Total|Taxa de transferência de mensagem arquivada do hub de eventos em um namespace (preterido)|Nenhum|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|SuccessfulRequests|Solicitações bem sucedidas|Contagem|Total|Solicitações Bem-sucedidas para Microsoft.EventHub.|OperationResult|
|ServerErrors|Erros do Servidor.|Contagem|Total|Erros do Servidor para o Microsoft.EventHub.|OperationResult|
|UserErrors|Erros de Usuário.|Contagem|Total|Erros de Usuário para o Microsoft.EventHub.|OperationResult|
|QuotaExceededErrors|Cota de Erros Excedida.|Contagem|Total|Cota de Erros Excedida para o Microsoft.EventHub.|OperationResult|
|ThrottledRequests|Solicitações Limitadas.|Contagem|Total|Solicitações Limitadas para o Microsoft.EventHub.|OperationResult|
|IncomingRequests|Solicitações de entrada|Contagem|Total|Solicitações de Entrada para o Microsoft.EventHub.|Nenhum|
|IncomingMessages|Mensagens de entrada|Contagem|Total|Mensagens de Entrada para o Microsoft.EventHub.|Nenhum|
|OutgoingMessages|Mensagens de saída|Contagem|Total|Mensagens de Saída para o Microsoft.EventHub.|Nenhum|
|IncomingBytes|Bytes de Entrada.|Bytes|Total|Bytes de Entrada para o Microsoft.EventHub.|Nenhum|
|OutgoingBytes|Bytes de Saída.|Bytes|Total|Bytes de Saída para o Microsoft.EventHub.|Nenhum|
|ActiveConnections|ActiveConnections|Contagem|Média|Total de Conexões Ativas para o Microsoft.EventHub.|Nenhum|
|ConnectionsOpened|Conexões Abertas.|Contagem|Média|Conexões Abertas para o Microsoft.EventHub.|Nenhum|
|ConnectionsClosed|Conexões Fechadas.|Contagem|Média|Conexões Fechadas para o Microsoft.EventHub.|Nenhum|
|CaptureBacklog|Lista de Pendências de Captura.|Contagem|Total|Lista de Pendências de Captura para o Microsoft.EventHub.|Nenhum|
|CapturedMessages|Mensagens Capturadas.|Contagem|Total|Mensagens Capturadas para o Microsoft.EventHub.|Nenhum|
|CapturedBytes|Bytes Capturados.|Bytes|Total|Bytes Capturados para o Microsoft.EventHub.|Nenhum|
|CPU|CPU|Porcentagem|Máximo|Utilização da CPU para o Cluster do Hub de Eventos como um percentual|Função|
|AvailableMemory|Memória disponível|Porcentagem|Máximo|Memória disponível para o cluster do hub de eventos como uma porcentagem da memória total.|Função|
|Tamanho|Tamanho de um EventHub em Bytes.|Bytes|Média|Tamanho de um EventHub em Bytes.|Função|


## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|GatewayRequests|Solicitações de gateway|Contagem|Total|Número de solicitações de gateway|HttpStatus|
|CategorizedGatewayRequests|Solicitações de gateway categorizadas|Contagem|Total|Número de solicitações de gateway por categorias (1xx/2xx/3xx/4xx/5xx)|HttpStatus|
|NumActiveWorkers|Número de Trabalhos Ativos|Contagem|Máximo|Número de Trabalhos Ativos|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ObservedMetricValue|Valor de Métrica Observado|Contagem|Média|O valor calculado pelo dimensionamento automático quando executado|MetricTriggerSource|
|MetricThreshold|Limite da Métrica|Contagem|Média|O limite do dimensionamento automático configurado quando o dimensionamento automático foi executado.|MetricTriggerRule|
|ObservedCapacity|Capacidade Observada|Contagem|Média|A capacidade relatada para o dimensionamento automático quando ele foi executado.|Nenhum|
|ScaleActionsInitiated|Ações de Escala Iniciadas|Contagem|Total|A direção da operação de escala.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Disponibilidade|Porcentagem|Média|Percentual de testes de disponibilidade concluídos com êxito|availabilityResult/nome, availabilityResult/local|
|availabilityResults/count|Testes de disponibilidade|Contagem|Contagem|Contagem de testes de disponibilidade|availabilityResult/nome, availabilityResult/local, availabilityResult/êxito|
|availabilityResults/duration|Duração do teste de disponibilidade|MilliSeconds|Média|Duração do teste de disponibilidade|availabilityResult/nome, availabilityResult/local, availabilityResult/êxito|
|browserTimings/networkDuration|Tempo de conexão de rede de carregamento de página|MilliSeconds|Média|Tempo entre a solicitação do usuário e a conexão de rede. Inclui a pesquisa DNS e a conexão de transporte.|Nenhum|
|browserTimings/processingDuration|Tempo de processamento do cliente|MilliSeconds|Média|Tempo entre o recebimento do último byte de um documento até que o DOM seja carregado. As solicitações assíncronas ainda podem estar sendo processadas.|Nenhum|
|browserTimings/receiveDuration|Tempo de resposta de recebimento|MilliSeconds|Média|Tempo entre o primeiro e o último byte, ou até a desconexão.|Nenhum|
|browserTimings/sendDuration|Tempo de solicitação de envio|MilliSeconds|Média|Tempo entre a conexão de rede e o recebimento do primeiro byte.|Nenhum|
|browserTimings/totalDuration|Tempo de carregamento de página do navegador|MilliSeconds|Média|Tempo de solicitação do usuário até que o DOM, as imagens, os scripts e as folhas de estilo sejam carregados.|Nenhum|
|dependencies/count|Chamadas de dependência|Contagem|Contagem|Contagem de chamadas feitas pelo aplicativo a recursos externos.|dependência/tipo, dependência/performanceBucket, dependência/êxito, dependência/destino, dependência/resultCode, operação/sintética, nuvem/roleInstance, nuvem/roleName|
|dependencies/duration|Duração da dependência|MilliSeconds|Média|Duração das chamadas feitas pelo aplicativo a recursos externos.|dependência/tipo, dependência/performanceBucket, dependência/êxito, dependência/destino, dependência/resultCode, operação/sintética, nuvem/roleInstance, nuvem/roleName|
|dependencies/failed|Falhas de chamada de dependência|Contagem|Contagem|Contagem de chamadas de dependência com falha feitas pelo aplicativo a recursos externos.|dependência/tipo, dependência/performanceBucket, dependência/êxito, dependência/destino, dependência/resultCode, operação/sintética, nuvem/roleInstance, nuvem/roleName|
|pageViews/count|Visualizações de página|Contagem|Contagem|Contagem de exibições de página.|operação/sintética, nuvem/roleName|
|pageViews/duration|Tempo de carregamento de exibição de página|MilliSeconds|Média|Tempo de carregamento de exibição de página|operação/sintética, nuvem/roleName|
|performanceCounters/requestExecutionTime|Tempo de execução de solicitação HTTP|MilliSeconds|Média|Tempo de execução da solicitação mais recente.|cloud/roleInstance|
|performanceCounters/requestsInQueue|Solicitações HTTP na fila do aplicativo|Contagem|Média|Comprimento da fila de solicitação de aplicativo.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|Taxa de solicitação HTTP|CountPerSecond|Média|Taxa de todas as solicitações para o aplicativo por segundo do ASP.NET.|cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|Taxa de exceção|CountPerSecond|Média|Contagem de exceções com e sem tratamento relatadas para o Windows, incluindo exceções do .NET e exceções não gerenciadas convertidas em exceções do .NET.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Taxa de E/S de processamento|BytesPerSecond|Média|Total de bytes por segundo lidos e gravados em arquivos, rede e dispositivos.|cloud/roleInstance|
|performanceCounters/processCpuPercentage|CPU do processo|Porcentagem|Média|O percentual de tempo decorrido que todos os threads do processo usaram o processador para executar instruções. Isso pode variar entre 0 e 100. Essa métrica indica o desempenho do processo de w3wp isoladamente.|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Tempo do processador|Porcentagem|Média|O percentual de tempo que o processador gasta em threads não ociosos.|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Memória disponível|Bytes|Média|Memória física disponível imediatamente para alocação a um processo ou para uso do sistema.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|Processar bytes particulares|Bytes|Média|Memória atribuída exclusivamente aos processos do aplicativo monitorado.|cloud/roleInstance|
|requests/duration|Tempo de resposta do servidor|MilliSeconds|Média|Tempo entre o recebimento de uma solicitação HTTP e a finalização do envio da resposta.|solicitação/performanceBucket, solicitação/resultCode, operação/sintética, nuvem/roleInstance, solicitação/êxito, nuvem/roleName|
|requests/count|Solicitações do servidor|Contagem|Contagem|Contagem de solicitações HTTP concluídas.|solicitação/performanceBucket, solicitação/resultCode, operação/sintética, nuvem/roleInstance, solicitação/êxito, nuvem/roleName|
|requests/failed|Solicitações falhas|Contagem|Contagem|Contagem de solicitações HTTP marcadas como com falha. Na maioria dos casos, essas são solicitações com um código de resposta >= 400 e diferente de 401.|solicitação/performanceBucket, solicitação/resultCode, solicitação/êxito, operação/sintética, nuvem/roleInstance, nuvem/roleName|
|solicitações/taxa|Taxa de solicitação do servidor|CountPerSecond|Média|Taxa de solicitações de servidor por segundo|solicitação/performanceBucket, solicitação/resultCode, operação/sintética, nuvem/roleInstance, solicitação/êxito, nuvem/roleName|
|exceptions/count|Exceções|Contagem|Contagem|Contagem combinada de todas as exceções não capturadas.|Cloud/roleName, nuvem/roleInstance, cliente/tipo|
|exceptions/browser|Exceções de navegador|Contagem|Contagem|Contagem de exceções não identificadas lançadas no navegador.|cliente/IsServer, nuvem/roleName|
|exceptions/server|Exceções do servidor|Contagem|Contagem|Contagem de exceções não capturadas geradas no aplicativo para servidores.|cliente/IsServer, nuvem/roleName, nuvem/roleInstance|
|traces/count|Rastreamentos|Contagem|Contagem|Contagem de documentos de rastreamento|Trace/nível, operação/sintético, nuvem/roleName, nuvem/roleInstance|


## <a name="microsoftiotcentraliotapps"></a>Microsoft. IoTCentral/IoTApps

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|connectedDeviceCount|Total de dispositivos conectados|Contagem|Média|Número de dispositivos conectados a IoT Central|Nenhum|
|C2D. Property. Read. Success|Leituras de propriedade de dispositivo bem-sucedidas de IoT Central|Contagem|Total|A contagem de todas as leituras de propriedade bem-sucedidas iniciadas a partir de IoT Central|Nenhum|
|C2D. Property. Read. Failure|Falha nas leituras de Propriedade do dispositivo de IoT Central|Contagem|Total|A contagem de todas as leituras de propriedade com falha iniciadas a partir de IoT Central|Nenhum|
|D2C. Property. Read. Success|Leituras de propriedade de dispositivo bem-sucedidas de dispositivos|Contagem|Total|A contagem de todas as leituras de propriedade bem-sucedidas iniciadas a partir de dispositivos|Nenhum|
|D2C. Property. Read. Failure|Falha nas leituras de Propriedade do dispositivo de dispositivos|Contagem|Total|A contagem de todas as leituras de propriedade com falha iniciadas a partir de dispositivos|Nenhum|
|C2D. Property. Update. Success|Atualizações de propriedade de dispositivo bem-sucedidas do IoT Central|Contagem|Total|A contagem de todas as atualizações de propriedade bem-sucedidas iniciadas a partir de IoT Central|Nenhum|
|C2D. Property. Update. Failure|Falha nas atualizações de propriedade de dispositivo do IoT Central|Contagem|Total|A contagem de todas as atualizações de propriedade com falha iniciadas a partir de IoT Central|Nenhum|
|D2C. Property. Update. Success|Atualizações de propriedade de dispositivo bem-sucedidas de dispositivos|Contagem|Total|A contagem de todas as atualizações de propriedade bem-sucedidas iniciadas a partir de dispositivos|Nenhum|
|D2C. Property. Update. Failure|Falha nas atualizações de Propriedade do dispositivo de dispositivos|Contagem|Total|A contagem de todas as atualizações de propriedade com falha iniciadas a partir de dispositivos|Nenhum|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ServiceApiHit|Total de ocorrências da API de Serviço|Contagem|Contagem|Número total de ocorrências da API de serviço|ActivityType, ActivityName|
|ServiceApiLatency|Latência geral da API de Serviço|Milissegundos|Média|Latência geral das solicitações da API de serviço|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|Total de resultados da API de Serviço|Contagem|Contagem|Número total de resultados da API de serviço|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|SaturationShoebox|Saturação do cofre geral|Porcentagem|Média|Capacidade do cofre usada|Activitytable, ActivityName, transactionType|
|Disponibilidade|Disponibilidade geral do cofre|Porcentagem|Média|Disponibilidade de solicitações do cofre|ActivityType, ActivityName, StatusCode, StatusCodeClass|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|CacheUtilization|Utilização do cache|Porcentagem|Média|Nível de utilização no escopo do cluster|Nenhum|
|QueryDuration|Duração da consulta|Milissegundos|Média|Duração das consultas em segundos|QueryStatus|
|IngestionUtilization|Utilização da ingestão|Porcentagem|Média|Taxa de slots de ingestão usados no cluster|Nenhum|
|KeepAlive|Keep alive|Contagem|Média|A verificação de integridade indica que o cluster responde às consultas|Nenhum|
|IngestionVolumeInMB|Volume de ingestão (em MB)|Contagem|Total|Volume total de dados ingeridos no cluster (em MB)|Banco de dados|
|IngestionLatencyInSeconds|Latência de ingestão (em segundos)|Segundos|Média|Tempo de ingestão da origem (por exemplo, mensagem no Hub de Eventos) para o cluster em segundos|Nenhum|
|EventsProcessedForEventHubs|Eventos processados (para os Hubs IoT/de Eventos)|Contagem|Total|Número de eventos processados pelo cluster ao ingerir de evento/Hub IoT|EventStatus|
|IngestionResult|Resultados da ingestão|Contagem|Contagem|Número de operações de ingestão|IngestionResultDetails|
|CPU|CPU|Porcentagem|Média|Nível de utilização da CPU|Nenhum|
|ContinuousExportNumOfRecordsExported|Exportação contínua – núm de registros exportados|Contagem|Total|Número de registros exportados, acionados para cada artefato de armazenamento gravado durante a operação de exportação|ContinuousExportName, banco de dados|
|ExportUtilization|Utilização da Exportação|Porcentagem|Máximo|Utilização da exportação|Nenhum|
|ContinuousExportPendingCount|Contagem de Exportação Contínua Pendente|Contagem|Máximo|O número de trabalhos de exportação contínuos pendentes prontos para execução|Nenhum|
|ContinuousExportMaxLatenessMinutes|Atraso máximo de exportação contínua|Contagem|Máximo|A finalização (em minutos) relatada pelos trabalhos de exportação contínua no cluster|Nenhum|
|ContinuousExportResult|Resultado da Exportação Contínua|Contagem|Contagem|Indica se a exportação contínua foi bem-sucedida ou falhou|ContinuousExportName, resultado, banco de dados|
|StreamingIngestDuration|Duração da Ingestão de Streaming|Milissegundos|Média|Duração da ingestão de streaming em milissegundos|Nenhum|
|StreamingIngestDataRate|Taxa de Dados de Ingestão de Streaming|Contagem|Média|Taxa de dados de ingestão de streaming (MB por segundo)|Nenhum|
|SteamingIngestRequestRate|Taxa de Solicitação de Ingestão de Streaming|Contagem|RateRequestsPerSecond|Taxa de solicitação de ingestão de streaming (solicitações por segundo)|Nenhum|
|StreamingIngestResults|Resultado da Ingestão de Streaming|Contagem|Média|Resultado de ingestão de streaming|Resultado|
|TotalNumberOfConcurrentQueries|Número total de consultas simultâneas|Contagem|Total|Número total de consultas simultâneas|Nenhum|
|TotalNumberOfThrottledQueries|Número total de consultas limitadas|Contagem|Total|Número total de consultas limitadas|Nenhum|
|TotalNumberOfThrottledCommands|Número total de comandos regulados|Contagem|Total|Número total de comandos regulados|CommandType|
|TotalNumberOfExtents|Número total de extensões|Contagem|Total|Número total de extensões de dados|Nenhum|
|InstanceCount|Contagem de Instâncias|Contagem|Média|Contagem total de instâncias|Nenhum|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|RunsStarted|Execuções iniciadas|Contagem|Total|Número de execuções fluxo de trabalho iniciadas.|Nenhum|
|RunsCompleted|Execuções concluídas|Contagem|Total|Número de execuções de fluxo de trabalho concluídas.|Nenhum|
|RunsSucceeded|Execuções bem sucedidas|Contagem|Total|Número de execuções de fluxo de trabalho bem sucedidas.|Nenhum|
|RunsFailed|Execuções com falha|Contagem|Total|Número de execuções de fluxo de trabalho com falha.|Nenhum|
|RunsCancelled|Execuções canceladas|Contagem|Total|Número de execuções de fluxo de trabalho canceladas.|Nenhum|
|RunLatency|Latência da execução|Segundos|Média|Latência das execuções de fluxo de trabalho concluídas.|Nenhum|
|RunSuccessLatency|Latência de execução bem sucedida|Segundos|Média|Latência de execuções de fluxo de trabalho bem sucedidas.|Nenhum|
|RunThrottledEvents|Eventos de restrição de execução|Contagem|Total|Número de eventos de restrição de ações ou gatilhos de fluxo de trabalho.|Nenhum|
|RunStartThrottledEvents|Executar Eventos Limitados por Início|Contagem|Total|Número de eventos restritos de início de fluxo de trabalho.|Nenhum|
|RunFailurePercentage|Porcentagem de falha de execução|Porcentagem|Total|Porcentagem de execuções do fluxo de trabalho com falha.|Nenhum|
|ActionsStarted|Ações iniciadas |Contagem|Total|Número de ações de fluxo de trabalho iniciadas.|Nenhum|
|ActionsCompleted|Ações concluídas |Contagem|Total|Número de ações de fluxo de trabalho concluídas.|Nenhum|
|ActionsSucceeded|Ações bem sucedidas |Contagem|Total|Número de ações de fluxo de trabalho bem sucedidas.|Nenhum|
|ActionsFailed|Ações com falha |Contagem|Total|Número de ações de fluxo de trabalho com falha.|Nenhum|
|ActionsSkipped|Ações ignoradas |Contagem|Total|Número de ações de fluxo de trabalho ignoradas.|Nenhum|
|ActionLatency|Latência da ação |Segundos|Média|Latência das ações de fluxo de trabalho concluídas.|Nenhum|
|ActionSuccessLatency|Latência das ações bem sucedidas |Segundos|Média|Latência das ações de fluxo de trabalho bem sucedidas.|Nenhum|
|ActionThrottledEvents|Eventos de restrição de ações|Contagem|Total|Número de eventos de restrição de ações de fluxo de trabalho.|Nenhum|
|TriggersStarted|Gatilhos iniciados |Contagem|Total|Número de gatilhos de fluxo de trabalho iniciadas.|Nenhum|
|TriggersCompleted|Gatilhos concluídos |Contagem|Total|Número de gatilhos de fluxo de trabalho concluídos.|Nenhum|
|TriggersSucceeded|Gatilhos bem sucedidos |Contagem|Total|Número de gatilhos de fluxo de trabalho bem sucedidos.|Nenhum|
|TriggersFailed|Gatilhos com falha |Contagem|Total|Número de gatilhos de fluxo de trabalho com falha.|Nenhum|
|TriggersSkipped|Gatilhos ignorados|Contagem|Total|Número de gatilhos de fluxo de trabalho ignorados.|Nenhum|
|TriggersFired|Gatilhos acionados |Contagem|Total|Número de gatilhos de fluxo de trabalho acionados.|Nenhum|
|TriggerLatency|Latência do gatilho |Segundos|Média|Latência dos gatilhos de fluxo de trabalho concluídos.|Nenhum|
|TriggerFireLatency|Latência do gatilho acionado |Segundos|Média|Latência dos gatilhos de fluxo de trabalho acionados.|Nenhum|
|TriggerSuccessLatency|Latência do gatilho bem sucedido |Segundos|Média|Latência dos gatilhos de fluxo de trabalho bem sucedidos.|Nenhum|
|TriggerThrottledEvents|Eventos de restrição do gatilho|Contagem|Total|Número de eventos de restrição do gatilho de fluxo de trabalho.|Nenhum|
|BillableActionExecutions|Execuções de ação faturáveis|Contagem|Total|Número de execuções de ação de fluxo de trabalho sendo faturadas.|Nenhum|
|BillableTriggerExecutions|Execuções de gatilho faturáveis|Contagem|Total|Número de execuções de gatilho do fluxo de trabalho sendo cobradas.|Nenhum|
|TotalBillableExecutions|Total de execuções faturáveis|Contagem|Total|Número de execuções de fluxo de trabalho sendo faturadas.|Nenhum|
|BillingUsageNativeOperation|Uso de Cobrança para Execuções de Operação Nativa|Contagem|Total|Número de execuções de operação nativa sendo cobradas.|Nenhum|
|BillingUsageStandardConnector|Uso de Cobrança para Execuções de Conector Padrão|Contagem|Total|Número de execuções do conector padrão sendo cobradas.|Nenhum|
|BillingUsageStorageConsumption|Uso de Cobrança para Execuções de Consumo de Armazenamento|Contagem|Total|Número de execuções de consumo de armazenamento sendo cobradas.|Nenhum|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|RunsStarted|Execuções iniciadas|Contagem|Total|Número de execuções fluxo de trabalho iniciadas.|Nenhum|
|RunsCompleted|Execuções concluídas|Contagem|Total|Número de execuções de fluxo de trabalho concluídas.|Nenhum|
|RunsSucceeded|Execuções bem sucedidas|Contagem|Total|Número de execuções de fluxo de trabalho bem sucedidas.|Nenhum|
|RunsFailed|Execuções com falha|Contagem|Total|Número de execuções de fluxo de trabalho com falha.|Nenhum|
|RunsCancelled|Execuções canceladas|Contagem|Total|Número de execuções de fluxo de trabalho canceladas.|Nenhum|
|RunLatency|Latência da execução|Segundos|Média|Latência das execuções de fluxo de trabalho concluídas.|Nenhum|
|RunSuccessLatency|Latência de execução bem sucedida|Segundos|Média|Latência de execuções de fluxo de trabalho bem sucedidas.|Nenhum|
|RunThrottledEvents|Eventos de restrição de execução|Contagem|Total|Número de eventos de restrição de ações ou gatilhos de fluxo de trabalho.|Nenhum|
|RunStartThrottledEvents|Executar Eventos Limitados por Início|Contagem|Total|Número de eventos restritos de início de fluxo de trabalho.|Nenhum|
|RunFailurePercentage|Porcentagem de falha de execução|Porcentagem|Total|Porcentagem de execuções do fluxo de trabalho com falha.|Nenhum|
|ActionsStarted|Ações iniciadas |Contagem|Total|Número de ações de fluxo de trabalho iniciadas.|Nenhum|
|ActionsCompleted|Ações concluídas |Contagem|Total|Número de ações de fluxo de trabalho concluídas.|Nenhum|
|ActionsSucceeded|Ações bem sucedidas |Contagem|Total|Número de ações de fluxo de trabalho bem sucedidas.|Nenhum|
|ActionsFailed|Ações com falha |Contagem|Total|Número de ações de fluxo de trabalho com falha.|Nenhum|
|ActionsSkipped|Ações ignoradas |Contagem|Total|Número de ações de fluxo de trabalho ignoradas.|Nenhum|
|ActionLatency|Latência da ação |Segundos|Média|Latência das ações de fluxo de trabalho concluídas.|Nenhum|
|ActionSuccessLatency|Latência das ações bem sucedidas |Segundos|Média|Latência das ações de fluxo de trabalho bem sucedidas.|Nenhum|
|ActionThrottledEvents|Eventos de restrição de ações|Contagem|Total|Número de eventos de restrição de ações de fluxo de trabalho.|Nenhum|
|TriggersStarted|Gatilhos iniciados |Contagem|Total|Número de gatilhos de fluxo de trabalho iniciadas.|Nenhum|
|TriggersCompleted|Gatilhos concluídos |Contagem|Total|Número de gatilhos de fluxo de trabalho concluídos.|Nenhum|
|TriggersSucceeded|Gatilhos bem sucedidos |Contagem|Total|Número de gatilhos de fluxo de trabalho bem sucedidos.|Nenhum|
|TriggersFailed|Gatilhos com falha |Contagem|Total|Número de gatilhos de fluxo de trabalho com falha.|Nenhum|
|TriggersSkipped|Gatilhos ignorados|Contagem|Total|Número de gatilhos de fluxo de trabalho ignorados.|Nenhum|
|TriggersFired|Gatilhos acionados |Contagem|Total|Número de gatilhos de fluxo de trabalho acionados.|Nenhum|
|TriggerLatency|Latência do gatilho |Segundos|Média|Latência dos gatilhos de fluxo de trabalho concluídos.|Nenhum|
|TriggerFireLatency|Latência do gatilho acionado |Segundos|Média|Latência dos gatilhos de fluxo de trabalho acionados.|Nenhum|
|TriggerSuccessLatency|Latência do gatilho bem sucedido |Segundos|Média|Latência dos gatilhos de fluxo de trabalho bem sucedidos.|Nenhum|
|TriggerThrottledEvents|Eventos de restrição do gatilho|Contagem|Total|Número de eventos de restrição do gatilho de fluxo de trabalho.|Nenhum|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Uso do Processador de Fluxo de Trabalho para o Ambiente de Serviço de Integração|Porcentagem|Média|Uso do processador do fluxo de trabalho para o ambiente do serviço de integração.|Nenhum|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Uso de Memória do Fluxo de Trabalho para o Ambiente de Serviço de Integração|Porcentagem|Média|Uso de memória do fluxo de trabalho para o ambiente do serviço de integração.|Nenhum|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Uso do Processador de Conector para o Ambiente de Serviço de Integração|Porcentagem|Média|Uso do processador do conector para o ambiente do serviço de integração.|Nenhum|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Uso de Memória do Conector para o Ambiente de Serviço de Integração|Porcentagem|Média|Uso de memória do conector para o ambiente do serviço de integração.|Nenhum|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Execuções canceladas|Execuções canceladas|Contagem|Total|Número de execuções canceladas para este espaço de trabalho|Cenário, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Cancelar execuções solicitadas|Cancelar execuções solicitadas|Contagem|Total|Número de execuções em que o cancelamento foi solicitado para este espaço de trabalho|Cenário, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Execuções Concluídas|Execuções Concluídas|Contagem|Total|Número de execuções concluídas com êxito para este espaço de trabalho|Cenário, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Execuções com falha|Execuções com falha|Contagem|Total|Número de execuções com falha para este espaço de trabalho|Cenário, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Finalizando execuções|Finalizando execuções|Contagem|Total|Número de execuções inseridas no estado final para este espaço de trabalho|Cenário, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Não respondendo execuções|Não respondendo execuções|Contagem|Total|Número de execuções não respondendo para este espaço de trabalho|Cenário, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Execuções não iniciadas|Execuções não iniciadas|Contagem|Total|Número de execuções no estado não iniciado para este espaço de trabalho|Cenário, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Preparando execuções|Preparando execuções|Contagem|Total|Número de execuções que estão se preparando para este espaço de trabalho|Cenário, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Execuções de provisionamento|Execuções de provisionamento|Contagem|Total|Número de execuções que estão sendo provisionadas para este espaço de trabalho|Cenário, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Execuções em fila|Execuções em fila|Contagem|Total|Número de execuções enfileiradas para este espaço de trabalho|Cenário, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Execuções Iniciadas|Execuções Iniciadas|Contagem|Total|Número de execuções iniciadas para este espaço de trabalho|Cenário, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Iniciando execuções|Iniciando execuções|Contagem|Total|Número de execuções iniciadas para este espaço de trabalho|Cenário, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Erros|Errors|Contagem|Total|Número de erros de execução neste espaço de trabalho|Cenário|
|Avisos|Avisos|Contagem|Total|Número de avisos de execução neste espaço de trabalho|Cenário|
|Registro de Modelo Bem-sucedido|Registro de Modelo Bem-sucedido|Contagem|Total|Número de registros de modelo que foram bem-sucedidos neste espaço de trabalho|Cenário|
|Registro de Modelo com Falha|Registro de Modelo com Falha|Contagem|Total|Número de registros de modelo que falharam neste espaço de trabalho|Cenário, StatusCode|
|Implantação de Modelo Iniciada|Implantação de Modelo Iniciada|Contagem|Total|Número de implantações de modelo iniciadas neste espaço de trabalho|Cenário|
|Implantação de Modelo Bem-sucedida|Implantação de Modelo Bem-sucedida|Contagem|Total|Número de implantações de modelo que foram bem-sucedidas neste espaço de trabalho|Cenário|
|Implantação de Modelo com Falha|Implantação de Modelo com Falha|Contagem|Total|Número de implantações de modelo que falharam neste espaço de trabalho|Cenário, StatusCode|
|Total de Nós|Total de Nós|Contagem|Média|Número total de nós. Esse total inclui alguns nós ativos, nós ociosos, nós inutilizáveis, nós Premepted, deixando nós|Cenário, ClusterName|
|Nós Ativos|Nós Ativos|Contagem|Média|Número de nós active. Esses são os nós que estão executando ativamente um trabalho.|Cenário, ClusterName|
|Nós Ociosos|Nós Ociosos|Contagem|Média|Número de nós ociosos. Os nós ociosos são os nós que não estão executando nenhum trabalho, mas podem aceitar novos trabalhos, se disponíveis.|Cenário, ClusterName|
|Nós Inutilizáveis|Nós Inutilizáveis|Contagem|Média|Número de nós inutilizáveis. Os nós inutilizáveis não são funcionais devido a algum problema não resolvido. O Azure reciclará esses nós.|Cenário, ClusterName|
|Nós com Preempção|Nós com Preempção|Contagem|Média|Número de nós admitidos. Esses nós são os nós de baixa prioridade que são desfeitos do pool de nós disponível.|Cenário, ClusterName|
|Nós em Saída|Nós em Saída|Contagem|Média|Número de nós de saída. A saída de nós são os nós que acabaram de processar um trabalho e vão para o estado ocioso.|Cenário, ClusterName|
|Total de Núcleos|Total de Núcleos|Contagem|Média|Número de núcleos totais|Cenário, ClusterName|
|Núcleos Ativos|Núcleos Ativos|Contagem|Média|Número de núcleos ativos|Cenário, ClusterName|
|Núcleos Ociosos|Núcleos Ociosos|Contagem|Média|Número de núcleos ociosos|Cenário, ClusterName|
|Núcleos Inutilizáveis|Núcleos Inutilizáveis|Contagem|Média|Número de núcleos inutilizáveis|Cenário, ClusterName|
|Núcleos com Preempção|Núcleos com Preempção|Contagem|Média|Número de núcleos admitidos|Cenário, ClusterName|
|Núcleos em Saída|Núcleos em Saída|Contagem|Média|Número de núcleos de saída|Cenário, ClusterName|
|Percentual de Utilização de Cota|Percentual de Utilização de Cota|Contagem|Média|Porcentagem de cota utilizada|Cenário, ClusterName, VmFamilyName, VmPriority|
|CpuUtilization|CpuUtilization|Contagem|Média|CPU (versão prévia)|Cenário, runId, NodeId, Createdtime|
|GpuUtilization|GpuUtilization|Contagem|Média|GPU (visualização)|Cenário, runId, NodeId, Createdtime, DeviceID|


## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Uso|Uso|Contagem|Contagem|Contagem de chamadas à API|ApiCategory, ApiName, ResultType, ResponseCode|
|Disponibilidade|Disponibilidade|Porcentagem|Média|Disponibilidade das APIs|ApiCategory, ApiName|

## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft.Media/mediaservices/streamingEndpoints

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Saída|Saída|Bytes|Total|A quantidade de dados de egresso, em bytes.|OutputFormat|
|SuccessE2ELatency|Latência de ponta a ponta bem-sucedida|Milissegundos|Média|A latência média de solicitações bem-sucedidas em milissegundos.|OutputFormat|
|Requests|Requests|Contagem|Total|Solicitações para um ponto de extremidade de streaming.|OutputFormat, HttpStatusCode, ErrorCode|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|AssetQuota|Cota de ativos|Contagem|Média|Quantos ativos são permitidos para a conta de serviço de mídia atual|Nenhum|
|AssetCount|Contagem de ativos|Contagem|Média|Quantos ativos já foram criados na conta de serviço de mídia atual|Nenhum|
|AssetQuotaUsedPercentage|Percentual de cota de ativos usado|Porcentagem|Média|Porcentagem de ativos usados na conta de serviço de mídia atual|Nenhum|
|ContentKeyPolicyQuota|Cota de Política de Chave de Conteúdo|Contagem|Média|Quantas políticas de chave de conteúdo são permitidas para a conta de serviço de mídia atual|Nenhum|
|ContentKeyPolicyCount|Contagem de Política de Chave de Conteúdo|Contagem|Média|Quantas políticas de chave de conteúdo já foram criadas na conta de serviço de mídia atual|Nenhum|
|ContentKeyPolicyQuotaUsedPercentage|Percentual de Cota Usada da Política de Chave de Conteúdo|Porcentagem|Média|Porcentagem usada da política de chave de conteúdo na conta de serviço de mídia atual|Nenhum|
|StreamingPolicyQuota|Cota de Política de Streaming|Contagem|Média|Quantas políticas de streaming são permitidas para a conta de serviço de mídia atual|Nenhum|
|StreamingPolicyCount|Contagem de Política de Streaming|Contagem|Média|Quantas políticas de streaming já foram criadas na conta de serviço de mídia atual|Nenhum|
|StreamingPolicyQuotaUsedPercentage|Percentual de cota usada da Política de Streaming|Porcentagem|Média|Percentual usado pela política de streaming na conta de serviço de mídia atual|Nenhum|


## <a name="microsoftmixedrealityremoterenderingaccounts"></a>Microsoft. MixedReality/remoteRenderingAccounts

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|AssetsConverted|Ativos convertidos|Contagem|Total|Número total de ativos convertidos|AppId, ResourceId, SDKVersion|
|ActiveRenderingSessions|Sessões de renderização ativas|Contagem|Total|Número total de sessões de renderização ativas|AppId, ResourceId, SessionType, SDKVersion|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/volumes

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|AverageReadLatency|Latência média de leitura|MilliSeconds|Média|Latência média de leitura em milissegundos por operação|Nenhum|
|AverageWriteLatency|Latência média de gravação|MilliSeconds|Média|Latência média de gravação em milissegundos por operação|Nenhum|
|VolumeLogicalSize|Tamanho consumido por volume|Bytes|Média|Tamanho lógico do volume (bytes usados)|Nenhum|
|VolumeSnapshotSize|Tamanho do instantâneo de volume|Bytes|Média|Tamanho de todos os instantâneos no volume|Nenhum|
|ReadIops|IOPS de leitura|CountPerSecond|Média|Operações de entrada/saída de leitura por segundo|Nenhum|
|WriteIops|IOPS de gravação|CountPerSecond|Média|Operações de entrada/saída de gravação por segundo|Nenhum|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|VolumePoolAllocatedUsed|Pool alocado para o tamanho do volume|Bytes|Média|Tamanho usado alocado do pool|Nenhum|
|VolumePoolTotalLogicalSize|Tamanho consumido do pool|Bytes|Média|Soma do tamanho lógico de todos os volumes que pertencem ao pool|Nenhum|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BytesSentRate|Bytes Enviados|Bytes|Total|Número de bytes enviados pela Interface de Rede|Nenhum|
|BytesReceivedRate|Bytes Recebidos|Bytes|Total|Número de bytes recebidos pela Interface de Rede|Nenhum|
|PacketsSentRate|Pacotes Enviados|Contagem|Total|Número de bytes recebidos pela Interface de Rede|Nenhum|
|PacketsReceivedRate|Pacotes Recebidos|Contagem|Total|Número de pacotes recebidos pela Interface de Rede|Nenhum|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|VipAvailability|Disponibilidade do Caminho de Dados|Contagem|Média|Disponibilidade média do caminho de dados do Balanceador de Carga por duração de tempo|FrontendIPAddress, FrontendPort|
|DipAvailability|Status de investigação de integridade|Contagem|Média|Status médio de investigação de integridade do Balanceador de Carga por duração de tempo|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Contagem de Bytes|Contagem|Total|Número total de Bytes transmitidos no período|FrontendIPAddress, FrontendPort, direção|
|PacketCount|Contagem de Pacotes|Contagem|Total|Número total de Pacotes transmitidos no período|FrontendIPAddress, FrontendPort, direção|
|SYNCount|Contagem de SYN|Contagem|Total|Número total de Pacotes de SYN transmitidos no período|FrontendIPAddress, FrontendPort, direção|
|SnatConnectionCount|Contagem de Conexões SNAT|Contagem|Total|Número total de novas conexões SNAT criadas no período|FrontendIPAddress, BackendIPAddress, ConnectionState|
|AllocatedSnatPorts|Portas SNAT alocadas (versão prévia)|Contagem|Total|Número total de portas SNAT alocadas no período|FrontendIPAddress, BackendIPAddress, ProtocolType, IsAwaitingRemoval|
|UsedSnatPorts|Portas SNAT usadas (versão prévia)|Contagem|Total|Número total de portas SNAT usadas no período|FrontendIPAddress, BackendIPAddress, ProtocolType, IsAwaitingRemoval|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|QueryVolume|Volume de consulta|Contagem|Total|Número de consultas atendidas para uma zona DNS|Nenhum|
|RecordSetCount|Contagem de Conjuntos de Registros|Contagem|Máximo|Número de Conjuntos de Registros em uma zona DNS|Nenhum|
|RecordSetCapacityUtilization|Utilização de capacidade de Conjuntos de Registros|Porcentagem|Máximo|Porcentagem de capacidade de Conjuntos de Registros utilizada por uma zona DNS|Nenhum|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
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
|IfUnderDDoSAttack|Sob ataque DDoS ou não|Contagem|Máximo|Sob ataque DDoS ou não|Nenhum|
|DDoSTriggerTCPPackets|Pacotes TCP de entrada a disparar a mitigação de DDoS|CountPerSecond|Máximo|Pacotes TCP de entrada a disparar a mitigação de DDoS|Nenhum|
|DDoSTriggerUDPPackets|Pacotes UDP de entrada a disparar a mitigação de DDoS|CountPerSecond|Máximo|Pacotes UDP de entrada a disparar a mitigação de DDoS|Nenhum|
|DDoSTriggerSYNPackets|Pacotes de entrada SYN para disparar a mitigação de DDoS|CountPerSecond|Máximo|Pacotes de entrada SYN para disparar a mitigação de DDoS|Nenhum|
|VipAvailability|Disponibilidade do Caminho de Dados|Contagem|Média|Disponibilidade média de Endereço IP por duração de tempo|Porta|
|ByteCount|Contagem de Bytes|Contagem|Total|Número total de Bytes transmitidos no período|Porta, direção|
|PacketCount|Contagem de Pacotes|Contagem|Total|Número total de Pacotes transmitidos no período|Porta, direção|
|SynCount|Contagem de SYN|Contagem|Total|Número total de Pacotes de SYN transmitidos no período|Porta, direção|



## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Tempo de ida e volta para Pings em uma VM|MilliSeconds|Média|Tempo de ida e volta para pings enviados para uma VM de destino|SourceCustomerAddress,DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Pings com Falha em uma VM|Porcentagem|Média|Porcentagem do número de pings com falha para o total de pings enviados de uma VM de destino|SourceCustomerAddress,DestinationCustomerAddress|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ApplicationRuleHit|Contagem de ocorrências de regras de aplicativo|Contagem|Total|Número de vezes que as regras de aplicativo foram atingidas|Status, motivo, protocolo|
|NetworkRuleHit|Contagem de ocorrências das regras de rede|Contagem|Total|Número de vezes que as regras de rede foram atingidas|Status, motivo, protocolo|
|FirewallHealth|Estado de integridade do firewall|Porcentagem|Média|Estado de integridade do firewall|Status, motivo|
|DataProcessed|Dados processados|Bytes|Total|Quantidade total de dados processados pelo firewall|Nenhum|
|SNATPortUtilization|Utilização da porta SNAT|Porcentagem|Média|Utilização da porta SNAT|Nenhum|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Taxa de transferência|Produtividade|BytesPerSecond|Média|Número de bytes por segundo atendidos pelo Gateway de Aplicativo|Nenhum|
|UnhealthyHostCount|Contagem de hosts não íntegros|Contagem|Média|Número de hosts de back-end não íntegros|BackendSettingsPool|
|HealthyHostCount|Contagem de hosts íntegros|Contagem|Média|Número de hosts de back-end íntegros|BackendSettingsPool|
|TotalRequests|Total de Solicitações|Contagem|Total|Contagem de solicitações bem sucedidas atendidas pelo Gateway de Aplicativo|BackendSettingsPool|
|AvgRequestCountPerHealthyHost|Solicitações por minuto por Host Íntegro|Contagem|Média|Contagem média de solicitações por minuto por host de back-end íntegro em um pool|BackendSettingsPool|
|FailedRequests|Solicitações com falha|Contagem|Total|Contagem de solicitações com falha atendidas pelo Gateway de Aplicativo|BackendSettingsPool|
|ResponseStatus|Status da Resposta|Contagem|Total|Status de resposta HTTP retornado pelo Gateway de Aplicativo|HttpStatusGroup|
|CurrentConnections|Conexões atuais|Contagem|Total|Contagem de conexões atuais estabelecidas com o Gateway de Aplicativo|Nenhum|
|NewConnectionsPerSecond|Novas conexões por segundo|CountPerSecond|Média|Novas conexões por segundo estabelecidas com o gateway de aplicativo|Nenhum|
|CpuUtilization|Utilização da CPU|Porcentagem|Média|Utilização atual da CPU do gateway de aplicativo|Nenhum|
|CapacityUnits|Unidades de Capacidade Atuais|Contagem|Média|Unidades de capacidade consumidas|Nenhum|
|FixedBillableCapacityUnits|Unidades de capacidade cobráveis fixas|Contagem|Média|Unidades de capacidade mínimas que serão cobradas|Nenhum|
|EstimatedBilledCapacityUnits|Unidades de capacidade cobradas estimadas|Contagem|Média|Unidades de capacidade estimadas que serão cobradas|Nenhum|
|ComputeUnits|Unidades de Computação Atuais|Contagem|Média|Unidades de computação consumidas|Nenhum|
|BackendResponseStatus|Status da Resposta de Back-end|Contagem|Total|O número de códigos de resposta HTTP gerados pelos membros de back-end. Isso não inclui nenhum código de resposta gerado pelo gateway de aplicativo.|BackendServer, Httpsettings, BackendHttpSetting, HttpStatusGroup|
|TlsProtocol|Protocolo TLS do Cliente|Contagem|Total|O número de solicitações TLS e não TLS iniciadas pelo cliente que estabeleceram a conexão com o gateway de aplicativo. Para exibir a distribuição de protocolo TLS, filtre pelo protocolo TLS de dimensão.|Ouvinte, TlsProtocol|
|BytesSent|Bytes Enviados|Bytes|Total|O número total de bytes enviados pelo gateway de aplicativo para os clientes|Ouvinte|
|BytesReceived|Bytes Recebidos|Bytes|Total|O número total de bytes recebidos pelo gateway de aplicativo dos clientes|Ouvinte|
|ClientRtt|RTT do Cliente|MilliSeconds|Média|Tempo médio de ida e volta entre clientes e o gateway de aplicativo. Essa métrica indica quanto tempo leva para estabelecer conexões e retornar confirmações|Ouvinte|
|ApplicationGatewayTotalTime|Tempo Total do Gateway de Aplicativo|MilliSeconds|Média|Tempo médio que leva para que uma solicitação seja processada e sua resposta seja enviada. Isso é calculado como a média do intervalo desde o momento em que o gateway de aplicativo recebe o primeiro byte de uma solicitação HTTP até o momento em que a operação de envio de resposta é concluída. É importante observar que isso geralmente inclui o tempo de processamento do gateway de aplicativo, o tempo que os pacotes de solicitação e resposta estão viajando pela rede e o tempo que o servidor back-end levou para responder.|Ouvinte|
|BackendConnectTime|Tempo de Conexão de Back-end|MilliSeconds|Média|Tempo gasto estabelecendo uma conexão com um servidor de back-end|Listener, BackendServer, Httpsettings, BackendHttpSetting|
|BackendFirstByteResponseTime|Tempo de Resposta do Primeiro Byte de Back-end|MilliSeconds|Média|Intervalo de tempo entre o início do estabelecimento de uma conexão com o servidor back-end e o recebimento do primeiro byte do cabeçalho de resposta, aproximar tempo de processamento do servidor back-end|Listener, BackendServer, Httpsettings, BackendHttpSetting|
|BackendLastByteResponseTime|Tempo de Resposta do Último Byte de Back-end|MilliSeconds|Média|Intervalo de tempo entre o início do estabelecimento de uma conexão com o servidor de back-end e o recebimento do último byte do corpo da resposta|Listener, BackendServer, Httpsettings, BackendHttpSetting|
|MatchedCount|Distribuição de regra total do firewall do aplicativo Web v1|Contagem|Total|Distribuição de regra total do firewall do aplicativo Web v1 para o tráfego de entrada|Ruler, RuleId|
|BlockedCount|Distribuição de regra de solicitações bloqueadas do firewall de aplicativo Web v1|Contagem|Total|Distribuição de regra de solicitações bloqueadas do firewall de aplicativo Web v1|Ruler, RuleId|
|BlockedReqCount|Contagem de solicitações bloqueadas do firewall do aplicativo Web v1|Contagem|Total|Contagem de solicitações bloqueadas do firewall do aplicativo Web v1|Nenhum|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|AverageBandwidth|Largura de banda de S2S do gateway|BytesPerSecond|Média|Largura de banda média site a site de um gateway em bytes por segundo|Nenhum|
|P2SBandwidth|Largura de banda de P2S do gateway|BytesPerSecond|Média|Largura de banda média ponto a site de um gateway em bytes por segundo|Nenhum|
|P2SConnectionCount|Contagem de conexões P2S|Contagem|Máximo|Contagem de conexões ponto a site de um gateway|Protocolo|
|TunnelAverageBandwidth|Largura de Banda de Túnel|BytesPerSecond|Média|Largura de banda média de um túnel em bytes por segundo|ConnectionName, RemoteIP|
|TunnelEgressBytes|Bytes de Saída de Túnel|Bytes|Total|Bytes de saída de um túnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Bytes de Entrada de Túnel|Bytes|Total|Bytes de entrada de um túnel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Pacotes de Saída de Túnel|Contagem|Total|Contagem de pacotes de saída de um túnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|Pacotes de Entrada de Túnel|Contagem|Total|Contagem de pacotes de entrada de um túnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Remoção de Pacotes Incompatíveis de TS de Saída de Túnel|Contagem|Total|Contagem de remoção de pacotes de saída da incompatibilidade do seletor de tráfego de um túnel|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Remoção de Pacotes Incompatíveis de TS de Entrada de Túnel|Contagem|Total|Contagem de remoção de pacotes de entrada da incompatibilidade do seletor de tráfego de um túnel|ConnectionName, RemoteIP|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft.Network/expressRoutePorts

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|RxLightLevel|RxLightLevel|Contagem|Média|Nível de luz Rx em dBm|Link, Lane|
|TxLightLevel|TxLightLevel|Contagem|Média|Nível de luz TX em dBm|Link, Lane|
|AdminState|AdminState|Contagem|Média|Estado do administrador da porta|Link|
|LineProtocol|LineProtocol|Contagem|Média|Status do protocolo de linha da porta|Link|
|PortBitsInPerSecond|BitsInPerSecond|CountPerSecond|Média|Bits de entrada no Azure por segundo|Link|
|PortBitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Média|Bits de saída no Azure por segundo|Link|



## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Média|Bits de entrada no Azure por segundo|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Média|Bits de saída no Azure por segundo|PeeringType|
|GlobalReachBitsInPerSecond|GlobalReachBitsInPerSecond|CountPerSecond|Média|Bits de entrada no Azure por segundo|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|GlobalReachBitsOutPerSecond|CountPerSecond|Média|Bits de saída no Azure por segundo|PeeredCircuitSKey|
|BgpAvailability|Disponibilidade de BGP|Porcentagem|Média|Disponibilidade de BGP de MSEE para todos os pares.|PeeringType, par|
|ArpAvailability|Disponibilidade de ARP|Porcentagem|Média|Disponibilidade ARP de MSEE para todos os pares.|PeeringType, par|
|QosDropBitsInPerSecond|DroppedInBitsPerSecond|CountPerSecond|Média|Bits de entrada de dados eliminados por segundo|Nenhum|
|QosDropBitsOutPerSecond|DroppedOutBitsPerSecond|CountPerSecond|Média|Bits de saída de dados eliminados por segundo|Nenhum|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Média|Bits de entrada no Azure por segundo|Nenhum|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Média|Bits de saída no Azure por segundo|Nenhum|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Média|Bits de entrada no Azure por segundo|Nenhum|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Média|Bits de saída no Azure por segundo|Nenhum|

## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft.Network/expressRouteGateways

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|BitsInPerSecond|CountPerSecond|Média|Bits de entrada no Azure por segundo|ConnectionName|
|ErGatewayConnectionBitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Média|Bits de saída no Azure por segundo|ConnectionName|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|QpsByEndpoint|Consultas pelo Ponto de Extremidade Retornado|Contagem|Total|Número de vezes que um ponto de extremidade do Gerenciador de Tráfego foi retornado no período determinado|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Status do Ponto de Extremidade por Ponto de Extremidade|Contagem|Máximo|1 se o status de investigação de um ponto de extremidade for “Habilitado”; caso contrário, 0.|EndpointName|



## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ProbesFailedPercent|Porcentagem de investigações com falha|Porcentagem|Média|Porcentagem de investigações de monitoramento de conectividade com falha|Nenhum|
|AverageRoundtripMs|Média Tempo de ida e volta (ms)|MilliSeconds|Média|Tempo ida e volta de rede médio (ms) para investigações de monitoramento de conectividade enviadas entre a origem e o destino|Nenhum|
|ChecksFailedPercent|Porcentagem de Verificações com Falha (Versão prévia)|Porcentagem|Média|% de verificações de monitoramento de conectividade com falha|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, Destinationname, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName|
|RoundTripTimeMs|Tempo de ida e volta (ms) (Versão prévia)|MilliSeconds|Média|Tempo de ida e volta em milissegundos para as verificações de monitoramento de conectividade|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, Destinationname, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|RequestCount|Contagem de solicitações|Contagem|Total|O número de solicitações de cliente atendidas pelo proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Tamanho da solicitação|Bytes|Total|O número de bytes enviados como solicitações de clientes ao proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Tamanho da resposta|Bytes|Total|O número de bytes enviados como respostas do proxy HTTP/S para clientes|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BillableResponseSize|Tamanho de Resposta Faturável|Bytes|Total|O número de bytes faturáveis (mínimo de 2 KB por solicitação) enviados como respostas do proxy HTTP/S para os clientes.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Contagem de solicitações de back-end|Contagem|Total|O número de solicitações enviadas do proxy HTTP/S aos back-ends|HttpStatus, HttpStatusGroup, back-end|
|BackendRequestLatency|Latência de solicitação de back-end|MilliSeconds|Média|O tempo calculado de quando a solicitação foi enviada pelo proxy HTTP/S ao back-end até o proxy HTTP/S receber o último byte de resposta do back-end|Back-end|
|TotalLatency|Latência total|MilliSeconds|Média|O tempo calculado de quando a solicitação do cliente foi recebida pelo proxy HTTP/S até o cliente confirmar o último byte de resposta do proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Percentual de integridade do back-end|Porcentagem|Média|O percentual de investigações de integridade bem-sucedidas do proxy HTTP/S aos back-ends|Back-end, Httpsettings|
|WebApplicationFirewallRequestCount|Contagem de solicitações do Firewall de Aplicativo Web|Contagem|Total|O número de solicitações de cliente processadas pelo Firewall do Aplicativo Web|PolicyName, RuleName, ação|


## <a name="microsoftnetworkprivatednszones"></a>Microsoft. Network/privateDnsZones

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|QueryVolume|Volume de consulta|Contagem|Total|Número de consultas servidas para uma zona de DNS privado|Nenhum|
|RecordSetCount|Contagem de Conjuntos de Registros|Contagem|Máximo|Número de conjuntos de registros em uma zona de DNS privado|Nenhum|
|RecordSetCapacityUtilization|Utilização de capacidade de Conjuntos de Registros|Porcentagem|Máximo|Porcentagem da capacidade do conjunto de registros utilizada por uma zona de DNS privado|Nenhum|
|VirtualNetworkLinkCount|Contagem de links de rede virtual|Contagem|Máximo|Número de redes virtuais vinculadas a uma zona de DNS privado|Nenhum|
|VirtualNetworkLinkCapacityUtilization|Utilização da capacidade de link de rede virtual|Porcentagem|Máximo|Porcentagem de capacidade de link de rede virtual utilizada por uma zona de DNS privado|Nenhum|
|VirtualNetworkWithRegistrationLinkCount|Contagem de links de registro de rede virtual|Contagem|Máximo|Número de redes virtuais vinculadas a uma zona de DNS privado com o registro automático habilitado|Nenhum|
|VirtualNetworkWithRegistrationCapacityUtilization|Utilização da capacidade do link de registro de rede virtual|Porcentagem|Máximo|Percentual de link de rede virtual com a capacidade de registro automático utilizada por uma zona de DNS privado|Nenhum|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|registration.all|Operações de Registro|Contagem|Total|A contagem de todas as operações de registro bem-sucedidas (consultas de atualizações de criações e exclusões). |Nenhum|
|registration.create|Operação de criação de registro|Contagem|Total|A contagem de todas as criações de registro bem-sucedidas.|Nenhum|
|registration.update|Operação de atualização de registro|Contagem|Total|A contagem de todas as atualizações de registro bem-sucedidas.|Nenhum|
|registration.get|Operações de leitura do registro|Contagem|Total|A contagem de todas as consultas de registro bem-sucedidas.|Nenhum|
|registration.delete|Operação de exclusão de registro|Contagem|Total|A contagem de todas as exclusões de registro bem-sucedidas.|Nenhum|
|incoming|Mensagens de entrada|Contagem|Total|A contagem de todas as chamadas de API enviadas com sucesso. |Nenhum|
|incoming.scheduled|Notificações por push agendadas enviadas|Contagem|Total|Notificações por push agendadas canceladas|Nenhum|
|incoming.scheduled.cancel|Notificações por push agendadas canceladas|Contagem|Total|Notificações por push agendadas canceladas|Nenhum|
|scheduled.pending|Notificações agendadas pendentes|Contagem|Total|Notificações agendadas pendentes|Nenhum|
|installation.all|Operações de gerenciamento de instalação|Contagem|Total|Operações de gerenciamento de instalação|Nenhum|
|installation.get|Obter operações de instalação|Contagem|Total|Obter operações de instalação|Nenhum|
|installation.upsert|Criar ou atualizar operações de instalação|Contagem|Total|Criar ou atualizar operações de instalação|Nenhum|
|installation.patch|Operações de instalação de patch|Contagem|Total|Operações de instalação de patch|Nenhum|
|installation.delete|Excluir operações de instalação|Contagem|Total|Excluir operações de instalação|Nenhum|
|outgoing.allpns.success|Notificações de sucesso|Contagem|Total|A contagem de todas as notificações bem-sucedidas.|Nenhum|
|outgoing.allpns.invalidpayload|Erros de conteúdo|Contagem|Total|A contagem de pushes que falharam porque o PNS retornou um erro de conteúdo inválido.|Nenhum|
|outgoing.allpns.pnserror|Erros de sistema de notificação externa|Contagem|Total|A contagem de pushes que falharam porque houve um problema na comunicação com o PNS (exclui problemas de autenticação).|Nenhum|
|outgoing.allpns.channelerror|Erros de canal|Contagem|Total|A contagem de pushes que falharam porque o canal era inválido, não associado com o aplicativo correto, restrito ou expirado.|Nenhum|
|outgoing.allpns.badorexpiredchannel|Erros de canal incorreto ou expirado|Contagem|Total|A contagem de pushes que falharam porque o canal/token/registrationId do registro estava expirado ou inválido.|Nenhum|
|outgoing.wns.success|Notificações do WNS bem-sucedidas|Contagem|Total|A contagem de todas as notificações bem-sucedidas.|Nenhum|
|outgoing.wns.invalidcredentials|Erros de autorização do WNS (credenciais inválidas)|Contagem|Total|A contagem de pushes que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas. (O Windows Live não reconhece as credenciais).|Nenhum|
|outgoing.wns.badchannel|Erro de canal inválido do WNS|Contagem|Total|A contagem de pushes que falharam porque o ChannelURI do registro não foi reconhecido (status do WNS: 404 não encontrado).|Nenhum|
|outgoing.wns.expiredchannel|Erro de canal expirado do WNS|Contagem|Total|A contagem de pushes que falharam porque o ChannelURI está expirado (status do WNS: 410 perdido).|Nenhum|
|outgoing.wns.throttled|Notificações restritas do WNS|Contagem|Total|A contagem de pushes que falharam porque o WNS está limitando este aplicativo (status do WNS: 406 não aceitável).|Nenhum|
|outgoing.wns.tokenproviderunreachable|Erros de autorização do WNS (inacessível)|Contagem|Total|O Windows Live não está acessível.|Nenhum|
|outgoing.wns.invalidtoken|Erros de autorização do WNS (token inválido)|Contagem|Total|O token fornecido ao WNS não é válido (status do WNS: 401 não autorizado).|Nenhum|
|outgoing.wns.wrongtoken|Erros de autorização do WNS (token incorreto)|Contagem|Total|O token fornecido ao WNS é válido, mas para outro aplicativo (status do WNS: 403 proibido). Isso pode acontecer se o ChannelURI do registro estiver associado a outro aplicativo. Verifique se o aplicativo cliente está associado ao mesmo aplicativo cujas credenciais estão no hub de notificação.|Nenhum|
|outgoing.wns.invalidnotificationformat|Formato de notificação inválido do WNS|Contagem|Total|O formato da notificação é inválido (status do WNS: 400). Observe que o WNS não rejeita todos os conteúdos inválidos.|Nenhum|
|outgoing.wns.invalidnotificationsize|Erro de tamanho de notificação inválido do WNS|Contagem|Total|O conteúdo de notificação é muito grande (status do WNS: 413).|Nenhum|
|outgoing.wns.channelthrottled|Canal do WNS restrito|Contagem|Total|A notificação foi descartada porque o ChannelURI do registro está restrito (cabeçalho de resposta do WNS: X-WNS-NotificationStatus:channelThrottled).|Nenhum|
|outgoing.wns.channeldisconnected|Canal do WNS desconectado|Contagem|Total|A notificação foi descartada porque o ChannelURI do registro está restrito (cabeçalho de resposta do WNS: X-WNS-DeviceConnectionStatus: disconnected).|Nenhum|
|outgoing.wns.dropped|Notificações do WNS descartadas|Contagem|Total|A notificação foi descartada porque o ChannelURI do registro está restrito (X-WNS-NotificationStatus: dropped but not X-WNS-DeviceConnectionStatus: disconnected).|Nenhum|
|outgoing.wns.pnserror|Erros do WNS|Contagem|Total|Notificação não entregue devido a erros de comunicação com o WNS.|Nenhum|
|outgoing.wns.authenticationerror|Erros de autenticação do WNS|Contagem|Total|Notificação não entregue devido a erros de comunicação com o Windows Live, credenciais inválidas ou token incorreto.|Nenhum|
|outgoing.apns.success|Notificações do APNS bem-sucedidas|Contagem|Total|A contagem de todas as notificações bem-sucedidas.|Nenhum|
|outgoing.apns.invalidcredentials|Erros de autorização do APNS|Contagem|Total|A contagem de pushes que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Nenhum|
|outgoing.apns.badchannel|Erro de canal inválido do APNS|Contagem|Total|A contagem de pushes que falharam porque o token é inválido (código de status do APNS: 8).|Nenhum|
|outgoing.apns.expiredchannel|Erro de canal expirado do APNS|Contagem|Total|A contagem de tokens que foram invalidados pelo canal de comentários do APNS.|Nenhum|
|outgoing.apns.invalidnotificationsize|Erro de tamanho de notificação inválido do APNS|Contagem|Total|A contagem de pushes que falharam porque o conteúdo era muito grande (código de status do APNS: 7).|Nenhum|
|outgoing.apns.pnserror|Erros do APNS|Contagem|Total|A contagem de pushes que falharam devido a erros de comunicação com o APNS.|Nenhum|
|outgoing.gcm.success|Notificações do GCM bem-sucedidas|Contagem|Total|A contagem de todas as notificações bem-sucedidas.|Nenhum|
|outgoing.gcm.invalidcredentials|Erros de autorização do GCM (credenciais inválidas)|Contagem|Total|A contagem de pushes que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Nenhum|
|outgoing.gcm.badchannel|Erro de canal inválido do GCM|Contagem|Total|A contagem de pushes que falharam porque o registrationId do registro estava expirado ou inválido (resultado GCM: registo inválido).|Nenhum|
|outgoing.gcm.expiredchannel|Erro de canal expirado do GCM|Contagem|Total|A contagem de pushes que falharam porque o registrationId do registro estava expirado (resultado do GCM: NotRegistered).|Nenhum|
|outgoing.gcm.throttled|Notificações restritas do GCM|Contagem|Total|A contagem de pushes que falharam porque o GCM restringiu este aplicativo (código de status do GCM: 501 a 599 ou resultado: indisponível).|Nenhum|
|outgoing.gcm.invalidnotificationformat|Formato de notificação inválido do GCM|Contagem|Total|A contagem de pushes que falharam porque o conteúdo não foi formatado corretamente (resultado do GCM: InvalidDataKey ou InvalidTtl).|Nenhum|
|outgoing.gcm.invalidnotificationsize|Erro de tamanho de notificação inválido do GCM|Contagem|Total|A contagem de pushes que falharam porque o conteúdo era muito grande (resultado do GCM: MessageTooBig).|Nenhum|
|outgoing.gcm.wrongchannel|Erro de canal incorreto do GCM|Contagem|Total|A contagem de pushes que falharam porque o registrationId do registro não está associado ao aplicativo atual (resultado do GCM: InvalidPackageName).|Nenhum|
|outgoing.gcm.pnserror|Erros do GCM|Contagem|Total|A contagem de pushes que falharam devido a erros de comunicação com o GCM.|Nenhum|
|outgoing.gcm.authenticationerror|Erros de autenticação do GCM|Contagem|Total|A contagem de pushes que falharam porque o PNS não aceitou as credenciais fornecidas, as credenciais estão bloqueadas ou o SenderId não está configurado corretamente no aplicativo (resultado do GCM: MismatchedSenderId).|Nenhum|
|outgoing.mpns.success|Notificações de sucesso do MPNS|Contagem|Total|A contagem de todas as notificações bem-sucedidas.|Nenhum|
|outgoing.mpns.invalidcredentials|Credenciais inválidas do MPNS|Contagem|Total|A contagem de pushes que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Nenhum|
|outgoing.mpns.badchannel|Erro de canal inválido do MPNS|Contagem|Total|A contagem de pushes que falharam porque o ChannelURI do registro não foi reconhecido (status do MPNS: 404 não encontrado).|Nenhum|
|outgoing.mpns.throttled|Notificações restritas do MPNS|Contagem|Total|A contagem de pushes que falharam porque o MPNS está limitando este aplicativo (WNS MPNS: 406 não aceitável).|Nenhum|
|outgoing.mpns.invalidnotificationformat|Erro de tamanho de notificação inválido do MPNS|Contagem|Total|A contagem de pushes que falharam porque o conteúdo da notificação era muito grande.|Nenhum|
|outgoing.mpns.channeldisconnected|Canal do MPNS desconectado|Contagem|Total|A contagem de pushes que falharam porque o ChannelURI do registro foi desconectado (status do MPNS: 412 não encontrado).|Nenhum|
|outgoing.mpns.dropped|Notificações descartadas do MPNS|Contagem|Total|A contagem de pushes que foram descartados pelo MPNS (cabeçalho de resposta do MPNS: X-NotificationStatus: QueueFull ou Suppressed).|Nenhum|
|outgoing.mpns.pnserror|Erros do MPNS|Contagem|Total|A contagem de pushes que falharam devido a erros de comunicação com o MPNS.|Nenhum|
|outgoing.mpns.authenticationerror|Erros de autenticação do MPNS|Contagem|Total|A contagem de pushes que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Nenhum|
|notificationhub.pushes|Todas as notificações de saída|Contagem|Total|Todas as notificações de saída do hub de notificação|Nenhum|
|incoming.all.requests|Todas as solicitações recebidas|Contagem|Total|Total de solicitações recebidas para um hub de notificação|Nenhum|
|incoming.all.failedrequests|Todas as solicitações com falha recebidas|Contagem|Total|Total de solicitações com falha recebidas para um hub de notificação|Nenhum|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Average_% Free Inodes|% de Inodes livres|Contagem|Média|Average_% Free Inodes|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% Free Space|% de Espaço Livre|Contagem|Média|Average_% Free Space|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% Used Inodes|% de Inodes Usados|Contagem|Média|Average_% Used Inodes|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% Used Space|% de Espaço Usado|Contagem|Média|Average_% Used Space|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Disk Read Bytes/sec|Bytes Lidos no Disco/s|Contagem|Média|Average_Disk Read Bytes/sec|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Disk Reads/sec|Leituras de Disco/s|Contagem|Média|Average_Disk Reads/sec|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Disk Transfers/sec|Transferências de Disco/s|Contagem|Média|Average_Disk Transfers/sec|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Disk Write Bytes/sec|Bytes Gravados no Disco/s|Contagem|Média|Average_Disk Write Bytes/sec|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Disk Writes/sec|Gravações de Disco/s|Contagem|Média|Average_Disk Writes/sec|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Free Megabytes|Megabytes Livres|Contagem|Média|Average_Free Megabytes|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Logical Disk Bytes/sec|Bytes de Disco Lógico/s|Contagem|Média|Average_Logical Disk Bytes/sec|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% Available Memory|% de Memória Disponível|Contagem|Média|Average_% Available Memory|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% Available Swap Space|% de Espaço de Permuta Disponível|Contagem|Média|Average_% Available Swap Space|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% Used Memory|% de Memória Usada|Contagem|Média|Average_% Used Memory|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% Used Swap Space|% de Espaço de Permuta Usado|Contagem|Média|Average_% Used Swap Space|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Available MBytes Memory|MBytes de Memória Disponíveis|Contagem|Média|Average_Available MBytes Memory|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Available MBytes Swap|MBytes de Espaço de Permuta Disponíveis|Contagem|Média|Average_Available MBytes Swap|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Page Reads/sec|Leituras de Página/s|Contagem|Média|Average_Page Reads/sec|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Page Writes/sec|Gravações de Página/s|Contagem|Média|Average_Page Writes/sec|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Pages/sec|Páginas/s|Contagem|Média|Average_Pages/sec|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Used MBytes Swap Space|MBytes de Espaço de Permuta Usado|Contagem|Média|Average_Used MBytes Swap Space|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Used Memory MBytes|MBytes de Memória Usada|Contagem|Média|Average_Used Memory MBytes|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Total Bytes Transmitted|Total de Bytes Transmitidos|Contagem|Média|Average_Total Bytes Transmitted|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Total Bytes Received|Total de Bytes Recebidos|Contagem|Média|Average_Total Bytes Received|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Total Bytes|Total de Bytes|Contagem|Média|Average_Total Bytes|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Total Packets Transmitted|Total de Pacotes Transmitidos|Contagem|Média|Average_Total Packets Transmitted|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Total Packets Received|Total de Pacotes Recebidos|Contagem|Média|Average_Total Packets Received|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Total Rx Errors|Total de Erros de Rx|Contagem|Média|Average_Total Rx Errors|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Total Tx Errors|Total de Erros de Tx|Contagem|Média|Average_Total Tx Errors|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Total Collisions|Total de Colisões|Contagem|Média|Average_Total Collisions|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Avg. de segundos/Leitura do Disco|Média de segundos/Leitura do Disco|Contagem|Média|Average_Avg. de segundos/Leitura do Disco|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Avg. de segundos/Transferência do Disco|Média de segundos/Transferência do Disco|Contagem|Média|Average_Avg. de segundos/Transferência do Disco|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Avg. de segundos/Gravação do Disco|Média de segundos/Gravação do Disco|Contagem|Média|Average_Avg. de segundos/Gravação do Disco|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Physical Disk Bytes/sec|Bytes/s do Disco Físico|Contagem|Média|Average_Physical Disk Bytes/sec|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Pct Privileged Time|% de Tempo Privilegiado|Contagem|Média|Average_Pct Privileged Time|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Pct User Time|% de Tempo do Usuário|Contagem|Média|Average_Pct User Time|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Used Memory kBytes|KBytes de Memória Usada|Contagem|Média|Average_Used Memory kBytes|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Virtual Shared Memory|Memória Virtual Compartilhada|Contagem|Média|Average_Virtual Shared Memory|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% DPC Time|% de Tempo de DPC|Contagem|Média|Average_% DPC Time|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% Idle Time|% de Tempo Ocioso|Contagem|Média|Average_% Idle Time|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% Interrupt Time|% de Tempo de Interrupção|Contagem|Média|Average_% Interrupt Time|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% IO Wait Time|% de Tempo de Espera de E/S|Contagem|Média|Average_% IO Wait Time|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% Nice Time|% de Tempo Adequado|Contagem|Média|Average_% Nice Time|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% Privileged Time|% de Tempo Privilegiado|Contagem|Média|Average_% Privileged Time|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% Processor Time|% Tempo do Processador|Contagem|Média|Average_% Processor Time|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% User Time|% de Tempo do Usuário|Contagem|Média|Average_% User Time|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Free Physical Memory|Memória Física Livre|Contagem|Média|Average_Free Physical Memory|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Free Space in Paging Files|Espaço Livre em Arquivos de Paginação|Contagem|Média|Average_Free Space in Paging Files|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Free Virtual Memory|Memória Virtual Livre|Contagem|Média|Average_Free Virtual Memory|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Processes|Processos|Contagem|Média|Average_Processes|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Size Stored In Paging Files|Tamanho Armazenado em Arquivos de Paginação|Contagem|Média|Average_Size Stored In Paging Files|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Uptime|Tempo de atividade|Contagem|Média|Average_Uptime|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Users|Usuários|Contagem|Média|Average_Users|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Current Disk Queue Length|Comprimento da fila atual de disco|Contagem|Média|Average_Current Disk Queue Length|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Available MBytes|MBytes Disponíveis|Contagem|Média|Average_Available MBytes|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_% Committed Bytes In Use|% de Bytes Confirmados em Uso|Contagem|Média|Average_% Committed Bytes In Use|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Bytes Received/sec|Bytes Recebidos/s|Contagem|Média|Average_Bytes Received/sec|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Bytes Sent/sec|Bytes Enviados/s|Contagem|Média|Average_Bytes Sent/sec|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Bytes Total/sec|Bytes Totais/s|Contagem|Média|Average_Bytes Total/sec|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Average_Processor Queue Length|Tamanho da fila do processador|Contagem|Média|Average_Processor Queue Length|Computador, ObjectName, InstanceName, caminho, SourceSystem|
|Pulsação|Pulsação|Contagem|Total|Pulsação|Computador, OSType, versão, SourceComputerId|
|Atualizar|Atualizar|Contagem|Média|Atualizar|Computador, produto, classificação, UpdateState, opcional, aprovado|
|Evento|Evento|Contagem|Média|Evento|Origem, EventLog, computador, EventCategory, EventLevel, EventLevelName, EventID|

## <a name="microsoftpeeringpeeringservices"></a>Microsoft. emparelhamento/peeringServices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PrefixLatency|Latência de prefixo|Milissegundos|Média|Latência de prefixo mediana|Prefixoname|

## <a name="microsoftpeeringpeerings"></a>Microsoft. emparelhamento/emparelhamentos

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|SessionAvailabilityV4|Disponibilidade de sessão v4|Porcentagem|Média|Disponibilidade da sessão v4|ConnectionId|
|SessionAvailabilityV6|Disponibilidade de sessão V6|Porcentagem|Média|Disponibilidade da sessão V6|ConnectionId|
|IngressTrafficRate|Taxa de tráfego de entrada|BitsPerSecond|Média|Taxa de tráfego de entrada em bits por segundo|ConnectionId|
|EgressTrafficRate|Taxa de tráfego de saída|BitsPerSecond|Média|Taxa de tráfego de saída em bits por segundo|ConnectionId|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|QueryDuration|Duração da consulta|Milissegundos|Média|Duração da consulta DAX no último intervalo|Sem dimensões|
|QueryPoolJobQueueLength|Threads: Tamanho da fila de trabalhos do pool consultas|Contagem|Média|Número de trabalhos na fila do pool de threads de consulta.|Sem dimensões|
|qpu_high_utilization_metric|Alta utilização de QPU|Contagem|Total|QPU de alta utilização no último minuto, 1 para QPU de alta utilização, caso contrário, 0|Sem dimensões|
|memory_metric|Memória|Bytes|Média|Memória. Intervalo de 0 a 3 GB para A1, 0 a 5 GB para A2, 0 a 10 GB para A3, 0 a 25 GB para A4, 0 a 50 GB para A5 e 0 a 100 GB para A6|Sem dimensões|
|memory_thrashing_metric|Sobrecarga de memória|Porcentagem|Média|Sobrecarga de memória média.|Sem dimensões|


## <a name="microsoftprojectbabylonaccounts"></a>Microsoft. ProjectBabylon/accounts

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|AssetDistributionByClassification|Distribuição de ativos por classificação|Contagem|Total|Indica o número de ativos com uma determinada classificação atribuída, ou seja, eles são classificados com esse rótulo.|Classificação, origem, ResourceId|
|AssetDistributionByStorageType|Distribuição de ativos por tipo de armazenamento|Contagem|Total|Indica o número de ativos com um determinado tipo de armazenamento.|Storagetype, ResourceId|
|CatalogActiveUsers|Usuários ativos diariamente|Contagem|Total|Número de usuários ativos diariamente|ResourceId|
|CatalogUsage|Distribuição de uso por operação|Contagem|Total|Indique o número de operações que o usuário faz no catálogo, ou seja, acessar, Pesquisar, Glossário.|Operação, ResourceId|
|NumberOfAssetsWithClassifications|Número de ativos com pelo menos uma classificação|Contagem|Média|Indica o número de ativos com pelo menos uma classificação de marca.|ResourceId|
|ScanCancelled|Verificação cancelada|Contagem|Total|Indica o número de verificações canceladas.|ResourceId|
|ScanCompleted|Verificação concluída|Contagem|Total|Indica o número de verificações concluídas com êxito.|ResourceId|
|ScanFailed|Falha na verificação|Contagem|Total|Indica o número de verificações que falharam.|ResourceId|
|ScanTimeTaken|Tempo de verificação tomado|Segundos|Total|Indica o tempo total de verificação em segundos.|ResourceId|




## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|Contagem|Total|ListenerConnections bem-sucedido para o Microsoft.Relay.|EntityName, OperationResult|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Contagem|Total|ClientError no ListenerConnections para o Microsoft.Relay.|EntityName, OperationResult|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Contagem|Total|ServerError no ListenerConnections para o Microsoft.Relay.|EntityName, OperationResult|
|SenderConnections-Success|SenderConnections-Success|Contagem|Total|SenderConnections bem-sucedido para o Microsoft.Relay.|EntityName, OperationResult|
|SenderConnections-ClientError|SenderConnections-ClientError|Contagem|Total|ClientError no SenderConnections para o Microsoft.Relay.|EntityName, OperationResult|
|SenderConnections-ServerError|SenderConnections-ServerError|Contagem|Total|ServerError no SenderConnections para o Microsoft.Relay.|EntityName, OperationResult|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Contagem|Total|Total de ListenerConnections para o  Microsoft.Relay.|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Contagem|Total|Total de solicitações SenderConnections para o Microsoft.Relay.|EntityName|
|ActiveConnections|ActiveConnections|Contagem|Total|Total de ActiveConnections para o Microsoft.Relay.|EntityName|
|ActiveListeners|ActiveListeners|Contagem|Total|Total de ActiveListeners para o Microsoft.Relay.|EntityName|
|BytesTransferred|BytesTransferred|Contagem|Total|Total de BytesTransferred para o Microsoft.Relay.|EntityName|
|ListenerDisconnects|ListenerDisconnects|Contagem|Total|Total de ListenerDisconnects para o Microsoft.Relay.|EntityName|
|SenderDisconnects|SenderDisconnects|Contagem|Total|Total de SenderDisconnects para o Microsoft.Relay.|EntityName|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|SearchLatency|Latência de pesquisa|Segundos|Média|Latência média de pesquisa do serviço de pesquisa|Nenhum|
|SearchQueriesPerSecond|Consultas de pesquisa por segundo|CountPerSecond|Média|Consultas de pesquisa por segundo para o serviço de pesquisa|Nenhum|
|ThrottledSearchQueriesPercentage|Porcentagem das consultas de pesquisa limitadas|Porcentagem|Média|Porcentagem de consultas de pesquisa que eram limitadas para o serviço de pesquisa|Nenhum|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|SuccessfulRequests|Solicitações bem sucedidas|Contagem|Total|Total de solicitações bem-sucedidas para um namespace|EntityName, OperationResult|
|ServerErrors|Erros do Servidor.|Contagem|Total|Erros do Servidor para o Microsoft.ServiceBus.|EntityName, OperationResult|
|UserErrors|Erros de Usuário.|Contagem|Total|Erros de Usuário para o Microsoft.ServiceBus.|EntityName, OperationResult|
|ThrottledRequests|Solicitações Limitadas.|Contagem|Total|Solicitações Limitadas para o Microsoft.ServiceBus.|EntityName, OperationResult|
|IncomingRequests|Solicitações de entrada|Contagem|Total|Solicitações de Entrada para o Microsoft.ServiceBus.|EntityName|
|IncomingMessages|Mensagens de entrada|Contagem|Total|Mensagens de Entrada para Microsoft.ServiceBus.|EntityName|
|OutgoingMessages|Mensagens de saída|Contagem|Total|Mensagens de Saída para o Microsoft.ServiceBus.|EntityName|
|ActiveConnections|ActiveConnections|Contagem|Total|Conexões Totalmente Ativas para o Microsoft.ServiceBus.|Nenhum|
|ConnectionsOpened|Conexões Abertas.|Contagem|Média|Conexões Abertas para o Microsoft.ServiceBus.|EntityName|
|ConnectionsClosed|Conexões Fechadas.|Contagem|Média|Conexões Fechadas para o Microsoft.ServiceBus.|EntityName|
|Tamanho|Tamanho|Bytes|Média|Tamanho de uma fila/tópico em bytes.|EntityName|
|Mensagens|Contagem de mensagens em uma fila/tópico.|Contagem|Média|Contagem de mensagens em uma fila/tópico.|EntityName|
|ActiveMessages|Contagem de mensagens em uma fila/tópico.|Contagem|Média|Contagem de mensagens em uma fila/tópico.|EntityName|
|DeadletteredMessages|Contagem de mensagens mortas em uma Fila/Tópico.|Contagem|Média|Contagem de mensagens mortas em uma Fila/Tópico.|EntityName|
|ScheduledMessages|Contagem de mensagens agendadas em uma Fila/Tópico.|Contagem|Média|Contagem de mensagens agendadas em uma Fila/Tópico.|EntityName|
|NamespaceCpuUsage|CPU|Porcentagem|Máximo|Métrica de uso de CPU do namespace Premium do barramento de serviço.|Réplica|
|NamespaceMemoryUsage|Uso de Memória|Porcentagem|Máximo|Métrica de uso de memória do namespace Premium do barramento de serviço.|Réplica|
|CPUXNS|CPU (Preterido)|Porcentagem|Máximo|Métrica de uso de CPU do namespace Premium do barramento de serviço. Essa métrica é preterida. Em vez disso, use a métrica de CPU (NamespaceCpuUsage).|Réplica|
|WSXNS|Uso de Memória (Preterido)|Porcentagem|Máximo|Métrica de uso de memória do namespace Premium do barramento de serviço. Essa métrica foi preterida. Use a métrica de uso de memória (NamespaceMemoryUsage) em vez disso.|Réplica|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Contagem|Média|CPU alocada para este contêiner em núcleos de mili|ApplicationName, ServiceName, CodePackageName, ServiceId|
|AllocatedMemory|AllocatedMemory|Bytes|Média|Memória alocada para este contêiner em MB|ApplicationName, ServiceName, CodePackageName, ServiceId|
|ActualCpu|ActualCpu|Contagem|Média|Uso real da CPU em núcleos de mili|ApplicationName, ServiceName, CodePackageName, ServiceId|
|ActualMemory|ActualMemory|Bytes|Média|Uso real da memória em MB|ApplicationName, ServiceName, CodePackageName, ServiceId|
|CpuUtilization|CpuUtilization|Porcentagem|Média|Utilização da CPU para este contêiner como porcentagem de AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceId|
|MemoryUtilization|MemoryUtilization|Porcentagem|Média|Utilização da CPU para este contêiner como porcentagem de AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceId|
|ApplicationStatus|ApplicationStatus|Contagem|Média|Status do aplicativo de malha Service Fabric|ApplicationName, status|
|ServiceStatus|ServiceStatus|Contagem|Média|Status de integridade de um serviço no aplicativo de malha Service Fabric|ApplicationName, status, ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|Contagem|Média|Status de integridade de uma réplica de serviço no aplicativo de malha Service Fabric|ApplicationName, status, ServiceName, nome de réplica|
|ContainerStatus|ContainerStatus|Contagem|Média|Status do contêiner no aplicativo de malha Service Fabric|ApplicationName, ServiceName, CodePackageName, ServiceId, status|
|RestartCount|RestartCount|Contagem|Média|Reiniciar a contagem de um contêiner no aplicativo de malha Service Fabric|ApplicationName, status, ServiceName, nome de réplica, CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ConnectionCount|Contagem de conexão|Contagem|Máximo|A quantidade de conexões do usuário.|Ponto de extremidade|
|MessageCount|Contagem de mensagens|Contagem|Total|A quantidade total de mensagens.|Nenhum|
|InboundTraffic|Tráfego de entrada|Bytes|Total|O tráfego de entrada do serviço|Nenhum|
|OutboundTraffic|Tráfego de saída|Bytes|Total|O tráfego de saída do serviço|Nenhum|
|UserErrors|Erros do usuário|Porcentagem|Máximo|A porcentagem de erros do usuário|Nenhum|
|SystemErrors|Erros do sistema|Porcentagem|Máximo|A porcentagem de erros do sistema|Nenhum|



## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Percentual de CPU|Porcentagem|Média|Percentual de CPU|Nenhum|
|physical_data_read_percent|Porcentagem de E/S de dados|Porcentagem|Média|Porcentagem de E/S de dados|Nenhum|
|log_write_percent|Porcentagem de E/S de log|Porcentagem|Média|Percentual de e/s de log. Não aplicável a data warehouses.|Nenhum|
|dtu_consumption_percent|Porcentagem de DTU|Porcentagem|Média|Porcentagem de DTU. Aplica-se a bancos de dados baseados em DTU.|Nenhum|
|armazenamento|Espaço de dados usado|Bytes|Máximo|Espaço de dados usado. Não aplicável a data warehouses.|Nenhum|
|connection_successful|Conexões bem sucedidas|Contagem|Total|Conexões bem sucedidas|Nenhum|
|connection_failed|Conexões com falha|Contagem|Total|Conexões com falha|Nenhum|
|blocked_by_firewall|Bloqueado pelo firewall|Contagem|Total|Bloqueado pelo firewall|Nenhum|
|deadlock|Deadlocks|Contagem|Total|Deadlocks. Não aplicável a data warehouses.|Nenhum|
|storage_percent|Porcentagem de espaço de dados usado|Porcentagem|Máximo|Porcentagem de espaço de dados usada. Não aplicável a data warehouses ou a bancos de dados de hiperescala.|Nenhum|
|xtp_storage_percent|Percentual de armazenamento do OLTP na memória|Porcentagem|Média|Porcentagem de armazenamento OLTP na memória. Não aplicável a data warehouses.|Nenhum|
|workers_percent|Porcentagem de funcionários|Porcentagem|Média|Porcentagem de trabalhadores. Não aplicável a data warehouses.|Nenhum|
|sessions_percent|Porcentagem de sessões|Porcentagem|Média|Porcentagem de sessões. Não aplicável a data warehouses.|Nenhum|
|dtu_limit|Limite de DTU|Contagem|Média|Limite de DTU. Aplica-se a bancos de dados baseados em DTU.|Nenhum|
|dtu_used|DTU usado|Contagem|Média|DTU usado. Aplica-se a bancos de dados baseados em DTU.|Nenhum|
|cpu_limit|Limite da CPU|Contagem|Média|Limite de CPU. Aplica-se a bancos de dados baseados em vCore.|Nenhum|
|cpu_used|CPU usada|Contagem|Média|CPU usada. Aplica-se a bancos de dados baseados em vCore.|Nenhum|
|dwu_limit|Limite de DWU|Contagem|Máximo|Limite de DWU. Aplica-se somente a data warehouses.|Nenhum|
|dwu_consumption_percent|Porcentagem de DWU|Porcentagem|Máximo|Porcentagem de DWU. Aplica-se somente a data warehouses.|Nenhum|
|dwu_used|DWU usado|Contagem|Máximo|DWU usado. Aplica-se somente a data warehouses.|Nenhum|
|cache_hit_percent|Percentual de ocorrência no cache|Porcentagem|Máximo|Porcentagem de acesso ao cache. Aplica-se somente a data warehouses.|Nenhum|
|cache_used_percent|Percentual de cache usado|Porcentagem|Máximo|Percentual de cache usado. Aplica-se somente a data warehouses.|Nenhum|
|sqlserver_process_core_percent<sup>1</sup> |Porcentagem de núcleo do processo de SQL Server|Porcentagem|Máximo|Percentual de uso da CPU para o processo de SQL Server, conforme medido pelo sistema operacional.|Nenhum|
|sqlserver_process_memory_percent<sup>1</sup> |Porcentagem de memória de processo SQL Server|Porcentagem|Máximo|Porcentagem de uso de memória para o processo de SQL Server, conforme medido pelo sistema operacional.|Nenhum|
|tempdb_data_size<sup>1</sup> |Quilobytes do Tamanho do Arquivo de Dados Tempdb|Contagem|Máximo|Tamanho do arquivo de dados tempdb em kilobytes.|Nenhum|
|tempdb_log_size<sup>1</sup> |Quilobytes do Tamanho do Arquivo de Log Tempdb|Contagem|Máximo|Tamanho do arquivo de log de tempdb em kilobytes.|Nenhum|
|tempdb_log_used_percent<sup>1</sup> |Porcentagem de Uso do Log Tempdb|Porcentagem|Máximo|Log de porcentagem de tempdb usado.|Nenhum|
|local_tempdb_usage_percent|Porcentagem de local de tempdb|Porcentagem|Média|Porcentagem de tempdb local. Aplica-se somente a data warehouses.|Nenhum|
|app_cpu_billed|CPU do aplicativo cobrado|Contagem|Total|CPU do aplicativo cobrada. Aplica-se a bancos de dados sem servidor.|Nenhum|
|app_cpu_percent|Percentual de CPU do aplicativo|Porcentagem|Média|Porcentagem de CPU do aplicativo. Aplica-se a bancos de dados sem servidor.|Nenhum|
|app_memory_percent|Percentual de memória do aplicativo|Porcentagem|Média|Porcentagem de memória do aplicativo. Aplica-se a bancos de dados sem servidor.|Nenhum|
|allocated_data_storage|Espaço alocado de dados|Bytes|Média|Armazenamento de dados alocado. Não aplicável a data warehouses.|Nenhum|
|memory_usage_percent|Percentual de memória|Porcentagem|Máximo|Porcentagem de memória. Aplica-se somente a data warehouses.|Nenhum|
|dw_backup_size_gb|Tamanho do armazenamento de dados|Contagem|Total|O tamanho do armazenamento de dados é composto do tamanho dos seus dados e do log de transações. A métrica é contada em direção à parte ' armazenamento ' da sua fatura. Aplica-se somente a data warehouses.|Nenhum|
|dw_snapshot_size_gb|Tamanho do armazenamento de instantâneos|Contagem|Total|Tamanho do armazenamento de instantâneos é o tamanho das alterações incrementais capturadas por instantâneos para criar pontos de restauração definidos pelo usuário e automáticos. A métrica é contada em direção à parte ' armazenamento ' da sua fatura. Aplica-se somente a data warehouses.|Nenhum|
|dw_geosnapshot_size_gb|Tamanho do armazenamento de recuperação de desastre|Contagem|Total|O tamanho do armazenamento de recuperação de desastres é refletido como ' armazenamento de recuperação de desastres ' em sua fatura. Aplica-se somente a data warehouses.|Nenhum|
|wlg_allocation_relative_to_system_percent|Percentual de alocação do grupo de carga de trabalho por sistema|Porcentagem|Máximo|Porcentagem alocada de recursos em relação a todo o sistema por grupo de carga de trabalho. Aplica-se somente a data warehouses.|WorkloadGroupName,IsUserDefined|
|wlg_allocation_relative_to_wlg_effective_cap_percent|Porcentagem de alocação do grupo de cargas de trabalho por limite|Porcentagem|Máximo|Porcentagem alocada de recursos em relação aos recursos de Cap especificados por grupo de carga de trabalho. Aplica-se somente a data warehouses.|WorkloadGroupName,IsUserDefined|
|wlg_active_queries|Consultas ativas do grupo de carga de trabalho|Contagem|Total|Consultas ativas dentro do grupo de cargas de trabalho. Aplica-se somente a data warehouses.|WorkloadGroupName,IsUserDefined|
|wlg_queued_queries|Consultas na fila do grupo de carga de trabalho|Contagem|Total|Consultas em fila no grupo de cargas de trabalho. Aplica-se somente a data warehouses.|WorkloadGroupName,IsUserDefined|
|active_queries|Consultas ativas|Contagem|Total|Consultas ativas em todos os grupos de carga de trabalho. Aplica-se somente a data warehouses.|Nenhum|
|queued_queries|Consultas em fila|Contagem|Total|Consultas em fila em todos os grupos de carga de trabalho. Aplica-se somente a data warehouses.|Nenhum|
|wlg_active_queries_timeouts|Tempos limite de consulta do grupo de carga de trabalho|Contagem|Total|Consultas que atingiram o tempo limite para o grupo de carga de trabalho. Aplica-se somente a data warehouses.|WorkloadGroupName,IsUserDefined|
|wlg_effective_min_resource_percent|Percentual mínimo efetivo de recursos|Porcentagem|Máximo|Percentual mínimo de recursos reservados e isolados para o grupo de carga de trabalho, levando em conta o nível de serviço mínimo. Aplica-se somente a data warehouses.|WorkloadGroupName,IsUserDefined|
|wlg_effective_cap_resource_percent|Percentual de limite efetivo de recursos|Porcentagem|Máximo|Um limite rígido na porcentagem de recursos permitidos para o grupo de cargas de trabalho, levando em conta o percentual de recursos mínimo efetivo alocado para outros grupos de carga de trabalho. Aplica-se somente a data warehouses.|WorkloadGroupName,IsUserDefined|
|full_backup_size_bytes|Tamanho de armazenamento de backup completo|Bytes|Máximo|Tamanho de armazenamento de backup completo cumulativo. Aplica-se a bancos de dados baseados em vCore. Não aplicável a bancos de dados de hiperescala.|Nenhum|
|diff_backup_size_bytes|Tamanho de armazenamento diferencial de backup|Bytes|Máximo|Tamanho do armazenamento de backup diferencial cumulativo. Aplica-se a bancos de dados baseados em vCore. Não aplicável a bancos de dados de hiperescala.|Nenhum|
|log_backup_size_bytes|Tamanho do armazenamento de backup de log|Bytes|Máximo|Tamanho de armazenamento de backup de log cumulativo. Aplica-se a bancos de dados com base em vCore e em hiperescala.|Nenhum|
|snapshot_backup_size_bytes|Tamanho do armazenamento de backup de instantâneo|Bytes|Máximo|Tamanho de armazenamento de backup de instantâneo cumulativo. Aplica-se a bancos de dados de hiperescala.|Nenhum|
|base_blob_size_bytes|Tamanho do armazenamento de BLOBs base|Bytes|Máximo|Tamanho do armazenamento de BLOBs base. Aplica-se a bancos de dados de hiperescala.|Nenhum|

<sup>1</sup> essa métrica está disponível para bancos de dados usando o modelo de compra vCore com 2 vCores e superior, ou 200 DTU e superior para o modelo de compra baseado em DTU. 

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Percentual de CPU|Porcentagem|Média|Percentual de CPU|Nenhum|
|database_cpu_percent|Percentual de CPU|Porcentagem|Média|Percentual de CPU|DatabaseResourceId|
|physical_data_read_percent|Porcentagem de E/S de dados|Porcentagem|Média|Porcentagem de E/S de dados|Nenhum|
|database_physical_data_read_percent|Porcentagem de E/S de dados|Porcentagem|Média|Porcentagem de E/S de dados|DatabaseResourceId|
|log_write_percent|Porcentagem de E/S de log|Porcentagem|Média|Porcentagem de E/S de log|Nenhum|
|database_log_write_percent|Porcentagem de E/S de log|Porcentagem|Média|Porcentagem de E/S de log|DatabaseResourceId|
|dtu_consumption_percent|Porcentagem de DTU|Porcentagem|Média|Porcentagem de DTU. Aplica-se a pools elásticos baseados em DTU.|Nenhum|
|database_dtu_consumption_percent|Porcentagem de DTU|Porcentagem|Média|Porcentagem de DTU|DatabaseResourceId|
|storage_percent|Porcentagem de espaço de dados usado|Porcentagem|Média|Porcentagem de espaço de dados usado|Nenhum|
|workers_percent|Porcentagem de funcionários|Porcentagem|Média|Porcentagem de funcionários|Nenhum|
|database_workers_percent|Porcentagem de funcionários|Porcentagem|Média|Porcentagem de funcionários|DatabaseResourceId|
|sessions_percent|Porcentagem de sessões|Porcentagem|Média|Porcentagem de sessões|Nenhum|
|database_sessions_percent|Porcentagem de sessões|Porcentagem|Média|Porcentagem de sessões|DatabaseResourceId|
|eDTU_limit|Limite de eDTU|Contagem|Média|limite de eDTU. Aplica-se a pools elásticos baseados em DTU.|Nenhum|
|storage_limit|Tamanho máximo dos dados|Bytes|Média|Tamanho máximo dos dados|Nenhum|
|eDTU_used|eDTU usado|Contagem|Média|eDTU usado. Aplica-se a pools elásticos baseados em DTU.|Nenhum|
|database_eDTU_used|eDTU usado|Contagem|Média|eDTU usado|DatabaseResourceId|
|storage_used|Espaço de dados usado|Bytes|Média|Espaço de dados usado|Nenhum|
|database_storage_used|Espaço de dados usado|Bytes|Média|Espaço de dados usado|DatabaseResourceId|
|xtp_storage_percent|Percentual de armazenamento do OLTP na memória|Porcentagem|Média|Percentual de armazenamento do OLTP na memória|Nenhum|
|cpu_limit|Limite da CPU|Contagem|Média|Limite de CPU. Aplica-se a pools elásticos baseados em vCore.|Nenhum|
|database_cpu_limit|Limite da CPU|Contagem|Média|Limite da CPU|DatabaseResourceId|
|cpu_used|CPU usada|Contagem|Média|CPU usada. Aplica-se a pools elásticos baseados em vCore.|Nenhum|
|database_cpu_used|CPU usada|Contagem|Média|CPU usada|DatabaseResourceId|
|sqlserver_process_core_percent<sup>1</sup>|Porcentagem de núcleo do processo de SQL Server|Porcentagem|Máximo|Percentual de uso da CPU para o processo de SQL Server, conforme medido pelo sistema operacional. Aplica-se a pools elásticos. |Nenhum|
|sqlserver_process_memory_percent<sup>1</sup>|Porcentagem de memória de processo SQL Server|Porcentagem|Máximo|Porcentagem de uso de memória para o processo de SQL Server, conforme medido pelo sistema operacional. Aplica-se a pools elásticos. |Nenhum|
|tempdb_data_size<sup>1</sup>|Quilobytes do Tamanho do Arquivo de Dados Tempdb|Contagem|Máximo|Tamanho do arquivo de dados tempdb em kilobytes.|Nenhum|
|tempdb_log_size<sup>1</sup>|Quilobytes do Tamanho do Arquivo de Log Tempdb|Contagem|Máximo|Tamanho do arquivo de log de tempdb em kilobytes. |Nenhum|
|tempdb_log_used_percent<sup>1</sup>|Porcentagem de Uso do Log Tempdb|Porcentagem|Máximo|Log de porcentagem de tempdb usado.|Nenhum|
|allocated_data_storage|Espaço alocado de dados|Bytes|Média|Espaço alocado de dados|Nenhum|
|database_allocated_data_storage|Espaço alocado de dados|Bytes|Média|Espaço alocado de dados|DatabaseResourceId|
|allocated_data_storage_percent|Porcentagem do espaço de dados alocado|Porcentagem|Máximo|Porcentagem do espaço de dados alocado|Nenhum|

<sup>1</sup> essa métrica está disponível para bancos de dados usando o modelo de compra vCore com 2 vCores e superior, ou 200 DTU e superior para o modelo de compra baseado em DTU. 

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|virtual_core_count|Contagem de núcleos virtuais|Contagem|Média|Contagem de núcleos virtuais|Nenhum|
|avg_cpu_percent|Percentual médio de CPU|Porcentagem|Média|Percentual médio de CPU|Nenhum|
|reserved_storage_mb|Espaço de armazenamento reservado|Contagem|Média|Espaço de armazenamento reservado|Nenhum|
|storage_space_used_mb|Espaço de armazenamento usado|Contagem|Média|Espaço de armazenamento usado|Nenhum|
|io_requests|Contagem de solicitações de E/S|Contagem|Média|Contagem de solicitações de E/S|Nenhum|
|io_bytes_read|Bytes de E/S lidos|Bytes|Média|Bytes de E/S lidos|Nenhum|
|io_bytes_written|Bytes de E/S gravados|Bytes|Média|Bytes de E/S gravados|Nenhum|



## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|UsedCapacity|Capacidade utilizada|Bytes|Média|Capacidade utilizada pela conta|Nenhum|
|Transações|Transactions|Contagem|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|ResponseType, GeoType, ApiName, autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|Geotipo, ApiName, autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável.|Geotipo, ApiName, autenticação|
|SuccessServerLatency|Latência de Servidor Com Sucesso|Milissegundos|Média|A latência média usada pelo Armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Esse valor não inclui a latência de rede especificada em AverageE2ELatency.|Geotipo, ApiName, autenticação|
|SuccessE2ELatency|Latência de E2E com Sucesso|Milissegundos|Média|A latência média de ponta a ponta das solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|Geotipo, ApiName, autenticação|
|Disponibilidade|Disponibilidade|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|Geotipo, ApiName, autenticação|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BlobCapacity|Capacidade do Blob|Bytes|Média|A quantidade de armazenamento usada pelo serviço BLOB da conta de armazenamento em bytes.|BlobType, camada|
|BlobCount|Contagem de Blobs|Contagem|Média|O número de blob no serviço BLOB da conta de armazenamento.|BlobType, camada|
|ContainerCount|Contagem de Contêineres de Blobs|Contagem|Média|O número de contêineres no serviço BLOB da conta de armazenamento.|Nenhum|
|IndexCapacity|Capacidade de Índice|Bytes|Média|A quantidade de armazenamento usada pelo índice ADLS Gen2 (hierárquico) em bytes.|Nenhum|
|Transações|Transactions|Contagem|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|ResponseType, GeoType, ApiName, autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|Geotipo, ApiName, autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável.|Geotipo, ApiName, autenticação|
|SuccessServerLatency|Latência de Servidor Com Sucesso|Milissegundos|Média|A latência média usada pelo Armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Esse valor não inclui a latência de rede especificada em AverageE2ELatency.|Geotipo, ApiName, autenticação|
|SuccessE2ELatency|Latência de E2E com Sucesso|Milissegundos|Média|A latência média de ponta a ponta das solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|Geotipo, ApiName, autenticação|
|Disponibilidade|Disponibilidade|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|Geotipo, ApiName, autenticação|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TableCapacity|Capacidade da Tabela|Bytes|Média|A quantidade de armazenamento usada pelo serviço tabela da conta de armazenamento em bytes.|Nenhum|
|TableCount|Contagem de Tabelas|Contagem|Média|O número de tabelas no serviço tabela da conta de armazenamento.|Nenhum|
|TableEntityCount|Contagem de Entidades de Tabela|Contagem|Média|O número de entidades de tabela no serviço tabela da conta de armazenamento.|Nenhum|
|Transações|Transactions|Contagem|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|ResponseType, GeoType, ApiName, autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|Geotipo, ApiName, autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável.|Geotipo, ApiName, autenticação|
|SuccessServerLatency|Latência de Servidor Com Sucesso|Milissegundos|Média|A latência média usada pelo Armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Esse valor não inclui a latência de rede especificada em AverageE2ELatency.|Geotipo, ApiName, autenticação|
|SuccessE2ELatency|Latência de E2E com Sucesso|Milissegundos|Média|A latência média de ponta a ponta das solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|Geotipo, ApiName, autenticação|
|Disponibilidade|Disponibilidade|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|Geotipo, ApiName, autenticação|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|FileCapacity|Capacidade do Arquivo|Bytes|Média|A quantidade de armazenamento usada pelo serviço de arquivo da conta de armazenamento em bytes.|FileShare|
|FileCount|Contagem de Arquivos|Contagem|Média|O número de arquivos no serviço de arquivo da conta de armazenamento.|FileShare|
|FileShareCount|Contagem de Compartilhamentos de Arquivos|Contagem|Média|O número de compartilhamentos de arquivos no serviço de arquivo da conta de armazenamento.|Nenhum|
|FileShareSnapshotCount|Contagem de instantâneos do compartilhamento de arquivo|Contagem|Média|O número de instantâneos presentes no serviço compartilhamento de arquivos da conta de armazenamento.|FileShare|
|FileShareSnapshotSize|Tamanho do instantâneo do compartilhamento de arquivo|Bytes|Média|A quantidade de armazenamento usada pelos instantâneos no serviço de arquivo da conta de armazenamento, em bytes.|FileShare|
|FileShareQuota|Tamanho da cota do compartilhamento de arquivo|Bytes|Média|O limite superior da quantidade de armazenamento que pode ser usada pelo serviço de arquivos do Azure em bytes.|FileShare|
|Transações|Transactions|Contagem|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|ResponseType, GeoType, ApiName, autenticação, FileShare|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|Geotipo, ApiName, autenticação, FileShare|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável.|Geotipo, ApiName, autenticação, FileShare|
|SuccessServerLatency|Latência de Servidor Com Sucesso|Milissegundos|Média|A latência média usada pelo Armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Esse valor não inclui a latência de rede especificada em AverageE2ELatency.|Geotipo, ApiName, autenticação, FileShare|
|SuccessE2ELatency|Latência de E2E com Sucesso|Milissegundos|Média|A latência média de ponta a ponta das solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|Geotipo, ApiName, autenticação, FileShare|
|Disponibilidade|Disponibilidade|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|Geotipo, ApiName, autenticação, FileShare|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|QueueCapacity|Capacidade da Fila|Bytes|Média|A quantidade de armazenamento usada pela serviço Fila da conta de armazenamento em bytes.|Nenhum|
|QueueCount|Contagem de Filas|Contagem|Média|O número de filas na serviço Fila da conta de armazenamento.|Nenhum|
|QueueMessageCount|Contagem de Mensagens da Fila|Contagem|Média|O número aproximado de mensagens de fila na serviço Fila da conta de armazenamento.|Nenhum|
|Transações|Transactions|Contagem|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|ResponseType, GeoType, ApiName, autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|Geotipo, ApiName, autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável.|Geotipo, ApiName, autenticação|
|SuccessServerLatency|Latência de Servidor Com Sucesso|Milissegundos|Média|A latência média usada pelo Armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Esse valor não inclui a latência de rede especificada em AverageE2ELatency.|Geotipo, ApiName, autenticação|
|SuccessE2ELatency|Latência de E2E com Sucesso|Milissegundos|Média|A latência média de ponta a ponta das solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|Geotipo, ApiName, autenticação|
|Disponibilidade|Disponibilidade|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|Geotipo, ApiName, autenticação|





## <a name="microsoftstoragecachecaches"></a>Microsoft.StorageCache/caches

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ClientIOPS|Total de IOPS do Cliente|Contagem|Média|A taxa de operações de arquivos do cliente processadas pelo cache.|Nenhum|
|ClientLatency|Latência Média do Cliente|Milissegundos|Média|Latência média de operações de arquivo do cliente para o cache de armazenamento.|Nenhum|
|ClientReadIOPS|IOPS de Leitura do Cliente|CountPerSecond|Média|Operações de leitura de cliente por segundo.|Nenhum|
|ClientReadThroughput|Taxa de Transferência Média de Leitura do Cache|BytesPerSecond|Média|Taxa de transferência de dados de leitura de cliente.|Nenhum|
|ClientWriteIOPS|IOPS de Gravação do Cliente|CountPerSecond|Média|Operações de gravação de cliente por segundo.|Nenhum|
|ClientWriteThroughput|Taxa de Transferência Média de Gravação no Cache|BytesPerSecond|Média|Taxa de transferência de dados de gravação de cliente.|Nenhum|
|ClientMetadataReadIOPS|IOPS de Leitura dos Metadados do Cliente|CountPerSecond|Média|A taxa de operações de arquivo do cliente enviadas ao cache, excluindo leituras de dados, que não modificam o estado persistente.|Nenhum|
|ClientMetadataWriteIOPS|IOPS de Gravação dos Metadados do Cliente|CountPerSecond|Média|A taxa de operações de arquivo do cliente enviadas ao cache, excluindo gravações de dados, que modificam o estado persistente.|Nenhum|
|ClientLockIOPS|IOPS de Bloqueio do Cliente|CountPerSecond|Média|Operações de bloqueio de arquivo do cliente por segundo.|Nenhum|
|StorageTargetHealth|Integridade do Destino de Armazenamento|Contagem|Média|Resultados boolianos de teste de conectividade entre o cache e os destinos de armazenamento.|Nenhum|
|Tempo de atividade|Tempo de atividade|Contagem|Média|Resultados boolianos de teste de conectividade entre o cache e o sistema de monitoramento.|Nenhum|
|StorageTargetIOPS|Total de IOPS do StorageTarget|Contagem|Média|A taxa de todas as operações de arquivo que o cache envia para um determinado StorageTarget.|StorageTarget|
|StorageTargetWriteIOPS|IOPS de Gravação do StorageTarget|Contagem|Média|A taxa das operações de gravação de arquivo que o cache envia para um determinado StorageTarget.|StorageTarget|
|StorageTargetAsyncWriteThroughput|Taxa de Transferência de Gravação Assíncrona do StorageTarget|BytesPerSecond|Média|A taxa em que o cache grava dados de forma assíncrona em um determinado StorageTarget. Essas são gravações oportunistas que não fazem com que os clientes bloqueiem.|StorageTarget|
|StorageTargetSyncWriteThroughput|Taxa de Transferência de Gravação Síncrona do StorageTarget|BytesPerSecond|Média|A taxa em que o cache grava dados de forma síncrona em um determinado StorageTarget. Essas são gravações que fazem com que os clientes bloqueiem.|StorageTarget|
|StorageTargetTotalWriteThroughput|Taxa de Transferência de Gravação Total do StorageTarget|BytesPerSecond|Média|A taxa total em que o cache grava dados em um determinado StorageTarget.|StorageTarget|
|StorageTargetLatency|Latência do StorageTarget|Milissegundos|Média|A latência média de ida e volta de todas as operações de arquivo que o cache envia para um partricular StorageTarget.|StorageTarget|
|StorageTargetMetadataReadIOPS|IOPS de Leitura de Metadados do StorageTarget|CountPerSecond|Média|A taxa de operações de arquivo que não modificam o estado persistente e excluindo a operação de leitura, que o cache envia para um determinado StorageTarget.|StorageTarget|
|StorageTargetMetadataWriteIOPS|IOPS de Gravação de Metadados do StorageTarget|CountPerSecond|Média|A taxa de operações de arquivo que modificam o estado persistente e excluindo a operação de gravação, que o cache envia para um determinado StorageTarget.|StorageTarget|
|StorageTargetReadIOPS|IOPS de Leitura do StorageTarget|CountPerSecond|Média|A taxa de operações de leitura de arquivo que o cache envia para um determinado StorageTarget.|StorageTarget|
|StorageTargetReadAheadThroughput|Taxa de Transferência de Leitura Antecipada do StorageTarget|BytesPerSecond|Média|A taxa em que o cache lê de forma oportunista os dados do StorageTarget.|StorageTarget|
|StorageTargetFillThroughput|Taxa de Transferência de Preenchimento do StorageTarget|BytesPerSecond|Média|A taxa em que o cache lê os dados do StorageTarget para lidar com um erro de cache.|StorageTarget|
|StorageTargetTotalReadThroughput|Taxa de Transferência de Leitura Total do StorageTarget|BytesPerSecond|Média|A taxa total em que o cache lê dados de um determinado StorageTarget.|StorageTarget|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Resultado da Sessão de Sincronização|Contagem|Média|Métrica que registra um valor de 1 cada vez que o ponto de extremidade do servidor conclui com êxito uma sessão de sincronização com o ponto de extremidade de nuvem|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Arquivos Sincronizados|Contagem|Total|Contagem de arquivos sincronizados|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Arquivos não sincronizando|Contagem|Total|Falha na sincronização da contagem de arquivos|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncBatchTransferredFileBytes|Bytes sincronizados|Bytes|Total|Tamanho total do arquivo transferido para sessões de sincronização|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncServerHeartbeat|Status Online do Servidor|Contagem|Máximo|Métrica que registra um valor de 1 cada vez que o servidor resigtered registra com êxito uma pulsação com o ponto de extremidade de nuvem|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Recall da camada de nuvem|Bytes|Total|Tamanho total dos dados recuperados pelo servidor|ServerName|
|StorageSyncRecalledTotalNetworkBytes|Tamanho de recall da camada de nuvem|Bytes|Total|Tamanho dos dados em recall|SyncGroupName, servername|
|StorageSyncRecallThroughputBytesPerSecond|Taxa de transferência de recall da camada de nuvem|BytesPerSecond|Média|Tamanho da taxa de transferência de recall de dados|SyncGroupName, servername|
|StorageSyncRecalledNetworkBytesByApplication|Tamanho de recall da camada de nuvem por aplicativo|Bytes|Total|Tamanho dos dados recuperados pelo aplicativo|SyncGroupName, servername, ApplicationName|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>microsoft.storagesync/storageSyncServices/syncGroups

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|SyncGroupSyncSessionAppliedFilesCount|Arquivos Sincronizados|Contagem|Total|Contagem de arquivos sincronizados|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|Arquivos não sincronizando|Contagem|Total|Falha na sincronização da contagem de arquivos|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncGroupBatchTransferredFileBytes|Bytes sincronizados|Bytes|Total|Tamanho total do arquivo transferido para sessões de sincronização|SyncGroupName,ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ServerEndpointSyncSessionAppliedFilesCount|Arquivos Sincronizados|Contagem|Total|Contagem de arquivos sincronizados|ServerEndpointName,SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|Arquivos não sincronizando|Contagem|Total|Falha na sincronização da contagem de arquivos|ServerEndpointName,SyncDirection|
|ServerEndpointBatchTransferredFileBytes|Bytes sincronizados|Bytes|Total|Tamanho total do arquivo transferido para sessões de sincronização|ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>microsoft.storagesync/storageSyncServices/registeredServers

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ServerHeartbeat|Status Online do Servidor|Contagem|Máximo|Métrica que registra um valor de 1 cada vez que o servidor resigtered registra com êxito uma pulsação com o ponto de extremidade de nuvem|ServerResourceId, servername|
|ServerRecallIOTotalSizeBytes|Recall da camada de nuvem|Bytes|Total|Tamanho total dos dados recuperados pelo servidor|ServerResourceId, servername|



## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ResourceUtilization|% de utilização do SU|Porcentagem|Máximo|% de utilização do SU|LogicalName, PartitionID|
|InputEvents|Eventos de entrada|Contagem|Total|Eventos de entrada|LogicalName, PartitionID|
|InputEventBytes|Bytes de evento de entrada|Bytes|Total|Bytes de evento de entrada|LogicalName, PartitionID|
|LateInputEvents|Eventos de entrada atrasados|Contagem|Total|Eventos de entrada atrasados|LogicalName, PartitionID|
|OutputEvents|Eventos de saída|Contagem|Total|Eventos de saída|LogicalName, PartitionID|
|ConversionErrors|Erros de conversão de dados|Contagem|Total|Erros de conversão de dados|LogicalName, PartitionID|
|Errors|Erros de runtime|Contagem|Total|Erros de runtime|LogicalName, PartitionID|
|DroppedOrAdjustedEvents|Eventos fora de ordem|Contagem|Total|Eventos fora de ordem|LogicalName, PartitionID|
|AMLCalloutRequests|Solicitações de função|Contagem|Total|Solicitações de função|LogicalName, PartitionID|
|AMLCalloutFailedRequests|Solicitações de função com falha|Contagem|Total|Solicitações de função com falha|LogicalName, PartitionID|
|AMLCalloutInputEvents|Eventos de função|Contagem|Total|Eventos de função|LogicalName, PartitionID|
|DeserializationError|Erros de desserialização de entrada|Contagem|Total|Erros de desserialização de entrada|LogicalName, PartitionID|
|EarlyInputEvents|Eventos de Entrada Antecipados|Contagem|Total|Eventos de Entrada Antecipados|LogicalName, PartitionID|
|OutputWatermarkDelaySeconds|Atraso de Marca-d'água|Segundos|Máximo|Atraso de Marca-d'água|LogicalName, PartitionID|
|InputEventsSourcesBacklogged|Eventos de Entrada Acumulados|Contagem|Máximo|Eventos de Entrada Acumulados|LogicalName, PartitionID|
|InputEventsSourcesPerSecond|Fontes de Entrada Recebidas|Contagem|Total|Fontes de Entrada Recebidas|LogicalName, PartitionID|

## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse/workspaces

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|OrchestrationPipelineRunsEnded|Execuções de pipeline finalizadas|Contagem|Total|Contagem de execuções do pipeline de orquestração que tiveram êxito, falharam ou foram canceladas|Resultado, FailureType, pipeline|
|OrchestrationActivityRunsEnded|Execuções de atividades encerradas|Contagem|Total|Contagem de atividades de orquestração que tiveram êxito, falharam ou foram canceladas|Resultado, FailureType, atividade, ActivityType, pipeline|
|OrchestrationTriggersEnded|Gatilhos encerrados|Contagem|Total|Contagem de gatilhos de orquestração que foram bem-sucedidos, com falha ou foram cancelados|Resultado, FailureType, gatilho|
|SQLOnDemandLoginAttempts|Tentativas de logon|Contagem|Total|Contagem de tentativas de logon que êxito na ou com falha|Resultado|
|SQLOnDemandQueriesEnded|Consultas encerradas|Contagem|Total|Contagem de consultas que tiveram êxito, falharam ou foram canceladas|Resultado|
|SQLOnDemandQueryProcessedBytes|Dados processados|Bytes|Total|Quantidade de dados processados por consultas|Nenhum|

## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft. Synapse/Workspaces/bigDataPools

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|SparkJobsEnded|Aplicativos finalizados|Contagem|Total|Contagem de aplicativos finalizados|JobType, JobResult|
|CoresCapacity|Capacidade de núcleos|Contagem|Máximo|Capacidade de núcleos|Nenhum|
|MemoryCapacityGB|Capacidade de memória (GB)|Contagem|Máximo|Capacidade de memória (GB)|Nenhum|

## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft. Synapse/espaços de trabalho/sqlpools

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|DWULimit|Limite de DWU|Contagem|Máximo|Objetivo de nível de serviço do pool do SQL|Nenhum|
|DWUUsed|DWU usado|Contagem|Máximo|Representa uma representação de uso de alto nível no pool do SQL. Medido por limite de DWU * DWU percentual|Nenhum|
|DWUUsedPercent|Percentual de DWU usado|Porcentagem|Máximo|Representa uma representação de uso de alto nível no pool do SQL. Medido com o máximo entre o percentual de CPU e a porcentagem de e/s de dados|Nenhum|
|ConnectionsBlockedByFirewall|Conexões bloqueadas pelo firewall|Contagem|Total|Contagem de conexões bloqueadas por regras de firewall. Reveja as políticas de controle de acesso para seu pool SQL e monitore essas conexões se a contagem for alta|Nenhum|
|AdaptiveCacheHitPercent|Percentual de impacto de cache adaptável|Porcentagem|Máximo|Mede o quão bem as cargas de trabalho estão utilizando o cache adaptável. Use essa métrica com a métrica percentual de impacto de cache para determinar se deve ser dimensionada para capacidade adicional ou executar novamente as cargas de trabalho para hidratar o cache|Nenhum|
|AdaptiveCacheUsedPercent|Percentual de cache adaptável usado|Porcentagem|Máximo|Mede o quão bem as cargas de trabalho estão utilizando o cache adaptável. Use essa métrica com a métrica percentual usada do cache para determinar se deve ser dimensionada para capacidade adicional ou executar novamente as cargas de trabalho para hidratar o cache|Nenhum|
|LocalTempDBUsedPercent|Percentual de uso do tempdb local|Porcentagem|Máximo|Utilização de tempdb local em todos os nós de computação-os valores são emitidos a cada cinco minutos|Nenhum|
|MemoryUsedPercent|Porcentagem de utilização da memória|Porcentagem|Máximo|Utilização de memória em todos os nós no pool de SQL|Nenhum|
|conexões|conexões|Contagem|Total|Contagem de logons totais para o pool do SQL|Resultado|
|WLGActiveQueries|Consultas ativas do grupo de carga de trabalho|Contagem|Total|As consultas ativas no grupo de cargas de trabalho. Usar essa métrica não filtrada e a divisão exibe todas as consultas ativas em execução no sistema|IsUserDefined, de carga de trabalho|
|WLGActiveQueriesTimeouts|Tempos limite de consulta do grupo de carga de trabalho|Contagem|Total|Consultas para o grupo de cargas de trabalho que atingiram o tempo limite. Os tempos limite de consulta relatados por essa métrica são apenas uma vez que a consulta começou a ser executada (ela não inclui o tempo de espera devido a esperas de bloqueio ou de recursos)|IsUserDefined, de carga de trabalho|
|WLGAllocationBySystemPercent|Percentual de alocação do grupo de carga de trabalho por sistema|Porcentagem|Máximo|A alocação percentual de recursos em relação a todo o sistema|IsUserDefined, de carga de trabalho|
|WLGAllocationByMaxResourcePercent|Alocação do grupo de carga de trabalho por porcentagem máxima de recursos|Porcentagem|Máximo|Exibe a porcentagem de alocação de recursos em relação à porcentagem de recursos de limite efetivo por grupo de carga de trabalho. Essa métrica fornece a utilização efetiva do grupo de cargas de trabalho|IsUserDefined, de carga de trabalho|
|WLGEffectiveCapResourcePercent|Percentual de limite efetivo de recursos|Porcentagem|Máximo|A porcentagem do recurso Cap efetivo para o grupo de carga de trabalho. Se houver outros grupos de cargas de trabalho com min_percentage_resource > 0, o effective_cap_percentage_resource será reduzido proporcionalmente|IsUserDefined, de carga de trabalho|
|wlg_effective_min_resource_percent|Percentual mínimo efetivo de recursos|Porcentagem|Mínimo|A configuração de porcentagem mínima de recursos efetiva permitida Considerando o nível de serviço e as configurações do grupo de carga de trabalho. Os min_percentage_resource efetivos podem ser ajustados em níveis mais baixos de serviço|IsUserDefined, de carga de trabalho|
|WLGQueuedQueries|Consultas na fila do grupo de carga de trabalho|Contagem|Total|Contagem cumulativa de solicitações enfileiradas após o limite de simultaneidade máxima ter sido atingido|IsUserDefined, de carga de trabalho|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|IngressReceivedMessages|Mensagens de entrada recebidas|Contagem|Total|Contagem de mensagens lidas de todos os Hubs de Eventos ou origens de evento do Hub IoT|Nenhum|
|IngressReceivedInvalidMessages|Mensagens de entrada inválidas recebidas|Contagem|Total|Contagem de mensagens inválidas lidas de todos os Hubs de Eventos ou origens de evento do Hub IoT|Nenhum|
|IngressReceivedBytes|Bytes de entrada recebidos|Bytes|Total|Contagem de bytes lidos de todas as fontes de evento|Nenhum|
|IngressStoredBytes|Bytes de entrada armazenados|Bytes|Total|Tamanho total de eventos processados com êxito e disponíveis para consulta|Nenhum|
|IngressStoredEvents|Eventos de entrada armazenados|Contagem|Total|Contagem de eventos nivelados processados com êxito e disponíveis para consulta|Nenhum|
|IngressReceivedMessagesTimeLag|Tempo de atraso das mensagens de entrada recebidas|Segundos|Máximo|Diferença entre a hora em que a mensagem é enfileirada na origem do evento e a hora em que ela é processada na entrada|Nenhum|
|IngressReceivedMessagesCountLag|Atraso na contagem das mensagens de entrada recebidas|Contagem|Média|Diferença entre o número de sequência da última mensagem enfileirada na partição de origem do evento e o número de sequência de mensagens sendo processadas na entrada|Nenhum|
|WarmStorageMaxProperties|Propriedades máximas de armazenamento quente|Contagem|Máximo|Número máximo de propriedades usadas permitidas pelo ambiente para SKU S1/S2 e número máximo de propriedades permitidas pelo armazenamento quente para o SKU PAYG|Nenhum|
|WarmStorageUsedProperties|Propriedades usadas de armazenamento quente |Contagem|Máximo|Número de propriedades usadas pelo ambiente para SKU S1/S2 e número de propriedades usadas pelo armazenamento quente para SKU PAYG|Nenhum|



## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|IngressReceivedMessages|Mensagens de entrada recebidas|Contagem|Total|Contagem de mensagens lidas na origem do evento|Nenhum|
|IngressReceivedInvalidMessages|Mensagens de entrada inválidas recebidas|Contagem|Total|Contagem de mensagens inválidas lidas na origem do evento|Nenhum|
|IngressReceivedBytes|Bytes de entrada recebidos|Bytes|Total|Contagem de bytes lidos da fonte de evento|Nenhum|
|IngressStoredBytes|Bytes de entrada armazenados|Bytes|Total|Tamanho total de eventos processados com êxito e disponíveis para consulta|Nenhum|
|IngressStoredEvents|Eventos de entrada armazenados|Contagem|Total|Contagem de eventos nivelados processados com êxito e disponíveis para consulta|Nenhum|
|IngressReceivedMessagesTimeLag|Tempo de atraso das mensagens de entrada recebidas|Segundos|Máximo|Diferença entre a hora em que a mensagem é enfileirada na origem do evento e a hora em que ela é processada na entrada|Nenhum|
|IngressReceivedMessagesCountLag|Atraso na contagem das mensagens de entrada recebidas|Contagem|Média|Diferença entre o número de sequência da última mensagem enfileirada na partição de origem do evento e o número de sequência de mensagens sendo processadas na entrada|Nenhum|
|WarmStorageMaxProperties|Propriedades máximas de armazenamento quente|Contagem|Máximo|Número máximo de propriedades usadas permitidas pelo ambiente para SKU S1/S2 e número máximo de propriedades permitidas pelo armazenamento quente para o SKU PAYG|Nenhum|
|WarmStorageUsedProperties|Propriedades usadas de armazenamento quente |Contagem|Máximo|Número de propriedades usadas pelo ambiente para SKU S1/S2 e número de propriedades usadas pelo armazenamento quente para SKU PAYG|Nenhum|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Disk Read Bytes/Sec|BytesPerSecond|Média|Taxa de transferência média do disco devido a operações de leitura no período de exemplo.|Nenhum|
|DiskWriteBytesPerSecond|Disk Write Bytes/Sec|BytesPerSecond|Média|Taxa de transferência média do disco devido a operações de gravação no período de exemplo.|Nenhum|
|Bytes de leitura de disco|Bytes de leitura de disco|Bytes|Total|Taxa de transferência total do disco devido a operações de leitura no período de exemplo.|Nenhum|
|Bytes de gravação de disco|Bytes de gravação de disco|Bytes|Total|Taxa de transferência total do disco devido a operações de gravação no período de exemplo.|Nenhum|
|DiskReadOperations|Operações de Leitura de Disco|Contagem|Total|O número de operações de leitura de e/s no período de exemplo anterior. Observe que essas operações podem ser de tamanho variável.|Nenhum|
|DiskWriteOperations|Operações de Gravação em Disco|Contagem|Total|O número de operações de gravação de e/s no período de exemplo anterior. Observe que essas operações podem ser de tamanho variável.|Nenhum|
|Operações de leitura de disco/Seg|Operações de leitura de disco/Seg|CountPerSecond|Média|O número médio de operações de leitura de e/s no período de exemplo anterior. Observe que essas operações podem ser de tamanho variável.|Nenhum|
|Operações de gravação de disco/Seg|Operações de gravação de disco/Seg|CountPerSecond|Média|O número médio de operações de gravação de e/s no período de exemplo anterior. Observe que essas operações podem ser de tamanho variável.|Nenhum|
|DiskReadLatency|Latência de leitura de disco|Milissegundos|Média|Latência de leitura total. A soma das latências de leitura do dispositivo e do kernel.|Nenhum|
|DiskWriteLatency|Latência de Gravação de disco|Milissegundos|Média|Latência de gravação total. A soma das latências de gravação do kernel e do dispositivo.|Nenhum|
|NetworkInBytesPerSecond|Bytes/s de Entrada de Rede|BytesPerSecond|Média|Taxa de transferência média de rede para o tráfego recebido.|Nenhum|
|NetworkOutBytesPerSecond|Bytes/s de Saída de Rede|BytesPerSecond|Média|Taxa de transferência média de rede para o tráfego transmitido.|Nenhum|
|Entrada na rede|Entrada na rede|Bytes|Total|Taxa de transferência total de rede para o tráfego recebido.|Nenhum|
|Saída da rede|Saída da rede|Bytes|Total|Taxa de transferência total de rede para o tráfego transmitido.|Nenhum|
|MemoryUsed|Memória Usada|Bytes|Média|A quantidade de memória da máquina que está sendo usada pela VM.|Nenhum|
|MemoryGranted|Memória Concedida|Bytes|Média|A quantidade de memória que foi concedida à VM pelo host. A memória não é concedida ao host até que ele seja tocado uma vez e a memória concedida possa ser trocada ou bloqueada se o VMkernel precisar da memória.|Nenhum|
|MemoryActive|Memória Ativa|Bytes|Média|A quantidade de memória usada pela VM na última pequena janela de tempo. Esse é o número "verdadeiro" da quantidade de memória necessária para a VM no momento. A memória adicional não utilizada pode ser trocada ou por balão sem impacto no desempenho do convidado.|Nenhum|
|Percentual de CPU|Porcentagem de CPU|Porcentagem|Média|A utilização da CPU. Esse valor é relatado com 100% que representa todos os núcleos de processador no sistema. Por exemplo, uma VM de 2 vias usando 50% de um sistema de quatro núcleos está usando completamente dois núcleos.|Nenhum|
|PercentageCpuReady|Percentual de CPU Pronta|Milissegundos|Total|O tempo de preparação é o tempo gasto aguardando que as CPUs fiquem disponíveis no intervalo de atualização anterior.|Nenhum|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|CpuPercentage|Percentual de CPU|Porcentagem|Média|Percentual de CPU|Instância|
|MemoryPercentage|Porcentagem de Memória|Porcentagem|Média|Porcentagem de Memória|Instância|
|DiskQueueLength|Tamanho da fila do disco|Contagem|Média|Tamanho da fila do disco|Instância|
|HttpQueueLength|Tamanho da Fila de Http|Contagem|Média|Tamanho da Fila de Http|Instância|
|BytesReceived|Entrada de Dados|Bytes|Total|Entrada de Dados|Instância|
|BytesSent|Saída de dados|Bytes|Total|Saída de dados|Instância|
|TcpSynSent|Sincronização de TCP Enviada|Contagem|Média|Sincronização de TCP Enviada|Instância|
|TcpSynReceived|Sincronização de TCP Recebida|Contagem|Média|Sincronização de TCP Recebida|Instância|
|TcpEstablished|TCP Estabelecido|Contagem|Média|TCP Estabelecido|Instância|
|TcpFinWait1|Espera 1 de Fin de TCP|Contagem|Média|Espera 1 de Fin de TCP|Instância|
|TcpFinWait2|Espera 2 de Fin de TCP|Contagem|Média|Espera 2 de Fin de TCP|Instância|
|TcpClosing|Fechamento de TCP|Contagem|Média|Fechamento de TCP|Instância|
|TcpCloseWait|Espera de Fechamento de TCP|Contagem|Média|Espera de Fechamento de TCP|Instância|
|TcpLastAck|Última Confirmação de TCP|Contagem|Média|Última Confirmação de TCP|Instância|
|TcpTimeWait|Espera de Tempo de TCP|Contagem|Média|Espera de Tempo de TCP|Instância|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (excluindo funções) 

> [!NOTE]
> O **uso do sistema de arquivos** é uma nova métrica sendo distribuída globalmente, nenhum dado é esperado, a menos que você tenha sido na lista de permissões para visualização privada.

> [!IMPORTANT]
> O **tempo médio de resposta** será preterido para evitar confusão com agregações de métricas. Use o **tempo de resposta** como uma substituição.

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|CpuTime|Tempo de CPU|Segundos|Total|Tempo de CPU|Instância|
|Requests|Requests|Contagem|Total|Requests|Instância|
|BytesReceived|Entrada de Dados|Bytes|Total|Entrada de Dados|Instância|
|BytesSent|Saída de dados|Bytes|Total|Saída de dados|Instância|
|Http101|Http 101|Contagem|Total|Http 101|Instância|
|Http2xx|Http 2xx|Contagem|Total|Http 2xx|Instância|
|Http3xx|Http 3xx|Contagem|Total|Http 3xx|Instância|
|Http401|Http 401|Contagem|Total|Http 401|Instância|
|Http403|Http 403|Contagem|Total|Http 403|Instância|
|Http404|Http 404|Contagem|Total|Http 404|Instância|
|Http406|Http 406|Contagem|Total|Http 406|Instância|
|Http4xx|Http 4xx|Contagem|Total|Http 4xx|Instância|
|Http5xx|Erros do Servidor Http|Contagem|Total|Erros do Servidor Http|Instância|
|MemoryWorkingSet|Conjunto de trabalho de memória|Bytes|Média|Conjunto de trabalho de memória|Instância|
|AverageMemoryWorkingSet|Conjunto de trabalho de memória média|Bytes|Média|Conjunto de trabalho de memória média|Instância|
|HttpResponseTime|Tempo de Resposta|Segundos|Total|Tempo de Resposta|Instância|
|AverageResponseTime|Tempo médio de resposta (preterido)|Segundos|Média|Tempo Médio de Resposta|Instância|
|AppConnections|conexões|Contagem|Média|conexões|Instância|
|Alças|Núm. de Identificadores|Contagem|Média|Núm. de Identificadores|Instância|
|Threads|Contagem de Threads|Contagem|Média|Contagem de Threads|Instância|
|PrivateBytes|Bytes Particulares|Bytes|Média|Bytes Particulares|Instância|
|IoReadBytesPerSecond|E/S de bytes de leitura por segundo|BytesPerSecond|Total|E/S de bytes de leitura por segundo|Instância|
|IoWriteBytesPerSecond|E/S de bytes de gravação por segundo|BytesPerSecond|Total|E/S de bytes de gravação por segundo|Instância|
|IoOtherBytesPerSecond|E/S de outros bytes por segundo|BytesPerSecond|Total|E/S de outros bytes por segundo|Instância|
|IoReadOperationsPerSecond|E/S de operações de leitura por segundo|BytesPerSecond|Total|E/S de operações de leitura por segundo|Instância|
|IoWriteOperationsPerSecond|E/S de operações de gravação por segundo|BytesPerSecond|Total|E/S de operações de gravação por segundo|Instância|
|IoOtherOperationsPerSecond|E/S de outras operações por segundo|BytesPerSecond|Total|E/S de outras operações por segundo|Instância|
|RequestsInApplicationQueue|Solicitações na fila do aplicativo|Contagem|Média|Solicitações na fila do aplicativo|Instância|
|CurrentAssemblies|Assemblies Atuais|Contagem|Média|Assemblies Atuais|Instância|
|TotalAppDomains|Total de domínios de aplicativo|Contagem|Média|Total de domínios de aplicativo|Instância|
|TotalAppDomainsUnloaded|Total de domínios de aplicativo descarregados|Contagem|Média|Total de domínios de aplicativo descarregados|Instância|
|Gen0Collections|Coletas de lixo da Ger 0|Contagem|Total|Coletas de lixo da Ger 0|Instância|
|Gen1Collections|Coletas de lixo da Ger 1|Contagem|Total|Coletas de lixo da Ger 1|Instância|
|Gen2Collections|Coletas de lixo da Ger 2|Contagem|Total|Coletas de lixo da Ger 2|Instância|
|HealthCheckStatus|Status da verificação de integridade|Contagem|Média|Status da verificação de integridade|Instância|
|FileSystemUsage|Uso do sistema de arquivos|Bytes|Média|Uso do sistema de arquivos|Nenhum|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (funções)

> [!NOTE]
> O **uso do sistema de arquivos** é uma nova métrica sendo distribuída globalmente, nenhum dado é esperado, a menos que você tenha sido na lista de permissões para visualização privada.

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BytesReceived|Entrada de Dados|Bytes|Total|Entrada de Dados|Instância|
|BytesSent|Saída de dados|Bytes|Total|Saída de dados|Instância|
|Http5xx|Erros do Servidor Http|Contagem|Total|Erros do Servidor Http|Instância|
|MemoryWorkingSet|Conjunto de trabalho de memória|Bytes|Média|Conjunto de trabalho de memória|Instância|
|AverageMemoryWorkingSet|Conjunto de trabalho de memória média|Bytes|Média|Conjunto de trabalho de memória média|Instância|
|FunctionExecutionUnits|Unidades de Execução de Função|MB/milissegundos|Total|[Unidades de Execução de Função](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Instância|
|FunctionExecutionCount|Contagem de Execução de Função|Contagem|Total|Contagem de Execução de Função|Instância|
|PrivateBytes|Bytes Particulares|Bytes|Média|Bytes Particulares|Instância|
|IoReadBytesPerSecond|E/S de bytes de leitura por segundo|BytesPerSecond|Total|E/S de bytes de leitura por segundo|Instância|
|IoWriteBytesPerSecond|E/S de bytes de gravação por segundo|BytesPerSecond|Total|E/S de bytes de gravação por segundo|Instância|
|IoOtherBytesPerSecond|E/S de outros bytes por segundo|BytesPerSecond|Total|E/S de outros bytes por segundo|Instância|
|IoReadOperationsPerSecond|E/S de operações de leitura por segundo|BytesPerSecond|Total|E/S de operações de leitura por segundo|Instância|
|IoWriteOperationsPerSecond|E/S de operações de gravação por segundo|BytesPerSecond|Total|E/S de operações de gravação por segundo|Instância|
|IoOtherOperationsPerSecond|E/S de outras operações por segundo|BytesPerSecond|Total|E/S de outras operações por segundo|Instância|
|RequestsInApplicationQueue|Solicitações na fila do aplicativo|Contagem|Média|Solicitações na fila do aplicativo|Instância|
|CurrentAssemblies|Assemblies Atuais|Contagem|Média|Assemblies Atuais|Instância|
|TotalAppDomains|Total de domínios de aplicativo|Contagem|Média|Total de domínios de aplicativo|Instância|
|TotalAppDomainsUnloaded|Total de domínios de aplicativo descarregados|Contagem|Média|Total de domínios de aplicativo descarregados|Instância|
|Gen0Collections|Coletas de lixo da Ger 0|Contagem|Total|Coletas de lixo da Ger 0|Instância|
|Gen1Collections|Coletas de lixo da Ger 1|Contagem|Total|Coletas de lixo da Ger 1|Instância|
|Gen2Collections|Coletas de lixo da Ger 2|Contagem|Total|Coletas de lixo da Ger 2|Instância|
|HealthCheckStatus|Status da verificação de integridade|Contagem|Média|Status da verificação de integridade|Instância|
|FileSystemUsage|Uso do sistema de arquivos|Bytes|Média|Uso do sistema de arquivos|Nenhum|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|CpuTime|Tempo de CPU|Segundos|Total|Tempo de CPU|Instância|
|Requests|Requests|Contagem|Total|Requests|Instância|
|BytesReceived|Entrada de Dados|Bytes|Total|Entrada de Dados|Instância|
|BytesSent|Saída de dados|Bytes|Total|Saída de dados|Instância|
|Http101|Http 101|Contagem|Total|Http 101|Instância|
|Http2xx|Http 2xx|Contagem|Total|Http 2xx|Instância|
|Http3xx|Http 3xx|Contagem|Total|Http 3xx|Instância|
|Http401|Http 401|Contagem|Total|Http 401|Instância|
|Http403|Http 403|Contagem|Total|Http 403|Instância|
|Http404|Http 404|Contagem|Total|Http 404|Instância|
|Http406|Http 406|Contagem|Total|Http 406|Instância|
|Http4xx|Http 4xx|Contagem|Total|Http 4xx|Instância|
|Http5xx|Erros do Servidor Http|Contagem|Total|Erros do Servidor Http|Instância|
|MemoryWorkingSet|Conjunto de trabalho de memória|Bytes|Média|Conjunto de trabalho de memória|Instância|
|AverageMemoryWorkingSet|Conjunto de trabalho de memória média|Bytes|Média|Conjunto de trabalho de memória média|Instância|
|AverageResponseTime|Tempo Médio de Resposta|Segundos|Média|Tempo Médio de Resposta|Instância|
|HttpResponseTime|Tempo de Resposta|Segundos|Média|Tempo de Resposta|Instância|
|FunctionExecutionUnits|Unidades de Execução de Função|Contagem|Total|Unidades de Execução de Função|Instância|
|FunctionExecutionCount|Contagem de Execução de Função|Contagem|Total|Contagem de Execução de Função|Instância|
|AppConnections|conexões|Contagem|Média|conexões|Instância|
|Alças|Núm. de Identificadores|Contagem|Média|Núm. de Identificadores|Instância|
|Threads|Contagem de Threads|Contagem|Média|Contagem de Threads|Instância|
|PrivateBytes|Bytes Particulares|Bytes|Média|Bytes Particulares|Instância|
|IoReadBytesPerSecond|E/S de bytes de leitura por segundo|BytesPerSecond|Total|E/S de bytes de leitura por segundo|Instância|
|IoWriteBytesPerSecond|E/S de bytes de gravação por segundo|BytesPerSecond|Total|E/S de bytes de gravação por segundo|Instância|
|IoOtherBytesPerSecond|E/S de outros bytes por segundo|BytesPerSecond|Total|E/S de outros bytes por segundo|Instância|
|IoReadOperationsPerSecond|E/S de operações de leitura por segundo|BytesPerSecond|Total|E/S de operações de leitura por segundo|Instância|
|IoWriteOperationsPerSecond|E/S de operações de gravação por segundo|BytesPerSecond|Total|E/S de operações de gravação por segundo|Instância|
|IoOtherOperationsPerSecond|E/S de outras operações por segundo|BytesPerSecond|Total|E/S de outras operações por segundo|Instância|
|RequestsInApplicationQueue|Solicitações na fila do aplicativo|Contagem|Média|Solicitações na fila do aplicativo|Instância|
|CurrentAssemblies|Assemblies Atuais|Contagem|Média|Assemblies Atuais|Instância|
|TotalAppDomains|Total de domínios de aplicativo|Contagem|Média|Total de domínios de aplicativo|Instância|
|TotalAppDomainsUnloaded|Total de domínios de aplicativo descarregados|Contagem|Média|Total de domínios de aplicativo descarregados|Instância|
|Gen0Collections|Coletas de lixo da Ger 0|Contagem|Total|Coletas de lixo da Ger 0|Instância|
|Gen1Collections|Coletas de lixo da Ger 1|Contagem|Total|Coletas de lixo da Ger 1|Instância|
|Gen2Collections|Coletas de lixo da Ger 2|Contagem|Total|Coletas de lixo da Ger 2|Instância|
|HealthCheckStatus|Status da verificação de integridade|Contagem|Média|Status da verificação de integridade|Instância|
|FileSystemUsage|Uso do sistema de arquivos|Bytes|Média|Uso do sistema de arquivos|Nenhum|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Requests|Requests|Contagem|Total|Requests|Instância|
|BytesReceived|Entrada de Dados|Bytes|Total|Entrada de Dados|Instância|
|BytesSent|Saída de dados|Bytes|Total|Saída de dados|Instância|
|Http101|Http 101|Contagem|Total|Http 101|Instância|
|Http2xx|Http 2xx|Contagem|Total|Http 2xx|Instância|
|Http3xx|Http 3xx|Contagem|Total|Http 3xx|Instância|
|Http401|Http 401|Contagem|Total|Http 401|Instância|
|Http403|Http 403|Contagem|Total|Http 403|Instância|
|Http404|Http 404|Contagem|Total|Http 404|Instância|
|Http406|Http 406|Contagem|Total|Http 406|Instância|
|Http4xx|Http 4xx|Contagem|Total|Http 4xx|Instância|
|Http5xx|Erros do Servidor Http|Contagem|Total|Erros do Servidor Http|Instância|
|AverageResponseTime|Tempo Médio de Resposta|Segundos|Média|Tempo Médio de Resposta|Instância|
|CpuPercentage|Percentual de CPU|Porcentagem|Média|Percentual de CPU|Instância|
|MemoryPercentage|Porcentagem de Memória|Porcentagem|Média|Porcentagem de Memória|Instância|
|DiskQueueLength|Tamanho da fila do disco|Contagem|Média|Tamanho da fila do disco|Instância|
|HttpQueueLength|Tamanho da Fila de Http|Contagem|Média|Tamanho da Fila de Http|Instância|
|ActiveRequests|Solicitações ativas|Contagem|Total|Solicitações ativas|Instância|
|TotalFrontEnds|Total de front-ends|Contagem|Média|Total de front-ends|Nenhum|
|SmallAppServicePlanInstances|Funções de trabalho pequenas do Plano do Serviço de Aplicativo|Contagem|Média|Funções de trabalho pequenas do Plano do Serviço de Aplicativo|Nenhum|
|MediumAppServicePlanInstances|Funções de trabalho médias do Plano do Serviço de Aplicativo|Contagem|Média|Funções de trabalho médias do Plano do Serviço de Aplicativo|Nenhum|
|LargeAppServicePlanInstances|Funções de trabalho grandes do Plano do Serviço de Aplicativo|Contagem|Média|Funções de trabalho grandes do Plano do Serviço de Aplicativo|Nenhum|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|WorkersTotal|Total de funções de trabalho|Contagem|Média|Total de funções de trabalho|Nenhum|
|WorkersAvailable|Funções de trabalho disponíveis|Contagem|Média|Funções de trabalho disponíveis|Nenhum|
|WorkersUsed|Funções de trabalho usadas|Contagem|Média|Funções de trabalho usadas|Nenhum|
|CpuPercentage|Percentual de CPU|Porcentagem|Média|Percentual de CPU|Instância|
|MemoryPercentage|Porcentagem de Memória|Porcentagem|Média|Porcentagem de Memória|Instância|
## <a name="next-steps"></a>Próximas etapas
* [Leia sobre as métricas no Azure Monitor](data-platform.md)
* [Criar alertas nas métricas](alerts-overview.md)
* [Exportar as métricas de armazenamento, Hub de eventos ou Log Analytics](platform-logs-overview.md)

