---
title: Gerencie clusters Azure HDInsight usando o Azure CLI
description: Saiba como usar o Azure CLI para gerenciar clusters Azure HDInsight. Os tipos de cluster incluem Apache Hadoop, Spark, HBase, Storm, Kafka, Interactive Query e ML Services.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/26/2020
ms.openlocfilehash: 2c6495454e5ba2449d4b3c74a096681f74610813
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272767"
---
# <a name="manage-azure-hdinsight-clusters-using-azure-cli"></a>Gerencie clusters Azure HDInsight usando o Azure CLI

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Saiba como usar [o Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) para gerenciar clusters Azure HDInsight. A CLI (interface de linha de comando) do Azure é a experiência da linha de comando de plataforma cruzada da Microsoft para gerenciar os recursos do Azure.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* CLI do Azure. Se você ainda não tiver instalado a CLI do Azure, confira [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) para obter as etapas.

* Um cluster do Apache Hadoop no HDInsight. Veja [Get Started com hdinsight no Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="connect-to-azure"></a>Conectar-se ao Azure

Entre em sua assinatura do Azure. Se você planeja usar o Azure Cloud Shell, selecione **Tente-o** no canto superior direito do bloco de código. Caso contrário, insira o comando a seguir:

```azurecli-interactive
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="list-clusters"></a>Listar clusters

Use [a lista az hdinsight](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-list) para listar clusters. Edite os comandos `RESOURCE_GROUP_NAME` abaixo substituindo com o nome do seu grupo de recursos e digite os comandos:

```azurecli-interactive
# List all clusters in the current subscription
az hdinsight list

# List only cluster name and its resource group
az hdinsight list --query "[].{Cluster:name, ResourceGroup:resourceGroup}" --output table

# List all cluster for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME

# List all cluster names for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME --query "[].{clusterName:name}" --output table
```

## <a name="show-cluster"></a>Mostrar cluster

Use [az hdinsight show](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-show) para mostrar informações para um cluster especificado. Edite o comando `RESOURCE_GROUP_NAME`abaixo `CLUSTER_NAME` substituindo e com as informações relevantes, digite o comando:

```azurecli-interactive
az hdinsight show --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

## <a name="delete-clusters"></a>Excluir clusters

Use [az hdinsight delete](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-delete) para excluir um cluster especificado. Edite o comando `RESOURCE_GROUP_NAME`abaixo `CLUSTER_NAME` substituindo e com as informações relevantes, digite o comando:

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

Você também pode excluir um cluster excluindo o grupo de recursos que contém o cluster. Observe, isso excluirá todos os recursos do grupo, incluindo a conta de armazenamento padrão.

```azurecli-interactive
az group delete --name RESOURCE_GROUP_NAME
```

## <a name="scale-clusters"></a>Dimensionar clusters

Use [o redimensionamento az hdinsight](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) para redimensionar o cluster HDInsight especificado para o tamanho especificado. Edite o comando `RESOURCE_GROUP_NAME`abaixo `CLUSTER_NAME` substituindo e com as informações relevantes. Substitua pelo `WORKERNODE_COUNT` número desejado de nós de trabalhador para o seu cluster. Para obter mais informações sobre clusters de dimensionamento, consulte [Clusters Scale HDInsight](./hdinsight-scaling-best-practices.md). Insira o comando:

```azurecli-interactive
az hdinsight resize --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME --workernode-count WORKERNODE_COUNT
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como executar diferentes tarefas administrativas de cluster HDInsight. Para saber mais, leia os seguintes artigos:

* [Gerenciar clusters do Apache Hadoop no HDInsight usando o portal do Azure](hdinsight-administer-use-portal-linux.md)
* [Administrar clusters HDInsight usando o Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Comece com o Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Introdução à CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)
