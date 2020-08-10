---
title: Notas da versão mais recente do Microsoft Azure HDInsight
description: Notas da versão mais recente do Microsoft Azure HDInsight. Obtenha detalhes e dicas de desenvolvimento para Hadoop, Spark, Microsoft R Server, Hive e muito mais.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/09/2020
ms.openlocfilehash: 82a66d88b3c7c3492e7cb90116ff8ff8e61ca1b0
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88036582"
---
# <a name="azure-hdinsight-release-notes"></a>Notas de versão do Azure HDInsight

Este artigo fornece informações sobre as atualizações de versão **mais recentes** do Azure HDInsight. Para obter informações sobre versões anteriores, consulte o [Arquivo morto de notas de versão do HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Resumo

O Microsoft Azure HDInsight é um dos serviços mais populares entre os clientes empresariais para análise de software livre no Azure.

## <a name="release-date-08092020"></a>Data de lançamento: 08/09/2020

Esta versão se aplica somente ao HDInsight 4,0. A versão do HDInsight é disponibilizada para todas as regiões durante vários dias. A data de lançamento mostrada aqui indica a data de lançamento da primeira região. Se você não vir as alterações abaixo, aguarde até que a liberação seja ativada em sua região em vários dias.

## <a name="new-features"></a>Novos recursos
### <a name="support-for-sparkcruise"></a>Suporte para SparkCruise
SparkCruise é um sistema de reutilização de computação automática para Spark. Ele seleciona subexpressões comuns para materializar com base na carga de trabalho de consulta anterior. O SparkCruise materializa essas subexpressões como parte do processamento de consulta e a reutilização de computação é aplicada automaticamente em segundo plano. Você pode se beneficiar do SparkCruise sem nenhuma modificação no código do Spark.
 
### <a name="support-hive-view-for-hdinsight-40"></a>Suporte à exibição do hive para o HDInsight 4,0
A exibição do hive do Apache Ambari foi projetada para ajudá-lo a criar, otimizar e executar consultas do hive a partir do seu navegador da Web. A exibição do hive tem suporte nativo para clusters HDInsight 4,0 a partir desta versão. Ele não se aplica a clusters existentes. Você precisa remover e recriar o cluster para obter a exibição interna do hive.
 
### <a name="support-tez-view-for-hdinsight-40"></a>Suporte à exibição do tez para o HDInsight 4,0
Apache Tez exibição é usada para rastrear e depurar a execução do trabalho do hive tez. A exibição tez tem suporte nativo para o HDInsight 4,0 a partir desta versão. Ele não se aplica a clusters existentes. Você precisa remover e recriar o cluster para obter a exibição tez interna.

## <a name="deprecation"></a>Reprovação
### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>Substituição do Spark 2.1 e 2.2 no cluster do Spark do HDInsight 3.6
A partir de julho de 1 2020, os clientes não podem criar novos clusters Spark com Spark 2,1 e 2,2 no HDInsight 3,6. Os clusters existentes serão executados como estão, sem o suporte da Microsoft. Considere a possibilidade de migrar para o Spark 2.3 no HDInsight 3.6 até 30 de junho de 2020 para evitar a interrupção potencial do sistema/suporte.
 
### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>Substituição do Spark 2.3 no cluster do Spark do HDInsight 4.0
A partir de julho de 1 2020, os clientes não podem criar novos clusters Spark com o Spark 2,3 no HDInsight 4,0. Os clusters existentes serão executados como estão, sem o suporte da Microsoft. Considere a possibilidade de migrar para o Spark 2.4 no HDInsight 4.0 até 30 de junho de 2020 para evitar a interrupção potencial do sistema/suporte.
 
### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>Substituição do Kafka 1.1 no cluster Kafka do HDInsight 4.0
A partir de 1º de julho de 2020, os clientes não poderão criar clusters Kafka com Kafka 1.1 no HDInsight 4.0. Os clusters existentes serão executados como estão, sem o suporte da Microsoft. Considere a possibilidade de migrar para o Kafka 2.1 no HDInsight 4.0 até 30 de junho de 2020 para evitar a interrupção potencial do sistema/suporte.

## <a name="behavior-changes"></a>Alterações de comportamento
### <a name="ambari-stack-version-change"></a>Alteração da versão do Ambari Stack
A partir desta versão, a versão do Ambari é alterada de 2. x. x para 4,1. Você pode obter a versão Ambari da interface do usuário do Ambari > sobre o.

## <a name="upcoming-changes"></a>Alterações futuras
Não há alterações futuras que você precise prestar atenção.

## <a name="bug-fixes"></a>Correções de bug
O HDInsight continua a fazer aprimoramentos de desempenho e confiabilidade do cluster. 

Abaixo, os JIRAs são portados de volta para o hive:
* [HIVE-23619](https://issues.apache.org/jira/browse/HIVE-23619)
* [HIVE-21223](https://issues.apache.org/jira/browse/HIVE-21223)
* [HIVE-22599](https://issues.apache.org/jira/browse/HIVE-22599)
* [HIVE-22121](https://issues.apache.org/jira/browse/HIVE-22121)
* [HIVE-22136](https://issues.apache.org/jira/browse/HIVE-22136)
* [HIVE-18786](https://issues.apache.org/jira/browse/HIVE-18786)

Abaixo, os JIRAs são portados de volta para o HBase:
* [HBASE-21458](https://issues.apache.org/jira/browse/HBASE-21458)
* [HBASE-24208](https://issues.apache.org/jira/browse/HBASE-24208)
* [HBASE-24205](https://issues.apache.org/jira/browse/HBASE-24205)

## <a name="component-version-change"></a>Alteração na versão do componente
Nenhuma alteração de versão de componente para esta versão. Você pode encontrar as versões de componente atuais para HDInsight 4,0 e HDInsight 3,6 neste [documento](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions).