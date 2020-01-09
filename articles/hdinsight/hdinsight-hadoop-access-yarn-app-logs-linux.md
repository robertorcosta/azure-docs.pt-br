---
title: Acessar Apache Hadoop logs de aplicativo do YARN-Azure HDInsight
description: Saiba como acessar os logs do aplicativo YARN em um cluster HDInsight (Apache Hadoop) baseado em Linux usando a linha de comando e um navegador da web.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/15/2019
ms.openlocfilehash: 437a0c95ea4b48baa74bf6a577dc06429833bc31
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75644572"
---
# <a name="access-apache-hadoop-yarn-application-logs-on-linux-based-hdinsight"></a>Acessar logs do aplicativo Apache Hadoop YARN no HDInsight baseado em Linux

Saiba como acessar os logs para os aplicativos [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) (Ainda Outro Negociador de Recursos) em um cluster do [Apache Hadoop](https://hadoop.apache.org/) no Azure HDInsight.

## <a name="what-is-apache-yarn"></a>O que é o Apache YARN?

O YARN suporta vários modelos de programação ([Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) sendo um deles) desacoplando o gerenciamento de recursos do agendamento/monitoramento de aplicativos. O YARN usa um RM (*ResourceManager*) global, NMs (*NodeManagers*) por nó de trabalho e AMs (*ApplicationMasters*) por aplicativo. O aplicativo AM negocia recursos (CPU, memória, disco e rede) para executar o aplicativo com o RM. O RM atua junto com os NMs para conceder esses recursos na forma de *contêineres*. O AM é responsável por controlar o andamento dos contêineres atribuídos pelo RM. Um aplicativo pode exigir um número de contêineres dependendo da natureza do aplicativo.

Cada aplicativo pode consistir em várias *tentativas do aplicativo*. Se um aplicativo falhar, ele poderá ser repetido como uma nova tentativa. Cada tentativa é executado em um contêiner. De certa forma, um contêiner fornece o contexto para a unidade básica de trabalho executado por um aplicativo YARN. Todo trabalho é feito no contexto de um contêiner é executado no nó de trabalho único no qual o contêiner foi alocado. Consulte [Hadoop: Writing yarn Applications](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html)ou [Apache Hadoop yarn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) para referência adicional.

Para dimensionar o cluster para dar suporte a uma taxa de transferência de processamento maior, você pode usar o [dimensionamento automático](hdinsight-autoscale-clusters.md) ou [dimensionar seus clusters manualmente usando alguns idiomas diferentes](hdinsight-scaling-best-practices.md#utilities-to-scale-clusters).

## <a name="YARNTimelineServer"></a>YARN Timeline Server

O [Servidor de Linha de Tempo do Apache Hadoop YARN](https://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) fornece informações genéricas sobre aplicativos concluídos

O YARN Timeline Server inclui o seguinte tipo de dados:

* A ID do aplicativo, um identificador exclusivo de um aplicativo
* O usuário que iniciou o aplicativo
* Informações sobre as tentativas feitas para concluir o aplicativo
* Os contêineres usados por uma determinada tentativa de aplicativo

## <a name="YARNAppsAndLogs"></a>Logs e aplicativos YARN

O YARN suporta vários modelos de programação ([Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) sendo um deles) desacoplando o gerenciamento de recursos do agendamento/monitoramento de aplicativos. O YARN usa um RM (*ResourceManager*) global, NMs (*NodeManagers*) por nó de trabalho e AMs (*ApplicationMasters*) por aplicativo. O aplicativo AM negocia recursos (CPU, memória, disco e rede) para executar o aplicativo com o RM. O RM atua junto com os NMs para conceder esses recursos na forma de *contêineres*. O AM é responsável por controlar o andamento dos contêineres atribuídos pelo RM. Um aplicativo pode exigir um número de contêineres dependendo da natureza do aplicativo.

Cada aplicativo pode consistir em várias *tentativas do aplicativo*. Se um aplicativo falhar, ele poderá ser repetido como uma nova tentativa. Cada tentativa é executado em um contêiner. De certa forma, um contêiner fornece o contexto para a unidade básica de trabalho executado por um aplicativo YARN. Todo trabalho é feito no contexto de um contêiner é executado no nó de trabalho único no qual o contêiner foi alocado. Consulte [Apache Hadoop conceitos de yarn](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html) para referência adicional.

Os logs de aplicativos (e os logs de contêiner associado) são essenciais na depuração de aplicativos problemáticos do Hadoop. O YARN fornece uma boa estrutura para coletar, agregar e armazenar logs de aplicativos com o recurso de [agregação de log](https://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/) . O recurso de agregação de logs permite acessar logs de aplicativos mais determinista. Ele agrega logs em todos os contêineres em um nó de trabalho e as armazena como um arquivo de log agregado por nó de trabalho. O log é armazenado no sistema de arquivos padrão após a conclusão de um aplicativo. O aplicativo deve usar centenas ou milhares de contêineres, mas logs para todos os contêineres executados em um nó único de trabalhado sempre são agregados a um único arquivo. Portanto, há apenas um log por nó de trabalho usado pelo seu aplicativo. A Agregação de Log é habilitada por padrão em clusters HDInsight versão 3.0 e superior. Logs agregados estão localizados no armazenamento padrão do cluster. O caminho a seguir é o caminho do HDFS para os logs:

    /app-logs/<user>/logs/<applicationId>

No caminho, `user` é o nome do usuário que iniciou o aplicativo. O `applicationId` é o identificador exclusivo atribuído a um aplicativo pelo RM do YARN.

Os logs agregados não são diretamente legíveis, pois são gravados em um formato [TFile](https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf), [binário](https://issues.apache.org/jira/browse/HADOOP-3315) indexado pelo contêiner. Use as ferramentas dos logs ResourceManager do YARN ou CLI para exibir esses logs como texto sem formatação para os aplicativos ou contêineres de interesse.

## <a name="yarn-cli-tools"></a>Ferramentas CLI do YARN

Para usar as ferramentas CLI do YARN, você deve primeiro se conectar ao cluster HDInsight usando o SSH. Para obter informações, consulte [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

É possível exibir esses logs como texto sem formatação, executando um dos seguintes comandos:

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>

Especifique as informações &lt;applicationId>, &lt;user-who-started-the-application>, &lt;containerId> e &lt;worker-node-address> ao executar esses comandos.

## <a name="yarn-resourcemanager-ui"></a>IU do ResourceManager YARN

A interface do usuário do ResourceManager YARN é executada no nó de cabeçalho do cluster. Ele é acessado por meio da interface do usuário do amAmbari Web. Execute as etapas a seguir para exibir os logs do YARN:

1. No navegador da Web, navegue até `https://CLUSTERNAME.azurehdinsight.net`. Substitua CLUSTERNAME com o nome do cluster HDInsight.
2. Na lista de serviços à esquerda da página, selecione **YARN**.

    ![Serviço yarn do Apache Ambari selecionado](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarn-service-selected.png)

3. Na lista suspensa **Links rápidos**, selecione um dos nós principais do cluster e selecione **Log do ResourceManager**.

    ![Links rápidos do Apache Ambari yarn](./media/hdinsight-hadoop-access-yarn-app-logs-linux/hdi-yarn-quick-links.png)

    Você verá uma lista de links para logs do YARN.
