---
title: Alertas do Monitor Azure para VMs
description: Descreve como criar regras de alerta a partir de dados de desempenho coletados pelo Azure Monitor para VMs.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/23/2020
ms.openlocfilehash: 987537d8497b3d8f2728941334d8328320ec6997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289596"
---
# <a name="how-to-create-alerts-from-azure-monitor-for-vms"></a>Como criar alertas do Azure Monitor para VMs
[Alertas no Azure Monitor](../platform/alerts-overview.md) notificam-no proativamente de dados e padrões interessantes em seus dados de monitoramento. O Monitor Azure para VMs não inclui regras de alerta pré-configuradas, mas você pode criar o seu próprio com base nos dados que ele coleta. Este artigo fornece orientações sobre a criação de regras de alerta, incluindo um conjunto de consultas de amostra.


## <a name="alert-rule-types"></a>Tipos de regras de alerta
O Azure Monitor tem [diferentes tipos de regras de alerta](../platform/alerts-overview.md#what-you-can-alert-on) com base nos dados que estão sendo usados para criar o alerta. Todos os dados coletados pelo Azure Monitor para VMs são armazenados no Azure Monitor Logs, que suporta [alertas de log](../platform/alerts-log.md). Atualmente, não é possível usar [alertas métricos](../platform/alerts-log.md) com dados de desempenho coletados do Azure Monitor para VMs porque os dados não são coletados no Azure Monitor Metrics. Para coletar dados para alertas métricos, instale a [extensão de diagnóstico para](../platform/diagnostics-extension-overview.md) VMs windows ou o [agente Telegraf](../platform/collect-custom-metrics-linux-telegraf.md) para VMs Linux para coletar dados de desempenho em Métricas.

Existem dois tipos de alertas de log no Azure Monitor:

- [O número de alertas de resultados](../platform/alerts-unified-log.md#number-of-results-alert-rules) cria um único alerta quando uma consulta retorna pelo menos um número especificado de registros. Estes são ideais para dados não numéricos, tais como e eventos do Windows e Syslog coletados pelo [agente Log Analytics](../platform/log-analytics-agent.md) ou para analisar tendências de desempenho em vários computadores.
- [Os alertas de medição métrica](../platform/alerts-unified-log.md#metric-measurement-alert-rules) criam um alerta separado para cada registro em uma consulta que tem um valor que excede um limite definido na regra de alerta. Essas regras de alerta são ideais para dados de desempenho coletados pelo Azure Monitor para VMs, uma vez que eles podem criar alertas individuais para cada computador.


## <a name="alert-rule-walkthrough"></a>Passo a passo da regra de alerta
Esta seção percorre a criação de uma regra de alerta de medição métrica usando dados de desempenho do Monitor Azure para VMs. Você pode usar este processo básico com uma variedade de consultas de log para alertar em diferentes contadores de desempenho.

Comece criando uma nova regra de alerta após o procedimento em [Criar, exibir e gerenciar alertas de log usando o Azure Monitor](../platform/alerts-log.md). Para o **recurso,** selecione o espaço de trabalho log analytics que o Azure Monitor VMs usa em sua assinatura. Uma vez que o recurso de destino para regras de alerta de log é sempre um espaço de trabalho do Log Analytics, a consulta de log deve incluir qualquer filtro para determinadas máquinas virtuais ou conjuntos de escala de máquinas virtuais. 

Para a **Condição** da regra de alerta, use uma das consultas na [seção abaixo](#sample-alert-queries) como a **consulta pesquisar**. A consulta deve retornar uma propriedade numérica chamada *AggregatedValue*. Ele deve resumir os dados pelo computador para que você possa criar um alerta separado para cada máquina virtual que exceda o limite.

Na **lógica Alerta,** selecione **Medição métrica** e, em seguida, forneça um **valor limiar**. Em **Trigger Alert Based**On , especifique quantas vezes o limite deve ser excedido antes de um alerta ser criado. Por exemplo, você provavelmente não se importa se o processador excede um limite uma vez e depois retorna ao normal, mas você se importa se ele continua a exceder o limite sobre várias medições consecutivas.

A **seção Avaliado com base na** seção define com que frequência a consulta é executada e a janela de tempo para a consulta. No exemplo abaixo, a consulta será executada a cada 15 minutos e avaliará os valores de desempenho coletados nos últimos 15 minutos.


![Regra de alerta de medição métrica](media/vminsights-alerts/metric-measurement-alert.png)

## <a name="sample-alert-queries"></a>Consultas de alerta de amostra
As seguintes consultas podem ser usadas com uma regra de alerta de medição métrica usando dados de desempenho coletados pelo Azure Monitor para VMs. Cada um resume os dados por computador para que um alerta seja criado para cada computador com um valor que exceda o limite.

### <a name="cpu-utilization"></a>Utilização da CPU

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "Processor" and Name == "UtilizationPercentage" 
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="available-memory-in-mb"></a>Memória disponível em MB

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Memory" and Name == "AvailableMB"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="available-memory-in-percentage"></a>Memória disponível em porcentagem

```kusto
InsightsMetrics 
| where Origin == "vm.azm.ms" 
| where Namespace == "Memory" and Name == "AvailableMB" 
| extend TotalMemory = toreal(todynamic(Tags)["vm.azm.ms/memorySizeMB"])
| extend AvailableMemoryPercentage = (toreal(Val) / TotalMemory) * 100.0 
| summarize AggregatedValue = avg(AvailableMemoryPercentage) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---all-disks-on-each-computer"></a>Disco lógico usado - todos os discos em cada computador

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---individual-disks"></a>Disco lógico usado - discos individuais

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, Disk
```

### <a name="logical-disk-iops"></a>IOPS de disco lógico

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "TransfersPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) ), Computer, _ResourceId, Disk
```

### <a name="logical-disk-data-rate"></a>Taxa de dados de disco lógico

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "BytesPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) , Computer, _ResourceId, Disk
```

### <a name="network-interfaces-bytes-received---all-interfaces"></a>Interfaces de rede bytes recebidos - todas as interfaces

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="network-interfaces-bytes-received---individual-interfaces"></a>Interfaces de rede bytes recebidos - interfaces individuais

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="network-interfaces-bytes-sent---all-interfaces"></a>Interfaces de rede bytes enviados - todas as interfaces

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="network-interfaces-bytes-sent---individual-interfaces"></a>Interfaces de rede bytes enviados - interfaces individuais

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="virtual-machine-scale-set"></a>Conjunto de escala de máquina virtual
Modifique com seu ID de assinatura, grupo de recursos e nome de conjunto de escala de máquina virtual.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/my-vm-scaleset" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="specific-virtual-machine"></a>Máquina virtual específica
Modifique com seu ID de assinatura, grupo de recursos e nome vm.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId =~ "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m)
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-subscription"></a>Utilização da CPU para todos os recursos de computação em uma assinatura
Modifique com seu ID de assinatura.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" and (_ResourceId contains "/providers/Microsoft.Compute/virtualMachines/" or _ResourceId contains "/providers/Microsoft.Compute/virtualMachineScaleSets/")
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-resource-group"></a>Utilização da CPU para todos os recursos de computação em um grupo de recursos
Modifique com seu ID de assinatura e grupo de recursos.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/"
or _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId

```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [alertas no Azure Monitor](../platform/alerts-overview.md).
- Saiba mais sobre [consultas de log usando dados do Azure Monitor para VMs](vminsights-log-search.md).
