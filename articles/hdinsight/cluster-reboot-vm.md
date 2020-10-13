---
title: Reinicializar VMs para clusters do Azure HDInsight
description: Saiba como reinicializar VMs sem resposta para clusters do Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/22/2020
ms.openlocfilehash: 2177e74bd627e80ea1afbcacaf85baf4e030834c
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91928972"
---
# <a name="reboot-vms-for-hdinsight-clusters"></a>Reinicializar VMs para clusters HDInsight

Os clusters do Azure HDInsight contêm grupos de VMs (máquinas virtuais) como nós de cluster. Para clusters de execução longa, esses nós podem ficar sem resposta por vários motivos. Este artigo descreve como reinicializar VMs sem resposta em um cluster HDInsight.

## <a name="when-to-reboot"></a>Quando reinicializar

> [!WARNING]
> Quando você reinicializa as VMs em um cluster, o nó não está disponível para uso e os serviços no nó devem ser reiniciados.

Quando um nó é reinicializado, o cluster pode se tornar não íntegro e os trabalhos podem ficar lentos ou falhar. Se você estiver tentando reinicializar o nó de cabeçalho ativo, todos os trabalhos em execução serão interrompidos. Você não poderá enviar trabalhos para o cluster até que os serviços estejam ativos e em execução novamente. Por esses motivos, você deve reinicializar VMs somente quando necessário. Considere reinicializar VMs quando:

- Você não pode usar o SSH para entrar no nó, mas ele responde a pings.
- O nó de trabalho está inoperante sem uma pulsação na interface do usuário do amAmbari.
- O disco temporário está cheio no nó.
- A tabela de processo na VM tem muitas entradas em que o processo foi concluído, mas está listado com "estado encerrado".

> [!NOTE]
> Não há suporte para a reinicialização de VMs em clusters **HBase** e **Kafka** porque a reinicialização pode fazer com que os dados sejam perdidos.

## <a name="use-powershell-to-reboot-vms"></a>Usar o PowerShell para reinicializar VMs

São necessárias duas etapas para usar a operação de reinicialização de nó: listar nós e reiniciar nós.

1. Listar nós. Você pode obter a lista de nós de cluster em [Get-AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsighthost).

      ```
      Get-AzHDInsightHost -ClusterName myclustername
      ```

1. Reinicie os hosts. Depois de obter os nomes dos nós que você deseja reinicializar, reinicie os nós usando [Restart-AzHDInsightHost](https://docs.microsoft.com/powershell/module/az.hdinsight/restart-azhdinsighthost).

      ```
      Restart-AzHDInsightHost -ClusterName myclustername -Name wn0-myclus, wn1-myclus
      ```

## <a name="use-a-rest-api-to-reboot-vms"></a>Usar uma API REST para reinicializar VMs

Você pode usar o recurso **experimentar** no documento da API para enviar solicitações ao HDInsight. São necessárias duas etapas para usar a operação de reinicialização de nó: listar nós e reiniciar nós.

1. Listar nós. Você pode obter a lista de nós de cluster da API REST ou no Ambari. Para obter mais informações, consulte a [lista do HDInsight hospeda a operação da API REST](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines/listhosts).

    ```
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/listHosts?api-version=2018-06-01-preview
    ```

1. Reinicie os hosts. Depois de obter os nomes dos nós que você deseja reinicializar, reinicie os nós usando a API REST para reinicializar os nós. O nome do nó segue o padrão de *NodeType (WN/HN/ZK/GW)*  +  *x*os  +  *primeiros seis caracteres do nome do cluster*. Para obter mais informações, consulte [host de reinicialização do HDInsight operação de API REST](https://docs.microsoft.com/rest/api/hdinsight/virtualmachines/restarthosts).

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
