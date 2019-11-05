---
title: Criar clusters do Apache Hadoop usando o PowerShell - Azure HDInsight
description: Aprenda a criar clusters Apache Hadoop, Apache HBase, Apache Storm ou Apache Spark no Linux para HDInsight usando o Azure PowerShell.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: hrasheed
ms.openlocfilehash: a6847e75a0a6dcf944b033054ac466841294d28b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494788"
---
# <a name="create-linux-based-clusters-in-hdinsight-using-azure-powershell"></a>Criar clusters baseados em Linux no HDInsight usando o Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

O Azure PowerShell é um ambiente de script poderoso que você pode usar para controlar e para automatizar a implantação e o gerenciamento de suas cargas de trabalho no Microsoft Azure. Este documento fornece informações sobre como criar um cluster HDInsight baseadas em Linux usando o Azure PowerShell. Ele também inclui um script de exemplo.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Você deve ter o seguinte antes de iniciar este procedimento:

* Uma assinatura do Azure. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [PowerShell do Azure](/powershell/azure/install-Az-ps)

    > [!IMPORTANT]  
    > O suporte do Azure PowerShell para gerenciar os recursos do HDInsight usando o Gerenciador de Serviços do Azure está **preterido** e foi removido em 1º de janeiro de 2017. As etapas neste documento usam os novos cmdlets do HDInsight que funcionam com o Azure Resource Manager.
    >
    > Siga as etapas em [Instalar o Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) para instalar a versão mais recente do Azure PowerShell. Se você tiver scripts que precisam ser modificados para usar os novos cmdlets que funcionam com o Azure Resource Manager, confira [Migrando para as ferramentas de desenvolvimento baseadas no Azure Resource Manager dos clusters de HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md) para obter mais informações.

## <a name="create-cluster"></a>Criar cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Para criar um cluster HDInsight usando o Azure PowerShell, siga estes procedimentos:

* Criar um grupo de recursos do Azure
* Criar uma conta de Armazenamento do Azure
* Criar um contêiner de Blob do Azure
* Crie um cluster HDInsight

O script a seguir demonstra como criar um novo cluster:

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster.ps1?range=5-71)]

Os valores especificados para o logon de cluster são usados para criar a conta de usuário do Hadoop para o cluster. Use essa conta para se conectar a serviços hospedados no cluster, como interfaces do usuário da Web ou APIs REST.

Os valores especificados para o usuário SSH são usados para criar o usuário SSH para o cluster. Use essa conta para iniciar uma sessão SSH remota no cluster e executar trabalhos. Para saber mais, consulte o documento [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

> [!IMPORTANT]  
> Se você planeja ter mais de 32 nós de trabalho (seja na criação do cluster ou em seu dimensionamento após a criação), deverá também especificar um tamanho de nó de cabeçalho com pelo menos oito núcleos e 14 GB de RAM.
>
> Para saber mais sobre tamanhos de nós e custos associados, consulte [Preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Pode levar até 20 minutos para criar um cluster.

## <a name="create-cluster-configuration-object"></a>Criar cluster: objeto de configuração

Você também pode criar um objeto de configuração de HDInsight usando o cmdlet `New-AzHDInsightClusterConfig`. Será possível, então, modificar esse objeto de configuração para habilitar as opções de configuração adicionais para o cluster. Por fim, use o parâmetro `-Config` do cmdlet `New-AzHDInsightCluster` para usar a configuração.

O script a seguir cria um objeto de configuração para configurar um R Server no tipo de cluster HDInsight. A configuração habilita um nó de borda, RStudio e uma conta de armazenamento adicionais.

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster-with-config.ps1?range=59-99)]

> [!WARNING]  
> Não há suporte para o uso de uma conta de armazenamento em um local diferente do cluster HDInsight. Ao usar este exemplo, crie a conta de armazenamento adicional no mesmo local do servidor.

## <a name="customize-clusters"></a>Personalizar clusters

* Consulte [Personalizar clusters do HDInsight usando a Inicialização](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
* Consulte [Personalizar os clusters HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>Excluir o cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Solucionar problemas

Se você tiver problemas com a criação de clusters HDInsight, confira os [requisitos de controle de acesso](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Próximas etapas

Agora que você criou com êxito um cluster HDInsight, use os seguintes recursos para aprender a trabalhar com o seu cluster.

### <a name="apache-hadoop-clusters"></a>Clusters do Apache Hadoop

* [Usar o Apache Hive com o HDInsight](hadoop/hdinsight-use-hive.md)
* [Usar o Apache Pig com o HDInsight](hadoop/hdinsight-use-pig.md)
* [Usar o MapReduce com o HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Clusters do Apache HBase

* [Introdução ao Apache HBase no HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Desenvolvimento de aplicativos Java para Apache HBase no HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Clusters Storm

* [Desenvolver topologias Java para Storm no HDInsight](storm/apache-storm-develop-java-topology.md)
* [Usar componentes de Python no Storm no HDInsight](storm/apache-storm-develop-python-topology.md)
* [Implantar e monitorar topologias com o Storm no HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Clusters do Apache Spark

* [Criar um aplicativo autônomo usando Scala](spark/apache-spark-create-standalone-application.md)
* [Execute trabalhos remotamente em um cluster do Apache Spark usando o Apache Livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark com BI: execute análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](spark/apache-spark-use-bi-tools.md)
* [Apache Spark com aprendizado de máquina: use o Spark no HDInsight para prever os resultados da inspeção de alimentos](spark/apache-spark-machine-learning-mllib-ipython.md)

