---
title: Notas da versão mais recente do Microsoft Azure HDInsight
description: Notas da versão mais recente do Microsoft Azure HDInsight. Obtenha detalhes e dicas de desenvolvimento para Hadoop, Spark, Microsoft R Server, Hive e muito mais.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 07/14/2020
ms.openlocfilehash: ef243d5b151f95a00e22ac7636a46b93090ccce3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87006527"
---
# <a name="azure-hdinsight-release-notes"></a>Notas de versão do Azure HDInsight

Este artigo fornece informações sobre as atualizações de versão **mais recentes** do Azure HDInsight. Para obter informações sobre versões anteriores, consulte o [Arquivo morto de notas de versão do HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Resumo

O Microsoft Azure HDInsight é um dos serviços mais populares entre os clientes empresariais para análise de software livre no Azure.

## <a name="release-date-07132020"></a>Data de lançamento: 07/13/2020

Esta versão se aplica tanto ao HDInsight 3.6 quanto ao 4.0. A versão do HDInsight é disponibilizada para todas as regiões durante vários dias. A data de lançamento mostrada aqui indica a data de lançamento da primeira região. Se você não vir as alterações abaixo, aguarde até que a liberação seja ativada em sua região em vários dias.

## <a name="new-features"></a>Novos recursos
### <a name="support-for-customer-lockbox-for-microsoft-azure"></a>Suporte para Sistema de Proteção de Dados do Cliente para Microsoft Azure
O Azure HDInsight agora dá suporte ao Azure Sistema de Proteção de Dados do Cliente. Ele fornece uma interface para os clientes revisarem e aprovarem ou rejeitarem solicitações de acesso a dados do cliente. Ele é usado quando o engenheiro da Microsoft precisa acessar os dados do cliente durante uma solicitação de suporte. Para obter mais informações, consulte [sistema de proteção de dados do cliente para Microsoft Azure](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-preview).

### <a name="service-endpoint-policies-for-storage"></a>Políticas de ponto de extremidade de serviço para armazenamento
Agora, os clientes podem usar as políticas de ponto de extremidade de serviço (SEP) na sub-rede do cluster HDInsight. Saiba mais sobre a [política de ponto de extremidade de serviço do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview).

## <a name="deprecation"></a>Reprovação
### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>Substituição do Spark 2.1 e 2.2 no cluster do Spark do HDInsight 3.6
A partir de julho de 1 2020, os clientes não podem criar novos clusters Spark com Spark 2,1 e 2,2 no HDInsight 3,6. Os clusters existentes serão executados como estão, sem o suporte da Microsoft. Considere a possibilidade de migrar para o Spark 2.3 no HDInsight 3.6 até 30 de junho de 2020 para evitar a interrupção potencial do sistema/suporte.
 
### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>Substituição do Spark 2.3 no cluster do Spark do HDInsight 4.0
A partir de julho de 1 2020, os clientes não podem criar novos clusters Spark com o Spark 2,3 no HDInsight 4,0. Os clusters existentes serão executados como estão, sem o suporte da Microsoft. Considere a possibilidade de migrar para o Spark 2.4 no HDInsight 4.0 até 30 de junho de 2020 para evitar a interrupção potencial do sistema/suporte.
 
### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>Substituição do Kafka 1.1 no cluster Kafka do HDInsight 4.0
A partir de 1º de julho de 2020, os clientes não poderão criar clusters Kafka com Kafka 1.1 no HDInsight 4.0. Os clusters existentes serão executados como estão, sem o suporte da Microsoft. Considere a possibilidade de migrar para o Kafka 2.1 no HDInsight 4.0 até 30 de junho de 2020 para evitar a interrupção potencial do sistema/suporte.

## <a name="behavior-changes"></a>Alterações de comportamento
Nenhuma alteração de comportamento para a qual você precisa prestar atenção.

## <a name="upcoming-changes"></a>Alterações futuras
As alterações a seguir ocorrerão em versões futuras. 

### <a name="ability-to-select-different-zookeeper-sku-for-spark-hadoop-and-ml-services"></a>Capacidade de selecionar um SKU Zookeeper diferente para os serviços Spark, Hadoop e ML
Atualmente, o HDInsight não dá suporte à alteração de SKU Zookeeper para tipos de cluster Spark, Hadoop e ML Services. Ele usa A2_v2 SKU/a2 para nós Zookeeper e os clientes não são cobrados por eles. Na próxima versão, os clientes poderão alterar Zookeeper SKU para os serviços Spark, Hadoop e ML, conforme necessário. Nós Zookeeper com SKU diferente de A2_v2/a2 serão cobrados. O SKU padrão ainda será A2_V2/a2 e sem encargos.

## <a name="bug-fixes"></a>Correções de bug
O HDInsight continua a fazer aprimoramentos de desempenho e confiabilidade do cluster. 
### <a name="fixed-hive-warehouse-connector-issue"></a>Correção do problema do conector de depósito do hive
Houve um problema para a usabilidade do conector do depósito do hive na versão anterior. O problema foi corrigido. 

### <a name="fixed-zeppelin-notebook-truncates-leading-zeros-issue"></a>O notebook Zeppelin fixo trunca o problema de zeros à esquerda
O Zeppelin estava truncando incorretamente os zeros à esquerda na saída da tabela para o formato da cadeia de caracteres. Corrigimos esse problema nesta versão.

## <a name="component-version-change"></a>Alteração na versão do componente
Nenhuma alteração de versão de componente para esta versão. Você pode encontrar as versões de componente atuais para HDInsight 4,0 e HDInsight 3,6 neste [documento](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions).
