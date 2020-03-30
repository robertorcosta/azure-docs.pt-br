---
title: Crie clusters Apache Hadoop usando Azure CLI - Azure HDInsight
description: Saiba como criar clusters Azure HDInsight usando a CLI multiplataforma Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/03/2020
ms.openlocfilehash: b9d935e72c67b78484337e39e0897d4962340636
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77199034"
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>Criar os clusters do HDInsight usando a CLI do Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

As etapas deste documento passo-a-passo criando um cluster HDInsight 3.6 usando o Cli Do Zure.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

CLI do Azure. Se você ainda não tiver instalado a CLI do Azure, confira [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) para obter as etapas.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-cluster"></a>Criar um cluster

1. Acesse a sua assinatura do Azure. Se você planeja usar o Azure Cloud Shell, selecione **Tente-o** no canto superior direito do bloco de código. Caso contrário, insira o comando a seguir:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Configure as variáveis de ambiente. O uso de variáveis neste artigo é baseado em Bash. Pequenas variações serão necessárias para outros ambientes. Consulte [az-hdinsight-create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) para obter uma lista completa de possíveis parâmetros para criação de clusters.

    |Parâmetro | Descrição |
    |---|---|
    |`--workernode-count`| O número de nódulos operários no cluster. Este artigo usa `clusterSizeInNodes` a variável `--workernode-count`como o valor passou para . |
    |`--version`| a versão do cluster do HDInsight. Este artigo usa `clusterVersion` a variável `--version`como o valor passou para . Veja também: [Versões hdinsight suportadas](./hdinsight-component-versioning.md#supported-hdinsight-versions).|
    |`--type`| Tipo de cluster HDInsight, como: hadoop, interactivehive, hbase, kafka, storm, spark, rserver, mlservices.  Este artigo usa `clusterType` a variável `--type`como o valor passou para . Veja também: [Tipos de cluster e configuração](./hdinsight-hadoop-provision-linux-clusters.md#cluster-type).|
    |`--component-version`|As versões de vários componentes Hadoop, em versões separadas pelo espaço no formato 'component=version'. Este artigo usa `componentVersion` a variável `--component-version`como o valor passou para . Veja também: [Componentes hadoop](./hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions).|

    `RESOURCEGROUPNAME`Substitua `LOCATION` `CLUSTERNAME`, `STORAGEACCOUNTNAME`e `PASSWORD` com os valores desejados. Alterar valores para as outras variáveis conforme desejado. Em seguida, digite os comandos CLI.

    ```azurecli-interactive
    export resourceGroupName=RESOURCEGROUPNAME
    export location=LOCATION
    export clusterName=CLUSTERNAME
    export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME
    export httpCredential='PASSWORD'
    export sshCredentials='PASSWORD'

    export AZURE_STORAGE_CONTAINER=$clusterName
    export clusterSizeInNodes=1
    export clusterVersion=3.6
    export clusterType=hadoop
    export componentVersion=Hadoop=2.7
    ```

3. [Crie o grupo de recursos](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) inserindo o comando abaixo:

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

    Para obter uma lista de `az account list-locations` locais válidos, use o `name` comando e use um dos locais do valor.

4. [Crie uma conta do Azure Storage](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) inserindo o comando abaixo:

    ```azurecli-interactive
    # Note: kind BlobStorage is not available as the default storage account.
    az storage account create \
        --name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

5. [Extrair a chave principal da conta do Azure Storage](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest#az-storage-account-keys-list) e armazená-la em uma variável inserindo o comando abaixo:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. [Crie um contêiner Azure Storage](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) digitando o comando abaixo:

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. [Crie o cluster HDInsight](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) inserindo o seguinte comando:

    ```azurecli-interactive
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpCredential \
        --http-user admin \
        --location $location \
        --workernode-count $clusterSizeInNodes \
        --ssh-password $sshCredentials \
        --ssh-user sshuser \
        --storage-account $AZURE_STORAGE_ACCOUNT \
        --storage-account-key $AZURE_STORAGE_KEY \
        --storage-container $AZURE_STORAGE_CONTAINER \
        --version $clusterVersion
    ```

    > [!IMPORTANT]  
    > Clusters HDInsight são fornecidos em uma variedade de tipos que correspondem à carga de trabalho ou à tecnologia para a qual o cluster está ajustado. Não há nenhum método com suporte para criar um cluster que combina vários tipos, como o Storm e HBase em um cluster.

    Pode levar vários minutos para que o processo de criação de clusters seja concluído. Geralmente, cerca de 15 minutos.

## <a name="clean-up-resources"></a>Limpar recursos

Após a conclusão do artigo, convém excluir o cluster. Com o HDInsight, seus dados são armazenados no Armazenamento do Azure, assim você poderá excluir, com segurança, um cluster quando ele não estiver em uso. Você também é cobrado por um cluster HDInsight, mesmo quando ele não está em uso. Como os encargos para o cluster são muitas vezes maiores do que os encargos para armazenamento, faz sentido, do ponto de vista econômico, excluir os clusters quando não estiverem em uso.

Insira todos ou alguns dos comandos a seguir para remover recursos:

```azurecli-interactive
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $AZURE_STORAGE_ACCOUNT \
    --name $AZURE_STORAGE_CONTAINER

# Remove storage account
az storage account delete \
    --name $AZURE_STORAGE_ACCOUNT \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="troubleshoot"></a>Solução de problemas

Se você tiver problemas com a criação de clusters HDInsight, confira os [requisitos de controle de acesso](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Próximas etapas

Agora que você criou com sucesso um cluster HDInsight usando o Azure CLI, use o seguinte para aprender como trabalhar com seu cluster:

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
