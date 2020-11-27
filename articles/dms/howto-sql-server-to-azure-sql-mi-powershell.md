---
title: 'PowerShell: migrar SQL Server para o SQL Instância Gerenciada'
titleSuffix: Azure Database Migration Service
description: Saiba como migrar de SQL Server para o SQL Instância Gerenciada do Azure usando Azure PowerShell e o serviço de migração de banco de dados do Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019,fasttrack-edit, devx-track-azurepowershell
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: aa97fa5e92d4afe11cf8af5f7cfd3458a3c12014
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/27/2020
ms.locfileid: "96302718"
---
# <a name="migrate-sql-server-to-sql-managed-instance-with-powershell--azure-database-migration-service"></a>Migrar SQL Server para o SQL Instância Gerenciada com o PowerShell & serviço de migração de banco de dados do Azure

Neste artigo, você migra o banco de dados **Adventureworks2016** restaurado para uma instância local do SQL Server 2005 ou superior para uma instância gerenciada SQL do Azure SQL usando o Microsoft Azure PowerShell. Você pode migrar bancos de dados de uma instância SQL Server para uma Instância Gerenciada SQL usando o `Az.DataMigration` módulo no Microsoft Azure PowerShell.

Neste artigo, você aprenderá como:
> [!div class="checklist"]
>
> * Crie um grupos de recursos.
> * Crie uma instância do Serviço de Migração de Banco de Dados do Azure.
> * Crie um projeto de migração em uma instância do serviço de migração de banco de dados do Azure.
> * Executar a migração.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Este artigo inclui detalhes sobre como executar migrações online e offline.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir essas etapas, você precisa:

* [SQL Server 2016 ou superior](https://www.microsoft.com/sql-server/sql-server-downloads) (qualquer edição).
* Uma cópia local do banco de dados **AdventureWorks2016** , que está disponível para download [aqui](/sql/samples/adventureworks-install-configure?view=sql-server-2017).
* Para habilitar o protocolo TCP/IP, que está desabilitado por padrão com a instalação do SQL Server Express. Habilite o protocolo TCP/IP seguindo o artigo [Habilitar ou desabilitar um protocolo de rede de servidor](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
* Configurar o [Firewall do Windows para acesso ao mecanismo de banco de dados](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Uma assinatura do Azure. Se você não tiver [uma conta gratuita](https://azure.microsoft.com/free/), crie uma antes de começar.
* Um Instância Gerenciada do SQL. Você pode criar um Instância Gerenciada do SQL seguindo os detalhes no artigo [criar um ASQL instância gerenciada](../azure-sql/managed-instance/instance-create-quickstart.md).
* Para baixar e instalar o [Assistente de migração de dados](https://www.microsoft.com/download/details.aspx?id=53595) v 3.3 ou posterior.
* Um Rede Virtual do Microsoft Azure criado usando o modelo de implantação Azure Resource Manager, que fornece o serviço de migração de banco de dados do Azure com conectividade site a site para seus servidores de origem locais usando o [ExpressRoute](../expressroute/expressroute-introduction.md) ou [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* Uma avaliação completa de seu banco de dados local e migração de esquema usando o Assistente de Migração de Dados, conforme descrito no artigo [executando uma avaliação de migração de SQL Server](/sql/dma/dma-assesssqlonprem).
* Para baixar e instalar o `Az.DataMigration` módulo (versão 0.7.2 ou posterior) do Galeria do PowerShell usando o [cmdlet Install-Module PowerShell](/powershell/module/powershellget/Install-Module?view=powershell-5.1).
* Para garantir que as credenciais usadas para se conectar à instância de SQL Server de origem tenham a permissão [Control Server](/sql/t-sql/statements/grant-server-permissions-transact-sql) .
* Para garantir que as credenciais usadas para se conectar ao SQL de destino Instância Gerenciada tenham a permissão CONTROL DATABASE nos bancos de dados SQL Instância Gerenciada de destino.

    > [!IMPORTANT]
    > Para migrações online, você já deve ter configurado suas credenciais de Azure Active Directory. Para obter mais informações, consulte o artigo [usar o portal para criar um aplicativo do Azure AD e uma entidade de serviço que pode acessar recursos](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Entrar na assinatura do Microsoft Azure

Entre em sua assinatura do Azure usando o PowerShell. Para obter mais informações, consulte o artigo [entrar com Azure PowerShell](/powershell/azure/authenticate-azureps).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos usando o [`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup) comando.

O exemplo a seguir cria um grupo de recursos chamado *MyResource* Group na região *leste dos EUA* .

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Criar uma instância do Serviço de Migração de Banco de Dados do Azure

Crie uma nova instância do Serviço de Migração de Banco de Dados do Azure usando o cmdlet `New-AzDataMigrationService`.
Esse cmdlet espera os seguintes parâmetros obrigatórios:

* *Nome do grupo de recursos do Azure*. Você pode usar o [`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup) comando para criar um grupo de recursos do Azure como mostrado anteriormente e fornecer seu nome como um parâmetro.
* *Nome do serviço*. Cadeia de caracteres que corresponde ao nome de serviço exclusivo desejado para o serviço de migração de banco de dados do Azure.
* *Local*. Especifica o local do serviço. Especifique um local de data center do Azure, como oeste dos EUA ou sudeste asiático.
* *SKU*. Esse parâmetro corresponde ao nome do SKU do DMS. Atualmente, os nomes de SKU com suporte são *Basic_1vCore*, *Basic_2vCores* *GeneralPurpose_4vCores*.
* *Identificador de Sub-rede Virtual*. Você pode usar o cmdlet [`New-AzVirtualNetworkSubnetConfig`](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) para criar uma sub-rede.

O exemplo a seguir cria um serviço chamado *mydms* no grupo de recursos *MyDMSResourceGroup* localizado na região *leste dos EUA* usando uma rede virtual chamada *MyVNET* e uma sub-rede chamada *mysubnet*.

> [!IMPORTANT]
> O trecho de código a seguir é para uma migração offline, que não requer uma instância do serviço de migração de banco de dados do Azure com base em uma SKU Premium. Para uma migração online, o valor do parâmetro-SKU deve incluir um SKU Premium.

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

Você pode criar um objeto de informações de conexão de banco de dados usando o `New-AzDmsConnInfo` cmdlet, que espera os seguintes parâmetros:

* *ServerType*. O tipo de conexão de banco de dados solicitado, por exemplo, SQL, Oracle ou MySQL. Use o SQL para SQL Server e SQL do Azure.
* *Fonte de fontes*. O nome ou IP de uma instância do SQL Server ou do banco de dados SQL do Azure.
* *AuthType*. O tipo de autenticação da conexão, que pode ser SqlAuthentication ou WindowsAuthentication.
* *TrustServerCertificate*. Esse parâmetro define um valor que indica se o canal é criptografado ao ignorar a cadeia de certificados para validar a confiança. O valor pode ser `$true` ou `$false` .

O exemplo a seguir cria um objeto de informações de conexão para um SQL Server de origem chamado *MySourceSQLServer* usando a autenticação do SQL:

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

O exemplo a seguir mostra a criação de informações de conexão para um Azure SQL Instância Gerenciada chamado ' targetmanagedinstance ':

```powershell
$targetResourceId = (Get-AzSqlInstance -Name "targetmanagedinstance").Id
$targetConnInfo = New-AzDmsConnInfo -ServerType SQLMI -MiResourceId $targetResourceId
```

### <a name="provide-databases-for-the-migration-project"></a>Fornecer bancos de dados para o projeto de migração

Crie uma lista de `AzDataMigrationDatabaseInfo` objetos que especificam bancos de dados como parte do projeto do serviço de migração de banco de dados do Azure, que pode ser fornecido como parâmetro para a criação do projeto. Você pode usar o cmdlet `New-AzDataMigrationDatabaseInfo` para criar `AzDataMigrationDatabaseInfo` .

O exemplo a seguir cria o `AzDataMigrationDatabaseInfo` projeto para o banco de dados **AdventureWorks2016** e o adiciona à lista a ser fornecida como parâmetro para a criação do projeto.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Criar um objeto de projeto

Por fim, você pode criar um projeto de serviço de migração de banco de dados do Azure chamado *MyDMSProject* localizado no *leste dos EUA* usando `New-AzDataMigrationProject` e adicionar as conexões de origem e de destino criadas anteriormente e a lista de bancos de dados a serem migrados.

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

Em seguida, crie e inicie uma tarefa do serviço de migração de banco de dados do Azure. Essa tarefa requer informações de credenciais de conexão para a origem e o destino, bem como a lista de tabelas de banco de dados a serem migradas e as informações já fornecidas com o projeto criado como um pré-requisito.

### <a name="create-credential-parameters-for-source-and-target"></a>Criar parâmetros de credenciais para a origem e o destino

Crie credenciais de segurança de conexão como um objeto [PSCredential](/dotnet/api/system.management.automation.pscredential?view=powershellsdk-1.1.0) .

O exemplo a seguir mostra a criação de objetos *PSCredential* para as conexões de origem e de destino, fornecendo senhas como variáveis de cadeia de caracteres *$sourcePassword* e *$targetPassword*.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-backup-fileshare-object"></a>Criar um objeto FileShare de backup

Agora crie um objeto FileShare que representa o compartilhamento de rede SMB local para o qual o serviço de migração de banco de dados do Azure pode pegar os backups de banco de dados de origem usando o `New-AzDmsFileShare` cmdlet

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>Criar objeto de banco de dados selecionado

A próxima etapa é selecionar os bancos de dados de origem e de destino usando o `New-AzDmsSelectedDB` cmdlet.

O exemplo a seguir é para migrar um banco de dados individual do SQL Server para um SQL do Azure Instância Gerenciada:

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

Se uma instância de SQL Server inteira precisar de um deslocamento e mudança para uma Instância Gerenciada do SQL do Azure, um loop para tirar todos os bancos de dados da fonte será fornecido abaixo. No exemplo a seguir, para $Server, $SourceUserName e $SourcePassword, forneça os detalhes de SQL Server de origem.

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

> [!NOTE]
> O serviço de migração de banco de dados do Azure não oferece suporte ao uso de um token SAS no nível da conta Você deve usar um URI de SAS para o contêiner da conta de armazenamento. [Saiba como obter o URI SAS do contêiner de blobs](../vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container).

### <a name="additional-configuration-requirements"></a>Requisitos de configuração adicionais

Há alguns requisitos adicionais que você precisa abordar, mas eles diferem dependendo se você está executando uma migração offline ou online.

#### <a name="offline-migrations"></a>Migrações offline

Somente para migrações offline, execute as seguintes tarefas de configuração adicionais.

* **Selecione logons**. Crie uma lista de logons a serem migrados, conforme mostrado no exemplo a seguir:

    ```powershell
    $selectedLogins = @("user1", "user2")
    ```

    > [!IMPORTANT]
    > Atualmente, o serviço de migração de banco de dados do Azure dá suporte apenas à migração de logons SQL

* **Selecione trabalhos do agente**. Crie a lista de trabalhos do Agent a serem migrados, conforme mostrado no exemplo a seguir:

    ```powershell
    $selectedAgentJobs = @("agentJob1", "agentJob2")
    ```

    > [!IMPORTANT]
    > Atualmente, o serviço de migração de banco de dados do Azure só dá suporte a trabalhos com etapas de trabalho do subsistema T-SQL.

#### <a name="online-migrations"></a>Migrações online

Para migrações online somente, execute as seguintes tarefas de configuração adicionais.

* **Configurar Azure Active Directory aplicativo**

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

### <a name="create-and-start-the-migration-task"></a>Criar e iniciar a tarefa de migração

Use o cmdlet `New-AzDataMigrationTask` para criar e iniciar uma tarefa de migração.

#### <a name="specify-parameters"></a>Especificar parâmetros

##### <a name="common-parameters"></a>Parâmetros comuns

Independentemente de você estar executando uma migração offline ou online, o `New-AzDataMigrationTask` cmdlet espera os seguintes parâmetros:

* *TaskType*. Tipo de tarefa de migração a ser criada para SQL Server para o tipo de migração do Azure SQL Instância Gerenciada *MigrateSqlServerSqlDbMi* é esperado. 
* *Nome do grupo de recursos*. Nome do grupo de recursos do Azure no qual a tarefa será criada.
* *ServiceName*. Instância do Serviço de Migração de Banco de Dados do Azure na qual a tarefa será criada.
* *ProjectName*. Nome do projeto do Serviço de Migração de Banco de Dados do Azure no qual criar a tarefa. 
* *Nome_Tarefa*. Nome da tarefa a ser criada. 
* *SourceConnection*. Objeto AzDmsConnInfo que representa a conexão SQL Server de origem.
* *TargetConnection*. Objeto AzDmsConnInfo que representa a conexão do Azure SQL Instância Gerenciada de destino.
* *SourceCred*. Objeto [PSCredential](/dotnet/api/system.management.automation.pscredential?view=powershellsdk-1.1.0) para se conectar ao servidor de origem.
* *TargetCred*. Objeto [PSCredential](/dotnet/api/system.management.automation.pscredential?view=powershellsdk-1.1.0) para se conectar ao servidor de destino.
* *SelectedDatabase*. Objeto AzDataMigrationSelectedDB que representa o mapeamento de banco de dados de origem e destino.
* *BackupFileShare*. Objeto FileShare que representa o compartilhamento de rede local para o qual o Serviço de Migração de Banco de Dados do Azure pode fazer backups de banco de dados de origem.
* *BackupBlobSasUri*. O URI de SAS que fornece ao Serviço de Migração de Banco de Dados do Azure acesso ao contêiner da conta de armazenamento para o qual o serviço faz upload dos arquivos de backup. Saiba como obter o URI de SAS para o contêiner de blob.
* *SelectedLogins*. Lista de logons selecionados para migrar.
* *SelectedAgentJobs*. Lista de trabalhos do agente selecionados para migrar.

##### <a name="additional-parameters"></a>Parâmetros adicionais

O `New-AzDataMigrationTask` cmdlet também espera parâmetros que são exclusivos para o tipo de migração, offline ou online, que você está executando.

* **Migrações offline**. Para migrações offline, o `New-AzDataMigrationTask` cmdlet também espera os seguintes parâmetros:

  * *SelectedLogins*. Lista de logons selecionados para migrar.
  * *SelectedAgentJobs*. Lista de trabalhos do agente selecionados para migrar.

* **Migrações online**. Para migrações online, o `New-AzDataMigrationTask` cmdlet também espera os parâmetros a seguir.

* *AzureActiveDirectoryApp*. Active Directory aplicativo.
* *StorageResourceID*. ID de recurso da conta de armazenamento.

#### <a name="create-and-start-an-offline-migration-task"></a>Criar e iniciar uma tarefa de migração offline

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

#### <a name="create-and-start-an-online-migration-task"></a>Criar e iniciar uma tarefa de migração online

O exemplo a seguir cria e inicia uma tarefa de migração online chamada **myDMSTask**:

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

    Para combinar detalhes de migração, como propriedades, estado e informações de banco de dados associadas à migração, use o seguinte trecho de código:

    ```powershell
    $CheckTask= Get-AzDataMigrationTask     -ResourceGroupName myResourceGroup `
                                            -ServiceName $service.Name `
                                        -ProjectName $project.Name `
                                            -Name myDMSTask `
                                            -ResultType DatabaseLevelOutput `
                        -Expand 
    Write-Host ‘$CheckTask.ProjectTask.Properties.Output’
    ```

2. Use a `$CheckTask` variável para obter o estado atual da tarefa de migração.

    Para usar a `$CheckTask` variável para obter o estado atual da tarefa de migração, você pode monitorar a tarefa de migração em execução consultando a propriedade State da tarefa, conforme mostrado no exemplo a seguir:

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

## <a name="performing-the-cutover-online-migrations-only"></a>Executando a transferência (somente migrações online)

Com uma migração online, um backup completo e a restauração de bancos de dados são executados e, em seguida, o trabalho prossegue na restauração dos logs de transações armazenados no BackupFileShare.

Quando o banco de dados em um Instância Gerenciada SQL do Azure é atualizado com os mais recentes e está em sincronia com o banco de dado de origem, você pode executar uma transferência.

O exemplo a seguir concluirá o cutover\migration. Os usuários chamam esse comando a seu critério.

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>Excluindo a instância do serviço de migração de banco de dados do Azure

Depois que a migração for concluída, você poderá excluir a instância do serviço de migração de banco de dados do Azure:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>Recursos adicionais

Para obter informações sobre cenários de migração adicionais (pares de origem/destino), consulte o [Guia de migração de banco de dados](https://datamigration.microsoft.com/)da Microsoft.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o serviço de migração de banco de dados do Azure no artigo o [que é o serviço de migração de banco de dados do Azure?](./dms-overview.md).