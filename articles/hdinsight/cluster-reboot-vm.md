---
title: Reinicializar VMs para o cluster HDInsight do Azure
description: Saiba como reinicializar VMs sem resposta para o cluster HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/22/2020
ms.openlocfilehash: c0f0bd9eb423b3de6a602647dff93fd9fce6e13e
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86077007"
---
# <a name="reboot-vms-for-hdinsight-cluster"></a>Reinicializar VMs para o cluster HDInsight

Os clusters HDInsight contêm grupos de VMs como nós de cluster. Para clusters de longa execução, esses nós podem ficar sem resposta por vários motivos. Este artigo descreve como reinicializar VMs sem resposta em um cluster HDInsight.

## <a name="when-to-reboot"></a>Quando reinicializar

> [!WARNING]  
> A reinicialização de VMs no cluster traz o tempo de inatividade do nó e a reinicialização de serviços no nó. 

Enquanto o nó está sendo reinicializado, o cluster pode se tornar não íntegro, os trabalhos podem ficar lentos ou falhar. Se você estiver tentando reinicializar o nó de cabeçalho ativo, todos os trabalhos em execução serão eliminados e você não poderá enviar trabalhos para o cluster até que os serviços estejam ativos e em execução novamente. Você deve considerar a reinicialização das VMs somente quando necessário. Aqui estão algumas diretrizes para quando considerar a reinicialização das VMs.

- Não é possível SSH no nó, mas ele responde a pings.
- O nó de trabalho está inoperante sem pulsação na interface do usuário do amAmbari.
- O disco temporário está cheio no nó.
- A tabela de processo na VM tem muitas entradas em que o processo foi concluído, mas está listado com "estado encerrado".

> [!WARNING]  
> Você deve ter mais cuidado ao reinicializar VMs para **HBase** e **Kafka** clustes, pois isso pode causar perda de dados.

## <a name="use-powershell-to-reboot-vms"></a>Usar o PowerShell para reinicializar VMs

Há duas etapas necessárias para usar a operação de reinicialização de nó: listar nós e reiniciar nós.

1. Listar nós. Você pode obter a lista de nós de cluster por meio de [Get-AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsighthost). 

  ```
  Get-AzHDInsightHost -ClusterName myclustername
  ```

2. Reinicie os hosts. Depois de obter os nomes dos nós que você deseja reinicializar, reinicie os nós usando [Restart-AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/restart-azhdinsighthost).

  ```
  Restart-AzHDInsightHost -ClusterName myclustername -Name wn0-myclus, wn1-myclus
  ```

## <a name="use-rest-api-to-reboot-vms"></a>Usar a API REST para reinicializar VMs

Você pode usar o recurso **experimentar** no documento da API para enviar solicitações ao HDInsight. Há duas etapas necessárias para usar a operação de reinicialização de nó: listar nós e reiniciar nós.

1. Listar nós. Você pode obter a lista de nós de cluster da API REST ou no Ambari. Mais detalhes podem ser encontrados na [lista do HDInsight hospeda a operação da API REST](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines/listhosts).

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/listHosts?api-version=2018-06-01-preview
    ```

2. Reinicie os hosts. Depois de obter os nomes dos nós que você deseja reinicializar, use a API REST de reinicialização de nós para reinicializar os nós. O nome do nó segue o padrão de **"NodeType (WN/HN/ZK/GW)" + "x" + "primeiros 6 caracteres do nome do cluster"**. Mais detalhes podem ser encontrados na [operação de reinicialização do HDInsight hospeda a API REST](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines/restarthosts).

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/restartHosts?api-version=2018-06-01-preview
    ```

Os nomes reais dos nós que você deseja reinicializar são especificados em uma matriz JSON no corpo da solicitação.

```json
[
  "wn0-abcdef",
  "zk1-abcdef"
]
```

## <a name="next-steps"></a>Próximas etapas

* [Restart-AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/restart-azhdinsighthost)
* [API REST de máquinas virtuais do HDInsight](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines)
* [API REST do HDInsight](https://docs.microsoft.com/rest/api/hdinsight/)
