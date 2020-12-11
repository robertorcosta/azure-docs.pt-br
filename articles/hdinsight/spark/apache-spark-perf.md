---
title: Otimizar os trabalhos do Spark para desempenho – Microsoft Azure HDInsight
description: Mostre estratégias comuns para o melhor desempenho de clusters do Apache Spark no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/21/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: d808339dd842e88e74efce5d56d12bc5250eb238
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97029287"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>Otimizar trabalhos do Apache Spark no HDInsight

Este artigo fornece uma visão geral das estratégias para otimizar trabalhos do Apache Spark no Azure HDInsight.

## <a name="overview"></a>Visão geral

O desempenho dos seus trabalhos do Apache Spark depende de vários fatores. Esses fatores de desempenho incluem: como os dados são armazenados, como o cluster é configurado e as operações que são usadas durante o processamento dos dados.

Os desafios comuns que você pode enfrentar incluem: restrições de memória devido a executores de tamanho inadequado, operações de longa execução e tarefas que resultam em operações cartesianas.

Também há muitas otimizações que podem ajudá-lo a superar esses desafios, como o Caching, e permitir a distorção de dados.

Em cada um dos artigos a seguir, você pode encontrar informações sobre diferentes aspectos da otimização do Spark.

* [Otimização do armazenamento de dados para Apache Spark](optimize-data-storage.md)
* [Otimização do processamento de dados para Apache Spark](optimize-data-processing.md)
* [Otimização do uso de memória para Apache Spark](optimize-memory-usage.md)
* [Otimizar a configuração do cluster HDInsight para Apache Spark](optimize-cluster-configuration.md)

## <a name="next-steps"></a>Próximas etapas

* [Depurar trabalhos do Apache Spark em execução no Azure HDInsight](apache-spark-job-debugging.md)
* [Gerenciar recursos para um cluster do Apache Spark no HDInsight](apache-spark-resource-manager.md)
* [Definir as configurações do Apache Spark](apache-spark-settings.md)
