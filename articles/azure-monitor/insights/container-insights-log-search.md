---
title: Como consultar logs de Azure Monitor para contêineres | Microsoft Docs
description: Azure Monitor para contêineres coleta dados de log e métricas e este artigo descreve os registros e inclui exemplos de consultas.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: dcd1656673e549b583de26bca897d0055f389d0a
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361775"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>Como consultar logs de Azure Monitor para contêineres

Azure Monitor para contêineres coleta métricas de desempenho, dados de inventário e informações de estado de integridade de hosts e contêineres de contêiner e encaminha-os para o espaço de trabalho Log Analytics no Azure Monitor. Os dados são coletados a cada três minutos. Esses dados estão disponíveis para [consulta](../../azure-monitor/log-query/log-query-overview.md) no Azure monitor. Você pode aplicar esses dados a cenários que incluem planejamento de migração, análise de capacidade, descoberta e solução de problemas de desempenho sob demanda.

## <a name="container-records"></a>Registros de contêiner

Exemplos de registros coletados pelo Azure Monitor para contêineres e os tipos de dados que aparecem nos resultados da pesquisa de logs são exibidos na tabela a seguir:

| Tipo de dados | Tipo de dados na Pesquisa de Log | Campos |
| --- | --- | --- |
| Desempenho de hosts e contêineres | `Perf` | Computer, ObjectName, CounterName &#40;%Processor Time, Disk Reads MB, Disk Writes MB, Memory Usage MB, Network Receive Bytes, Network Send Bytes, Processor Usage sec, Network&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem |
| Inventário de contêiner | `ContainerInventory` | TimeGenerated, Computer, container name, ContainerHostname, Image, ImageTag, ContainerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Log do contêiner | `ContainerLog` | TimeGenerated, Computer, image ID, container name, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Inventário de nós do contêiner | `ContainerNodeInventory`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Inventário de pods em um cluster Kubernetes | `KubePodInventory` | TimeGenerated, computador, Clusterid, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, ControllerName, ContainerStatus,  ContainerStatusReason, ContainerId, Idcontêiner, Name, PodLabel, namespace, PodStatus, ClusterName, PodIp, SourceSystem |
| Inventário da parte de nós de um cluster Kubernetes | `KubeNodeInventory` | TimeGenerated, Computer, ClusterName, ClusterId, LastTransitionTimeReady, Labels, Status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Eventos de Kubernetes | `KubeEvents` | TimeGenerated, Computer, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, Message,  SourceSystem | 
| Serviços no cluster Kubernetes | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Métricas de desempenho para a parte de nós do cluster Kubernetes | Perf &#124; em que ObjectName == “K8SNode” | Computador, ObjectName, CounterName &#40;CpuAllocatableBytes, MemoryAllocatableBytes, CpuCapacityNanoCores, MemoryCapacityBytes, MemoryRssBytes, CpuUsageNanoCores, MemoryWorkingsetBytes, restartTimeEpoch&#41;, myValue, TimeGenerated, dePath, SourceSystem | 
| Métricas de desempenho para a parte de contêineres do cluster Kubernetes | Perf &#124; em que ObjectName == “K8SContainer” | CounterName &#40; CpuRequestNanoCores, MemoryRequestBytes, CpuLimitNanoCores, MemoryWorkingSetBytes, RestartTimeEpoch, CpuUsageNanoCores, memoryRssBytes&#41;, myValue, TimeGenerated, dePath, SourceSystem | 
| Métricas personalizadas |`InsightsMetrics` | Computador, nome, namespace, origem, SourceSystem, marcas<sup>1</sup>, TimeGenerated, tipo, Va, _ResourceId | 

<sup>1</sup> a propriedade *Tags* representa [várias dimensões](../platform/data-platform-metrics.md#multi-dimensional-metrics) para a métrica correspondente. Para obter informações adicionais sobre as métricas coletadas e armazenadas na tabela `InsightsMetrics` e uma descrição das propriedades do registro, consulte [visão geral do InsightsMetrics](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md).

>[!NOTE]
>O suporte para Prometheus é um recurso em visualização pública no momento.
>

## <a name="search-logs-to-analyze-data"></a>Pesquisar logs para analisar dados

Os logs de Azure Monitor podem ajudá-lo a procurar tendências, diagnosticar afunilamentos, prever ou correlacionar dados que podem ajudá-lo a determinar se a configuração atual do cluster está sendo executada de forma ideal. Pesquisas de logs predefinidas são fornecidas para você começar a usar imediatamente ou para personalizar para retornar as informações da maneira que você deseja.

Você pode executar a análise interativa de dados no espaço de trabalho selecionando a opção **Exibir logs de eventos kubernetes** ou **Exibir logs de contêiner** no painel de visualização na lista suspensa **exibição na análise** . A página **Pesquisa de Logs** aparece acima da página de portal do Azure em que você estava.

![Análise de dados no Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)   

A saída dos logs de contêiner que é encaminhada para seu espaço de trabalho são STDOUT e STDERR. Uma vez que o Azure Monitor está monitorando o Kubernetes gerenciado pelo Azure (AKS), o Kube-system não é coletado hoje devido ao grande volume de dados gerado. 

### <a name="example-log-search-queries"></a>Exemplo de consultas de pesquisa de log

Costuma ser útil criar consultas começando com um ou dois exemplos e, em seguida, modificá-las de acordo com seus requisitos. Para ajudar a criar consultas mais avançadas, você pode experimentar as seguintes consultas de amostra:

| Consulta | DESCRIÇÃO | 
|-------|-------------|
| ContainerInventory<br> &#124; projetar Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124;processar tabela | Listar todas as informações do ciclo de vida do contêiner| 
| KubeEvents_CL<br> &#124; em que not(isempty(Namespace_s))<br> &#124; classificar por TimeGenerated desc<br> &#124;processar tabela | Eventos de Kubernetes|
| ContainerImageInventory<br> &#124; resumir AggregatedValue = count() by Image, ImageTag, Running | Inventário de imagem | 
| **Selecione a opção de exibição Gráfico de linhas**:<br> Perf<br> &#124; em que ObjectName == "K8SContainer" e CounterName == "cpuUsageNanoCores" &#124; resumir AvgCPUUsageNanoCores = avg(CounterValue) por bin(TimeGenerated, 30m), InstanceName | CPU do contêiner | 
| **Selecione a opção de exibição Gráfico de linhas**:<br> Perf<br> &#124; em que ObjectName == "K8SContainer" e CounterName == "memoryRssBytes" &#124; resumir AvgUsedRssMemoryBytes = avg(CounterValue) por bin(TimeGenerated, 30m), InstanceName | Memória de contêiner |
| InsightsMetrics<br> &#124;WHERE name = = "requests_count"<br> &#124;resumir Val = Any (Val) por TimeGenerated = bin (TimeGenerated, 1m)<br> &#124;classificar por TimeGenerated ASC<br> &#124;projeto RequestsPerMinute = Val-anterior (Val), TimeGenerated <br> &#124;renderizar barChart  | Solicitações por minuto com métricas personalizadas |

## <a name="query-prometheus-metrics-data"></a>Consultar dados de métricas do Prometheus

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

Para exibir as métricas de Prometheus recortadas por Azure Monitor filtradas pelo namespace, especifique "Prometheus". Aqui está uma consulta de exemplo para exibir as métricas de Prometheus do namespace do `default` kubernetes.

```
InsightsMetrics 
| where Namespace == "prometheus"
| extend tags=parse_json(Tags)
| summarize count() by Name
```

Os dados de Prometheus também podem ser consultados diretamente por nome.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where Name contains "some_prometheus_metric"
```

### <a name="query-config-or-scraping-errors"></a>Erros de configuração de consulta ou de sucata

Para investigar qualquer configuração ou erros de recorte, a consulta de exemplo a seguir retorna eventos informativos da tabela `KubeMonAgentEvents`.

```
KubeMonAgentEvents | where Level != "Info" 
```

A saída mostrará resultados semelhantes ao seguinte:

![Registrar resultados de consulta de eventos informativos do agente](./media/container-insights-log-search/log-query-example-kubeagent-events.png)

## <a name="next-steps"></a>Próximas etapas

Azure Monitor para contêineres não inclui um conjunto predefinido de alertas. Examine [criar alertas de desempenho com Azure monitor para contêineres](container-insights-alerts.md) para saber como criar alertas recomendados para alta utilização de CPU e memória para dar suporte aos processos e procedimentos operacionais ou DevOps. 
