---
title: Monitoramento e diagnóstico em aplicativos de malha de malha de malha de serviço do Azure
description: Saiba mais sobre como monitorar e diagnosticar aplicativos na Malha do Service Fabric no Azure.
author: srrengar
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter
ms.openlocfilehash: 247a1de4d00668371337295616d31caf101f0cc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75498151"
---
# <a name="monitoring-and-diagnostics"></a>Monitoramento e diagnóstico
A Malha do Microsoft Azure Service Fabric é um serviço totalmente gerenciado que permite aos desenvolvedores implantar aplicativos de microsserviços sem gerenciar máquinas virtuais, armazenamento ou rede. Monitoramento e diagnóstico para a malha de Service Fabric é categorizado em três principais tipos de dados de diagnóstico:

- Logs de aplicativos - estes são definidos como os logs de aplicativos em contêineres, com base em como você tiver instrumentado seu aplicativo (por exemplo, logs de docker)
- Eventos de plataforma – eventos da plataforma Malha relevantes à sua operação de contêiner, incluindo atualmente encerramento, desativação e a ativação de contêiner.
- Métricas de contêiner - as métricas de desempenho e a utilização de recursos para seus contêineres (estatísticas do docker)

Este artigo discute as opções de monitoramento e diagnóstico para a versão de visualização mais recente disponível.

## <a name="application-logs"></a>Logs de aplicativo

Você pode exibir os logs de docker de seus contêineres implantados, em uma base por contêiner. No modelo de aplicativo de Malha do Service Fabric, cada contêiner é um pacote de código em seu aplicativo. Para ver os logs associados com um pacote de códigos, use o seguinte comando:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> Você pode usar o comando "az mesh service-replica" para obter o nome da réplica. Os nomes das réplicas estão incrementando inteiros de 0.

Aqui é com o que isso se parece para ver os logs do contêiner do aplicativo de votação VotingWeb.Code:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>Métricas de contêineres 

O ambiente Mesh expõe um punhado de métricas indicando o desempenho dos recipientes. As seguintes métricas estão disponíveis através do portal Azure e do monitor Azure CLI:

| Métrica | Descrição | Unidades|
|----|----|----|
| CpuUtilização | Cpu/Alocação atual como porcentagem | % |
| MemóriaUtilização | ActualMem/AllocatedMem em percentagem | % |
| Cpu alocada | Cpu alocado conforme modelo do Azure Resource Manager | Miliscores |
| Memória alocada | Memória alocada conforme modelo do Azure Resource Manager | MB |
| Cpu real | Uso da CPU | Miliscores |
| Memória real | Uso de memória | MB |
| Status do contêiner | 0 - Inválido: O status do contêiner é desconhecido <br> 1 - Pendente: O contêiner tem programado para começar <br> 2 - Início: O contêiner está em processo de partida <br> 3 - Iniciado: O contêiner começou com sucesso <br> 4 - Parada: O contêiner está sendo parado <br> 5 - Parado: O contêiner parou com sucesso | N/D |
| Status do aplicativo | 0 - Desconhecido: O status não é recuperável <br> 1 - Pronto: O aplicativo está sendo executado com sucesso <br> 2 - Atualização: Há um upgrade em andamento <br> 3 - Criação: O aplicativo está sendo criado <br> 4 - Exclusão: O aplicativo está sendo excluído <br> 5 - Falha: O aplicativo falhou em implantar | N/D |
| Status de serviço | 0 - Inválido: O serviço atualmente não possui um estado de saúde <br> 1 - Ok: O serviço é saudável  <br> 2 - Aviso: Pode haver algo errado exigindo investigação <br> 3 - Erro: Há algo errado que precisa ser investigado <br> 4 - Desconhecido: O status não é recuperável | N/D |
| ServiceReplicaStatus | 0 - Inválido: A réplica não possui atualmente um estado de saúde <br> 1 - Ok: O serviço é saudável  <br> 2 - Aviso: Pode haver algo errado exigindo investigação <br> 3 - Erro: Há algo errado que precisa ser investigado <br> 4 - Desconhecido: O status não é recuperável | N/D | 
| Contagem de reinicialização | Número de reinicializações de contêineres | N/D |

> [!NOTE]
> Os valores ServiceStatus e ServiceReplicaStatus são os mesmos do [HealthState](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet) in Service Fabric. 

Cada métrica está disponível em diferentes dimensões para que você possa ver agregados em diferentes níveis. A lista atual de dimensões é a seguinte:

* ApplicationName
* ServiceName
* ServiceReplicaName
* Codinome do Pacote

> [!NOTE]
> A dimensão CodePackageName não está disponível para aplicativos Linux. 

Cada dimensão corresponde a diferentes componentes do [modelo service fabric application](service-fabric-mesh-service-fabric-resources.md#applications-and-services)

### <a name="azure-monitor-cli"></a>Azure Monitor CLI

Uma lista completa de comandos está disponível nos [docs do Azure Monitor CLI,](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list) mas incluímos alguns exemplos úteis abaixo 

Em cada exemplo, o ID de recurso segue esse padrão

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* Utilização da CPU dos recipientes em um aplicativo

```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* Utilização de memória para cada réplica de serviço
```cli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* Reinicializa para cada contêiner em uma janela de 1 hora 
```cli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* Utilização média da CPU em serviços chamados "VotingWeb" em uma janela de 1 hora
```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z --aggregation "Average" --filter "ServiceName eq 'VotingWeb'"
``` 

### <a name="metrics-explorer"></a>Metrics Explorer

O metrics explorer é uma lâmina no portal em que você pode visualizar todas as métricas para o seu aplicativo Mesh. Esta lâmina está acessível na página do aplicativo no portal e na lâmina do monitor do Azure, a última das quais você pode usar para visualizar métricas para todos os seus recursos do Azure que suportam o Monitor Do Azure. 

![Metrics Explorer](./media/service-fabric-mesh-monitoring-diagnostics/metricsexplorer.png)


<!--
### Container Insights

In addition to the metrics explorer, we also have a dashboard available out of the box that shows sample metrics over time under the Insights blade in the application's page in the portal. 

![Container Insights](./media/service-fabric-mesh-monitoring-diagnostics/containerinsights.png)
-->

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre Malha do Service Fabric, leia [visão geral sobre Malha do Service Fabric](service-fabric-mesh-overview.md).
* Para saber mais sobre os comandos de métricas do Azure Monitor, confira os [docs do Azure Monitor CLI](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list).
