---
title: Métricas compatíveis por tipo de recurso do Azure Monitor
description: Lista de métricas disponíveis para cada tipo de recurso com o Azure Monitor.
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 03/17/2020
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: 79bae9712cea04425cc36414ec56fdddd4345eab
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586032"
---
# <a name="supported-metrics-with-azure-monitor"></a>Métricas compatíveis com o Azure Monitor

> [!NOTE]
> Esta lista é gerada em grande parte automaticamente a partir da API Rest Do Monitor Do Azure. Qualquer modificação feita nesta lista via Github pode ser escrita sem aviso prévio. Entre em contato com o autor deste artigo para obter detalhes sobre como fazer atualizações permanentes.

O Azure Monitor fornece várias maneiras de interagir com as métricas, incluindo a criação de gráficos para os mesmos no portal, acessá-las por meio da API REST ou consultá-las usando o PowerShell ou a CLI. Abaixo está uma lista completa de todas as métricas atualmente disponíveis do pipeline de métrica do Azure Monitor. Outras métricas podem estar disponíveis no portal ou usando as APIs herdadas. Essa lista abaixo inclui apenas as métricas disponíveis usando o pipeline de métrica consolidado do Azure Monitor. As métricas são organizadas por namespace. Para obter uma lista de serviços e os namespaces que pertencem a eles, consulte [provedores de recursos para serviços Do Azure](../../azure-resource-manager/management/azure-services-resource-providers.md). Para consultar e acessar essas métricas de forma programática, use a [versão api 2018-01-01](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)

> [!NOTE]
> Atualmente, não há suporte para o envio da métrica multidimensional por meio das configurações de diagnóstico. As métricas com dimensões são exportadas como métricas dimensionais simples, agregadas nos valores da dimensão.
>
> *Por exemplo*: a métrica “Mensagens de Entrada” em um Hub de Eventos pode ser explorada e mapeada por nível da fila. No entanto, quando exportada por meio das configurações de diagnóstico, a métrica será representada como todas as mensagens de entrada em todas as filas no Hub de Eventos.
>
> Para obter uma lista de métricas da plataforma exportáveis através de configurações de diagnóstico, consulte [este artigo](metrics-supported-export-diagnostic-settings.md).


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
|CurrentConnections|Conexão: conexões atuais|Contagem|Média|Número atual de conexões de cliente estabelecidas.|ServerResourceType|
|CleanerCurrentPrice|Memória: preço atual do limpador|Contagem|Média|Preço atual da memória, $/byte/tempo, normalizado em 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Memória: memória do limpador reduzível|Bytes|Média|Quantidade de memória, em bytes, sujeita a eliminação pelo limpador na tela de fundo.|ServerResourceType|
|CleanerMemoryNonshrinkable|Memória: memória do limpador não reduzível|Bytes|Média|Quantidade de memória, em bytes, não sujeita a eliminação pelo limpador na tela de fundo.|ServerResourceType|
|MemoryUsage|Memória: uso de memória|Bytes|Média|Uso de memória do processo do servidor, como usado no cálculo de preço de memória do limpador. Igual ao contador Processo\PrivateBytes mais o tamanho dos dados mapeados em memória, ignorando qualquer memória mapeada ou alocada pelo mecanismo de análise de memória xVelocity (VertiPaq), além do limite de memória do mecanismo xVelocity.|ServerResourceType|
|MemoryLimitHard|Memória: limite de memória física|Bytes|Média|Limite de memória física, do arquivo de configuração.|ServerResourceType|
|MemoryLimitHigh|Memória: limite de memória superior|Bytes|Média|Limite de memória superior, do arquivo de configuração.|ServerResourceType|
|MemoryLimitLow|Memória: limite de memória inferior|Bytes|Média|Limite de memória inferior, do arquivo de configuração.|ServerResourceType|
|MemoryLimitVertiPaq|Memória: VertiPaq do limite de memória|Bytes|Média|Limite na memória, do arquivo de configuração.|ServerResourceType|
|Quota|Memória: cota|Bytes|Média|Cota de memória atual, em bytes. A cota de memória também é conhecida como uma reserva de memória ou concessão de memória.|ServerResourceType|
|QuotaBlocked|Memória: cota bloqueada|Contagem|Média|Número atual de solicitações de cota bloqueadas até que outras cotas de memória sejam liberadas.|ServerResourceType|
|VertiPaqNonpaged|Memória: VertiPaq não paginado|Bytes|Média|Bytes de memória bloqueada no conjunto de trabalho para uso pelo mecanismo na memória.|ServerResourceType|
|VertiPaqPaged|Memória: VertiPaq paginado|Bytes|Média|Bytes de memória paginada em uso para dados na memória.|ServerResourceType|
|RowsReadPerSec|Processamento: linhas lidas por segundo|CountPerSecond|Média|Taxa de linhas lidas de todos os bancos de dados relacionais.|ServerResourceType|
|RowsConvertedPerSec|Processamento: linhas convertidas por segundo|CountPerSecond|Média|Taxa de linhas convertidas durante o processamento.|ServerResourceType|
|RowsWrittenPerSec|Processamento: linhas gravadas por segundo|CountPerSecond|Média|Taxa de linhas gravadas durante o processamento.|ServerResourceType|
|CommandPoolBusyThreads|Threads: threads ocupados do pool comando|Contagem|Média|Número de threads ocupados no pool de threads de comando.|ServerResourceType|
|CommandPoolIdleThreads|Threads: threads ociosos do pool de comandos|Contagem|Média|Número de threads ociosos no pool de threads de comando.|ServerResourceType|
|LongParsingBusyThreads|Threads: threads ocupados de análise longa|Contagem|Média|Número de threads ocupados no pool de threads de análise longa.|ServerResourceType|
|LongParsingIdleThreads|Threads: threads ociosos de análise longa|Contagem|Média|Número de threads ociosos no pool de threads de análise longa.|ServerResourceType|
|LongParsingJobQueueLength|Threads: tamanho da fila de trabalhos de análise longa|Contagem|Média|Número de trabalhos na fila do pool de threads de análise longa.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Threads: threads de trabalho de E/S ocupados do pool de processamento|Contagem|Média|Número de threads que executam trabalhos de E/S no pool de threads de processamento.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Threads: threads de trabalho não E/S ocupados do pool de processamento|Contagem|Média|Número de threads que executam trabalhos não E/S no pool de threads de processamento.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Threads: tamanho da fila de trabalhos de E/S do pool de processamento|Contagem|Média|Número de trabalhos de E/S na fila do pool de threads de processamento.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Threads: threads de trabalho de E/S ociosos do pool de processamento|Contagem|Média|Número de threads ociosos para trabalhos de E/S no pool de threads de processamento.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Threads: threads de trabalho não E/S ociosos do pool de processamento|Contagem|Média|Número de threads ociosos no pool de threads de processamento dedicado a trabalhos não E/S.|ServerResourceType|
|QueryPoolIdleThreads|Threads: threads ociosos do pool de consultas|Contagem|Média|Número de threads ociosos para trabalhos de E/S no pool de threads de processamento.|ServerResourceType|
|QueryPoolJobQueueLength|Threads: tamanho da fila de trabalhos do pool consultas|Contagem|Média|Número de trabalhos na fila do pool de threads de consulta.|ServerResourceType|
|ShortParsingBusyThreads|Threads: threads ocupados de análise resumida|Contagem|Média|Número de threads ocupados no pool de threads de análise resumida.|ServerResourceType|
|ShortParsingIdleThreads|Threads: threads ociosos de análise resumida|Contagem|Média|Número de threads ociosos no pool de threads de análise resumida.|ServerResourceType|
|ShortParsingJobQueueLength|Threads: tamanho da fila de trabalhos de análise resumida|Contagem|Média|Número de trabalhos na fila do pool de threads de análise resumida.|ServerResourceType|
|memory_thrashing_metric|Sobrecarga de memória|Porcentagem|Média|Sobrecarga de memória média.|ServerResourceType|
|mashup_engine_qpu_metric|QPU do mecanismo M|Contagem|Média|Uso de QPU por processos de mecanismo de mashup|ServerResourceType|
|mashup_engine_memory_metric|Memória do mecanismo M|Bytes|Média|Uso de memória pelos processos do mecanismo de mashup|ServerResourceType|
|mashup_engine_private_bytes_metric|M Engine Private Bytes|Bytes|Média|Uso de bytes privados por processos de mecanismo de mashup.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|M Engine Virtual Bytes|Bytes|Média|Uso de bytes virtuais por processos de mecanismo de mashup.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TotalRequests|Total de solicitações de gateway (depreciadas)|Contagem|Total|Número de solicitações de gateway - Use métrica de solicitação multi-dimensão com a dimensão GatewayResponseCodeCategory em vez disso|Localização,Nome do host|
|SuccessfulRequests|Solicitações de gateway bem-sucedidas (depreciadas)|Contagem|Total|Número de solicitações de gateway bem-sucedidas - Use métrica de solicitação de várias dimensões com a dimensão GatewayResponseCodeCategory em vez disso|Localização,Nome do host|
|UnauthorizedRequests|Solicitações de gateway não autorizadas (depreciadas)|Contagem|Total|Número de solicitações de gateway não autorizadas - Use métrica de solicitação de várias dimensões com a dimensão GatewayResponseCodeCategory em vez disso|Localização,Nome do host|
|FailedRequests|Solicitações de gateway com falha (depreciadas)|Contagem|Total|Número de falhas nas solicitações de gateway - Use métrica de solicitação multi-dimensão com a dimensão GatewayResponseCodeCategory em vez disso|Localização,Nome do host|
|OtherRequests|Outras solicitações de gateway (depreciadas)|Contagem|Total|Número de outras solicitações de gateway - Use métrica de solicitação multi-dimensão com a dimensão GatewayResponseCodeCategory em vez disso|Localização,Nome do host|
|Duration|Duração total de solicitações de gateway|Milissegundos|Média|Duração total de solicitações de gateway em milissegundos|Localização,Nome do host|
|Duração do backend|Duração das solicitações de backend|Milissegundos|Média|Duração das solicitações de backend em milissegundos|Localização,Nome do host|
|Capacity|Capacity|Porcentagem|Média|Métrica de utilização para o serviço ApiManagement|Location|
|EventHubTotalEvents|Total de Eventos EventHub|Contagem|Total|Número de eventos enviados ao EventHub|Location|
|EventHubEventos bem-sucedidos|Eventos de sucesso do EventHub|Contagem|Total|Número de eventos bem-sucedidos do EventHub|Location|
|EventHubTotalFailedEvents|Eventos eventhub com falha|Contagem|Total|Número de eventos do EventHub com falha|Location|
|EventHubRejectedEvents|Eventos eventhub rejeitados|Contagem|Total|Número de eventos eventhub rejeitados (configuração errada ou não autorizada)|Location|
|Eventos EventHubThrottledEvents|Eventos de EventHub estrangulados|Contagem|Total|Número de eventos do EventHub estrangulados|Location|
|EventHubTimedoutEvents|Eventos EventHub com data esgotado|Contagem|Total|Número de eventos eventhub cronometrados|Location|
|EventHubDroppedEvents|Eventos EventHub descartados|Contagem|Total|Número de eventos ignorados por causa do limite de tamanho da fila atingido|Location|
|EventHubTotalBytesSent|Tamanho dos eventos eventhub|Bytes|Total|Tamanho total dos eventos eventHub em bytes|Location|
|Requests|Requests|Contagem|Total|Métricas de solicitação de gateway com várias dimensões|Localização,nome de host,LastErrorReason,BackendResponseCode,GatewayResponseCode,BackendResponseCo-metragem,GatewayResponseCCategoria|
|Conectividade de rede|Status de conectividade de rede de recursos (Visualização)|Contagem|Total|Status de conectividade de rede de tipos de recursos dependentes do serviço de gerenciamento de API|Localização,Tipo de recurso|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguração/configuraçãoLojas

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|HttpIncomingRequestCount|HttpIncomingRequestCount|Contagem|Contagem|Número total de solicitações http recebidas.|StatusCode|
|HttpIncomingRequestDuration|HttpIncomingRequestDuration|Contagem|Média|Latência em um pedido http.|StatusCode|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|SistemaCpuUsagePercentage|Porcentagem de uso da CPU do sistema|Porcentagem|Média|O uso recente da CPU para todo o sistema|Nome do aplicativo,Pod|
|AppCpuUsagePercentage|Porcentagem de uso da CPU do aplicativo|Porcentagem|Média|Porcentagem de uso da CPU do App JVM|Nome do aplicativo,Pod|
|Memória de aplicativocomprometida|Memória do aplicativo atribuída|Bytes|Média|Memória atribuída ao JVM em bytes|Nome do aplicativo,Pod|
|AppMemoryUsed|Memória do aplicativo usada|Bytes|Média|Memória do aplicativo Usada em bytes|Nome do aplicativo,Pod|
|AppMemoryMax|Memória do aplicativo Max|Bytes|Máximo|A quantidade máxima de memória em bytes que podem ser usados para o gerenciamento de memória|Nome do aplicativo,Pod|
|MaxOldGenMemoryPoolBytes|Tamanho máximo de dados da geração antiga disponível|Bytes|Média|Tamanho máximo do pool de memória da geração antiga|Nome do aplicativo,Pod|
|OldGenMemoryPoolBytes|Tamanho de dados da geração antiga|Bytes|Média|Tamanho do pool de memória da geração antiga depois de um GC completo|Nome do aplicativo,Pod|
|OldGenPromotedBytes|Promover ao tamanho de dados da geração antiga|Bytes|Máximo|Contagem de aumentos positivos no tamanho do pool de memória da geração antiga antes de GC para depois de GC|Nome do aplicativo,Pod|
|YoungGenPromotedBytes|Promover ao Tamanho de Dados da Geração Jovem|Bytes|Máximo|Incrementado para um aumento no tamanho do pool de memória da geração jovem depois de um GC para antes do próximo|Nome do aplicativo,Pod|
|Contagem total de GCPause|Contagem de pausa sinuosa gc|Contagem|Total|Contagem de pausa sinuosa gc|Nome do aplicativo,Pod|
|GCPauseTotalTimeTime|TEMPO total de pausa do GC|Milissegundos|Total|TEMPO total de pausa do GC|Nome do aplicativo,Pod|
|TomcatSentBytes|Tomcat Total Sent Bytes|Bytes|Total|Tomcat Total Sent Bytes|Nome do aplicativo,Pod|
|TomcatReceivedBytes|Bytes recebidos no total de tomcat|Bytes|Total|Bytes recebidos no total de tomcat|Nome do aplicativo,Pod|
|TomcatRequestTotalTime|Tomcat Request Total Times|Milissegundos|Total|Tomcat Request Total Times|Nome do aplicativo,Pod|
|TomcatRequestTotalCount|Contagem total de solicitações de tomcat|Contagem|Total|Contagem total de solicitações de tomcat|Nome do aplicativo,Pod|
|TomcatResponseAvgTime|Tempo médio de solicitação de tomcat|Milissegundos|Média|Tempo médio de solicitação de tomcat|Nome do aplicativo,Pod|
|TomcatRequestMaxTime|Tomcat Solicitar Tempo Máximo|Milissegundos|Máximo|Tomcat Solicitar Tempo Máximo|Nome do aplicativo,Pod|
|TomcatErrorCount|Erro global da Tomcat|Contagem|Total|Erro global da Tomcat|Nome do aplicativo,Pod|
|TomcatSessionActiveMaxCount|Contagem ativa da sessão Tomcat|Contagem|Total|Contagem ativa da sessão Tomcat|Nome do aplicativo,Pod|
|TomcatSessionAliveMaxTime|Tomcat Session Max Tempo Vivo|Milissegundos|Máximo|Tomcat Session Max Tempo Vivo|Nome do aplicativo,Pod|
|TomcatSessionActiveCurrentCount|Tomcat Session Alive Count|Contagem|Total|Tomcat Session Alive Count|Nome do aplicativo,Pod|
|TomcatSessionCrioucontagem|Contagem criada da sessão tomcat|Contagem|Total|Contagem criada da sessão tomcat|Nome do aplicativo,Pod|
|Contagem expirada do TomcatSession|Contagem expirada da sessão tomcat|Contagem|Total|Contagem expirada da sessão tomcat|Nome do aplicativo,Pod|
|Contagem rejeitada de TomcatSession|Contagem rejeitada da sessão de Tomcat|Contagem|Total|Contagem rejeitada da sessão de Tomcat|Nome do aplicativo,Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TotalJob|Total de trabalhos|Contagem|Total|O número total de trabalhos|Runbook,Status|
|TotalUpdateDeploymentExecutado|Total de execuções da implantação de atualização|Contagem|Total|Total de implantações de atualização de software é executada|Nome de configuração de atualização de software,status|
|TotalUpdateDeploymentMachineExecut|Total de execuções de computador da implantação de atualização|Contagem|Total|Máquina de implantação de atualização de software total é executada em uma execução de implantação de atualização de software|SoftwareAtualizaçãoNomede configuração,status,TargetComputer,SoftwareUpdateConfigurationRunId|


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
|TaskStartEvent|Eventos da tarefa de inicialização|Contagem|Total|Número total de tarefas que iniciaram|poolId,jobId|
|TaskCompleteEvent|Eventos de conclusão de tarefa|Contagem|Total|Número total de tarefas concluídas|poolId,jobId|
|TaskFailEvent|Eventos de falha de tarefa|Contagem|Total|Número total de tarefas que foram concluídas em um estado com falha|poolId,jobId|
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


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/espaços de trabalho

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Trabalho submetido|Trabalho submetido|Contagem|Total|Número de vagas submetidas|Cenário,Nome de Cluster|
|Trabalho concluído|Trabalho concluído|Contagem|Total|Número de vagas concluídas|Cenário,Nome de cluster,tipo de resultado|
|Nodes Totais|Nodes Totais|Contagem|Média|Número de nodos totais|Cenário,Nome de Cluster|
|Nódulos ativos|Nódulos ativos|Contagem|Média|Número de nós em execução|Cenário,Nome de Cluster|
|Nódulos ociosos|Nódulos ociosos|Contagem|Média|Número de nós ociosos|Cenário,Nome de Cluster|
|Nós inutilizáveis|Nós inutilizáveis|Contagem|Média|Número de nós inutilizáveis|Cenário,Nome de Cluster|
|Lemos pré-empáticos|Lemos pré-empáticos|Contagem|Média|Número de nós com preempção|Cenário,Nome de Cluster|
|Deixando nódulos|Deixando nódulos|Contagem|Média|Número de nódulos de saída|Cenário,Nome de Cluster|
|Núcleos Totais|Núcleos Totais|Contagem|Média|Número de núcleos totais|Cenário,Nome de Cluster|
|Núcleos ativos|Núcleos ativos|Contagem|Média|Número de núcleos ativos|Cenário,Nome de Cluster|
|Núcleos ociosos|Núcleos ociosos|Contagem|Média|Número de núcleos ociosos|Cenário,Nome de Cluster|
|Núcleos inutilizáveis|Núcleos inutilizáveis|Contagem|Média|Número de núcleos inutilizáveis|Cenário,Nome de Cluster|
|Núcleos pré-empáveis|Núcleos pré-empáveis|Contagem|Média|Número de núcleos pré-empáveis|Cenário,Nome de Cluster|
|Deixando núcleos|Deixando núcleos|Contagem|Média|Número de núcleos de saída|Cenário,Nome de Cluster|
|Percentual de Utilização de Cotas|Percentual de Utilização de Cotas|Contagem|Média|Percentual de cota utilizada|Cenário,ClusterName,VmFamilyName,VmPriority|

## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|CpuUsagePercentageInDouble|Porcentagem de uso da CPU|Porcentagem|Máximo|Porcentagem de uso da CPU|Nó|
|MemoryUsage|Uso de Memória|Bytes|Média|Uso de Memória|Nó|
|MemoryLimit|Limite de memória|Bytes|Média|Limite de memória|Nó|
|Memóriautilizaçãoporcentagememdobrar|Porcentagem de uso da memória|Porcentagem|Média|Porcentagem de uso da memória|Nó|
|ArmazenamentoUso|Uso de armazenamento|Bytes|Média|Uso de armazenamento|Nó|
|IOReadBytes|IO Ler Bytes|Bytes|Total|IO Ler Bytes|Nó|
|IOWriteBytes|IO Write Bytes|Bytes|Total|IO Write Bytes|Nó|
|ConexãoAceita|Conexões aceitas|Contagem|Total|Conexões aceitas|Nó|
|ConexãoManipulada|Conexões manuseadas|Contagem|Total|Conexões manuseadas|Nó|
|ConexãoAtiva|Conexões ativas|Contagem|Média|Conexões ativas|Nó|
|RequestHandle|Solicitações manipuladas|Contagem|Total|Solicitações manipuladas|Nó|
|Blocos processados|Blocos processados|Contagem|Total|Blocos processados|Nó|
|Transações processadas|Transações processadas|Contagem|Total|Transações processadas|Nó|
|Transações pendentes|Transações pendentes|Contagem|Média|Transações pendentes|Nó|
|Transações enfileiradas|Transações enfileiradas|Contagem|Média|Transações enfileiradas|Nó|



## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|connectedclients|Clientes conectados|Contagem|Máximo||ShardId|
|totalcommandsprocessed|Total de Operações|Contagem|Total||ShardId|
|cachehits|Acertos do Cache|Contagem|Total||ShardId|
|cachemisses|Erros de Cache|Contagem|Total||ShardId|
|cachemissrate|Taxa de Miss cache|Porcentagem|cachemissrate||ShardId|
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
|erros|Errors|Contagem|Máximo||ShardId,ErroTipo|
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




## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft.Cdn/cdnWebapplicationpolicies

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Contagem de solicitações do Firewall de Aplicativo Web|Contagem|Total|O número de solicitações de cliente processadas pelo Firewall do Aplicativo Web|Nome da política,Nome de regra,ação|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Porcentagem de CPU|Porcentagem de CPU|Porcentagem|Média|O percentual das unidades de computação alocadas que estão atualmente em uso pelas Máquinas Virtuais.|Nenhum|
|Entrada na rede|Entrada na rede|Bytes|Total|O número de bytes recebidos em todos os adaptadores de rede pelas Máquinas Virtuais (Tráfego de Entrada).|Nenhum|
|Saída da rede|Saída da rede|Bytes|Total|O número de bytes de saída em todos os adaptadores de rede pelas Máquinas Virtuais (Tráfego de Saída).|Nenhum|
|Disk Read Bytes/Sec|Leitura de disco|BytesPerSecond|Média|Média de bytes lidos do disco durante o período de monitoramento.|Nenhum|
|Disk Write Bytes/Sec|Gravação de disco|BytesPerSecond|Média|Média de bytes gravados em disco durante o período de monitoramento.|Nenhum|
|Operações de leitura de disco/Seg|Operações de leitura de disco/Seg|CountPerSecond|Média|IOPS de leitura de disco.|Nenhum|
|Operações de gravação de disco/Seg|Operações de gravação de disco/Seg|CountPerSecond|Média|IOPS de gravação de disco.|Nenhum|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Porcentagem de CPU|Porcentagem de CPU|Porcentagem|Média|O percentual das unidades de computação alocadas que estão atualmente em uso pelas Máquinas Virtuais.|RoleInstanceId|
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
|Transactions|Transactions|Contagem|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|Tipo de resposta,GeoType,ApiName,Autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|GeoType,ApiName,Autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável.|GeoType,ApiName,Autenticação|
|SuccessServerLatency|Latência de Servidor Com Sucesso|Milissegundos|Média|A latência usada pelo Azure Storage para processar uma solicitação bem sucedida, em milissegundos. Esse valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType,ApiName,Autenticação|
|SuccessE2ELatency|Latência de E2E com Sucesso|Milissegundos|Média|A latência de ponta a ponta de solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|GeoType,ApiName,Autenticação|
|Disponibilidade|Disponibilidade|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|GeoType,ApiName,Autenticação|

## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft.ClassicArmazenamento/armazenamentoContas/blobServices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BlobCapacity|Capacidade do Blob|Bytes|Média|A quantidade de armazenamento usada pelo serviço Blob da conta de armazenamento em bytes.|BlobType,Tier|
|BlobCount|Contagem de Blobs|Contagem|Média|O número de Blob no serviço Blob da conta de armazenamento.|BlobType,Tier|
|ContainerCount|Contagem de Contêineres de Blobs|Contagem|Média|O número de contêineres no serviço Blob da conta de armazenamento.|Nenhum|
|IndexCapacity|Capacidade de índice|Bytes|Média|A quantidade de armazenamento utilizada pelo Índice ADLS Gen2 (Hierárquico) em bytes.|Nenhum|
|Transactions|Transactions|Contagem|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|Tipo de resposta,GeoType,ApiName,Autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|GeoType,ApiName,Autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável.|GeoType,ApiName,Autenticação|
|SuccessServerLatency|Latência de Servidor Com Sucesso|Milissegundos|Média|A latência usada pelo Azure Storage para processar uma solicitação bem sucedida, em milissegundos. Esse valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType,ApiName,Autenticação|
|SuccessE2ELatency|Latência de E2E com Sucesso|Milissegundos|Média|A latência de ponta a ponta de solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|GeoType,ApiName,Autenticação|
|Disponibilidade|Disponibilidade|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|GeoType,ApiName,Autenticação|

## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft.ClassicArmazenamento/armazenamentoContas/tabelaServiços

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TableCapacity|Capacidade da Tabela|Bytes|Média|A quantidade de armazenamento usada pelo serviço table da conta de armazenamento em bytes.|Nenhum|
|TableCount|Contagem de Tabelas|Contagem|Média|O número de tabela no serviço de tabela da conta de armazenamento.|Nenhum|
|TableEntityCount|Contagem de Entidades de Tabela|Contagem|Média|O número de entidades de tabela no serviço de Tabela da conta de armazenamento.|Nenhum|
|Transactions|Transactions|Contagem|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|Tipo de resposta,GeoType,ApiName,Autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|GeoType,ApiName,Autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável.|GeoType,ApiName,Autenticação|
|SuccessServerLatency|Latência de Servidor Com Sucesso|Milissegundos|Média|A latência usada pelo Azure Storage para processar uma solicitação bem sucedida, em milissegundos. Esse valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType,ApiName,Autenticação|
|SuccessE2ELatency|Latência de E2E com Sucesso|Milissegundos|Média|A latência de ponta a ponta de solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|GeoType,ApiName,Autenticação|
|Disponibilidade|Disponibilidade|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|GeoType,ApiName,Autenticação|

## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft.ClassicArmazenamento/armazenamentoContas/arquivosServiços

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|FileCapacity|Capacidade do Arquivo|Bytes|Média|A quantidade de armazenamento usada pelo serviço arquivo da conta de armazenamento em bytes.|FileShare|
|FileCount|Contagem de Arquivos|Contagem|Média|O número de arquivos no serviço arquivo da conta de armazenamento.|FileShare|
|FileShareCount|Contagem de Compartilhamentos de Arquivos|Contagem|Média|O número de compartilhamentos de arquivos no serviço de arquivo da conta de armazenamento.|Nenhum|
|FileShareSnapshotContag|Contagem de instantâneos de compartilhamento de arquivos|Contagem|Média|O número de instantâneos presentes na parte no Serviço de Arquivos da conta de armazenamento.|FileShare|
|FileShareSnapshotSize|Tamanho do instantâneo do compartilhamento de arquivos|Bytes|Média|A quantidade de armazenamento usada pelos snapshots no serviço Arquivo da conta de armazenamento em bytes.|FileShare|
|FileShareQuota|Tamanho da cota de compartilhamento de arquivos|Bytes|Média|O limite superior na quantidade de armazenamento que pode ser usado pelo Azure Files Service em bytes.|FileShare|
|Transactions|Transactions|Contagem|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|Tipo de resposta,GeoType,ApiName,Autenticação,FileShare|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|GeoType,ApiName,Autenticação,FileShare|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável.|GeoType,ApiName,Autenticação,FileShare|
|SuccessServerLatency|Latência de Servidor Com Sucesso|Milissegundos|Média|A latência usada pelo Azure Storage para processar uma solicitação bem sucedida, em milissegundos. Esse valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType,ApiName,Autenticação,FileShare|
|SuccessE2ELatency|Latência de E2E com Sucesso|Milissegundos|Média|A latência de ponta a ponta de solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|GeoType,ApiName,Autenticação,FileShare|
|Disponibilidade|Disponibilidade|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|GeoType,ApiName,Autenticação,FileShare|

## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft.ClassicArmazenamento/armazenamentoContas/queueServices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|QueueCapacity|Capacidade da Fila|Bytes|Média|A quantidade de armazenamento usada pelo serviço de fila da conta de armazenamento em bytes.|Nenhum|
|QueueCount|Contagem de Filas|Contagem|Média|O número de filas no serviço de fila da conta de armazenamento.|Nenhum|
|QueueMessageCount|Contagem de Mensagens da Fila|Contagem|Média|O número aproximado de mensagens de fila no serviço de fila da conta de armazenamento.|Nenhum|
|Transactions|Transactions|Contagem|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|Tipo de resposta,GeoType,ApiName,Autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|GeoType,ApiName,Autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável.|GeoType,ApiName,Autenticação|
|SuccessServerLatency|Latência de Servidor Com Sucesso|Milissegundos|Média|A latência usada pelo Azure Storage para processar uma solicitação bem sucedida, em milissegundos. Esse valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType,ApiName,Autenticação|
|SuccessE2ELatency|Latência de E2E com Sucesso|Milissegundos|Média|A latência de ponta a ponta de solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|GeoType,ApiName,Autenticação|
|Disponibilidade|Disponibilidade|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|GeoType,ApiName,Autenticação|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TotalCalls|Total de Chamadas|Contagem|Total|Número total de chamadas.|ApiName,OperationName,Região|
|SuccessfulCalls|Chamadas com Êxito|Contagem|Total|Número de chamadas com êxito.|ApiName,OperationName,Região|
|TotalErrors|Total de Erros|Contagem|Total|Número total de chamadas com resposta de erro (código de resposta HTTP 4xx ou 5xx).|ApiName,OperationName,Região|
|BlockedCalls|Chamadas Bloqueadas|Contagem|Total|Número de chamadas que excederam a taxa ou o limite de cota.|ApiName,OperationName,Região|
|ServerErrors|Erros do Servidor|Contagem|Total|Número de chamadas com erro interno do serviço (código de resposta HTTP 5xx).|ApiName,OperationName,Região|
|ClientErrors|Erros do Cliente|Contagem|Total|Número de chamadas com erro do lado do cliente (código de resposta HTTP 4xx).|ApiName,OperationName,Região|
|DataIn|Entrada de Dados|Bytes|Total|Tamanho dos dados de entrada em bytes.|ApiName,OperationName,Região|
|DataOut|Saída de dados|Bytes|Total|Tamanho dos dados de saída em bytes.|ApiName,OperationName,Região|
|Latency|Latency|MilliSeconds|Média|Latência em milissegundos.|ApiName,OperationName,Região|
|TotalTokenCalls|Total de chamadas de token|Contagem|Total|Número total de chamadas de token.|ApiName,OperationName,Região|
|CharactersTranslated|Caracteres traduzidos|Contagem|Total|Número total de caracteres na solicitação de texto de entrada.|ApiName,OperationName,Região|
|Personagenstreinados|Personagens treinados|Contagem|Total|Número total de personagens treinados.|ApiName,OperationName,Região|
|SpeechSessionDuration|Duração da Sessão de Fala|Segundos|Total|Duração total da sessão de fala em segundos.|ApiName,OperationName,Região|
|TotalTransactions|Total de Transações|Contagem|Total|Número total de transações.|Nenhum|
|ProcessedImages|Imagens processadas|Contagem|Total| Número de transações para processamento de imagens.|ApiName,FeatureName,Canal,Região|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Porcentagem de CPU|Porcentagem de CPU|Porcentagem|Média|A porcentagem das unidades de computação alocadas que estão atualmente em uso pela máquina virtual ou pelas máquinas virtuais|Nenhum|
|Entrada na rede|Rede Em Billable (Depreciada)|Bytes|Total|O número de bytes faturados recebidos em todas as interfaces de rede pela Máquina Virtual (Tráfego de Entrada) (Depreciado)|Nenhum|
|Saída da rede|Rede Out Billable (Depreciada)|Bytes|Total|O número de bytes faturados em todas as interfaces de rede pela Virtual Machine(s) (Outgoing Traffic) (Depreciada)|Nenhum|
|Bytes de leitura de disco|Bytes de leitura de disco|Bytes|Total|Bytes lidos do disco durante o período de monitoramento|Nenhum|
|Bytes de gravação de disco|Bytes de gravação de disco|Bytes|Total|Bytes gravados em disco durante o período de monitoramento|Nenhum|
|Operações de leitura de disco/Seg|Operações de leitura de disco/Seg|CountPerSecond|Média|IOPS de leitura de disco|Nenhum|
|Operações de gravação de disco/Seg|Operações de gravação de disco/Seg|CountPerSecond|Média|IOPS de gravação de disco|Nenhum|
|Créditos de CPU Restantes|Créditos de CPU Restantes|Contagem|Média|Número total de créditos disponíveis para intermitência|Nenhum|
|Créditos de CPU Consumidos|Créditos de CPU Consumidos|Contagem|Média|Número total de créditos consumido pela Máquina Virtual|Nenhum|
|Por Bytes de Leitura do Disco/s|Leitura de disco de dados Bytes/Seg [(preterido)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Bytes/Seg lidos a partir de um único disco durante o período de monitoramento|SlotId|
|Por Bytes de Gravação de Disco/s|Bytes de gravação de disco de dados/seg [(preteridos)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Bytes/Seg gravado em um único disco durante o período de monitoramento|SlotId|
|Por operações de Leitura de Disco/s|Operações de leitura de disco de dados/seg [(preterido)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Leia IOPS de um único disco durante o período de monitoramento|SlotId|
|Por Operações de Gravação de Disco/s|Operações de gravação de disco de dados/seg [(preterido)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitoramento|SlotId|
|Por QD de Disco|QD do disco [de dados (preterido)](portal-disk-metrics-deprecation.md)](portal-disk-metrics-deprecation.md)|Contagem|Média|Profundidade da Fila do Disco de Dados (ou Tamanho da Fila)|SlotId|
|SO Por Bytes de Leitura do Disco/s|Leitura de disco do SISTEMA OPERACIONAL Bytes/Seg [(preterida)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Bytes/Sec lidos de um único disco durante o período de monitoramento do disco DO S|Nenhum|
|SO Por Bytes de Gravação do Disco/s|Bytes de gravação de disco do SISTEMA OPERACIONAL/Seg [(preteridos)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Bytes/Sec gravado em um único disco durante o período de monitoramento do disco DO S|Nenhum|
|SO Por Operações de Leitura do Disco/s|Operações de leitura de disco do SISTEMA OPERACIONAL/Seg [(preteridas)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Leia IOPS de um único disco durante o período de monitoramento do disco do Sistema Operacional|Nenhum|
|SO Por Operações de Gravação do Disco/s|Operações de gravação de disco do SISTEMA OPERACIONAL/Seg [(preteridas)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitoramento do disco DO S|Nenhum|
|Os Por QD de Disco|Disco do SISTEMA OPERACIONAL [QD (Preterido)](portal-disk-metrics-deprecation.md)|Contagem|Média|Profundidade da Fila do Disco do SO (ou Tamanho da Fila)|Nenhum|
|Leitura de disco de dados Bytes/seg|Bytes de Leitura do Disco de Dados/s (Visualização)|CountPerSecond|Média|Bytes/Seg lidos a partir de um único disco durante o período de monitoramento|LUN|
|Bytes de gravação de disco de dados/seg|Bytes de Gravação do Disco de Dados/s (Visualização)|CountPerSecond|Média|Bytes/Seg gravado em um único disco durante o período de monitoramento|LUN|
|Operações de leitura de disco de dados/seg|Operações de Leitura do Disco de Dados/s (Visualização)|CountPerSecond|Média|Leia IOPS de um único disco durante o período de monitoramento|LUN|
|Operações de gravação de disco de dados/seg|Operações de Gravação do Disco de Dados/s (Visualização)|CountPerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitoramento|LUN|
|Profundidade da Fila de Disco de Dados|Profundidade da fila de discos de dados (visualização)|Contagem|Média|Profundidade da Fila do Disco de Dados (ou Tamanho da Fila)|LUN|
|Leitura de disco do SISTEMA OPERACIONAL Bytes/seg|Bytes de Leitura do Disco do SO/s (Visualização)|CountPerSecond|Média|Bytes/Sec lidos de um único disco durante o período de monitoramento do disco DO S|Nenhum|
|Bytes de gravação de disco do SISTEMA OPERACIONAL/seg|Bytes de Gravação do Disco do SO/s (Visualização)|CountPerSecond|Média|Bytes/Sec gravado em um único disco durante o período de monitoramento do disco DO S|Nenhum|
|Operações de leitura de disco do SISTEMA OPERACIONAL/Seg|Operações de Leitura do Disco do SO/s (Visualização)|CountPerSecond|Média|Leia IOPS de um único disco durante o período de monitoramento do disco do Sistema Operacional|Nenhum|
|Operações de gravação de disco do SISTEMA OPERACIONAL/Seg|Operações de Gravação do Disco do SO/s (Visualização)|CountPerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitoramento do disco DO S|Nenhum|
|Profundidade da Fila de Disco do SO|Profundidade da fila do disco do SISTEMA OPERACIONAL (Visualização)|Contagem|Média|Profundidade da Fila do Disco do SO (ou Tamanho da Fila)|Nenhum|
|Fluxos de Entrada|Fluxos de Entrada|Contagem|Média|Fluxos de entrada são o número de fluxos de corrente na direção de entrada (tráfego indo para a VM)|Nenhum|
|Fluxos de saída|Fluxos de saída|Contagem|Média|Fluxos de saída são o número de fluxos de corrente na direção de saída (tráfego saindo da VM)|Nenhum|
|Taxa de criação máxima dos fluxos de entrada|Taxa de criação máxima dos fluxos de entrada|CountPerSecond|Média|A taxa máxima de criação de fluxos de entrada (tráfego indo para a VM)|Nenhum|
|Taxa de criação máxima dos fluxos de saída|Taxa de criação máxima dos fluxos de saída|CountPerSecond|Média|A taxa máxima de criação de fluxos de saída (tráfego saindo da VM)|Nenhum|
|Impacto de leitura de leitura de armazenamento de disco de dados premium|Impacto de leitura de leitura de armazenamento de dados premium (visualização)|Porcentagem|Média|Impacto de leitura de leitura de armazenamento de disco de dados premium|LUN|
|Cache de disco de dados premium Leia Miss|Cache de disco de dados premium Ler falta (visualização)|Porcentagem|Média|Cache de disco de dados premium Leia Miss|LUN|
|Impacto de leitura de leitura de cache do sistema operacional premium|A cessação de leitura de leitura do cache do sistema operacional premium (visualização)|Porcentagem|Média|Impacto de leitura de leitura de cache do sistema operacional premium|Nenhum|
|Cache de disco premium do SISTEMA OPERACIONAL Ler Miss|Cache de disco premium do Sistema Operacional Read Miss (Visualização)|Porcentagem|Média|Cache de disco premium do SISTEMA OPERACIONAL Ler Miss|Nenhum|
|Rede no total|Rede no total|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela máquina virtual ou pelas máquinas virtuais (tráfego de entrada)|Nenhum|
|Rede Out Total|Rede Out Total|Bytes|Total|O número de bytes de saída em todas as interfaces de rede pela máquina virtual ou pelas máquinas virtuais (tráfego de saída)|Nenhum|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Porcentagem de CPU|Porcentagem de CPU|Porcentagem|Média|A porcentagem das unidades de computação alocadas que estão atualmente em uso pela máquina virtual ou pelas máquinas virtuais|VMName|
|Entrada na rede|Rede Em Billable (Depreciada)|Bytes|Total|O número de bytes faturados recebidos em todas as interfaces de rede pela Máquina Virtual (Tráfego de Entrada) (Depreciado)|VMName|
|Saída da rede|Rede Out Billable (Depreciada)|Bytes|Total|O número de bytes faturados em todas as interfaces de rede pela Virtual Machine(s) (Outgoing Traffic) (Depreciada)|VMName|
|Bytes de leitura de disco|Bytes de leitura de disco|Bytes|Total|Bytes lidos do disco durante o período de monitoramento|VMName|
|Bytes de gravação de disco|Bytes de gravação de disco|Bytes|Total|Bytes gravados em disco durante o período de monitoramento|VMName|
|Operações de leitura de disco/Seg|Operações de leitura de disco/Seg|CountPerSecond|Média|IOPS de leitura de disco|VMName|
|Operações de gravação de disco/Seg|Operações de gravação de disco/Seg|CountPerSecond|Média|IOPS de gravação de disco|VMName|
|Créditos de CPU Restantes|Créditos de CPU Restantes|Contagem|Média|Número total de créditos disponíveis para intermitência|Nenhum|
|Créditos de CPU Consumidos|Créditos de CPU Consumidos|Contagem|Média|Número total de créditos consumido pela Máquina Virtual|Nenhum|
|Por Bytes de Leitura do Disco/s|Leitura de disco de dados Bytes/Seg [(preterido)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Bytes/Seg lidos a partir de um único disco durante o período de monitoramento|SlotId|
|Por Bytes de Gravação de Disco/s|Bytes de gravação de disco de dados/seg [(preteridos)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Bytes/Seg gravado em um único disco durante o período de monitoramento|SlotId|
|Por operações de Leitura de Disco/s|Operações de leitura de disco de dados/seg [(preterido)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Leia IOPS de um único disco durante o período de monitoramento|SlotId|
|Por Operações de Gravação de Disco/s|Operações de gravação de disco de dados/seg [(preterido)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitoramento|SlotId|
|Por QD de Disco|QD do disco [de dados (preterido)](portal-disk-metrics-deprecation.md)|Contagem|Média|Profundidade da Fila do Disco de Dados (ou Tamanho da Fila)|SlotId|
|SO Por Bytes de Leitura do Disco/s|Leitura de disco do SISTEMA OPERACIONAL Bytes/Seg [(preterida)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Bytes/Sec lidos de um único disco durante o período de monitoramento do disco DO S|Nenhum|
|SO Por Bytes de Gravação do Disco/s|Bytes de gravação de disco do SISTEMA OPERACIONAL/Seg [(preteridos)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Bytes/Sec gravado em um único disco durante o período de monitoramento do disco DO S|Nenhum|
|SO Por Operações de Leitura do Disco/s|Operações de leitura de disco do SISTEMA OPERACIONAL/Seg [(preteridas)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Leia IOPS de um único disco durante o período de monitoramento do disco do Sistema Operacional|Nenhum|
|SO Por Operações de Gravação do Disco/s|Operações de gravação de disco do SISTEMA OPERACIONAL/Seg [(preteridas)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitoramento do disco DO S|Nenhum|
|Os Por QD de Disco|Disco do SISTEMA OPERACIONAL [QD (Preterido)](portal-disk-metrics-deprecation.md)|Contagem|Média|Profundidade da Fila do Disco do SO (ou Tamanho da Fila)|Nenhum|
|Leitura de disco de dados Bytes/seg|Bytes de Leitura do Disco de Dados/s (Visualização)|CountPerSecond|Média|Bytes/Seg lidos a partir de um único disco durante o período de monitoramento|LUN, VMName|
|Bytes de gravação de disco de dados/seg|Bytes de Gravação do Disco de Dados/s (Visualização)|CountPerSecond|Média|Bytes/Seg gravado em um único disco durante o período de monitoramento|LUN, VMName|
|Operações de leitura de disco de dados/seg|Operações de Leitura do Disco de Dados/s (Visualização)|CountPerSecond|Média|Leia IOPS de um único disco durante o período de monitoramento|LUN, VMName|
|Operações de gravação de disco de dados/seg|Operações de Gravação do Disco de Dados/s (Visualização)|CountPerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitoramento|LUN, VMName|
|Profundidade da Fila de Disco de Dados|Profundidade da fila de discos de dados (visualização)|Contagem|Média|Profundidade da Fila do Disco de Dados (ou Tamanho da Fila)|LUN, VMName|
|Leitura de disco do SISTEMA OPERACIONAL Bytes/seg|Bytes de Leitura do Disco do SO/s (Visualização)|CountPerSecond|Média|Bytes/Sec lidos de um único disco durante o período de monitoramento do disco DO S|VMName|
|Bytes de gravação de disco do SISTEMA OPERACIONAL/seg|Bytes de Gravação do Disco do SO/s (Visualização)|CountPerSecond|Média|Bytes/Sec gravado em um único disco durante o período de monitoramento do disco DO S|VMName|
|Operações de leitura de disco do SISTEMA OPERACIONAL/Seg|Operações de Leitura do Disco do SO/s (Visualização)|CountPerSecond|Média|Leia IOPS de um único disco durante o período de monitoramento do disco do Sistema Operacional|VMName|
|Operações de gravação de disco do SISTEMA OPERACIONAL/Seg|Operações de Gravação do Disco do SO/s (Visualização)|CountPerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitoramento do disco DO S|VMName|
|Profundidade da Fila de Disco do SO|Profundidade da fila do disco do SISTEMA OPERACIONAL (Visualização)|Contagem|Média|Profundidade da Fila do Disco do SO (ou Tamanho da Fila)|VMName|
|Fluxos de Entrada|Fluxos de Entrada|Contagem|Média|Fluxos de entrada são o número de fluxos de corrente na direção de entrada (tráfego indo para a VM)|VMName|
|Fluxos de saída|Fluxos de saída|Contagem|Média|Fluxos de saída são o número de fluxos de corrente na direção de saída (tráfego saindo da VM)|VMName|
|Taxa de criação máxima dos fluxos de entrada|Taxa de criação máxima dos fluxos de entrada|CountPerSecond|Média|A taxa máxima de criação de fluxos de entrada (tráfego indo para a VM)|VMName|
|Taxa de criação máxima dos fluxos de saída|Taxa de criação máxima dos fluxos de saída|CountPerSecond|Média|A taxa máxima de criação de fluxos de saída (tráfego saindo da VM)|VMName|
|Impacto de leitura de leitura de armazenamento de disco de dados premium|Impacto de leitura de leitura de armazenamento de dados premium (visualização)|Porcentagem|Média|Impacto de leitura de leitura de armazenamento de disco de dados premium|LUN, VMName|
|Cache de disco de dados premium Leia Miss|Cache de disco de dados premium Ler falta (visualização)|Porcentagem|Média|Cache de disco de dados premium Leia Miss|LUN, VMName|
|Impacto de leitura de leitura de cache do sistema operacional premium|A cessação de leitura de leitura do cache do sistema operacional premium (visualização)|Porcentagem|Média|Impacto de leitura de leitura de cache do sistema operacional premium|VMName|
|Cache de disco premium do SISTEMA OPERACIONAL Ler Miss|Cache de disco premium do Sistema Operacional Read Miss (Visualização)|Porcentagem|Média|Cache de disco premium do SISTEMA OPERACIONAL Ler Miss|VMName|
|Rede no total|Rede no total|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela máquina virtual ou pelas máquinas virtuais (tráfego de entrada)|VMName|
|Rede Out Total|Rede Out Total|Bytes|Total|O número de bytes de saída em todas as interfaces de rede pela máquina virtual ou pelas máquinas virtuais (tráfego de saída)|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Porcentagem de CPU|Porcentagem de CPU|Porcentagem|Média|A porcentagem das unidades de computação alocadas que estão atualmente em uso pela máquina virtual ou pelas máquinas virtuais|Nenhum|
|Entrada na rede|Rede Em Billable (Depreciada)|Bytes|Total|O número de bytes faturados recebidos em todas as interfaces de rede pela Máquina Virtual (Tráfego de Entrada) (Depreciado)|Nenhum|
|Saída da rede|Rede Out Billable (Depreciada)|Bytes|Total|O número de bytes faturados em todas as interfaces de rede pela Virtual Machine(s) (Outgoing Traffic) (Depreciada)|Nenhum|
|Bytes de leitura de disco|Bytes de leitura de disco|Bytes|Total|Bytes lidos do disco durante o período de monitoramento|Nenhum|
|Bytes de gravação de disco|Bytes de gravação de disco|Bytes|Total|Bytes gravados em disco durante o período de monitoramento|Nenhum|
|Operações de leitura de disco/Seg|Operações de leitura de disco/Seg|CountPerSecond|Média|IOPS de leitura de disco|Nenhum|
|Operações de gravação de disco/Seg|Operações de gravação de disco/Seg|CountPerSecond|Média|IOPS de gravação de disco|Nenhum|
|Créditos de CPU Restantes|Créditos de CPU Restantes|Contagem|Média|Número total de créditos disponíveis para intermitência|Nenhum|
|Créditos de CPU Consumidos|Créditos de CPU Consumidos|Contagem|Média|Número total de créditos consumido pela Máquina Virtual|Nenhum|
|Por Bytes de Leitura do Disco/s|Leitura de disco de dados Bytes/Seg [(preterido)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Bytes/Seg lidos a partir de um único disco durante o período de monitoramento|SlotId|
|Por Bytes de Gravação de Disco/s|Bytes de gravação de disco de dados/seg [(preteridos)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Bytes/Seg gravado em um único disco durante o período de monitoramento|SlotId|
|Por operações de Leitura de Disco/s|Operações de leitura de disco de dados/seg [(preterido)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Leia IOPS de um único disco durante o período de monitoramento|SlotId|
|Por Operações de Gravação de Disco/s|Operações de gravação de disco de dados/seg [(preterido)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitoramento|SlotId|
|Por QD de Disco|QD do disco [de dados (preterido)](portal-disk-metrics-deprecation.md)|Contagem|Média|Profundidade da Fila do Disco de Dados (ou Tamanho da Fila)|SlotId|
|SO Por Bytes de Leitura do Disco/s|Leitura de disco do SISTEMA OPERACIONAL Bytes/Seg [(preterida)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Bytes/Sec lidos de um único disco durante o período de monitoramento do disco DO S|Nenhum|
|SO Por Bytes de Gravação do Disco/s|Bytes de gravação de disco do SISTEMA OPERACIONAL/Seg [(preteridos)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Bytes/Sec gravado em um único disco durante o período de monitoramento do disco DO S|Nenhum|
|SO Por Operações de Leitura do Disco/s|Operações de leitura de disco do SISTEMA OPERACIONAL/Seg [(preteridas)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Leia IOPS de um único disco durante o período de monitoramento do disco do Sistema Operacional|Nenhum|
|SO Por Operações de Gravação do Disco/s|Operações de gravação de disco do SISTEMA OPERACIONAL/Seg [(preteridas)](portal-disk-metrics-deprecation.md)|CountPerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitoramento do disco DO S|Nenhum|
|Os Por QD de Disco|Disco do SISTEMA OPERACIONAL [QD (Preterido)](portal-disk-metrics-deprecation.md)|Contagem|Média|Profundidade da Fila do Disco do SO (ou Tamanho da Fila)|Nenhum|
|Leitura de disco de dados Bytes/seg|Bytes de Leitura do Disco de Dados/s (Visualização)|CountPerSecond|Média|Bytes/Seg lidos a partir de um único disco durante o período de monitoramento|LUN|
|Bytes de gravação de disco de dados/seg|Bytes de Gravação do Disco de Dados/s (Visualização)|CountPerSecond|Média|Bytes/Seg gravado em um único disco durante o período de monitoramento|LUN|
|Operações de leitura de disco de dados/seg|Operações de Leitura do Disco de Dados/s (Visualização)|CountPerSecond|Média|Leia IOPS de um único disco durante o período de monitoramento|LUN|
|Operações de gravação de disco de dados/seg|Operações de Gravação do Disco de Dados/s (Visualização)|CountPerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitoramento|LUN|
|Profundidade da Fila de Disco de Dados|Profundidade da fila de discos de dados (visualização)|Contagem|Média|Profundidade da Fila do Disco de Dados (ou Tamanho da Fila)|LUN|
|Leitura de disco do SISTEMA OPERACIONAL Bytes/seg|Bytes de Leitura do Disco do SO/s (Visualização)|CountPerSecond|Média|Bytes/Sec lidos de um único disco durante o período de monitoramento do disco DO S|Nenhum|
|Bytes de gravação de disco do SISTEMA OPERACIONAL/seg|Bytes de Gravação do Disco do SO/s (Visualização)|CountPerSecond|Média|Bytes/Sec gravado em um único disco durante o período de monitoramento do disco DO S|Nenhum|
|Operações de leitura de disco do SISTEMA OPERACIONAL/Seg|Operações de Leitura do Disco do SO/s (Visualização)|CountPerSecond|Média|Leia IOPS de um único disco durante o período de monitoramento do disco do Sistema Operacional|Nenhum|
|Operações de gravação de disco do SISTEMA OPERACIONAL/Seg|Operações de Gravação do Disco do SO/s (Visualização)|CountPerSecond|Média|Escreva IOPS a partir de um único disco durante o período de monitoramento do disco DO S|Nenhum|
|Profundidade da Fila de Disco do SO|Profundidade da fila do disco do SISTEMA OPERACIONAL (Visualização)|Contagem|Média|Profundidade da Fila do Disco do SO (ou Tamanho da Fila)|Nenhum|
|Fluxos de Entrada|Fluxos de Entrada|Contagem|Média|Fluxos de entrada são o número de fluxos de corrente na direção de entrada (tráfego indo para a VM)|Nenhum|
|Fluxos de saída|Fluxos de saída|Contagem|Média|Fluxos de saída são o número de fluxos de corrente na direção de saída (tráfego saindo da VM)|Nenhum|
|Taxa de criação máxima dos fluxos de entrada|Taxa de criação máxima dos fluxos de entrada|CountPerSecond|Média|A taxa máxima de criação de fluxos de entrada (tráfego indo para a VM)|Nenhum|
|Taxa de criação máxima dos fluxos de saída|Taxa de criação máxima dos fluxos de saída|CountPerSecond|Média|A taxa máxima de criação de fluxos de saída (tráfego saindo da VM)|Nenhum|
|Impacto de leitura de leitura de armazenamento de disco de dados premium|Impacto de leitura de leitura de armazenamento de dados premium (visualização)|Porcentagem|Média|Impacto de leitura de leitura de armazenamento de disco de dados premium|LUN|
|Cache de disco de dados premium Leia Miss|Cache de disco de dados premium Ler falta (visualização)|Porcentagem|Média|Cache de disco de dados premium Leia Miss|LUN|
|Impacto de leitura de leitura de cache do sistema operacional premium|A cessação de leitura de leitura do cache do sistema operacional premium (visualização)|Porcentagem|Média|Impacto de leitura de leitura de cache do sistema operacional premium|Nenhum|
|Cache de disco premium do SISTEMA OPERACIONAL Ler Miss|Cache de disco premium do Sistema Operacional Read Miss (Visualização)|Porcentagem|Média|Cache de disco premium do SISTEMA OPERACIONAL Ler Miss|Nenhum|
|Rede no total|Rede no total|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela máquina virtual ou pelas máquinas virtuais (tráfego de entrada)|Nenhum|
|Rede Out Total|Rede Out Total|Bytes|Total|O número de bytes de saída em todas as interfaces de rede pela máquina virtual ou pelas máquinas virtuais (tráfego de saída)|Nenhum|

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
|TotalPullCount|Contagem total de puxadas|Contagem|Média|Número de puxações de imagem no total|Nenhum|
|SucessoPullCount|Contagem de tração bem sucedida|Contagem|Média|Número de puxadores de imagem bem sucedidos|Nenhum|
|TotalPushCount|Contagem total de push|Contagem|Média|Número de empurrões de imagem no total|Nenhum|
|SucessopushCount|Contagem de push bem sucedida|Contagem|Média|Número de empurrões de imagem bem-sucedidos|Nenhum|
|Duração do execução|Duração da execução|Milissegundos|Total|Duração da execução em milissegundos|Nenhum|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Número total de núcleos de cpu disponíveis em um cluster gerenciado|Contagem|Média|Número total de núcleos de cpu disponíveis em um cluster gerenciado|Nenhum|
|kube_node_status_allocatable_memory_bytes|Número total de memória disponível em um cluster gerenciado|Bytes|Média|Número total de memória disponível em um cluster gerenciado|Nenhum|
|kube_pod_status_ready|Número de compartimentos no estado Pronto|Contagem|Média|Número de compartimentos no estado Pronto|namespace,pod|
|kube_node_status_condition|Status para as várias condições de nó|Contagem|Média|Status para as várias condições de nó|condição,status,status2,nó|
|kube_pod_status_phase|Número de pods por fase|Contagem|Média|Número de pods por fase|fase,namespace,pod|



## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft.CustomProviders/resourceproviders

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Solicitações bem-sucedidas|Solicitações bem sucedidas|Contagem|Total|Solicitações bem sucedidas feitas pelo provedor personalizado|httpmethod,callpath,statuscode|
|FailedRequests|Solicitações com falha|Contagem|Total|Obtém os logs disponíveis para provedores de recursos personalizados|httpmethod,callpath,statuscode|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDispositivos

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|NICReadThroughput|Ler throughput (rede)|BytesPerSecond|Média|O throughput de leitura da interface de rede no dispositivo no período de emissão de relatórios para todos os volumes no gateway.|InstanceName|
|NICWriteThroughput|Gravar throughput (rede)|BytesPerSecond|Média|O throughput de gravação da interface de rede no dispositivo no período de emissão de relatórios para todos os volumes no gateway.|InstanceName|
|CloudReadthroughputPerShare|Throughput de download na nuvem (Compartilhar)|BytesPerSecond|Média|O throughput de download para o Azure a partir de uma ação durante o período de relatórios.|Compartilhar|
|CloudUploadthroughputperShare|Throughput de upload de nuvem (Compartilhar)|BytesPerSecond|Média|O throughput de upload para o Azure a partir de uma ação durante o período de relatórios.|Compartilhar|
|BytesUploadedToCloudPerShare|Cloud Bytes Carregado (Compartilhar)|Bytes|Média|O número total de bytes que é carregado para o Azure a partir de uma ação durante o período de relatórios.|Compartilhar|
|Capacidade Total|Capacidade total|Bytes|Média|Capacidade total|Nenhum|
|Capacidade disponível|Capacidade Disponível|Bytes|Média|A capacidade disponível em bytes durante o período de relatórios.|Nenhum|
|CloudUploadThroughput|Throughput de upload de nuvem|BytesPerSecond|Média|O throughput de upload de nuvem para o Azure durante o período de relatórios.|Nenhum|
|CloudReadThroughput|Throughput de download na nuvem|BytesPerSecond|Média|O throughput de download na nuvem para o Azure durante o período de relatórios.|Nenhum|
|BytesUploadedToCloud|Cloud Bytes Carregado (Dispositivo)|Bytes|Média|O número total de bytes que são carregados para o Azure a partir de um dispositivo durante o período de relatórios.|Nenhum|
|HyperVVirtualProcessorUtilização|Edge Compute - CPU percentual|Porcentagem|Média|Porcentagem de uso da CPU|InstanceName|
|HyperVMemoryUtilização|Edge Compute - Uso da memória|Porcentagem|Média|Quantidade de RAM em uso|InstanceName|


## <a name="microsoftdatacatalogdatacatalogs"></a>Microsoft.DataCatalog/datacatalogs

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Alocação de ativosPor classificação|Distribuição de ativos por classificação|Contagem|Total|Indica o número de ativos com uma determinada classificação atribuída, ou seja, eles são classificados com esse rótulo.|Classificação,Fonte|
|AssetDistributionByStorageType|Distribuição de ativos por tipo de armazenamento|Contagem|Total|Indica o número de ativos com um determinado tipo de armazenamento.|StorageType|
|NúmeroOfAssetsComClassificações|Número de ativos com pelo menos uma classificação|Contagem|Média|Indica o número de ativos com pelo menos uma classificação de tag.|Nenhum|
|ScanCancelado|Scan cancelado|Contagem|Total|Indica o número de exames cancelados.|Nenhum|
|ScanConcluído|Varredura concluída|Contagem|Total|Indica o número de varreduras concluídas com sucesso.|Nenhum|
|ScanFailed|Falha na varredura|Contagem|Total|Indica que o número de exames falhou.|Nenhum|
|ScanTimeTaken|Escaneie o tempo tomado|Segundos|Total|Indica o tempo total de varredura em segundos.|Nenhum|
|CatalogActiveUsers|Usuários ativos diários|Contagem|Total|Número de usuários ativos diariamente|Nenhum|
|CatálogoUso|Distribuição de uso por operação|Contagem|Total|Indique o número de operações que o usuário faz ao catálogo, ou seja, Acesso, Pesquisa, Glossário.|Operação|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|FailedRuns|Execuções com falha|Contagem|Total||pipelineNome,activityName|
|SuccessfulRuns|Execuções com êxito|Contagem|Total||pipelineNome,activityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PipelineFailedRuns|Métricas de execução do pipeline com falha|Contagem|Total||Tipo de falha,nome|
|PipelineSucceededRuns|Métricas de execução do pipeline bem-sucedido|Contagem|Total||Tipo de falha,nome|
|PipelineCanceledRuns|Pipeline cancelado executa métricas|Contagem|Total||Tipo de falha,nome|
|ActivityFailedRuns|Métricas de execução de atividades com falha|Contagem|Total||tipo de atividade,pipelineName,Type de falha,nome|
|ActivitySucceededRuns|Métricas de execução de atividades bem-sucedidas|Contagem|Total||tipo de atividade,pipelineName,Type de falha,nome|
|ActivityCancelledRuns|Atividade cancelada executa métricas|Contagem|Total||tipo de atividade,pipelineName,Type de falha,nome|
|TriggerFailedRuns|Métricas de execuções do gatilho com falha|Contagem|Total||Nome,Tipo de falha|
|TriggerSucceededRuns|Métricas de execuções do gatilho bem-sucedidas|Contagem|Total||Nome,Tipo de falha|
|TriggerCanceledRuns|Gatilho cancelado executa métricas|Contagem|Total||Nome,Tipo de falha|
|IntegrationRuntimeCpuPercentage|Utilização de CPU de runtime de integração|Porcentagem|Média||IntegraçãoRuntimeName,NodeName|
|IntegrationRuntimeAvailableMemory|Memória disponível de runtime de integração|Bytes|Média||IntegraçãoRuntimeName,NodeName|
|IntegraçãoExecutartempoAverageTaskPickupAtraso|Duração da fila de tempo de execução de integração|Segundos|Média||IntegraçãoRuntimeName|
|IntegraçãoRuntimeQueueLength|Comprimento da fila de tempo de execução de integração|Contagem|Média||IntegraçãoRuntimeName|
|IntegraçãoExecutartempoDisponívelNúmero de números|Contagem de nó disponível em tempo de execução de integração|Contagem|Média||IntegraçãoRuntimeName|
|Contagem de recursos máximas permitidas|Contagem máxima de entidades permitidas|Contagem|Máximo||Nenhum|
|MaxallowedfactorySizeingbunits|Tamanho máximo permitido de fábrica (unidade GB)|Contagem|Máximo||Nenhum|
|Contagem de recursos|Contagem total de entidades|Contagem|Máximo||Nenhum|
|FactorySizeInGbUnits|Tamanho total da fábrica (unidade GB)|Contagem|Máximo||Nenhum|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|JobEndedSuccess|Trabalhos com êxito|Contagem|Total|Contagem de trabalhos com êxito.|Nenhum|
|JobEndedFailure|Trabalhos com falha|Contagem|Total|Contagem de trabalhos com falha.|Nenhum|
|JobEndedCancelled|Trabalhos cancelados|Contagem|Total|Contagem de trabalhos cancelados.|Nenhum|
|JobAUEndedSuccess|Tempo de AU com êxito|Segundos|Total|Tempo total de AU dos trabalhos com êxito.|Nenhum|
|JobAUEndedFailure|Tempo de AU com falha|Segundos|Total|Tempo total de AU dos trabalhos com falha.|Nenhum|
|JobAUEndedCancelled|Tempo de AU cancelada|Segundos|Total|Tempo total de AU dos trabalhos cancelados.|Nenhum|
|JobStage|Vagas em Estágio|Contagem|Total|Número de empregos em cada etapa.|Nenhum|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TotalStorage|Armazenamento total|Bytes|Máximo|Quantidade total de dados armazenados na conta.|Nenhum|
|DataWritten|Dados gravados|Bytes|Total|Quantidade total de dados gravados na conta.|Nenhum|
|DataRead|Leitura de dados|Bytes|Total|Quantidade total de dados lidos na conta.|Nenhum|
|WriteRequests|Solicitações de gravação|Contagem|Total|Contagem de solicitações de gravação de dados para a conta.|Nenhum|
|ReadRequests|Solicitações de leitura|Contagem|Total|Contagem solicitações de leitura de dados para a conta.|Nenhum|


## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/contas

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ShareCount|Ações enviadas|Contagem|Máximo|Número de ações enviadas na conta|Nome_do_compartilhamento|
|ShareSubscriptionCount|Ações Recebidas|Contagem|Máximo|Número de ações recebidas na conta|Compartilharnome de assinatura|
|Sincronizações de compartilhamento de compartilhamento bem-sucedidas|Instantâneos com sucesso de compartilhamento enviado|Contagem|Contagem|Número de compartilhamentoenviado teve sucesso instantâneos na conta|Nenhum|
|Sincronizações com compartilhamento de compartilhamento de falhas|Fotos falhadas do compartilhamento enviado|Contagem|Contagem|Número de instantâneos de compartilhamento enviados falhados na conta|Nenhum|
|Sucedeuassincronizações de assinaturas de compartilhamento|Instantâneos de compartilhamento recebidos|Contagem|Contagem|Número de compartilhamento recebido teve sucesso instantâneos na conta|Nenhum|
|FalhacompartilhamentodeassinaturasSincronizações sincronizadas|Instantâneos com falha de compartilhamento recebidos|Contagem|Contagem|Número de instantâneos de compartilhamento recebidos falhados na conta|Nenhum|


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
|backup_storage_used|Armazenamento de backup usado|Bytes|Média|Armazenamento de backup usado|Nenhum|
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
|backup_storage_used|Armazenamento de backup usado|Bytes|Média|Armazenamento de backup usado|Nenhum|
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
|backup_storage_used|Armazenamento de backup usado|Bytes|Média|Armazenamento de backup usado|Nenhum|
|network_bytes_egress|Saída da rede|Bytes|Total|Saída de Rede em conexões ativas|Nenhum|
|network_bytes_ingress|Entrada na rede|Bytes|Total|Entrada de Rede em conexões ativas|Nenhum|
|pg_replica_log_delay_in_seconds|Retardo da Réplica|Segundos|Máximo|Atraso de réplica em segundos|Nenhum|
|pg_replica_log_delay_in_bytes|Retardo Máximo entre Réplicas|Bytes|Máximo|Defasagem em bytes da réplica mais atrasada|Nenhum|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Porcentagem de CPU|Porcentagem|Média|Porcentagem de CPU|Nenhum|
|memory_percent|Porcentagem de memória|Porcentagem|Média|Porcentagem de memória|Nenhum|
|Iops|IOPS|Contagem|Média|Operações de IO por segundo|Nenhum|
|storage_percent|Porcentagem de armazenamento|Porcentagem|Média|Porcentagem de armazenamento|Nenhum|
|storage_used|Armazenamento usado|Bytes|Média|Armazenamento usado|Nenhum|
|active_connections|Conexões ativas|Contagem|Média|Conexões ativas|Nenhum|
|network_bytes_egress|Saída da rede|Bytes|Total|Saída de Rede em conexões ativas|Nenhum|
|network_bytes_ingress|Entrada na rede|Bytes|Total|Entrada de Rede em conexões ativas|Nenhum|


## <a name="microsoftdbforpostgresqlsingleservers"></a>Microsoft.DBforPostgreSQL/servidores únicos

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Porcentagem de CPU|Porcentagem|Média|Porcentagem de CPU|Nenhum|
|memory_percent|Porcentagem de memória|Porcentagem|Média|Porcentagem de memória|Nenhum|
|Iops|IOPS|Contagem|Média|Operações de IO por segundo|Nenhum|
|storage_percent|Porcentagem de armazenamento|Porcentagem|Média|Porcentagem de armazenamento|Nenhum|
|storage_used|Armazenamento usado|Bytes|Média|Armazenamento usado|Nenhum|
|active_connections|Conexões ativas|Contagem|Média|Conexões ativas|Nenhum|
|network_bytes_egress|Saída da rede|Bytes|Total|Saída de Rede em conexões ativas|Nenhum|
|network_bytes_ingress|Entrada na rede|Bytes|Total|Entrada de Rede em conexões ativas|Nenhum|
|connections_failed|Conexões com falha|Contagem|Total|Conexões com falha|Nenhum|
|connections_succeeded|Conexões bem sucedidas|Contagem|Total|Conexões bem sucedidas|Nenhum|
|maximum_used_transactionIDs|IDs de transação usados máximos|Contagem|Média|IDs de transação usados máximos|Nenhum|





## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Tentativas de envio de mensagem de telemetria|Contagem|Total|Número de tentativas de envio de mensagens de telemetria do dispositivo para nuvem para o hub IoT|Nenhum|
|d2c.telemetry.ingress.success|Mensagens de telemetria enviadas|Contagem|Total|Número de mensagens de telemetria do dispositivo para nuvem enviadas com êxito para o hub IoT|Nenhum|
|c2d.commands.egress.complete.success|Entregas de mensagens C2D concluídas|Contagem|Total|Número de entregas de mensagens nuvem-dispositivo concluídas com sucesso pelo dispositivo|Nenhum|
|c2d.commands.egress.abandon.success|Mensagens C2D abandonadas|Contagem|Total|Número de mensagens nuvem-dispositivo abandonadas pelo dispositivo|Nenhum|
|c2d.commands.egress.reject.success|Mensagens C2D rejeitadas|Contagem|Total|Número de mensagens nuvem-dispositivo rejeitadas pelo dispositivo|Nenhum|
|C2DMessagesExpirado|Mensagens C2D expiradas (visualização)|Contagem|Total|Número de mensagens de nuvem para dispositivo expiradas|Nenhum|
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
|EventGridEntregas|Entregas da Event Grid (pré-visualização)|Contagem|Total|O número de eventos do IoT Hub publicados no Event Grid. Use a dimensão Resultado para o número de solicitações bem sucedidas e com falha. A dimensão EventType mostrahttps://aka.ms/ioteventgrid)o tipo de evento ( .|ResourceId,Resultado,EventType|
|EventGridLatency|Latência do Grid de Eventos (visualização)|Milissegundos|Média|A latência média (milissegundos) de quando o evento Iot Hub foi gerado até quando o evento foi publicado para event Grid. Este número é uma média entre todos os tipos de eventos. Use a dimensão EventType para ver a latência de um tipo específico de evento.|ResourceId,EventType|
|Entregas de roteamento|Entregas de roteamento (visualização)|Milissegundos|Total|O número de vezes que o IoT Hub tentou entregar mensagens para todos os pontos finais usando roteamento. Para ver o número de tentativas bem sucedidas ou fracassadas, use a dimensão Resultado. Para ver a razão do fracasso, como inválido, descartado ou órfão, use a dimensão FailureReasonCategory. Você também pode usar as dimensões EndpointName e EndpointType para entender quantas mensagens foram entregues em seus diferentes pontos finais. O valor métrico aumenta em um para cada tentativa de entrega, incluindo se a mensagem for entregue em vários pontos finais ou se a mensagem for entregue no mesmo ponto final várias vezes.|RecursoId,EndpointType,EndpointName,FailureReasonCategory,Resultado,RoteamentoFonte|
|RoteamentoEntrega-de-tempo|Latência de entrega de roteamento (visualização)|Milissegundos|Média|A latência média (milissegundos) entre a ingestão de mensagens para IoT Hub e a inserção de mensagens de telemetria em um ponto final. Você pode usar as dimensões EndpointName e EndpointType para entender a latência de seus diferentes pontos finais.|RecursoId,EndpointType,EndpointName,RrotamentoFonte|
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
|RegistrationAttempts|Tentativas de Registro|Contagem|Total|Número de tentativas de registros do dispositivo|ProvisionandoNome de serviço,IotHubName,Status|
|DeviceAssignments|Dispositivos atribuídos|Contagem|Total|Número de dispositivos atribuídos a um hub IoT|ProvisionandoNome de Serviço, IotHubName|
|AttestationAttempts|Tentativas de atestado|Contagem|Total|Número de tentativas de atestado do dispositivo|ProvisionamentoNome de serviço,status,protocolo|




## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|AddRegion|Região Adicionada|Contagem|Contagem|Região Adicionada|Região|
|AvailableStorage|Armazenamento disponível|Bytes|Total|Armazenamento total disponível relatado em 5 minutos de granularidade|Nome da coleção,nome do banco de dados,região|
|CassandraConnectionClosures|Fechamentos de Conexão do Cassandra|Contagem|Total|Número de conexões Cassandra que foram fechadas, relatadas em uma granularidade de 1 minuto|APIType,região,encerramentorazão|
|CassandraKeyspaceDelete|Cassandra Keyspace Excluída|Contagem|Contagem|Cassandra Keyspace Excluída|Nome de recursos,ApiKind,ApiKindResourceType,OperationType|
|CassandraKeyspaceThroughputUpdate|Cassandra Keyspace Throughput Atualizado|Contagem|Contagem|Cassandra Keyspace Throughput Atualizado|Nome de recursos,ApiKind,ApiKindResourceType,IsThroughputRequest|
|CassandraKeyspaceUpdate|Cassandra Keyspace Atualizado|Contagem|Contagem|Cassandra Keyspace Atualizado|Nome de recursos,ApiKind,ApiKindResourceType,IsThroughputRequest|
|CassandraRequestCharges|Encargos de Solicitação do Cassandra|Contagem|Total|RUs consumidos para pedidos de Cassandra feitos|APIType,Nome de banco de dados,nome de coleta,região,tipo de operação,tipo de recurso|
|CassandraRequests|Solicitações do Cassandra|Contagem|Contagem|Número de pedidos de Cassandra feitos|APIType,Nome de banco de dados,nome de coleta,região,tipo de operação,tipo de recurso,código de erro|
|CassandraTableDelete|Tabela de Cassandra excluída|Contagem|Contagem|Tabela de Cassandra excluída|Nome de recursos,ChildResourceName,ApiKind,ApiKindResourceType,OperationType|
|CassandraTableThroughputUpdate|Cassandra Table Throughput Atualizado|Contagem|Contagem|Cassandra Table Throughput Atualizado|Nome de recurso,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|CassandraTableUpdate|Tabela Cassandra atualizada|Contagem|Contagem|Tabela Cassandra atualizada|Nome de recurso,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|CriarConta|Conta criada|Contagem|Contagem|Conta criada|Nenhum|
|DataUsage|Uso de dados|Bytes|Total|Uso total de dados relatado em 5 minutos de granularidade|Nome da coleção,nome do banco de dados,região|
|ExcluirConta|Conta excluída|Contagem|Contagem|Conta excluída|Nenhum|
|DocumentCount|Contagem de documentos|Contagem|Total|Contagem total de documentos relatada em 5 minutos de granularidade|Nome da coleção,nome do banco de dados,região|
|DocumentQuota|Cota de documento|Bytes|Total|Cota total de armazenamento relatada em 5 minutos de granularidade|Nome da coleção,nome do banco de dados,região|
|GremlinDatabaseDelete|Banco de dados Gremlin excluído|Contagem|Contagem|Banco de dados Gremlin excluído|Nome de recursos,ApiKind,ApiKindResourceType,OperationType|
|GremlinDatabaseThroughputUpdate|Throughput do banco de dados Gremlin atualizado|Contagem|Contagem|Throughput do banco de dados Gremlin atualizado|Nome de recursos,ApiKind,ApiKindResourceType,IsThroughputRequest|
|GremlinDatabaseUpdate|Banco de dados Gremlin atualizado|Contagem|Contagem|Banco de dados Gremlin atualizado|Nome de recursos,ApiKind,ApiKindResourceType,IsThroughputRequest|
|GremlinGraphDelete|Gráfico de Gremlin excluído|Contagem|Contagem|Gráfico de Gremlin excluído|Nome de recursos,ChildResourceName,ApiKind,ApiKindResourceType,OperationType|
|GremlinGraphThroughputUpdate|Throughput do gráfico gremlin atualizado|Contagem|Contagem|Throughput do gráfico gremlin atualizado|Nome de recurso,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|GremlinGraphUpdate|Gráfico gremlin atualizado|Contagem|Contagem|Gráfico gremlin atualizado|Nome de recurso,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|IndexUsage|Uso do índice|Bytes|Total|Uso total do índice relatado em 5 minutos de granularidade|Nome da coleção,nome do banco de dados,região|
|MetadataRequests|MetadataRequests|Contagem|Contagem|Contagem de solicitações de metadados. O Cosmos DB mantém uma coleção de metadados do sistema para cada conta, o que permite que você enumere coleções, bancos de dados, etc, e suas configurações, gratuitamente.|Nome do banco de dados,nome de coleta,região,código de status,função|
|MongoCollectionDelete|Coleção Mongo Excluída|Contagem|Contagem|Coleção Mongo Excluída|Nome de recursos,ChildResourceName,ApiKind,ApiKindResourceType,OperationType|
|MongoCollectionThroughputUpdate|Throughput da coleção Mongo atualizado|Contagem|Contagem|Throughput da coleção Mongo atualizado|Nome de recurso,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|MongoCollectionUpdate|Coleção Mongo Atualizada|Contagem|Contagem|Coleção Mongo Atualizada|Nome de recurso,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|MongoDBDatabaseAtualização|Banco de dados Mongo atualizado|Contagem|Contagem|Banco de dados Mongo atualizado|Nome de recursos,ApiKind,ApiKindResourceType,IsThroughputRequest|
|MongoDatabaseDelete|Banco de dados Mongo Excluído|Contagem|Contagem|Banco de dados Mongo Excluído|Nome de recursos,ApiKind,ApiKindResourceType,OperationType|
|MongoDatabaseThroughputUpdate|Throughput do banco de dados Mongo atualizado|Contagem|Contagem|Throughput do banco de dados Mongo atualizado|Nome de recursos,ApiKind,ApiKindResourceType,IsThroughputRequest|
|MongoRequestCharge|MongoRequestCharge|Contagem|Total|Unidades Solicitadas do Mongo Consumidas|Nome do banco de dados,nome de coleta,região,nome de comando,código de erro,status|
|MongoRequests|Solicitações do Mongo|Contagem|Contagem|Número de Solicitações do Mongo Feitas|Nome do banco de dados,nome de coleta,região,nome de comando,código de erro,status|
|MongoRequestsCount|Taxa de Solicitação de Mongo|CountPerSecond|Média|Mongo solicitar contagem por segundo|Nome do banco de dados,nome de coleta,região,nome de comando,código de erro|
|MongoRequestsDelete|Taxa de solicitação de exclusão de Mongo|CountPerSecond|Média|Pedido de Exclusão de Mongo por segundo|Nome do banco de dados,nome de coleta,região,nome de comando,código de erro|
|MongoRequestsInsert|Taxa de solicitação de inserção de Mongo|CountPerSecond|Média|Contagem de inserção de Mongo por segundo|Nome do banco de dados,nome de coleta,região,nome de comando,código de erro|
|MongoRequestsQuery|Taxa de solicitação de consulta de Mongo|CountPerSecond|Média|Solicitação de consulta mongo por segundo|Nome do banco de dados,nome de coleta,região,nome de comando,código de erro|
|MongoRequestsUpdate|Taxa de solicitação de atualização do Mongo|CountPerSecond|Média|Solicitação de Atualização mongo por segundo|Nome do banco de dados,nome de coleta,região,nome de comando,código de erro|
|NormalizadoRUConsumo|Consumo normalizado de RU|Porcentagem|Máximo|Percentual máximo de consumo de RU por minuto|Nome da coleção,nome do banco de dados,região|
|ProvisionedThroughput|Taxa de transferência provisionada|Contagem|Máximo|Taxa de transferência provisionada|Nome do banco de dados,Nome de coleta|
|RegionFailover|Região falhou|Contagem|Contagem|Região falhou|Nenhum|
|RemoverRegião|Região Removida|Contagem|Contagem|Região Removida|Região|
|ReplicationLatency|Latência de replicação P99|MilliSeconds|Média|Latência de replicação P99 nas regiões de origem e de destino para conta habilitada geograficamente|Região de Origem de Origem,Região alvo|
|ServidorSidelatency|Latência lateral do servidor|MilliSeconds|Média|Latência lateral do servidor|Nome do banco de dados,nome de coleta,região,modo de conexão,tipo de operação,PublicAPIType|
|ServiceAvailability|Disponibilidade do serviço|Porcentagem|Média|Solicitações de conta disponibilidade em uma hora, dia ou mês granularidade|Nenhum|
|SqlContainerDelete|Contêiner Sql excluído|Contagem|Contagem|Contêiner Sql excluído|Nome de recursos,ChildResourceName,ApiKind,ApiKindResourceType,OperationType|
|SqlContainerThroughputUpdate|Throughput do contêiner Sql atualizado|Contagem|Contagem|Throughput do contêiner Sql atualizado|Nome de recurso,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|SqlContainerUpdate|Contêiner Sql atualizado|Contagem|Contagem|Contêiner Sql atualizado|Nome de recurso,ChildResourceName,ApiKind,ApiKindResourceType,IsThroughputRequest|
|SqlDatabaseDelete|Banco de dados Sql Excluído|Contagem|Contagem|Banco de dados Sql Excluído|Nome de recursos,ApiKind,ApiKindResourceType,OperationType|
|SqlDatabaseThroughputUpdate|Throughput do banco de dados Sql atualizado|Contagem|Contagem|Throughput do banco de dados Sql atualizado|Nome de recursos,ApiKind,ApiKindResourceType,IsThroughputRequest|
|SqlDatabaseUpdate|Banco de dados Sql atualizado|Contagem|Contagem|Banco de dados Sql atualizado|Nome de recursos,ApiKind,ApiKindResourceType,IsThroughputRequest|
|TableTableDelete|Tabela AzureTable excluída|Contagem|Contagem|Tabela AzureTable excluída|Nome de recursos,ApiKind,ApiKindResourceType,OperationType|
|TableTableThroughputUpdate|Throughput da tabela azuretable atualizado|Contagem|Contagem|Throughput da tabela azuretable atualizado|Nome de recursos,ApiKind,ApiKindResourceType,IsThroughputRequest|
|TableTableUpdate|Tabela AzureTable atualizada|Contagem|Contagem|Tabela AzureTable atualizada|Nome de recursos,ApiKind,ApiKindResourceType,IsThroughputRequest|
|TotalRequestUnits|Total de Unidades Solicitadas|Contagem|Total|Unidades Solicitadas Consumidas|Nome do banco de dados,nome de coleta,região,statusCódigo,tipo de operação,status|
|TotalRequests|Total de Solicitações|Contagem|Contagem|Número de solicitações feitas|Nome do banco de dados,nome de coleta,região,statusCódigo,tipo de operação,status|
|UpdateAccountKeys|Chaves da conta atualizadas|Contagem|Contagem|Chaves da conta atualizadas|KeyType|
|AtualizaçãoContasRedes de rede|Configurações da rede de contas atualizadas|Contagem|Contagem|Configurações da rede de contas atualizadas|Nenhum|
|AtualizarConfigurações de replicação de contas|Configurações de replicação da conta atualizadas|Contagem|Contagem|Configurações de replicação da conta atualizadas|Nenhum|
|AtualizaçãoDiagnósticoConfigurações|Configurações de diagnóstico da conta atualizadas|Contagem|Contagem|Configurações de diagnóstico da conta atualizadas|Configurações de diagnósticoNome,ResourceGroupName|



## <a name="microsoftenterpriseknowledgegraphservices"></a>Microsoft.EnterpriseKnowledgeGraph/services

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Contagem de transações|Contagem de transações|Contagem|Contagem|Contagem total de transações|Contagem de transações|
|Contagem de sucessos|Contagem de Êxito|Contagem|Contagem|Contagem de transações bem sucedidas|Contagem de sucessos|
|Contagem de falhas|Contagem de Falha|Contagem|Contagem|Contagem de transações com falha|Contagem de falhas|
|SuccessLatency|Latência de sucesso|MilliSeconds|Média|Latência de transações bem sucedidas|Contagem de sucessos|

## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domínios

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventos publicados|Contagem|Total|Total de eventos publicados neste tópico|Tópico|
|PublishFailCount|Publicar eventos com falha|Contagem|Total|Total de eventos com falha ao publicar neste tópico|Tópico,ErroTipo,Erro|
|PublicarSucessoLatencyInms|Publicar latência de sucesso|Milissegundos|Total|Publicar latência de sucesso em milissegundos|Nenhum|
|MatchedEventCount|Eventos correspondentes|Contagem|Total|Total de eventos correspondentes a essa assinatura de evento|Tópico,Nome de assinatura de eventos,DomínioNome de registro de eventos|
|DeliveryAttemptFailCount|Eventos com falha de entrega|Contagem|Total|Total de eventos não entregues a essa assinatura de evento|Tópico,Nome de assinatura de eventos,domínioNome de marcaçãodeeventos,erro,erro|
|DeliverySuccessCount|Eventos entregues|Contagem|Total|Total de eventos entregues a essa assinatura de evento|Tópico,Nome de assinatura de eventos,DomínioNome de registro de eventos|
|DestinationProcessingDurationInMs|Duração do processamento de destino|Milissegundos|Média|Duração do processamento de destino em milissegundos|Tópico,Nome de assinatura de eventos,DomínioNome de registro de eventos|
|DroppedEventCount|Eventos removidos|Contagem|Total|Total de eventos removidos correspondentes a essa assinatura de evento|Tópico,Nome de assinatura de eventos,domínioNome de marcação de eventos,razão de abandono|
|DeadLetteredCount|Eventos de mensagens mortas|Contagem|Total|Total de eventos de mensagens mortas correspondentes a essa assinatura de evento|Tópico,Nome de assinatura de eventos,domínioNome de assinatura de eventos,DeadLetterReason|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventos publicados|Contagem|Total|Total de eventos publicados neste tópico|Nenhum|
|PublishFailCount|Publicar eventos com falha|Contagem|Total|Total de eventos com falha ao publicar neste tópico|ErroTipo,Erro|
|UnmatchedEventCount|Eventos sem correspondência|Contagem|Total|Total de eventos sem correspondência com nenhuma das assinaturas de evento para este tópico|Nenhum|
|PublicarSucessoLatencyInms|Publicar latência de sucesso|Milissegundos|Total|Publicar latência de sucesso em milissegundos|Nenhum|
|MatchedEventCount|Eventos correspondentes|Contagem|Total|Total de eventos correspondentes a essa assinatura de evento|Nome da Assinatura do Evento|
|DeliveryAttemptFailCount|Eventos com falha de entrega|Contagem|Total|Total de eventos não entregues a essa assinatura de evento|Erro,ErroTipo,Nome de assinatura de eventos|
|DeliverySuccessCount|Eventos entregues|Contagem|Total|Total de eventos entregues a essa assinatura de evento|Nome da Assinatura do Evento|
|DestinationProcessingDurationInMs|Duração do processamento de destino|Milissegundos|Média|Duração do processamento de destino em milissegundos|Nome da Assinatura do Evento|
|DroppedEventCount|Eventos removidos|Contagem|Total|Total de eventos removidos correspondentes a essa assinatura de evento|DropReason,Nome de assinatura de eventos|
|DeadLetteredCount|Eventos de mensagens mortas|Contagem|Total|Total de eventos de mensagens mortas correspondentes a essa assinatura de evento|DeadLetterReason,Nome de assinatura de eventos|

## <a name="microsofteventgridsystemtopics"></a>Microsoft.EventGrid/systemTopics

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventos publicados|Contagem|Total|Total de eventos publicados neste tópico|Nenhum|
|PublishFailCount|Publicar eventos com falha|Contagem|Total|Total de eventos com falha ao publicar neste tópico|ErroTipo,Erro|
|UnmatchedEventCount|Eventos sem correspondência|Contagem|Total|Total de eventos sem correspondência com nenhuma das assinaturas de evento para este tópico|Nenhum|
|PublicarSucessoLatencyInms|Publicar latência de sucesso|Milissegundos|Total|Publicar latência de sucesso em milissegundos|Nenhum|
|MatchedEventCount|Eventos correspondentes|Contagem|Total|Total de eventos correspondentes a essa assinatura de evento|Nome da Assinatura do Evento|
|DeliveryAttemptFailCount|Eventos com falha de entrega|Contagem|Total|Total de eventos não entregues a essa assinatura de evento|Erro,ErroTipo,Nome de assinatura de eventos|
|DeliverySuccessCount|Eventos entregues|Contagem|Total|Total de eventos entregues a essa assinatura de evento|Nome da Assinatura do Evento|
|DestinationProcessingDurationInMs|Duração do processamento de destino|Milissegundos|Média|Duração do processamento de destino em milissegundos|Nome da Assinatura do Evento|
|DroppedEventCount|Eventos removidos|Contagem|Total|Total de eventos removidos correspondentes a essa assinatura de evento|DropReason,Nome de assinatura de eventos|
|DeadLetteredCount|Eventos de mensagens mortas|Contagem|Total|Total de eventos de mensagens mortas correspondentes a essa assinatura de evento|DeadLetterReason,Nome de assinatura de eventos|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|MatchedEventCount|Eventos correspondentes|Contagem|Total|Total de eventos correspondentes a essa assinatura de evento|Nenhum|
|DeliveryAttemptFailCount|Eventos com falha de entrega|Contagem|Total|Total de eventos não entregues a essa assinatura de evento|Erro,ErroTipo|
|DeliverySuccessCount|Eventos entregues|Contagem|Total|Total de eventos entregues a essa assinatura de evento|Nenhum|
|DestinationProcessingDurationInMs|Duração do processamento de destino|Milissegundos|Média|Duração do processamento de destino em milissegundos|Nenhum|
|DroppedEventCount|Eventos removidos|Contagem|Total|Total de eventos removidos correspondentes a essa assinatura de evento|DropReason|
|DeadLetteredCount|Eventos de mensagens mortas|Contagem|Total|Total de eventos de mensagens mortas correspondentes a essa assinatura de evento|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PublishSuccessCount|Eventos publicados|Contagem|Total|Total de eventos publicados neste tópico|Nenhum|
|PublishFailCount|Publicar eventos com falha|Contagem|Total|Total de eventos com falha ao publicar neste tópico|ErroTipo,Erro|
|UnmatchedEventCount|Eventos sem correspondência|Contagem|Total|Total de eventos sem correspondência com nenhuma das assinaturas de evento para este tópico|Nenhum|
|PublicarSucessoLatencyInms|Publicar latência de sucesso|Milissegundos|Total|Publicar latência de sucesso em milissegundos|Nenhum|




## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|SuccessfulRequests|Solicitações bem sucedidas|Contagem|Total|Solicitações Bem-sucedidas para Microsoft.EventHub.|EntityName,OperationResult|
|ServerErrors|Erros do Servidor.|Contagem|Total|Erros do Servidor para o Microsoft.EventHub.|EntityName,OperationResult|
|UserErrors|Erros de Usuário.|Contagem|Total|Erros de Usuário para o Microsoft.EventHub.|EntityName,OperationResult|
|QuotaExceededErrors|Cota de Erros Excedida.|Contagem|Total|Cota de Erros Excedida para o Microsoft.EventHub.|EntityName,OperationResult|
|ThrottledRequests|Solicitações Limitadas.|Contagem|Total|Solicitações Limitadas para o Microsoft.EventHub.|EntityName,OperationResult|
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
|INREQS|Pedidos de entrada (preteridos)|Contagem|Total|Total de solicitações de envio de entrada para um namespace (Depreciado)|Nenhum|
|SUCCREQ|Solicitações bem sucedidas (depreciadas)|Contagem|Total|Total de solicitações bem sucedidas para um namespace (Depreciado)|Nenhum|
|FAILREQ|Solicitações com falha (depreciadas)|Contagem|Total|Total de solicitações de falha para um namespace (Depreciado)|Nenhum|
|SVRBSY|Erros ocupados do servidor (preteridos)|Contagem|Total|Total de erros ocupados do servidor para um namespace (Preterido)|Nenhum|
|INTERR|Erros internos do servidor (preteridos)|Contagem|Total|Total de erros internos do servidor para um namespace (Preterido)|Nenhum|
|MISCERR|Outros erros (preteridos)|Contagem|Total|Total de solicitações de falha para um namespace (Depreciado)|Nenhum|
|INMSGS|Mensagens recebidas (obsoletas) (Depreciadas)|Contagem|Total|Total de mensagens de entrada para um namespace. Essa métrica foi preterida. Por favor, use a métrica Mensagens recebidas em vez disso (depreciado)|Nenhum|
|EHINMSGS|Mensagens de Entrada (Preterida)|Contagem|Total|Total de mensagens recebidas para um namespace (Depreciado)|Nenhum|
|OUTMSGS|Mensagens de saída (obsoletas) (Depreciadas)|Contagem|Total|Total de mensagens de saída para um namespace. Essa métrica foi preterida. Por favor, use a métrica Mensagens de Saída (Depreciada)|Nenhum|
|EHOUTMSGS|Mensagens de Saída (Preterida)|Contagem|Total|Mensagens de saída totais para um namespace (Depreciada)|Nenhum|
|EHINMBS|Bytes de entrada (obsoletos) (Depreciados)|Bytes|Total|Taxa de transferência de mensagem de entrada do Hub de Eventos para um namespace. Essa métrica foi preterida. Por favor, use a métrica de bytes de entrada em vez disso (depreciado)|Nenhum|
|EHINBYTES|Bytes de entrada (Preterida)|Bytes|Total|Passe de mensagem recebida do Event Hub para um namespace (Preterido)|Nenhum|
|EHOUTMBS|Bytes de saída (obsoletos) (Depreciados)|Bytes|Total|Taxa de transferência de mensagem de saída do Hub de Eventos para um namespace. Essa métrica foi preterida. Por favor, use a métrica de bytes outgoing em vez disso (depreciado)|Nenhum|
|EHOUTBYTES|Bytes de saída (Preterida)|Bytes|Total|Verificação de mensagens de saída do Event Hub para um namespace (Preterido)|Nenhum|
|EHABL|Arquivar mensagens de atraso (depreciadas)|Contagem|Total|Mensagens de arquivamento do Event Hub em atraso para um namespace (Depreciado)|Nenhum|
|EHAMSGS|Arquivar mensagens (Depreciadas)|Contagem|Total|Event Hub arquivou mensagens em um namespace (Depreciado)|Nenhum|
|EHAMBS|Throughput de mensagem de arquivamento (Preterido)|Bytes|Total|Event Hub arquivou o throughput de mensagem em um namespace (Preterido)|Nenhum|

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
|AvailableMemory|Memória disponível|Porcentagem|Máximo|Memória disponível para o Event Hub Cluster como uma porcentagem da memória total.|Função|
|Tamanho|Tamanho de um EventHub em Bytes.|Bytes|Média|Tamanho de um EventHub em Bytes.|Função|


## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|GatewayRequests|Solicitações de gateway|Contagem|Total|Número de solicitações de gateway|HttpStatus|
|CategorizedGatewayRequests|Solicitações de gateway categorizadas|Contagem|Total|Número de solicitações de gateway por categorias (1xx/2xx/3xx/4xx/5xx)|HttpStatus|
|NumActiveWorkers|Número de trabalhadores ativos|Contagem|Máximo|Número de trabalhadores ativos|MetricName|


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
|disponibilidadeResultados/disponibilidadePorcentagem|Disponibilidade|Porcentagem|Média|Percentual de testes de disponibilidade concluídos com sucesso|disponibilidadeResultado/nome,disponibilidadeResultado/localização|
|disponibilidadeResultados/contagem|Testes de disponibilidade|Contagem|Contagem|Contagem de testes de disponibilidade|disponibilidadeResultado/nome,disponibilidadeResultado/localização,disponibilidadeResultado/sucesso|
|availabilityResults/duration|Duração do teste de disponibilidade|MilliSeconds|Média|Duração do teste de disponibilidade|disponibilidadeResultado/nome,disponibilidadeResultado/localização,disponibilidadeResultado/sucesso|
|browserTimings/networkDuration|Tempo de conexão de rede de carregamento de página|MilliSeconds|Média|Tempo entre a solicitação do usuário e a conexão de rede. Inclui a pesquisa DNS e a conexão de transporte.|Nenhum|
|browserTimings/processingDuration|Tempo de processamento do cliente|MilliSeconds|Média|Tempo entre o recebimento do último byte de um documento até que o DOM seja carregado. As solicitações assíncronas ainda podem estar sendo processadas.|Nenhum|
|browserTimings/receiveDuration|Tempo de resposta de recebimento|MilliSeconds|Média|Tempo entre o primeiro e o último byte, ou até a desconexão.|Nenhum|
|browserTimings/sendDuration|Tempo de solicitação de envio|MilliSeconds|Média|Tempo entre a conexão de rede e o recebimento do primeiro byte.|Nenhum|
|browserTimings/totalDuration|Tempo de carregamento de página do navegador|MilliSeconds|Média|Tempo de solicitação do usuário até que o DOM, as imagens, os scripts e as folhas de estilo sejam carregados.|Nenhum|
|dependencies/count|Chamadas de dependência|Contagem|Contagem|Contagem de chamadas feitas pelo aplicativo a recursos externos.|dependência/tipo,dependência/desempenhoBucket,dependência/sucesso,dependência/target,dependência/resultCode,operação/sintético,cloud/roleInstance,cloud/roleName|
|dependencies/duration|Duração da dependência|MilliSeconds|Média|Duração das chamadas feitas pelo aplicativo a recursos externos.|dependência/tipo,dependência/desempenhoBucket,dependência/sucesso,dependência/target,dependência/resultCode,operação/sintético,cloud/roleInstance,cloud/roleName|
|dependencies/failed|Falhas de chamada de dependência|Contagem|Contagem|Contagem de chamadas de dependência com falha feitas pelo aplicativo a recursos externos.|dependência/tipo,dependência/desempenhoBucket,dependência/sucesso,dependência/target,dependência/resultCode,operação/sintético,cloud/roleInstance,cloud/roleName|
|pageViews/count|Visualizações de página|Contagem|Contagem|Contagem de exibições de página.|operação/sintético,nuvem/roleName|
|pageViews/duration|Tempo de carregamento de exibição de página|MilliSeconds|Média|Tempo de carregamento de exibição de página|operação/sintético,nuvem/roleName|
|performanceCounters/requestExecutionTime|Tempo de execução de solicitação HTTP|MilliSeconds|Média|Tempo de execução da solicitação mais recente.|cloud/roleInstance|
|performanceCounters/requestsInQueue|Solicitações HTTP na fila do aplicativo|Contagem|Média|Comprimento da fila de solicitação de aplicativo.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|Taxa de solicitação HTTP|CountPerSecond|Média|Taxa de todas as solicitações para o aplicativo por segundo do ASP.NET.|cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|Taxa de exceção|CountPerSecond|Média|Contagem de exceções com e sem tratamento relatadas para o Windows, incluindo exceções do .NET e exceções não gerenciadas convertidas em exceções do .NET.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Taxa de E/S de processamento|BytesPerSecond|Média|Total de bytes por segundo lidos e gravados em arquivos, rede e dispositivos.|cloud/roleInstance|
|performanceCounters/processCpuPercentage|CPU do processo|Porcentagem|Média|O percentual de tempo decorrido que todos os threads do processo usaram o processador para executar instruções. Isso pode variar entre 0 e 100. Essa métrica indica o desempenho do processo de w3wp isoladamente.|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Tempo do processador|Porcentagem|Média|O percentual de tempo que o processador gasta em threads não ociosos.|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Memória disponível|Bytes|Média|Memória física disponível imediatamente para alocação a um processo ou para uso do sistema.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|Processar bytes particulares|Bytes|Média|Memória atribuída exclusivamente aos processos do aplicativo monitorado.|cloud/roleInstance|
|requests/duration|Tempo de resposta do servidor|MilliSeconds|Média|Tempo entre o recebimento de uma solicitação HTTP e a finalização do envio da resposta.|request/performanceBucket,request/resultCode,operation/synthetic,cloud/roleInstance,request/success,cloud/roleName|
|requests/count|Solicitações do servidor|Contagem|Contagem|Contagem de solicitações HTTP concluídas.|request/performanceBucket,request/resultCode,operation/synthetic,cloud/roleInstance,request/success,cloud/roleName|
|requests/failed|Solicitações falhas|Contagem|Contagem|Contagem de solicitações HTTP marcadas como com falha. Na maioria dos casos, essas são solicitações com um código de resposta >= 400 e diferente de 401.|request/performanceBucket,request/resultCode,request/success,operation/synthetic,cloud/roleInstance,cloud/roleName|
|solicitações/taxa|Taxa de solicitação de servidor|CountPerSecond|Média|Taxa de solicitações de servidores por segundo|request/performanceBucket,request/resultCode,operation/synthetic,cloud/roleInstance,request/success,cloud/roleName|
|exceptions/count|Exceções|Contagem|Contagem|Contagem combinada de todas as exceções não capturadas.|nuvem/roleName,nuvem/roleInstance,cliente/tipo|
|exceptions/browser|Exceções de navegador|Contagem|Contagem|Contagem de exceções não identificadas lançadas no navegador.|cliente/isServer,nuvem/roleName|
|exceptions/server|Exceções do servidor|Contagem|Contagem|Contagem de exceções não capturadas geradas no aplicativo para servidores.|cliente/isServer,nuvem/roleName,nuvem/roleInstance|
|traces/count|Rastreamentos|Contagem|Contagem|Contagem de documentos de rastreamento|trace/gravidadeNível,operação/sintético,nuvem/roleName,nuvem/roleInstance|


## <a name="microsoftiotcentraliotapps"></a>Microsoft.IoTCentral/IoTApps

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|connectedDeviceCount|Total de dispositivos conectados|Contagem|Média|Número de dispositivos conectados à Central de IoT|Nenhum|
|c2d.property.read.success|Leituras bem sucedidas da propriedade do dispositivo da IoT Central|Contagem|Total|A contagem de todas as leituras de propriedade bem sucedidas iniciadas a partir de IoT Central|Nenhum|
|c2d.property.read.failure|Leituras de propriedade do dispositivo com falha da Central de IoT|Contagem|Total|A contagem de todas as leituras de propriedade falhadas iniciadas a partir de IoT Central|Nenhum|
|d2c.property.read.success|Leituras bem sucedidas da propriedade do dispositivo a partir de dispositivos|Contagem|Total|A contagem de todas as leituras de propriedade bem sucedidas iniciadas a partir de dispositivos|Nenhum|
|d2c.property.read.failure|Leituras de propriedade do dispositivo com falha de dispositivos|Contagem|Total|A contagem de todas as leituras de propriedade falhadas iniciadas a partir de dispositivos|Nenhum|
|c2d.property.update.success|Atualizações bem sucedidas da propriedade do dispositivo da IoT Central|Contagem|Total|A contagem de todas as atualizações de propriedade bem sucedidas iniciadas a partir da IoT Central|Nenhum|
|c2d.property.update.failure|Atualizações de propriedade do dispositivo com falha da IoT Central|Contagem|Total|A contagem de todas as atualizações de propriedade falhadas iniciadas a partir da IoT Central|Nenhum|
|d2c.property.update.success|Atualizações bem-sucedidas da propriedade do dispositivo de dispositivos|Contagem|Total|A contagem de todas as atualizações de propriedade bem sucedidas iniciadas a partir de dispositivos|Nenhum|
|d2c.property.update.failure|Atualizações de propriedade do dispositivo com falha de dispositivos|Contagem|Total|A contagem de todas as atualizações de propriedade com falha iniciadas a partir de dispositivos|Nenhum|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ServiceApiHit|Total de ocorrências da API de Serviço|Contagem|Contagem|Número total de ocorrências da API de serviço|Tipo de atividade,nome de atividade|
|ServiceApiLatency|Latência geral da API de Serviço|Milissegundos|Média|Latência geral das solicitações da API de serviço|'Operação de atividade',Nome de atividade,''''''''''''''''''''''''''''|
|ServiceApiResult|Total de resultados da API de Serviço|Contagem|Contagem|Número total de resultados da API de serviço|'Operação de atividade',Nome de atividade,''''''''''''''''''''''''''''|
|Caixa de sapato de saturação|Saturação geral do cofre|Porcentagem|Média|Capacidade do cofre usado|Tipo de atividade,nome de atividade,transactionType|
|Disponibilidade|Disponibilidade geral do cofre|Porcentagem|Média|Disponibilidade de solicitações de cofre|'Operação de atividade',Nome de atividade,''''''''''''''''''''''''''''|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|CacheUtilização|Utilização do cache|Porcentagem|Média|Nível de utilização no escopo do cluster|Nenhum|
|QueryDuration|Duração da consulta|Milissegundos|Média|Duração das consultas em segundos|QueryStatus|
|IngestãoUtilização|Utilização da ingestão|Porcentagem|Média|Taxa de slots de ingestão usados no cluster|Nenhum|
|Keepalive|Mantenha-se vivo|Contagem|Média|A verificação de integridade indica que o cluster responde às consultas|Nenhum|
|IngestionVolumeInMB|Volume de ingestão (em MB)|Contagem|Total|Volume total de dados ingeridos no cluster (em MB)|Banco de dados|
|InstionLatencyInSeconds|Latência de ingestão (em segundos)|Segundos|Média|Tempo de ingestão da origem (por exemplo, mensagem no Hub de Eventos) para o cluster em segundos|Nenhum|
|EventosProcessadosParaEventosHubs|Eventos processados (para Hubs de Eventos/IoT)|Contagem|Total|Número de eventos processados pelo cluster ao ingerindo do Event/IoT Hub|EventStatus|
|IngestionResult|Resultado da ingestão|Contagem|Contagem|Número de operações de ingestão|IngestionResultDetails|
|CPU|CPU|Porcentagem|Média|Nível de utilização da CPU|Nenhum|
|ContinuousExportNumOfRecordsExported|Exportação contínua – num dos registros exportados|Contagem|Total|Número de registros exportados, disparados para cada artefato de armazenamento escrito durante a operação de exportação|Nome de exportação contínua,banco de dados|
|ExportAçãoUtilização|Utilização da exportação|Porcentagem|Máximo|Utilização da exportação|Nenhum|
|Contagem contínuadeexportações|Contagem contínua pendente de exportação|Contagem|Máximo|O número de empregos de exportação contínuos pendentes prontos para execução|Nenhum|
|ContinuousExportMaxLatenessMinutes|Atraso máximo da exportação contínua|Contagem|Máximo|O atraso (em minutos) relatado pelos empregos de exportação contínua no cluster|Nenhum|
|Resultado de exportação contínua|Resultado contínuo da exportação|Contagem|Contagem|Indica se a Exportação Contínua teve sucesso ou falha|Nome de exportação contínua,resultado,banco de dados|
|StreamingIngestDuration|Streaming de duração ingerida|Milissegundos|Média|Streaming ingere duração em milissegundos|Nenhum|
|StreamingIngestDataRate|Taxa de dados de ingestão de streaming|Contagem|Média|Taxa de dados de ingestão de streaming (MB por segundo)|Nenhum|
|Taxa de solicitação mais vaporosa|Taxa de solicitação de ingestão de streaming|Contagem|RateRequestsPerSegundo|Taxa de solicitação de ingestão de streaming (solicitações por segundo)|Nenhum|
|StreamingingestResultados|Resultado de Streaming Ingest|Contagem|Média|Resultado de streaming|Result|
|TotalNumberOfConcurrentqueries|Número total de consultas simultâneas|Contagem|Total|Número total de consultas simultâneas|Nenhum|
|TotalNumberOfThrottledQueries|Número total de consultas estranguladas|Contagem|Total|Número total de consultas estranguladas|Nenhum|
|TotalNumberOfThrottledCommands|Número total de comandos estrangulados|Contagem|Total|Número total de comandos estrangulados|CommandType|
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
|RunStartThrottledEvents|Executar eventos estrangulados|Contagem|Total|Número de eventos de execução de fluxo de trabalho iniciam eventos estrangulados.|Nenhum|
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
|RunStartThrottledEvents|Executar eventos estrangulados|Contagem|Total|Número de eventos de execução de fluxo de trabalho iniciam eventos estrangulados.|Nenhum|
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
|IntegraçãoServiçoAmbienteambienteTrabalhotrabalhoControlede utilizaçãodotrabalho|Uso do processador de fluxo de trabalho para o ambiente de serviço de integração|Porcentagem|Média|Uso do processador de fluxo de trabalho para ambiente de serviço de integração.|Nenhum|
|IntegraçãoServiçoAmbientede trabalhoTrabalhotrabalhoTrabalhosmaisbem-ministroUsode trabalho|Uso da memória do fluxo de trabalho para o ambiente de serviço de integração|Porcentagem|Média|Uso de memória de fluxo de trabalho para ambiente de serviço de integração.|Nenhum|
|IntegraçãoServiceEnvironmentConectorProcessorUseusouso|Uso do processador conector para ambiente de serviço de integração|Porcentagem|Média|Uso do processador conector para ambiente de serviço de integração.|Nenhum|
|IntegraçãoServiceEnvironmentConectorMemoryUseUso|Uso da memória do conector para o ambiente de serviço de integração|Porcentagem|Média|Uso de memória do conector para ambiente de serviço de integração.|Nenhum|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Corridas canceladas|Corridas canceladas|Contagem|Total|Número de corridas canceladas para este espaço de trabalho|Cenário,RunType,PublicadoPipelineId,ComputeType,PipelineStepType|
|Cancelar corridas solicitadas|Cancelar corridas solicitadas|Contagem|Total|Número de corridas onde o cancelamento foi solicitado para este espaço de trabalho|Cenário,RunType,PublicadoPipelineId,ComputeType,PipelineStepType|
|Executa concluído|Executa concluído|Contagem|Total|Número de corridas concluídas com sucesso para este espaço de trabalho|Cenário,RunType,PublicadoPipelineId,ComputeType,PipelineStepType|
|Execuções com falha|Execuções com falha|Contagem|Total|Número de corridas falhadas para este espaço de trabalho|Cenário,RunType,PublicadoPipelineId,ComputeType,PipelineStepType|
|Finalizando corridas|Finalizando corridas|Contagem|Total|Número de corridas inseridas no estado final para este espaço de trabalho|Cenário,RunType,PublicadoPipelineId,ComputeType,PipelineStepType|
|Não responder corridas|Não responder corridas|Contagem|Total|Número de corridas que não respondem por este espaço de trabalho|Cenário,RunType,PublicadoPipelineId,ComputeType,PipelineStepType|
|Não iniciado saqueio|Não iniciado saqueio|Contagem|Total|Número de corridas em estado não iniciado para este espaço de trabalho|Cenário,RunType,PublicadoPipelineId,ComputeType,PipelineStepType|
|Preparando corridas|Preparando corridas|Contagem|Total|Número de corridas que estão se preparando para este espaço de trabalho|Cenário,RunType,PublicadoPipelineId,ComputeType,PipelineStepType|
|Corridas de provisionamento|Corridas de provisionamento|Contagem|Total|Número de corridas que estão provisionando para este espaço de trabalho|Cenário,RunType,PublicadoPipelineId,ComputeType,PipelineStepType|
|Corridas enfileiradas|Corridas enfileiradas|Contagem|Total|Número de corridas que estão na fila para este espaço de trabalho|Cenário,RunType,PublicadoPipelineId,ComputeType,PipelineStepType|
|Started Runs|Started Runs|Contagem|Total|Número de corridas iniciadas para este espaço de trabalho|Cenário,RunType,PublicadoPipelineId,ComputeType,PipelineStepType|
|Iniciando corridas|Iniciando corridas|Contagem|Total|Número de corridas iniciadas para este espaço de trabalho|Cenário,RunType,PublicadoPipelineId,ComputeType,PipelineStepType|
|Errors|Errors|Contagem|Total|Número de erros de execução neste espaço de trabalho|Cenário|
|Warnings|Warnings|Contagem|Total|Número de avisos de execução neste espaço de trabalho|Cenário|
|Registro de modelo sucedendo|Registro de modelo sucedendo|Contagem|Total|Número de registros de modelos que tiveram sucesso neste espaço de trabalho|Cenário|
|Falha no registro do modelo|Falha no registro do modelo|Contagem|Total|Número de registros de modelos que falharam neste espaço de trabalho|Cenário,StatusCode|
|Implantação do modelo iniciado|Implantação do modelo iniciado|Contagem|Total|Número de implantações de modelos iniciadas neste espaço de trabalho|Cenário|
|Modelo Implantar Bem Sucedido|Modelo Implantar Bem Sucedido|Contagem|Total|Número de implantações de modelos que tiveram sucesso neste espaço de trabalho|Cenário|
|Falha na implantação do modelo|Falha na implantação do modelo|Contagem|Total|Número de implantações de modelos que falharam neste espaço de trabalho|Cenário,StatusCode|
|Nodes Totais|Nodes Totais|Contagem|Média|Número de nódulos totais. Este total inclui alguns dos Nós Ativos, Nós Ociosos, Nós Inutilizáveis, Nós Premepted, Deixando Nós|Cenário,Nome de Cluster|
|Nódulos ativos|Nódulos ativos|Contagem|Média|Número de nódulos Acitve. Estes são os nós que estão ativamente executando um trabalho.|Cenário,Nome de Cluster|
|Nódulos ociosos|Nódulos ociosos|Contagem|Média|Número de nódulos ociosos. Nós ociosos são os nós que não estão executando nenhum trabalho, mas podem aceitar novos empregos se disponíveis.|Cenário,Nome de Cluster|
|Nós inutilizáveis|Nós inutilizáveis|Contagem|Média|Número de nós inutilizáveis. Nós inutilizáveis não estão funcionais devido a algum problema insolúvel. Azure reciclará esses nódulos.|Cenário,Nome de Cluster|
|Lemos pré-empáticos|Lemos pré-empáticos|Contagem|Média|Número de nódulos pré-emptos. Esses nós são os nós de baixa prioridade que são retirados do pool de nós disponíveis.|Cenário,Nome de Cluster|
|Deixando nódulos|Deixando nódulos|Contagem|Média|Número de nódulos de saída. Deixando nós são os nós que acabaram de processar um trabalho e irão para o estado ocioso.|Cenário,Nome de Cluster|
|Núcleos Totais|Núcleos Totais|Contagem|Média|Número de núcleos totais|Cenário,Nome de Cluster|
|Núcleos ativos|Núcleos ativos|Contagem|Média|Número de núcleos ativos|Cenário,Nome de Cluster|
|Núcleos ociosos|Núcleos ociosos|Contagem|Média|Número de núcleos ociosos|Cenário,Nome de Cluster|
|Núcleos inutilizáveis|Núcleos inutilizáveis|Contagem|Média|Número de núcleos inutilizáveis|Cenário,Nome de Cluster|
|Núcleos pré-empáveis|Núcleos pré-empáveis|Contagem|Média|Número de núcleos pré-empáveis|Cenário,Nome de Cluster|
|Deixando núcleos|Deixando núcleos|Contagem|Média|Número de núcleos de saída|Cenário,Nome de Cluster|
|Percentual de Utilização de Cotas|Percentual de Utilização de Cotas|Contagem|Média|Percentual de cota utilizada|Cenário,ClusterName,VmFamilyName,VmPriority|
|CpuUtilização|CpuUtilização|Contagem|Média|CPU (versão prévia)|Cenário,runId,NodeId,CreatedTime|
|GPUUtilização|GPUUtilização|Contagem|Média|GPU (Visualização)|Cenário,runId,NodeId,CreatedTime,DeviceId|


## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Uso|Uso|Contagem|Contagem|Contagem de chamadas à API|ApiCategory,ApiName,ResultType,ResponseCode|
|Disponibilidade|Disponibilidade|Porcentagem|Média|Disponibilidade das APIs|ApiCategory,ApiName|

## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft.Media/mediaservices/streamingEndpoints

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Saída|Saída|Bytes|Total|A quantidade de dados de Egress, em bytes.|OutputFormat|
|SuccessE2ELatency|Sucesso de ponta a ponta Latência|Milissegundos|Média|A latência média para pedidos bem sucedidos em milissegundos.|OutputFormat|
|Requests|Requests|Contagem|Total|Solicita para um ponto final de streaming.|Formato de saída,httpstatuscode,ErrorCode|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Cotação patrimonial|Cota de ativos|Contagem|Média|Quantos ativos são permitidos para conta de serviço de mídia atual|Nenhum|
|Contagem de ativos|Contagem de ativos|Contagem|Média|Quantos ativos já estão criados na conta atual de serviçode mídia|Nenhum|
|AssetQuotaUsedPercentage|Cota de ativos utilizada|Porcentagem|Média|Ativo usado percentual na conta de serviço de mídia atual|Nenhum|
|Cota de política de conteúdo|Cota de política de chave de conteúdo|Contagem|Média|Quantos policiais-chave de conteúdo são permitidos para a conta atual do serviço de mídia|Nenhum|
|Contagem de políticas de conteúdo|Contagem de políticas de chave de conteúdo|Contagem|Média|Quantas políticas-chave de conteúdo já são criadas na conta atual do serviço de mídia|Nenhum|
|ConteúdochavePolicyQuotaUsedPercentage|Cota de política de chave de conteúdo usada|Porcentagem|Média|A Política de Chave de Conteúdo usou porcentagem na conta atual do serviço de mídia|Nenhum|
|StreamingPolicyQuota|Cota de Política de Streaming|Contagem|Média|Quantas políticas de streaming são permitidas para a conta atual do serviço de mídia|Nenhum|
|StreamingPolicyCount|Contagem de políticas de streaming|Contagem|Média|Quantas políticas de streaming já estão criadas na conta atual do serviço de mídia|Nenhum|
|StreamingPolicyQuotaUsedPercentage|Cota de Política de Streaming usada|Porcentagem|Média|A Política de Streaming usou porcentagem na conta atual do serviço de mídia|Nenhum|


## <a name="microsoftmixedrealityremoterenderingaccounts"></a>Microsoft.MixedReality/remoteRenderingAccounts

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|AtivosConvertidos|Ativos convertidos|Contagem|Total|Número total de ativos convertidos|AppId,ResourceId,SDKVersion|
|AtivaRenderingSessions|Sessões de renderização ativa|Contagem|Total|Número total de sessões de renderização ativa|AppId,ResourceId,SessionType,SDKVersion|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppContas/capacityPools/volumes

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|AverageReadLatency|Latência média de leitura|MilliSeconds|Média|Latência média de leitura em milissegundos por operação|Nenhum|
|AverageWriteLatency|Latência média de gravação|MilliSeconds|Média|Latência média de gravação em milissegundos por operação|Nenhum|
|VolumeLogicalSize|Tamanho do volume consumido|Bytes|Média|Tamanho lógico do volume (bytes usados)|Nenhum|
|VolumeSnapshotSize|Tamanho do instantâneo de volume|Bytes|Média|Tamanho de todos os instantâneos no volume|Nenhum|
|ReadIops|IOPS de leitura|CountPerSecond|Média|Operações de entrada/saída de leitura por segundo|Nenhum|
|WriteIops|IOPS de gravação|CountPerSecond|Média|Operações de entrada/saída de gravação por segundo|Nenhum|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|VolumePoolAllocatedUsed|Pool alocado para o tamanho do volume|Bytes|Média|Tamanho usado alocado do pool|Nenhum|
|VolumePoolTotalLogicalSize|Tamanho consumido da piscina|Bytes|Média|Soma do tamanho lógico de todos os volumes que pertencem ao pool|Nenhum|

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
|VipAvailability|Disponibilidade do Caminho de Dados|Contagem|Média|Disponibilidade média do caminho de dados do Balanceador de Carga por duração de tempo|FrontendIPAddress,FrontendPort|
|DipAvailability|Status de investigação de integridade|Contagem|Média|Status médio de investigação de integridade do Balanceador de Carga por duração de tempo|ProtocolType,BackendPort,FrontendIPAddress,FrontendPort,BackendIPAddress|
|ByteCount|Contagem de Bytes|Contagem|Total|Número total de Bytes transmitidos no período|FrontendIPAddress,FrontendPort,Direction|
|PacketCount|Contagem de Pacotes|Contagem|Total|Número total de Pacotes transmitidos no período|FrontendIPAddress,FrontendPort,Direction|
|SYNCount|Contagem de SYN|Contagem|Total|Número total de Pacotes de SYN transmitidos no período|FrontendIPAddress,FrontendPort,Direction|
|SnatConnectionCount|Contagem de Conexões SNAT|Contagem|Total|Número total de novas conexões SNAT criadas no período|FrontendIPAddress,BackendIPAddress,ConnectionState|
|AllocatedSnatPorts|Portas SNAT alocadas (versão prévia)|Contagem|Total|Número total de portas SNAT alocadas no período|FrontendIPAddress,BackendIPAddress,ProtocolType,IsAwaitingRemoval|
|UsedSnatPorts|Portas SNAT usadas (versão prévia)|Contagem|Total|Número total de portas SNAT usadas no período|FrontendIPAddress,BackendIPAddress,ProtocolType,IsAwaitingRemoval|

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
|ByteCount|Contagem de Bytes|Contagem|Total|Número total de Bytes transmitidos no período|Porto,Direção|
|PacketCount|Contagem de Pacotes|Contagem|Total|Número total de Pacotes transmitidos no período|Porto,Direção|
|SynCount|Contagem de SYN|Contagem|Total|Número total de Pacotes de SYN transmitidos no período|Porto,Direção|



## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Tempo de ida e volta para Pings para uma VM|MilliSeconds|Média|Tempo de ida e volta para Pings enviado para um vm de destino|SourceCustomerAddress,DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Pings com falha em uma VM|Porcentagem|Média|Porcentagem do número de Pings falhados para pings enviados de uma VM de destino|SourceCustomerAddress,DestinationCustomerAddress|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ApplicationRuleHit|Contagem de acerto de regras de aplicação|Contagem|Total|Número de vezes que as regras do aplicativo foram atingidas|Status,Razão,Protocolo|
|NetworkRuleHit|Contagem de acerto de regras de rede|Contagem|Total|Número de vezes que as regras da rede foram atingidas|Status,Razão,Protocolo|
|FirewallHealth|Estado de saúde do firewall|Porcentagem|Média|Estado de saúde do firewall|Status,Razão|
|DataProcessado|Dados processados|Bytes|Total|Quantidade total de dados processados pelo Firewall|Nenhum|
|SNATPortUtilization|Utilização da porta SNAT|Porcentagem|Média|Utilização da porta SNAT|Nenhum|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Produtividade|Produtividade|BytesPerSecond|Média|Número de bytes por segundo atendidos pelo Gateway de Aplicativo|Nenhum|
|UnhealthyHostCount|Contagem de hosts não íntegros|Contagem|Média|Número de hosts de back-end não íntegros|BackendSettingsPool|
|HealthyHostCount|Contagem de hosts íntegros|Contagem|Média|Número de hosts de back-end íntegros|BackendSettingsPool|
|TotalRequests|Total de Solicitações|Contagem|Total|Contagem de solicitações bem sucedidas atendidas pelo Gateway de Aplicativo|BackendSettingsPool|
|AvgRequestCountPerHealthyHost|Solicitações por minuto por Host Saudável|Contagem|Média|Contagem média de solicitação por minuto por host backend saudável em uma piscina|BackendSettingsPool|
|FailedRequests|Solicitações com falha|Contagem|Total|Contagem de solicitações com falha atendidas pelo Gateway de Aplicativo|BackendSettingsPool|
|ResponseStatus|Status da Resposta|Contagem|Total|Status de resposta HTTP retornado pelo Gateway de Aplicativo|HttpStatusGroup|
|CurrentConnections|Conexões atuais|Contagem|Total|Contagem de conexões atuais estabelecidas com o Gateway de Aplicativo|Nenhum|
|Novas conexõesPerSecond|Novas conexões por segundo|CountPerSecond|Média|Novas conexões por segundo estabelecidas com o Application Gateway|Nenhum|
|CpuUtilização|Utilização da CPU|Porcentagem|Média|Utilização atual da CPU do Gateway de aplicativos|Nenhum|
|Unidades de capacitação|Unidades de capacidade atuais|Contagem|Média|Unidades de Capacidade consumidas|Nenhum|
|Unidades de capacidade de cobrança fixa|Unidades de capacidade de faturamento fixas|Contagem|Média|Unidades de capacidade mínima que serão cobradas|Nenhum|
|EstimativadeUnidades de Capacidade De Billed|Unidades de capacidade estimadas|Contagem|Média|Unidades de capacidade estimadas que serão cobradas|Nenhum|
|ComputaçãoUnidades|Unidades atuais de computação|Contagem|Média|Unidades de Computação consumidas|Nenhum|
|BackendResponseStatus|Status de resposta backend|Contagem|Total|O número de códigos de resposta HTTP gerados pelos membros do backend. Isso não inclui nenhum código de resposta gerado pelo Gateway do aplicativo.|BackendServer,BackendPool,BackendHttpSetting,HttpStatusGroup|
|TlsProtocol|Protocolo TLS do cliente|Contagem|Total|O número de solicitações TLS e não-TLS iniciadas pelo cliente que estabeleceu conexão com o Gateway de aplicativo. Para visualizar a distribuição do protocolo TLS, filtre pela dimensão Protocolo TLS.|Ouvinte, TlsProtocol|
|BytesSent|Bytes Enviados|Bytes|Total|O número total de bytes enviados pelo Gateway de aplicativo para os clientes|Ouvinte|
|BytesReceived|Bytes Recebidos|Bytes|Total|O número total de bytes recebidos pelo Gateway de aplicativo dos clientes|Ouvinte|
|ClienteRtt|Cliente RTT|MilliSeconds|Média|Tempo médio de ida e volta entre clientes e Gateway de aplicativo. Esta métrica indica quanto tempo leva para estabelecer conexões e retornar reconhecimentos|Ouvinte|
|ApplicationGatewayTotalTime|Tempo total do gateway de aplicativo|MilliSeconds|Média|Tempo médio que leva para que uma solicitação seja processada e sua resposta seja enviada. Isso é calculado como média do intervalo do momento em que o Application Gateway recebe o primeiro byte de uma solicitação HTTP até o momento em que a operação de envio de resposta termina. É importante notar que isso geralmente inclui o tempo de processamento do Application Gateway, o tempo que os pacotes de solicitação e resposta estão viajando pela rede e o tempo que o servidor backend levou para responder.|Ouvinte|
|BackendConnectTime|Tempo de conexão backend|MilliSeconds|Média|Tempo gasto estabelecendo uma conexão com um servidor backend|Ouvinte,BackendServer,BackendPool,BackendHttpSetting|
|BackendFirstByteResponseTime|Backend First Byte Response Time|MilliSeconds|Média|Intervalo de tempo entre o início do estabelecimento de uma conexão com o servidor backend e o recebimento do primeiro byte do cabeçalho de resposta, aproximando o tempo de processamento do servidor backend|Ouvinte,BackendServer,BackendPool,BackendHttpSetting|
|BackendLastByteResponseTime|Backend Last Byte Response Time|MilliSeconds|Média|Intervalo de tempo entre o início do estabelecimento de uma conexão com o servidor backend e o recebimento do último byte do corpo de resposta|Ouvinte,BackendServer,BackendPool,BackendHttpSetting|
|Contagem combinada|Firewall de aplicativos web v1 Distribuição total de regras|Contagem|Total|Web Application Firewall v1 Distribuição total de regras para o tráfego de entrada|RuleGroup,RuleId|
|Contagem bloqueada|Firewall de aplicativos web v1 bloqueia a distribuição de regras de solicitações|Contagem|Total|O Web Application Firewall v1 bloqueou a distribuição de regras de solicitações|RuleGroup,RuleId|
|BloqueadoReqCount|Contagem de solicitações bloqueadas do Firewall de Aplicativos da Web v1|Contagem|Total|Contagem de solicitações bloqueadas do Web Application Firewall v1|Nenhum|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|AverageBandwidth|Largura de banda de S2S do gateway|BytesPerSecond|Média|Largura de banda média site a site de um gateway em bytes por segundo|Nenhum|
|P2SBandwidth|Largura de banda de P2S do gateway|BytesPerSecond|Média|Largura de banda média ponto a site de um gateway em bytes por segundo|Nenhum|
|P2SConnectionCount|Contagem de conexões P2S|Contagem|Máximo|Contagem de conexões ponto a site de um gateway|Protocolo|
|TunnelAverageBandwidth|Largura de Banda de Túnel|BytesPerSecond|Média|Largura de banda média de um túnel em bytes por segundo|Nome de conexão,RemotaIP|
|TunnelEgressBytes|Bytes de Saída de Túnel|Bytes|Total|Bytes de saída de um túnel|Nome de conexão,RemotaIP|
|TunnelIngressBytes|Bytes de Entrada de Túnel|Bytes|Total|Bytes de entrada de um túnel|Nome de conexão,RemotaIP|
|TunnelEgressPackets|Pacotes de Saída de Túnel|Contagem|Total|Contagem de pacotes de saída de um túnel|Nome de conexão,RemotaIP|
|TunnelIngressPackets|Pacotes de Entrada de Túnel|Contagem|Total|Contagem de pacotes de entrada de um túnel|Nome de conexão,RemotaIP|
|TunnelEgressPacketDropTSMismatch|Remoção de Pacotes Incompatíveis de TS de Saída de Túnel|Contagem|Total|Contagem de remoção de pacotes de saída da incompatibilidade do seletor de tráfego de um túnel|Nome de conexão,RemotaIP|
|TunnelIngressPacketDropTSMismatch|Remoção de Pacotes Incompatíveis de TS de Entrada de Túnel|Contagem|Total|Contagem de remoção de pacotes de entrada da incompatibilidade do seletor de tráfego de um túnel|Nome de conexão,RemotaIP|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft.Network/expressRoutePorts

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|RxLightLevel|RxLightLevel|Contagem|Média|Nível de Luz Rx em dBm|Link,Lane|
|TxLightLevel|TxLightLevel|Contagem|Média|Nível de luz Tx em dBm|Link,Lane|
|AdminState|AdminState|Contagem|Média|Estado de admin do porto|Link|
|Protocolo de linha|Protocolo de linha|Contagem|Média|Status do protocolo de linha da porta|Link|
|PortBitsInPerSegundo|BitsInPerSecond|CountPerSecond|Média|Bits de entrada no Azure por segundo|Link|
|PortBitsOutPerSegundo|BitsOutPerSecond|CountPerSecond|Média|Bits de saída no Azure por segundo|Link|



## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Média|Bits de entrada no Azure por segundo|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Média|Bits de saída no Azure por segundo|PeeringType|
|GlobalReachBitsPerSegundo|GlobalReachBitsPerSegundo|CountPerSecond|Média|Bits de entrada no Azure por segundo|Chave de peeredCircuitSKey|
|GlobalReachBitsOutSegundo|GlobalReachBitsOutSegundo|CountPerSecond|Média|Bits de saída no Azure por segundo|Chave de peeredCircuitSKey|
|BgpAvailability|Disponibilidade do BGP|Porcentagem|Média|Disponibilidade de BGP do MSEE para todos os pares.|PeeringType,Peer|
|ArpAvailability|Disponibilidade de Arp|Porcentagem|Média|Disponibilidade de ARP do MSEE para todos os pares.|PeeringType,Peer|
|QosDropBitsInPerSegundo|DroppedInBitsPerSecond|CountPerSecond|Média|Os bits de ingress caíram por segundo|Nenhum|
|QosDropBitsOutPerSegundo|DroppedOutBitsPerSecond|CountPerSecond|Média|Os bits de saída de dados caíram por segundo|Nenhum|

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
|ErGatewayConnectionBitsPerSegundo|BitsInPerSecond|CountPerSecond|Média|Bits de entrada no Azure por segundo|Connectionname|
|ErGatewayConnectionBitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Média|Bits de saída no Azure por segundo|Connectionname|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|QpsByEndpoint|Consultas pelo Ponto de Extremidade Retornado|Contagem|Total|Número de vezes que um ponto de extremidade do Gerenciador de Tráfego foi retornado no período determinado|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Status do Ponto de Extremidade por Ponto de Extremidade|Contagem|Máximo|1 se o status de investigação de um ponto de extremidade for “Habilitado”; caso contrário, 0.|EndpointName|



## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ProbesFailedPercent|Porcentagem de investigações com falha|Porcentagem|Média|Porcentagem de investigações de monitoramento de conectividade com falha|Nenhum|
|AverageRoundtripMs|Avg. Tempo de ida e volta (ms)|MilliSeconds|Média|Tempo ida e volta de rede médio (ms) para investigações de monitoramento de conectividade enviadas entre a origem e o destino|Nenhum|
|ChecksFailedPercent|Verifica a porcentagem de falha (visualização)|Porcentagem|Média|% das verificações de monitoramento de conectividade falhadas|Endereço de origem,Nome de origem,SourceResourceId,SourceType,Protocol,DestinationAddress,DestinationName,DestinationResourceId,DestinationType,DestinationPort,TestGroupName,TestConfigurationName|
|RoundTripTimeMs|Tempo de ida e volta (ms) (Visualização)|MilliSeconds|Média|Tempo de ida e volta em milissegundos para as verificações de monitoramento de conectividade|Endereço de origem,Nome de origem,SourceResourceId,SourceType,Protocol,DestinationAddress,DestinationName,DestinationResourceId,DestinationType,DestinationPort,TestGroupName,TestConfigurationName|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|RequestCount|Contagem de solicitações|Contagem|Total|O número de solicitações de cliente atendidas pelo proxy HTTP/S|Httpstatus,httpstatusgroup,clientregion,clientcountry|
|RequestSize|Tamanho da solicitação|Bytes|Total|O número de bytes enviados como solicitações de clientes ao proxy HTTP/S|Httpstatus,httpstatusgroup,clientregion,clientcountry|
|ResponseSize|Tamanho da resposta|Bytes|Total|O número de bytes enviados como respostas do proxy HTTP/S para clientes|Httpstatus,httpstatusgroup,clientregion,clientcountry|
|Tamanho da resposta de Billable|Tamanho da resposta billable|Bytes|Total|O número de bytes faturados (mínimo 2KB por solicitação) enviados como respostas do proxy HTTP/S aos clientes.|Httpstatus,httpstatusgroup,clientregion,clientcountry|
|BackendRequestCount|Contagem de solicitações de back-end|Contagem|Total|O número de solicitações enviadas do proxy HTTP/S aos back-ends|HttpStatus,HttpStatusGroup,Backend|
|BackendRequestLatency|Latência de solicitação de back-end|MilliSeconds|Média|O tempo calculado de quando a solicitação foi enviada pelo proxy HTTP/S ao back-end até o proxy HTTP/S receber o último byte de resposta do back-end|Back-end|
|TotalLatency|Latência total|MilliSeconds|Média|O tempo calculado de quando a solicitação do cliente foi recebida pelo proxy HTTP/S até o cliente confirmar o último byte de resposta do proxy HTTP/S|Httpstatus,httpstatusgroup,clientregion,clientcountry|
|BackendHealthPercentage|Percentual de integridade do back-end|Porcentagem|Média|O percentual de investigações de integridade bem-sucedidas do proxy HTTP/S aos back-ends|Backend,BackendPool|
|WebApplicationFirewallRequestCount|Contagem de solicitações do Firewall de Aplicativo Web|Contagem|Total|O número de solicitações de cliente processadas pelo Firewall do Aplicativo Web|Nome da política,Nome de regra,ação|


## <a name="microsoftnetworkprivatednszones"></a>Microsoft.Network/privateDnsZones

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|QueryVolume|Volume de consulta|Contagem|Total|Número de consultas atendidas para uma região de DNS privada|Nenhum|
|RecordSetCount|Contagem de Conjuntos de Registros|Contagem|Máximo|Número de conjuntos de registros em uma região de DNS privado|Nenhum|
|RecordSetCapacityUtilization|Utilização de capacidade de Conjuntos de Registros|Porcentagem|Máximo|Porcentagem da capacidade do conjunto de registros utilizado por uma zona de DNS privada|Nenhum|
|VirtualNetworkLinkCount|Contagem de links de rede virtual|Contagem|Máximo|Número de redes virtuais vinculadas a uma zona de DNS privada|Nenhum|
|Utilização virtualnetworklink|Utilização da capacidade de link de rede virtual|Porcentagem|Máximo|Porcentagem da capacidade de Link de Rede Virtual utilizada por uma região de DNS privada|Nenhum|
|VirtualNetworkWithRegistrationLinkCount|Contagem de links de registro de rede virtual|Contagem|Máximo|Número de Redes Virtuais vinculadas a uma zona DeD Privada com registro automático ativado|Nenhum|
|Rede virtualcomcapacidade de registroUtilização|Utilização da capacidade de vínculo de registro de rede virtual|Porcentagem|Máximo|Porcentagem de Link de Rede Virtual com capacidade de registro automático utilizada por uma zona de DNS privada|Nenhum|

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
|Average_% Free Inodes|% de Inodes livres|Contagem|Média|Average_% Free Inodes|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_% Free Space|% de Espaço Livre|Contagem|Média|Average_% Free Space|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_% Used Inodes|% de Inodes Usados|Contagem|Média|Average_% Used Inodes|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_% Used Space|% de Espaço Usado|Contagem|Média|Average_% Used Space|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_Disk Read Bytes/sec|Bytes Lidos no Disco/s |Contagem|Média|Average_Disk Read Bytes/sec|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_Disk Reads/sec|Leituras de Disco/s |Contagem|Média|Average_Disk Reads/sec|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_Disk Transfers/sec|Transferências de Disco/s|Contagem|Média|Average_Disk Transfers/sec|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_Disk Write Bytes/sec| Bytes Gravados no Disco/s|Contagem|Média|Average_Disk Write Bytes/sec|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_Disk Writes/sec| Gravações de Disco/s|Contagem|Média|Average_Disk Writes/sec|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_Free Megabytes|Megabytes Livres|Contagem|Média|Average_Free Megabytes|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_Logical Disk Bytes/sec|Bytes de Disco Lógico/s|Contagem|Média|Average_Logical Disk Bytes/sec|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_% Available Memory|% de Memória Disponível|Contagem|Média|Average_% Available Memory|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_% Available Swap Space|% de Espaço de Permuta Disponível|Contagem|Média|Average_% Available Swap Space|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_% Used Memory|% de Memória Usada|Contagem|Média|Average_% Used Memory|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_% Used Swap Space|% de Espaço de Permuta Usado|Contagem|Média|Average_% Used Swap Space|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_Available MBytes Memory|MBytes de Memória Disponíveis|Contagem|Média|Average_Available MBytes Memory|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_Available MBytes Swap|MBytes de Espaço de Permuta Disponíveis|Contagem|Média|Average_Available MBytes Swap|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_Page Reads/sec|Leituras de Página/s|Contagem|Média|Average_Page Reads/sec|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_Page Writes/sec|Gravações de Página/s|Contagem|Média|Average_Page Writes/sec|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_Pages/sec|Páginas/s|Contagem|Média|Average_Pages/sec|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_Used MBytes Swap Space|MBytes de Espaço de Permuta Usado|Contagem|Média|Average_Used MBytes Swap Space|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_Used Memory MBytes|MBytes de Memória Usada|Contagem|Média|Average_Used Memory MBytes|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_Total Bytes Transmitted|Total de Bytes Transmitidos|Contagem|Média|Average_Total Bytes Transmitted|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_Total Bytes Received|Total de Bytes Recebidos|Contagem|Média|Average_Total Bytes Received|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_Total Bytes|Total de Bytes|Contagem|Média|Average_Total Bytes|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_Total Packets Transmitted|Total de Pacotes Transmitidos|Contagem|Média|Average_Total Packets Transmitted|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_Total Packets Received|Total de Pacotes Recebidos|Contagem|Média|Average_Total Packets Received|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_Total Rx Errors|Total de Erros de Rx|Contagem|Média|Average_Total Rx Errors|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_Total Tx Errors|Total de Erros de Tx|Contagem|Média|Average_Total Tx Errors|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_Total Collisions|Total de Colisões|Contagem|Média|Average_Total Collisions|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_Avg. de segundos/Leitura do Disco|Avg. Disk sec/Read|Contagem|Média|Average_Avg. de segundos/Leitura do Disco|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_Avg. de segundos/Transferência do Disco|Avg. Disk sec/Transfer|Contagem|Média|Average_Avg. de segundos/Transferência do Disco|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_Avg. de segundos/Gravação do Disco|Avg. Disk sec/Write|Contagem|Média|Average_Avg. de segundos/Gravação do Disco|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_Physical Disk Bytes/sec|Bytes/s do Disco Físico|Contagem|Média|Average_Physical Disk Bytes/sec|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_Pct Privileged Time|% de Tempo Privilegiado|Contagem|Média|Average_Pct Privileged Time|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_Pct User Time|% de Tempo do Usuário|Contagem|Média|Average_Pct User Time|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_Used Memory kBytes|KBytes de Memória Usada|Contagem|Média|Average_Used Memory kBytes|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_Virtual Shared Memory|Memória Virtual Compartilhada|Contagem|Média|Average_Virtual Shared Memory|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_% DPC Time|% de Tempo de DPC|Contagem|Média|Average_% DPC Time|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_% Idle Time|% de Tempo Ocioso|Contagem|Média|Average_% Idle Time|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_% Interrupt Time|% de Tempo de Interrupção|Contagem|Média|Average_% Interrupt Time|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_% IO Wait Time|% de Tempo de Espera de E/S|Contagem|Média|Average_% IO Wait Time|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_% Nice Time|% de Tempo Adequado|Contagem|Média|Average_% Nice Time|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_% Privileged Time|% de Tempo Privilegiado|Contagem|Média|Average_% Privileged Time|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_% Processor Time|% Tempo do Processador|Contagem|Média|Average_% Processor Time|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_% User Time|% de Tempo do Usuário|Contagem|Média|Average_% User Time|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_Free Physical Memory|Memória Física Livre|Contagem|Média|Average_Free Physical Memory|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_Free Space in Paging Files|Espaço Livre em Arquivos de Paginação|Contagem|Média|Average_Free Space in Paging Files|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_Free Virtual Memory|Memória Virtual Livre|Contagem|Média|Average_Free Virtual Memory|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_Processes|Processos|Contagem|Média|Average_Processes|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_Size Stored In Paging Files|Tamanho Armazenado em Arquivos de Paginação|Contagem|Média|Average_Size Stored In Paging Files|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_Uptime|Tempo de atividade|Contagem|Média|Average_Uptime|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_Users|Usuários|Contagem|Média|Average_Users|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_Current Disk Queue Length|Comprimento da fila atual de disco|Contagem|Média|Average_Current Disk Queue Length|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_Available MBytes|MBytes Disponíveis|Contagem|Média|Average_Available MBytes|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_% Committed Bytes In Use|% de Bytes Confirmados em Uso|Contagem|Média|Average_% Committed Bytes In Use|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_Bytes Received/sec|Bytes Recebidos/s|Contagem|Média|Average_Bytes Received/sec|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_Bytes Sent/sec|Bytes Enviados/s|Contagem|Média|Average_Bytes Sent/sec|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_Bytes Total/sec|Bytes Totais/s|Contagem|Média|Average_Bytes Total/sec|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Average_Processor Queue Length|Tamanho da fila do processador|Contagem|Média|Average_Processor Queue Length|Computador,nome do objeto,nome de instância,contra-caminho,sistema de origem|
|Pulsação|Pulsação|Contagem|Total|Pulsação|Computador,OSType,Versão,SourceComputerId|
|Atualizar|Atualizar|Contagem|Média|Atualizar|Computador,produto,classificação,UpdateState,Opcional,Aprovado|
|Evento|Evento|Contagem|Média|Evento|Origem principal,EventLog,Computador,EventCategory,EventLevel,EventLevelName,EventID|

## <a name="microsoftpeeringpeeringservices"></a>Microsoft.Peering/peeringServices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Prefixlatency|Latência prefixo|Milissegundos|Média|Latência de prefixo mediana|PrefixO|

## <a name="microsoftpeeringpeerings"></a>Microsoft.Peering/peerings

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Disponibilidade de sessãoV4|Disponibilidade de sessão V4|Porcentagem|Média|Disponibilidade da sessão V4|ConnectionId|
|Disponibilidade de sessãoV6|Disponibilidade de sessão V6|Porcentagem|Média|Disponibilidade da sessão V6|ConnectionId|
|InressTrafficRate|Taxa de tráfego de ingress|BitsPerSegundo|Média|Taxa de tráfego de ingress em bits por segundo|ConnectionId|
|EgressTrafficRate|Taxa de tráfego de egress|BitsPerSegundo|Média|Taxa de tráfego de saída em bits por segundo|ConnectionId|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|QueryDuration|Duração da consulta|Milissegundos|Média|Duração da consulta DAX no último intervalo|Sem dimensões|
|QueryPoolJobQueueLength|Threads: tamanho da fila de trabalhos do pool consultas|Contagem|Média|Número de trabalhos na fila do pool de threads de consulta.|Sem dimensões|
|qpu_high_utilization_metric|Alta utilização de QPU|Contagem|Total|QPU de alta utilização no último minuto, 1 para QPU de alta utilização, caso contrário, 0|Sem dimensões|
|memory_metric|Memória|Bytes|Média|Memória. Intervalo de 0 a 3 GB para A1, 0 a 5 GB para A2, 0 a 10 GB para A3, 0 a 25 GB para A4, 0 a 50 GB para A5 e 0 a 100 GB para A6|Sem dimensões|
|memory_thrashing_metric|Sobrecarga de memória|Porcentagem|Média|Sobrecarga de memória média.|Sem dimensões|


## <a name="microsoftprojectbabylonaccounts"></a>Microsoft.ProjectBabylon/contas

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Alocação de ativosPor classificação|Distribuição de ativos por classificação|Contagem|Total|Indica o número de ativos com uma determinada classificação atribuída, ou seja, eles são classificados com esse rótulo.|Classificação,Fonte,RecursoId|
|AssetDistributionByStorageType|Distribuição de ativos por tipo de armazenamento|Contagem|Total|Indica o número de ativos com um determinado tipo de armazenamento.|Tipo de armazenamento,ResourceId|
|CatalogActiveUsers|Usuários ativos diários|Contagem|Total|Número de usuários ativos diariamente|ResourceId|
|CatálogoUso|Distribuição de uso por operação|Contagem|Total|Indique o número de operações que o usuário faz ao catálogo, ou seja, Acesso, Pesquisa, Glossário.|Operação,ResourceId|
|NúmeroOfAssetsComClassificações|Número de ativos com pelo menos uma classificação|Contagem|Média|Indica o número de ativos com pelo menos uma classificação de tag.|ResourceId|
|ScanCancelado|Scan cancelado|Contagem|Total|Indica o número de exames cancelados.|ResourceId|
|ScanConcluído|Varredura concluída|Contagem|Total|Indica o número de varreduras concluídas com sucesso.|ResourceId|
|ScanFailed|Falha na varredura|Contagem|Total|Indica que o número de exames falhou.|ResourceId|
|ScanTimeTaken|Escaneie o tempo tomado|Segundos|Total|Indica o tempo total de varredura em segundos.|ResourceId|




## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|Contagem|Total|ListenerConnections bem-sucedido para o Microsoft.Relay.|EntityName,OperationResult|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Contagem|Total|ClientError no ListenerConnections para o Microsoft.Relay.|EntityName,OperationResult|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Contagem|Total|ServerError no ListenerConnections para o Microsoft.Relay.|EntityName,OperationResult|
|SenderConnections-Success|SenderConnections-Success|Contagem|Total|SenderConnections bem-sucedido para o Microsoft.Relay.|EntityName,OperationResult|
|SenderConnections-ClientError|SenderConnections-ClientError|Contagem|Total|ClientError no SenderConnections para o Microsoft.Relay.|EntityName,OperationResult|
|SenderConnections-ServerError|SenderConnections-ServerError|Contagem|Total|ServerError no SenderConnections para o Microsoft.Relay.|EntityName,OperationResult|
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
|SuccessfulRequests|Solicitações bem sucedidas|Contagem|Total|Total de solicitações bem-sucedidas para um namespace|EntityName,OperationResult|
|ServerErrors|Erros do Servidor.|Contagem|Total|Erros do Servidor para o Microsoft.ServiceBus.|EntityName,OperationResult|
|UserErrors|Erros de Usuário.|Contagem|Total|Erros de Usuário para o Microsoft.ServiceBus.|EntityName,OperationResult|
|ThrottledRequests|Solicitações Limitadas.|Contagem|Total|Solicitações Limitadas para o Microsoft.ServiceBus.|EntityName,OperationResult|
|IncomingRequests|Solicitações de entrada|Contagem|Total|Solicitações de Entrada para o Microsoft.ServiceBus.|EntityName|
|IncomingMessages|Mensagens de entrada|Contagem|Total|Mensagens de Entrada para Microsoft.ServiceBus.|EntityName|
|OutgoingMessages|Mensagens de saída|Contagem|Total|Mensagens de Saída para o Microsoft.ServiceBus.|EntityName|
|ActiveConnections|ActiveConnections|Contagem|Total|Conexões Totalmente Ativas para o Microsoft.ServiceBus.|Nenhum|
|ConnectionsOpened|Conexões Abertas.|Contagem|Média|Conexões Abertas para o Microsoft.ServiceBus.|EntityName|
|ConnectionsClosed|Conexões Fechadas.|Contagem|Média|Conexões Fechadas para o Microsoft.ServiceBus.|EntityName|
|Tamanho|Tamanho|Bytes|Média|Tamanho de uma fila/tópico em bytes.|EntityName|
|Mensagens|Contagem de mensagens em uma fila/tópico.|Contagem|Média|Contagem de mensagens em uma fila/tópico.|EntityName|
|ActiveMessages|Contagem de mensagens em uma fila/tópico.|Contagem|Média|Contagem de mensagens em uma fila/tópico.|EntityName|
|DeadletteredMessages|Contagem de mensagens com letras mortas em uma fila/tópico.|Contagem|Média|Contagem de mensagens com letras mortas em uma fila/tópico.|EntityName|
|Mensagens agendadas|Contagem de mensagens agendadas em uma fila/tópico.|Contagem|Média|Contagem de mensagens agendadas em uma fila/tópico.|EntityName|
|NamespaceCpuUso|CPU|Porcentagem|Máximo|Métrica de uso da CPU de namespace premium do ponto de serviço.|Réplica|
|NamespaceMemoryUso|Uso de Memória|Porcentagem|Máximo|Métrica de uso da memória namespace premium do barramento de serviço.|Réplica|
|CPUXNS|CPU (Preterida)|Porcentagem|Máximo|Métrica de uso da CPU de namespace premium do ponto de serviço. Esta métrica é desprecificada. Por favor, use a métrica da CPU (NamespaceCpuUsage) em vez disso.|Réplica|
|WSXNS|Uso da memória (preterido)|Porcentagem|Máximo|Métrica de uso da memória namespace premium do barramento de serviço. Essa métrica foi preterida. Por favor, use a métrica Uso de memória (NamespaceMemoryUsage) em vez disso.|Réplica|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/aplicativos

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Cpu alocada|Cpu alocada|Contagem|Média|Cpu alocada neste recipiente em miliscores|Nome do aplicativo,nome de serviço,nome de códigodenome,servicereplicaName|
|Memória alocada|Memória alocada|Bytes|Média|Memória alocada a este recipiente em MB|Nome do aplicativo,nome de serviço,nome de códigodenome,servicereplicaName|
|Cpu real|Cpu real|Contagem|Média|Uso real da CPU em milis cores|Nome do aplicativo,nome de serviço,nome de códigodenome,servicereplicaName|
|Memória real|Memória real|Bytes|Média|Uso real da memória em MB|Nome do aplicativo,nome de serviço,nome de códigodenome,servicereplicaName|
|CpuUtilização|CpuUtilização|Porcentagem|Média|Utilização da CPU para este contêiner como porcentagem de AllocatedCpu|Nome do aplicativo,nome de serviço,nome de códigodenome,servicereplicaName|
|MemóriaUtilização|MemóriaUtilização|Porcentagem|Média|Utilização da CPU para este contêiner como porcentagem de AllocatedCpu|Nome do aplicativo,nome de serviço,nome de códigodenome,servicereplicaName|
|Status do aplicativo|Status do aplicativo|Contagem|Média|Status do aplicativo de malha de malha de malha de malha de malha de malha de malha de serviço|Nome do aplicativo,status|
|Status de serviço|Status de serviço|Contagem|Média|Estado de Saúde de um serviço em service fabric mesh aplicativo|Nome do aplicativo,status,nome de serviço|
|ServiceReplicaStatus|ServiceReplicaStatus|Contagem|Média|Estado de saúde de uma réplica de serviço no aplicativo Service Fabric Mesh|Nome do aplicativo,status,nome do serviço,servicereplicaname|
|Status do contêiner|Status do contêiner|Contagem|Média|Status do contêiner no aplicativo Service Fabric Mesh|Nome do aplicativo,nome de serviço,nome de códigodenome,servicereplicaName,status|
|Contagem de reinicialização|Contagem de reinicialização|Contagem|Média|Contagem de reinicialização de um contêiner no aplicativo Service Fabric Mesh|Nome do aplicativo,status,nome de serviço,servicereplicaname,codinomePacotenome|

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
|log_write_percent|Porcentagem de E/S de log|Porcentagem|Média|Percentual de IO de log. Não aplicável a data warehouses.|Nenhum|
|dtu_consumption_percent|Porcentagem de DTU|Porcentagem|Média|Porcentagem de DTU. Aplica-se a bancos de dados baseados em DTU.|Nenhum|
|armazenamento|Espaço de dados usado|Bytes|Máximo|Espaço de dados usado. Não aplicável a data warehouses.|Nenhum|
|connection_successful|Conexões bem sucedidas|Contagem|Total|Conexões bem sucedidas|Nenhum|
|connection_failed|Conexões com falha|Contagem|Total|Conexões com falha|Nenhum|
|blocked_by_firewall|Bloqueado pelo firewall|Contagem|Total|Bloqueado pelo firewall|Nenhum|
|deadlock|Deadlocks|Contagem|Total|Deadlocks. Não aplicável a data warehouses.|Nenhum|
|storage_percent|Espaço de dados usado por cento|Porcentagem|Máximo|O espaço de dados foi usado por cento. Não aplicável a data warehouses ou bancos de dados de hiperescala.|Nenhum|
|xtp_storage_percent|Percentual de armazenamento do OLTP na memória|Porcentagem|Média|Porcentagem de armazenamento OLTP na memória. Não aplicável a data warehouses.|Nenhum|
|workers_percent|Porcentagem de funcionários|Porcentagem|Média|Percentual de trabalhadores. Não aplicável a data warehouses.|Nenhum|
|sessions_percent|Porcentagem de sessões|Porcentagem|Média|Porcentagem de sessões. Não aplicável a data warehouses.|Nenhum|
|dtu_limit|Limite de DTU|Contagem|Média|Limite DTU. Aplica-se a bancos de dados baseados em DTU.|Nenhum|
|dtu_used|DTU usado|Contagem|Média|DTU usado. Aplica-se a bancos de dados baseados em DTU.|Nenhum|
|cpu_limit|Limite da CPU|Contagem|Média|Limite da CPU. Aplica-se a bancos de dados baseados em vCore.|Nenhum|
|cpu_used|CPU usada|Contagem|Média|CPU usada. Aplica-se a bancos de dados baseados em vCore.|Nenhum|
|dwu_limit|Limite de DWU|Contagem|Máximo|Limite DWU. Aplica-se apenas a data warehouses.|Nenhum|
|dwu_consumption_percent|Porcentagem de DWU|Porcentagem|Máximo|Porcentagem de DWU. Aplica-se apenas a data warehouses.|Nenhum|
|dwu_used|DWU usado|Contagem|Máximo|DWU usado. Aplica-se apenas a data warehouses.|Nenhum|
|cache_hit_percent|Percentual de ocorrência no cache|Porcentagem|Máximo|Porcentagem de acerto de cache. Aplica-se apenas a data warehouses.|Nenhum|
|cache_used_percent|Percentual de cache usado|Porcentagem|Máximo|Porcentagem utilizada em cache. Aplica-se apenas a data warehouses.|Nenhum|
|sqlserver_process_core_percent|Porcentagem do núcleo do processo do SQL Server|Porcentagem|Máximo|Porcentagem de uso da CPU para o processo sql server, medida pelo sistema operacional.|Nenhum|
|sqlserver_process_memory_percent|Porcentagem de memória do processo do SQL Server|Porcentagem|Máximo|Porcentagem de uso de memória para o processo SQL Server, medida pelo sistema operacional.|Nenhum|
|tempdb_data_size|Tamanho do arquivo de dados tempdb Kilobytes|Contagem|Máximo|Tamanho do arquivo de dados tempdb Kilobytes. Não aplicável a data warehouses.|Nenhum|
|tempdb_log_size|Tamanho do arquivo de log tempdb Kilobytes|Contagem|Máximo|Tamanho do arquivo de log tempdb Kilobytes. Não aplicável a data warehouses.|Nenhum|
|tempdb_log_used_percent|Registro percentual de tempdb usado|Porcentagem|Máximo|Registro percentual de tempdb usado. Não aplicável a data warehouses.|Nenhum|
|local_tempdb_usage_percent|Porcentagem de local de tempdb|Porcentagem|Média|Porcentagem de temperatura local. Aplica-se apenas a data warehouses.|Nenhum|
|app_cpu_billed|CPU do aplicativo cobrado|Contagem|Total|CPU do aplicativo cobrado. Aplica-se a bancos de dados sem servidor.|Nenhum|
|app_cpu_percent|Porcentagem da CPU do aplicativo|Porcentagem|Média|Porcentagem da CPU do aplicativo. Aplica-se a bancos de dados sem servidor.|Nenhum|
|app_memory_percent|Porcentagem de memória do aplicativo|Porcentagem|Média|Porcentagem de memória do aplicativo. Aplica-se a bancos de dados sem servidor.|Nenhum|
|allocated_data_storage|Espaço alocado de dados|Bytes|Média|Armazenamento de dados alocado. Não aplicável a data warehouses.|Nenhum|
|memory_usage_percent|Porcentagem de memória|Porcentagem|Máximo|Porcentagem de memória. Aplica-se apenas a data warehouses.|Nenhum|
|dw_backup_size_gb|Tamanho do armazenamento de dados|Contagem|Total|O Tamanho do Armazenamento de Dados é composto pelo tamanho de seus dados e pelo registro de transações. A métrica é contada para a parte 'Armazenamento' da sua conta. Aplica-se apenas a data warehouses.|Nenhum|
|dw_snapshot_size_gb|Tamanho do armazenamento de instantâneos|Contagem|Total|Snapshot Storage Size é o tamanho das alterações incrementais capturadas por instantâneos para criar pontos de restauração definidos pelo usuário e automáticos. A métrica é contada para a parte 'Armazenamento' da sua conta. Aplica-se apenas a data warehouses.|Nenhum|
|dw_geosnapshot_size_gb|Tamanho do armazenamento de recuperação de desastres|Contagem|Total|O tamanho do armazenamento de recuperação de desastres é refletido como 'Armazenamento de recuperação de desastres' em sua conta. Aplica-se apenas a data warehouses.|Nenhum|
|wlg_allocation_relative_to_system_percent|Alocação do grupo de carga de trabalho por porcentagem do sistema|Porcentagem|Máximo|Percentual alocado de recursos relativos a todo o sistema por grupo de carga de trabalho. Aplica-se apenas a data warehouses.|WorkloadGroupName,IsUserDefined|
|wlg_allocation_relative_to_wlg_effective_cap_percent|Alocação do grupo de carga de trabalho por porcentagem de recursos de limite|Porcentagem|Máximo|Percentual alocado de recursos relativos aos recursos de limite especificados por grupo de carga de trabalho. Aplica-se apenas a data warehouses.|WorkloadGroupName,IsUserDefined|
|wlg_active_queries|Consultas ativas do grupo de trabalho|Contagem|Total|Consultas ativas dentro do grupo de carga de trabalho. Aplica-se apenas a data warehouses.|WorkloadGroupName,IsUserDefined|
|wlg_queued_queries|Consultas enfileiradas do grupo de trabalho|Contagem|Total|Consultas enfileiradas dentro do grupo de carga de trabalho. Aplica-se apenas a data warehouses.|WorkloadGroupName,IsUserDefined|
|active_queries|Consultas ativas|Contagem|Total|Consultas ativas em todos os grupos de carga de trabalho. Aplica-se apenas a data warehouses.|Nenhum|
|queued_queries|Consultas em fila|Contagem|Total|Consultas enfileiradas em todos os grupos de carga de trabalho. Aplica-se apenas a data warehouses.|Nenhum|
|wlg_active_queries_timeouts|Tempos de tempo out sem saída de tempo de consulta de grupo de carga de trabalho|Contagem|Total|Consultas que foram cronometradas para o grupo de carga de trabalho. Aplica-se apenas a data warehouses.|WorkloadGroupName,IsUserDefined|
|wlg_effective_min_resource_percent|Porcentagem de recursos min eficaz|Porcentagem|Máximo|Percentual mínimo de recursos reservados e isolados para o grupo de carga horária, levando em conta o mínimo de nível de serviço. Aplica-se apenas a data warehouses.|WorkloadGroupName,IsUserDefined|
|wlg_effective_cap_resource_percent|Porcentagem de recursos de limite efetivo|Porcentagem|Máximo|Um limite rígido na porcentagem de recursos permitido para o grupo de carga de trabalho, levando em conta o Percentual efetivo de recursos min alocado para outros grupos de carga de trabalho. Aplica-se apenas a data warehouses.|WorkloadGroupName,IsUserDefined|
|full_backup_size_bytes|Tamanho total do armazenamento de backup|Bytes|Máximo|Tamanho de armazenamento de backup completo cumulativo. Aplica-se a bancos de dados baseados em vCore. Não aplicável a bancos de dados Hyperscale.|Nenhum|
|diff_backup_size_bytes|Tamanho diferencial de armazenamento de backup|Bytes|Máximo|Tamanho de armazenamento de backup diferencial cumulativo. Aplica-se a bancos de dados baseados em vCore. Não aplicável a bancos de dados Hyperscale.|Nenhum|
|log_backup_size_bytes|Tamanho de armazenamento de backup de log|Bytes|Máximo|Tamanho de armazenamento de backup de log cumulativo. Aplica-se a bancos de dados baseados em vCore e hyperscale.|Nenhum|
|snapshot_backup_size_bytes|Tamanho de armazenamento de backup instantâneo|Bytes|Máximo|Tamanho de armazenamento de backup de instantâneo cumulativo. Aplica-se a bancos de dados Hyperscale.|Nenhum|
|base_blob_size_bytes|Tamanho de armazenamento de bolha base|Bytes|Máximo|Tamanho de armazenamento de bolha base. Aplica-se a bancos de dados Hyperscale.|Nenhum|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|cpu_percent|Percentual de CPU|Porcentagem|Média|Percentual de CPU|Nenhum|
|database_cpu_percent|Percentual de CPU|Porcentagem|Média|Percentual de CPU|DatabaseResourceId|
|physical_data_read_percent|Porcentagem de E/S de dados|Porcentagem|Média|Porcentagem de E/S de dados|Nenhum|
|database_physical_data_read_percent|Porcentagem de E/S de dados|Porcentagem|Média|Porcentagem de E/S de dados|DatabaseResourceId|
|log_write_percent|Porcentagem de E/S de log|Porcentagem|Média|Porcentagem de E/S de log|Nenhum|
|database_log_write_percent|Porcentagem de E/S de log|Porcentagem|Média|Porcentagem de E/S de log|DatabaseResourceId|
|dtu_consumption_percent|Porcentagem de DTU|Porcentagem|Média|Porcentagem de DTU. Aplica-se a piscinas elásticas baseadas em DTU.|Nenhum|
|database_dtu_consumption_percent|Porcentagem de DTU|Porcentagem|Média|Porcentagem de DTU|DatabaseResourceId|
|storage_percent|Espaço de dados usado por cento|Porcentagem|Média|Espaço de dados usado por cento|Nenhum|
|workers_percent|Porcentagem de funcionários|Porcentagem|Média|Porcentagem de funcionários|Nenhum|
|database_workers_percent|Porcentagem de funcionários|Porcentagem|Média|Porcentagem de funcionários|DatabaseResourceId|
|sessions_percent|Porcentagem de sessões|Porcentagem|Média|Porcentagem de sessões|Nenhum|
|database_sessions_percent|Porcentagem de sessões|Porcentagem|Média|Porcentagem de sessões|DatabaseResourceId|
|eDTU_limit|Limite de eDTU|Contagem|Média|limite eDTU. Aplica-se a piscinas elásticas baseadas em DTU.|Nenhum|
|storage_limit|Tamanho máximo dos dados|Bytes|Média|Tamanho máximo dos dados|Nenhum|
|eDTU_used|eDTU usado|Contagem|Média|eDTU usado. Aplica-se a piscinas elásticas baseadas em DTU.|Nenhum|
|database_eDTU_used|eDTU usado|Contagem|Média|eDTU usado|DatabaseResourceId|
|storage_used|Espaço de dados usado|Bytes|Média|Espaço de dados usado|Nenhum|
|database_storage_used|Espaço de dados usado|Bytes|Média|Espaço de dados usado|DatabaseResourceId|
|xtp_storage_percent|Percentual de armazenamento do OLTP na memória|Porcentagem|Média|Percentual de armazenamento do OLTP na memória|Nenhum|
|cpu_limit|Limite da CPU|Contagem|Média|Limite da CPU. Aplica-se a piscinas elásticas baseadas em vCore.|Nenhum|
|database_cpu_limit|Limite da CPU|Contagem|Média|Limite da CPU|DatabaseResourceId|
|cpu_used|CPU usada|Contagem|Média|CPU usada. Aplica-se a piscinas elásticas baseadas em vCore.|Nenhum|
|database_cpu_used|CPU usada|Contagem|Média|CPU usada|DatabaseResourceId|
|sqlserver_process_core_percent|Porcentagem do núcleo do processo do SQL Server|Porcentagem|Máximo|Porcentagem de uso da CPU para o processo sql server, medida pelo sistema operacional. Aplica-se a piscinas elásticas.|Nenhum|
|sqlserver_process_memory_percent|Porcentagem de memória do processo do SQL Server|Porcentagem|Máximo|Porcentagem de uso de memória para o processo SQL Server, medida pelo sistema operacional. Aplica-se a piscinas elásticas.|Nenhum|
|tempdb_data_size|Tamanho do arquivo de dados tempdb Kilobytes|Contagem|Máximo|Tamanho do arquivo de dados tempdb Kilobytes|Nenhum|
|tempdb_log_size|Tamanho do arquivo de log tempdb Kilobytes|Contagem|Máximo|Tamanho do arquivo de log tempdb Kilobytes|Nenhum|
|tempdb_log_used_percent|Registro percentual de tempdb usado|Porcentagem|Máximo|Registro percentual de tempdb usado|Nenhum|
|allocated_data_storage|Espaço alocado de dados|Bytes|Média|Espaço alocado de dados|Nenhum|
|database_allocated_data_storage|Espaço alocado de dados|Bytes|Média|Espaço alocado de dados|DatabaseResourceId|
|allocated_data_storage_percent|Espaço de dados alocado por cento|Porcentagem|Máximo|Espaço de dados alocado por cento|Nenhum|

## <a name="microsoftsqlservers"></a>Microsoft.Sql/servers

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|dtu_consumption_percent|Porcentagem de DTU|Porcentagem|Média|Porcentagem de DTU|ElasticPoolResourceId|
|database_dtu_consumption_percent|Porcentagem de DTU|Porcentagem|Média|Porcentagem de DTU|DatabaseResourceId,ElasticPoolResourceId|
|storage_used|Espaço de dados usado|Bytes|Média|Espaço de dados usado|ElasticPoolResourceId|
|database_storage_used|Espaço de dados usado|Bytes|Média|Espaço de dados usado|DatabaseResourceId,ElasticPoolResourceId|
|dtu_used|DTU usado|Contagem|Média|DTU usado|DatabaseResourceId|

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
|Transactions|Transactions|Contagem|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|Tipo de resposta,GeoType,ApiName,Autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|GeoType,ApiName,Autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável.|GeoType,ApiName,Autenticação|
|SuccessServerLatency|Latência de Servidor Com Sucesso|Milissegundos|Média|A latência média usada pelo Armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Esse valor não inclui a latência de rede especificada em AverageE2ELatency.|GeoType,ApiName,Autenticação|
|SuccessE2ELatency|Latência de E2E com Sucesso|Milissegundos|Média|A latência média de ponta a ponta das solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|GeoType,ApiName,Autenticação|
|Disponibilidade|Disponibilidade|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|GeoType,ApiName,Autenticação|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BlobCapacity|Capacidade do Blob|Bytes|Média|A quantidade de armazenamento usada pelo serviço Blob da conta de armazenamento em bytes.|BlobType,Tier|
|BlobCount|Contagem de Blobs|Contagem|Média|O número de Blob no serviço Blob da conta de armazenamento.|BlobType,Tier|
|ContainerCount|Contagem de Contêineres de Blobs|Contagem|Média|O número de contêineres no serviço Blob da conta de armazenamento.|Nenhum|
|IndexCapacity|Capacidade de índice|Bytes|Média|A quantidade de armazenamento utilizada pelo Índice ADLS Gen2 (Hierárquico) em bytes.|Nenhum|
|Transactions|Transactions|Contagem|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|Tipo de resposta,GeoType,ApiName,Autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|GeoType,ApiName,Autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável.|GeoType,ApiName,Autenticação|
|SuccessServerLatency|Latência de Servidor Com Sucesso|Milissegundos|Média|A latência média usada pelo Armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Esse valor não inclui a latência de rede especificada em AverageE2ELatency.|GeoType,ApiName,Autenticação|
|SuccessE2ELatency|Latência de E2E com Sucesso|Milissegundos|Média|A latência média de ponta a ponta das solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|GeoType,ApiName,Autenticação|
|Disponibilidade|Disponibilidade|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|GeoType,ApiName,Autenticação|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|TableCapacity|Capacidade da Tabela|Bytes|Média|A quantidade de armazenamento usada pelo serviço table da conta de armazenamento em bytes.|Nenhum|
|TableCount|Contagem de Tabelas|Contagem|Média|O número de tabela no serviço de tabela da conta de armazenamento.|Nenhum|
|TableEntityCount|Contagem de Entidades de Tabela|Contagem|Média|O número de entidades de tabela no serviço de Tabela da conta de armazenamento.|Nenhum|
|Transactions|Transactions|Contagem|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|Tipo de resposta,GeoType,ApiName,Autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|GeoType,ApiName,Autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável.|GeoType,ApiName,Autenticação|
|SuccessServerLatency|Latência de Servidor Com Sucesso|Milissegundos|Média|A latência média usada pelo Armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Esse valor não inclui a latência de rede especificada em AverageE2ELatency.|GeoType,ApiName,Autenticação|
|SuccessE2ELatency|Latência de E2E com Sucesso|Milissegundos|Média|A latência média de ponta a ponta das solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|GeoType,ApiName,Autenticação|
|Disponibilidade|Disponibilidade|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|GeoType,ApiName,Autenticação|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|FileCapacity|Capacidade do Arquivo|Bytes|Média|A quantidade de armazenamento usada pelo serviço arquivo da conta de armazenamento em bytes.|FileShare|
|FileCount|Contagem de Arquivos|Contagem|Média|O número de arquivos no serviço arquivo da conta de armazenamento.|FileShare|
|FileShareCount|Contagem de Compartilhamentos de Arquivos|Contagem|Média|O número de compartilhamentos de arquivos no serviço de arquivo da conta de armazenamento.|Nenhum|
|FileShareSnapshotContag|Contagem de instantâneos de compartilhamento de arquivos|Contagem|Média|O número de instantâneos presentes na parte no Serviço de Arquivos da conta de armazenamento.|FileShare|
|FileShareSnapshotSize|Tamanho do instantâneo do compartilhamento de arquivos|Bytes|Média|A quantidade de armazenamento usada pelos snapshots no serviço Arquivo da conta de armazenamento em bytes.|FileShare|
|FileShareQuota|Tamanho da cota de compartilhamento de arquivos|Bytes|Média|O limite superior na quantidade de armazenamento que pode ser usado pelo Azure Files Service em bytes.|FileShare|
|Transactions|Transactions|Contagem|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|Tipo de resposta,GeoType,ApiName,Autenticação,FileShare|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|GeoType,ApiName,Autenticação,FileShare|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável.|GeoType,ApiName,Autenticação,FileShare|
|SuccessServerLatency|Latência de Servidor Com Sucesso|Milissegundos|Média|A latência média usada pelo Armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Esse valor não inclui a latência de rede especificada em AverageE2ELatency.|GeoType,ApiName,Autenticação,FileShare|
|SuccessE2ELatency|Latência de E2E com Sucesso|Milissegundos|Média|A latência média de ponta a ponta das solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|GeoType,ApiName,Autenticação,FileShare|
|Disponibilidade|Disponibilidade|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|GeoType,ApiName,Autenticação,FileShare|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|QueueCapacity|Capacidade da Fila|Bytes|Média|A quantidade de armazenamento usada pelo serviço de fila da conta de armazenamento em bytes.|Nenhum|
|QueueCount|Contagem de Filas|Contagem|Média|O número de filas no serviço de fila da conta de armazenamento.|Nenhum|
|QueueMessageCount|Contagem de Mensagens da Fila|Contagem|Média|O número aproximado de mensagens de fila no serviço de fila da conta de armazenamento.|Nenhum|
|Transactions|Transactions|Contagem|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|Tipo de resposta,GeoType,ApiName,Autenticação|
|Entrada|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|GeoType,ApiName,Autenticação|
|Saída|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável.|GeoType,ApiName,Autenticação|
|SuccessServerLatency|Latência de Servidor Com Sucesso|Milissegundos|Média|A latência média usada pelo Armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Esse valor não inclui a latência de rede especificada em AverageE2ELatency.|GeoType,ApiName,Autenticação|
|SuccessE2ELatency|Latência de E2E com Sucesso|Milissegundos|Média|A latência média de ponta a ponta das solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|GeoType,ApiName,Autenticação|
|Disponibilidade|Disponibilidade|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|GeoType,ApiName,Autenticação|





## <a name="microsoftstoragecachecaches"></a>Microsoft.StorageCache/caches

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ClientIOPS|IOPS de cliente total|Contagem|Média|A taxa de operações de arquivos do cliente processadas pelo Cache.|Nenhum|
|Perfilabilidade do cliente|Latência média do cliente|Milissegundos|Média|Latência média das operações de arquivo do cliente para o Cache de Armazenamento.|Nenhum|
|ClientReadIOPS|Cliente ler IOPS|CountPerSecond|Média|Operações de leitura do cliente por segundo.|Nenhum|
|ClientReadThroughput|Throughput de leitura média de cache|BytesPerSecond|Média|Taxa de transferência de dados de leitura do cliente.|Nenhum|
|ClientWriteIOPS|IOPS de gravação de cliente|CountPerSecond|Média|Operações de gravação do cliente por segundo.|Nenhum|
|ClientWriteThroughput|Throughput médio de gravação de cache|BytesPerSecond|Média|Taxa de transferência de dados de gravação do cliente.|Nenhum|
|ClienteMetadataReadIOPS|Metadados do cliente ler IOPS|CountPerSecond|Média|A taxa de operações de arquivos do cliente enviadas ao Cache, excluindo leituras de dados, que não modificam o estado persistente.|Nenhum|
|ClienteMetadataWriteIOPS|IOPS de gravação de metadados do cliente|CountPerSecond|Média|A taxa de operações de arquivos do cliente enviadas ao Cache, excluindo gravações de dados, que modificam o estado persistente.|Nenhum|
|ClientLockIOPS|IOPS de bloqueio de cliente|CountPerSecond|Média|Operações de bloqueio de arquivos do cliente por segundo.|Nenhum|
|ArmazenamentoTargetHealth|Saúde alvo de armazenamento|Contagem|Média|Resultados booleanos do teste de conectividade entre os alvos cache e armazenamento.|Nenhum|
|Tempo de atividade|Tempo de atividade|Contagem|Média|Resultados booleanos do teste de conectividade entre o cache e o sistema de monitoramento.|Nenhum|
|TargetIOPS de armazenamento|IOPS de alvo de armazenamento total|Contagem|Média|A taxa de todas as operações de arquivo que o Cache envia para um Determinado StorageTarget.|Alvo de armazenamento|
|ArmazenamentoTargetWriteIOPS|IOPS de gravação de alvo de armazenamento|Contagem|Média|A taxa das operações de gravação de arquivo que o Cache envia para um determinado StorageTarget.|Alvo de armazenamento|
|ArmazenamentoTargetAsyncWriteThroughputput|Throughput de gravação assíncrona de alvo de armazenamento|BytesPerSecond|Média|A taxa de cache asincronizadamente grava dados para um determinado StorageTarget. São gravações oportunistas que não fazem com que os clientes bloqueiem.|Alvo de armazenamento|
|ArmazenamentoTargetSyncWriteThroughputput|Throughput de gravação síncrona de alvo de armazenamento|BytesPerSecond|Média|A taxa de cache grava sincronizadamente dados para um determinado StorageTarget. São gravações que fazem com que os clientes bloqueiem.|Alvo de armazenamento|
|ArmazenamentoTargetTotalWriteWriteput|Throughput total de gravação de armazenamentotarget|BytesPerSecond|Média|A taxa total que o Cache grava dados para um determinado StorageTarget.|Alvo de armazenamento|
|Gerenciamento de segmentação de armazenamento|Latência de destino de armazenamento|Milissegundos|Média|A latência média de ida e volta de todas as operações de arquivo que o Cache envia para um StorageTarget partricular.|Alvo de armazenamento|
|ArmazenamentoTargetMetadataReadIOPS|ArmazenamentoMetadados target ler IOPS|CountPerSecond|Média|A taxa de operações de arquivo que não modificam o estado persistente e excluindo a operação de leitura, que o Cache envia para um Determinado StorageTarget.|Alvo de armazenamento|
|ArmazenamentoTargetMetadataWriteIOPS|ArmazenamentoTarget Metadata Write IOPS|CountPerSecond|Média|A taxa de operações de arquivos que modificam o estado persistente e excluindo a operação de gravação, que o Cache envia para um Determinado StorageTarget.|Alvo de armazenamento|
|Alvo de armazenamentoReadIOPS|Alvo de armazenamento Ler IOPS|CountPerSecond|Média|A taxa de operações de leitura de arquivos que o Cache envia para um determinado StorageTarget.|Alvo de armazenamento|
|ArmazenamentoTargetReadAheadThroughput|ArmazenamentoTarget Ler throughput à frente|BytesPerSecond|Média|A taxa de cache lê dados oportunicamente do StorageTarget.|Alvo de armazenamento|
|ArmazenamentoTargetPreenchimentodepreenchimentoput|Throughput de preenchimento de destino de armazenamento|BytesPerSecond|Média|A taxa que o cache lê dados do StorageTarget para lidar com uma falha de cache.|Alvo de armazenamento|
|ArmazenamentoTargetTotalReadThroughput|Throughput total de leitura do alvo de armazenamento|BytesPerSecond|Média|A taxa total que o cache lê dados de um determinado StorageTarget.|Alvo de armazenamento|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storageSync/storageSyncServices

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Resultado da sessão de sincronização|Contagem|Média|Métrica que registra um valor de 1 cada vez que o Ponto Final do Servidor completa com sucesso uma sessão de sincronização com o ponto final da nuvem|SINCRONIANome do grupo,Nome do ponto final do servidor,SyncDirection|
|ArmazenamentoSincronizaSCSOSistemaDecandidatar-SEAContagemdesCtivaSContagem regressiva|Arquivos Sincronizados|Contagem|Total|Contagem de arquivos sincronizados|SINCRONIANome do grupo,Nome do ponto final do servidor,SyncDirection|
|ArmazenamentoSincronizasOSincronizaÇãoDeitensdoper-erroContagemdectosContagem regressiva|Arquivos não sincronizando|Contagem|Total|Contagem de arquivos não sincronizado|SINCRONIANome do grupo,Nome do ponto final do servidor,SyncDirection|
|ArmazenamentoSyncBatchTransferredFileBytes|Bytes sincronizados|Bytes|Total|Tamanho total do arquivo transferido para sessões de sincronização|SINCRONIANome do grupo,Nome do ponto final do servidor,SyncDirection|
|ArmazenamentoSyncServerHeartbeat|Status on-line do servidor|Contagem|Máximo|Métrica que registra um valor de 1 cada vez que o servidor resigtered registra com sucesso um batimento cardíaco com o Ponto final da nuvem|ServerName|
|ArmazenamentoSyncRecallIOTotalSizeBytes|Recall da camada de nuvem|Bytes|Total|Tamanho total dos dados lembrados pelo servidor|ServerName|
|ArmazenamentoSyncRecalledTotalNetworkBytes|Tamanho de recall de hierarquiagem de nuvem|Bytes|Total|Tamanho dos dados em recall|SincronizarNome de grupo,nome do servidor|
|ArmazenamentoSyncRecallThroughputBytesPerSecond|Throughput de recall de hierarquidade na nuvem|BytesPerSecond|Média|Tamanho do throughput de recall de dados|SincronizarNome de grupo,nome do servidor|
|ArmazenamentoSyncRecalledNetworkBytesByApplication|Tamanho de recall de hierarquiagem em nuvem por aplicativo|Bytes|Total|Tamanho dos dados lembrados por aplicativo|SinCRONIANome do grupo,Nome do servidor,Nome do aplicativo|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>microsoft.storageSync/storageSyncServices/syncGroups

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|SincronizaçãoGrupoSincronizaçãoSessãoAplicadacontaminaçãoArquivosContagemde|Arquivos Sincronizados|Contagem|Total|Contagem de arquivos sincronizados|SINCRONIANome do grupo,Nome do ponto final do servidor,SyncDirection|
|SincronizaçãoGrupoSincronizaçãoConsultaPer-errocontagemContagemdecência|Arquivos não sincronizando|Contagem|Total|Contagem de arquivos não sincronizado|SINCRONIANome do grupo,Nome do ponto final do servidor,SyncDirection|
|SyncGroupBatchTransferredFileBytes|Bytes sincronizados|Bytes|Total|Tamanho total do arquivo transferido para sessões de sincronização|SINCRONIANome do grupo,Nome do ponto final do servidor,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>microsoft.storageSync/storageSyncS/syncGroups/serverEndpoints

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ServidorEndpointSyncSessãoderecursosaplicadoSContagemregressiva|Arquivos Sincronizados|Contagem|Total|Contagem de arquivos sincronizados|Nome do ponto final do servidor,SyncDirection|
|ServidorEndpointSyncConsultaPer-erroproblemaContagemdectosContagem regressiva|Arquivos não sincronizando|Contagem|Total|Contagem de arquivos não sincronizado|Nome do ponto final do servidor,SyncDirection|
|ServerEndpointBatchTransferBytes|Bytes sincronizados|Bytes|Total|Tamanho total do arquivo transferido para sessões de sincronização|Nome do ponto final do servidor,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>microsoft.storageSync/storageSyncServices/registeredServers

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|Heartbeat do servidor|Status on-line do servidor|Contagem|Máximo|Métrica que registra um valor de 1 cada vez que o servidor resigtered registra com sucesso um batimento cardíaco com o Ponto final da nuvem|Recurso do servidor,Nome do servidor|
|ServerRecallIOTotalSizeBytes|Recall da camada de nuvem|Bytes|Total|Tamanho total dos dados lembrados pelo servidor|Recurso do servidor,Nome do servidor|



## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|ResourceUtilization|% de utilização do SU|Porcentagem|Máximo|% de utilização do SU|LogicalName,PartitionId|
|InputEvents|Eventos de entrada|Contagem|Total|Eventos de entrada|LogicalName,PartitionId|
|InputEventBytes|Bytes de evento de entrada|Bytes|Total|Bytes de evento de entrada|LogicalName,PartitionId|
|LateInputEvents|Eventos de entrada atrasados|Contagem|Total|Eventos de entrada atrasados|LogicalName,PartitionId|
|OutputEvents|Eventos de saída|Contagem|Total|Eventos de saída|LogicalName,PartitionId|
|ConversionErrors|Erros de conversão de dados|Contagem|Total|Erros de conversão de dados|LogicalName,PartitionId|
|Errors|Erros de runtime|Contagem|Total|Erros de runtime|LogicalName,PartitionId|
|DroppedOrAdjustedEvents|Eventos fora de ordem|Contagem|Total|Eventos fora de ordem|LogicalName,PartitionId|
|AMLCalloutRequests|Solicitações de função|Contagem|Total|Solicitações de função|LogicalName,PartitionId|
|AMLCalloutFailedRequests|Solicitações de função com falha|Contagem|Total|Solicitações de função com falha|LogicalName,PartitionId|
|AMLCalloutInputEvents|Eventos de função|Contagem|Total|Eventos de função|LogicalName,PartitionId|
|DeserializationError|Erros de desserialização de entrada|Contagem|Total|Erros de desserialização de entrada|LogicalName,PartitionId|
|EarlyInputEvents|Eventos de Entrada Antecipados|Contagem|Total|Eventos de Entrada Antecipados|LogicalName,PartitionId|
|OutputWatermarkDelaySeconds|Atraso de Marca-d'água|Segundos|Máximo|Atraso de Marca-d'água|LogicalName,PartitionId|
|InputEventsSourcesBacklogged|Eventos de Entrada Acumulados|Contagem|Máximo|Eventos de Entrada Acumulados|LogicalName,PartitionId|
|InputEventsSourcesPerSegundo|Fontes de Entrada Recebidas|Contagem|Total|Fontes de Entrada Recebidas|LogicalName,PartitionId|

## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse/espaços de trabalho

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|OrchestrationPipelineÉ|As corridas de gasodutos terminaram|Contagem|Total|Contagem de corridas de gasodutos de orquestração que conseguiram, falharam ou foram canceladas|Resultado,Tipo de falha,pipeline|
|OrchestrationActivitysEnded|As corridas de atividade saem|Contagem|Total|Contagem de atividades de orquestração que tiveram sucesso, falharam ou foram canceladas|Resultado,Tipo de falha,atividade,tipo de atividade,pipeline|
|OrchestrationTriggersEnded|Os gatilhos terminaram|Contagem|Total|Contagem de gatilhos de orquestração que conseguiram, falharam ou foram cancelados|Resultado,Tipo de falha,gatilho|
|SqlonDemandTentativas de login|Tentativas de login|Contagem|Total|Contagem de tentativas de login que sucederam ou falharam|Result|
|SqlonDemandQueriesended|Consultas encerradas|Contagem|Total|Contagem de consultas que tiveram sucesso, falharam ou foram canceladas|Result|
|SQLOnDemandQueryProcessedBytes|Dados processados|Bytes|Total|Quantidade de dados processados por consultas|Nenhum|

## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft.Synapse/workspaces/bigDataPools

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|SparkJobsEnded|Aplicações encerradas|Contagem|Total|Contagem de aplicações encerradas|JobType,JobResult|
|Capacidade de cores|Capacidade dos núcleos|Contagem|Máximo|Capacidade dos núcleos|Nenhum|
|Capacidade de memóriaGB|Capacidade de memória (GB)|Contagem|Máximo|Capacidade de memória (GB)|Nenhum|

## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft.Synapse/workspaces/sqlPools

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|DWULimit|Limite de DWU|Contagem|Máximo|Objetivo de nível de serviço do pool SQL|Nenhum|
|DWUUsed|DWU usado|Contagem|Máximo|Representa uma representação de alto nível de uso em todo o pool SQL. Medido pelo limite DWU * percentual de DWU|Nenhum|
|DWUUsedPercent|DWU usou porcentagem|Porcentagem|Máximo|Representa uma representação de alto nível de uso em todo o pool SQL. Medido tomando o máximo entre a porcentagem da CPU e a porcentagem de IO de Dados|Nenhum|
|ConexõesBloqueadasPorFirewall|Conexões bloqueadas por firewall|Contagem|Total|Contagem de conexões bloqueadas por regras de firewall. Revisite as políticas de controle de acesso para o seu pool SQL e monitore essas conexões se a contagem for alta|Nenhum|
|AdaptativosCacheHitPercent|Porcentagem de acerto de cache adaptativo|Porcentagem|Máximo|Mede o quão bem as cargas de trabalho estão utilizando o cache adaptativo. Use essa métrica com a métrica de porcentagem de acerto de cache para determinar se deve escalar para capacidade adicional ou executar cargas de trabalho para hidratar o cache|Nenhum|
|AdaptiveCacheUsedPercent|Cache adaptativo usado porcentagem|Porcentagem|Máximo|Mede o quão bem as cargas de trabalho estão utilizando o cache adaptativo. Use essa métrica com a métrica percentual usada em cache para determinar se deve escalar para capacidade adicional ou executar cargas de trabalho para hidratar o cache|Nenhum|
|LocalTempDBUsedPercent|Tempdb local usado porcentagem|Porcentagem|Máximo|Utilização temporária local em todos os nós de computação - valores são emitidos a cada cinco minutos|Nenhum|
|MemóriausedPercent|Porcentagem de memória usada|Porcentagem|Máximo|Utilização de memória em todos os nós no pool SQL|Nenhum|
|conexões|conexões|Contagem|Total|Contagem de logins totais no pool SQL|Result|
|WLGActiveQueries|Consultas ativas do grupo de trabalho|Contagem|Total|As consultas ativas dentro do grupo de carga de trabalho. Usando esta métrica não filtrada e não dividida exibe todas as consultas ativas em execução no sistema|isuserdefined,WorkloadGroup|
|WLGActiveQueriesTimeouts|Tempos de tempo out sem saída de tempo de consulta de grupo de carga de trabalho|Contagem|Total|Consultas para o grupo de carga de trabalho que se estiveram fora do tempo. Os tempos de consulta relatados por essa métrica são apenas uma vez que a consulta tenha começado a ser executada (ela não inclui o tempo de espera devido ao bloqueio ou espera de recursos)|isuserdefined,WorkloadGroup|
|WLGAllocationBySystemPercent|Alocação do grupo de carga de trabalho por porcentagem do sistema|Porcentagem|Máximo|A alocação percentual de recursos relativos a todo o sistema|isuserdefined,WorkloadGroup|
|WLGAllocationByMaxResourcepercent|Alocação do grupo de carga de trabalho por percentual máximo de recursos|Porcentagem|Máximo|Exibe a alocação percentual de recursos em relação ao percentual de recurso de limite efetivo por grupo de carga de trabalho. Esta métrica fornece a utilização efetiva do grupo de carga de trabalho|isuserdefined,WorkloadGroup|
|WLGEffectiveCapResourcepercent|Porcentagem de recursos de limite efetivo|Porcentagem|Máximo|A porcentagem de recurso de limite efetivo para o grupo de carga de trabalho. Se houver outros grupos de carga de trabalho com min_percentage_resource > 0, o effective_cap_percentage_resource é reduzido proporcionalmente|isuserdefined,WorkloadGroup|
|wlg_effective_min_resource_percent|Porcentagem de recursos min eficaz|Porcentagem|Mínimo|A configuração efetiva do percentual de recursos min permitiu considerar o nível de serviço e as configurações do grupo de carga de trabalho. O min_percentage_resource efetivo pode ser ajustado mais alto em níveis de serviço mais baixos|isuserdefined,WorkloadGroup|
|WLGQueuedQueries|Consultas enfileiradas do grupo de trabalho|Contagem|Total|Contagem cumulativa de solicitações enfileiradas após o limite máximo de simultuos foi atingido|isuserdefined,WorkloadGroup|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|IngressReceivedMessages|Mensagens de entrada recebidas|Contagem|Total|Contagem de mensagens lidas de todos os Hubs de Eventos ou origens de evento do Hub IoT|Nenhum|
|IngressReceivedInvalidMessages|Mensagens de entrada inválidas recebidas|Contagem|Total|Contagem de mensagens inválidas lidas de todos os Hubs de Eventos ou origens de evento do Hub IoT|Nenhum|
|IngressReceivedBytes|Bytes de entrada recebidos|Bytes|Total|Contagem de bytes lidos de todas as fontes de evento|Nenhum|
|IngressStoredBytes|Bytes de entrada armazenados|Bytes|Total|Tamanho total de eventos processados com êxito e disponíveis para consulta|Nenhum|
|IngressStoredEvents|Eventos de entrada armazenados|Contagem|Total|Contagem de eventos nivelados processados com êxito e disponíveis para consulta|Nenhum|
|IngressReceivedMessagesTimeLag|Tempo de atraso das mensagens de entrada recebidas|Segundos|Máximo|Diferença entre a hora em que a mensagem é enfileirada na origem do evento e a hora em que ela é processada na entrada|Nenhum|
|IngressReceivedMessagesCountLag|Atraso na contagem das mensagens de entrada recebidas|Contagem|Média|Diferença entre o número de seqüência da última mensagem enfileirada na partição de origem do evento e o número de seqüência de mensagens sendo processadas em Ingress|Nenhum|
|WarmStorageMaxProperties|Propriedades máximas de armazenamento quente|Contagem|Máximo|Número máximo de propriedades utilizadas pelo ambiente para SKU S1/S2 e número máximo de propriedades permitidas pelo Warm Store para PAYG SKU|Nenhum|
|WarmStorageUsedPropriedadesusadas|Propriedades usadas de armazenamento aquecido |Contagem|Máximo|Número de propriedades usadas pelo ambiente para SKU S1/S2 e número de propriedades usadas pela Warm Store para PAYG SKU|Nenhum|



## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|IngressReceivedMessages|Mensagens de entrada recebidas|Contagem|Total|Contagem de mensagens lidas na origem do evento|Nenhum|
|IngressReceivedInvalidMessages|Mensagens de entrada inválidas recebidas|Contagem|Total|Contagem de mensagens inválidas lidas na origem do evento|Nenhum|
|IngressReceivedBytes|Bytes de entrada recebidos|Bytes|Total|Contagem de bytes lidos da fonte de evento|Nenhum|
|IngressStoredBytes|Bytes de entrada armazenados|Bytes|Total|Tamanho total de eventos processados com êxito e disponíveis para consulta|Nenhum|
|IngressStoredEvents|Eventos de entrada armazenados|Contagem|Total|Contagem de eventos nivelados processados com êxito e disponíveis para consulta|Nenhum|
|IngressReceivedMessagesTimeLag|Tempo de atraso das mensagens de entrada recebidas|Segundos|Máximo|Diferença entre a hora em que a mensagem é enfileirada na origem do evento e a hora em que ela é processada na entrada|Nenhum|
|IngressReceivedMessagesCountLag|Atraso na contagem das mensagens de entrada recebidas|Contagem|Média|Diferença entre o número de seqüência da última mensagem enfileirada na partição de origem do evento e o número de seqüência de mensagens sendo processadas em Ingress|Nenhum|
|WarmStorageMaxProperties|Propriedades máximas de armazenamento quente|Contagem|Máximo|Número máximo de propriedades utilizadas pelo ambiente para SKU S1/S2 e número máximo de propriedades permitidas pelo Warm Store para PAYG SKU|Nenhum|
|WarmStorageUsedPropriedadesusadas|Propriedades usadas de armazenamento aquecido |Contagem|Máximo|Número de propriedades usadas pelo ambiente para SKU S1/S2 e número de propriedades usadas pela Warm Store para PAYG SKU|Nenhum|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudMáquinas simples/virtuais

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Disk Read Bytes/Sec|BytesPerSecond|Média|Rendimento médio do disco devido às operações de leitura durante o período amostral.|Nenhum|
|DiskWriteBytesPerSecond|Disk Write Bytes/Sec|BytesPerSecond|Média|Rendimento médio do disco devido às operações de gravação durante o período amostral.|Nenhum|
|Bytes de leitura de disco|Bytes de leitura de disco|Bytes|Total|Rendimento total do disco devido às operações de leitura durante o período amostral.|Nenhum|
|Bytes de gravação de disco|Bytes de gravação de disco|Bytes|Total|Rendimento total do disco devido às operações de gravação durante o período amostral.|Nenhum|
|Operações de leitura de disco|Operações de leitura de disco|Contagem|Total|O número de operações de leitura de IO no período amostral anterior. Observe que essas operações podem ser de tamanho variável.|Nenhum|
|Operações de gravação de disco|Operações de gravação de disco|Contagem|Total|O número de operações de gravação de IO no período de amostra anterior. Observe que essas operações podem ser de tamanho variável.|Nenhum|
|Operações de leitura de disco/Seg|Operações de leitura de disco/Seg|CountPerSecond|Média|O número médio de operações de leitura de IO no período amostral anterior. Observe que essas operações podem ser de tamanho variável.|Nenhum|
|Operações de gravação de disco/Seg|Operações de gravação de disco/Seg|CountPerSecond|Média|O número médio de operações de gravação de IO no período amostral anterior. Observe que essas operações podem ser de tamanho variável.|Nenhum|
|DiskReadLatency|Latência de leitura de disco|Milissegundos|Média|Latência total de leitura. A soma do dispositivo e do kernel lê latências.|Nenhum|
|Gerenciamento de disco|Latência de Gravação de disco|Milissegundos|Média|Latência total de escrita. A soma do dispositivo e do kernel escrevem latências.|Nenhum|
|NetworkInBytesPerSecond|Rede em Bytes/Sec|BytesPerSecond|Média|Rendimento médio da rede para tráfego recebido.|Nenhum|
|NetworkOutBytesPerSecond|Rede Out Bytes/Sec|BytesPerSecond|Média|Rendimento médio da rede para tráfego transmitido.|Nenhum|
|Entrada na rede|Entrada na rede|Bytes|Total|Total de throughput de rede para tráfego recebido.|Nenhum|
|Saída da rede|Saída da rede|Bytes|Total|Rendimento total da rede para tráfego transmitido.|Nenhum|
|Memóriausada|Memória usada|Bytes|Média|A quantidade de memória da máquina que está em uso pela VM.|Nenhum|
|Memóriaconcedida|Memória concedida|Bytes|Média|A quantidade de memória que foi concedida ao VM pelo host. A memória não é concedida ao host até que ele seja tocado uma vez e a memória concedida pode ser trocada ou reparada se o VMkernel precisar da memória.|Nenhum|
|MemóriaAtiva|Memória Ativa|Bytes|Média|A quantidade de memória usada pelo VM na última pequena janela de tempo. Este é o número "verdadeiro" de quanta memória a VM precisa atualmente. Memória adicional não utilizada pode ser trocada ou trocada sem impacto no desempenho do hóspede.|Nenhum|
|Porcentagem de CPU|Porcentagem de CPU|Porcentagem|Média|A utilização da CPU. Esse valor é relatado com 100% representando todos os núcleos do processador no sistema. Como exemplo, uma VM de duas vias usando 50% de um sistema de quatro núcleos está completamente usando dois núcleos.|Nenhum|
|PorcentagemCpuReady|Porcentagem de CPU pronta|Milissegundos|Total|Tempo pronto é o tempo gasto esperando que as CPU(s) estejam disponíveis no intervalo de atualização passado.|Nenhum|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|CpuPercentage|Percentual de CPU|Porcentagem|Média|Percentual de CPU|Instância|
|MemoryPercentage|Porcentagem de Memória|Porcentagem|Média|Porcentagem de Memória|Instância|
|DiskQueueLength|Tamanho da fila do disco|Contagem|Média|Tamanho da fila do disco|Instância|
|HttpQueueLength|Tamanho da Fila de Http|Contagem|Média|Tamanho da Fila de Http|Instância|
|BytesReceived|Entrada de Dados|Bytes|Total|Entrada de Dados|Instância|
|BytesSent|Saída de dados|Bytes|Total|Saída de dados|Instância|
|TcpSynSent|TCP Syn Enviado|Contagem|Média|TCP Syn Enviado|Instância|
|TcpSynReceived|TCP Syn Recebido|Contagem|Média|TCP Syn Recebido|Instância|
|TcpEstabelecido|TCP Estabelecido|Contagem|Média|TCP Estabelecido|Instância|
|TcpFinWait1|TCP Fin Wait 1|Contagem|Média|TCP Fin Wait 1|Instância|
|TcpFinWait2|TCP Fin Wait 2|Contagem|Média|TCP Fin Wait 2|Instância|
|TcpClosing|Fechamento do TCP|Contagem|Média|Fechamento do TCP|Instância|
|TcpCloseWait|TCP Fechar Espera|Contagem|Média|TCP Fechar Espera|Instância|
|TcpLastAck|TCP Último Ack|Contagem|Média|TCP Último Ack|Instância|
|TcpTimeWait|Espera por tempo tcp|Contagem|Média|Espera por tempo tcp|Instância|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (excluindo funções) 

> [!NOTE]
> **O uso do sistema de arquivos** é uma nova métrica que está sendo implementada globalmente, nenhum dado é esperado a menos que você tenha sido listado em branco para visualização privada.

> [!IMPORTANT]
> **O Tempo médio de resposta** será preterido para evitar confusão com agregações métricas. Use **o Tempo** de Resposta como substituto.

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
|ResponseTime|Tempo de resposta|Segundos|Total|Tempo de resposta|Instância|
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
|HealthCheckStatus|Situação de verificação de saúde|Contagem|Média|Situação de verificação de saúde|Instância|
|FileSystemUsage|Uso do sistema de arquivos|Bytes|Média|Uso do sistema de arquivos|Nenhum|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (funções)

> [!NOTE]
> **O uso do sistema de arquivos** é uma nova métrica que está sendo implementada globalmente, nenhum dado é esperado a menos que você tenha sido listado em branco para visualização privada.

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|
|BytesReceived|Entrada de Dados|Bytes|Total|Entrada de Dados|Instância|
|BytesSent|Saída de dados|Bytes|Total|Saída de dados|Instância|
|Http5xx|Erros do Servidor Http|Contagem|Total|Erros do Servidor Http|Instância|
|MemoryWorkingSet|Conjunto de trabalho de memória|Bytes|Média|Conjunto de trabalho de memória|Instância|
|AverageMemoryWorkingSet|Conjunto de trabalho de memória média|Bytes|Média|Conjunto de trabalho de memória média|Instância|
|FunctionExecutionUnits|Unidades de Execução de Função|MB / Milissegundos|Total|[Unidades de Execução de Função](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Instância|
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
|HealthCheckStatus|Situação de verificação de saúde|Contagem|Média|Situação de verificação de saúde|Instância|
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
|HttpResponseTime|Tempo de resposta|Segundos|Média|Tempo de resposta|Instância|
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
|HealthCheckStatus|Situação de verificação de saúde|Contagem|Média|Situação de verificação de saúde|Instância|
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

