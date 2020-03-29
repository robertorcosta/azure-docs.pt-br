---
title: Migrar Azure HDInsight 3.6 Apache Storm para HDInsight 4.0 Apache Spark
description: As diferenças e o fluxo migratório para migrar cargas de trabalho do Apache Storm para o Streaming Spark ou o Spark Structured Streaming.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/16/2019
ms.openlocfilehash: 916c54c3739d1164e4e9c1db67aa1f4e0dbd0c6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157786"
---
# <a name="migrate-azure-hdinsight-36-apache-storm-to-hdinsight-40-apache-spark"></a>Migrar Azure HDInsight 3.6 Apache Storm para HDInsight 4.0 Apache Spark

Este documento descreve como migrar as cargas de trabalho do Apache Storm no HDInsight 3.6 para o HDInsight 4.0. O HDInsight 4.0 não suporta o tipo de cluster Apache Storm e você precisará migrar para outra plataforma de dados de streaming. Duas opções adequadas são Apache Spark Streaming e Spark Structured Streaming. Este documento descreve as diferenças entre essas plataformas e também recomenda um fluxo de trabalho para migrar cargas de trabalho apache storm.

## <a name="storm-migration-paths-in-hdinsight"></a>Caminhos de migração de tempestades no HDInsight

Se você quiser migrar do Apache Storm no HDInsight 3.6, você tem várias opções:

* Spark Streaming no HDInsight 4.0
* Spark Structured Streaming no HDInsight 4.0
* Stream Analytics do Azure

Este documento fornece um guia para migrar de Apache Storm para Spark Streaming e Spark Structured Streaming.

> [!div class="mx-imgBorder"]
> ![Caminho de migração de tempestade hdinsight](./media/migrate-storm-to-spark/storm-migration-path.png)

## <a name="comparison-between-apache-storm-and-spark-streaming-spark-structured-streaming"></a>Comparação entre Apache Storm e Spark Streaming, Streaming Estruturado de Faíscas

O Apache Storm pode oferecer diferentes níveis de processamento de mensagem garantido. Por exemplo, um aplicativo básico Storm pode garantir um processamento pelo menos uma vez e o [Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) pode garantir o processamento exatamente uma vez. O Spark Streaming e o Spark Structured Streaming garantem que qualquer evento de entrada seja processado exatamente uma vez, mesmo que ocorra uma falha no nó. Storm tem um modelo que processa cada evento, e você também pode usar o modelo Micro Batch com Trident. O Spark Streaming e o Spark Structured Streaming fornecem o modelo de processamento em microlotes.

|  |Storm |Streaming do Spark | Streaming estruturado de faíscas|
|---|---|---|---|
|**Garantia de processamento de eventos**|Pelo menos uma vez. <br> Exatamente uma vez (Trident) |[Exatamente uma vez](https://spark.apache.org/docs/latest/streaming-programming-guide.html)|[Exatamente uma vez](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**Modelo de Processamento**|Tempo real <br> Micro Lote (Trident) |Micro lote |Micro lote |
|**Suporte ao tempo do evento**|[Sim](https://storm.apache.org/releases/2.0.0/Windowing.html)|Não|[Sim](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**Idiomas**|Java, etc.|Scala, Java, Python|Python, R, Scala, Java, SQL|

### <a name="spark-streaming-vs-spark-structured-streaming"></a>Streaming de faíscavs Spark estruturado

O Spark Structured Streaming está substituindo o Spark Streaming (DStreams). O Streaming estruturado continuará recebendo melhorias e manutenção, enquanto os DStreams estarão apenas no modo de manutenção. **Nota: precisa de links para enfatizar este ponto**. O Streaming estruturado não tem tantos recursos quanto dStreams para as fontes e afundamentos que ele suporta fora da caixa, então avalie seus requisitos para escolher a opção apropriada de processamento de fluxo Spark.

## <a name="streaming-single-event-processing-vs-micro-batch-processing"></a>Processamento de streaming (evento único) vs processamento em microlotes

Storm fornece um modelo que processa cada evento. Isso significa que todos os registros de entrada serão processados assim que chegarem. Os aplicativos Spark Streaming precisam aguardar uma fração de segundo para coletar cada microlote de eventos antes de enviar esse lote para processamento. Por outro lado, um aplicativo controlado por evento processa cada evento imediatamente. A latência do Spark Streaming normalmente fica abaixo de alguns segundos. Os benefícios da abordagem de microlote são um processamento de dados mais eficiente e cálculos de agregação mais simples.

> [!div class="mx-imgBorder"]
> ![processamento de streaming e microlotes](./media/migrate-storm-to-spark/streaming-and-micro-batch-processing.png)

## <a name="storm-architecture-and-components"></a>Arquitetura e componentes de tempestade

As topologias do Storm são compostas por vários componentes organizados em um DAG (grafo direcionado acíclico). Fluxo de dados entre os componentes no grafo. Cada componente consome um ou mais fluxos de dados e, opcionalmente, pode emitir um ou mais fluxos.

|Componente |Descrição |
|---|---|
|Bico|Traz dados para uma topologia. Eles emitem um ou mais fluxos na topologia.|
|Parafuso|Consome correntes emitidas a partir de bicos ou outros parafusos. Os bolts podem opcionalmente emitir fluxos na topologia. Os bolts também são responsáveis por gravar dados em armazenamentos ou serviço externos, como HDFS, Kafka ou HBase.|

> [!div class="mx-imgBorder"]
> ![interação de componentes da tempestade](./media/migrate-storm-to-spark/apache-storm-components.png)

A tempestade consiste nos três daemons seguintes, que mantêm o cluster storm funcionando.

|Daemon |Descrição |
|---|---|
|Nimbus|Semelhante ao Hadoop JobTracker, ele é responsável por distribuir código em torno do cluster e atribuir tarefas a máquinas e monitorar falhas.|
|Zookeeper|Usado para coordenação de clusters.|
|Supervisor|Ouve o trabalho atribuído à sua máquina e inicia e interrompe os processos dos trabalhadores com base nas diretrizes da Nimbus. Cada processo do trabalhador executa um subconjunto de uma topologia. A lógica do aplicativo do usuário (Bicos e Parafusos) é executada aqui.|

> [!div class="mx-imgBorder"]
> ![nimbus, zookeeper, e daemons supervisor](./media/migrate-storm-to-spark/nimbus-zookeeper-supervisor.png)

## <a name="spark-streaming-architecture-and-components"></a>Arquitetura e componentes do Spark Streaming

As etapas a seguir resumem como os componentes trabalham juntos no Spark Streaming (DStreams) e no Spark Structured Streaming:

* Quando o Spark Streaming é lançado, o driver inicia a tarefa no executor.
* O executor recebe um fluxo de uma fonte de dados de streaming.
* Quando o executor recebe fluxos de dados, ele divide o fluxo em blocos e os mantém na memória.
* Blocos de dados são replicados para outros executores.
* Os dados processados são então armazenados no armazenamento de dados de destino.

> [!div class="mx-imgBorder"]
> ![faísca caminho de streaming para a saída](./media/migrate-storm-to-spark/spark-streaming-to-output.png)

## <a name="spark-streaming-dstream-workflow"></a>Fluxo de trabalho Spark Streaming (DStream)

Com a expiração de cada intervalo de lote, um novo RDD é produzido, contendo todos os dados desse intervalo. Os conjuntos contínuos de RDDs são coletados em um DStream. Por exemplo, se o intervalo de lote for de um segundo, o DStream emitirá um lote a cada segundo com um RDD que contém todos os dados ingeridos durante esse segundo. Ao processar o DStream, o evento de temperatura aparece em um desses lotes. Um aplicativo Spark Streaming processa os lotes que contêm os eventos e, por fim, atua nos dados armazenados em cada RDD.

> [!div class="mx-imgBorder"]
> ![centelha lotes de processamento de streaming](./media/migrate-storm-to-spark/spark-streaming-batches.png)

Para obter detalhes sobre as diferentes transformações disponíveis com o Spark Streaming, consulte [Transformações em DStreams](https://spark.apache.org/docs/latest/streaming-programming-guide.html#transformations-on-dstreams).

## <a name="spark-structured-streaming"></a>Streaming estruturado do Spark

O Streaming Estruturado spark representa um fluxo de dados como uma tabela que não é limitada em profundidade. A tabela continua a crescer à medida que novos dados chegam. Esta tabela de entrada é continuamente processada por uma consulta de longa duração, e os resultados são enviados para uma tabela de saída.

No Structured Streaming, os dados chegam ao sistema e são ingeridos imediatamente em uma tabela de entrada. Você grava as consultas (usando as APIs de DataFrame e conjuntos de dados) que executam operações nessa tabela de entrada.

A saída de consulta produz uma *tabela de resultados,* que contém os resultados da sua consulta. Você pode desenhar dados da tabela de resultados para um datastore externo, tal banco de dados relacional.

O tempo de quando os dados serão processados a partir da tabela de entrada é controlado pelo intervalo do gatilho. Por padrão, o intervalo do gatilho é zero, para que o Structured Streaming tente processar os dados assim que eles chegam. Na prática, isso significa que assim que o Structured Streaming termina de processar a execução da consulta anterior, ele inicia outro processamento executado quaisquer dados recebidos recentemente. É possível configurar o gatilho para executar em um intervalo, de modo que os dados de streaming sejam processados em lotes baseados em tempo.

> [!div class="mx-imgBorder"]
> ![processamento de dados em streaming estruturado](./media/migrate-storm-to-spark/structured-streaming-data-processing.png)

> [!div class="mx-imgBorder"]
> ![modelo de programação para streaming estruturado](./media/migrate-storm-to-spark/structured-streaming-model.png)

## <a name="general-migration-flow"></a>Fluxo migratório geral

O fluxo de migração recomendado de Tempestade para Faísca assume a seguinte arquitetura inicial:

* Kafka é usado como fonte de dados de streaming
* Kafka e Storm são implantados na mesma rede virtual
* Os dados processados pela Storm são gravados em um dissipador de dados, como o Azure Storage ou o Azure Data Lake Storage Gen2.

    > [!div class="mx-imgBorder"]
    > ![diagrama de ambiente presumido atual](./media/migrate-storm-to-spark/presumed-current-environment.png)

Para migrar seu aplicativo do Storm para uma das APIs de streaming do Spark, faça o seguinte:

1. **Implantar um novo cluster.** Implante um novo cluster HDInsight 4.0 Spark na mesma rede virtual e implante seu aplicativo de streaming estruturado Spark nele e teste-o completamente.

    > [!div class="mx-imgBorder"]
    > ![nova implantação de faísca no HDInsight](./media/migrate-storm-to-spark/new-spark-deployment.png)

1. **Pare de consumir no velho aglomerado da Tempestade.** Na Tempestade existente, pare de consumir dados da fonte de dados de streaming e espere que os dados terminem a escrita para o dissipador de destino.

    > [!div class="mx-imgBorder"]
    > ![parar de consumir no cluster atual](./media/migrate-storm-to-spark/stop-consuming-current-cluster.png)

1. **Comece a consumir no novo cluster Spark.** Inicie a transmissão de dados de um recém-implantado cluster HDInsight 4.0 Spark. Neste momento, o processo é tomado pelo consumo do último deslocamento de Kafka.

    > [!div class="mx-imgBorder"]
    > ![começar a consumir em novo cluster](./media/migrate-storm-to-spark/start-consuming-new-cluster.png)

1. **Remova o antigo cluster conforme necessário.** Uma vez que o switch esteja completo e funcionando corretamente, remova o antigo cluster HDInsight 3.6 Storm conforme necessário.

    > [!div class="mx-imgBorder"]
    > ![remover antigos clusters HDInsight conforme necessário](./media/migrate-storm-to-spark/remove-old-clusters1.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre storm, spark streaming e spark structured streaming, consulte os seguintes documentos:

* [Visão geral de Streaming do Apache Spark](../spark/apache-spark-streaming-overview.md)
* [Visão geral do streaming estruturado do Apache Spark](../spark/apache-spark-structured-streaming-overview.md)