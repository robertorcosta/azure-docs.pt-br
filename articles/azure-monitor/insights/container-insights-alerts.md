---
title: Crie alertas de desempenho para o Azure Monitor para contêineres | Microsoft Docs
description: Este artigo descreve como criar alertas personalizados com base em consultas de log para a utilização de memória e CPU do Azure Monitor para contêineres.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 5d73f4399d10683597fb2a2e8a3a2ab4ba0d1165
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75730918"
---
# <a name="how-to-set-up-alerts-for-performance-problems-in-azure-monitor-for-containers"></a>Como configurar alertas no Azure Monitor para problemas de desempenho de contêineres

O Azure Monitor para contêineres monitora o desempenho das cargas de trabalho de contêineres que são implantadas em Instâncias de Contêineres Do Azure ou para clusters Kubernetes gerenciados hospedados no Azure Kubernetes Service (AKS).

Este artigo descreve como ativar alertas para as seguintes situações:

- Quando a utilização da CPU ou da memória em nós de cluster excede um limite
- Quando a utilização de CPU ou memória em qualquer contêiner dentro de um controlador excede um limite em comparação com um limite definido no recurso correspondente
- Contatos de nó de status *NotReady*
- *Contagens de*fase de pod *sumidas,* *pendentes,* *desconhecidas* *ou*em execução ou bem sucedidas
- Quando o espaço livre em disco em nós de cluster excede um limite 

Para alertar para alta utilização de CPU ou memória, ou baixo espaço em disco livre em nós de cluster, use as consultas fornecidas para criar um alerta métrico ou um alerta de medição métrica. Os alertas métricos têm menor latência do que os alertas de registro. Mas os alertas de log fornecem consultas avançadas e maior sofisticação. As consultas de alertas de log comparam uma data com a presente usando o *agora* operador e voltando uma hora. (Monitor azure para lojas de contêineres todas as datas no formato Coordinated Universal Time (UTC).)

Se você não estiver familiarizado com os alertas do Azure Monitor, consulte [Visão geral dos alertas no Microsoft Azure](../platform/alerts-overview.md) antes de iniciar. Para saber mais sobre os alertas que usam consultas de log, consulte [Alertas de log no Azure Monitor](../platform/alerts-unified-log.md). Para obter mais sobre alertas métricos, consulte [Alertas métricos no Azure Monitor](../platform/alerts-metric-overview.md).

## <a name="resource-utilization-log-search-queries"></a>Consultas de pesquisa de log de utilização de recursos

As consultas nesta seção suportam cada cenário de alerta. Eles são usados na etapa 7 da seção [de alerta](#create-an-alert-rule) de criação deste artigo.

A consulta a seguir calcula a utilização média da CPU como uma média da utilização da CPU dos nodos de membros a cada minuto.  

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuCapacityNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```

A consulta a seguir calcula a utilização média da memória como uma média da utilização da memória dos nódulos membros a cada minuto.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryCapacityBytes';
let usageCounterName = 'memoryRssBytes';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```
>[!IMPORTANT]
>As consultas a seguir usam \<os valores do \<espaço reservado> e o nome do seu controlador> para representar o cluster e o controlador. Substitua-os por valores específicos do seu ambiente quando configurar alertas.

A consulta a seguir calcula a utilização média da CPU de todos os recipientes em um controlador como uma média de utilização da CPU de cada instância de contêiner em um controlador a cada minuto. A medição é uma porcentagem do limite estabelecido para um contêiner.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuLimitNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

A consulta a seguir calcula a utilização média da memória de todos os recipientes em um controlador como uma média de utilização da memória de cada instância de contêiner em um controlador a cada minuto. A medição é uma porcentagem do limite estabelecido para um contêiner.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryLimitBytes';
let usageCounterName = 'memoryRssBytes';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

A consulta a seguir retorna todos os nós e contagens que têm um status de *Ready* and *NotReady*.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| distinct ClusterName, Computer, TimeGenerated
| summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName, Computer
| join hint.strategy=broadcast kind=inner (
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | summarize TotalCount = count(), ReadyCount = sumif(1, Status contains ('Ready'))
                by ClusterName, Computer,  bin(TimeGenerated, trendBinSize)
    | extend NotReadyCount = TotalCount - ReadyCount
) on ClusterName, Computer, TimeGenerated
| project   TimeGenerated,
            ClusterName,
            Computer,
            ReadyCount = todouble(ReadyCount) / ClusterSnapshotCount,
            NotReadyCount = todouble(NotReadyCount) / ClusterSnapshotCount
| order by ClusterName asc, Computer asc, TimeGenerated desc
```
A seguinte consulta retorna a fase de retornos conta gensiotas com base em todas as fases: *Failed*, *Pending*, *Unknown*, *Running*ou *Succeeded*.  

```kusto
let endDateTime = now();
    let startDateTime = ago(1h);
    let trendBinSize = 1m;
    let clusterName = '<your-cluster-name>';
    KubePodInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ClusterName == clusterName
    | distinct ClusterName, TimeGenerated
    | summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName
    | join hint.strategy=broadcast (
        KubePodInventory
        | where TimeGenerated < endDateTime
        | where TimeGenerated >= startDateTime
        | distinct ClusterName, Computer, PodUid, TimeGenerated, PodStatus
        | summarize TotalCount = count(),
                    PendingCount = sumif(1, PodStatus =~ 'Pending'),
                    RunningCount = sumif(1, PodStatus =~ 'Running'),
                    SucceededCount = sumif(1, PodStatus =~ 'Succeeded'),
                    FailedCount = sumif(1, PodStatus =~ 'Failed')
                 by ClusterName, bin(TimeGenerated, trendBinSize)
    ) on ClusterName, TimeGenerated
    | extend UnknownCount = TotalCount - PendingCount - RunningCount - SucceededCount - FailedCount
    | project TimeGenerated,
              TotalCount = todouble(TotalCount) / ClusterSnapshotCount,
              PendingCount = todouble(PendingCount) / ClusterSnapshotCount,
              RunningCount = todouble(RunningCount) / ClusterSnapshotCount,
              SucceededCount = todouble(SucceededCount) / ClusterSnapshotCount,
              FailedCount = todouble(FailedCount) / ClusterSnapshotCount,
              UnknownCount = todouble(UnknownCount) / ClusterSnapshotCount
| summarize AggregatedValue = avg(PendingCount) by bin(TimeGenerated, trendBinSize)
```

>[!NOTE]
>Para alertar sobre determinadas fases do pod, como *Pendente*, *Falha,* ou *Desconhecido,* modifique a última linha da consulta. Por exemplo, para alertar sobre o uso *de Count Falha:* <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

A consulta a seguir retorna os discos de nó de cluster que excedem 90% do espaço livre utilizado. Para obter o ID do cluster, primeiro execute a `ClusterId` seguinte consulta e copie o valor da propriedade:

```kusto
InsightsMetrics
| extend Tags = todynamic(Tags)            
| project ClusterId = Tags['container.azm.ms/clusterId']   
| distinct tostring(ClusterId)   
``` 

```kusto
let clusterId = '<cluster-id>';
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
InsightsMetrics
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where Origin == 'container.azm.ms/telegraf'            
| where Namespace == 'container.azm.ms/disk'            
| extend Tags = todynamic(Tags)            
| project TimeGenerated, ClusterId = Tags['container.azm.ms/clusterId'], Computer = tostring(Tags.hostName), Device = tostring(Tags.device), Path = tostring(Tags.path), DiskMetricName = Name, DiskMetricValue = Val   
| where ClusterId =~ clusterId       
| where DiskMetricName == 'used_percent'
| summarize AggregatedValue = max(DiskMetricValue) by bin(TimeGenerated, trendBinSize)
| where AggregatedValue >= 90
```

## <a name="create-an-alert-rule"></a>Criar uma regra de alerta

Siga essas etapas para criar um alerta de log no Azure Monitor usando uma das regras de pesquisa de log fornecidas anteriormente. Para criar usando um modelo ARM, consulte [criação de alerta de registro de exemplo usando o Modelo de recurso do Azure](../platform/alerts-log.md#sample-log-alert-creation-using-azure-resource-template).

>[!NOTE]
>O procedimento a seguir para criar uma regra de alerta para a utilização de recursos de contêiner exige que você mude para uma nova API de alertas de log, conforme descrito na [preferência da API do Switch para alertas de log](../platform/alerts-log-api-switch.md).
>

1. Faça login no [portal Azure](https://portal.azure.com).
2. No portal Azure, procure e selecione **espaços de trabalho do Log Analytics**.
3. Na sua lista de espaços de trabalho do Log Analytics, selecione o espaço de trabalho que suporta o Monitor Azure para contêineres. 
4. No painel do lado esquerdo, selecione **Logs** para abrir a página de logs do Monitor do Azure. Você usa esta página para escrever e executar consultas do Azure Log Analytics.
5. Na página **Logs,** cole uma das [consultas fornecidas](#resource-utilization-log-search-queries) anteriormente no campo **de consulta de Pesquisa** e selecione **Executar** para validar os resultados. Se você não executar esta etapa, a opção **+Novo alerta** não está disponível para seleção.
6. Selecione **+Novo alerta** para criar um alerta de log.
7. Na seção **Condição,** selecione **"Sempre \<que a pesquisa de log personalizado for lógica indefinida>** condição de log personalizado predefinida. O tipo de sinal **de pesquisa de log personalizado** é selecionado automaticamente porque estamos criando uma regra de alerta diretamente da página de logs do Azure Monitor.  
8. Cole uma das [consultas fornecidas](#resource-utilization-log-search-queries) anteriormente no campo **de consulta de pesquisa.**
9. Configure o alerta da seguinte forma:

    1. A partir da **lista de** desestatados, selecione **Medição métrica**. Uma medição métrica cria um alerta para cada objeto na consulta que tem um valor acima do nosso limite especificado.
    1. Para **Condição,** selecione **Maior do que**e digite **75** como um **limiar** inicial de linha de base para os alertas de utilização da CPU e da memória. Para o alerta de espaço em disco baixo, digite **90**. Ou digite um valor diferente que atenda aos seus critérios.
    1. Na seção **Alerta de gatilho baseado na** seção, **selecione Violações consecutivas**. Na lista de isenções, selecione **Maior do que**e digite **2**.
    1. Para configurar um alerta para a utilização da CPU ou da memória do contêiner, em **Aggregate,** selecione **ContainerName**. Para configurar o alerta de disco baixo do nó de cluster, selecione **ClusterId**.
    1. Na **seção Avaliado,** defina o valor do **período** para **60 minutos**. A regra será executada a cada 5 minutos e os registros de retorno que foram criados na última hora a partir do horário atual. Definir o período de tempo como uma ampla janela de contas para uma possível latência de dados. Também garante que a consulta retorne dados para evitar um falso negativo no qual o alerta nunca é acionado.

10. Selecione **Concluído** para completar a regra de alerta.
11. Digite um nome no campo **Nome da regra Alerta.** Especifique uma **descrição** que forneça detalhes sobre o alerta. E selecione um nível de gravidade apropriado das opções fornecidas.
12. Para ativar imediatamente a regra de alerta, aceite o valor padrão para **Ativar regra após a criação**.
13. Selecione um **grupo de ação** existente ou crie um novo grupo. Esta etapa garante que as mesmas ações sejam tomadas sempre que um alerta é acionado. Configure com base em como sua equipe de operações de TI ou DevOps gerencia incidentes.
14. Selecione **Criar regra de alerta** para completar a regra de alerta. Ela começa a ser executada imediatamente.

## <a name="next-steps"></a>Próximas etapas

- Exibir [exemplos de consulta de log](container-insights-log-search.md#search-logs-to-analyze-data) para ver consultas e exemplos pré-definidos para avaliar ou personalizar para alertar, visualizar ou analisar seus clusters.
- Para saber mais sobre o Azure Monitor e como monitorar outros aspectos do seu cluster Kubernetes, consulte [Ver o desempenho do cluster Kubernetes](container-insights-analyze.md) e visualizar a saúde do cluster [Kubernetes](container-insights-health.md).
