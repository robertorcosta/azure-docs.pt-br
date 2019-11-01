---
title: Trabalhos de streaming do Spark altamente disponíveis no YARN – Azure HDInsight
description: Como configurar o streaming de Apache Spark para um cenário de alta disponibilidade no Azure HDInsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/26/2018
ms.openlocfilehash: 3e48f220035c56d34d6ca5a7347e9a4ee100e1f1
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241250"
---
# <a name="create-high-availability-apache-spark-streaming-jobs-with-yarn"></a>Crie tarefas do Apache Spark Streaming de alta disponibilidade com o YARN

[O Fluxo do Apache Spark](https://spark.apache.org/) permite que você implemente aplicativos dimensionáveis, tolerantes a falhas e alto rendimento para o processamento de fluxos de dados. Você pode conectar os aplicativos Spark Streaming em um cluster do HDInsight Spark a uma variedade de fontes de dados, como Hubs de Eventos do Azure, Hub IoT do Azure, [Apache Kafka](https://kafka.apache.org/), [Apache Flume](https://flume.apache.org/), Twitter, [ZeroMQ](http://zeromq.org/), soquetes TCP brutos ou monitorando o [Sistema de arquivos do Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) para mudanças. O streaming do Spark fornece suporte para tolerância a falhas com a garantia de que um determinado evento seja processado exatamente uma vez, mesmo com uma falha de nó.

O streaming do Spark cria trabalhos de execução longa, em que você pode aplicar transformações para os dados e, em seguida, efetuar push dos resultados para sistemas de arquivos, bancos de dados, painéis e console. O streaming do Spark processa microlotes de dados, coletando primeiro um lote de eventos ao longo de um intervalo de tempo definido. Em seguida, esse lote é enviado para processamento e saída. Os intervalos de tempo do lote geralmente são definidos em frações de um segundo.

![Spark Streaming](./media/apache-spark-streaming-high-availability/apache-spark-streaming.png)

## <a name="dstreams"></a>DStreams

O streaming do Spark representa um fluxo contínuo de dados usando um *DStream* (fluxo discretizado). Esse DStream pode ser criado a partir de fontes de entrada como Hubs de Eventos ou Kafka, ou aplicando transformações em outro DStream. Quando um evento chega ao aplicativo de streaming do Spark, o evento é armazenado de forma confiável. Ou seja, os dados de evento são replicados para que vários nós tenham uma cópia dele. Isso garante que a falha de um único nó não resultará na perda de seu evento.

O núcleo do Spark usa *RDDs* (conjuntos de dados distribuídos resilientes). Os RDDs distribuem dados em vários nós no cluster, onde cada nó geralmente mantém seus dados completamente na memória para obter o melhor desempenho. Cada RDD representa eventos coletados ao longo de um intervalo de lotes. Quando o intervalo de lotes tiver decorrido, o streaming do Spark produz um novo RDD contendo todos os dados desse intervalo. Esse conjunto contínuo de RDDs é coletado em um DStream. Um aplicativo de streaming do Spark processa os dados armazenados no RDD de cada lote.

![DStream do Spark](./media/apache-spark-streaming-high-availability/apache-spark-dstream.png)

## <a name="spark-structured-streaming-jobs"></a>Trabalhos de streaming estruturado do Spark

O streaming estruturado do Spark foi introduzido no Spark 2.0 como um mecanismo analítico para uso em streaming de dados estruturados. O streaming estruturado do Spark usa as APIs do mecanismo de lote do SparkSQL. Tal como acontece com o streaming do Spark, o streaming estruturado do Spark executa seus cálculos sobre os microlotes de dados chegando continuamente. O streaming estruturado do Spark representa um fluxo de dados como uma Tabela de Entrada com linhas ilimitadas. Ou seja, a Tabela de Entrada continua aumentando na medida em que novos dados chegam. Essa Tabela de Entrada é processada continuamente por uma consulta de execução longa e os resultados são gravados em uma Tabela de Saída.

![Streaming estruturado do Spark](./media/apache-spark-streaming-high-availability/structured-streaming.png)

No streaming estruturado, os dados chegam ao sistema e são ingeridos imediatamente na Tabela de Entrada. Você grava as consultas que executam operações nessa Tabela de Entrada. O resultado da consulta produz outra tabela, chamada de Tabela de Resultados. A Tabela de Resultados contém os resultados da sua consulta, a partir da qual você desenha dados para enviar a um armazenamento de dados externo, como um banco de dados relacional. O *intervalo de disparos* define o tempo para quando os dados serão processados a partir da Tabela de Entrada. Por padrão, o streaming estruturado processa os dados assim que eles chegam. No entanto, também é possível configurar o gatilho para executar em um intervalo mais longo, de modo que os dados de streaming sejam processados em lotes baseados em tempo. Os dados na Tabela de Resultados podem ser completamente atualizados a cada vez que houver novos dados, de modo que inclua todos os dados de saída assim que a consulta de streaming tiver iniciado (*modo completo*) ou, pode conter apenas os dados que forem novos desde a última vez que a consulta foi processada (*modo de acréscimo* ).

## <a name="create-fault-tolerant-spark-streaming-jobs"></a>Criar trabalhos de streaming do Spark com tolerância a falhas

Para criar um ambiente altamente disponível para os trabalhos de streaming do Spark, inicie codificando os trabalhos individuais para recuperação em caso de falha. Esses trabalhos de recuperação automática são tolerantes a falhas.

Os RDDs têm várias propriedades que auxiliam trabalhos de streaming do Spark altamente disponíveis e com tolerância a falhas:

* Os lotes de dados de entrada armazenados em RDDs como um DStream são replicados automaticamente na memória para tolerância a falhas.
* Os dados perdidos devido a falha de trabalho poderão ser recomputados a partir dos dados de entrada replicados em diferentes trabalhos, desde que esses nós de trabalhos estejam disponíveis.
* A recuperação rápida de falhas pode ocorrer dentro de um segundo, assim como a recuperação de falhas/intervalos irregulares ocorre por meio de computação na memória.

### <a name="exactly-once-semantics-with-spark-streaming"></a>Semântica exatamente uma vez com streaming do Spark

Para criar um aplicativo que processe cada evento uma vez (e apenas uma vez), considere como todos os pontos de falha do sistema são reiniciados depois de ter um problema e como é possível evitar a perda de dados. As semânticas exatamente uma vez exigem que nenhum dado seja perdido em nenhum ponto e que o processamento de mensagens seja reiniciado, independentemente de onde a falha ocorrer. Consulte [Criar trabalhos de streaming do Spark com processamento de eventos exatamente uma vez](apache-spark-streaming-exactly-once.md).

## <a name="spark-streaming-and-apache-hadoop-yarn"></a>Streaming do Spark e Apache Hadoop YARN

No HDInsight, o trabalho em cluster é coordenado por *YARN* (Another Resource Negotiator). Projetar alta disponibilidade para streaming do Spark inclui técnicas para o streaming do Spark e também para componentes YARN.  Um exemplo de configuração usando YARN é mostrado a seguir. 

![Arquitetura YARN](./media/apache-spark-streaming-high-availability/hdi-yarn-architecture.png)

As seções a seguir descrevem considerações de design para essa configuração.

### <a name="plan-for-failures"></a>Plano para falhas

Para criar uma configuração YARN para alta disponibilidade, é necessário planejar uma possível falha no executor ou no driver. Alguns trabalhos de streaming do Spark também incluem requisitos de garantia de dados que precisam de configuração e instalação adicionais. Por exemplo, um aplicativo de streaming pode ter um requisito de negócios para uma garantia de ausência de perda de dados, apesar de qualquer erro que ocorra no sistema de streaming de hospedagem ou no cluster HDInsight.

Se um **executor** falhar, suas tarefas e receptores serão reiniciados automaticamente pelo Spark, portanto, não haverá alteração de configuração necessária.

No entanto, se um **driver** falhar, todos os executores associados falharão e todos os blocos e resultados de computação recebidos serão perdidos. Para recuperar uma falha do driver, use o *ponto de verificação DStream*, conforme descrito em [Criar trabalhos de streaming do Spark com processamento de eventos exatamente uma vez](apache-spark-streaming-exactly-once.md#use-checkpoints-for-drivers). O ponto de verificação DStream periodicamente salva o *DAG* (grafo direcionado acíclico) de DStreams para armazenamento com tolerância a falhas, como Armazenamento do Microsoft Azure.  O ponto de verificação permite streaming estruturado do Spark para reiniciar o driver com falha a partir das informações do ponto de verificação.  Este reinício do driver inicializa novos executores e também reinicia os receptores.

Para recuperar drivers com o ponto de verificação DStream:

* Configure o reinício automático do driver no YARN com a Definição de configuração`yarn.resourcemanager.am.max-attempts`.
* Defina um diretório de ponto de verificação em um sistema de arquivos compatível com HDFS com `streamingContext.checkpoint(hdfsDirectory)`.
* Reestruture o código-fonte para usar pontos de verificação para recuperação, por exemplo:

    ```scala
        def creatingFunc() : StreamingContext = {
            val context = new StreamingContext(...)
            val lines = KafkaUtils.createStream(...)
            val words = lines.flatMap(...)
            ...
            context.checkpoint(hdfsDir)
        }

        val context = StreamingContext.getOrCreate(hdfsDir, creatingFunc)
        context.start()
    ```

* Configure a recuperação de dados perdidos ativando o WAL (write-ahead log) com `sparkConf.set("spark.streaming.receiver.writeAheadLog.enable","true")` e desabilite a replicação na memória para entrada de DStreams com `StorageLevel.MEMORY_AND_DISK_SER`.

Para resumir, usando o ponto de verificação + WAL + receptores confiáveis, você poderá entregar a recuperação de dados "pelo menos uma vez":

* Exatamente uma vez, desde que os dados recebidos não sejam perdidos e as saídas sejam idempotentes ou transacionais.
* Exatamente uma vez, com a nova abordagem Kafka Direct que usa Kafka como um log replicado, em vez de usar receptores ou WALs.

### <a name="typical-concerns-for-high-availability"></a>Problemas típicos de alta disponibilidade

* É mais difícil monitorar trabalhos de streaming que os trabalhos em lote. Os trabalhos de streaming do Spark geralmente são de execução longa e o YARN não agrega os logs até o término de um trabalho.  Os pontos de verificação do Spark são perdidos durante as atualizações do aplicativo ou do Spark, e você precisará limpar o diretório do ponto de verificação durante uma atualização.

* Configure seu modo de cluster YARN para executar drivers mesmo se um cliente falhar. Para configurar o reinício automático para drivers:

    ```
    spark.yarn.maxAppAttempts = 2
    spark.yarn.am.attemptFailuresValidityInterval=1h
    ```

* O Spark e a interface do usuário de streaming do Spark possuem um sistema de métricas configurável. Também é possível usar bibliotecas adicionais, como Graphite/Grafana para fazer download de métricas do painel, como 'num registros processados', 'uso de GC/memória no driver e executores', 'atraso total', 'utilização do cluster', e assim por diante. No streaming estruturado versão 2.1 ou posterior, é possível utilizar `StreamingQueryListener` para coletar métricas adicionais.

* Você deve segmentar trabalhos de execução longa.  Quando um aplicativo de streaming do Spark é submetido ao cluster, a fila YARN onde o trabalho é executado deve ser definida. É possível usar um [Agendador de Capacidade do YARN](https://hadoop.apache.org/docs/stable/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html) para enviar trabalhos de execução longa para separar filas.

* Desligue o aplicativo de streaming normalmente. Se os deslocamentos forem conhecidos e todo o estado do aplicativo estiver armazenado externamente, você poderá parar programaticamente seu aplicativo de streaming no local apropriado. Uma técnica é usar "thread hook" no Spark, ao verificar se há um sinalizador externo a cada *n* segundos. Também é possível usar um *arquivo marcador* que é criado no HDFS ao iniciar o aplicativo e removido quando você desejar parar. Para uma abordagem de arquivo de marcador, use um thread separado no aplicativo Spark que chame um código semelhante a este:

    ```scala
    streamingContext.stop(stopSparkContext = true, stopGracefully = true)
    // to be able to recover on restart, store all offsets in an external database
    ```

## <a name="next-steps"></a>Próximos passos

* [Visão geral do Streaming do Apache Spark](apache-spark-streaming-overview.md)
* [Criar trabalhos de Streaming do Apache Spark com exatamente-uma vez o evento de processamento](apache-spark-streaming-exactly-once.md)
* [Trabalhos de Streaming do Apache de longa execução Spark no YARN](https://mkuthan.github.io/blog/2016/09/30/spark-streaming-on-yarn/) 
* [Streaming estruturado: semântica de tolerância a falhas](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html#fault-tolerance-semantics)
* [Streams discretizado: um modelo de tolerância a falhas para processamento de fluxo escalonável](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2012/EECS-2012-259.pdf)
