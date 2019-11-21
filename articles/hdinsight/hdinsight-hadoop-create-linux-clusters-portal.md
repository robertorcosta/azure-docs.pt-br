---
title: Create Apache Hadoop clusters using web browser, Azure HDInsight
description: Learn how to create Apache Hadoop, Apache HBase, Apache Storm, or Apache Spark clusters on Linux for HDInsight by using a web browser and the Azure portal.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: 820ddb8d06cfd2aac2b053305f23ad330e4fd7c3
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74215893"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>Criar clusters baseados em Linux no HDInsight usando o portal do Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

O portal do Azure é uma ferramenta de gerenciamento baseada na Web para serviços e recursos hospedados na nuvem do Microsoft Azure. Neste artigo, você aprenderá como criar clusters Azure HDInsight baseados no Linux usando o portal.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Pré-requisitos

* **Uma assinatura do Azure**. Consulte [Como obter a avaliação gratuita do Azure para testar o Hadoop no HDInsight](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Um navegador da Web**. O portal do Azure usa HTML5 e JavaScript. É possível que não funcione corretamente em navegadores da Web mais antigos.

## <a name="create-clusters"></a>Criar clusters

O portal do Azure expõe a maioria das propriedades do cluster. Usando modelos do Azure Resource Manager, é possível ocultar muitos detalhes. Para obter mais informações, consulte [Criar clusters Apache Hadoop no HDInsight usando modelos do Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

1. Entre no [portal do Azure](https://portal.azure.com).

1. From the left menu, navigate to **+ Create a resource** >  **Analytics** > **Azure HDInsight**.

    ![Create a new cluster in the Azure portal](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster.png "Criando um novo cluster no portal do Azure")

1. From the **Create HDInsight cluster** page, select **Go to classic create experience**.

    ![Go to classic create experience](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-create-classic.png)

1. Na página **HDInsight**, selecione **Personalizado (tamanho, configurações, aplicativos)** .

1. Selecione **1 Básico**. Then enter the following information:

    |Propriedade |Descrição |
    |---|---|
    |Nome do cluster|Esse nome deve ser globalmente exclusivo.|
    |Subscription|From the drop-down list, select the Azure subscription that's used for the cluster.|
    |Tipo de cluster|Select the type of cluster you want to create. Os exemplos são Hadoop e Apache Spark. O **Sistema operacional** será **Linux**. Em seguida, selecione uma versão do tipo de cluster. Use a versão padrão se não souber o que escolher. Para obter mais informações, consulte [Versões de cluster do HDInsight](hdinsight-component-versioning.md).|
    |Nome de usuário de logon do cluster|Provide the username, default is **admin**.|
    |Senha de logon do cluster|Provide the password.|
    |Nome de usuário do Secure Shell (SSH)|Default is **sshuser**. If you want the same SSH password as the admin password you specified earlier, select the **Use cluster login password for SSH** check box. Caso contrário, forneça uma **SENHA** ou **CHAVE PÚBLICA** para autenticar o usuário SSH. Uma chave pública é a abordagem recomendada. Escolha **Selecionar** na parte inferior para salvar a configuração das credenciais.  Para obter mais informações, consulte [Conectar HDInsight (Apache Hadoop) usando SSH](hdinsight-hadoop-linux-use-ssh-unix.md).|
    |Resource group|Especifique se deseja criar um novo grupo de recursos ou usar um existente.|
    |Location|Specify a datacenter where the cluster is created.|

    ![HDInsight create cluster basics](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-basics.png "Criando um novo cluster no portal do Azure")

    > [!IMPORTANT]  
    > Os clusters HDInsight são fornecidos em vários tipos. Eles correspondem à carga de trabalho ou tecnologia para a qual o cluster está sintonizado. Não há nenhum método com suporte para criar um cluster que combine vários tipos. Exemplos são Storm e HBase em um cluster.

    Selecione **Próximo** para ir para a próxima página.

1. Em **2 Segurança + rede**, é possível conectar o cluster a uma rede virtual, usando o menu suspenso fornecido. Selecione uma rede virtual do Azure e a sub-rede se você desejar colocar o cluster em uma rede virtual. For information on using HDInsight with a virtual network, see [Plan a virtual network deployment for Azure HDInsight clusters](hdinsight-plan-virtual-network-deployment.md). O artigo inclui requisitos de configuração específicos para a rede virtual.

    If you want to use the **Enterprise Security Package**, follow these instructions: [Configure a HDInsight cluster with Enterprise Security Package by using Azure Active Directory Domain Services](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds).

    Selecione **Próximo** para ir para a próxima página.

1. From **3 Storage**, for **Storage Account Settings**, specify whether you want Azure Storage or Azure Data Lake Storage as your default storage. Para obter mais informações, confira a tabela a seguir.

    | Primary Storage type | Descrição |
    |------------------|-------------|
    | Armazenamento do Azure   |  * For **Selection method**, choose **My subscriptions** if you want to specify a storage account that's part of your Azure subscription. Em seguida, selecione a conta de armazenamento. Caso contrário, selecione **Chave de acesso**. Em seguida, forneça as informações da conta de armazenamento que você quer escolher fora da sua assinatura do Azure.</br></br> * For **Default container**, choose the default container name suggested by the portal or specify your own.</br></br> * If Azure Blob storage is your default storage, you can also select **Additional Storage Accounts** to specify additional storage accounts to associate with the cluster. Para **Chaves de Armazenamento do Azure**, selecione **Adicionar uma chave de armazenamento**. Em seguida, você poderá fornecer uma conta de armazenamento a partir das suas assinaturas do Azure, ou de outras assinaturas. Forneça a chave de acesso à conta de armazenamento.</br></br> * If Blob storage is your default storage, you can also select **Data Lake Storage access** to specify Azure Data Lake Storage as additional storage. Para obter mais informações, veja [Início Rápido: Configurar clusters Hadoop no HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).</li></ul> |
    | Azure Data Lake Store | Select **Azure Data Lake Storage Gen1** or **Azure Data Lake Storage Gen2**. Then refer to the article [Quickstart: Set up clusters in HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) for instructions. |

    **Metastore Settings (optional)**

    Como opção, especifique um banco de dados SQL para salvar metadados do Apache Hive e do Apache Oozie associados ao cluster. Para **Selecionar um banco de dados SQL para Hive**, selecione um banco de dados SQL. Em seguida, forneça o nome de usuário e a senha para o banco de dados. Repita essas etapas para metadados do Oozie.

    Algumas considerações sobre o uso do banco de dados SQL do Azure para metastores são as seguintes:
    * O banco de dados SQL do Azure usado para o metastore deve permitir conectividade com outros serviços do Azure, incluindo o Azure HDInsight. No lado direito do painel do banco de dados SQL do Azure, selecione o nome do servidor. Esse servidor é aquele no qual a instância do banco de dados SQL é executada. Após exibir o servidor, selecione **Configurar**. Em seguida, para **Serviços do Azure**, selecione **Sim**. Em seguida, selecione **Salvar**.
    * Ao criar um metastore, não use traços nem hifens para nomear um banco de dados. Esses caracteres podem causar falha no processo de criação do cluster.

    ![HDInsight create cluster storage](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-storage.png "Criando um novo cluster no portal do Azure")

    > [!WARNING]  
    > Não há suporte para o uso de uma conta de armazenamento adicional em uma localização diferente do cluster HDInsight.

    Selecione **Próximo** para ir para a próxima página.

1. Em **4 Aplicativos (opcional)** , selecione os aplicativos desejados. Você, a Microsoft ou os fornecedores independentes de software (ISVs)  podem desenvolver esses aplicativos. Para obter mais informações, consulte [Instalar aplicativos durante a criação do cluster](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).

    Selecione **Próximo** para ir para a próxima página.

1. **5 Tamanho do cluster** mostra informações sobre os nós usados para esse cluster. Defina o número de nós de trabalho que você precisa para o cluster. O custo estimado de executar o cluster também é mostrado.

    ![HDInsight create cluster nodes](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-nodes.png "Specify number of cluster nodes")

   > [!IMPORTANT]  
   > Se você planeja mais de 32 nós de trabalho, selecione um tamanho de nó principal com pelo menos oito núcleos e 14 GB de RAM. Planeje os nós na criação de cluster ou, dimensionando o cluster após a criação.
   >
   > Para obter mais informações sobre tamanhos de nós e custos associados, consulte [Preços do Azure HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

    Selecione **Próximo** para ir para a próxima página.

1. Em **6 Ações de script**, você pode personalizar um cluster para instalar componentes personalizados. Essa opção funcionará se você quiser usar um script personalizado para personalizar um cluster, enquanto o cluster estiver sendo criado. Para obter mais informações sobre ações de script, consulte [Personalizar clusters HDInsight baseados em Linux usando ações de script](hdinsight-hadoop-customize-cluster-linux.md).

   Selecione **Próximo** para ir para a próxima página.

1. Em **7 Resumo**, verifique as informações inseridas anteriormente. Em seguida, selecione **Criar**.

    ![HDInsight create cluster summary](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-summary.png "Specify number of cluster nodes")

    > [!NOTE]  
    > Levará algum tempo para que o cluster seja criado, geralmente, cerca de 20 minutos. Monitore as **Notificações** para conferir o processo de provisionamento.

1. Após o término do processo de criação, selecione **Ir para Recurso** na notificação **Implantação com êxito**. A janela do cluster fornece as informações a seguir.

    ![HDI Azure portal cluster overview](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "Propriedades do cluster")

    Some of the icons in the window are explained as follows:

    |Propriedade | Descrição |
    |---|---|
    |Visão Geral|Provides all the essential information about the cluster. Exemplos são o nome, o grupo de recursos ao qual ele pertence, a localização, o sistema operacional e a URL do painel do cluster.|
    |Painéis de cluster|Directs you to the Ambari portal associated with the cluster.|
    |SSH + Cluster login|Provides information needed to access the cluster by using SSH.|
    |Excluir|exclui o cluster HDInsight.|

## <a name="customize-clusters"></a>Personalizar clusters

* [Personalizar clusters HDInsight usando Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md)
* [Personalizar clusters HDInsight baseados em Linux usando ações de script](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="delete-the-cluster"></a>Excluir o cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Solucionar problemas

Se você tiver problemas com a criação de clusters HDInsight, confira os [requisitos de controle de acesso](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Próximos passos

Você criou com êxito um cluster HDInsight. Saiba agora como trabalhar com o cluster.

### <a name="apache-hadoop-clusters"></a>Clusters do Apache Hadoop

* [Usar o Apache Hive com o HDInsight](hadoop/hdinsight-use-hive.md)
* [Usar o MapReduce com o HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Clusters do Apache HBase

* [Introdução ao Apache HBase no HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Desenvolvimento de aplicativos Java para Apache HBase no HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Clusters do Apache Storm

* [Desenvolver topologias em Java para o Apache Storm no HDInsight](storm/apache-storm-develop-java-topology.md)
* [Usar componentes de Python no Apache Storm no HDInsight](storm/apache-storm-develop-python-topology.md)
* [Implantar e monitorar topologias com o Apache Storm no HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Clusters do Apache Spark

* [Criar um aplicativo independente usando o Scala](spark/apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster Apache Spark usando Apache Livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark com BI: realize a análise interativa de dados usando o Spark no HDInsight com ferramentas de BI](spark/apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: use o Spark no HDInsight para prever os resultados da inspeção de alimentos](spark/apache-spark-machine-learning-mllib-ipython.md)
