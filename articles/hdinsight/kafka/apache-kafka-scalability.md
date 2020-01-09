---
title: Escala de aumento do Apache Kafka - Azure HDInsight
description: Saiba como configurar discos gerenciados para o cluster Apache Kafka no Azure HDInsight para aumentar a escalabilidade.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/09/2019
ms.openlocfilehash: 5ce8414376862b66314f754252aba3ab6afdaf25
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435309"
---
# <a name="configure-storage-and-scalability-for-apache-kafka-on-hdinsight"></a>Configurar o armazenamento e a escalabilidade para o Apache Kafka no HDInsight

Saiba como configurar o número de discos gerenciados usados pelo [Apache Kafka](https://kafka.apache.org/) no HDInsight.

O Kafka no HDInsight usa o disco local das máquinas virtuais no cluster HDInsight. Como o Kafka tem E/S bastante pesadas, os [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md) são usados para fornecer a alta taxa de transferência e fornecer mais armazenamento por nó. Se os discos rígidos virtuais (VHD) tradicionais tiverem sido usados para o Kafka, cada nó estará limitado a 1 TB. Com os discos gerenciados, você pode usar vários discos para alcançar 16 TB para cada nó no cluster.

O diagrama a seguir fornece uma comparação entre o Kafka no HDInsight antes dos discos gerenciados e o Kafka no HDInsight com os discos gerenciados:

![Kafka com arquitetura de Managed disks](./media/apache-kafka-scalability/kafka-with-managed-disks-architecture.png)

## <a name="configure-managed-disks-azure-portal"></a>Configurar discos gerenciados: portal do Azure

1. Siga as etapas de [Criar um cluster HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md) para compreender as etapas comuns para criar um cluster usando o portal. Não conclua o processo de criação do Portal.

2. Na seção **configuração de preços do &** , use o campo __número de nós__ para configurar o número de discos.

    > [!NOTE]  
    > O tipo de disco gerenciado pode ser __Standard__ (HDD) ou __Premium__ (SSD). Os discos Premium são usados com as VMs das séries DS e GS. Todos os outros tipos VM usam o padrão.

    ![seção tamanho do cluster com os discos por nó de trabalho realçados](./media/apache-kafka-scalability/azure-portal-cluster-configuration-pricing-kafka.png)

## <a name="configure-managed-disks-resource-manager-template"></a>Configurar discos gerenciados: modelo do Gerenciador de Recursos

Para controlar o número de discos usados por nós de trabalho em um cluster do Kafka, use a seção a seguir do modelo:

```json
"dataDisksGroups": [
    {
        "disksPerNode": "[variables('disksPerWorkerNode')]"
    }
    ],
```

Você pode encontrar um modelo completo que demonstra como configurar discos gerenciados em [https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json](https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json).

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre como trabalhar com o Apache Kafka no HDInsight, consulte os seguintes documentos:

* [Use MirrorMaker para criar uma réplica de Apache Kafka no HDInsight](apache-kafka-mirroring.md)
* [Use o Apache Storm com o Apache Kafka no HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Usar o Apache Spark com o Apache Kafka no HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Conectar-se ao Apache Kafka por meio de uma Rede Virtual do Azure](apache-kafka-connect-vpn-gateway.md)

* [Blog do HDInsight em discos gerenciados com o Apache Kafka](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/)
