---
title: 'PowerShell: migre o SQL Server para a instância gerenciada pelo SQL'
titleSuffix: Azure Database Migration Service
description: Aprenda a migrar do SQL Server no local para a instância gerenciada do Azure SQL Database usando o Azure PowerShell e o Serviço de Migração de Banco de Dados do Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 9ea9f55681b93e79eec836f5808d2c6feaa6bb29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650717"
---
# <a name="migrate-sql-server-to-sql-database-managed-instance-with-powershell--azure-database-migration-service"></a>Migre o SQL Server para a instância gerenciada do Banco de Dados SQL com o PowerShell & serviço de migração de banco de dados do Azure

Neste artigo, você migra o banco de dados **Adventureworks2016** restaurado para uma instância local do SQL Server 2005 ou superior a uma instância gerenciada do Azure SQL Database usando o Microsoft Azure PowerShell. Você pode migrar bancos de dados de uma instância sql server local para uma `Az.DataMigration` instância gerenciada do Azure SQL Database usando o módulo no Microsoft Azure PowerShell.

Neste artigo, você aprenderá como:
> [!div class="checklist"]
>
> * Crie um grupos de recursos.
> * Criar uma instância do Serviço de Migração de Banco de Dados do Azure.
> * Crie um projeto de migração em uma instância do Azure Database Migration Service.
> * Executar a migração.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo inclui detalhes sobre como realizar migrações on-line e off-line.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir essas etapas, você precisa:

* [SQL Server 2016 ou superior](https://www.microsoft.com/sql-server/sql-server-downloads) (qualquer edição).
* Uma cópia local do banco de dados **AdventureWorks2016,** que está disponível para download [aqui](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017).
* Para habilitar o protocolo TCP/IP, que está desabilitado por padrão com a instalação do SQL Server Express. Habilite o protocolo TCP/IP seguindo o artigo [Habilitar ou desabilitar um protocolo de rede de servidor](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
* Configurar o [Firewall do Windows para acesso ao mecanismo de banco de dados](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Uma assinatura do Azure. Se você não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Uma instância gerenciada do Azure SQL Database. Você pode criar uma instância gerenciada do Azure SQL Database seguindo os detalhes do artigo [Criar uma instância gerenciada do Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started).
* Para baixar e instalar [o Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 ou posterior.
* Uma rede virtual do Microsoft Azure criada usando o modelo de implantação do Azure Resource Manager, que fornece ao Azure Database Migration Service conectividade site-a-site com seus servidores de origem no local usando [expressroute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Uma avaliação completa do seu banco de dados local e da migração de esquemas usando o Data Migration Assistant, conforme descrito no artigo Realizando uma avaliação de [migração do SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem).
* Para baixar e `Az.DataMigration` instalar o módulo (versão 0.7.2 ou posterior) da Galeria PowerShell usando [o cmdlet Install-Module PowerShell](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1).
* Para garantir que as credenciais usadas para se conectar à instância do SQL Server de origem tenham a permissão [DO SERVIDOR DE CONTROLE.](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql)
* Para garantir que as credenciais usadas para se conectar à instância gerenciada do Banco de Dados SQL do Azure tenha a permissão do BANCO DE DADOS DE CONTROLE nos bancos de dados gerenciados do Banco de Dados Azure SQL.

    > [!IMPORTANT]
    > Para migrações on-line, você já deve ter configurado suas credenciais do Azure Active Directory. Para obter mais informações, consulte o artigo [Use o portal para criar um aplicativo azure AD e um princípio de serviço que possa acessar recursos](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Faça login na sua assinatura do Microsoft Azure

Faça login na sua assinatura do Azure usando o PowerShell. Para obter mais informações, consulte o artigo [Faça login com o Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de [`New-AzResourceGroup`](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) recursos usando o comando.

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na região *leste dos EUA.*

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Criar uma instância do Serviço de Migração de Banco de Dados do Azure

Crie uma nova instância do Serviço de Migração de Banco de Dados do Azure usando o cmdlet `New-AzDataMigrationService`.
Esse cmdlet espera os seguintes parâmetros obrigatórios:

* *Nome do Grupo de Recursos Azure*. Você pode [`New-AzResourceGroup`](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) usar o comando para criar um grupo de recursos do Azure como mostrado anteriormente e fornecer seu nome como parâmetro.
* *Nome do serviço*. String que corresponde ao nome de serviço exclusivo desejado para O Serviço de Migração de Banco de Dados do Azure.
* *Localização*. Especifica o local do serviço. Especifique uma localização do data center do Azure, como o Oeste dos EUA ou o Sudeste Asiático.
* *Sku.* Esse parâmetro corresponde ao nome do SKU do DMS. Atualmente, os nomes Sku são *Basic_1vCore,* *Basic_2vCores,* *GeneralPurpose_4vCores*.
* *Identificador de Sub-rede Virtual*. Você pode usar o [`New-AzVirtualNetworkSubnetConfig`](https://docs.microsoft.com//powershell/module/az.network/new-azvirtualnetworksubnetconfig) cmdlet para criar uma sub-rede.

O exemplo a seguir cria um serviço chamado *MyDMS* no grupo de recursos *MyDMSResourceGroup* localizado na região *leste dos EUA* usando uma rede virtual chamada *MyVNET* e uma sub-rede chamada *MySubnet*.

> [!IMPORTANT]
> O trecho de código abaixo é para uma migração offline, que não requer uma instância do Azure Database Migration Service com base em um SKU Premium. Para uma migração on-line, o valor do parâmetro -Sku deve incluir um SKU Premium.

```powershell
$vNet = Get-AzVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>Criar um projeto de migração

Depois de criar uma instância do Serviço de Migração de Banco de Dados do Azure, crie um projeto de migração. Um projeto do Serviço de Migração de Banco de Dados do Azure exige informações de conexão para as instâncias de origem e de destino, bem como uma lista de bancos de dados que você deseja migrar como parte do projeto.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Criar um objeto de Informações de Conexão de Banco de Dados para as conexões de origem e destino

Você pode criar um objeto Database `New-AzDmsConnInfo` Connection Info usando o cmdlet, que espera os seguintes parâmetros:

* *Tipo de servidor*. O tipo de conexão de banco de dados solicitado, por exemplo, SQL, Oracle ou MySQL. Use o SQL para SQL Server e SQL do Azure.
* *DataSource*. O nome ou IP de uma instância do SQL Server ou da instância do Banco de Dados SQL do Azure.
* *AuthType*. O tipo de autenticação da conexão, que pode ser SqlAuthentication ou WindowsAuthentication.
* *TrustServerCertificate*. Este parâmetro define um valor que indica se o canal está criptografado ao ignorar a cadeia de certificados para validar a confiança. O valor `$true` pode `$false`ser ou .

O exemplo a seguir cria um objeto Connection Info para um SQL Server de origem chamado *MySourceSQLServer* usando autenticação sql:

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

O próximo exemplo mostra a criação de informações de conexão para um servidor de instância gerenciada do Azure SQL Database chamado 'targetmanagedinstance.database.windows.net' usando autenticação sql:

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "targetmanagedinstance.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Fornecer bancos de dados para o projeto de migração

Crie uma `AzDataMigrationDatabaseInfo` lista de objetos que especifica bancos de dados como parte do projeto Azure Database Migration Service, que pode ser fornecido como parâmetro para a criação do projeto. Você pode usar o `New-AzDataMigrationDatabaseInfo` cmdlet para criar `AzDataMigrationDatabaseInfo`.

O exemplo a `AzDataMigrationDatabaseInfo` seguir cria o projeto para o banco de dados **AdventureWorks2016** e o adiciona à lista a ser fornecida como parâmetro para criação de projetos.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Criar um objeto de projeto

Finalmente, você pode criar um projeto do Azure Database Migration Service `New-AzDataMigrationProject` chamado *MyDMSProject* localizado no Leste dos *EUA* usando e adicionar as conexões de origem e destino criadas anteriormente e a lista de bancos de dados a migrar.

```powershell
$project = New-AzDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLMI `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfo $dbList
```

## <a name="create-and-start-a-migration-task"></a>Criar e iniciar uma tarefa de migração

Em seguida, crie e inicie uma tarefa do Serviço de Migração de Banco de Dados do Azure. Essa tarefa requer informações de credencial de conexão tanto para a origem quanto para o destino, bem como a lista de tabelas de banco de dados a serem migradas e as informações já fornecidas com o projeto criado como pré-requisito.

### <a name="create-credential-parameters-for-source-and-target"></a>Criar parâmetros de credenciais para a origem e o destino

Crie credenciais de segurança de conexão como um objeto [PSCredential.](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0)

O exemplo a seguir mostra a criação de objetos *PSCredential* para as conexões de origem e destino, fornecendo senhas como variáveis de seqüência *$sourcePassword* e *$targetPassword*.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-backup-fileshare-object"></a>Criar um objeto fileShare de backup

Agora crie um objeto FileShare representando o compartilhamento de rede SMB local para o qual `New-AzDmsFileShare` o Azure Database Migration Service pode fazer os backups do banco de dados de origem usando o cmdlet.

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>Criar objeto de banco de dados selecionado

O próximo passo é selecionar os bancos `New-AzDmsSelectedDB` de dados de origem e destino usando o cmdlet.

O exemplo a seguir é para migrar um único banco de dados do SQL Server para uma instância gerenciada do Azure SQL Database:

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

Se uma instância inteira do SQL Server precisar de um lift-and-shift em uma instância gerenciada do Azure SQL Database, então um loop para tirar todos os bancos de dados da fonte é fornecido abaixo. No exemplo a seguir, para $Server, $SourceUserName e $SourcePassword, forneça os detalhes do SQL Server de origem.

```powershell
$Query = "(select name as Database_Name from master.sys.databases where Database_id>4)";
$Databases= (Invoke-Sqlcmd -ServerInstance "$Server" -Username $SourceUserName
-Password $SourcePassword -database master -Query $Query)
$selectedDbs=@()
foreach($DataBase in $Databases.Database_Name)
    {
      $SourceDB=$DataBase
      $TargetDB=$DataBase
      
$selectedDbs += New-AzureRmDmsSelectedDB -MigrateSqlServerSqlDbMi `
                                              -Name $SourceDB `
                                              -TargetDatabaseName $TargetDB `
                                              -BackupFileShare $backupFileShare
      }
```

### <a name="sas-uri-for-azure-storage-container"></a>URI de SAS para Contêiner do Armazenamento do Microsoft Azure

Crie uma variável contendo o URI de SAS que fornece ao Serviço de Migração de Banco de Dados do Azure o acesso ao contêiner da conta de armazenamento para o qual o serviço faz upload dos arquivos de backup.

```powershell
$blobSasUri="https://mystorage.blob.core.windows.net/test?st=2018-07-13T18%3A10%3A33Z&se=2019-07-14T18%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=qKlSA512EVtest3xYjvUg139tYSDrasbftY%3D"
```

### <a name="additional-configuration-requirements"></a>Requisitos adicionais de configuração

Existem alguns requisitos adicionais que você precisa resolver, mas eles diferem dependendo se você está realizando uma migração offline ou on-line.

#### <a name="offline-migrations"></a>Migrações offline

Apenas para migrações off-line, execute as seguintes tarefas adicionais de configuração.

* **Selecione logins**. Crie uma lista de logins a serem migrados como mostrado no exemplo a seguir:

    ```powershell
    $selectedLogins = @("user1", "user2")
    ```

    > [!IMPORTANT]
    > Atualmente, o Azure Database Migration Service só suporta logins SQL migratórios.

* **Selecione trabalhos de agente**. Criar lista de trabalhos de agente a serem migrados como mostrado no exemplo a seguir:

    ```powershell
    $selectedAgentJobs = @("agentJob1", "agentJob2")
    ```

    > [!IMPORTANT]
    > Atualmente, o Azure Database Migration Service só suporta empregos com etapas de trabalho do subsistema T-SQL.

#### <a name="online-migrations"></a>Migrações online

Apenas para migrações on-line, execute as seguintes tarefas adicionais de configuração.

* **Configure o aplicativo de diretório ativo do Azure**

    ```powershell
    $pwd = "Azure App Key"
    $appId = "Azure App Id"
    $AppPasswd = ConvertTo-SecureString $pwd -AsPlainText -Force
    $app = New-AzDmsAdApp -ApplicationId $appId -AppKey $AppPasswd
    ```

* **Configurar recurso de armazenamento**

    ```powershell
    $storageResourceId = "Storage Resource Id"
    ```

### <a name="create-and-start-the-migration-task"></a>Crie e inicie a tarefa de migração

Use o cmdlet `New-AzDataMigrationTask` para criar e iniciar uma tarefa de migração.

#### <a name="specify-parameters"></a>Especificar parâmetros

##### <a name="common-parameters"></a>Parâmetros comuns

Independentemente de você estar realizando uma migração offline ou on-line, o `New-AzDataMigrationTask` cmdlet espera os seguintes parâmetros:

* *TaskType*. O tipo de tarefa de migração a ser criada para o SQL Server para o tipo de migração da Instância Gerenciada do Banco de Dados SQL do Azure *MigrateSqlServerSqlDbMi* é esperado. 
* *Nome do grupo de recursos*. Nome do grupo de recursos do Azure no qual a tarefa será criada.
* *Nome de serviço*. Instância do Serviço de Migração de Banco de Dados do Azure na qual a tarefa será criada.
* *ProjectName*. Nome do projeto do Serviço de Migração de Banco de Dados do Azure no qual criar a tarefa. 
* *Nome da tarefa*. Nome da tarefa a ser criada. 
* *SourceConnection*. Objeto AzDmsConnInfo representando a conexão SQL Server de origem.
* *TargetConnection*. O objeto AzDmsConnInfo representa a conexão de instância gerenciada do banco de dados Azure SQL.
* *SourceCred*. Objeto [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) para se conectar ao servidor de origem.
* *TargetCred*. Objeto [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) para se conectar ao servidor de destino.
* *SelectedDatabase*. AzDataMigrationSelectedDB objeto representando o mapeamento de banco de dados de origem e destino.
* *BackupFileShare*. Objeto FileShare que representa o compartilhamento de rede local para o qual o Serviço de Migração de Banco de Dados do Azure pode fazer backups de banco de dados de origem.
* *BackupBlobSasUri*. O URI de SAS que fornece ao Serviço de Migração de Banco de Dados do Azure acesso ao contêiner da conta de armazenamento para o qual o serviço faz upload dos arquivos de backup. Saiba como obter o URI de SAS para o contêiner de blob.
* *SelectedLogins*. Lista de logons selecionados para migrar.
* *SelectedAgentJobs*. Lista de trabalhos do agente selecionados para migrar.

##### <a name="additional-parameters"></a>Parâmetros adicionais

O `New-AzDataMigrationTask` cmdlet também espera parâmetros exclusivos para o tipo de migração, offline ou online, que você está realizando.

* **Migrações offline**. Para migrações offline, o `New-AzDataMigrationTask` cmdlet também espera os seguintes parâmetros:

  * *SelectedLogins*. Lista de logons selecionados para migrar.
  * *SelectedAgentJobs*. Lista de trabalhos do agente selecionados para migrar.

* **Migrações online**. Para migrações on-line, o `New-AzDataMigrationTask` cmdlet também espera os seguintes parâmetros.

* *AzureActiveDirectoryApp*. Aplicação de diretório ativo.
* *StorageResourceID*. ID de recursos da conta de armazenamento.

#### <a name="create-and-start-an-offline-migration-task"></a>Crie e inicie uma tarefa de migração offline

O exemplo a seguir cria e inicia uma tarefa de migração offline chamada **myDMSTask**:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMi `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -BackupFileShare $backupFileShare `
  -BackupBlobSasUri $blobSasUri `
  -SelectedLogins $selectedLogins `
  -SelectedAgentJobs $selectedJobs `
```

#### <a name="create-and-start-an-online-migration-task"></a>Crie e inicie uma tarefa de migração on-line

O exemplo a seguir cria e inicia uma tarefa de migração on-line chamada **myDMSTask**:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMiSync `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -BackupFileShare $backupFileShare `
  -SelectedDatabase $selectedDbs `
  -AzureActiveDirectoryApp $app `
  -StorageResourceId $storageResourceId
```

## <a name="monitor-the-migration"></a>Monitorar a migração

Para monitorar a migração, execute as seguintes tarefas.

1. Consolide todos os detalhes de migração em uma variável chamada $CheckTask.

    Para combinar detalhes de migração, como propriedades, estado e informações de banco de dados associados à migração, use o seguinte trecho de código:

    ```powershell
    $CheckTask= Get-AzDataMigrationTask     -ResourceGroupName myResourceGroup `
                                            -ServiceName $service.Name `
                                        -ProjectName $project.Name `
                                            -Name myDMSTask `
                                            -ResultType DatabaseLevelOutput `
                        -Expand 
    Write-Host ‘$CheckTask.ProjectTask.Properties.Output’
    ```

2. Use `$CheckTask` a variável para obter o estado atual da tarefa de migração.

    Para usar `$CheckTask` a variável para obter o estado atual da tarefa de migração, você pode monitorar a tarefa de migração em execução consultando a propriedade estatal da tarefa, conforme mostrado no exemplo a seguir:

    ```powershell
    if (($CheckTask.ProjectTask.Properties.State -eq "Running") -or ($CheckTask.ProjectTask.Properties.State -eq "Queued"))
    {
      Write-Host "migration task running"
    }
    else if($CheckTask.ProjectTask.Properties.State -eq "Succeeded")
    { 
      Write-Host "Migration task is completed Successfully"
    }
    else if($CheckTask.ProjectTask.Properties.State -eq "Failed" -or $CheckTask.ProjectTask.Properties.State -eq "FailedInputValidation" -or $CheckTask.ProjectTask.Properties.State -eq "Faulted")
    { 
      Write-Host "Migration Task Failed"
    }
    ```

## <a name="performing-the-cutover-online-migrations-only"></a>Realizando o cutover (somente migrações on-line)

Com uma migração on-line, um backup completo e restauração de bancos de dados é realizado e, em seguida, o trabalho prossegue na restauração dos Logs de transações armazenados no BackupFileShare.

Quando o banco de dados em uma instância gerenciada do Azure SQL Database é atualizado com dados mais recentes e está em sincronia com o banco de dados de origem, você pode executar um corte.

O exemplo a seguir completará a migração de cutover\. Os usuários invocam este comando a seu critério.

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>Excluindo a instância do Serviço de Migração de Banco de Dados do Azure

Depois que a migração estiver concluída, você pode excluir a instância do Serviço de Migração do Banco de Dados do Azure:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>Recursos adicionais

Para obter informações sobre cenários migratórios adicionais (pares de origem/destino), consulte o [Guia de Migração do Banco de Dados da](https://datamigration.microsoft.com/)Microsoft .

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o Azure Database Migration Service no artigo [O que é o Serviço de Migração de Banco de Dados do Azure?](https://docs.microsoft.com/azure/dms/dms-overview).
