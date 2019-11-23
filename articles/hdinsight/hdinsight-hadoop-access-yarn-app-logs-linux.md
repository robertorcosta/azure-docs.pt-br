---
title: Access Apache Hadoop YARN application logs - Azure HDInsight
description: Saiba como acessar os logs do aplicativo YARN em um cluster HDInsight (Apache Hadoop) baseado em Linux usando a linha de comando e um navegador da web.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/15/2019
ms.openlocfilehash: 883e1007b35991c1f5d8f0c6e949efcb48c27a1d
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327217"
---
# <a name="access-apache-hadoop-yarn-application-logs-on-linux-based-hdinsight"></a>Acessar logs do aplicativo Apache Hadoop YARN no HDInsight baseado em Linux

Saiba como acessar os logs para os aplicativos [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) (Ainda Outro Negociador de Recursos) em um cluster do [Apache Hadoop](https://hadoop.apache.org/) no Azure HDInsight.

## <a name="what-is-apache-yarn"></a>What is Apache YARN?

O YARN suporta vários modelos de programação ([Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) sendo um deles) desacoplando o gerenciamento de recursos do agendamento/monitoramento de aplicativos. O YARN usa um RM (*ResourceManager*) global, NMs (*NodeManagers*) por nó de trabalho e AMs (*ApplicationMasters*) por aplicativo. O aplicativo AM negocia recursos (CPU, memória, disco e rede) para executar o aplicativo com o RM. O RM atua junto com os NMs para conceder esses recursos na forma de *contêineres*. O AM é responsável por controlar o andamento dos contêineres atribuídos pelo RM. Um aplicativo pode exigir um número de contêineres dependendo da natureza do aplicativo.

Cada aplicativo pode consistir em várias *tentativas do aplicativo*. Se um aplicativo falhar, ele poderá ser repetido como uma nova tentativa. Cada tentativa é executado em um contêiner. De certa forma, um contêiner fornece o contexto para a unidade básica de trabalho executado por um aplicativo YARN. Todo trabalho é feito no contexto de um contêiner é executado no nó de trabalho único no qual o contêiner foi alocado. See [Hadoop: Writing YARN Applications](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html), or [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) for further reference.

To scale your cluster to support greater processing throughput, you can use [Autoscale](hdinsight-autoscale-clusters.md) or [Scale your clusters manually using a few different languages](hdinsight-scaling-best-practices.md#utilities-to-scale-clusters).

## <a name="YARNTimelineServer"></a>YARN Timeline Server

O [Servidor de Linha de Tempo do Apache Hadoop YARN](https://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) fornece informações genéricas sobre aplicativos concluídos

O YARN Timeline Server inclui o seguinte tipo de dados:

* A ID do aplicativo, um identificador exclusivo de um aplicativo
* O usuário que iniciou o aplicativo
* Informações sobre as tentativas feitas para concluir o aplicativo
* Os contêineres usados por uma determinada tentativa de aplicativo

## <a name="YARNAppsAndLogs"></a>Logs e aplicativos YARN

O YARN suporta vários modelos de programação ([Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) sendo um deles) desacoplando o gerenciamento de recursos do agendamento/monitoramento de aplicativos. O YARN usa um RM (*ResourceManager*) global, NMs (*NodeManagers*) por nó de trabalho e AMs (*ApplicationMasters*) por aplicativo. O aplicativo AM negocia recursos (CPU, memória, disco e rede) para executar o aplicativo com o RM. O RM atua junto com os NMs para conceder esses recursos na forma de *contêineres*. O AM é responsável por controlar o andamento dos contêineres atribuídos pelo RM. Um aplicativo pode exigir um número de contêineres dependendo da natureza do aplicativo.

Cada aplicativo pode consistir em várias *tentativas do aplicativo*. Se um aplicativo falhar, ele poderá ser repetido como uma nova tentativa. Cada tentativa é executado em um contêiner. De certa forma, um contêiner fornece o contexto para a unidade básica de trabalho executado por um aplicativo YARN. Todo trabalho é feito no contexto de um contêiner é executado no nó de trabalho único no qual o contêiner foi alocado. See [Apache Hadoop YARN Concepts](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html) for further reference.

Os logs de aplicativos (e os logs de contêiner associado) são essenciais na depuração de aplicativos problemáticos do Hadoop. YARN provides a nice framework for collecting, aggregating, and storing application logs with the [Log Aggregation](https://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/) feature. O recurso de agregação de logs permite acessar logs de aplicativos mais determinista. Ele agrega logs em todos os contêineres em um nó de trabalho e as armazena como um arquivo de log agregado por nó de trabalho. O log é armazenado no sistema de arquivos padrão após a conclusão de um aplicativo. O aplicativo deve usar centenas ou milhares de contêineres, mas logs para todos os contêineres executados em um nó único de trabalhado sempre são agregados a um único arquivo. So there's only 1 log per worker node used by your application. A Agregação de Log é habilitada por padrão em clusters HDInsight versão 3.0 e superior. Logs agregados estão localizados no armazenamento padrão do cluster. O caminho a seguir é o caminho do HDFS para os logs:

    /app-logs/<user>/logs/<applicationId>

No caminho, `user` é o nome do usuário que iniciou o aplicativo. O `applicationId` é o identificador exclusivo atribuído a um aplicativo pelo RM do YARN.

The aggregated logs aren't directly readable, as they're written in a [TFile](https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf), [binary format](https://issues.apache.org/jira/browse/HADOOP-3315) indexed by container. Use as ferramentas dos logs ResourceManager do YARN ou CLI para exibir esses logs como texto sem formatação para os aplicativos ou contêineres de interesse.

## <a name="yarn-cli-tools"></a>Ferramentas CLI do YARN

Para usar as ferramentas CLI do YARN, você deve primeiro se conectar ao cluster HDInsight usando o SSH. Para obter informações, consulte [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

É possível exibir esses logs como texto sem formatação, executando um dos seguintes comandos:

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>

Especifique as informações &lt;applicationId>, &lt;user-who-started-the-application>, &lt;containerId> e &lt;worker-node-address> ao executar esses comandos.

## <a name="yarn-resourcemanager-ui"></a>IU do ResourceManager YARN

A interface do usuário do ResourceManager YARN é executada no nó de cabeçalho do cluster. It's accessed through the Ambari web UI. Execute as etapas a seguir para exibir os logs do YARN:

1. No navegador da Web, navegue até https://CLUSTERNAME.azurehdinsight.net. Substitua CLUSTERNAME com o nome do cluster HDInsight.
2. Na lista de serviços à esquerda da página, selecione **YARN**.

    ![Apache Ambari Yarn service selected](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarn-service-selected.png)

3. Na lista suspensa **Links rápidos**, selecione um dos nós principais do cluster e selecione **Log do ResourceManager**.

    ![Apache Ambari Yarn quick links](./media/hdinsight-hadoop-access-yarn-app-logs-linux/hdi-yarn-quick-links.png)

    You're presented with a list of links to YARN logs.
