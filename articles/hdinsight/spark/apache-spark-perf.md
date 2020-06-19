---
title: Otimizar os trabalhos do Spark para desempenho – Microsoft Azure HDInsight
description: Mostre estratégias comuns para o melhor desempenho de clusters do Apache Spark no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/21/2020
ms.openlocfilehash: f92a351087670ce0b37921a496eabfa883a3b1fc
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83780101"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>Otimizar trabalhos do Apache Spark no HDInsight

Este artigo fornece uma visão geral das estratégias para otimizar trabalhos do Apache Spark no Azure HDInsight.

## <a name="overview"></a>Visão geral

O desempenho dos seus trabalhos do Apache Spark depende de vários fatores. Esses fatores de desempenho incluem: como os dados são armazenados, como o cluster é configurado e as operações que são usadas durante o processamento dos dados.

Desafios comuns que você pode enfrentar incluem restrições de memória devido a executores de tamanho inadequado, operações de longa execução e tarefas que resultam em operações cartesianas.

Também há várias estratégias que podem ajudá-lo a superar esses desafios, como o cache, e permitir a distorção de dados.

Em cada um dos artigos a seguir, você poderá encontrar desafios e soluções comuns para um aspecto diferente da otimização do Spark.

* [Otimizar armazenamento de dados](optimize-data-storage.md)
* [Otimizar processamento de dados](optimize-data-processing.md)
* [Otimizar uso da memória](optimize-memory-usage.md)
* [Otimizar configuração do cluster](optimize-cluster-configuration.md)

## <a name="next-steps"></a>Próximas etapas

* [Depurar trabalhos do Apache Spark em execução no Azure HDInsight](apache-spark-job-debugging.md)
* [Gerenciar recursos para um cluster do Apache Spark no HDInsight](apache-spark-resource-manager.md)
* [Definir as configurações do Apache Spark](apache-spark-settings.md)
