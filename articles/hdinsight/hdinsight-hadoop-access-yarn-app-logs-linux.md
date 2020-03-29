---
title: Acesse os registros de aplicativos Apache Hadoop YARN - Azure HDInsight
description: Saiba como acessar os logs do aplicativo YARN em um cluster HDInsight (Apache Hadoop) baseado em Linux usando a linha de comando e um navegador da web.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/23/2020
ms.openlocfilehash: 2a7d71c6d751d4a48ec93f020e657a4d43114cfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76764375"
---
# <a name="access-apache-hadoop-yarn-application-logs-on-linux-based-hdinsight"></a>Acessar logs do aplicativo Apache Hadoop YARN no HDInsight baseado em Linux

Saiba como acessar os logs para os aplicativos [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) (Ainda Outro Negociador de Recursos) em um cluster do [Apache Hadoop](https://hadoop.apache.org/) no Azure HDInsight.

## <a name="what-is-apache-yarn"></a>O que é apache YARN?

O YARN suporta vários modelos de programação ([Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) sendo um deles) desacoplando o gerenciamento de recursos do agendamento/monitoramento de aplicativos. O YARN usa um RM (*ResourceManager*) global, NMs (*NodeManagers*) por nó de trabalho e AMs (*ApplicationMasters*) por aplicativo. O aplicativo AM negocia recursos (CPU, memória, disco e rede) para executar o aplicativo com o RM. O RM atua junto com os NMs para conceder esses recursos na forma de *contêineres*. O AM é responsável por controlar o andamento dos contêineres atribuídos pelo RM. Um aplicativo pode exigir um número de contêineres dependendo da natureza do aplicativo.

Cada aplicativo pode consistir em várias *tentativas do aplicativo*. Se um aplicativo falhar, ele poderá ser repetido como uma nova tentativa. Cada tentativa é executado em um contêiner. De certa forma, um contêiner fornece o contexto para a unidade básica de trabalho executado por um aplicativo YARN. Todo trabalho é feito no contexto de um contêiner é executado no nó de trabalho único no qual o contêiner foi alocado. Consulte [Hadoop: Escrevendo aplicações de Fio](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html), ou Apache [Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) para mais referências.

Para dimensionar seu cluster para suportar maior throughput de processamento, você pode usar [autoescala](hdinsight-autoscale-clusters.md) ou [dimensionar seus clusters manualmente usando alguns idiomas diferentes](hdinsight-scaling-best-practices.md#utilities-to-scale-clusters).

## <a name="yarn-timeline-server"></a>Servidor de linha do tempo do YARN

O [Servidor de Linha de Tempo do Apache Hadoop YARN](https://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) fornece informações genéricas sobre aplicativos concluídos

O YARN Timeline Server inclui o seguinte tipo de dados:

* A ID do aplicativo, um identificador exclusivo de um aplicativo
* O usuário que iniciou o aplicativo
* Informações sobre as tentativas feitas para concluir o aplicativo
* Os contêineres usados por uma determinada tentativa de aplicativo

## <a name="yarn-applications-and-logs"></a>Aplicativos e logs YARN

O YARN suporta vários modelos de programação ([Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) sendo um deles) desacoplando o gerenciamento de recursos do agendamento/monitoramento de aplicativos. O YARN usa um RM (*ResourceManager*) global, NMs (*NodeManagers*) por nó de trabalho e AMs (*ApplicationMasters*) por aplicativo. O aplicativo AM negocia recursos (CPU, memória, disco e rede) para executar o aplicativo com o RM. O RM atua junto com os NMs para conceder esses recursos na forma de *contêineres*. O AM é responsável por controlar o andamento dos contêineres atribuídos pelo RM. Um aplicativo pode exigir um número de contêineres dependendo da natureza do aplicativo.

Cada aplicativo pode consistir em várias *tentativas do aplicativo*. Se um aplicativo falhar, ele poderá ser repetido como uma nova tentativa. Cada tentativa é executado em um contêiner. De certa forma, um contêiner fornece o contexto para a unidade básica de trabalho executado por um aplicativo YARN. Todo trabalho é feito no contexto de um contêiner é executado no nó de trabalho único no qual o contêiner foi alocado. Consulte [Conceitos do Apache Hadoop YARN](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html) para referência futura.

Os logs de aplicativos (e os logs de contêiner associado) são essenciais na depuração de aplicativos problemáticos do Hadoop. O YARN fornece uma ótima estrutura para coletar, agregar e armazenar logs de aplicativos com o recurso [Agregação de Logs](https://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/). O recurso de agregação de logs permite acessar logs de aplicativos mais determinista. Ele agrega logs em todos os contêineres em um nó de trabalho e as armazena como um arquivo de log agregado por nó de trabalho. O log é armazenado no sistema de arquivos padrão após a conclusão de um aplicativo. O aplicativo deve usar centenas ou milhares de contêineres, mas logs para todos os contêineres executados em um nó único de trabalhado sempre são agregados a um único arquivo. Portanto, há apenas 1 log por nó de trabalhador usado pelo seu aplicativo. A Agregação de Log é habilitada por padrão em clusters HDInsight versão 3.0 e superior. Logs agregados estão localizados no armazenamento padrão do cluster. O caminho a seguir é o caminho do HDFS para os logs:

```
/app-logs/<user>/logs/<applicationId>
```

No caminho, `user` é o nome do usuário que iniciou o aplicativo. O `applicationId` é o identificador exclusivo atribuído a um aplicativo pelo RM do YARN.

Os logs agregados não são diretamente legíveis, pois são escritos em um [TFile](https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf), [formato binário](https://issues.apache.org/jira/browse/HADOOP-3315) indexado por contêiner. Use as ferramentas dos logs ResourceManager do YARN ou CLI para exibir esses logs como texto sem formatação para os aplicativos ou contêineres de interesse.

## <a name="yarn-logs-in-an-esp-cluster"></a>Logs de fio em um cluster ESP

Duas configurações devem ser `mapred-site` adicionadas ao costume em Ambari.

1. A partir de um `https://CLUSTERNAME.azurehdinsight.net`navegador `CLUSTERNAME` da Web, navegue até , onde está o nome do seu cluster.

1. Da UI Ambari, navegue até **mapReduce2** > **Configs** > **Advanced** > Custom**mapred-site**.

1. Adicione *um* dos seguintes conjuntos de propriedades:

    **Conjunto 1**

    ```
    mapred.acls.enabled=true
    mapreduce.job.acl-view-job=*
    ```

    **Conjunto 2**

    ```
    mapreduce.job.acl-view-job=<user1>,<user2>,<user3>
    ```

1. Salve as alterações e reinicie todos os serviços afetados.

## <a name="yarn-cli-tools"></a>Ferramentas CLI do YARN

1. Use [o comando ssh](./hdinsight-hadoop-linux-use-ssh-unix.md) para se conectar ao seu cluster. Edite o comando abaixo substituindo CLUSTERNAME pelo nome do seu cluster e, em seguida, digite o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Liste todos os ids de aplicativos dos aplicativos Yarn em execução no momento com o seguinte comando:

    ```bash
    yarn top
    ```

    Observe o id `APPLICATIONID` do aplicativo da coluna cujos logs devem ser baixados.

    ```output
    YARN top - 18:00:07, up 19d, 0:14, 0 active users, queue(s): root
    NodeManager(s): 4 total, 4 active, 0 unhealthy, 0 decommissioned, 0 lost, 0 rebooted
    Queue(s) Applications: 2 running, 10 submitted, 0 pending, 8 completed, 0 killed, 0 failed
    Queue(s) Mem(GB): 97 available, 3 allocated, 0 pending, 0 reserved
    Queue(s) VCores: 58 available, 2 allocated, 0 pending, 0 reserved
    Queue(s) Containers: 2 allocated, 0 pending, 0 reserved
    
                      APPLICATIONID USER             TYPE      QUEUE   #CONT  #RCONT  VCORES RVCORES     MEM    RMEM  VCORESECS    MEMSECS %PROGR       TIME NAME
     application_1490377567345_0007 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628407    2442611  10.00   18:20:20 Thrift JDBC/ODBC Server
     application_1490377567345_0006 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628430    2442645  10.00   18:20:20 Thrift JDBC/ODBC Server
    ```

1. É possível exibir esses logs como texto sem formatação, executando um dos seguintes comandos:

    ```bash
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
    ```

    Especifique as informações &lt;applicationId>, &lt;user-who-started-the-application>, &lt;containerId> e &lt;worker-node-address> ao executar esses comandos.

### <a name="other-sample-commands"></a>Outros comandos de amostra

1. Baixe os logs de contêineres yarn para todos os mestres de aplicativos com o comando abaixo. Isso criará o arquivo de log chamado `amlogs.txt` no formato de texto.

    ```bash
    yarn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

1. Baixe os logs do contêiner do Yarn apenas para o mestre aplicativo mais recente com o seguinte comando:

    ```bash
    yarn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

1. Baixe os logs do contêiner do YARN para os dois primeiros mestres de aplicativo com o seguinte comando:

    ```bash
    yarn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt
    ```

1. Baixe todos os logs do contêiner do Yarn com o seguinte comando:

    ```bash
    yarn logs -applicationId <application_id> > logs.txt
    ```

1. Baixe o log do contêiner yarn para um contêiner específico com o seguinte comando:

    ```bash
    yarn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt
    ```

## <a name="yarn-resourcemanager-ui"></a>IU do ResourceManager YARN

A interface do usuário do ResourceManager YARN é executada no nó de cabeçalho do cluster. É acessado através da ui da web Ambari. Execute as etapas a seguir para exibir os logs do YARN:

1. No navegador da Web, navegue até `https://CLUSTERNAME.azurehdinsight.net`. Substitua CLUSTERNAME com o nome do cluster HDInsight.

2. Na lista de serviços à esquerda, selecione **YARN**.

    ![Serviço Apache Ambari Yarn selecionado](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarn-service-selected.png)

3. Na lista suspensa **Links rápidos**, selecione um dos nós principais do cluster e selecione **Log do ResourceManager**.

    ![Links rápidos apache ambari yarn](./media/hdinsight-hadoop-access-yarn-app-logs-linux/hdi-yarn-quick-links.png)

    Você é apresentado com uma lista de links para logs YARN.
