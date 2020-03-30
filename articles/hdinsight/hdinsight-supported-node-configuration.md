---
title: Configurações de nó suportadas pelo Azure HDInsight
description: Aprenda as configurações mínimas e recomendadas para os nós de cluster HDInsight.
keywords: tamanhos de vm, tamanhos de cluster, configuração de cluster
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: e75146266568001d8fee7be26898ac8bdfffb7fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77484778"
---
# <a name="what-are-the-default-and-recommended-node-configurations-for-azure-hdinsight"></a>Quais são as configurações padrão e recomendadas de nó para o Azure HDInsight?

Este artigo discute configurações padrão e recomendadas de nó para clusters Azure HDInsight.

## <a name="default-and-minimum-recommended-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Configuração padrão e mínima recomendada de nó e tamanhos de máquinas virtuais para clusters

As tabelas a seguir listam tamanhos padrão e máquina virtual recomendada (VM) para clusters HDInsight.  Essas informações são necessárias para entender os tamanhos de VM para usar quando você está criando scripts PowerShell ou Azure CLI para implantar clusters HDInsight.

Se você precisar de mais de 32 nós de trabalhadores em um cluster, selecione um tamanho de nó de cabeça com pelo menos 8 núcleos e 14 GB de RAM. 

Os únicos tipos de cluster que possuem discos de dados são os clusters Kafka e HBase com o recurso Accelerated Writes ativado. O HDInsight suporta tamanhos de disco P30 e S30 nesses cenários.

As especificações de todos os tipos mínimos de VM recomendados neste documento são resumidas na tabela a seguir.

| Tamanho              | vCPU | Memória: GiB | Armazenamento temporário (SSD) GiB | Taxa de transferência máxima de armazenamento temporário: IOPS / MBps de leitura / MBps de gravação | Discos de dados máximos / taxa de transferência: IOPS | Máximo de NICs/Largura de banda de rede esperado (Mbps) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D3_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 16 / 16x500           | 4 / 3000                                       |
| Standard_D4_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 32 / 32x500           | 8 / 6000                                       |
| Standard_D5_v2 | 16   | 56          | 800                    | 48000 / 750 / 375                                          | 64 / 64x500           | 8 / 12000                                    |
| Standard_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16 x 500                         | 4 / 3000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32 x 500                       | 8 / 6000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 12000          |
| Standard_A1_v2  | 1         | 2           | 10             | 1000 / 20 / 10                                           | 2 / 2 x 500               | 2 / 250                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000 / 40 / 20                                           | 4 / 4 x 500               | 2 / 500                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000 / 80 / 40                                           | 8 / 8 x 500               | 4 / 1000                     |

Para obter mais detalhes sobre as especificações de cada tipo de VM, consulte os seguintes documentos:

* [Tamanhos de máquinas virtuais de uso geral: Série Dv2 1-5](../virtual-machines/dv2-dsv2-series.md)
* [Tamanhos otimizados de memória da máquina virtual: Série Dv2 11-15](../virtual-machines/dv2-dsv2-series-memory.md)
* [Tamanhos de máquinas virtuais de uso geral: Série Av2 1-8](../virtual-machines/av2-series.md)

### <a name="all-supported-regions-except-brazil-south-and-japan-west"></a>Todas as regiões apoiadas, exceto o Sul do Brasil e o Japão oeste

> [!Note]
> Para obter o identificador SKU para uso em `Standard_` powershell e outros scripts, adicione ao início de todas as SKUs VM nas tabelas abaixo. Por exemplo, `D12_v2` `Standard_D12_v2`se tornaria.

| Tipo de cluster | O Hadoop | HBase | Consulta Interativa | Storm | Spark | ML Server | Kafka |
|---|---|---|---|---|---|---|---|
| Cabeçalho: tamanho padrão da VM | D12_v2 | D12_v2 | D13_v2 | A4_v2 | D12_v2, <br/>D13_v2* | D12_v2 | D3_v2 |
| Cabeça: tamanhos mínimos recomendados de VM | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2, <br/>D13_v2* | D12_v2 | D3_v2 |
| Trabalho: tamanho de VM padrão | D4_v2 | D4_v2 | D14_v2 | D3_v2 | D13_v2 | D4_v2 | 4 D12_v2 com 2 discos S30 por corretora |
| Trabalhador: tamanhos mínimos recomendados de VM | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 | D3_v2 |
| Zookeeper: tamanho de VM padrão |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 | A4_v2 |
| ZooKeeper: tamanhos mínimos recomendados de VM |  | A4_v2 | A4_v2 | A2_v2 |  | A2_v2 | A4_v2 |
| Serviços ML: tamanho padrão de VM |  |  |  |  |  | D4_v2 |  |
| Serviços ML: tamanho mínimo recomendado de VM |  |  |  |  |  | D4_v2 |  |

\*= Tamanhos de VM para clusters Spark Enterprise Security Package (ESP)

### <a name="brazil-south-and-japan-west-only"></a>Brasil sul e Japão apenas oeste

| Tipo de cluster | O Hadoop | HBase | Consulta Interativa | Storm | Spark | ML Services |
|---|---|---|---|---|---|---|
| Cabeçalho: tamanho padrão da VM | D12 | D12 | D13 | A4_v2 | D12 | D12 |
| Cabeça: tamanhos mínimos recomendados de VM | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2 | D12_v2 |
| Trabalho: tamanho de VM padrão | D4 | D4 | D14 | D3 | D13 | D4 |
| Trabalhador: tamanhos mínimos recomendados de VM | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 |
| Zookeeper: tamanho de VM padrão |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| ZooKeeper: tamanhos mínimos recomendados de VM |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| Serviços ML: tamanhos vm padrão |  |  |  |  |  | D4 |
| Serviços ML: tamanhos mínimos recomendados de VM |  |  |  |  |  | D4_v2 |

> [!NOTE]
> - O cabeçalho é conhecido como *Nimbus* para o tipo de cluster Storm.
> - A função de trabalho é conhecida como *Supervisor* para o tipo de cluster Storm.
> - A função de trabalho é conhecida como *Região* para o tipo de cluster HBase.

## <a name="next-steps"></a>Próximas etapas

* [Quais são os componentes e versões do Apache Hadoop disponíveis com o HDInsight?](hdinsight-component-versioning.md)
