---
title: Adicione contas adicionais de armazenamento Azure ao HDInsight
description: Saiba como adicionar contas adicionais do Azure Storage a um cluster HDInsight existente.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 87eb04b7323186175195babf6a602fa12d25176f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206700"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Adicionar outras contas de armazenamento ao HDInsight

Aprenda a usar ações de script para adicionar *contas* adicionais do Azure Storage ao HDInsight. As etapas deste documento adicionam uma *conta* de armazenamento a um cluster HDInsight existente. Este artigo se aplica a *contas* de armazenamento (não à conta de armazenamento de cluster padrão) e não ao armazenamento adicional, como [o Azure Data Lake Storage Gen1](hdinsight-hadoop-use-data-lake-store.md) e [o Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md).

> [!IMPORTANT]  
> As informações neste documento são sobre adicionar conta de armazenamento adicional a um cluster depois de criada. Para saber mais sobre como adicionar contas de armazenamento durante a criação do cluster, veja [Configurar clusters no HDInsight com Apache Hadoop, Apache Spark, Apache Kafka e mais](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster Hadoop no HDInsight. Veja [Get Started com hdinsight no Linux](./hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Nome e chave da conta de armazenamento. Consulte [Gerenciar as chaves de acesso da conta de armazenamento](../storage/common/storage-account-keys-manage.md).
* Se estiver usando o PowerShell, você precisará do módulo AZ.  Ver [Visão Geral do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

## <a name="how-it-works"></a>Como ele funciona

Durante o processamento, o script executa as ações a seguir:

* Se a conta de armazenamento já existir na configuração de core-site.xml do cluster, o script será encerrado e nenhuma ação será executada.

* Verifica se a conta de armazenamento existe e se pode ser acessada usando a chave.

* Criptografa a chave usando a credencial do cluster.

* Adiciona a conta de armazenamento ao arquivo core-site.xml.

* Pare e reinicie o [Apache Oozie](https://oozie.apache.org/), [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), [Apache Hadoop MapReduce2](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html), e [HDFS do Apache Hadoop](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) serviços. Interromper e iniciar esses serviços permite que eles usem a nova conta de armazenamento.

> [!WARNING]  
> Não há suporte para o uso de uma conta de armazenamento em um local diferente do cluster HDInsight.

## <a name="add-storage-account"></a>Nova conta de armazenamento

Use [a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster) para aplicar as alterações com as seguintes considerações:

|Propriedade | Valor |
|---|---|
|URI do script Bash|`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`|
|Tipo(s) de nó|Head|
|Parâmetros|`ACCOUNTNAME``ACCOUNTKEY` `-p` (opcional)|

* `ACCOUNTNAME`é o nome da conta de armazenamento para adicionar ao cluster HDInsight.
* `ACCOUNTKEY`é a chave `ACCOUNTNAME`de acesso para .
* `-p` é opcional. Se especificado, a chave não é criptografada e é armazenada no arquivo core-site.xml como texto simples.

## <a name="verification"></a>Verificação

Ao visualizar o cluster HDInsight no portal Azure, selecionar a entrada __Contas de armazenamento__ em __Propriedades__ não exibe contas de armazenamento adicionadas através desta ação de script. O Azure PowerShell e o Azure CLI também não exibem a conta de armazenamento adicional. As informações de armazenamento não são exibidas `core-site.xml` porque o script modifica apenas a configuração para o cluster. Essas informações não são usadas ao recuperar as informações de cluster usando APIs de gerenciamento do Azure.

Para verificar o armazenamento adicional, use um dos métodos mostrados abaixo:

### <a name="powershell"></a>PowerShell

O script retornará o nome da conta de armazenamento associado ao determinado cluster. Substitua pelo `CLUSTERNAME` nome real do cluster e execute o script.

```powershell
# Update values
$clusterName = "CLUSTERNAME"

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"

$clusterName = $clusterName.ToLower();

# getting service_config_version
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content

$configVersion=$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content

# extract account names
$value = ($respObj.items.configurations | Where type -EQ "core-site").properties | Get-Member -membertype properties | Where Name -Like "fs.azure.account.key.*"
foreach ($name in $value ) { $name.Name.Split(".")[4]}
```

### <a name="apache-ambari"></a>Apache Ambari

1. A partir de um `https://CLUSTERNAME.azurehdinsight.net`navegador `CLUSTERNAME` da Web, navegue até , onde está o nome do seu cluster.

1. Navegue até **o hdfs** > **Configs** > **Advanced** > Custom**core-site**.

1. Observe as chaves `fs.azure.account.key`que começam com . O nome da conta será uma parte da chave como visto nesta imagem de amostra:

   ![verificação através de Apache Ambari](./media/hdinsight-hadoop-add-storage/apache-ambari-verification.png)

## <a name="remove-storage-account"></a>Remover conta de armazenamento

1. A partir de um `https://CLUSTERNAME.azurehdinsight.net`navegador `CLUSTERNAME` da Web, navegue até , onde está o nome do seu cluster.

1. Navegue até **o hdfs** > **Configs** > **Advanced** > Custom**core-site**.

1. Remova as seguintes teclas:
    * `fs.azure.account.key.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`
    * `fs.azure.account.keyprovider.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`

Depois de remover essas chaves e salvar a configuração, você precisa reiniciar Oozie, Fio, MapReduce2, HDFS e Hive um por um.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="storage-firewall"></a>Firewall de armazenamento

Se você optar por proteger sua conta de armazenamento com os **Firewalls e** as restrições de redes virtuais em **redes selecionadas,** certifique-se de ativar a exceção **Permita serviços confiáveis** da Microsoft... para que o HDInsight possa acessar sua conta de armazenamento.

### <a name="unable-to-access-storage-after-changing-key"></a>Não é possível acessar o armazenamento após a alteração da chave

Se você alterar a chave de uma conta de armazenamento, o HDInsight não poderá mais acessar a conta de armazenamento. O HDInsight usa uma cópia em cache da chave no core-site.xml do cluster. Essa cópia armazenada em cache deve ser atualizada para corresponder à nova chave.

Executar a ação de script novamente __não__ atualiza a chave, pois o script verifica se já existe alguma entrada para a conta de armazenamento. Se uma entrada já existe, ela não faz nenhuma alteração.

Para contornar esse problema:  
1. Remova a conta de armazenamento.
1. Adicione a conta de armazenamento.

> [!IMPORTANT]  
> Não é suportada a chave de armazenamento da conta de armazenamento principal anexada a um cluster.

### <a name="poor-performance"></a>Desempenho ruim

Se a conta de armazenamento estiver em uma região diferente do cluster do HDInsight, você poderá enfrentar um desempenho ruim. O acesso a dados em uma região diferente envia o tráfego de rede para fora do data center regional do Azure e para a internet pública, o que pode introduzir latência.

### <a name="additional-charges"></a>Custos adicionais

Se a conta de armazenamento estiver em uma região diferente do cluster do HDInsight, você poderá observar encargos adicionais em sua cobrança do Azure. Um encargo de saída é aplicado quando os dados saem de um data center regional. Esse encargo se aplica mesmo se o tráfego for destinado a outro data center do Azure em uma região diferente.

## <a name="next-steps"></a>Próximas etapas

Você aprendeu como adicionar contas de armazenamento adicionais a um cluster HDInsight existente. Para saber mais sobre as ações de script, confira [Personalizar clusters HDInsight com base em Linux usando a ação de script](hdinsight-hadoop-customize-cluster-linux.md)
