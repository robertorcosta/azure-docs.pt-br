---
title: Logs do Monitor do Azure para Apache Kafka - Azure HDInsight
description: Aprenda a usar os logs do Azure Monitor para analisar logs do cluster Apache Kafka no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/17/2020
ms.openlocfilehash: 3f8ff3cbc24f6e3a7e0eccf1b18e01941c9584b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471173"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>Analise logs do Apache Kafka no HDInsight

Saiba como usar os logs do Azure Monitor para analisar registros gerados pelo Apache Kafka no HDInsight.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="logs-location"></a>Localização dos registros

Os logs Apache Kafka no `/var/log/kafka`cluster estão localizados em . Os registros kafka não são salvos ou persistidos em ciclos de vida de cluster, independentemente se os discos gerenciados são usados. A tabela a seguir mostra os registros disponíveis.

|Log |Descrição |
|---|---|
|kafka.out|stdout e stderr do processo Kafka. Você encontrará registros de inicialização e desligamento de Kafka neste arquivo.|
|server.log|O principal registro do servidor Kafka. Todos os registros de corretores kafka acabam aqui.|
|controlador.log|O controlador registra se o corretor estiver agindo como controlador.|
|statechange.log|Todos os eventos de alteração de estado para corretores estão registrados neste arquivo.|
|kafka-gc.log|Estatísticas da Coleta de Lixo kafka.|

## <a name="enable-azure-monitor-logs-for-apache-kafka"></a>Habilite os registros do Monitor do Azure para Apache Kafka

As etapas para habilitar os logs do Monitor Azure para o HDInsight são as mesmas para todos os clusters HDInsight. Use os links a seguir para entender como criar e configurar os serviços necessários:

1. Criar um espaço de trabalho do Log Analytics. Para obter mais informações, consulte o [documento Logs in Azure Monitor.](../../azure-monitor/platform/data-platform-logs.md)

2. Criar um Kafka no cluster HDInsight. Para obter mais informações, consulte o documento [Introdução ao Apache Kafka no HDInsight](apache-kafka-get-started.md).

3. Configure o cluster Kafka para usar os registros do Monitor Do Azure. Para obter mais informações, consulte os registros do [Use Azure Monitor para monitorar o documento HDInsight.](../hdinsight-hadoop-oms-log-analytics-tutorial.md)

> [!IMPORTANT]  
> Pode levar cerca de 20 minutos até que os dados estejam disponíveis para os registros do Monitor do Azure.

## <a name="query-logs"></a>Logs de consulta

1. No [portal Azure,](https://portal.azure.com)selecione o espaço de trabalho do Log Analytics.

2. No menu à esquerda, em **Geral,** **selecione Logs**. A partir desse ponto, é possível pesquisar os dados coletados do Kafka. Digite uma consulta na janela de consulta e, em seguida, **selecione Executar**. Veja a seguir alguns exemplos de pesquisas:

* Uso do disco: 

    ```kusto
    Perf
    | where ObjectName == "Logical Disk" and CounterName == "Free Megabytes" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    ```

* Uso da CPU:

    ```kusto
    Perf 
    | where CounterName == "% Processor Time" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    ```

* Mensagens recebidas por segundo: (Substitua pelo `your_kafka_cluster_name` nome do cluster.)

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)
    ```

* Bytes de entrada por `wn0-kafka` segundo: (Substitua por um nome de host nó do trabalhador.)

    ```kusto
    metrics_kafka_CL 
    | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

* Bytes de saída por `your_kafka_cluster_name` segundo: (Substitua pelo nome do cluster.)

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

    Também é possível inserir `*` para pesquisas todos os tipos registrados. Atualmente, os logs a seguir estão disponíveis para consultas:

    | Tipo de log | Descrição |
    | ---- | ---- |
    | log\_kafkaserver\_CL | Kafka broker server.log |
    | log\_kafkacontroller\_CL | Kafka broker controller.log |
    | metrics\_kafka\_CL | Métricas do Kafka JMX |

    ![Uso de cpu de log analytics apache kafka](./media/apache-kafka-log-analytics-operations-management/apache-kafka-cpu-usage.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure Monitor, consulte [a visão geral do Monitor do Azure](../../log-analytics/log-analytics-get-started.md)e os registros do [Query Azure Monitor para monitorar os clusters HDInsight](../hdinsight-hadoop-oms-log-analytics-use-queries.md).

Para saber mais sobre como trabalhar com o Apache Kafka, confira os seguintes documentos:

* [Espelhamento do Apache Kafka entre clusters HDInsight](apache-kafka-mirroring.md)
* [Aumente a escala do Apache Kafka no HDInsight](apache-kafka-scalability.md)
* [Use o fluxo do Apache Spark (DStreams) com Apache Kafka](../hdinsight-apache-spark-with-kafka.md)
* [Use o fluxo estruturado do Apache Spark com o Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
