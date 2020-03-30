---
title: Exceções outofmemoryerror para Apache Spark no Azure HDInsight
description: Várias exceções outofmemoryError para o cluster Apache Spark no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/15/2019
ms.openlocfilehash: 31cdef281b1cb26d01a4690c815e3d3621e2c053
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271961"
---
# <a name="outofmemoryerror-exceptions-for-apache-spark-in-azure-hdinsight"></a>Exceções outofmemoryerror para Apache Spark no Azure HDInsight

Este artigo descreve etapas de solução de problemas e possíveis resoluções para problemas ao usar componentes Apache Spark em clusters Azure HDInsight.

## <a name="scenario-outofmemoryerror-exception-for-apache-spark"></a>Cenário: Exceção de memóriaFalha para Apache Spark

### <a name="issue"></a>Problema

O aplicativo Apache Spark falhou com uma exceção outofmemoryError não tratada. Você pode receber uma mensagem de erro semelhante a:

```error
ERROR Executor: Exception in task 7.0 in stage 6.0 (TID 439)

java.lang.OutOfMemoryError
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source)
    at java.io.ByteArrayOutputStream.grow(Unknown Source)
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source)
    at java.io.ByteArrayOutputStream.write(Unknown Source)
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source)
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source)
    at java.io.ObjectOutputStream.writeObject0(Unknown Source)
    at java.io.ObjectOutputStream.writeObject(Unknown Source)
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44)
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101)
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239)
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source)
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source)
    at java.lang.Thread.run(Unknown Source)
```

```error
ERROR SparkUncaughtExceptionHandler: Uncaught exception in thread Thread[Executor task launch worker-0,5,main]

java.lang.OutOfMemoryError
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source)
    ...
```

### <a name="cause"></a>Causa

A causa mais provável dessa exceção é que não há memória de heap suficiente alocada para as JVMs (máquinas virtuais Java). Estes JVMs são lançados como executores ou drivers como parte do aplicativo Apache Spark.

### <a name="resolution"></a>Resolução

1. Determine o tamanho máximo dos dados com os quais o aplicativo Spark lidará. Faça uma estimativa do tamanho com base no tamanho máximo dos dados de entrada, os dados intermediários produzidos pela transformação dos dados de entrada e os dados de saída produzidos transformando ainda mais os dados intermediários. Se a estimativa inicial não for suficiente, aumente ligeiramente o tamanho e iteraaté que os erros de memória diminuam.

1. Verifique se o cluster HDInsight a ser usado tem recursos suficientes em termos de memória e também núcleos para aceitar o aplicativo Spark. Isso pode ser determinado visualizando a seção Métricas de cluster da UI YARN do cluster para os valores de **Memória Usada** vs. **Memória Total** e **VCores Usados** vs. **VCores Total**.

    ![visão de memória do núcleo do fio](./media/apache-spark-ts-outofmemory/yarn-core-memory-view.png)

1. Defina as seguintes configurações de Faísca para valores apropriados. Equilibre os requisitos de aplicação com os recursos disponíveis no cluster. Esses valores não devem exceder 90% da memória disponível e dos núcleos como visto pelo YARN, e também devem atender ao requisito mínimo de memória do aplicativo Spark:

    ```
    spark.executor.instances (Example: 8 for 8 executor count)
    spark.executor.memory (Example: 4g for 4 GB)
    spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB)
    spark.executor.cores (Example: 2 for 2 cores per executor)
    spark.driver.memory (Example: 8g for 8GB)
    spark.driver.cores (Example: 4 for 4 cores)
    spark.yarn.driver.memoryOverhead (Example: 384m for 384MB)
    ```

    Memória total usada por todos os executores =

    ```
    spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
    ```

    Memória total usada pelo driver =

    ```
    spark.driver.memory + spark.yarn.driver.memoryOverhead
    ```

---

## <a name="scenario-java-heap-space-error-when-trying-to-open-apache-spark-history-server"></a>Cenário: Java heap space error when trying to open Apache Spark history server

### <a name="issue"></a>Problema

Você recebe o seguinte erro ao abrir eventos no servidor Spark History:

```
scala.MatchError: java.lang.OutOfMemoryError: Java heap space (of class java.lang.OutOfMemoryError)
```

### <a name="cause"></a>Causa

Este problema é muitas vezes causado pela falta de recursos ao abrir grandes arquivos de eventos de faísca. O tamanho do heap Spark é definido como 1 GB por padrão, mas grandes arquivos de eventos Spark podem exigir mais do que isso.

Se você quiser verificar o tamanho dos arquivos que você está tentando carregar, você pode executar os seguintes comandos:

```bash
hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0274_1/
**576.5 M**  wasb:///hdp/spark2-events/application_1503957839788_0274_1

hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0264_1/
**2.1 G**  wasb:///hdp/spark2-events/application_1503957839788_0264_1
```

### <a name="resolution"></a>Resolução

Você pode aumentar a memória do `SPARK_DAEMON_MEMORY` Spark History Server editando a propriedade na configuração Spark e reiniciando todos os serviços.

Você pode fazer isso dentro da interface do usuário do navegador Ambari selecionando a seção Spark2/Config/Advanced spark2-env.

![Seção avançada de faísca2-env](./media/apache-spark-ts-outofmemory-heap-space/apache-spark-image01.png)

Adicione a seguinte propriedade para alterar a memória Spark `SPARK_DAEMON_MEMORY=4g`History Server de 1g para 4g: .

![Propriedade spark](./media/apache-spark-ts-outofmemory-heap-space/apache-spark-image02.png)

Certifique-se de reiniciar todos os serviços afetados da Ambari.

---

## <a name="scenario-livy-server-fails-to-start-on-apache-spark-cluster"></a>Cenário: O Livy Server não consegue iniciar no cluster Apache Spark

### <a name="issue"></a>Problema

O Livy Server não pode ser iniciado em um Apache Spark [(Spark 2.1 no Linux (HDI 3.6)]. Tentando reiniciar os resultados na seguinte pilha de erros, a partir dos logs da Livy:

```log
17/07/27 17:52:50 INFO CuratorFrameworkImpl: Starting
17/07/27 17:52:50 INFO ZooKeeper: Client environment:zookeeper.version=3.4.6-29--1, built on 05/15/2017 17:55 GMT
17/07/27 17:52:50 INFO ZooKeeper: Client environment:host.name=10.0.0.66
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.version=1.8.0_131
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.vendor=Oracle Corporation
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.home=/usr/lib/jvm/java-8-openjdk-amd64/jre
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.class.path= <DELETED>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.library.path= <DELETED>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.io.tmpdir=/tmp
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.compiler=<NA>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.name=Linux
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.arch=amd64
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.version=4.4.0-81-generic
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.name=livy
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.home=/home/livy
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.dir=/home/livy
17/07/27 17:52:50 INFO ZooKeeper: Initiating client connection, connectString=zk2-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181,zk3-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181,zk6-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181 sessionTimeout=60000 watcher=org.apache.curator.ConnectionState@25fb8912
17/07/27 17:52:50 INFO StateStore$: Using ZooKeeperStateStore for recovery.
17/07/27 17:52:50 INFO ClientCnxn: Opening socket connection to server 10.0.0.61/10.0.0.61:2181. Will not attempt to authenticate using SASL (unknown error)
17/07/27 17:52:50 INFO ClientCnxn: Socket connection established to 10.0.0.61/10.0.0.61:2181, initiating session
17/07/27 17:52:50 INFO ClientCnxn: Session establishment complete on server 10.0.0.61/10.0.0.61:2181, sessionid = 0x25d666f311d00b3, negotiated timeout = 60000
17/07/27 17:52:50 INFO ConnectionStateManager: State change: CONNECTED
17/07/27 17:52:50 WARN NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
17/07/27 17:52:50 INFO AHSProxy: Connecting to Application History server at headnodehost/10.0.0.67:10200
Exception in thread "main" java.lang.OutOfMemoryError: unable to create new native thread
  at java.lang.Thread.start0(Native Method)
  at java.lang.Thread.start(Thread.java:717)
  at com.cloudera.livy.Utils$.startDaemonThread(Utils.scala:98)
  at com.cloudera.livy.utils.SparkYarnApp.<init>(SparkYarnApp.scala:232)
  at com.cloudera.livy.utils.SparkApp$.create(SparkApp.scala:93)
  at com.cloudera.livy.server.batch.BatchSession$$anonfun$recover$2$$anonfun$apply$4.apply(BatchSession.scala:117)
  at com.cloudera.livy.server.batch.BatchSession$$anonfun$recover$2$$anonfun$apply$4.apply(BatchSession.scala:116)
  at com.cloudera.livy.server.batch.BatchSession.<init>(BatchSession.scala:137)
  at com.cloudera.livy.server.batch.BatchSession$.recover(BatchSession.scala:108)
  at com.cloudera.livy.sessions.BatchSessionManager$$anonfun$$init$$1.apply(SessionManager.scala:47)
  at com.cloudera.livy.sessions.BatchSessionManager$$anonfun$$init$$1.apply(SessionManager.scala:47)
  at scala.collection.TraversableLike$$anonfun$map$1.apply(TraversableLike.scala:244)
  at scala.collection.TraversableLike$$anonfun$map$1.apply(TraversableLike.scala:244)
  at scala.collection.mutable.ResizableArray$class.foreach(ResizableArray.scala:59)
  at scala.collection.mutable.ArrayBuffer.foreach(ArrayBuffer.scala:47)
  at scala.collection.TraversableLike$class.map(TraversableLike.scala:244)
  at scala.collection.AbstractTraversable.map(Traversable.scala:105)
  at com.cloudera.livy.sessions.SessionManager.com$cloudera$livy$sessions$SessionManager$$recover(SessionManager.scala:150)
  at com.cloudera.livy.sessions.SessionManager$$anonfun$1.apply(SessionManager.scala:82)
  at com.cloudera.livy.sessions.SessionManager$$anonfun$1.apply(SessionManager.scala:82)
  at scala.Option.getOrElse(Option.scala:120)
  at com.cloudera.livy.sessions.SessionManager.<init>(SessionManager.scala:82)
  at com.cloudera.livy.sessions.BatchSessionManager.<init>(SessionManager.scala:42)
  at com.cloudera.livy.server.LivyServer.start(LivyServer.scala:99)
  at com.cloudera.livy.server.LivyServer$.main(LivyServer.scala:302)
  at com.cloudera.livy.server.LivyServer.main(LivyServer.scala)
  
  ## using "vmstat" found  we had enough free memory
```

### <a name="cause"></a>Causa

`java.lang.OutOfMemoryError: unable to create new native thread`destaca que o SISTEMA OPERACIONAL não pode atribuir mais threads nativos aos JVMs. Confirmou que esta Exceção é causada pela violação do limite de contagem de segmentos por processo.

Quando o Livy Server termina inesperadamente, todas as conexões para Clusters Spark também são encerradas, o que significa que todos os empregos e dados relacionados serão perdidos. No mecanismo de recuperação de sessão HDP 2.6 foi introduzido, livy armazena os detalhes da sessão no Zookeeper para serem recuperados depois que o Servidor Lívio está de volta.

Quando um grande número de empregos são enviados via Livy, como parte da alta disponibilidade para as lojas do Livy Server, essas sessões são em ZK (em clusters HDInsight) e recuperam essas sessões quando o serviço Livy é reiniciado. Na reinicialização após o término inesperado, a Livy cria um thread por sessão e isso acumula um certo número de sessões a serem recuperadas, causando a criação de muitos threads.

### <a name="resolution"></a>Resolução

Exclua todas as entradas usando as etapas detalhadas abaixo.

1. Obtenha o endereço IP dos Ádeis do Zookeeper usando

    ```bash
    grep -R zk /etc/hadoop/conf  
    ```

1. Acima comando listado todos os zookeepers para o meu cluster

    ```bash
    /etc/hadoop/conf/core-site.xml:      <value>zk1-hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181,zk2-      hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181,zk4-hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181</value>
    ```

1. Obtenha todo o endereço IP dos nós do zookeeper usando ping Ou você também pode se conectar ao zookeeper a partir de headnode usando o nome zk

    ```bash
    /usr/hdp/current/zookeeper-client/bin/zkCli.sh -server zk2-hwxspa:2181
    ```

1. Uma vez conectado ao zookeeper execute o seguinte comando para listar todas as sessões que são tentadas para reiniciar.

    1. A maioria dos casos pode ser uma lista de mais de 8000 sessões ####

        ```bash
        ls /livy/v1/batch
        ```

    1. O comando a seguir é remover todas as sessões a serem recuperadas. #####

        ```bash
        rmr /livy/v1/batch
        ```

1. Aguarde que o comando acima seja concluído e o cursor retorne o prompt e, em seguida, reinicie o serviço Livy da Ambari, que deve ter sucesso.

> [!NOTE]
> `DELETE`a sessão lívida uma vez que é concluída sua execução. As sessões de lote da Livy não serão excluídas automaticamente assim que o aplicativo spark for concluído, que é por design. Uma sessão livy é uma entidade criada por uma solicitação DO POST contra o servidor Livy Rest. Uma `DELETE` chamada é necessária para excluir essa entidade. Ou devemos esperar o GC entrar em ação.

---

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* [Visão geral do gerenciamento de memória spark](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [Depuração do aplicativo Spark em clusters HDInsight](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/).

* Obtenha respostas de especialistas do Azure através [do Azure Community Support](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se você precisar de mais ajuda, você pode enviar uma solicitação de suporte do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o centro **de suporte Ajuda +.** Para obter informações mais [detalhadas, consulte Como criar uma solicitação de suporte ao Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e suporte ao faturamento está incluído na assinatura do Microsoft Azure, e o suporte técnico é fornecido através de um dos Planos de Suporte do [Azure](https://azure.microsoft.com/support/plans/).
