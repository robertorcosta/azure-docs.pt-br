---
title: Portas usadas pelos serviços do Hadoop no HDInsight – Azure
description: Este artigo fornece uma lista de portas usadas por Apache Hadoop serviços em execução no Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 46c2cd49258b8eb6813caaf50e9895990ce67287
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72529553"
---
# <a name="ports-used-by-apache-hadoop-services-on-hdinsight"></a>Portas usadas pelos serviços de Apache Hadoop no HDInsight

Este documento fornece uma lista das portas usadas por Apache Hadoop serviços em execução em clusters HDInsight. Ele também fornece informações sobre as portas usadas para se conectar ao cluster usando o SSH.

## <a name="public-ports-vs-non-public-ports"></a>Portas públicas versus portas não públicas

Clusters HDInsight baseados em Linux expõem apenas três portas publicamente na Internet; 22, 23 e 443. Essas portas são usadas para acessar com segurança o cluster usando SSH e serviços expostos por meio do protocolo HTTPS seguro.

Internamente, o HDInsight é implementado por várias máquinas virtuais do Azure (os nós no cluster) em execução em uma rede virtual do Azure. De dentro da rede virtual, você pode acessar portas não expostas pela Internet. Por exemplo, se você se conectar a um dos nós de cabeçalho usando SSH, do nó de cabeçalho, é possível acessar diretamente os serviços em execução nos nós de cluster.

> [!IMPORTANT]  
> Se você não especificar uma rede virtual do Azure como uma opção de configuração para o HDInsight, uma será criada automaticamente. No entanto, você não pode unir outros computadores (como outras máquinas virtuais do Azure ou seu computador de desenvolvimento de cliente) a essa rede virtual.

Para ingressar mais computadores na rede virtual, primeiramente é preciso criar a rede virtual e depois especificá-la na criação do cluster HDInsight. Para obter mais informações, consulte [planejar uma rede virtual para o HDInsight](hdinsight-plan-virtual-network-deployment.md).

## <a name="public-ports"></a>Portas públicas

Todos os nós em um cluster HDInsight estão localizados em uma rede virtual do Azure e não podem ser acessados diretamente pela Internet. Um gateway público fornece acesso à Internet para as seguintes portas, que são comuns em todos os tipos de cluster HDInsight.

| Serviço | Port | Protocolo | Descrição |
| --- | --- | --- | --- |
| sshd |22 |SSH |Conecta os clientes a sshd no nó de cabeçalho primário. Para obter mais informações, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| sshd |22 |SSH |Conecta clientes a sshd no nó de borda. Para obter mais informações, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| sshd |23 |SSH |Conecta os clientes a sshd no nó de cabeçalho secundário. Para obter mais informações, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| Ambari |443 |HTTPS |Interface do usuário da Web do amAmbari. Consulte [gerenciar o HDInsight usando a interface do usuário da Web do Apache Ambari](hdinsight-hadoop-manage-ambari.md) |
| Ambari |443 |HTTPS |API REST do Ambari. Consulte [gerenciar o HDInsight usando a API REST do Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md) |
| WebHCat |443 |HTTPS |API REST do HCatalog. Consulte [usar o MapReduce com ondulação](hadoop/apache-hadoop-use-mapreduce-curl.md) |
| HiveServer2 |443 |ODBC |Conecta-se ao hive usando ODBC. Confira [Connect Excel to HDInsight with the Microsoft ODBC driver (Conectar o Excel ao HDInsight com o driver ODBC da Microsoft)](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md). |
| HiveServer2 |443 |JDBC |Conecta-se ao ApacheHive usando JDBC. Consulte [conectar-se a Apache Hive no HDInsight usando o driver JDBC do hive](hadoop/apache-hadoop-connect-hive-jdbc-driver.md) |

Os itens a seguir estão disponíveis para tipos de cluster específicos:

| Serviço | Port | Protocolo | Tipo de cluster | Descrição |
| --- | --- | --- | --- | --- |
| Stargate |443 |HTTPS |HBase |API REST do HBase. Consulte [introdução ao uso do Apache HBase](hbase/apache-hbase-tutorial-get-started-linux.md) |
| Livy |443 |HTTPS |Spark |API REST do Spark. Consulte [enviar trabalhos de Apache Spark remotamente usando o Apache Livy](spark/apache-spark-livy-rest-interface.md) |
| Servidor Spark thrift |443 |HTTPS |Spark |Servidor Spark Thrift usado para enviar consultas de Hive. Consulte [usar o beeline com o Apache Hive no HDInsight](hadoop/apache-hadoop-use-hive-beeline.md) |
| Storm |443 |HTTPS |Storm |Interface do usuário da Web do Storm. Consulte [implantar e gerenciar topologias de Apache Storm no HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md) |

### <a name="authentication"></a>Autenticação

Todos os serviços publicamente expostos na Internet devem ser autenticados:

| Port | Credenciais |
| --- | --- |
| 22 ou 23 |As credenciais de usuário SSH especificadas durante a criação do cluster |
| 443 |O nome de logon (padrão: admin) e a senha que foram definidos durante a criação do cluster |

## <a name="non-public-ports"></a>Portas não públicas

> [!NOTE]  
> Alguns serviços estão disponíveis apenas em tipos de cluster específicos. Por exemplo, HBase só está disponível em tipos de cluster HBase.

> [!IMPORTANT]  
> Alguns serviços são executados somente em um cabeçalho por vez. Se você tentar se conectar ao serviço no cabeçalho primário e receber um erro, tente novamente usando o cabeçalho secundário.

### <a name="ambari"></a>Ambari

| Serviço | Nós | Port | Caminho da URL | Protocolo |
| --- | --- | --- | --- | --- |
| Interface do usuário da Web do amAmbari | Nós de cabeçalho | 8080 | / | HTTP |
| API REST do Ambari | Nós de cabeçalho | 8080 | /api/v1 | HTTP |

Exemplos:

* API REST do Ambari: `curl -u admin "http://10.0.0.11:8080/api/v1/clusters"`

### <a name="hdfs-ports"></a>Portas HDFS

| Serviço | Nós | Port | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| Interface do usuário da Web do amNameNode |Nós de cabeçalho |30070 |HTTPS |Interface do usuário da Web para exibir o status |
| Serviço de metadados NameNode |nós de cabeçalho |8020 |IPC |Metadados do sistema de arquivos |
| DataNode |Todos os nós de trabalho |30075 |HTTPS |Interface do usuário da Web para exibir status, logs, etc. |
| DataNode |Todos os nós de trabalho |30010 |&nbsp; |Transferência de dados |
| DataNode |Todos os nós de trabalho |30020 |IPC |Operações de metadados |
| NameNode secundário |Nós de cabeçalho |50090 |HTTP |Ponto de verificação para metadados NameNode |

### <a name="yarn-ports"></a>Portas YARN

| Serviço | Nós | Port | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| Interface do usuário da Web do Resource Manager |Nós de cabeçalho |8088 |HTTP |Interface do usuário da Web para o Gerenciador de recursos |
| Interface do usuário da Web do Resource Manager |Nós de cabeçalho |8090 |HTTPS |Interface do usuário da Web para o Gerenciador de recursos |
| Interface de administração do Gerenciador de recursos |nós de cabeçalho |8141 |IPC |Para envios de aplicativos (Hive, servidor Hive, Pig, etc.) |
| Agendador do Gerenciador de recursos |nós de cabeçalho |8030 |HTTP |Interface administrativa |
| Interface do aplicativo do Resource Manager |nós de cabeçalho |8050 |HTTP |Endereço da interface do Gerenciador de aplicativos |
| NodeManager |Todos os nós de trabalho |30050 |&nbsp; |O endereço do Gerenciador de contêineres |
| Interface do usuário da Web do NodeManager |Todos os nós de trabalho |30060 |HTTP |Interface do Gerenciador de recursos |
| Endereço de linha do tempo |Nós de cabeçalho |10200 |RPC |O serviço RPC do serviço de linha do tempo. |
| Linha do tempo da IU Web |Nós de cabeçalho |8188 |HTTP |A IU da Web do serviço de linha do tempo |

### <a name="hive-ports"></a>Portas do hive

| Serviço | Nós | Port | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| HiveServer2 |Nós de cabeçalho |10001 |Thrift |Serviço para conexão com o hive (Thrift/JDBC) |
| Metastore do hive |Nós de cabeçalho |9083 |Thrift |Serviço para se conectar a metadados do hive (Thrift/JDBC) |

### <a name="webhcat-ports"></a>Portas WebHCat

| Serviço | Nós | Port | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| Servidor WebHCat |Nós de cabeçalho |30111 |HTTP |API da Web sobre HCatalog e outros serviços do Hadoop |

### <a name="mapreduce-ports"></a>Portas MapReduce

| Serviço | Nós | Port | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| JobHistory |Nós de cabeçalho |19888 |HTTP |Interface do usuário da Web do MapReduce JobHistory |
| JobHistory |Nós de cabeçalho |10020 |&nbsp; |Servidor JobHistory MapReduce |
| ShuffleHandler |&nbsp; |13562 |&nbsp; |Transfere as saídas do mapa intermediário para os redutores de solicitação |

### <a name="oozie"></a>Oozie

| Serviço | Nós | Port | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| Servidor Oozie |Nós de cabeçalho |11000 |HTTP |URL para o serviço Oozie |
| Servidor Oozie |Nós de cabeçalho |11001 |HTTP |Porta para administrador do Oozie |

### <a name="ambari-metrics"></a>Métricas de Ambari

| Serviço | Nós | Port | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| Linha do tempo (histórico do aplicativo) |Nós de cabeçalho |6188 |HTTP |A IU da Web do serviço de linha do tempo |
| Linha do tempo (histórico do aplicativo) |Nós de cabeçalho |30200 |RPC |A IU da Web do serviço de linha do tempo |

### <a name="hbase-ports"></a>Portas do HBase

| Serviço | Nós | Port | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| HMaster |Nós de cabeçalho |16000 |&nbsp; |&nbsp; |
| Interface do usuário da Web do HMaster info |Nós de cabeçalho |16010 |HTTP |A porta para a interface do usuário da Web do HBase Master |
| Servidor de região |Todos os nós de trabalho |16020 |&nbsp; |&nbsp; |
| &nbsp; |&nbsp; |2181 |&nbsp; |A porta que os clientes usam para se conectar ao ZooKeeper |

### <a name="kafka-ports"></a>Portas Kafka

| Serviço | Nós | Port | Protocolo | Descrição |
| --- | --- | --- | --- | --- |
| Orienta |Nós de trabalho |9092 |[Protocolo Kafka Wire](https://kafka.apache.org/protocol.html) |Usado para comunicação do cliente |
| &nbsp; |Nós do ZooKeeper |2181 |&nbsp; |A porta que os clientes usam para se conectar ao Zookeeper |

### <a name="spark-ports"></a>Portas do Spark

| Serviço | Nós | Port | Protocolo | Caminho da URL | Descrição |
| --- | --- | --- | --- | --- | --- |
| Servidores Spark thrift |Nós de cabeçalho |10002 |Thrift | &nbsp; | Serviço para se conectar ao Spark SQL (Thrift/JDBC) |
| Servidor Livy | Nós de cabeçalho | 8998 | HTTP | &nbsp; | Serviço para executar instruções, trabalhos e aplicativos |
| Jupyter Notebook | Nós de cabeçalho | 8001 | HTTP | &nbsp; | Site do notebook Jupyter |

Exemplos:

* Livy: `curl -u admin -G "http://10.0.0.11:8998/"`. Neste exemplo, `10.0.0.11` é o endereço IP do cabeçalho que hospeda o serviço Livy.
