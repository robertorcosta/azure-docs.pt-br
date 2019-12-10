---
title: Gerenciar clusters do Azure HDInsight usando o CLI do Azure
description: Saiba como usar o CLI do Azure para gerenciar clusters do Azure HDInsight. Os tipos de cluster incluem Apache Hadoop, Spark, HBase, Storm, Kafka, consulta interativa e serviços de ML.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/06/2019
ms.openlocfilehash: 81bc632f1061f0ee73d2295cafa5f7a8472d20ee
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951795"
---
# <a name="manage-azure-hdinsight-clusters-using-azure-cli"></a>Gerenciar clusters do Azure HDInsight usando o CLI do Azure

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Saiba como usar [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) para gerenciar clusters do Azure HDInsight. A CLI (interface de linha de comando) do Azure é a experiência da linha de comando de plataforma cruzada da Microsoft para gerenciar os recursos do Azure.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* CLI do Azure. Se você ainda não tiver instalado a CLI do Azure, confira [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) para obter as etapas.

* Um cluster do Apache Hadoop no HDInsight. Consulte [Introdução ao HDInsight no Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="connect-to-azure"></a>Conecte-se ao Azure

Entre em sua assinatura do Azure. Se você planeja usar Azure Cloud Shell, selecione **Experimente** no canto superior direito do bloco de código. Caso contrário, insira o comando a seguir:

```azurecli-interactive
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="list-clusters"></a>Listar clusters

Use [AZ hdinsight List](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-list) para listar clusters. Edite os comandos a seguir, substituindo `RESOURCE_GROUP_NAME` pelo nome do seu grupo de recursos e, em seguida, insira os comandos:

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

Use [AZ hdinsight show](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-show) para mostrar informações de um cluster especificado. Edite o comando a seguir substituindo `RESOURCE_GROUP_NAME`e `CLUSTER_NAME` com as informações relevantes e, em seguida, digite o comando:

```azurecli-interactive
az hdinsight show --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

## <a name="delete-clusters"></a>Excluir clusters

Use [AZ hdinsight Delete](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-delete) para excluir um cluster especificado. Edite o comando a seguir substituindo `RESOURCE_GROUP_NAME`e `CLUSTER_NAME` com as informações relevantes e, em seguida, digite o comando:

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

Você também pode excluir um cluster excluindo o grupo de recursos que contém o cluster. Observe que isso excluirá todos os recursos no grupo, incluindo a conta de armazenamento padrão.

```azurecli-interactive
az group delete --name RESOURCE_GROUP_NAME
```

## <a name="scale-clusters"></a>Escalonar clusters

Use [AZ hdinsight Resize](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) para redimensionar o cluster hdinsight especificado para o tamanho especificado. Edite o comando a seguir, substituindo `RESOURCE_GROUP_NAME`e `CLUSTER_NAME` com as informações relevantes. Substitua `TARGET_INSTANCE_COUNT` pelo número desejado de nós de trabalho para o cluster. Para obter mais informações sobre o dimensionamento de clusters, consulte [dimensionar clusters HDInsight](./hdinsight-scaling-best-practices.md). Insira o comando:

```azurecli-interactive
az hdinsight resize --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME --target-instance-count TARGET_INSTANCE_COUNT
```

## <a name="next-steps"></a>Próximos passos

Neste artigo, você aprendeu a executar diferentes tarefas administrativas do cluster HDInsight. Para saber mais, consulte os seguintes artigos:

* [Gerenciar clusters do Apache Hadoop no HDInsight usando o portal do Azure](hdinsight-administer-use-portal-linux.md)
* [Administrar o HDInsight usando o Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Introdução ao Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Introdução à CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)
