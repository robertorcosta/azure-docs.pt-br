---
title: Métricas compatíveis por tipo de recurso do Azure Monitor
description: Lista de métricas disponíveis para cada tipo de recurso com o Azure Monitor.
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 02/06/2021
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: 51cd3bf3349e1880d4dc9f5d98d2d9b5dc5bb228
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100605577"
---
# <a name="supported-metrics-with-azure-monitor"></a>Métricas compatíveis com o Azure Monitor

> [!NOTE]
> Essa lista é amplamente gerada automaticamente. Qualquer modificação feita nessa lista por meio do GitHub pode ser gravada sem aviso. Contate o autor deste artigo para obter detalhes sobre como fazer atualizações permanentes.

O Azure Monitor fornece várias maneiras de interagir com as métricas, incluindo a criação de gráficos para os mesmos no portal, acessá-las por meio da API REST ou consultá-las usando o PowerShell ou a CLI. 

Este artigo é uma lista completa de todas as métricas de plataforma (ou seja, coletadas automaticamente) atualmente disponíveis com o pipeline de métrica consolidado do Azure Monitor. As métricas alteradas ou adicionadas após a data na parte superior deste artigo talvez ainda não apareçam abaixo. Para consultar e acessar a lista de métricas programaticamente, use a [versão de api 2018-01-01](/rest/api/monitor/metricdefinitions). Outras métricas que não estão nessa lista podem estar disponíveis no portal ou usando APIs herdadas.

As métricas são organizadas por provedores de recursos e tipo de recurso. Para obter uma lista de serviços e os provedores de recursos e tipos que pertencem a eles, consulte [provedores de recursos para serviços do Azure](../../azure-resource-manager/management/azure-services-resource-providers.md).  

## <a name="exporting-platform-metrics-to-other-locations"></a>Exportando métricas de plataforma para outros locais

Você pode exportar as métricas de plataforma do pipeline do Azure Monitor para outros locais de uma das duas maneiras.
1. Usar a [API REST de métricas](/rest/api/monitor/metrics/list)
2. Usar [configurações de diagnóstico](../essentials/diagnostic-settings.md) para rotear métricas de plataforma para 
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
> A prática recomendada é usar e configurar a [extensão diagnóstico do Azure](../agents/diagnostics-extension-overview.md) para enviar métricas de desempenho do sistema operacional convidado para o mesmo banco de dados Azure monitor métrica em que as métricas de plataforma são armazenadas. A extensão roteia as métricas do sistema operacional convidado por meio da API de [métricas personalizada](../essentials/metrics-custom-overview.md) . Em seguida, você pode enviar um gráfico, alertar e, de outra forma, usar métricas de SO convidado como métricas de plataforma Como alternativa ou também, você pode usar o agente de Log Analytics para enviar métricas do sistema operacional convidado para Azure Monitor logs/Log Analytics. Lá, você pode consultar essas métricas em combinação com dados não métricas. 

Para obter informações adicionais importantes, consulte [visão geral dos agentes de monitoramento](../agents/agents-overview.md).

## <a name="table-formatting"></a>Formatação de tabela

> [!IMPORTANT] 
> Essa atualização mais recente adiciona uma nova coluna e reordenada a métrica para ser alfabética. As informações de adição significam que as tabelas abaixo podem ter uma barra de rolagem horizontal na parte inferior, dependendo da largura da janela do navegador. Se você achar que faltam informações, use a barra de rolagem para ver todo o índice da tabela.

## <a name="microsoftaadiamazureadmetrics"></a>Microsoft. aadiam/azureADMetrics

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ThrottledRequests|No|ThrottledRequests|Contagem|Média|métrica do tipo azureADMetrics|Sem dimensões|


## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|CleanerCurrentPrice|Yes|Memória: Preço atual do limpador|Contagem|Média|Preço atual da memória, $/byte/tempo, normalizado em 1000.|ServerResourceType|
|CleanerMemoryNonshrinkable|Yes|Memória: Memória do limpador não reduzível|Bytes|Média|Quantidade de memória, em bytes, não sujeita a eliminação pelo limpador na tela de fundo.|ServerResourceType|
|CleanerMemoryShrinkable|Yes|Memória: Memória do limpador reduzível|Bytes|Média|Quantidade de memória, em bytes, sujeita a eliminação pelo limpador na tela de fundo.|ServerResourceType|
|CommandPoolBusyThreads|Yes|Threads: Threads ocupados do pool comando|Contagem|Média|Número de threads ocupados no pool de threads de comando.|ServerResourceType|
|CommandPoolIdleThreads|Yes|Threads: Threads ociosos do pool comando|Contagem|Média|Número de threads ociosos no pool de threads de comando.|ServerResourceType|
|CommandPoolJobQueueLength|Yes|Comprimento da fila de trabalho do pool de comando|Contagem|Média|Número de trabalhos na fila do pool de threads de comando.|ServerResourceType|
|CurrentConnections|Yes|Conexão: Conexões atuais|Contagem|Média|Número atual de conexões de cliente estabelecidas.|ServerResourceType|
|CurrentUserSessions|Yes|Sessões de usuário atuais|Contagem|Média|Número atual de sessões de usuário estabelecidas.|ServerResourceType|
|LongParsingBusyThreads|Yes|Threads: Threads ocupados de análise longa|Contagem|Média|Número de threads ocupados no pool de threads de análise longa.|ServerResourceType|
|LongParsingIdleThreads|Yes|Threads: Threads ociosos de análise longa|Contagem|Média|Número de threads ociosos no pool de threads de análise longa.|ServerResourceType|
|LongParsingJobQueueLength|Yes|Threads: Tamanho da fila de trabalhos de análise longa|Contagem|Média|Número de trabalhos na fila do pool de threads de análise longa.|ServerResourceType|
|mashup_engine_memory_metric|Yes|Memória do mecanismo M|Bytes|Média|Uso de memória pelos processos do mecanismo de mashup|ServerResourceType|
|mashup_engine_private_bytes_metric|Yes|Bytes Particulares do Mecanismo M|Bytes|Média|Uso de bytes privados por processos do mecanismo mashup.|ServerResourceType|
|mashup_engine_qpu_metric|Yes|QPU do mecanismo M|Contagem|Média|Uso de QPU por processos de mecanismo de mashup|ServerResourceType|
|mashup_engine_virtual_bytes_metric|Yes|Bytes Virtuais do Mecanismo M|Bytes|Média|Uso de bytes virtuais por processos do mecanismo mashup.|ServerResourceType|
|memory_metric|Yes|Memória|Bytes|Média|Memória. Intervalo de 0 a 25 GB para S1, 0 a 50 GB para S2 e 0 a 100 GB para S4|ServerResourceType|
|memory_thrashing_metric|Yes|Sobrecarga de memória|Porcentagem|Média|Sobrecarga de memória média.|ServerResourceType|
|MemoryLimitHard|Yes|Memória: Limite de memória física|Bytes|Média|Limite de memória física, do arquivo de configuração.|ServerResourceType|
|MemoryLimitHigh|Yes|Memória: Limite de memória superior|Bytes|Média|Limite de memória superior, do arquivo de configuração.|ServerResourceType|
|MemoryLimitLow|Yes|Memória: Limite de memória inferior|Bytes|Média|Limite de memória inferior, do arquivo de configuração.|ServerResourceType|
|MemoryLimitVertiPaq|Yes|Memória: Limite de memória VertiPaq|Bytes|Média|Limite na memória, do arquivo de configuração.|ServerResourceType|
|MemoryUsage|Yes|Memória: Uso de Memória|Bytes|Média|Uso de memória do processo do servidor, como usado no cálculo de preço de memória do limpador. Igual ao contador Processo\PrivateBytes mais o tamanho dos dados mapeados em memória, ignorando qualquer memória mapeada ou alocada pelo mecanismo de análise de memória xVelocity (VertiPaq), além do limite de memória do mecanismo xVelocity.|ServerResourceType|
|private_bytes_metric|Yes|Bytes Particulares|Bytes|Média|Bytes privados.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Yes|Threads: Threads de trabalho de E/S ocupados do pool de processamento|Contagem|Média|Número de threads que executam trabalhos de E/S no pool de threads de processamento.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Yes|Threads: Threads de trabalho não E/S ocupados do pool de processamento|Contagem|Média|Número de threads que executam trabalhos não E/S no pool de threads de processamento.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Yes|Threads: Threads de trabalho de E/S ociosos do pool de processamento|Contagem|Média|Número de threads ociosos para trabalhos de E/S no pool de threads de processamento.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Yes|Threads: Threads de trabalho não E/S ociosos do pool de processamento|Contagem|Média|Número de threads ociosos no pool de threads de processamento dedicado a trabalhos não E/S.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Yes|Threads: Comprimento da fila de trabalho de E/S do pool de processamento|Contagem|Média|Número de trabalhos de E/S na fila do pool de threads de processamento.|ServerResourceType|
|ProcessingPoolJobQueueLength|Yes|Comprimento da fila de trabalho do pool de processamento|Contagem|Média|Número de trabalhos não de E/S na fila do pool de threads de processamento.|ServerResourceType|
|qpu_metric|Yes|QPU|Contagem|Média|QPU. Intervalo de 0 a 100 para S1, 0 a 200 para S2 e 0 a 400 para S4|ServerResourceType|
|QueryPoolBusyThreads|Yes|Threads ocupados do pool de consulta|Contagem|Média|Número de threads ocupados no pool de threads de consulta.|ServerResourceType|
|QueryPoolIdleThreads|Yes|Threads: Threads ociosos do pool de consultas|Contagem|Média|Número de threads ociosos para trabalhos de E/S no pool de threads de processamento.|ServerResourceType|
|QueryPoolJobQueueLength|Yes|Threads: tamanho da fila de trabalhos do pool consultas|Contagem|Média|Número de trabalhos na fila do pool de threads de consulta.|ServerResourceType|
|Quota|Yes|Memória: Quota|Bytes|Média|Cota de memória atual, em bytes. A cota de memória também é conhecida como uma reserva de memória ou concessão de memória.|ServerResourceType|
|QuotaBlocked|Yes|Memória: Cota bloqueada|Contagem|Média|Número atual de solicitações de cota bloqueadas até que outras cotas de memória sejam liberadas.|ServerResourceType|
|RowsConvertedPerSec|Yes|Processamento: Linhas convertidas por segundo|CountPerSecond|Média|Taxa de linhas convertidas durante o processamento.|ServerResourceType|
|RowsReadPerSec|Yes|Processamento: Linhas lidas por segundo|CountPerSecond|Média|Taxa de linhas lidas de todos os bancos de dados relacionais.|ServerResourceType|
|RowsWrittenPerSec|Yes|Processamento: Linhas gravadas por segundo|CountPerSecond|Média|Taxa de linhas gravadas durante o processamento.|ServerResourceType|
|ShortParsingBusyThreads|Yes|Threads: Threads ocupados de análise resumida|Contagem|Média|Número de threads ocupados no pool de threads de análise resumida.|ServerResourceType|
|ShortParsingIdleThreads|Yes|Threads: Threads ociosos de análise resumida|Contagem|Média|Número de threads ociosos no pool de threads de análise resumida.|ServerResourceType|
|ShortParsingJobQueueLength|Yes|Threads: Tamanho da fila de trabalhos de análise resumida|Contagem|Média|Número de trabalhos na fila do pool de threads de análise resumida.|ServerResourceType|
|SuccessfullConnectionsPerSec|Yes|Conexões bem-sucedidas por segundo|CountPerSecond|Média|Taxa de conclusões de conexão bem-sucedidas.|ServerResourceType|
|TotalConnectionFailures|Yes|Falhas de conexão totais|Contagem|Média|Total de falhas em tentativas de conexão.|ServerResourceType|
|TotalConnectionRequests|Yes|Solicitações de conexão totais|Contagem|Média|Solicitações de conexão totais. Estas são chegadas.|ServerResourceType|
|VertiPaqNonpaged|Yes|Memória: VertiPaq não paginado|Bytes|Média|Bytes de memória bloqueada no conjunto de trabalho para uso pelo mecanismo na memória.|ServerResourceType|
|VertiPaqPaged|Yes|Memória: VertiPaq paginado|Bytes|Média|Bytes de memória paginada em uso para dados na memória.|ServerResourceType|
|virtual_bytes_metric|Yes|Bytes Virtuais|Bytes|Média|Bytes virtuais.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|BackendDuration|Yes|Duração de Solicitações de Back-end|Milissegundos|Média|Duração de solicitações de back-end em milissegundos|Local, Nome do host|
|Capacity|Yes|Capacity|Porcentagem|Média|Métrica de utilização para o serviço ApiManagement|Location|
|Duração|Yes|Duração total de solicitações de gateway|Milissegundos|Média|Duração total de solicitações de gateway em milissegundos|Local, Nome do host|
|EventHubDroppedEvents|Yes|Eventos do EventHub Ignorados|Contagem|Total|Número de eventos ignorados devido ao limite de tamanho de fila atingido|Location|
|EventHubRejectedEvents|Yes|Eventos do EventHub Rejeitados|Contagem|Total|Número de eventos do EventHub rejeitados (configuração incorreta ou não autorizada)|Location|
|EventHubSuccessfulEvents|Yes|Eventos do EventHub Bem-sucedidos|Contagem|Total|Número de eventos de EventHub bem-sucedidos|Location|
|EventHubThrottledEvents|Yes|Eventos do EventHub Restringidos|Contagem|Total|Número de eventos regulados do EventHub|Location|
|EventHubTimedoutEvents|Yes|Eventos do EventHub com Tempo Limite Excedido|Contagem|Total|Número de eventos do EventHub com tempo limite excedido|Location|
|EventHubTotalBytesSent|Yes|Tamanho dos Eventos do EventHub|Bytes|Total|Tamanho total dos eventos do EventHub em bytes|Location|
|EventHubTotalEvents|Yes|Eventos Totais do EventHub|Contagem|Total|Número de eventos enviados ao EventHub|Location|
|EventHubTotalFailedEvents|Yes|Eventos do EventHub com Falha|Contagem|Total|Número de eventos de EventHub com falha|Location|
|FailedRequests|Yes|Solicitações de Gateway com Falha (Preterido)|Contagem|Total|Número de falhas em solicitações de gateway-use a métrica de solicitação de várias dimensões com a dimensão GatewayResponseCodeCategory em vez disso|Local, Nome do host|
|Conectividade|Yes|Status de conectividade de rede de recursos (versão prévia)|Contagem|Média|Status de conectividade de rede de tipos de recursos dependentes do serviço de gerenciamento de API|Local, ResourceType|
|OtherRequests|Yes|Outras Solicitações de Gateway (Preterido)|Contagem|Total|Número de outras solicitações de gateway – use a métrica de solicitação de várias dimensões com a dimensão GatewayResponseCodeCategory em vez disso|Local, Nome do host|
|Requests|Yes|Requests|Contagem|Total|Métricas de solicitação de gateway com várias dimensões|Local, nome do host, LastErrorReason, BackendResponseCode, GatewayResponseCode, BackendResponseCodeCategory, GatewayResponseCodeCategory|
|SuccessfulRequests|Yes|Solicitações de Gateway com Êxito (Preterido)|Contagem|Total|Número de solicitações de gateway bem-sucedidas-use a métrica de solicitação de várias dimensões com a dimensão GatewayResponseCodeCategory em vez disso|Local, Nome do host|
|TotalRequests|Yes|Solicitações Totais de Gateway (Preterido)|Contagem|Total|Número de solicitações de gateway-use a métrica de solicitação de várias dimensões com a dimensão GatewayResponseCodeCategory em vez disso|Local, Nome do host|
|UnauthorizedRequests|Yes|Solicitações de Gateway Não Autorizadas (Preterido)|Contagem|Total|Número de solicitações de gateway não autorizadas-use a métrica de solicitação de várias dimensões com a dimensão GatewayResponseCodeCategory em vez disso|Local, Nome do host|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|HttpIncomingRequestCount|Yes|HttpIncomingRequestCount|Contagem|Contagem|Número total de solicitações HTTP de entrada.|StatusCode, autenticação|
|HttpIncomingRequestDuration|Yes|HttpIncomingRequestDuration|Contagem|Média|Latência em uma solicitação HTTP.|StatusCode, autenticação|
|ThrottledHttpRequestCount|Yes|ThrottledHttpRequestCount|Contagem|Contagem|Solicitações HTTP limitadas.|Sem dimensões|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|active-timer-Count|Yes|{1&gt;System.Runtime&lt;1}|active-timer-Count|Contagem|Média|Número de timers atualmente ativos|Implantação, AppName, Pod|
|taxa de alocação|Yes|{1&gt;System.Runtime&lt;1}|taxa de alocação|Bytes|Média|Número de bytes alocados no heap gerenciado|Implantação, AppName, Pod|
|AppCpuUsage|Yes|Uso de CPU do aplicativo (versão prévia)|Porcentagem|Média|O uso recente da CPU para o aplicativo|Implantação, AppName, Pod|
|assembly-contagem|Yes|{1&gt;System.Runtime&lt;1}|assembly-contagem|Contagem|Média|Número de assemblies carregados|Implantação, AppName, Pod|
|uso da CPU|Yes|{1&gt;System.Runtime&lt;1}|uso da CPU|Porcentagem|Média|% de tempo que o processo utilizou a CPU|Implantação, AppName, Pod|
|solicitações atuais|Yes|Microsoft.AspNetCore.Hosting|solicitações atuais|Contagem|Média|Número total de solicitações no processamento no tempo de vida do processo|Implantação, AppName, Pod|
|contagem de exceção|Yes|{1&gt;System.Runtime&lt;1}|contagem de exceção|Contagem|Total|Número de exceções|Implantação, AppName, Pod|
|solicitações com falha|Yes|Microsoft.AspNetCore.Hosting|solicitações com falha|Contagem|Média|Número total de solicitações com falha no tempo de vida do processo|Implantação, AppName, Pod|
|GC-heap-tamanho|Yes|{1&gt;System.Runtime&lt;1}|GC-heap-tamanho|Contagem|Média|Tamanho total do heap relatado pelo GC (MB)|Implantação, AppName, Pod|
|Gen-0-GC-contagem|Yes|{1&gt;System.Runtime&lt;1}|Gen-0-GC-contagem|Contagem|Média|Número de Ger 0 GCs|Implantação, AppName, Pod|
|Ger-0-tamanho|Yes|{1&gt;System.Runtime&lt;1}|Ger-0-tamanho|Bytes|Média|Tamanho de heap de Gen 0|Implantação, AppName, Pod|
|Gen-1-GC-contagem|Yes|{1&gt;System.Runtime&lt;1}|Gen-1-GC-contagem|Contagem|Média|{1&gt;System.Runtime&lt;1}|Número de Ger 1 GCs|Implantação, AppName, Pod|
|Ger-1-tamanho|Yes|{1&gt;System.Runtime&lt;1}|Ger-1-tamanho|Bytes|Média|Tamanho do heap Gen 1|Implantação, AppName, Pod|
|Gen-2-GC-contagem|Yes|{1&gt;System.Runtime&lt;1}|Gen-2-GC-contagem|Contagem|Média|Número de Ger 2 GCs|Implantação, AppName, Pod|
|Gen-2-tamanho|Yes|{1&gt;System.Runtime&lt;1}|Gen-2-tamanho|Bytes|Média|Tamanho do heap de Gen 2|Implantação, AppName, Pod|
|JVM. GC. Live. Data. Size|Yes|JVM. GC. Live. Data. Size|Bytes|Média|Tamanho do pool de memória de geração antiga após um GC completo|Implantação, AppName, Pod|
|JVM. GC. Max. Data. Size|Yes|JVM. GC. Max. Data. Size|Bytes|Média|Tamanho máximo do pool de memória de geração antiga|Implantação, AppName, Pod|
|JVM. GC. Memory. allocated|Yes|JVM. GC. Memory. allocated|Bytes|Máximo|Incrementado para um aumento no tamanho do pool de memória de geração jovem após um GC antes do próximo|Implantação, AppName, Pod|
|JVM. GC. Memory. promovida|Yes|JVM. GC. Memory. promovida|Bytes|Máximo|Contagem de aumentos positivos no tamanho do pool de memória de geração antiga antes do GC ser após o GC|Implantação, AppName, Pod|
|JVM. GC. PAUSE. total. Count|Yes|JVM. GC. PAUSE. total. Count|Contagem|Total|Contagem de Pausa do GC|Implantação, AppName, Pod|
|JVM. GC. PAUSE. total. time|Yes|JVM. GC. PAUSE. total. time|Milissegundos|Total|Tempo Total de Pausa do GC|Implantação, AppName, Pod|
|JVM. Memory. Committed|Yes|JVM. Memory. Committed|Bytes|Média|Memória atribuída à JVM em bytes|Implantação, AppName, Pod|
|JVM. Memory. Max|Yes|JVM. Memory. Max|Bytes|Máximo|A quantidade máxima de memória, em bytes, que pode ser usada para gerenciamento de memória|Implantação, AppName, Pod|
|JVM. Memory. Used|Yes|JVM. Memory. Used|Bytes|Média|Memória do aplicativo usada em bytes|Implantação, AppName, Pod|
|Loh-tamanho|Yes|{1&gt;System.Runtime&lt;1}|Loh-tamanho|Bytes|Média|Tamanho do heap de LOH|Implantação, AppName, Pod|
|monitor-contagem de contenção de bloqueio|Yes|{1&gt;System.Runtime&lt;1}|monitor-contagem de contenção de bloqueio|Contagem|Média|Número de vezes que houve contenção ao tentar fazer o bloqueio do monitor|Implantação, AppName, Pod|
|Process. CPU. Usage|Yes|Process. CPU. Usage|Porcentagem|Média|O uso recente da CPU para o processo JVM|Implantação, AppName, Pod|
|solicitações por segundo|Yes|Microsoft.AspNetCore.Hosting|solicitações-taxa|Contagem|Média|Taxa de solicitação|Implantação, AppName, Pod|
|System. CPU. Usage|Yes|System. CPU. Usage|Porcentagem|Média|O uso recente da CPU para todo o sistema|Implantação, AppName, Pod|
|ThreadPool-concluído-itens-contagem|Yes|{1&gt;System.Runtime&lt;1}|ThreadPool-concluído-itens-contagem|Contagem|Média|Contagem de itens de trabalho de ThreadPool concluído|Implantação, AppName, Pod|
|ThreadPool-comprimento da fila|Yes|{1&gt;System.Runtime&lt;1}|ThreadPool-comprimento da fila|Contagem|Média|Comprimento da fila de itens de trabalho do ThreadPool|Implantação, AppName, Pod|
|ThreadPool-contagem de threads|Yes|{1&gt;System.Runtime&lt;1}|ThreadPool-contagem de threads|Contagem|Média|Número de threads ThreadPool|Implantação, AppName, Pod|
|tempo de GC|Yes|{1&gt;System.Runtime&lt;1}|tempo de GC|Porcentagem|Média|% de tempo no GC desde o último GC|Implantação, AppName, Pod|
|Tomcat. global. Error|Yes|Tomcat. global. Error|Contagem|Total|Erro Global do Tomcat|Implantação, AppName, Pod|
|Tomcat. global. Received|Yes|Tomcat. global. Received|Bytes|Total|Total de Bytes Recebidos do Tomcat|Implantação, AppName, Pod|
|Tomcat. global. Request. AVG. time|Yes|Tomcat. global. Request. AVG. time|Milissegundos|Média|Tempo Médio da Solicitação do Tomcat|Implantação, AppName, Pod|
|Tomcat. global. Request. Max|Yes|Tomcat. global. Request. Max|Milissegundos|Máximo|Tempo Máximo de Solicitação do Tomcat|Implantação, AppName, Pod|
|Tomcat. global. Request. total. Count|Yes|Tomcat. global. Request. total. Count|Contagem|Total|Contagem Total de Solicitações do Tomcat|Implantação, AppName, Pod|
|Tomcat. global. Request. total. time|Yes|Tomcat. global. Request. total. time|Milissegundos|Total|Tempo total da solicitação do Tomcat|Implantação, AppName, Pod|
|Tomcat. global. sent|Yes|Tomcat. global. sent|Bytes|Total|Total de Bytes Enviados do Tomcat|Implantação, AppName, Pod|
|Tomcat. Sessions. Active. Current|Yes|Tomcat. Sessions. Active. Current|Contagem|Total|Contagem ativa da sessão Tomcat|Implantação, AppName, Pod|
|Tomcat. Sessions. Active. Max|Yes|Tomcat. Sessions. Active. Max|Contagem|Total|Contagem de Sessões Ativas Máximas do Tomcat|Implantação, AppName, Pod|
|Tomcat. Sessions. Alive. Max|Yes|Tomcat. Sessions. Alive. Max|Milissegundos|Máximo|Tempo de Sessões Ativas Máximas do Tomcat|Implantação, AppName, Pod|
|Tomcat. Sessions. Created|Yes|Tomcat. Sessions. Created|Contagem|Total|Contagem de Sessões Criadas do Tomcat|Implantação, AppName, Pod|
|Tomcat. Sessions. Expired|Yes|Tomcat. Sessions. Expired|Contagem|Total|Contagem de Sessões Expiradas do Tomcat|Implantação, AppName, Pod|
|Tomcat. Sessions. Rejected|Yes|Tomcat. Sessions. Rejected|Contagem|Total|Contagem de Sessões Rejeitadas do Tomcat|Implantação, AppName, Pod|
|tomcat.threads.config. Max|Yes|tomcat.threads.config. Max|Contagem|Total|Contagem máxima de threads de configuração do Tomcat|Implantação, AppName, Pod|
|Tomcat. threads. Current|Yes|Tomcat. threads. Current|Contagem|Total|Contagem de threads atuais do Tomcat|Implantação, AppName, Pod|
|total-solicitações|Yes|Microsoft.AspNetCore.Hosting|total-solicitações|Contagem|Média|Número total de solicitações no tempo de vida do processo|Implantação, AppName, Pod|
|conjunto de trabalho|Yes|{1&gt;System.Runtime&lt;1}|conjunto de trabalho|Contagem|Média|Quantidade de conjunto de trabalho usado pelo processo (MB)|Implantação, AppName, Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|TotalJob|Yes|Total de trabalhos|Contagem|Total|O número total de trabalhos|Runbook, Status|
|TotalUpdateDeploymentMachineRuns|Yes|Total de Execuções de Computador da Implantação de Atualização|Contagem|Total|O computador de implantação de atualização de software total é executado em uma execução de implantação de atualização de software|SoftwareUpdateConfigurationName, status, Computadordedestino, SoftwareUpdateConfigurationRunId|
|TotalUpdateDeploymentRuns|Yes|Total de Execuções da Implantação de Atualização|Contagem|Total|Total de execuções de implantação de atualização de software|SoftwareUpdateConfigurationName, status|


## <a name="microsoftavsprivateclouds"></a>Microsoft. AVS/privateClouds

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|CapacityLatest|Yes|Capacidade total do disco do repositório de armazenamento|Bytes|Média|A capacidade total do disco no repositório de armazenamento|dsname|
|DiskUsedPercentage|Yes| Percentual de disco de repositório de armazenamento usado|Porcentagem|Média|Porcentagem de disco disponível usado no repositório de armazenamento|dsname|
|EffectiveCpuAverage|Yes|Porcentagem de CPU|Porcentagem|Média|Percentual de recursos de CPU usados no cluster|ClusterName|
|EffectiveMemAverage|Yes|Memória efetiva média|Bytes|Média|Quantidade total disponível de memória do computador no cluster|ClusterName|
|OverheadAverage|Yes|Sobrecarga de memória média|Bytes|Média|Memória física do host consumida pela infraestrutura de virtualização|ClusterName|
|TotalMbAverage|Yes|Média de memória total|Bytes|Média|Memória total no cluster|ClusterName|
|UsageAverage|Yes|Média de Utilização de Memória|Porcentagem|Média|Uso de memória como porcentagem da memória total configurada ou disponível|ClusterName|
|UsedLatest|Yes|Disco de repositório de armazenamento usado|Bytes|Média|A quantidade total de disco usada no repositório de armazenamento|dsname|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|CoreCount|No|Contagem de núcleos dedicados|Contagem|Total|Número total de núcleos dedicados na conta do lote|Sem dimensões|
|CreatingNodeCount|No|Criação de contagem de nós|Contagem|Total|Número de nós sendo criados|Sem dimensões|
|IdleNodeCount|No|Contagem de nós ociosos|Contagem|Total|Número de nós ociosos|Sem dimensões|
|JobDeleteCompleteEvent|Yes|Eventos de conclusão de exclusão do trabalho|Contagem|Total|Número total de trabalhos que foram excluídos com êxito.|jobId|
|JobDeleteStartEvent|Yes|Eventos de início de exclusão do trabalho|Contagem|Total|Número total de trabalhos que foram solicitados a serem excluídos.|jobId|
|JobDisableCompleteEvent|Yes|Eventos de conclusão de desabilitação do trabalho|Contagem|Total|Número total de trabalhos que foram desabilitados com êxito.|jobId|
|JobDisableStartEvent|Yes|Eventos de início de desabilitação do trabalho|Contagem|Total|Número total de trabalhos que foram solicitados a serem desabilitados.|jobId|
|JobStartEvent|Yes|Eventos de início do trabalho|Contagem|Total|Número total de trabalhos que foram iniciados com êxito.|jobId|
|JobTerminateCompleteEvent|Yes|Eventos de conclusão de encerramento do trabalho|Contagem|Total|Número total de trabalhos que foram terminados com êxito.|jobId|
|JobTerminateStartEvent|Yes|Eventos de início de encerramento do trabalho|Contagem|Total|Número total de trabalhos que foram solicitados a serem encerrados.|jobId|
|LeavingPoolNodeCount|No|Contagem de nós saindo do pool|Contagem|Total|Número de nós saindo do pool|Sem dimensões|
|LowPriorityCoreCount|No|Contagem de núcleos LowPriority|Contagem|Total|Número total de núcleos de baixa prioridade na conta do lote|Sem dimensões|
|OfflineNodeCount|No|Contagem de nós off-line|Contagem|Total|Número de nós off-line|Sem dimensões|
|PoolCreateEvent|Yes|Eventos de criação de pool|Contagem|Total|Número total de pools criados|poolId|
|PoolDeleteCompleteEvent|Yes|Eventos de conclusão de exclusão do pool|Contagem|Total|Número total de exclusões de pool concluídas|poolId|
|PoolDeleteStartEvent|Yes|Eventos de início de exclusão de pool|Contagem|Total|Número total de exclusões de pool iniciados|poolId|
|PoolResizeCompleteEvent|Yes|Eventos de conclusão de redimensionamento de pool|Contagem|Total|Número total de redimensionamentos de pool concluídos|poolId|
|PoolResizeStartEvent|Yes|Eventos de início de redimensionamento de pool|Contagem|Total|Número total de tarefas de redimensionamento de pool que iniciaram|poolId|
|PreemptedNodeCount|No|Contagem de nós com preempção|Contagem|Total|Número de nós com preempção|Sem dimensões|
|RebootingNodeCount|No|Contagem de nós de reinicialização|Contagem|Total|Número de nós de reinicialização|Sem dimensões|
|ReimagingNodeCount|No|Contagem de nós refazendo a imagem|Contagem|Total|Número de nós refazendo a imagem|Sem dimensões|
|RunningNodeCount|No|Contagem de nós em execução|Contagem|Total|Número de nós em execução|Sem dimensões|
|StartingNodeCount|No|Contagem inicial dos nós|Contagem|Total|Número de nós iniciais|Sem dimensões|
|StartTaskFailedNodeCount|No|Falha na contagem de nós para a tarefa de inicialização|Contagem|Total|Número de nós com falha na tarefa de inicialização|Sem dimensões|
|TaskCompleteEvent|Yes|Eventos de conclusão de tarefa|Contagem|Total|Número total de tarefas concluídas|poolid, jobId|
|TaskFailEvent|Yes|Eventos de falha de tarefa|Contagem|Total|Número total de tarefas que foram concluídas em um estado com falha|poolid, jobId|
|TaskStartEvent|Yes|Eventos da tarefa de inicialização|Contagem|Total|Número total de tarefas que iniciaram|poolid, jobId|
|TotalLowPriorityNodeCount|No|Contagem de nós de baixa prioridade|Contagem|Total|Número total de nós de baixa prioridade na conta do lote|Sem dimensões|
|TotalNodeCount|No|Contagem de nós dedicados|Contagem|Total|Número total de nós dedicados na conta do lote|Sem dimensões|
|UnusableNodeCount|No|Contagem de nós inutilizáveis|Contagem|Total|Número de nós inutilizáveis|Sem dimensões|
|WaitingForStartTaskNodeCount|No|Contagem de nós para tarefa de inicialização em espera|Contagem|Total|Número de nós aguardando a conclusão da Tarefa de Inicialização|Sem dimensões|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/workspaces

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Núcleos Ativos|Yes|Núcleos Ativos|Contagem|Média|Número de núcleos ativos|Cenário, ClusterName|
|Nós Ativos|Yes|Nós Ativos|Contagem|Média|Número de nós em execução|Cenário, ClusterName|
|Núcleos Ociosos|Yes|Núcleos Ociosos|Contagem|Média|Número de núcleos ociosos|Cenário, ClusterName|
|Nós Ociosos|Yes|Nós Ociosos|Contagem|Média|Número de nós ociosos|Cenário, ClusterName|
|Trabalho Concluído|Yes|Trabalho Concluído|Contagem|Total|Número de trabalhos concluídos|Cenário, ClusterName, Resultadotype|
|Trabalho Enviado|Yes|Trabalho Enviado|Contagem|Total|Número de trabalhos enviados|Cenário, ClusterName|
|Núcleos em Saída|Yes|Núcleos em Saída|Contagem|Média|Número de núcleos de saída|Cenário, ClusterName|
|Nós em Saída|Yes|Nós em Saída|Contagem|Média|Número de nós de saída|Cenário, ClusterName|
|Núcleos com Preempção|Yes|Núcleos com Preempção|Contagem|Média|Número de núcleos admitidos|Cenário, ClusterName|
|Nós com Preempção|Yes|Nós com Preempção|Contagem|Média|Número de nós com preempção|Cenário, ClusterName|
|Percentual de Utilização de Cota|Yes|Percentual de Utilização de Cota|Contagem|Média|Porcentagem de cota utilizada|Cenário, ClusterName, VmFamilyName, VmPriority|
|Total de Núcleos|Yes|Total de Núcleos|Contagem|Média|Número de núcleos totais|Cenário, ClusterName|
|Total de Nós|Yes|Total de Nós|Contagem|Média|Número total de nós|Cenário, ClusterName|
|Núcleos Inutilizáveis|Yes|Núcleos Inutilizáveis|Contagem|Média|Número de núcleos inutilizáveis|Cenário, ClusterName|
|Nós Inutilizáveis|Yes|Nós Inutilizáveis|Contagem|Média|Número de nós inutilizáveis|Cenário, ClusterName|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|BroadcastProcessedCount|Yes|BroadcastProcessedCountDisplayName|Contagem|Média|O número de transações processadas.|Nó, canal, tipo, status|
|ChaincodeExecuteTimeouts|Yes|ChaincodeExecuteTimeoutsDisplayName|Contagem|Média|O número de execuções de chaincode (init ou Invoke) que atingiram o tempo limite.|Nó, chaincode|
|ChaincodeLaunchFailures|Yes|ChaincodeLaunchFailuresDisplayName|Contagem|Média|O número de inicializações de chaincode que falharam.|Nó, chaincode|
|ChaincodeLaunchTimeouts|Yes|ChaincodeLaunchTimeoutsDisplayName|Contagem|Média|O número de inicializações de chaincode que atingiram o tempo limite.|Nó, chaincode|
|ChaincodeShimRequestsCompleted|Yes|ChaincodeShimRequestsCompletedDisplayName|Contagem|Média|O número de solicitações de Shim chaincode concluídas.|Nó, tipo, canal, chaincode, êxito|
|ChaincodeShimRequestsReceived|Yes|ChaincodeShimRequestsReceivedDisplayName|Contagem|Média|O número de solicitações de Shim chaincode recebidas.|Nó, tipo, canal, chaincode|
|ClusterCommEgressQueueCapacity|Yes|ClusterCommEgressQueueCapacityDisplayName|Contagem|Média|Capacidade da fila de saída.|Nó, host, msg_type, canal|
|ClusterCommEgressQueueLength|Yes|ClusterCommEgressQueueLengthDisplayName|Contagem|Média|Comprimento da fila de saída.|Nó, host, msg_type, canal|
|ClusterCommEgressQueueWorkers|Yes|ClusterCommEgressQueueWorkersDisplayName|Contagem|Média|Contagem de trabalhos de fila de egresso.|Nó, canal|
|ClusterCommEgressStreamCount|Yes|ClusterCommEgressStreamCountDisplayName|Contagem|Média|Contagem de fluxos para outros nós.|Nó, canal|
|ClusterCommEgressTlsConnectionCount|Yes|ClusterCommEgressTlsConnectionCountDisplayName|Contagem|Média|Contagem de conexões TLS com outros nós.|Nó|
|ClusterCommIngressStreamCount|Yes|ClusterCommIngressStreamCountDisplayName|Contagem|Média|Contagem de fluxos de outros nós.|Nó|
|ClusterCommMsgDroppedCount|Yes|ClusterCommMsgDroppedCountDisplayName|Contagem|Média|Contagem de mensagens eliminadas.|Nó, host, canal|
|ConnectionAccepted|Yes|Conexões Aceitas|Contagem|Total|Conexões Aceitas|Nó|
|ConnectionActive|Yes|Conexões ativas|Contagem|Média|Conexões ativas|Nó|
|ConnectionHandled|Yes|Conexões Manipuladas|Contagem|Total|Conexões Manipuladas|Nó|
|ConsensusEtcdraftActiveNodes|Yes|ConsensusEtcdraftActiveNodesDisplayName|Contagem|Média|Número de nós ativos neste canal.|Nó, canal|
|ConsensusEtcdraftClusterSize|Yes|ConsensusEtcdraftClusterSizeDisplayName|Contagem|Média|Número de nós neste canal.|Nó, canal|
|ConsensusEtcdraftCommittedBlockNumber|Yes|ConsensusEtcdraftCommittedBlockNumberDisplayName|Contagem|Média|O número de bloco do bloco mais recente confirmado.|Nó, canal|
|ConsensusEtcdraftConfigProposalsReceived|Yes|ConsensusEtcdraftConfigProposalsReceivedDisplayName|Contagem|Média|O número total de propostas recebidas para transações de tipo de configuração.|Nó, canal|
|ConsensusEtcdraftIsLeader|Yes|ConsensusEtcdraftIsLeaderDisplayName|Contagem|Média|O status de liderança do nó atual: 1 se for o líder 0.|Nó, canal|
|ConsensusEtcdraftLeaderChanges|Yes|ConsensusEtcdraftLeaderChangesDisplayName|Contagem|Média|O número de alterações de líder desde o início do processo.|Nó, canal|
|ConsensusEtcdraftNormalProposalsReceived|Yes|ConsensusEtcdraftNormalProposalsReceivedDisplayName|Contagem|Média|O número total de propostas recebidas para transações de tipo normal.|Nó, canal|
|ConsensusEtcdraftProposalFailures|Yes|ConsensusEtcdraftProposalFailuresDisplayName|Contagem|Média|O número de falhas de proposta.|Nó, canal|
|ConsensusEtcdraftSnapshotBlockNumber|Yes|ConsensusEtcdraftSnapshotBlockNumberDisplayName|Contagem|Média|O número de bloco do instantâneo mais recente.|Nó, canal|
|ConsensusKafkaBatchSize|Yes|ConsensusKafkaBatchSizeDisplayName|Contagem|Média|O tamanho do lote médio em bytes enviados aos tópicos.|Nó, tópico|
|ConsensusKafkaCompressionRatio|Yes|ConsensusKafkaCompressionRatioDisplayName|Contagem|Média|A taxa de compactação média (como porcentagem) para tópicos.|Nó, tópico|
|ConsensusKafkaIncomingByteRate|Yes|ConsensusKafkaIncomingByteRateDisplayName|Contagem|Média|Bytes/segundo lidores de agentes.|Nó, broker_id|
|ConsensusKafkaLastOffsetPersisted|Yes|ConsensusKafkaLastOffsetPersistedDisplayName|Contagem|Média|O deslocamento especificado nos metadados de bloco do bloco confirmado mais recentemente.|Nó, canal|
|ConsensusKafkaOutgoingByteRate|Yes|ConsensusKafkaOutgoingByteRateDisplayName|Contagem|Média|Bytes/segundo gravados em agentes.|Nó, broker_id|
|ConsensusKafkaRecordSendRate|Yes|ConsensusKafkaRecordSendRateDisplayName|Contagem|Média|O número de registros por segundo enviados aos tópicos.|Nó, tópico|
|ConsensusKafkaRecordsPerRequest|Yes|ConsensusKafkaRecordsPerRequestDisplayName|Contagem|Média|O número médio de registros enviados por solicitação para tópicos.|Nó, tópico|
|ConsensusKafkaRequestLatency|Yes|ConsensusKafkaRequestLatencyDisplayName|Contagem|Média|A latência média da solicitação em MS para agentes.|Nó, broker_id|
|ConsensusKafkaRequestRate|Yes|ConsensusKafkaRequestRateDisplayName|Contagem|Média|Solicitações/segundo enviadas aos agentes.|Nó, broker_id|
|ConsensusKafkaRequestSize|Yes|ConsensusKafkaRequestSizeDisplayName|Contagem|Média|O tamanho médio da solicitação em bytes para agentes.|Nó, broker_id|
|ConsensusKafkaResponseRate|Yes|ConsensusKafkaResponseRateDisplayName|Contagem|Média|Solicitações/segundo enviadas aos agentes.|Nó, broker_id|
|ConsensusKafkaResponseSize|Yes|ConsensusKafkaResponseSizeDisplayName|Contagem|Média|O tamanho médio da resposta em bytes de agentes.|Nó, broker_id|
|CpuUsagePercentageInDouble|Yes|Percentual de Uso de CPU|Porcentagem|Máximo|Percentual de Uso de CPU|Nó|
|DeliverBlocksSent|Yes|DeliverBlocksSentDisplayName|Contagem|Média|O número de blocos enviados pelo serviço de entrega.|Nó, canal, filtrado, data_type|
|DeliverRequestsCompleted|Yes|DeliverRequestsCompletedDisplayName|Contagem|Média|O número de solicitações de entrega que foram concluídas.|Nó, canal, filtrado, data_type, êxito|
|DeliverRequestsReceived|Yes|DeliverRequestsReceivedDisplayName|Contagem|Média|O número de solicitações de entrega que foram recebidas.|Nó, canal, filtrado, data_type|
|DeliverStreamsClosed|Yes|DeliverStreamsClosedDisplayName|Contagem|Média|O número de fluxos de GRPC que foram fechados para o serviço de entrega.|Nó|
|DeliverStreamsOpened|Yes|DeliverStreamsOpenedDisplayName|Contagem|Média|O número de fluxos de GRPC que foram abertos para o serviço de entrega.|Nó|
|EndorserChaincodeInstantiationFailures|Yes|EndorserChaincodeInstantiationFailuresDisplayName|Contagem|Média|O número de instanciações chaincode ou atualização que falharam.|Nó, canal, chaincode|
|EndorserDuplicateTransactionFailures|Yes|EndorserDuplicateTransactionFailuresDisplayName|Contagem|Média|O número de propostas com falha devido à ID de transação duplicada.|Nó, canal, chaincode|
|EndorserEndorsementFailures|Yes|EndorserEndorsementFailuresDisplayName|Contagem|Média|O número de endossos com falha.|Nó, canal, chaincode, chaincodeerror|
|EndorserProposalAclFailures|Yes|EndorserProposalAclFailuresDisplayName|Contagem|Média|O número de propostas que falharam verificações de ACL.|Nó, canal, chaincode|
|EndorserProposalSimulationFailures|Yes|EndorserProposalSimulationFailuresDisplayName|Contagem|Média|O número de simulações de proposta com falha.|Nó, canal, chaincode|
|EndorserProposalsReceived|Yes|EndorserProposalsReceivedDisplayName|Contagem|Média|O número de propostas recebidas.|Nó|
|EndorserProposalValidationFailures|Yes|EndorserProposalValidationFailuresDisplayName|Contagem|Média|O número de propostas que falharam na validação inicial.|Nó|
|EndorserSuccessfulProposals|Yes|EndorserSuccessfulProposalsDisplayName|Contagem|Média|O número de propostas bem-sucedidas.|Nó|
|FabricVersion|Yes|FabricVersionDisplayName|Contagem|Média|A versão ativa da malha.|Nó, versão|
|GossipCommMessagesReceived|Yes|GossipCommMessagesReceivedDisplayName|Contagem|Média|Número de mensagens recebidas.|Nó|
|GossipCommMessagesSent|Yes|GossipCommMessagesSentDisplayName|Contagem|Média|Número de mensagens enviadas.|Nó|
|GossipCommOverflowCount|Yes|GossipCommOverflowCountDisplayName|Contagem|Média|Número de estouros de buffer de fila de saída.|Nó|
|GossipLeaderElectionLeader|Yes|GossipLeaderElectionLeaderDisplayName|Contagem|Média|O par é líder (1) ou seguidor (0).|Nó, canal|
|GossipMembershipTotalPeersKnown|Yes|GossipMembershipTotalPeersKnownDisplayName|Contagem|Média|Total de pares conhecidos.|Nó, canal|
|GossipPayloadBufferSize|Yes|GossipPayloadBufferSizeDisplayName|Contagem|Média|Tamanho do buffer de carga.|Nó, canal|
|GossipStateHeight|Yes|GossipStateHeightDisplayName|Contagem|Média|Altura do razão atual.|Nó, canal|
|GrpcCommConnClosed|Yes|GrpcCommConnClosedDisplayName|Contagem|Média|conexões gRPC fechadas. Abrir menos fechado é o número ativo de conexões.|Nó|
|GrpcCommConnOpened|Yes|GrpcCommConnOpenedDisplayName|Contagem|Média|conexões gRPC abertas. Abrir menos fechado é o número ativo de conexões.|Nó|
|GrpcServerStreamMessagesReceived|Yes|GrpcServerStreamMessagesReceivedDisplayName|Contagem|Média|O número de mensagens de fluxo recebidas.|Nó, serviço, método|
|GrpcServerStreamMessagesSent|Yes|GrpcServerStreamMessagesSentDisplayName|Contagem|Média|O número de mensagens de fluxo enviadas.|Nó, serviço, método|
|GrpcServerStreamRequestsCompleted|Yes|GrpcServerStreamRequestsCompletedDisplayName|Contagem|Média|O número de solicitações de fluxo concluídas.|Nó, serviço, método, código|
|GrpcServerUnaryRequestsReceived|Yes|GrpcServerUnaryRequestsReceivedDisplayName|Contagem|Média|O número de solicitações unários recebidas.|Nó, serviço, método|
|IOReadBytes|Yes|Bytes de Leitura de E/S|Bytes|Total|Bytes de Leitura de E/S|Nó|
|IOWriteBytes|Yes|Bytes de Gravação de E/S|Bytes|Total|Bytes de Gravação de E/S|Nó|
|LedgerBlockchainHeight|Yes|LedgerBlockchainHeightDisplayName|Contagem|Média|Altura da cadeia em blocos.|Nó, canal|
|LedgerTransactionCount|Yes|LedgerTransactionCountDisplayName|Contagem|Média|Número de transações processadas.|Nó, canal, transaction_type, chaincode, validation_code|
|LoggingEntriesChecked|Yes|LoggingEntriesCheckedDisplayName|Contagem|Média|Número de entradas de log verificadas no nível de log ativo.|Nó, nível|
|LoggingEntriesWritten|Yes|LoggingEntriesWrittenDisplayName|Contagem|Média|Número de entradas de log que são gravadas.|Nó, nível|
|MemoryLimit|Yes|Limite de Memória|Bytes|Média|Limite de Memória|Nó|
|MemoryUsage|Yes|Uso de Memória|Bytes|Média|Uso de Memória|Nó|
|MemoryUsagePercentageInDouble|Yes|Percentual de Uso de Memória|Porcentagem|Média|Percentual de Uso de Memória|Nó|
|PendingTransactions|Yes|Transações Pendentes|Contagem|Média|Transações Pendentes|Nó|
|ProcessedBlocks|Yes|Blocos Processados|Contagem|Total|Blocos Processados|Nó|
|ProcessedTransactions|Yes|Transações Processadas|Contagem|Total|Transações Processadas|Nó|
|QueuedTransactions|Yes|Transações em Fila|Contagem|Média|Transações em Fila|Nó|
|RequestHandled|Yes|Solicitações Manipuladas|Contagem|Total|Solicitações Manipuladas|Nó|
|StorageUsage|Yes|Uso de Armazenamento|Bytes|Média|Uso de Armazenamento|Nó|


## <a name="microsoftbotservicebotservices"></a>microsoft.botservice/botservices

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|RequestLatency|Yes|Latência de solicitação|Milissegundos|Total|Tempo gasto pelo servidor para processar a solicitação|Operação, autenticação, protocolo|
|RequestsTraffic|Yes|Solicita tráfego|Porcentagem|Contagem|Número de solicitações feitas|Operação, autenticação, protocolo, StatusCode, StatusCodeClass|


## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|allcachehits|Yes|Acertos de cache (baseado em instância)|Contagem|Total||Fragmentoid, porta, primário|
|allcachemisses|Yes|Erros de cache (baseado em instância)|Contagem|Total||Fragmentoid, porta, primário|
|allcacheRead|Yes|Leitura de cache (baseada em instância)|BytesPerSecond|Máximo||Fragmentoid, porta, primário|
|allcacheWrite|Yes|Gravação em cache (baseada em instância)|BytesPerSecond|Máximo||Fragmentoid, porta, primário|
|allconnectedclients|Yes|Clientes conectados (baseados em instância)|Contagem|Máximo||Fragmentoid, porta, primário|
|allevictedkeys|Yes|Chaves removidas (baseadas em instância)|Contagem|Total||Fragmentoid, porta, primário|
|allexpiredkeys|Yes|Chaves expiradas (baseadas em instância)|Contagem|Total||Fragmentoid, porta, primário|
|allgetcommands|Yes|Gets (baseado em instância)|Contagem|Total||Fragmentoid, porta, primário|
|alloperationsPerSecond|Yes|Operações por segundo (baseadas em instância)|Contagem|Máximo||Fragmentoid, porta, primário|
|allserverLoad|Yes|Carga do servidor (baseada em instância)|Porcentagem|Máximo||Fragmentoid, porta, primário|
|allsetcommands|Yes|Conjuntos (baseados em instância)|Contagem|Total||Fragmentoid, porta, primário|
|alltotalcommandsprocessed|Yes|Total de operações (baseadas em instância)|Contagem|Total||Fragmentoid, porta, primário|
|alltotalkeys|Yes|Total de chaves (baseadas em instância)|Contagem|Máximo||Fragmentoid, porta, primário|
|allusedmemory|Yes|Memória usada (baseada em instância)|Bytes|Máximo||Fragmentoid, porta, primário|
|allusedmemorypercentage|Yes|Porcentagem de memória usada (baseada em instância)|Porcentagem|Máximo||Fragmentoid, porta, primário|
|allusedmemoryRss|Yes|Memória RSS usada (baseada em instância)|Bytes|Máximo||Fragmentoid, porta, primário|
|cachehits|Yes|Acertos do Cache|Contagem|Total||ShardId|
|cachehits0|Yes|Ocorrências no cache (Fragmento 0)|Contagem|Total||Sem dimensões|
|cachehits1|Yes|Ocorrências no cache (Fragmento 1)|Contagem|Total||Sem dimensões|
|cachehits2|Yes|Ocorrências no cache (Fragmento 2)|Contagem|Total||Sem dimensões|
|cachehits3|Yes|Ocorrências no cache (Fragmento 3)|Contagem|Total||Sem dimensões|
|cachehits4|Yes|Ocorrências no cache (Fragmento 4)|Contagem|Total||Sem dimensões|
|cachehits5|Yes|Ocorrências no cache (Fragmento 5)|Contagem|Total||Sem dimensões|
|cachehits6|Yes|Ocorrências no cache (Fragmento 6)|Contagem|Total||Sem dimensões|
|cachehits7|Yes|Ocorrências no cache (Fragmento 7)|Contagem|Total||Sem dimensões|
|cachehits8|Yes|Ocorrências no cache (Fragmento 8)|Contagem|Total||Sem dimensões|
|cachehits9|Yes|Ocorrências no cache (Fragmento 9)|Contagem|Total||Sem dimensões|
|cacheLatency|Yes|Microssegundos de latência de cache (versão prévia)|Contagem|Média||ShardId|
|cachemisses|Yes|Erros de Cache|Contagem|Total||ShardId|
|cachemisses0|Yes|Perdas no cache (Fragmento 0)|Contagem|Total||Sem dimensões|
|cachemisses1|Yes|Perdas no cache (Fragmento 1)|Contagem|Total||Sem dimensões|
|cachemisses2|Yes|Perdas no cache (Fragmento 2)|Contagem|Total||Sem dimensões|
|cachemisses3|Yes|Perdas no cache (Fragmento 3)|Contagem|Total||Sem dimensões|
|cachemisses4|Yes|Perdas no cache (Fragmento 4)|Contagem|Total||Sem dimensões|
|cachemisses5|Yes|Perdas no cache (Fragmento 5)|Contagem|Total||Sem dimensões|
|cachemisses6|Yes|Perdas no cache (Fragmento 6)|Contagem|Total||Sem dimensões|
|cachemisses7|Yes|Perdas no cache (Fragmento 7)|Contagem|Total||Sem dimensões|
|cachemisses8|Yes|Perdas no cache (Fragmento 8)|Contagem|Total||Sem dimensões|
|cachemisses9|Yes|Perdas no cache (Fragmento 9)|Contagem|Total||Sem dimensões|
|cachemissrate|Yes|Taxa de erros de cache|Porcentagem|cachemissrate||ShardId|
|cacheRead|Yes|Cache Lido|BytesPerSecond|Máximo||ShardId|
|cacheRead0|Yes|Leitura no cache (Fragmento 0)|BytesPerSecond|Máximo||Sem dimensões|
|cacheRead1|Yes|Leitura no cache (Fragmento 1)|BytesPerSecond|Máximo||Sem dimensões|
|cacheRead2|Yes|Leitura no cache (Fragmento 2)|BytesPerSecond|Máximo||Sem dimensões|
|cacheRead3|Yes|Leitura no cache (Fragmento 3)|BytesPerSecond|Máximo||Sem dimensões|
|cacheRead4|Yes|Leitura no cache (Fragmento 4)|BytesPerSecond|Máximo||Sem dimensões|
|cacheRead5|Yes|Leitura no cache (Fragmento 5)|BytesPerSecond|Máximo||Sem dimensões|
|cacheRead6|Yes|Leitura no cache (Fragmento 6)|BytesPerSecond|Máximo||Sem dimensões|
|cacheRead7|Yes|Leitura no cache (Fragmento 7)|BytesPerSecond|Máximo||Sem dimensões|
|cacheRead8|Yes|Leitura no cache (Fragmento 8)|BytesPerSecond|Máximo||Sem dimensões|
|cacheRead9|Yes|Leitura no cache (Fragmento 9)|BytesPerSecond|Máximo||Sem dimensões|
|cacheWrite|Yes|Gravação no Cache|BytesPerSecond|Máximo||ShardId|
|cacheWrite0|Yes|Gravação no cache (Fragmento 0)|BytesPerSecond|Máximo||Sem dimensões|
|cacheWrite1|Yes|Gravação no cache (Fragmento 1)|BytesPerSecond|Máximo||Sem dimensões|
|cacheWrite2|Yes|Gravação no cache (Fragmento 2)|BytesPerSecond|Máximo||Sem dimensões|
|cacheWrite3|Yes|Gravação no cache (Fragmento 3)|BytesPerSecond|Máximo||Sem dimensões|
|cacheWrite4|Yes|Gravação no cache (Fragmento 4)|BytesPerSecond|Máximo||Sem dimensões|
|cacheWrite5|Yes|Gravação no cache (Fragmento 5)|BytesPerSecond|Máximo||Sem dimensões|
|cacheWrite6|Yes|Gravação no cache (Fragmento 6)|BytesPerSecond|Máximo||Sem dimensões|
|cacheWrite7|Yes|Gravação no cache (Fragmento 7)|BytesPerSecond|Máximo||Sem dimensões|
|cacheWrite8|Yes|Gravação no cache (Fragmento 8)|BytesPerSecond|Máximo||Sem dimensões|
|cacheWrite9|Yes|Gravação no cache (Fragmento 9)|BytesPerSecond|Máximo||Sem dimensões|
|connectedclients|Yes|Clientes conectados|Contagem|Máximo||ShardId|
|connectedclients0|Yes|Clientes conectados (Fragmento 0)|Contagem|Máximo||Sem dimensões|
|connectedclients1|Yes|Clientes conectados (Fragmento 1)|Contagem|Máximo||Sem dimensões|
|connectedclients2|Yes|Clientes conectados (Fragmento 2)|Contagem|Máximo||Sem dimensões|
|connectedclients3|Yes|Clientes conectados (Fragmento 3)|Contagem|Máximo||Sem dimensões|
|connectedclients4|Yes|Clientes conectados (Fragmento 4)|Contagem|Máximo||Sem dimensões|
|connectedclients5|Yes|Clientes conectados (Fragmento 5)|Contagem|Máximo||Sem dimensões|
|connectedclients6|Yes|Clientes conectados (Fragmento 6)|Contagem|Máximo||Sem dimensões|
|connectedclients7|Yes|Clientes conectados (Fragmento 7)|Contagem|Máximo||Sem dimensões|
|connectedclients8|Yes|Clientes conectados (Fragmento 8)|Contagem|Máximo||Sem dimensões|
|connectedclients9|Yes|Clientes conectados (Fragmento 9)|Contagem|Máximo||Sem dimensões|
|erros|Yes|Errors|Contagem|Máximo||ShardId, ErrorType|
|evictedkeys|Yes|Chaves removidas|Contagem|Total||ShardId|
|evictedkeys0|Yes|Chaves removidas (Fragmento 0)|Contagem|Total||Sem dimensões|
|evictedkeys1|Yes|Chaves removidas (Fragmento 1)|Contagem|Total||Sem dimensões|
|evictedkeys2|Yes|Chaves removidas (Fragmento 2)|Contagem|Total||Sem dimensões|
|evictedkeys3|Yes|Chaves removidas (Fragmento 3)|Contagem|Total||Sem dimensões|
|evictedkeys4|Yes|Chaves removidas (Fragmento 4)|Contagem|Total||Sem dimensões|
|evictedkeys5|Yes|Chaves removidas (Fragmento 5)|Contagem|Total||Sem dimensões|
|evictedkeys6|Yes|Chaves removidas (Fragmento 6)|Contagem|Total||Sem dimensões|
|evictedkeys7|Yes|Chaves removidas (Fragmento 7)|Contagem|Total||Sem dimensões|
|evictedkeys8|Yes|Chaves removidas (Fragmento 8)|Contagem|Total||Sem dimensões|
|evictedkeys9|Yes|Chaves removidas (Fragmento 9)|Contagem|Total||Sem dimensões|
|expiredkeys|Yes|Chaves expiradas|Contagem|Total||ShardId|
|expiredkeys0|Yes|Chaves expiradas (Fragmento 0)|Contagem|Total||Sem dimensões|
|expiredkeys1|Yes|Chaves expiradas (Fragmento 1)|Contagem|Total||Sem dimensões|
|expiredkeys2|Yes|Chaves expiradas (Fragmento 2)|Contagem|Total||Sem dimensões|
|expiredkeys3|Yes|Chaves expiradas (Fragmento 3)|Contagem|Total||Sem dimensões|
|expiredkeys4|Yes|Chaves expiradas (Fragmento 4)|Contagem|Total||Sem dimensões|
|expiredkeys5|Yes|Chaves expiradas (Fragmento 5)|Contagem|Total||Sem dimensões|
|expiredkeys6|Yes|Chaves expiradas (Fragmento 6)|Contagem|Total||Sem dimensões|
|expiredkeys7|Yes|Chaves expiradas (Fragmento 7)|Contagem|Total||Sem dimensões|
|expiredkeys8|Yes|Chaves expiradas (Fragmento 8)|Contagem|Total||Sem dimensões|
|expiredkeys9|Yes|Chaves expiradas (Fragmento 9)|Contagem|Total||Sem dimensões|
|getcommands|Yes|Gets|Contagem|Total||ShardId|
|getcommands0|Yes|Gets (Fragmento 0)|Contagem|Total||Sem dimensões|
|getcommands1|Yes|Gets (Fragmento 1)|Contagem|Total||Sem dimensões|
|getcommands2|Yes|Gets (Fragmento 2)|Contagem|Total||Sem dimensões|
|getcommands3|Yes|Gets (Fragmento 3)|Contagem|Total||Sem dimensões|
|getcommands4|Yes|Gets (Fragmento 4)|Contagem|Total||Sem dimensões|
|getcommands5|Yes|Gets (Fragmento 5)|Contagem|Total||Sem dimensões|
|getcommands6|Yes|Gets (Fragmento 6)|Contagem|Total||Sem dimensões|
|getcommands7|Yes|Gets (Fragmento 7)|Contagem|Total||Sem dimensões|
|getcommands8|Yes|Gets (Fragmento 8)|Contagem|Total||Sem dimensões|
|getcommands9|Yes|Gets (Fragmento 9)|Contagem|Total||Sem dimensões|
|operationsPerSecond|Yes|Operations por segundo|Contagem|Máximo||ShardId|
|operationsPerSecond0|Yes|Operações por segundo (fragmento 0)|Contagem|Máximo||Sem dimensões|
|operationsPerSecond1|Yes|Operações por segundo (fragmento 1)|Contagem|Máximo||Sem dimensões|
|operationsPerSecond2|Yes|Operações por segundo (fragmento 2)|Contagem|Máximo||Sem dimensões|
|operationsPerSecond3|Yes|Operações por segundo (fragmento 3)|Contagem|Máximo||Sem dimensões|
|operationsPerSecond4|Yes|Operações por segundo (fragmento 4)|Contagem|Máximo||Sem dimensões|
|operationsPerSecond5|Yes|Operações por segundo (fragmento 5)|Contagem|Máximo||Sem dimensões|
|operationsPerSecond6|Yes|Operações por segundo (fragmento 6)|Contagem|Máximo||Sem dimensões|
|operationsPerSecond7|Yes|Operações por segundo (fragmento 7)|Contagem|Máximo||Sem dimensões|
|operationsPerSecond8|Yes|Operações por segundo (fragmento 8)|Contagem|Máximo||Sem dimensões|
|operationsPerSecond9|Yes|Operações por segundo (fragmento 9)|Contagem|Máximo||Sem dimensões|
|percentProcessorTime|Sim|CPU|Porcentagem|Máximo||ShardId|
|percentProcessorTime0|Yes|CPU (Fragmento 0)|Porcentagem|Máximo||Sem dimensões|
|percentProcessorTime1|Yes|CPU (Fragmento 1)|Porcentagem|Máximo||Sem dimensões|
|percentProcessorTime2|Yes|CPU (Fragmento 2)|Porcentagem|Máximo||Sem dimensões|
|percentProcessorTime3|Yes|CPU (Fragmento 3)|Porcentagem|Máximo||Sem dimensões|
|percentProcessorTime4|Yes|CPU (Fragmento 4)|Porcentagem|Máximo||Sem dimensões|
|percentProcessorTime5|Yes|CPU (Fragmento 5)|Porcentagem|Máximo||Sem dimensões|
|percentProcessorTime6|Yes|CPU (Fragmento 6)|Porcentagem|Máximo||Sem dimensões|
|percentProcessorTime7|Yes|CPU (Fragmento 7)|Porcentagem|Máximo||Sem dimensões|
|percentProcessorTime8|Yes|CPU (Fragmento 8)|Porcentagem|Máximo||Sem dimensões|
|percentProcessorTime9|Yes|CPU (Fragmento 9)|Porcentagem|Máximo||Sem dimensões|
|serverLoad|Yes|Carga do Servidor|Porcentagem|Máximo||ShardId|
|serverLoad0|Yes|Carga do servidor (Fragmento 0)|Porcentagem|Máximo||Sem dimensões|
|serverLoad1|Yes|Carga do servidor (Fragmento 1)|Porcentagem|Máximo||Sem dimensões|
|serverLoad2|Yes|Carga do servidor (Fragmento 2)|Porcentagem|Máximo||Sem dimensões|
|serverLoad3|Yes|Carga do servidor (Fragmento 3)|Porcentagem|Máximo||Sem dimensões|
|serverLoad4|Yes|Carga do servidor (Fragmento 4)|Porcentagem|Máximo||Sem dimensões|
|serverLoad5|Yes|Carga do servidor (Fragmento 5)|Porcentagem|Máximo||Sem dimensões|
|serverLoad6|Yes|Carga do servidor (Fragmento 6)|Porcentagem|Máximo||Sem dimensões|
|serverLoad7|Yes|Carga do servidor (Fragmento 7)|Porcentagem|Máximo||Sem dimensões|
|serverLoad8|Yes|Carga do servidor (Fragmento 8)|Porcentagem|Máximo||Sem dimensões|
|serverLoad9|Yes|Carga do servidor (Fragmento 9)|Porcentagem|Máximo||Sem dimensões|
|setcommands|Yes|Conjuntos|Contagem|Total||ShardId|
|setcommands0|Yes|Sets (Fragmento 0)|Contagem|Total||Sem dimensões|
|setcommands1|Yes|Sets (Fragmento 1)|Contagem|Total||Sem dimensões|
|setcommands2|Yes|Sets (Fragmento 2)|Contagem|Total||Sem dimensões|
|setcommands3|Yes|Sets (Fragmento 3)|Contagem|Total||Sem dimensões|
|setcommands4|Yes|Sets (Fragmento 4)|Contagem|Total||Sem dimensões|
|setcommands5|Yes|Sets (Fragmento 5)|Contagem|Total||Sem dimensões|
|setcommands6|Yes|Sets (Fragmento 6)|Contagem|Total||Sem dimensões|
|setcommands7|Yes|Sets (Fragmento 7)|Contagem|Total||Sem dimensões|
|setcommands8|Yes|Sets (Fragmento 8)|Contagem|Total||Sem dimensões|
|setcommands9|Yes|Sets (Fragmento 9)|Contagem|Total||Sem dimensões|
|totalcommandsprocessed|Yes|Total de Operações|Contagem|Total||ShardId|
|totalcommandsprocessed0|Yes|Total de operações (Fragmento 0)|Contagem|Total||Sem dimensões|
|totalcommandsprocessed1|Yes|Total de operações (Fragmento 1)|Contagem|Total||Sem dimensões|
|totalcommandsprocessed2|Yes|Total de operações (Fragmento 2)|Contagem|Total||Sem dimensões|
|totalcommandsprocessed3|Yes|Total de operações (Fragmento 3)|Contagem|Total||Sem dimensões|
|totalcommandsprocessed4|Yes|Total de operações (Fragmento 4)|Contagem|Total||Sem dimensões|
|totalcommandsprocessed5|Yes|Total de operações (Fragmento 5)|Contagem|Total||Sem dimensões|
|totalcommandsprocessed6|Yes|Total de operações (Fragmento 6)|Contagem|Total||Sem dimensões|
|totalcommandsprocessed7|Yes|Total de operações (Fragmento 7)|Contagem|Total||Sem dimensões|
|totalcommandsprocessed8|Yes|Total de operações (Fragmento 8)|Contagem|Total||Sem dimensões|
|totalcommandsprocessed9|Yes|Total de operações (Fragmento 9)|Contagem|Total||Sem dimensões|
|totalkeys|Yes|Total de chaves|Contagem|Máximo||ShardId|
|totalkeys0|Yes|Total de Chaves (Fragmento 0)|Contagem|Máximo||Sem dimensões|
|totalkeys1|Yes|Total de Chaves (Fragmento 1)|Contagem|Máximo||Sem dimensões|
|totalkeys2|Yes|Total de Chaves (Fragmento 2)|Contagem|Máximo||Sem dimensões|
|totalkeys3|Yes|Total de Chaves (Fragmento 3)|Contagem|Máximo||Sem dimensões|
|totalkeys4|Yes|Total de Chaves (Fragmento 4)|Contagem|Máximo||Sem dimensões|
|totalkeys5|Yes|Total de Chaves (Fragmento 5)|Contagem|Máximo||Sem dimensões|
|totalkeys6|Yes|Total de Chaves (Fragmento 6)|Contagem|Máximo||Sem dimensões|
|totalkeys7|Yes|Total de Chaves (Fragmento 7)|Contagem|Máximo||Sem dimensões|
|totalkeys8|Yes|Total de Chaves (Fragmento 8)|Contagem|Máximo||Sem dimensões|
|totalkeys9|Yes|Total de Chaves (Fragmento 9)|Contagem|Máximo||Sem dimensões|
|usedmemory|Yes|Memória Usada|Bytes|Máximo||ShardId|
|usedmemory0|Yes|Memória usada (Fragmento 0)|Bytes|Máximo||Sem dimensões|
|usedmemory1|Yes|Memória usada (Fragmento 1)|Bytes|Máximo||Sem dimensões|
|usedmemory2|Yes|Memória usada (Fragmento 2)|Bytes|Máximo||Sem dimensões|
|usedmemory3|Yes|Memória usada (Fragmento 3)|Bytes|Máximo||Sem dimensões|
|usedmemory4|Yes|Memória usada (Fragmento 4)|Bytes|Máximo||Sem dimensões|
|usedmemory5|Yes|Memória usada (Fragmento 5)|Bytes|Máximo||Sem dimensões|
|usedmemory6|Yes|Memória usada (Fragmento 6)|Bytes|Máximo||Sem dimensões|
|usedmemory7|Yes|Memória usada (Fragmento 7)|Bytes|Máximo||Sem dimensões|
|usedmemory8|Yes|Memória usada (Fragmento 8)|Bytes|Máximo||Sem dimensões|
|usedmemory9|Yes|Memória usada (Fragmento 9)|Bytes|Máximo||Sem dimensões|
|usedmemorypercentage|Yes|Porcentagem de memória utilizada|Porcentagem|Máximo||ShardId|
|usedmemoryRss|Yes|Memória RSS Usada|Bytes|Máximo||ShardId|
|usedmemoryRss0|Yes|Memória RSS usada (Fragmento 0)|Bytes|Máximo||Sem dimensões|
|usedmemoryRss1|Yes|Memória RSS usada (Fragmento 1)|Bytes|Máximo||Sem dimensões|
|usedmemoryRss2|Yes|Memória RSS usada (Fragmento 2)|Bytes|Máximo||Sem dimensões|
|usedmemoryRss3|Yes|Memória RSS usada (Fragmento 3)|Bytes|Máximo||Sem dimensões|
|usedmemoryRss4|Yes|Memória RSS usada (Fragmento 4)|Bytes|Máximo||Sem dimensões|
|usedmemoryRss5|Yes|Memória RSS usada (Fragmento 5)|Bytes|Máximo||Sem dimensões|
|usedmemoryRss6|Yes|Memória RSS usada (Fragmento 6)|Bytes|Máximo||Sem dimensões|
|usedmemoryRss7|Yes|Memória RSS usada (Fragmento 7)|Bytes|Máximo||Sem dimensões|
|usedmemoryRss8|Yes|Memória RSS usada (Fragmento 8)|Bytes|Máximo||Sem dimensões|
|usedmemoryRss9|Yes|Memória RSS usada (Fragmento 9)|Bytes|Máximo||Sem dimensões|


## <a name="microsoftcacheredisenterprise"></a>Microsoft. cache/redisEnterprise

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|cachehits|Yes|Acertos do Cache|Contagem|Total||Sem dimensões|
|cacheLatency|Yes|Microssegundos de latência de cache (versão prévia)|Contagem|Média||InstanceId|
|cachemisses|Yes|Erros de Cache|Contagem|Total||InstanceId|
|cacheRead|Yes|Cache Lido|BytesPerSecond|Máximo||InstanceId|
|cacheWrite|Yes|Gravação no Cache|BytesPerSecond|Máximo||InstanceId|
|connectedclients|Yes|Clientes conectados|Contagem|Máximo||InstanceId|
|erros|Yes|Errors|Contagem|Máximo||InstanceId, ErrorType|
|evictedkeys|Yes|Chaves removidas|Contagem|Total||Sem dimensões|
|expiredkeys|Yes|Chaves expiradas|Contagem|Total||Sem dimensões|
|getcommands|Yes|Gets|Contagem|Total||Sem dimensões|
|operationsPerSecond|Yes|Operations por segundo|Contagem|Máximo||Sem dimensões|
|percentProcessorTime|Sim|CPU|Porcentagem|Máximo||InstanceId|
|serverLoad|Yes|Carga do Servidor|Porcentagem|Máximo||Sem dimensões|
|setcommands|Yes|Conjuntos|Contagem|Total||Sem dimensões|
|totalcommandsprocessed|Yes|Total de Operações|Contagem|Total||Sem dimensões|
|totalkeys|Yes|Total de chaves|Contagem|Máximo||Sem dimensões|
|usedmemory|Yes|Memória Usada|Bytes|Máximo||Sem dimensões|
|usedmemorypercentage|Yes|Porcentagem de memória utilizada|Porcentagem|Máximo||InstanceId|
|usedmemoryRss|Yes|Memória RSS Usada|Bytes|Máximo||InstanceId|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/cdnwebapplicationfirewallpolicies

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Yes|Contagem de solicitações do Firewall de Aplicativo Web|Contagem|Total|O número de solicitações de cliente processadas pelo Firewall do Aplicativo Web|PolicyName, RuleName, Action|


## <a name="microsoftcdnprofiles"></a>Microsoft.Cdn/profiles

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ByteHitRatio|Yes|Taxa de acertos de bytes|Porcentagem|Média|Esta é a taxa do total de bytes atendidos do cache em comparação com o total de bytes de resposta|Ponto de extremidade|
|OriginHealthPercentage|Yes|Porcentagem de integridade da origem|Porcentagem|Média|A porcentagem de investigações de integridade bem-sucedidas de AFDX para back-ends.|Origem, OriginPool|
|OriginLatency|Yes|Latência de origem|MilliSeconds|Média|O tempo calculado a partir de quando a solicitação foi enviada pelo AFDX Edge para o back-end até que AFDX recebeu o último byte de resposta do back-end.|Origem, ponto de extremidade|
|OriginRequestCount|Yes|Contagem de solicitação de origem|Contagem|Total|O número de solicitações enviadas do AFDX para a origem.|HttpStatus, HttpStatusGroup, origem, ponto de extremidade|
|Percentage4XX|Yes|Percentual de 4XX|Porcentagem|Média|A porcentagem de todas as solicitações de cliente para as quais o código de status de resposta é 4XX|Ponto de extremidade, ClientRegion, ClientCountry|
|Percentage5XX|Yes|Percentual do 5XX|Porcentagem|Média|A porcentagem de todas as solicitações de cliente para as quais o código de status de resposta é 5XX|Ponto de extremidade, ClientRegion, ClientCountry|
|RequestCount|Yes|Contagem de solicitações|Contagem|Total|O número de solicitações de cliente atendidas pelo proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, Endpoint|
|RequestSize|Yes|Tamanho da solicitação|Bytes|Total|O número de bytes enviados como solicitações de clientes a AFDX.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, Endpoint|
|ResponseSize|Yes|Tamanho da resposta|Bytes|Total|O número de bytes enviados como respostas do proxy HTTP/S para clientes|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, Endpoint|
|TotalLatency|Yes|Latência total|MilliSeconds|Média|O tempo calculado de quando a solicitação do cliente foi recebida pelo proxy HTTP/S até o cliente confirmar o último byte de resposta do proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, Endpoint|
|WebApplicationFirewallRequestCount|Yes|Contagem de solicitações do Firewall de Aplicativo Web|Contagem|Total|O número de solicitações de cliente processadas pelo Firewall do Aplicativo Web|PolicyName, RuleName, Action|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Disk Read Bytes/Sec|No|Leitura de disco|BytesPerSecond|Média|Média de bytes lidos do disco durante o período de monitoramento.|RoleInstanceId|
|Operações de leitura de disco/Seg|Yes|Operações de leitura de disco/Seg|CountPerSecond|Média|IOPS de leitura de disco.|RoleInstanceId|
|Disk Write Bytes/Sec|No|Gravação de disco|BytesPerSecond|Média|Média de bytes gravados em disco durante o período de monitoramento.|RoleInstanceId|
|Operações de gravação de disco/Seg|Yes|Operações de gravação de disco/Seg|CountPerSecond|Média|IOPS de gravação de disco.|RoleInstanceId|
|Entrada na rede|Yes|Entrada na rede|Bytes|Total|O número de bytes recebidos em todos os adaptadores de rede pelas Máquinas Virtuais (Tráfego de Entrada).|RoleInstanceId|
|Saída da rede|Yes|Saída da rede|Bytes|Total|O número de bytes de saída em todos os adaptadores de rede pelas Máquinas Virtuais (Tráfego de Saída).|RoleInstanceId|
|Porcentagem de CPU|Yes|Porcentagem de CPU|Porcentagem|Média|O percentual das unidades de computação alocadas que estão atualmente em uso pelas Máquinas Virtuais.|RoleInstanceId|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Disk Read Bytes/Sec|No|Leitura de disco|BytesPerSecond|Média|Média de bytes lidos do disco durante o período de monitoramento.|Sem dimensões|
|Operações de leitura de disco/Seg|Yes|Operações de leitura de disco/Seg|CountPerSecond|Média|IOPS de leitura de disco.|Sem dimensões|
|Disk Write Bytes/Sec|No|Gravação de disco|BytesPerSecond|Média|Média de bytes gravados em disco durante o período de monitoramento.|Sem dimensões|
|Operações de gravação de disco/Seg|Yes|Operações de gravação de disco/Seg|CountPerSecond|Média|IOPS de gravação de disco.|Sem dimensões|
|Entrada na rede|Yes|Entrada na rede|Bytes|Total|O número de bytes recebidos em todos os adaptadores de rede pelas Máquinas Virtuais (Tráfego de Entrada).|Sem dimensões|
|Saída da rede|Yes|Saída da rede|Bytes|Total|O número de bytes de saída em todos os adaptadores de rede pelas Máquinas Virtuais (Tráfego de Saída).|Sem dimensões|
|Porcentagem de CPU|Yes|Porcentagem de CPU|Porcentagem|Média|O percentual das unidades de computação alocadas que estão atualmente em uso pelas Máquinas Virtuais.|Sem dimensões|


## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft.ClassicStorage/storageAccounts

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Yes|Disponibilidade|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|GeoType, ApiName, Authentication|
|Saída|Yes|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável.|GeoType, ApiName, Authentication|
|Entrada|Yes|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Yes|Latência de E2E com Sucesso|Milissegundos|Média|A latência de ponta a ponta de solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Yes|Latência de Servidor Com Sucesso|Milissegundos|Média|A latência usada pelo armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Esse valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType, ApiName, Authentication|
|Transactions|Sim|Transactions|Contagem|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|ResponseType, GeoType, ApiName, Authentication|
|UsedCapacity|No|Capacidade utilizada|Bytes|Média|Capacidade utilizada pela conta|Sem dimensões|


## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft.ClassicStorage/storageAccounts/blobServices

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Yes|Disponibilidade|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|GeoType, ApiName, Authentication|
|BlobCapacity|No|Capacidade do Blob|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço Blob da conta de armazenamento em bytes.|BlobType, camada|
|BlobCount|No|Contagem de Blobs|Contagem|Média|O número de Blobs no serviço Blob da conta de armazenamento.|BlobType, camada|
|ContainerCount|Yes|Contagem de Contêineres de Blobs|Contagem|Média|O número de contêineres no serviço Blob da conta de armazenamento.|Sem dimensões|
|Saída|Yes|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável.|GeoType, ApiName, Authentication|
|IndexCapacity|No|Capacidade de Índice|Bytes|Média|A quantidade de armazenamento usada pelo índice ADLS Gen2 (hierárquico) em bytes.|Sem dimensões|
|Entrada|Yes|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Yes|Latência de E2E com Sucesso|Milissegundos|Média|A latência de ponta a ponta de solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Yes|Latência de Servidor Com Sucesso|Milissegundos|Média|A latência usada pelo armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Esse valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType, ApiName, Authentication|
|Transactions|Sim|Transactions|Contagem|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft.ClassicStorage/storageAccounts/fileServices

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Yes|Disponibilidade|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|Geotipo, ApiName, autenticação, FileShare|
|Saída|Yes|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável.|Geotipo, ApiName, autenticação, FileShare|
|FileCapacity|No|Capacidade do Arquivo|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço Arquivo da conta de armazenamento em bytes.|FileShare|
|FileCount|No|Contagem de Arquivos|Contagem|Média|O número de arquivos no serviço Arquivo da conta de armazenamento.|FileShare|
|FileShareCount|No|Contagem de Compartilhamentos de Arquivos|Contagem|Média|O número de compartilhamentos de arquivos no serviço Arquivo da conta de armazenamento.|Sem dimensões|
|FileShareQuota|No|Tamanho da cota do compartilhamento de arquivo|Bytes|Média|O limite superior da quantidade de armazenamento que pode ser usada pelo serviço de arquivos do Azure em bytes.|FileShare|
|FileShareSnapshotCount|No|Contagem de Instantâneos do Compartilhamento de Arquivo|Contagem|Média|O número de instantâneos presentes no serviço compartilhamento de arquivos da conta de armazenamento.|FileShare|
|FileShareSnapshotSize|No|Tamanho do Instantâneo do Compartilhamento de Arquivo|Bytes|Média|A quantidade de armazenamento usada pelos instantâneos no serviço de arquivo da conta de armazenamento, em bytes.|FileShare|
|Entrada|Yes|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|Geotipo, ApiName, autenticação, FileShare|
|SuccessE2ELatency|Yes|Latência de E2E com Sucesso|Milissegundos|Média|A latência de ponta a ponta de solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|Geotipo, ApiName, autenticação, FileShare|
|SuccessServerLatency|Yes|Latência de Servidor Com Sucesso|Milissegundos|Média|A latência usada pelo armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Esse valor não inclui a latência de rede especificada em SuccessE2ELatency.|Geotipo, ApiName, autenticação, FileShare|
|Transactions|Sim|Transactions|Contagem|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|ResponseType, GeoType, ApiName, autenticação, FileShare|


## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft.ClassicStorage/storageAccounts/queueServices

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Yes|Disponibilidade|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|GeoType, ApiName, Authentication|
|Saída|Yes|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável.|GeoType, ApiName, Authentication|
|Entrada|Yes|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|GeoType, ApiName, Authentication|
|QueueCapacity|Yes|Capacidade da Fila|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço Fila da conta de armazenamento em bytes.|Sem dimensões|
|QueueCount|Yes|Contagem de Filas|Contagem|Média|O número de filas no serviço Fila da conta de armazenamento.|Sem dimensões|
|QueueMessageCount|Yes|Contagem de Mensagens da Fila|Contagem|Média|O número aproximado de mensagens da fila no serviço Fila da conta de armazenamento.|Sem dimensões|
|SuccessE2ELatency|Yes|Latência de E2E com Sucesso|Milissegundos|Média|A latência de ponta a ponta de solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Yes|Latência de Servidor Com Sucesso|Milissegundos|Média|A latência usada pelo armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Esse valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType, ApiName, Authentication|
|Transactions|Sim|Transactions|Contagem|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft.ClassicStorage/storageAccounts/tableServices

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Yes|Disponibilidade|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|GeoType, ApiName, Authentication|
|Saída|Yes|Saída|Bytes|Total|A quantidade de dados de saída, em bytes. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável.|GeoType, ApiName, Authentication|
|Entrada|Yes|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Yes|Latência de E2E com Sucesso|Milissegundos|Média|A latência de ponta a ponta de solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Yes|Latência de Servidor Com Sucesso|Milissegundos|Média|A latência usada pelo armazenamento do Azure para processar uma solicitação bem-sucedida, em milissegundos. Esse valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType, ApiName, Authentication|
|TableCapacity|Yes|Capacidade da Tabela|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço Tabela da conta de armazenamento em bytes.|Sem dimensões|
|TableCount|Yes|Contagem de Tabelas|Contagem|Média|O número de tabelas no serviço Tabela da conta de armazenamento.|Sem dimensões|
|TableEntityCount|Yes|Contagem de Entidades de Tabela|Contagem|Média|O número de entidades de tabelas no serviço Tabela da conta de armazenamento.|Sem dimensões|
|Transactions|Sim|Transactions|Contagem|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|BlockedCalls|Yes|Chamadas Bloqueadas|Contagem|Total|Número de chamadas que excederam a taxa ou o limite de cota.|ApiName, OperationName, região|
|CharactersTrained|Yes|Caracteres Treinados|Contagem|Total|Número total de caracteres treinados.|ApiName, OperationName, região|
|CharactersTranslated|Yes|Caracteres traduzidos|Contagem|Total|Número total de caracteres na solicitação de texto de entrada.|ApiName, OperationName, região|
|ClientErrors|Yes|Erros do Cliente|Contagem|Total|Número de chamadas com erro do lado do cliente (código de resposta HTTP 4xx).|ApiName, OperationName, região|
|DataIn|Yes|Entrada de Dados|Bytes|Total|Tamanho dos dados de entrada em bytes.|ApiName, OperationName, região|
|DataOut|Yes|Saída de dados|Bytes|Total|Tamanho dos dados de saída em bytes.|ApiName, OperationName, região|
|Latency|Yes|Latency|MilliSeconds|Média|Latência em milissegundos.|ApiName, OperationName, região|
|LearnedEvents|Yes|Eventos aprendidos|Contagem|Total|Número de eventos aprendidos.|IsMatchBaseline, modo, RunId|
|MatchedRewards|Yes|Recompensas correspondentes|Contagem|Total| Número de recompensas correspondentes.|IsMatchBaseline, modo, RunId|
|ObservedRewards|Yes|Recompensas observadas|Contagem|Total|Número de recompensas observadas.|IsMatchBaseline, modo, RunId|
|ProcessedCharacters|Yes|Caracteres processados|Contagem|Total|Número de caracteres.|ApiName, FeatureName, UsageChannel, região|
|ProcessedTextRecords|Yes|Registros de texto processados|Contagem|Total|Contagem de registros de texto.|ApiName, FeatureName, UsageChannel, região|
|ServerErrors|Yes|Erros do Servidor|Contagem|Total|Número de chamadas com erro interno do serviço (código de resposta HTTP 5xx).|ApiName, OperationName, região|
|SpeechSessionDuration|Yes|Duração da Sessão de Fala|Segundos|Total|Duração total da sessão de fala em segundos.|ApiName, OperationName, região|
|SuccessfulCalls|Yes|Chamadas com Êxito|Contagem|Total|Número de chamadas com êxito.|ApiName, OperationName, região|
|TotalCalls|Yes|Total de Chamadas|Contagem|Total|Número total de chamadas.|ApiName, OperationName, região|
|TotalErrors|Yes|Total de Erros|Contagem|Total|Número total de chamadas com resposta de erro (código de resposta HTTP 4xx ou 5xx).|ApiName, OperationName, região|
|TotalTokenCalls|Yes|Total de chamadas de token|Contagem|Total|Número total de chamadas de token.|ApiName, OperationName, região|
|TotalTransactions|Yes|Total de Transações|Contagem|Total|Número total de transações.|Sem dimensões|


## <a name="microsoftcommunicationcommunicationservices"></a>Microsoft. Communication/Communications

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|APIRequestAuthentication|No|Solicitações de API de autenticação|Contagem|Contagem|Contagem de todas as solicitações no ponto de extremidade de autenticação dos serviços de comunicação.|Operação, StatusCode, StatusCodeClass|
|APIRequestChat|Yes|Solicitações da API de chat|Contagem|Contagem|Contagem de todas as solicitações no ponto de extremidade de bate-papo dos serviços de comunicação.|Operação, StatusCode, StatusCodeClass|
|APIRequestSMS|Yes|Solicitações de API do SMS|Contagem|Contagem|Contagem de todas as solicitações no ponto de extremidade de SMS dos serviços de comunicação.|Operação, StatusCode, StatusCodeClass|


## <a name="microsoftcomputecloudservices"></a>Microsoft. Compute/cloudservices

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Bytes de leitura de disco|Yes|Bytes de leitura de disco|Bytes|Total|Bytes lidos do disco durante o período de monitoramento|RoleInstanceId, RoleID|
|Operações de leitura de disco/Seg|Yes|Operações de leitura de disco/Seg|CountPerSecond|Média|IOPS de leitura de disco|RoleInstanceId, RoleID|
|Bytes de gravação de disco|Yes|Bytes de gravação de disco|Bytes|Total|Bytes gravados no disco durante o período de monitoramento|RoleInstanceId, RoleID|
|Operações de gravação de disco/Seg|Yes|Operações de gravação de disco/Seg|CountPerSecond|Média|IOPS de gravação de disco|RoleInstanceId, RoleID|
|Porcentagem de CPU|Yes|Porcentagem de CPU|Porcentagem|Média|A porcentagem das unidades de computação alocadas que estão atualmente em uso pela máquina virtual ou pelas máquinas virtuais|RoleInstanceId, RoleID|


## <a name="microsoftcomputecloudservicesroles"></a>Microsoft. Compute/cloudservices/Roles

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Bytes de leitura de disco|Yes|Bytes de leitura de disco|Bytes|Total|Bytes lidos do disco durante o período de monitoramento|RoleInstanceId, RoleID|
|Operações de leitura de disco/Seg|Yes|Operações de leitura de disco/Seg|CountPerSecond|Média|IOPS de leitura de disco|RoleInstanceId, RoleID|
|Bytes de gravação de disco|Yes|Bytes de gravação de disco|Bytes|Total|Bytes gravados no disco durante o período de monitoramento|RoleInstanceId, RoleID|
|Operações de gravação de disco/Seg|Yes|Operações de gravação de disco/Seg|CountPerSecond|Média|IOPS de gravação de disco|RoleInstanceId, RoleID|
|Porcentagem de CPU|Yes|Porcentagem de CPU|Porcentagem|Média|A porcentagem das unidades de computação alocadas que estão atualmente em uso pela máquina virtual ou pelas máquinas virtuais|RoleInstanceId, RoleID|


## <a name="microsoftcomputedisks"></a>microsoft.compute/disks

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Bytes de leitura do disco de composição/s|No|Bytes de leitura de disco/s (visualização)|Bytes|Média|Bytes/s lidos no disco durante o período de monitoramento, observe que essa métrica está em visualização e está sujeita a alterações antes de tornar-se disponível de forma geral||
|Operações de leitura de disco de composição/s|No|Operações de leitura de disco/s (visualização)|Bytes|Média|Número de IOs de leitura executados em um disco durante o período de monitoramento, observe que essa métrica está em visualização e está sujeita a alterações antes de tornar-se disponível de forma geral||
|Bytes de gravação do disco de composição/s|No|Bytes de gravação de disco/s (visualização)|Bytes|Média|Bytes/s gravados em disco durante o período de monitoramento, observe que essa métrica está em visualização e está sujeita a alterações antes de tornar-se disponível de forma geral||
|Operações de gravação no disco composto/s|No|Operações de gravação de disco/s (visualização)|Bytes|Média|Número de IOs de gravação executados em um disco durante o período de monitoramento, observe que essa métrica está em visualização e está sujeita a alterações antes de tornar-se disponível de forma geral||


## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Créditos de CPU Consumidos|Yes|Créditos de CPU Consumidos|Contagem|Média|Número total de créditos consumido pela Máquina Virtual|Sem dimensões|
|Créditos de CPU Restantes|Yes|Créditos de CPU Restantes|Contagem|Média|Número total de créditos disponíveis para intermitência|Sem dimensões|
|Porcentagem consumida da largura de banda do disco|Yes|Porcentagem consumida da largura de banda do disco|Porcentagem|Média|Porcentagem de largura de banda de disco de dados consumida por minuto|LUN|
|Percentual de IOPS consumido de disco de dados|Yes|Percentual de IOPS consumido de disco de dados|Porcentagem|Média|Porcentagem de e/SS de disco de dados consumidas por minuto|LUN|
|Largura de banda máxima de intermitência do disco de dados|Yes|Largura de banda máxima de intermitência do disco de dados|Contagem|Média|O disco de dados de taxa de transferência máximo de bytes por segundo pode alcançar com intermitência|LUN|
|IOPS de intermitência máxima do disco de dados|Yes|IOPS de intermitência máxima do disco de dados|Contagem|Média|O disco de dados de IOPS máximo pode atingir com intermitência|LUN|
|Profundidade da Fila de Disco de Dados|Yes|Profundidade da Fila de Disco de Dados|Contagem|Média|Profundidade da Fila do Disco de Dados (ou Tamanho da Fila)|LUN|
|Bytes de Leitura do Disco de Dados/s|Yes|Bytes de leitura do disco de dados/s|BytesPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento|LUN|
|Operações de Leitura do Disco de Dados/s|Yes|Operações de Leitura do Disco de Dados/s|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento|LUN|
|Largura de banda de destino do disco de dados|Yes|Largura de banda de destino do disco de dados|Contagem|Média|O disco de dados de taxa de transferência por segundo de bytes de linha de base pode atingir sem intermitência|LUN|
|IOPS de destino do disco de dados|Yes|IOPS de destino do disco de dados|Contagem|Média|O disco de dados de IOPS de linha de base pode atingir sem intermitência|LUN|
|Percentual de créditos de pico de BPS usados no disco de dados|Yes|Percentual de créditos de pico de BPS usados no disco de dados|Porcentagem|Média|Percentual de créditos de largura de banda de intermitência de disco de dados usados até agora|LUN|
|Percentual de créditos de e/s de disco de dados usados|Yes|Percentual de créditos de e/s de disco de dados usados|Porcentagem|Média|Porcentagem de créditos de e/s de intermitência de disco de dados usados até agora|LUN|
|Bytes de Gravação do Disco de Dados/s|Yes|Bytes de gravação do disco de dados/s|BytesPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento|LUN|
|Operações de Gravação do Disco de Dados/s|Yes|Operações de Gravação do Disco de Dados/s|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento|LUN|
|Bytes de leitura de disco|Yes|Bytes de leitura de disco|Bytes|Total|Bytes lidos do disco durante o período de monitoramento|Sem dimensões|
|Operações de leitura de disco/Seg|Yes|Operações de leitura de disco/Seg|CountPerSecond|Média|IOPS de leitura de disco|Sem dimensões|
|Bytes de gravação de disco|Yes|Bytes de gravação de disco|Bytes|Total|Bytes gravados no disco durante o período de monitoramento|Sem dimensões|
|Operações de gravação de disco/Seg|Yes|Operações de gravação de disco/Seg|CountPerSecond|Média|IOPS de gravação de disco|Sem dimensões|
|Fluxos de Entrada|Yes|Fluxos de Entrada|Contagem|Média|Os fluxos de entrada são o número de fluxos atuais na direção de entrada (tráfego entrando na VM)|Sem dimensões|
|Taxa Máxima de Criação de Fluxos de Entrada|Yes|Taxa Máxima de Criação de Fluxos de Entrada|CountPerSecond|Média|A taxa de criação máxima de fluxos de entrada (tráfego entrando na VM)|Sem dimensões|
|Entrada na rede|Yes|Rede em Faturável (Preterido)|Bytes|Total|O número de bytes faturáveis recebidos em todas as interfaces de rede pelas máquinas virtuais (tráfego de entrada) (preterido)|Sem dimensões|
|Rede no Total|Yes|Rede no Total|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela máquina virtual ou pelas máquinas virtuais (tráfego de entrada)|Sem dimensões|
|Saída da rede|Yes|Rede Fora de Faturável (Preterido)|Bytes|Total|O número de bytes faturáveis de saída em todas as interfaces de rede pelas máquinas virtuais (tráfego de saída) (preterido)|Sem dimensões|
|Rede Fora do Total|Yes|Rede Fora do Total|Bytes|Total|O número de bytes de saída em todas as interfaces de rede pela máquina virtual ou pelas máquinas virtuais (tráfego de saída)|Sem dimensões|
|Porcentagem consumida da largura de banda do disco|Yes|Porcentagem consumida da largura de banda do disco|Porcentagem|Média|Percentual de largura de banda de disco do sistema operacional consumido por minuto|LUN|
|Porcentagem consumida de IOPS de disco do so|Yes|Porcentagem consumida de IOPS de disco do so|Porcentagem|Média|Porcentagem de e/SS de disco do sistema operacional consumidas por minuto|LUN|
|Largura de banda máxima de intermitência do disco do so|Yes|Largura de banda máxima de intermitência do disco do so|Contagem|Média|Taxa de transferência máxima de bytes por segundo que o disco do so pode atingir com intermitência|LUN|
|IOPS de intermitência máxima do disco do so|Yes|IOPS de intermitência máxima do disco do so|Contagem|Média|O máximo de disco do sistema operacional de IOPS pode atingir com intermitência|LUN|
|Profundidade da Fila de Disco do SO|Yes|Profundidade da Fila de Disco do SO|Contagem|Média|Profundidade da Fila do Disco do SO (ou Tamanho da Fila)|Sem dimensões|
|Bytes de Leitura do Disco do SO/s|Yes|Bytes de Leitura do Disco do SO/s|BytesPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento para o disco do sistema operacional|Sem dimensões|
|Operações de Leitura do Disco do SO/s|Yes|Operações de Leitura do Disco do SO/s|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Sem dimensões|
|Largura de banda de destino do disco do so|Yes|Largura de banda de destino do disco do so|Contagem|Média|Taxa de transferência de bytes por segundo que o disco do sistema operacional pode atingir sem intermitência|LUN|
|IOPS de destino de disco do so|Yes|IOPS de destino de disco do so|Contagem|Média|O disco de sistema operacional IOPS de linha de base pode alcançar sem intermitência|LUN|
|Percentual de créditos de disparo de BPS de disco do so usados|Yes|Percentual de créditos de disparo de BPS de disco do so usados|Porcentagem|Média|Percentual de créditos de largura de banda de intermitência de disco do so usados até agora|LUN|
|Porcentagem de créditos de e/s de disco do so usados|Yes|Porcentagem de créditos de e/s de disco do so usados|Porcentagem|Média|Percentual de créditos de e/s de intermitência de disco do so usados até agora|LUN|
|Bytes de Gravação do Disco do SO/s|Yes|Bytes de gravação de disco do sistema operacional/s|BytesPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento do disco do sistema operacional|Sem dimensões|
|Operações de Gravação do Disco do SO/s|Yes|Operações de Gravação do Disco do SO/s|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Sem dimensões|
|Fluxos de Saída|Yes|Fluxos de Saída|Contagem|Média|Fluxos de saída são o número de fluxos atuais na direção de saída (tráfego saindo da VM)|Sem dimensões|
|Taxa Máxima de Criação de Fluxos de Saída|Yes|Taxa Máxima de Criação de Fluxos de Saída|CountPerSecond|Média|A taxa de criação máxima de fluxos de saída (tráfego saindo da VM)|Sem dimensões|
|Porcentagem de CPU|Yes|Porcentagem de CPU|Porcentagem|Média|A porcentagem das unidades de computação alocadas que estão atualmente em uso pela máquina virtual ou pelas máquinas virtuais|Sem dimensões|
|Ocorrência de Leitura do Cache de Disco de Dados Premium|Yes|Ocorrência de Leitura do Cache de Disco de Dados Premium|Porcentagem|Média|Ocorrência de Leitura do Cache de Disco de Dados Premium|LUN|
|Ausência de Leitura do Cache de Disco de Dados Premium|Yes|Ausência de Leitura do Cache de Disco de Dados Premium|Porcentagem|Média|Ausência de Leitura do Cache de Disco de Dados Premium|LUN|
|Ocorrência de Leitura do Cache de Disco do SO Premium|Yes|Ocorrência de Leitura do Cache de Disco do SO Premium|Porcentagem|Média|Ocorrência de Leitura do Cache de Disco do SO Premium|Sem dimensões|
|Ausência de Leitura do Cache de Disco de SO Premium|Yes|Ausência de Leitura do Cache de Disco de SO Premium|Porcentagem|Média|Ausência de Leitura do Cache de Disco de SO Premium|Sem dimensões|
|Porcentagem consumida da largura de banda em cache da VM|Yes|Porcentagem consumida da largura de banda em cache da VM|Porcentagem|Média|Porcentagem da largura de banda de disco armazenada em cache consumida pela VM|Sem dimensões|
|Porcentagem consumida de IOPS em cache da VM|Yes|Porcentagem consumida de IOPS em cache da VM|Porcentagem|Média|Porcentagem de IOPS de disco em cache consumida pela VM|Sem dimensões|
|Porcentagem consumida da largura de banda desarmazenada da VM|Yes|Porcentagem consumida da largura de banda desarmazenada da VM|Porcentagem|Média|Porcentagem de largura de banda de disco não armazenada em cache consumida pela VM|Sem dimensões|
|Porcentagem consumida de IOPS não armazenado em cache da VM|Yes|Porcentagem consumida de IOPS não armazenado em cache da VM|Porcentagem|Média|Percentual de IOPS de disco não armazenado em cache consumidos pela VM|Sem dimensões|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Créditos de CPU Consumidos|Yes|Créditos de CPU Consumidos|Contagem|Média|Número total de créditos consumido pela Máquina Virtual|Sem dimensões|
|Créditos de CPU Restantes|Yes|Créditos de CPU Restantes|Contagem|Média|Número total de créditos disponíveis para intermitência|Sem dimensões|
|Porcentagem consumida da largura de banda do disco|Yes|Porcentagem consumida da largura de banda do disco|Porcentagem|Média|Porcentagem de largura de banda de disco de dados consumida por minuto|LUN, VMName|
|Percentual de IOPS consumido de disco de dados|Yes|Percentual de IOPS consumido de disco de dados|Porcentagem|Média|Porcentagem de e/SS de disco de dados consumidas por minuto|LUN, VMName|
|Largura de banda máxima de intermitência do disco de dados|Yes|Largura de banda máxima de intermitência do disco de dados|Contagem|Média|O disco de dados de taxa de transferência máximo de bytes por segundo pode alcançar com intermitência|LUN, VMName|
|IOPS de intermitência máxima do disco de dados|Yes|IOPS de intermitência máxima do disco de dados|Contagem|Média|O disco de dados de IOPS máximo pode atingir com intermitência|LUN, VMName|
|Profundidade da Fila de Disco de Dados|Yes|Profundidade da Fila de Disco de Dados|Contagem|Média|Profundidade da Fila do Disco de Dados (ou Tamanho da Fila)|LUN, VMName|
|Bytes de Leitura do Disco de Dados/s|Yes|Bytes de leitura do disco de dados/s|BytesPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento|LUN, VMName|
|Operações de Leitura do Disco de Dados/s|Yes|Operações de Leitura do Disco de Dados/s|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento|LUN, VMName|
|Largura de banda de destino do disco de dados|Yes|Largura de banda de destino do disco de dados|Contagem|Média|O disco de dados de taxa de transferência por segundo de bytes de linha de base pode atingir sem intermitência|LUN, VMName|
|IOPS de destino do disco de dados|Yes|IOPS de destino do disco de dados|Contagem|Média|O disco de dados de IOPS de linha de base pode atingir sem intermitência|LUN, VMName|
|Percentual de créditos de pico de BPS usados no disco de dados|Yes|Percentual de créditos de pico de BPS usados no disco de dados|Porcentagem|Média|Percentual de créditos de largura de banda de intermitência de disco de dados usados até agora|LUN, VMName|
|Percentual de créditos de e/s de disco de dados usados|Yes|Percentual de créditos de e/s de disco de dados usados|Porcentagem|Média|Porcentagem de créditos de e/s de intermitência de disco de dados usados até agora|LUN, VMName|
|Bytes de Gravação do Disco de Dados/s|Yes|Bytes de gravação do disco de dados/s|BytesPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento|LUN, VMName|
|Operações de Gravação do Disco de Dados/s|Yes|Operações de Gravação do Disco de Dados/s|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento|LUN, VMName|
|Bytes de leitura de disco|Yes|Bytes de leitura de disco|Bytes|Total|Bytes lidos do disco durante o período de monitoramento|VMName|
|Operações de leitura de disco/Seg|Yes|Operações de leitura de disco/Seg|CountPerSecond|Média|IOPS de leitura de disco|VMName|
|Bytes de gravação de disco|Yes|Bytes de gravação de disco|Bytes|Total|Bytes gravados no disco durante o período de monitoramento|VMName|
|Operações de gravação de disco/Seg|Yes|Operações de gravação de disco/Seg|CountPerSecond|Média|IOPS de gravação de disco|VMName|
|Fluxos de Entrada|Yes|Fluxos de Entrada|Contagem|Média|Os fluxos de entrada são o número de fluxos atuais na direção de entrada (tráfego entrando na VM)|VMName|
|Taxa Máxima de Criação de Fluxos de Entrada|Yes|Taxa Máxima de Criação de Fluxos de Entrada|CountPerSecond|Média|A taxa de criação máxima de fluxos de entrada (tráfego entrando na VM)|VMName|
|Entrada na rede|Yes|Rede em Faturável (Preterido)|Bytes|Total|O número de bytes faturáveis recebidos em todas as interfaces de rede pelas máquinas virtuais (tráfego de entrada) (preterido)|VMName|
|Rede no Total|Yes|Rede no Total|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela máquina virtual ou pelas máquinas virtuais (tráfego de entrada)|VMName|
|Saída da rede|Yes|Rede Fora de Faturável (Preterido)|Bytes|Total|O número de bytes faturáveis de saída em todas as interfaces de rede pelas máquinas virtuais (tráfego de saída) (preterido)|VMName|
|Rede Fora do Total|Yes|Rede Fora do Total|Bytes|Total|O número de bytes de saída em todas as interfaces de rede pela máquina virtual ou pelas máquinas virtuais (tráfego de saída)|VMName|
|Porcentagem consumida da largura de banda do disco|Yes|Porcentagem consumida da largura de banda do disco|Porcentagem|Média|Percentual de largura de banda de disco do sistema operacional consumido por minuto|LUN, VMName|
|Porcentagem consumida de IOPS de disco do so|Yes|Porcentagem consumida de IOPS de disco do so|Porcentagem|Média|Porcentagem de e/SS de disco do sistema operacional consumidas por minuto|LUN, VMName|
|Largura de banda máxima de intermitência do disco do so|Yes|Largura de banda máxima de intermitência do disco do so|Contagem|Média|Taxa de transferência máxima de bytes por segundo que o disco do so pode atingir com intermitência|LUN, VMName|
|IOPS de intermitência máxima do disco do so|Yes|IOPS de intermitência máxima do disco do so|Contagem|Média|O máximo de disco do sistema operacional de IOPS pode atingir com intermitência|LUN, VMName|
|Profundidade da Fila de Disco do SO|Yes|Profundidade da Fila de Disco do SO|Contagem|Média|Profundidade da Fila do Disco do SO (ou Tamanho da Fila)|VMName|
|Bytes de Leitura do Disco do SO/s|Yes|Bytes de Leitura do Disco do SO/s|BytesPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento para o disco do sistema operacional|VMName|
|Operações de Leitura do Disco do SO/s|Yes|Operações de Leitura do Disco do SO/s|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|VMName|
|Largura de banda de destino do disco do so|Yes|Largura de banda de destino do disco do so|Contagem|Média|Taxa de transferência de bytes por segundo que o disco do sistema operacional pode atingir sem intermitência|LUN, VMName|
|IOPS de destino de disco do so|Yes|IOPS de destino de disco do so|Contagem|Média|O disco de sistema operacional IOPS de linha de base pode alcançar sem intermitência|LUN, VMName|
|Percentual de créditos de disparo de BPS de disco do so usados|Yes|Percentual de créditos de disparo de BPS de disco do so usados|Porcentagem|Média|Percentual de créditos de largura de banda de intermitência de disco do so usados até agora|LUN, VMName|
|Porcentagem de créditos de e/s de disco do so usados|Yes|Porcentagem de créditos de e/s de disco do so usados|Porcentagem|Média|Percentual de créditos de e/s de intermitência de disco do so usados até agora|LUN, VMName|
|Bytes de Gravação do Disco do SO/s|Yes|Bytes de gravação de disco do sistema operacional/s|BytesPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento do disco do sistema operacional|VMName|
|Operações de Gravação do Disco do SO/s|Yes|Operações de Gravação do Disco do SO/s|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|VMName|
|Fluxos de Saída|Yes|Fluxos de Saída|Contagem|Média|Fluxos de saída são o número de fluxos atuais na direção de saída (tráfego saindo da VM)|VMName|
|Taxa Máxima de Criação de Fluxos de Saída|Yes|Taxa Máxima de Criação de Fluxos de Saída|CountPerSecond|Média|A taxa de criação máxima de fluxos de saída (tráfego saindo da VM)|VMName|
|Porcentagem de CPU|Yes|Porcentagem de CPU|Porcentagem|Média|A porcentagem das unidades de computação alocadas que estão atualmente em uso pela máquina virtual ou pelas máquinas virtuais|VMName|
|Ocorrência de Leitura do Cache de Disco de Dados Premium|Yes|Ocorrência de Leitura do Cache de Disco de Dados Premium|Porcentagem|Média|Ocorrência de Leitura do Cache de Disco de Dados Premium|LUN, VMName|
|Ausência de Leitura do Cache de Disco de Dados Premium|Yes|Ausência de Leitura do Cache de Disco de Dados Premium|Porcentagem|Média|Ausência de Leitura do Cache de Disco de Dados Premium|LUN, VMName|
|Ocorrência de Leitura do Cache de Disco do SO Premium|Yes|Ocorrência de Leitura do Cache de Disco do SO Premium|Porcentagem|Média|Ocorrência de Leitura do Cache de Disco do SO Premium|VMName|
|Ausência de Leitura do Cache de Disco de SO Premium|Yes|Ausência de Leitura do Cache de Disco de SO Premium|Porcentagem|Média|Ausência de Leitura do Cache de Disco de SO Premium|VMName|
|Porcentagem consumida da largura de banda em cache da VM|Yes|Porcentagem consumida da largura de banda em cache da VM|Porcentagem|Média|Porcentagem da largura de banda de disco armazenada em cache consumida pela VM|VMName|
|Porcentagem consumida de IOPS em cache da VM|Yes|Porcentagem consumida de IOPS em cache da VM|Porcentagem|Média|Porcentagem de IOPS de disco em cache consumida pela VM|VMName|
|Porcentagem consumida da largura de banda desarmazenada da VM|Yes|Porcentagem consumida da largura de banda desarmazenada da VM|Porcentagem|Média|Porcentagem de largura de banda de disco não armazenada em cache consumida pela VM|VMName|
|Porcentagem consumida de IOPS não armazenado em cache da VM|Yes|Porcentagem consumida de IOPS não armazenado em cache da VM|Porcentagem|Média|Percentual de IOPS de disco não armazenado em cache consumidos pela VM|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Créditos de CPU Consumidos|Yes|Créditos de CPU Consumidos|Contagem|Média|Número total de créditos consumido pela Máquina Virtual|Sem dimensões|
|Créditos de CPU Restantes|Yes|Créditos de CPU Restantes|Contagem|Média|Número total de créditos disponíveis para intermitência|Sem dimensões|
|Porcentagem consumida da largura de banda do disco|Yes|Porcentagem consumida da largura de banda do disco|Porcentagem|Média|Porcentagem de largura de banda de disco de dados consumida por minuto|LUN|
|Percentual de IOPS consumido de disco de dados|Yes|Percentual de IOPS consumido de disco de dados|Porcentagem|Média|Porcentagem de e/SS de disco de dados consumidas por minuto|LUN|
|Largura de banda máxima de intermitência do disco de dados|Yes|Largura de banda máxima de intermitência do disco de dados|Contagem|Média|O disco de dados de taxa de transferência máximo de bytes por segundo pode alcançar com intermitência|LUN|
|IOPS de intermitência máxima do disco de dados|Yes|IOPS de intermitência máxima do disco de dados|Contagem|Média|O disco de dados de IOPS máximo pode atingir com intermitência|LUN|
|Profundidade da Fila de Disco de Dados|Yes|Profundidade da Fila de Disco de Dados|Contagem|Média|Profundidade da Fila do Disco de Dados (ou Tamanho da Fila)|LUN|
|Bytes de Leitura do Disco de Dados/s|Yes|Bytes de leitura do disco de dados/s|BytesPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento|LUN|
|Operações de Leitura do Disco de Dados/s|Yes|Operações de Leitura do Disco de Dados/s|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento|LUN|
|Largura de banda de destino do disco de dados|Yes|Largura de banda de destino do disco de dados|Contagem|Média|O disco de dados de taxa de transferência por segundo de bytes de linha de base pode atingir sem intermitência|LUN|
|IOPS de destino do disco de dados|Yes|IOPS de destino do disco de dados|Contagem|Média|O disco de dados de IOPS de linha de base pode atingir sem intermitência|LUN|
|Percentual de créditos de pico de BPS usados no disco de dados|Yes|Percentual de créditos de pico de BPS usados no disco de dados|Porcentagem|Média|Percentual de créditos de largura de banda de intermitência de disco de dados usados até agora|LUN|
|Percentual de créditos de e/s de disco de dados usados|Yes|Percentual de créditos de e/s de disco de dados usados|Porcentagem|Média|Porcentagem de créditos de e/s de intermitência de disco de dados usados até agora|LUN|
|Bytes de Gravação do Disco de Dados/s|Yes|Bytes de gravação do disco de dados/s|BytesPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento|LUN|
|Operações de Gravação do Disco de Dados/s|Yes|Operações de Gravação do Disco de Dados/s|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento|LUN|
|Bytes de leitura de disco|Yes|Bytes de leitura de disco|Bytes|Total|Bytes lidos do disco durante o período de monitoramento|Sem dimensões|
|Operações de leitura de disco/Seg|Yes|Operações de leitura de disco/Seg|CountPerSecond|Média|IOPS de leitura de disco|Sem dimensões|
|Bytes de gravação de disco|Yes|Bytes de gravação de disco|Bytes|Total|Bytes gravados no disco durante o período de monitoramento|Sem dimensões|
|Operações de gravação de disco/Seg|Yes|Operações de gravação de disco/Seg|CountPerSecond|Média|IOPS de gravação de disco|Sem dimensões|
|Fluxos de Entrada|Yes|Fluxos de Entrada|Contagem|Média|Os fluxos de entrada são o número de fluxos atuais na direção de entrada (tráfego entrando na VM)|Sem dimensões|
|Taxa Máxima de Criação de Fluxos de Entrada|Yes|Taxa Máxima de Criação de Fluxos de Entrada|CountPerSecond|Média|A taxa de criação máxima de fluxos de entrada (tráfego entrando na VM)|Sem dimensões|
|Entrada na rede|Yes|Rede em Faturável (Preterido)|Bytes|Total|O número de bytes faturáveis recebidos em todas as interfaces de rede pelas máquinas virtuais (tráfego de entrada) (preterido)|Sem dimensões|
|Rede no Total|Yes|Rede no Total|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela máquina virtual ou pelas máquinas virtuais (tráfego de entrada)|Sem dimensões|
|Saída da rede|Yes|Rede Fora de Faturável (Preterido)|Bytes|Total|O número de bytes faturáveis de saída em todas as interfaces de rede pelas máquinas virtuais (tráfego de saída) (preterido)|Sem dimensões|
|Rede Fora do Total|Yes|Rede Fora do Total|Bytes|Total|O número de bytes de saída em todas as interfaces de rede pela máquina virtual ou pelas máquinas virtuais (tráfego de saída)|Sem dimensões|
|Porcentagem consumida da largura de banda do disco|Yes|Porcentagem consumida da largura de banda do disco|Porcentagem|Média|Percentual de largura de banda de disco do sistema operacional consumido por minuto|LUN|
|Porcentagem consumida de IOPS de disco do so|Yes|Porcentagem consumida de IOPS de disco do so|Porcentagem|Média|Porcentagem de e/SS de disco do sistema operacional consumidas por minuto|LUN|
|Largura de banda máxima de intermitência do disco do so|Yes|Largura de banda máxima de intermitência do disco do so|Contagem|Média|Taxa de transferência máxima de bytes por segundo que o disco do so pode atingir com intermitência|LUN|
|IOPS de intermitência máxima do disco do so|Yes|IOPS de intermitência máxima do disco do so|Contagem|Média|O máximo de disco do sistema operacional de IOPS pode atingir com intermitência|LUN|
|Profundidade da Fila de Disco do SO|Yes|Profundidade da Fila de Disco do SO|Contagem|Média|Profundidade da Fila do Disco do SO (ou Tamanho da Fila)|Sem dimensões|
|Bytes de Leitura do Disco do SO/s|Yes|Bytes de Leitura do Disco do SO/s|BytesPerSecond|Média|Bytes/s lidos de um único disco durante o período de monitoramento para o disco do sistema operacional|Sem dimensões|
|Operações de Leitura do Disco do SO/s|Yes|Operações de Leitura do Disco do SO/s|CountPerSecond|Média|Ler IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Sem dimensões|
|Largura de banda de destino do disco do so|Yes|Largura de banda de destino do disco do so|Contagem|Média|Taxa de transferência de bytes por segundo que o disco do sistema operacional pode atingir sem intermitência|LUN|
|IOPS de destino de disco do so|Yes|IOPS de destino de disco do so|Contagem|Média|O disco de sistema operacional IOPS de linha de base pode alcançar sem intermitência|LUN|
|Percentual de créditos de disparo de BPS de disco do so usados|Yes|Percentual de créditos de disparo de BPS de disco do so usados|Porcentagem|Média|Percentual de créditos de largura de banda de intermitência de disco do so usados até agora|LUN|
|Porcentagem de créditos de e/s de disco do so usados|Yes|Porcentagem de créditos de e/s de disco do so usados|Porcentagem|Média|Percentual de créditos de e/s de intermitência de disco do so usados até agora|LUN|
|Bytes de Gravação do Disco do SO/s|Yes|Bytes de gravação de disco do sistema operacional/s|BytesPerSecond|Média|Bytes/s gravados em um único disco durante o período de monitoramento do disco do sistema operacional|Sem dimensões|
|Operações de Gravação do Disco do SO/s|Yes|Operações de Gravação do Disco do SO/s|CountPerSecond|Média|Gravar IOPS de um único disco durante o período de monitoramento para o disco do sistema operacional|Sem dimensões|
|Fluxos de Saída|Yes|Fluxos de Saída|Contagem|Média|Fluxos de saída são o número de fluxos atuais na direção de saída (tráfego saindo da VM)|Sem dimensões|
|Taxa Máxima de Criação de Fluxos de Saída|Yes|Taxa Máxima de Criação de Fluxos de Saída|CountPerSecond|Média|A taxa de criação máxima de fluxos de saída (tráfego saindo da VM)|Sem dimensões|
|Porcentagem de CPU|Yes|Porcentagem de CPU|Porcentagem|Média|A porcentagem das unidades de computação alocadas que estão atualmente em uso pela máquina virtual ou pelas máquinas virtuais|Sem dimensões|
|Ocorrência de Leitura do Cache de Disco de Dados Premium|Yes|Ocorrência de Leitura do Cache de Disco de Dados Premium|Porcentagem|Média|Ocorrência de Leitura do Cache de Disco de Dados Premium|LUN|
|Ausência de Leitura do Cache de Disco de Dados Premium|Yes|Ausência de Leitura do Cache de Disco de Dados Premium|Porcentagem|Média|Ausência de Leitura do Cache de Disco de Dados Premium|LUN|
|Ocorrência de Leitura do Cache de Disco do SO Premium|Yes|Ocorrência de Leitura do Cache de Disco do SO Premium|Porcentagem|Média|Ocorrência de Leitura do Cache de Disco do SO Premium|Sem dimensões|
|Ausência de Leitura do Cache de Disco de SO Premium|Yes|Ausência de Leitura do Cache de Disco de SO Premium|Porcentagem|Média|Ausência de Leitura do Cache de Disco de SO Premium|Sem dimensões|
|Porcentagem consumida da largura de banda em cache da VM|Yes|Porcentagem consumida da largura de banda em cache da VM|Porcentagem|Média|Porcentagem da largura de banda de disco armazenada em cache consumida pela VM|Sem dimensões|
|Porcentagem consumida de IOPS em cache da VM|Yes|Porcentagem consumida de IOPS em cache da VM|Porcentagem|Média|Porcentagem de IOPS de disco em cache consumida pela VM|Sem dimensões|
|Porcentagem consumida da largura de banda desarmazenada da VM|Yes|Porcentagem consumida da largura de banda desarmazenada da VM|Porcentagem|Média|Porcentagem de largura de banda de disco não armazenada em cache consumida pela VM|Sem dimensões|
|Porcentagem consumida de IOPS não armazenado em cache da VM|Yes|Porcentagem consumida de IOPS não armazenado em cache da VM|Porcentagem|Média|Percentual de IOPS de disco não armazenado em cache consumidos pela VM|Sem dimensões|


## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|CpuUsage|Yes|Uso da CPU|Contagem|Média|Uso de CPU em todos os núcleos em milicores.|containerName|
|MemoryUsage|Yes|Uso de Memória|Bytes|Média|Uso de memória total em bytes.|containerName|
|NetworkBytesReceivedPerSecond|Yes|Bytes de Rede Recebidos por Segundo|Bytes|Média|Os bytes de rede recebidos por segundo.|Sem dimensões|
|NetworkBytesTransmittedPerSecond|Yes|Bytes de Rede Transmitidos por Segundo|Bytes|Média|Os bytes de rede transmitidos por segundo.|Sem dimensões|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|AgentPoolCPUTime|Yes|AgentPool tempo de CPU|Segundos|Total|AgentPool tempo de CPU em segundos|Sem dimensões|
|RunDuration|Yes|Duração da Execução|Milissegundos|Total|Duração da execução em milissegundos|Sem dimensões|
|SuccessfulPullCount|Yes|Contagem de Pull com Êxito|Contagem|Média|Número de pulls de imagem bem-sucedidos|Sem dimensões|
|SuccessfulPushCount|Yes|Contagem de Push com Êxito|Contagem|Média|Número de Pushes de imagem bem-sucedidos|Sem dimensões|
|TotalPullCount|Yes|Contagem Total de Pull|Contagem|Média|Número de pulls de imagem no total|Sem dimensões|
|TotalPushCount|Yes|Contagem Total de Push|Contagem|Média|Número de envios por push de imagem no total|Sem dimensões|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|apiserver_current_inflight_requests|No|Solicitações em andamento|Contagem|Média|Número máximo de solicitações de em andamento atualmente usadas no apiserver por tipo de solicitação no último segundo|requestKind|
|kube_node_status_allocatable_cpu_cores|No|Número total de núcleos de cpu disponíveis em um cluster gerenciado|Contagem|Média|Número total de núcleos de cpu disponíveis em um cluster gerenciado||
|kube_node_status_allocatable_memory_bytes|No|Número total de memória disponível em um cluster gerenciado|Bytes|Média|Número total de memória disponível em um cluster gerenciado||
|kube_node_status_condition|No|Status para as várias condições de nó|Contagem|Média|Status para as várias condições de nó|condição, status, status2, nó|
|kube_pod_status_phase|No|Número de pods por fase|Contagem|Média|Número de pods por fase|fase, namespace, pod|
|kube_pod_status_ready|No|Número de compartimentos no estado Pronto|Contagem|Média|Número de compartimentos no estado Pronto|namespace, Pod, condição|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|FailedRequests|Yes|Solicitações com falha|Contagem|Total|Obtém os logs disponíveis para provedores de recursos personalizados|HttpMethod, CallPath, StatusCode|
|SuccessfullRequests|Yes|Solicitações bem sucedidas|Contagem|Total|Solicitações bem-sucedidas feitas pelo provedor personalizado|HttpMethod, CallPath, StatusCode|


## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|AvailableCapacity|Yes|Capacidade Disponível|Bytes|Média|A capacidade disponível em bytes durante o período de relatório.|Sem dimensões|
|BytesUploadedToCloud|Yes|Bytes Carregados na Nuvem (Dispositivo)|Bytes|Média|O número total de bytes que são carregados no Azure de um dispositivo durante o período de relatório.|Sem dimensões|
|BytesUploadedToCloudPerShare|Yes|Bytes Carregados na Nuvem (Compartilhamento)|Bytes|Média|O número total de bytes que são carregados no Azure de um compartilhamento durante o período de relatório.|Compartilhar|
|CloudReadThroughput|Yes|Taxa de Transferência de Download na Nuvem|BytesPerSecond|Média|A taxa de transferência de download de nuvem para o Azure durante o período de relatório.|Sem dimensões|
|CloudReadThroughputPerShare|Yes|Taxa de Transferência de Downloads na Nuvem (Compartilhamento)|BytesPerSecond|Média|A taxa de transferência de download para o Azure de um compartilhamento durante o período de relatório.|Compartilhar|
|CloudUploadThroughput|Yes|Taxa de Transferência de Uploads na Nuvem|BytesPerSecond|Média|A taxa de transferência de upload de nuvem para o Azure durante o período de relatório.|Sem dimensões|
|CloudUploadThroughputPerShare|Yes|Taxa de Transferência de Uploads na Nuvem (Compartilhamento)|BytesPerSecond|Média|A taxa de transferência de upload para o Azure de um compartilhamento durante o período de relatório.|Compartilhar|
|HyperVMemoryUtilization|Yes|Computação de Borda – Uso de Memória|Porcentagem|Média|Quantidade de RAM em uso|InstanceName|
|HyperVVirtualProcessorUtilization|Yes|Computação de Borda – Percentual de CPU|Porcentagem|Média|Porcentagem de uso da CPU|InstanceName|
|NICReadThroughput|Yes|Taxa de Transferência de Leitura (Rede)|BytesPerSecond|Média|A taxa de transferência de leitura da interface de rede no dispositivo no período de relatório para todos os volumes no gateway.|InstanceName|
|NICWriteThroughput|Yes|Taxa de Transferência de Gravações (Rede)|BytesPerSecond|Média|A taxa de transferência de gravação da interface de rede no dispositivo no período de relatório para todos os volumes no gateway.|InstanceName|
|TotalCapacity|Yes|Capacidade Total|Bytes|Média|Capacidade Total|Sem dimensões|


## <a name="microsoftdatacollaborationworkspaces"></a>Microsoft. datacollaboration/Workspaces

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|DataAssetCount|Yes|Ativos de dados criados|Contagem|Máximo|Número de ativos de dados criados|Dataativoname|
|PipelineCount|Yes|Pipelines criados|Contagem|Máximo|Número de pipelines criados|PipelineName|
|ProposalCount|Yes|Propostas criadas|Contagem|Máximo|Número de propostas criadas|Proposta de|
|ScriptCount|Yes|Scripts criados|Contagem|Máximo|Número de scripts criados|ScriptName|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|FailedRuns|Yes|Execuções com falha|Contagem|Total||pipelinename, ActivityName|
|SuccessfulRuns|Yes|Execuções com êxito|Contagem|Total||pipelinename, ActivityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ActivityCancelledRuns|Yes|Métricas de execuções de atividades canceladas|Contagem|Total||ActivityType, PipelineName, FailureType, Nome|
|ActivityFailedRuns|Yes|Métricas de execução de atividades com falha|Contagem|Total||ActivityType, PipelineName, FailureType, Nome|
|ActivitySucceededRuns|Yes|Métricas de execução de atividades bem-sucedidas|Contagem|Total||ActivityType, PipelineName, FailureType, Nome|
|FactorySizeInGbUnits|Yes|Tamanho total do alocador (unidade GB)|Contagem|Máximo||Sem dimensões|
|IntegrationRuntimeAvailableMemory|Yes|Memória disponível de runtime de integração|Bytes|Média||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAvailableNodeNumber|Yes|Contagem de nós disponíveis do Integration Runtime|Contagem|Média||IntegrationRuntimeName|
|IntegrationRuntimeAverageTaskPickupDelay|Yes|Duração da fila do runtime de integração|Segundos|Média||IntegrationRuntimeName|
|IntegrationRuntimeCpuPercentage|Yes|Utilização de CPU de runtime de integração|Porcentagem|Média||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeQueueLength|Yes|Tamanho da fila do runtime de integração|Contagem|Média||IntegrationRuntimeName|
|MaxAllowedFactorySizeInGbUnits|Yes|Tamanho máximo de alocador permitido (unidade GB)|Contagem|Máximo||Sem dimensões|
|MaxAllowedResourceCount|Yes|Contagem máxima de entidades permitidas|Contagem|Máximo||Sem dimensões|
|PipelineCancelledRuns|Yes|Métricas de execução do pipeline canceladas|Contagem|Total||FailureType, Nome|
|PipelineElapsedTimeRuns|Yes|Métricas de execução de pipeline de tempo decorrido|Contagem|Total||RunId, nome|
|PipelineFailedRuns|Yes|Métricas de execução do pipeline com falha|Contagem|Total||FailureType, Nome|
|PipelineSucceededRuns|Yes|Métricas de execução do pipeline bem-sucedido|Contagem|Total||FailureType, Nome|
|ResourceCount|Yes|Contagem total de entidades|Contagem|Máximo||Sem dimensões|
|SSISIntegrationRuntimeStartCancel|Yes|Métricas de início do tempo de execução de integração SSIS canceladas|Contagem|Total||IntegrationRuntimeName|
|SSISIntegrationRuntimeStartFailed|Yes|Métricas de início do tempo de execução de integração SSIS com falha|Contagem|Total||IntegrationRuntimeName|
|SSISIntegrationRuntimeStartSucceeded|Yes|Métricas de início de tempo de execução de integração SSIS com êxito|Contagem|Total||IntegrationRuntimeName|
|SSISIntegrationRuntimeStopStuck|Yes|Métricas de parada de tempo de execução de integração SSIS travadas|Contagem|Total||IntegrationRuntimeName|
|SSISIntegrationRuntimeStopSucceeded|Yes|Métricas de parada de tempo de execução de integração SSIS com êxito|Contagem|Total||IntegrationRuntimeName|
|SSISPackageExecutionCancel|Yes|Métricas de execução de pacote SSIS canceladas|Contagem|Total||IntegrationRuntimeName|
|SSISPackageExecutionFailed|Yes|Métricas de execução de pacote SSIS com falha|Contagem|Total||IntegrationRuntimeName|
|SSISPackageExecutionSucceeded|Yes|Métricas de execução de pacote SSIS com êxito|Contagem|Total||IntegrationRuntimeName|
|TriggerCancelledRuns|Yes|Métricas de execuções do gatilho canceladas|Contagem|Total||Nome, FailureType|
|TriggerFailedRuns|Yes|Métricas de execuções do gatilho com falha|Contagem|Total||Nome, FailureType|
|TriggerSucceededRuns|Yes|Métricas de execuções do gatilho bem-sucedidas|Contagem|Total||Nome, FailureType|


## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|JobAUEndedCancelled|Yes|Tempo de AU cancelada|Segundos|Total|Tempo total de AU dos trabalhos cancelados.|Sem dimensões|
|JobAUEndedFailure|Yes|Tempo de AU com falha|Segundos|Total|Tempo total de AU dos trabalhos com falha.|Sem dimensões|
|JobAUEndedSuccess|Yes|Tempo de AU com êxito|Segundos|Total|Tempo total de AU dos trabalhos com êxito.|Sem dimensões|
|JobEndedCancelled|Yes|Trabalhos cancelados|Contagem|Total|Contagem de trabalhos cancelados.|Sem dimensões|
|JobEndedFailure|Yes|Trabalhos com falha|Contagem|Total|Contagem de trabalhos com falha.|Sem dimensões|
|JobEndedSuccess|Yes|Trabalhos com êxito|Contagem|Total|Contagem de trabalhos com êxito.|Sem dimensões|
|JobStage|Yes|Trabalhos no estágio|Contagem|Total|Número de trabalhos em cada estágio.|Sem dimensões|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|DataRead|Yes|Leitura de dados|Bytes|Total|Quantidade total de dados lidos na conta.|Sem dimensões|
|DataWritten|Yes|Dados gravados|Bytes|Total|Quantidade total de dados gravados na conta.|Sem dimensões|
|ReadRequests|Yes|Solicitações de leitura|Contagem|Total|Contagem solicitações de leitura de dados para a conta.|Sem dimensões|
|TotalStorage|Yes|Armazenamento total|Bytes|Máximo|Quantidade total de dados armazenados na conta.|Sem dimensões|
|WriteRequests|Yes|Solicitações de gravação|Contagem|Total|Contagem de solicitações de gravação de dados para a conta.|Sem dimensões|


## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/accounts

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|FailedShareSubscriptionSynchronizations|Yes|Instantâneos de falha de compartilhamento recebidos|Contagem|Contagem|Número de instantâneos de compartilhamento recebidos com falha na conta|Sem dimensões|
|FailedShareSynchronizations|Yes|Instantâneos com falha no compartilhamento enviado|Contagem|Contagem|Número de instantâneos de compartilhamento enviados com falha na conta|Sem dimensões|
|ShareCount|Yes|Compartilhamentos enviados|Contagem|Máximo|Número de compartilhamentos enviados na conta|ShareName|
|ShareSubscriptionCount|Yes|Compartilhamentos recebidos|Contagem|Máximo|Número de compartilhamentos recebidos na conta|ShareSubscriptionName|
|SucceededShareSubscriptionSynchronizations|Yes|Instantâneos recebidos com êxito do compartilhamento|Contagem|Contagem|Número de instantâneos recebidos com êxito no compartilhamento na conta|Sem dimensões|
|SucceededShareSynchronizations|Yes|Instantâneos de compartilhamento enviados com êxito|Contagem|Contagem|Número de instantâneos de compartilhamento enviados com êxito na conta|Sem dimensões|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|active_connections|Yes|Conexões ativas|Contagem|Média|Conexões ativas|Sem dimensões|
|backup_storage_used|Yes|Armazenamento de Backup usado|Bytes|Média|Armazenamento de Backup usado|Sem dimensões|
|connections_failed|Yes|Conexões com falha|Contagem|Total|Conexões com falha|Sem dimensões|
|cpu_percent|Yes|Porcentagem de CPU|Porcentagem|Média|Porcentagem de CPU|Sem dimensões|
|io_consumption_percent|Yes|Porcentagem de E/S|Porcentagem|Média|Porcentagem de E/S|Sem dimensões|
|memory_percent|Yes|Porcentagem de memória|Porcentagem|Média|Porcentagem de memória|Sem dimensões|
|network_bytes_egress|Yes|Saída da rede|Bytes|Total|Saída de Rede em conexões ativas|Sem dimensões|
|network_bytes_ingress|Yes|Entrada na rede|Bytes|Total|Entrada de Rede em conexões ativas|Sem dimensões|
|seconds_behind_master|Yes|Retardo de replicação em segundos|Contagem|Máximo|Retardo de replicação em segundos|Sem dimensões|
|serverlog_storage_limit|Yes|Limite de armazenamento do Log do Servidor|Bytes|Máximo|Limite de armazenamento do Log do Servidor|Sem dimensões|
|serverlog_storage_percent|Yes|Porcentagem de armazenamento do Log do Servidor|Porcentagem|Média|Porcentagem de armazenamento do Log do Servidor|Sem dimensões|
|serverlog_storage_usage|Yes|Armazenamento do Log do Servidor usado|Bytes|Média|Armazenamento do Log do Servidor usado|Sem dimensões|
|storage_limit|Yes|Limite de armazenamento|Bytes|Máximo|Limite de armazenamento|Sem dimensões|
|storage_percent|Yes|Porcentagem de armazenamento|Porcentagem|Média|Porcentagem de armazenamento|Sem dimensões|
|storage_used|Yes|Armazenamento usado|Bytes|Média|Armazenamento usado|Sem dimensões|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft.DBforMySQL/flexibleServers

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|aborted_connections|Yes|Conexões anuladas|Contagem|Total|Conexões anuladas|Sem dimensões|
|active_connections|Yes|Conexões ativas|Contagem|Máximo|Conexões ativas|Sem dimensões|
|backup_storage_used|Yes|Backup do Microsoft Azure|Bytes|Máximo|Backup do Microsoft Azure|Sem dimensões|
|cpu_percent|Yes|Porcentagem de CPU do host|Porcentagem|Máximo|Porcentagem de CPU do host|Sem dimensões|
|io_consumption_percent|Yes|Porcentagem de e/s|Porcentagem|Máximo|Porcentagem de e/s|Sem dimensões|
|memory_percent|Yes|Porcentagem de memória do host|Porcentagem|Máximo|Porcentagem de memória do host|Sem dimensões|
|network_bytes_egress|Yes|Saída da rede do host|Bytes|Total|Saída de rede do host em bytes|Sem dimensões|
|network_bytes_ingress|Yes|Rede de host em|Bytes|Total|Entrada de rede de host em bytes|Sem dimensões|
|Consultas|Yes|Consultas|Contagem|Total|Consultas|Sem dimensões|
|replication_lag|Yes|Atraso de replicação em segundos|Segundos|Máximo|Retardo de replicação em segundos|Sem dimensões|
|storage_limit|Yes|Limite de armazenamento|Bytes|Máximo|Limite de armazenamento|Sem dimensões|
|storage_percent|Yes|Porcentagem de armazenamento|Porcentagem|Máximo|Porcentagem de armazenamento|Sem dimensões|
|storage_used|Yes|Armazenamento usado|Bytes|Máximo|Armazenamento usado|Sem dimensões|
|total_connections|Yes|Total de conexões|Contagem|Total|Total de conexões|Sem dimensões|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|active_connections|Yes|Conexões ativas|Contagem|Média|Conexões ativas|Sem dimensões|
|backup_storage_used|Yes|Armazenamento de Backup usado|Bytes|Média|Armazenamento de Backup usado|Sem dimensões|
|connections_failed|Yes|Conexões com falha|Contagem|Total|Conexões com falha|Sem dimensões|
|cpu_percent|Yes|Porcentagem de CPU|Porcentagem|Média|Porcentagem de CPU|Sem dimensões|
|io_consumption_percent|Yes|Porcentagem de E/S|Porcentagem|Média|Porcentagem de E/S|Sem dimensões|
|memory_percent|Yes|Porcentagem de memória|Porcentagem|Média|Porcentagem de memória|Sem dimensões|
|network_bytes_egress|Yes|Saída da rede|Bytes|Total|Saída de Rede em conexões ativas|Sem dimensões|
|network_bytes_ingress|Yes|Entrada na rede|Bytes|Total|Entrada de Rede em conexões ativas|Sem dimensões|
|seconds_behind_master|Yes|Retardo de replicação em segundos|Contagem|Máximo|Retardo de replicação em segundos|Sem dimensões|
|serverlog_storage_limit|Yes|Limite de armazenamento do Log do Servidor|Bytes|Máximo|Limite de armazenamento do Log do Servidor|Sem dimensões|
|serverlog_storage_percent|Yes|Porcentagem de armazenamento do Log do Servidor|Porcentagem|Média|Porcentagem de armazenamento do Log do Servidor|Sem dimensões|
|serverlog_storage_usage|Yes|Armazenamento do Log do Servidor usado|Bytes|Média|Armazenamento do Log do Servidor usado|Sem dimensões|
|storage_limit|Yes|Limite de armazenamento|Bytes|Máximo|Limite de armazenamento|Sem dimensões|
|storage_percent|Yes|Porcentagem de armazenamento|Porcentagem|Média|Porcentagem de armazenamento|Sem dimensões|
|storage_used|Yes|Armazenamento usado|Bytes|Média|Armazenamento usado|Sem dimensões|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft.DBforPostgreSQL/flexibleServers

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|active_connections|Yes|Conexões ativas|Contagem|Média|Conexões ativas|Sem dimensões|
|backup_storage_used|Yes|Backup do Microsoft Azure|Bytes|Média|Backup do Microsoft Azure|Sem dimensões|
|connections_failed|Yes|Conexões com falha|Contagem|Total|Conexões com falha|Sem dimensões|
|connections_succeeded|Yes|Conexões com êxito|Contagem|Total|Conexões com êxito|Sem dimensões|
|cpu_credits_consumed|Yes|Créditos de CPU Consumidos|Contagem|Média|Número total de créditos consumidos pelo servidor de banco de dados|Sem dimensões|
|cpu_credits_remaining|Yes|Créditos de CPU Restantes|Contagem|Média|Número total de créditos disponíveis para intermitência|Sem dimensões|
|cpu_percent|Yes|Porcentagem de CPU|Porcentagem|Média|Porcentagem de CPU|Sem dimensões|
|disk_queue_depth|Yes|Profundidade da fila de disco|Contagem|Média|Número de operações de e/s pendentes para o disco de dados|Sem dimensões|
|iops|Yes|IOPS|Contagem|Média|Operações de e/s por segundo|Sem dimensões|
|maximum_used_transactionIDs|Yes|Máximo de IDs de transação usadas|Contagem|Média|Máximo de IDs de transação usadas|Sem dimensões|
|memory_percent|Yes|Porcentagem de memória|Porcentagem|Média|Porcentagem de memória|Sem dimensões|
|network_bytes_egress|Yes|Saída da rede|Bytes|Total|Saída de Rede em conexões ativas|Sem dimensões|
|network_bytes_ingress|Yes|Entrada na rede|Bytes|Total|Entrada de Rede em conexões ativas|Sem dimensões|
|read_iops|Yes|IOPS de leitura|Contagem|Média|Número de operações de leitura de e/s de disco de dados por segundo|Sem dimensões|
|read_throughput|Yes|Bytes de taxa de transferência de leitura/s|Contagem|Média|Bytes lidos por segundo do disco de dados durante o período de monitoramento|Sem dimensões|
|storage_free|Yes|Armazenamento livre|Bytes|Média|Armazenamento livre|Sem dimensões|
|storage_percent|Yes|Porcentagem de armazenamento|Porcentagem|Média|Porcentagem de armazenamento|Sem dimensões|
|storage_used|Yes|Armazenamento usado|Bytes|Média|Armazenamento usado|Sem dimensões|
|txlogs_storage_used|Yes|Armazenamento de log de transações usado|Bytes|Média|Armazenamento de log de transações usado|Sem dimensões|
|write_iops|Yes|IOPS de gravação|Contagem|Média|Número de operações de gravação de e/s de disco de dados por segundo|Sem dimensões|
|write_throughput|Yes|Bytes de taxa de transferência de gravação/s|Contagem|Média|Bytes gravados por segundo no disco de dados durante o período de monitoramento|Sem dimensões|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|active_connections|Yes|Conexões ativas|Contagem|Média|Conexões ativas|Sem dimensões|
|backup_storage_used|Yes|Backup do Microsoft Azure|Bytes|Média|Backup do Microsoft Azure|Sem dimensões|
|connections_failed|Yes|Conexões com falha|Contagem|Total|Conexões com falha|Sem dimensões|
|cpu_percent|Yes|Porcentagem de CPU|Porcentagem|Média|Porcentagem de CPU|Sem dimensões|
|io_consumption_percent|Yes|Porcentagem de E/S|Porcentagem|Média|Porcentagem de E/S|Sem dimensões|
|memory_percent|Yes|Porcentagem de memória|Porcentagem|Média|Porcentagem de memória|Sem dimensões|
|network_bytes_egress|Yes|Saída da rede|Bytes|Total|Saída de Rede em conexões ativas|Sem dimensões|
|network_bytes_ingress|Yes|Entrada na rede|Bytes|Total|Entrada de Rede em conexões ativas|Sem dimensões|
|pg_replica_log_delay_in_bytes|Yes|Retardo Máximo entre Réplicas|Bytes|Máximo|Atraso em bytes da réplica mais defasante|Sem dimensões|
|pg_replica_log_delay_in_seconds|Yes|Retardo da Réplica|Segundos|Máximo|Atraso de réplica em segundos|Sem dimensões|
|serverlog_storage_limit|Yes|Limite de armazenamento do Log do Servidor|Bytes|Máximo|Limite de armazenamento do Log do Servidor|Sem dimensões|
|serverlog_storage_percent|Yes|Porcentagem de armazenamento do Log do Servidor|Porcentagem|Média|Porcentagem de armazenamento do Log do Servidor|Sem dimensões|
|serverlog_storage_usage|Yes|Armazenamento do Log do Servidor usado|Bytes|Média|Armazenamento do Log do Servidor usado|Sem dimensões|
|storage_limit|Yes|Limite de armazenamento|Bytes|Máximo|Limite de armazenamento|Sem dimensões|
|storage_percent|Yes|Porcentagem de armazenamento|Porcentagem|Média|Porcentagem de armazenamento|Sem dimensões|
|storage_used|Yes|Armazenamento usado|Bytes|Média|Armazenamento usado|Sem dimensões|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|active_connections|Yes|Conexões ativas|Contagem|Média|Conexões ativas|Sem dimensões|
|cpu_percent|Yes|Porcentagem de CPU|Porcentagem|Média|Porcentagem de CPU|Sem dimensões|
|iops|Yes|IOPS|Contagem|Média|Operações de e/s por segundo|Sem dimensões|
|memory_percent|Yes|Porcentagem de memória|Porcentagem|Média|Porcentagem de memória|Sem dimensões|
|network_bytes_egress|Yes|Saída da rede|Bytes|Total|Saída de Rede em conexões ativas|Sem dimensões|
|network_bytes_ingress|Yes|Entrada na rede|Bytes|Total|Entrada de Rede em conexões ativas|Sem dimensões|
|storage_percent|Yes|Porcentagem de armazenamento|Porcentagem|Média|Porcentagem de armazenamento|Sem dimensões|
|storage_used|Yes|Armazenamento usado|Bytes|Média|Armazenamento usado|Sem dimensões|


## <a name="microsoftdeviceselasticpools"></a>Microsoft.Devices/ElasticPools

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|elasticPool.requestedUsageRate|Yes|taxa de uso solicitado|Porcentagem|Média|taxa de uso solicitado|Sem dimensões|


## <a name="microsoftdeviceselasticpoolsiothubtenants"></a>Microsoft.Devices/ElasticPools/IotHubTenants

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|c2d.commands.egress.abandon.success|Yes|Mensagens C2D abandonadas|Contagem|Total|Número de mensagens da nuvem para o dispositivo abandonadas pelo dispositivo|Sem dimensões|
|c2d.commands.egress.complete.success|Yes|Entregas de mensagem C2D concluídas|Contagem|Total|Número de entregas de mensagem da nuvem para o dispositivo concluídas com êxito pelo dispositivo|Sem dimensões|
|c2d.commands.egress.reject.success|Yes|Mensagens C2D rejeitadas|Contagem|Total|Número de mensagens da nuvem para o dispositivo rejeitadas pelo dispositivo|Sem dimensões|
|c2d.methods.failure|Yes|Invocações de método diretas com falhas|Contagem|Total|A contagem de todas as chamadas de método diretas com falhas.|Sem dimensões|
|c2d.methods.requestSize|Yes|Tamanho da solicitação das invocações de método diretas|Bytes|Média|A média, o mínimo e o máximo de todas as solicitações de método diretas bem-sucedidas.|Sem dimensões|
|c2d.methods.responseSize|Yes|Tamanho da resposta das invocações de método diretas|Bytes|Média|A média, o mínimo e o máximo de todas as respostas de método diretas bem-sucedidas.|Sem dimensões|
|c2d.methods.success|Yes|Invocações de método diretas bem-sucedidas|Contagem|Total|A contagem de todas as chamadas de método diretas bem-sucedidas.|Sem dimensões|
|c2d.twin.read.failure|Yes|Leituras de gêmeos de back-end com falhas|Contagem|Total|A contagem de todas as falhas de leituras de gêmeos iniciadas pelo back-end.|Sem dimensões|
|c2d.twin.read.size|Yes|Tamanho da resposta das leituras de gêmeos de back-end|Bytes|Média|A média, o mínimo e o máximo de todas as leituras de gêmeos bem-sucedidas iniciadas pelo back-end.|Sem dimensões|
|c2d.twin.read.success|Yes|Leituras de gêmeos de back-end bem-sucedidas|Contagem|Total|A contagem de todas as leituras de gêmeos bem-sucedidas iniciadas pelo back-end.|Sem dimensões|
|c2d.twin.update.failure|Yes|Atualizações de gêmeos de back-end com falhas|Contagem|Total|A contagem de todas as falhas de atualizações de gêmeos iniciadas pelo back-end.|Sem dimensões|
|c2d.twin.update.size|Yes|Tamanho das atualizações de gêmeos de back-end|Bytes|Média|A média, o mínimo e o máximo de todas as atualizações de gêmeos iniciadas pelo back-end bem-sucedidas.|Sem dimensões|
|c2d.twin.update.success|Yes|Atualizações de gêmeos de back-end bem-sucedidas|Contagem|Total|A contagem de todas as atualizações de gêmeos iniciadas pelo back-end bem-sucedidas.|Sem dimensões|
|C2DMessagesExpired|Yes|C2D mensagens expiradas|Contagem|Total|Número de mensagens de nuvem para o dispositivo expiradas|Sem dimensões|
|configurações|Yes|Métricas de configuração|Contagem|Total|Métricas para operações de configuração|Sem dimensões|
|connectedDeviceCount|Yes|Dispositivos conectados|Contagem|Média|Número de dispositivos registrados ao seu hub IoT|Sem dimensões|
|d2c.endpoints.egress.builtIn.events|Yes|Roteamento: mensagens entregues a mensagens/eventos|Contagem|Total|O número de vezes que o roteamento do Hub IoT entregou com êxito mensagens ao ponto de extremidade interno (mensagens/eventos).|Sem dimensões|
|d2c.endpoints.egress.eventHubs|Yes|Roteamento: mensagens entregues ao Hub de Eventos|Contagem|Total|O número de vezes que o roteamento do Hub IoT entregou com êxito mensagens aos pontos de extremidade do Hub de Eventos.|Sem dimensões|
|d2c.endpoints.egress.serviceBusQueues|Yes|Roteamento: mensagens entregues à Fila do Barramento de Serviço|Contagem|Total|O número de vezes que o roteamento do Hub IoT entregou com êxito mensagens aos pontos de extremidade da fila do Barramento de Serviço.|Sem dimensões|
|d2c.endpoints.egress.serviceBusTopics|Yes|Roteamento: mensagens entregues ao Tópico do Barramento de Serviço|Contagem|Total|O número de vezes que o roteamento do Hub IoT entregou com êxito mensagens aos pontos de extremidade do tópico do Barramento de Serviço.|Sem dimensões|
|d2c.endpoints.egress.storage|Yes|Roteamento: mensagens entregues ao armazenamento|Contagem|Total|O número de vezes que o roteamento do Hub IoT entregou com êxito mensagens aos pontos de extremidade de armazenamento.|Sem dimensões|
|d2c.endpoints.egress.storage.blobs|Yes|Roteamento: blobs entregues ao armazenamento|Contagem|Total|O número de vezes que o roteamento do Hub IoT entregou blobs aos pontos de extremidade de armazenamento.|Sem dimensões|
|d2c.endpoints.egress.storage.bytes|Yes|Roteamento: dados entregues ao armazenamento|Bytes|Total|A quantidade de dados (bytes) de roteamento do Hub IoT entregue aos pontos de extremidade de armazenamento.|Sem dimensões|
|d2c.Endpoints.Latency.builtIn.Events|Yes|Roteamento: latência de mensagem para mensagens/eventos|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem no Hub IoT e a entrada da mensagem de telemetria no ponto de extremidade interno (mensagens/eventos).|Sem dimensões|
|d2c.endpoints.latency.eventHubs|Yes|Roteamento: latência de mensagem para o Hub de Eventos|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem no Hub IoT e a entrada da mensagem em um ponto de extremidade do Hub de Eventos.|Sem dimensões|
|d2c.endpoints.latency.serviceBusQueues|Yes|Roteamento: latência de mensagem para a Fila do Barramento de Serviço|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem no Hub IoT e a entrada da mensagem de telemetria em um ponto de extremidade da fila do Barramento de Serviço.|Sem dimensões|
|d2c.endpoints.latency.serviceBusTopics|Yes|Roteamento: latência de mensagem para o Tópico do Barramento de Serviço|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem no Hub IoT e a entrada da mensagem de telemetria em um ponto de extremidade do tópico do Barramento de Serviço.|Sem dimensões|
|d2c.endpoints.latency.storage|Yes|Roteamento: latência de mensagem para armazenamento|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem no Hub IoT e a entrada da mensagem de telemetria em um ponto de extremidade de armazenamento.|Sem dimensões|
|d2c.telemetry.egress.dropped|Yes|Roteamento: mensagens de telemetria removidas |Contagem|Total|O número de vezes que as mensagens foram removidas pelo roteamento do Hub IoT devido à inatividade dos pontos de extremidade. Esse valor não conta as mensagens entregues à rota de fallback, pois as mensagens removidas não são entregues.|Sem dimensões|
|d2c.telemetry.egress.fallback|Yes|Roteamento: mensagens entregues ao fallback|Contagem|Total|O número de vezes que o roteamento do Hub IoT entregou mensagens ao ponto de extremidade associado à rota de fallback.|Sem dimensões|
|d2c.telemetry.egress.invalid|Yes|Roteamento: mensagens de telemetria incompatíveis|Contagem|Total|O número de vezes que o roteamento do Hub IoT não conseguiu entregar mensagens devido a uma incompatibilidade com o ponto de extremidade. Esse valor não inclui novas tentativas.|Sem dimensões|
|d2c.telemetry.egress.orphaned|Yes|Roteamento: mensagens de telemetria órfãs |Contagem|Total|O número de vezes que as mensagens ficaram órfãos do roteamento do Hub IoT porque não correspondiam a nenhuma regra de roteamento (incluindo a regra de fallback). |Sem dimensões|
|d2c.telemetry.egress.success|Yes|Roteamento: mensagens de telemetria entregues|Contagem|Total|O número de vezes que as mensagens foram entregues com êxito a todos os pontos de extremidade usando o roteamento do Hub IoT. Se uma mensagem é encaminhada para vários pontos de extremidade, esse valor aumenta em um para cada entrega bem-sucedida. Se uma mensagem é entregue ao mesmo ponto de extremidade várias vezes, esse valor aumenta em um para cada entrega bem-sucedida.|Sem dimensões|
|d2c.telemetry.ingress.allProtocol|Yes|Tentativas de envio de mensagem de telemetria|Contagem|Total|Número de tentativas de envio de mensagens de telemetria do dispositivo para nuvem para o hub IoT|Sem dimensões|
|d2c.telemetry.ingress.sendThrottle|Yes|Número de erros de limitação|Contagem|Total|Número de erros de limitação devido a limitações da taxa de transferência do dispositivo|Sem dimensões|
|d2c.telemetry.ingress.success|Yes|Mensagens de telemetria enviadas|Contagem|Total|Número de mensagens de telemetria do dispositivo para nuvem enviadas com êxito para o hub IoT|Sem dimensões|
|d2c.twin.read.failure|Yes|Leituras de gêmeos dos dispositivos com falhas|Contagem|Total|A contagem de todas as leituras de gêmeos iniciadas pelo dispositivo com falhas.|Sem dimensões|
|d2c.twin.read.size|Yes|Tamanho da resposta das leituras de gêmeos dos dispositivos|Bytes|Média|A média, o mínimo e o máximo de todas as leituras de gêmeos iniciadas pelo dispositivo bem-sucedidas.|Sem dimensões|
|d2c.twin.read.success|Yes|Leituras de gêmeos dos dispositivos bem-sucedidas|Contagem|Total|A contagem de todas as leituras de gêmeos iniciadas pelo dispositivo bem-sucedidas.|Sem dimensões|
|d2c.twin.update.failure|Yes|Atualizações de gêmeos dos dispositivos com falhas|Contagem|Total|A contagem de todas as atualizações de gêmeos iniciadas pelo dispositivo.|Sem dimensões|
|d2c.twin.update.size|Yes|Tamanho das atualizações de gêmeos dos dispositivos|Bytes|Média|A média, o mínimo e o máximo de todas as atualizações de gêmeos iniciadas pelo dispositivo bem-sucedidas.|Sem dimensões|
|d2c.twin.update.success|Yes|Atualizações de gêmeos dos dispositivos bem-sucedidas|Contagem|Total|A contagem de todas as atualizações de gêmeos iniciadas pelo dispositivo bem-sucedidas.|Sem dimensões|
|dailyMessageQuotaUsed|Yes|Número total de mensagens usadas|Contagem|Máximo|Número total de mensagens usadas hoje|Sem dimensões|
|deviceDataUsage|Yes|Uso total de dados do dispositivo|Bytes|Total|Bytes transferidos entre todos os dispositivos conectados ao lotHub|Sem dimensões|
|deviceDataUsageV2|Yes|Uso total de dados de dispositivos (versão prévia)|Bytes|Total|Bytes transferidos entre todos os dispositivos conectados ao lotHub|Sem dimensões|
|devices.connectedDevices.allProtocol|Yes|Dispositivos conectados (preteridos) |Contagem|Total|Número de dispositivos registrados ao seu hub IoT|Sem dimensões|
|devices.totalDevices|Yes|Total de dispositivos (preterido)|Contagem|Total|Número de dispositivos registrados para o seu hub IoT|Sem dimensões|
|EventGridDeliveries|Yes|Entregas da grade de eventos|Contagem|Total|O número de eventos do Hub IoT publicados na grade de eventos. Use a dimensão de resultado para o número de solicitações bem-sucedidas e com falha. A dimensão EventType mostra o tipo de evento ( https://aka.ms/ioteventgrid) .|Resultado, EventType|
|EventGridLatency|Yes|Latência da grade de eventos|Milissegundos|Média|A latência média (milissegundos) de quando o evento do Hub IOT foi gerado quando o evento foi publicado na grade de eventos. Esse número é uma média entre todos os tipos de evento. Use a dimensão EventType para ver a latência de um tipo específico de evento.|EventType|
|jobs.cancelJob.failure|Yes|Cancelamentos de trabalho com falhas|Contagem|Total|A contagem de todas as chamadas para cancelar um trabalho com falhas.|Sem dimensões|
|jobs.cancelJob.success|Yes|Cancelamentos de trabalho bem-sucedidos|Contagem|Total|A contagem de todas as chamadas para cancelar um trabalho bem-sucedidas.|Sem dimensões|
|jobs.completed|Yes|Trabalhos concluídos|Contagem|Total|A contagem de todos os trabalhos concluídos.|Sem dimensões|
|jobs.createDirectMethodJob.failure|Yes|Criações de trabalhos de invocação de método com falhas|Contagem|Total|A contagem de todas as criações de trabalhos de invocação de método direto com falhas.|Sem dimensões|
|jobs.createDirectMethodJob.success|Yes|Criações de trabalhos de invocação de método bem-sucedidas|Contagem|Total|A contagem de todas as criações de trabalhos de invocação de método direto bem-sucedidas.|Sem dimensões|
|jobs.createTwinUpdateJob.failure|Yes|Criações de trabalhos de atualização de gêmeos com falhas|Contagem|Total|A contagem de todos os trabalhos de criação de atualização de gêmeos com falhas.|Sem dimensões|
|jobs.createTwinUpdateJob.success|Yes|Criações de trabalhos de atualização de gêmeos bem-sucedidas|Contagem|Total|A contagem de todos os trabalhos de criação de atualização de gêmeos bem-sucedidos.|Sem dimensões|
|jobs.failed|Yes|Trabalhos com falha|Contagem|Total|A contagem de todos os trabalhos com falha.|Sem dimensões|
|jobs.listJobs.failure|Yes|Chamadas para listar trabalhos com falhas|Contagem|Total|A contagem de todas as chamadas para listar trabalhos com falhas.|Sem dimensões|
|jobs.listJobs.success|Yes|Chamadas para listar trabalhos bem-sucedidas|Contagem|Total|A contagem de todas as chamadas para listar trabalhos bem-sucedidas.|Sem dimensões|
|jobs.queryJobs.failure|Yes|Consultas de trabalho com falhas|Contagem|Total|A contagem de todas as chamadas para consultas de trabalho com falhas.|Sem dimensões|
|jobs.queryJobs.success|Yes|Consultas de trabalho bem-sucedidas|Contagem|Total|A contagem de todas as chamadas para consultas de trabalho bem-sucedidas.|Sem dimensões|
|RoutingDataSizeInBytesDelivered|Yes|Tamanho da mensagem de entrega de roteamento em bytes (versão prévia)|Bytes|Total|O tamanho total em bytes de mensagens entregues pelo Hub IoT a um ponto de extremidade. Você pode usar as dimensões EndpointName e EndpointType para exibir o tamanho das mensagens em bytes entregues aos diferentes pontos de extremidade. O valor de métrica aumenta para cada mensagem entregue, incluindo se a mensagem é entregue a vários pontos de extremidade ou se a mensagem é entregue ao mesmo ponto de extremidade várias vezes.|EndpointType, EndpointName, roteamento|
|RoutingDeliveries|Yes|Entregas de roteamento (visualização)|Contagem|Total|O número de vezes que o Hub IoT tentou entregar mensagens a todos os pontos de extremidade usando o roteamento. Para ver o número de tentativas bem-sucedidas ou com falha, use a dimensão de resultado. Para ver o motivo da falha, como inválida, descartada ou órfã, use a dimensão FailureReasonCategory. Você também pode usar as dimensões EndpointName e EndpointType para entender quantas mensagens foram entregues aos diferentes pontos de extremidade. O valor da métrica aumenta em uma para cada tentativa de entrega, incluindo se a mensagem é entregue a vários pontos de extremidade ou se a mensagem é entregue ao mesmo ponto de extremidades várias vezes.|EndpointType, EndpointName, FailureReasonCategory, resultado, roteamento|
|RoutingDeliveryLatency|Yes|Latência de entrega de roteamento (visualização)|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem e o Hub IoT e a entrada da mensagem de telemetria em um ponto de extremidade. Você pode usar as dimensões EndpointName e EndpointType para entender a latência para seus pontos de extremidade diferentes.|EndpointType, EndpointName, roteamento|
|tenantHub.requestedUsageRate|Yes|taxa de uso solicitado|Porcentagem|Média|taxa de uso solicitado|Sem dimensões|
|totalDeviceCount|Yes|Total de dispositivos|Contagem|Média|Número de dispositivos registrados para o seu hub IoT|Sem dimensões|
|twinQueries.failure|Yes|Consultas de gêmeos com falhas|Contagem|Total|A contagem de todas as consultas de gêmeos com falhas.|Sem dimensões|
|twinQueries.resultSize|Yes|Tamanho do resultado das consultas de gêmeos|Bytes|Média|A média, o mínimo e o máximo do tamanho do resultado de todas as consultas de gêmeos bem-sucedidas.|Sem dimensões|
|twinQueries.success|Yes|Consultas de gêmeos bem-sucedidas|Contagem|Total|A contagem de todas as consultas de gêmeos bem-sucedidas.|Sem dimensões|


## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|c2d.commands.egress.abandon.success|Yes|Mensagens C2D abandonadas|Contagem|Total|Número de mensagens da nuvem para o dispositivo abandonadas pelo dispositivo|Sem dimensões|
|c2d.commands.egress.complete.success|Yes|Entregas de mensagem C2D concluídas|Contagem|Total|Número de entregas de mensagem da nuvem para o dispositivo concluídas com êxito pelo dispositivo|Sem dimensões|
|c2d.commands.egress.reject.success|Yes|Mensagens C2D rejeitadas|Contagem|Total|Número de mensagens da nuvem para o dispositivo rejeitadas pelo dispositivo|Sem dimensões|
|c2d.methods.failure|Yes|Invocações de método diretas com falhas|Contagem|Total|A contagem de todas as chamadas de método diretas com falhas.|Sem dimensões|
|c2d.methods.requestSize|Yes|Tamanho da solicitação das invocações de método diretas|Bytes|Média|A média, o mínimo e o máximo de todas as solicitações de método diretas bem-sucedidas.|Sem dimensões|
|c2d.methods.responseSize|Yes|Tamanho da resposta das invocações de método diretas|Bytes|Média|A média, o mínimo e o máximo de todas as respostas de método diretas bem-sucedidas.|Sem dimensões|
|c2d.methods.success|Yes|Invocações de método diretas bem-sucedidas|Contagem|Total|A contagem de todas as chamadas de método diretas bem-sucedidas.|Sem dimensões|
|c2d.twin.read.failure|Yes|Leituras de gêmeos de back-end com falhas|Contagem|Total|A contagem de todas as falhas de leituras de gêmeos iniciadas pelo back-end.|Sem dimensões|
|c2d.twin.read.size|Yes|Tamanho da resposta das leituras de gêmeos de back-end|Bytes|Média|A média, o mínimo e o máximo de todas as leituras de gêmeos bem-sucedidas iniciadas pelo back-end.|Sem dimensões|
|c2d.twin.read.success|Yes|Leituras de gêmeos de back-end bem-sucedidas|Contagem|Total|A contagem de todas as leituras de gêmeos bem-sucedidas iniciadas pelo back-end.|Sem dimensões|
|c2d.twin.update.failure|Yes|Atualizações de gêmeos de back-end com falhas|Contagem|Total|A contagem de todas as falhas de atualizações de gêmeos iniciadas pelo back-end.|Sem dimensões|
|c2d.twin.update.size|Yes|Tamanho das atualizações de gêmeos de back-end|Bytes|Média|A média, o mínimo e o máximo de todas as atualizações de gêmeos iniciadas pelo back-end bem-sucedidas.|Sem dimensões|
|c2d.twin.update.success|Yes|Atualizações de gêmeos de back-end bem-sucedidas|Contagem|Total|A contagem de todas as atualizações de gêmeos iniciadas pelo back-end bem-sucedidas.|Sem dimensões|
|C2DMessagesExpired|Yes|C2D mensagens expiradas|Contagem|Total|Número de mensagens de nuvem para o dispositivo expiradas|Sem dimensões|
|configurações|Yes|Métricas de configuração|Contagem|Total|Métricas para operações de configuração|Sem dimensões|
|connectedDeviceCount|No|Dispositivos conectados|Contagem|Média|Número de dispositivos registrados ao seu hub IoT|Sem dimensões|
|d2c.endpoints.egress.builtIn.events|Yes|Roteamento: mensagens entregues a mensagens/eventos|Contagem|Total|O número de vezes que o roteamento do Hub IoT entregou com êxito mensagens ao ponto de extremidade interno (mensagens/eventos).|Sem dimensões|
|d2c.endpoints.egress.eventHubs|Yes|Roteamento: mensagens entregues ao Hub de Eventos|Contagem|Total|O número de vezes que o roteamento do Hub IoT entregou com êxito mensagens aos pontos de extremidade do Hub de Eventos.|Sem dimensões|
|d2c.endpoints.egress.serviceBusQueues|Yes|Roteamento: mensagens entregues à Fila do Barramento de Serviço|Contagem|Total|O número de vezes que o roteamento do Hub IoT entregou com êxito mensagens aos pontos de extremidade da fila do Barramento de Serviço.|Sem dimensões|
|d2c.endpoints.egress.serviceBusTopics|Yes|Roteamento: mensagens entregues ao Tópico do Barramento de Serviço|Contagem|Total|O número de vezes que o roteamento do Hub IoT entregou com êxito mensagens aos pontos de extremidade do tópico do Barramento de Serviço.|Sem dimensões|
|d2c.endpoints.egress.storage|Yes|Roteamento: mensagens entregues ao armazenamento|Contagem|Total|O número de vezes que o roteamento do Hub IoT entregou com êxito mensagens aos pontos de extremidade de armazenamento.|Sem dimensões|
|d2c.endpoints.egress.storage.blobs|Yes|Roteamento: blobs entregues ao armazenamento|Contagem|Total|O número de vezes que o roteamento do Hub IoT entregou blobs aos pontos de extremidade de armazenamento.|Sem dimensões|
|d2c.endpoints.egress.storage.bytes|Yes|Roteamento: dados entregues ao armazenamento|Bytes|Total|A quantidade de dados (bytes) de roteamento do Hub IoT entregue aos pontos de extremidade de armazenamento.|Sem dimensões|
|d2c.Endpoints.Latency.builtIn.Events|Yes|Roteamento: latência de mensagem para mensagens/eventos|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem no Hub IoT e a entrada da mensagem de telemetria no ponto de extremidade interno (mensagens/eventos).|Sem dimensões|
|d2c.endpoints.latency.eventHubs|Yes|Roteamento: latência de mensagem para o Hub de Eventos|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem no Hub IoT e a entrada da mensagem em um ponto de extremidade do Hub de Eventos.|Sem dimensões|
|d2c.endpoints.latency.serviceBusQueues|Yes|Roteamento: latência de mensagem para a Fila do Barramento de Serviço|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem no Hub IoT e a entrada da mensagem de telemetria em um ponto de extremidade da fila do Barramento de Serviço.|Sem dimensões|
|d2c.endpoints.latency.serviceBusTopics|Yes|Roteamento: latência de mensagem para o Tópico do Barramento de Serviço|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem no Hub IoT e a entrada da mensagem de telemetria em um ponto de extremidade do tópico do Barramento de Serviço.|Sem dimensões|
|d2c.endpoints.latency.storage|Yes|Roteamento: latência de mensagem para armazenamento|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem no Hub IoT e a entrada da mensagem de telemetria em um ponto de extremidade de armazenamento.|Sem dimensões|
|d2c.telemetry.egress.dropped|Yes|Roteamento: mensagens de telemetria removidas |Contagem|Total|O número de vezes que as mensagens foram removidas pelo roteamento do Hub IoT devido à inatividade dos pontos de extremidade. Esse valor não conta as mensagens entregues à rota de fallback, pois as mensagens removidas não são entregues.|Sem dimensões|
|d2c.telemetry.egress.fallback|Yes|Roteamento: mensagens entregues ao fallback|Contagem|Total|O número de vezes que o roteamento do Hub IoT entregou mensagens ao ponto de extremidade associado à rota de fallback.|Sem dimensões|
|d2c.telemetry.egress.invalid|Yes|Roteamento: mensagens de telemetria incompatíveis|Contagem|Total|O número de vezes que o roteamento do Hub IoT não conseguiu entregar mensagens devido a uma incompatibilidade com o ponto de extremidade. Esse valor não inclui novas tentativas.|Sem dimensões|
|d2c.telemetry.egress.orphaned|Yes|Roteamento: mensagens de telemetria órfãs |Contagem|Total|O número de vezes que as mensagens ficaram órfãos do roteamento do Hub IoT porque não correspondiam a nenhuma regra de roteamento (incluindo a regra de fallback). |Sem dimensões|
|d2c.telemetry.egress.success|Yes|Roteamento: mensagens de telemetria entregues|Contagem|Total|O número de vezes que as mensagens foram entregues com êxito a todos os pontos de extremidade usando o roteamento do Hub IoT. Se uma mensagem é encaminhada para vários pontos de extremidade, esse valor aumenta em um para cada entrega bem-sucedida. Se uma mensagem é entregue ao mesmo ponto de extremidade várias vezes, esse valor aumenta em um para cada entrega bem-sucedida.|Sem dimensões|
|d2c.telemetry.ingress.allProtocol|Yes|Tentativas de envio de mensagem de telemetria|Contagem|Total|Número de tentativas de envio de mensagens de telemetria do dispositivo para nuvem para o hub IoT|Sem dimensões|
|d2c.telemetry.ingress.sendThrottle|Yes|Número de erros de limitação|Contagem|Total|Número de erros de limitação devido a limitações da taxa de transferência do dispositivo|Sem dimensões|
|d2c.telemetry.ingress.success|Yes|Mensagens de telemetria enviadas|Contagem|Total|Número de mensagens de telemetria do dispositivo para nuvem enviadas com êxito para o hub IoT|Sem dimensões|
|d2c.twin.read.failure|Yes|Leituras de gêmeos dos dispositivos com falhas|Contagem|Total|A contagem de todas as leituras de gêmeos iniciadas pelo dispositivo com falhas.|Sem dimensões|
|d2c.twin.read.size|Yes|Tamanho da resposta das leituras de gêmeos dos dispositivos|Bytes|Média|A média, o mínimo e o máximo de todas as leituras de gêmeos iniciadas pelo dispositivo bem-sucedidas.|Sem dimensões|
|d2c.twin.read.success|Yes|Leituras de gêmeos dos dispositivos bem-sucedidas|Contagem|Total|A contagem de todas as leituras de gêmeos iniciadas pelo dispositivo bem-sucedidas.|Sem dimensões|
|d2c.twin.update.failure|Yes|Atualizações de gêmeos dos dispositivos com falhas|Contagem|Total|A contagem de todas as atualizações de gêmeos iniciadas pelo dispositivo.|Sem dimensões|
|d2c.twin.update.size|Yes|Tamanho das atualizações de gêmeos dos dispositivos|Bytes|Média|A média, o mínimo e o máximo de todas as atualizações de gêmeos iniciadas pelo dispositivo bem-sucedidas.|Sem dimensões|
|d2c.twin.update.success|Yes|Atualizações de gêmeos dos dispositivos bem-sucedidas|Contagem|Total|A contagem de todas as atualizações de gêmeos iniciadas pelo dispositivo bem-sucedidas.|Sem dimensões|
|dailyMessageQuotaUsed|Yes|Número total de mensagens usadas|Contagem|Máximo|Número total de mensagens usadas hoje|Sem dimensões|
|deviceDataUsage|Yes|Uso total de dados do dispositivo|Bytes|Total|Bytes transferidos entre todos os dispositivos conectados ao lotHub|Sem dimensões|
|deviceDataUsageV2|Yes|Uso total de dados de dispositivos (versão prévia)|Bytes|Total|Bytes transferidos entre todos os dispositivos conectados ao lotHub|Sem dimensões|
|devices.connectedDevices.allProtocol|Yes|Dispositivos conectados (preteridos) |Contagem|Total|Número de dispositivos registrados ao seu hub IoT|Sem dimensões|
|devices.totalDevices|Yes|Total de dispositivos (preterido)|Contagem|Total|Número de dispositivos registrados para o seu hub IoT|Sem dimensões|
|EventGridDeliveries|Yes|Entregas da grade de eventos|Contagem|Total|O número de eventos do Hub IoT publicados na grade de eventos. Use a dimensão de resultado para o número de solicitações bem-sucedidas e com falha. A dimensão EventType mostra o tipo de evento ( https://aka.ms/ioteventgrid) .|Resultado, EventType|
|EventGridLatency|Yes|Latência da grade de eventos|Milissegundos|Média|A latência média (milissegundos) de quando o evento do Hub IOT foi gerado quando o evento foi publicado na grade de eventos. Esse número é uma média entre todos os tipos de evento. Use a dimensão EventType para ver a latência de um tipo específico de evento.|EventType|
|jobs.cancelJob.failure|Yes|Cancelamentos de trabalho com falhas|Contagem|Total|A contagem de todas as chamadas para cancelar um trabalho com falhas.|Sem dimensões|
|jobs.cancelJob.success|Yes|Cancelamentos de trabalho bem-sucedidos|Contagem|Total|A contagem de todas as chamadas para cancelar um trabalho bem-sucedidas.|Sem dimensões|
|jobs.completed|Yes|Trabalhos concluídos|Contagem|Total|A contagem de todos os trabalhos concluídos.|Sem dimensões|
|jobs.createDirectMethodJob.failure|Yes|Criações de trabalhos de invocação de método com falhas|Contagem|Total|A contagem de todas as criações de trabalhos de invocação de método direto com falhas.|Sem dimensões|
|jobs.createDirectMethodJob.success|Yes|Criações de trabalhos de invocação de método bem-sucedidas|Contagem|Total|A contagem de todas as criações de trabalhos de invocação de método direto bem-sucedidas.|Sem dimensões|
|jobs.createTwinUpdateJob.failure|Yes|Criações de trabalhos de atualização de gêmeos com falhas|Contagem|Total|A contagem de todos os trabalhos de criação de atualização de gêmeos com falhas.|Sem dimensões|
|jobs.createTwinUpdateJob.success|Yes|Criações de trabalhos de atualização de gêmeos bem-sucedidas|Contagem|Total|A contagem de todos os trabalhos de criação de atualização de gêmeos bem-sucedidos.|Sem dimensões|
|jobs.failed|Yes|Trabalhos com falha|Contagem|Total|A contagem de todos os trabalhos com falha.|Sem dimensões|
|jobs.listJobs.failure|Yes|Chamadas para listar trabalhos com falhas|Contagem|Total|A contagem de todas as chamadas para listar trabalhos com falhas.|Sem dimensões|
|jobs.listJobs.success|Yes|Chamadas para listar trabalhos bem-sucedidas|Contagem|Total|A contagem de todas as chamadas para listar trabalhos bem-sucedidas.|Sem dimensões|
|jobs.queryJobs.failure|Yes|Consultas de trabalho com falhas|Contagem|Total|A contagem de todas as chamadas para consultas de trabalho com falhas.|Sem dimensões|
|jobs.queryJobs.success|Yes|Consultas de trabalho bem-sucedidas|Contagem|Total|A contagem de todas as chamadas para consultas de trabalho bem-sucedidas.|Sem dimensões|
|RoutingDataSizeInBytesDelivered|Yes|Tamanho da mensagem de entrega de roteamento em bytes (versão prévia)|Bytes|Total|O tamanho total em bytes de mensagens entregues pelo Hub IoT a um ponto de extremidade. Você pode usar as dimensões EndpointName e EndpointType para exibir o tamanho das mensagens em bytes entregues aos diferentes pontos de extremidade. O valor de métrica aumenta para cada mensagem entregue, incluindo se a mensagem é entregue a vários pontos de extremidade ou se a mensagem é entregue ao mesmo ponto de extremidade várias vezes.|EndpointType, EndpointName, roteamento|
|RoutingDeliveries|Yes|Entregas de roteamento (visualização)|Contagem|Total|O número de vezes que o Hub IoT tentou entregar mensagens a todos os pontos de extremidade usando o roteamento. Para ver o número de tentativas bem-sucedidas ou com falha, use a dimensão de resultado. Para ver o motivo da falha, como inválida, descartada ou órfã, use a dimensão FailureReasonCategory. Você também pode usar as dimensões EndpointName e EndpointType para entender quantas mensagens foram entregues aos diferentes pontos de extremidade. O valor da métrica aumenta em uma para cada tentativa de entrega, incluindo se a mensagem é entregue a vários pontos de extremidade ou se a mensagem é entregue ao mesmo ponto de extremidades várias vezes.|EndpointType, EndpointName, FailureReasonCategory, resultado, roteamento|
|RoutingDeliveryLatency|Yes|Latência de entrega de roteamento (visualização)|Milissegundos|Média|A latência média (milissegundos) entre a entrada da mensagem e o Hub IoT e a entrada da mensagem de telemetria em um ponto de extremidade. Você pode usar as dimensões EndpointName e EndpointType para entender a latência para seus pontos de extremidade diferentes.|EndpointType, EndpointName, roteamento|
|totalDeviceCount|No|Total de dispositivos|Contagem|Média|Número de dispositivos registrados para o seu hub IoT|Sem dimensões|
|twinQueries.failure|Yes|Consultas de gêmeos com falhas|Contagem|Total|A contagem de todas as consultas de gêmeos com falhas.|Sem dimensões|
|twinQueries.resultSize|Yes|Tamanho do resultado das consultas de gêmeos|Bytes|Média|A média, o mínimo e o máximo do tamanho do resultado de todas as consultas de gêmeos bem-sucedidas.|Sem dimensões|
|twinQueries.success|Yes|Consultas de gêmeos bem-sucedidas|Contagem|Total|A contagem de todas as consultas de gêmeos bem-sucedidas.|Sem dimensões|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|AttestationAttempts|Yes|Tentativas de atestado|Contagem|Total|Número de tentativas de atestado do dispositivo|ProvisioningServiceName, Status, Protocol|
|DeviceAssignments|Yes|Dispositivos atribuídos|Contagem|Total|Número de dispositivos atribuídos a um hub IoT|ProvisioningServiceName, IotHubName|
|RegistrationAttempts|Yes|Tentativas de Registro|Contagem|Total|Número de tentativas de registros do dispositivo|ProvisioningServiceName, IotHubName, Status|


## <a name="microsoftdigitaltwinsdigitaltwinsinstances"></a>Microsoft. DigitalTwins/digitalTwinsInstances

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ApiRequests|Yes|Solicitações da API|Contagem|Total|O número de solicitações de API feitas para operações de leitura, gravação, exclusão e consulta gêmeos digitais.|Operação, autenticação, protocolo, StatusCode, StatusCodeClass, StatusText|
|ApiRequestsFailureRate|Yes|Taxa de falha de solicitações de API|Porcentagem|Média|A porcentagem de solicitações de API que o serviço recebe para sua instância que retorna um código de resposta de erro interno (500) para operações de leitura, gravação, exclusão e consulta de gêmeos digital.|Operação, autenticação, protocolo|
|ApiRequestsLatency|Yes|Latência de solicitações de API|Milissegundos|Média|O tempo de resposta para solicitações de API, ou seja, de quando a solicitação é recebida pelo Azure digital gêmeos até que o serviço envie um resultado de êxito/falha para operações de leitura, gravação, exclusão e consulta de gêmeos digital.|Operação, autenticação, protocolo, StatusCode, StatusCodeClass, StatusText|
|BillingApiOperations|Yes|Operações de API de cobrança|Contagem|Total|Métrica de cobrança para a contagem de todas as solicitações de API feitas no serviço de Gêmeosção digital do Azure.|MeterId|
|BillingMessagesProcessed|Yes|Mensagens de cobrança processadas|Contagem|Total|Métrica de cobrança para o número de mensagens enviadas do Azure digital gêmeos para pontos de extremidade externos.|MeterId|
|BillingQueryUnits|Yes|Unidades de consulta de cobrança|Contagem|Total|O número de unidades de consulta, uma medida interna computada de uso de recursos de serviço, consumida para executar consultas.|MeterId|
|IngressEvents|Yes|Eventos de entrada|Contagem|Total|O número de eventos de telemetria de entrada no Azure digital gêmeos.|Result|
|IngressEventsFailureRate|Yes|Taxa de falha de eventos de entrada|Porcentagem|Média|A porcentagem de eventos de telemetria de entrada para os quais o serviço retorna um código de resposta de erro interno (500).|Sem dimensões|
|IngressEventsLatency|Yes|Latência de eventos de entrada|Milissegundos|Média|A hora de quando um evento chega quando está pronto para ser enviado pelo gêmeos digital do Azure, em que ponto o serviço envia um resultado de êxito/falha.|Result|
|ModelCount|Yes|Contagem de Modelos|Contagem|Total|Número total de modelos na instância do gêmeos digital do Azure. Use essa métrica para determinar se você está se aproximando do limite de serviço para o número máximo de modelos permitidos por instância.|Sem dimensões|
|Roteamento|Yes|Mensagens roteadas|Contagem|Total|O número de mensagens roteadas para um serviço do Azure de ponto de extremidade, como o Hub de eventos, o barramento de serviço ou a grade de eventos.|EndpointType, resultado|
|RoutingFailureRate|Yes|Taxa de falha de roteamento|Porcentagem|Média|A porcentagem de eventos que resultam em um erro à medida que são roteados do Azure digital gêmeos para um serviço do Azure de ponto de extremidade, como o Hub de eventos, o barramento de serviço ou a grade de eventos.|EndpointType|
|RoutingLatency|Yes|Latência de roteamento|Milissegundos|Média|Tempo decorrido entre um evento sendo roteado do Azure digital gêmeos para quando ele é Postado para o serviço do Azure do ponto de extremidade, como o Hub de eventos, o barramento de serviço ou a grade de eventos.|EndpointType, resultado|
|TwinCount|Yes|Contagem de entrelaçamento|Contagem|Total|Número total de gêmeos na instância do gêmeos digital do Azure. Use essa métrica para determinar se você está se aproximando do limite de serviço para o número máximo de gêmeos permitidas por instância.|Sem dimensões|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Addregion|Yes|Região adicionada|Contagem|Contagem|Região adicionada|Região|
|AutoscaleMaxThroughput|No|Taxa de transferência máxima de dimensionamento automático|Contagem|Máximo|Taxa de transferência máxima de dimensionamento automático|DatabaseName, CollectionName|
|AvailableStorage|No|preterido Armazenamento disponível|Bytes|Total|O "armazenamento disponível" será removido do Azure Monitor no final de setembro de 2023. O tamanho do armazenamento de coleta de Cosmos DB agora é ilimitado. A única restrição é que o tamanho do armazenamento para cada chave de partição lógica é de 20 GB. Você pode habilitar o PartitionKeyStatistics no log de diagnóstico para saber o consumo de armazenamento para as principais chaves de partição. Para obter mais informações sobre Cosmos DB cota de armazenamento, consulte este documento https://docs.microsoft.com/azure/cosmos-db/concepts-limits . Após a reprovação, as demais regras de alerta ainda definidas na métrica preterida serão desabilitadas automaticamente após a data de reprovação.|CollectionName, DatabaseName, região|
|CassandraConnectionClosures|No|Fechamentos de Conexão do Cassandra|Contagem|Total|Número de conexões Cassandra que foram fechadas, relatadas a uma granularidade de 1 minuto|Região, ClosureReason|
|CassandraConnectorAvgReplicationLatency|No|ReplicationLatency do conector Cassandra média|MilliSeconds|Média|ReplicationLatency do conector Cassandra média|Sem dimensões|
|CassandraConnectorReplicationHealthStatus|No|Status de integridade da replicação do conector Cassandra|Contagem|Contagem|Status de integridade da replicação do conector Cassandra|Não iniciado, ReplicationInProgress, erro|
|CassandraKeyspaceCreate|No|Keyspace Cassandra criado|Contagem|Contagem|Keyspace Cassandra criado|Source |
|CassandraKeyspaceDelete|No|Cassandra keyspace excluído|Contagem|Contagem|Cassandra keyspace excluído|Source |
|CassandraKeyspaceThroughputUpdate|No|Taxa de transferência de keyspace Cassandra atualizada|Contagem|Contagem|Taxa de transferência de keyspace Cassandra atualizada|Source |
|CassandraKeyspaceUpdate|No|Cassandra keyspace atualizado|Contagem|Contagem|Cassandra keyspace atualizado|Source |
|CassandraRequestCharges|No|Encargos de Solicitação do Cassandra|Contagem|Total|RUs consumido para solicitações Cassandra feitas|DatabaseName, CollectionName, Region, OperationType, ResourceType|
|CassandraRequests|No|Solicitações do Cassandra|Contagem|Contagem|Número de solicitações Cassandra feitas|DatabaseName, CollectionName, região, OperationType, ResourceType, ErrorCode|
|CassandraTableCreate|No|Tabela Cassandra criada|Contagem|Contagem|Tabela Cassandra criada|ResourceName, ChildResourceName, |
|CassandraTableDelete|No|Tabela Cassandra excluída|Contagem|Contagem|Tabela Cassandra excluída|ResourceName, ChildResourceName, |
|CassandraTableThroughputUpdate|No|Taxa de transferência de tabela Cassandra atualizada|Contagem|Contagem|Taxa de transferência de tabela Cassandra atualizada|ResourceName, ChildResourceName, |
|CassandraTableUpdate|No|Tabela Cassandra atualizada|Contagem|Contagem|Tabela Cassandra atualizada|ResourceName, ChildResourceName, |
|CreateAccount|Yes|Conta criada|Contagem|Contagem|Conta criada|Sem dimensões|
|DataUsage|No|Uso de dados|Bytes|Total|Uso total de dados relatados à granularidade de 5 minutos|CollectionName, DatabaseName, região|
|DeleteAccount|Yes|Conta excluída|Contagem|Contagem|Conta excluída|Sem dimensões|
|DocumentCount|No|Contagem de documentos|Contagem|Total|Contagem total de documentos relatados à granularidade de 5 minutos|CollectionName, DatabaseName, região|
|DocumentQuota|No|Cota de documento|Bytes|Total|Cota de armazenamento total relatada à granularidade de 5 minutos|CollectionName, DatabaseName, região|
|GremlinDatabaseCreate|No|Banco de dados Gremlin criado|Contagem|Contagem|Banco de dados Gremlin criado|Source |
|GremlinDatabaseDelete|No|Banco de dados Gremlin excluído|Contagem|Contagem|Banco de dados Gremlin excluído|Source |
|GremlinDatabaseThroughputUpdate|No|Taxa de transferência do banco de dados Gremlin atualizada|Contagem|Contagem|Taxa de transferência do banco de dados Gremlin atualizada|Source |
|GremlinDatabaseUpdate|No|Banco de dados Gremlin atualizado|Contagem|Contagem|Banco de dados Gremlin atualizado|Source |
|GremlinGraphCreate|No|Grafo Gremlin criado|Contagem|Contagem|Grafo Gremlin criado|ResourceName, ChildResourceName, |
|GremlinGraphDelete|No|Grafo Gremlin excluído|Contagem|Contagem|Grafo Gremlin excluído|ResourceName, ChildResourceName, |
|GremlinGraphThroughputUpdate|No|Taxa de transferência do grafo Gremlin atualizada|Contagem|Contagem|Taxa de transferência do grafo Gremlin atualizada|ResourceName, ChildResourceName, |
|GremlinGraphUpdate|No|Grafo Gremlin atualizado|Contagem|Contagem|Grafo Gremlin atualizado|ResourceName, ChildResourceName, |
|IndexUsage|No|Uso do índice|Bytes|Total|Uso total do índice relatado à granularidade de 5 minutos|CollectionName, DatabaseName, região|
|IntegratedCacheEvictedEntriesSize|No|IntegratedCacheEvictedEntriesSize|Bytes|Média|Tamanho das entradas removidas do cache integrado|CacheType, região|
|IntegratedCacheHitRate|No|IntegratedCacheHitRate|Porcentagem|Média|Taxa de acesso do cache para caches integrados|CacheType, região|
|IntegratedCacheSize|No|IntegratedCacheSize|Bytes|Média|Tamanho dos caches integrados para solicitações de gateway dedicadas|CacheType, região|
|IntegratedCacheTTLExpirationCount|No|IntegratedCacheTTLExpirationCount|Contagem|Média|Número de entradas removidas do cache integrado devido à expiração TTL|CacheType, região|
|MetadataRequests|No|MetadataRequests|Contagem|Contagem|Contagem de solicitações de metadados. O Cosmos DB mantém uma coleção de metadados do sistema para cada conta, o que permite que você enumere coleções, bancos de dados, etc, e suas configurações, gratuitamente.|DatabaseName, CollectionName, região, StatusCode, |
|MongoCollectionCreate|No|Coleção Mongo criada|Contagem|Contagem|Coleção Mongo criada|ResourceName, ChildResourceName, |
|MongoCollectionDelete|No|Coleção Mongo excluída|Contagem|Contagem|Coleção Mongo excluída|ResourceName, ChildResourceName, |
|MongoCollectionThroughputUpdate|No|Taxa de transferência de coleção Mongo atualizada|Contagem|Contagem|Taxa de transferência de coleção Mongo atualizada|ResourceName, ChildResourceName, |
|MongoCollectionUpdate|No|Coleção Mongo atualizada|Contagem|Contagem|Coleção Mongo atualizada|ResourceName, ChildResourceName, |
|MongoDatabaseDelete|No|Banco de dados Mongo excluído|Contagem|Contagem|Banco de dados Mongo excluído|Source |
|MongoDatabaseThroughputUpdate|No|Taxa de transferência do banco de dados Mongo atualizada|Contagem|Contagem|Taxa de transferência do banco de dados Mongo atualizada|Source |
|MongoDBDatabaseCreate|No|Banco de dados Mongo criado|Contagem|Contagem|Banco de dados Mongo criado|Source |
|MongoDBDatabaseUpdate|No|Banco de dados Mongo atualizado|Contagem|Contagem|Banco de dados Mongo atualizado|Source |
|MongoRequestCharge|Yes|MongoRequestCharge|Contagem|Total|Unidades Solicitadas do Mongo Consumidas|DatabaseName, CollectionName, região, CommandName, ErrorCode, status|
|MongoRequests|Yes|Solicitações do Mongo|Contagem|Contagem|Número de Solicitações do Mongo Feitas|DatabaseName, CollectionName, região, CommandName, ErrorCode, status|
|MongoRequestsCount|No|preterido Taxa de solicitação de Mongo|CountPerSecond|Média|Contagem de solicitações Mongo por segundo|DatabaseName, CollectionName, região, ErrorCode|
|MongoRequestsDelete|No|preterido Taxa de solicitação de exclusão de Mongo|CountPerSecond|Média|Solicitação de exclusão de Mongo por segundo|DatabaseName, CollectionName, região, ErrorCode|
|MongoRequestsInsert|No|preterido Taxa de solicitação de inserção de Mongo|CountPerSecond|Média|Contagem de inserção de Mongo por segundo|DatabaseName, CollectionName, região, ErrorCode|
|MongoRequestsQuery|No|preterido Taxa de solicitação de consulta do Mongo|CountPerSecond|Média|Solicitação de consulta Mongo por segundo|DatabaseName, CollectionName, região, ErrorCode|
|MongoRequestsUpdate|No|preterido Taxa de solicitação de atualização do Mongo|CountPerSecond|Média|Solicitação de atualização de Mongo por segundo|DatabaseName, CollectionName, região, ErrorCode|
|NormalizedRUConsumption|No|Consumo de RU normalizado|Porcentagem|Máximo|Porcentagem máxima de consumo de RU por minuto|CollectionName, DatabaseName, região, PartitionKeyRangeId|
|ProvisionedThroughput|No|Taxa de transferência provisionada|Contagem|Máximo|Taxa de transferência provisionada|DatabaseName, CollectionName|
|RegionFailover|Yes|Falha na região|Contagem|Contagem|Falha na região|Sem dimensões|
|RemoveRegion|Yes|Região removida|Contagem|Contagem|Região removida|Região|
|ReplicationLatency|Yes|Latência de Replicação P99|MilliSeconds|Média|Latência de replicação P99 nas regiões de origem e de destino para conta habilitada geograficamente|SourceRegion, TargetRegion|
|ServerSideLatency|No|Latência do lado do servidor|MilliSeconds|Média|Latência do lado do servidor|DatabaseName, CollectionName, região, ConnectionMode, OperationType, PublicAPIType|
|ServiceAvailability|No|Disponibilidade do serviço|Porcentagem|Média|Disponibilidade de solicitações de conta em uma hora, granularidade de dia ou mês|Sem dimensões|
|SqlContainerCreate|No|Contêiner SQL criado|Contagem|Contagem|Contêiner SQL criado|ResourceName, ChildResourceName, |
|SqlContainerDelete|No|Contêiner SQL excluído|Contagem|Contagem|Contêiner SQL excluído|ResourceName, ChildResourceName, |
|SqlContainerThroughputUpdate|No|Produtividade do contêiner SQL atualizada|Contagem|Contagem|Produtividade do contêiner SQL atualizada|ResourceName, ChildResourceName, |
|SqlContainerUpdate|No|Contêiner SQL atualizado|Contagem|Contagem|Contêiner SQL atualizado|ResourceName, ChildResourceName, |
|SqlDatabaseCreate|No|Banco de dados SQL criado|Contagem|Contagem|Banco de dados SQL criado|Source |
|SqlDatabaseDelete|No|Banco de dados SQL excluído|Contagem|Contagem|Banco de dados SQL excluído|Source |
|SqlDatabaseThroughputUpdate|No|Produtividade do banco de dados SQL atualizada|Contagem|Contagem|Produtividade do banco de dados SQL atualizada|Source |
|SqlDatabaseUpdate|No|Banco de dados SQL atualizado|Contagem|Contagem|Banco de dados SQL atualizado|Source |
|TableTableCreate|No|Tabela do azuretable criada|Contagem|Contagem|Tabela do azuretable criada|Source |
|TableTableDelete|No|Tabela do azuretable excluída|Contagem|Contagem|Tabela do azuretable excluída|Source |
|TableTableThroughputUpdate|No|Taxa de transferência de tabela do azuretable atualizada|Contagem|Contagem|Taxa de transferência de tabela do azuretable atualizada|Source |
|TableTableUpdate|No|Tabela do azuretable atualizada|Contagem|Contagem|Tabela do azuretable atualizada|Source |
|TotalRequests|Yes|Total de Solicitações|Contagem|Contagem|Número de solicitações feitas|DatabaseName, CollectionName, região, StatusCode, OperationType, status|
|TotalRequestUnits|Yes|Total de Unidades Solicitadas|Contagem|Total|Unidades Solicitadas Consumidas|DatabaseName, CollectionName, região, StatusCode, OperationType, status|
|UpdateAccountKeys|Yes|Chaves de conta atualizadas|Contagem|Contagem|Chaves de conta atualizadas|KeyType|
|UpdateAccountNetworkSettings|Yes|Configurações de rede da conta atualizadas|Contagem|Contagem|Configurações de rede da conta atualizadas|Sem dimensões|
|UpdateAccountReplicationSettings|Yes|Configurações de replicação de conta atualizadas|Contagem|Contagem|Configurações de replicação de conta atualizadas|Sem dimensões|
|UpdateDiagnosticsSettings|No|Configurações de diagnóstico da conta atualizadas|Contagem|Contagem|Configurações de diagnóstico da conta atualizadas|DiagnosticSettingsName, ResourceGroupName|


## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Yes|Avaliações de filtro avançado|Contagem|Total|Total de filtros avançados avaliados em assinaturas de evento para este tópico.|Tópico, EventSubscriptionName, DomainEventSubscriptionName|
|DeadLetteredCount|Yes|Eventos de mensagens mortas|Contagem|Total|Total de eventos de mensagens mortas correspondentes a essa assinatura de evento|Tópico, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|
|DeliveryAttemptFailCount|No|Eventos com falha de entrega|Contagem|Total|Total de eventos não entregues a essa assinatura de evento|Tópico, EventSubscriptionName, DomainEventSubscriptionName, erro, ErrorType|
|DeliverySuccessCount|Yes|Eventos entregues|Contagem|Total|Total de eventos entregues a essa assinatura de evento|Tópico, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|No|Duração do processamento de destino|Milissegundos|Média|Duração do processamento de destino em milissegundos|Tópico, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Yes|Eventos removidos|Contagem|Total|Total de eventos removidos correspondentes a essa assinatura de evento|Tópico, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|MatchedEventCount|Yes|Eventos correspondentes|Contagem|Total|Total de eventos correspondentes a essa assinatura de evento|Tópico, EventSubscriptionName, DomainEventSubscriptionName|
|PublishFailCount|Yes|Publicar Eventos com Falha|Contagem|Total|Total de eventos com falha ao publicar neste tópico|Tópico, ErrorType, erro|
|PublishSuccessCount|Yes|Eventos publicados|Contagem|Total|Total de eventos publicados neste tópico|Tópico|
|PublishSuccessLatencyInMs|Yes|Publicar Latência de Êxito|Milissegundos|Total|Latência de êxito de publicação em milissegundos|Sem dimensões|


## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|Eventos de mensagens mortas|Contagem|Total|Total de eventos de mensagens mortas correspondentes a essa assinatura de evento|DeadLetterReason|
|DeliveryAttemptFailCount|No|Eventos com falha de entrega|Contagem|Total|Total de eventos não entregues a essa assinatura de evento|Error, ErrorType|
|DeliverySuccessCount|Yes|Eventos entregues|Contagem|Total|Total de eventos entregues a essa assinatura de evento|Sem dimensões|
|DestinationProcessingDurationInMs|No|Duração do processamento de destino|Milissegundos|Média|Duração do processamento de destino em milissegundos|Sem dimensões|
|DroppedEventCount|Yes|Eventos removidos|Contagem|Total|Total de eventos removidos correspondentes a essa assinatura de evento|DropReason|
|MatchedEventCount|Yes|Eventos correspondentes|Contagem|Total|Total de eventos correspondentes a essa assinatura de evento|Sem dimensões|


## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|PublishFailCount|Yes|Publicar Eventos com Falha|Contagem|Total|Total de eventos com falha ao publicar neste tópico|ErrorType, Error|
|PublishSuccessCount|Yes|Eventos publicados|Contagem|Total|Total de eventos publicados neste tópico|Sem dimensões|
|PublishSuccessLatencyInMs|Yes|Publicar Latência de Êxito|Milissegundos|Total|Latência de êxito de publicação em milissegundos|Sem dimensões|
|UnmatchedEventCount|Yes|Eventos sem correspondência|Contagem|Total|Total de eventos sem correspondência com nenhuma das assinaturas de evento para este tópico|Sem dimensões|


## <a name="microsofteventgridpartnernamespaces"></a>Microsoft. EventGrid/partnerNamespaces

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|Eventos de mensagens mortas|Contagem|Total|Total de eventos de mensagens mortas correspondentes a essa assinatura de evento|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Eventos com falha de entrega|Contagem|Total|Total de eventos não entregues a essa assinatura de evento|Erro, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Yes|Eventos entregues|Contagem|Total|Total de eventos entregues a essa assinatura de evento|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Duração do processamento de destino|Milissegundos|Média|Duração do processamento de destino em milissegundos|EventSubscriptionName|
|DroppedEventCount|Yes|Eventos removidos|Contagem|Total|Total de eventos removidos correspondentes a essa assinatura de evento|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Eventos correspondentes|Contagem|Total|Total de eventos correspondentes a essa assinatura de evento|EventSubscriptionName|
|PublishFailCount|Yes|Publicar Eventos com Falha|Contagem|Total|Total de eventos com falha ao publicar neste tópico|ErrorType, Error|
|PublishSuccessCount|Yes|Eventos publicados|Contagem|Total|Total de eventos publicados neste tópico|Sem dimensões|
|PublishSuccessLatencyInMs|Yes|Publicar Latência de Êxito|Milissegundos|Total|Latência de êxito de publicação em milissegundos|Sem dimensões|
|UnmatchedEventCount|Yes|Eventos sem correspondência|Contagem|Total|Total de eventos sem correspondência com nenhuma das assinaturas de evento para este tópico|Sem dimensões|


## <a name="microsofteventgridpartnertopics"></a>Microsoft. EventGrid/partnerTopics

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Yes|Avaliações de filtro avançado|Contagem|Total|Total de filtros avançados avaliados em assinaturas de evento para este tópico.|EventSubscriptionName|
|DeadLetteredCount|Yes|Eventos de mensagens mortas|Contagem|Total|Total de eventos de mensagens mortas correspondentes a essa assinatura de evento|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Eventos com falha de entrega|Contagem|Total|Total de eventos não entregues a essa assinatura de evento|Erro, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Yes|Eventos entregues|Contagem|Total|Total de eventos entregues a essa assinatura de evento|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Duração do processamento de destino|Milissegundos|Média|Duração do processamento de destino em milissegundos|EventSubscriptionName|
|DroppedEventCount|Yes|Eventos removidos|Contagem|Total|Total de eventos removidos correspondentes a essa assinatura de evento|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Eventos correspondentes|Contagem|Total|Total de eventos correspondentes a essa assinatura de evento|EventSubscriptionName|
|PublishFailCount|Yes|Publicar Eventos com Falha|Contagem|Total|Total de eventos com falha ao publicar neste tópico|ErrorType, Error|
|PublishSuccessCount|Yes|Eventos publicados|Contagem|Total|Total de eventos publicados neste tópico|Sem dimensões|
|UnmatchedEventCount|Yes|Eventos sem correspondência|Contagem|Total|Total de eventos sem correspondência com nenhuma das assinaturas de evento para este tópico|Sem dimensões|


## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Yes|Avaliações de filtro avançado|Contagem|Total|Total de filtros avançados avaliados em assinaturas de evento para este tópico.|EventSubscriptionName|
|DeadLetteredCount|Yes|Eventos de mensagens mortas|Contagem|Total|Total de eventos de mensagens mortas correspondentes a essa assinatura de evento|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Eventos com falha de entrega|Contagem|Total|Total de eventos não entregues a essa assinatura de evento|Erro, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Yes|Eventos entregues|Contagem|Total|Total de eventos entregues a essa assinatura de evento|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Duração do processamento de destino|Milissegundos|Média|Duração do processamento de destino em milissegundos|EventSubscriptionName|
|DroppedEventCount|Yes|Eventos removidos|Contagem|Total|Total de eventos removidos correspondentes a essa assinatura de evento|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Eventos correspondentes|Contagem|Total|Total de eventos correspondentes a essa assinatura de evento|EventSubscriptionName|
|PublishFailCount|Yes|Publicar Eventos com Falha|Contagem|Total|Total de eventos com falha ao publicar neste tópico|ErrorType, Error|
|PublishSuccessCount|Yes|Eventos publicados|Contagem|Total|Total de eventos publicados neste tópico|Sem dimensões|
|PublishSuccessLatencyInMs|Yes|Publicar Latência de Êxito|Milissegundos|Total|Latência de êxito de publicação em milissegundos|Sem dimensões|
|UnmatchedEventCount|Yes|Eventos sem correspondência|Contagem|Total|Total de eventos sem correspondência com nenhuma das assinaturas de evento para este tópico|Sem dimensões|


## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Yes|Avaliações de filtro avançado|Contagem|Total|Total de filtros avançados avaliados em assinaturas de evento para este tópico.|EventSubscriptionName|
|DeadLetteredCount|Yes|Eventos de mensagens mortas|Contagem|Total|Total de eventos de mensagens mortas correspondentes a essa assinatura de evento|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Eventos com falha de entrega|Contagem|Total|Total de eventos não entregues a essa assinatura de evento|Erro, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Yes|Eventos entregues|Contagem|Total|Total de eventos entregues a essa assinatura de evento|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Duração do processamento de destino|Milissegundos|Média|Duração do processamento de destino em milissegundos|EventSubscriptionName|
|DroppedEventCount|Yes|Eventos removidos|Contagem|Total|Total de eventos removidos correspondentes a essa assinatura de evento|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Eventos correspondentes|Contagem|Total|Total de eventos correspondentes a essa assinatura de evento|EventSubscriptionName|
|PublishFailCount|Yes|Publicar Eventos com Falha|Contagem|Total|Total de eventos com falha ao publicar neste tópico|ErrorType, Error|
|PublishSuccessCount|Yes|Eventos publicados|Contagem|Total|Total de eventos publicados neste tópico|Sem dimensões|
|PublishSuccessLatencyInMs|Yes|Publicar Latência de Êxito|Milissegundos|Total|Latência de êxito de publicação em milissegundos|Sem dimensões|
|UnmatchedEventCount|Yes|Eventos sem correspondência|Contagem|Total|Total de eventos sem correspondência com nenhuma das assinaturas de evento para este tópico|Sem dimensões|


## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ActiveConnections|No|ActiveConnections|Contagem|Média|Total de Conexões Ativas para o Microsoft.EventHub.||
|AvailableMemory|No|Memória disponível|Porcentagem|Máximo|Memória disponível para o cluster do hub de eventos como uma porcentagem da memória total.|Função|
|CaptureBacklog|No|Lista de Pendências de Captura.|Contagem|Total|Lista de Pendências de Captura para o Microsoft.EventHub.||
|CapturedBytes|No|Bytes Capturados.|Bytes|Total|Bytes Capturados para o Microsoft.EventHub.||
|CapturedMessages|No|Mensagens Capturadas.|Contagem|Total|Mensagens Capturadas para o Microsoft.EventHub.||
|ConnectionsClosed|No|Conexões Fechadas.|Contagem|Média|Conexões Fechadas para o Microsoft.EventHub.||
|ConnectionsOpened|No|Conexões Abertas.|Contagem|Média|Conexões Abertas para o Microsoft.EventHub.||
|CPU|No|CPU|Porcentagem|Máximo|Utilização da CPU para o Cluster do Hub de Eventos como um percentual|Função|
|IncomingBytes|Yes|Bytes de Entrada.|Bytes|Total|Bytes de Entrada para o Microsoft.EventHub.||
|IncomingMessages|Yes|Mensagens de entrada|Contagem|Total|Mensagens de Entrada para o Microsoft.EventHub.||
|IncomingRequests|Yes|Solicitações de entrada|Contagem|Total|Solicitações de Entrada para o Microsoft.EventHub.||
|OutgoingBytes|Yes|Bytes de Saída.|Bytes|Total|Bytes de Saída para o Microsoft.EventHub.||
|OutgoingMessages|Yes|Mensagens de saída|Contagem|Total|Mensagens de Saída para o Microsoft.EventHub.||
|QuotaExceededErrors|No|Cota de Erros Excedida.|Contagem|Total|Cota de Erros Excedida para o Microsoft.EventHub.|OperationResult|
|ServerErrors|No|Erros do Servidor.|Contagem|Total|Erros do Servidor para o Microsoft.EventHub.|OperationResult|
|Tamanho|No|Tamanho|Bytes|Média|Tamanho de um EventHub em Bytes.|Função|
|SuccessfulRequests|No|Solicitações bem sucedidas|Contagem|Total|Solicitações Bem-sucedidas para Microsoft.EventHub.|OperationResult|
|ThrottledRequests|No|Solicitações Limitadas.|Contagem|Total|Solicitações Limitadas para o Microsoft.EventHub.|OperationResult|
|UserErrors|No|Erros de Usuário.|Contagem|Total|Erros de Usuário para o Microsoft.EventHub.|OperationResult|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ActiveConnections|No|ActiveConnections|Contagem|Média|Total de Conexões Ativas para o Microsoft.EventHub.||
|CaptureBacklog|No|Lista de Pendências de Captura.|Contagem|Total|Lista de Pendências de Captura para o Microsoft.EventHub.|EntityName|
|CapturedBytes|No|Bytes Capturados.|Bytes|Total|Bytes Capturados para o Microsoft.EventHub.|EntityName|
|CapturedMessages|No|Mensagens Capturadas.|Contagem|Total|Mensagens Capturadas para o Microsoft.EventHub.|EntityName|
|ConnectionsClosed|No|Conexões Fechadas.|Contagem|Média|Conexões Fechadas para o Microsoft.EventHub.|EntityName|
|ConnectionsOpened|No|Conexões Abertas.|Contagem|Média|Conexões Abertas para o Microsoft.EventHub.|EntityName|
|EHABL|Yes|Arquivar mensagens da lista de pendências (Preterido)|Contagem|Total|Mensagens de arquivamento do hub de eventos no backlog para um namespace (preterido)||
|EHAMBS|Yes|Arquivar taxa de transferência de mensagem (Preterido)|Bytes|Total|Taxa de transferência de mensagem arquivada do hub de eventos em um namespace (preterido)||
|EHAMSGS|Yes|Arquivar mensagens (Preterido)|Contagem|Total|Mensagens arquivadas do hub de eventos em um namespace (preterido)||
|EHINBYTES|Yes|Bytes de entrada (Preterida)|Bytes|Total|Taxa de transferência de mensagem de entrada do hub de eventos para um namespace (preterido)||
|EHINMBS|Yes|Bytes de entrada (obsoleto) (Preterido)|Bytes|Total|Taxa de transferência de mensagem de entrada do Hub de Eventos para um namespace. Essa métrica foi preterida. Use a métrica de bytes de entrada em vez disso (preterido)||
|EHINMSGS|Yes|Mensagens de Entrada (Preterida)|Contagem|Total|Total de mensagens de entrada para um namespace (preterido)||
|EHOUTBYTES|Yes|Bytes de saída (Preterida)|Bytes|Total|Taxa de transferência de mensagem de saída do hub de eventos para um namespace (preterido)||
|EHOUTMBS|Yes|Bytes de saída (obsoleto) (Preterido)|Bytes|Total|Taxa de transferência de mensagem de saída do Hub de Eventos para um namespace. Essa métrica foi preterida. Use a métrica de bytes de saída em vez disso (preterido)||
|EHOUTMSGS|Yes|Mensagens de Saída (Preterida)|Contagem|Total|Total de mensagens de saída para um namespace (preterido)||
|FAILREQ|Yes|Solicitações com Falha (Preterido)|Contagem|Total|Total de solicitações com falha para um namespace (preterido)||
|IncomingBytes|Yes|Bytes de Entrada.|Bytes|Total|Bytes de Entrada para o Microsoft.EventHub.|EntityName|
|IncomingMessages|Yes|Mensagens de entrada|Contagem|Total|Mensagens de Entrada para o Microsoft.EventHub.|EntityName|
|IncomingRequests|Yes|Solicitações de entrada|Contagem|Total|Solicitações de Entrada para o Microsoft.EventHub.|EntityName|
|INMSGS|Yes|Mensagens de entrada (obsoleto) (Preterido)|Contagem|Total|Total de mensagens de entrada para um namespace. Essa métrica foi preterida. Use a métrica de mensagens de entrada em vez disso (preterido)||
|INREQS|Yes|Solicitações de Entrada (Preterido)|Contagem|Total|Total de solicitações de envio de entrada para um namespace (preterido)||
|INTERR|Yes|Erros Internos do Servidor (Preterido)|Contagem|Total|Total de erros internos do servidor para um namespace (preterido)||
|MISCERR|Yes|Outros Erros (Preterido)|Contagem|Total|Total de solicitações com falha para um namespace (preterido)||
|OutgoingBytes|Yes|Bytes de Saída.|Bytes|Total|Bytes de Saída para o Microsoft.EventHub.|EntityName|
|OutgoingMessages|Yes|Mensagens de saída|Contagem|Total|Mensagens de Saída para o Microsoft.EventHub.|EntityName|
|OUTMSGS|Yes|Mensagens de Saída (obsoleto) (Preterido)|Contagem|Total|Total de mensagens de saída para um namespace. Essa métrica foi preterida. Use a métrica de mensagens de saída em vez disso (preterido)||
|QuotaExceededErrors|No|Cota de Erros Excedida.|Contagem|Total|Cota de Erros Excedida para o Microsoft.EventHub.|EntityName, OperationResult|
|ServerErrors|No|Erros do Servidor.|Contagem|Total|Erros do Servidor para o Microsoft.EventHub.|EntityName, OperationResult|
|Tamanho|No|Tamanho|Bytes|Média|Tamanho de um EventHub em Bytes.|EntityName|
|SuccessfulRequests|No|Solicitações bem sucedidas|Contagem|Total|Solicitações Bem-sucedidas para Microsoft.EventHub.|EntityName, OperationResult|
|SUCCREQ|Yes|Solicitações Bem-sucedidas (Preterido)|Contagem|Total|Total de solicitações bem-sucedidas para um namespace (preterido)||
|SVRBSY|Yes|Erros de Servidor Ocupado (Preterido)|Contagem|Total|Total de erros do servidor ocupado para um namespace (preterido)||
|ThrottledRequests|No|Solicitações Limitadas.|Contagem|Total|Solicitações Limitadas para o Microsoft.EventHub.|EntityName, OperationResult|
|UserErrors|No|Erros de Usuário.|Contagem|Total|Erros de Usuário para o Microsoft.EventHub.|EntityName, OperationResult|


## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|CategorizedGatewayRequests|Yes|Solicitações de gateway categorizadas|Contagem|Total|Número de solicitações de gateway por categorias (1xx/2xx/3xx/4xx/5xx)|HttpStatus|
|GatewayRequests|Yes|Solicitações de gateway|Contagem|Total|Número de solicitações de gateway|HttpStatus|
|KafkaRestProxy.ConsumerRequest.m1_delta|Yes|RequestThroughput do consumidor do proxy REST|CountPerSecond|Total|Número de solicitações de consumidor para o proxy REST do Kafka|Computador, tópico|
|KafkaRestProxy.ConsumerRequestTime.p95|Yes|RequestLatency do consumidor do proxy REST|Milissegundos|Média|Latência de mensagem em uma solicitação de consumidor por meio do proxy REST do Kafka|Computador, tópico|
|KafkaRestProxy.MessagesIn.m1_delta|Yes|MessageThroughput do produtor de proxy REST|CountPerSecond|Total|Número de mensagens de produtor por meio do proxy REST do Kafka|Computador, tópico|
|KafkaRestProxy.MessagesOut.m1_delta|Yes|MessageThroughput do consumidor do proxy REST|CountPerSecond|Total|Número de mensagens do consumidor por meio do proxy REST do Kafka|Computador, tópico|
|KafkaRestProxy.OpenConnections|Yes|ConcurrentConnections do proxy REST|Contagem|Total|Número de conexões simultâneas por meio do proxy REST Kafka|Computador, tópico|
|KafkaRestProxy.ProducerRequest.m1_delta|Yes|RequestThroughput do produtor de proxy REST|CountPerSecond|Total|Número de solicitações de produtor para o proxy REST do Kafka|Computador, tópico|
|KafkaRestProxy.ProducerRequestTime.p95|Yes|RequestLatency do produtor de proxy REST|Milissegundos|Média|Latência de mensagem em uma solicitação de produtor por meio do proxy REST do Kafka|Computador, tópico|
|NumActiveWorkers|Yes|Número de Trabalhos Ativos|Contagem|Máximo|Número de Trabalhos Ativos|MetricName|


## <a name="microsofthealthcareapisservices"></a>Microsoft.HealthcareApis/services

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Yes|Disponibilidade|Porcentagem|Média|A taxa de disponibilidade do serviço.|Sem dimensões|
|CosmosDbCollectionSize|Yes|Tamanho da coleção de Cosmos DB|Bytes|Total|O tamanho da coleção de Cosmos DB de backup, em bytes.|Sem dimensões|
|CosmosDbIndexSize|Yes|Tamanho do índice de Cosmos DB|Bytes|Total|O tamanho do índice da coleção de Cosmos DB de backup, em bytes.|Sem dimensões|
|CosmosDbRequestCharge|Yes|Uso do Cosmos DB RU|Contagem|Total|O uso do RU das solicitações para o Cosmos DB de backup do serviço.|Operação, ResourceType|
|CosmosDbRequests|Yes|Solicitações de Cosmos DB de serviço|Contagem|Somar|O número total de solicitações feitas ao Cosmos DB de backup de um serviço.|Operação, ResourceType|
|CosmosDbThrottleRate|Yes|Taxa de limitação de Cosmos DB de serviço|Contagem|Somar|O número total de 429 respostas do Cosmos DB de backup de um serviço.|Operação, ResourceType|
|IoTConnectorDeviceEvent|Yes|Número de mensagens de entrada|Contagem|Somar|O número total de mensagens recebidas pelo conector IoT do Azure para FHIR antes de qualquer normalização.|Operação, connectorname|
|IoTConnectorDeviceEventProcessingLatencyMs|Yes|Latência média de estágio de normalização|Milissegundos|Média|O tempo médio entre o tempo de ingestão de um evento e a hora em que o evento é processado para normalização.|Operação, connectorname|
|IoTConnectorMeasurement|Yes|Número de medições|Contagem|Somar|O número de leituras de valor normalizado recebidas pelo estágio de conversão FHIR do conector IoT do Azure para FHIR.|Operação, connectorname|
|IoTConnectorMeasurementGroup|Yes|Número de grupos de mensagens|Contagem|Somar|O número total de agrupamentos exclusivos de medidas em tipo, dispositivo, paciente e período de tempo configurado gerado pelo estágio de conversão FHIR.|Operação, connectorname|
|IoTConnectorMeasurementIngestionLatencyMs|Yes|Latência média de estágio de grupo|Milissegundos|Média|O período de tempo entre o momento em que o conector IoT recebeu os dados do dispositivo e quando os dados são processados pelo estágio de conversão FHIR.|Operação, connectorname|
|IoTConnectorNormalizedEvent|Yes|Número de mensagens normalizadas|Contagem|Somar|O número total de valores normalizados mapeados enviados do estágio de normalização do conector de IoT do Azure para FHIR.|Operação, connectorname|
|IoTConnectorTotalErrors|Yes|Contagem Total de Erros|Contagem|Somar|O número total de erros registrados pelo conector de IoT do Azure para FHIR|Nome, operação, ErrorType, ErrorSeverity, connectorname|
|ServiceApiErrors|Yes|Erros de serviço|Contagem|Somar|O número total de erros de servidor interno gerados pelo serviço.|Protocolo, autenticação, operação, ResourceType, StatusCode, StatusCodeClass, StatusCodeText|
|ServiceApiLatency|Yes|Latência de serviço|Milissegundos|Média|A latência de resposta do serviço.|Protocolo, autenticação, operação, ResourceType, StatusCode, StatusCodeClass, StatusCodeText|
|ServiceApiRequests|Yes|Service Requests|Contagem|Somar|O número total de solicitações recebidas pelo serviço.|Protocolo, autenticação, operação, ResourceType, StatusCode, StatusCodeClass, StatusCodeText|
|TotalErrors|Yes|Total de Erros|Contagem|Somar|O número total de erros de servidor interno encontrados pelo serviço.|Protocolo, StatusCode, StatusCodeClass, StatusCodeText|
|TotalLatency|Yes|Latência total|Milissegundos|Média|A latência de resposta do serviço.|Protocolo|
|TotalRequests|Yes|Total de Solicitações|Contagem|Somar|O número total de solicitações recebidas pelo serviço.|Protocolo|


## <a name="microsofthybridnetworknetworkfunctions"></a>Microsoft. hybridnetwork/networkfunctions

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|HyperVVirtualProcessorUtilization|Yes|Utilização da CPU média|Porcentagem|Média|Percentual médio total de utilização da CPU virtual em um intervalo de um minuto. O número total de CPU virtual é baseado no valor configurado pelo usuário na definição de SKU. Um filtro adicional pode ser aplicado com base em RoleName definido no SKU.|InstanceName|


## <a name="microsofthybridnetworkvirtualnetworkfunctions"></a>Microsoft. hybridnetwork/virtualnetworkfunctions

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|HyperVVirtualProcessorUtilization|Yes|Utilização da CPU média|Porcentagem|Média|Percentual médio total de utilização da CPU virtual em um intervalo de um minuto. O número total de CPU virtual é baseado no valor configurado pelo usuário na definição de SKU. Um filtro adicional pode ser aplicado com base em RoleName definido no SKU.|InstanceName|


## <a name="microsoftinsightsautoscalesettings"></a>microsoft.insights/autoscalesettings

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|MetricThreshold|Yes|Limite da Métrica|Contagem|Média|O limite do dimensionamento automático configurado quando o dimensionamento automático foi executado.|MetricTriggerRule|
|ObservedCapacity|Yes|Capacidade Observada|Contagem|Média|A capacidade relatada para o dimensionamento automático quando ele foi executado.||
|ObservedMetricValue|Yes|Valor de Métrica Observado|Contagem|Média|O valor calculado pelo dimensionamento automático quando executado|MetricTriggerSource|
|ScaleActionsInitiated|Yes|Ações de Escala Iniciadas|Contagem|Total|A direção da operação de escala.|ScaleDirection|


## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Yes|Disponibilidade|Porcentagem|Média|Percentual de testes de disponibilidade concluídos com êxito|availabilityResult/nome, availabilityResult/local|
|availabilityResults/count|No|Testes de disponibilidade|Contagem|Contagem|Contagem de testes de disponibilidade|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|availabilityResults/duration|Yes|Duração do teste de disponibilidade|MilliSeconds|Média|Duração do teste de disponibilidade|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|browserTimings/networkDuration|Yes|Tempo de conexão de rede de carregamento de página|MilliSeconds|Média|Tempo entre a solicitação do usuário e a conexão de rede. Inclui a pesquisa DNS e a conexão de transporte.|Sem dimensões|
|browserTimings/processingDuration|Yes|Tempo de processamento do cliente|MilliSeconds|Média|Tempo entre o recebimento do último byte de um documento até que o DOM seja carregado. As solicitações assíncronas ainda podem estar sendo processadas.|Sem dimensões|
|browserTimings/receiveDuration|Yes|Tempo de resposta de recebimento|MilliSeconds|Média|Tempo entre o primeiro e o último byte, ou até a desconexão.|Sem dimensões|
|browserTimings/sendDuration|Yes|Tempo de solicitação de envio|MilliSeconds|Média|Tempo entre a conexão de rede e o recebimento do primeiro byte.|Sem dimensões|
|browserTimings/totalDuration|Yes|Tempo de carregamento de página do navegador|MilliSeconds|Média|Tempo de solicitação do usuário até que o DOM, as imagens, os scripts e as folhas de estilo sejam carregados.|Sem dimensões|
|dependencies/count|No|Chamadas de dependência|Contagem|Contagem|Contagem de chamadas feitas pelo aplicativo a recursos externos.|dependência/tipo, dependência/performanceBucket, dependência/êxito, dependência/destino, dependência/resultCode, operação/sintética, nuvem/roleInstance, nuvem/roleName|
|dependencies/duration|Yes|Duração da dependência|MilliSeconds|Média|Duração das chamadas feitas pelo aplicativo a recursos externos.|dependência/tipo, dependência/performanceBucket, dependência/êxito, dependência/destino, dependência/resultCode, operação/sintética, nuvem/roleInstance, nuvem/roleName|
|dependencies/failed|No|Falhas de chamada de dependência|Contagem|Contagem|Contagem de chamadas de dependência com falha feitas pelo aplicativo a recursos externos.|dependência/tipo, dependência/performanceBucket, dependência/destino, dependência/resultCode, operação/sintética, nuvem/roleInstance, nuvem/roleName|
|exceptions/browser|No|Exceções de navegador|Contagem|Contagem|Contagem de exceções não identificadas lançadas no navegador.|nuvem/roleName|
|exceptions/count|Yes|Exceções|Contagem|Contagem|Contagem combinada de todas as exceções não capturadas.|cloud/roleName, cloud/roleInstance, client/type|
|exceptions/server|No|Exceções do servidor|Contagem|Contagem|Contagem de exceções não capturadas geradas no aplicativo para servidores.|cloud/roleName, cloud/roleInstance|
|pageViews/count|Yes|Visualizações de página|Contagem|Contagem|Contagem de exibições de página.|operação/sintética, nuvem/roleName|
|pageViews/duration|Yes|Tempo de carregamento de exibição de página|MilliSeconds|Média|Tempo de carregamento de exibição de página|operação/sintética, nuvem/roleName|
|performanceCounters/exceptionsPerSecond|Yes|Taxa de exceção|CountPerSecond|Média|Contagem de exceções com e sem tratamento relatadas para o Windows, incluindo exceções do .NET e exceções não gerenciadas convertidas em exceções do .NET.|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Yes|Memória disponível|Bytes|Média|Memória física disponível imediatamente para alocação a um processo ou para uso do sistema.|cloud/roleInstance|
|performanceCounters/processCpuPercentage|Yes|CPU do processo|Porcentagem|Média|O percentual de tempo decorrido que todos os threads do processo usaram o processador para executar instruções. Isso pode variar entre 0 e 100. Essa métrica indica o desempenho do processo de w3wp isoladamente.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Yes|Taxa de E/S de processamento|BytesPerSecond|Média|Total de bytes por segundo lidos e gravados em arquivos, rede e dispositivos.|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Yes|Tempo do processador|Porcentagem|Média|O percentual de tempo que o processador gasta em threads não ociosos.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|Yes|Processar bytes particulares|Bytes|Média|Memória atribuída exclusivamente aos processos do aplicativo monitorado.|cloud/roleInstance|
|performanceCounters/requestExecutionTime|Yes|Tempo de execução de solicitação HTTP|MilliSeconds|Média|Tempo de execução da solicitação mais recente.|cloud/roleInstance|
|performanceCounters/requestsInQueue|Yes|Solicitações HTTP na fila do aplicativo|Contagem|Média|Comprimento da fila de solicitação de aplicativo.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|Yes|Taxa de solicitação HTTP|CountPerSecond|Média|Taxa de todas as solicitações para o aplicativo por segundo do ASP.NET.|cloud/roleInstance|
|requests/count|No|Solicitações do servidor|Contagem|Contagem|Contagem de solicitações HTTP concluídas.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/duration|Yes|Tempo de resposta do servidor|MilliSeconds|Média|Tempo entre o recebimento de uma solicitação HTTP e a finalização do envio da resposta.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/failed|No|Solicitações falhas|Contagem|Contagem|Contagem de solicitações HTTP marcadas como com falha. Na maioria dos casos, essas são solicitações com um código de resposta >= 400 e diferente de 401.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|solicitações/taxa|No|Taxa de solicitação do servidor|CountPerSecond|Média|Taxa de solicitações de servidor por segundo|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|traces/count|Yes|Rastreamentos|Contagem|Contagem|Contagem de documentos de rastreamento|trace/severityLevel, operation/synthetic, cloud/roleName, cloud/roleInstance|


## <a name="microsoftiotcentraliotapps"></a>Microsoft.IoTCentral/IoTApps

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|C2D. Property. Read. Failure|Yes|Falha nas leituras de Propriedade do dispositivo de IoT Central|Contagem|Total|A contagem de todas as leituras de propriedade com falha iniciadas a partir de IoT Central|Sem dimensões|
|C2D. Property. Read. Success|Yes|Leituras de propriedade de dispositivo bem-sucedidas de IoT Central|Contagem|Total|A contagem de todas as leituras de propriedade bem-sucedidas iniciadas a partir de IoT Central|Sem dimensões|
|C2D. Property. Update. Failure|Yes|Falha nas atualizações de propriedade de dispositivo do IoT Central|Contagem|Total|A contagem de todas as atualizações de propriedade com falha iniciadas a partir de IoT Central|Sem dimensões|
|C2D. Property. Update. Success|Yes|Atualizações de propriedade de dispositivo bem-sucedidas do IoT Central|Contagem|Total|A contagem de todas as atualizações de propriedade bem-sucedidas iniciadas a partir de IoT Central|Sem dimensões|
|connectedDeviceCount|No|Total de dispositivos conectados|Contagem|Média|Número de dispositivos conectados a IoT Central|Sem dimensões|
|D2C. Property. Read. Failure|Yes|Falha nas leituras de Propriedade do dispositivo de dispositivos|Contagem|Total|A contagem de todas as leituras de propriedade com falha iniciadas a partir de dispositivos|Sem dimensões|
|D2C. Property. Read. Success|Yes|Leituras de propriedade de dispositivo bem-sucedidas de dispositivos|Contagem|Total|A contagem de todas as leituras de propriedade bem-sucedidas iniciadas a partir de dispositivos|Sem dimensões|
|D2C. Property. Update. Failure|Yes|Falha nas atualizações de Propriedade do dispositivo de dispositivos|Contagem|Total|A contagem de todas as atualizações de propriedade com falha iniciadas a partir de dispositivos|Sem dimensões|
|D2C. Property. Update. Success|Yes|Atualizações de propriedade de dispositivo bem-sucedidas de dispositivos|Contagem|Total|A contagem de todas as atualizações de propriedade bem-sucedidas iniciadas a partir de dispositivos|Sem dimensões|
|DataExport. erro|Yes|Erros de exportação de dados|Contagem|Total|Número de erros encontrados para exportação de dados|exportid, exportDisplayName, DestinationId, destinationDisplayName|
|DataExport. messages. Filtered|Yes|Mensagens de exportação de dados filtradas|Contagem|Total|Número de mensagens que passaram por filtros na exportação de dados|exportid, exportDisplayName, DestinationId, destinationDisplayName|
|DataExport. messages. Received|Yes|Mensagens de exportação de dados recebidas|Contagem|Total|Número de mensagens de entrada para exportação de dados, antes da filtragem e processamento de enriquecimento|exportid, exportDisplayName, DestinationId, destinationDisplayName|
|dataexportar. mensagens. gravadas|Yes|Mensagens de exportação de dados gravadas|Contagem|Total|Número de mensagens gravadas em um destino|exportid, exportDisplayName, DestinationId, destinationDisplayName|


## <a name="microsoftiotspacesgraph"></a>Microsoft.IoTSpaces/Graph

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ApiLatency|No|ApiLatency|6|0|Mede a latência de solicitações de API feitas para Microsoft. IoTSpaces em milissegundos|Sem dimensões|
|FunctionExecutionLatency|No|FunctionExecutionLatency|6|0|Mede a latência da execução da função definida pelo usuário em milissegundos para Microsoft. IoTSpaces|Sem dimensões|
|MessageEgressFailure|No|MessageEgressFailure|2|3|Pesquisa uma cadeia de caracteres localizada semelhante ao evento de contagem de falhas de medidas em contagem para Microsoft. IoTSpaces|Sem dimensões|
|MessageEgressLatency|No|MessageEgressLatency|6|0|Mede a latência do Dispatcher para outros pontos de extremidade em milissegundos para Microsoft. IoTSpaces|Sem dimensões|
|MessageEgressSuccess|No|MessageEgressSuccess|2|3|Pesquisa uma cadeia de caracteres localizada semelhante a medidas concluídas evento de contagem em contagem para Microsoft. IoTSpaces|Sem dimensões|
|ProcessingLatency|No|ProcessingLatency|6|0|Mede a latência da mensagem ingerida para o evento expedida em milissegundos para Microsoft. IoTSpaces|Sem dimensões|


## <a name="microsoftkeyvaultmanagedhsms"></a>Microsoft. keyvault/managedhsms

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|No|Disponibilidade geral do serviço|Porcentagem|Média|Disponibilidade de solicitações de serviço|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiHit|Yes|Total de ocorrências da API de Serviço|Contagem|Contagem|Número total de ocorrências da API de serviço|ActivityType, ActivityName|
|ServiceApiLatency|No|Latência geral da API de Serviço|Milissegundos|Média|Latência geral das solicitações da API de serviço|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|Yes|Total de resultados da API de Serviço|Contagem|Contagem|Número total de resultados da API de serviço|ActivityType, ActivityName, StatusCode, StatusCodeClass|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Yes|Disponibilidade geral do cofre|Porcentagem|Média|Disponibilidade de solicitações do cofre|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|SaturationShoebox|No|Saturação do cofre geral|Porcentagem|Média|Capacidade do cofre usada|Activitytable, ActivityName, transactionType|
|ServiceApiHit|Yes|Total de ocorrências da API de Serviço|Contagem|Contagem|Número total de ocorrências da API de serviço|ActivityType, ActivityName|
|ServiceApiLatency|Yes|Latência geral da API de Serviço|Milissegundos|Média|Latência geral das solicitações da API de serviço|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|Yes|Total de resultados da API de Serviço|Contagem|Contagem|Número total de resultados da API de serviço|ActivityType, ActivityName, StatusCode, StatusCodeClass|


## <a name="microsoftkubernetesconnectedclusters"></a>Microsoft. kubernetes/connectedClusters

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|capacity_cpu_cores|Yes|Número total de núcleos de CPU em um cluster conectado|Contagem|Total|Número total de núcleos de CPU em um cluster conectado||


## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|BatchBlobCount|Yes|Contagem de BLOBs do lote|Contagem|Média|Número de fontes de dados em um lote agregado para ingestão.|Banco de dados|
|BatchDuration|Yes|Duração do lote|Segundos|Média|A duração da fase de agregação no fluxo de ingestão.|Banco de dados|
|BatchesProcessed|Yes|Lotes processados|Contagem|Total|Número de lotes agregados para ingestão. Tipo de envio em lote: se o lote atingiu o tempo de envio em lote, o tamanho dos dados ou o número de limites de arquivos definidos pela política de envio em lote|Banco de dados, SealReason|
|BatchSize|Yes|Tamanho do lote|Bytes|Média|Tamanho de dados esperado não compactado em um lote agregado para ingestão.|Banco de dados|
|BlobsDropped|Yes|BLOBs removidos|Contagem|Total|Número de BLOBs rejeitados permanentemente por um componente.|Database, ComponentType, ComponentName|
|BlobsProcessed|Yes|BLOBs processados|Contagem|Total|Número de BLOBs processados por um componente.|Database, ComponentType, ComponentName|
|BlobsReceived|Yes|BLOBs recebidos|Contagem|Total|Número de BLOBs recebidos do fluxo de entrada por um componente.|Database, ComponentType, ComponentName|
|CacheUtilization|Yes|Utilização do cache|Porcentagem|Média|Nível de utilização no escopo do cluster|Sem dimensões|
|ContinuousExportMaxLatenessMinutes|Yes|Atraso máximo de exportação contínua|Contagem|Máximo|A finalização (em minutos) relatada pelos trabalhos de exportação contínua no cluster|Sem dimensões|
|ContinuousExportNumOfRecordsExported|Yes|Exportação contínua – núm de registros exportados|Contagem|Total|Número de registros exportados, acionados para cada artefato de armazenamento gravado durante a operação de exportação|ContinuousExportName, banco de dados|
|ContinuousExportPendingCount|Yes|Contagem de Exportação Contínua Pendente|Contagem|Máximo|O número de trabalhos de exportação contínuos pendentes prontos para execução|Sem dimensões|
|ContinuousExportResult|Yes|Resultado da Exportação Contínua|Contagem|Contagem|Indica se a exportação contínua foi bem-sucedida ou falhou|ContinuousExportName, resultado, banco de dados|
|CPU|Sim|CPU|Porcentagem|Média|Nível de utilização da CPU|Sem dimensões|
|DiscoveryLatency|Yes|Latência de descoberta|Segundos|Média|Relatado por conexões de dados (se existir). Tempo em segundos desde quando uma mensagem é enfileirada ou o evento é criado até ser descoberto pela conexão de dados. Esse tempo não está incluído na duração total da ingestão do Data Explorer do Azure.|ComponentType, ComponentName|
|EventsDropped|Yes|Eventos removidos|Contagem|Total|Número de eventos removidos permanentemente pela conexão de dados. Uma métrica de resultado de ingestão com um motivo de falha será enviada.|ComponentType, ComponentName|
|EventsProcessed|Yes|Eventos processados|Contagem|Total|Número de eventos processados pelo cluster|ComponentType, ComponentName|
|EventsProcessedForEventHubs|Yes|Eventos processados (para hubs de evento/IoT)|Contagem|Total|Número de eventos processados pelo cluster ao ingerir de evento/Hub IoT|EventStatus|
|EventsReceived|Yes|Eventos recebidos|Contagem|Total|Número de eventos recebidos pela conexão de dados.|ComponentType, ComponentName|
|ExportUtilization|Yes|Utilização da Exportação|Porcentagem|Máximo|Utilização da exportação|Sem dimensões|
|IngestionLatencyInSeconds|Yes|Latência de ingestão|Segundos|Média|Latência de dados ingeridos, desde o momento em que os dados foram recebidos no cluster até que eles estejam prontos para consulta. O período de latência de ingestão depende do cenário de ingestão.|Sem dimensões|
|IngestionResult|Yes|Resultados da ingestão|Contagem|Total|Número de operações de ingestão|IngestionResultDetails|
|IngestionUtilization|Yes|Utilização da ingestão|Porcentagem|Média|Taxa de slots de ingestão usados no cluster|Sem dimensões|
|IngestionVolumeInMB|Yes|Volume de ingestão|Bytes|Total|Volume geral de dados ingeridos para o cluster|Banco de dados|
|InstanceCount|Yes|Contagem de Instâncias|Contagem|Média|Contagem total de instâncias|Sem dimensões|
|KeepAlive|Yes|Keep alive|Contagem|Média|A verificação de integridade indica que o cluster responde às consultas|Sem dimensões|
|MaterializedViewAgeMinutes|Yes|Idade de exibição materializada|Contagem|Média|A idade da exibição materializada em minutos|Banco de dados, MaterializedViewName|
|MaterializedViewDataLoss|Yes|Perda de dados de exibição materializada|Contagem|Máximo|Indica potencial perda de dados na exibição materializada|Banco de dados, MaterializedViewName, tipo|
|MaterializedViewExtentsRebuild|Yes|Recompilação de extensões de exibição materializada|Contagem|Média|Número de extensões Rebuild|Banco de dados, MaterializedViewName|
|MaterializedViewHealth|Yes|Integridade da exibição materializada|Contagem|Média|A integridade da exibição materializada (1 para íntegro, 0 para não íntegro)|Banco de dados, MaterializedViewName|
|MaterializedViewRecordsInDelta|Yes|Registros de exibição materializada no Delta|Contagem|Média|O número de registros na parte não materializada da exibição|Banco de dados, MaterializedViewName|
|MaterializedViewResult|Yes|Resultado da exibição materializada|Contagem|Média|O resultado do processo de materialização|Banco de dados, MaterializedViewName, resultado|
|QueryDuration|Yes|Duração da consulta|Milissegundos|Média|Duração das consultas em segundos|QueryStatus|
|QueryResult|No|Resultado da consulta|Contagem|Contagem|Número total de consultas.|QueryStatus|
|QueueLength|Yes|Comprimento da fila|Contagem|Média|Número de mensagens pendentes na fila de um componente.|ComponentType|
|QueueOldestMessage|Yes|Mensagem de fila mais antiga|Contagem|Média|Tempo em segundos desde quando a mensagem mais antiga na fila foi inserida.|ComponentType|
|ReceivedDataSizeBytes|Yes|Bytes de tamanho de dados recebidos|Bytes|Média|Tamanho dos dados recebidos pela conexão de dados. Esse é o tamanho do fluxo de dados ou do tamanho de dados brutos, se fornecido.|ComponentType, ComponentName|
|StageLatency|Yes|Latência de preparo|Segundos|Média|Tempo cumulativo de quando uma mensagem é descoberta até ser recebida pelo componente de relatório para processamento (o tempo de descoberta é definido quando a mensagem é enfileirada para a fila de ingestão ou quando descoberta pela conexão de dados).|Banco de dados, ComponentType|
|SteamingIngestRequestRate|Yes|Taxa de Solicitação de Ingestão de Streaming|Contagem|RateRequestsPerSecond|Taxa de solicitação de ingestão de streaming (solicitações por segundo)|Sem dimensões|
|StreamingIngestDataRate|Yes|Taxa de Dados de Ingestão de Streaming|Contagem|Média|Taxa de dados de ingestão de streaming (MB por segundo)|Sem dimensões|
|StreamingIngestDuration|Yes|Duração da Ingestão de Streaming|Milissegundos|Média|Duração da ingestão de streaming em milissegundos|Sem dimensões|
|StreamingIngestResults|Yes|Resultado da Ingestão de Streaming|Contagem|Média|Resultado de ingestão de streaming|Result|
|TotalNumberOfConcurrentQueries|Yes|Número total de consultas simultâneas|Contagem|Máximo|Número total de consultas simultâneas|Sem dimensões|
|TotalNumberOfExtents|Yes|Número total de extensões|Contagem|Total|Número total de extensões de dados|Sem dimensões|
|TotalNumberOfThrottledCommands|Yes|Número total de comandos regulados|Contagem|Total|Número total de comandos regulados|CommandType|
|TotalNumberOfThrottledQueries|Yes|Número total de consultas limitadas|Contagem|Máximo|Número total de consultas limitadas|Sem dimensões|


## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ActionLatency|Yes|Latência da ação |Segundos|Média|Latência das ações de fluxo de trabalho concluídas.|Sem dimensões|
|ActionsCompleted|Yes|Ações concluídas |Contagem|Total|Número de ações de fluxo de trabalho concluídas.|Sem dimensões|
|ActionsFailed|Yes|Ações com falha |Contagem|Total|Número de ações de fluxo de trabalho com falha.|Sem dimensões|
|ActionsSkipped|Yes|Ações ignoradas |Contagem|Total|Número de ações de fluxo de trabalho ignoradas.|Sem dimensões|
|ActionsStarted|Yes|Ações iniciadas |Contagem|Total|Número de ações de fluxo de trabalho iniciadas.|Sem dimensões|
|ActionsSucceeded|Yes|Ações bem sucedidas |Contagem|Total|Número de ações de fluxo de trabalho bem sucedidas.|Sem dimensões|
|ActionSuccessLatency|Yes|Latência das ações bem sucedidas |Segundos|Média|Latência das ações de fluxo de trabalho bem sucedidas.|Sem dimensões|
|ActionThrottledEvents|Yes|Eventos de restrição de ações|Contagem|Total|Número de eventos de restrição de ações de fluxo de trabalho.|Sem dimensões|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Yes|Uso de Memória do Conector para o Ambiente de Serviço de Integração|Porcentagem|Média|Uso de memória do conector para o ambiente do serviço de integração.|Sem dimensões|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Yes|Uso do Processador de Conector para o Ambiente de Serviço de Integração|Porcentagem|Média|Uso do processador do conector para o ambiente do serviço de integração.|Sem dimensões|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Yes|Uso de Memória do Fluxo de Trabalho para o Ambiente de Serviço de Integração|Porcentagem|Média|Uso de memória do fluxo de trabalho para o ambiente do serviço de integração.|Sem dimensões|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Yes|Uso do Processador de Fluxo de Trabalho para o Ambiente de Serviço de Integração|Porcentagem|Média|Uso do processador do fluxo de trabalho para o ambiente do serviço de integração.|Sem dimensões|
|RunFailurePercentage|Yes|Porcentagem de falha de execução|Porcentagem|Total|Porcentagem de execuções do fluxo de trabalho com falha.|Sem dimensões|
|RunLatency|Yes|Latência da execução|Segundos|Média|Latência das execuções de fluxo de trabalho concluídas.|Sem dimensões|
|RunsCancelled|Yes|Execuções canceladas|Contagem|Total|Número de execuções de fluxo de trabalho canceladas.|Sem dimensões|
|RunsCompleted|Yes|Execuções concluídas|Contagem|Total|Número de execuções de fluxo de trabalho concluídas.|Sem dimensões|
|RunsFailed|Yes|Execuções com falha|Contagem|Total|Número de execuções de fluxo de trabalho com falha.|Sem dimensões|
|RunsStarted|Yes|Execuções iniciadas|Contagem|Total|Número de execuções fluxo de trabalho iniciadas.|Sem dimensões|
|RunsSucceeded|Yes|Execuções bem sucedidas|Contagem|Total|Número de execuções de fluxo de trabalho bem sucedidas.|Sem dimensões|
|RunStartThrottledEvents|Yes|Executar Eventos Limitados por Início|Contagem|Total|Número de eventos restritos de início de fluxo de trabalho.|Sem dimensões|
|RunSuccessLatency|Yes|Latência de execução bem sucedida|Segundos|Média|Latência de execuções de fluxo de trabalho bem sucedidas.|Sem dimensões|
|RunThrottledEvents|Yes|Eventos de restrição de execução|Contagem|Total|Número de eventos de restrição de ações ou gatilhos de fluxo de trabalho.|Sem dimensões|
|TriggerFireLatency|Yes|Latência do gatilho acionado |Segundos|Média|Latência dos gatilhos de fluxo de trabalho acionados.|Sem dimensões|
|TriggerLatency|Yes|Latência do gatilho |Segundos|Média|Latência dos gatilhos de fluxo de trabalho concluídos.|Sem dimensões|
|TriggersCompleted|Yes|Gatilhos concluídos |Contagem|Total|Número de gatilhos de fluxo de trabalho concluídos.|Sem dimensões|
|TriggersFailed|Yes|Gatilhos com falha |Contagem|Total|Número de gatilhos de fluxo de trabalho com falha.|Sem dimensões|
|TriggersFired|Yes|Gatilhos acionados |Contagem|Total|Número de gatilhos de fluxo de trabalho acionados.|Sem dimensões|
|TriggersSkipped|Yes|Gatilhos ignorados|Contagem|Total|Número de gatilhos de fluxo de trabalho ignorados.|Sem dimensões|
|TriggersStarted|Yes|Gatilhos iniciados |Contagem|Total|Número de gatilhos de fluxo de trabalho iniciadas.|Sem dimensões|
|TriggersSucceeded|Yes|Gatilhos bem sucedidos |Contagem|Total|Número de gatilhos de fluxo de trabalho bem sucedidos.|Sem dimensões|
|TriggerSuccessLatency|Yes|Latência do gatilho bem sucedido |Segundos|Média|Latência dos gatilhos de fluxo de trabalho bem sucedidos.|Sem dimensões|
|TriggerThrottledEvents|Yes|Eventos de restrição do gatilho|Contagem|Total|Número de eventos de restrição do gatilho de fluxo de trabalho.|Sem dimensões|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ActionLatency|Yes|Latência da ação |Segundos|Média|Latência das ações de fluxo de trabalho concluídas.|Sem dimensões|
|ActionsCompleted|Yes|Ações concluídas |Contagem|Total|Número de ações de fluxo de trabalho concluídas.|Sem dimensões|
|ActionsFailed|Yes|Ações com falha |Contagem|Total|Número de ações de fluxo de trabalho com falha.|Sem dimensões|
|ActionsSkipped|Yes|Ações ignoradas |Contagem|Total|Número de ações de fluxo de trabalho ignoradas.|Sem dimensões|
|ActionsStarted|Yes|Ações iniciadas |Contagem|Total|Número de ações de fluxo de trabalho iniciadas.|Sem dimensões|
|ActionsSucceeded|Yes|Ações bem sucedidas |Contagem|Total|Número de ações de fluxo de trabalho bem sucedidas.|Sem dimensões|
|ActionSuccessLatency|Yes|Latência das ações bem sucedidas |Segundos|Média|Latência das ações de fluxo de trabalho bem sucedidas.|Sem dimensões|
|ActionThrottledEvents|Yes|Eventos de restrição de ações|Contagem|Total|Número de eventos de restrição de ações de fluxo de trabalho.|Sem dimensões|
|BillableActionExecutions|Yes|Execuções de ação faturáveis|Contagem|Total|Número de execuções de ação de fluxo de trabalho sendo faturadas.|Sem dimensões|
|BillableTriggerExecutions|Yes|Execuções de gatilho faturáveis|Contagem|Total|Número de execuções de gatilho do fluxo de trabalho sendo cobradas.|Sem dimensões|
|BillingUsageNativeOperation|Yes|Uso de Cobrança para Execuções de Operação Nativa|Contagem|Total|Número de execuções de operação nativa sendo cobradas.|Sem dimensões|
|BillingUsageStandardConnector|Yes|Uso de Cobrança para Execuções de Conector Padrão|Contagem|Total|Número de execuções do conector padrão sendo cobradas.|Sem dimensões|
|BillingUsageStorageConsumption|Yes|Uso de Cobrança para Execuções de Consumo de Armazenamento|Contagem|Total|Número de execuções de consumo de armazenamento sendo cobradas.|Sem dimensões|
|RunFailurePercentage|Yes|Porcentagem de falha de execução|Porcentagem|Total|Porcentagem de execuções do fluxo de trabalho com falha.|Sem dimensões|
|RunLatency|Yes|Latência da execução|Segundos|Média|Latência das execuções de fluxo de trabalho concluídas.|Sem dimensões|
|RunsCancelled|Yes|Execuções canceladas|Contagem|Total|Número de execuções de fluxo de trabalho canceladas.|Sem dimensões|
|RunsCompleted|Yes|Execuções concluídas|Contagem|Total|Número de execuções de fluxo de trabalho concluídas.|Sem dimensões|
|RunsFailed|Yes|Execuções com falha|Contagem|Total|Número de execuções de fluxo de trabalho com falha.|Sem dimensões|
|RunsStarted|Yes|Execuções iniciadas|Contagem|Total|Número de execuções fluxo de trabalho iniciadas.|Sem dimensões|
|RunsSucceeded|Yes|Execuções bem sucedidas|Contagem|Total|Número de execuções de fluxo de trabalho bem sucedidas.|Sem dimensões|
|RunStartThrottledEvents|Yes|Executar Eventos Limitados por Início|Contagem|Total|Número de eventos restritos de início de fluxo de trabalho.|Sem dimensões|
|RunSuccessLatency|Yes|Latência de execução bem sucedida|Segundos|Média|Latência de execuções de fluxo de trabalho bem sucedidas.|Sem dimensões|
|RunThrottledEvents|Yes|Eventos de restrição de execução|Contagem|Total|Número de eventos de restrição de ações ou gatilhos de fluxo de trabalho.|Sem dimensões|
|TotalBillableExecutions|Yes|Total de execuções faturáveis|Contagem|Total|Número de execuções de fluxo de trabalho sendo faturadas.|Sem dimensões|
|TriggerFireLatency|Yes|Latência do gatilho acionado |Segundos|Média|Latência dos gatilhos de fluxo de trabalho acionados.|Sem dimensões|
|TriggerLatency|Yes|Latência do gatilho |Segundos|Média|Latência dos gatilhos de fluxo de trabalho concluídos.|Sem dimensões|
|TriggersCompleted|Yes|Gatilhos concluídos |Contagem|Total|Número de gatilhos de fluxo de trabalho concluídos.|Sem dimensões|
|TriggersFailed|Yes|Gatilhos com falha |Contagem|Total|Número de gatilhos de fluxo de trabalho com falha.|Sem dimensões|
|TriggersFired|Yes|Gatilhos acionados |Contagem|Total|Número de gatilhos de fluxo de trabalho acionados.|Sem dimensões|
|TriggersSkipped|Yes|Gatilhos ignorados|Contagem|Total|Número de gatilhos de fluxo de trabalho ignorados.|Sem dimensões|
|TriggersStarted|Yes|Gatilhos iniciados |Contagem|Total|Número de gatilhos de fluxo de trabalho iniciadas.|Sem dimensões|
|TriggersSucceeded|Yes|Gatilhos bem sucedidos |Contagem|Total|Número de gatilhos de fluxo de trabalho bem sucedidos.|Sem dimensões|
|TriggerSuccessLatency|Yes|Latência do gatilho bem sucedido |Segundos|Média|Latência dos gatilhos de fluxo de trabalho bem sucedidos.|Sem dimensões|
|TriggerThrottledEvents|Yes|Eventos de restrição do gatilho|Contagem|Total|Número de eventos de restrição do gatilho de fluxo de trabalho.|Sem dimensões|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Núcleos Ativos|Yes|Núcleos Ativos|Contagem|Média|Número de núcleos ativos|Cenário, ClusterName|
|Nós Ativos|Yes|Nós Ativos|Contagem|Média|Número de nós active. Esses são os nós que estão executando ativamente um trabalho.|Cenário, ClusterName|
|Cancelar execuções solicitadas|Yes|Cancelar execuções solicitadas|Contagem|Total|Número de execuções em que o cancelamento foi solicitado para este espaço de trabalho. A contagem é atualizada quando a solicitação de cancelamento é recebida para uma execução.|Cenário, RunType, PublishedPipelineId, Computetype, PipelineStepType, Experimentoname|
|Execuções canceladas|Yes|Execuções canceladas|Contagem|Total|Número de execuções canceladas para este espaço de trabalho. A contagem é atualizada quando uma execução é cancelada com êxito.|Cenário, RunType, PublishedPipelineId, Computetype, PipelineStepType, Experimentoname|
|Execuções Concluídas|Yes|Execuções Concluídas|Contagem|Total|Número de execuções concluídas com êxito para este espaço de trabalho. A contagem é atualizada quando uma execução é concluída e a saída é coletada.|Cenário, RunType, PublishedPipelineId, Computetype, PipelineStepType, Experimentoname|
|CpuUtilization|Yes|CpuUtilization|Contagem|Média|Percentual de utilização em um nó de CPU. A utilização é relatada em intervalos de um minuto.|Cenário, runId, NodeId, ClusterName|
|Errors|Yes|Errors|Contagem|Total|Número de erros de execução neste espaço de trabalho. A contagem é atualizada sempre que a execução encontra um erro.|Cenário|
|Execuções com falha|Yes|Execuções com falha|Contagem|Total|Número de execuções com falha para este espaço de trabalho. A contagem é atualizada quando uma execução falha.|Cenário, RunType, PublishedPipelineId, Computetype, PipelineStepType, Experimentoname|
|Finalizando execuções|Yes|Finalizando execuções|Contagem|Total|Número de execuções inseridas no estado final para este espaço de trabalho. A contagem é atualizada quando uma execução é concluída, mas a coleta de saída ainda está em andamento.|Cenário, RunType, PublishedPipelineId, Computetype, PipelineStepType, Experimentoname|
|GpuMemoryUtilization|Yes|GpuMemoryUtilization|Contagem|Média|Percentual de utilização de memória em um nó de GPU. A utilização é relatada em intervalos de um minuto.|Cenário, runId, NodeId, DeviceID, ClusterName|
|GpuUtilization|Yes|GpuUtilization|Contagem|Média|Percentual de utilização em um nó de GPU. A utilização é relatada em intervalos de um minuto.|Cenário, runId, NodeId, DeviceID, ClusterName|
|Núcleos Ociosos|Yes|Núcleos Ociosos|Contagem|Média|Número de núcleos ociosos|Cenário, ClusterName|
|Nós Ociosos|Yes|Nós Ociosos|Contagem|Média|Número de nós ociosos. Os nós ociosos são os nós que não estão executando nenhum trabalho, mas podem aceitar novos trabalhos, se disponíveis.|Cenário, ClusterName|
|Núcleos em Saída|Yes|Núcleos em Saída|Contagem|Média|Número de núcleos de saída|Cenário, ClusterName|
|Nós em Saída|Yes|Nós em Saída|Contagem|Média|Número de nós de saída. A saída de nós são os nós que acabaram de processar um trabalho e vão para o estado ocioso.|Cenário, ClusterName|
|Implantação de Modelo com Falha|Yes|Implantação de Modelo com Falha|Contagem|Total|Número de implantações de modelo que falharam neste espaço de trabalho|Cenário, StatusCode|
|Implantação de Modelo Iniciada|Yes|Implantação de Modelo Iniciada|Contagem|Total|Número de implantações de modelo iniciadas neste espaço de trabalho|Cenário|
|Implantação de Modelo Bem-sucedida|Yes|Implantação de Modelo Bem-sucedida|Contagem|Total|Número de implantações de modelo que foram bem-sucedidas neste espaço de trabalho|Cenário|
|Registro de Modelo com Falha|Yes|Registro de Modelo com Falha|Contagem|Total|Número de registros de modelo que falharam neste espaço de trabalho|Cenário, StatusCode|
|Registro de Modelo Bem-sucedido|Yes|Registro de Modelo Bem-sucedido|Contagem|Total|Número de registros de modelo que foram bem-sucedidos neste espaço de trabalho|Cenário|
|Não respondendo execuções|Yes|Não respondendo execuções|Contagem|Total|Número de execuções não respondendo a este espaço de trabalho. A contagem é atualizada quando uma execução entra em estado de não respondendo.|Cenário, RunType, PublishedPipelineId, Computetype, PipelineStepType, Experimentoname|
|Execuções não iniciadas|Yes|Execuções não iniciadas|Contagem|Total|Número de execuções no estado não iniciado para este espaço de trabalho. A contagem é atualizada quando uma solicitação é recebida para criar uma execução, mas as informações de execução ainda não foram preenchidas. |Cenário, RunType, PublishedPipelineId, Computetype, PipelineStepType, Experimentoname|
|Núcleos com Preempção|Yes|Núcleos com Preempção|Contagem|Média|Número de núcleos admitidos|Cenário, ClusterName|
|Nós com Preempção|Yes|Nós com Preempção|Contagem|Média|Número de nós admitidos. Esses nós são os nós de baixa prioridade que são desfeitos do pool de nós disponível.|Cenário, ClusterName|
|Preparando execuções|Yes|Preparando execuções|Contagem|Total|Número de execuções que estão se preparando para este espaço de trabalho. A contagem é atualizada quando uma execução entra em estado de preparação enquanto o ambiente de execução está sendo preparado.|Cenário, RunType, PublishedPipelineId, Computetype, PipelineStepType, Experimentoname|
|Execuções de provisionamento|Yes|Execuções de provisionamento|Contagem|Total|Número de execuções que estão sendo provisionadas para este espaço de trabalho. A contagem é atualizada quando uma execução está aguardando a criação ou o provisionamento de destino de computação.|Cenário, RunType, PublishedPipelineId, Computetype, PipelineStepType, Experimentoname|
|Execuções em fila|Yes|Execuções em fila|Contagem|Total|Número de execuções enfileiradas para este espaço de trabalho. A contagem é atualizada quando uma execução é enfileirada no destino de computação. Pode ocorrer ao aguardar que os nós de computação necessários estejam prontos.|Cenário, RunType, PublishedPipelineId, Computetype, PipelineStepType, Experimentoname|
|Percentual de Utilização de Cota|Yes|Percentual de Utilização de Cota|Contagem|Média|Porcentagem de cota utilizada|Cenário, ClusterName, VmFamilyName, VmPriority|
|Execuções Iniciadas|Yes|Execuções Iniciadas|Contagem|Total|Número de execuções em execução para este espaço de trabalho. A contagem é atualizada quando a execução começa a ser executada nos recursos necessários.|Cenário, RunType, PublishedPipelineId, Computetype, PipelineStepType, Experimentoname|
|Iniciando execuções|Yes|Iniciando execuções|Contagem|Total|Número de execuções iniciadas para este espaço de trabalho. A contagem é atualizada após a solicitação para criar informações de execução e execução, como a ID de execução, foi populada|Cenário, RunType, PublishedPipelineId, Computetype, PipelineStepType, Experimentoname|
|Total de Núcleos|Yes|Total de Núcleos|Contagem|Média|Número de núcleos totais|Cenário, ClusterName|
|Total de Nós|Yes|Total de Nós|Contagem|Média|Número total de nós. Esse total inclui alguns nós ativos, nós ociosos, nós inutilizáveis, nós Premepted, deixando nós|Cenário, ClusterName|
|Núcleos Inutilizáveis|Yes|Núcleos Inutilizáveis|Contagem|Média|Número de núcleos inutilizáveis|Cenário, ClusterName|
|Nós Inutilizáveis|Yes|Nós Inutilizáveis|Contagem|Média|Número de nós inutilizáveis. Os nós inutilizáveis não são funcionais devido a algum problema não resolvido. O Azure reciclará esses nós.|Cenário, ClusterName|
|Warnings|Yes|Warnings|Contagem|Total|Número de avisos de execução neste espaço de trabalho. A contagem é atualizada sempre que uma execução encontra um aviso.|Cenário|


## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Yes|Disponibilidade|Porcentagem|Média|Disponibilidade das APIs|ApiCategory, ApiName|
|Uso|No|Uso|Contagem|Contagem|Contagem de chamadas à API|ApiCategory, ApiName, ResultType, ResponseCode|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|AssetCount|Yes|Contagem de ativos|Contagem|Média|Quantos ativos já foram criados na conta de serviço de mídia atual|Sem dimensões|
|AssetQuota|Yes|Cota de ativos|Contagem|Média|Quantos ativos são permitidos para a conta de serviço de mídia atual|Sem dimensões|
|AssetQuotaUsedPercentage|Yes|Percentual de cota de ativos usado|Porcentagem|Média|Porcentagem de ativos usados na conta de serviço de mídia atual|Sem dimensões|
|ChannelsAndLiveEventsCount|Yes|Contagem de eventos ao vivo|Contagem|Média|O número total de eventos ao vivo na conta de serviços de mídia atual|Sem dimensões|
|ContentKeyPolicyCount|Yes|Contagem de Política de Chave de Conteúdo|Contagem|Média|Quantas políticas de chave de conteúdo já foram criadas na conta de serviço de mídia atual|Sem dimensões|
|ContentKeyPolicyQuota|Yes|Cota de Política de Chave de Conteúdo|Contagem|Média|Quantas políticas de chave de conteúdo são permitidas para a conta de serviço de mídia atual|Sem dimensões|
|ContentKeyPolicyQuotaUsedPercentage|Yes|Percentual de Cota Usada da Política de Chave de Conteúdo|Porcentagem|Média|Porcentagem usada da política de chave de conteúdo na conta de serviço de mídia atual|Sem dimensões|
|RunningChannelsAndLiveEventsCount|Yes|Contagem de eventos ao vivo em execução|Contagem|Média|O número total de eventos em execução ao vivo na conta de serviços de mídia atual|Sem dimensões|
|StreamingPolicyCount|Yes|Contagem de Política de Streaming|Contagem|Média|Quantas políticas de streaming já foram criadas na conta de serviço de mídia atual|Sem dimensões|
|StreamingPolicyQuota|Yes|Cota de Política de Streaming|Contagem|Média|Quantas políticas de streaming são permitidas para a conta de serviço de mídia atual|Sem dimensões|
|StreamingPolicyQuotaUsedPercentage|Yes|Percentual de cota usada da Política de Streaming|Porcentagem|Média|Percentual usado pela política de streaming na conta de serviço de mídia atual|Sem dimensões|


## <a name="microsoftmediamediaservicesliveevents"></a>Microsoft. Media/mediaservices/liveEvents

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|IngestBitrate|Yes|Taxa de bits de ingestão de eventos ativos|BitsPerSecond|Média|A taxa de bits de entrada ingerida para um evento ao vivo, em bits por segundo.|TrackName|
|IngestDriftValue|Yes|Valor de descompasso de ingestão de evento ao vivo|Segundos|Máximo|Descompasso entre o carimbo de data/hora do conteúdo ingerido e o relógio do sistema, medido em segundos por minuto. Um valor diferente de zero indica que o conteúdo ingerido chega mais devagar do que o horário do relógio do sistema.|TrackName|
|IngestLastTimestamp|Yes|Último carimbo de data/hora do evento ao vivo|Milissegundos|Máximo|Último carimbo de data/hora ingerido para um evento ao vivo.|TrackName|
|LiveOutputLastTimestamp|Yes|Carimbo de data/hora da última saída|Milissegundos|Máximo|Carimbo de data/hora do último fragmento carregado no armazenamento para uma saída de evento ao vivo.|TrackName|


## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft.Media/mediaservices/streamingEndpoints

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|CPU|Yes|Uso da CPU|Porcentagem|Média|Uso da CPU para pontos de extremidade de streaming Premium. Esses dados não estão disponíveis para pontos de extremidade de streaming padrão.|VmId|
|Saída|Yes|Saída|Bytes|Total|A quantidade de dados de egresso, em bytes.|OutputFormat|
|EgressBandwidth|No|Largura de banda de saída|BitsPerSecond|Média|Largura de banda de saída em bits por segundo.|VmId|
|Requests|Yes|Requests|Contagem|Total|Solicitações para um ponto de extremidade de streaming.|OutputFormat, HttpStatusCode, ErrorCode|
|SuccessE2ELatency|Yes|Latência de ponta a ponta bem-sucedida|Milissegundos|Média|A latência média de solicitações bem-sucedidas em milissegundos.|OutputFormat|


## <a name="microsoftmixedrealityremoterenderingaccounts"></a>Microsoft. MixedReality/remoteRenderingAccounts

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ActiveRenderingSessions|Yes|Sessões de renderização ativas|Contagem|Média|Número total de sessões de renderização ativas|SessionType, SDKVersion|
|AssetsConverted|Yes|Ativos convertidos|Contagem|Total|Número total de ativos convertidos|SDKVersion|


## <a name="microsoftmixedrealityspatialanchorsaccounts"></a>Microsoft. MixedReality/spatialAnchorsAccounts

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|AnchorsCreated|Yes|Âncoras criadas|Contagem|Total|Número de âncoras criadas|DeviceFamily, SDKVersion|
|AnchorsDeleted|Yes|Âncoras excluídas|Contagem|Total|Número de âncoras excluídas|DeviceFamily, SDKVersion|
|AnchorsQueried|Yes|Âncoras consultadas|Contagem|Total|Número de âncoras espaciais consultadas|DeviceFamily, SDKVersion|
|AnchorsUpdated|Yes|Âncoras atualizadas|Contagem|Total|Número de âncoras atualizadas|DeviceFamily, SDKVersion|
|PosesFound|Yes|Poses encontradas|Contagem|Total|Número de poses retornadas|DeviceFamily, SDKVersion|
|TotalDailyAnchors|Yes|Total de âncoras diárias|Contagem|Média|Número total de âncoras – diariamente|DeviceFamily, SDKVersion|


## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Yes|Tamanho alocado do pool|Bytes|Média|Tamanho provisionado deste pool|Sem dimensões|
|VolumePoolAllocatedToVolumeThroughput|Yes|Taxa de transferência alocada do pool|BytesPerSecond|Média|Soma da taxa de transferência de todos os volumes pertencentes ao pool|Sem dimensões|
|VolumePoolAllocatedUsed|Yes|Pool alocado para o tamanho do volume|Bytes|Média|Tamanho usado alocado do pool|Sem dimensões|
|VolumePoolProvisionedThroughput|Yes|Taxa de transferência provisionada para o pool|BytesPerSecond|Média|Taxa de transferência provisionada deste pool|Sem dimensões|
|VolumePoolTotalLogicalSize|Yes|Tamanho consumido do pool|Bytes|Média|Soma do tamanho lógico de todos os volumes que pertencem ao pool|Sem dimensões|
|VolumePoolTotalSnapshotSize|Yes|Tamanho total do instantâneo do pool|Bytes|Média|Soma do tamanho do instantâneo de todos os volumes neste pool|Sem dimensões|


## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/volumes

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|AverageReadLatency|Yes|Latência média de leitura|MilliSeconds|Média|Latência média de leitura em milissegundos por operação|Sem dimensões|
|AverageWriteLatency|Yes|Latência média de gravação|MilliSeconds|Média|Latência média de gravação em milissegundos por operação|Sem dimensões|
|CbsVolumeBackupActive|Yes|O backup do volume foi suspenso|Contagem|Média|A política de backup foi suspensa para o volume? 1 se Sim, 0 se não.|Sem dimensões|
|CbsVolumeLogicalBackupBytes|Yes|Bytes de backup de volume|Bytes|Média|Total de bytes de backup para este volume.|Sem dimensões|
|CbsVolumeOperationComplete|Yes|Operação de backup de volume concluída|Contagem|Média|A operação de backup ou restauração do último volume foi concluída com êxito? 1 se Sim, 0 se não.|Sem dimensões|
|CbsVolumeOperationTransferredBytes|Yes|Bytes de backup por volume transferidos pela última vez|Bytes|Média|Total de bytes transferidos para a última operação de backup ou restauração.|Sem dimensões|
|CbsVolumeProtected|Yes|O backup de volume está habilitado|Contagem|Média|O backup está habilitado para o volume? 1 se Sim, 0 se não.|Sem dimensões|
|OtherThroughput|Yes|Outras taxas de transferência|BytesPerSecond|Média|Outra taxa de transferência (que não é leitura ou gravação) em bytes por segundo|Sem dimensões|
|ReadIops|Yes|IOPS de leitura|CountPerSecond|Média|Operações de entrada/saída de leitura por segundo|Sem dimensões|
|ReadThroughput|Yes|Taxa de transferência de leitura|BytesPerSecond|Média|Taxa de transferência de leitura em bytes por segundo|Sem dimensões|
|TotalThroughput|Yes|Taxa de transferência total|BytesPerSecond|Média|Soma de todas as taxas de transferência em bytes por segundo|Sem dimensões|
|VolumeAllocatedSize|Yes|Tamanho alocado do volume|Bytes|Média|O tamanho provisionado de um volume|Sem dimensões|
|VolumeConsumedSizePercentage|Yes|Percentual de tamanho consumido por volume|Porcentagem|Média|O percentual do volume consumido, incluindo instantâneos.|Sem dimensões|
|VolumeLogicalSize|Yes|Tamanho consumido por volume|Bytes|Média|Tamanho lógico do volume (bytes usados)|Sem dimensões|
|VolumeSnapshotSize|Yes|Tamanho do instantâneo de volume|Bytes|Média|Tamanho de todos os instantâneos no volume|Sem dimensões|
|WriteIops|Yes|IOPS de gravação|CountPerSecond|Média|Operações de entrada/saída de gravação por segundo|Sem dimensões|
|WriteThroughput|Yes|Taxa de transferência de gravação|BytesPerSecond|Média|Taxa de transferência de gravação em bytes por segundo|Sem dimensões|
|XregionReplicationHealthy|Yes|O status de replicação do volume é íntegro|Contagem|Média|Condição da relação, 1 ou 0.|Sem dimensões|
|XregionReplicationLagTime|Yes|Tempo de retardo da replicação do volume|Segundos|Média|A quantidade de tempo em segundos pela qual os dados no espelho atrasam a origem.|Sem dimensões|
|XregionReplicationLastTransferDuration|Yes|Duração da última transferência da replicação de volume|Segundos|Média|A quantidade de tempo em segundos que levou para a última transferência ser concluída.|Sem dimensões|
|XregionReplicationLastTransferSize|Yes|Tamanho da última transferência da replicação de volume|Bytes|Média|O número total de bytes transferidos como parte da última transferência.|Sem dimensões|
|XregionReplicationRelationshipProgress|Yes|Progresso da replicação do volume|Bytes|Média|Quantidade total de dados transferidos para a operação de transferência atual.|Sem dimensões|
|XregionReplicationRelationshipTransferring|Yes|A replicação de volume está sendo transtransferência|Contagem|Média|Se o status da replicação do volume é ' Transferindo '.|Sem dimensões|
|XregionReplicationTotalTransferBytes|Yes|Transferência total de replicação de volume|Bytes|Média|Bytes cumulativos transferidos para a relação.|Sem dimensões|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ApplicationGatewayTotalTime|No|Tempo Total do Gateway de Aplicativo|MilliSeconds|Média|Tempo médio que leva para que uma solicitação seja processada e sua resposta seja enviada. Isso é calculado como a média do intervalo desde o momento em que o gateway de aplicativo recebe o primeiro byte de uma solicitação HTTP até o momento em que a operação de envio de resposta é concluída. É importante observar que isso geralmente inclui o tempo de processamento do gateway de aplicativo, o tempo que os pacotes de solicitação e resposta estão viajando pela rede e o tempo que o servidor back-end levou para responder.|Ouvinte|
|AvgRequestCountPerHealthyHost|No|Solicitações por minuto por Host Íntegro|Contagem|Média|Contagem média de solicitações por minuto por host de back-end íntegro em um pool|BackendSettingsPool|
|BackendConnectTime|No|Tempo de Conexão de Back-end|MilliSeconds|Média|Tempo gasto estabelecendo uma conexão com um servidor de back-end|Listener, BackendServer, Httpsettings, BackendHttpSetting|
|BackendFirstByteResponseTime|No|Tempo de Resposta do Primeiro Byte de Back-end|MilliSeconds|Média|Intervalo de tempo entre o início do estabelecimento de uma conexão com o servidor back-end e o recebimento do primeiro byte do cabeçalho de resposta, aproximar tempo de processamento do servidor back-end|Listener, BackendServer, Httpsettings, BackendHttpSetting|
|BackendLastByteResponseTime|No|Tempo de Resposta do Último Byte de Back-end|MilliSeconds|Média|Intervalo de tempo entre o início do estabelecimento de uma conexão com o servidor de back-end e o recebimento do último byte do corpo da resposta|Listener, BackendServer, Httpsettings, BackendHttpSetting|
|BackendResponseStatus|Yes|Status da Resposta de Back-end|Contagem|Total|O número de códigos de resposta HTTP gerados pelos membros de back-end. Isso não inclui nenhum código de resposta gerado pelo gateway de aplicativo.|BackendServer, Httpsettings, BackendHttpSetting, HttpStatusGroup|
|BlockedCount|Yes|Distribuição de Regra de Solicitações Bloqueadas do Firewall do Aplicativo Web|Contagem|Total|Distribuição de regra de solicitações bloqueadas do firewall do aplicativo Web|Ruler, RuleId|
|BlockedReqCount|Yes|Contagem de Solicitações Bloqueadas do Firewall do Aplicativo Web|Contagem|Total|Contagem de solicitações bloqueadas do firewall do aplicativo Web|Sem dimensões|
|BytesReceived|Yes|Bytes Recebidos|Bytes|Total|O número total de bytes recebidos pelo gateway de aplicativo dos clientes|Ouvinte|
|BytesSent|Yes|Bytes Enviados|Bytes|Total|O número total de bytes enviados pelo gateway de aplicativo para os clientes|Ouvinte|
|CapacityUnits|No|Unidades de Capacidade Atuais|Contagem|Média|Unidades de capacidade consumidas|Sem dimensões|
|ClientRtt|No|RTT do Cliente|MilliSeconds|Média|Tempo médio de ida e volta entre clientes e o gateway de aplicativo. Essa métrica indica quanto tempo leva para estabelecer conexões e retornar confirmações|Ouvinte|
|ComputeUnits|No|Unidades de Computação Atuais|Contagem|Média|Unidades de computação consumidas|Sem dimensões|
|CpuUtilization|No|Utilização da CPU|Porcentagem|Média|Utilização atual da CPU do gateway de aplicativo|Sem dimensões|
|CurrentConnections|Yes|Conexões atuais|Contagem|Total|Contagem de conexões atuais estabelecidas com o Gateway de Aplicativo|Sem dimensões|
|EstimatedBilledCapacityUnits|No|Unidades de capacidade cobradas estimadas|Contagem|Média|Unidades de capacidade estimadas que serão cobradas|Sem dimensões|
|FailedRequests|Yes|Solicitações com falha|Contagem|Total|Contagem de solicitações com falha atendidas pelo Gateway de Aplicativo|BackendSettingsPool|
|FixedBillableCapacityUnits|No|Unidades de capacidade cobráveis fixas|Contagem|Média|Unidades de capacidade mínimas que serão cobradas|Sem dimensões|
|HealthyHostCount|Yes|Contagem de hosts íntegros|Contagem|Média|Número de hosts de back-end íntegros|BackendSettingsPool|
|MatchedCount|Yes|Distribuição de Regra Total do Firewall do Aplicativo Web|Contagem|Total|Distribuição da regra total do firewall do aplicativo Web para o tráfego de entrada|Ruler, RuleId|
|NewConnectionsPerSecond|No|Novas conexões por segundo|CountPerSecond|Média|Novas conexões por segundo estabelecidas com o gateway de aplicativo|Sem dimensões|
|ResponseStatus|Yes|Status da Resposta|Contagem|Total|Status de resposta HTTP retornado pelo Gateway de Aplicativo|HttpStatusGroup|
|Produtividade|No|Produtividade|BytesPerSecond|Média|Número de bytes por segundo atendidos pelo Gateway de Aplicativo|Sem dimensões|
|TlsProtocol|Yes|Protocolo TLS do Cliente|Contagem|Total|O número de solicitações TLS e não TLS iniciadas pelo cliente que estabeleceram a conexão com o gateway de aplicativo. Para exibir a distribuição de protocolo TLS, filtre pelo protocolo TLS de dimensão.|Ouvinte, TlsProtocol|
|TotalRequests|Yes|Total de Solicitações|Contagem|Total|Contagem de solicitações bem sucedidas atendidas pelo Gateway de Aplicativo|BackendSettingsPool|
|UnhealthyHostCount|Yes|Contagem de hosts não íntegros|Contagem|Média|Número de hosts de back-end não íntegros|BackendSettingsPool|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ApplicationRuleHit|Yes|Contagem de ocorrências de regras de aplicativo|Contagem|Total|Número de vezes que as regras de aplicativo foram atingidas|Status, motivo, protocolo|
|DataProcessed|Yes|Dados processados|Bytes|Total|Quantidade total de dados processados por esse firewall|Sem dimensões|
|FirewallHealth|Yes|Estado de integridade do firewall|Porcentagem|Média|Indica a integridade geral deste firewall|Status, motivo|
|NetworkRuleHit|Yes|Contagem de ocorrências das regras de rede|Contagem|Total|Número de vezes que as regras de rede foram atingidas|Status, motivo, protocolo|
|SNATPortUtilization|Yes|Utilização da porta SNAT|Porcentagem|Média|Porcentagem de portas SNAT de saída atualmente em uso|Protocolo|
|Produtividade|No|Produtividade|BitsPerSecond|Média|Taxa de transferência processada por este firewall|Sem dimensões|


## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|BitsInPerSecond|Yes|BitsInPerSecond|BitsPerSecond|Média|Bits de entrada no Azure por segundo|Sem dimensões|
|BitsOutPerSecond|Yes|BitsOutPerSecond|BitsPerSecond|Média|Bits de saída no Azure por segundo|Sem dimensões|


## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|QueryVolume|Yes|Volume de consulta|Contagem|Total|Número de consultas atendidas para uma zona DNS|Sem dimensões|
|RecordSetCapacityUtilization|No|Utilização de capacidade de Conjuntos de Registros|Porcentagem|Máximo|Porcentagem de capacidade de Conjuntos de Registros utilizada por uma zona DNS|Sem dimensões|
|RecordSetCount|Yes|Contagem de Conjuntos de Registros|Contagem|Máximo|Número de Conjuntos de Registros em uma zona DNS|Sem dimensões|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ArpAvailability|Yes|Disponibilidade de ARP|Porcentagem|Média|Disponibilidade ARP de MSEE para todos os pares.|PeeringType, par|
|BgpAvailability|Yes|Disponibilidade de BGP|Porcentagem|Média|Disponibilidade de BGP de MSEE para todos os pares.|PeeringType, par|
|BitsInPerSecond|No|BitsInPerSecond|BitsPerSecond|Média|Bits de entrada no Azure por segundo|PeeringType, DeviceRole|
|BitsOutPerSecond|No|BitsOutPerSecond|BitsPerSecond|Média|Bits de saída no Azure por segundo|PeeringType, DeviceRole|
|GlobalReachBitsInPerSecond|No|GlobalReachBitsInPerSecond|BitsPerSecond|Média|Bits de entrada no Azure por segundo|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|No|GlobalReachBitsOutPerSecond|BitsPerSecond|Média|Bits de saída no Azure por segundo|PeeredCircuitSKey|
|QosDropBitsInPerSecond|Yes|DroppedInBitsPerSecond|BitsPerSecond|Média|Bits de entrada de dados eliminados por segundo|Sem dimensões|
|QosDropBitsOutPerSecond|Yes|DroppedOutBitsPerSecond|BitsPerSecond|Média|Bits de saída de dados eliminados por segundo|Sem dimensões|


## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|BitsInPerSecond|Yes|BitsInPerSecond|BitsPerSecond|Média|Bits de entrada no Azure por segundo|Sem dimensões|
|BitsOutPerSecond|Yes|BitsOutPerSecond|BitsPerSecond|Média|Bits de saída no Azure por segundo|Sem dimensões|


## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft.Network/expressRouteGateways

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|No|BitsInPerSecond|BitsPerSecond|Média|Bits de entrada no Azure por segundo|ConnectionName|
|ErGatewayConnectionBitsOutPerSecond|No|BitsOutPerSecond|BitsPerSecond|Média|Bits de saída no Azure por segundo|ConnectionName|
|ExpressRouteGatewayCountOfRoutesAdvertisedToPeer|Yes|Contagem de rotas anunciadas para o par (visualização)|Contagem|Máximo|Contagem de rotas anunciadas para o par por ExpressRouteGateway|roleInstance|
|ExpressRouteGatewayCountOfRoutesLearnedFromPeer|Yes|Contagem de rotas aprendidas do par (versão prévia)|Contagem|Máximo|Contagem de rotas aprendidas do ponto por ExpressRouteGateway|roleInstance|
|ExpressRouteGatewayCpuUtilization|Yes|Utilização da CPU (versão prévia)|Contagem|Média|Utilização da CPU do gateway de ExpressRoute|roleInstance|
|ExpressRouteGatewayFrequencyOfRoutesChanged|No|Frequência de alteração de rotas (versão prévia)|Contagem|Total|Frequência de alteração de rotas no gateway de ExpressRoute|roleInstance|
|ExpressRouteGatewayNumberOfVmInVnet|No|Número de VMs na rede virtual (versão prévia)|Contagem|Máximo|Número de VMs na rede virtual|Sem dimensões|
|ExpressRouteGatewayPacketsPerSecond|No|Pacotes por segundo (visualização)|CountPerSecond|Média|Contagem de pacotes do gateway de ExpressRoute|roleInstance|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft.Network/expressRoutePorts

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|AdminState|Yes|AdminState|Contagem|Média|Estado do administrador da porta|Link|
|LineProtocol|Yes|LineProtocol|Contagem|Média|Status do protocolo de linha da porta|Link|
|PortBitsInPerSecond|Yes|BitsInPerSecond|BitsPerSecond|Média|Bits de entrada no Azure por segundo|Link|
|PortBitsOutPerSecond|Yes|BitsOutPerSecond|BitsPerSecond|Média|Bits de saída no Azure por segundo|Link|
|RxLightLevel|Yes|RxLightLevel|Contagem|Média|Nível de luz Rx em dBm|Link, Lane|
|TxLightLevel|Yes|TxLightLevel|Contagem|Média|Nível de luz TX em dBm|Link, Lane|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|BackendHealthPercentage|Yes|Percentual de integridade do back-end|Porcentagem|Média|O percentual de investigações de integridade bem-sucedidas do proxy HTTP/S aos back-ends|Backend, BackendPool|
|BackendRequestCount|Yes|Contagem de solicitações de back-end|Contagem|Total|O número de solicitações enviadas do proxy HTTP/S aos back-ends|HttpStatus, HttpStatusGroup, Backend|
|BackendRequestLatency|Yes|Latência de solicitação de back-end|MilliSeconds|Média|O tempo calculado de quando a solicitação foi enviada pelo proxy HTTP/S ao back-end até o proxy HTTP/S receber o último byte de resposta do back-end|Back-end|
|BillableResponseSize|Yes|Tamanho de Resposta Faturável|Bytes|Total|O número de bytes faturáveis (mínimo de 2 KB por solicitação) enviados como respostas do proxy HTTP/S para os clientes.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestCount|Yes|Contagem de solicitações|Contagem|Total|O número de solicitações de cliente atendidas pelo proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Yes|Tamanho da solicitação|Bytes|Total|O número de bytes enviados como solicitações de clientes ao proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Yes|Tamanho da resposta|Bytes|Total|O número de bytes enviados como respostas do proxy HTTP/S para clientes|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|TotalLatency|Yes|Latência total|MilliSeconds|Média|O tempo calculado de quando a solicitação do cliente foi recebida pelo proxy HTTP/S até o cliente confirmar o último byte de resposta do proxy HTTP/S|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|WebApplicationFirewallRequestCount|Yes|Contagem de solicitações do Firewall de Aplicativo Web|Contagem|Total|O número de solicitações de cliente processadas pelo Firewall do Aplicativo Web|PolicyName, RuleName, Action|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|AllocatedSnatPorts|No|Portas SNAT alocadas|Contagem|Média|Número total de portas SNAT alocadas no período|FrontendIPAddress, BackendIPAddress, ProtocolType, |
|ByteCount|Yes|Contagem de Bytes|Bytes|Total|Número total de Bytes transmitidos no período|FrontendIPAddress, FrontendPort, Direction|
|DipAvailability|Yes|Status de investigação de integridade|Contagem|Média|Status médio de investigação de integridade do Balanceador de Carga por duração de tempo|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|PacketCount|Yes|Contagem de Pacotes|Contagem|Total|Número total de Pacotes transmitidos no período|FrontendIPAddress, FrontendPort, Direction|
|SnatConnectionCount|Yes|Contagem de Conexões SNAT|Contagem|Total|Número total de novas conexões SNAT criadas no período|FrontendIPAddress, BackendIPAddress, ConnectionState|
|SYNCount|Yes|Contagem de SYN|Contagem|Total|Número total de Pacotes de SYN transmitidos no período|FrontendIPAddress, FrontendPort, Direction|
|UsedSnatPorts|No|Portas SNAT usadas|Contagem|Média|Número total de portas SNAT usadas no período|FrontendIPAddress, BackendIPAddress, ProtocolType, |
|VipAvailability|Yes|Disponibilidade do Caminho de Dados|Contagem|Média|Disponibilidade média do caminho de dados do Balanceador de Carga por duração de tempo|FrontendIPAddress, FrontendPort|


## <a name="microsoftnetworknatgateways"></a>Microsoft. Network/natGateways

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ByteCount|Yes|Bytes|Bytes|Total|Número total de Bytes transmitidos no período|Protocolo, direção|
|DatapathAvailability|Yes|Disponibilidade do caminho de DataPath (visualização)|Contagem|Média|Disponibilidade do caminho de DataPath do gateway NAT|Sem dimensões|
|PacketCount|Yes|Pacotes|Contagem|Total|Número total de Pacotes transmitidos no período|Protocolo, direção|
|PacketDropCount|Yes|Pacotes descartados|Contagem|Total|Contagem de pacotes eliminados|Sem dimensões|
|SNATConnectionCount|Yes|Contagem de Conexões SNAT|Contagem|Total|Total de conexões ativas simultâneas|Protocolo, ConnectionState|
|TotalConnectionCount|Yes|Contagem total de conexões SNAT|Contagem|Total|Número total de conexões SNAT ativas|Protocolo|


## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|BytesReceivedRate|Yes|Bytes Recebidos|Bytes|Total|Número de bytes recebidos pela Interface de Rede|Sem dimensões|
|BytesSentRate|Yes|Bytes Enviados|Bytes|Total|Número de bytes enviados pela Interface de Rede|Sem dimensões|
|PacketsReceivedRate|Yes|Pacotes Recebidos|Contagem|Total|Número de pacotes recebidos pela Interface de Rede|Sem dimensões|
|PacketsSentRate|Yes|Pacotes Enviados|Contagem|Total|Número de bytes recebidos pela Interface de Rede|Sem dimensões|


## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|AverageRoundtripMs|Yes|Média de tempo de ida e volta (MS) (clássico)|MilliSeconds|Média|Tempo ida e volta de rede médio (ms) para investigações de monitoramento de conectividade enviadas entre a origem e o destino|Sem dimensões|
|ChecksFailedPercent|Yes|Verificação de porcentagem de falhas|Porcentagem|Média|% de verificações de monitoramento de conectividade com falha|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, Destinationname, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName, SourceIP, DestinationIP, SourceSubnet, DestinationSubnet|
|ProbesFailedPercent|Yes|% De investigações com falha (clássico)|Porcentagem|Média|Porcentagem de investigações de monitoramento de conectividade com falha|Sem dimensões|
|RoundTripTimeMs|Yes|Tempo de Round-Trip (MS)|MilliSeconds|Média|Tempo de ida e volta em milissegundos para as verificações de monitoramento de conectividade|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, Destinationname, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName, SourceIP, DestinationIP, SourceSubnet, DestinationSubnet|
|Result|Yes|Resultado do teste|Contagem|Média|Resultado do teste do monitor de conexão|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, Destinationname, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName, TestResultCriterion, SourceIP, DestinationIP, SourceSubnet, DestinationSubnet|


## <a name="microsoftnetworkp2svpngateways"></a>Microsoft. Network/p2sVpnGateways

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|P2SBandwidth|Yes|Largura de banda de P2S do gateway|BytesPerSecond|Média|Largura de banda média ponto a site de um gateway em bytes por segundo|Sem dimensões|
|P2SConnectionCount|Yes|Contagem de conexões P2S|Contagem|Total|Contagem de conexões ponto a site de um gateway|Protocolo|


## <a name="microsoftnetworkprivatednszones"></a>Microsoft. Network/privateDnsZones

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|QueryVolume|Yes|Volume de consulta|Contagem|Total|Número de consultas servidas para uma zona de DNS privado|Sem dimensões|
|RecordSetCapacityUtilization|No|Utilização de capacidade de Conjuntos de Registros|Porcentagem|Máximo|Porcentagem da capacidade do conjunto de registros utilizada por uma zona de DNS privado|Sem dimensões|
|RecordSetCount|Yes|Contagem de Conjuntos de Registros|Contagem|Máximo|Número de conjuntos de registros em uma zona de DNS privado|Sem dimensões|
|VirtualNetworkLinkCapacityUtilization|No|Utilização da capacidade de link de rede virtual|Porcentagem|Máximo|Porcentagem de capacidade de link de rede virtual utilizada por uma zona de DNS privado|Sem dimensões|
|VirtualNetworkLinkCount|Yes|Contagem de links de rede virtual|Contagem|Máximo|Número de redes virtuais vinculadas a uma zona de DNS privado|Sem dimensões|
|VirtualNetworkWithRegistrationCapacityUtilization|No|Utilização da capacidade do link de registro de rede virtual|Porcentagem|Máximo|Percentual de link de rede virtual com a capacidade de registro automático utilizada por uma zona de DNS privado|Sem dimensões|
|VirtualNetworkWithRegistrationLinkCount|Yes|Contagem de links de registro de rede virtual|Contagem|Máximo|Número de redes virtuais vinculadas a uma zona de DNS privado com o registro automático habilitado|Sem dimensões|


## <a name="microsoftnetworkprivateendpoints"></a>Microsoft. Network/privateEndpoints

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|PEBytesIn|Yes|Bytes de entrada|Contagem|Total|Número total de bytes de saída|PrivateEndpointId|
|PEBytesOut|Yes|Bytes de saída|Contagem|Total|Número total de bytes de saída|PrivateEndpointId|


## <a name="microsoftnetworkprivatelinkservices"></a>Microsoft.Network/privateLinkServices

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|PLSBytesIn|Yes|Bytes de entrada|Contagem|Total|Número total de bytes de saída|PrivateLinkServiceId|
|PLSBytesOut|Yes|Bytes de saída|Contagem|Total|Número total de bytes de saída|PrivateLinkServiceId|
|PLSNatPortsUsage|Yes|Uso de portas NAT|Porcentagem|Média|Uso de portas NAT|PrivateLinkServiceId, PrivateLinkServiceIPAddress|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ByteCount|Yes|Contagem de Bytes|Bytes|Total|Número total de Bytes transmitidos no período|Port, Direction|
|BytesDroppedDDoS|Yes|DDoS de bytes de entrada removidos|BytesPerSecond|Máximo|DDoS de bytes de entrada removidos|Sem dimensões|
|BytesForwardedDDoS|Yes|DDoS de bytes de entrada encaminhados|BytesPerSecond|Máximo|DDoS de bytes de entrada encaminhados|Sem dimensões|
|BytesInDDoS|Yes|DDoS de bytes de entrada|BytesPerSecond|Máximo|DDoS de bytes de entrada|Sem dimensões|
|DDoSTriggerSYNPackets|Yes|Pacotes de entrada SYN para disparar a mitigação de DDoS|CountPerSecond|Máximo|Pacotes de entrada SYN para disparar a mitigação de DDoS|Sem dimensões|
|DDoSTriggerTCPPackets|Yes|Pacotes TCP de entrada a disparar a mitigação de DDoS|CountPerSecond|Máximo|Pacotes TCP de entrada a disparar a mitigação de DDoS|Sem dimensões|
|DDoSTriggerUDPPackets|Yes|Pacotes UDP de entrada a disparar a mitigação de DDoS|CountPerSecond|Máximo|Pacotes UDP de entrada a disparar a mitigação de DDoS|Sem dimensões|
|IfUnderDDoSAttack|Yes|Sob ataque DDoS ou não|Contagem|Máximo|Sob ataque DDoS ou não|Sem dimensões|
|PacketCount|Yes|Contagem de Pacotes|Contagem|Total|Número total de Pacotes transmitidos no período|Port, Direction|
|PacketsDroppedDDoS|Yes|DDoS de pacotes de entrada removidos|CountPerSecond|Máximo|DDoS de pacotes de entrada removidos|Sem dimensões|
|PacketsForwardedDDoS|Yes|DDoS de pacotes de entrada encaminhados|CountPerSecond|Máximo|DDoS de pacotes de entrada encaminhados|Sem dimensões|
|PacketsInDDoS|Yes|DDoS de pacotes de entrada|CountPerSecond|Máximo|DDoS de pacotes de entrada|Sem dimensões|
|SynCount|Yes|Contagem de SYN|Contagem|Total|Número total de Pacotes de SYN transmitidos no período|Port, Direction|
|TCPBytesDroppedDDoS|Yes|DDoS de bytes de TCP de entrada removidos|BytesPerSecond|Máximo|DDoS de bytes de TCP de entrada removidos|Sem dimensões|
|TCPBytesForwardedDDoS|Yes|DDoS de bytes de TCP de entrada encaminhados|BytesPerSecond|Máximo|DDoS de bytes de TCP de entrada encaminhados|Sem dimensões|
|TCPBytesInDDoS|Yes|DDoS de bytes de TCP de entrada|BytesPerSecond|Máximo|DDoS de bytes de TCP de entrada|Sem dimensões|
|TCPPacketsDroppedDDoS|Yes|DDoS de pacotes TCP de entrada removidos|CountPerSecond|Máximo|DDoS de pacotes TCP de entrada removidos|Sem dimensões|
|TCPPacketsForwardedDDoS|Yes|DDoS de pacotes TCP de entrada encaminhados|CountPerSecond|Máximo|DDoS de pacotes TCP de entrada encaminhados|Sem dimensões|
|TCPPacketsInDDoS|Yes|DDoS de pacotes TCP de entrada|CountPerSecond|Máximo|DDoS de pacotes TCP de entrada|Sem dimensões|
|UDPBytesDroppedDDoS|Yes|DDoS de bytes de UDP de entrada removidos|BytesPerSecond|Máximo|DDoS de bytes de UDP de entrada removidos|Sem dimensões|
|UDPBytesForwardedDDoS|Yes|DDoS de bytes de UDP de entrada encaminhados|BytesPerSecond|Máximo|DDoS de bytes de UDP de entrada encaminhados|Sem dimensões|
|UDPBytesInDDoS|Yes|DDoS de bytes de UDP de entrada|BytesPerSecond|Máximo|DDoS de bytes de UDP de entrada|Sem dimensões|
|UDPPacketsDroppedDDoS|Yes|DDoS de pacotes UDP de entrada removidos|CountPerSecond|Máximo|DDoS de pacotes UDP de entrada removidos|Sem dimensões|
|UDPPacketsForwardedDDoS|Yes|DDoS de pacotes UDP de entrada encaminhados|CountPerSecond|Máximo|DDoS de pacotes UDP de entrada encaminhados|Sem dimensões|
|UDPPacketsInDDoS|Yes|DDoS de pacotes UDP de entrada|CountPerSecond|Máximo|DDoS de pacotes UDP de entrada|Sem dimensões|
|VipAvailability|Yes|Disponibilidade do Caminho de Dados|Contagem|Média|Disponibilidade média de Endereço IP por duração de tempo|Porta|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Yes|Status do Ponto de Extremidade por Ponto de Extremidade|Contagem|Máximo|1 se o status de investigação de um ponto de extremidade for “Habilitado”; caso contrário, 0.|EndpointName|
|QpsByEndpoint|Yes|Consultas pelo Ponto de Extremidade Retornado|Contagem|Total|Número de vezes que um ponto de extremidade do Gerenciador de Tráfego foi retornado no período determinado|EndpointName|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|AverageBandwidth|Yes|Largura de banda de S2S do gateway|BytesPerSecond|Média|Largura de banda média site a site de um gateway em bytes por segundo|Sem dimensões|
|ExpressRouteGatewayCountOfRoutesAdvertisedToPeer|Yes|Contagem de rotas anunciadas para o par (visualização)|Contagem|Máximo|Contagem de rotas anunciadas para o par por ExpressRouteGateway|roleInstance|
|ExpressRouteGatewayCountOfRoutesLearnedFromPeer|Yes|Contagem de rotas aprendidas do par (versão prévia)|Contagem|Máximo|Contagem de rotas aprendidas do ponto por ExpressRouteGateway|roleInstance|
|ExpressRouteGatewayCpuUtilization|Yes|Utilização da CPU (versão prévia)|Contagem|Média|Utilização da CPU do gateway de ExpressRoute|roleInstance|
|ExpressRouteGatewayFrequencyOfRoutesChanged|No|Frequência de alteração de rotas (versão prévia)|Contagem|Total|Frequência de alteração de rotas no gateway de ExpressRoute|roleInstance|
|ExpressRouteGatewayNumberOfVmInVnet|No|Número de VMs na rede virtual (versão prévia)|Contagem|Máximo|Número de VMs na rede virtual|Sem dimensões|
|ExpressRouteGatewayPacketsPerSecond|No|Pacotes por segundo (visualização)|CountPerSecond|Média|Contagem de pacotes do gateway de ExpressRoute|roleInstance|
|P2SBandwidth|Yes|Largura de banda de P2S do gateway|BytesPerSecond|Média|Largura de banda média ponto a site de um gateway em bytes por segundo|Sem dimensões|
|P2SConnectionCount|Yes|Contagem de conexões P2S|Contagem|Máximo|Contagem de conexões ponto a site de um gateway|Protocolo|
|TunnelAverageBandwidth|Yes|Largura de Banda de Túnel|BytesPerSecond|Média|Largura de banda média de um túnel em bytes por segundo|ConnectionName, RemoteIP|
|TunnelEgressBytes|Yes|Bytes de Saída de Túnel|Bytes|Total|Bytes de saída de um túnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Yes|Remoção de Pacotes Incompatíveis de TS de Saída de Túnel|Contagem|Total|Contagem de remoção de pacotes de saída da incompatibilidade do seletor de tráfego de um túnel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Yes|Pacotes de Saída de Túnel|Contagem|Total|Contagem de pacotes de saída de um túnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Yes|Bytes de Entrada de Túnel|Bytes|Total|Bytes de entrada de um túnel|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Yes|Remoção de Pacotes Incompatíveis de TS de Entrada de Túnel|Contagem|Total|Contagem de remoção de pacotes de entrada da incompatibilidade do seletor de tráfego de um túnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|Yes|Pacotes de Entrada de Túnel|Contagem|Total|Contagem de pacotes de entrada de um túnel|ConnectionName, RemoteIP|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Yes|Tempo de ida e volta para Pings em uma VM|MilliSeconds|Média|Tempo de ida e volta para pings enviados para uma VM de destino|SourceCustomerAddress, DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Yes|Pings com Falha em uma VM|Porcentagem|Média|Porcentagem do número de pings com falha para o total de pings enviados de uma VM de destino|SourceCustomerAddress, DestinationCustomerAddress|


## <a name="microsoftnetworkvirtualrouters"></a>Microsoft. Network/virtualRouters

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|PeeringAvailability|Yes|Disponibilidade de BGP|Porcentagem|Média|Disponibilidade de BGP entre VirtualRouter e pares remotos|Par|


## <a name="microsoftnetworkvpngateways"></a>Microsoft. Network/vpnGateways

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|AverageBandwidth|Yes|Largura de banda de S2S do gateway|BytesPerSecond|Média|Largura de banda média site a site de um gateway em bytes por segundo|Sem dimensões|
|TunnelAverageBandwidth|Yes|Largura de Banda de Túnel|BytesPerSecond|Média|Largura de banda média de um túnel em bytes por segundo|ConnectionName, RemoteIP|
|TunnelEgressBytes|Yes|Bytes de Saída de Túnel|Bytes|Total|Bytes de saída de um túnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Yes|Remoção de Pacotes Incompatíveis de TS de Saída de Túnel|Contagem|Total|Contagem de remoção de pacotes de saída da incompatibilidade do seletor de tráfego de um túnel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Yes|Pacotes de Saída de Túnel|Contagem|Total|Contagem de pacotes de saída de um túnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Yes|Bytes de Entrada de Túnel|Bytes|Total|Bytes de entrada de um túnel|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Yes|Remoção de Pacotes Incompatíveis de TS de Entrada de Túnel|Contagem|Total|Contagem de remoção de pacotes de entrada da incompatibilidade do seletor de tráfego de um túnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|Yes|Pacotes de Entrada de Túnel|Contagem|Total|Contagem de pacotes de entrada de um túnel|ConnectionName, RemoteIP|


## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|incoming|Yes|Mensagens de entrada|Contagem|Total|A contagem de todas as chamadas de API enviadas com sucesso. |Sem dimensões|
|incoming.all.failedrequests|Yes|Todas as solicitações com falha recebidas|Contagem|Total|Total de solicitações com falha recebidas para um hub de notificação|Sem dimensões|
|incoming.all.requests|Yes|Todas as solicitações recebidas|Contagem|Total|Total de solicitações recebidas para um hub de notificação|Sem dimensões|
|incoming.scheduled|Yes|Notificações por push agendadas enviadas|Contagem|Total|Notificações por push agendadas canceladas|Sem dimensões|
|incoming.scheduled.cancel|Yes|Notificações por push agendadas canceladas|Contagem|Total|Notificações por push agendadas canceladas|Sem dimensões|
|installation.all|Yes|Operações de gerenciamento de instalação|Contagem|Total|Operações de gerenciamento de instalação|Sem dimensões|
|installation.delete|Yes|Excluir operações de instalação|Contagem|Total|Excluir operações de instalação|Sem dimensões|
|installation.get|Yes|Obter operações de instalação|Contagem|Total|Obter operações de instalação|Sem dimensões|
|installation.patch|Yes|Operações de instalação de patch|Contagem|Total|Operações de instalação de patch|Sem dimensões|
|installation.upsert|Yes|Criar ou atualizar operações de instalação|Contagem|Total|Criar ou atualizar operações de instalação|Sem dimensões|
|notificationhub.pushes|Yes|Todas as notificações de saída|Contagem|Total|Todas as notificações de saída do hub de notificação|Sem dimensões|
|outgoing.allpns.badorexpiredchannel|Yes|Erros de canal incorreto ou expirado|Contagem|Total|A contagem de pushes que falharam porque o canal/token/registrationId do registro estava expirado ou inválido.|Sem dimensões|
|outgoing.allpns.channelerror|Yes|Erros de canal|Contagem|Total|A contagem de pushes que falharam porque o canal era inválido, não associado com o aplicativo correto, restrito ou expirado.|Sem dimensões|
|outgoing.allpns.invalidpayload|Yes|Erros de conteúdo|Contagem|Total|A contagem de pushes que falharam porque o PNS retornou um erro de conteúdo inválido.|Sem dimensões|
|outgoing.allpns.pnserror|Yes|Erros de sistema de notificação externa|Contagem|Total|A contagem de pushes que falharam porque houve um problema na comunicação com o PNS (exclui problemas de autenticação).|Sem dimensões|
|outgoing.allpns.success|Yes|Notificações de sucesso|Contagem|Total|A contagem de todas as notificações bem-sucedidas.|Sem dimensões|
|outgoing.apns.badchannel|Yes|Erro de canal inválido do APNS|Contagem|Total|A contagem de pushes que falharam porque o token é inválido (código de status do APNS: 8).|Sem dimensões|
|outgoing.apns.expiredchannel|Yes|Erro de canal expirado do APNS|Contagem|Total|A contagem de tokens que foram invalidados pelo canal de comentários do APNS.|Sem dimensões|
|outgoing.apns.invalidcredentials|Yes|Erros de autorização do APNS|Contagem|Total|A contagem de pushes que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Sem dimensões|
|outgoing.apns.invalidnotificationsize|Yes|Erro de tamanho de notificação inválido do APNS|Contagem|Total|A contagem de pushes que falharam porque o conteúdo era muito grande (código de status do APNS: 7).|Sem dimensões|
|outgoing.apns.pnserror|Yes|Erros do APNS|Contagem|Total|A contagem de pushes que falharam devido a erros de comunicação com o APNS.|Sem dimensões|
|outgoing.apns.success|Yes|Notificações do APNS bem-sucedidas|Contagem|Total|A contagem de todas as notificações bem-sucedidas.|Sem dimensões|
|outgoing.gcm.authenticationerror|Yes|Erros de autenticação do GCM|Contagem|Total|A contagem de pushes que falharam porque o PNS não aceitou as credenciais fornecidas, as credenciais estão bloqueadas ou o SenderId não está configurado corretamente no aplicativo (resultado do GCM: MismatchedSenderId).|Sem dimensões|
|outgoing.gcm.badchannel|Yes|Erro de canal inválido do GCM|Contagem|Total|A contagem de pushes que falharam porque o registrationId do registro estava expirado ou inválido (resultado GCM: registo inválido).|Sem dimensões|
|outgoing.gcm.expiredchannel|Yes|Erro de canal expirado do GCM|Contagem|Total|A contagem de pushes que falharam porque o registrationId do registro estava expirado (resultado do GCM: NotRegistered).|Sem dimensões|
|outgoing.gcm.invalidcredentials|Yes|Erros de autorização do GCM (credenciais inválidas)|Contagem|Total|A contagem de pushes que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Sem dimensões|
|outgoing.gcm.invalidnotificationformat|Yes|Formato de notificação inválido do GCM|Contagem|Total|A contagem de pushes que falharam porque o conteúdo não foi formatado corretamente (resultado do GCM: InvalidDataKey ou InvalidTtl).|Sem dimensões|
|outgoing.gcm.invalidnotificationsize|Yes|Erro de tamanho de notificação inválido do GCM|Contagem|Total|A contagem de pushes que falharam porque o conteúdo era muito grande (resultado do GCM: MessageTooBig).|Sem dimensões|
|outgoing.gcm.pnserror|Yes|Erros do GCM|Contagem|Total|A contagem de pushes que falharam devido a erros de comunicação com o GCM.|Sem dimensões|
|outgoing.gcm.success|Yes|Notificações do GCM bem-sucedidas|Contagem|Total|A contagem de todas as notificações bem-sucedidas.|Sem dimensões|
|outgoing.gcm.throttled|Yes|Notificações restritas do GCM|Contagem|Total|A contagem de pushes que falharam porque o GCM restringiu este aplicativo (código de status do GCM: 501 a 599 ou resultado: indisponível).|Sem dimensões|
|outgoing.gcm.wrongchannel|Yes|Erro de canal incorreto do GCM|Contagem|Total|A contagem de pushes que falharam porque o registrationId do registro não está associado ao aplicativo atual (resultado do GCM: InvalidPackageName).|Sem dimensões|
|outgoing.mpns.authenticationerror|Yes|Erros de autenticação do MPNS|Contagem|Total|A contagem de pushes que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Sem dimensões|
|outgoing.mpns.badchannel|Yes|Erro de canal inválido do MPNS|Contagem|Total|A contagem de pushes que falharam porque o ChannelURI do registro não foi reconhecido (status do MPNS: 404 não encontrado).|Sem dimensões|
|outgoing.mpns.channeldisconnected|Yes|Canal do MPNS desconectado|Contagem|Total|A contagem de pushes que falharam porque o ChannelURI do registro foi desconectado (status do MPNS: 412 não encontrado).|Sem dimensões|
|outgoing.mpns.dropped|Yes|Notificações descartadas do MPNS|Contagem|Total|A contagem de pushes que foram descartados pelo MPNS (cabeçalho de resposta do MPNS: X-NotificationStatus: QueueFull ou Suppressed).|Sem dimensões|
|outgoing.mpns.invalidcredentials|Yes|Credenciais inválidas do MPNS|Contagem|Total|A contagem de pushes que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas.|Sem dimensões|
|outgoing.mpns.invalidnotificationformat|Yes|Erro de tamanho de notificação inválido do MPNS|Contagem|Total|A contagem de pushes que falharam porque o conteúdo da notificação era muito grande.|Sem dimensões|
|outgoing.mpns.pnserror|Yes|Erros do MPNS|Contagem|Total|A contagem de pushes que falharam devido a erros de comunicação com o MPNS.|Sem dimensões|
|outgoing.mpns.success|Yes|Notificações de sucesso do MPNS|Contagem|Total|A contagem de todas as notificações bem-sucedidas.|Sem dimensões|
|outgoing.mpns.throttled|Yes|Notificações restritas do MPNS|Contagem|Total|A contagem de pushes que falharam porque o MPNS está limitando este aplicativo (WNS MPNS: 406 não aceitável).|Sem dimensões|
|outgoing.wns.authenticationerror|Yes|Erros de autenticação do WNS|Contagem|Total|Notificação não entregue devido a erros de comunicação com o Windows Live, credenciais inválidas ou token incorreto.|Sem dimensões|
|outgoing.wns.badchannel|Yes|Erro de canal inválido do WNS|Contagem|Total|A contagem de pushes que falharam porque o ChannelURI do registro não foi reconhecido (status do WNS: 404 não encontrado).|Sem dimensões|
|outgoing.wns.channeldisconnected|Yes|Canal do WNS desconectado|Contagem|Total|A notificação foi descartada porque o ChannelURI do registro está restrito (cabeçalho de resposta do WNS: X-WNS-DeviceConnectionStatus: disconnected).|Sem dimensões|
|outgoing.wns.channelthrottled|Yes|Canal do WNS restrito|Contagem|Total|A notificação foi descartada porque o ChannelURI do registro está restrito (cabeçalho de resposta do WNS: X-WNS-NotificationStatus:channelThrottled).|Sem dimensões|
|outgoing.wns.dropped|Yes|Notificações do WNS descartadas|Contagem|Total|A notificação foi descartada porque o ChannelURI do registro está restrito (X-WNS-NotificationStatus: dropped but not X-WNS-DeviceConnectionStatus: disconnected).|Sem dimensões|
|outgoing.wns.expiredchannel|Yes|Erro de canal expirado do WNS|Contagem|Total|A contagem de pushes que falharam porque o ChannelURI está expirado (status do WNS: 410 perdido).|Sem dimensões|
|outgoing.wns.invalidcredentials|Yes|Erros de autorização do WNS (credenciais inválidas)|Contagem|Total|A contagem de pushes que falharam porque o PNS não aceitou as credenciais fornecidas ou as credenciais estão bloqueadas. (O Windows Live não reconhece as credenciais).|Sem dimensões|
|outgoing.wns.invalidnotificationformat|Yes|Formato de notificação inválido do WNS|Contagem|Total|O formato da notificação é inválido (status do WNS: 400). Observe que o WNS não rejeita todos os conteúdos inválidos.|Sem dimensões|
|outgoing.wns.invalidnotificationsize|Yes|Erro de tamanho de notificação inválido do WNS|Contagem|Total|O conteúdo de notificação é muito grande (status do WNS: 413).|Sem dimensões|
|outgoing.wns.invalidtoken|Yes|Erros de autorização do WNS (token inválido)|Contagem|Total|O token fornecido ao WNS não é válido (status do WNS: 401 não autorizado).|Sem dimensões|
|outgoing.wns.pnserror|Yes|Erros do WNS|Contagem|Total|Notificação não entregue devido a erros de comunicação com o WNS.|Sem dimensões|
|outgoing.wns.success|Yes|Notificações do WNS bem-sucedidas|Contagem|Total|A contagem de todas as notificações bem-sucedidas.|Sem dimensões|
|outgoing.wns.throttled|Yes|Notificações restritas do WNS|Contagem|Total|A contagem de pushes que falharam porque o WNS está limitando este aplicativo (status do WNS: 406 não aceitável).|Sem dimensões|
|outgoing.wns.tokenproviderunreachable|Yes|Erros de autorização do WNS (inacessível)|Contagem|Total|O Windows Live não está acessível.|Sem dimensões|
|outgoing.wns.wrongtoken|Yes|Erros de autorização do WNS (token incorreto)|Contagem|Total|O token fornecido ao WNS é válido, mas para outro aplicativo (status do WNS: 403 proibido). Isso pode acontecer se o ChannelURI do registro estiver associado a outro aplicativo. Verifique se o aplicativo cliente está associado ao mesmo aplicativo cujas credenciais estão no hub de notificação.|Sem dimensões|
|registration.all|Yes|Operações de Registro|Contagem|Total|A contagem de todas as operações de registro bem-sucedidas (consultas de atualizações de criações e exclusões). |Sem dimensões|
|registration.create|Yes|Operação de criação de registro|Contagem|Total|A contagem de todas as criações de registro bem-sucedidas.|Sem dimensões|
|registration.delete|Yes|Operação de exclusão de registro|Contagem|Total|A contagem de todas as exclusões de registro bem-sucedidas.|Sem dimensões|
|registration.get|Yes|Operações de leitura do registro|Contagem|Total|A contagem de todas as consultas de registro bem-sucedidas.|Sem dimensões|
|registration.update|Yes|Operação de atualização de registro|Contagem|Total|A contagem de todas as atualizações de registro bem-sucedidas.|Sem dimensões|
|scheduled.pending|Yes|Notificações agendadas pendentes|Contagem|Total|Notificações agendadas pendentes|Sem dimensões|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Average_% Available Memory|Yes|% de Memória Disponível|Contagem|Média|Average_% Available Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Available Swap Space|Yes|% de Espaço de Permuta Disponível|Contagem|Média|Average_% Available Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Committed Bytes In Use|Yes|% de Bytes Confirmados em Uso|Contagem|Média|Average_% Committed Bytes In Use|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% DPC Time|Yes|% de Tempo de DPC|Contagem|Média|Average_% DPC Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Inodes|Yes|% de Inodes livres|Contagem|Média|Average_% Free Inodes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Space|Yes|% de Espaço Livre|Contagem|Média|Average_% Free Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Idle Time|Yes|% de Tempo Ocioso|Contagem|Média|Average_% Idle Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Interrupt Time|Yes|% de Tempo de Interrupção|Contagem|Média|Average_% Interrupt Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% IO Wait Time|Yes|% de Tempo de Espera de E/S|Contagem|Média|Average_% IO Wait Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Nice Time|Yes|% de Tempo Adequado|Contagem|Média|Average_% Nice Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Privileged Time|Yes|% de Tempo Privilegiado|Contagem|Média|Average_% Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Processor Time|Yes|% Tempo do Processador|Contagem|Média|Average_% Processor Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Inodes|Yes|% de Inodes Usados|Contagem|Média|Average_% Used Inodes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Memory|Yes|% de Memória Usada|Contagem|Média|Average_% Used Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Space|Yes|% de Espaço Usado|Contagem|Média|Average_% Used Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Swap Space|Yes|% de Espaço de Permuta Usado|Contagem|Média|Average_% Used Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% User Time|Yes|% de Tempo do Usuário|Contagem|Média|Average_% User Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes|Yes|MBytes Disponíveis|Contagem|Média|Average_Available MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Memory|Yes|MBytes de Memória Disponíveis|Contagem|Média|Average_Available MBytes Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Swap|Yes|MBytes de Espaço de Permuta Disponíveis|Contagem|Média|Average_Available MBytes Swap|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. de segundos/Leitura do Disco|Yes|Média de segundos/Leitura do Disco|Contagem|Média|Average_Avg. de segundos/Leitura do Disco|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. de segundos/Transferência do Disco|Yes|Média de segundos/Transferência do Disco|Contagem|Média|Average_Avg. de segundos/Transferência do Disco|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. de segundos/Gravação do Disco|Yes|Média de segundos/Gravação do Disco|Contagem|Média|Average_Avg. de segundos/Gravação do Disco|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Received/sec|Yes|Bytes Recebidos/s|Contagem|Média|Average_Bytes Received/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Sent/sec|Yes|Bytes Enviados/s|Contagem|Média|Average_Bytes Sent/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Total/sec|Yes|Bytes Totais/s|Contagem|Média|Average_Bytes Total/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current Disk Queue Length|Yes|Comprimento da fila atual de disco|Contagem|Média|Average_Current Disk Queue Length|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Read Bytes/sec|Yes|Bytes Lidos no Disco/s|Contagem|Média|Average_Disk Read Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Reads/sec|Yes|Leituras de Disco/s|Contagem|Média|Average_Disk Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Transfers/sec|Yes|Transferências de Disco/s|Contagem|Média|Average_Disk Transfers/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Write Bytes/sec|Yes|Bytes Gravados no Disco/s|Contagem|Média|Average_Disk Write Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Writes/sec|Yes|Gravações de Disco/s|Contagem|Média|Average_Disk Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Megabytes|Yes|Megabytes Livres|Contagem|Média|Average_Free Megabytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Physical Memory|Yes|Memória Física Livre|Contagem|Média|Average_Free Physical Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Space in Paging Files|Yes|Espaço Livre em Arquivos de Paginação|Contagem|Média|Average_Free Space in Paging Files|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Virtual Memory|Yes|Memória Virtual Livre|Contagem|Média|Average_Free Virtual Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical Disk Bytes/sec|Yes|Bytes de Disco Lógico/s|Contagem|Média|Average_Logical Disk Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Reads/sec|Yes|Leituras de Página/s|Contagem|Média|Average_Page Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Writes/sec|Yes|Gravações de Página/s|Contagem|Média|Average_Page Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages/sec|Yes|Páginas/s|Contagem|Média|Average_Pages/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct Privileged Time|Yes|% de Tempo Privilegiado|Contagem|Média|Average_Pct Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct User Time|Yes|% de Tempo do Usuário|Contagem|Média|Average_Pct User Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical Disk Bytes/sec|Yes|Bytes/s do Disco Físico|Contagem|Média|Average_Physical Disk Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Yes|Processos|Contagem|Média|Average_Processes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processor Queue Length|Yes|Tamanho da fila do processador|Contagem|Média|Average_Processor Queue Length|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size Stored In Paging Files|Yes|Tamanho Armazenado em Arquivos de Paginação|Contagem|Média|Average_Size Stored In Paging Files|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes|Yes|Total de Bytes|Contagem|Média|Average_Total Bytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Received|Yes|Total de Bytes Recebidos|Contagem|Média|Average_Total Bytes Received|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Transmitted|Yes|Total de Bytes Transmitidos|Contagem|Média|Average_Total Bytes Transmitted|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Collisions|Yes|Total de Colisões|Contagem|Média|Average_Total Collisions|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Packets Received|Yes|Total de Pacotes Recebidos|Contagem|Média|Average_Total Packets Received|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Packets Transmitted|Yes|Total de Pacotes Transmitidos|Contagem|Média|Average_Total Packets Transmitted|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Rx Errors|Yes|Total de Erros de Rx|Contagem|Média|Average_Total Rx Errors|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Tx Errors|Yes|Total de Erros de Tx|Contagem|Média|Average_Total Tx Errors|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Yes|Tempo de atividade|Contagem|Média|Average_Uptime|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used MBytes Swap Space|Yes|MBytes de Espaço de Permuta Usado|Contagem|Média|Average_Used MBytes Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory kBytes|Yes|KBytes de Memória Usada|Contagem|Média|Average_Used Memory kBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory MBytes|Yes|MBytes de Memória Usada|Contagem|Média|Average_Used Memory MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Sim|Usuários|Contagem|Média|Average_Users|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual Shared Memory|Yes|Memória Virtual Compartilhada|Contagem|Média|Average_Virtual Shared Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Evento|Yes|Evento|Contagem|Média|Evento|Source, EventLog, Computer, EventCategory, EventLevel, EventLevelName, EventID|
|Pulsação|Yes|Pulsação|Contagem|Total|Pulsação|Computador, OSType, Versão, SourceComputerId|
|Atualizar|Yes|Atualizar|Contagem|Média|Atualizar|Computer, Product, Classification, UpdateState, Optional, Approved|


## <a name="microsoftpeeringpeerings"></a>Microsoft. emparelhamento/emparelhamentos

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|EgressTrafficRate|Yes|Taxa de tráfego de saída|BitsPerSecond|Média|Taxa de tráfego de saída em bits por segundo|ConnectionID, SessionIp, TrafficClass|
|IngressTrafficRate|Yes|Taxa de tráfego de entrada|BitsPerSecond|Média|Taxa de tráfego de entrada em bits por segundo|ConnectionID, SessionIp, TrafficClass|
|SessionAvailability|Yes|Disponibilidade da sessão|Contagem|Média|Disponibilidade da sessão de emparelhamento|ConnectionID, SessionIp|


## <a name="microsoftpeeringpeeringservices"></a>Microsoft. emparelhamento/peeringServices

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|PrefixLatency|Yes|Latência de prefixo|Milissegundos|Média|Latência de prefixo mediana|Prefixoname|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|memory_metric|Yes|Memória (Gen1)|Bytes|Média|Memória. Intervalo de 0 a 3 GB para A1, 0 a 5 GB para A2, 0 a 10 GB para A3, 0 a 25 GB para A4, 0 a 50 GB para A5 e 0 a 100 GB para A6. Com suporte apenas para recursos do Power BI Embedded Geração 1.|Sem dimensões|
|memory_thrashing_metric|Yes|Ultrapaginação de memória (conjuntos de dados) (Gen1)|Porcentagem|Média|Sobrecarga de memória média. Com suporte apenas para recursos do Power BI Embedded Geração 1.|Sem dimensões|
|qpu_high_utilization_metric|Yes|Alta utilização de QPU (Gen1)|Contagem|Total|Alta utilização de QPU no último minuto, 1 para alta utilização de QPU, caso contrário 0. Com suporte apenas para recursos do Power BI Embedded Geração 1.|Sem dimensões|
|QueryDuration|Yes|Duração da consulta (conjuntos de dados) (Gen1)|Milissegundos|Média|Duração da Consulta DAX no último intervalo. Com suporte apenas para recursos do Power BI Embedded Geração 1.|Sem dimensões|
|QueryPoolJobQueueLength|Yes|Comprimento da fila de trabalho do pool de consulta (conjunto de dados) (Gen1)|Contagem|Média|Número de trabalhos na fila do pool de threads de consulta. Com suporte apenas para recursos do Power BI Embedded Geração 1.|Sem dimensões|


## <a name="microsoftprojectbabylonaccounts"></a>Microsoft. ProjectBabylon/accounts

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ScanCancelled|Yes|Verificação cancelada|Contagem|Total|Indica o número de verificações canceladas.|ResourceId|
|ScanCompleted|Yes|Verificação concluída|Contagem|Total|Indica o número de verificações concluídas com êxito.|ResourceId|
|ScanFailed|Yes|Falha na verificação|Contagem|Total|Indica o número de verificações que falharam.|ResourceId|
|ScanTimeTaken|Yes|Tempo de verificação tomado|Segundos|Total|Indica o tempo total de verificação em segundos.|ResourceId|


## <a name="microsoftpurviewaccounts"></a>Microsoft. alcance/accounts

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ScanCancelled|Yes|Verificação cancelada|Contagem|Total|Indica o número de verificações canceladas.||
|ScanCompleted|Yes|Verificação concluída|Contagem|Total|Indica o número de verificações concluídas com êxito.||
|ScanFailed|Yes|Falha na verificação|Contagem|Total|Indica o número de verificações que falharam.||
|ScanTimeTaken|Yes|Tempo de verificação tomado|Segundos|Total|Indica o tempo total de verificação em segundos.||


## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ActiveConnections|No|ActiveConnections|Contagem|Total|Total de ActiveConnections para o Microsoft.Relay.|EntityName|
|ActiveListeners|No|ActiveListeners|Contagem|Total|Total de ActiveListeners para o Microsoft.Relay.|EntityName|
|BytesTransferred|Yes|BytesTransferred|Bytes|Total|Total de BytesTransferred para o Microsoft.Relay.|EntityName|
|ListenerConnections-ClientError|No|ListenerConnections-ClientError|Contagem|Total|ClientError no ListenerConnections para o Microsoft.Relay.|EntityName, OperationResult|
|ListenerConnections-ServerError|No|ListenerConnections-ServerError|Contagem|Total|ServerError no ListenerConnections para o Microsoft.Relay.|EntityName, OperationResult|
|ListenerConnections-Success|No|ListenerConnections-Success|Contagem|Total|ListenerConnections bem-sucedido para o Microsoft.Relay.|EntityName, OperationResult|
|ListenerConnections-TotalRequests|No|ListenerConnections-TotalRequests|Contagem|Total|Total de ListenerConnections para o  Microsoft.Relay.|EntityName|
|ListenerDisconnects|No|ListenerDisconnects|Contagem|Total|Total de ListenerDisconnects para o Microsoft.Relay.|EntityName|
|SenderConnections-ClientError|No|SenderConnections-ClientError|Contagem|Total|ClientError no SenderConnections para o Microsoft.Relay.|EntityName, OperationResult|
|SenderConnections-ServerError|No|SenderConnections-ServerError|Contagem|Total|ServerError no SenderConnections para o Microsoft.Relay.|EntityName, OperationResult|
|SenderConnections-Success|No|SenderConnections-Success|Contagem|Total|SenderConnections bem-sucedido para o Microsoft.Relay.|EntityName, OperationResult|
|SenderConnections-TotalRequests|No|SenderConnections-TotalRequests|Contagem|Total|Total de solicitações SenderConnections para o Microsoft.Relay.|EntityName|
|SenderDisconnects|No|SenderDisconnects|Contagem|Total|Total de SenderDisconnects para o Microsoft.Relay.|EntityName|


## <a name="microsoftresourcessubscriptions"></a>microsoft.resources/subscriptions

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Latency|Yes|Dados de latência de solicitações de entrada http|Contagem|Média|Dados de latência de solicitações de entrada http|Método, namespace, RequestRegion, ResourceType, Microsoft. SubscriptionId|
|Tráfego|Yes|Tráfego|Contagem|Média|Tráfego http|RequestRegion, StatusCode, StatusCodeClass, ResourceType, namespace, Microsoft. SubscriptionId|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|SearchLatency|Yes|Latência de pesquisa|Segundos|Média|Latência média de pesquisa do serviço de pesquisa|Sem dimensões|
|SearchQueriesPerSecond|Yes|Consultas de pesquisa por segundo|CountPerSecond|Média|Consultas de pesquisa por segundo para o serviço de pesquisa|Sem dimensões|
|ThrottledSearchQueriesPercentage|Yes|Porcentagem das consultas de pesquisa limitadas|Porcentagem|Média|Porcentagem de consultas de pesquisa que eram limitadas para o serviço de pesquisa|Sem dimensões|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ActiveConnections|No|ActiveConnections|Contagem|Total|Conexões Totalmente Ativas para o Microsoft.ServiceBus.||
|ActiveMessages|No|Contagem de mensagens em uma fila/tópico.|Contagem|Média|Contagem de mensagens em uma fila/tópico.|EntityName|
|ConnectionsClosed|No|Conexões Fechadas.|Contagem|Média|Conexões Fechadas para o Microsoft.ServiceBus.|EntityName|
|ConnectionsOpened|No|Conexões Abertas.|Contagem|Média|Conexões Abertas para o Microsoft.ServiceBus.|EntityName|
|CPUXNS|No|CPU (Preterido)|Porcentagem|Máximo|Métrica de uso de CPU do namespace Premium do barramento de serviço. Essa métrica é preterida. Em vez disso, use a métrica de CPU (NamespaceCpuUsage).|Réplica|
|DeadletteredMessages|No|Contagem de mensagens mortas em uma Fila/Tópico.|Contagem|Média|Contagem de mensagens mortas em uma Fila/Tópico.|EntityName|
|IncomingMessages|Yes|Mensagens de entrada|Contagem|Total|Mensagens de Entrada para Microsoft.ServiceBus.|EntityName|
|IncomingRequests|Yes|Solicitações de entrada|Contagem|Total|Solicitações de Entrada para o Microsoft.ServiceBus.|EntityName|
|Mensagens|No|Contagem de mensagens em uma fila/tópico.|Contagem|Média|Contagem de mensagens em uma fila/tópico.|EntityName|
|NamespaceCpuUsage|No|CPU|Porcentagem|Máximo|Métrica de uso de CPU do namespace Premium do barramento de serviço.|Réplica|
|NamespaceMemoryUsage|No|Uso de Memória|Porcentagem|Máximo|Métrica de uso de memória do namespace Premium do barramento de serviço.|Réplica|
|OutgoingMessages|Yes|Mensagens de saída|Contagem|Total|Mensagens de Saída para o Microsoft.ServiceBus.|EntityName|
|ScheduledMessages|No|Contagem de mensagens agendadas em uma Fila/Tópico.|Contagem|Média|Contagem de mensagens agendadas em uma Fila/Tópico.|EntityName|
|ServerErrors|No|Erros do Servidor.|Contagem|Total|Erros do Servidor para o Microsoft.ServiceBus.|EntityName, OperationResult|
|Tamanho|No|Tamanho|Bytes|Média|Tamanho de uma fila/tópico em bytes.|EntityName|
|SuccessfulRequests|No|Solicitações bem sucedidas|Contagem|Total|Total de solicitações bem-sucedidas para um namespace|EntityName, OperationResult|
|ThrottledRequests|No|Solicitações Limitadas.|Contagem|Total|Solicitações Limitadas para o Microsoft.ServiceBus.|EntityName, OperationResult|
|UserErrors|No|Erros de Usuário.|Contagem|Total|Erros de Usuário para o Microsoft.ServiceBus.|EntityName, OperationResult|
|WSXNS|No|Uso de Memória (Preterido)|Porcentagem|Máximo|Métrica de uso de memória do namespace Premium do barramento de serviço. Essa métrica foi preterida. Use a métrica de uso de memória (NamespaceMemoryUsage) em vez disso.|Réplica|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ActualCpu|No|ActualCpu|Contagem|Média|Uso real da CPU em núcleos de mili|ApplicationName, ServiceName, CodePackageName, ServiceId|
|ActualMemory|No|ActualMemory|Bytes|Média|Uso real da memória em MB|ApplicationName, ServiceName, CodePackageName, ServiceId|
|AllocatedCpu|No|AllocatedCpu|Contagem|Média|CPU alocada para este contêiner em núcleos de mili|ApplicationName, ServiceName, CodePackageName, ServiceId|
|AllocatedMemory|No|AllocatedMemory|Bytes|Média|Memória alocada para este contêiner em MB|ApplicationName, ServiceName, CodePackageName, ServiceId|
|ApplicationStatus|No|ApplicationStatus|Contagem|Média|Status do aplicativo de malha Service Fabric|ApplicationName, status|
|ContainerStatus|No|ContainerStatus|Contagem|Média|Status do contêiner no aplicativo de malha Service Fabric|ApplicationName, ServiceName, CodePackageName, ServiceId, status|
|CpuUtilization|No|CpuUtilization|Porcentagem|Média|Utilização da CPU para este contêiner como porcentagem de AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceId|
|MemoryUtilization|No|MemoryUtilization|Porcentagem|Média|Utilização da CPU para este contêiner como porcentagem de AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceId|
|RestartCount|No|RestartCount|Contagem|Média|Reiniciar a contagem de um contêiner no aplicativo de malha Service Fabric|ApplicationName, status, ServiceName, nome de réplica, CodePackageName|
|ServiceReplicaStatus|No|ServiceReplicaStatus|Contagem|Média|Status de integridade de uma réplica de serviço no aplicativo de malha Service Fabric|ApplicationName, status, ServiceName, nome de réplica|
|ServiceStatus|No|ServiceStatus|Contagem|Média|Status de integridade de um serviço no aplicativo de malha Service Fabric|ApplicationName, status, ServiceName|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ConnectionCount|Yes|Contagem de conexão|Contagem|Máximo|A quantidade de conexões do usuário.|Ponto de extremidade|
|InboundTraffic|Yes|Tráfego de entrada|Bytes|Total|O tráfego de entrada do serviço|Sem dimensões|
|MessageCount|Yes|Contagem de mensagens|Contagem|Total|A quantidade total de mensagens.|Sem dimensões|
|OutboundTraffic|Yes|Tráfego de saída|Bytes|Total|O tráfego de saída do serviço|Sem dimensões|
|SystemErrors|Yes|Erros do sistema|Porcentagem|Máximo|A porcentagem de erros do sistema|Sem dimensões|
|UserErrors|Yes|Erros do usuário|Porcentagem|Máximo|A porcentagem de erros do usuário|Sem dimensões|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|avg_cpu_percent|Yes|Percentual médio de CPU|Porcentagem|Média|Percentual médio de CPU|Sem dimensões|
|io_bytes_read|Yes|Bytes de E/S lidos|Bytes|Média|Bytes de E/S lidos|Sem dimensões|
|io_bytes_written|Yes|Bytes de E/S gravados|Bytes|Média|Bytes de E/S gravados|Sem dimensões|
|io_requests|Yes|Contagem de solicitações de E/S|Contagem|Média|Contagem de solicitações de E/S|Sem dimensões|
|reserved_storage_mb|Yes|Espaço de armazenamento reservado|Contagem|Média|Espaço de armazenamento reservado|Sem dimensões|
|storage_space_used_mb|Yes|Espaço de armazenamento usado|Contagem|Média|Espaço de armazenamento usado|Sem dimensões|
|virtual_core_count|Yes|Contagem de núcleos virtuais|Contagem|Média|Contagem de núcleos virtuais|Sem dimensões|


## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|active_queries|Yes|Consultas ativas|Contagem|Total|Consultas ativas em todos os grupos de carga de trabalho. Aplica-se somente a data warehouses.|Sem dimensões|
|allocated_data_storage|Yes|Espaço alocado de dados|Bytes|Média|Armazenamento de dados alocado. Não aplicável a data warehouses.|Sem dimensões|
|app_cpu_billed|Yes|CPU do aplicativo cobrado|Contagem|Total|CPU do aplicativo cobrada. Aplica-se a bancos de dados sem servidor.|Sem dimensões|
|app_cpu_percent|Yes|Percentual de CPU do aplicativo|Porcentagem|Média|Porcentagem de CPU do aplicativo. Aplica-se a bancos de dados sem servidor.|Sem dimensões|
|app_memory_percent|Yes|Percentual de memória do aplicativo|Porcentagem|Média|Porcentagem de memória do aplicativo. Aplica-se a bancos de dados sem servidor.|Sem dimensões|
|base_blob_size_bytes|Yes|Tamanho do armazenamento de BLOBs base|Bytes|Máximo|Tamanho do armazenamento de BLOBs base. Aplica-se a bancos de dados de hiperescala.|Sem dimensões|
|blocked_by_firewall|Yes|Bloqueado pelo firewall|Contagem|Total|Bloqueado pelo firewall|Sem dimensões|
|cache_hit_percent|Yes|Percentual de ocorrência no cache|Porcentagem|Máximo|Porcentagem de acesso ao cache. Aplica-se somente a data warehouses.|Sem dimensões|
|cache_used_percent|Yes|Percentual de cache usado|Porcentagem|Máximo|Percentual de cache usado. Aplica-se somente a data warehouses.|Sem dimensões|
|connection_failed|Yes|Conexões com falha|Contagem|Total|Conexões com falha|Sem dimensões|
|connection_successful|Yes|Conexões bem sucedidas|Contagem|Total|Conexões bem sucedidas|Sem dimensões|
|cpu_limit|Yes|Limite da CPU|Contagem|Média|Limite de CPU. Aplica-se a bancos de dados baseados em vCore.|Sem dimensões|
|cpu_percent|Yes|Percentual de CPU|Porcentagem|Média|Percentual de CPU|Sem dimensões|
|cpu_used|Yes|CPU usada|Contagem|Média|CPU usada. Aplica-se a bancos de dados baseados em vCore.|Sem dimensões|
|deadlock|Yes|Deadlocks|Contagem|Total|Deadlocks. Não aplicável a data warehouses.|Sem dimensões|
|diff_backup_size_bytes|Yes|Tamanho de armazenamento diferencial de backup|Bytes|Máximo|Tamanho do armazenamento de backup diferencial cumulativo. Aplica-se a bancos de dados baseados em vCore. Não aplicável a bancos de dados de hiperescala.|Sem dimensões|
|dtu_consumption_percent|Yes|Porcentagem de DTU|Porcentagem|Média|Porcentagem de DTU. Aplica-se a bancos de dados baseados em DTU.|Sem dimensões|
|dtu_limit|Yes|Limite de DTU|Contagem|Média|Limite de DTU. Aplica-se a bancos de dados baseados em DTU.|Sem dimensões|
|dtu_used|Yes|DTU usado|Contagem|Média|DTU usado. Aplica-se a bancos de dados baseados em DTU.|Sem dimensões|
|dwu_consumption_percent|Yes|Porcentagem de DWU|Porcentagem|Máximo|Porcentagem de DWU. Aplica-se somente a data warehouses.|Sem dimensões|
|dwu_limit|Yes|Limite de DWU|Contagem|Máximo|Limite de DWU. Aplica-se somente a data warehouses.|Sem dimensões|
|dwu_used|Yes|DWU usado|Contagem|Máximo|DWU usado. Aplica-se somente a data warehouses.|Sem dimensões|
|full_backup_size_bytes|Yes|Tamanho de armazenamento de backup completo|Bytes|Máximo|Tamanho de armazenamento de backup completo cumulativo. Aplica-se a bancos de dados baseados em vCore. Não aplicável a bancos de dados de hiperescala.|Sem dimensões|
|local_tempdb_usage_percent|Yes|Porcentagem de local de tempdb|Porcentagem|Média|Porcentagem de tempdb local. Aplica-se somente a data warehouses.|Sem dimensões|
|log_backup_size_bytes|Yes|Tamanho do armazenamento de backup de log|Bytes|Máximo|Tamanho de armazenamento de backup de log cumulativo. Aplica-se a bancos de dados com base em vCore e em hiperescala.|Sem dimensões|
|log_write_percent|Yes|Porcentagem de E/S de log|Porcentagem|Média|Percentual de e/s de log. Não aplicável a data warehouses.|Sem dimensões|
|memory_usage_percent|Yes|Percentual de memória|Porcentagem|Máximo|Porcentagem de memória. Aplica-se somente a data warehouses.|Sem dimensões|
|physical_data_read_percent|Yes|Porcentagem de E/S de dados|Porcentagem|Média|Porcentagem de E/S de dados|Sem dimensões|
|queued_queries|Yes|Consultas em fila|Contagem|Total|Consultas em fila em todos os grupos de carga de trabalho. Aplica-se somente a data warehouses.|Sem dimensões|
|sessions_percent|Yes|Porcentagem de sessões|Porcentagem|Média|Porcentagem de sessões. Não aplicável a data warehouses.|Sem dimensões|
|snapshot_backup_size_bytes|Yes|Tamanho do armazenamento de backup de instantâneo|Bytes|Máximo|Tamanho de armazenamento de backup de instantâneo cumulativo. Aplica-se a bancos de dados de hiperescala.|Sem dimensões|
|sqlserver_process_core_percent|Yes|Porcentagem de núcleo do processo de SQL Server|Porcentagem|Máximo|Uso da CPU como uma porcentagem do processo do BD SQL. Não aplicável a data warehouses.|Sem dimensões|
|sqlserver_process_memory_percent|Yes|Porcentagem de memória de processo SQL Server|Porcentagem|Máximo|Uso de memória como uma porcentagem do processo do BD SQL. Não aplicável a data warehouses.|Sem dimensões|
|armazenamento|Yes|Espaço de dados usado|Bytes|Máximo|Espaço de dados usado. Não aplicável a data warehouses.|Sem dimensões|
|storage_percent|Yes|Porcentagem de espaço de dados usado|Porcentagem|Máximo|Porcentagem de espaço de dados usada. Não aplicável a data warehouses ou a bancos de dados de hiperescala.|Sem dimensões|
|tempdb_data_size|Yes|Quilobytes do Tamanho do Arquivo de Dados Tempdb|Contagem|Máximo|Espaço usado em arquivos de dados tempdb em kilobytes. Não aplicável a data warehouses.|Sem dimensões|
|tempdb_log_size|Yes|Quilobytes do Tamanho do Arquivo de Log Tempdb|Contagem|Máximo|Espaço usado no arquivo de log de transações de tempdb em kilobytes. Não aplicável a data warehouses.|Sem dimensões|
|tempdb_log_used_percent|Yes|Porcentagem de Uso do Log Tempdb|Porcentagem|Máximo|Porcentagem de espaço usado no arquivo de log de transações tempdb. Não aplicável a data warehouses.|Sem dimensões|
|wlg_active_queries|Yes|Consultas ativas do grupo de carga de trabalho|Contagem|Total|Consultas ativas dentro do grupo de cargas de trabalho. Aplica-se somente a data warehouses.|WorkloadGroupName, IsUserDefined|
|wlg_active_queries_timeouts|Yes|Tempos limite de consulta do grupo de carga de trabalho|Contagem|Total|Consultas que atingiram o tempo limite para o grupo de carga de trabalho. Aplica-se somente a data warehouses.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_system_percent|Yes|Percentual de alocação do grupo de carga de trabalho por sistema|Porcentagem|Máximo|Porcentagem alocada de recursos em relação a todo o sistema por grupo de carga de trabalho. Aplica-se somente a data warehouses.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_wlg_effective_cap_percent|Yes|Porcentagem de alocação do grupo de cargas de trabalho por limite|Porcentagem|Máximo|Porcentagem alocada de recursos em relação aos recursos de Cap especificados por grupo de carga de trabalho. Aplica-se somente a data warehouses.|WorkloadGroupName, IsUserDefined|
|wlg_effective_cap_resource_percent|Yes|Percentual de limite efetivo de recursos|Porcentagem|Máximo|Um limite rígido na porcentagem de recursos permitidos para o grupo de cargas de trabalho, levando em conta o percentual de recursos mínimo efetivo alocado para outros grupos de carga de trabalho. Aplica-se somente a data warehouses.|WorkloadGroupName, IsUserDefined|
|wlg_effective_min_resource_percent|Yes|Percentual mínimo efetivo de recursos|Porcentagem|Máximo|Percentual mínimo de recursos reservados e isolados para o grupo de carga de trabalho, levando em conta o nível de serviço mínimo. Aplica-se somente a data warehouses.|WorkloadGroupName, IsUserDefined|
|wlg_queued_queries|Yes|Consultas na fila do grupo de carga de trabalho|Contagem|Total|Consultas em fila no grupo de cargas de trabalho. Aplica-se somente a data warehouses.|WorkloadGroupName, IsUserDefined|
|workers_percent|Yes|Porcentagem de funcionários|Porcentagem|Média|Porcentagem de trabalhadores. Não aplicável a data warehouses.|Sem dimensões|
|xtp_storage_percent|Yes|Percentual de armazenamento do OLTP na memória|Porcentagem|Média|Porcentagem de armazenamento de OLTP In-Memory. Não aplicável a data warehouses.|Sem dimensões|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|allocated_data_storage|Yes|Espaço alocado de dados|Bytes|Média|Espaço alocado de dados|Sem dimensões|
|allocated_data_storage_percent|Yes|Porcentagem do espaço de dados alocado|Porcentagem|Máximo|Porcentagem do espaço de dados alocado|Sem dimensões|
|cpu_limit|Yes|Limite da CPU|Contagem|Média|Limite de CPU. Aplica-se a pools elásticos baseados em vCore.|Sem dimensões|
|cpu_percent|Yes|Percentual de CPU|Porcentagem|Média|Percentual de CPU|Sem dimensões|
|cpu_used|Yes|CPU usada|Contagem|Média|CPU usada. Aplica-se a pools elásticos baseados em vCore.|Sem dimensões|
|database_allocated_data_storage|No|Espaço alocado de dados|Bytes|Média|Espaço alocado de dados|DatabaseResourceId|
|database_cpu_limit|No|Limite da CPU|Contagem|Média|Limite da CPU|DatabaseResourceId|
|database_cpu_percent|No|Percentual de CPU|Porcentagem|Média|Percentual de CPU|DatabaseResourceId|
|database_cpu_used|No|CPU usada|Contagem|Média|CPU usada|DatabaseResourceId|
|database_dtu_consumption_percent|No|Porcentagem de DTU|Porcentagem|Média|Porcentagem de DTU|DatabaseResourceId|
|database_eDTU_used|No|eDTU usado|Contagem|Média|eDTU usado|DatabaseResourceId|
|database_log_write_percent|No|Porcentagem de E/S de log|Porcentagem|Média|Porcentagem de E/S de log|DatabaseResourceId|
|database_physical_data_read_percent|No|Porcentagem de E/S de dados|Porcentagem|Média|Porcentagem de E/S de dados|DatabaseResourceId|
|database_sessions_percent|No|Porcentagem de sessões|Porcentagem|Média|Porcentagem de sessões|DatabaseResourceId|
|database_storage_used|No|Espaço de dados usado|Bytes|Média|Espaço de dados usado|DatabaseResourceId|
|database_workers_percent|No|Porcentagem de funcionários|Porcentagem|Média|Porcentagem de funcionários|DatabaseResourceId|
|dtu_consumption_percent|Yes|Porcentagem de DTU|Porcentagem|Média|Porcentagem de DTU. Aplica-se a pools elásticos baseados em DTU.|Sem dimensões|
|eDTU_limit|Yes|Limite de eDTU|Contagem|Média|limite de eDTU. Aplica-se a pools elásticos baseados em DTU.|Sem dimensões|
|eDTU_used|Yes|eDTU usado|Contagem|Média|eDTU usado. Aplica-se a pools elásticos baseados em DTU.|Sem dimensões|
|log_write_percent|Yes|Porcentagem de E/S de log|Porcentagem|Média|Porcentagem de E/S de log|Sem dimensões|
|physical_data_read_percent|Yes|Porcentagem de E/S de dados|Porcentagem|Média|Porcentagem de E/S de dados|Sem dimensões|
|sessions_percent|Yes|Porcentagem de sessões|Porcentagem|Média|Porcentagem de sessões|Sem dimensões|
|sqlserver_process_core_percent|Yes|Porcentagem de núcleo do processo de SQL Server|Porcentagem|Máximo|Uso da CPU como uma porcentagem do processo do BD SQL. Aplica-se a pools elásticos.|Sem dimensões|
|sqlserver_process_memory_percent|Yes|Porcentagem de memória de processo SQL Server|Porcentagem|Máximo|Uso de memória como uma porcentagem do processo do BD SQL. Aplica-se a pools elásticos.|Sem dimensões|
|storage_limit|Yes|Tamanho máximo dos dados|Bytes|Média|Tamanho máximo dos dados|Sem dimensões|
|storage_percent|Yes|Porcentagem de espaço de dados usado|Porcentagem|Média|Porcentagem de espaço de dados usado|Sem dimensões|
|storage_used|Yes|Espaço de dados usado|Bytes|Média|Espaço de dados usado|Sem dimensões|
|tempdb_data_size|Yes|Quilobytes do Tamanho do Arquivo de Dados Tempdb|Contagem|Máximo|Espaço usado em arquivos de dados tempdb em kilobytes.|Sem dimensões|
|tempdb_log_size|Yes|Quilobytes do Tamanho do Arquivo de Log Tempdb|Contagem|Máximo|Espaço usado no arquivo de log de transações de tempdb em kilobytes.|Sem dimensões|
|tempdb_log_used_percent|Yes|Porcentagem de Uso do Log Tempdb|Porcentagem|Máximo|Porcentagem de espaço usado no arquivo de log de transações do tempdb|Sem dimensões|
|workers_percent|Yes|Porcentagem de funcionários|Porcentagem|Média|Porcentagem de funcionários|Sem dimensões|
|xtp_storage_percent|Yes|Percentual de armazenamento do OLTP na memória|Porcentagem|Média|Percentual de armazenamento do OLTP na memória|Sem dimensões|


## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Yes|Disponibilidade|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|GeoType, ApiName, Authentication|
|Saída|Yes|Saída|Bytes|Total|A quantidade de dados de saída. Esse número inclui saída para cliente externo do armazenamento do Azure, bem como saída no Azure. Como resultado, esse número não reflete a saída faturável.|GeoType, ApiName, Authentication|
|Entrada|Yes|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Yes|Latência de E2E com Sucesso|Milissegundos|Média|A latência média de ponta a ponta das solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Yes|Latência de Servidor Com Sucesso|Milissegundos|Média|O tempo médio usado para processar uma solicitação bem-sucedida pelo Armazenamento do Azure. Esse valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType, ApiName, Authentication|
|Transactions|Sim|Transactions|Contagem|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|ResponseType, GeoType, ApiName, Authentication|
|UsedCapacity|No|Capacidade utilizada|Bytes|Média|A quantidade de armazenamento utilizada pela conta de armazenamento. Para contas de armazenamento Standard, é a soma da capacidade usada por blob, tabela, arquivo e fila. Para contas de armazenamento Premium e contas de armazenamento de BLOBs, ele é o mesmo que capacidade ou filecapacity.|Sem dimensões|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Yes|Disponibilidade|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|GeoType, ApiName, Authentication|
|BlobCapacity|No|Capacidade do Blob|Bytes|Média|A quantidade de armazenamento utilizada pelo serviço Blob da conta de armazenamento em bytes.|BlobType, camada|
|BlobCount|No|Contagem de Blobs|Contagem|Média|O número de objetos blob armazenados na conta de armazenamento.|BlobType, camada|
|BlobProvisionedSize|No|Tamanho provisionado do blob|Bytes|Média|A quantidade de armazenamento provisionada no serviço BLOB da conta de armazenamento, em bytes.|BlobType, camada|
|ContainerCount|Yes|Contagem de Contêineres de Blobs|Contagem|Média|O número de contêineres na conta de armazenamento.|Sem dimensões|
|Saída|Yes|Saída|Bytes|Total|A quantidade de dados de saída. Esse número inclui saída para cliente externo do armazenamento do Azure, bem como saída no Azure. Como resultado, esse número não reflete a saída faturável.|GeoType, ApiName, Authentication|
|IndexCapacity|No|Capacidade de Índice|Bytes|Média|A quantidade de armazenamento usada pelo Azure Data Lake Storage Gen2 índice hierárquico.|Sem dimensões|
|Entrada|Yes|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Yes|Latência de E2E com Sucesso|Milissegundos|Média|A latência média de ponta a ponta das solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Yes|Latência de Servidor Com Sucesso|Milissegundos|Média|O tempo médio usado para processar uma solicitação bem-sucedida pelo Armazenamento do Azure. Esse valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType, ApiName, Authentication|
|Transactions|Sim|Transactions|Contagem|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Yes|Disponibilidade|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|Geotipo, ApiName, autenticação, FileShare|
|Saída|Yes|Saída|Bytes|Total|A quantidade de dados de saída. Esse número inclui saída para cliente externo do armazenamento do Azure, bem como saída no Azure. Como resultado, esse número não reflete a saída faturável.|Geotipo, ApiName, autenticação, FileShare|
|FileCapacity|No|Capacidade do Arquivo|Bytes|Média|A quantidade de armazenamento de Arquivos usada pela conta de armazenamento.|FileShare|
|FileCount|No|Contagem de Arquivos|Contagem|Média|O número de arquivos na conta de armazenamento.|FileShare|
|FileShareCapacityQuota|No|Cota de capacidade de compartilhamento de arquivos|Bytes|Média|O limite superior da quantidade de armazenamento que pode ser usada pelo serviço de arquivos do Azure em bytes.|FileShare|
|FileShareCount|No|Contagem de Compartilhamentos de Arquivos|Contagem|Média|O número de compartilhamentos de arquivos na conta de armazenamento.|Sem dimensões|
|FileShareProvisionedIOPS|No|IOPS provisionado de compartilhamento de arquivos|Bytes|Média|O número da linha de base de IOPS provisionados para o compartilhamento de arquivos Premium na conta de armazenamento de arquivos premium. Esse número é calculado com base no tamanho provisionado (cota) da capacidade de compartilhamento.|FileShare|
|FileShareSnapshotCount|No|Contagem de Instantâneos do Compartilhamento de Arquivo|Contagem|Média|O número de instantâneos presentes no serviço compartilhamento de arquivos da conta de armazenamento.|FileShare|
|FileShareSnapshotSize|No|Tamanho do Instantâneo do Compartilhamento de Arquivo|Bytes|Média|A quantidade de armazenamento usada pelos instantâneos no serviço de arquivo da conta de armazenamento, em bytes.|FileShare|
|Entrada|Yes|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|Geotipo, ApiName, autenticação, FileShare|
|SuccessE2ELatency|Yes|Latência de E2E com Sucesso|Milissegundos|Média|A latência média de ponta a ponta das solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|Geotipo, ApiName, autenticação, FileShare|
|SuccessServerLatency|Yes|Latência de Servidor Com Sucesso|Milissegundos|Média|O tempo médio usado para processar uma solicitação bem-sucedida pelo Armazenamento do Azure. Esse valor não inclui a latência de rede especificada em SuccessE2ELatency.|Geotipo, ApiName, autenticação, FileShare|
|Transactions|Sim|Transactions|Contagem|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|ResponseType, GeoType, ApiName, autenticação, FileShare|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Yes|Disponibilidade|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|GeoType, ApiName, Authentication|
|Saída|Yes|Saída|Bytes|Total|A quantidade de dados de saída. Esse número inclui saída para cliente externo do armazenamento do Azure, bem como saída no Azure. Como resultado, esse número não reflete a saída faturável.|GeoType, ApiName, Authentication|
|Entrada|Yes|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|GeoType, ApiName, Authentication|
|QueueCapacity|Yes|Capacidade da Fila|Bytes|Média|A quantidade de armazenamento de fila usada pela conta de armazenamento.|Sem dimensões|
|QueueCount|Yes|Contagem de Filas|Contagem|Média|O número de filas em uma conta de armazenamento.|Sem dimensões|
|QueueMessageCount|Yes|Contagem de Mensagens da Fila|Contagem|Média|O número de mensagens da fila não expiradas na conta de armazenamento.|Sem dimensões|
|SuccessE2ELatency|Yes|Latência de E2E com Sucesso|Milissegundos|Média|A latência média de ponta a ponta das solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Yes|Latência de Servidor Com Sucesso|Milissegundos|Média|O tempo médio usado para processar uma solicitação bem-sucedida pelo Armazenamento do Azure. Esse valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType, ApiName, Authentication|
|Transactions|Sim|Transactions|Contagem|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Disponibilidade|Yes|Disponibilidade|Porcentagem|Média|O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor de TotalBillableRequests e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada.|GeoType, ApiName, Authentication|
|Saída|Yes|Saída|Bytes|Total|A quantidade de dados de saída. Esse número inclui saída para cliente externo do armazenamento do Azure, bem como saída no Azure. Como resultado, esse número não reflete a saída faturável.|GeoType, ApiName, Authentication|
|Entrada|Yes|Entrada|Bytes|Total|A quantidade de dados de entrada, em bytes. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Yes|Latência de E2E com Sucesso|Milissegundos|Média|A latência média de ponta a ponta das solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada, em milissegundos. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Yes|Latência de Servidor Com Sucesso|Milissegundos|Média|O tempo médio usado para processar uma solicitação bem-sucedida pelo Armazenamento do Azure. Esse valor não inclui a latência de rede especificada em SuccessE2ELatency.|GeoType, ApiName, Authentication|
|TableCapacity|Yes|Capacidade da Tabela|Bytes|Média|A quantidade de armazenamento de Tabelas usada pela conta de armazenamento.|Sem dimensões|
|TableCount|Yes|Contagem de Tabelas|Contagem|Média|O número de tabelas em uma conta de armazenamento.|Sem dimensões|
|TableEntityCount|Yes|Contagem de Entidades de Tabela|Contagem|Média|O número de entidades de tabela na conta de armazenamento.|Sem dimensões|
|Transactions|Sim|Transactions|Contagem|Total|O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. Use a dimensão ResponseType para obter o número de tipo diferente de resposta.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftstoragecachecaches"></a>Microsoft.StorageCache/caches

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ClientIOPS|Yes|Total de IOPS do Cliente|Contagem|Média|A taxa de operações de arquivo do cliente processadas pelo cache.|Sem dimensões|
|ClientLatency|Yes|Latência Média do Cliente|Milissegundos|Média|Latência média de operações de arquivo do cliente para o cache.|Sem dimensões|
|ClientLockIOPS|Yes|IOPS de Bloqueio do Cliente|CountPerSecond|Média|Operações de bloqueio de arquivo do cliente por segundo.|Sem dimensões|
|ClientMetadataReadIOPS|Yes|IOPS de Leitura dos Metadados do Cliente|CountPerSecond|Média|A taxa de operações de arquivo do cliente enviadas ao cache, excluindo leituras de dados, que não modificam o estado persistente.|Sem dimensões|
|ClientMetadataWriteIOPS|Yes|IOPS de Gravação dos Metadados do Cliente|CountPerSecond|Média|A taxa de operações de arquivo do cliente enviadas ao cache, excluindo gravações de dados, que modificam o estado persistente.|Sem dimensões|
|ClientReadIOPS|Yes|IOPS de Leitura do Cliente|CountPerSecond|Média|Operações de leitura de cliente por segundo.|Sem dimensões|
|ClientReadThroughput|Yes|Taxa de Transferência Média de Leitura do Cache|BytesPerSecond|Média|Taxa de transferência de dados de leitura de cliente.|Sem dimensões|
|ClientWriteIOPS|Yes|IOPS de Gravação do Cliente|CountPerSecond|Média|Operações de gravação de cliente por segundo.|Sem dimensões|
|ClientWriteThroughput|Yes|Taxa de Transferência Média de Gravação no Cache|BytesPerSecond|Média|Taxa de transferência de dados de gravação de cliente.|Sem dimensões|
|StorageTargetAsyncWriteThroughput|Yes|Taxa de Transferência de Gravação Assíncrona do StorageTarget|BytesPerSecond|Média|A taxa em que o cache grava dados de forma assíncrona em um determinado StorageTarget. Essas são gravações oportunistas que não fazem com que os clientes bloqueiem.|StorageTarget|
|StorageTargetFillThroughput|Yes|Taxa de Transferência de Preenchimento do StorageTarget|BytesPerSecond|Média|A taxa em que o cache lê os dados do StorageTarget para lidar com um erro de cache.|StorageTarget|
|StorageTargetHealth|Yes|Integridade do Destino de Armazenamento|Contagem|Média|Resultados boolianos de teste de conectividade entre o cache e os destinos de armazenamento.|Sem dimensões|
|StorageTargetIOPS|Yes|Total de IOPS do StorageTarget|Contagem|Média|A taxa de todas as operações de arquivo que o cache envia para um determinado StorageTarget.|StorageTarget|
|StorageTargetLatency|Yes|Latência do StorageTarget|Milissegundos|Média|A latência média de ida e volta de todas as operações de arquivo que o cache envia para um partricular StorageTarget.|StorageTarget|
|StorageTargetMetadataReadIOPS|Yes|IOPS de Leitura de Metadados do StorageTarget|CountPerSecond|Média|A taxa de operações de arquivo que não modificam o estado persistente e excluindo a operação de leitura, que o cache envia para um determinado StorageTarget.|StorageTarget|
|StorageTargetMetadataWriteIOPS|Yes|IOPS de Gravação de Metadados do StorageTarget|CountPerSecond|Média|A taxa de operações de arquivo que modificam o estado persistente e excluindo a operação de gravação, que o cache envia para um determinado StorageTarget.|StorageTarget|
|StorageTargetReadAheadThroughput|Yes|Taxa de Transferência de Leitura Antecipada do StorageTarget|BytesPerSecond|Média|A taxa em que o cache lê de forma oportunista os dados do StorageTarget.|StorageTarget|
|StorageTargetReadIOPS|Yes|IOPS de Leitura do StorageTarget|CountPerSecond|Média|A taxa de operações de leitura de arquivo que o cache envia para um determinado StorageTarget.|StorageTarget|
|StorageTargetSyncWriteThroughput|Yes|Taxa de Transferência de Gravação Síncrona do StorageTarget|BytesPerSecond|Média|A taxa em que o cache grava dados de forma síncrona em um determinado StorageTarget. Essas são gravações que fazem com que os clientes bloqueiem.|StorageTarget|
|StorageTargetTotalReadThroughput|Yes|Taxa de Transferência de Leitura Total do StorageTarget|BytesPerSecond|Média|A taxa total em que o cache lê dados de um determinado StorageTarget.|StorageTarget|
|StorageTargetTotalWriteThroughput|Yes|Taxa de Transferência de Gravação Total do StorageTarget|BytesPerSecond|Média|A taxa total em que o cache grava dados em um determinado StorageTarget.|StorageTarget|
|StorageTargetWriteIOPS|Yes|IOPS de Gravação do StorageTarget|Contagem|Média|A taxa das operações de gravação de arquivo que o cache envia para um determinado StorageTarget.|StorageTarget|
|Tempo de atividade|Yes|Tempo de atividade|Contagem|Média|Resultados boolianos de teste de conectividade entre o cache e o sistema de monitoramento.|Sem dimensões|


## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ServerSyncSessionResult|Yes|Resultado da Sessão de Sincronização|Contagem|Média|Métrica que registra um valor de 1 cada vez que o ponto de extremidade do servidor conclui com êxito uma sessão de sincronização com o ponto de extremidade de nuvem|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Yes|Bytes sincronizados|Bytes|Total|Tamanho total do arquivo transferido para sessões de sincronização|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncRecalledNetworkBytesByApplication|Yes|Tamanho de recall da camada de nuvem por aplicativo|Bytes|Total|Tamanho dos dados recuperados pelo aplicativo|SyncGroupName, servername, ApplicationName|
|StorageSyncRecalledTotalNetworkBytes|Yes|Tamanho de recall da camada de nuvem|Bytes|Total|Tamanho dos dados em recall|SyncGroupName, servername|
|StorageSyncRecallIOTotalSizeBytes|Yes|Recall da camada de nuvem|Bytes|Total|Tamanho total dos dados recuperados pelo servidor|ServerName|
|StorageSyncRecallThroughputBytesPerSecond|Yes|Taxa de transferência de recall da camada de nuvem|BytesPerSecond|Média|Tamanho da taxa de transferência de recall de dados|SyncGroupName, servername|
|StorageSyncServerHeartbeat|Yes|Status Online do Servidor|Contagem|Máximo|Métrica que registra um valor de 1 cada vez que o servidor resigtered registra com êxito uma pulsação com o ponto de extremidade de nuvem|ServerName|
|StorageSyncSyncSessionAppliedFilesCount|Yes|Arquivos Sincronizados|Contagem|Total|Contagem de arquivos sincronizados|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Yes|Arquivos não sincronizando|Contagem|Total|Falha na sincronização da contagem de arquivos|SyncGroupName, ServerEndpointName, SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>microsoft.storagesync/storageSyncServices/registeredServers

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ServerHeartbeat|Yes|Status Online do Servidor|Contagem|Máximo|Métrica que registra um valor de 1 cada vez que o servidor resigtered registra com êxito uma pulsação com o ponto de extremidade de nuvem|ServerResourceId, servername|
|ServerRecallIOTotalSizeBytes|Yes|Recall da camada de nuvem|Bytes|Total|Tamanho total dos dados recuperados pelo servidor|ServerResourceId, servername|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>microsoft.storagesync/storageSyncServices/syncGroups

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|SyncGroupBatchTransferredFileBytes|Yes|Bytes sincronizados|Bytes|Total|Tamanho total do arquivo transferido para sessões de sincronização|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupSyncSessionAppliedFilesCount|Yes|Arquivos Sincronizados|Contagem|Total|Contagem de arquivos sincronizados|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|Yes|Arquivos não sincronizando|Contagem|Total|Falha na sincronização da contagem de arquivos|SyncGroupName, ServerEndpointName, SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ServerEndpointBatchTransferredFileBytes|Yes|Bytes sincronizados|Bytes|Total|Tamanho total do arquivo transferido para sessões de sincronização|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionAppliedFilesCount|Yes|Arquivos Sincronizados|Contagem|Total|Contagem de arquivos sincronizados|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|Yes|Arquivos não sincronizando|Contagem|Total|Falha na sincronização da contagem de arquivos|ServerEndpointName, SyncDirection|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|AMLCalloutFailedRequests|Yes|Solicitações de função com falha|Contagem|Total|Solicitações de função com falha|LogicalName, PartitionID|
|AMLCalloutInputEvents|Yes|Eventos de função|Contagem|Total|Eventos de função|LogicalName, PartitionID|
|AMLCalloutRequests|Yes|Solicitações de função|Contagem|Total|Solicitações de função|LogicalName, PartitionID|
|ConversionErrors|Yes|Erros de conversão de dados|Contagem|Total|Erros de conversão de dados|LogicalName, PartitionID|
|DeserializationError|Yes|Erros de desserialização de entrada|Contagem|Total|Erros de desserialização de entrada|LogicalName, PartitionID|
|DroppedOrAdjustedEvents|Yes|Eventos fora de ordem|Contagem|Total|Eventos fora de ordem|LogicalName, PartitionID|
|EarlyInputEvents|Yes|Eventos de Entrada Antecipados|Contagem|Total|Eventos de Entrada Antecipados|LogicalName, PartitionID|
|Errors|Yes|Erros de runtime|Contagem|Total|Erros de runtime|LogicalName, PartitionID|
|InputEventBytes|Yes|Bytes de evento de entrada|Bytes|Total|Bytes de evento de entrada|LogicalName, PartitionID|
|InputEvents|Yes|Eventos de entrada|Contagem|Total|Eventos de entrada|LogicalName, PartitionID|
|InputEventsSourcesBacklogged|Yes|Eventos de Entrada Acumulados|Contagem|Máximo|Eventos de Entrada Acumulados|LogicalName, PartitionID|
|InputEventsSourcesPerSecond|Yes|Fontes de Entrada Recebidas|Contagem|Total|Fontes de Entrada Recebidas|LogicalName, PartitionID|
|LateInputEvents|Yes|Eventos de entrada atrasados|Contagem|Total|Eventos de entrada atrasados|LogicalName, PartitionID|
|OutputEvents|Yes|Eventos de saída|Contagem|Total|Eventos de saída|LogicalName, PartitionID|
|OutputWatermarkDelaySeconds|Yes|Atraso de Marca-d'água|Segundos|Máximo|Atraso de Marca-d'água|LogicalName, PartitionID|
|ResourceUtilization|Yes|% de utilização do SU|Porcentagem|Máximo|% de utilização do SU|LogicalName, PartitionID|


## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse/workspaces

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|BuiltinSqlPoolDataProcessedBytes|No|Dados processados (bytes)|Bytes|Total|Quantidade de dados processados por consultas|Sem dimensões|
|BuiltinSqlPoolLoginAttempts|No|Tentativas de logon|Contagem|Total|Contagem de tentativas de logon que êxito na ou com falha|Result|
|BuiltinSqlPoolRequestsEnded|No|Solicitações encerradas|Contagem|Total|Contagem de solicitações que tiveram êxito, falharam ou foram canceladas|Result|
|IntegrationActivityRunsEnded|No|Execuções de atividades encerradas|Contagem|Total|Contagem de atividades de integração que tiveram êxito, falharam ou foram canceladas|Resultado, FailureType, atividade, ActivityType, pipeline|
|IntegrationPipelineRunsEnded|No|Execuções de pipeline finalizadas|Contagem|Total|Contagem de execuções de pipeline de integração que tiveram êxito, falharam ou foram canceladas|Resultado, FailureType, pipeline|
|IntegrationTriggerRunsEnded|No|Execuções de gatilho finalizadas|Contagem|Total|Contagem de gatilhos de integração que foram bem-sucedidos, com falha ou foram cancelados|Resultado, FailureType, gatilho|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft. Synapse/Workspaces/bigDataPools

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|BigDataPoolAllocatedCores|No|vCores alocada|Contagem|Máximo|VCores alocada para um pool de Apache Spark|Emissor da|
|BigDataPoolAllocatedMemory|No|Memória alocada (GB)|Contagem|Máximo|Memória alocada para o pool do Spark Apach (GB)|Emissor da|
|BigDataPoolApplicationsActive|No|Aplicativos do Active Apache Spark|Contagem|Máximo|Total de aplicativos do pool de Apache Spark ativos|JobState|
|BigDataPoolApplicationsEnded|No|Apache Spark aplicativos encerrados|Contagem|Total|Contagem de aplicativos do pool de Apache Spark encerrada|JobType, JobResult|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft. Synapse/espaços de trabalho/sqlpools

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ActiveQueries|No|Consultas ativas|Contagem|Total|As consultas ativas. Usar essa métrica não filtrada e a divisão exibe todas as consultas ativas em execução no sistema|IsUserDefined|
|AdaptiveCacheHitPercent|No|Percentual de impacto de cache adaptável|Porcentagem|Máximo|Mede o quão bem as cargas de trabalho estão utilizando o cache adaptável. Use essa métrica com a métrica percentual de impacto de cache para determinar se deve ser dimensionada para capacidade adicional ou executar novamente as cargas de trabalho para hidratar o cache|Sem dimensões|
|AdaptiveCacheUsedPercent|No|Percentual de cache adaptável usado|Porcentagem|Máximo|Mede o quão bem as cargas de trabalho estão utilizando o cache adaptável. Use essa métrica com a métrica percentual usada do cache para determinar se deve ser dimensionada para capacidade adicional ou executar novamente as cargas de trabalho para hidratar o cache|Sem dimensões|
|conexões|Yes|conexões|Contagem|Total|Contagem de logons totais para o pool do SQL|Result|
|ConnectionsBlockedByFirewall|No|Conexões bloqueadas pelo firewall|Contagem|Total|Contagem de conexões bloqueadas por regras de firewall. Reveja as políticas de controle de acesso para seu pool SQL e monitore essas conexões se a contagem for alta|Sem dimensões|
|CPUPercent|No|Percentual de utilização da CPU|Porcentagem|Máximo|Utilização da CPU em todos os nós no pool do SQL|Sem dimensões|
|DWULimit|No|Limite de DWU|Contagem|Máximo|Objetivo de nível de serviço do pool do SQL|Sem dimensões|
|DWUUsed|No|DWU usado|Contagem|Máximo|Representa uma representação de uso de alto nível no pool do SQL. Medido por limite de DWU * DWU percentual|Sem dimensões|
|DWUUsedPercent|No|Percentual de DWU usado|Porcentagem|Máximo|Representa uma representação de uso de alto nível no pool do SQL. Medido com o máximo entre o percentual de CPU e a porcentagem de e/s de dados|Sem dimensões|
|LocalTempDBUsedPercent|No|Percentual de uso do tempdb local|Porcentagem|Máximo|Utilização de tempdb local em todos os nós de computação-os valores são emitidos a cada cinco minutos|Sem dimensões|
|MemoryUsedPercent|No|Porcentagem de utilização da memória|Porcentagem|Máximo|Utilização de memória em todos os nós no pool de SQL|Sem dimensões|
|QueuedQueries|No|Consultas em fila|Contagem|Total|Contagem cumulativa de solicitações enfileiradas após o limite de simultaneidade máxima ter sido atingido|IsUserDefined|
|WLGActiveQueries|No|Consultas ativas do grupo de carga de trabalho|Contagem|Total|As consultas ativas no grupo de cargas de trabalho. Usar essa métrica não filtrada e a divisão exibe todas as consultas ativas em execução no sistema|IsUserDefined, de carga de trabalho|
|WLGActiveQueriesTimeouts|No|Tempos limite de consulta do grupo de carga de trabalho|Contagem|Total|Consultas para o grupo de cargas de trabalho que atingiram o tempo limite. Os tempos limite de consulta relatados por essa métrica são apenas uma vez que a consulta começou a ser executada (ela não inclui o tempo de espera devido a esperas de bloqueio ou de recursos)|IsUserDefined, de carga de trabalho|
|WLGAllocationByEffectiveCapResourcePercent|No|Alocação do grupo de carga de trabalho por porcentagem máxima de recursos|Porcentagem|Máximo|Exibe a porcentagem de alocação de recursos em relação à porcentagem de recursos de limite efetivo por grupo de carga de trabalho. Essa métrica fornece a utilização efetiva do grupo de cargas de trabalho|IsUserDefined, de carga de trabalho|
|WLGAllocationBySystemPercent|No|Percentual de alocação do grupo de carga de trabalho por sistema|Porcentagem|Máximo|A alocação percentual de recursos em relação a todo o sistema|IsUserDefined, de carga de trabalho|
|WLGEffectiveCapResourcePercent|No|Percentual de limite efetivo de recursos|Porcentagem|Máximo|A porcentagem do recurso Cap efetivo para o grupo de carga de trabalho. Se houver outros grupos de cargas de trabalho com min_percentage_resource > 0, o effective_cap_percentage_resource será reduzido proporcionalmente|IsUserDefined, de carga de trabalho|
|WLGEffectiveMinResourcePercent|No|Percentual mínimo efetivo de recursos|Porcentagem|Máximo|A configuração de porcentagem mínima de recursos efetiva permitida Considerando o nível de serviço e as configurações do grupo de carga de trabalho. Os min_percentage_resource efetivos podem ser ajustados em níveis mais baixos de serviço|IsUserDefined, de carga de trabalho|
|WLGQueuedQueries|No|Consultas na fila do grupo de carga de trabalho|Contagem|Total|Contagem cumulativa de solicitações enfileiradas após o limite de simultaneidade máxima ter sido atingido|IsUserDefined, de carga de trabalho|


## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|Yes|Bytes de entrada recebidos|Bytes|Total|Contagem de bytes lidos de todas as fontes de evento|Sem dimensões|
|IngressReceivedInvalidMessages|Yes|Mensagens de entrada inválidas recebidas|Contagem|Total|Contagem de mensagens inválidas lidas de todos os Hubs de Eventos ou origens de evento do Hub IoT|Sem dimensões|
|IngressReceivedMessages|Yes|Mensagens de entrada recebidas|Contagem|Total|Contagem de mensagens lidas de todos os Hubs de Eventos ou origens de evento do Hub IoT|Sem dimensões|
|IngressReceivedMessagesCountLag|Yes|Atraso na contagem das mensagens de entrada recebidas|Contagem|Média|Diferença entre o número de sequência da última mensagem enfileirada na partição de origem do evento e o número de sequência de mensagens sendo processadas na entrada|Sem dimensões|
|IngressReceivedMessagesTimeLag|Yes|Tempo de atraso das mensagens de entrada recebidas|Segundos|Máximo|Diferença entre a hora em que a mensagem é enfileirada na origem do evento e a hora em que ela é processada na entrada|Sem dimensões|
|IngressStoredBytes|Yes|Bytes de entrada armazenados|Bytes|Total|Tamanho total de eventos processados com êxito e disponíveis para consulta|Sem dimensões|
|IngressStoredEvents|Yes|Eventos de entrada armazenados|Contagem|Total|Contagem de eventos nivelados processados com êxito e disponíveis para consulta|Sem dimensões|
|WarmStorageMaxProperties|Yes|Propriedades máximas de armazenamento quente|Contagem|Máximo|Número máximo de propriedades usadas permitidas pelo ambiente para SKU S1/S2 e número máximo de propriedades permitidas pelo armazenamento quente para o SKU PAYG|Sem dimensões|
|WarmStorageUsedProperties|Yes|Propriedades usadas de armazenamento quente |Contagem|Máximo|Número de propriedades usadas pelo ambiente para SKU S1/S2 e número de propriedades usadas pelo armazenamento quente para SKU PAYG|Sem dimensões|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|Yes|Bytes de entrada recebidos|Bytes|Total|Contagem de bytes lidos da fonte de evento|Sem dimensões|
|IngressReceivedInvalidMessages|Yes|Mensagens de entrada inválidas recebidas|Contagem|Total|Contagem de mensagens inválidas lidas na origem do evento|Sem dimensões|
|IngressReceivedMessages|Yes|Mensagens de entrada recebidas|Contagem|Total|Contagem de mensagens lidas na origem do evento|Sem dimensões|
|IngressReceivedMessagesCountLag|Yes|Atraso na contagem das mensagens de entrada recebidas|Contagem|Média|Diferença entre o número de sequência da última mensagem enfileirada na partição de origem do evento e o número de sequência de mensagens sendo processadas na entrada|Sem dimensões|
|IngressReceivedMessagesTimeLag|Yes|Tempo de atraso das mensagens de entrada recebidas|Segundos|Máximo|Diferença entre a hora em que a mensagem é enfileirada na origem do evento e a hora em que ela é processada na entrada|Sem dimensões|
|IngressStoredBytes|Yes|Bytes de entrada armazenados|Bytes|Total|Tamanho total de eventos processados com êxito e disponíveis para consulta|Sem dimensões|
|IngressStoredEvents|Yes|Eventos de entrada armazenados|Contagem|Total|Contagem de eventos nivelados processados com êxito e disponíveis para consulta|Sem dimensões|
|WarmStorageMaxProperties|Yes|Propriedades máximas de armazenamento quente|Contagem|Máximo|Número máximo de propriedades usadas permitidas pelo ambiente para SKU S1/S2 e número máximo de propriedades permitidas pelo armazenamento quente para o SKU PAYG|Sem dimensões|
|WarmStorageUsedProperties|Yes|Propriedades usadas de armazenamento quente |Contagem|Máximo|Número de propriedades usadas pelo ambiente para SKU S1/S2 e número de propriedades usadas pelo armazenamento quente para SKU PAYG|Sem dimensões|


## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|Bytes de leitura de disco|Yes|Bytes de leitura de disco|Bytes|Total|Taxa de transferência total do disco devido a operações de leitura no período de exemplo.|Sem dimensões|
|Operações de leitura de disco/Seg|Yes|Operações de leitura de disco/Seg|CountPerSecond|Média|O número médio de operações de leitura de e/s no período de exemplo anterior. Observe que essas operações podem ser de tamanho variável.|Sem dimensões|
|Bytes de gravação de disco|Yes|Bytes de gravação de disco|Bytes|Total|Taxa de transferência total do disco devido a operações de gravação no período de exemplo.|Sem dimensões|
|Operações de gravação de disco/Seg|Yes|Operações de gravação de disco/Seg|CountPerSecond|Média|O número médio de operações de gravação de e/s no período de exemplo anterior. Observe que essas operações podem ser de tamanho variável.|Sem dimensões|
|DiskReadBytesPerSecond|Yes|Disk Read Bytes/Sec|BytesPerSecond|Média|Taxa de transferência média do disco devido a operações de leitura no período de exemplo.|Sem dimensões|
|DiskReadLatency|Yes|Latência de leitura de disco|Milissegundos|Média|Latência de leitura total. A soma das latências de leitura do dispositivo e do kernel.|Sem dimensões|
|DiskReadOperations|Yes|Operações de Leitura de Disco|Contagem|Total|O número de operações de leitura de e/s no período de exemplo anterior. Observe que essas operações podem ser de tamanho variável.|Sem dimensões|
|DiskWriteBytesPerSecond|Yes|Disk Write Bytes/Sec|BytesPerSecond|Média|Taxa de transferência média do disco devido a operações de gravação no período de exemplo.|Sem dimensões|
|DiskWriteLatency|Yes|Latência de Gravação de disco|Milissegundos|Média|Latência de gravação total. A soma das latências de gravação do kernel e do dispositivo.|Sem dimensões|
|DiskWriteOperations|Yes|Operações de Gravação em Disco|Contagem|Total|O número de operações de gravação de e/s no período de exemplo anterior. Observe que essas operações podem ser de tamanho variável.|Sem dimensões|
|MemoryActive|Yes|Memória Ativa|Bytes|Média|A quantidade de memória usada pela VM na última pequena janela de tempo. Esse é o número "verdadeiro" da quantidade de memória necessária para a VM no momento. A memória adicional não utilizada pode ser trocada ou por balão sem impacto no desempenho do convidado.|Sem dimensões|
|MemoryGranted|Yes|Memória Concedida|Bytes|Média|A quantidade de memória que foi concedida à VM pelo host. A memória não é concedida ao host até que ele seja tocado uma vez e a memória concedida possa ser trocada ou bloqueada se o VMkernel precisar da memória.|Sem dimensões|
|MemoryUsed|Yes|Memória Usada|Bytes|Média|A quantidade de memória da máquina que está sendo usada pela VM.|Sem dimensões|
|Entrada na rede|Yes|Entrada na rede|Bytes|Total|Taxa de transferência total de rede para o tráfego recebido.|Sem dimensões|
|Saída da rede|Yes|Saída da rede|Bytes|Total|Taxa de transferência total de rede para o tráfego transmitido.|Sem dimensões|
|NetworkInBytesPerSecond|Yes|Bytes/s de Entrada de Rede|BytesPerSecond|Média|Taxa de transferência média de rede para o tráfego recebido.|Sem dimensões|
|NetworkOutBytesPerSecond|Yes|Bytes/s de Saída de Rede|BytesPerSecond|Média|Taxa de transferência média de rede para o tráfego transmitido.|Sem dimensões|
|Porcentagem de CPU|Yes|Porcentagem de CPU|Porcentagem|Média|A utilização da CPU. Esse valor é relatado com 100% que representa todos os núcleos de processador no sistema. Por exemplo, uma VM de 2 vias usando 50% de um sistema de quatro núcleos está usando completamente dois núcleos.|Sem dimensões|
|PercentageCpuReady|Yes|Percentual de CPU Pronta|Milissegundos|Total|O tempo de preparação é o tempo gasto aguardando que as CPUs fiquem disponíveis no intervalo de atualização anterior.|Sem dimensões|


## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|ActiveRequests|Yes|Solicitações ativas (preteridas)|Contagem|Total|Solicitações ativas|Instância|
|AverageResponseTime|Yes|Tempo médio de resposta (preterido)|Segundos|Média|O tempo médio necessário para o front-end atender às solicitações, em segundos.|Instância|
|BytesReceived|Yes|Entrada de Dados|Bytes|Total|A largura de banda de entrada média usada em todos os front-ends, na MiB.|Instância|
|BytesSent|Yes|Saída de dados|Bytes|Total|A largura de banda de entrada média usada em todo o front-end, na MiB.|Instância|
|CpuPercentage|Yes|Percentual de CPU|Porcentagem|Média|A média de CPU usada em todas as instâncias de front-end.|Instância|
|DiskQueueLength|Yes|Tamanho da fila do disco|Contagem|Média|O número médio de solicitações de leitura e gravação enfileiradas no armazenamento. Um comprimento de fila de disco alto é uma indicação de um aplicativo que pode estar causando lentidão devido à e/s excessiva de disco.|Instância|
|Http101|Yes|Http 101|Contagem|Total|A contagem de solicitações que resultam em um código de status HTTP 101.|Instância|
|Http2xx|Yes|Http 2xx|Contagem|Total|A contagem de solicitações que resultam em um código de status HTTP = 200, mas < 300.|Instância|
|Http3xx|Yes|Http 3xx|Contagem|Total|A contagem de solicitações que resultam em um código de status HTTP = 300, mas < 400.|Instância|
|Http401|Yes|Http 401|Contagem|Total|A contagem de solicitações que resultam em um código de status HTTP 401.|Instância|
|Http403|Yes|Http 403|Contagem|Total|A contagem de solicitações que resultam em um código de status HTTP 403.|Instância|
|Http404|Yes|Http 404|Contagem|Total|A contagem de solicitações que resultam em um código de status HTTP 404.|Instância|
|Http406|Yes|Http 406|Contagem|Total|A contagem de solicitações que resultam em um código de status HTTP 406.|Instância|
|Http4xx|Yes|Http 4xx|Contagem|Total|A contagem de solicitações que resultam em um código de status HTTP = 400, mas < 500.|Instância|
|Http5xx|Yes|Erros do Servidor Http|Contagem|Total|A contagem de solicitações que resultam em um código de status HTTP = 500, mas < 600.|Instância|
|HttpQueueLength|Yes|Tamanho da Fila de Http|Contagem|Média|O número médio de solicitações HTTP que tiveram de esperar na fila antes de serem atendidas. Um tamanho de fila HTTP alto ou crescente é um sintoma de um plano sob carga pesada.|Instância|
|HttpResponseTime|Yes|Tempo de Resposta|Segundos|Média|O tempo necessário para o front-end atender às solicitações, em segundos.|Instância|
|LargeAppServicePlanInstances|Yes|Funções de trabalho grandes do Plano do Serviço de Aplicativo|Contagem|Média|Funções de trabalho grandes do Plano do Serviço de Aplicativo|Sem dimensões|
|MediumAppServicePlanInstances|Yes|Funções de trabalho médias do Plano do Serviço de Aplicativo|Contagem|Média|Funções de trabalho médias do Plano do Serviço de Aplicativo|Sem dimensões|
|MemoryPercentage|Yes|Porcentagem de Memória|Porcentagem|Média|A média de memória usada em todas as instâncias de front-end.|Instância|
|Requests|Yes|Requests|Contagem|Total|O número total de solicitações, independentemente de seu código de status HTTP resultante.|Instância|
|SmallAppServicePlanInstances|Yes|Funções de trabalho pequenas do Plano do Serviço de Aplicativo|Contagem|Média|Funções de trabalho pequenas do Plano do Serviço de Aplicativo|Sem dimensões|
|TotalFrontEnds|Yes|Total de front-ends|Contagem|Média|Total de front-ends|Sem dimensões|


## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|CpuPercentage|Yes|Percentual de CPU|Porcentagem|Média|A média de CPU usada em todas as instâncias do pool de trabalho.|Instância|
|MemoryPercentage|Yes|Porcentagem de Memória|Porcentagem|Média|A média de memória usada em todas as instâncias do pool de trabalho.|Instância|
|WorkersAvailable|Yes|Funções de trabalho disponíveis|Contagem|Média|Funções de trabalho disponíveis|Sem dimensões|
|WorkersTotal|Yes|Total de funções de trabalho|Contagem|Média|Total de funções de trabalho|Sem dimensões|
|WorkersUsed|Yes|Funções de trabalho usadas|Contagem|Média|Funções de trabalho usadas|Sem dimensões|


## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|BytesReceived|Yes|Entrada de Dados|Bytes|Total|A média de largura de banda de entrada usada em todas as instâncias do plano.|Instância|
|BytesSent|Yes|Saída de dados|Bytes|Total|A média de largura de banda de saída usada em todas as instâncias do plano.|Instância|
|CpuPercentage|Yes|Percentual de CPU|Porcentagem|Média|A média de CPU usada em todas as instâncias do plano.|Instância|
|DiskQueueLength|Yes|Tamanho da fila do disco|Contagem|Média|O número médio de solicitações de leitura e gravação enfileiradas no armazenamento. Um comprimento de fila de disco alto é uma indicação de um aplicativo que pode estar causando lentidão devido à e/s excessiva de disco.|Instância|
|HttpQueueLength|Yes|Tamanho da Fila de Http|Contagem|Média|O número médio de solicitações HTTP que tiveram de esperar na fila antes de serem atendidas. Um tamanho de fila HTTP alto ou crescente é um sintoma de um plano sob carga pesada.|Instância|
|MemoryPercentage|Yes|Porcentagem de Memória|Porcentagem|Média|A média de memória usada em todas as instâncias do plano.|Instância|
|SocketInboundAll|Yes|SocketInboundAll|Contagem|Média|SocketInboundAll|Instância|
|SocketLoopback|Yes|SocketLoopback|Contagem|Média|SocketLoopback|Instância|
|SocketOutboundAll|Yes|SocketOutboundAll|Contagem|Média|SocketOutboundAll|Instância|
|SocketOutboundEstablished|Yes|SocketOutboundEstablished|Contagem|Média|SocketOutboundEstablished|Instância|
|SocketOutboundTimeWait|Yes|SocketOutboundTimeWait|Contagem|Média|SocketOutboundTimeWait|Instância|
|TcpCloseWait|Yes|Espera de Fechamento de TCP|Contagem|Média|Espera de Fechamento de TCP|Instância|
|TcpClosing|Yes|Fechamento de TCP|Contagem|Média|Fechamento de TCP|Instância|
|TcpEstablished|Yes|TCP Estabelecido|Contagem|Média|TCP Estabelecido|Instância|
|TcpFinWait1|Yes|Espera 1 de Fin de TCP|Contagem|Média|Espera 1 de Fin de TCP|Instância|
|TcpFinWait2|Yes|Espera 2 de Fin de TCP|Contagem|Média|Espera 2 de Fin de TCP|Instância|
|TcpLastAck|Yes|Última Confirmação de TCP|Contagem|Média|Última Confirmação de TCP|Instância|
|TcpSynReceived|Yes|Sincronização de TCP Recebida|Contagem|Média|Sincronização de TCP Recebida|Instância|
|TcpSynSent|Yes|Sincronização de TCP Enviada|Contagem|Média|Sincronização de TCP Enviada|Instância|
|TcpTimeWait|Yes|Espera de Tempo de TCP|Contagem|Média|Espera de Tempo de TCP|Instância|


## <a name="microsoftwebsites"></a>Microsoft.Web/sites

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|AppConnections|Yes|conexões|Contagem|Média|O número de soquetes associados existentes na área restrita (w3wp.exe e seus processos filho). Um soquete associado é criado chamando APIs bind()/connect() e permanece até que seja fechado com CloseHandle()/closesocket().|Instância|
|AverageMemoryWorkingSet|Yes|Conjunto de trabalho de memória média|Bytes|Média|A quantidade média de memória usada pelo aplicativo em megabytes (MiB).|Instância|
|AverageResponseTime|Yes|Tempo médio de resposta (preterido)|Segundos|Média|O tempo médio necessário para o aplicativo atender solicitações, em segundos.|Instância|
|BytesReceived|Yes|Entrada de Dados|Bytes|Total|A quantidade de largura de banda de entrada consumida pelo aplicativo em MiB.|Instância|
|BytesSent|Yes|Saída de dados|Bytes|Total|A quantidade de largura de banda de saída consumida pelo aplicativo em MiB.|Instância|
|CpuTime|Yes|Tempo de CPU|Segundos|Total|A quantidade de CPU consumida pelo aplicativo em segundos. Para obter mais informações sobre essa métrica. Consulte https://docs.microsoft.com/azure/app-service/web-sites-monitor#cpu-time-vs-cpu-percentage (tempo de CPU versus percentual de CPU).|Instância|
|CurrentAssemblies|Yes|Assemblies Atuais|Contagem|Média|O número atual de Assemblies carregados em todos os AppDomains nesse aplicativo.|Instância|
|FileSystemUsage|Yes|Uso do sistema de arquivos|Bytes|Média|Porcentagem da cota do sistema de arquivos consumida pelo aplicativo.|Sem dimensões|
|FunctionExecutionCount|Yes|Contagem de Execução de Função|Contagem|Total|Contagem de Execução de Função|Instância|
|FunctionExecutionUnits|Yes|Unidades de Execução de Função|Contagem|Total|Unidades de Execução de Função|Instância|
|Gen0Collections|Yes|Coletas de lixo da Ger 0|Contagem|Total|O número de vezes que os objetos da geração 0 são coletados como lixo desde o início do processo do aplicativo. As coletas de lixo de geração superior incluem todas as coletas da geração inferior.|Instância|
|Gen1Collections|Yes|Coletas de lixo da Ger 1|Contagem|Total|O número de vezes que os objetos da geração 1 são coletados como lixo desde o início do processo do aplicativo. As coletas de lixo de geração superior incluem todas as coletas da geração inferior.|Instância|
|Gen2Collections|Yes|Coletas de lixo da Ger 2|Contagem|Total|O número de vezes que os objetos da geração 2 são coletados como lixo desde o início do processo do aplicativo.|Instância|
|Alças|Yes|Núm. de Identificadores|Contagem|Média|O número total de identificadores atualmente abertos pelo processo do aplicativo.|Instância|
|HealthCheckStatus|Yes|Status da verificação de integridade|Contagem|Média|Status da verificação de integridade|Instância|
|Http101|Yes|Http 101|Contagem|Total|A contagem de solicitações que resultam em um código de status HTTP 101.|Instância|
|Http2xx|Yes|Http 2xx|Contagem|Total|A contagem de solicitações que resultam em um código de status HTTP = 200, mas < 300.|Instância|
|Http3xx|Yes|Http 3xx|Contagem|Total|A contagem de solicitações que resultam em um código de status HTTP = 300, mas < 400.|Instância|
|Http401|Yes|Http 401|Contagem|Total|A contagem de solicitações que resultam em um código de status HTTP 401.|Instância|
|Http403|Yes|Http 403|Contagem|Total|A contagem de solicitações que resultam em um código de status HTTP 403.|Instância|
|Http404|Yes|Http 404|Contagem|Total|A contagem de solicitações que resultam em um código de status HTTP 404.|Instância|
|Http406|Yes|Http 406|Contagem|Total|A contagem de solicitações que resultam em um código de status HTTP 406.|Instância|
|Http4xx|Yes|Http 4xx|Contagem|Total|A contagem de solicitações que resultam em um código de status HTTP = 400, mas < 500.|Instância|
|Http5xx|Yes|Erros do Servidor Http|Contagem|Total|A contagem de solicitações que resultam em um código de status HTTP = 500, mas < 600.|Instância|
|HttpResponseTime|Yes|Tempo de Resposta|Segundos|Média|O tempo necessário para o aplicativo atender solicitações, em segundos.|Instância|
|IoOtherBytesPerSecond|Yes|E/S de outros bytes por segundo|BytesPerSecond|Total|A taxa na qual o processo do aplicativo está emitindo bytes para operações de e/s que não envolvem dados, como operações de controle.|Instância|
|IoOtherOperationsPerSecond|Yes|E/S de outras operações por segundo|BytesPerSecond|Total|A taxa na qual o processo do aplicativo está emitindo operações de e/s que não são operações de leitura ou gravação.|Instância|
|IoReadBytesPerSecond|Yes|E/S de bytes de leitura por segundo|BytesPerSecond|Total|A taxa na qual o processo do aplicativo está lendo bytes das operações de E/S.|Instância|
|IoReadOperationsPerSecond|Yes|E/S de operações de leitura por segundo|BytesPerSecond|Total|A taxa na qual o processo do aplicativo está gerando operações de E/S de leitura.|Instância|
|IoWriteBytesPerSecond|Yes|E/S de bytes de gravação por segundo|BytesPerSecond|Total|A taxa na qual o processo do aplicativo está gravando bytes nas operações de E/S.|Instância|
|IoWriteOperationsPerSecond|Yes|E/S de operações de gravação por segundo|BytesPerSecond|Total|A taxa na qual o processo do aplicativo está gerando operações de E/S de gravação.|Instância|
|MemoryWorkingSet|Yes|Conjunto de trabalho de memória|Bytes|Média|A quantidade atual de memória usada pelo aplicativo em MiB.|Instância|
|PrivateBytes|Yes|Bytes Particulares|Bytes|Média|Bytes privados é o tamanho atual, em bytes, da memória que o processo do aplicativo alocou que não pode ser compartilhado com outros processos.|Instância|
|Requests|Yes|Requests|Contagem|Total|O número total de solicitações, independentemente de seu código de status HTTP resultante.|Instância|
|RequestsInApplicationQueue|Yes|Solicitações na fila do aplicativo|Contagem|Média|O número de solicitações na fila de solicitação do aplicativo.|Instância|
|Threads|Yes|Contagem de Threads|Contagem|Média|O número de threads atualmente ativos no processo do aplicativo.|Instância|
|TotalAppDomains|Yes|Total de domínios de aplicativo|Contagem|Média|O número atual de AppDomains carregados nesse aplicativo.|Instância|
|TotalAppDomainsUnloaded|Yes|Total de domínios de aplicativo descarregados|Contagem|Média|O número total de AppDomains descarregados desde o início do aplicativo.|Instância|


## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|AppConnections|Yes|conexões|Contagem|Média|O número de soquetes associados existentes na área restrita (w3wp.exe e seus processos filho). Um soquete associado é criado chamando APIs bind()/connect() e permanece até que seja fechado com CloseHandle()/closesocket().|Instância|
|AverageMemoryWorkingSet|Yes|Conjunto de trabalho de memória média|Bytes|Média|A quantidade média de memória usada pelo aplicativo em megabytes (MiB).|Instância|
|AverageResponseTime|Yes|Tempo médio de resposta (preterido)|Segundos|Média|O tempo médio necessário para o aplicativo atender solicitações, em segundos.|Instância|
|BytesReceived|Yes|Entrada de Dados|Bytes|Total|A quantidade de largura de banda de entrada consumida pelo aplicativo em MiB.|Instância|
|BytesSent|Yes|Saída de dados|Bytes|Total|A quantidade de largura de banda de saída consumida pelo aplicativo em MiB.|Instância|
|CpuTime|Yes|Tempo de CPU|Segundos|Total|A quantidade de CPU consumida pelo aplicativo em segundos. Para obter mais informações sobre essa métrica. Consulte https://docs.microsoft.com/azure/app-service/web-sites-monitor#cpu-time-vs-cpu-percentage (tempo de CPU versus percentual de CPU).|Instância|
|CurrentAssemblies|Yes|Assemblies Atuais|Contagem|Média|O número atual de Assemblies carregados em todos os AppDomains nesse aplicativo.|Instância|
|FileSystemUsage|Yes|Uso do sistema de arquivos|Bytes|Média|Porcentagem da cota do sistema de arquivos consumida pelo aplicativo.|Sem dimensões|
|FunctionExecutionCount|Yes|Contagem de Execução de Função|Contagem|Total|Contagem de Execução de Função|Instância|
|FunctionExecutionUnits|Yes|Unidades de Execução de Função|Contagem|Total|Unidades de Execução de Função|Instância|
|Gen0Collections|Yes|Coletas de lixo da Ger 0|Contagem|Total|O número de vezes que os objetos da geração 0 são coletados como lixo desde o início do processo do aplicativo. As coletas de lixo de geração superior incluem todas as coletas da geração inferior.|Instância|
|Gen1Collections|Yes|Coletas de lixo da Ger 1|Contagem|Total|O número de vezes que os objetos da geração 1 são coletados como lixo desde o início do processo do aplicativo. As coletas de lixo de geração superior incluem todas as coletas da geração inferior.|Instância|
|Gen2Collections|Yes|Coletas de lixo da Ger 2|Contagem|Total|O número de vezes que os objetos da geração 2 são coletados como lixo desde o início do processo do aplicativo.|Instância|
|Alças|Yes|Núm. de Identificadores|Contagem|Média|O número total de identificadores atualmente abertos pelo processo do aplicativo.|Instância|
|HealthCheckStatus|Yes|Status da verificação de integridade|Contagem|Média|Status da verificação de integridade|Instância|
|Http101|Yes|Http 101|Contagem|Total|A contagem de solicitações que resultam em um código de status HTTP 101.|Instância|
|Http2xx|Yes|Http 2xx|Contagem|Total|A contagem de solicitações que resultam em um código de status HTTP = 200, mas < 300.|Instância|
|Http3xx|Yes|Http 3xx|Contagem|Total|A contagem de solicitações que resultam em um código de status HTTP = 300, mas < 400.|Instância|
|Http401|Yes|Http 401|Contagem|Total|A contagem de solicitações que resultam em um código de status HTTP 401.|Instância|
|Http403|Yes|Http 403|Contagem|Total|A contagem de solicitações que resultam em um código de status HTTP 403.|Instância|
|Http404|Yes|Http 404|Contagem|Total|A contagem de solicitações que resultam em um código de status HTTP 404.|Instância|
|Http406|Yes|Http 406|Contagem|Total|A contagem de solicitações que resultam em um código de status HTTP 406.|Instância|
|Http4xx|Yes|Http 4xx|Contagem|Total|A contagem de solicitações que resultam em um código de status HTTP = 400, mas < 500.|Instância|
|Http5xx|Yes|Erros do Servidor Http|Contagem|Total|A contagem de solicitações que resultam em um código de status HTTP = 500, mas < 600.|Instância|
|HttpResponseTime|Yes|Tempo de Resposta|Segundos|Média|O tempo necessário para o aplicativo atender solicitações, em segundos.|Instância|
|IoOtherBytesPerSecond|Yes|E/S de outros bytes por segundo|BytesPerSecond|Total|A taxa na qual o processo do aplicativo está emitindo bytes para operações de e/s que não envolvem dados, como operações de controle.|Instância|
|IoOtherOperationsPerSecond|Yes|E/S de outras operações por segundo|BytesPerSecond|Total|A taxa na qual o processo do aplicativo está emitindo operações de e/s que não são operações de leitura ou gravação.|Instância|
|IoReadBytesPerSecond|Yes|E/S de bytes de leitura por segundo|BytesPerSecond|Total|A taxa na qual o processo do aplicativo está lendo bytes das operações de E/S.|Instância|
|IoReadOperationsPerSecond|Yes|E/S de operações de leitura por segundo|BytesPerSecond|Total|A taxa na qual o processo do aplicativo está gerando operações de E/S de leitura.|Instância|
|IoWriteBytesPerSecond|Yes|E/S de bytes de gravação por segundo|BytesPerSecond|Total|A taxa na qual o processo do aplicativo está gravando bytes nas operações de E/S.|Instância|
|IoWriteOperationsPerSecond|Yes|E/S de operações de gravação por segundo|BytesPerSecond|Total|A taxa na qual o processo do aplicativo está gerando operações de E/S de gravação.|Instância|
|MemoryWorkingSet|Yes|Conjunto de trabalho de memória|Bytes|Média|A quantidade atual de memória usada pelo aplicativo em MiB.|Instância|
|PrivateBytes|Yes|Bytes Particulares|Bytes|Média|Bytes privados é o tamanho atual, em bytes, da memória que o processo do aplicativo alocou que não pode ser compartilhado com outros processos.|Instância|
|Requests|Yes|Requests|Contagem|Total|O número total de solicitações, independentemente de seu código de status HTTP resultante.|Instância|
|RequestsInApplicationQueue|Yes|Solicitações na fila do aplicativo|Contagem|Média|O número de solicitações na fila de solicitação do aplicativo.|Instância|
|Threads|Yes|Contagem de Threads|Contagem|Média|O número de threads atualmente ativos no processo do aplicativo.|Instância|
|TotalAppDomains|Yes|Total de domínios de aplicativo|Contagem|Média|O número atual de AppDomains carregados nesse aplicativo.|Instância|
|TotalAppDomainsUnloaded|Yes|Total de domínios de aplicativo descarregados|Contagem|Média|O número total de AppDomains descarregados desde o início do aplicativo.|Instância|


## <a name="microsoftwebstaticsites"></a>Microsoft. Web/staticSites

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|BytesSent|Yes|Saída de dados|Bytes|Total|BytesSent|Instância|
|FunctionErrors|Yes|FunctionErrors|Contagem|Total|FunctionErrors|Instância|
|FunctionHits|Yes|FunctionHits|Contagem|Total|FunctionHits|Instância|
|SiteErrors|Yes|SiteErrors|Contagem|Total|SiteErrors|Instância|
|SiteHits|Yes|SiteHits|Contagem|Total|SiteHits|Instância|


## <a name="next-steps"></a>Próximas etapas

- [Leia sobre as métricas no Azure Monitor](../data-platform.md)
- [Criar alertas nas métricas](../alerts/alerts-overview.md)
- [Exportar as métricas de armazenamento, Hub de eventos ou Log Analytics](../essentials/platform-logs-overview.md)
