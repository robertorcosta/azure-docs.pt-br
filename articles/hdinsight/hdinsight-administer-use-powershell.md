---
title: Gerenciar clusters Apache Hadoop com PowerShell - Azure HDInsight
description: Aprenda a executar tarefas administrativas para os clusters do Apache Hadoop no HDInsight usando o Azure PowerShell.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/13/2020
ms.openlocfilehash: 104975e6424ed96d43434a588997957033c31d93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560347"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Gerenciar clusters do Apache Hadoop no HDInsight usando o Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

O Azure PowerShell pode ser usado para controlar e automatizar a implantação e o gerenciamento de suas cargas de trabalho no Azure. Neste artigo, você aprende a gerenciar clusters [Apache Hadoop](https://hadoop.apache.org/) no Azure HDInsight usando o módulo Azure PowerShell Az. Para obter a lista dos cmdlets HDInsight PowerShell, consulte a [referência Az.HDInsight](https://docs.microsoft.com/powershell/module/az.hdinsight).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

O [Módulo Az](https://docs.microsoft.com/powershell/azure/overview) do PowerShell instalado.

## <a name="create-clusters"></a>Criar clusters

Confira [Criar clusters baseados em Linux no HDInsight usando o Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

## <a name="list-clusters"></a>Listar clusters

Use o seguinte comando para listar todos os clusters na assinatura atual:

```powershell
Get-AzHDInsightCluster
```

## <a name="show-cluster"></a>Mostrar cluster

Use o seguinte comando para mostrar os detalhes de um cluster específico na assinatura atual:

```powershell
Get-AzHDInsightCluster -ClusterName <Cluster Name>
```

## <a name="delete-clusters"></a>Excluir clusters

Use o seguinte comando para excluir um cluster:

```powershell
Remove-AzHDInsightCluster -ClusterName <Cluster Name>
```

Você também pode excluir um cluster removendo o grupo de recursos que contém o cluster. Excluir um grupo de recursos excluirá todos os recursos no grupo, incluindo a conta de armazenamento padrão.

```powershell
Remove-AzResourceGroup -Name <Resource Group Name>
```

## <a name="scale-clusters"></a>Dimensionar clusters

O recurso de dimensionamento de clusters permite que você altere o número de nós de trabalhador usados por um cluster em execução no Azure HDInsight sem precisar recriar o cluster. Para alterar o tamanho do cluster Hadoop usando o PowerShell do Azure, execute este comando no computador cliente:

```powershell
Set-AzHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```

 Para obter mais informações sobre clusters de dimensionamento, consulte [Clusters Scale HDInsight](./hdinsight-scaling-best-practices.md).

## <a name="update-http-user-credentials"></a>Atualizar credenciais de usuário HTTP

[Set-AzHDInsightGatewayCredential](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightgatewaycredential) define as credenciais HTTP do gateway de um cluster Azure HDInsight.

```powershell
$clusterName = "CLUSTERNAME"
$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"

Set-AzHDInsightGatewayCredential -ClusterName $clusterName -HttpCredential $credential
```

## <a name="find-the-default-storage-account"></a>Encontrar a conta de armazenamento padrão

O script do PowerShell a seguir demonstra como obter o nome da conta de armazenamento padrão e as informações relacionadas:

```powershell
#Connect-AzAccount
$clusterName = "<HDInsight Cluster Name>"

$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$storageInfo = $clusterInfo.DefaultStorageAccount.split('.')
$defaultStoreageType = $storageInfo[1]
$defaultStorageName = $storageInfo[0]

echo "Default Storage account name: $defaultStorageName"
echo "Default Storage account type: $defaultStoreageType"

if ($defaultStoreageType -eq "blob")
{
    $defaultBlobContainerName = $cluster.DefaultStorageContainer
    $defaultStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey

    echo "Default Blob container name: $defaultBlobContainerName"
    echo "Default Storage account key: $defaultStorageAccountKey"
}
```

## <a name="find-the-resource-group"></a>Encontrar o grupo de recursos

No modo do Gerenciador de Recursos, cada cluster HDInsight pertence a um grupo de recursos do Azure.  Encontrar o grupo de recursos:

```powershell
$clusterName = "<HDInsight Cluster Name>"

$cluster = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $cluster.ResourceGroup
```

## <a name="submit-jobs"></a>Enviar trabalhos

**Enviar trabalhos MapReduce**

Veja [Execute os exemplos MapReduce incluídos no HDInsight](hadoop/apache-hadoop-run-samples-linux.md).

**Para enviar trabalhos do Apache Hive**

Consulte [Executar consultas do Apache Hive usando o PowerShell](hadoop/apache-hadoop-use-hive-powershell.md).

**Para enviar trabalhos do Apache Sqoop**

Veja [Use o Apache Sqoop com o HDInsight](hadoop/hdinsight-use-sqoop.md).

**Para enviar trabalhos do Apache Oozie**

Consulte [Use o Apache Oozie com o Apache Hadoop para definir e executar um fluxo de trabalho no HDInsight](hdinsight-use-oozie-linux-mac.md).

## <a name="upload-data-to-azure-blob-storage"></a>Carregar dados no armazenamento de Blob do Azure

Veja [Carregar dados no HDInsight](hdinsight-upload-data.md).

## <a name="see-also"></a>Consulte também

* [Cmdlets Az.HDInsight](https://docs.microsoft.com/powershell/module/az.hdinsight/?view=azps-3.1.0#hdinsight)
* [Gerenciar clusters do Apache Hadoop no HDInsight usando o portal do Azure](hdinsight-administer-use-portal-linux.md)
* [Administrar o HDInsight usando uma interface de linha de comando](hdinsight-administer-use-command-line.md)
* [Criar clusters do HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Enviar trabalhos do Apache Hadoop de forma programática](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Comece com o Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
