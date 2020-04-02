---
title: Personalize as configurações do cluster Azure HDInsight usando bootstrap
description: Aprenda a personalizar a configuração do cluster HDInsight de forma programática usando modelos .NET, PowerShell e Resource Manager.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/01/2020
ms.openlocfilehash: 796dbc53d1adf310028e06dea319b9a60d5cf54b
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529360"
---
# <a name="customize-hdinsight-clusters-using-bootstrap"></a>Personalizar clusters do HDInsight usando a Inicialização

Os scripts Bootstrap permitem instalar e configurar componentes no Azure HDInsight de forma programática.

Existem três abordagens para definir as configurações de arquivo à medida que o cluster HDInsight é criado:

* Usar PowerShell do Azure
* Usar o SDK do .NET
* Usar o modelo do Azure Resource Manager

Por exemplo, usando esses métodos programáticos, você pode configurar opções nesses arquivos:

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml
* server.properties (configuração do kafka broker)

Para obter informações sobre a instalação de componentes adicionais no cluster HDInsight durante o tempo de criação, consulte [Personalizar clusters HDInsight usando Script Action (Linux)](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="prerequisites"></a>Pré-requisitos

* Se estiver usando o PowerShell, você precisará do [Módulo AZ](https://docs.microsoft.com/powershell/azure/overview).

## <a name="use-azure-powershell"></a>Usar PowerShell do Azure

O seguinte código do PowerShell personaliza uma configuração de [Apache Hive](https://hive.apache.org/):

> [!IMPORTANT]  
> O parâmetro `Spark2Defaults` pode precisar ser usado com [Add-AzHDInsightConfigValue](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalue). Você pode passar valores vazios para o parâmetro como mostrado no exemplo de código abaixo.

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90s" }

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValue `
        -HiveSite $hiveConfigValues `
        -Spark2Defaults @{}

New-AzHDInsightCluster `
    -ResourceGroupName $existingResourceGroupName `
    -ClusterName $clusterName `
    -Location $location `
    -ClusterSizeInNodes $clusterSizeInNodes `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.6" `
    -HttpCredential $httpCredential `
    -Config $config
```

Um script do PowerShell completo em funcionamento pode ser encontrado no [Apêndice](#appendix-powershell-sample).

**Para verificar a alteração:**

1. Navegue `https://CLUSTERNAME.azurehdinsight.net/` `CLUSTERNAME` até onde está o nome do seu cluster.
1. Do menu esquerdo, navegue até **hive** > **Configs** > **Advanced**.
1. Expandir **o local avançado da colmeia**.
1. Localize **hive.metastore.client.socket.timeout** e confirme que o valor é **de 90s**.

Alguns outros exemplos de personalização de outros arquivos de configuração:

```xml
# hdfs-site.xml configuration
$HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1

# core-site.xml configuration
$CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50

# mapred-site.xml configuration
$MapRedConfigValues = @{ "mapreduce.task.timeout"="1200000" } #default 600000

# oozie-site.xml configuration
$OozieConfigValues = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120
```

## <a name="use-net-sdk"></a>Usar o SDK do .NET

Consulte [o Azure HDInsight SDK para .NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet).

## <a name="use-resource-manager-template"></a>Usar modelo do Resource Manager

Você pode usar o bootstrap no modelo do Resource Manager:

```json
"configurations": {
    "hive-site": {
        "hive.metastore.client.connect.retry.delay": "5",
        "hive.execution.engine": "mr",
        "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
    }
}
```

![Hadoop personaliza o modelo de gerenciador de recursos do Cluster Bootstrap Azure](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)

Exemplo de recurso de modelo do Resource Manager para alternar a configuração em spark2-defaults para limpar periodicamente os logs de eventos do armazenamento.  

```json
"configurations": {
    "spark2-defaults": {
        "spark.history.fs.cleaner.enabled": "true",
        "spark.history.fs.cleaner.interval": "7d",
        "spark.history.fs.cleaner.maxAge": "90d"
    }
}
```

## <a name="see-also"></a>Confira também

* [Criar clusters Apache Hadoop no HDInsight](hdinsight-hadoop-provision-linux-clusters.md) fornece instruções sobre como criar um cluster HDInsight usando outras opções personalizadas.
* [Desenvolver scripts de ação de script para HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Instalar e usar o Apache Spark em clusters HDInsight](spark/apache-spark-jupyter-spark-sql-use-portal.md)
* [Instale e use o Apache Giraph em clusters HDInsight](hdinsight-hadoop-giraph-install.md).

## <a name="appendix-powershell-sample"></a>Apêndice: amostra do PowerShell

Este script PowerShell cria um cluster HDInsight e personaliza uma configuração de Colmeia. Certifique-se de `$nameToken`inserir `$httpPassword`valores para , e `$sshPassword`.

```powershell
####################################
# Set these variables
####################################
#region - used for creating Azure service names
$nameToken = "<ENTER AN ALIAS>"
#endregion

#region - cluster user accounts
$httpUserName = "admin"  #HDInsight cluster username
$httpPassword = '<ENTER A PASSWORD>'

$sshUserName = "sshuser" #HDInsight ssh user name
$sshPassword = '<ENTER A PASSWORD>'
#endregion

####################################
# Service names and varialbes
####################################
#region - service names
$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

$resourceGroupName = $namePrefix + "rg"
$hdinsightClusterName = $namePrefix + "hdi"
$defaultStorageAccountName = $namePrefix + "store"
$defaultBlobContainerName = $hdinsightClusterName

$location = "East US"
#endregion


####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

#endregion

#region - Create an HDInsight cluster
####################################
# Create dependent components
####################################
Write-Host "Creating a resource group ..." -ForegroundColor Green
New-AzResourceGroup `
    -Name  $resourceGroupName `
    -Location $location

Write-Host "Creating the default storage account and default blob container ..."  -ForegroundColor Green
New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableHttpsTrafficOnly 1

# Note: Storage account kind BlobStorage cannot be used as primary storage.

$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value

$defaultStorageContext = New-AzStorageContext `
                                -StorageAccountName $defaultStorageAccountName `
                                -StorageAccountKey $defaultStorageAccountKey

New-AzStorageContainer `
    -Name $defaultBlobContainerName `
    -Context $defaultStorageContext #use the cluster name as the container name

####################################
# Create a configuration object
####################################
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90s"}

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValue `
        -HiveSite $hiveConfigValues `
        -Spark2Defaults @{}

####################################
# Create an HDInsight cluster
####################################
$httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

$sshPW = ConvertTo-SecureString -String $sshPassword -AsPlainText -Force
$sshCredential = New-Object System.Management.Automation.PSCredential($sshUserName,$sshPW)

New-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -Location $location `
    -ClusterSizeInNodes 1 `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.6" `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -Config $config

####################################
# Verify the cluster
####################################
Get-AzHDInsightCluster `
    -ClusterName $hdinsightClusterName

#endregion
```
