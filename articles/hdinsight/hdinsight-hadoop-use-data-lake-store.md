---
title: Usar o Azure Data Lake Storage Gen1 com o Hadoop no Azure HDInsight
description: Aprenda a consultar dados do Azure Data Lake Storage Gen1 e a armazenar os resultados da sua análise.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: b45af924b75392374265ca41bd4dc1627edd4e01
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82190802"
---
# <a name="use-data-lake-storage-gen1-with-azure-hdinsight-clusters"></a>Usar Data Lake Storage Gen1 com clusters Azure HDInsight

> [!Note]
> Implante novos clusters HDInsight usando o [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md) para ter um melhor desempenho e novos recursos.

Para analisar dados no cluster HDInsight, você pode armazenar os dados no [`Azure Storage`](../storage/common/storage-introduction.md), [Azure data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md)ou [Azure data Lake Storage Gen 2](../storage/blobs/data-lake-storage-introduction.md). Todas as opções de armazenamento permitem que os clusters HDInsight usados para cálculo sejam excluídos com segurança sem que ocorra perda de dados do usuário.

Neste artigo, você aprenderá como funciona o Data Lake Storage Gen1 com clusters HDInsight. Para saber como o Armazenamento do Azure funciona com clusters HDInsight, consulte [Usar o Armazenamento do Azure com clusters do Azure HDInsight](hdinsight-hadoop-use-blob-storage.md). Para saber mais sobre a criação de um cluster HDInsight, consulte [Criar clusters do Apache Hadoop no HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!NOTE]  
> O Data Lake Storage Gen1 é sempre acessado por meio de um canal seguro, portanto não há nenhum nome do esquema de sistema de arquivos `adls`. Use sempre `adl`.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="availability-for-hdinsight-clusters"></a>Disponibilidade para clusters do HDInsight

O Apache Hadoop dá suporte a uma noção do sistema de arquivos padrão. O sistema de arquivos padrão implica esquema e autoridade padrões. Ele também pode ser usado para resolver caminhos relativos. Durante o processo de criação do cluster HDInsight, especifique um contêiner de blob no armazenamento do Azure como o sistema de arquivos padrão. Ou com o HDInsight 3,5 e versões mais recentes, você pode selecionar o armazenamento do Azure ou Azure Data Lake Storage Gen1 como o sistema de arquivos padrão com algumas exceções. O cluster e a conta de armazenamento devem ser hospedados na mesma região.

Os clusters HDInsight podem usar o Data Lake Storage Gen1 de duas maneiras:

* Como armazenamento padrão
* Como armazenamento adicional, com o Azure Storage Blob como o armazenamento padrão.

Atualmente, apenas alguns dos tipos/versões de cluster HDInsight dão suporte ao uso de Data Lake Storage Gen1 como armazenamento padrão e contas de armazenamento adicionais:

| Tipo de cluster HDInsight | Data Lake Storage Gen1 como armazenamento padrão | Data Lake Storage Gen1 como armazenamento adicional| Observações |
|------------------------|------------------------------------|---------------------------------------|------|
| HDInsight versão 4,0 | Não | Não |ADLS Gen1 não tem suporte com o HDInsight 4,0 |
| HDInsight versão 3.6 | Sim | Sim | Exceto HBase|
| HDInsight versão 3.5 | Sim | Sim | Exceto HBase|
| HDInsight versão 3.4 | Não | Sim | |
| HDInsight versão 3.3 | Não | Não | |
| HDInsight versão 3.2 | Não | Sim | |
| Storm | | |É possível usar o Data Lake Storage Gen1 para gravar os dados de uma topologia do Storm. Também é possível usar o Data Lake Storage para dados de referência que, em seguida, podem ser lidos por uma topologia do Storm.|

> [!WARNING]  
> O HDInsight HBase não é compatível com o Azure Data Lake Storage Gen1

O uso de Data Lake Storage Gen1 como uma conta de armazenamento adicional não afeta o desempenho. Ou a capacidade de ler ou gravar no armazenamento do Azure do cluster.

## <a name="use-data-lake-storage-gen1-as-default-storage"></a>Usar o Data Lake Storage Gen1 como armazenamento padrão

Quando o HDInsight é implantado com o Data Lake Storage Gen1 como armazenamento padrão, os arquivos relacionados ao cluster são armazenados no `adl://mydatalakestore/<cluster_root_path>/`, em que `<cluster_root_path>` é o nome de uma pasta que você cria no Data Lake Storage. Ao especificar um caminho raiz para cada cluster, você pode usar a mesma conta do Data Lake Storage para mais de um cluster. Portanto, você terá uma configuração em que:

* O Cluster1 pode usar o caminho `adl://mydatalakestore/cluster1storage`
* O Cluster2 pode usar o caminho `adl://mydatalakestore/cluster2storage`

Observe que os dois clusters usam a mesma conta **mydatalakestore** do Data Lake Storage Gen1. Cada cluster tem acesso ao seu próprio sistema de arquivos raiz no Data Lake Storage. A experiência de implantação portal do Azure solicita que você use um nome de pasta, **como\</clusters/ClusterName>** para o caminho raiz.

Para usar Data Lake Storage Gen1 como armazenamento padrão, você deve conceder acesso à entidade de serviço para os seguintes caminhos:

* A raiz da conta do Data Lake Storage Gen1.  Por exemplo: adl://mydatalakestore/.
* A pasta para todas as pastas de cluster.  Por exemplo: adl://mydatalakestore/clusters.
* A pasta para o cluster.  Por exemplo: adl://mydatalakestore/clusters/cluster1storage.

Para saber mais sobre como criar a entidade de serviço e conceder acesso, confira Configurar acesso ao Data Lake Storage.

### <a name="extracting-a-certificate-from-azure-keyvault-for-use-in-cluster-creation"></a>Extrair um certificado do Azure Key Vault para uso na criação do cluster

Se o certificado para a entidade de serviço estiver armazenado em Azure Key Vault, você deverá converter o certificado para o formato correto. Os trechos de código a seguir mostram como fazer a conversão.

Primeiro, baixe o certificado do Key Vault e extraia o `SecretValueText`.

```powershell
$certPassword = Read-Host "Enter Certificate Password"
$cert = (Get-AzureKeyVaultSecret -VaultName 'MY-KEY-VAULT' -Name 'MY-SECRET-NAME')
$certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
```

Em seguida, converta o `SecretValueText` em um certificado.

```powershell
$certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue,$null,"Exportable, PersistKeySet"
$certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);
$identityCertificate = [System.Convert]::ToBase64String($certBytes)
```

Depois, use o `$identityCertificate` para implantar um novo cluster, como no snippet a seguir:

```powershell
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $pathToArmTemplate `
    -identityCertificate $identityCertificate `
    -identityCertificatePassword $certPassword.SecretValueText `
    -clusterName  $clusterName `
    -clusterLoginPassword $SSHpassword `
    -sshPassword $SSHpassword `
    -servicePrincipalApplicationId $application.ApplicationId
```

## <a name="use-data-lake-storage-gen1-as-additional-storage"></a>Usar o Data Lake Storage Gen1 como armazenamento adicional

É possível usar o Data Lake Storage Gen1 como armazenamento adicional para o cluster também. Nesses casos, o armazenamento padrão do cluster pode ser um Azure Storage Blob ou uma conta do Data Lake Storage. Ao executar trabalhos do HDInsight em relação aos dados armazenados em Data Lake Storage como armazenamento adicional, use o caminho totalmente qualificado. Por exemplo:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Não há **cluster_root_path** na URL agora. Isso porque Data Lake Storage não é um armazenamento padrão nesse caso. Portanto, tudo o que você precisa fazer é fornecer o caminho para os arquivos.

Para usar um Data Lake Storage Gen1 como armazenamento adicional, conceda à entidade de serviço acesso aos caminhos em que os arquivos são armazenados.  Por exemplo:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Para saber mais sobre como criar a entidade de serviço e conceder acesso, confira Configurar acesso ao Data Lake Storage.

## <a name="use-more-than-one-data-lake-storage-accounts"></a>Usar mais de uma conta do Data Lake Storage

Adicionar uma conta de Data Lake Storage como adicional e adicionar mais de uma Data Lake Storage contas pode ser feito. Conceda à permissão do cluster HDInsight dados em uma ou mais contas de Data Lake Storage. Confira Configurar acesso ao Data Lake Storage.

## <a name="configure-data-lake-storage-access"></a>Configurar o acesso ao Data Lake Storage

Para configurar o acesso ao Data Lake Storage do seu cluster HDInsight, você precisa ter uma entidade de serviço do Azure AD (Azure Active Directory). Somente um administrador do Azure AD pode criar uma entidade de serviço. A entidade de serviço deve ser criada com um certificado. Para obter mais informações, consulte [Início rápido: configurar clusters no HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md), e [Criar entidade de serviço com certificado autoassinado](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

> [!NOTE]  
> Se você pretende usar o Azure Data Lake Storage Gen1 como armazenamento adicional para o cluster HDInsight, é altamente recomendável que faça isso ao criar o cluster, conforme descrito neste artigo. Não há suporte à adição do Azure Data Lake Storage Gen1 como armazenamento adicional a um cluster HDInsight.

Para obter mais informações sobre o modelo de controle de acesso, consulte [Access Control in Azure data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

## <a name="access-files-from-the-cluster"></a>Acessar arquivos do cluster

Há várias maneiras de acessar os arquivos no Data Lake Storage em um cluster HDInsight.

* **Usando o nome totalmente qualificado**. Com essa abordagem, é necessário fornecer o caminho completo para o arquivo que você deseja acessar.

    ```
    adl://<data_lake_account>.azuredatalakestore.net/<cluster_root_path>/<file_path>
    ```

* **Usando o formato de caminho encurtado**. Com essa abordagem, você substitui o caminho até a raiz do cluster por:

    ```
    adl:///<file path>
    ```

* **Usando o caminho relativo**. Com essa abordagem, você só fornece o caminho relativo para o arquivo que deseja acessar.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Exemplos de acesso a dados

Os exemplos são baseados em uma [conexão SSH](./hdinsight-hadoop-linux-use-ssh-unix.md) com o nó principal do cluster. Os exemplos usam todos os três esquemas de URI. Substitua `DATALAKEACCOUNT` e `CLUSTERNAME` pelos valores relevantes.

#### <a name="a-few-hdfs-commands"></a>Alguns comandos do HDFS

1. Crie um arquivo no armazenamento local.

    ```bash
    touch testFile.txt
    ```

1. Crie diretórios no armazenamento de cluster.

    ```bash
    hdfs dfs -mkdir adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -mkdir adl:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Copie dados do armazenamento local para o armazenamento de cluster.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt adl:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt /sampledata3/
    ```

1. Liste o conteúdo do diretório no armazenamento de cluster.

    ```bash
    hdfs dfs -ls adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -ls adl:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Criando uma tabela Hive

Três locais de arquivo são mostrados para fins ilustrativos. Para a execução real, use apenas uma das `LOCATION` entradas.

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/example/data/';
LOCATION 'adl:///example/data/';
LOCATION '/example/data/';
```

## <a name="identify-storage-path-from-ambari"></a>Identificar o caminho de armazenamento do Ambari

Para identificar o caminho completo para o repositório padrão configurado, navegue até **HDFS** > **configurações** do HDFS e insira `fs.defaultFS` na caixa de entrada do filtro.

## <a name="create-hdinsight-clusters-with-access-to-data-lake-storage-gen1"></a>Criar clusters HDInsight com acesso ao Data Lake Storage Gen1

Use os links a seguir para obter instruções detalhadas sobre como criar clusters HDInsight com acesso ao Data Lake Storage Gen1.

* [Usando o Portal](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Usando o PowerShell (com o Data Lake Storage Gen1 como armazenamento padrão)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Usando o PowerShell (com o Data Lake Storage Gen1 como armazenamento adicional)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Usando modelos do Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

## <a name="refresh-the-hdinsight-certificate-for-data-lake-storage-gen1-access"></a>Atualizar o certificado do HDInsight para acesso do Data Lake Storage Gen1

O código de exemplo do PowerShell a seguir lê um certificado de um arquivo local ou do Azure Key Vault e atualiza seu cluster HDInsight com o novo certificado para acessar o Azure Data Lake Storage Gen1. Forneça seu próprio nome de cluster HDInsight, nome do grupo de recursos, `app ID`ID da assinatura,, caminho local para o certificado. Digite a senha quando solicitado.

```powershell-interactive
$clusterName = '<clustername>'
$resourceGroupName = '<resourcegroupname>'
$subscriptionId = '01234567-8a6c-43bc-83d3-6b318c6c7305'
$appId = '01234567-e100-4118-8ba6-c25834f4e938'
$addNewCertKeyCredential = $true
$certFilePath = 'C:\localfolder\adls.pfx'
$KeyVaultName = "my-key-vault-name"
$KeyVaultSecretName = "my-key-vault-secret-name"
$certPassword = Read-Host "Enter Certificate Password"
# certSource
# 0 - create self signed cert
# 1 - read cert from file path
# 2 - read cert from key vault
$certSource = 0

Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

if($certSource -eq 0)
{
    Write-Host "Generating new SelfSigned certificate"

    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=hdinsightAdlsCert" -KeySpec KeyExchange
    $certBytes = $cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword);
    $certString = [System.Convert]::ToBase64String($certBytes)
}
elseif($certSource -eq 1)
{

    Write-Host "Reading the cert file from path $certFilePath"

    $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($certFilePath, $certPassword)
    $certString = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes($certFilePath))
}
elseif($certSource -eq 2)
{

    Write-Host "Reading the cert file from Azure Key Vault $KeyVaultName"

    $cert = (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName)
    $certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
    $certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue, $null,"Exportable, PersistKeySet"

    $certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);

    $certString =[System.Convert]::ToBase64String($certBytes)
}

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    New-AzADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    Write-Host "Waiting for 7 minutes for the permissions to get propagated"
    Start-Sleep -s 420 #7 minutes
}

Write-Host "Updating the certificate on HDInsight cluster..."

Invoke-AzResourceAction `
    -ResourceGroupName $resourceGroupName `
    -ResourceType 'Microsoft.HDInsight/clusters' `
    -ResourceName $clusterName `
    -ApiVersion '2015-03-01-preview' `
    -Action 'updateclusteridentitycertificate' `
    -Parameters @{ ApplicationId = $appId; Certificate = $certString; CertificatePassword = $certPassword.ToString() } `
    -Force
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a usar o Azure Data Lake Storage Gen1 compatível com HDFS com o HDInsight. Esse armazenamento permite que você crie soluções de aquisição de dados de arquivamento adaptáveis e de longo prazo. E use o HDInsight para desbloquear as informações dentro dos dados armazenados estruturados e não estruturados.

Para obter mais informações, consulte:

* [Início rápido: Configurar clusters no HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Criar um cluster HDInsight para usar o Data Lake Storage Gen1 usando o Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Carregar dados no HDInsight](hdinsight-upload-data.md)
* [Usar Assinaturas de Acesso Compartilhado do Armazenamento do Azure para restringir o acesso a dados com o HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)
