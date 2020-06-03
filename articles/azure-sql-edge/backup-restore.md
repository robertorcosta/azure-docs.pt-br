---
title: Backup e restauração de bancos de dados – SQL do Azure no Edge (Versão prévia)
description: Saiba mais sobre o backup e como restaurar recursos no SQL do Azure no Edge (Versão prévia)
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 902576f82faa18fbb0e7c7eaed5c06993bd379cc
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235177"
---
# <a name="backup-and-restore-databases-in-azure-sql-edge-preview"></a>Backup e restauração de bancos de dados no SQL do Azure no Edge (Versão prévia) 

O SQL do Azure no Edge baseia-se nas versões mais recentes do Mecanismo de Banco de Dados do Microsoft SQL Server no Linux, fornecendo backup e restauração de banco de dados semelhantes aos disponíveis no SQL Server em Linux e no SQL Server em execução em contêineres. O componente de backup e restauração oferece uma proteção essencial para dados críticos armazenados nos seus bancos de dados do SQL do Azure no Edge. Para minimizar o risco de uma perda de dados catastrófica, é recomendável que você faça backup de seus bancos de dados para preservar as modificações nos seus dados regularmente. Uma estratégia de backup e restauração bem planejada ajuda a proteger os bancos de dados contra perda de dados causada por uma variedade de falhas. Teste sua estratégia restaurando um conjunto de backups e, em seguida, recuperando seu banco de dados para se preparar para responder com eficiência a um desastre.

Para saber mais sobre por que os backups são importantes, consulte [Backup e restauração de bancos de dados do SQL Server](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases/).

O SQL do Azure no Edge dá suporte a backup e restauração de armazenamento local ou de blobs do Azure. Para obter informações sobre o backup do Armazenamento de Blobs, consulte [Backup e restauração do SQL Server com Serviço de Armazenamento de Blobs da Microsoft](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service/) e [ Backup do SQL Server para URL ](/sql/relational-databases/backup-restore/sql-server-backup-to-url).

## <a name="backing-up-a-database-in-azure-sql-edge"></a>Fazendo backup de um banco de dados no SQL do Azure no Edge

O SQL do Azure no Edge dá suporte aos mesmos tipos de backup que os suportados pelo SQL Server. Para obter uma lista completa dos tipos de backup com suporte no SQL Server, consulte [Visão geral do backup](/sql/relational-databases/backup-restore/backup-overview-sql-server/).

> [!IMPORTANT] 
> Os bancos de dados criados no SQL do Azure no Edge usam o modelo de recuperação simples por padrão. Assim sendo, os backups de log não podem ser executados nesses bancos de dados. Se houver a necessidade de realizar backups de log nesses bancos de dados, um administrador precisará alterar o modelo de recuperação de banco de dados para o modelo de recuperação completa. Para obter uma lista completa dos modelos de recuperação com suporte pelo SQL Server, consulte [Visão geral do modelo de recuperação](/sql/relational-databases/backup-restore/recovery-models-sql-server#RMov).

### <a name="backup-to-local-disk"></a>Backup para o disco local

No exemplo fornecido abaixo, o comando BACKUP DATABASE do Transact-SQL é usado para criar um backup de banco de dados no contêiner. Para fins deste exemplo, uma nova pasta chamada "backup" foi criada para armazenar os arquivos de backup.

1. Crie uma pasta para os backups. Esse comando precisa ser executado no host onde o contêiner do SQL do Azure no Edge está em execução. No comando a seguir, substitua **< AzureSQLEdge_Container_Name >** pelo nome do contêiner do SQL do Azure no Edge em sua implantação.

    ```bash
    sudo docker exec -it <AzureSQLEdge_Container_Name> mkdir /var/opt/mssql/backup
    ```

2. Conecte-se à instância do SQL do Azure no Edge usando o SQL Server Management Studio (SSMS) ou usando o Azure Data Studio (ADS) e execute o comando de banco de dados Backup para fazer o backup do seu banco de dados de usuário. No exemplo a seguir, estamos fazendo o backup do banco de dados *IronOreSilicaPrediction*.

    ```sql
    BACKUP DATABASE [IronOreSilicaPrediction] 
    TO DISK = N'/var/opt/mssql/backup/IronOrePredictDB.bak' 
    WITH NOFORMAT, NOINIT,  NAME = N'IronOreSilicaPrediction-Full Database Backup', 
    SKIP, NOREWIND, NOUNLOAD, COMPRESSION,  STATS = 10
    GO
    ```

3. Depois de executar o comando e se o backup do banco de dados for bem-sucedido, você verá mensagens semelhantes às seguintes na seção de resultados do SSMS ou ADS.

    ```txt
    10 percent processed.
    20 percent processed.
    30 percent processed.
    40 percent processed.
    50 percent processed.
    60 percent processed.
    70 percent processed.
    80 percent processed.
    90 percent processed.
    100 percent processed.
    Processed 51648 pages for database 'IronOreSilicaPrediction', file 'IronOreSilicaPrediction' on file 1.
    Processed 2 pages for database 'IronOreSilicaPrediction', file 'IronOreSilicaPrediction_log' on file 1.
    BACKUP DATABASE successfully processed 51650 pages in 3.588 seconds (112.461 MB/sec).

    Completion time: 2020-04-09T23:54:48.4957691-07:00
    ```

### <a name="backup-to-url"></a>Backup para URL

O SQL do Azure no Edge dá suporte a backups para blobs de página e blobs de blocos. Para obter mais informações sobre blobs de páginas e blobs de blocos, consulte [Backup para blob de blocos versus](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-ver15#blockbloborpageblob)blob de páginas. No exemplo abaixo, o banco de dados *IronOreSilicaPrediction* está sendo submetido a backup em um blob de blocos. 

1. A primeira etapa na configuração de backups para bloquear blobs de blocos é gerar um token SAS (assinatura de acesso compartilhado) que pode ser usado para criar uma credencial do SQL Server no SQL do Azure no Edge. O script cria uma assinatura de acesso compartilhado que está associada a uma Política de Acesso Armazenada. Para obter mais informações, confira [Assinaturas de Acesso Compartilhado, parte 1: Understanding the SAS Model](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/) (Assinaturas de Acesso Compartilhado, Parte 1: noções básicas sobre o modelo de SAS) O script também grava o comando T-SQL necessário para criar a credencial no SQL Server. O script a seguir pressupõe que você já tenha uma assinatura do Azure com uma conta de armazenamento e um contêiner de armazenamento para os backups.

    ```PowerShell
    # Define global variables for the script  
    $subscriptionName='<your subscription name>'   # the name of subscription name you will use
    $resourcegroupName = '<your resource group name>' # the name of resource group you will use
    $storageAccountName= '<your storage account name>' # the storage account name you will use for backups
    $containerName= '<your storage container name>'  # the storage container name to which you will attach the SAS policy with its SAS token  
    $policyName = 'SASPolicy' # the name of the SAS policy  

    # adds an authenticated Azure account for use in the session
    Login-AzAccount

    # set the tenant, subscription and environment for use in the rest of
    Select-AzSubscription -Subscription $subscriptionName

    # Generate the SAS token
    $sa = Get-AzStorageAccount -ResourceGroupName $resourcegroupName -Name $storageAccountName 
    $storagekey = Get-AzStorageAccountKey -ResourceGroupName $resourcegroupName -Name $storageAccountName 
    $storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storagekey[0].Value
    $cbc = Get-AzStorageContainer -Name $containerName -Context $storageContext
    $policy = New-AzStorageContainerStoredAccessPolicy -Container $containerName -Policy $policyName -Context $storageContext -ExpiryTime $(Get-Date).ToUniversalTime().AddYears(10) -Permission "rwld"
    $sas = New-AzStorageContainerSASToken -Policy $policyName -Context $storageContext -Container $containerName
    Write-Host 'Shared Access Signature= '$($sas.Substring(1))''

    # Outputs the Transact SQL to the clipboard and to the screen to create the credential using the Shared Access Signature  
    Write-Host 'Credential T-SQL'  
    $tSql = "CREATE CREDENTIAL [{0}] WITH IDENTITY='Shared Access Signature', SECRET='{1}'" -f $cbc.CloudBlobContainer.Uri.AbsoluteUri,$sas.Substring(1)
    $tSql | clip  
    Write-Host $tSql
    ```

    Depois de executar o script com êxito, copie o comando CREATE CREDENTIAL para uma ferramenta de consulta, conecte-se a uma instância do SQL Server e execute o comando para criar a credencial com a Assinatura de Acesso Compartilhado.

2. Conecte-se à instância do SQL do Azure no Edge usando o SQL Server Management Studio (SSMS) ou usando o Azure Data Studio (ADS) e crie a credencial usando o comando da etapa anterior. Certifique-se de substituir o comando CREATE CREDENCIAL pela saída real da etapa anterior.

    ```sql
    IF NOT EXISTS  
    (SELECT * FROM sys.credentials
    WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
    CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
       WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
       SECRET = '<SAS_TOKEN>';
    ```

3. O comando a seguir faz um backup do *IronOreSilicaPrediction* para o contêiner de armazenamento do Azure.

    ```sql
    BACKUP DATABASE IronOreSilicaPrediction
    TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/IronOreSilicaPrediction.bak'
    With MAXTRANSFERSIZE = 4194304,BLOCKSIZE=65536;  
    GO
    ```

## <a name="restoring-a-database-in-azure-sql-edge"></a>Restaurando um banco de dados no SQL do Azure no Edge

O SQL do Azure no Edge dá suporte à restauração de um disco local, de um local de rede ou de uma conta de armazenamento de blobs do Azure. Para obter uma visão geral da restauração e recuperação no SQL Server, consulte [Visão geral de restauração e recuperação](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-and-recovery-overview-sql-server?view=sql-server-ver15). Para obter uma visão geral do modelo de recuperação simples no SQL Server, consulte [Restaurações completas do banco de dados (Modelo de recuperação simples)](https://docs.microsoft.com/sql/relational-databases/backup-restore/complete-database-restores-simple-recovery-model?view=sql-server-ver15).

### <a name="restore-from-local-disk"></a>Restaurar do disco local

Este exemplo usa o backup do *IronOreSilicaPrediction* executado no exemplo anterior para restauração como um novo banco de dados com um nome diferente.

1. Se o arquivo de backup do banco de dados ainda não estiver presente no contêiner, você poderá usar o comando a seguir para copiar o arquivo para o contêiner. O exemplo a seguir pressupõe que o arquivo de backup está presente no host local e está sendo copiado para a pasta /var/opt/mssql/backup em um contêiner do Azure SQL Edge chamado sql1.

    ```bash
    sudo docker cp IronOrePredictDB.bak sql1:/var/opt/mssql/backup
    ```

2. Conecte-se à instância do SQL do Azure no Edge usando o SQL Server Management Studio (SSMS) ou usando o Azure Data Studio (ADS) para executar o comando de restauração. No exemplo a seguir, o **IronOrePredictDB. bak** é restaurado para criar um novo banco de dados **IronOreSilicaPrediction_2**

    ```sql
    Restore FilelistOnly from disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'

    Restore Database IronOreSilicaPrediction_2
    From disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'
    WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.mdf',
    MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.ldf'
    ```

3. Depois de executar o comando Restore e se a operação de restauração tiver sido bem-sucedida, você verá mensagens parecidas com as mostradas a seguir na janela de saída. 

    ```txt
    Processed 51648 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction' on file 1.
    Processed 2 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction_log' on file 1.
    RESTORE DATABASE successfully processed 51650 pages in 6.543 seconds (61.670 MB/sec).

    Completion time: 2020-04-13T23:49:21.1600986-07:00
    ```

### <a name="restore-from-url"></a>Restauração a partir da URL

O SQL do Azure no Edge também dá suporte à restauração de um banco de dados de uma conta de armazenamento do Azure. As restaurações podem ser executadas a partir dos blobs de blocos ou backups de blobs de páginas. No exemplo fornecido abaixo, o arquivo de backup do banco de dados *IronOreSilicaPrediction_2020_04_16. bak* em um blob de blocos é restaurado para criar o banco de dados *IronOreSilicaPrediction_3*.

```sql
RESTORE DATABASE IronOreSilicaPrediction_3
FROM URL = 'https://mystorageaccount.blob.core.windows.net/mysecondcontainer/IronOreSilicaPrediction_2020_04_16.bak'
WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.mdf', 
MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.ldf',
STATS = 10;
```


