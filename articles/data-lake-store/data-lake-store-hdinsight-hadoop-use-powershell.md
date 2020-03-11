---
title: 'PowerShell: Cluster HDInsight do Azure com o Armazenamento de Data Lake do Azure Gen1 como armazenamento complementar | Microsoft Docs'
description: Saiba como usar Azure PowerShell para configurar um cluster HDInsight com Azure Data Lake Storage Gen1 como armazenamento adicional.
services: data-lake-store,hdinsight
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 164ada5a-222e-4be2-bd32-e51dbe993bc0
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 4cd61619e0417ab1db8d8413872b2dff1c904fc1
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970137"
---
# <a name="use-azure-powershell-to-create-an-hdinsight-cluster-with-azure-data-lake-storage-gen1-as-additional-storage"></a>Use o Azure PowerShell para criar um cluster do HDInsight com o Armazenamento de Data Lake do Azure Gen1 (como armazenamento adicional)

> [!div class="op_single_selector"]
> * [Usando o Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Usando o PowerShell (para o armazenamento padrão)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Usando o PowerShell (para o armazenamento adicional)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Usando o Gerenciador de Recursos](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Saiba como usar o Azure PowerShell para configurar um cluster HDInsight com Azure Data Lake Storage Gen1, **como armazenamento adicional**. Para obter instruções sobre como criar um cluster HDInsight com Data Lake armazenamento Gen1 como armazenamento padrão, consulte [criar um cluster de HDInsight com Data Lake armazenamento Gen1 como armazenamento padrão](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md).

> [!NOTE]
> Se você pretende usar Gen1 de armazenamento do Data Lake como armazenamento adicional para o cluster HDInsight, é altamente recomendável que você faça isso ao criar o cluster conforme descrito neste artigo. Adicionar o Data Lake Storage Gen1 como armazenamento adicional a um cluster existente do HDInsight é um processo complicado e propenso a erros.
>

Para tipos de cluster com suporte, Gen1 de armazenamento do Data Lake pode ser usado como um armazenamento padrão ou uma conta de armazenamento adicional. Quando o Data Lake Storage Gen1 é usado como armazenamento adicional, a conta de armazenamento padrão para os clusters ainda será Azure Storage Blobs (WASB) e os arquivos relacionados ao cluster (como logs, etc.) ainda serão gravados no armazenamento padrão, enquanto os dados que você quer processar podem ser armazenados em uma conta do Data Lake Storage Gen1. Usar o Data Lake Storage Gen1 como uma conta de armazenamento adicional não afeta o desempenho nem a capacidade de leitura/gravação no armazenamento do cluster.

## <a name="using-data-lake-storage-gen1-for-hdinsight-cluster-storage"></a>Usar o Data Lake Storage Gen1 para armazenamento de cluster HDInsight

Aqui estão algumas considerações importantes para usar HDInsight com Data Lake Storage Gen1:

* A opção para criar clusters HDInsight com acesso ao Data Lake Storage Gen1 como armazenamento adicional está disponível para as versões 3.2, 3.4, 3.5 e 3.6 do HDInsight.

Configurar o HDInsight para trabalhar com o Data Lake Storage Gen1 usando o PowerShell envolve as seguintes etapas:

* Criar uma conta do Data Lake Storage Gen1
* Configurar a autenticação para acesso baseado em função para o Data Lake armazenamento Gen1
* Criar o cluster HDInsight com a autenticação para o Data Lake armazenamento Gen1
* Executar um trabalho de teste no cluster

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Antes de começar este tutorial, você deve ter o seguinte:

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 ou superior**. Consulte [Como instalar e configurar o PowerShell do Azure](/powershell/azure/overview).
* **SDK do Windows**. Você pode instalá-lo clicando [aqui](https://dev.windows.com/en-us/downloads). Use isso para criar um certificado de segurança.
* **Entidade de serviço do Azure Active Directory**. As etapas neste tutorial fornecem instruções sobre como criar uma entidade de serviço no Azure AD. No entanto, você deve ser administrador do Azure AD para poder criar uma entidade de serviço. Se você for administrador do Azure AD, poderá ignorar esse pré-requisito e continuar com o tutorial.

    **Se você não for um administrador do Azure AD**, não poderá executar as etapas necessárias para criar uma entidade de serviço. Nesse caso, o administrador do Azure AD primeiro deve criar uma entidade de serviço antes de criar um cluster HDInsight com Data Lake Storage Gen1. Além disso, a entidade de serviço deve ser criada usando um certificado, conforme descrito em [Criar uma entidade de serviço com certificado](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority).

## <a name="create-a-data-lake-storage-gen1-account"></a>Criar uma conta do Data Lake Storage Gen1
Siga estas etapas para criar uma conta do Data Lake Storage Gen1.

1. Na área de trabalho, abra uma nova janela do Azure PowerShell e insira o snippet a seguir. Quando solicitado a fazer logon, lembre-se de fazer logon como um dos proprietários/administradores da assinatura:

        # Log in to your Azure account
        Connect-AzAccount

        # List all the subscriptions associated to your account
        Get-AzSubscription

        # Select a subscription
        Set-AzContext -SubscriptionId <subscription ID>

        # Register for Data Lake Storage Gen1
        Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

   > [!NOTE]
   > Se você receber um erro semelhante a `Register-AzResourceProvider : InvalidResourceNamespace: The resource namespace 'Microsoft.DataLakeStore' is invalid` ao registrar o provedor de recursos do Data Lake Storage Gen1, é possível que sua assinatura não esteja na lista de permissões do Data Lake Storage Gen1. Certifique-se de ativar sua assinatura do Azure para o Data Lake Storage Gen1 seguindo estas [instruções](data-lake-store-get-started-portal.md).
   >
   >
2. Uma conta Gen1 do Data Lake Storage está associada a um Grupo de Recursos do Azure. Comece criando um Grupo de Recursos do Azure.

        $resourceGroupName = "<your new resource group name>"
        New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"

    Você verá uma saída semelhante à seguinte:

        ResourceGroupName : hdiadlgrp
        Location          : eastus2
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp

3. Crie uma conta do Azure Data Lake Storage Gen1. O nome especificado para a conta deve conter apenas letras minúsculas e números.

        $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 account name>"
        New-AzDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStorageGen1Name -Location "East US 2"

    Você verá algo semelhante ao mostrado a seguir:

        ...
        ProvisioningState           : Succeeded
        State                       : Active
        CreationTime                : 5/5/2017 10:53:56 PM
        EncryptionState             : Enabled
        ...
        LastModifiedTime            : 5/5/2017 10:53:56 PM
        Endpoint                    : hdiadlstore.azuredatalakestore.net
        DefaultGroup                :
        Id                          : /subscriptions/<subscription-id>/resourceGroups/hdiadlgrp/providers/Microsoft.DataLakeStore/accounts/hdiadlstore
        Name                        : hdiadlstore
        Type                        : Microsoft.DataLakeStore/accounts
        Location                    : East US 2
        Tags                        : {}

5. Carregar alguns dados de exemplo no Data Lake armazenamento Gen1. Usaremos isso posteriormente neste artigo para verificar se os dados podem ser acessados a partir de um cluster HDInsight. Se estiver procurando alguns dados de exemplo para carregar, é possível obter a pasta **Dados da Ambulância** no [Repositório Git do Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

        $myrootdir = "/"
        Import-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path "C:\<path to data>\vehicle1_09142014.csv" -Destination $myrootdir\vehicle1_09142014.csv


## <a name="set-up-authentication-for-role-based-access-to-data-lake-storage-gen1"></a>Configurar a autenticação para acesso baseado em função para o Data Lake armazenamento Gen1

Cada assinatura do Azure está associada com um Azure Active Directory. Os usuários e serviços que acessam os recursos da assinatura usando o Portal Clássico do Azure ou a API do Azure Resource Manager precisam primeiro realizar a autenticação com o Azure Active Directory. O acesso é concedido às assinaturas e serviços do Azure atribuindo a função apropriada para eles em um recurso do Azure.  Para serviços, uma entidade de serviço identifica o serviço no Active Directory do Azure (AAD). Esta seção ilustra como conceder a um serviço de aplicativo, como o HDInsight, acesso a um recurso do Azure (a conta do Data Lake armazenamento Gen1 criado anteriormente), criando uma entidade de serviço para o aplicativo e atribuindo funções a ela por meio do PowerShell do Azure.

Para configurar a autenticação do Active Directory para o Data Lake armazenamento Gen1, você deve executar as seguintes tarefas.

* Crie um certificado autoassinado
* Criar um aplicativo no Active Directory do Azure e uma entidade de serviço

### <a name="create-a-self-signed-certificate"></a>Crie um certificado autoassinado

Verifique se o [SDK do Windows](https://dev.windows.com/en-us/downloads) está instalado antes de continuar com as etapas nesta seção. Você também deve ter criado um diretório, como **C:\mycertdir**, no qual o certificado será criado.

1. Na janela do PowerShell, navegue até o local onde você instalou SDK do Windows (normalmente, `C:\Program Files (x86)\Windows Kits\10\bin\x86` e use o utilitário [MakeCert][makecert] para criar um certificado autoassinado e uma chave privada. Use os seguintes comandos.

        $certificateFileDir = "<my certificate directory>"
        cd $certificateFileDir

        makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -r -len 2048

    Você receberá uma solicitação para inserir a senha da chave privada. Após a execução bem-sucedida do comando, você verá um **CertFile.cer** e **mykey.pvk** no diretório de certificado especificado.
2. Use o utilitário [pvk2pfx][pvk2pfx] para converter os arquivos. pvk e. cer que o MakeCert criou em um arquivo. pfx. Execute o comando a seguir.

        pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

    Quando receber a solicitação, digite a senha da chave privada especificada anteriormente. O valor especificado para o parâmetro **-po** é a senha que está associada ao arquivo .pfx. Após a conclusão bem-sucedida do comando, você também verá um arquivo CertFile.pfx no diretório de certificado especificado.

### <a name="create-an-azure-active-directory-and-a-service-principal"></a>Criar um Active Directory do Azure e uma entidade de serviço

Nesta seção, você executará as etapas para criar uma entidade de serviço para um aplicativo do Active Directory do Azure, atribuir uma função à entidade de serviço e autenticar como a entidade de serviço ao fornecer um certificado. Execute os seguintes comandos para criar um aplicativo no Active Directory do Azure.

1. Cole os seguintes cmdlets na janela do console do PowerShell. Verifique se o valor especificado para a propriedade **-DisplayName** é exclusivo. Além disso, os valores para **-HomePage** e **-IdentiferUris** são valores de espaço reservado e não são verificados.

        $certificateFilePath = "$certificateFileDir\CertFile.pfx"

        $password = Read-Host -Prompt "Enter the password" # This is the password you specified for the .pfx file

        $certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)

        $rawCertificateData = $certificatePFX.GetRawCertData()

        $credential = [System.Convert]::ToBase64String($rawCertificateData)

        $application = New-AzADApplication `
            -DisplayName "HDIADL" `
            -HomePage "https://contoso.com" `
            -IdentifierUris "https://mycontoso.com" `
            -CertValue $credential  `
            -StartDate $certificatePFX.NotBefore  `
            -EndDate $certificatePFX.NotAfter

        $applicationId = $application.ApplicationId
2. Crie uma entidade de serviço usando a ID do aplicativo.

        $servicePrincipal = New-AzADServicePrincipal -ApplicationId $applicationId

        $objectId = $servicePrincipal.Id
3. Conceda o acesso à entidade de serviço para a pasta do Data Lake armazenamento Gen1 e o arquivo que você acessará do cluster HDInsight. O snippet a seguir fornece acesso à raiz da conta do Data Lake armazenamento Gen1 (onde você copiou o arquivo de dados de exemplo) e o próprio arquivo.

        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path / -AceType User -Id $objectId -Permissions All
        Set-AzDataLakeStoreItemAclEntry -AccountName $dataLakeStorageGen1Name -Path /vehicle1_09142014.csv -AceType User -Id $objectId -Permissions All

## <a name="create-an-hdinsight-linux-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Criar um cluster HDInsight Linux com Data Lake armazenamento Gen1 como armazenamento adicional

Nesta seção, criamos um cluster Linux de Hadoop do HDInsight com Data Lake armazenamento Gen1 como armazenamento adicional. Para esta versão, o cluster HDInsight e a conta do Data Lake armazenamento Gen1 devem ser no mesmo local.

1. Comece recuperando a ID do locatário da assinatura. Você precisará disso posteriormente.

        $tenantID = (Get-AzContext).Tenant.TenantId
2. Para esta versão, para um cluster Hadoop, Data Lake armazenamento Gen1 pode ser usado apenas como um armazenamento adicional para o cluster. O armazenamento padrão ainda será nos blobs de armazenamento do Azure (WASB). Portanto, vamos criar primeiro a conta de armazenamento e os contêineres de armazenamento exigidos para o cluster.

        # Create an Azure storage account
        $location = "East US 2"
        $storageAccountName = "<StorageAccountName>"   # Provide a Storage account name

        New-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

        # Create an Azure Blob Storage container
        $containerName = "<ContainerName>"              # Provide a container name
        $storageAccountKey = (Get-AzStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
        $destContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        New-AzStorageContainer -Name $containerName -Context $destContext
3. Crie o cluster HDInsight. Use os seguintes cmdlets.

        # Set these variables
        $clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
        $httpCredentials = Get-Credential
        $sshCredentials = Get-Credential

        New-AzHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $httpCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -Version "3.4" -OSType Linux -SshCredential $sshCredentials -ObjectID $objectId -AadTenantId $tenantID -CertificateFilePath $certificateFilePath -CertificatePassword $password

    Após a conclusão do cmdlet, você verá uma saída listando os detalhes do cluster.


## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-storage-gen1-account"></a>Execute tarefas de teste no cluster do HDInsight para usar a conta do Data Lake Storage Gen1
Após configurar um cluster HDInsight, você poderá executar trabalhos de teste no cluster para testar se o cluster HDInsight pode acessar o Data Lake Storage Gen1. Para fazer isso, executaremos um exemplo de trabalho do Hive que cria uma tabela usando os dados de exemplo que você carregou anteriormente para a conta do Data Lake Storage Gen1.

Nesta seção, você acessará o cluster Linux HDInsight criado por você por SSH e executará uma consulta Hive de exemplo.

* Se você estiver usando um cliente Windows para acessar o cluster por SSH, veja [Usar SSH com Hadoop baseado em Linux no HDInsight do Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Se você estiver usando um cliente Linux para acessar o cluster por SSH, veja [Usar SSH com Hadoop baseado em Linux no HDInsight do Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

1. Uma vez conectado, inicie a CLI do Hive usando o seguinte comando:

        hive
2. Usando a CLI, insira as instruções a seguir para criar uma nova tabela nomeada **veículos**, usando os dados de exemplo no Data Lake Storage Gen1:

        DROP TABLE vehicles;
        CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestoragegen1>.azuredatalakestore.net:443/';
        SELECT * FROM vehicles LIMIT 10;

    Você deverá ver um resultado semelhante ao seguinte:

        1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
        1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
        1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
        1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
        1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
        1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
        1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
        1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
        1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
        1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1

## <a name="access-data-lake-storage-gen1-using-hdfs-commands"></a>Acessar o Data Lake Storage Gen1 usando os comandos do HDFS
Após configurar o cluster HDInsight para usar o Data Lake Storage Gen1, você poderá usar os comandos do shell do HDFS para acessar o armazenamento.

Nesta seção, você acessará o cluster Linux HDInsight criado por você por SSH e executará os comandos HDFS.

* Se você estiver usando um cliente Windows para acessar o cluster por SSH, veja [Usar SSH com Hadoop baseado em Linux no HDInsight do Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).
* Se você estiver usando um cliente Linux para acessar o cluster por SSH, veja [Usar SSH com Hadoop baseado em Linux no HDInsight do Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)

Uma vez conectado, use o seguinte comando do sistema de arquivos HDFS para listar os arquivos na conta do Data Lake Storage Gen1.

    hdfs dfs -ls adl://<Data Lake Storage Gen1 account name>.azuredatalakestore.net:443/

Isso deve listar o arquivo que você carregou anteriormente para o Data Lake Storage Gen1.

    15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
    Found 1 items
    -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestoragegen1.azuredatalakestore.net:443/mynewfolder

Também é possível usar o comando `hdfs dfs -put` para carregar alguns arquivos no Data Lake Storage Gen1 e, em seguida, usar `hdfs dfs -ls` para verificar se os arquivos foram carregados com êxito.

## <a name="see-also"></a>Consulte Também
* [Usar Data Lake Storage Gen1 com clusters HDInsight do Azure](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Portal: Criar um cluster de HDInsight para usar o Data Lake armazenamento Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
