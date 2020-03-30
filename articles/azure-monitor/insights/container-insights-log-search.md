---
title: Como consultar logs do Monitor Azure para contêineres | Microsoft Docs
description: O Monitor do Azure para contêineres coleta métricas e dados de registro e este artigo descreve os registros e inclui consultas de amostra.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: ff7cbff708b794847d8be69ca8f829e622d7c7ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80333472"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>Como consultar logs do Azure Monitor para contêineres

O Azure Monitor para contêineres coleta métricas de desempenho, dados de inventário e informações do estado de saúde de hosts e contêineres de contêineres e os encaminha para o espaço de trabalho Log Analytics no Azure Monitor. Os dados são coletados a cada três minutos. Esses dados estão disponíveis [para consulta no](../../azure-monitor/log-query/log-query-overview.md) Azure Monitor. Você pode aplicar esses dados a cenários que incluem planejamento de migração, análise de capacidade, descoberta e solução de problemas de desempenho sob demanda.

## <a name="container-records"></a>Registros de contêiner

Exemplos de registros coletados pelo Azure Monitor para contêineres e os tipos de dados que aparecem nos resultados da pesquisa de logs são exibidos na tabela a seguir:

| Tipo de dados | Tipo de dados na Pesquisa de Log | Campos |
| --- | --- | --- |
| Desempenho de hosts e contêineres | `Perf` | Computer, ObjectName, CounterName &#40;%Processor Time, Disk Reads MB, Disk Writes MB, Memory Usage MB, Network Receive Bytes, Network Send Bytes, Processor Usage sec, Network&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem |
| Inventário de contêiner | `ContainerInventory` | TimeGenerated, Computer, container name, ContainerHostname, Image, ImageTag, ContainerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Log do contêiner | `ContainerLog` | TimeGenerated, Computer, image ID, container name, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Inventário de nós do contêiner | `ContainerNodeInventory`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Inventário de pods em um cluster Kubernetes | `KubePodInventory` | TimeGenerated, Computador, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, ControllerName, ContainerStatus,  ContainerStatusReason, ContainerID, ContainerName, Name, PodLabel, Namespace, PodStatus, ClusterName, PodIp, SourceSystem |
| Inventário da parte de nós de um cluster Kubernetes | `KubeNodeInventory` | TimeGenerated, Computer, ClusterName, ClusterId, LastTransitionTimeReady, Labels, Status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Eventos de Kubernetes | `KubeEvents` | TimeGenerated, Computer, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, Message,  SourceSystem | 
| Serviços no cluster Kubernetes | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Métricas de desempenho para a parte de nós do cluster Kubernetes | Perf &#124; onde ObjectName == "K8SNode" | Computador, Nome do Objeto, Contranome &#40;cpuLoloáveis, memóriaLocávelBytes, cpuCapacidadeNanoCores, memóriaCapacidadeBytes, memóriaRssBytes, cpuUsageNanoCores, memoryWorkingsetBytes, restartTimeEpoch&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Métricas de desempenho para a parte de contêineres do cluster Kubernetes | Perf &#124; onde ObjectName == "K8SContainer" | Contranome &#40; cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryWorkingSetBytes, restartTimeEpoch, cpuUsageNanoCores, memoryRssBytes&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Métricas personalizadas |`InsightsMetrics` | Computador, Nome, Namespace, Origem, SourceSystem, Tags<sup>1</sup>, TimeGenerated, Type, Va, _ResourceId | 

<sup>1</sup> A propriedade *Tags* representa [várias dimensões](../platform/data-platform-metrics.md#multi-dimensional-metrics) para a métrica correspondente. Para obter informações adicionais sobre as `InsightsMetrics` métricas coletadas e armazenadas na tabela e uma descrição das propriedades de registro, consulte [visão geral do InsightsMetrics](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md).

## <a name="search-logs-to-analyze-data"></a>Pesquisar logs para analisar dados

O Azure Monitor Logs pode ajudá-lo a procurar tendências, diagnosticar gargalos, prever ou correlacionar dados que podem ajudá-lo a determinar se a configuração atual do cluster está funcionando de forma ideal. Pesquisas de logs predefinidas são fornecidas para você começar a usar imediatamente ou para personalizar para retornar as informações da maneira que você deseja.

Você pode executar a análise interativa de dados no espaço de trabalho selecionando os **logs de eventos Do Exibir Kubernetes** ou exibir a opção **Registros de contêiner** no painel de visualização da lista de paradas de **análise.** A página **Pesquisa de Logs** aparece acima da página de portal do Azure em que você estava.

![Análise de dados no Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)

A saída de registros de contêineres que é encaminhada para o seu espaço de trabalho são STDOUT e STDERR. Uma vez que o Azure Monitor está monitorando o Kubernetes gerenciado pelo Azure (AKS), o Kube-system não é coletado hoje devido ao grande volume de dados gerado. 

### <a name="example-log-search-queries"></a>Exemplo de consultas de pesquisa de log

Costuma ser útil criar consultas começando com um ou dois exemplos e, em seguida, modificá-las de acordo com seus requisitos. Para ajudar a criar consultas mais avançadas, você pode experimentar as seguintes consultas de amostra:

| Consulta | Descrição | 
|-------|-------------|
| ContainerInventory<br> &#124; projetar Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124;processar tabela | Listar todas as informações do ciclo de vida do contêiner| 
| KubeEvents_CL<br> &#124; em que not(isempty(Namespace_s))<br> &#124; classificar por TimeGenerated desc<br> &#124;processar tabela | Eventos de Kubernetes|
| ContainerImageInventory<br> &#124; resumir AggregatedValue = count() by Image, ImageTag, Running | Inventário de imagem | 
| **Selecione a opção de exibição Gráfico de linhas**:<br> Perf<br> &#124; em que ObjectName == "K8SContainer" e CounterName == "cpuUsageNanoCores" &#124; resumir AvgCPUUsageNanoCores = avg(CounterValue) por bin(TimeGenerated, 30m), InstanceName | CPU do contêiner | 
| **Selecione a opção de exibição Gráfico de linhas**:<br> Perf<br> &#124; em que ObjectName == "K8SContainer" e CounterName == "memoryRssBytes" &#124; resumir AvgUsedRssMemoryBytes = avg(CounterValue) por bin(TimeGenerated, 30m), InstanceName | Memória de contêiner |
| InsightsMetrics<br> &#124; onde Name == "requests_count"<br> &#124; resumir Val=any(Val) por TimeGenerated=bin (TimeGenerated, 1m)<br> &#124; classificação por timeGenerated asc<br> &#124; solicitações do projetoPerMinute = Val - prev(Val), TimeGenerated <br> &#124; render barchart  | Solicitações por minuto com métricas personalizadas |

## <a name="query-prometheus-metrics-data"></a>Dados de métricas do Query Prometheus

O exemplo a seguir é uma consulta de métricas Prometheus mostrando leituras de disco por segundo por disco por nó.

```
InsightsMetrics
| where Namespace == 'container.azm.ms/diskio'
| where TimeGenerated > ago(1h)
| where Name == 'reads'
| extend Tags = todynamic(Tags)
| extend HostName = tostring(Tags.hostName), Device = Tags.name
| extend NodeDisk = strcat(Device, "/", HostName)
| order by NodeDisk asc, TimeGenerated asc
| serialize
| extend PrevVal = iif(prev(NodeDisk) != NodeDisk, 0.0, prev(Val)), PrevTimeGenerated = iif(prev(NodeDisk) != NodeDisk, datetime(null), prev(TimeGenerated))
| where isnotnull(PrevTimeGenerated) and PrevTimeGenerated != TimeGenerated
| extend Rate = iif(PrevVal > Val, Val / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1), iif(PrevVal == Val, 0.0, (Val - PrevVal) / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1)))
| where isnotnull(Rate)
| project TimeGenerated, NodeDisk, Rate
| render timechart

```

Para ver as métricas do Prometheus raspadas pelo Azure Monitor filtradas pelo Namespace, especifique "prometheus". Aqui está uma consulta de exemplo para visualizar `default` as métricas do Prometeu a partir do namespace kubernetes.

```
InsightsMetrics 
| where Namespace == "prometheus"
| extend tags=parse_json(Tags)
| summarize count() by Name
```

Os dados do Prometeu também podem ser consultados diretamente pelo nome.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where Name contains "some_prometheus_metric"
```

### <a name="query-config-or-scraping-errors"></a>Erros de configuração ou raspagem

Para investigar quaisquer erros de configuração ou raspagem, a `KubeMonAgentEvents` consulta de exemplo a seguir retorna eventos informativos da tabela.

```
KubeMonAgentEvents | where Level != "Info" 
```

A saída mostrará resultados semelhantes aos seguintes:

![Log de consulta resultados de eventos informativos do agente](./media/container-insights-log-search/log-query-example-kubeagent-events.png)

## <a name="next-steps"></a>Próximas etapas

O Monitor Azure para contêineres não inclui um conjunto predefinido de alertas. Revise os [alertas de desempenho criar com o Azure Monitor para contêineres](container-insights-alerts.md) para aprender como criar alertas recomendados para alta utilização de CPU e memória para suportar seus DevOps ou processos e procedimentos operacionais. 
