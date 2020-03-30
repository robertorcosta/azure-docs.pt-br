---
title: Como selecionar o tamanho de VM certo para o cluster Azure HDInsight
description: Saiba como selecionar o tamanho de VM certo para o seu cluster HDInsight.
keywords: tamanhos de vm, tamanhos de cluster, configuração de cluster
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: a21e8d6c76c93b3084619c09f6a7664a25c1929c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73682198"
---
# <a name="selecting-the-right-vm-size-for-your-azure-hdinsight-cluster"></a>Selecionando o tamanho de VM certo para o cluster Azure HDInsight

Este artigo discute como selecionar o tamanho de VM certo para os vários nomes do seu cluster HDInsight. 

Comece entendendo como as propriedades de uma máquina virtual, como processamento de CPU, tamanho de RAM e latência de rede afetarão o processamento de suas cargas de trabalho. Em seguida, pense em sua aplicação e como ela combina com o que diferentes famílias de VM são otimizadas. Certifique-se de que a família VM que você gostaria de usar é compatível com o tipo de cluster que você planeja implantar. Para obter uma lista de todos os tamanhos de VM suportados e recomendados para cada tipo de cluster, consulte [configurações de nó suportadas pelo Azure HDInsight](hdinsight-supported-node-configuration.md). Por fim, você pode usar um processo de benchmarking para testar algumas cargas de trabalho de amostra e verificar qual SKU dentro dessa família é o certo para você.

Para obter mais informações sobre o planejamento de outros aspectos do cluster, como selecionar um tipo de armazenamento ou tamanho de cluster, consulte [O planejamento de capacidade para clusters HDInsight](hdinsight-capacity-planning.md).

## <a name="vm-properties-and-big-data-workloads"></a>Propriedades vm e cargas de trabalho de big data

O tamanho e o tipo da VM são determinados pelo poder de processamento da CPU, pelo tamanho da RAM e pela latência da rede:

- CPU: o tamanho da VM determina o número de núcleos. Quanto mais núcleos, maior o grau de computação paralela que cada nó pode alcançar. Além disso, alguns tipos de VM tem núcleos mais rápidos.

- RAM: o tamanho da VM também determina a quantidade de RAM disponível na VM. Para cargas de trabalho que armazenam dados na memória para processamento em vez de ler no disco, verifique se os nós de trabalho têm memória suficiente que se ajuste aos dados.

- Rede: Para a maioria dos tipos de cluster, os dados processados pelo cluster não estão no disco local, mas sim em um serviço de armazenamento externo, como data lake storage ou Azure Storage. Considere a largura de banda e taxa de transferência da rede entre a VM do nó e o serviço de armazenamento. Geralmente, a largura de banda da rede disponível para uma VM aumenta com tamanhos maiores. Para mais detalhes, consulte [Visão geral sobre tamanhos de VM](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

## <a name="understanding-vm-optimization"></a>Entendendo a otimização de VM

As famílias de máquinas virtuais no Azure são otimizadas para atender a diferentes casos de uso. Na tabela abaixo, você pode encontrar alguns dos casos de uso mais populares e as famílias de VM que combinam com eles.

| Type                     | Tamanhos           |    Descrição       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Nível de entrada](../virtual-machines/linux/sizes-general.md)          | A, Av2  | Tenha as configurações de desempenho e memória da CPU mais adequadas para cargas de trabalho de nível de entrada, como desenvolvimento e teste. Eles são econômicos e fornecem uma opção de baixo custo para começar com o Azure. |
| [Propósito geral](../virtual-machines/linux/sizes-general.md)          | D, DSv2, Dv2  | Relação equilibrada de CPU/memória. Ideal para teste e desenvolvimento, bancos de dados pequenos a médios e servidores Web de tráfego baixo a médio. |
| [Otimizado para computação](../virtual-machines/linux/sizes-compute.md)        | F           | Alta relação de CPU/memória. Boa para servidores web de tráfego médio, dispositivos de rede, processos de lote e servidores de aplicativo.        |
| [Memória otimizada](../virtual-machines/linux/sizes-memory.md)         | Esv3  | Alta relação de memória/CPU. Ótima para servidores de banco de dados relacionais, caches médios a grandes e análises na memória.                 |

- Para obter informações sobre preços de instâncias vm disponíveis em regiões suportadas pelo HDInsight, consulte [HDInsight Pricing](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="cost-saving-vm-types-for-light-workloads"></a>Economia de custos tipos de VM para cargas de trabalho leves

Se você tem requisitos de processamento leve, a [série F](https://azure.microsoft.com/blog/f-series-vm-size/) pode ser uma boa escolha para começar com o HDInsight. A um preço de lista inferior por hora, a série F é o melhor valor de preço/desempenho no portfólio do Azure com base na ACU (Unidade de Computação do Azure) por vCPU.

A tabela a seguir descreve os tipos de cluster e tipos de nó, que podem ser criados com as VMs da série Fsv2.

| Tipo de cluster | Versão | Nó do trabalhador | Nó da cabeça | Nódulo zookeeper |
|---|---|---|---|---|
| Spark | Todos | F4 e acima | não | não |
| O Hadoop | Todos | F4 e acima | não | não |
| Kafka | Todos | F4 e acima | não | não |
| HBase | Todos | F4 e acima | não | não |
| II | desabilitado | não | não | não |
| Storm | desabilitado | não | não | não |
| Serviço do Machine Learning | SOMENTE HDI 3.6 | F4 e acima | não | não |

Para ver as especificações de cada SKU da série F, consulte [os tamanhos de VM da série F](https://azure.microsoft.com/blog/f-series-vm-size/).

## <a name="benchmarking"></a>Parâmetros de comparação

Benchmarking é o processo de execução de cargas de trabalho simuladas em diferentes VMs para medir o desempenho que eles executarão para suas cargas de trabalho de produção. 

Para obter mais informações sobre benchmarking para SKUs VM e tamanhos de cluster, consulte [O planejamento da capacidade do Cluster no Azure HDInsight ](hdinsight-capacity-planning.md#choose-the-vm-size-and-type).

## <a name="next-steps"></a>Próximas etapas

- [Configurações de nó suportadas pelo Azure HDInsight](hdinsight-supported-node-configuration.md)
- [Tamanhos das máquinas virtuais do Linux no Azure](../virtual-machines/linux/sizes.md)
