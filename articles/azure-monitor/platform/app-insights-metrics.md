---
title: Métricas baseadas em log do Azure Application Insights | Microsoft Docs
description: Este artigo lista métricas do Azure Application Insights com agregações e dimensões suportadas. Os detalhes sobre métricas baseadas em log incluem as instruções de consulta kusto subjacentes.
author: vgorbenko
services: azure-monitor
ms.topic: reference
ms.date: 07/03/2019
ms.author: vitalyg
ms.subservice: application-insights
ms.openlocfilehash: 12bc51e800ef5ccd4ad3c72d3860fb22bac5b749
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77664908"
---
# <a name="application-insights-log-based-metrics"></a>Métricas baseadas em log do Application Insights

As métricas baseadas em log do Application Insights permitem analisar a saúde de seus aplicativos monitorados, criar painéis poderosos e configurar alertas. Existem dois tipos de métricas:

* [As métricas baseadas em log](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#log-based-metrics) por trás da cena são traduzidas em [consultas kusto](https://docs.microsoft.com/azure/kusto/query/) de eventos armazenados.
* [As métricas padrão](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics) são armazenadas como séries temporias pré-agregadas.

Uma *vez que as métricas padrão* são pré-agregadas durante a coleta, elas têm melhor desempenho no momento da consulta. Isso faz deles uma escolha melhor para dashboarding e em alerta em tempo real. As *métricas baseadas em log* têm mais dimensões, o que as torna a opção superior para análise de dados e diagnósticos ad-hoc. Use o [seletor de namespace](metrics-getting-started.md#create-your-first-metric-chart) para alternar entre métricas baseadas em log e padrão no [explorador de métricas](metrics-getting-started.md).

## <a name="interpret-and-use-queries-from-this-article"></a>Interprete e use consultas deste artigo

Este artigo lista métricas com agregações e dimensões suportadas. Os detalhes sobre métricas baseadas em log incluem as instruções de consulta kusto subjacentes. Para conveniência, cada consulta usa padrões para granularidade de tempo, tipo de gráfico e, às vezes, dimensão de divisão que simplifica o uso da consulta no Log Analytics sem qualquer necessidade de modificação.

Quando você plota a mesma métrica no [explorador de métricas,](metrics-getting-started.md)não há padrões - a consulta é dinamicamente ajustada com base nas configurações do gráfico:

- O **intervalo de tempo** selecionado é traduzido para uma cláusula adicional *onde o carimbo de tempo...* para escolher apenas os eventos do intervalo de tempo selecionado. Por exemplo, um gráfico mostrando dados para as 24 horas mais recentes, a consulta inclui *| onde o carimbo de data e hora > atrás(24 horas)*.

- A **granularidade do tempo** selecionada é colocada na resumição final *... por cláusula bin (carimbo de tempo, [grão de tempo]).*

- Quaisquer dimensões **de filtro** selecionadas são traduzidas em outras *cláusulas.*

- A dimensão **do gráfico Split** selecionado é traduzida em uma propriedade de resumição extra. Por exemplo, se você dividir seu gráfico por *localização*, e plotar usando uma granularidade de tempo de 5 minutos, a cláusula *de resumição* é resumida *... por bin (carimbo de tempo, 5 m), localização*.

> [!NOTE]
> Se você é novo no idioma de consulta kusto, você começa copiando e colando instruções kusto no painel de consulta do Log Analytics sem fazer quaisquer modificações. Clique **em Executar** para ver o gráfico básico. À medida que você começa a entender a sintaxe da linguagem de consulta, você pode começar a fazer pequenas modificações e ver o impacto da sua mudança. Explorar seus próprios dados é uma ótima maneira de começar a perceber todo o poder do [Log Analytics](../../azure-monitor/log-query/get-started-portal.md) e [do Azure Monitor](../../azure-monitor/overview.md).

## <a name="availability-metrics"></a>Métricas de disponibilidade

As métricas na categoria Disponibilidade permitem que você veja a saúde do seu aplicativo web como observado a partir de pontos ao redor do mundo. [Configure os testes de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md) para começar a usar quaisquer métricas desta categoria.

### <a name="availability-availabilityresultsavailabilitypercentage"></a>DisponibilidadeResultados/disponibilidadePercentual)
A *métrica Disponibilidade* mostra a porcentagem das corridas de teste da Web que não detectaram problemas. O menor valor possível é 0, o que indica que todas as corridas de teste web falharam. O valor de 100 significa que todos os testes web executados passaram pelos critérios de validação.

|Unidade de medida|Agregações suportadas|Dimensões suportadas|
|---|---|---|---|---|---|
|Porcentagem|Média|Local de execução, nome do teste|

```Kusto
availabilityResults 
| summarize sum(todouble(success == 1) * 100) / count() by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-test-duration-availabilityresultsduration"></a>Duração do teste de disponibilidade (disponibilidadeResultados/duração)

A métrica *de duração do teste de disponibilidade* mostra quanto tempo levou para o teste web ser executado. Para os [testes web em várias etapas,](../../azure-monitor/app/availability-multistep.md)a métrica reflete o tempo total de execução de todas as etapas.

|Unidade de medida|Agregações suportadas|Dimensões suportadas|
|---|---|---|---|---|---|
|Milissegundos|Média, Min, Max|Local de execução, nome do teste, resultado do teste

```Kusto
availabilityResults
| where notempty(duration)
| extend availabilityResult_duration = iif(itemType == 'availabilityResult', duration, todouble(''))
| summarize sum(availabilityResult_duration)/sum(itemCount) by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-tests-availabilityresultscount"></a>Testes de disponibilidade (disponibilidadeResultados/contagem)

A métrica *de testes de disponibilidade* reflete a contagem dos testes web executados pelo Azure Monitor.

|Unidade de medida|Agregações suportadas|Dimensões suportadas|
|---|---|---|---|---|---|
|Contagem|Contagem|Local de execução, nome do teste, resultado do teste|

```Kusto
availabilityResults
| summarize sum(itemCount) by bin(timestamp, 5m)
| render timechart
```

## <a name="browser-metrics"></a>Métricas do navegador

As métricas do navegador são coletadas pelo Application Insights JavaScript SDK de navegadores reais do usuário final. Eles fornecem grandes insights sobre a experiência de seus usuários com seu aplicativo web. As métricas do navegador normalmente não são amostradas, o que significa que elas fornecem maior precisão dos números de uso em comparação com métricas do lado do servidor que podem ser distorcidas por amostragem.

> [!NOTE]
> Para coletar métricas do navegador, seu aplicativo deve ser instrumentado com o [Application Insights JavaScript SDK](../../azure-monitor/app/javascript.md).

### <a name="browser-page-load-time-browsertimingstotalduration"></a>Tempo de carga da página do navegador (navegadorTimings/totalDuração)

|Unidade de medida|Agregações suportadas|Dimensões pré-agregadas|
|---|---|---|
|Milissegundos|Média, Min, Max|Nenhum|

```Kusto
browserTimings
| where notempty(totalDuration)
| extend _sum = totalDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="client-processing-time-browsertimingprocessingduration"></a>Tempo de processamento do cliente (navegadorTiming/processamentoDuração)

|Unidade de medida|Agregações suportadas|Dimensões pré-agregadas|
|---|---|---|
|Milissegundos|Média, Min, Max|Nenhum|

```Kusto
browserTimings
| where notempty(processingDuration)
| extend _sum = processingDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="page-load-network-connect-time-browsertimingsnetworkduration"></a>Tempo de conexão de rede de carga de página (navegadorTimings/redeDuração)

|Unidade de medida|Agregações suportadas|Dimensões pré-agregadas|
|---|---|---|
|Milissegundos|Média, Min, Max|Nenhum|

```Kusto
browserTimings
| where notempty(networkDuration)
| extend _sum = networkDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="receiving-response-time-browsertimingsreceiveduration"></a>Recebimento do tempo de resposta (navegadorTimings/receiveDuration)

|Unidade de medida|Agregações suportadas|Dimensões pré-agregadas|
|---|---|---|
|Milissegundos|Média, Min, Max|Nenhum|

```Kusto
browserTimings
| where notempty(receiveDuration)
| extend _sum = receiveDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="send-request-time-browsertimingssendduration"></a>Enviar tempo de solicitação (navegadorTimings/sendDuração)

|Unidade de medida|Agregações suportadas|Dimensões pré-agregadas|
|---|---|---|
|Milissegundos|Média, Min, Max|Nenhum|

```Kusto
browserTimings
| where notempty(sendDuration)
| extend _sum = sendDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

## <a name="failure-metrics"></a>Métricas de falha

As métricas em **Falhas** mostram problemas com solicitações de processamento, chamadas de dependência e exceções lançadas.

### <a name="browser-exceptions-exceptionsbrowser"></a>Exceções do navegador (exceções/navegador)

Essa métrica reflete o número de exceções lançadas do código do aplicativo em execução no navegador. Apenas exceções que são ```trackException()``` rastreadas com uma chamada aPI do Application Insights estão incluídas na métrica.

|Unidade de medida|Agregações suportadas|Dimensões pré-agregadas|Observações|
|---|---|---|---|
|Contagem|Contagem|Nenhum|A versão baseada em log usa agregação **de soma**|

```Kusto
exceptions
| where notempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-call-failures-dependenciesfailed"></a>Falhas de chamada de dependência (dependências/falha)

O número de chamadas de dependência falhadas.

|Unidade de medida|Agregações suportadas|Dimensões pré-agregadas|Observações|
|---|---|---|---|
|Contagem|Contagem|Nenhum|A versão baseada em log usa agregação **de soma**|

```Kusto
dependencies
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="exceptions-exceptionscount"></a>Exceções (exceções/contagem)

Cada vez que você registra uma exceção ao Application Insights, há uma chamada para o [método trackException()](../../azure-monitor/app/api-custom-events-metrics.md#trackexception) do SDK. A métrica Exceções mostra o número de exceções registradas.

|Unidade de medida|Agregações suportadas|Dimensões pré-agregadas|Observações|
|---|---|---|---|
|Contagem|Contagem|Nome da função na nuvem, instância de função na nuvem, tipo de dispositivo|A versão baseada em log usa agregação **de soma**|

```Kusto
exceptions
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="failed-requests-requestsfailed"></a>Solicitações com falha (solicitações/falha)

A contagem de solicitações de servidor rastreado que foram marcadas como *falha*. Por padrão, o Application Insights SDK marca automaticamente cada solicitação de servidor que retornou o código de resposta HTTP 5xx ou 4xx como uma solicitação com falha. Você pode personalizar essa lógica modificando a propriedade de *sucesso* do item de telemetria de solicitação em um [inicializador de telemetria personalizado](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer).

|Unidade de medida|Agregações suportadas|Dimensões pré-agregadas|Observações|
|---|---|---|---|
|Contagem|Contagem|Exemplo de função na nuvem, nome da função cloud, tráfego real ou sintético, desempenho de solicitação, código de resposta|A versão baseada em log usa agregação **de soma**|

```Kusto
requests
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-exceptions-exceptionsserver"></a>Exceções do servidor (exceções/servidor)

Esta métrica mostra o número de exceções do servidor.

|Unidade de medida|Agregações suportadas|Dimensões pré-agregadas|Observações|
|---|---|---|---|
|Contagem|Contagem|Nome da função na nuvem, instância de função na nuvem|A versão baseada em log usa agregação **de soma**|

```Kusto
exceptions
| where isempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

## <a name="performance-counters"></a>Contadores de desempenho

Use métricas na categoria **contadores performance** para acessar contadores de desempenho do [sistema coletados pelo Application Insights](../../azure-monitor/app/performance-counters.md).

### <a name="available-memory-performancecountersavailablememory"></a>Memória disponível (performanceCounters/availableMemory)

```Kusto
performanceCounters
| where ((category == "Memory" and counter == "Available Bytes") or name == "availableMemory")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="exception-rate-performancecountersexceptionrate"></a>Taxa de exceção (performanceCounters/exceptionRate)

```Kusto
performanceCounters
| where ((category == ".NET CLR Exceptions" and counter == "# of Exceps Thrown / sec") or name == "exceptionRate")
| extend performanceCounter_value = iif(itemType == 'performanceCounter',value,todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-execution-time-performancecountersrequestexecutiontime"></a>TEMPO de execução da solicitação HTTP (performanceCounters/requestExecutionTime)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Request Execution Time") or name == "requestExecutionTime")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-rate-performancecountersrequestspersecond"></a>Taxa de solicitação HTTP (performanceCounters/requestsPerSecond)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests/Sec") or name == "requestsPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-requests-in-application-queue-performancecountersrequestsinqueue"></a>Solicitações HTTP na fila de aplicativos (performanceCounters/requestsInQueue)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests In Application Queue") or name == "requestsInQueue")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-cpu-performancecountersprocesscpupercentage"></a>CPU de processo (desempenhoContadors/processosCpuPercentage)

A métrica mostra quanto da capacidade total do processador é consumida pelo processo que está hospedando seu aplicativo monitorado.

|Unidade de medida|Agregações suportadas|Dimensões suportadas|
|---|---|---|
|Porcentagem|Média, Min, Max|Instância de função de nuvem

```Kusto
performanceCounters
| where ((category == "Process" and counter == "% Processor Time Normalized") or name == "processCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-io-rate-performancecountersprocessiobytespersecond"></a>Taxa de IO do processo (performanceCounters/processIOBytesPerSecond)

|Unidade de medida|Agregações suportadas|Dimensões suportadas|
|---|---|---|
|Bytes por segundo|Média, Min, Max|Instância de função de nuvem

```Kusto
performanceCounters
| where ((category == "Process" and counter == "IO Data Bytes/sec") or name == "processIOBytesPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-private-bytes-performancecountersprocessprivatebytes"></a>Processe bytes privados (performanceCounters/processPrivateBytes)

Quantidade de memória não compartilhada que o processo monitorado alocou para seus dados.

|Unidade de medida|Agregações suportadas|Dimensões suportadas|
|---|---|---|
|Bytes|Média, Min, Max|Instância de função de nuvem

```Kusto
performanceCounters
| where ((category == "Process" and counter == "Private Bytes") or name == "processPrivateBytes")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="processor-time-performancecountersprocessorcpupercentage"></a>Tempo do processador (desempenhoContadors/processadorCpuPercentage)

Consumo de CPU por *todos os* processos executados na instância do servidor monitorado.

|Unidade de medida|Agregações suportadas|Dimensões suportadas|
|---|---|---|
|Porcentagem|Média, Min, Max|Instância de função de nuvem

>[!NOTE]
> A métrica de tempo do processador não está disponível para os aplicativos hospedados no Azure App Services. Use a métrica [process CPU](#process-cpu-performancecountersprocesscpupercentage) para rastrear a utilização da CPU dos aplicativos da Web hospedados nos Serviços de Aplicativos.

```Kusto
performanceCounters
| where ((category == "Processor" and counter == "% Processor Time") or name == "processorCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

## <a name="server-metrics"></a>Métricas do servidor

### <a name="dependency-calls-dependenciescount"></a>Chamadas de dependência (dependências/contagem)

Esta métrica é em relação ao número de chamadas de dependência.

```Kusto
dependencies
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-duration-dependenciesduration"></a>Duração da dependência (dependências/duração)

Esta métrica refere-se à duração das chamadas de dependência.

```Kusto
dependencies
| where notempty(duration)
| extend dependency_duration = iif(itemType == 'dependency',duration,todouble(''))
| extend _sum = dependency_duration
| extend _count = itemCount
| extend _sum = _sum*_count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 1m)
| render timechart
```

### <a name="server-requests-requestscount"></a>Solicitações de servidor (solicitações/contagem)

Essa métrica reflete o número de solicitações de servidor recebidas recebidas pelo seu aplicativo web.

```Kusto
requests
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-response-time-requestsduration"></a>Tempo de resposta do servidor (solicitações/duração)

Essa métrica reflete o tempo que os servidores levaram para processar as solicitações recebidas.

```Kusto
requests
| where notempty(duration)
| extend request_duration = iif(itemType == 'request', duration, todouble(''))
| extend _sum = request_duration
| extend _count = itemCount
| extend _sum = _sum*_count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 1m)
| render timechart
```

## <a name="usage-metrics"></a>Métricas de uso

### <a name="page-view-load-time-pageviewsduration"></a>Tempo de carga de exibição de página (páginaViews/duração)

Esta métrica refere-se à quantidade de tempo que levou para os eventos do PageView carregarem.

```Kusto
pageViews
| where notempty(duration)
| extend pageView_duration = iif(itemType == 'pageView', duration, todouble(''))
| extend _sum = pageView_duration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render barchart
```

### <a name="page-views-pageviewscount"></a>Visualizações de página (pageViews/count)

A contagem de eventos do PageView registrada com a API trackPageView() Application Insights.

```Kusto
pageViews
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="sessions-sessionscount"></a>Sessões (sessões/contagem)

Esta métrica refere-se à contagem de IDs de sessão distintos.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(session_Id)
| summarize dcount(session_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="traces-tracescount"></a>Traços (traços/contagem)

A contagem de demonstrações de rastreamento registradas com a chamada aPI do aplicativo TrackTrace().

```Kusto
traces
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-userscount"></a>Usuários (usuários/contagem)

O número de usuários distintos que acessaram seu aplicativo. A precisão desta métrica pode ser significativamente impactada pelo uso de amostragem e filtragem de telemetria.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_Id)
| summarize dcount(user_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-authenticated-usersauthenticated"></a>Usuários, Autenticados (usuários/autenticados)

O número de usuários distintos que autenticaram em seu aplicativo.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_AuthenticatedId)
| summarize dcount(user_AuthenticatedId) by bin(timestamp, 1h)
| render barchart
```
