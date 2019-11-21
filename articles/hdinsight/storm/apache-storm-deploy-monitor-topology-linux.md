---
title: Implantar e gerenciar topologias Apache Storm no Microsoft Azure HDInsight
description: Learn how to deploy, monitor, and manage Apache Storm topologies using the Storm Dashboard on Linux-based HDInsight. Use as ferramentas do Hadoop para Visual Studio.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 82c5db4f75f131ebdc2434955108e7d50237d9ba
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228887"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-azure-hdinsight"></a>Implantar e gerenciar topologias Apache Storm no Microsoft Azure HDInsight 

Neste documento, conheça as noções básicas de gerenciamento e monitoramento de topologias [Apache Storm](https://storm.apache.org/) em execução no Storm em clusters HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Storm no HDInsight. Confira [Criar clusters Apache Hadoop usando o portal do Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e selecione **Storm** como **Tipo de cluster**.

* (Optional) Familiarity with Secure Shell (SSH) and Secure Copy (SCP). Para saber mais, confira [Conectar-se ao HDInsight (Apache Hadoop) usando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* (Optional) Visual Studio, Azure SDK 2.5.1 or newer, and the Data Lake Tools for Visual Studio. For more information, see [Apache Hadoop & Visual Studio Data Lake Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

## <a name="submit-a-topology-using-visual-studio"></a>Submit a topology using Visual Studio

You can use the Data Lake Tools for Visual Studio to submit C# or hybrid topologies to your Storm cluster. As etapas a seguir usam um aplicativo de exemplo. For information about topology creation using the Data Lake Tools, see [Apache Storm topologies with Visual Studio and C#](apache-storm-develop-csharp-visual-studio-topology.md).

1. If you haven't already installed the latest version of the Data Lake tools for Visual Studio, see [Use Data Lake Tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    > [!NOTE]  
    > Anteriormente, as Ferramentas do Data Lake para Visual Studio se chamavam Ferramentas do HDInsight para Visual Studio.
    >
    > Data Lake Tools for Visual Studio are included in the **Azure Workload** for Visual Studio 2019.

2. Abra o Visual Studio.

3. In the **Start** window, select **Create a new project**.

4. In the **Create a new project** window, select the search box, and enter *Storm*. Then choose **Storm Sample** from the result list and select **Next**.

5. In the **Configure your new project** window, enter a **Project name**, and go to or create a **Location** to save the new project in. Em seguida, selecione **Criar**.

    ![Configure your new project window, Visual Studio](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-sample1.png)

6. In **Solution Explorer**, right-click the project, and choose **Submit to Storm on HDInsight**.

    > [!NOTE]  
    > Se solicitado, insira as credenciais de logon para sua assinatura do Azure. Se você tiver mais de uma assinatura, entre naquela que contém seu cluster Storm no HDInsight.

7. In the **Submit Topology** dialog box, under the **Storm Cluster** drop-down list, choose your Storm on HDInsight cluster, and then select **Submit**. You can monitor whether the submission is successful by viewing the **Output** pane.

## <a name="submit-a-topology-using-ssh-and-the-storm-command"></a>Submit a topology using SSH and the Storm command

To submit a topology to Storm using SSH:

1. Use o SSH para conectar ao cluster HDInsight. Replace `USERNAME` with the name of your SSH user name (such as *sshuser*). Substitua o `CLUSTERNAME` pelo nome do seu cluster HDInsight.

    ```shell
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    For more information on using SSH to connect to your HDInsight cluster, see [Connect to HDInsight (Apache Hadoop) using SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Use the following command to start the *WordCount* example topology:

    ```ssh
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount
    ```

    Esse comando inicia a topologia de WordCount de exemplo no cluster. Essa topologia gera frases aleatoriamente e conta a ocorrência de cada palavra nas frases.

    > [!NOTE]  
    > When submitting topology to the cluster, you must first copy the .jar file containing the cluster before using the `storm` command. Para copiar o arquivo para o cluster, é possível usar o comando `scp`. Por exemplo, insira: `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`.
    >
    > The *WordCount* example, and other storm starter examples, are already included on your cluster at `/usr/hdp/current/storm-client/contrib/storm-starter/`.

## <a name="submit-a-topology-programmatically"></a>Submit a topology programmatically

É possível implantar programaticamente uma topologia usando o serviço Nimbus. [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology) fornece um exemplo de aplicativo Java que demonstra como implantar e iniciar uma topologia por meio do serviço Nimbus.

## <a name="monitor-and-manage-a-topology-in-visual-studio"></a>Monitor and manage a topology in Visual Studio

When you submit a topology using Visual Studio, the **Storm Topologies View** window appears. Selecione a topologia da lista para exibir informações sobre a topologia em execução.

![Monitor topology, Storm Topologies View window, Visual Studio](./media/apache-storm-deploy-monitor-topology-linux/visual-studio-monitor.png)

> [!NOTE]  
> Você também pode exibir **topologias Storm** do **Gerenciador de Servidores**. Expanda **Azure** > **HDInsight**, clique com o botão direito do mouse em cluster do Storm no HDInsight e selecione **Exibir Topologias do Storm**.

Escolha a forma dos spouts ou bolts para exibir informações sobre esses componentes. A tooltip with component information appears for the item selected.

### <a name="deactivate-and-reactivate-a-topology"></a>Deactivate and reactivate a topology

Deactivating a topology pauses it until the topology is killed or reactivated. To do these operations, use the **Deactivate** and **Reactivate** buttons in the **Actions** area at the top of the **Storm Topologies View** window.

### <a name="rebalance-a-topology"></a>Rebalance a topology

Rebalancear uma topologia permite que o sistema revise o paralelismo da topologia. For example, if you've resized the cluster to add more notes, rebalancing allows a topology to see the new nodes.

To rebalance a topology, use the **Rebalance** button in the **Actions** area of the **Storm Topologies View** window.

> [!WARNING]  
> Rebalancing a topology deactivates the topology, redistributes workers evenly across the cluster, and then returns the topology to the state it was in before rebalancing occurred. If the topology was active, it becomes active again. If the topology was deactivated, it remains deactivated.

### <a name="kill-a-running-topology"></a>Eliminar uma topologia em execução

Storm topologies continue running until they're stopped or the cluster is deleted. To stop a topology, use the **Kill** button in the **Actions** area.

## <a name="monitor-and-manage-a-topology-using-ssh-and-the-storm-command"></a>Monitor and manage a topology using SSH and the Storm command

O `storm` utilitário permite que você trabalhe com as topologias de execução na linha de comando. Use `storm -h` para uma lista completa de comandos.

### <a name="list-topologies"></a>Topologias de lista

Use o comando a seguir para listar todas as topologias em execução:

```shell
storm list
```

Esse comando retorna informações semelhantes ao seguinte texto:

```shell
Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
-------------------------------------------------------------------
WordCount            ACTIVE     29         2            263
```

### <a name="deactivate-and-reactivate-a-topology"></a>Deactivate and reactivate a topology

Deactivating a topology pauses it until the topology is killed or reactivated. Use the following commands to deactivate or reactivate:

```shell
storm Deactivate TOPOLOGYNAME
```

```shell
storm Activate TOPOLOGYNAME
```

### <a name="kill-a-running-topology"></a>Eliminar uma topologia em execução

As topologias Storm, depois de iniciadas, continuarão em execução até serem interrompidas. Para interrompê-la, use o comando a seguir:

```shell
storm kill TOPOLOGYNAME
```

### <a name="rebalance-a-topology"></a>Rebalance a topology

Rebalancear uma topologia permite que o sistema revise o paralelismo da topologia. For example, if you've resized the cluster to add more notes, rebalancing allows a topology to see the new nodes.

> [!WARNING]  
> Rebalancing a topology deactivates the topology, redistributes workers evenly across the cluster, and then returns the topology to the state it was in before rebalancing occurred. If the topology was active, it becomes active again. Se ela foi desativada, ela permanecerá desativada.

```shell
storm rebalance TOPOLOGYNAME
```

## <a name="monitor-and-manage-a-topology-using-the-storm-ui"></a>Monitor and manage a topology using the Storm UI

The Storm UI provides a web interface for working with running topologies, and it's included on your HDInsight cluster. To view the Storm UI, use a web browser to open `https://CLUSTERNAME.azurehdinsight.net/stormui`, where *CLUSTERNAME* is the name of your cluster.

> [!NOTE]  
> If you're asked to provide a user name and password, enter the cluster administrator username and password that you used when creating the cluster.

### <a name="storm-ui-main-page"></a>Storm UI main page

A página principal da interface do usuário do Storm fornece as seguintes informações:

| Seção | Descrição |
| --- | --- |
| **Cluster summary** | Informações básicas sobre o cluster do Storm. |
| **Nimbus summary** | A list of basic Nimbus information. |
| **Topology summary** | Uma lista das topologias em execução. To view more information about a specific topology, select its link in the **Name** column. |
| **Supervisor summary** | Informações sobre o supervisor do Storm. To see the worker resources associated with a specific supervisor, select its link in the **Host** or **Id** column. |
| **Nimbus configuration** | Configuração do Nimbus para o cluster. |

The Storm UI main page looks similar to this web page:

![Main page, Storm UI, Apache Storm topologies, Azure Insight](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-main-page.png)

#### <a name="topology-summary"></a>Resumo da topologia

Selecionar um link na seção **Resumo da topologia** exibirá as seguintes informações sobre a topologia:

| Seção | Descrição |
| --- | --- |
| **Topology summary** | Informações básicas sobre a topologia. |
| **Topology actions** | Management actions that you can do for the topology. The available actions are described later in this section. |
| **Topology stats** | Estatísticas sobre a topologia. To set the time frame for an entry in this section, select its link in the **Window** column. |
| **Spouts** *(time frame)* | Os spouts usados pela topologia. To view more information about a specific spout, select its link in the **Id** column. |
| **Bolts** *(time frame)* | Os bolts usados pela topologia. To view more information about a specific bolt, select its link in the **Id** column. |
| **Worker resources** | A list of worker resources. To view more information about a specific worker resource, select its link in the **Host** column. |
| **Topology visualization** | A **Show Visualization** button that displays a visualization of the topology. |
| **Topology configuration** | A configuração da topologia selecionada. |

The Storm topology summary page looks similar to this web page:

![Topology summary page, Storm UI, Apache Storm, Azure Insight](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-topology-summary.png)

In the **Topology actions** section, you can select the following buttons to do an action:

| Botão | Descrição |
| --- | --- |
| **Ativar** | Retoma o processamento de uma topologia desativada. |
| **Deactivate** | Pausa uma topologia em execução. |
| **Rebalance** | Ajusta o paralelismo da topologia. You should rebalance running topologies after you've changed the number of nodes in the cluster. This operation allows the topology to adjust parallelism to compensate for the additional or reduced number of nodes in the cluster.<br/><br/>Para saber mais, consulte <a href="https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Noções básicas sobre o paralelismo de uma topologia do Apache Storm</a>.
| **Kill** | Termina uma topologia do Storm após o tempo limite especificado. |
| **Depurar** | Begins a debugging session for the running topology. |
| **Stop Debug** | Ends the debugging session for the running topology. |
| **Change Log Level** | Modifies the debugging log level. |

##### <a name="spout-and-bolt-summary"></a>Spout and bolt summary

Selecionar um spout nas seções **Spouts** ou **Bolts** exibirá as seguintes informações sobre o item selecionado:

| Seção | Descrição |
| --- | --- |
| **Component summary** | Informações básicas sobre o spout ou o bolt. |
| **Component actions** | **Debug** and **Stop Debug** buttons. |
| **Spout stats** or **Bolt stats** | Estatísticas sobre o spout ou o bolt. To set the time frame for an entry in this section, select its link in the **Window** column. |
| (Bolt-only)<br/>**Input stats** *(time frame)* | Informações sobre os fluxos de entrada consumidos pelo bolt. |
| **Output stats** *(time frame)* | Informações sobre os fluxos emitidos pelo spout ou pelo bolt. |
| **Profiling and debugging** | Controls for profiling and debugging the components on this page. You can set the **Status / Timeout (Minutes)** value, and you can select buttons for **JStack**, **Restart Worker**, and **Heap**. |
| **Executors** *(time frame)* | Informações sobre as instâncias do spout ou bolt. To view a log of diagnostic information produced for this instance, select the **Port** entry for a specific executor. You can also see the worker resources associated with a specific executor by selecting its link in the **Host** column. |
| **Erros** | Qualquer informação de erro para o spout ou bolt. |

The Storm bolt summary page looks similar to this web page:

![Bolt summary page, Storm UI, Apache Storm, Azure Insight](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-bolt-summary.png)

## <a name="monitor-and-manage-the-topology-using-the-rest-api"></a>Monitor and manage the topology using the REST API

The Storm UI is built on top of the REST API, so you can do similar management and monitoring tasks by using the REST API. Você pode usar a API REST para criar ferramentas personalizadas para o gerenciamento e o monitoramento de topologias Storm.

Para obter mais informações, veja [API REST da interface do usuário do Apache Storm](https://storm.apache.org/releases/current/STORM-UI-REST-API.html). As informações a seguir são específicas para o uso da API REST com Apache Storm no HDInsight.

> [!IMPORTANT]  
> The Storm REST API is not publicly available over the internet. It must be accessed using an SSH tunnel to the HDInsight cluster head node. For information on creating and using an SSH tunnel, see [Use SSH tunneling to access Azure HDInsight](../hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>URI de base

The base URI for the REST API on Linux-based HDInsight clusters is available at URL address `https://HEADNODEFQDN:8744/api/v1/`, where you replace *HEADNODEFQDN* with the head node. The domain name of the head node is generated during cluster creation and isn't static.

You can find the fully qualified domain name (FQDN) for the cluster head node in several ways:

| FQDN discovery method | Descrição |
| --- | --- |
| SSH session | Use the command `headnode -f` from an SSH session to the cluster. |
| Ambari Web | On the Ambari cluster web page (`https://CLUSTERNAME.azurehdinsight.net`), select **Services** from the top of the page, then select **Storm**. Na guia **Resumo** selecione **Servidor de IU do Storm**. O FQDN do nó que hospeda a interface do usuário do Storm e a API REST são exibidos na parte superior da página. |
| API REST do Ambari | Use the command `curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` to retrieve information about the node that the Storm UI and REST API are running on. Replace the two instances of *CLUSTERNAME* with the cluster name. When you're prompted, enter the password for the user (admin) account. In the response, the "host_name" entry of the JSON output contains the FQDN of the node. |

### <a name="authentication"></a>Authentication

Requests to the REST API must use *basic authentication*, so you have to use the administrator name and password for the HDInsight cluster.

> [!NOTE]  
> Como a autenticação básica é enviada usando texto não criptografado, você *sempre* deverá usar HTTPS para proteger as comunicações com o cluster.

### <a name="return-values"></a>Valores de retorno

As informações retornadas pela API REST só podem ser usadas dentro do cluster. For example, the fully qualified domain name (FQDN) returned for [Apache ZooKeeper](https://zookeeper.apache.org/) servers isn't accessible from the internet.

## <a name="next-steps"></a>Próximos passos

A seguir, aprenda a [Desenvolver topologias baseadas em Java usando o Apache Maven](apache-storm-develop-java-topology.md).

For a list of more example topologies, see [Example Apache Storm topologies in Azure HDInsight](apache-storm-example-topology.md).
