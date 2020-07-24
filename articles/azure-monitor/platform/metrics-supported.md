---
title: Métricas compatíveis por tipo de recurso do Azure Monitor
description: Lista de métricas disponíveis para cada tipo de recurso com o Azure Monitor.
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 07/16/2020
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: 81e2abc1b4fd0c540b08f96e6b34c16fca3319f1
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87132009"
---
# <a name="supported-metrics-with-azure-monitor"></a>Métricas compatíveis com o Azure Monitor

> [!NOTE]
> Essa lista é amplamente gerada automaticamente a partir da API REST de métricas Azure Monitor. Qualquer modificação feita nessa lista por meio do GitHub pode ser gravada sem aviso. Contate o autor deste artigo para obter detalhes sobre como fazer atualizações permanentes.

O Azure Monitor fornece várias maneiras de interagir com as métricas, incluindo a criação de gráficos para os mesmos no portal, acessá-las por meio da API REST ou consultá-las usando o PowerShell ou a CLI. 

Este artigo é uma lista completa de todas as métricas de plataforma (ou seja, coletadas automaticamente) atualmente disponíveis com o pipeline de métrica consolidado do Azure Monitor. A lista foi atualizada pela última vez em 27 de março de 2020. As métricas alteradas ou adicionadas após essa data podem não aparecer abaixo. Para consultar e acessar a lista de métricas programaticamente, use a [versão de api 2018-01-01](/rest/api/monitor/metricdefinitions). Outras métricas que não estão nessa lista podem estar disponíveis no portal ou usando APIs herdadas.

As métricas são organizadas por provedores de recursos e tipo de recurso. Para obter uma lista de serviços e os provedores de recursos que pertencem a eles, consulte [provedores de recursos para serviços do Azure](../../azure-resource-manager/management/azure-services-resource-providers.md). 

## <a name="exporting-platform-metrics-to-other-locations"></a>Exportando métricas de plataforma para outros locais

Você pode exportar as métricas de plataforma do pipeline do Azure Monitor para outros locais de uma das duas maneiras.
1. Usar a [API REST de métricas](/rest/api/monitor/metrics/list)
2. Usar [configurações de diagnóstico](diagnostic-settings.md) para rotear métricas de plataforma para 
    - Armazenamento do Azure
    - Logs de Azure Monitor (e, portanto, Log Analytics)
    - Hubs de eventos, que é como você os obtém para sistemas que não são da Microsoft 

O uso de configurações de diagnóstico é a maneira mais fácil de rotear as métricas, mas há algumas limitações: 

- **Alguns não exportáveis** – todas as métricas são exportáveis usando a API REST, mas algumas não podem ser exportadas usando configurações de diagnóstico devido a complexidades no back-end de Azure monitor. A coluna *exportável por meio das configurações de diagnóstico* nas tabelas abaixo listam quais métricas podem ser exportadas dessa maneira.  

- As **métricas multidimensionais** -o envio de métricas multidimensionais para outros locais por meio de configurações de diagnóstico não tem suporte no momento. As métricas com dimensões são exportadas como métricas dimensionais simples, agregadas nos valores da dimensão. *Por exemplo*: a métrica “Mensagens de Entrada” em um Hub de Eventos pode ser explorada e mapeada por nível da fila. No entanto, quando exportada por meio das configurações de diagnóstico, a métrica será representada como todas as mensagens de entrada em todas as filas no Hub de Eventos.

## <a name="guest-os-and-host-os-metrics"></a>Métricas do SO convidado e do sistema operacional do host

> [!WARNING]
> As métricas para o sistema operacional convidado (SO convidado) que é executado em máquinas virtuais do Azure, Service Fabric e serviços de nuvem **não** estão listadas aqui. As métricas do SO convidado devem ser coletadas por meio de um ou mais agentes que são executados no ou como parte do sistema operacional convidado.  As métricas do sistema operacional convidado incluem contadores de desempenho que acompanham o percentual de CPU ou o uso de memória do convidado, que são frequentemente usados para o dimensionamento automático ou o alerta. 
>
> **As métricas do sistema operacional do host estão disponíveis e listadas abaixo.** Eles não são os mesmos. As métricas do sistema operacional do host estão relacionadas à sessão do Hyper-V que hospeda a sessão do sistema operacional convidado. 

> [!TIP]
> A prática recomendada é usar e configurar a [extensão diagnóstico do Azure](diagnostics-extension-overview.md) para enviar métricas de desempenho do sistema operacional convidado para o mesmo banco de dados Azure monitor métrica em que as métricas de plataforma são armazenadas. A extensão roteia as métricas do sistema operacional convidado por meio da API de [métricas personalizada](metrics-custom-overview.md) . Em seguida, você pode enviar um gráfico, alertar e, de outra forma, usar métricas de SO convidado como métricas de plataforma Como alternativa ou também, você pode usar o agente de Log Analytics para enviar métricas do sistema operacional convidado para Azure Monitor logs/Log Analytics. Lá, você pode consultar essas métricas em combinação com dados não métricas. 

Para obter informações adicionais importantes, consulte [visão geral dos agentes de monitoramento](agents-overview.md).    

## <a name="table-formatting"></a>Formatação de tabela

> [!IMPORTANT] 
> Essa atualização mais recente adiciona uma nova coluna e reordenada a métrica para ser alfabética. As informações de adição significam que as tabelas abaixo podem ter uma barra de rolagem horizontal na parte inferior, dependendo da largura da janela do navegador. Se você achar que faltam informações, use a barra de rolagem para ver todo o índice da tabela.


## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|CleanerCurrentPrice|Sim|Memória: Preço atual do limpador|Contagem|Média|Preço atual da memória, $/byte/tempo, normalizado em 1000.|ServerResourceType|
|CleanerMemoryNonshrinkable|Sim|Memória: Memória do limpador não reduzível|Bytes|Média|Quantidade de memória, em bytes, não sujeita a eliminação pelo limpador na tela de fundo.|ServerResourceType|
|CleanerMemoryShrinkable|Sim|Memória: Memória do limpador reduzível|Bytes|Média|Quantidade de memória, em bytes, sujeita a eliminação pelo limpador na tela de fundo.|ServerResourceType|
|CommandPoolBusyThreads|Sim|Threads: Threads ocupados do pool comando|Contagem|Média|Número de threads ocupados no pool de threads de comando.|ServerResourceType|
|CommandPoolIdleThreads|Sim|Threads: Threads ociosos do pool comando|Contagem|Média|Número de threads ociosos no pool de threads de comando.|ServerResourceType|
|CommandPoolJobQueueLength|Sim|Comprimento da fila de trabalho do pool de comando|Contagem|Média|Número de trabalhos na fila do pool de threads de comando.|ServerResourceType|
|CurrentConnections|Sim|Conexão: Conexões atuais|Contagem|Média|Número atual de conexões de cliente estabelecidas.|ServerResourceType|
|CurrentUserSessions|Sim|Sessões de usuário atuais|Contagem|Média|Número atual de sessões de usuário estabelecidas.|ServerResourceType|
|LongParsingBusyThreads|Sim|Threads: Threads ocupados de análise longa|Contagem|Média|Número de threads ocupados no pool de threads de análise longa.|ServerResourceType|
|LongParsingIdleThreads|Sim|Threads: Threads ociosos de análise longa|Contagem|Média|Número de threads ociosos no pool de threads de análise longa.|ServerResourceType|
|LongParsingJobQueueLength|Sim|Threads: Tamanho da fila de trabalhos de análise longa|Contagem|Média|Número de trabalhos na fila do pool de threads de análise longa.|ServerResourceType|
|mashup_engine_memory_metric|Sim|Memória do mecanismo M|Bytes|Média|Uso de memória pelos processos do mecanismo de mashup|ServerResourceType|
|mashup_engine_private_bytes_metric|Sim|Bytes Particulares do Mecanismo M|Bytes|Média|Uso de bytes privados por processos do mecanismo mashup.|ServerResourceType|
|mashup_engine_qpu_metric|Sim|QPU do mecanismo M|Contagem|Média|Uso de QPU por processos de mecanismo de mashup|ServerResourceType|
|mashup_engine_virtual_bytes_metric|Sim|Bytes Virtuais do Mecanismo M|Bytes|Média|Uso de bytes virtuais por processos do mecanismo mashup.|ServerResourceType|
|memory_metric|Sim|Memória|Bytes|Média|Memória. Intervalo de 0 a 25 GB para S1, 0 a 50 GB para S2 e 0 a 100 GB para S4|ServerResourceType|
|memory_thrashing_metric|Sim|Sobrecarga de memória|Porcentagem|Média|Sobrecarga de memória média.|ServerResourceType|
|MemoryLimitHard|Sim|Memória: Limite de memória física|Bytes|Média|Limite de memória física, do arquivo de configuração.|ServerResourceType|
|MemoryLimitHigh|Sim|Memória: Limite de memória superior|Bytes|Média|Limite de memória superior, do arquivo de configuração.|ServerResourceType|
|MemoryLimitLow|Sim|Memória: Limite de memória inferior|Bytes|Média|Limite de memória inferior, do arquivo de configuração.|ServerResourceType|
|MemoryLimitVertiPaq|Sim|Memória: Limite de memória VertiPaq|Bytes|Média|Limite na memória, do arquivo de configuração.|ServerResourceType|
|MemoryUsage|Sim|Memória: Uso de Memória|Bytes|Média|Uso de memória do processo do servidor, como usado no cálculo de preço de memória do limpador. Igual ao contador Processo\PrivateBytes mais o tamanho dos dados mapeados em memória, ignorando qualquer memória mapeada ou alocada pelo mecanismo de análise de memória xVelocity (VertiPaq), além do limite de memória do mecanismo xVelocity.|ServerResourceType|
|private_bytes_metric|Sim|Bytes Particulares|Bytes|Média|Bytes privados.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Sim|Threads: Threads de trabalho de E/S ocupados do pool de processamento|Contagem|Média|Número de threads que executam trabalhos de E/S no pool de threads de processamento.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Sim|Threads: Threads de trabalho não E/S ocupados do pool de processamento|Contagem|Média|Número de threads que executam trabalhos não E/S no pool de threads de processamento.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Sim|Threads: Threads de trabalho de E/S ociosos do pool de processamento|Contagem|Média|Número de threads ociosos para trabalhos de E/S no pool de threads de processamento.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Sim|Threads: Threads de trabalho não E/S ociosos do pool de processamento|Contagem|Média|Número de threads ociosos no pool de threads de processamento dedicado a trabalhos não E/S.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Sim|Threads: Comprimento da fila de trabalho de E/S do pool de processamento|Contagem|Média|Número de trabalhos de E/S na fila do pool de threads de processamento.|ServerResourceType|
|ProcessingPoolJobQueueLength|Sim|Comprimento da fila de trabalho do pool de processamento|Contagem|Média|Número de trabalhos não de E/S na fila do pool de threads de processamento.|ServerResourceType|
|qpu_metric|Sim|QPU|Contagem|Média|QPU. Intervalo de 0 a 100 para S1, 0 a 200 para S2 e 0 a 400 para S4|ServerResourceType|
|QueryPoolBusyThreads|Sim|Threads ocupados do pool de consulta|Contagem|Média|Número de threads ocupados no pool de threads de consulta.|ServerResourceType|
|QueryPoolIdleThreads|Sim|Threads: Threads ociosos do pool de consultas|Contagem|Média|Número de threads ociosos para trabalhos de E/S no pool de threads de processamento.|ServerResourceType|
|QueryPoolJobQueueLength|Sim|Threads: Tamanho da fila de trabalhos do pool consultas|Contagem|Média|Número de trabalhos na fila do pool de threads de consulta.|ServerResourceType|
|Cota|Sim|Memória: Quota|Bytes|Média|Cota de memória atual, em bytes. A cota de memória também é conhecida como uma reserva de memória ou concessão de memória.|ServerResourceType|
|QuotaBlocked|Sim|Memória: Cota bloqueada|Contagem|Média|Número atual de solicitações de cota bloqueadas até que outras cotas de memória sejam liberadas.|ServerResourceType|
|RowsConvertedPerSec|Sim|Processamento: Linhas convertidas por segundo|CountPerSecond|Média|Taxa de linhas convertidas durante o processamento.|ServerResourceType|
|RowsReadPerSec|Sim|Processamento: Linhas lidas por segundo|CountPerSecond|Média|Taxa de linhas lidas de todos os bancos de dados relacionais.|ServerResourceType|
|RowsWrittenPerSec|Sim|Processamento: Linhas gravadas por segundo|CountPerSecond|Média|Taxa de linhas gravadas durante o processamento.|ServerResourceType|
|ShortParsingBusyThreads|Sim|Threads: Threads ocupados de análise resumida|Contagem|Média|Número de threads ocupados no pool de threads de análise resumida.|ServerResourceType|
|ShortParsingIdleThreads|Sim|Threads: Threads ociosos de análise resumida|Contagem|Média|Número de threads ociosos no pool de threads de análise resumida.|ServerResourceType|
|ShortParsingJobQueueLength|Sim|Threads: Tamanho da fila de trabalhos de análise resumida|Contagem|Média|Número de trabalhos na fila do pool de threads de análise resumida.|ServerResourceType|
|SuccessfullConnectionsPerSec|Sim|Conexões bem-sucedidas por segundo|CountPerSecond|Média|Taxa de conclusões de conexão bem-sucedidas.|ServerResourceType|
|TotalConnectionFailures|Sim|Falhas de conexão totais|Contagem|Média|Total de falhas em tentativas de conexão.|ServerResourceType|
|TotalConnectionRequests|Sim|Solicitações de conexão totais|Contagem|Média|Solicitações de conexão totais. Estas são chegadas.|ServerResourceType|
|VertiPaqNonpaged|Sim|Memória: VertiPaq não paginado|Bytes|Média|Bytes de memória bloqueada no conjunto de trabalho para uso pelo mecanismo na memória.|ServerResourceType|
|VertiPaqPaged|Sim|Memória: VertiPaq paginado|Bytes|Média|Bytes de memória paginada em uso para dados na memória.|ServerResourceType|
|virtual_bytes_metric|Sim|Bytes Virtuais|Bytes|Média|Bytes virtuais.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|BackendDuration|Sim|Duração de Solicitações de Back-end|Milissegundos|Média|Duração de solicitações de back-end em milissegundos|Local, Nome do host|
|Capacity|Sim|Capacity|Porcentagem|Média|Métrica de utilização para o serviço ApiManagement|Location|
|Duration|Sim|Duração total de solicitações de gateway|Milissegundos|Média|Duração total de solicitações de gateway em milissegundos|Local, Nome do host|
|EventHubDroppedEvents|Sim|Eventos do EventHub Ignorados|Contagem|Total|Número de eventos ignorados devido ao limite de tamanho de fila atingido|Location|
|EventHubRejectedEvents|Sim|Eventos do EventHub Rejeitados|Contagem|Total|Número de eventos do EventHub rejeitados (configuração incorreta ou não autorizada)|Location|
|EventHubSuccessfulEvents|Sim|Eventos do EventHub Bem-sucedidos|Contagem|Total|Número de eventos de EventHub bem-sucedidos|Location|
|EventHubThrottledEvents|Sim|Eventos do EventHub Restringidos|Contagem|Total|Número de eventos regulados do EventHub|Location|
|EventHubTimedoutEvents|Sim|Eventos do EventHub com Tempo Limite Excedido|Contagem|Total|Número de eventos do EventHub com tempo limite excedido|Location|
|EventHubTotalBytesSent|Sim|Tamanho dos Eventos do EventHub|Bytes|Total|Tamanho total dos eventos do EventHub em bytes|Location|
|EventHubTotalEvents|Sim|Eventos Totais do EventHub|Contagem|Total|Número de eventos enviados ao EventHub|Location|
|EventHubTotalFailedEvents|Sim|Eventos do EventHub com Falha|Contagem|Total|Número de eventos de EventHub com falha|Location|
|FailedRequests|Sim|Solicitações de Gateway com Falha (Preterido)|Contagem|Total|Número de falhas em solicitações de gateway-use a métrica de solicitação de várias dimensões com a dimensão GatewayResponseCodeCategory em vez disso|Local, Nome do host|
|Conectividade|Sim|Status de conectividade de rede de recursos (versão prévia)|Contagem|Média|Status de conectividade de rede de tipos de recursos dependentes do serviço de gerenciamento de API|Local, ResourceType|
|OtherRequests|Sim|Outras Solicitações de Gateway (Preterido)|Contagem|Total|Número de outras solicitações de gateway – use a métrica de solicitação de várias dimensões com a dimensão GatewayResponseCodeCategory em vez disso|Local, Nome do host|
|Requests|Sim|Requests|Contagem|Total|Métricas de solicitação de gateway com várias dimensões|Local, nome do host, LastErrorReason, BackendResponseCode, GatewayResponseCode, BackendResponseCodeCategory, GatewayResponseCodeCategory|
|SuccessfulRequests|Sim|Solicitações de Gateway com Êxito (Preterido)|Contagem|Total|Número de solicitações de gateway bem-sucedidas-use a métrica de solicitação de várias dimensões com a dimensão GatewayResponseCodeCategory em vez disso|Local, Nome do host|
|TotalRequests|Sim|Solicitações Totais de Gateway (Preterido)|Contagem|Total|Número de solicitações de gateway-use a métrica de solicitação de várias dimensões com a dimensão GatewayResponseCodeCategory em vez disso|Local, Nome do host|
|UnauthorizedRequests|Sim|Solicitações de Gateway Não Autorizadas (Preterido)|Contagem|Total|Número de solicitações de gateway não autorizadas-use a métrica de solicitação de várias dimensões com a dimensão GatewayResponseCodeCategory em vez disso|Local, Nome do host|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|HttpIncomingRequestCount|Sim|HttpIncomingRequestCount|Contagem|Contagem|Número total de solicitações HTTP de entrada.|StatusCode, autenticação|
|HttpIncomingRequestDuration|Sim|HttpIncomingRequestDuration|Contagem|Média|Latência em uma solicitação HTTP.|StatusCode, autenticação|
|ThrottledHttpRequestCount|Sim|ThrottledHttpRequestCount|Contagem|Contagem|Solicitações HTTP limitadas.|Sem dimensões|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|JVM. GC. Live. Data. Size|Sim|JVM. GC. Live. Data. Size|Bytes|Média|Tamanho do pool de memória de geração antiga após um GC completo|Implantação, AppName, Pod|
|JVM. GC. Max. Data. Size|Sim|JVM. GC. Max. Data. Size|Bytes|Média|Tamanho máximo do pool de memória de geração antiga|Implantação, AppName, Pod|
|JVM. GC. Memory. allocated|Sim|JVM. GC. Memory. allocated|Bytes|Máximo|Incrementado para um aumento no tamanho do pool de memória de geração jovem após um GC antes do próximo|Implantação, AppName, Pod|
|JVM. GC. Memory. promovida|Sim|JVM. GC. Memory. promovida|Bytes|Máximo|Contagem de aumentos positivos no tamanho do pool de memória de geração antiga antes do GC ser após o GC|Implantação, AppName, Pod|
|JVM. GC. PAUSE. total. Count|Sim|JVM. GC. PAUSE. total. Count|Contagem|Total|Contagem de Pausa do GC|Implantação, AppName, Pod|
|JVM. GC. PAUSE. total. time|Sim|JVM. GC. PAUSE. total. time|Milissegundos|Total|Tempo Total de Pausa do GC|Implantação, AppName, Pod|
|JVM. Memory. Committed|Sim|JVM. Memory. Committed|Bytes|Média|Memória atribuída à JVM em bytes|Implantação, AppName, Pod|
|JVM. Memory. Max|Sim|JVM. Memory. Max|Bytes|Máximo|A quantidade máxima de memória, em bytes, que pode ser usada para gerenciamento de memória|Implantação, AppName, Pod|
|JVM. Memory. Used|Sim|JVM. Memory. Used|Bytes|Média|Memória do aplicativo usada em bytes|Implantação, AppName, Pod|
|Process. CPU. Usage|Sim|Process. CPU. Usage|Porcentagem|Média|Porcentagem de uso da CPU da JVM do aplicativo|Implantação, AppName, Pod|
|System. CPU. Usage|Sim|System. CPU. Usage|Porcentagem|Média|O uso recente da CPU para todo o sistema|Implantação, AppName, Pod|
|Tomcat. global. Error|Sim|Tomcat. global. Error|Contagem|Total|Erro Global do Tomcat|Implantação, AppName, Pod|
|Tomcat. global. Received|Sim|Tomcat. global. Received|Bytes|Total|Total de Bytes Recebidos do Tomcat|Implantação, AppName, Pod|
|Tomcat. global. Request. AVG. time|Sim|Tomcat. global. Request. AVG. time|Milissegundos|Média|Tempo Médio da Solicitação do Tomcat|Implantação, AppName, Pod|
|Tomcat. global. Request. Max|Sim|Tomcat. global. Request. Max|Milissegundos|Máximo|Tempo Máximo de Solicitação do Tomcat|Implantação, AppName, Pod|
|Tomcat. global. Request. total. Count|Sim|Tomcat. global. Request. total. Count|Contagem|Total|Contagem Total de Solicitações do Tomcat|Implantação, AppName, Pod|
|Tomcat. global. Request. total. time|Sim|Tomcat. global. Request. total. time|Milissegundos|Total|Tempo total da solicitação do Tomcat|Implantação, AppName, Pod|
|Tomcat. global. sent|Sim|Tomcat. global. sent|Bytes|Total|Total de Bytes Enviados do Tomcat|Implantação, AppName, Pod|
|Tomcat. Sessions. Active. Current|Sim|Tomcat. Sessions. Active. Current|Contagem|Total|Contagem ativa da sessão Tomcat|Implantação, AppName, Pod|
|Tomcat. Sessions. Active. Max|Sim|Tomcat. Sessions. Active. Max|Contagem|Total|Contagem de Sessões Ativas Máximas do Tomcat|Implantação, AppName, Pod|
|Tomcat. Sessions. Alive. Max|Sim|Tomcat. Sessions. Alive. Max|Milissegundos|Máximo|Tempo de Sessões Ativas Máximas do Tomcat|Implantação, AppName, Pod|
|Tomcat. Sessions. Created|Sim|Tomcat. Sessions. Created|Contagem|Total|Contagem de Sessões Criadas do Tomcat|Implantação, AppName, Pod|
|Tomcat. Sessions. Expired|Sim|Tomcat. Sessions. Expired|Contagem|Total|Contagem de Sessões Expiradas do Tomcat|Implantação, AppName, Pod|
|Tomcat. Sessions. Rejected|Sim|Tomcat. Sessions. Rejected|Contagem|Total|Contagem de Sessões Rejeitadas do Tomcat|Implantação, AppName, Pod|
|tomcat.threads.config. Max|Sim|tomcat.threads.config. Max|Contagem|Total|Contagem máxima de threads de configuração do Tomcat|Implantação, AppName, Pod|
|Tomcat. threads. Current|Sim|Tomcat. threads. Current|Contagem|Total|Contagem de threads atuais do Tomcat|Implantação, AppName, Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|TotalJob|Sim|Total de trabalhos|Contagem|Total|O número total de trabalhos|Runbook, Status|
|TotalUpdateDeploymentMachineRuns|Sim|Total de Execuções de Computador da Implantação de Atualização|Contagem|Total|O computador de implantação de atualização de software total é executado em uma execução de implantação de atualização de software|SoftwareUpdateConfigurationName, status, Computadordedestino, SoftwareUpdateConfigurationRunId|
|TotalUpdateDeploymentRuns|Sim|Total de Execuções da Implantação de Atualização|Contagem|Total|Total de execuções de implantação de atualização de software|SoftwareUpdateConfigurationName, status|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|CoreCount|Não|Contagem de núcleos dedicados|Contagem|Total|Número total de núcleos dedicados na conta do lote|Sem dimensões|
|CreatingNodeCount|Não|Criação de contagem de nós|Contagem|Total|Número de nós sendo criados|Sem dimensões|
|IdleNodeCount|Não|Contagem de nós ociosos|Contagem|Total|Número de nós ociosos|Sem dimensões|
|JobDeleteCompleteEvent|Sim|Eventos de conclusão de exclusão do trabalho|Contagem|Total|Número total de trabalhos que foram excluídos com êxito.|jobId|
|JobDeleteStartEvent|Sim|Eventos de início de exclusão do trabalho|Contagem|Total|Número total de trabalhos que foram solicitados a serem excluídos.|jobId|
|JobDisableCompleteEvent|Sim|Eventos de conclusão de desabilitação do trabalho|Contagem|Total|Número total de trabalhos que foram desabilitados com êxito.|jobId|
|JobDisableStartEvent|Sim|Eventos de início de desabilitação do trabalho|Contagem|Total|Número total de trabalhos que foram solicitados a serem desabilitados.|jobId|
|JobStartEvent|Sim|Eventos de início do trabalho|Contagem|Total|Número total de trabalhos que foram iniciados com êxito.|jobId|
|JobTerminateCompleteEvent|Sim|Eventos de conclusão de encerramento do trabalho|Contagem|Total|Número total de trabalhos que foram terminados com êxito.|jobId|
|JobTerminateStartEvent|Sim|Eventos de início de encerramento do trabalho|Contagem|Total|Número total de trabalhos que foram solicitados a serem encerrados.|jobId|
|LeavingPoolNodeCount|Não|Contagem de nós saindo do pool|Contagem|Total|Número de nós saindo do pool|Sem dimensões|
|LowPriorityCoreCount|Não|Contagem de núcleos LowPriority|Contagem|Total|Número total de núcleos de baixa prioridade na conta do lote|Sem dimensões|
|OfflineNodeCount|Não|Contagem de nós off-line|Contagem|Total|Número de nós off-line|Sem dimensões|
|PoolCreateEvent|Sim|Eventos de criação de pool|Contagem|Total|Número total de pools criados|poolId|
|PoolDeleteCompleteEvent|Sim|Eventos de conclusão de exclusão do pool|Contagem|Total|Número total de exclusões de pool concluídas|poolId|
|PoolDeleteStartEvent|Sim|Eventos de início de exclusão de pool|Contagem|Total|Número total de exclusões de pool iniciados|poolId|
|PoolResizeCompleteEvent|Sim|Eventos de conclusão de redimensionamento de pool|Contagem|Total|Número total de redimensionamentos de pool concluídos|poolId|
|PoolResizeStartEvent|Sim|Eventos de início de redimensionamento de pool|Contagem|Total|Número total de tarefas de redimensionamento de pool que iniciaram|poolId|
|PreemptedNodeCount|Não|Contagem de nós com preempção|Contagem|Total|Número de nós com preempção|Sem dimensões|
|RebootingNodeCount|Não|Contagem de nós de reinicialização|Contagem|Total|Número de nós de reinicialização|Sem dimensões|
|ReimagingNodeCount|Não|Contagem de nós refazendo a imagem|Contagem|Total|Número de nós refazendo a imagem|Sem dimensões|
|RunningNodeCount|Não|Contagem de nós em execução|Contagem|Total|Número de nós em execução|Sem dimensões|
|StartingNodeCount|Não|Contagem inicial dos nós|Contagem|Total|Número de nós iniciais|Sem dimensões|
|StartTaskFailedNodeCount|Não|Falha na contagem de nós para a tarefa de inicialização|Contagem|Total|Número de nós com falha na tarefa de inicialização|Sem dimensões|
|TaskCompleteEvent|Sim|Eventos de conclusão de tarefa|Contagem|Total|Número total de tarefas concluídas|poolid, jobId|
|TaskFailEvent|Sim|Eventos de falha de tarefa|Contagem|Total|Número total de tarefas que foram concluídas em um estado com falha|poolid, jobId|
|TaskStartEvent|Sim|Eventos da tarefa de inicialização|Contagem|Total|Número total de tarefas que iniciaram|poolid, jobId|
|TotalLowPriorityNodeCount|Não|Contagem de nós de baixa prioridade|Contagem|Total|Número total de nós de baixa prioridade na conta do lote|Sem dimensões|
|TotalNodeCount|Não|Contagem de nós dedicados|Contagem|Total|Número total de nós dedicados na conta do lote|Sem dimensões|
|UnusableNodeCount|Não|Contagem de nós inutilizáveis|Contagem|Total|Número de nós inutilizáveis|Sem dimensões|
|WaitingForStartTaskNodeCount|Não|Contagem de nós para tarefa de inicialização em espera|Contagem|Total|Número de nós aguardando a conclusão da Tarefa de Inicialização|Sem dimensões|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/workspaces

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Núcleos Ativos|Sim|Núcleos Ativos|Contagem|Média|Número de núcleos ativos|Cenário, ClusterName|
|Nós Ativos|Sim|Nós Ativos|Contagem|Média|Número de nós em execução|Cenário, ClusterName|
|Núcleos Ociosos|Sim|Núcleos Ociosos|Contagem|Média|Número de núcleos ociosos|Cenário, ClusterName|
|Nós Ociosos|Sim|Nós Ociosos|Contagem|Média|Número de nós ociosos|Cenário, ClusterName|
|Trabalho Concluído|Sim|Trabalho Concluído|Contagem|Total|Número de trabalhos concluídos|Cenário, ClusterName, Resultadotype|
|Trabalho Enviado|Sim|Trabalho Enviado|Contagem|Total|Número de trabalhos enviados|Cenário, ClusterName|
|Núcleos em Saída|Sim|Núcleos em Saída|Contagem|Média|Número de núcleos de saída|Cenário, ClusterName|
|Nós em Saída|Sim|Nós em Saída|Contagem|Média|Número de nós de saída|Cenário, ClusterName|
|Núcleos com Preempção|Sim|Núcleos com Preempção|Contagem|Média|Número de núcleos admitidos|Cenário, ClusterName|
|Nós com Preempção|Sim|Nós com Preempção|Contagem|Média|Número de nós com preempção|Cenário, ClusterName|
|Percentual de Utilização de Cota|Sim|Percentual de Utilização de Cota|Contagem|Média|Porcentagem de cota utilizada|Cenário, ClusterName, VmFamilyName, VmPriority|
|Total de Núcleos|Sim|Total de Núcleos|Contagem|Média|Número de núcleos totais|Cenário, ClusterName|
|Total de Nós|Sim|Total de Nós|Contagem|Média|Número total de nós|Cenário, ClusterName|
|Núcleos Inutilizáveis|Sim|Núcleos Inutilizáveis|Contagem|Média|Número de núcleos inutilizáveis|Cenário, ClusterName|
|Nós Inutilizáveis|Sim|Nós Inutilizáveis|Contagem|Média|Número de nós inutilizáveis|Cenário, ClusterName|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|BroadcastProcessedCount|Sim|Contagem de difusão processada|Contagem|Média|O número de transações processadas|Nó, canal, tipo, status|
|ConnectionAccepted|Sim|Conexões Aceitas|Contagem|Total|Conexões Aceitas|Nó|
|ConnectionActive|Sim|Conexões ativas|Contagem|Média|Conexões ativas|Nó|
|ConnectionHandled|Sim|Conexões Manipuladas|Contagem|Total|Conexões Manipuladas|Nó|
|ConsensusEtcdraftCommittedBlockNumber|Sim|Número de bloco comprometido de consenso Etcdraft|Contagem|Média|O número de bloco do bloco mais recente confirmado|Nó, canal|
|CpuUsagePercentageInDouble|Sim|Percentual de Uso de CPU|Porcentagem|Máximo|Percentual de Uso de CPU|Nó|
|EndorserEndorsementFailures|Sim|Falhas de endosso do endossador|Contagem|Média|O número de endossos com falha.|Nó, canal, chaincode, chaincodeerror|
|GossipLeaderElectionLeader|Sim|Líder de eleição de líder de fofocas|Contagem|Total|O par é líder (1) ou seguidor (0)|Nó, canal|
|GossipMembershipTotalPeersKnown|Sim|Fofocas associação total de colegas conhecidos|Contagem|Média|Total de pares conhecidos|Nó, canal|
|GossipStateHeight|Sim|Altura do estado de fofocas|Contagem|Média|Altura do razão atual|Nó, canal|
|IOReadBytes|Sim|Bytes de Leitura de E/S|Bytes|Total|Bytes de Leitura de E/S|Nó|
|IOWriteBytes|Sim|Bytes de Gravação de E/S|Bytes|Total|Bytes de Gravação de E/S|Nó|
|LedgerTransactionCount|Sim|Contagem de transações do razão|Contagem|Média|Número de transações processadas|Nó, canal, transaction_type, chaincode, validation_code|
|MemoryLimit|Sim|Limite de Memória|Bytes|Média|Limite de Memória|Nó|
|MemoryUsage|Sim|Uso de Memória|Bytes|Média|Uso de Memória|Nó|
|MemoryUsagePercentageInDouble|Sim|Percentual de Uso de Memória|Porcentagem|Média|Percentual de Uso de Memória|Nó|
|PendingTransactions|Sim|Transações Pendentes|Contagem|Média|Transações Pendentes|Nó|
|ProcessedBlocks|Sim|Blocos Processados|Contagem|Total|Blocos Processados|Nó|
|ProcessedTransactions|Sim|Transações Processadas|Contagem|Total|Transações Processadas|Nó|
|QueuedTransactions|Sim|Transações em Fila|Contagem|Média|Transações em Fila|Nó|
|RequestHandled|Sim|Solicitações Manipuladas|Contagem|Total|Solicitações Manipuladas|Nó|
|StorageUsage|Sim|Uso de Armazenamento|Bytes|Média|Uso de Armazenamento|Nó|


## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|cachehits|Sim|Acertos do Cache|Contagem|Total||ShardId|
|cachehits0|Sim|Ocorrências no cache (Fragmento 0)|Contagem|Total||Sem dimensões|
|cachehits1|Sim|Ocorrências no cache (Fragmento 1)|Contagem|Total||Sem dimensões|
|cachehits2|Sim|Ocorrências no cache (Fragmento 2)|Contagem|Total||Sem dimensões|
|cachehits3|Sim|Ocorrências no cache (Fragmento 3)|Contagem|Total||Sem dimensões|
|cachehits4|Sim|Ocorrências no cache (Fragmento 4)|Contagem|Total||Sem dimensões|
|cachehits5|Sim|Ocorrências no cache (Fragmento 5)|Contagem|Total||Sem dimensões|
|cachehits6|Sim|Ocorrências no cache (Fragmento 6)|Contagem|Total||Sem dimensões|
|cachehits7|Sim|Ocorrências no cache (Fragmento 7)|Contagem|Total||Sem dimensões|
|cachehits8|Sim|Ocorrências no cache (Fragmento 8)|Contagem|Total||Sem dimensões|
|cachehits9|Sim|Ocorrências no cache (Fragmento 9)|Contagem|Total||Sem dimensões|
|cacheLatency|Sim|Microssegundos de latência de cache (versão prévia)|Contagem|Média||ShardId|
|cachemisses|Sim|Erros de Cache|Contagem|Total||ShardId|
|cachemisses0|Sim|Perdas no cache (Fragmento 0)|Contagem|Total||Sem dimensões|
|cachemisses1|Sim|Perdas no cache (Fragmento 1)|Contagem|Total||Sem dimensões|
|cachemisses2|Sim|Perdas no cache (Fragmento 2)|Contagem|Total||Sem dimensões|
|cachemisses3|Sim|Perdas no cache (Fragmento 3)|Contagem|Total||Sem dimensões|
|cachemisses4|Sim|Perdas no cache (Fragmento 4)|Contagem|Total||Sem dimensões|
|cachemisses5|Sim|Perdas no cache (Fragmento 5)|Contagem|Total||Sem dimensões|
|cachemisses6|Sim|Perdas no cache (Fragmento 6)|Contagem|Total||Sem dimensões|
|cachemisses7|Sim|Perdas no cache (Fragmento 7)|Contagem|Total||Sem dimensões|
|cachemisses8|Sim|Perdas no cache (Fragmento 8)|Contagem|Total||Sem dimensões|
|cachemisses9|Sim|Perdas no cache (Fragmento 9)|Contagem|Total||Sem dimensões|
|cachemissrate|Sim|Taxa de erros de cache|Porcentagem|cachemissrate||ShardId|
|cacheRead|Sim|Cache Lido|BytesPerSecond|Máximo||ShardId|
|cacheRead0|Sim|Leitura no cache (Fragmento 0)|BytesPerSecond|Máximo||Sem dimensões|
|cacheRead1|Sim|Leitura no cache (Fragmento 1)|BytesPerSecond|Máximo||Sem dimensões|
|cacheRead2|Sim|Leitura no cache (Fragmento 2)|BytesPerSecond|Máximo||Sem dimensões|
|cacheRead3|Sim|Leitura no cache (Fragmento 3)|BytesPerSecond|Máximo||Sem dimensões|
|cacheRead4|Sim|Leitura no cache (Fragmento 4)|BytesPerSecond|Máximo||Sem dimensões|
|cacheRead5|Sim|Leitura no cache (Fragmento 5)|BytesPerSecond|Máximo||Sem dimensões|
|cacheRead6|Sim|Leitura no cache (Fragmento 6)|BytesPerSecond|Máximo||Sem dimensões|
|cacheRead7|Sim|Leitura no cache (Fragmento 7)|BytesPerSecond|Máximo||Sem dimensões|
|cacheRead8|Sim|Leitura no cache (Fragmento 8)|BytesPerSecond|Máximo||Sem dimensões|
|cacheRead9|Sim|Leitura no cache (Fragmento 9)|BytesPerSecond|Máximo||Sem dimensões|
|cacheWrite|Sim|Gravação no Cache|BytesPerSecond|Máximo||ShardId|
|cacheWrite0|Sim|Gravação no cache (Fragmento 0)|BytesPerSecond|Máximo||Sem dimensões|
|cacheWrite1|Sim|Gravação no cache (Fragmento 1)|BytesPerSecond|Máximo||Sem dimensões|
|cacheWrite2|Sim|Gravação no cache (Fragmento 2)|BytesPerSecond|Máximo||Sem dimensões|
|cacheWrite3|Sim|Gravação no cache (Fragmento 3)|BytesPerSecond|Máximo||Sem dimensões|
|cacheWrite4|Sim|Gravação no cache (Fragmento 4)|BytesPerSecond|Máximo||Sem dimensões|
|cacheWrite5|Sim|Gravação no cache (Fragmento 5)|BytesPerSecond|Máximo||Sem dimensões|
|cacheWrite6|Sim|Gravação no cache (Fragmento 6)|BytesPerSecond|Máximo||Sem dimensões|
|cacheWrite7|Sim|Gravação no cache (Fragmento 7)|BytesPerSecond|Máximo||Sem dimensões|
|cacheWrite8|Sim|Gravação no cache (Fragmento 8)|BytesPerSecond|Máximo||Sem dimensões|
|cacheWrite9|Sim|Gravação no cache (Fragmento 9)|BytesPerSecond|Máximo||Sem dimensões|
|connectedclients|Sim|Clientes conectados|Contagem|Máximo||ShardId|
|connectedclients0|Sim|Clientes conectados (Fragmento 0)|Contagem|Máximo||Sem dimensões|
|connectedclients1|Sim|Clientes conectados (Fragmento 1)|Contagem|Máximo||Sem dimensões|
|connectedclients2|Sim|Clientes conectados (Fragmento 2)|Contagem|Máximo||Sem dimensões|
|connectedclients3|Sim|Clientes conectados (Fragmento 3)|Contagem|Máximo||Sem dimensões|
|connectedclients4|Sim|Clientes conectados (Fragmento 4)|Contagem|Máximo||Sem dimensões|
|connectedclients5|Sim|Clientes conectados (Fragmento 5)|Contagem|Máximo||Sem dimensões|
|connectedclients6|Sim|Clientes conectados (Fragmento 6)|Contagem|Máximo||Sem dimensões|
|connectedclients7|Sim|Clientes conectados (Fragmento 7)|Contagem|Máximo||Sem dimensões|
|connectedclients8|Sim|Clientes conectados (Fragmento 8)|Contagem|Máximo||Sem dimensões|
|connectedclients9|Sim|Clientes conectados (Fragmento 9)|Contagem|Máximo||Sem dimensões|
|erros|Sim|Errors|Contagem|Máximo||ShardId, ErrorType|
|evictedkeys|Sim|Chaves removidas|Contagem|Total||ShardId|
|evictedkeys0|Sim|Chaves removidas (Fragmento 0)|Contagem|Total||Sem dimensões|
|evictedkeys1|Sim|Chaves removidas (Fragmento 1)|Contagem|Total||Sem dimensões|
|evictedkeys2|Sim|Chaves removidas (Fragmento 2)|Contagem|Total||Sem dimensões|
|evictedkeys3|Sim|Chaves removidas (Fragmento 3)|Contagem|Total||Sem dimensões|
|evictedkeys4|Sim|Chaves removidas (Fragmento 4)|Contagem|Total||Sem dimensões|
|evictedkeys5|Sim|Chaves removidas (Fragmento 5)|Contagem|Total||Sem dimensões|
|evictedkeys6|Sim|Chaves removidas (Fragmento 6)|Contagem|Total||Sem dimensões|
|evictedkeys7|Sim|Chaves removidas (Fragmento 7)|Contagem|Total||Sem dimensões|
|evictedkeys8|Sim|Chaves removidas (Fragmento 8)|Contagem|Total||Sem dimensões|
|evictedkeys9|Sim|Chaves removidas (Fragmento 9)|Contagem|Total||Sem dimensões|
|expiredkeys|Sim|Chaves expiradas|Contagem|Total||ShardId|
|expiredkeys0|Sim|Chaves expiradas (Fragmento 0)|Contagem|Total||Sem dimensões|
|expiredkeys1|Sim|Chaves expiradas (Fragmento 1)|Contagem|Total||Sem dimensões|
|expiredkeys2|Sim|Chaves expiradas (Fragmento 2)|Contagem|Total||Sem dimensões|
|expiredkeys3|Sim|Chaves expiradas (Fragmento 3)|Contagem|Total||Sem dimensões|
|expiredkeys4|Sim|Chaves expiradas (Fragmento 4)|Contagem|Total||Sem dimensões|
|expiredkeys5|Sim|Chaves expiradas (Fragmento 5)|Contagem|Total||Sem dimensões|
|expiredkeys6|Sim|Chaves expiradas (Fragmento 6)|Contagem|Total||Sem dimensões|
|expiredkeys7|Sim|Chaves expiradas (Fragmento 7)|Contagem|Total||Sem dimensões|
|expiredkeys8|Sim|Chaves expiradas (Fragmento 8)|Contagem|Total||Sem dimensões|
|expiredkeys9|Sim|Chaves expiradas (Fragmento 9)|Contagem|Total||Sem dimensões|
|getcommands|Sim|Gets|Contagem|Total||ShardId|
|getcommands0|Sim|Gets (Fragmento 0)|Contagem|Total||Sem dimensões|
|getcommands1|Sim|Gets (Fragmento 1)|Contagem|Total||Sem dimensões|
|getcommands2|Sim|Gets (Fragmento 2)|Contagem|Total||Sem dimensões|
|getcommands3|Sim|Gets (Fragmento 3)|Contagem|Total||Sem dimensões|
|getcommands4|Sim|Gets (Fragmento 4)|Contagem|Total||Sem dimensões|
|getcommands5|Sim|Gets (Fragmento 5)|Contagem|Total||Sem dimensões|
|getcommands6|Sim|Gets (Fragmento 6)|Contagem|Total||Sem dimensões|
|getcommands7|Sim|Gets (Fragmento 7)|Contagem|Total||Sem dimensões|
|getcommands8|Sim|Gets (Fragmento 8)|Contagem|Total||Sem dimensões|
|getcommands9|Sim|Gets (Fragmento 9)|Contagem|Total||Sem dimensões|
|operationsPerSecond|Sim|Operations por segundo|Contagem|Máximo||ShardId|
|operationsPerSecond0|Sim|Operações por segundo (fragmento 0)|Contagem|Máximo||Sem dimensões|
|operationsPerSecond1|Sim|Operações por segundo (fragmento 1)|Contagem|Máximo||Sem dimensões|
|operationsPerSecond2|Sim|Operações por segundo (fragmento 2)|Contagem|Máximo||Sem dimensões|
|operationsPerSecond3|Sim|Operações por segundo (fragmento 3)|Contagem|Máximo||Sem dimensões|
|operationsPerSecond4|Sim|Operações por segundo (fragmento 4)|Contagem|Máximo||Sem dimensões|
|operationsPerSecond5|Sim|Operações por segundo (fragmento 5)|Contagem|Máximo||Sem dimensões|
|operationsPerSecond6|Sim|Operações por segundo (fragmento 6)|Contagem|Máximo||Sem dimensões|
|operationsPerSecond7|Sim|Operações por segundo (fragmento 7)|Contagem|Máximo||Sem dimensões|
|operationsPerSecond8|Sim|Operações por segundo (fragmento 8)|Contagem|Máximo||Sem dimensões|
|operationsPerSecond9|Sim|Operações por segundo (fragmento 9)|Contagem|Máximo||Sem dimensões|
|percentProcessorTime|Sim|CPU|Porcentagem|Máximo||ShardId|
|percentProcessorTime0|Sim|CPU (Fragmento 0)|Porcentagem|Máximo||Sem dimensões|
|percentProcessorTime1|Sim|CPU (Fragmento 1)|Porcentagem|Máximo||Sem dimensões|
|percentProcessorTime2|Sim|CPU (Fragmento 2)|Porcentagem|Máximo||Sem dimensões|
|percentProcessorTime3|Sim|CPU (Fragmento 3)|Porcentagem|Máximo||Sem dimensões|
|percentProcessorTime4|Sim|CPU (Fragmento 4)|Porcentagem|Máximo||Sem dimensões|
|percentProcessorTime5|Sim|CPU (Fragmento 5)|Porcentagem|Máximo||Sem dimensões|
|percentProcessorTime6|Sim|CPU (Fragmento 6)|Porcentagem|Máximo||Sem dimensões|
|percentProcessorTime7|Sim|CPU (Fragmento 7)|Porcentagem|Máximo||Sem dimensões|
|percentProcessorTime8|Sim|CPU (Fragmento 8)|Porcentagem|Máximo||Sem dimensões|
|percentProcessorTime9|Sim|CPU (Fragmento 9)|Porcentagem|Máximo||Sem dimensões|
|serverLoad|Sim|Carga do Servidor|Porcentagem|Máximo||ShardId|
|serverLoad0|Sim|Carga do servidor (Fragmento 0)|Porcentagem|Máximo||Sem dimensões|
|serverLoad1|Sim|Carga do servidor (Fragmento 1)|Porcentagem|Máximo||Sem dimensões|
|serverLoad2|Sim|Carga do servidor (Fragmento 2)|Porcentagem|Máximo||Sem dimensões|
|serverLoad3|Sim|Carga do servidor (Fragmento 3)|Porcentagem|Máximo||Sem dimensões|
|serverLoad4|Sim|Carga do servidor (Fragmento 4)|Porcentagem|Máximo||Sem dimensões|
|serverLoad5|Sim|Carga do servidor (Fragmento 5)|Porcentagem|Máximo||Sem dimensões|
|serverLoad6|Sim|Carga do servidor (Fragmento 6)|Porcentagem|Máximo||Sem dimensões|
|serverLoad7|Sim|Carga do servidor (Fragmento 7)|Porcentagem|Máximo||Sem dimensões|
|serverLoad8|Sim|Carga do servidor (Fragmento 8)|Porcentagem|Máximo||Sem dimensões|
|serverLoad9|Sim|Carga do servidor (Fragmento 9)|Porcentagem|Máximo||Sem dimensões|
|setcommands|Sim|Conjuntos|Contagem|Total||ShardId|
|setcommands0|Sim|Sets (Fragmento 0)|Contagem|Total||Sem dimensões|
|setcommands1|Sim|Sets (Fragmento 1)|Contagem|Total||Sem dimensões|
|setcommands2|Sim|Sets (Fragmento 2)|Contagem|Total||Sem dimensões|
|setcommands3|Sim|Sets (Fragmento 3)|Contagem|Total||Sem dimensões|
|setcommands4|Sim|Sets (Fragmento 4)|Contagem|Total||Sem dimensões|
|setcommands5|Sim|Sets (Fragmento 5)|Contagem|Total||Sem dimensões|
|setcommands6|Sim|Sets (Fragmento 6)|Contagem|Total||Sem dimensões|
|setcommands7|Sim|Sets (Fragmento 7)|Contagem|Total||Sem dimensões|
|setcommands8|Sim|Sets (Fragmento 8)|Contagem|Total||Sem dimensões|
|setcommands9|Sim|Sets (Fragmento 9)|Contagem|Total||Sem dimensões|
|totalcommandsprocessed|Sim|Total de Operações|Contagem|Total||ShardId|
|totalcommandsprocessed0|Sim|Total de operações (Fragmento 0)|Contagem|Total||Sem dimensões|
|totalcommandsprocessed1|Sim|Total de operações (Fragmento 1)|Contagem|Total||Sem dimensões|
|totalcommandsprocessed2|Sim|Total de operações (Fragmento 2)|Contagem|Total||Sem dimensões|
|totalcommandsprocessed3|Sim|Total de operações (Fragmento 3)|Contagem|Total||Sem dimensões|
|totalcommandsprocessed4|Sim|Total de operações (Fragmento 4)|Contagem|Total||Sem dimensões|
|totalcommandsprocessed5|Sim|Total de operações (Fragmento 5)|Contagem|Total||Sem dimensões|
|totalcommandsprocessed6|Sim|Total de operações (Fragmento 6)|Contagem|Total||Sem dimensões|
|totalcommandsprocessed7|Sim|Total de operações (Fragmento 7)|Contagem|Total||Sem dimensões|
|totalcommandsprocessed8|Sim|Total de operações (Fragmento 8)|Contagem|Total||Sem dimensões|
|totalcommandsprocessed9|Sim|Total de operações (Fragmento 9)|Contagem|Total||Sem dimensões|
|totalkeys|Sim|Total de chaves|Contagem|Máximo||ShardId|
|totalkeys0|Sim|Total de Chaves (Fragmento 0)|Contagem|Máximo||Sem dimensões|
|totalkeys1|Sim|Total de Chaves (Fragmento 1)|Contagem|Máximo||Sem dimensões|
|totalkeys2|Sim|Total de Chaves (Fragmento 2)|Contagem|Máximo||Sem dimensões|
|totalkeys3|Sim|Total de Chaves (Fragmento 3)|Contagem|Máximo||Sem dimensões|
|totalkeys4|Sim|Total de Chaves (Fragmento 4)|Contagem|Máximo||Sem dimensões|
|totalkeys5|Sim|Total de Chaves (Fragmento 5)|Contagem|Máximo||Sem dimensões|
|totalkeys6|Sim|Total de Chaves (Fragmento 6)|Contagem|Máximo||Sem dimensões|
|totalkeys7|Sim|Total de Chaves (Fragmento 7)|Contagem|Máximo||Sem dimensões|
|totalkeys8|Sim|Total de Chaves (Fragmento 8)|Contagem|Máximo||Sem dimensões|
|totalkeys9|Sim|Total de Chaves (Fragmento 9)|Contagem|Máximo||Sem dimensões|
|usedmemory|Sim|Memória Usada|Bytes|Máximo||ShardId|
|usedmemory0|Sim|Memória usada (Fragmento 0)|Bytes|Máximo||Sem dimensões|
|usedmemory1|Sim|Memória usada (Fragmento 1)|Bytes|Máximo||Sem dimensões|
|usedmemory2|Sim|Memória usada (Fragmento 2)|Bytes|Máximo||Sem dimensões|
|usedmemory3|Sim|Memória usada (Fragmento 3)|Bytes|Máximo||Sem dimensões|
|usedmemory4|Sim|Memória usada (Fragmento 4)|Bytes|Máximo||Sem dimensões|
|usedmemory5|Sim|Memória usada (Fragmento 5)|Bytes|Máximo||Sem dimensões|
|usedmemory6|Sim|Memória usada (Fragmento 6)|Bytes|Máximo||Sem dimensões|
|usedmemory7|Sim|Memória usada (Fragmento 7)|Bytes|Máximo||Sem dimensões|
|usedmemory8|Sim|Memória usada (Fragmento 8)|Bytes|Máximo||Sem dimensões|
|usedmemory9|Sim|Memória usada (Fragmento 9)|Bytes|Máximo||Sem dimensões|
|usedmemorypercentage|Sim|Porcentagem de memória utilizada|Porcentagem|Máximo||ShardId|
|usedmemoryRss|Sim|Memória RSS Usada|Bytes|Máximo||ShardId|
|usedmemoryRss0|Sim|Memória RSS usada (Fragmento 0)|Bytes|Máximo||Sem dimensões|
|usedmemoryRss1|Sim|Memória RSS usada (Fragmento 1)|Bytes|Máximo||Sem dimensões|
|usedmemoryRss2|Sim|Memória RSS usada (Fragmento 2)|Bytes|Máximo||Sem dimensões|
|usedmemoryRss3|Sim|Memória RSS usada (Fragmento 3)|Bytes|Máximo||Sem dimensões|
|usedmemoryRss4|Sim|Memória RSS usada (Fragmento 4)|Bytes|Máximo||Sem dimensões|
|usedmemoryRss5|Sim|Memória RSS usada (Fragmento 5)|Bytes|Máximo||Sem dimensões|
|usedmemoryRss6|Sim|Memória RSS usada (Fragmento 6)|Bytes|Máximo||Sem dimensões|
|usedmemoryRss7|Sim|Memória RSS usada (Fragmento 7)|Bytes|Máximo||Sem dimensões|
|usedmemoryRss8|Sim|Memória RSS usada (Fragmento 8)|Bytes|Máximo||Sem dimensões|
|usedmemoryRss9|Sim|Memória RSS usada (Fragmento 9)|Bytes|Máximo||Sem dimensões|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/cdnwebapplicationfirewallpolicies

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Sim|Contagem de solicitações do Firewall de Aplicativo Web|Contagem|Total|O número de solicitações de cliente processadas pelo Firewall do Aplicativo Web|PolicyName, RuleName, Action|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Disk Read Bytes/Sec|Não|Leitura de disco|BytesPerSecond|Média|Média de bytes lidos do disco durante o período de monitoramento.|RoleInstanceId|
|Operações de leitura de disco/Seg|Sim|Operações de leitura de disco/Seg|CountPerSecond|Média|IOPS de leitura de disco.|RoleInstanceId|
|Disk Write Bytes/Sec|Não|Gravação de disco|BytesPerSecond|Média|Média de bytes gravados em disco durante o período de monitoramento.|RoleInstanceId|
|Operações de gravação de disco/Seg|Sim|Operações de gravação de disco/Seg|CountPerSecond|Média|IOPS de gravação de disco.|RoleInstanceId|
|Entrada na rede|Sim|Entrada na rede|Bytes|Total|O número de bytes recebidos em todos os adaptadores de rede pelas Máquinas Virtuais (Tráfego de Entrada).|RoleInstanceId|
|Saída da rede|Sim|Saída da rede|Bytes|Total|O número de bytes de saída em todos os adaptadores de rede pelas Máquinas Virtuais (Tráfego de Saída).|RoleInstanceId|
|Percentual de CPU|Sim|Porcentagem de CPU|Porcentagem|Média|O percentual das unidades de computação alocadas que estão atualmente em uso pelas Máquinas Virtuais.|RoleInstanceId|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Disk Read Bytes/Sec|Não|Leitura de disco|BytesPerSecond|Média|Média de bytes lidos do disco durante o período de monitoramento.|Sem dimensões|
|Operações de leitura de disco/Seg|Sim|Operações de leitura de disco/Seg|CountPerSecond|Média|IOPS de leitura de disco.|Sem dimensões|
|Disk Write Bytes/Sec|Não|Gravação de disco|BytesPerSecond|Média|Média de bytes gravados em disco durante o período de monitoramento.|Sem dimensões|
|Operações de gravação de disco/Seg|Sim|Operações de gravação de disco/Seg|CountPerSecond|Média|IOPS de gravação de disco.|Sem dimensões|
|Entrada na rede|Sim|Entrada na rede|Bytes|Total|O número de bytes recebidos em todos os adaptadores de rede pelas Máquinas Virtuais (Tráfego de Entrada).|Sem dimensões|
|Saída da rede|Sim|Saída da rede|Bytes|Total|O número de bytes de saída em todos os adaptadores de rede pelas Máquinas Virtuais (Tráfego de Saída).|Sem dimensões|
|Percentual de CPU|Sim|Porcentagem de CPU|Porcentagem|Média|O percentual das unidades de computação alocadas que estão atualmente em uso pelas Máquinas Virtuais.|Sem dimensões|


## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft.ClassicStorage/storageAccounts

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Sim|Disponibilidade|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|GeoType, ApiName, Authentication|
|Saída|Sim|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável.|GeoType, ApiName, Authentication|
|Entrada|Sim|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Sim|Latência de E2E com Sucesso|Milissegundos|Média|A latência de ponta a ponta de solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Sim|Latência de Servidor Com Sucesso|Milissegundos|Média|A latência usada pelo armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Esse valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType, ApiName, Authentication|
|Transactions|Sim|Transactions|Contagem|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|ResponseType, GeoType, ApiName, Authentication|
|UsedCapacity|Não|Capacidade utilizada|Bytes|Média|Capacidade utilizada pela conta|Sem dimensões|


## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft.ClassicStorage/storageAccounts/blobServices

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Sim|Disponibilidade|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|GeoType, ApiName, Authentication|
|BlobCapacity|Não|Capacidade do Blob|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço Blob da conta de armazenamento em bytes.|BlobType, camada|
|BlobCount|Não|Contagem de Blobs|Contagem|Média|O número de Blobs no serviço Blob da conta de armazenamento.|BlobType, camada|
|ContainerCount|Sim|Contagem de Contêineres de Blobs|Contagem|Média|O número de contêineres no serviço Blob da conta de armazenamento.|Sem dimensões|
|Saída|Sim|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável.|GeoType, ApiName, Authentication|
|IndexCapacity|Não|Capacidade de Índice|Bytes|Média|A quantidade de armazenamento usada pelo índice ADLS Gen2 (hierárquico) em bytes.|Sem dimensões|
|Entrada|Sim|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Sim|Latência de E2E com Sucesso|Milissegundos|Média|A latência de ponta a ponta de solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Sim|Latência de Servidor Com Sucesso|Milissegundos|Média|A latência usada pelo armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Esse valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType, ApiName, Authentication|
|Transactions|Sim|Transactions|Contagem|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft.ClassicStorage/storageAccounts/fileServices

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Sim|Disponibilidade|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|Geotipo, ApiName, autenticação, FileShare|
|Saída|Sim|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável.|Geotipo, ApiName, autenticação, FileShare|
|FileCapacity|Não|Capacidade do Arquivo|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço Arquivo da conta de armazenamento em bytes.|FileShare|
|FileCount|Não|Contagem de Arquivos|Contagem|Média|O número de arquivos no serviço Arquivo da conta de armazenamento.|FileShare|
|FileShareCount|Não|Contagem de Compartilhamentos de Arquivos|Contagem|Média|O número de compartilhamentos de arquivos no serviço Arquivo da conta de armazenamento.|Sem dimensões|
|FileShareQuota|Não|Tamanho da cota do compartilhamento de arquivo|Bytes|Média|O limite superior da quantidade de armazenamento que pode ser usada pelo serviço de arquivos do Azure em bytes.|FileShare|
|FileShareSnapshotCount|Não|Contagem de Instantâneos do Compartilhamento de Arquivo|Contagem|Média|O número de instantâneos presentes no serviço compartilhamento de arquivos da conta de armazenamento.|FileShare|
|FileShareSnapshotSize|Não|Tamanho do Instantâneo do Compartilhamento de Arquivo|Bytes|Média|A quantidade de armazenamento usada pelos instantâneos no serviço de arquivo da conta de armazenamento, em bytes.|FileShare|
|Entrada|Sim|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|Geotipo, ApiName, autenticação, FileShare|
|SuccessE2ELatency|Sim|Latência de E2E com Sucesso|Milissegundos|Média|A latência de ponta a ponta de solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|Geotipo, ApiName, autenticação, FileShare|
|SuccessServerLatency|Sim|Latência de Servidor Com Sucesso|Milissegundos|Média|A latência usada pelo armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Esse valor não inclui a latência de rede especificada em SuccessE2ELatency.|Geotipo, ApiName, autenticação, FileShare|
|Transactions|Sim|Transactions|Contagem|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|ResponseType, GeoType, ApiName, autenticação, FileShare|


## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft.ClassicStorage/storageAccounts/queueServices

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Sim|Disponibilidade|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|GeoType, ApiName, Authentication|
|Saída|Sim|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável.|GeoType, ApiName, Authentication|
|Entrada|Sim|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|GeoType, ApiName, Authentication|
|QueueCapacity|Sim|Capacidade da Fila|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço Fila da conta de armazenamento em bytes.|Sem dimensões|
|QueueCount|Sim|Contagem de Filas|Contagem|Média|O número de filas no serviço Fila da conta de armazenamento.|Sem dimensões|
|QueueMessageCount|Sim|Contagem de Mensagens da Fila|Contagem|Média|O número aproximado de mensagens da fila no serviço Fila da conta de armazenamento.|Sem dimensões|
|SuccessE2ELatency|Sim|Latência de E2E com Sucesso|Milissegundos|Média|A latência de ponta a ponta de solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Sim|Latência de Servidor Com Sucesso|Milissegundos|Média|A latência usada pelo armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Esse valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType, ApiName, Authentication|
|Transactions|Sim|Transactions|Contagem|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft.ClassicStorage/storageAccounts/tableServices

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Sim|Disponibilidade|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|GeoType, ApiName, Authentication|
|Saída|Sim|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável.|GeoType, ApiName, Authentication|
|Entrada|Sim|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Sim|Latência de E2E com Sucesso|Milissegundos|Média|A latência de ponta a ponta de solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Sim|Latência de Servidor Com Sucesso|Milissegundos|Média|A latência usada pelo armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Esse valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType, ApiName, Authentication|
|TableCapacity|Sim|Capacidade da Tabela|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço Tabela da conta de armazenamento em bytes.|Sem dimensões|
|TableCount|Sim|Contagem de Tabelas|Contagem|Média|O número de tabelas no serviço Tabela da conta de armazenamento.|Sem dimensões|
|TableEntityCount|Sim|Contagem de Entidades de Tabela|Contagem|Média|O número de entidades de tabelas no serviço Tabela da conta de armazenamento.|Sem dimensões|
|Transactions|Sim|Transactions|Contagem|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|BlockedCalls|Sim|Chamadas Bloqueadas|Contagem|Total|Número de chamadas que excederam a taxa ou o limite de cota.|ApiName, OperationName, região|
|CharactersTrained|Sim|Caracteres Treinados|Contagem|Total|Número total de caracteres treinados.|ApiName, OperationName, região|
|CharactersTranslated|Sim|Caracteres traduzidos|Contagem|Total|Número total de caracteres na solicitação de texto de entrada.|ApiName, OperationName, região|
|ClientErrors|Sim|Erros do Cliente|Contagem|Total|Número de chamadas com erro do lado do cliente (código de resposta HTTP 4xx).|ApiName, OperationName, região|
|DataIn|Sim|Entrada de Dados|Bytes|Total|Tamanho dos dados de entrada em bytes.|ApiName, OperationName, região|
|DataOut|Sim|Saída de dados|Bytes|Total|Tamanho dos dados de saída em bytes.|ApiName, OperationName, região|
|Latency|Sim|Latency|MilliSeconds|Média|Latência em milissegundos.|ApiName, OperationName, região|
|ProcessedImages|Sim|Imagens processadas|Contagem|Total| Número de transações para processamento de imagem.|ApiName, FeatureName, UsageChannel, região|
|ServerErrors|Sim|Erros do Servidor|Contagem|Total|Número de chamadas com erro interno do serviço (código de resposta HTTP 5xx).|ApiName, OperationName, região|
|SpeechSessionDuration|Sim|Duração da Sessão de Fala|Segundos|Total|Duração total da sessão de fala em segundos.|ApiName, OperationName, região|
|SuccessfulCalls|Sim|Chamadas com Êxito|Contagem|Total|Número de chamadas com êxito.|ApiName, OperationName, região|
|TotalCalls|Sim|Total de Chamadas|Contagem|Total|Número total de chamadas.|ApiName, OperationName, região|
|TotalErrors|Sim|Total de Erros|Contagem|Total|Número total de chamadas com resposta de erro (código de resposta HTTP 4xx ou 5xx).|ApiName, OperationName, região|
|TotalTokenCalls|Sim|Total de chamadas de token|Contagem|Total|Número total de chamadas de token.|ApiName, OperationName, região|
|TotalTransactions|Sim|Total de Transações|Contagem|Total|Número total de transações.|Sem dimensões|


## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Créditos de CPU Consumidos|Sim|Créditos de CPU Consumidos|Contagem|Média|Número total de créditos consumido pela Máquina Virtual|Sem dimensões|
|Créditos de CPU Restantes|Sim|Créditos de CPU Restantes|Contagem|Média|Número total de créditos disponíveis para intermitência|Sem dimensões|
|Profundidade da Fila de Disco de Dados|Sim|Profundidade da Fila de Disco de Dados (Versão prévia)|Contagem|Média|Profundidade da Fila do Disco de Dados (ou Tamanho da Fila)|LUN|
|Bytes de Leitura do Disco de Dados/s|Sim|Bytes de Leitura do Disco de Dados/s (Visualização)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento|LUN|
|Operações de Leitura do Disco de Dados/s|Sim|Operações de Leitura do Disco de Dados/s (Visualização)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento|LUN|
|Bytes de Gravação do Disco de Dados/s|Sim|Bytes de Gravação do Disco de Dados/s (Visualização)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento|LUN|
|Operações de Gravação do Disco de Dados/s|Sim|Operações de Gravação do Disco de Dados/s (Visualização)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento|LUN|
|Bytes de leitura de disco|Sim|Bytes de leitura de disco|Bytes|Total|Bytes lidos do disco durante o período de monitoramento|Sem dimensões|
|Operações de leitura de disco/Seg|Sim|Operações de leitura de disco/Seg|CountPerSecond|Média|IOPS de leitura de disco|Sem dimensões|
|Bytes de gravação de disco|Sim|Bytes de gravação de disco|Bytes|Total|Bytes gravados no disco durante o período de monitoramento|Sem dimensões|
|Operações de gravação de disco/Seg|Sim|Operações de gravação de disco/Seg|CountPerSecond|Média|IOPS de gravação de disco|Sem dimensões|
|Fluxos de Entrada|Sim|Fluxos de Entrada|Contagem|Média|Os fluxos de entrada são o número de fluxos atuais na direção de entrada (tráfego entrando na VM)|Sem dimensões|
|Taxa Máxima de Criação de Fluxos de Entrada|Sim|Taxa Máxima de Criação de Fluxos de Entrada|CountPerSecond|Média|A taxa de criação máxima de fluxos de entrada (tráfego entrando na VM)|Sem dimensões|
|Entrada na rede|Sim|Rede em Faturável (Preterido)|Bytes|Total|O número de bytes faturáveis recebidos em todas as interfaces de rede pelas máquinas virtuais (tráfego de entrada) (preterido)|Sem dimensões|
|Rede no Total|Sim|Rede no Total|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela máquina virtual ou pelas máquinas virtuais (tráfego de entrada)|Sem dimensões|
|Saída da rede|Sim|Rede Fora de Faturável (Preterido)|Bytes|Total|O número de bytes faturáveis de saída em todas as interfaces de rede pelas máquinas virtuais (tráfego de saída) (preterido)|Sem dimensões|
|Rede Fora do Total|Sim|Rede Fora do Total|Bytes|Total|O número de bytes de saída em todas as interfaces de rede pela máquina virtual ou pelas máquinas virtuais (tráfego de saída)|Sem dimensões|
|Profundidade da Fila de Disco do SO|Sim|Profundidade da Fila de Disco do SO (Versão prévia)|Contagem|Média|Profundidade da Fila do Disco do SO (ou Tamanho da Fila)|Sem dimensões|
|Bytes de Leitura do Disco do SO/s|Sim|Bytes de Leitura do Disco do SO/s (Visualização)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento para o disco do sistema operacional|Sem dimensões|
|Operações de Leitura do Disco do SO/s|Sim|Operações de Leitura do Disco do SO/s (Visualização)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Sem dimensões|
|Bytes de Gravação do Disco do SO/s|Sim|Bytes de Gravação do Disco do SO/s (Visualização)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento do disco do sistema operacional|Sem dimensões|
|Operações de Gravação do Disco do SO/s|Sim|Operações de Gravação do Disco do SO/s (Visualização)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Sem dimensões|
|Os Por QD de Disco|Sim|QD de Disco do SO (Preterido)|Contagem|Média|Profundidade da Fila do Disco do SO (ou Tamanho da Fila)|Sem dimensões|
|SO Por Bytes de Leitura do Disco/s|Sim|Bytes de Leitura do Disco do SO/s (Preterido)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento para o disco do sistema operacional|Sem dimensões|
|SO Por Operações de Leitura do Disco/s|Sim|Operações de Leitura do Disco do SO/s (Preterido)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Sem dimensões|
|SO Por Bytes de Gravação do Disco/s|Sim|Bytes de Gravação do Disco do SO/s (Preterido)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento do disco do sistema operacional|Sem dimensões|
|SO Por Operações de Gravação do Disco/s|Sim|Operações de Gravação do Disco do SO/s (Preterido)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Sem dimensões|
|Fluxos de Saída|Sim|Fluxos de Saída|Contagem|Média|Fluxos de saída são o número de fluxos atuais na direção de saída (tráfego saindo da VM)|Sem dimensões|
|Taxa Máxima de Criação de Fluxos de Saída|Sim|Taxa Máxima de Criação de Fluxos de Saída|CountPerSecond|Média|A taxa de criação máxima de fluxos de saída (tráfego saindo da VM)|Sem dimensões|
|Por QD de Disco|Sim|QD do Disco de Dados (Preterido)|Contagem|Média|Profundidade da Fila do Disco de Dados (ou Tamanho da Fila)|SlotId|
|Por Bytes de Leitura do Disco/s|Sim|Bytes de Leitura do Disco de Dados/s (Preterido)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento|SlotId|
|Por operações de Leitura de Disco/s|Sim|Operações de Leitura do Disco de Dados/s (Preterido)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento|SlotId|
|Por Bytes de Gravação de Disco/s|Sim|Bytes de Gravação do Disco de Dados/s (Preterido)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento|SlotId|
|Por Operações de Gravação de Disco/s|Sim|Operações de Gravação do Disco de Dados/s (Preterido)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento|SlotId|
|Percentual de CPU|Sim|Porcentagem de CPU|Porcentagem|Média|A porcentagem das unidades de computação alocadas que estão atualmente em uso pela máquina virtual ou pelas máquinas virtuais|Sem dimensões|
|Ocorrência de Leitura do Cache de Disco de Dados Premium|Sim|Ocorrência de Leitura do Cache de Disco de Dados Premium (Versão prévia)|Porcentagem|Média|Ocorrência de Leitura do Cache de Disco de Dados Premium|LUN|
|Ausência de Leitura do Cache de Disco de Dados Premium|Sim|Ausência de Leitura do Cache de Disco de Dados Premium (Versão prévia)|Porcentagem|Média|Ausência de Leitura do Cache de Disco de Dados Premium|LUN|
|Ocorrência de Leitura do Cache de Disco do SO Premium|Sim|Ocorrência de Leitura do Cache de Disco do SO Premium (Versão prévia)|Porcentagem|Média|Ocorrência de Leitura do Cache de Disco do SO Premium|Sem dimensões|
|Ausência de Leitura do Cache de Disco de SO Premium|Sim|Ausência de Leitura do Cache de Disco de SO Premium (Versão prévia)|Porcentagem|Média|Ausência de Leitura do Cache de Disco de SO Premium|Sem dimensões|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Créditos de CPU Consumidos|Sim|Créditos de CPU Consumidos|Contagem|Média|Número total de créditos consumido pela Máquina Virtual|Sem dimensões|
|Créditos de CPU Restantes|Sim|Créditos de CPU Restantes|Contagem|Média|Número total de créditos disponíveis para intermitência|Sem dimensões|
|Profundidade da Fila de Disco de Dados|Sim|Profundidade da Fila de Disco de Dados (Versão prévia)|Contagem|Média|Profundidade da Fila do Disco de Dados (ou Tamanho da Fila)|LUN, VMName|
|Bytes de Leitura do Disco de Dados/s|Sim|Bytes de Leitura do Disco de Dados/s (Visualização)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento|LUN, VMName|
|Operações de Leitura do Disco de Dados/s|Sim|Operações de Leitura do Disco de Dados/s (Visualização)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento|LUN, VMName|
|Bytes de Gravação do Disco de Dados/s|Sim|Bytes de Gravação do Disco de Dados/s (Visualização)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento|LUN, VMName|
|Operações de Gravação do Disco de Dados/s|Sim|Operações de Gravação do Disco de Dados/s (Visualização)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento|LUN, VMName|
|Bytes de leitura de disco|Sim|Bytes de leitura de disco|Bytes|Total|Bytes lidos do disco durante o período de monitoramento|VMName|
|Operações de leitura de disco/Seg|Sim|Operações de leitura de disco/Seg|CountPerSecond|Média|IOPS de leitura de disco|VMName|
|Bytes de gravação de disco|Sim|Bytes de gravação de disco|Bytes|Total|Bytes gravados no disco durante o período de monitoramento|VMName|
|Operações de gravação de disco/Seg|Sim|Operações de gravação de disco/Seg|CountPerSecond|Média|IOPS de gravação de disco|VMName|
|Fluxos de Entrada|Sim|Fluxos de Entrada|Contagem|Média|Os fluxos de entrada são o número de fluxos atuais na direção de entrada (tráfego entrando na VM)|VMName|
|Taxa Máxima de Criação de Fluxos de Entrada|Sim|Taxa Máxima de Criação de Fluxos de Entrada|CountPerSecond|Média|A taxa de criação máxima de fluxos de entrada (tráfego entrando na VM)|VMName|
|Entrada na rede|Sim|Rede em Faturável (Preterido)|Bytes|Total|O número de bytes faturáveis recebidos em todas as interfaces de rede pelas máquinas virtuais (tráfego de entrada) (preterido)|VMName|
|Rede no Total|Sim|Rede no Total|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela máquina virtual ou pelas máquinas virtuais (tráfego de entrada)|VMName|
|Saída da rede|Sim|Rede Fora de Faturável (Preterido)|Bytes|Total|O número de bytes faturáveis de saída em todas as interfaces de rede pelas máquinas virtuais (tráfego de saída) (preterido)|VMName|
|Rede Fora do Total|Sim|Rede Fora do Total|Bytes|Total|O número de bytes de saída em todas as interfaces de rede pela máquina virtual ou pelas máquinas virtuais (tráfego de saída)|VMName|
|Profundidade da Fila de Disco do SO|Sim|Profundidade da Fila de Disco do SO (Versão prévia)|Contagem|Média|Profundidade da Fila do Disco do SO (ou Tamanho da Fila)|VMName|
|Bytes de Leitura do Disco do SO/s|Sim|Bytes de Leitura do Disco do SO/s (Visualização)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento para o disco do sistema operacional|VMName|
|Operações de Leitura do Disco do SO/s|Sim|Operações de Leitura do Disco do SO/s (Visualização)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|VMName|
|Bytes de Gravação do Disco do SO/s|Sim|Bytes de Gravação do Disco do SO/s (Visualização)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento do disco do sistema operacional|VMName|
|Operações de Gravação do Disco do SO/s|Sim|Operações de Gravação do Disco do SO/s (Visualização)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|VMName|
|Os Por QD de Disco|Sim|QD de Disco do SO (Preterido)|Contagem|Média|Profundidade da Fila do Disco do SO (ou Tamanho da Fila)|Sem dimensões|
|SO Por Bytes de Leitura do Disco/s|Sim|Bytes de Leitura do Disco do SO/s (Preterido)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento para o disco do sistema operacional|Sem dimensões|
|SO Por Operações de Leitura do Disco/s|Sim|Operações de Leitura do Disco do SO/s (Preterido)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Sem dimensões|
|SO Por Bytes de Gravação do Disco/s|Sim|Bytes de Gravação do Disco do SO/s (Preterido)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento do disco do sistema operacional|Sem dimensões|
|SO Por Operações de Gravação do Disco/s|Sim|Operações de Gravação do Disco do SO/s (Preterido)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Sem dimensões|
|Fluxos de Saída|Sim|Fluxos de Saída|Contagem|Média|Fluxos de saída são o número de fluxos atuais na direção de saída (tráfego saindo da VM)|VMName|
|Taxa Máxima de Criação de Fluxos de Saída|Sim|Taxa Máxima de Criação de Fluxos de Saída|CountPerSecond|Média|A taxa de criação máxima de fluxos de saída (tráfego saindo da VM)|VMName|
|Por QD de Disco|Sim|QD do Disco de Dados (Preterido)|Contagem|Média|Profundidade da Fila do Disco de Dados (ou Tamanho da Fila)|SlotId|
|Por Bytes de Leitura do Disco/s|Sim|Bytes de Leitura do Disco de Dados/s (Preterido)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento|SlotId|
|Por operações de Leitura de Disco/s|Sim|Operações de Leitura do Disco de Dados/s (Preterido)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento|SlotId|
|Por Bytes de Gravação de Disco/s|Sim|Bytes de Gravação do Disco de Dados/s (Preterido)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento|SlotId|
|Por Operações de Gravação de Disco/s|Sim|Operações de Gravação do Disco de Dados/s (Preterido)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento|SlotId|
|Percentual de CPU|Sim|Porcentagem de CPU|Porcentagem|Média|A porcentagem das unidades de computação alocadas que estão atualmente em uso pela máquina virtual ou pelas máquinas virtuais|VMName|
|Ocorrência de Leitura do Cache de Disco de Dados Premium|Sim|Ocorrência de Leitura do Cache de Disco de Dados Premium (Versão prévia)|Porcentagem|Média|Ocorrência de Leitura do Cache de Disco de Dados Premium|LUN, VMName|
|Ausência de Leitura do Cache de Disco de Dados Premium|Sim|Ausência de Leitura do Cache de Disco de Dados Premium (Versão prévia)|Porcentagem|Média|Ausência de Leitura do Cache de Disco de Dados Premium|LUN, VMName|
|Ocorrência de Leitura do Cache de Disco do SO Premium|Sim|Ocorrência de Leitura do Cache de Disco do SO Premium (Versão prévia)|Porcentagem|Média|Ocorrência de Leitura do Cache de Disco do SO Premium|VMName|
|Ausência de Leitura do Cache de Disco de SO Premium|Sim|Ausência de Leitura do Cache de Disco de SO Premium (Versão prévia)|Porcentagem|Média|Ausência de Leitura do Cache de Disco de SO Premium|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Créditos de CPU Consumidos|Sim|Créditos de CPU Consumidos|Contagem|Média|Número total de créditos consumido pela Máquina Virtual|Sem dimensões|
|Créditos de CPU Restantes|Sim|Créditos de CPU Restantes|Contagem|Média|Número total de créditos disponíveis para intermitência|Sem dimensões|
|Profundidade da Fila de Disco de Dados|Sim|Profundidade da Fila de Disco de Dados (Versão prévia)|Contagem|Média|Profundidade da Fila do Disco de Dados (ou Tamanho da Fila)|LUN|
|Bytes de Leitura do Disco de Dados/s|Sim|Bytes de Leitura do Disco de Dados/s (Visualização)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento|LUN|
|Operações de Leitura do Disco de Dados/s|Sim|Operações de Leitura do Disco de Dados/s (Visualização)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento|LUN|
|Bytes de Gravação do Disco de Dados/s|Sim|Bytes de Gravação do Disco de Dados/s (Visualização)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento|LUN|
|Operações de Gravação do Disco de Dados/s|Sim|Operações de Gravação do Disco de Dados/s (Visualização)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento|LUN|
|Bytes de leitura de disco|Sim|Bytes de leitura de disco|Bytes|Total|Bytes lidos do disco durante o período de monitoramento|Sem dimensões|
|Operações de leitura de disco/Seg|Sim|Operações de leitura de disco/Seg|CountPerSecond|Média|IOPS de leitura de disco|Sem dimensões|
|Bytes de gravação de disco|Sim|Bytes de gravação de disco|Bytes|Total|Bytes gravados no disco durante o período de monitoramento|Sem dimensões|
|Operações de gravação de disco/Seg|Sim|Operações de gravação de disco/Seg|CountPerSecond|Média|IOPS de gravação de disco|Sem dimensões|
|Fluxos de Entrada|Sim|Fluxos de Entrada|Contagem|Média|Os fluxos de entrada são o número de fluxos atuais na direção de entrada (tráfego entrando na VM)|Sem dimensões|
|Taxa Máxima de Criação de Fluxos de Entrada|Sim|Taxa Máxima de Criação de Fluxos de Entrada|CountPerSecond|Média|A taxa de criação máxima de fluxos de entrada (tráfego entrando na VM)|Sem dimensões|
|Entrada na rede|Sim|Rede em Faturável (Preterido)|Bytes|Total|O número de bytes faturáveis recebidos em todas as interfaces de rede pelas máquinas virtuais (tráfego de entrada) (preterido)|Sem dimensões|
|Rede no Total|Sim|Rede no Total|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela máquina virtual ou pelas máquinas virtuais (tráfego de entrada)|Sem dimensões|
|Saída da rede|Sim|Rede Fora de Faturável (Preterido)|Bytes|Total|O número de bytes faturáveis de saída em todas as interfaces de rede pelas máquinas virtuais (tráfego de saída) (preterido)|Sem dimensões|
|Rede Fora do Total|Sim|Rede Fora do Total|Bytes|Total|O número de bytes de saída em todas as interfaces de rede pela máquina virtual ou pelas máquinas virtuais (tráfego de saída)|Sem dimensões|
|Profundidade da Fila de Disco do SO|Sim|Profundidade da Fila de Disco do SO (Versão prévia)|Contagem|Média|Profundidade da Fila do Disco do SO (ou Tamanho da Fila)|Sem dimensões|
|Bytes de Leitura do Disco do SO/s|Sim|Bytes de Leitura do Disco do SO/s (Visualização)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento para o disco do sistema operacional|Sem dimensões|
|Operações de Leitura do Disco do SO/s|Sim|Operações de Leitura do Disco do SO/s (Visualização)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Sem dimensões|
|Bytes de Gravação do Disco do SO/s|Sim|Bytes de Gravação do Disco do SO/s (Visualização)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento do disco do sistema operacional|Sem dimensões|
|Operações de Gravação do Disco do SO/s|Sim|Operações de Gravação do Disco do SO/s (Visualização)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Sem dimensões|
|Os Por QD de Disco|Sim|QD de Disco do SO (Preterido)|Contagem|Média|Profundidade da Fila do Disco do SO (ou Tamanho da Fila)|Sem dimensões|
|SO Por Bytes de Leitura do Disco/s|Sim|Bytes de Leitura do Disco do SO/s (Preterido)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento para o disco do sistema operacional|Sem dimensões|
|SO Por Operações de Leitura do Disco/s|Sim|Operações de Leitura do Disco do SO/s (Preterido)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Sem dimensões|
|SO Por Bytes de Gravação do Disco/s|Sim|Bytes de Gravação do Disco do SO/s (Preterido)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento do disco do sistema operacional|Sem dimensões|
|SO Por Operações de Gravação do Disco/s|Sim|Operações de Gravação do Disco do SO/s (Preterido)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Sem dimensões|
|Fluxos de Saída|Sim|Fluxos de Saída|Contagem|Média|Fluxos de saída são o número de fluxos atuais na direção de saída (tráfego saindo da VM)|Sem dimensões|
|Taxa Máxima de Criação de Fluxos de Saída|Sim|Taxa Máxima de Criação de Fluxos de Saída|CountPerSecond|Média|A taxa de criação máxima de fluxos de saída (tráfego saindo da VM)|Sem dimensões|
|Por QD de Disco|Sim|QD do Disco de Dados (Preterido)|Contagem|Média|Profundidade da Fila do Disco de Dados (ou Tamanho da Fila)|SlotId|
|Por Bytes de Leitura do Disco/s|Sim|Bytes de Leitura do Disco de Dados/s (Preterido)|CountPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento|SlotId|
|Por operações de Leitura de Disco/s|Sim|Operações de Leitura do Disco de Dados/s (Preterido)|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento|SlotId|
|Por Bytes de Gravação de Disco/s|Sim|Bytes de Gravação do Disco de Dados/s (Preterido)|CountPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento|SlotId|
|Por Operações de Gravação de Disco/s|Sim|Operações de Gravação do Disco de Dados/s (Preterido)|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento|SlotId|
|Percentual de CPU|Sim|Porcentagem de CPU|Porcentagem|Média|A porcentagem das unidades de computação alocadas que estão atualmente em uso pela máquina virtual ou pelas máquinas virtuais|Sem dimensões|
|Ocorrência de Leitura do Cache de Disco de Dados Premium|Sim|Ocorrência de Leitura do Cache de Disco de Dados Premium (Versão prévia)|Porcentagem|Média|Ocorrência de Leitura do Cache de Disco de Dados Premium|LUN|
|Ausência de Leitura do Cache de Disco de Dados Premium|Sim|Ausência de Leitura do Cache de Disco de Dados Premium (Versão prévia)|Porcentagem|Média|Ausência de Leitura do Cache de Disco de Dados Premium|LUN|
|Ocorrência de Leitura do Cache de Disco do SO Premium|Sim|Ocorrência de Leitura do Cache de Disco do SO Premium (Versão prévia)|Porcentagem|Média|Ocorrência de Leitura do Cache de Disco do SO Premium|Sem dimensões|
|Ausência de Leitura do Cache de Disco de SO Premium|Sim|Ausência de Leitura do Cache de Disco de SO Premium (Versão prévia)|Porcentagem|Média|Ausência de Leitura do Cache de Disco de SO Premium|Sem dimensões|


## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|CpuUsage|Sim|Uso da CPU|Contagem|Média|Uso de CPU em todos os núcleos em milicores.|containerName|
|MemoryUsage|Sim|Uso de Memória|Bytes|Média|Uso de memória total em bytes.|containerName|
|NetworkBytesReceivedPerSecond|Sim|Bytes de Rede Recebidos por Segundo|Bytes|Média|Os bytes de rede recebidos por segundo.|Sem dimensões|
|NetworkBytesTransmittedPerSecond|Sim|Bytes de Rede Transmitidos por Segundo|Bytes|Média|Os bytes de rede transmitidos por segundo.|Sem dimensões|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|AgentPoolCPUTime|Sim|AgentPool tempo de CPU|Segundos|Total|AgentPool tempo de CPU em segundos|Sem dimensões|
|RunDuration|Sim|Duração da Execução|Milissegundos|Total|Duração da execução em milissegundos|Sem dimensões|
|SuccessfulPullCount|Sim|Contagem de Pull com Êxito|Contagem|Média|Número de pulls de imagem bem-sucedidos|Sem dimensões|
|SuccessfulPushCount|Sim|Contagem de Push com Êxito|Contagem|Média|Número de Pushes de imagem bem-sucedidos|Sem dimensões|
|TotalPullCount|Sim|Contagem Total de Pull|Contagem|Média|Número de pulls de imagem no total|Sem dimensões|
|TotalPushCount|Sim|Contagem Total de Push|Contagem|Média|Número de envios por push de imagem no total|Sem dimensões|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Não|Número total de núcleos de cpu disponíveis em um cluster gerenciado|Contagem|Média|Número total de núcleos de cpu disponíveis em um cluster gerenciado|Sem dimensões|
|kube_node_status_allocatable_memory_bytes|Não|Número total de memória disponível em um cluster gerenciado|Bytes|Média|Número total de memória disponível em um cluster gerenciado|Sem dimensões|
|kube_node_status_condition|Não|Status para as várias condições de nó|Contagem|Média|Status para as várias condições de nó|condição, status, status2, nó|
|kube_pod_status_phase|Não|Número de pods por fase|Contagem|Média|Número de pods por fase|fase, namespace, pod|
|kube_pod_status_ready|Não|Número de compartimentos no estado Pronto|Contagem|Média|Número de compartimentos no estado Pronto|namespace, pod|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|FailedRequests|Sim|Solicitações com falha|Contagem|Total|Obtém os logs disponíveis para provedores de recursos personalizados|HttpMethod, CallPath, StatusCode|
|SuccessfullRequests|Sim|Solicitações bem sucedidas|Contagem|Total|Solicitações bem-sucedidas feitas pelo provedor personalizado|HttpMethod, CallPath, StatusCode|


## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|AvailableCapacity|Sim|Capacidade Disponível|Bytes|Média|A capacidade disponível em bytes durante o período de relatório.|Sem dimensões|
|BytesUploadedToCloud|Sim|Bytes Carregados na Nuvem (Dispositivo)|Bytes|Média|O número total de bytes que são carregados no Azure de um dispositivo durante o período de relatório.|Sem dimensões|
|BytesUploadedToCloudPerShare|Sim|Bytes Carregados na Nuvem (Compartilhamento)|Bytes|Média|O número total de bytes que são carregados no Azure de um compartilhamento durante o período de relatório.|Compartilhar|
|CloudReadThroughput|Sim|Taxa de Transferência de Download na Nuvem|BytesPerSecond|Média|A taxa de transferência de download de nuvem para o Azure durante o período de relatório.|Sem dimensões|
|CloudReadThroughputPerShare|Sim|Taxa de Transferência de Downloads na Nuvem (Compartilhamento)|BytesPerSecond|Média|A taxa de transferência de download para o Azure de um compartilhamento durante o período de relatório.|Compartilhar|
|CloudUploadThroughput|Sim|Taxa de Transferência de Uploads na Nuvem|BytesPerSecond|Média|A taxa de transferência de upload de nuvem para o Azure durante o período de relatório.|Sem dimensões|
|CloudUploadThroughputPerShare|Sim|Taxa de Transferência de Uploads na Nuvem (Compartilhamento)|BytesPerSecond|Média|A taxa de transferência de upload para o Azure de um compartilhamento durante o período de relatório.|Compartilhar|
|HyperVMemoryUtilization|Sim|Computação de Borda – Uso de Memória|Porcentagem|Média|Quantidade de RAM em uso|InstanceName|
|HyperVVirtualProcessorUtilization|Sim|Computação de Borda – Percentual de CPU|Porcentagem|Média|Porcentagem de uso da CPU|InstanceName|
|NICReadThroughput|Sim|Taxa de Transferência de Leitura (Rede)|BytesPerSecond|Média|A taxa de transferência de leitura da interface de rede no dispositivo no período de relatório para todos os volumes no gateway.|InstanceName|
|NICWriteThroughput|Sim|Taxa de Transferência de Gravações (Rede)|BytesPerSecond|Média|A taxa de transferência de gravação da interface de rede no dispositivo no período de relatório para todos os volumes no gateway.|InstanceName|
|TotalCapacity|Sim|Capacidade Total|Bytes|Média|Capacidade Total|Sem dimensões|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|FailedRuns|Sim|Execuções com falha|Contagem|Total||pipelinename, ActivityName|
|SuccessfulRuns|Sim|Execuções com êxito|Contagem|Total||pipelinename, ActivityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|ActivityCancelledRuns|Sim|Métricas de execuções de atividades canceladas|Contagem|Total||ActivityType, PipelineName, FailureType, Nome|
|ActivityFailedRuns|Sim|Métricas de execução de atividades com falha|Contagem|Total||ActivityType, PipelineName, FailureType, Nome|
|ActivitySucceededRuns|Sim|Métricas de execução de atividades bem-sucedidas|Contagem|Total||ActivityType, PipelineName, FailureType, Nome|
|FactorySizeInGbUnits|Sim|Tamanho total do alocador (unidade GB)|Contagem|Máximo||Sem dimensões|
|IntegrationRuntimeAvailableMemory|Sim|Memória disponível de runtime de integração|Bytes|Média||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAvailableNodeNumber|Sim|Contagem de nós disponíveis do Integration Runtime|Contagem|Média||IntegrationRuntimeName|
|IntegrationRuntimeAverageTaskPickupDelay|Sim|Duração da fila do runtime de integração|Segundos|Média||IntegrationRuntimeName|
|IntegrationRuntimeCpuPercentage|Sim|Utilização de CPU de runtime de integração|Porcentagem|Média||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeQueueLength|Sim|Tamanho da fila do runtime de integração|Contagem|Média||IntegrationRuntimeName|
|MaxAllowedFactorySizeInGbUnits|Sim|Tamanho máximo de alocador permitido (unidade GB)|Contagem|Máximo||Sem dimensões|
|MaxAllowedResourceCount|Sim|Contagem máxima de entidades permitidas|Contagem|Máximo||Sem dimensões|
|PipelineCancelledRuns|Sim|Métricas de execução do pipeline canceladas|Contagem|Total||FailureType, Nome|
|PipelineFailedRuns|Sim|Métricas de execução do pipeline com falha|Contagem|Total||FailureType, Nome|
|PipelineSucceededRuns|Sim|Métricas de execução do pipeline bem-sucedido|Contagem|Total||FailureType, Nome|
|ResourceCount|Sim|Contagem total de entidades|Contagem|Máximo||Sem dimensões|
|TriggerCancelledRuns|Sim|Métricas de execuções do gatilho canceladas|Contagem|Total||Nome, FailureType|
|TriggerFailedRuns|Sim|Métricas de execuções do gatilho com falha|Contagem|Total||Nome, FailureType|
|TriggerSucceededRuns|Sim|Métricas de execuções do gatilho bem-sucedidas|Contagem|Total||Nome, FailureType|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|DataRead|Sim|Leitura de dados|Bytes|Total|Quantidade total de dados lidos na conta.|Sem dimensões|
|DataWritten|Sim|Dados gravados|Bytes|Total|Quantidade total de dados gravados na conta.|Sem dimensões|
|ReadRequests|Sim|Solicitações de leitura|Contagem|Total|Contagem solicitações de leitura de dados para a conta.|Sem dimensões|
|TotalStorage|Sim|Armazenamento total|Bytes|Máximo|Quantidade total de dados armazenados na conta.|Sem dimensões|
|WriteRequests|Sim|Solicitações de gravação|Contagem|Total|Contagem de solicitações de gravação de dados para a conta.|Sem dimensões|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|active_connections|Sim|Conexões ativas|Contagem|Média|Conexões ativas|Sem dimensões|
|backup_storage_used|Sim|Armazenamento de Backup usado|Bytes|Média|Armazenamento de Backup usado|Sem dimensões|
|connections_failed|Sim|Conexões com falha|Contagem|Total|Conexões com falha|Sem dimensões|
|cpu_percent|Sim|Porcentagem de CPU|Porcentagem|Média|Porcentagem de CPU|Sem dimensões|
|io_consumption_percent|Sim|Porcentagem de E/S|Porcentagem|Média|Porcentagem de E/S|Sem dimensões|
|memory_percent|Sim|Porcentagem de memória|Porcentagem|Média|Porcentagem de memória|Sem dimensões|
|network_bytes_egress|Sim|Saída da rede|Bytes|Total|Saída de Rede em conexões ativas|Sem dimensões|
|network_bytes_ingress|Sim|Entrada na rede|Bytes|Total|Entrada de Rede em conexões ativas|Sem dimensões|
|seconds_behind_master|Sim|Retardo de replicação em segundos|Contagem|Máximo|Retardo de replicação em segundos|Sem dimensões|
|serverlog_storage_limit|Sim|Limite de armazenamento do Log do Servidor|Bytes|Média|Limite de armazenamento do Log do Servidor|Sem dimensões|
|serverlog_storage_percent|Sim|Porcentagem de armazenamento do Log do Servidor|Porcentagem|Média|Porcentagem de armazenamento do Log do Servidor|Sem dimensões|
|serverlog_storage_usage|Sim|Armazenamento do Log do Servidor usado|Bytes|Média|Armazenamento do Log do Servidor usado|Sem dimensões|
|storage_limit|Sim|Limite de armazenamento|Bytes|Máximo|Limite de armazenamento|Sem dimensões|
|storage_percent|Sim|Porcentagem de armazenamento|Porcentagem|Média|Porcentagem de armazenamento|Sem dimensões|
|storage_used|Sim|Armazenamento usado|Bytes|Média|Armazenamento usado|Sem dimensões|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|active_connections|Sim|Conexões ativas|Contagem|Média|Conexões ativas|Sem dimensões|
|backup_storage_used|Sim|Armazenamento de Backup usado|Bytes|Média|Armazenamento de Backup usado|Sem dimensões|
|connections_failed|Sim|Conexões com falha|Contagem|Total|Conexões com falha|Sem dimensões|
|cpu_percent|Sim|Porcentagem de CPU|Porcentagem|Média|Porcentagem de CPU|Sem dimensões|
|io_consumption_percent|Sim|Porcentagem de E/S|Porcentagem|Média|Porcentagem de E/S|Sem dimensões|
|memory_percent|Sim|Porcentagem de memória|Porcentagem|Média|Porcentagem de memória|Sem dimensões|
|network_bytes_egress|Sim|Saída da rede|Bytes|Total|Saída de Rede em conexões ativas|Sem dimensões|
|network_bytes_ingress|Sim|Entrada na rede|Bytes|Total|Entrada de Rede em conexões ativas|Sem dimensões|
|seconds_behind_master|Sim|Retardo de replicação em segundos|Contagem|Máximo|Retardo de replicação em segundos|Sem dimensões|
|serverlog_storage_limit|Sim|Limite de armazenamento do Log do Servidor|Bytes|Máximo|Limite de armazenamento do Log do Servidor|Sem dimensões|
|serverlog_storage_percent|Sim|Porcentagem de armazenamento do Log do Servidor|Porcentagem|Média|Porcentagem de armazenamento do Log do Servidor|Sem dimensões|
|serverlog_storage_usage|Sim|Armazenamento do Log do Servidor usado|Bytes|Média|Armazenamento do Log do Servidor usado|Sem dimensões|
|storage_limit|Sim|Limite de armazenamento|Bytes|Máximo|Limite de armazenamento|Sem dimensões|
|storage_percent|Sim|Porcentagem de armazenamento|Porcentagem|Média|Porcentagem de armazenamento|Sem dimensões|
|storage_used|Sim|Armazenamento usado|Bytes|Média|Armazenamento usado|Sem dimensões|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|active_connections|Sim|Conexões ativas|Contagem|Média|Conexões ativas|Sem dimensões|
|backup_storage_used|Sim|Armazenamento de Backup usado|Bytes|Média|Armazenamento de Backup usado|Sem dimensões|
|connections_failed|Sim|Conexões com falha|Contagem|Total|Conexões com falha|Sem dimensões|
|cpu_percent|Sim|Porcentagem de CPU|Porcentagem|Média|Porcentagem de CPU|Sem dimensões|
|io_consumption_percent|Sim|Porcentagem de E/S|Porcentagem|Média|Porcentagem de E/S|Sem dimensões|
|memory_percent|Sim|Porcentagem de memória|Porcentagem|Média|Porcentagem de memória|Sem dimensões|
|network_bytes_egress|Sim|Saída da rede|Bytes|Total|Saída de Rede em conexões ativas|Sem dimensões|
|network_bytes_ingress|Sim|Entrada na rede|Bytes|Total|Entrada de Rede em conexões ativas|Sem dimensões|
|pg_replica_log_delay_in_bytes|Sim|Retardo Máximo entre Réplicas|Bytes|Máximo|Atraso em bytes da réplica mais defasante|Sem dimensões|
|pg_replica_log_delay_in_seconds|Sim|Retardo da Réplica|Segundos|Máximo|Atraso de réplica em segundos|Sem dimensões|
|serverlog_storage_limit|Sim|Limite de armazenamento do Log do Servidor|Bytes|Máximo|Limite de armazenamento do Log do Servidor|Sem dimensões|
|serverlog_storage_percent|Sim|Porcentagem de armazenamento do Log do Servidor|Porcentagem|Média|Porcentagem de armazenamento do Log do Servidor|Sem dimensões|
|serverlog_storage_usage|Sim|Armazenamento do Log do Servidor usado|Bytes|Média|Armazenamento do Log do Servidor usado|Sem dimensões|
|storage_limit|Sim|Limite de armazenamento|Bytes|Máximo|Limite de armazenamento|Sem dimensões|
|storage_percent|Sim|Porcentagem de armazenamento|Porcentagem|Média|Porcentagem de armazenamento|Sem dimensões|
|storage_used|Sim|Armazenamento usado|Bytes|Média|Armazenamento usado|Sem dimensões|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|active_connections|Sim|Conexões ativas|Contagem|Média|Conexões ativas|Sem dimensões|
|cpu_percent|Sim|Porcentagem de CPU|Porcentagem|Média|Porcentagem de CPU|Sem dimensões|
|iops|Sim|IOPS|Contagem|Média|Operações de e/s por segundo|Sem dimensões|
|memory_percent|Sim|Porcentagem de memória|Porcentagem|Média|Porcentagem de memória|Sem dimensões|
|network_bytes_egress|Sim|Saída da rede|Bytes|Total|Saída de Rede em conexões ativas|Sem dimensões|
|network_bytes_ingress|Sim|Entrada na rede|Bytes|Total|Entrada de Rede em conexões ativas|Sem dimensões|
|storage_percent|Sim|Porcentagem de armazenamento|Porcentagem|Média|Porcentagem de armazenamento|Sem dimensões|
|storage_used|Sim|Armazenamento usado|Bytes|Média|Armazenamento usado|Sem dimensões|


## <a name="microsoftdbforpostgresqlsingleservers"></a>Microsoft. DBforPostgreSQL/singleservers

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|active_connections|Sim|Conexões ativas|Contagem|Média|Conexões ativas|Sem dimensões|
|connections_failed|Sim|Conexões com falha|Contagem|Total|Conexões com falha|Sem dimensões|
|connections_succeeded|Sim|Conexões com êxito|Contagem|Total|Conexões com êxito|Sem dimensões|
|cpu_percent|Sim|Porcentagem de CPU|Porcentagem|Média|Porcentagem de CPU|Sem dimensões|
|iops|Sim|IOPS|Contagem|Média|Operações de e/s por segundo|Sem dimensões|
|maximum_used_transactionIDs|Sim|Máximo de IDs de transação usadas|Contagem|Média|Máximo de IDs de transação usadas|Sem dimensões|
|memory_percent|Sim|Porcentagem de memória|Porcentagem|Média|Porcentagem de memória|Sem dimensões|
|network_bytes_egress|Sim|Saída da rede|Bytes|Total|Saída de Rede em conexões ativas|Sem dimensões|
|network_bytes_ingress|Sim|Entrada na rede|Bytes|Total|Entrada de Rede em conexões ativas|Sem dimensões|
|storage_percent|Sim|Porcentagem de armazenamento|Porcentagem|Média|Porcentagem de armazenamento|Sem dimensões|
|storage_used|Sim|Armazenamento usado|Bytes|Média|Armazenamento usado|Sem dimensões|


## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|c2d.commands.egress.abandon.success|Sim|Mensagens C2D abandonadas|Contagem|Total|Número de mensagens da nuvem para o dispositivo abandonadas pelo dispositivo|Sem dimensões|
|c2d.commands.egress.complete.success|Sim|Entregas de mensagem C2D concluídas|Contagem|Total|Número de entregas de mensagem da nuvem para o dispositivo concluídas com êxito pelo dispositivo|Sem dimensões|
|c2d.commands.egress.reject.success|Sim|Mensagens C2D rejeitadas|Contagem|Total|Número de mensagens da nuvem para o dispositivo rejeitadas pelo dispositivo|Sem dimensões|
|c2d.methods.failure|Sim|Invocações de método diretas com falhas|Contagem|Total|A contagem de todas as chamadas de método diretas com falhas.|Sem dimensões|
|c2d.methods.requestSize|Sim|Tamanho da solicitação das invocações de método diretas|Bytes|Média|A média, o mínimo e o máximo de todas as solicitações de método diretas bem-sucedidas.|Sem dimensões|
|c2d.methods.responseSize|Sim|Tamanho da resposta das invocações de método diretas|Bytes|Média|A média, o mínimo e o máximo de todas as respostas de método diretas bem-sucedidas.|Sem dimensões|
|c2d.methods.success|Sim|Invocações de método diretas bem-sucedidas|Contagem|Total|A contagem de todas as chamadas de método diretas bem-sucedidas.|Sem dimensões|
|c2d.twin.read.failure|Sim|Leituras de gêmeos de back-end com falhas|Contagem|Total|A contagem de todas as falhas de leituras de gêmeos iniciadas pelo back-end.|Sem dimensões|
|c2d.twin.read.size|Sim|Tamanho da resposta das leituras de gêmeos de back-end|Bytes|Média|A média, o mínimo e o máximo de todas as leituras de gêmeos bem-sucedidas iniciadas pelo back-end.|Sem dimensões|
|c2d.twin.read.success|Sim|Leituras de gêmeos de back-end bem-sucedidas|Contagem|Total|A contagem de todas as leituras de gêmeos bem-sucedidas iniciadas pelo back-end.|Sem dimensões|
|c2d.twin.update.failure|Sim|Atualizações de gêmeos de back-end com falhas|Contagem|Total|A contagem de todas as falhas de atualizações de gêmeos iniciadas pelo back-end.|Sem dimensões|
|c2d.twin.update.size|Sim|Tamanho das atualizações de gêmeos de back-end|Bytes|Média|A média, o mínimo e o máximo de todas as atualizações de gêmeos iniciadas pelo back-end bem-sucedidas.|Sem dimensões|
|c2d.twin.update.success|Sim|Atualizações de gêmeos de back-end bem-sucedidas|Contagem|Total|A contagem de todas as atualizações de gêmeos iniciadas pelo back-end bem-sucedidas.|Sem dimensões|
|C2DMessagesExpired|Sim|Mensagens C2D expiradas (versão prévia)|Contagem|Total|Número de mensagens de nuvem para o dispositivo expiradas|Sem dimensões|
|configurações|Sim|Métricas de configuração|Contagem|Total|Métricas para operações de configuração|Sem dimensões|
|connectedDeviceCount|Não|Dispositivos conectados (versão prévia)|Contagem|Média|Número de dispositivos registrados ao seu hub IoT|Sem dimensões|
|d2c.endpoints.egress.builtIn.events|Sim|Roteamento: mensagens entregues a mensagens/eventos|Contagem|Total|O número de vezes que o roteamento do Hub IoT entregou com êxito mensagens ao ponto de extremidade interno (mensagens/eventos).|Sem dimensões|
|d2c.endpoints.egress.eventHubs|Sim|Roteamento: mensagens entregues ao Hub de Eventos|Contagem|Total|O número de vezes que o roteamento do Hub IoT entregou com êxito mensagens aos pontos de extremidade do Hub de Eventos.|Sem dimensões|
|d2c.endpoints.egress.serviceBusQueues|Sim|Roteamento: mensagens entregues à Fila do Barramento de Serviço|Contagem|Total|O número de vezes que o roteamento do Hub IoT entregou com êxito mensagens aos pontos de extremidade da fila do Barramento de Serviço.|Sem dimensões|
|d2c.endpoints.egress.serviceBusTopics|Sim|Roteamento: mensagens entregues ao Tópico do Barramento de Serviço|Contagem|Total|O número de vezes que o roteamento do Hub IoT entregou com êxito mensagens aos pontos de extremidade do tópico do Barramento de Serviço.|Sem dimensões|
|d2c.endpoints.egress.storage|Sim|Roteamento: mensagens entregues ao armazenamento|Contagem|Total|O número de vezes que o roteamento do Hub IoT entregou com êxito mensagens aos pontos de extremidade de armazenamento.|Sem dimensões|
|d2c.endpoints.egress.storage.blobs|Sim|Roteamento: blobs entregues ao armazenamento|Contagem|Total|O número de vezes que o roteamento do Hub IoT entregou blobs aos pontos de extremidade de armazenamento.|Sem dimensões|
|d2c.endpoints.egress.storage.bytes|Sim|Roteamento: dados entregues ao armazenamento|Bytes|Total|A quantidade de dados (bytes) de roteamento do Hub IoT entregue aos pontos de extremidade de armazenamento.|Sem dimensões|
|d2c.Endpoints.Latency.builtIn.Events|Sim|Roteamento: latência de mensagem para mensagens/eventos|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem no Hub IoT e a entrada da mensagem de telemetria no ponto de extremidade interno (mensagens/eventos).|Sem dimensões|
|d2c.endpoints.latency.eventHubs|Sim|Roteamento: latência de mensagem para o Hub de Eventos|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem no Hub IoT e a entrada da mensagem em um ponto de extremidade do Hub de Eventos.|Sem dimensões|
|d2c.endpoints.latency.serviceBusQueues|Sim|Roteamento: latência de mensagem para a Fila do Barramento de Serviço|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem no Hub IoT e a entrada da mensagem de telemetria em um ponto de extremidade da fila do Barramento de Serviço.|Sem dimensões|
|d2c.endpoints.latency.serviceBusTopics|Sim|Roteamento: latência de mensagem para o Tópico do Barramento de Serviço|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem no Hub IoT e a entrada da mensagem de telemetria em um ponto de extremidade do tópico do Barramento de Serviço.|Sem dimensões|
|d2c.endpoints.latency.storage|Sim|Roteamento: latência de mensagem para armazenamento|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem no Hub IoT e a entrada da mensagem de telemetria em um ponto de extremidade de armazenamento.|Sem dimensões|
|d2c.telemetry.egress.dropped|Sim|Roteamento: mensagens de telemetria removidas |Contagem|Total|O número de vezes que as mensagens foram removidas pelo roteamento do Hub IoT devido à inatividade dos pontos de extremidade. Esse valor não conta as mensagens entregues à rota de fallback, pois as mensagens removidas não são entregues.|Sem dimensões|
|d2c.telemetry.egress.fallback|Sim|Roteamento: mensagens entregues ao fallback|Contagem|Total|O número de vezes que o roteamento do Hub IoT entregou mensagens ao ponto de extremidade associado à rota de fallback.|Sem dimensões|
|d2c.telemetry.egress.invalid|Sim|Roteamento: mensagens de telemetria incompatíveis|Contagem|Total|O número de vezes que o roteamento do Hub IoT não conseguiu entregar mensagens devido a uma incompatibilidade com o ponto de extremidade. Esse valor não inclui novas tentativas.|Sem dimensões|
|d2c.telemetry.egress.orphaned|Sim|Roteamento: mensagens de telemetria órfãs |Contagem|Total|O número de vezes que as mensagens ficaram órfãos do roteamento do Hub IoT porque não correspondiam a nenhuma regra de roteamento (incluindo a regra de fallback). |Sem dimensões|
|d2c.telemetry.egress.success|Sim|Roteamento: mensagens de telemetria entregues|Contagem|Total|O número de vezes que as mensagens foram entregues com êxito a todos os pontos de extremidade usando o roteamento do Hub IoT. Se uma mensagem é encaminhada para vários pontos de extremidade, esse valor aumenta em um para cada entrega bem-sucedida. Se uma mensagem é entregue ao mesmo ponto de extremidade várias vezes, esse valor aumenta em um para cada entrega bem-sucedida.|Sem dimensões|
|d2c.telemetry.ingress.allProtocol|Sim|Tentativas de envio de mensagem de telemetria|Contagem|Total|Número de tentativas de envio de mensagens de telemetria do dispositivo para nuvem para o hub IoT|Sem dimensões|
|d2c.telemetry.ingress.sendThrottle|Sim|Número de erros de limitação|Contagem|Total|Número de erros de limitação devido a limitações da taxa de transferência do dispositivo|Sem dimensões|
|d2c.telemetry.ingress.success|Sim|Mensagens de telemetria enviadas|Contagem|Total|Número de mensagens de telemetria do dispositivo para nuvem enviadas com êxito para o hub IoT|Sem dimensões|
|d2c.twin.read.failure|Sim|Leituras de gêmeos dos dispositivos com falhas|Contagem|Total|A contagem de todas as leituras de gêmeos iniciadas pelo dispositivo com falhas.|Sem dimensões|
|d2c.twin.read.size|Sim|Tamanho da resposta das leituras de gêmeos dos dispositivos|Bytes|Média|A média, o mínimo e o máximo de todas as leituras de gêmeos iniciadas pelo dispositivo bem-sucedidas.|Sem dimensões|
|d2c.twin.read.success|Sim|Leituras de gêmeos dos dispositivos bem-sucedidas|Contagem|Total|A contagem de todas as leituras de gêmeos iniciadas pelo dispositivo bem-sucedidas.|Sem dimensões|
|d2c.twin.update.failure|Sim|Atualizações de gêmeos dos dispositivos com falhas|Contagem|Total|A contagem de todas as atualizações de gêmeos iniciadas pelo dispositivo.|Sem dimensões|
|d2c.twin.update.size|Sim|Tamanho das atualizações de gêmeos dos dispositivos|Bytes|Média|A média, o mínimo e o máximo de todas as atualizações de gêmeos iniciadas pelo dispositivo bem-sucedidas.|Sem dimensões|
|d2c.twin.update.success|Sim|Atualizações de gêmeos dos dispositivos bem-sucedidas|Contagem|Total|A contagem de todas as atualizações de gêmeos iniciadas pelo dispositivo bem-sucedidas.|Sem dimensões|
|dailyMessageQuotaUsed|Sim|Número total de mensagens usadas|Contagem|Máximo|Número total de mensagens usadas hoje|Sem dimensões|
|deviceDataUsage|Sim|Uso total de dados do dispositivo|Bytes|Total|Bytes transferidos entre todos os dispositivos conectados ao lotHub|Sem dimensões|
|deviceDataUsageV2|Sim|Uso total de dados de dispositivos (versão prévia)|Bytes|Total|Bytes transferidos entre todos os dispositivos conectados ao lotHub|Sem dimensões|
|devices.connectedDevices.allProtocol|Sim|Dispositivos conectados (preteridos) |Contagem|Total|Número de dispositivos registrados ao seu hub IoT|Sem dimensões|
|devices.totalDevices|Sim|Total de dispositivos (preterido)|Contagem|Total|Número de dispositivos registrados para o seu hub IoT|Sem dimensões|
|EventGridDeliveries|Sim|Entregas da grade de eventos (visualização)|Contagem|Total|O número de eventos do Hub IoT publicados na grade de eventos. Use a dimensão de resultado para o número de solicitações bem-sucedidas e com falha. A dimensão EventType mostra o tipo de evento ( https://aka.ms/ioteventgrid) .|Resultado, EventType|
|EventGridLatency|Sim|Latência da Grade de Eventos (versão prévia)|Milissegundos|Média|A latência média (milissegundos) de quando o evento do Hub IOT foi gerado quando o evento foi publicado na grade de eventos. Esse número é uma média entre todos os tipos de evento. Use a dimensão EventType para ver a latência de um tipo específico de evento.|EventType|
|jobs.cancelJob.failure|Sim|Cancelamentos de trabalho com falhas|Contagem|Total|A contagem de todas as chamadas para cancelar um trabalho com falhas.|Sem dimensões|
|jobs.cancelJob.success|Sim|Cancelamentos de trabalho bem-sucedidos|Contagem|Total|A contagem de todas as chamadas para cancelar um trabalho bem-sucedidas.|Sem dimensões|
|jobs.completed|Sim|Trabalhos concluídos|Contagem|Total|A contagem de todos os trabalhos concluídos.|Sem dimensões|
|jobs.createDirectMethodJob.failure|Sim|Criações de trabalhos de invocação de método com falhas|Contagem|Total|A contagem de todas as criações de trabalhos de invocação de método direto com falhas.|Sem dimensões|
|jobs.createDirectMethodJob.success|Sim|Criações de trabalhos de invocação de método bem-sucedidas|Contagem|Total|A contagem de todas as criações de trabalhos de invocação de método direto bem-sucedidas.|Sem dimensões|
|jobs.createTwinUpdateJob.failure|Sim|Criações de trabalhos de atualização de gêmeos com falhas|Contagem|Total|A contagem de todos os trabalhos de criação de atualização de gêmeos com falhas.|Sem dimensões|
|jobs.createTwinUpdateJob.success|Sim|Criações de trabalhos de atualização de gêmeos bem-sucedidas|Contagem|Total|A contagem de todos os trabalhos de criação de atualização de gêmeos bem-sucedidos.|Sem dimensões|
|jobs.failed|Sim|Trabalhos com falha|Contagem|Total|A contagem de todos os trabalhos com falha.|Sem dimensões|
|jobs.listJobs.failure|Sim|Chamadas para listar trabalhos com falhas|Contagem|Total|A contagem de todas as chamadas para listar trabalhos com falhas.|Sem dimensões|
|jobs.listJobs.success|Sim|Chamadas para listar trabalhos bem-sucedidas|Contagem|Total|A contagem de todas as chamadas para listar trabalhos bem-sucedidas.|Sem dimensões|
|jobs.queryJobs.failure|Sim|Consultas de trabalho com falhas|Contagem|Total|A contagem de todas as chamadas para consultas de trabalho com falhas.|Sem dimensões|
|jobs.queryJobs.success|Sim|Consultas de trabalho bem-sucedidas|Contagem|Total|A contagem de todas as chamadas para consultas de trabalho bem-sucedidas.|Sem dimensões|
|RoutingDataSizeInBytesDelivered|Sim|Tamanho da mensagem de entrega de roteamento em bytes (versão prévia)|Bytes|Total|O tamanho total em bytes de mensagens entregues pelo Hub IoT a um ponto de extremidade. Você pode usar as dimensões EndpointName e EndpointType para exibir o tamanho das mensagens em bytes entregues aos diferentes pontos de extremidade. O valor de métrica aumenta para cada mensagem entregue, incluindo se a mensagem é entregue a vários pontos de extremidade ou se a mensagem é entregue ao mesmo ponto de extremidade várias vezes.|EndpointType, EndpointName, roteamento|
|RoutingDeliveries|Sim|Entregas de roteamento (visualização)|Contagem|Total|O número de vezes que o Hub IoT tentou entregar mensagens a todos os pontos de extremidade usando o roteamento. Para ver o número de tentativas bem-sucedidas ou com falha, use a dimensão de resultado. Para ver o motivo da falha, como inválida, descartada ou órfã, use a dimensão FailureReasonCategory. Você também pode usar as dimensões EndpointName e EndpointType para entender quantas mensagens foram entregues aos diferentes pontos de extremidade. O valor da métrica aumenta em uma para cada tentativa de entrega, incluindo se a mensagem é entregue a vários pontos de extremidade ou se a mensagem é entregue ao mesmo ponto de extremidades várias vezes.|EndpointType, EndpointName, FailureReasonCategory, resultado, roteamento|
|RoutingDeliveryLatency|Sim|Latência de entrega de roteamento (visualização)|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem e o Hub IoT e a entrada da mensagem de telemetria em um ponto de extremidade. Você pode usar as dimensões EndpointName e EndpointType para entender a latência para seus pontos de extremidade diferentes.|EndpointType, EndpointName, roteamento|
|totalDeviceCount|Não|Total de dispositivos (versão prévia)|Contagem|Média|Número de dispositivos registrados para o seu hub IoT|Sem dimensões|
|twinQueries.failure|Sim|Consultas de gêmeos com falhas|Contagem|Total|A contagem de todas as consultas de gêmeos com falhas.|Sem dimensões|
|twinQueries.resultSize|Sim|Tamanho do resultado das consultas de gêmeos|Bytes|Média|A média, o mínimo e o máximo do tamanho do resultado de todas as consultas de gêmeos bem-sucedidas.|Sem dimensões|
|twinQueries.success|Sim|Consultas de gêmeos bem-sucedidas|Contagem|Total|A contagem de todas as consultas de gêmeos bem-sucedidas.|Sem dimensões|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|AttestationAttempts|Sim|Tentativas de atestado|Contagem|Total|Número de tentativas de atestado do dispositivo|ProvisioningServiceName, Status, Protocol|
|DeviceAssignments|Sim|Dispositivos atribuídos|Contagem|Total|Número de dispositivos atribuídos a um hub IoT|ProvisioningServiceName, IotHubName|
|RegistrationAttempts|Sim|Tentativas de Registro|Contagem|Total|Número de tentativas de registros do dispositivo|ProvisioningServiceName, IotHubName, Status|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Addregion|Sim|Região adicionada|Contagem|Contagem|Região adicionada|Região|
|AutoscaleMaxThroughput|Não|Taxa de transferência máxima de dimensionamento automático|Contagem|Máximo|Taxa de transferência máxima de dimensionamento automático|DatabaseName, CollectionName|
|AvailableStorage|Não|preterido Armazenamento disponível|Bytes|Total|O "armazenamento disponível" será removido do Azure Monitor no final de setembro de 2020. O tamanho do armazenamento de coleta de Cosmos DB agora é ilimitado. A única restrição é que o tamanho do armazenamento para cada chave de partição lógica é de 20 GB. Você pode habilitar o PartitionKeyStatistics no log de diagnóstico para saber o consumo de armazenamento para as principais chaves de partição. Para obter mais informações sobre Cosmos DB cota de armazenamento, consulte este documento https://docs.microsoft.com/azure/cosmos-db/concepts-limits . Após a reprovação, as demais regras de alerta ainda definidas na métrica preterida serão desabilitadas automaticamente após a data de reprovação.|CollectionName, DatabaseName, região|
|CassandraConnectionClosures|Não|Fechamentos de Conexão do Cassandra|Contagem|Total|Número de conexões Cassandra que foram fechadas, relatadas a uma granularidade de 1 minuto|Região, ClosureReason|
|CassandraConnectorAvgReplicationLatency|Não|ReplicationLatency do conector Cassandra média|MilliSeconds|Média|ReplicationLatency do conector Cassandra média|Sem dimensões|
|CassandraConnectorReplicationHealthStatus|Não|Status de integridade da replicação do conector Cassandra|Contagem|Contagem|Status de integridade da replicação do conector Cassandra|Não iniciado, ReplicationInProgress, erro|
|CassandraKeyspaceCreate|Não|Keyspace Cassandra criado|Contagem|Contagem|Keyspace Cassandra criado|Source |
|CassandraKeyspaceDelete|Não|Cassandra keyspace excluído|Contagem|Contagem|Cassandra keyspace excluído|Source |
|CassandraKeyspaceThroughputUpdate|Não|Taxa de transferência de keyspace Cassandra atualizada|Contagem|Contagem|Taxa de transferência de keyspace Cassandra atualizada|Source |
|CassandraKeyspaceUpdate|Não|Cassandra keyspace atualizado|Contagem|Contagem|Cassandra keyspace atualizado|Source |
|CassandraRequestCharges|Não|Encargos de Solicitação do Cassandra|Contagem|Total|RUs consumido para solicitações Cassandra feitas|DatabaseName, CollectionName, Region, OperationType, ResourceType|
|CassandraRequests|Não|Solicitações do Cassandra|Contagem|Contagem|Número de solicitações Cassandra feitas|DatabaseName, CollectionName, região, OperationType, ResourceType, ErrorCode|
|CassandraTableCreate|Não|Tabela Cassandra criada|Contagem|Contagem|Tabela Cassandra criada|ResourceName, ChildResourceName, |
|CassandraTableDelete|Não|Tabela Cassandra excluída|Contagem|Contagem|Tabela Cassandra excluída|ResourceName, ChildResourceName, |
|CassandraTableThroughputUpdate|Não|Taxa de transferência de tabela Cassandra atualizada|Contagem|Contagem|Taxa de transferência de tabela Cassandra atualizada|ResourceName, ChildResourceName, |
|CassandraTableUpdate|Não|Tabela Cassandra atualizada|Contagem|Contagem|Tabela Cassandra atualizada|ResourceName, ChildResourceName, |
|CreateAccount|Sim|Conta criada|Contagem|Contagem|Conta criada|Sem dimensões|
|DataUsage|Não|Uso de dados|Bytes|Total|Uso total de dados relatados à granularidade de 5 minutos|CollectionName, DatabaseName, região|
|DeleteAccount|Sim|Conta excluída|Contagem|Contagem|Conta excluída|Sem dimensões|
|DocumentCount|Não|Contagem de documentos|Contagem|Total|Contagem total de documentos relatados à granularidade de 5 minutos|CollectionName, DatabaseName, região|
|DocumentQuota|Não|Cota de documento|Bytes|Total|Cota de armazenamento total relatada à granularidade de 5 minutos|CollectionName, DatabaseName, região|
|GremlinDatabaseCreate|Não|Banco de dados Gremlin criado|Contagem|Contagem|Banco de dados Gremlin criado|Source |
|GremlinDatabaseDelete|Não|Banco de dados Gremlin excluído|Contagem|Contagem|Banco de dados Gremlin excluído|Source |
|GremlinDatabaseThroughputUpdate|Não|Taxa de transferência do banco de dados Gremlin atualizada|Contagem|Contagem|Taxa de transferência do banco de dados Gremlin atualizada|Source |
|GremlinDatabaseUpdate|Não|Banco de dados Gremlin atualizado|Contagem|Contagem|Banco de dados Gremlin atualizado|Source |
|GremlinGraphCreate|Não|Grafo Gremlin criado|Contagem|Contagem|Grafo Gremlin criado|ResourceName, ChildResourceName, |
|GremlinGraphDelete|Não|Grafo Gremlin excluído|Contagem|Contagem|Grafo Gremlin excluído|ResourceName, ChildResourceName, |
|GremlinGraphThroughputUpdate|Não|Taxa de transferência do grafo Gremlin atualizada|Contagem|Contagem|Taxa de transferência do grafo Gremlin atualizada|ResourceName, ChildResourceName, |
|GremlinGraphUpdate|Não|Grafo Gremlin atualizado|Contagem|Contagem|Grafo Gremlin atualizado|ResourceName, ChildResourceName, |
|IndexUsage|Não|Uso do índice|Bytes|Total|Uso total do índice relatado à granularidade de 5 minutos|CollectionName, DatabaseName, região|
|MetadataRequests|Não|MetadataRequests|Contagem|Contagem|Contagem de solicitações de metadados. O Cosmos DB mantém uma coleção de metadados do sistema para cada conta, o que permite que você enumere coleções, bancos de dados, etc, e suas configurações, gratuitamente.|DatabaseName, CollectionName, região, StatusCode, |
|MongoCollectionCreate|Não|Coleção Mongo criada|Contagem|Contagem|Coleção Mongo criada|ResourceName, ChildResourceName, |
|MongoCollectionDelete|Não|Coleção Mongo excluída|Contagem|Contagem|Coleção Mongo excluída|ResourceName, ChildResourceName, |
|MongoCollectionThroughputUpdate|Não|Taxa de transferência de coleção Mongo atualizada|Contagem|Contagem|Taxa de transferência de coleção Mongo atualizada|ResourceName, ChildResourceName, |
|MongoCollectionUpdate|Não|Coleção Mongo atualizada|Contagem|Contagem|Coleção Mongo atualizada|ResourceName, ChildResourceName, |
|MongoDatabaseDelete|Não|Banco de dados Mongo excluído|Contagem|Contagem|Banco de dados Mongo excluído|Source |
|MongoDatabaseThroughputUpdate|Não|Taxa de transferência do banco de dados Mongo atualizada|Contagem|Contagem|Taxa de transferência do banco de dados Mongo atualizada|Source |
|MongoDBDatabaseCreate|Não|Banco de dados Mongo criado|Contagem|Contagem|Banco de dados Mongo criado|Source |
|MongoDBDatabaseUpdate|Não|Banco de dados Mongo atualizado|Contagem|Contagem|Banco de dados Mongo atualizado|Source |
|MongoRequestCharge|Sim|MongoRequestCharge|Contagem|Total|Unidades Solicitadas do Mongo Consumidas|DatabaseName, CollectionName, região, CommandName, ErrorCode, status|
|MongoRequests|Sim|Solicitações do Mongo|Contagem|Contagem|Número de Solicitações do Mongo Feitas|DatabaseName, CollectionName, região, CommandName, ErrorCode, status|
|MongoRequestsCount|Não|Taxa de Solicitação do Mongo|CountPerSecond|Média|Contagem de solicitações Mongo por segundo|DatabaseName, CollectionName, região, ErrorCode|
|MongoRequestsDelete|Não|Taxa de Solicitação de Exclusão do Mongo|CountPerSecond|Média|Solicitação de exclusão de Mongo por segundo|DatabaseName, CollectionName, região, ErrorCode|
|MongoRequestsInsert|Não|Taxa de Solicitação de Inserção do Mongo|CountPerSecond|Média|Contagem de inserção de Mongo por segundo|DatabaseName, CollectionName, região, ErrorCode|
|MongoRequestsQuery|Não|Taxa de Solicitação de Consulta do Mongo|CountPerSecond|Média|Solicitação de consulta Mongo por segundo|DatabaseName, CollectionName, região, ErrorCode|
|MongoRequestsUpdate|Não|Taxa de Solicitação de Atualização do Mongo|CountPerSecond|Média|Solicitação de atualização de Mongo por segundo|DatabaseName, CollectionName, região, ErrorCode|
|NormalizedRUConsumption|Não|Consumo de RU normalizado|Porcentagem|Máximo|Porcentagem máxima de consumo de RU por minuto|CollectionName, DatabaseName, região, PartitionKeyRangeId|
|ProvisionedThroughput|Não|Taxa de transferência provisionada|Contagem|Máximo|Taxa de transferência provisionada|DatabaseName, CollectionName|
|RegionFailover|Sim|Falha na região|Contagem|Contagem|Falha na região|Sem dimensões|
|RemoveRegion|Sim|Região removida|Contagem|Contagem|Região removida|Região|
|ReplicationLatency|Sim|Latência de Replicação P99|MilliSeconds|Média|Latência de replicação P99 nas regiões de origem e de destino para conta habilitada geograficamente|SourceRegion, TargetRegion|
|ServerSideLatency|Não|Latência do lado do servidor|MilliSeconds|Média|Latência do lado do servidor|DatabaseName, CollectionName, região, ConnectionMode, OperationType, PublicAPIType|
|ServiceAvailability|Não|Disponibilidade do serviço|Porcentagem|Média|Disponibilidade de solicitações de conta em uma hora, granularidade de dia ou mês|Sem dimensões|
|SqlContainerCreate|Não|Contêiner SQL criado|Contagem|Contagem|Contêiner SQL criado|ResourceName, ChildResourceName, |
|SqlContainerDelete|Não|Contêiner SQL excluído|Contagem|Contagem|Contêiner SQL excluído|ResourceName, ChildResourceName, |
|SqlContainerThroughputUpdate|Não|Produtividade do contêiner SQL atualizada|Contagem|Contagem|Produtividade do contêiner SQL atualizada|ResourceName, ChildResourceName, |
|SqlContainerUpdate|Não|Contêiner SQL atualizado|Contagem|Contagem|Contêiner SQL atualizado|ResourceName, ChildResourceName, |
|SqlDatabaseCreate|Não|Banco de dados SQL criado|Contagem|Contagem|Banco de dados SQL criado|Source |
|SqlDatabaseDelete|Não|Banco de dados SQL excluído|Contagem|Contagem|Banco de dados SQL excluído|Source |
|SqlDatabaseThroughputUpdate|Não|Produtividade do banco de dados SQL atualizada|Contagem|Contagem|Produtividade do banco de dados SQL atualizada|Source |
|SqlDatabaseUpdate|Não|Banco de dados SQL atualizado|Contagem|Contagem|Banco de dados SQL atualizado|Source |
|TableTableCreate|Não|Tabela do azuretable criada|Contagem|Contagem|Tabela do azuretable criada|Source |
|TableTableDelete|Não|Tabela do azuretable excluída|Contagem|Contagem|Tabela do azuretable excluída|Source |
|TableTableThroughputUpdate|Não|Taxa de transferência de tabela do azuretable atualizada|Contagem|Contagem|Taxa de transferência de tabela do azuretable atualizada|Source |
|TableTableUpdate|Não|Tabela do azuretable atualizada|Contagem|Contagem|Tabela do azuretable atualizada|Source |
|TotalRequests|Sim|Total de Solicitações|Contagem|Contagem|Número de solicitações feitas|DatabaseName, CollectionName, região, StatusCode, OperationType, status|
|TotalRequestUnits|Sim|Total de Unidades Solicitadas|Contagem|Total|Unidades Solicitadas Consumidas|DatabaseName, CollectionName, região, StatusCode, OperationType, status|
|UpdateAccountKeys|Sim|Chaves de conta atualizadas|Contagem|Contagem|Chaves de conta atualizadas|KeyType|
|UpdateAccountNetworkSettings|Sim|Configurações de rede da conta atualizadas|Contagem|Contagem|Configurações de rede da conta atualizadas|Sem dimensões|
|UpdateAccountReplicationSettings|Sim|Configurações de replicação de conta atualizadas|Contagem|Contagem|Configurações de replicação de conta atualizadas|Sem dimensões|
|UpdateDiagnosticsSettings|Não|Configurações de diagnóstico da conta atualizadas|Contagem|Contagem|Configurações de diagnóstico da conta atualizadas|DiagnosticSettingsName, ResourceGroupName|


## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Sim|Eventos de mensagens mortas|Contagem|Total|Total de eventos de mensagens mortas correspondentes a essa assinatura de evento|Tópico, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|
|DeliveryAttemptFailCount|Não|Eventos com falha de entrega|Contagem|Total|Total de eventos não entregues a essa assinatura de evento|Tópico, EventSubscriptionName, DomainEventSubscriptionName, erro, ErrorType|
|DeliverySuccessCount|Sim|Eventos entregues|Contagem|Total|Total de eventos entregues a essa assinatura de evento|Tópico, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|Não|Duração do processamento de destino|Milissegundos|Média|Duração do processamento de destino em milissegundos|Tópico, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Sim|Eventos removidos|Contagem|Total|Total de eventos removidos correspondentes a essa assinatura de evento|Tópico, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|MatchedEventCount|Sim|Eventos correspondentes|Contagem|Total|Total de eventos correspondentes a essa assinatura de evento|Tópico, EventSubscriptionName, DomainEventSubscriptionName|
|PublishFailCount|Sim|Publicar Eventos com Falha|Contagem|Total|Total de eventos com falha ao publicar neste tópico|Tópico, ErrorType, erro|
|PublishSuccessCount|Sim|Eventos publicados|Contagem|Total|Total de eventos publicados neste tópico|Tópico|
|PublishSuccessLatencyInMs|Sim|Publicar Latência de Êxito|Milissegundos|Total|Latência de êxito de publicação em milissegundos|Sem dimensões|


## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Sim|Eventos de mensagens mortas|Contagem|Total|Total de eventos de mensagens mortas correspondentes a essa assinatura de evento|DeadLetterReason|
|DeliveryAttemptFailCount|Não|Eventos com falha de entrega|Contagem|Total|Total de eventos não entregues a essa assinatura de evento|Error, ErrorType|
|DeliverySuccessCount|Sim|Eventos entregues|Contagem|Total|Total de eventos entregues a essa assinatura de evento|Sem dimensões|
|DestinationProcessingDurationInMs|Não|Duração do processamento de destino|Milissegundos|Média|Duração do processamento de destino em milissegundos|Sem dimensões|
|DroppedEventCount|Sim|Eventos removidos|Contagem|Total|Total de eventos removidos correspondentes a essa assinatura de evento|DropReason|
|MatchedEventCount|Sim|Eventos correspondentes|Contagem|Total|Total de eventos correspondentes a essa assinatura de evento|Sem dimensões|


## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|PublishFailCount|Sim|Publicar Eventos com Falha|Contagem|Total|Total de eventos com falha ao publicar neste tópico|ErrorType, Error|
|PublishSuccessCount|Sim|Eventos publicados|Contagem|Total|Total de eventos publicados neste tópico|Sem dimensões|
|PublishSuccessLatencyInMs|Sim|Publicar Latência de Êxito|Milissegundos|Total|Latência de êxito de publicação em milissegundos|Sem dimensões|
|UnmatchedEventCount|Sim|Eventos sem correspondência|Contagem|Total|Total de eventos sem correspondência com nenhuma das assinaturas de evento para este tópico|Sem dimensões|


## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Sim|Eventos de mensagens mortas|Contagem|Total|Total de eventos de mensagens mortas correspondentes a essa assinatura de evento|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|Não|Eventos com falha de entrega|Contagem|Total|Total de eventos não entregues a essa assinatura de evento|Erro, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Sim|Eventos entregues|Contagem|Total|Total de eventos entregues a essa assinatura de evento|EventSubscriptionName|
|DestinationProcessingDurationInMs|Não|Duração do processamento de destino|Milissegundos|Média|Duração do processamento de destino em milissegundos|EventSubscriptionName|
|DroppedEventCount|Sim|Eventos removidos|Contagem|Total|Total de eventos removidos correspondentes a essa assinatura de evento|DropReason, EventSubscriptionName|
|MatchedEventCount|Sim|Eventos correspondentes|Contagem|Total|Total de eventos correspondentes a essa assinatura de evento|EventSubscriptionName|
|PublishFailCount|Sim|Publicar Eventos com Falha|Contagem|Total|Total de eventos com falha ao publicar neste tópico|ErrorType, Error|
|PublishSuccessCount|Sim|Eventos publicados|Contagem|Total|Total de eventos publicados neste tópico|Sem dimensões|
|PublishSuccessLatencyInMs|Sim|Publicar Latência de Êxito|Milissegundos|Total|Latência de êxito de publicação em milissegundos|Sem dimensões|
|UnmatchedEventCount|Sim|Eventos sem correspondência|Contagem|Total|Total de eventos sem correspondência com nenhuma das assinaturas de evento para este tópico|Sem dimensões|


## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Sim|Eventos de mensagens mortas|Contagem|Total|Total de eventos de mensagens mortas correspondentes a essa assinatura de evento|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|Não|Eventos com falha de entrega|Contagem|Total|Total de eventos não entregues a essa assinatura de evento|Erro, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Sim|Eventos entregues|Contagem|Total|Total de eventos entregues a essa assinatura de evento|EventSubscriptionName|
|DestinationProcessingDurationInMs|Não|Duração do processamento de destino|Milissegundos|Média|Duração do processamento de destino em milissegundos|EventSubscriptionName|
|DroppedEventCount|Sim|Eventos removidos|Contagem|Total|Total de eventos removidos correspondentes a essa assinatura de evento|DropReason, EventSubscriptionName|
|MatchedEventCount|Sim|Eventos correspondentes|Contagem|Total|Total de eventos correspondentes a essa assinatura de evento|EventSubscriptionName|
|PublishFailCount|Sim|Publicar Eventos com Falha|Contagem|Total|Total de eventos com falha ao publicar neste tópico|ErrorType, Error|
|PublishSuccessCount|Sim|Eventos publicados|Contagem|Total|Total de eventos publicados neste tópico|Sem dimensões|
|PublishSuccessLatencyInMs|Sim|Publicar Latência de Êxito|Milissegundos|Total|Latência de êxito de publicação em milissegundos|Sem dimensões|
|UnmatchedEventCount|Sim|Eventos sem correspondência|Contagem|Total|Total de eventos sem correspondência com nenhuma das assinaturas de evento para este tópico|Sem dimensões|


## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|ActiveConnections|Não|ActiveConnections|Contagem|Média|Total de Conexões Ativas para o Microsoft.EventHub.|Sem dimensões|
|AvailableMemory|Não|Memória disponível|Porcentagem|Máximo|Memória disponível para o cluster do hub de eventos como uma porcentagem da memória total.|Função|
|CaptureBacklog|Não|Lista de Pendências de Captura.|Contagem|Total|Lista de Pendências de Captura para o Microsoft.EventHub.|Sem dimensões|
|CapturedBytes|Não|Bytes Capturados.|Bytes|Total|Bytes Capturados para o Microsoft.EventHub.|Sem dimensões|
|CapturedMessages|Não|Mensagens Capturadas.|Contagem|Total|Mensagens Capturadas para o Microsoft.EventHub.|Sem dimensões|
|ConnectionsClosed|Não|Conexões Fechadas.|Contagem|Média|Conexões Fechadas para o Microsoft.EventHub.|Sem dimensões|
|ConnectionsOpened|Não|Conexões Abertas.|Contagem|Média|Conexões Abertas para o Microsoft.EventHub.|Sem dimensões|
|CPU|Não|CPU|Porcentagem|Máximo|Utilização da CPU para o Cluster do Hub de Eventos como um percentual|Função|
|IncomingBytes|Sim|Bytes de Entrada.|Bytes|Total|Bytes de Entrada para o Microsoft.EventHub.|Sem dimensões|
|IncomingMessages|Sim|Mensagens de entrada|Contagem|Total|Mensagens de Entrada para o Microsoft.EventHub.|Sem dimensões|
|IncomingRequests|Sim|Solicitações de entrada|Contagem|Total|Solicitações de Entrada para o Microsoft.EventHub.|Sem dimensões|
|OutgoingBytes|Sim|Bytes de Saída.|Bytes|Total|Bytes de Saída para o Microsoft.EventHub.|Sem dimensões|
|OutgoingMessages|Sim|Mensagens de saída|Contagem|Total|Mensagens de Saída para o Microsoft.EventHub.|Sem dimensões|
|QuotaExceededErrors|Não|Cota de Erros Excedida.|Contagem|Total|Cota de Erros Excedida para o Microsoft.EventHub.|Sem dimensões|
|ServerErrors|Não|Erros do Servidor.|Contagem|Total|Erros do Servidor para o Microsoft.EventHub.|Sem dimensões|
|Tamanho|Não|Tamanho|Bytes|Média|Tamanho de um EventHub em Bytes.|Função|
|SuccessfulRequests|Não|Solicitações bem sucedidas|Contagem|Total|Solicitações Bem-sucedidas para Microsoft.EventHub.|Sem dimensões|
|ThrottledRequests|Não|Solicitações Limitadas.|Contagem|Total|Solicitações Limitadas para o Microsoft.EventHub.|Sem dimensões|
|UserErrors|Não|Erros de Usuário.|Contagem|Total|Erros de Usuário para o Microsoft.EventHub.|Sem dimensões|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|ActiveConnections|Não|ActiveConnections|Contagem|Média|Total de Conexões Ativas para o Microsoft.EventHub.|Sem dimensões|
|CaptureBacklog|Não|Lista de Pendências de Captura.|Contagem|Total|Lista de Pendências de Captura para o Microsoft.EventHub.|EntityName|
|CapturedBytes|Não|Bytes Capturados.|Bytes|Total|Bytes Capturados para o Microsoft.EventHub.|EntityName|
|CapturedMessages|Não|Mensagens Capturadas.|Contagem|Total|Mensagens Capturadas para o Microsoft.EventHub.|EntityName|
|ConnectionsClosed|Não|Conexões Fechadas.|Contagem|Média|Conexões Fechadas para o Microsoft.EventHub.|EntityName|
|ConnectionsOpened|Não|Conexões Abertas.|Contagem|Média|Conexões Abertas para o Microsoft.EventHub.|EntityName|
|EHABL|Sim|Arquivar mensagens da lista de pendências (Preterido)|Contagem|Total|Mensagens de arquivamento do hub de eventos no backlog para um namespace (preterido)|Sem dimensões|
|EHAMBS|Sim|Arquivar taxa de transferência de mensagem (Preterido)|Bytes|Total|Taxa de transferência de mensagem arquivada do hub de eventos em um namespace (preterido)|Sem dimensões|
|EHAMSGS|Sim|Arquivar mensagens (Preterido)|Contagem|Total|Mensagens arquivadas do hub de eventos em um namespace (preterido)|Sem dimensões|
|EHINBYTES|Sim|Bytes de entrada (Preterida)|Bytes|Total|Taxa de transferência de mensagem de entrada do hub de eventos para um namespace (preterido)|Sem dimensões|
|EHINMBS|Sim|Bytes de entrada (obsoleto) (Preterido)|Bytes|Total|Taxa de transferência de mensagem de entrada do Hub de Eventos para um namespace. Essa métrica foi preterida. Use a métrica de bytes de entrada em vez disso (preterido)|Sem dimensões|
|EHINMSGS|Sim|Mensagens de Entrada (Preterida)|Contagem|Total|Total de mensagens de entrada para um namespace (preterido)|Sem dimensões|
|EHOUTBYTES|Sim|Bytes de saída (Preterida)|Bytes|Total|Taxa de transferência de mensagem de saída do hub de eventos para um namespace (preterido)|Sem dimensões|
|EHOUTMBS|Sim|Bytes de saída (obsoleto) (Preterido)|Bytes|Total|Taxa de transferência de mensagem de saída do Hub de Eventos para um namespace. Essa métrica foi preterida. Use a métrica de bytes de saída em vez disso (preterido)|Sem dimensões|
|EHOUTMSGS|Sim|Mensagens de Saída (Preterida)|Contagem|Total|Total de mensagens de saída para um namespace (preterido)|Sem dimensões|
|FAILREQ|Sim|Solicitações com Falha (Preterido)|Contagem|Total|Total de solicitações com falha para um namespace (preterido)|Sem dimensões|
|IncomingBytes|Sim|Bytes de Entrada.|Bytes|Total|Bytes de Entrada para o Microsoft.EventHub.|EntityName|
|IncomingMessages|Sim|Mensagens de entrada|Contagem|Total|Mensagens de Entrada para o Microsoft.EventHub.|EntityName|
|IncomingRequests|Sim|Solicitações de entrada|Contagem|Total|Solicitações de Entrada para o Microsoft.EventHub.|EntityName|
|INMSGS|Sim|Mensagens de entrada (obsoleto) (Preterido)|Contagem|Total|Total de mensagens de entrada para um namespace. Essa métrica foi preterida. Use a métrica de mensagens de entrada em vez disso (preterido)|Sem dimensões|
|INREQS|Sim|Solicitações de Entrada (Preterido)|Contagem|Total|Total de solicitações de envio de entrada para um namespace (preterido)|Sem dimensões|
|INTERR|Sim|Erros Internos do Servidor (Preterido)|Contagem|Total|Total de erros internos do servidor para um namespace (preterido)|Sem dimensões|
|MISCERR|Sim|Outros Erros (Preterido)|Contagem|Total|Total de solicitações com falha para um namespace (preterido)|Sem dimensões|
|OutgoingBytes|Sim|Bytes de Saída.|Bytes|Total|Bytes de Saída para o Microsoft.EventHub.|EntityName|
|OutgoingMessages|Sim|Mensagens de saída|Contagem|Total|Mensagens de Saída para o Microsoft.EventHub.|EntityName|
|OUTMSGS|Sim|Mensagens de Saída (obsoleto) (Preterido)|Contagem|Total|Total de mensagens de saída para um namespace. Essa métrica foi preterida. Use a métrica de mensagens de saída em vez disso (preterido)|Sem dimensões|
|QuotaExceededErrors|Não|Cota de Erros Excedida.|Contagem|Total|Cota de Erros Excedida para o Microsoft.EventHub.|EntityName, |
|ServerErrors|Não|Erros do Servidor.|Contagem|Total|Erros do Servidor para o Microsoft.EventHub.|EntityName, |
|Tamanho|Não|Tamanho|Bytes|Média|Tamanho de um EventHub em Bytes.|EntityName|
|SuccessfulRequests|Não|Solicitações bem sucedidas|Contagem|Total|Solicitações Bem-sucedidas para Microsoft.EventHub.|EntityName, |
|SUCCREQ|Sim|Solicitações Bem-sucedidas (Preterido)|Contagem|Total|Total de solicitações bem-sucedidas para um namespace (preterido)|Sem dimensões|
|SVRBSY|Sim|Erros de Servidor Ocupado (Preterido)|Contagem|Total|Total de erros do servidor ocupado para um namespace (preterido)|Sem dimensões|
|ThrottledRequests|Não|Solicitações Limitadas.|Contagem|Total|Solicitações Limitadas para o Microsoft.EventHub.|EntityName, |
|UserErrors|Não|Erros de Usuário.|Contagem|Total|Erros de Usuário para o Microsoft.EventHub.|EntityName, |


## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|CategorizedGatewayRequests|Sim|Solicitações de gateway categorizadas|Contagem|Total|Número de solicitações de gateway por categorias (1xx/2xx/3xx/4xx/5xx)|HttpStatus|
|GatewayRequests|Sim|Solicitações de gateway|Contagem|Total|Número de solicitações de gateway|HttpStatus|
|NumActiveWorkers|Sim|Número de Trabalhos Ativos|Contagem|Máximo|Número de Trabalhos Ativos|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|MetricThreshold|Sim|Limite da Métrica|Contagem|Média|O limite do dimensionamento automático configurado quando o dimensionamento automático foi executado.|MetricTriggerRule|
|ObservedCapacity|Sim|Capacidade Observada|Contagem|Média|A capacidade relatada para o dimensionamento automático quando ele foi executado.|Sem dimensões|
|ObservedMetricValue|Sim|Valor de Métrica Observado|Contagem|Média|O valor calculado pelo dimensionamento automático quando executado|MetricTriggerSource|
|ScaleActionsInitiated|Sim|Ações de Escala Iniciadas|Contagem|Total|A direção da operação de escala.|ScaleDirection|


## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Sim|Disponibilidade|Porcentagem|Média|Percentual de testes de disponibilidade concluídos com êxito|availabilityResult/nome, availabilityResult/local|
|availabilityResults/count|Não|Testes de disponibilidade|Contagem|Contagem|Contagem de testes de disponibilidade|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|availabilityResults/duration|Sim|Duração do teste de disponibilidade|MilliSeconds|Média|Duração do teste de disponibilidade|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|browserTimings/networkDuration|Sim|Tempo de conexão de rede de carregamento de página|MilliSeconds|Média|Tempo entre a solicitação do usuário e a conexão de rede. Inclui a pesquisa DNS e a conexão de transporte.|Sem dimensões|
|browserTimings/processingDuration|Sim|Tempo de processamento do cliente|MilliSeconds|Média|Tempo entre o recebimento do último byte de um documento até que o DOM seja carregado. As solicitações assíncronas ainda podem estar sendo processadas.|Sem dimensões|
|browserTimings/receiveDuration|Sim|Tempo de resposta de recebimento|MilliSeconds|Média|Tempo entre o primeiro e o último byte, ou até a desconexão.|Sem dimensões|
|browserTimings/sendDuration|Sim|Tempo de solicitação de envio|MilliSeconds|Média|Tempo entre a conexão de rede e o recebimento do primeiro byte.|Sem dimensões|
|browserTimings/totalDuration|Sim|Tempo de carregamento de página do navegador|MilliSeconds|Média|Tempo de solicitação do usuário até que o DOM, as imagens, os scripts e as folhas de estilo sejam carregados.|Sem dimensões|
|dependencies/count|Não|Chamadas de dependência|Contagem|Contagem|Contagem de chamadas feitas pelo aplicativo a recursos externos.|dependência/tipo, dependência/performanceBucket, dependência/êxito, dependência/destino, dependência/resultCode, operação/sintética, nuvem/roleInstance, nuvem/roleName|
|dependencies/duration|Sim|Duração da dependência|MilliSeconds|Média|Duração das chamadas feitas pelo aplicativo a recursos externos.|dependência/tipo, dependência/performanceBucket, dependência/êxito, dependência/destino, dependência/resultCode, operação/sintética, nuvem/roleInstance, nuvem/roleName|
|dependencies/failed|Não|Falhas de chamada de dependência|Contagem|Contagem|Contagem de chamadas de dependência com falha feitas pelo aplicativo a recursos externos.|dependência/tipo, dependência/performanceBucket, dependência/destino, dependência/resultCode, operação/sintética, nuvem/roleInstance, nuvem/roleName|
|exceptions/browser|Não|Exceções de navegador|Contagem|Contagem|Contagem de exceções não identificadas lançadas no navegador.|nuvem/roleName|
|exceptions/count|Sim|Exceções|Contagem|Contagem|Contagem combinada de todas as exceções não capturadas.|cloud/roleName, cloud/roleInstance, client/type|
|exceptions/server|Não|Exceções do servidor|Contagem|Contagem|Contagem de exceções não capturadas geradas no aplicativo para servidores.|cloud/roleName, cloud/roleInstance|
|pageViews/count|Sim|Visualizações de página|Contagem|Contagem|Contagem de exibições de página.|operação/sintética, nuvem/roleName|
|pageViews/duration|Sim|Tempo de carregamento de exibição de página|MilliSeconds|Média|Tempo de carregamento de exibição de página|operação/sintética, nuvem/roleName|
|performanceCounters/exceptionsPerSecond|Sim|Taxa de exceção|CountPerSecond|Média|Contagem de exceções com e sem tratamento relatadas para o Windows, incluindo exceções do .NET e exceções não gerenciadas convertidas em exceções do .NET.|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Sim|Memória disponível|Bytes|Média|Memória física disponível imediatamente para alocação a um processo ou para uso do sistema.|cloud/roleInstance|
|performanceCounters/processCpuPercentage|Sim|CPU do processo|Porcentagem|Média|O percentual de tempo decorrido que todos os threads do processo usaram o processador para executar instruções. Isso pode variar entre 0 e 100. Essa métrica indica o desempenho do processo de w3wp isoladamente.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Sim|Taxa de E/S de processamento|BytesPerSecond|Média|Total de bytes por segundo lidos e gravados em arquivos, rede e dispositivos.|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Sim|Tempo do processador|Porcentagem|Média|O percentual de tempo que o processador gasta em threads não ociosos.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|Sim|Processar bytes particulares|Bytes|Média|Memória atribuída exclusivamente aos processos do aplicativo monitorado.|cloud/roleInstance|
|performanceCounters/requestExecutionTime|Sim|Tempo de execução de solicitação HTTP|MilliSeconds|Média|Tempo de execução da solicitação mais recente.|cloud/roleInstance|
|performanceCounters/requestsInQueue|Sim|Solicitações HTTP na fila do aplicativo|Contagem|Média|Comprimento da fila de solicitação de aplicativo.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|Sim|Taxa de solicitação HTTP|CountPerSecond|Média|Taxa de todas as solicitações para o aplicativo por segundo do ASP.NET.|cloud/roleInstance|
|requests/count|Não|Solicitações do servidor|Contagem|Contagem|Contagem de solicitações HTTP concluídas.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/duration|Sim|Tempo de resposta do servidor|MilliSeconds|Média|Tempo entre o recebimento de uma solicitação HTTP e a finalização do envio da resposta.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/failed|Não|Solicitações falhas|Contagem|Contagem|Contagem de solicitações HTTP marcadas como com falha. Na maioria dos casos, essas são solicitações com um código de resposta >= 400 e diferente de 401.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|solicitações/taxa|Não|Taxa de solicitação do servidor|CountPerSecond|Média|Taxa de solicitações de servidor por segundo|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|traces/count|Sim|Rastreamentos|Contagem|Contagem|Contagem de documentos de rastreamento|trace/severityLevel, operation/synthetic, cloud/roleName, cloud/roleInstance|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Sim|Disponibilidade geral do cofre|Porcentagem|Média|Disponibilidade de solicitações do cofre|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|SaturationShoebox|Não|Saturação do cofre geral|Porcentagem|Média|Capacidade do cofre usada|Activitytable, ActivityName, transactionType|
|ServiceApiHit|Sim|Total de ocorrências da API de Serviço|Contagem|Contagem|Número total de ocorrências da API de serviço|ActivityType, ActivityName|
|ServiceApiLatency|Sim|Latência geral da API de Serviço|Milissegundos|Média|Latência geral das solicitações da API de serviço|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|Sim|Total de resultados da API de Serviço|Contagem|Contagem|Número total de resultados da API de serviço|ActivityType, ActivityName, StatusCode, StatusCodeClass|


## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|BatchBlobCount|Sim|Contagem de BLOBs do lote|Contagem|Média|Número de fontes de dados em um lote agregado para ingestão.|Banco de dados|
|BatchDuration|Sim|Duração do lote|Segundos|Média|A duração da fase de agregação no fluxo de ingestão.|Banco de dados|
|BatchesProcessed|Sim|Lotes processados|Contagem|Média|Número de lotes agregados para ingestão. Motivo da conclusão do lote: se o lote atingiu o tempo de envio em lote, o tamanho dos dados ou o número de limites de arquivos definidos pela política de envio em lote|Banco de dados, SealReason|
|BatchSize|Sim|Tamanho do lote|Bytes|Média|Tamanho de dados esperado não compactado em um lote agregado para ingestão.|Banco de dados|
|CacheUtilization|Sim|Utilização do cache|Porcentagem|Média|Nível de utilização no escopo do cluster|Sem dimensões|
|ContinuousExportMaxLatenessMinutes|Sim|Atraso máximo de exportação contínua|Contagem|Máximo|A finalização (em minutos) relatada pelos trabalhos de exportação contínua no cluster|Sem dimensões|
|ContinuousExportNumOfRecordsExported|Sim|Exportação contínua – núm de registros exportados|Contagem|Total|Número de registros exportados, acionados para cada artefato de armazenamento gravado durante a operação de exportação|ContinuousExportName, banco de dados|
|ContinuousExportPendingCount|Sim|Contagem de Exportação Contínua Pendente|Contagem|Máximo|O número de trabalhos de exportação contínuos pendentes prontos para execução|Sem dimensões|
|ContinuousExportResult|Sim|Resultado da Exportação Contínua|Contagem|Contagem|Indica se a exportação contínua foi bem-sucedida ou falhou|ContinuousExportName, resultado, banco de dados|
|CPU|Sim|CPU|Porcentagem|Média|Nível de utilização da CPU|Sem dimensões|
|EventsProcessedForEventHubs|Sim|Eventos processados (para os Hubs IoT/de Eventos)|Contagem|Total|Número de eventos processados pelo cluster ao ingerir de evento/Hub IoT|EventStatus|
|ExportUtilization|Sim|Utilização da Exportação|Porcentagem|Máximo|Utilização da exportação|Sem dimensões|
|IngestionLatencyInSeconds|Sim|Latência de ingestão (em segundos)|Segundos|Média|Tempo de ingestão da origem (por exemplo, mensagem no Hub de Eventos) para o cluster em segundos|Sem dimensões|
|IngestionResult|Sim|Resultados da ingestão|Contagem|Contagem|Número de operações de ingestão|IngestionResultDetails|
|IngestionUtilization|Sim|Utilização da ingestão|Porcentagem|Média|Taxa de slots de ingestão usados no cluster|Sem dimensões|
|IngestionVolumeInMB|Sim|Volume de ingestão (em MB)|Contagem|Total|Volume total de dados ingeridos no cluster (em MB)|Sem dimensões|
|InstanceCount|Sim|Contagem de Instâncias|Contagem|Média|Contagem total de instâncias|Sem dimensões|
|KeepAlive|Sim|Keep alive|Contagem|Média|A verificação de integridade indica que o cluster responde às consultas|Sem dimensões|
|QueryDuration|Sim|Duração da consulta|Milissegundos|Média|Duração das consultas em segundos|QueryStatus|
|SteamingIngestRequestRate|Sim|Taxa de Solicitação de Ingestão de Streaming|Contagem|RateRequestsPerSecond|Taxa de solicitação de ingestão de streaming (solicitações por segundo)|Sem dimensões|
|StreamingIngestDataRate|Sim|Taxa de Dados de Ingestão de Streaming|Contagem|Média|Taxa de dados de ingestão de streaming (MB por segundo)|Sem dimensões|
|StreamingIngestDuration|Sim|Duração da Ingestão de Streaming|Milissegundos|Média|Duração da ingestão de streaming em milissegundos|Sem dimensões|
|StreamingIngestResults|Sim|Resultado da Ingestão de Streaming|Contagem|Média|Resultado de ingestão de streaming|Result|
|TotalNumberOfConcurrentQueries|Sim|Número total de consultas simultâneas|Contagem|Total|Número total de consultas simultâneas|Sem dimensões|
|TotalNumberOfExtents|Sim|Número total de extensões|Contagem|Total|Número total de extensões de dados|Sem dimensões|
|TotalNumberOfThrottledCommands|Sim|Número total de comandos regulados|Contagem|Total|Número total de comandos regulados|CommandType|
|TotalNumberOfThrottledQueries|Sim|Número total de consultas limitadas|Contagem|Total|Número total de consultas limitadas|Sem dimensões|


## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|ActionLatency|Sim|Latência da ação |Segundos|Média|Latência das ações de fluxo de trabalho concluídas.|Sem dimensões|
|ActionsCompleted|Sim|Ações concluídas |Contagem|Total|Número de ações de fluxo de trabalho concluídas.|Sem dimensões|
|ActionsFailed|Sim|Ações com falha |Contagem|Total|Número de ações de fluxo de trabalho com falha.|Sem dimensões|
|ActionsSkipped|Sim|Ações ignoradas |Contagem|Total|Número de ações de fluxo de trabalho ignoradas.|Sem dimensões|
|ActionsStarted|Sim|Ações iniciadas |Contagem|Total|Número de ações de fluxo de trabalho iniciadas.|Sem dimensões|
|ActionsSucceeded|Sim|Ações bem sucedidas |Contagem|Total|Número de ações de fluxo de trabalho bem sucedidas.|Sem dimensões|
|ActionSuccessLatency|Sim|Latência das ações bem sucedidas |Segundos|Média|Latência das ações de fluxo de trabalho bem sucedidas.|Sem dimensões|
|ActionThrottledEvents|Sim|Eventos de restrição de ações|Contagem|Total|Número de eventos de restrição de ações de fluxo de trabalho.|Sem dimensões|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Sim|Uso de Memória do Conector para o Ambiente de Serviço de Integração|Porcentagem|Média|Uso de memória do conector para o ambiente do serviço de integração.|Sem dimensões|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Sim|Uso do Processador de Conector para o Ambiente de Serviço de Integração|Porcentagem|Média|Uso do processador do conector para o ambiente do serviço de integração.|Sem dimensões|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Sim|Uso de Memória do Fluxo de Trabalho para o Ambiente de Serviço de Integração|Porcentagem|Média|Uso de memória do fluxo de trabalho para o ambiente do serviço de integração.|Sem dimensões|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Sim|Uso do Processador de Fluxo de Trabalho para o Ambiente de Serviço de Integração|Porcentagem|Média|Uso do processador do fluxo de trabalho para o ambiente do serviço de integração.|Sem dimensões|
|RunFailurePercentage|Sim|Porcentagem de falha de execução|Porcentagem|Total|Porcentagem de execuções do fluxo de trabalho com falha.|Sem dimensões|
|RunLatency|Sim|Latência da execução|Segundos|Média|Latência das execuções de fluxo de trabalho concluídas.|Sem dimensões|
|RunsCancelled|Sim|Execuções canceladas|Contagem|Total|Número de execuções de fluxo de trabalho canceladas.|Sem dimensões|
|RunsCompleted|Sim|Execuções concluídas|Contagem|Total|Número de execuções de fluxo de trabalho concluídas.|Sem dimensões|
|RunsFailed|Sim|Execuções com falha|Contagem|Total|Número de execuções de fluxo de trabalho com falha.|Sem dimensões|
|RunsStarted|Sim|Execuções iniciadas|Contagem|Total|Número de execuções fluxo de trabalho iniciadas.|Sem dimensões|
|RunsSucceeded|Sim|Execuções bem sucedidas|Contagem|Total|Número de execuções de fluxo de trabalho bem sucedidas.|Sem dimensões|
|RunStartThrottledEvents|Sim|Executar Eventos Limitados por Início|Contagem|Total|Número de eventos restritos de início de fluxo de trabalho.|Sem dimensões|
|RunSuccessLatency|Sim|Latência de execução bem sucedida|Segundos|Média|Latência de execuções de fluxo de trabalho bem sucedidas.|Sem dimensões|
|RunThrottledEvents|Sim|Eventos de restrição de execução|Contagem|Total|Número de eventos de restrição de ações ou gatilhos de fluxo de trabalho.|Sem dimensões|
|TriggerFireLatency|Sim|Latência do gatilho acionado |Segundos|Média|Latência dos gatilhos de fluxo de trabalho acionados.|Sem dimensões|
|TriggerLatency|Sim|Latência do gatilho |Segundos|Média|Latência dos gatilhos de fluxo de trabalho concluídos.|Sem dimensões|
|TriggersCompleted|Sim|Gatilhos concluídos |Contagem|Total|Número de gatilhos de fluxo de trabalho concluídos.|Sem dimensões|
|TriggersFailed|Sim|Gatilhos com falha |Contagem|Total|Número de gatilhos de fluxo de trabalho com falha.|Sem dimensões|
|TriggersFired|Sim|Gatilhos acionados |Contagem|Total|Número de gatilhos de fluxo de trabalho acionados.|Sem dimensões|
|TriggersSkipped|Sim|Gatilhos ignorados|Contagem|Total|Número de gatilhos de fluxo de trabalho ignorados.|Sem dimensões|
|TriggersStarted|Sim|Gatilhos iniciados |Contagem|Total|Número de gatilhos de fluxo de trabalho iniciadas.|Sem dimensões|
|TriggersSucceeded|Sim|Gatilhos bem sucedidos |Contagem|Total|Número de gatilhos de fluxo de trabalho bem sucedidos.|Sem dimensões|
|TriggerSuccessLatency|Sim|Latência do gatilho bem sucedido |Segundos|Média|Latência dos gatilhos de fluxo de trabalho bem sucedidos.|Sem dimensões|
|TriggerThrottledEvents|Sim|Eventos de restrição do gatilho|Contagem|Total|Número de eventos de restrição do gatilho de fluxo de trabalho.|Sem dimensões|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|ActionLatency|Sim|Latência da ação |Segundos|Média|Latência das ações de fluxo de trabalho concluídas.|Sem dimensões|
|ActionsCompleted|Sim|Ações concluídas |Contagem|Total|Número de ações de fluxo de trabalho concluídas.|Sem dimensões|
|ActionsFailed|Sim|Ações com falha |Contagem|Total|Número de ações de fluxo de trabalho com falha.|Sem dimensões|
|ActionsSkipped|Sim|Ações ignoradas |Contagem|Total|Número de ações de fluxo de trabalho ignoradas.|Sem dimensões|
|ActionsStarted|Sim|Ações iniciadas |Contagem|Total|Número de ações de fluxo de trabalho iniciadas.|Sem dimensões|
|ActionsSucceeded|Sim|Ações bem sucedidas |Contagem|Total|Número de ações de fluxo de trabalho bem sucedidas.|Sem dimensões|
|ActionSuccessLatency|Sim|Latência das ações bem sucedidas |Segundos|Média|Latência das ações de fluxo de trabalho bem sucedidas.|Sem dimensões|
|ActionThrottledEvents|Sim|Eventos de restrição de ações|Contagem|Total|Número de eventos de restrição de ações de fluxo de trabalho.|Sem dimensões|
|BillableActionExecutions|Sim|Execuções de ação faturáveis|Contagem|Total|Número de execuções de ação de fluxo de trabalho sendo faturadas.|Sem dimensões|
|BillableTriggerExecutions|Sim|Execuções de gatilho faturáveis|Contagem|Total|Número de execuções de gatilho do fluxo de trabalho sendo cobradas.|Sem dimensões|
|BillingUsageNativeOperation|Sim|Uso de Cobrança para Execuções de Operação Nativa|Contagem|Total|Número de execuções de operação nativa sendo cobradas.|Sem dimensões|
|BillingUsageStandardConnector|Sim|Uso de Cobrança para Execuções de Conector Padrão|Contagem|Total|Número de execuções do conector padrão sendo cobradas.|Sem dimensões|
|BillingUsageStorageConsumption|Sim|Uso de Cobrança para Execuções de Consumo de Armazenamento|Contagem|Total|Número de execuções de consumo de armazenamento sendo cobradas.|Sem dimensões|
|RunFailurePercentage|Sim|Porcentagem de falha de execução|Porcentagem|Total|Porcentagem de execuções do fluxo de trabalho com falha.|Sem dimensões|
|RunLatency|Sim|Latência da execução|Segundos|Média|Latência das execuções de fluxo de trabalho concluídas.|Sem dimensões|
|RunsCancelled|Sim|Execuções canceladas|Contagem|Total|Número de execuções de fluxo de trabalho canceladas.|Sem dimensões|
|RunsCompleted|Sim|Execuções concluídas|Contagem|Total|Número de execuções de fluxo de trabalho concluídas.|Sem dimensões|
|RunsFailed|Sim|Execuções com falha|Contagem|Total|Número de execuções de fluxo de trabalho com falha.|Sem dimensões|
|RunsStarted|Sim|Execuções iniciadas|Contagem|Total|Número de execuções fluxo de trabalho iniciadas.|Sem dimensões|
|RunsSucceeded|Sim|Execuções bem sucedidas|Contagem|Total|Número de execuções de fluxo de trabalho bem sucedidas.|Sem dimensões|
|RunStartThrottledEvents|Sim|Executar Eventos Limitados por Início|Contagem|Total|Número de eventos restritos de início de fluxo de trabalho.|Sem dimensões|
|RunSuccessLatency|Sim|Latência de execução bem sucedida|Segundos|Média|Latência de execuções de fluxo de trabalho bem sucedidas.|Sem dimensões|
|RunThrottledEvents|Sim|Eventos de restrição de execução|Contagem|Total|Número de eventos de restrição de ações ou gatilhos de fluxo de trabalho.|Sem dimensões|
|TotalBillableExecutions|Sim|Total de execuções faturáveis|Contagem|Total|Número de execuções de fluxo de trabalho sendo faturadas.|Sem dimensões|
|TriggerFireLatency|Sim|Latência do gatilho acionado |Segundos|Média|Latência dos gatilhos de fluxo de trabalho acionados.|Sem dimensões|
|TriggerLatency|Sim|Latência do gatilho |Segundos|Média|Latência dos gatilhos de fluxo de trabalho concluídos.|Sem dimensões|
|TriggersCompleted|Sim|Gatilhos concluídos |Contagem|Total|Número de gatilhos de fluxo de trabalho concluídos.|Sem dimensões|
|TriggersFailed|Sim|Gatilhos com falha |Contagem|Total|Número de gatilhos de fluxo de trabalho com falha.|Sem dimensões|
|TriggersFired|Sim|Gatilhos acionados |Contagem|Total|Número de gatilhos de fluxo de trabalho acionados.|Sem dimensões|
|TriggersSkipped|Sim|Gatilhos ignorados|Contagem|Total|Número de gatilhos de fluxo de trabalho ignorados.|Sem dimensões|
|TriggersStarted|Sim|Gatilhos iniciados |Contagem|Total|Número de gatilhos de fluxo de trabalho iniciadas.|Sem dimensões|
|TriggersSucceeded|Sim|Gatilhos bem sucedidos |Contagem|Total|Número de gatilhos de fluxo de trabalho bem sucedidos.|Sem dimensões|
|TriggerSuccessLatency|Sim|Latência do gatilho bem sucedido |Segundos|Média|Latência dos gatilhos de fluxo de trabalho bem sucedidos.|Sem dimensões|
|TriggerThrottledEvents|Sim|Eventos de restrição do gatilho|Contagem|Total|Número de eventos de restrição do gatilho de fluxo de trabalho.|Sem dimensões|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Núcleos Ativos|Sim|Núcleos Ativos|Contagem|Média|Número de núcleos ativos|Cenário, ClusterName|
|Nós Ativos|Sim|Nós Ativos|Contagem|Média|Número de nós active. Esses são os nós que estão executando ativamente um trabalho.|Cenário, ClusterName|
|Cancelar execuções solicitadas|Sim|Cancelar execuções solicitadas|Contagem|Total|Número de execuções em que o cancelamento foi solicitado para este espaço de trabalho. A contagem é atualizada quando a solicitação de cancelamento é recebida para uma execução.|Cenário, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Execuções canceladas|Sim|Execuções canceladas|Contagem|Total|Número de execuções canceladas para este espaço de trabalho. A contagem é atualizada quando uma execução é cancelada com êxito.|Cenário, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Execuções Concluídas|Sim|Execuções Concluídas|Contagem|Total|Número de execuções concluídas com êxito para este espaço de trabalho. A contagem é atualizada quando uma execução é concluída e a saída é coletada.|Cenário, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|CpuUtilization|Sim|CpuUtilization|Contagem|Média|Percentual de utilização de memória em um nó de CPU. A utilização é relatada em intervalos de um minuto.|Cenário, runId, NodeId, ClusterName|
|Erros|Sim|Errors|Contagem|Total|Número de erros de execução neste espaço de trabalho. A contagem é atualizada sempre que a execução encontra um erro.|Cenário|
|Execuções com falha|Sim|Execuções com falha|Contagem|Total|Número de execuções com falha para este espaço de trabalho. A contagem é atualizada quando uma execução falha.|Cenário, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Finalizando execuções|Sim|Finalizando execuções|Contagem|Total|Número de execuções inseridas no estado final para este espaço de trabalho. A contagem é atualizada quando uma execução é concluída, mas a coleta de saída ainda está em andamento.|Cenário, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|GpuUtilization|Sim|GpuUtilization|Contagem|Média|Percentual de utilização de memória em um nó de GPU. A utilização é relatada em intervalos de um minuto.|Cenário, runId, NodeId, DeviceID, ClusterName|
|Núcleos Ociosos|Sim|Núcleos Ociosos|Contagem|Média|Número de núcleos ociosos|Cenário, ClusterName|
|Nós Ociosos|Sim|Nós Ociosos|Contagem|Média|Número de nós ociosos. Os nós ociosos são os nós que não estão executando nenhum trabalho, mas podem aceitar novos trabalhos, se disponíveis.|Cenário, ClusterName|
|Núcleos em Saída|Sim|Núcleos em Saída|Contagem|Média|Número de núcleos de saída|Cenário, ClusterName|
|Nós em Saída|Sim|Nós em Saída|Contagem|Média|Número de nós de saída. A saída de nós são os nós que acabaram de processar um trabalho e vão para o estado ocioso.|Cenário, ClusterName|
|Implantação de Modelo com Falha|Sim|Implantação de Modelo com Falha|Contagem|Total|Número de implantações de modelo que falharam neste espaço de trabalho|Cenário, StatusCode|
|Implantação de Modelo Iniciada|Sim|Implantação de Modelo Iniciada|Contagem|Total|Número de implantações de modelo iniciadas neste espaço de trabalho|Cenário|
|Implantação de Modelo Bem-sucedida|Sim|Implantação de Modelo Bem-sucedida|Contagem|Total|Número de implantações de modelo que foram bem-sucedidas neste espaço de trabalho|Cenário|
|Registro de Modelo com Falha|Sim|Registro de Modelo com Falha|Contagem|Total|Número de registros de modelo que falharam neste espaço de trabalho|Cenário, StatusCode|
|Registro de Modelo Bem-sucedido|Sim|Registro de Modelo Bem-sucedido|Contagem|Total|Número de registros de modelo que foram bem-sucedidos neste espaço de trabalho|Cenário|
|Não respondendo execuções|Sim|Não respondendo execuções|Contagem|Total|Número de execuções não respondendo a este espaço de trabalho. A contagem é atualizada quando uma execução entra em estado de não respondendo.|Cenário, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Execuções não iniciadas|Sim|Execuções não iniciadas|Contagem|Total|Número de execuções no estado não iniciado para este espaço de trabalho. A contagem é atualizada quando uma solicitação é recebida para criar uma execução, mas as informações de execução ainda não foram preenchidas. |Cenário, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Núcleos com Preempção|Sim|Núcleos com Preempção|Contagem|Média|Número de núcleos admitidos|Cenário, ClusterName|
|Nós com Preempção|Sim|Nós com Preempção|Contagem|Média|Número de nós admitidos. Esses nós são os nós de baixa prioridade que são desfeitos do pool de nós disponível.|Cenário, ClusterName|
|Preparando execuções|Sim|Preparando execuções|Contagem|Total|Número de execuções que estão se preparando para este espaço de trabalho. A contagem é atualizada quando uma execução entra em estado de preparação enquanto o ambiente de execução está sendo preparado.|Cenário, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Execuções de provisionamento|Sim|Execuções de provisionamento|Contagem|Total|Número de execuções que estão sendo provisionadas para este espaço de trabalho. A contagem é atualizada quando uma execução está aguardando a criação ou o provisionamento de destino de computação.|Cenário, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Execuções em fila|Sim|Execuções em fila|Contagem|Total|Número de execuções enfileiradas para este espaço de trabalho. A contagem é atualizada quando uma execução é enfileirada no destino de computação. Pode ocorrer ao aguardar que os nós de computação necessários estejam prontos.|Cenário, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Percentual de Utilização de Cota|Sim|Percentual de Utilização de Cota|Contagem|Média|Porcentagem de cota utilizada|Cenário, ClusterName, VmFamilyName, VmPriority|
|Execuções Iniciadas|Sim|Execuções Iniciadas|Contagem|Total|Número de execuções em execução para este espaço de trabalho. A contagem é atualizada quando a execução começa a ser executada nos recursos necessários.|Cenário, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Iniciando execuções|Sim|Iniciando execuções|Contagem|Total|Número de execuções iniciadas para este espaço de trabalho. A contagem é atualizada após a solicitação para criar informações de execução e execução, como a ID de execução, foi populada|Cenário, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Total de Núcleos|Sim|Total de Núcleos|Contagem|Média|Número de núcleos totais|Cenário, ClusterName|
|Total de Nós|Sim|Total de Nós|Contagem|Média|Número total de nós. Esse total inclui alguns nós ativos, nós ociosos, nós inutilizáveis, nós Premepted, deixando nós|Cenário, ClusterName|
|Núcleos Inutilizáveis|Sim|Núcleos Inutilizáveis|Contagem|Média|Número de núcleos inutilizáveis|Cenário, ClusterName|
|Nós Inutilizáveis|Sim|Nós Inutilizáveis|Contagem|Média|Número de nós inutilizáveis. Os nós inutilizáveis não são funcionais devido a algum problema não resolvido. O Azure reciclará esses nós.|Cenário, ClusterName|
|Avisos|Sim|Avisos|Contagem|Total|Número de avisos de execução neste espaço de trabalho. A contagem é atualizada sempre que uma execução encontra um aviso.|Cenário|


## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Sim|Disponibilidade|Porcentagem|Média|Disponibilidade das APIs|ApiCategory, ApiName|
|Uso|Não|Uso|Contagem|Contagem|Contagem de chamadas à API|ApiCategory, ApiName, ResultType, ResponseCode|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|AssetCount|Sim|Contagem de ativos|Contagem|Média|Quantos ativos já foram criados na conta de serviço de mídia atual|Sem dimensões|
|AssetQuota|Sim|Cota de ativos|Contagem|Média|Quantos ativos são permitidos para a conta de serviço de mídia atual|Sem dimensões|
|AssetQuotaUsedPercentage|Sim|Percentual de cota de ativos usado|Porcentagem|Média|Porcentagem de ativos usados na conta de serviço de mídia atual|Sem dimensões|
|ContentKeyPolicyCount|Sim|Contagem de Política de Chave de Conteúdo|Contagem|Média|Quantas políticas de chave de conteúdo já foram criadas na conta de serviço de mídia atual|Sem dimensões|
|ContentKeyPolicyQuota|Sim|Cota de Política de Chave de Conteúdo|Contagem|Média|Quantas políticas de chave de conteúdo são permitidas para a conta de serviço de mídia atual|Sem dimensões|
|ContentKeyPolicyQuotaUsedPercentage|Sim|Percentual de Cota Usada da Política de Chave de Conteúdo|Porcentagem|Média|Porcentagem usada da política de chave de conteúdo na conta de serviço de mídia atual|Sem dimensões|
|StreamingPolicyCount|Sim|Contagem de Política de Streaming|Contagem|Média|Quantas políticas de streaming já foram criadas na conta de serviço de mídia atual|Sem dimensões|
|StreamingPolicyQuota|Sim|Cota de Política de Streaming|Contagem|Média|Quantas políticas de streaming são permitidas para a conta de serviço de mídia atual|Sem dimensões|
|StreamingPolicyQuotaUsedPercentage|Sim|Percentual de cota usada da Política de Streaming|Porcentagem|Média|Percentual usado pela política de streaming na conta de serviço de mídia atual|Sem dimensões|


## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft.Media/mediaservices/streamingEndpoints

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Saída|Sim|Saída|Bytes|Total|A quantidade de dados de egresso, em bytes.|OutputFormat|
|Requests|Sim|Requests|Contagem|Total|Solicitações para um ponto de extremidade de streaming.|OutputFormat, HttpStatusCode, ErrorCode|
|SuccessE2ELatency|Sim|Latência de ponta a ponta bem-sucedida|Milissegundos|Média|A latência média de solicitações bem-sucedidas em milissegundos.|OutputFormat|


## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Sim|Tamanho alocado do pool|Bytes|Média|Tamanho provisionado deste pool|Sem dimensões|
|VolumePoolAllocatedUsed|Sim|Pool alocado para o tamanho do volume|Bytes|Média|Tamanho usado alocado do pool|Sem dimensões|
|VolumePoolTotalLogicalSize|Sim|Tamanho consumido do pool|Bytes|Média|Soma do tamanho lógico de todos os volumes que pertencem ao pool|Sem dimensões|
|VolumePoolTotalSnapshotSize|Sim|Tamanho total do instantâneo do pool|Bytes|Média|Soma do tamanho do instantâneo de todos os volumes neste pool|Sem dimensões|


## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/volumes

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|AverageReadLatency|Sim|Latência média de leitura|MilliSeconds|Média|Latência média de leitura em milissegundos por operação|Sem dimensões|
|AverageWriteLatency|Sim|Latência média de gravação|MilliSeconds|Média|Latência média de gravação em milissegundos por operação|Sem dimensões|
|CbsVolumeBackupActive|Sim|Estado ativo de backup de volume|Contagem|Média|O backup está suspenso no momento para o volume.|Sem dimensões|
|CbsVolumeLogicalBackupBytes|Sim|Backup de bytes lógicos com backup|Bytes|Média|Toatl backup de bytes não compactados/não criptografados para este volume.|Sem dimensões|
|CbsVolumeOperationComplete|Sim|Estado da operação|Contagem|Média|A última operação de backup/restauração foi bem-sucedida.|Sem dimensões|
|CbsVolumeOperationTransferredBytes|Sim|Bytes transferidos para a operação|Bytes|Média|Total de bytes transferidos para a última operação de backup/restauração.|Sem dimensões|
|CbsVolumeProtected|Sim|Estado protegido por volume|Contagem|Média|O volume é protegido pelo serviço de backup na nuvem.|Sem dimensões|
|ReadIops|Sim|IOPS de leitura|CountPerSecond|Média|Operações de entrada/saída de leitura por segundo|Sem dimensões|
|VolumeAllocatedSize|Sim|Tamanho alocado do volume|Bytes|Média|O tamanho provisionado de um volume|Sem dimensões|
|VolumeLogicalSize|Sim|Tamanho consumido por volume|Bytes|Média|Tamanho lógico do volume (bytes usados)|Sem dimensões|
|VolumeSnapshotSize|Sim|Tamanho do instantâneo de volume|Bytes|Média|Tamanho de todos os instantâneos no volume|Sem dimensões|
|WriteIops|Sim|IOPS de gravação|CountPerSecond|Média|Operações de entrada/saída de gravação por segundo|Sem dimensões|
|XregionReplicationHealthy|Sim|O status de replicação do volume é íntegro|Contagem|Média|Condição da relação, 1 ou 0.|Sem dimensões|
|XregionReplicationLagTime|Sim|Tempo de retardo da replicação do volume|Segundos|Média|A quantidade de tempo em segundos pela qual os dados no espelho atrasam a origem.|Sem dimensões|
|XregionReplicationLastTransferDuration|Sim|Duração da última transferência da replicação de volume|Segundos|Média|A quantidade de tempo em segundos que levou para a última transferência ser concluída.|Sem dimensões|
|XregionReplicationLastTransferSize|Sim|Tamanho da última transferência da replicação de volume|Bytes|Média|O número total de bytes transferidos como parte da última transferência.|Sem dimensões|
|XregionReplicationRelationshipProgress|Sim|Progresso da replicação do volume|Bytes|Média|Quantidade total de dados transferidos para a operação de transferência atual.|Sem dimensões|
|XregionReplicationRelationshipTransferring|Sim|A replicação de volume está sendo transtransferência|Contagem|Média|Se o status da replicação do volume é ' Transferindo '.|Sem dimensões|
|XregionReplicationTotalTransferBytes|Sim|Transferência total de replicação de volume|Bytes|Média|Bytes cumulativos transferidos para a relação.|Sem dimensões|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|ApplicationGatewayTotalTime|Não|Tempo Total do Gateway de Aplicativo|MilliSeconds|Média|Tempo médio que leva para que uma solicitação seja processada e sua resposta seja enviada. Isso é calculado como a média do intervalo desde o momento em que o gateway de aplicativo recebe o primeiro byte de uma solicitação HTTP até o momento em que a operação de envio de resposta é concluída. É importante observar que isso geralmente inclui o tempo de processamento do gateway de aplicativo, o tempo que os pacotes de solicitação e resposta estão viajando pela rede e o tempo que o servidor back-end levou para responder.|Ouvinte|
|AvgRequestCountPerHealthyHost|Não|Solicitações por minuto por Host Íntegro|Contagem|Média|Contagem média de solicitações por minuto por host de back-end íntegro em um pool|BackendSettingsPool|
|BackendConnectTime|Não|Tempo de Conexão de Back-end|MilliSeconds|Média|Tempo gasto estabelecendo uma conexão com um servidor de back-end|Listener, BackendServer, Httpsettings, BackendHttpSetting|
|BackendFirstByteResponseTime|Não|Tempo de Resposta do Primeiro Byte de Back-end|MilliSeconds|Média|Intervalo de tempo entre o início do estabelecimento de uma conexão com o servidor back-end e o recebimento do primeiro byte do cabeçalho de resposta, aproximar tempo de processamento do servidor back-end|Listener, BackendServer, Httpsettings, BackendHttpSetting|
|BackendLastByteResponseTime|Não|Tempo de Resposta do Último Byte de Back-end|MilliSeconds|Média|Intervalo de tempo entre o início do estabelecimento de uma conexão com o servidor de back-end e o recebimento do último byte do corpo da resposta|Listener, BackendServer, Httpsettings, BackendHttpSetting|
|BackendResponseStatus|Sim|Status da Resposta de Back-end|Contagem|Total|O número de códigos de resposta HTTP gerados pelos membros de back-end. Isso não inclui nenhum código de resposta gerado pelo gateway de aplicativo.|BackendServer, Httpsettings, BackendHttpSetting, HttpStatusGroup|
|BlockedCount|Sim|Distribuição de Regra de Solicitações Bloqueadas do Firewall do Aplicativo Web|Contagem|Total|Distribuição de regra de solicitações bloqueadas do firewall do aplicativo Web|Ruler, RuleId|
|BlockedReqCount|Sim|Contagem de Solicitações Bloqueadas do Firewall do Aplicativo Web|Contagem|Total|Contagem de solicitações bloqueadas do firewall do aplicativo Web|Sem dimensões|
|BytesReceived|Sim|Bytes Recebidos|Bytes|Total|O número total de bytes recebidos pelo gateway de aplicativo dos clientes|Ouvinte|
|BytesSent|Sim|Bytes Enviados|Bytes|Total|O número total de bytes enviados pelo gateway de aplicativo para os clientes|Ouvinte|
|CapacityUnits|Não|Unidades de Capacidade Atuais|Contagem|Média|Unidades de capacidade consumidas|Sem dimensões|
|ClientRtt|Não|RTT do Cliente|MilliSeconds|Média|Tempo médio de ida e volta entre clientes e o gateway de aplicativo. Essa métrica indica quanto tempo leva para estabelecer conexões e retornar confirmações|Ouvinte|
|ComputeUnits|Não|Unidades de Computação Atuais|Contagem|Média|Unidades de computação consumidas|Sem dimensões|
|CpuUtilization|Não|Utilização da CPU|Porcentagem|Média|Utilização atual da CPU do gateway de aplicativo|Sem dimensões|
|CurrentConnections|Sim|Conexões atuais|Contagem|Total|Contagem de conexões atuais estabelecidas com o Gateway de Aplicativo|Sem dimensões|
|EstimatedBilledCapacityUnits|Não|Unidades de capacidade cobradas estimadas|Contagem|Média|Unidades de capacidade estimadas que serão cobradas|Sem dimensões|
|FailedRequests|Sim|Solicitações com falha|Contagem|Total|Contagem de solicitações com falha atendidas pelo Gateway de Aplicativo|BackendSettingsPool|
|FixedBillableCapacityUnits|Não|Unidades de capacidade cobráveis fixas|Contagem|Média|Unidades de capacidade mínimas que serão cobradas|Sem dimensões|
|HealthyHostCount|Sim|Contagem de hosts íntegros|Contagem|Média|Número de hosts de back-end íntegros|BackendSettingsPool|
|MatchedCount|Sim|Distribuição de Regra Total do Firewall do Aplicativo Web|Contagem|Total|Distribuição da regra total do firewall do aplicativo Web para o tráfego de entrada|Ruler, RuleId|
|NewConnectionsPerSecond|Não|Novas conexões por segundo|CountPerSecond|Média|Novas conexões por segundo estabelecidas com o gateway de aplicativo|Sem dimensões|
|ResponseStatus|Sim|Status da Resposta|Contagem|Total|Status de resposta HTTP retornado pelo Gateway de Aplicativo|HttpStatusGroup|
|Taxa de transferência|Não|Produtividade|BytesPerSecond|Média|Número de bytes por segundo atendidos pelo Gateway de Aplicativo|Sem dimensões|
|TlsProtocol|Sim|Protocolo TLS do Cliente|Contagem|Total|O número de solicitações TLS e não TLS iniciadas pelo cliente que estabeleceram a conexão com o gateway de aplicativo. Para exibir a distribuição de protocolo TLS, filtre pelo protocolo TLS de dimensão.|Ouvinte, TlsProtocol|
|TotalRequests|Sim|Total de Solicitações|Contagem|Total|Contagem de solicitações bem sucedidas atendidas pelo Gateway de Aplicativo|BackendSettingsPool|
|UnhealthyHostCount|Sim|Contagem de hosts não íntegros|Contagem|Média|Número de hosts de back-end não íntegros|BackendSettingsPool|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|ApplicationRuleHit|Sim|Contagem de ocorrências de regras de aplicativo|Contagem|Total|Número de vezes que as regras de aplicativo foram atingidas|Status, motivo, protocolo|
|DataProcessed|Sim|Dados processados|Bytes|Total|Quantidade total de dados processados por esse firewall|Sem dimensões|
|FirewallHealth|Sim|Estado de integridade do firewall|Porcentagem|Média|Indica a integridade geral deste firewall|Status, motivo|
|NetworkRuleHit|Sim|Contagem de ocorrências das regras de rede|Contagem|Total|Número de vezes que as regras de rede foram atingidas|Status, motivo, protocolo|
|SNATPortUtilization|Sim|Utilização da porta SNAT|Porcentagem|Média|Porcentagem de portas SNAT de saída atualmente em uso|Protocolo|
|Taxa de transferência|Não|Taxa de transferência|BitsPerSecond|Média|Taxa de transferência processada por este firewall|Sem dimensões|


## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|BitsInPerSecond|Sim|BitsInPerSecond|CountPerSecond|Média|Bits de entrada no Azure por segundo|Sem dimensões|
|BitsOutPerSecond|Sim|BitsOutPerSecond|CountPerSecond|Média|Bits de saída no Azure por segundo|Sem dimensões|


## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|QueryVolume|Sim|Volume de consulta|Contagem|Total|Número de consultas atendidas para uma zona DNS|Sem dimensões|
|RecordSetCapacityUtilization|Não|Utilização de capacidade de Conjuntos de Registros|Porcentagem|Máximo|Porcentagem de capacidade de Conjuntos de Registros utilizada por uma zona DNS|Sem dimensões|
|RecordSetCount|Sim|Contagem de Conjuntos de Registros|Contagem|Máximo|Número de Conjuntos de Registros em uma zona DNS|Sem dimensões|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|ArpAvailability|Sim|Disponibilidade de ARP|Porcentagem|Média|Disponibilidade ARP de MSEE para todos os pares.|PeeringType, par|
|BgpAvailability|Sim|Disponibilidade de BGP|Porcentagem|Média|Disponibilidade de BGP de MSEE para todos os pares.|PeeringType, par|
|BitsInPerSecond|Não|BitsInPerSecond|CountPerSecond|Média|Bits de entrada no Azure por segundo|PeeringType|
|BitsOutPerSecond|Não|BitsOutPerSecond|CountPerSecond|Média|Bits de saída no Azure por segundo|PeeringType|
|GlobalReachBitsInPerSecond|Não|GlobalReachBitsInPerSecond|CountPerSecond|Média|Bits de entrada no Azure por segundo|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|Não|GlobalReachBitsOutPerSecond|CountPerSecond|Média|Bits de saída no Azure por segundo|PeeredCircuitSKey|
|QosDropBitsInPerSecond|Não|DroppedInBitsPerSecond|CountPerSecond|Média|Bits de entrada de dados eliminados por segundo|Sem dimensões|
|QosDropBitsOutPerSecond|Não|DroppedOutBitsPerSecond|CountPerSecond|Média|Bits de saída de dados eliminados por segundo|Sem dimensões|


## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|BitsInPerSecond|Sim|BitsInPerSecond|CountPerSecond|Média|Bits de entrada no Azure por segundo|Sem dimensões|
|BitsOutPerSecond|Sim|BitsOutPerSecond|CountPerSecond|Média|Bits de saída no Azure por segundo|Sem dimensões|


## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft.Network/expressRouteGateways

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|Não|BitsInPerSecond|CountPerSecond|Média|Bits de entrada no Azure por segundo|ConnectionName|
|ErGatewayConnectionBitsOutPerSecond|Não|BitsOutPerSecond|CountPerSecond|Média|Bits de saída no Azure por segundo|ConnectionName|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft.Network/expressRoutePorts

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|AdminState|Sim|AdminState|Contagem|Média|Estado do administrador da porta|Link|
|LineProtocol|Sim|LineProtocol|Contagem|Média|Status do protocolo de linha da porta|Link|
|PortBitsInPerSecond|Sim|BitsInPerSecond|CountPerSecond|Média|Bits de entrada no Azure por segundo|Link|
|PortBitsOutPerSecond|Sim|BitsOutPerSecond|CountPerSecond|Média|Bits de saída no Azure por segundo|Link|
|RxLightLevel|Sim|RxLightLevel|Contagem|Média|Nível de luz Rx em dBm|Link, Lane|
|TxLightLevel|Sim|TxLightLevel|Contagem|Média|Nível de luz TX em dBm|Link, Lane|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|BackendHealthPercentage|Sim|Percentual de integridade do back-end|Porcentagem|Média|O percentual de investigações de integridade bem-sucedidas do proxy HTTP/S aos back-ends|Backend, BackendPool|
|BackendRequestCount|Sim|Contagem de solicitações de back-end|Contagem|Total|O número de solicitações enviadas do proxy HTTP/S aos back-ends|HttpStatus, HttpStatusGroup, Backend|
|BackendRequestLatency|Sim|Latência de solicitação de back-end|MilliSeconds|Média|O tempo calculado de quando a solicitação foi enviada pelo proxy HTTP/S ao back-end até o proxy HTTP/S receber o último byte de resposta do back-end|Back-end|
|BillableResponseSize|Sim|Tamanho de Resposta Faturável|Bytes|Total|O número de bytes faturáveis (mínimo de 2 KB por solicitação) enviados como respostas do proxy HTTP/S para os clientes.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestCount|Sim|Contagem de solicitações|Contagem|Total|O número de solicitações de cliente atendidas pelo proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Sim|Tamanho da solicitação|Bytes|Total|O número de bytes enviados como solicitações de clientes ao proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Sim|Tamanho da resposta|Bytes|Total|O número de bytes enviados como respostas do proxy HTTP/S para clientes|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|TotalLatency|Sim|Latência total|MilliSeconds|Média|O tempo calculado de quando a solicitação do cliente foi recebida pelo proxy HTTP/S até o cliente confirmar o último byte de resposta do proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|WebApplicationFirewallRequestCount|Sim|Contagem de solicitações do Firewall de Aplicativo Web|Contagem|Total|O número de solicitações de cliente processadas pelo Firewall do Aplicativo Web|PolicyName, RuleName, Action|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|AllocatedSnatPorts|Não|Portas SNAT alocadas|Contagem|Média|Número total de portas SNAT alocadas no período|FrontendIPAddress, BackendIPAddress, ProtocolType, |
|ByteCount|Sim|Contagem de Bytes|Contagem|Total|Número total de Bytes transmitidos no período|FrontendIPAddress, FrontendPort, Direction|
|DipAvailability|Sim|Status de investigação de integridade|Contagem|Média|Status médio de investigação de integridade do Balanceador de Carga por duração de tempo|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|PacketCount|Sim|Contagem de Pacotes|Contagem|Total|Número total de Pacotes transmitidos no período|FrontendIPAddress, FrontendPort, Direction|
|SnatConnectionCount|Sim|Contagem de Conexões SNAT|Contagem|Total|Número total de novas conexões SNAT criadas no período|FrontendIPAddress, BackendIPAddress, ConnectionState|
|SYNCount|Sim|Contagem de SYN|Contagem|Total|Número total de Pacotes de SYN transmitidos no período|FrontendIPAddress, FrontendPort, Direction|
|UsedSnatPorts|Não|Portas SNAT usadas|Contagem|Média|Número total de portas SNAT usadas no período|FrontendIPAddress, BackendIPAddress, ProtocolType, |
|VipAvailability|Sim|Disponibilidade do Caminho de Dados|Contagem|Média|Disponibilidade média do caminho de dados do Balanceador de Carga por duração de tempo|FrontendIPAddress, FrontendPort|


## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|BytesReceivedRate|Sim|Bytes Recebidos|Bytes|Total|Número de bytes recebidos pela Interface de Rede|Sem dimensões|
|BytesSentRate|Sim|Bytes Enviados|Bytes|Total|Número de bytes enviados pela Interface de Rede|Sem dimensões|
|PacketsReceivedRate|Sim|Pacotes Recebidos|Contagem|Total|Número de pacotes recebidos pela Interface de Rede|Sem dimensões|
|PacketsSentRate|Sim|Pacotes Enviados|Contagem|Total|Número de bytes recebidos pela Interface de Rede|Sem dimensões|


## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|AverageRoundtripMs|Sim|Média Tempo de ida e volta (ms)|MilliSeconds|Média|Tempo ida e volta de rede médio (ms) para investigações de monitoramento de conectividade enviadas entre a origem e o destino|Sem dimensões|
|ChecksFailedPercent|Sim|Porcentagem de Verificações com Falha (Versão prévia)|Porcentagem|Média|% de verificações de monitoramento de conectividade com falha|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, Destinationname, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName|
|ProbesFailedPercent|Sim|Porcentagem de investigações com falha|Porcentagem|Média|Porcentagem de investigações de monitoramento de conectividade com falha|Sem dimensões|
|RoundTripTimeMs|Sim|Tempo de ida e volta (ms) (Versão prévia)|MilliSeconds|Média|Tempo de ida e volta em milissegundos para as verificações de monitoramento de conectividade|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, Destinationname, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|ByteCount|Sim|Contagem de Bytes|Contagem|Total|Número total de Bytes transmitidos no período|Port, Direction|
|BytesDroppedDDoS|Sim|DDoS de bytes de entrada removidos|BytesPerSecond|Máximo|DDoS de bytes de entrada removidos|Sem dimensões|
|BytesForwardedDDoS|Sim|DDoS de bytes de entrada encaminhados|BytesPerSecond|Máximo|DDoS de bytes de entrada encaminhados|Sem dimensões|
|BytesInDDoS|Sim|DDoS de bytes de entrada|BytesPerSecond|Máximo|DDoS de bytes de entrada|Sem dimensões|
|DDoSTriggerSYNPackets|Sim|Pacotes de entrada SYN para disparar a mitigação de DDoS|CountPerSecond|Máximo|Pacotes de entrada SYN para disparar a mitigação de DDoS|Sem dimensões|
|DDoSTriggerTCPPackets|Sim|Pacotes TCP de entrada a disparar a mitigação de DDoS|CountPerSecond|Máximo|Pacotes TCP de entrada a disparar a mitigação de DDoS|Sem dimensões|
|DDoSTriggerUDPPackets|Sim|Pacotes UDP de entrada a disparar a mitigação de DDoS|CountPerSecond|Máximo|Pacotes UDP de entrada a disparar a mitigação de DDoS|Sem dimensões|
|IfUnderDDoSAttack|Sim|Sob ataque DDoS ou não|Contagem|Máximo|Sob ataque DDoS ou não|Sem dimensões|
|PacketCount|Sim|Contagem de Pacotes|Contagem|Total|Número total de Pacotes transmitidos no período|Port, Direction|
|PacketsDroppedDDoS|Sim|DDoS de pacotes de entrada removidos|CountPerSecond|Máximo|DDoS de pacotes de entrada removidos|Sem dimensões|
|PacketsForwardedDDoS|Sim|DDoS de pacotes de entrada encaminhados|CountPerSecond|Máximo|DDoS de pacotes de entrada encaminhados|Sem dimensões|
|PacketsInDDoS|Sim|DDoS de pacotes de entrada|CountPerSecond|Máximo|DDoS de pacotes de entrada|Sem dimensões|
|SynCount|Sim|Contagem de SYN|Contagem|Total|Número total de Pacotes de SYN transmitidos no período|Port, Direction|
|TCPBytesDroppedDDoS|Sim|DDoS de bytes de TCP de entrada removidos|BytesPerSecond|Máximo|DDoS de bytes de TCP de entrada removidos|Sem dimensões|
|TCPBytesForwardedDDoS|Sim|DDoS de bytes de TCP de entrada encaminhados|BytesPerSecond|Máximo|DDoS de bytes de TCP de entrada encaminhados|Sem dimensões|
|TCPBytesInDDoS|Sim|DDoS de bytes de TCP de entrada|BytesPerSecond|Máximo|DDoS de bytes de TCP de entrada|Sem dimensões|
|TCPPacketsDroppedDDoS|Sim|DDoS de pacotes TCP de entrada removidos|CountPerSecond|Máximo|DDoS de pacotes TCP de entrada removidos|Sem dimensões|
|TCPPacketsForwardedDDoS|Sim|DDoS de pacotes TCP de entrada encaminhados|CountPerSecond|Máximo|DDoS de pacotes TCP de entrada encaminhados|Sem dimensões|
|TCPPacketsInDDoS|Sim|DDoS de pacotes TCP de entrada|CountPerSecond|Máximo|DDoS de pacotes TCP de entrada|Sem dimensões|
|UDPBytesDroppedDDoS|Sim|DDoS de bytes de UDP de entrada removidos|BytesPerSecond|Máximo|DDoS de bytes de UDP de entrada removidos|Sem dimensões|
|UDPBytesForwardedDDoS|Sim|DDoS de bytes de UDP de entrada encaminhados|BytesPerSecond|Máximo|DDoS de bytes de UDP de entrada encaminhados|Sem dimensões|
|UDPBytesInDDoS|Sim|DDoS de bytes de UDP de entrada|BytesPerSecond|Máximo|DDoS de bytes de UDP de entrada|Sem dimensões|
|UDPPacketsDroppedDDoS|Sim|DDoS de pacotes UDP de entrada removidos|CountPerSecond|Máximo|DDoS de pacotes UDP de entrada removidos|Sem dimensões|
|UDPPacketsForwardedDDoS|Sim|DDoS de pacotes UDP de entrada encaminhados|CountPerSecond|Máximo|DDoS de pacotes UDP de entrada encaminhados|Sem dimensões|
|UDPPacketsInDDoS|Sim|DDoS de pacotes UDP de entrada|CountPerSecond|Máximo|DDoS de pacotes UDP de entrada|Sem dimensões|
|VipAvailability|Sim|Disponibilidade do Caminho de Dados|Contagem|Média|Disponibilidade média de Endereço IP por duração de tempo|Porta|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Sim|Status do Ponto de Extremidade por Ponto de Extremidade|Contagem|Máximo|1 se o status de investigação de um ponto de extremidade for “Habilitado”; caso contrário, 0.|EndpointName|
|QpsByEndpoint|Sim|Consultas pelo Ponto de Extremidade Retornado|Contagem|Total|Número de vezes que um ponto de extremidade do Gerenciador de Tráfego foi retornado no período determinado|EndpointName|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|AverageBandwidth|Sim|Largura de banda de S2S do gateway|BytesPerSecond|Média|Largura de banda média site a site de um gateway em bytes por segundo|Sem dimensões|
|P2SBandwidth|Sim|Largura de banda de P2S do gateway|BytesPerSecond|Média|Largura de banda média ponto a site de um gateway em bytes por segundo|Sem dimensões|
|P2SConnectionCount|Sim|Contagem de conexões P2S|Contagem|Máximo|Contagem de conexões ponto a site de um gateway|Protocolo|
|TunnelAverageBandwidth|Sim|Largura de Banda de Túnel|BytesPerSecond|Média|Largura de banda média de um túnel em bytes por segundo|ConnectionName, RemoteIP|
|TunnelEgressBytes|Sim|Bytes de Saída de Túnel|Bytes|Total|Bytes de saída de um túnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Sim|Remoção de Pacotes Incompatíveis de TS de Saída de Túnel|Contagem|Total|Contagem de remoção de pacotes de saída da incompatibilidade do seletor de tráfego de um túnel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Sim|Pacotes de Saída de Túnel|Contagem|Total|Contagem de pacotes de saída de um túnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Sim|Bytes de Entrada de Túnel|Bytes|Total|Bytes de entrada de um túnel|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Sim|Remoção de Pacotes Incompatíveis de TS de Entrada de Túnel|Contagem|Total|Contagem de remoção de pacotes de entrada da incompatibilidade do seletor de tráfego de um túnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|Sim|Pacotes de Entrada de Túnel|Contagem|Total|Contagem de pacotes de entrada de um túnel|ConnectionName, RemoteIP|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Sim|Tempo de ida e volta para Pings em uma VM|MilliSeconds|Média|Tempo de ida e volta para pings enviados para uma VM de destino|SourceCustomerAddress, DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Sim|Pings com Falha em uma VM|Porcentagem|Média|Porcentagem do número de pings com falha para o total de pings enviados de uma VM de destino|SourceCustomerAddress, DestinationCustomerAddress|


## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|incoming|Sim|Mensagens de entrada|Contagem|Total|A contagem de todas as chamadas de API enviadas com sucesso. |Sem dimensões|
|incoming.all.failedrequests|Sim|Todas as solicitações com falha recebidas|Contagem|Total|Total de solicitações com falha recebidas para um hub de notificação|Sem dimensões|
|incoming.all.requests|Sim|Todas as solicitações recebidas|Contagem|Total|Total de solicitações recebidas para um hub de notificação|Sem dimensões|
|incoming.scheduled|Sim|Notificações por push agendadas enviadas|Contagem|Total|Notificações por push agendadas canceladas|Sem dimensões|
|incoming.scheduled.cancel|Sim|Notificações por push agendadas canceladas|Contagem|Total|Notificações por push agendadas canceladas|Sem dimensões|
|installation.all|Sim|Operações de gerenciamento de instalação|Contagem|Total|Operações de gerenciamento de instalação|Sem dimensões|
|installation.delete|Sim|Excluir operações de instalação|Contagem|Total|Excluir operações de instalação|Sem dimensões|
|installation.get|Sim|Obter operações de instalação|Contagem|Total|Obter operações de instalação|Sem dimensões|
|installation.patch|Sim|Operações de instalação de patch|Contagem|Total|Operações de instalação de patch|Sem dimensões|
|installation.upsert|Sim|Criar ou atualizar operações de instalação|Contagem|Total|Criar ou atualizar operações de instalação|Sem dimensões|
|notificationhub.pushes|Sim|Todas as notificações de saída|Contagem|Total|Todas as notificações de saída do hub de notificação|Sem dimensões|
|outgoing.allpns.badorexpiredchannel|Sim|Erros de canal incorreto ou expirado|Contagem|Total|A contagem de pushes que falharam porque o canal/token/registrationId do registro estava expirado ou inválido.|Sem dimensões|
|outgoing.allpns.channelerror|Sim|Erros de canal|Contagem|Total|A contagem de pushes que falharam porque o canal era inválido, não associado com o aplicativo correto, restrito ou expirado.|Sem dimensões|
|outgoing.allpns.invalidpayload|Sim|Erros de conteúdo|Contagem|Total|A contagem de pushes que falharam porque o PNS retornou um erro de conteúdo inválido.|Sem dimensões|
|outgoing.allpns.pnserror|Sim|Erros de sistema de notificação externa|Contagem|Total|A contagem de pushes que falharam porque houve um problema na comunicação com o PNS (exclui problemas de autenticação).|Sem dimensões|
|outgoing.allpns.success|Sim|Notificações de sucesso|Contagem|Total|A contagem de todas as notificações bem-sucedidas.|Sem dimensões|
|outgoing.apns.badchannel|Sim|Erro de canal inválido do APNS|Contagem|Total|A contagem de pushes que falharam porque o token é inválido (código de status do APNS: 8).|Sem dimensões|
|outgoing.apns.expiredchannel|Sim|Erro de canal expirado do APNS|Contagem|Total|A contagem de tokens que foram invalidados pelo canal de comentários do APNS.|Sem dimensões|
|outgoing.apns.invalidcredentials|Sim|Erros de autorização do APNS|Contagem|Total|A contagem de pushes que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Sem dimensões|
|outgoing.apns.invalidnotificationsize|Sim|Erro de tamanho de notificação inválido do APNS|Contagem|Total|A contagem de pushes que falharam porque o conteúdo era muito grande (código de status do APNS: 7).|Sem dimensões|
|outgoing.apns.pnserror|Sim|Erros do APNS|Contagem|Total|A contagem de pushes que falharam devido a erros de comunicação com o APNS.|Sem dimensões|
|outgoing.apns.success|Sim|Notificações do APNS bem-sucedidas|Contagem|Total|A contagem de todas as notificações bem-sucedidas.|Sem dimensões|
|outgoing.gcm.authenticationerror|Sim|Erros de autenticação do GCM|Contagem|Total|A contagem de pushes que falharam porque o PNS não aceitou as credenciais fornecidas, as credenciais estão bloqueadas ou o SenderId não está configurado corretamente no aplicativo (resultado do GCM: MismatchedSenderId).|Sem dimensões|
|outgoing.gcm.badchannel|Sim|Erro de canal inválido do GCM|Contagem|Total|A contagem de pushes que falharam porque o registrationId do registro estava expirado ou inválido (resultado GCM: registo inválido).|Sem dimensões|
|outgoing.gcm.expiredchannel|Sim|Erro de canal expirado do GCM|Contagem|Total|A contagem de pushes que falharam porque o registrationId do registro estava expirado (resultado do GCM: NotRegistered).|Sem dimensões|
|outgoing.gcm.invalidcredentials|Sim|Erros de autorização do GCM (credenciais inválidas)|Contagem|Total|A contagem de pushes que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Sem dimensões|
|outgoing.gcm.invalidnotificationformat|Sim|Formato de notificação inválido do GCM|Contagem|Total|A contagem de pushes que falharam porque o conteúdo não foi formatado corretamente (resultado do GCM: InvalidDataKey ou InvalidTtl).|Sem dimensões|
|outgoing.gcm.invalidnotificationsize|Sim|Erro de tamanho de notificação inválido do GCM|Contagem|Total|A contagem de pushes que falharam porque o conteúdo era muito grande (resultado do GCM: MessageTooBig).|Sem dimensões|
|outgoing.gcm.pnserror|Sim|Erros do GCM|Contagem|Total|A contagem de pushes que falharam devido a erros de comunicação com o GCM.|Sem dimensões|
|outgoing.gcm.success|Sim|Notificações do GCM bem-sucedidas|Contagem|Total|A contagem de todas as notificações bem-sucedidas.|Sem dimensões|
|outgoing.gcm.throttled|Sim|Notificações restritas do GCM|Contagem|Total|A contagem de pushes que falharam porque o GCM restringiu este aplicativo (código de status do GCM: 501 a 599 ou resultado: indisponível).|Sem dimensões|
|outgoing.gcm.wrongchannel|Sim|Erro de canal incorreto do GCM|Contagem|Total|A contagem de pushes que falharam porque o registrationId do registro não está associado ao aplicativo atual (resultado do GCM: InvalidPackageName).|Sem dimensões|
|outgoing.mpns.authenticationerror|Sim|Erros de autenticação do MPNS|Contagem|Total|A contagem de pushes que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Sem dimensões|
|outgoing.mpns.badchannel|Sim|Erro de canal inválido do MPNS|Contagem|Total|A contagem de pushes que falharam porque o ChannelURI do registro não foi reconhecido (status do MPNS: 404 não encontrado).|Sem dimensões|
|outgoing.mpns.channeldisconnected|Sim|Canal do MPNS desconectado|Contagem|Total|A contagem de pushes que falharam porque o ChannelURI do registro foi desconectado (status do MPNS: 412 não encontrado).|Sem dimensões|
|outgoing.mpns.dropped|Sim|Notificações descartadas do MPNS|Contagem|Total|A contagem de pushes que foram descartados pelo MPNS (cabeçalho de resposta do MPNS: X-NotificationStatus: QueueFull ou Suppressed).|Sem dimensões|
|outgoing.mpns.invalidcredentials|Sim|Credenciais inválidas do MPNS|Contagem|Total|A contagem de pushes que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Sem dimensões|
|outgoing.mpns.invalidnotificationformat|Sim|Erro de tamanho de notificação inválido do MPNS|Contagem|Total|A contagem de pushes que falharam porque o conteúdo da notificação era muito grande.|Sem dimensões|
|outgoing.mpns.pnserror|Sim|Erros do MPNS|Contagem|Total|A contagem de pushes que falharam devido a erros de comunicação com o MPNS.|Sem dimensões|
|outgoing.mpns.success|Sim|Notificações de sucesso do MPNS|Contagem|Total|A contagem de todas as notificações bem-sucedidas.|Sem dimensões|
|outgoing.mpns.throttled|Sim|Notificações restritas do MPNS|Contagem|Total|A contagem de pushes que falharam porque o MPNS está limitando este aplicativo (WNS MPNS: 406 não aceitável).|Sem dimensões|
|outgoing.wns.authenticationerror|Sim|Erros de autenticação do WNS|Contagem|Total|Notificação não entregue devido a erros de comunicação com o Windows Live, credenciais inválidas ou token incorreto.|Sem dimensões|
|outgoing.wns.badchannel|Sim|Erro de canal inválido do WNS|Contagem|Total|A contagem de pushes que falharam porque o ChannelURI do registro não foi reconhecido (status do WNS: 404 não encontrado).|Sem dimensões|
|outgoing.wns.channeldisconnected|Sim|Canal do WNS desconectado|Contagem|Total|A notificação foi descartada porque o ChannelURI do registro está restrito (cabeçalho de resposta do WNS: X-WNS-DeviceConnectionStatus: disconnected).|Sem dimensões|
|outgoing.wns.channelthrottled|Sim|Canal do WNS restrito|Contagem|Total|A notificação foi descartada porque o ChannelURI do registro está restrito (cabeçalho de resposta do WNS: X-WNS-NotificationStatus:channelThrottled).|Sem dimensões|
|outgoing.wns.dropped|Sim|Notificações do WNS descartadas|Contagem|Total|A notificação foi descartada porque o ChannelURI do registro está restrito (X-WNS-NotificationStatus: dropped but not X-WNS-DeviceConnectionStatus: disconnected).|Sem dimensões|
|outgoing.wns.expiredchannel|Sim|Erro de canal expirado do WNS|Contagem|Total|A contagem de pushes que falharam porque o ChannelURI está expirado (status do WNS: 410 perdido).|Sem dimensões|
|outgoing.wns.invalidcredentials|Sim|Erros de autorização do WNS (credenciais inválidas)|Contagem|Total|A contagem de pushes que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas. (O Windows Live não reconhece as credenciais).|Sem dimensões|
|outgoing.wns.invalidnotificationformat|Sim|Formato de notificação inválido do WNS|Contagem|Total|O formato da notificação é inválido (status do WNS: 400). Observe que o WNS não rejeita todos os conteúdos inválidos.|Sem dimensões|
|outgoing.wns.invalidnotificationsize|Sim|Erro de tamanho de notificação inválido do WNS|Contagem|Total|O conteúdo de notificação é muito grande (status do WNS: 413).|Sem dimensões|
|outgoing.wns.invalidtoken|Sim|Erros de autorização do WNS (token inválido)|Contagem|Total|O token fornecido ao WNS não é válido (status do WNS: 401 não autorizado).|Sem dimensões|
|outgoing.wns.pnserror|Sim|Erros do WNS|Contagem|Total|Notificação não entregue devido a erros de comunicação com o WNS.|Sem dimensões|
|outgoing.wns.success|Sim|Notificações do WNS bem-sucedidas|Contagem|Total|A contagem de todas as notificações bem-sucedidas.|Sem dimensões|
|outgoing.wns.throttled|Sim|Notificações restritas do WNS|Contagem|Total|A contagem de pushes que falharam porque o WNS está limitando este aplicativo (status do WNS: 406 não aceitável).|Sem dimensões|
|outgoing.wns.tokenproviderunreachable|Sim|Erros de autorização do WNS (inacessível)|Contagem|Total|O Windows Live não está acessível.|Sem dimensões|
|outgoing.wns.wrongtoken|Sim|Erros de autorização do WNS (token incorreto)|Contagem|Total|O token fornecido ao WNS é válido, mas para outro aplicativo (status do WNS: 403 proibido). Isso pode acontecer se o ChannelURI do registro estiver associado a outro aplicativo. Verifique se o aplicativo cliente está associado ao mesmo aplicativo cujas credenciais estão no hub de notificação.|Sem dimensões|
|registration.all|Sim|Operações de Registro|Contagem|Total|A contagem de todas as operações de registro bem-sucedidas (consultas de atualizações de criações e exclusões). |Sem dimensões|
|registration.create|Sim|Operação de criação de registro|Contagem|Total|A contagem de todas as criações de registro bem-sucedidas.|Sem dimensões|
|registration.delete|Sim|Operação de exclusão de registro|Contagem|Total|A contagem de todas as exclusões de registro bem-sucedidas.|Sem dimensões|
|registration.get|Sim|Operações de leitura do registro|Contagem|Total|A contagem de todas as consultas de registro bem-sucedidas.|Sem dimensões|
|registration.update|Sim|Operação de atualização de registro|Contagem|Total|A contagem de todas as atualizações de registro bem-sucedidas.|Sem dimensões|
|scheduled.pending|Sim|Notificações agendadas pendentes|Contagem|Total|Notificações agendadas pendentes|Sem dimensões|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Average_% Available Memory|Sim|% de Memória Disponível|Contagem|Média|Average_% Available Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Available Swap Space|Sim|% de Espaço de Permuta Disponível|Contagem|Média|Average_% Available Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Committed Bytes In Use|Sim|% de Bytes Confirmados em Uso|Contagem|Média|Average_% Committed Bytes In Use|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% DPC Time|Sim|% de Tempo de DPC|Contagem|Média|Average_% DPC Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Inodes|Sim|% de Inodes livres|Contagem|Média|Average_% Free Inodes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Space|Sim|% de Espaço Livre|Contagem|Média|Average_% Free Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Idle Time|Sim|% de Tempo Ocioso|Contagem|Média|Average_% Idle Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Interrupt Time|Sim|% de Tempo de Interrupção|Contagem|Média|Average_% Interrupt Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% IO Wait Time|Sim|% de Tempo de Espera de E/S|Contagem|Média|Average_% IO Wait Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Nice Time|Sim|% de Tempo Adequado|Contagem|Média|Average_% Nice Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Privileged Time|Sim|% de Tempo Privilegiado|Contagem|Média|Average_% Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Processor Time|Sim|% Tempo do Processador|Contagem|Média|Average_% Processor Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Inodes|Sim|% de Inodes Usados|Contagem|Média|Average_% Used Inodes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Memory|Sim|% de Memória Usada|Contagem|Média|Average_% Used Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Space|Sim|% de Espaço Usado|Contagem|Média|Average_% Used Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Swap Space|Sim|% de Espaço de Permuta Usado|Contagem|Média|Average_% Used Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% User Time|Sim|% de Tempo do Usuário|Contagem|Média|Average_% User Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes|Sim|MBytes Disponíveis|Contagem|Média|Average_Available MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Memory|Sim|MBytes de Memória Disponíveis|Contagem|Média|Average_Available MBytes Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Swap|Sim|MBytes de Espaço de Permuta Disponíveis|Contagem|Média|Average_Available MBytes Swap|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. de segundos/Leitura do Disco|Sim|Média de segundos/Leitura do Disco|Contagem|Média|Average_Avg. de segundos/Leitura do Disco|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. de segundos/Transferência do Disco|Sim|Média de segundos/Transferência do Disco|Contagem|Média|Average_Avg. de segundos/Transferência do Disco|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. de segundos/Gravação do Disco|Sim|Média de segundos/Gravação do Disco|Contagem|Média|Average_Avg. de segundos/Gravação do Disco|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Received/sec|Sim|Bytes Recebidos/s|Contagem|Média|Average_Bytes Received/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Sent/sec|Sim|Bytes Enviados/s|Contagem|Média|Average_Bytes Sent/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Total/sec|Sim|Bytes Totais/s|Contagem|Média|Average_Bytes Total/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current Disk Queue Length|Sim|Comprimento da fila atual de disco|Contagem|Média|Average_Current Disk Queue Length|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Read Bytes/sec|Sim|Bytes Lidos no Disco/s|Contagem|Média|Average_Disk Read Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Reads/sec|Sim|Leituras de Disco/s|Contagem|Média|Average_Disk Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Transfers/sec|Sim|Transferências de Disco/s|Contagem|Média|Average_Disk Transfers/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Write Bytes/sec|Sim|Bytes Gravados no Disco/s|Contagem|Média|Average_Disk Write Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Writes/sec|Sim|Gravações de Disco/s|Contagem|Média|Average_Disk Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Megabytes|Sim|Megabytes Livres|Contagem|Média|Average_Free Megabytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Physical Memory|Sim|Memória Física Livre|Contagem|Média|Average_Free Physical Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Space in Paging Files|Sim|Espaço Livre em Arquivos de Paginação|Contagem|Média|Average_Free Space in Paging Files|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Virtual Memory|Sim|Memória Virtual Livre|Contagem|Média|Average_Free Virtual Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical Disk Bytes/sec|Sim|Bytes de Disco Lógico/s|Contagem|Média|Average_Logical Disk Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Reads/sec|Sim|Leituras de Página/s|Contagem|Média|Average_Page Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Writes/sec|Sim|Gravações de Página/s|Contagem|Média|Average_Page Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages/sec|Sim|Páginas/s|Contagem|Média|Average_Pages/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct Privileged Time|Sim|% de Tempo Privilegiado|Contagem|Média|Average_Pct Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct User Time|Sim|% de Tempo do Usuário|Contagem|Média|Average_Pct User Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical Disk Bytes/sec|Sim|Bytes/s do Disco Físico|Contagem|Média|Average_Physical Disk Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Sim|Processos|Contagem|Média|Average_Processes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processor Queue Length|Sim|Tamanho da fila do processador|Contagem|Média|Average_Processor Queue Length|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size Stored In Paging Files|Sim|Tamanho Armazenado em Arquivos de Paginação|Contagem|Média|Average_Size Stored In Paging Files|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes|Sim|Total de Bytes|Contagem|Média|Average_Total Bytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Received|Sim|Total de Bytes Recebidos|Contagem|Média|Average_Total Bytes Received|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Transmitted|Sim|Total de Bytes Transmitidos|Contagem|Média|Average_Total Bytes Transmitted|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Collisions|Sim|Total de Colisões|Contagem|Média|Average_Total Collisions|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Packets Received|Sim|Total de Pacotes Recebidos|Contagem|Média|Average_Total Packets Received|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Packets Transmitted|Sim|Total de Pacotes Transmitidos|Contagem|Média|Average_Total Packets Transmitted|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Rx Errors|Sim|Total de Erros de Rx|Contagem|Média|Average_Total Rx Errors|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Tx Errors|Sim|Total de Erros de Tx|Contagem|Média|Average_Total Tx Errors|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Sim|Tempo de atividade|Contagem|Média|Average_Uptime|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used MBytes Swap Space|Sim|MBytes de Espaço de Permuta Usado|Contagem|Média|Average_Used MBytes Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory kBytes|Sim|KBytes de Memória Usada|Contagem|Média|Average_Used Memory kBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory MBytes|Sim|MBytes de Memória Usada|Contagem|Média|Average_Used Memory MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Sim|Usuários|Contagem|Média|Average_Users|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual Shared Memory|Sim|Memória Virtual Compartilhada|Contagem|Média|Average_Virtual Shared Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Evento|Sim|Evento|Contagem|Média|Evento|Source, EventLog, Computer, EventCategory, EventLevel, EventLevelName, EventID|
|Pulsação|Sim|Pulsação|Contagem|Total|Pulsação|Computador, OSType, Versão, SourceComputerId|
|Atualizar|Sim|Atualizar|Contagem|Média|Atualizar|Computer, Product, Classification, UpdateState, Optional, Approved|


## <a name="microsoftpeeringpeerings"></a>Microsoft. emparelhamento/emparelhamentos

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|EgressTrafficRate|Sim|Taxa de tráfego de saída|BitsPerSecond|Média|Taxa de tráfego de saída em bits por segundo|ConnectionId|
|IngressTrafficRate|Sim|Taxa de tráfego de entrada|BitsPerSecond|Média|Taxa de tráfego de entrada em bits por segundo|ConnectionId|
|SessionAvailabilityV4|Sim|Disponibilidade de sessão v4|Porcentagem|Média|Disponibilidade da sessão v4|ConnectionId|
|SessionAvailabilityV6|Sim|Disponibilidade de sessão V6|Porcentagem|Média|Disponibilidade da sessão V6|ConnectionId|


## <a name="microsoftpeeringpeeringservices"></a>Microsoft. emparelhamento/peeringServices

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|PrefixLatency|Sim|Latência de prefixo|Milissegundos|Média|Latência de prefixo mediana|Prefixoname|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|memory_metric|Sim|Memória|Bytes|Média|Memória. Intervalo de 0 a 3 GB para A1, 0 a 5 GB para A2, 0 a 10 GB para A3, 0 a 25 GB para A4, 0 a 50 GB para A5 e 0 a 100 GB para A6|Sem dimensões|
|memory_thrashing_metric|Sim|Sobrecarga de Memória (Conjuntos de dados)|Porcentagem|Média|Sobrecarga de memória média.|Sem dimensões|
|qpu_high_utilization_metric|Sim|Alta utilização de QPU|Contagem|Total|QPU de alta utilização no último minuto, 1 para QPU de alta utilização, caso contrário, 0|Sem dimensões|
|QueryDuration|Sim|Duração da Consulta (Conjuntos de dados)|Milissegundos|Média|Duração da consulta DAX no último intervalo|Sem dimensões|
|QueryPoolJobQueueLength|Sim|Tamanho da Fila de Trabalhos do Pool de Consultas (Conjuntos de dados)|Contagem|Média|Número de trabalhos na fila do pool de threads de consulta.|Sem dimensões|


## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|ActiveConnections|Não|ActiveConnections|Contagem|Total|Total de ActiveConnections para o Microsoft.Relay.|EntityName|
|ActiveListeners|Não|ActiveListeners|Contagem|Total|Total de ActiveListeners para o Microsoft.Relay.|EntityName|
|BytesTransferred|Sim|BytesTransferred|Contagem|Total|Total de BytesTransferred para o Microsoft.Relay.|EntityName|
|ListenerConnections-ClientError|Não|ListenerConnections-ClientError|Contagem|Total|ClientError no ListenerConnections para o Microsoft.Relay.|EntityName, |
|ListenerConnections-ServerError|Não|ListenerConnections-ServerError|Contagem|Total|ServerError no ListenerConnections para o Microsoft.Relay.|EntityName, |
|ListenerConnections-Success|Não|ListenerConnections-Success|Contagem|Total|ListenerConnections bem-sucedido para o Microsoft.Relay.|EntityName, |
|ListenerConnections-TotalRequests|Não|ListenerConnections-TotalRequests|Contagem|Total|Total de ListenerConnections para o  Microsoft.Relay.|EntityName|
|ListenerDisconnects|Não|ListenerDisconnects|Contagem|Total|Total de ListenerDisconnects para o Microsoft.Relay.|EntityName|
|SenderConnections-ClientError|Não|SenderConnections-ClientError|Contagem|Total|ClientError no SenderConnections para o Microsoft.Relay.|EntityName, |
|SenderConnections-ServerError|Não|SenderConnections-ServerError|Contagem|Total|ServerError no SenderConnections para o Microsoft.Relay.|EntityName, |
|SenderConnections-Success|Não|SenderConnections-Success|Contagem|Total|SenderConnections bem-sucedido para o Microsoft.Relay.|EntityName, |
|SenderConnections-TotalRequests|Não|SenderConnections-TotalRequests|Contagem|Total|Total de solicitações SenderConnections para o Microsoft.Relay.|EntityName|
|SenderDisconnects|Não|SenderDisconnects|Contagem|Total|Total de SenderDisconnects para o Microsoft.Relay.|EntityName|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|SearchLatency|Sim|Latência de pesquisa|Segundos|Média|Latência média de pesquisa do serviço de pesquisa|Sem dimensões|
|SearchQueriesPerSecond|Sim|Consultas de pesquisa por segundo|CountPerSecond|Média|Consultas de pesquisa por segundo para o serviço de pesquisa|Sem dimensões|
|ThrottledSearchQueriesPercentage|Sim|Porcentagem das consultas de pesquisa limitadas|Porcentagem|Média|Porcentagem de consultas de pesquisa que eram limitadas para o serviço de pesquisa|Sem dimensões|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|ActiveConnections|Não|ActiveConnections|Contagem|Total|Conexões Totalmente Ativas para o Microsoft.ServiceBus.|Sem dimensões|
|ActiveMessages|Não|Contagem de mensagens em uma fila/tópico.|Contagem|Média|Contagem de mensagens em uma fila/tópico.|EntityName|
|ConnectionsClosed|Não|Conexões Fechadas.|Contagem|Média|Conexões Fechadas para o Microsoft.ServiceBus.|EntityName|
|ConnectionsOpened|Não|Conexões Abertas.|Contagem|Média|Conexões Abertas para o Microsoft.ServiceBus.|EntityName|
|CPUXNS|Não|CPU (Preterido)|Porcentagem|Máximo|Métrica de uso de CPU do namespace Premium do barramento de serviço. Essa métrica é preterida. Em vez disso, use a métrica de CPU (NamespaceCpuUsage).|Sem dimensões|
|DeadletteredMessages|Não|Contagem de mensagens mortas em uma Fila/Tópico.|Contagem|Média|Contagem de mensagens mortas em uma Fila/Tópico.|EntityName|
|IncomingMessages|Sim|Mensagens de entrada|Contagem|Total|Mensagens de Entrada para Microsoft.ServiceBus.|EntityName|
|IncomingRequests|Sim|Solicitações de entrada|Contagem|Total|Solicitações de Entrada para o Microsoft.ServiceBus.|EntityName|
|Mensagens|Não|Contagem de mensagens em uma fila/tópico.|Contagem|Média|Contagem de mensagens em uma fila/tópico.|EntityName|
|NamespaceCpuUsage|Não|CPU|Porcentagem|Máximo|Métrica de uso de CPU do namespace Premium do barramento de serviço.|Sem dimensões|
|NamespaceMemoryUsage|Não|Uso de Memória|Porcentagem|Máximo|Métrica de uso de memória do namespace Premium do barramento de serviço.|Sem dimensões|
|OutgoingMessages|Sim|Mensagens de saída|Contagem|Total|Mensagens de Saída para o Microsoft.ServiceBus.|EntityName|
|ScheduledMessages|Não|Contagem de mensagens agendadas em uma Fila/Tópico.|Contagem|Média|Contagem de mensagens agendadas em uma Fila/Tópico.|EntityName|
|ServerErrors|Não|Erros do Servidor.|Contagem|Total|Erros do Servidor para o Microsoft.ServiceBus.|EntityName, |
|Tamanho|Não|Tamanho|Bytes|Média|Tamanho de uma fila/tópico em bytes.|EntityName|
|SuccessfulRequests|Não|Solicitações bem sucedidas|Contagem|Total|Total de solicitações bem-sucedidas para um namespace|EntityName, |
|ThrottledRequests|Não|Solicitações Limitadas.|Contagem|Total|Solicitações Limitadas para o Microsoft.ServiceBus.|EntityName, |
|UserErrors|Não|Erros de Usuário.|Contagem|Total|Erros de Usuário para o Microsoft.ServiceBus.|EntityName, |
|WSXNS|Não|Uso de Memória (Preterido)|Porcentagem|Máximo|Métrica de uso de memória do namespace Premium do barramento de serviço. Essa métrica foi preterida. Use a métrica de uso de memória (NamespaceMemoryUsage) em vez disso.|Sem dimensões|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|ActualCpu|Não|ActualCpu|Contagem|Média|Uso real da CPU em núcleos de mili|ApplicationName, ServiceName, CodePackageName, ServiceId|
|ActualMemory|Não|ActualMemory|Bytes|Média|Uso real da memória em MB|ApplicationName, ServiceName, CodePackageName, ServiceId|
|AllocatedCpu|Não|AllocatedCpu|Contagem|Média|CPU alocada para este contêiner em núcleos de mili|ApplicationName, ServiceName, CodePackageName, ServiceId|
|AllocatedMemory|Não|AllocatedMemory|Bytes|Média|Memória alocada para este contêiner em MB|ApplicationName, ServiceName, CodePackageName, ServiceId|
|ApplicationStatus|Não|ApplicationStatus|Contagem|Média|Status do aplicativo de malha Service Fabric|ApplicationName, status|
|ContainerStatus|Não|ContainerStatus|Contagem|Média|Status do contêiner no aplicativo de malha Service Fabric|ApplicationName, ServiceName, CodePackageName, ServiceId, status|
|CpuUtilization|Não|CpuUtilization|Porcentagem|Média|Utilização da CPU para este contêiner como porcentagem de AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceId|
|MemoryUtilization|Não|MemoryUtilization|Porcentagem|Média|Utilização da CPU para este contêiner como porcentagem de AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceId|
|RestartCount|Não|RestartCount|Contagem|Média|Reiniciar a contagem de um contêiner no aplicativo de malha Service Fabric|ApplicationName, status, ServiceName, nome de réplica, CodePackageName|
|ServiceReplicaStatus|Não|ServiceReplicaStatus|Contagem|Média|Status de integridade de uma réplica de serviço no aplicativo de malha Service Fabric|ApplicationName, status, ServiceName, nome de réplica|
|ServiceStatus|Não|ServiceStatus|Contagem|Média|Status de integridade de um serviço no aplicativo de malha Service Fabric|ApplicationName, status, ServiceName|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|ConnectionCount|Sim|Contagem de conexão|Contagem|Máximo|A quantidade de conexões do usuário.|Ponto de extremidade|
|InboundTraffic|Sim|Tráfego de entrada|Bytes|Total|O tráfego de entrada do serviço|Sem dimensões|
|MessageCount|Sim|Contagem de mensagens|Contagem|Total|A quantidade total de mensagens.|Sem dimensões|
|OutboundTraffic|Sim|Tráfego de saída|Bytes|Total|O tráfego de saída do serviço|Sem dimensões|
|SystemErrors|Sim|Erros do sistema|Porcentagem|Máximo|A porcentagem de erros do sistema|Sem dimensões|
|UserErrors|Sim|Erros do usuário|Porcentagem|Máximo|A porcentagem de erros do usuário|Sem dimensões|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|avg_cpu_percent|Sim|Percentual médio de CPU|Porcentagem|Média|Percentual médio de CPU|Sem dimensões|
|io_bytes_read|Sim|Bytes de E/S lidos|Bytes|Média|Bytes de E/S lidos|Sem dimensões|
|io_bytes_written|Sim|Bytes de E/S gravados|Bytes|Média|Bytes de E/S gravados|Sem dimensões|
|io_requests|Sim|Contagem de solicitações de E/S|Contagem|Média|Contagem de solicitações de E/S|Sem dimensões|
|reserved_storage_mb|Sim|Espaço de armazenamento reservado|Contagem|Média|Espaço de armazenamento reservado|Sem dimensões|
|storage_space_used_mb|Sim|Espaço de armazenamento usado|Contagem|Média|Espaço de armazenamento usado|Sem dimensões|
|virtual_core_count|Sim|Contagem de núcleos virtuais|Contagem|Média|Contagem de núcleos virtuais|Sem dimensões|


## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|active_queries|Sim|Consultas ativas|Contagem|Total|Consultas ativas em todos os grupos de carga de trabalho. Aplica-se somente a data warehouses.|Sem dimensões|
|allocated_data_storage|Sim|Espaço alocado de dados|Bytes|Média|Armazenamento de dados alocado. Não aplicável a data warehouses.|Sem dimensões|
|app_cpu_billed|Sim|CPU do aplicativo cobrado|Contagem|Total|CPU do aplicativo cobrada. Aplica-se a bancos de dados sem servidor.|Sem dimensões|
|app_cpu_percent|Sim|Percentual de CPU do aplicativo|Porcentagem|Média|Porcentagem de CPU do aplicativo. Aplica-se a bancos de dados sem servidor.|Sem dimensões|
|app_memory_percent|Sim|Percentual de memória do aplicativo|Porcentagem|Média|Porcentagem de memória do aplicativo. Aplica-se a bancos de dados sem servidor.|Sem dimensões|
|base_blob_size_bytes|Sim|Tamanho do armazenamento de BLOBs base|Bytes|Máximo|Tamanho do armazenamento de BLOBs base. Aplica-se a bancos de dados de hiperescala.|Sem dimensões|
|blocked_by_firewall|Sim|Bloqueado pelo firewall|Contagem|Total|Bloqueado pelo firewall|Sem dimensões|
|cache_hit_percent|Sim|Percentual de ocorrência no cache|Porcentagem|Máximo|Porcentagem de acesso ao cache. Aplica-se somente a data warehouses.|Sem dimensões|
|cache_used_percent|Sim|Percentual de cache usado|Porcentagem|Máximo|Percentual de cache usado. Aplica-se somente a data warehouses.|Sem dimensões|
|connection_failed|Sim|Conexões com falha|Contagem|Total|Conexões com falha|Sem dimensões|
|connection_successful|Sim|Conexões bem sucedidas|Contagem|Total|Conexões bem sucedidas|Sem dimensões|
|cpu_limit|Sim|Limite da CPU|Contagem|Média|Limite de CPU. Aplica-se a bancos de dados baseados em vCore.|Sem dimensões|
|cpu_percent|Sim|Percentual de CPU|Porcentagem|Média|Percentual de CPU|Sem dimensões|
|cpu_used|Sim|CPU usada|Contagem|Média|CPU usada. Aplica-se a bancos de dados baseados em vCore.|Sem dimensões|
|deadlock|Sim|Deadlocks|Contagem|Total|Deadlocks. Não aplicável a data warehouses.|Sem dimensões|
|diff_backup_size_bytes|Sim|Tamanho de armazenamento diferencial de backup|Bytes|Máximo|Tamanho do armazenamento de backup diferencial cumulativo. Aplica-se a bancos de dados baseados em vCore. Não aplicável a bancos de dados de hiperescala.|Sem dimensões|
|dtu_consumption_percent|Sim|Porcentagem de DTU|Porcentagem|Média|Porcentagem de DTU. Aplica-se a bancos de dados baseados em DTU.|Sem dimensões|
|dtu_limit|Sim|Limite de DTU|Contagem|Média|Limite de DTU. Aplica-se a bancos de dados baseados em DTU.|Sem dimensões|
|dtu_used|Sim|DTU usado|Contagem|Média|DTU usado. Aplica-se a bancos de dados baseados em DTU.|Sem dimensões|
|dw_backup_size_gb|Sim|Tamanho do armazenamento de dados (GB)|Contagem|Total|O tamanho do armazenamento de dados é composto do tamanho dos seus dados e do log de transações. A métrica é contada em direção à parte ' armazenamento ' da sua fatura. Aplica-se somente a data warehouses.|Sem dimensões|
|dw_geosnapshot_size_gb|Sim|Tamanho do armazenamento de recuperação de desastre (GB)|Contagem|Total|O tamanho do armazenamento de recuperação de desastres é refletido como ' armazenamento de recuperação de desastres ' em sua fatura. Aplica-se somente a data warehouses.|Sem dimensões|
|dw_snapshot_size_gb|Sim|Tamanho do armazenamento de instantâneos (GB)|Contagem|Total|Tamanho do armazenamento de instantâneos é o tamanho das alterações incrementais capturadas por instantâneos para criar pontos de restauração definidos pelo usuário e automáticos. A métrica é contada em direção à parte ' armazenamento ' da sua fatura. Aplica-se somente a data warehouses.|Sem dimensões|
|dwu_consumption_percent|Sim|Porcentagem de DWU|Porcentagem|Máximo|Porcentagem de DWU. Aplica-se somente a data warehouses.|Sem dimensões|
|dwu_limit|Sim|Limite de DWU|Contagem|Máximo|Limite de DWU. Aplica-se somente a data warehouses.|Sem dimensões|
|dwu_used|Sim|DWU usado|Contagem|Máximo|DWU usado. Aplica-se somente a data warehouses.|Sem dimensões|
|full_backup_size_bytes|Sim|Tamanho de armazenamento de backup completo|Bytes|Máximo|Tamanho de armazenamento de backup completo cumulativo. Aplica-se a bancos de dados baseados em vCore. Não aplicável a bancos de dados de hiperescala.|Sem dimensões|
|local_tempdb_usage_percent|Sim|Porcentagem de local de tempdb|Porcentagem|Média|Porcentagem de tempdb local. Aplica-se somente a data warehouses.|Sem dimensões|
|log_backup_size_bytes|Sim|Tamanho do armazenamento de backup de log|Bytes|Máximo|Tamanho de armazenamento de backup de log cumulativo. Aplica-se a bancos de dados com base em vCore e em hiperescala.|Sem dimensões|
|log_write_percent|Sim|Porcentagem de E/S de log|Porcentagem|Média|Percentual de e/s de log. Não aplicável a data warehouses.|Sem dimensões|
|memory_usage_percent|Sim|Percentual de memória|Porcentagem|Máximo|Porcentagem de memória. Aplica-se somente a data warehouses.|Sem dimensões|
|physical_data_read_percent|Sim|Porcentagem de E/S de dados|Porcentagem|Média|Porcentagem de E/S de dados|Sem dimensões|
|queued_queries|Sim|Consultas em fila|Contagem|Total|Consultas em fila em todos os grupos de carga de trabalho. Aplica-se somente a data warehouses.|Sem dimensões|
|sessions_percent|Sim|Porcentagem de sessões|Porcentagem|Média|Porcentagem de sessões. Não aplicável a data warehouses.|Sem dimensões|
|snapshot_backup_size_bytes|Sim|Tamanho do armazenamento de backup de instantâneo|Bytes|Máximo|Tamanho de armazenamento de backup de instantâneo cumulativo. Aplica-se a bancos de dados de hiperescala.|Sem dimensões|
|sqlserver_process_core_percent|Sim|Porcentagem de núcleo do processo de SQL Server|Porcentagem|Máximo|Uso da CPU como uma porcentagem do processo do BD SQL. Não aplicável a data warehouses.|Sem dimensões|
|sqlserver_process_memory_percent|Sim|Porcentagem de memória de processo SQL Server|Porcentagem|Máximo|Uso de memória como uma porcentagem do processo do BD SQL. Não aplicável a data warehouses.|Sem dimensões|
|armazenamento|Sim|Espaço de dados usado|Bytes|Máximo|Espaço de dados usado. Não aplicável a data warehouses.|Sem dimensões|
|storage_percent|Sim|Porcentagem de espaço de dados usado|Porcentagem|Máximo|Porcentagem de espaço de dados usada. Não aplicável a data warehouses ou a bancos de dados de hiperescala.|Sem dimensões|
|tempdb_data_size|Sim|Quilobytes do Tamanho do Arquivo de Dados Tempdb|Contagem|Máximo|Tamanho do arquivo de dados tempdb em kilobytes. Não aplicável a data warehouses.|Sem dimensões|
|tempdb_log_size|Sim|Quilobytes do Tamanho do Arquivo de Log Tempdb|Contagem|Máximo|Tamanho do arquivo de log de tempdb em kilobytes. Não aplicável a data warehouses.|Sem dimensões|
|tempdb_log_used_percent|Sim|Porcentagem de Uso do Log Tempdb|Porcentagem|Máximo|Log de porcentagem de tempdb usado. Não aplicável a data warehouses.|Sem dimensões|
|wlg_active_queries|Sim|Consultas ativas do grupo de carga de trabalho|Contagem|Total|Consultas ativas dentro do grupo de cargas de trabalho. Aplica-se somente a data warehouses.|WorkloadGroupName, IsUserDefined|
|wlg_active_queries_timeouts|Sim|Tempos limite de consulta do grupo de carga de trabalho|Contagem|Total|Consultas que atingiram o tempo limite para o grupo de carga de trabalho. Aplica-se somente a data warehouses.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_system_percent|Sim|Percentual de alocação do grupo de carga de trabalho por sistema|Porcentagem|Máximo|Porcentagem alocada de recursos em relação a todo o sistema por grupo de carga de trabalho. Aplica-se somente a data warehouses.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_wlg_effective_cap_percent|Sim|Porcentagem de alocação do grupo de cargas de trabalho por limite|Porcentagem|Máximo|Porcentagem alocada de recursos em relação aos recursos de Cap especificados por grupo de carga de trabalho. Aplica-se somente a data warehouses.|WorkloadGroupName, IsUserDefined|
|wlg_effective_cap_resource_percent|Sim|Percentual de limite efetivo de recursos|Porcentagem|Máximo|Um limite rígido na porcentagem de recursos permitidos para o grupo de cargas de trabalho, levando em conta o percentual de recursos mínimo efetivo alocado para outros grupos de carga de trabalho. Aplica-se somente a data warehouses.|WorkloadGroupName, IsUserDefined|
|wlg_effective_min_resource_percent|Sim|Percentual mínimo efetivo de recursos|Porcentagem|Máximo|Percentual mínimo de recursos reservados e isolados para o grupo de carga de trabalho, levando em conta o nível de serviço mínimo. Aplica-se somente a data warehouses.|WorkloadGroupName, IsUserDefined|
|wlg_queued_queries|Sim|Consultas na fila do grupo de carga de trabalho|Contagem|Total|Consultas em fila no grupo de cargas de trabalho. Aplica-se somente a data warehouses.|WorkloadGroupName, IsUserDefined|
|workers_percent|Sim|Porcentagem de funcionários|Porcentagem|Média|Porcentagem de trabalhadores. Não aplicável a data warehouses.|Sem dimensões|
|xtp_storage_percent|Sim|Percentual de armazenamento do OLTP na memória|Porcentagem|Média|Porcentagem de armazenamento OLTP na memória. Não aplicável a data warehouses.|Sem dimensões|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|allocated_data_storage|Sim|Espaço alocado de dados|Bytes|Média|Espaço alocado de dados|Sem dimensões|
|allocated_data_storage_percent|Sim|Porcentagem do espaço de dados alocado|Porcentagem|Máximo|Porcentagem do espaço de dados alocado|Sem dimensões|
|cpu_limit|Sim|Limite da CPU|Contagem|Média|Limite de CPU. Aplica-se a pools elásticos baseados em vCore.|Sem dimensões|
|cpu_percent|Sim|Percentual de CPU|Porcentagem|Média|Percentual de CPU|Sem dimensões|
|cpu_used|Sim|CPU usada|Contagem|Média|CPU usada. Aplica-se a pools elásticos baseados em vCore.|Sem dimensões|
|database_allocated_data_storage|Não|Espaço alocado de dados|Bytes|Média|Espaço alocado de dados|DatabaseResourceId|
|database_cpu_limit|Não|Limite da CPU|Contagem|Média|Limite da CPU|DatabaseResourceId|
|database_cpu_percent|Não|Percentual de CPU|Porcentagem|Média|Percentual de CPU|DatabaseResourceId|
|database_cpu_used|Não|CPU usada|Contagem|Média|CPU usada|DatabaseResourceId|
|database_dtu_consumption_percent|Não|Porcentagem de DTU|Porcentagem|Média|Porcentagem de DTU|DatabaseResourceId|
|database_eDTU_used|Não|eDTU usado|Contagem|Média|eDTU usado|DatabaseResourceId|
|database_log_write_percent|Não|Porcentagem de E/S de log|Porcentagem|Média|Porcentagem de E/S de log|DatabaseResourceId|
|database_physical_data_read_percent|Não|Porcentagem de E/S de dados|Porcentagem|Média|Porcentagem de E/S de dados|DatabaseResourceId|
|database_sessions_percent|Não|Porcentagem de sessões|Porcentagem|Média|Porcentagem de sessões|DatabaseResourceId|
|database_storage_used|Não|Espaço de dados usado|Bytes|Média|Espaço de dados usado|DatabaseResourceId|
|database_workers_percent|Não|Porcentagem de funcionários|Porcentagem|Média|Porcentagem de funcionários|DatabaseResourceId|
|dtu_consumption_percent|Sim|Porcentagem de DTU|Porcentagem|Média|Porcentagem de DTU. Aplica-se a pools elásticos baseados em DTU.|Sem dimensões|
|eDTU_limit|Sim|Limite de eDTU|Contagem|Média|limite de eDTU. Aplica-se a pools elásticos baseados em DTU.|Sem dimensões|
|eDTU_used|Sim|eDTU usado|Contagem|Média|eDTU usado. Aplica-se a pools elásticos baseados em DTU.|Sem dimensões|
|log_write_percent|Sim|Porcentagem de E/S de log|Porcentagem|Média|Porcentagem de E/S de log|Sem dimensões|
|physical_data_read_percent|Sim|Porcentagem de E/S de dados|Porcentagem|Média|Porcentagem de E/S de dados|Sem dimensões|
|sessions_percent|Sim|Porcentagem de sessões|Porcentagem|Média|Porcentagem de sessões|Sem dimensões|
|sqlserver_process_core_percent|Sim|Porcentagem de núcleo do processo de SQL Server|Porcentagem|Máximo|Uso da CPU como uma porcentagem do processo do BD SQL. Aplica-se a pools elásticos.|Sem dimensões|
|sqlserver_process_memory_percent|Sim|Porcentagem de memória de processo SQL Server|Porcentagem|Máximo|Uso de memória como uma porcentagem do processo do BD SQL. Aplica-se a pools elásticos.|Sem dimensões|
|storage_limit|Sim|Tamanho máximo dos dados|Bytes|Média|Tamanho máximo dos dados|Sem dimensões|
|storage_percent|Sim|Porcentagem de espaço de dados usado|Porcentagem|Média|Porcentagem de espaço de dados usado|Sem dimensões|
|storage_used|Sim|Espaço de dados usado|Bytes|Média|Espaço de dados usado|Sem dimensões|
|tempdb_data_size|Sim|Quilobytes do Tamanho do Arquivo de Dados Tempdb|Contagem|Máximo|Quilobytes do Tamanho do Arquivo de Dados Tempdb|Sem dimensões|
|tempdb_log_size|Sim|Quilobytes do Tamanho do Arquivo de Log Tempdb|Contagem|Máximo|Quilobytes do Tamanho do Arquivo de Log Tempdb|Sem dimensões|
|tempdb_log_used_percent|Sim|Porcentagem de Uso do Log Tempdb|Porcentagem|Máximo|Porcentagem de Uso do Log Tempdb|Sem dimensões|
|workers_percent|Sim|Porcentagem de funcionários|Porcentagem|Média|Porcentagem de funcionários|Sem dimensões|
|xtp_storage_percent|Sim|Percentual de armazenamento do OLTP na memória|Porcentagem|Média|Percentual de armazenamento do OLTP na memória|Sem dimensões|


## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Sim|Disponibilidade|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|GeoType, ApiName, Authentication|
|Saída|Sim|Saída|Bytes|Total|A quantidade de dados de saída. Esse número inclui saída para cliente externo do armazenamento do Azure, bem como saída no Azure. Como resultado, esse número não reflete a saída faturável.|GeoType, ApiName, Authentication|
|Entrada|Sim|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Sim|Latência de E2E com Sucesso|Milissegundos|Média|A latência média de ponta a ponta das solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Sim|Latência de Servidor Com Sucesso|Milissegundos|Média|O tempo médio usado para processar uma solicitação bem-sucedida pelo Armazenamento do Azure. Esse valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType, ApiName, Authentication|
|Transactions|Sim|Transactions|Contagem|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|ResponseType, GeoType, ApiName, Authentication|
|UsedCapacity|Não|Capacidade utilizada|Bytes|Média|A quantidade de armazenamento utilizada pela conta de armazenamento. Para contas de armazenamento Standard, é a soma da capacidade usada por blob, tabela, arquivo e fila. Para contas de armazenamento Premium e contas de armazenamento de BLOBs, ele é o mesmo que capacidade ou filecapacity.|Sem dimensões|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Sim|Disponibilidade|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|GeoType, ApiName, Authentication|
|BlobCapacity|Não|Capacidade do Blob|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço Blob da conta de armazenamento em bytes.|BlobType, camada|
|BlobCount|Não|Contagem de Blobs|Contagem|Média|O número de objetos blob armazenados na conta de armazenamento.|BlobType, camada|
|BlobProvisionedSize|Não|Tamanho provisionado do blob|Bytes|Média|A quantidade de armazenamento provisionada no serviço BLOB da conta de armazenamento, em bytes.|BlobType, camada|
|ContainerCount|Sim|Contagem de Contêineres de Blobs|Contagem|Média|O número de contêineres na conta de armazenamento.|Sem dimensões|
|Saída|Sim|Saída|Bytes|Total|A quantidade de dados de saída. Esse número inclui saída para cliente externo do armazenamento do Azure, bem como saída no Azure. Como resultado, esse número não reflete a saída faturável.|GeoType, ApiName, Authentication|
|IndexCapacity|Não|Capacidade de Índice|Bytes|Média|A quantidade de armazenamento usada pelo Azure Data Lake Storage Gen2 índice hierárquico.|Sem dimensões|
|Entrada|Sim|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Sim|Latência de E2E com Sucesso|Milissegundos|Média|A latência média de ponta a ponta das solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Sim|Latência de Servidor Com Sucesso|Milissegundos|Média|O tempo médio usado para processar uma solicitação bem-sucedida pelo Armazenamento do Azure. Esse valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType, ApiName, Authentication|
|Transactions|Sim|Transactions|Contagem|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Sim|Disponibilidade|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|Geotipo, ApiName, autenticação, FileShare|
|Saída|Sim|Saída|Bytes|Total|A quantidade de dados de saída. Esse número inclui saída para cliente externo do armazenamento do Azure, bem como saída no Azure. Como resultado, esse número não reflete a saída faturável.|Geotipo, ApiName, autenticação, FileShare|
|FileCapacity|Não|Capacidade do Arquivo|Bytes|Média|A quantidade de armazenamento de Arquivos usada pela conta de armazenamento.|FileShare|
|FileCount|Não|Contagem de Arquivos|Contagem|Média|O número de arquivos na conta de armazenamento.|FileShare|
|FileShareCapacityQuota|Não|Cota de capacidade de compartilhamento de arquivos|Bytes|Média|O limite superior da quantidade de armazenamento que pode ser usada pelo serviço de arquivos do Azure em bytes.|FileShare|
|FileShareCount|Não|Contagem de Compartilhamentos de Arquivos|Contagem|Média|O número de compartilhamentos de arquivos na conta de armazenamento.|Sem dimensões|
|FileShareProvisionedIOPS|Não|IOPS provisionado de compartilhamento de arquivos|Bytes|Média|O número da linha de base de IOPS provisionados para o compartilhamento de arquivos Premium na conta de armazenamento de arquivos premium. Esse número é calculado com base no tamanho provisionado (cota) da capacidade de compartilhamento.|FileShare|
|FileShareSnapshotCount|Não|Contagem de Instantâneos do Compartilhamento de Arquivo|Contagem|Média|O número de instantâneos presentes no serviço compartilhamento de arquivos da conta de armazenamento.|FileShare|
|FileShareSnapshotSize|Não|Tamanho do Instantâneo do Compartilhamento de Arquivo|Bytes|Média|A quantidade de armazenamento usada pelos instantâneos no serviço de arquivo da conta de armazenamento, em bytes.|FileShare|
|Entrada|Sim|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|Geotipo, ApiName, autenticação, FileShare|
|SuccessE2ELatency|Sim|Latência de E2E com Sucesso|Milissegundos|Média|A latência média de ponta a ponta das solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|Geotipo, ApiName, autenticação, FileShare|
|SuccessServerLatency|Sim|Latência de Servidor Com Sucesso|Milissegundos|Média|O tempo médio usado para processar uma solicitação bem-sucedida pelo Armazenamento do Azure. Esse valor não inclui a latência de rede especificada em SuccessE2ELatency.|Geotipo, ApiName, autenticação, FileShare|
|Transactions|Sim|Transactions|Contagem|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|ResponseType, GeoType, ApiName, autenticação, FileShare|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Sim|Disponibilidade|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|GeoType, ApiName, Authentication|
|Saída|Sim|Saída|Bytes|Total|A quantidade de dados de saída. Esse número inclui saída para cliente externo do armazenamento do Azure, bem como saída no Azure. Como resultado, esse número não reflete a saída faturável.|GeoType, ApiName, Authentication|
|Entrada|Sim|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|GeoType, ApiName, Authentication|
|QueueCapacity|Sim|Capacidade da Fila|Bytes|Média|A quantidade de armazenamento de fila usada pela conta de armazenamento.|Sem dimensões|
|QueueCount|Sim|Contagem de Filas|Contagem|Média|O número de filas em uma conta de armazenamento.|Sem dimensões|
|QueueMessageCount|Sim|Contagem de Mensagens da Fila|Contagem|Média|O número de mensagens da fila não expiradas na conta de armazenamento.|Sem dimensões|
|SuccessE2ELatency|Sim|Latência de E2E com Sucesso|Milissegundos|Média|A latência média de ponta a ponta das solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Sim|Latência de Servidor Com Sucesso|Milissegundos|Média|O tempo médio usado para processar uma solicitação bem-sucedida pelo Armazenamento do Azure. Esse valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType, ApiName, Authentication|
|Transactions|Sim|Transactions|Contagem|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Sim|Disponibilidade|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|GeoType, ApiName, Authentication|
|Saída|Sim|Saída|Bytes|Total|A quantidade de dados de saída. Esse número inclui saída para cliente externo do armazenamento do Azure, bem como saída no Azure. Como resultado, esse número não reflete a saída faturável.|GeoType, ApiName, Authentication|
|Entrada|Sim|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Sim|Latência de E2E com Sucesso|Milissegundos|Média|A latência média de ponta a ponta das solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Sim|Latência de Servidor Com Sucesso|Milissegundos|Média|O tempo médio usado para processar uma solicitação bem-sucedida pelo Armazenamento do Azure. Esse valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType, ApiName, Authentication|
|TableCapacity|Sim|Capacidade da Tabela|Bytes|Média|A quantidade de armazenamento de Tabelas usada pela conta de armazenamento.|Sem dimensões|
|TableCount|Sim|Contagem de Tabelas|Contagem|Média|O número de tabelas em uma conta de armazenamento.|Sem dimensões|
|TableEntityCount|Sim|Contagem de Entidades de Tabela|Contagem|Média|O número de entidades de tabela na conta de armazenamento.|Sem dimensões|
|Transactions|Sim|Transactions|Contagem|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|ServerSyncSessionResult|Sim|Resultado da Sessão de Sincronização|Contagem|Média|Métrica que registra um valor de 1 cada vez que o ponto de extremidade do servidor conclui com êxito uma sessão de sincronização com o ponto de extremidade de nuvem|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Sim|Bytes sincronizados|Bytes|Total|Tamanho total do arquivo transferido para sessões de sincronização|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncRecalledNetworkBytesByApplication|Sim|Tamanho de recall da camada de nuvem por aplicativo|Bytes|Total|Tamanho dos dados recuperados pelo aplicativo|SyncGroupName, servername, ApplicationName|
|StorageSyncRecalledTotalNetworkBytes|Sim|Tamanho de recall da camada de nuvem|Bytes|Total|Tamanho dos dados em recall|SyncGroupName, servername|
|StorageSyncRecallIOTotalSizeBytes|Sim|Recall da camada de nuvem|Bytes|Total|Tamanho total dos dados recuperados pelo servidor|ServerName|
|StorageSyncRecallThroughputBytesPerSecond|Sim|Taxa de transferência de recall da camada de nuvem|BytesPerSecond|Média|Tamanho da taxa de transferência de recall de dados|SyncGroupName, servername|
|StorageSyncServerHeartbeat|Sim|Status Online do Servidor|Contagem|Máximo|Métrica que registra um valor de 1 cada vez que o servidor resigtered registra com êxito uma pulsação com o ponto de extremidade de nuvem|ServerName|
|StorageSyncSyncSessionAppliedFilesCount|Sim|Arquivos Sincronizados|Contagem|Total|Contagem de arquivos sincronizados|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Sim|Arquivos não sincronizando|Contagem|Total|Falha na sincronização da contagem de arquivos|SyncGroupName, ServerEndpointName, SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>microsoft.storagesync/storageSyncServices/registeredServers

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|ServerHeartbeat|Sim|Status Online do Servidor|Contagem|Máximo|Métrica que registra um valor de 1 cada vez que o servidor resigtered registra com êxito uma pulsação com o ponto de extremidade de nuvem|ServerResourceId, servername|
|ServerRecallIOTotalSizeBytes|Sim|Recall da camada de nuvem|Bytes|Total|Tamanho total dos dados recuperados pelo servidor|ServerResourceId, servername|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>microsoft.storagesync/storageSyncServices/syncGroups

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|SyncGroupBatchTransferredFileBytes|Sim|Bytes sincronizados|Bytes|Total|Tamanho total do arquivo transferido para sessões de sincronização|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupSyncSessionAppliedFilesCount|Sim|Arquivos Sincronizados|Contagem|Total|Contagem de arquivos sincronizados|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|Sim|Arquivos não sincronizando|Contagem|Total|Falha na sincronização da contagem de arquivos|SyncGroupName, ServerEndpointName, SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|ServerEndpointBatchTransferredFileBytes|Sim|Bytes sincronizados|Bytes|Total|Tamanho total do arquivo transferido para sessões de sincronização|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionAppliedFilesCount|Sim|Arquivos Sincronizados|Contagem|Total|Contagem de arquivos sincronizados|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|Sim|Arquivos não sincronizando|Contagem|Total|Falha na sincronização da contagem de arquivos|ServerEndpointName, SyncDirection|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|AMLCalloutFailedRequests|Sim|Solicitações de função com falha|Contagem|Total|Solicitações de função com falha|LogicalName, PartitionID|
|AMLCalloutInputEvents|Sim|Eventos de função|Contagem|Total|Eventos de função|LogicalName, PartitionID|
|AMLCalloutRequests|Sim|Solicitações de função|Contagem|Total|Solicitações de função|LogicalName, PartitionID|
|ConversionErrors|Sim|Erros de conversão de dados|Contagem|Total|Erros de conversão de dados|LogicalName, PartitionID|
|DeserializationError|Sim|Erros de desserialização de entrada|Contagem|Total|Erros de desserialização de entrada|LogicalName, PartitionID|
|DroppedOrAdjustedEvents|Sim|Eventos fora de ordem|Contagem|Total|Eventos fora de ordem|LogicalName, PartitionID|
|EarlyInputEvents|Sim|Eventos de Entrada Antecipados|Contagem|Total|Eventos de Entrada Antecipados|LogicalName, PartitionID|
|Erros|Sim|Erros de runtime|Contagem|Total|Erros de runtime|LogicalName, PartitionID|
|InputEventBytes|Sim|Bytes de evento de entrada|Bytes|Total|Bytes de evento de entrada|LogicalName, PartitionID|
|InputEvents|Sim|Eventos de entrada|Contagem|Total|Eventos de entrada|LogicalName, PartitionID|
|InputEventsSourcesBacklogged|Sim|Eventos de Entrada Acumulados|Contagem|Máximo|Eventos de Entrada Acumulados|LogicalName, PartitionID|
|InputEventsSourcesPerSecond|Sim|Fontes de Entrada Recebidas|Contagem|Total|Fontes de Entrada Recebidas|LogicalName, PartitionID|
|LateInputEvents|Sim|Eventos de entrada atrasados|Contagem|Total|Eventos de entrada atrasados|LogicalName, PartitionID|
|OutputEvents|Sim|Eventos de saída|Contagem|Total|Eventos de saída|LogicalName, PartitionID|
|OutputWatermarkDelaySeconds|Sim|Atraso de Marca-d'água|Segundos|Máximo|Atraso de Marca-d'água|LogicalName, PartitionID|
|ResourceUtilization|Sim|% de utilização do SU|Porcentagem|Máximo|% de utilização do SU|LogicalName, PartitionID|


## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse/workspaces

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|OrchestrationActivityRunsEnded|Não|Execuções de atividades encerradas|Contagem|Total|Contagem de atividades de orquestração que tiveram êxito, falharam ou foram canceladas|Resultado, FailureType, atividade, ActivityType, pipeline|
|OrchestrationPipelineRunsEnded|Não|Execuções de pipeline finalizadas|Contagem|Total|Contagem de execuções do pipeline de orquestração que tiveram êxito, falharam ou foram canceladas|Resultado, FailureType, pipeline|
|OrchestrationTriggersEnded|Não|Gatilhos encerrados|Contagem|Total|Contagem de gatilhos de orquestração que foram bem-sucedidos, com falha ou foram cancelados|Resultado, FailureType, gatilho|
|SQLOnDemandLoginAttempts|Não|Tentativas de logon|Contagem|Total|Contagem de tentativas de logon que êxito na ou com falha|Result|
|SQLOnDemandQueriesEnded|Não|Consultas encerradas|Contagem|Total|Contagem de consultas que tiveram êxito, falharam ou foram canceladas|Result|
|SQLOnDemandQueryProcessedBytes|Não|Dados processados|Bytes|Total|Quantidade de dados processados por consultas|Sem dimensões|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft. Synapse/Workspaces/bigDataPools

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|CoresCapacity|Não|Capacidade de núcleos|Contagem|Máximo|Capacidade de núcleos|Sem dimensões|
|MemoryCapacityGB|Não|Capacidade de memória (GB)|Contagem|Máximo|Capacidade de memória (GB)|Sem dimensões|
|SparkJobsEnded|Sim|Aplicativos finalizados|Contagem|Total|Contagem de aplicativos finalizados|JobType, JobResult|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft. Synapse/espaços de trabalho/sqlpools

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|AdaptiveCacheHitPercent|Não|Percentual de impacto de cache adaptável|Porcentagem|Máximo|Mede o quão bem as cargas de trabalho estão utilizando o cache adaptável. Use essa métrica com a métrica percentual de impacto de cache para determinar se deve ser dimensionada para capacidade adicional ou executar novamente as cargas de trabalho para hidratar o cache|Sem dimensões|
|AdaptiveCacheUsedPercent|Não|Percentual de cache adaptável usado|Porcentagem|Máximo|Mede o quão bem as cargas de trabalho estão utilizando o cache adaptável. Use essa métrica com a métrica percentual usada do cache para determinar se deve ser dimensionada para capacidade adicional ou executar novamente as cargas de trabalho para hidratar o cache|Sem dimensões|
|conexões|Sim|conexões|Contagem|Total|Contagem de logons totais para o pool do SQL|Result|
|ConnectionsBlockedByFirewall|Não|Conexões bloqueadas pelo firewall|Contagem|Total|Contagem de conexões bloqueadas por regras de firewall. Reveja as políticas de controle de acesso para seu pool SQL e monitore essas conexões se a contagem for alta|Sem dimensões|
|DWULimit|Não|Limite de DWU|Contagem|Máximo|Objetivo de nível de serviço do pool do SQL|Sem dimensões|
|DWUUsed|Não|DWU usado|Contagem|Máximo|Representa uma representação de uso de alto nível no pool do SQL. Medido por limite de DWU * DWU percentual|Sem dimensões|
|DWUUsedPercent|Não|Percentual de DWU usado|Porcentagem|Máximo|Representa uma representação de uso de alto nível no pool do SQL. Medido com o máximo entre o percentual de CPU e a porcentagem de e/s de dados|Sem dimensões|
|LocalTempDBUsedPercent|Não|Percentual de uso do tempdb local|Porcentagem|Máximo|Utilização de tempdb local em todos os nós de computação-os valores são emitidos a cada cinco minutos|Sem dimensões|
|MemoryUsedPercent|Não|Porcentagem de utilização da memória|Porcentagem|Máximo|Utilização de memória em todos os nós no pool de SQL|Sem dimensões|
|wlg_effective_min_resource_percent|Sim|Percentual mínimo efetivo de recursos|Porcentagem|Mínimo|A configuração de porcentagem mínima de recursos efetiva permitida Considerando o nível de serviço e as configurações do grupo de carga de trabalho. Os min_percentage_resource efetivos podem ser ajustados em níveis mais baixos de serviço|IsUserDefined, de carga de trabalho|
|WLGActiveQueries|Não|Consultas ativas do grupo de carga de trabalho|Contagem|Total|As consultas ativas no grupo de cargas de trabalho. Usar essa métrica não filtrada e a divisão exibe todas as consultas ativas em execução no sistema|IsUserDefined, de carga de trabalho|
|WLGActiveQueriesTimeouts|Não|Tempos limite de consulta do grupo de carga de trabalho|Contagem|Total|Consultas para o grupo de cargas de trabalho que atingiram o tempo limite. Os tempos limite de consulta relatados por essa métrica são apenas uma vez que a consulta começou a ser executada (ela não inclui o tempo de espera devido a esperas de bloqueio ou de recursos)|IsUserDefined, de carga de trabalho|
|WLGAllocationByMaxResourcePercent|Não|Alocação do grupo de carga de trabalho por porcentagem máxima de recursos|Porcentagem|Máximo|Exibe a porcentagem de alocação de recursos em relação à porcentagem de recursos de limite efetivo por grupo de carga de trabalho. Essa métrica fornece a utilização efetiva do grupo de cargas de trabalho|IsUserDefined, de carga de trabalho|
|WLGAllocationBySystemPercent|Não|Percentual de alocação do grupo de carga de trabalho por sistema|Porcentagem|Máximo|A alocação percentual de recursos em relação a todo o sistema|IsUserDefined, de carga de trabalho|
|WLGEffectiveCapResourcePercent|Sim|Percentual de limite efetivo de recursos|Porcentagem|Máximo|A porcentagem do recurso Cap efetivo para o grupo de carga de trabalho. Se houver outros grupos de cargas de trabalho com min_percentage_resource > 0, o effective_cap_percentage_resource será reduzido proporcionalmente|IsUserDefined, de carga de trabalho|
|WLGQueuedQueries|Não|Consultas na fila do grupo de carga de trabalho|Contagem|Total|Contagem cumulativa de solicitações enfileiradas após o limite de simultaneidade máxima ter sido atingido|IsUserDefined, de carga de trabalho|


## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|Sim|Bytes de entrada recebidos|Bytes|Total|Contagem de bytes lidos de todas as fontes de evento|Sem dimensões|
|IngressReceivedInvalidMessages|Sim|Mensagens de entrada inválidas recebidas|Contagem|Total|Contagem de mensagens inválidas lidas de todos os Hubs de Eventos ou origens de evento do Hub IoT|Sem dimensões|
|IngressReceivedMessages|Sim|Mensagens de entrada recebidas|Contagem|Total|Contagem de mensagens lidas de todos os Hubs de Eventos ou origens de evento do Hub IoT|Sem dimensões|
|IngressReceivedMessagesCountLag|Sim|Atraso na contagem das mensagens de entrada recebidas|Contagem|Média|Diferença entre o número de sequência da última mensagem enfileirada na partição de origem do evento e o número de sequência de mensagens sendo processadas na entrada|Sem dimensões|
|IngressReceivedMessagesTimeLag|Sim|Tempo de atraso das mensagens de entrada recebidas|Segundos|Máximo|Diferença entre a hora em que a mensagem é enfileirada na origem do evento e a hora em que ela é processada na entrada|Sem dimensões|
|IngressStoredBytes|Sim|Bytes de entrada armazenados|Bytes|Total|Tamanho total de eventos processados com êxito e disponíveis para consulta|Sem dimensões|
|IngressStoredEvents|Sim|Eventos de entrada armazenados|Contagem|Total|Contagem de eventos nivelados processados com êxito e disponíveis para consulta|Sem dimensões|
|WarmStorageMaxProperties|Sim|Propriedades máximas de armazenamento quente|Contagem|Máximo|Número máximo de propriedades usadas permitidas pelo ambiente para SKU S1/S2 e número máximo de propriedades permitidas pelo armazenamento quente para o SKU PAYG|Sem dimensões|
|WarmStorageUsedProperties|Sim|Propriedades usadas de armazenamento quente |Contagem|Máximo|Número de propriedades usadas pelo ambiente para SKU S1/S2 e número de propriedades usadas pelo armazenamento quente para SKU PAYG|Sem dimensões|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|Sim|Bytes de entrada recebidos|Bytes|Total|Contagem de bytes lidos da fonte de evento|Sem dimensões|
|IngressReceivedInvalidMessages|Sim|Mensagens de entrada inválidas recebidas|Contagem|Total|Contagem de mensagens inválidas lidas na origem do evento|Sem dimensões|
|IngressReceivedMessages|Sim|Mensagens de entrada recebidas|Contagem|Total|Contagem de mensagens lidas na origem do evento|Sem dimensões|
|IngressReceivedMessagesCountLag|Sim|Atraso na contagem das mensagens de entrada recebidas|Contagem|Média|Diferença entre o número de sequência da última mensagem enfileirada na partição de origem do evento e o número de sequência de mensagens sendo processadas na entrada|Sem dimensões|
|IngressReceivedMessagesTimeLag|Sim|Tempo de atraso das mensagens de entrada recebidas|Segundos|Máximo|Diferença entre a hora em que a mensagem é enfileirada na origem do evento e a hora em que ela é processada na entrada|Sem dimensões|
|IngressStoredBytes|Sim|Bytes de entrada armazenados|Bytes|Total|Tamanho total de eventos processados com êxito e disponíveis para consulta|Sem dimensões|
|IngressStoredEvents|Sim|Eventos de entrada armazenados|Contagem|Total|Contagem de eventos nivelados processados com êxito e disponíveis para consulta|Sem dimensões|
|WarmStorageMaxProperties|Sim|Propriedades máximas de armazenamento quente|Contagem|Máximo|Número máximo de propriedades usadas permitidas pelo ambiente para SKU S1/S2 e número máximo de propriedades permitidas pelo armazenamento quente para o SKU PAYG|Sem dimensões|
|WarmStorageUsedProperties|Sim|Propriedades usadas de armazenamento quente |Contagem|Máximo|Número de propriedades usadas pelo ambiente para SKU S1/S2 e número de propriedades usadas pelo armazenamento quente para SKU PAYG|Sem dimensões|


## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|Bytes de leitura de disco|Sim|Bytes de leitura de disco|Bytes|Total|Taxa de transferência total do disco devido a operações de leitura no período de exemplo.|Sem dimensões|
|Operações de leitura de disco/Seg|Sim|Operações de leitura de disco/Seg|CountPerSecond|Média|O número médio de operações de leitura de e/s no período de exemplo anterior. Observe que essas operações podem ser de tamanho variável.|Sem dimensões|
|Bytes de gravação de disco|Sim|Bytes de gravação de disco|Bytes|Total|Taxa de transferência total do disco devido a operações de gravação no período de exemplo.|Sem dimensões|
|Operações de gravação de disco/Seg|Sim|Operações de gravação de disco/Seg|CountPerSecond|Média|O número médio de operações de gravação de e/s no período de exemplo anterior. Observe que essas operações podem ser de tamanho variável.|Sem dimensões|
|DiskReadBytesPerSecond|Sim|Disk Read Bytes/Sec|BytesPerSecond|Média|Taxa de transferência média do disco devido a operações de leitura no período de exemplo.|Sem dimensões|
|DiskReadLatency|Sim|Latência de leitura de disco|Milissegundos|Média|Latência de leitura total. A soma das latências de leitura do dispositivo e do kernel.|Sem dimensões|
|DiskReadOperations|Sim|Operações de Leitura de Disco|Contagem|Total|O número de operações de leitura de e/s no período de exemplo anterior. Observe que essas operações podem ser de tamanho variável.|Sem dimensões|
|DiskWriteBytesPerSecond|Sim|Disk Write Bytes/Sec|BytesPerSecond|Média|Taxa de transferência média do disco devido a operações de gravação no período de exemplo.|Sem dimensões|
|DiskWriteLatency|Sim|Latência de Gravação de disco|Milissegundos|Média|Latência de gravação total. A soma das latências de gravação do kernel e do dispositivo.|Sem dimensões|
|DiskWriteOperations|Sim|Operações de Gravação em Disco|Contagem|Total|O número de operações de gravação de e/s no período de exemplo anterior. Observe que essas operações podem ser de tamanho variável.|Sem dimensões|
|MemoryActive|Sim|Memória Ativa|Bytes|Média|A quantidade de memória usada pela VM na última pequena janela de tempo. Esse é o número "verdadeiro" da quantidade de memória necessária para a VM no momento. A memória adicional não utilizada pode ser trocada ou por balão sem impacto no desempenho do convidado.|Sem dimensões|
|MemoryGranted|Sim|Memória Concedida|Bytes|Média|A quantidade de memória que foi concedida à VM pelo host. A memória não é concedida ao host até que ele seja tocado uma vez e a memória concedida possa ser trocada ou bloqueada se o VMkernel precisar da memória.|Sem dimensões|
|MemoryUsed|Sim|Memória Usada|Bytes|Média|A quantidade de memória da máquina que está sendo usada pela VM.|Sem dimensões|
|Entrada na rede|Sim|Entrada na rede|Bytes|Total|Taxa de transferência total de rede para o tráfego recebido.|Sem dimensões|
|Saída da rede|Sim|Saída da rede|Bytes|Total|Taxa de transferência total de rede para o tráfego transmitido.|Sem dimensões|
|NetworkInBytesPerSecond|Sim|Bytes/s de Entrada de Rede|BytesPerSecond|Média|Taxa de transferência média de rede para o tráfego recebido.|Sem dimensões|
|NetworkOutBytesPerSecond|Sim|Bytes/s de Saída de Rede|BytesPerSecond|Média|Taxa de transferência média de rede para o tráfego transmitido.|Sem dimensões|
|Percentual de CPU|Sim|Porcentagem de CPU|Porcentagem|Média|A utilização da CPU. Esse valor é relatado com 100% que representa todos os núcleos de processador no sistema. Por exemplo, uma VM de 2 vias usando 50% de um sistema de quatro núcleos está usando completamente dois núcleos.|Sem dimensões|
|PercentageCpuReady|Sim|Percentual de CPU Pronta|Milissegundos|Total|O tempo de preparação é o tempo gasto aguardando que as CPUs fiquem disponíveis no intervalo de atualização anterior.|Sem dimensões|


## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|ActiveRequests|Sim|Solicitações ativas|Contagem|Total|Solicitações ativas|Instância|
|AverageResponseTime|Sim|Tempo Médio de Resposta|Segundos|Média|Tempo Médio de Resposta|Instância|
|BytesReceived|Sim|Entrada de Dados|Bytes|Total|Entrada de Dados|Instância|
|BytesSent|Sim|Saída de dados|Bytes|Total|Saída de dados|Instância|
|CpuPercentage|Sim|Percentual de CPU|Porcentagem|Média|Percentual de CPU|Instância|
|DiskQueueLength|Sim|Tamanho da fila do disco|Contagem|Média|Tamanho da fila do disco|Instância|
|Http101|Sim|Http 101|Contagem|Total|Http 101|Instância|
|Http2xx|Sim|Http 2xx|Contagem|Total|Http 2xx|Instância|
|Http3xx|Sim|Http 3xx|Contagem|Total|Http 3xx|Instância|
|Http401|Sim|Http 401|Contagem|Total|Http 401|Instância|
|Http403|Sim|Http 403|Contagem|Total|Http 403|Instância|
|Http404|Sim|Http 404|Contagem|Total|Http 404|Instância|
|Http406|Sim|Http 406|Contagem|Total|Http 406|Instância|
|Http4xx|Sim|Http 4xx|Contagem|Total|Http 4xx|Instância|
|Http5xx|Sim|Erros do Servidor Http|Contagem|Total|Erros do Servidor Http|Instância|
|HttpQueueLength|Sim|Tamanho da Fila de Http|Contagem|Média|Tamanho da Fila de Http|Instância|
|LargeAppServicePlanInstances|Sim|Funções de trabalho grandes do Plano do Serviço de Aplicativo|Contagem|Média|Funções de trabalho grandes do Plano do Serviço de Aplicativo|Sem dimensões|
|MediumAppServicePlanInstances|Sim|Funções de trabalho médias do Plano do Serviço de Aplicativo|Contagem|Média|Funções de trabalho médias do Plano do Serviço de Aplicativo|Sem dimensões|
|MemoryPercentage|Sim|Porcentagem de Memória|Porcentagem|Média|Porcentagem de Memória|Instância|
|Requests|Sim|Requests|Contagem|Total|Requests|Instância|
|SmallAppServicePlanInstances|Sim|Funções de trabalho pequenas do Plano do Serviço de Aplicativo|Contagem|Média|Funções de trabalho pequenas do Plano do Serviço de Aplicativo|Sem dimensões|
|TotalFrontEnds|Sim|Total de front-ends|Contagem|Média|Total de front-ends|Sem dimensões|


## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|CpuPercentage|Sim|Percentual de CPU|Porcentagem|Média|Percentual de CPU|Instância|
|MemoryPercentage|Sim|Porcentagem de Memória|Porcentagem|Média|Porcentagem de Memória|Instância|
|WorkersAvailable|Sim|Funções de trabalho disponíveis|Contagem|Média|Funções de trabalho disponíveis|Sem dimensões|
|WorkersTotal|Sim|Total de funções de trabalho|Contagem|Média|Total de funções de trabalho|Sem dimensões|
|WorkersUsed|Sim|Funções de trabalho usadas|Contagem|Média|Funções de trabalho usadas|Sem dimensões|


## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|BytesReceived|Sim|Entrada de Dados|Bytes|Total|Entrada de Dados|Instância|
|BytesSent|Sim|Saída de dados|Bytes|Total|Saída de dados|Instância|
|CpuPercentage|Sim|Percentual de CPU|Porcentagem|Média|Percentual de CPU|Instância|
|DiskQueueLength|Sim|Tamanho da fila do disco|Contagem|Média|Tamanho da fila do disco|Instância|
|HttpQueueLength|Sim|Tamanho da Fila de Http|Contagem|Média|Tamanho da Fila de Http|Instância|
|MemoryPercentage|Sim|Porcentagem de Memória|Porcentagem|Média|Porcentagem de Memória|Instância|
|SocketInboundAll|Sim|SocketInboundAll|Contagem|Média|SocketInboundAll|Instância|
|SocketLoopback|Sim|SocketLoopback|Contagem|Média|SocketLoopback|Instância|
|SocketOutboundAll|Sim|SocketOutboundAll|Contagem|Média|SocketOutboundAll|Instância|
|SocketOutboundEstablished|Sim|SocketOutboundEstablished|Contagem|Média|SocketOutboundEstablished|Instância|
|SocketOutboundTimeWait|Sim|SocketOutboundTimeWait|Contagem|Média|SocketOutboundTimeWait|Instância|
|TcpCloseWait|Sim|Espera de Fechamento de TCP|Contagem|Média|Espera de Fechamento de TCP|Instância|
|TcpClosing|Sim|Fechamento de TCP|Contagem|Média|Fechamento de TCP|Instância|
|TcpEstablished|Sim|TCP Estabelecido|Contagem|Média|TCP Estabelecido|Instância|
|TcpFinWait1|Sim|Espera 1 de Fin de TCP|Contagem|Média|Espera 1 de Fin de TCP|Instância|
|TcpFinWait2|Sim|Espera 2 de Fin de TCP|Contagem|Média|Espera 2 de Fin de TCP|Instância|
|TcpLastAck|Sim|Última Confirmação de TCP|Contagem|Média|Última Confirmação de TCP|Instância|
|TcpSynReceived|Sim|Sincronização de TCP Recebida|Contagem|Média|Sincronização de TCP Recebida|Instância|
|TcpSynSent|Sim|Sincronização de TCP Enviada|Contagem|Média|Sincronização de TCP Enviada|Instância|
|TcpTimeWait|Sim|Espera de Tempo de TCP|Contagem|Média|Espera de Tempo de TCP|Instância|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (excluindo funções) 

> [!NOTE]
> O **uso do sistema de arquivos** é uma nova métrica sendo distribuída globalmente, nenhum dado é esperado, a menos que você tenha sido na lista de permissões para visualização privada.

> [!IMPORTANT]
> O **tempo médio de resposta** será preterido para evitar confusão com agregações de métricas. Use o **tempo de resposta** como uma substituição.

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|AppConnections|Sim|conexões|Contagem|Média|conexões|Instância|
|AverageMemoryWorkingSet|Sim|Conjunto de trabalho de memória média|Bytes|Média|Conjunto de trabalho de memória média|Instância|
|AverageResponseTime|Sim|Tempo médio **de resposta (preterido)**|Segundos|Média|Tempo Médio de Resposta|Instância|
|BytesReceived|Sim|Entrada de Dados|Bytes|Total|Entrada de Dados|Instância|
|BytesSent|Sim|Saída de dados|Bytes|Total|Saída de dados|Instância|
|CpuTime|Sim|Tempo de CPU|Segundos|Total|Tempo de CPU|Instância|
|CurrentAssemblies|Sim|Assemblies Atuais|Contagem|Média|Assemblies Atuais|Instância|
|FileSystemUsage|Sim|Uso do sistema de arquivos|Bytes|Média|Uso do sistema de arquivos|Sem dimensões|
|Gen0Collections|Sim|Coletas de lixo da Ger 0|Contagem|Total|Coletas de lixo da Ger 0|Instância|
|Gen1Collections|Sim|Coletas de lixo da Ger 1|Contagem|Total|Coletas de lixo da Ger 1|Instância|
|Gen2Collections|Sim|Coletas de lixo da Ger 2|Contagem|Total|Coletas de lixo da Ger 2|Instância|
|Alças|Sim|Núm. de Identificadores|Contagem|Média|Núm. de Identificadores|Instância|
|HealthCheckStatus|Sim|Status da verificação de integridade|Contagem|Média|Status da verificação de integridade|Instância|
|Http101|Sim|Http 101|Contagem|Total|Http 101|Instância|
|Http2xx|Sim|Http 2xx|Contagem|Total|Http 2xx|Instância|
|Http3xx|Http 3xx|Contagem|Total|Http 3xx|Instância|
|Http401|Sim|Http 401|Contagem|Total|Http 401|Instância|
|Http403|Sim|Http 403|Contagem|Total|Http 403|Instância|
|Http404|Sim|Http 404|Contagem|Total|Http 404|Instância|
|Http406|Sim|Http 406|Contagem|Total|Http 406|Instância|
|Http4xx|Sim|Http 4xx|Contagem|Total|Http 4xx|Instância|
|Http5xx|Sim|Erros do Servidor Http|Contagem|Total|Erros do Servidor Http|Instância|
|HttpResponseTime|Sim|Tempo de Resposta|Segundos|Média|Tempo de Resposta|Instância|
|IoOtherBytesPerSecond|Sim|E/S de outros bytes por segundo|BytesPerSecond|Total|E/S de outros bytes por segundo|Instância|
|IoOtherOperationsPerSecond|Sim|E/S de outras operações por segundo|BytesPerSecond|Total|E/S de outras operações por segundo|Instância|
|IoReadBytesPerSecond|Sim|E/S de bytes de leitura por segundo|BytesPerSecond|Total|E/S de bytes de leitura por segundo|Instância|
|IoReadOperationsPerSecond|Sim|E/S de operações de leitura por segundo|BytesPerSecond|Total|E/S de operações de leitura por segundo|Instância|
|IoWriteBytesPerSecond|Sim|E/S de bytes de gravação por segundo|BytesPerSecond|Total|E/S de bytes de gravação por segundo|Instância|
|IoWriteOperationsPerSecond|Sim|E/S de operações de gravação por segundo|BytesPerSecond|Total|E/S de operações de gravação por segundo|Instância|
|MemoryWorkingSet|Sim|Conjunto de trabalho de memória|Bytes|Média|Conjunto de trabalho de memória|Instância|
|PrivateBytes|Sim|Bytes Particulares|Bytes|Média|Bytes Particulares|Instância|
|Requests|Sim|Requests|Contagem|Total|Requests|Instância|
|RequestsInApplicationQueue|Sim|Solicitações na fila do aplicativo|Contagem|Média|Solicitações na fila do aplicativo|Instância|
|Threads|Sim|Contagem de Threads|Contagem|Média|Contagem de Threads|Instância|
|TotalAppDomains|Sim|Total de domínios de aplicativo|Contagem|Média|Total de domínios de aplicativo|Instância|
|TotalAppDomainsUnloaded|Sim|Total de domínios de aplicativo descarregados|Contagem|Média|Total de domínios de aplicativo descarregados|Instância|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (funções)

> [!NOTE]
> O **uso do sistema de arquivos** é uma nova métrica sendo distribuída globalmente, nenhum dado é esperado, a menos que você tenha sido na lista de permissões para visualização privada.

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|AverageMemoryWorkingSet|Sim|Conjunto de trabalho de memória média|Bytes|Média|Conjunto de trabalho de memória média|Instância|
|BytesReceived|Sim|Entrada de Dados|Bytes|Total|Entrada de Dados|Instância|
|BytesSent|Sim|Saída de dados|Bytes|Total|Saída de dados|Instância|
|CurrentAssemblies|Sim|Assemblies Atuais|Contagem|Média|Assemblies Atuais|Instância|
|FileSystemUsage|Sim|Uso do sistema de arquivos|Bytes|Média|Uso do sistema de arquivos|Sem dimensões|
|FunctionExecutionCount|Sim|Contagem de Execução de Função|Contagem|Total|Contagem de Execução de Função|Instância|
|FunctionExecutionUnits|Sim|Unidades de Execução de Função|Contagem|Total|[Unidades de Execução de Função](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Instância|
|Gen0Collections|Sim|Coletas de lixo da Ger 0|Contagem|Total|Coletas de lixo da Ger 0|Instância|
|Gen1Collections|Sim|Coletas de lixo da Ger 1|Contagem|Total|Coletas de lixo da Ger 1|Instância|
|Gen2Collections|Sim|Coletas de lixo da Ger 2|Contagem|Total|Coletas de lixo da Ger 2|Instância|
|HealthCheckStatus|Sim|Status da verificação de integridade|Contagem|Média|Status da verificação de integridade|Instância|
|Http5xx|Sim|Erros do Servidor Http|Contagem|Total|Erros do Servidor Http|Instância|
|IoOtherBytesPerSecond|Sim|E/S de outros bytes por segundo|BytesPerSecond|Total|E/S de outros bytes por segundo|Instância|
|IoOtherOperationsPerSecond|Sim|E/S de outras operações por segundo|BytesPerSecond|Total|E/S de outras operações por segundo|Instância|
|IoReadBytesPerSecond|Sim|E/S de bytes de leitura por segundo|BytesPerSecond|Total|E/S de bytes de leitura por segundo|Instância|
|IoReadOperationsPerSecond|Sim|E/S de operações de leitura por segundo|BytesPerSecond|Total|E/S de operações de leitura por segundo|Instância|
|IoWriteBytesPerSecond|Sim|E/S de bytes de gravação por segundo|BytesPerSecond|Total|E/S de bytes de gravação por segundo|Instância|
|IoWriteOperationsPerSecond|Sim|E/S de operações de gravação por segundo|BytesPerSecond|Total|E/S de operações de gravação por segundo|Instância|
|MemoryWorkingSet|Sim|Conjunto de trabalho de memória|Bytes|Média|Conjunto de trabalho de memória|Instância|
|PrivateBytes|Sim|Bytes Particulares|Bytes|Média|Bytes Particulares|Instância|
|RequestsInApplicationQueue|Sim|Solicitações na fila do aplicativo|Contagem|Média|Solicitações na fila do aplicativo|Instância|
|TotalAppDomains|Sim|Total de domínios de aplicativo|Contagem|Média|Total de domínios de aplicativo|Instância|
|TotalAppDomainsUnloaded|Sim|Total de domínios de aplicativo descarregados|Contagem|Média|Total de domínios de aplicativo descarregados|Instância|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|AppConnections|Sim|conexões|Contagem|Média|conexões|Instância|
|AverageMemoryWorkingSet|Sim|Conjunto de trabalho de memória média|Bytes|Média|Conjunto de trabalho de memória média|Instância|
|AverageResponseTime|Sim|Tempo Médio de Resposta|Segundos|Média|Tempo Médio de Resposta|Instância|
|BytesReceived|Sim|Entrada de Dados|Bytes|Total|Entrada de Dados|Instância|
|BytesSent|Sim|Saída de dados|Bytes|Total|Saída de dados|Instância|
|CpuTime|Sim|Tempo de CPU|Segundos|Total|Tempo de CPU|Instância|
|CurrentAssemblies|Sim|Assemblies Atuais|Contagem|Média|Assemblies Atuais|Instância|
|FileSystemUsage|Sim|Uso do sistema de arquivos|Bytes|Média|Uso do sistema de arquivos|Sem dimensões|
|FunctionExecutionCount|Sim|Contagem de Execução de Função|Contagem|Total|Contagem de Execução de Função|Instância|
|FunctionExecutionUnits|Sim|Unidades de Execução de Função|Contagem|Total|Unidades de Execução de Função|Instância|
|Gen0Collections|Sim|Coletas de lixo da Ger 0|Contagem|Total|Coletas de lixo da Ger 0|Instância|
|Gen1Collections|Sim|Coletas de lixo da Ger 1|Contagem|Total|Coletas de lixo da Ger 1|Instância|
|Gen2Collections|Sim|Coletas de lixo da Ger 2|Contagem|Total|Coletas de lixo da Ger 2|Instância|
|Alças|Sim|Núm. de Identificadores|Contagem|Média|Núm. de Identificadores|Instância|
|HealthCheckStatus|Sim|Status da verificação de integridade|Contagem|Média|Status da verificação de integridade|Instância|
|Http101|Sim|Http 101|Contagem|Total|Http 101|Instância|
|Http2xx|Sim|Http 2xx|Contagem|Total|Http 2xx|Instância|
|Http3xx|Sim|Http 3xx|Contagem|Total|Http 3xx|Instância|
|Http401|Sim|Http 401|Contagem|Total|Http 401|Instância|
|Http403|Sim|Http 403|Contagem|Total|Http 403|Instância|
|Http404|Sim|Http 404|Contagem|Total|Http 404|Instância|
|Http406|Sim|Http 406|Contagem|Total|Http 406|Instância|
|Http4xx|Sim|Http 4xx|Contagem|Total|Http 4xx|Instância|
|Http5xx|Sim|Erros do Servidor Http|Contagem|Total|Erros do Servidor Http|Instância|
|HttpResponseTime|Sim|Tempo de Resposta|Segundos|Média|Tempo de Resposta|Instância|
|IoOtherBytesPerSecond|Sim|E/S de outros bytes por segundo|BytesPerSecond|Total|E/S de outros bytes por segundo|Instância|
|IoOtherOperationsPerSecond|Sim|E/S de outras operações por segundo|BytesPerSecond|Total|E/S de outras operações por segundo|Instância|
|IoReadBytesPerSecond|Sim|E/S de bytes de leitura por segundo|BytesPerSecond|Total|E/S de bytes de leitura por segundo|Instância|
|IoReadOperationsPerSecond|Sim|E/S de operações de leitura por segundo|BytesPerSecond|Total|E/S de operações de leitura por segundo|Instância|
|IoWriteBytesPerSecond|Sim|E/S de bytes de gravação por segundo|BytesPerSecond|Total|E/S de bytes de gravação por segundo|Instância|
|IoWriteOperationsPerSecond|Sim|E/S de operações de gravação por segundo|BytesPerSecond|Total|E/S de operações de gravação por segundo|Instância|
|MemoryWorkingSet|Sim|Conjunto de trabalho de memória|Bytes|Média|Conjunto de trabalho de memória|Instância|
|PrivateBytes|Sim|Bytes Particulares|Bytes|Média|Bytes Particulares|Instância|
|Requests|Sim|Requests|Contagem|Total|Requests|Instância|
|RequestsInApplicationQueue|Sim|Solicitações na fila do aplicativo|Contagem|Média|Solicitações na fila do aplicativo|Instância|
|Threads|Sim|Contagem de Threads|Contagem|Média|Contagem de Threads|Instância|
|TotalAppDomains|Sim|Total de domínios de aplicativo|Contagem|Média|Total de domínios de aplicativo|Instância|
|TotalAppDomainsUnloaded|Sim|Total de domínios de aplicativo descarregados|Contagem|Média|Total de domínios de aplicativo descarregados|Instância|


## <a name="next-steps"></a>Próximas etapas
* [Leia sobre as métricas no Azure Monitor](data-platform.md)
* [Criar alertas nas métricas](alerts-overview.md)
* [Exportar as métricas de armazenamento, Hub de eventos ou Log Analytics](platform-logs-overview.md)
