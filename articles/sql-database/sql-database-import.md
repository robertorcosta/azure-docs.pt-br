---
title: Importar um arquivo BACPAC para criar um banco de dados
description: Crie um novo banco de dados SQL do Azure importando um arquivo BACPAC existente.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/20/2019
ms.openlocfilehash: 05698596f966f879da1affc58af0122d08d519ff
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79256231"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database"></a>Início rápido: importar um arquivo BACPAC para um banco de dados no banco de dados SQL do Azure

Você pode importar um banco de dados do SQL Server para um banco de dados no Banco de Dados SQL do Azure usando um arquivo [BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac). Você pode importar dados de um arquivo `BACPAC` armazenado no Armazenamento de Blobs do Azure (apenas no Armazenamento Standard) ou de uma determinada localização do armazenamento local. Para maximizar a velocidade de importação fornecendo mais recursos e mais rápidos, dimensione seu banco de dados para uma camada de serviço superior e o tamanho da computação durante o processo de importação. Em seguida, você poderá reduzir verticalmente após a importação ser realizada.

> [!NOTE]
> O nível de compatibilidade do banco de dados se baseia no nível de compatibilidade do banco de dados de origem.

> [!IMPORTANT]
> Após a importação dos dados, é possível operar o banco de dados em seu nível de compatibilidade atual (nível 100 para o banco de dados AdventureWorks2008R2) ou em um nível superior. Para obter mais informações sobre as implicações e as opções para a operação de um banco de dados em um nível de compatibilidade específico, consulte [nível de compatibilidade de ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level). Consulte também [ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) para obter informações sobre configurações de nível de banco de dados adicionais relacionadas aos níveis de compatibilidade.

## <a name="using-azure-portal"></a>Usando o Portal do Azure

Assista a este vídeo para ver como importar de um arquivo BACPAC no portal do Azure ou Continue lendo abaixo:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Its-just-SQL-Restoring-a-database-to-Azure-SQL-DB-from-backup/player?WT.mc_id=dataexposed-c9-niner]

O [portal do Azure](https://portal.azure.com) *só* dá suporte à criação de um único banco de dados no banco de dados SQL do Azure e *somente* de um arquivo BACPAC armazenado no armazenamento de BLOBs do Azure.

Atualmente, não há suporte para migrar um banco de dados para uma [instância gerenciada](sql-database-managed-instance.md) de um arquivo BACPAC usando o Azure PowerShell. Em vez disso, use SQL Server Management Studio ou SqlPackage.

> [!NOTE]
> Os computadores que processam solicitações de importação/exportação enviadas por meio do portal do Azure ou do PowerShell precisam armazenar o arquivo BACPAC, bem como arquivos temporários gerados pela estrutura de aplicativo da camada de dados (DacFX). O espaço em disco necessário varia significativamente entre os bancos de dados com o mesmo tamanho e pode exigir espaço em disco de até 3 vezes o tamanho do banco de dados. Os computadores que executam a solicitação de importação/exportação só têm 450GB espaço em disco local. Como resultado, algumas solicitações podem falhar com o erro `There is not enough space on the disk`. Nesse caso, a solução alternativa é executar SqlPackage. exe em um computador com espaço em disco local suficiente. Incentivamos o uso do SqlPackage para importar/exportar bancos de dados maiores que 150 GB para evitar esse problema.

1. Para importar de arquivo BACPAC para um novo banco de dados individual usando o portal do Azure, abra a página do servidor de banco de dados apropriada e, em seguida, na barra de ferramentas, selecione **Importar banco de dados**.  

   ![Banco de dados import1](./media/sql-database-import/sql-server-import-database.png)

1. Selecione a conta de armazenamento e o contêiner para o arquivo BACPAC e, em seguida, selecione o arquivo BACPAC do qual importar.

1. Especifique o tamanho do novo banco de dados (normalmente, o mesmo que o original) e forneça as credenciais do SQL Server de destino. Para obter uma lista de valores possíveis para um novo Banco de Dados SQL do Azure, confira [Criar banco de dados](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current).

   ![Banco de dados import2](./media/sql-database-import/sql-server-import-database-settings.png)

1. Clique em **OK**.

1. Para monitorar o progresso de uma importação, abra a página do servidor do banco de dados e, em **Configurações**, selecione **Histórico de importação/exportação**. Quando obtiver êxito, a importação terá o status **Concluído**.

   ![Status de importação do banco de dados](./media/sql-database-import/sql-server-import-database-history.png)

1. Para verificar se o banco de dados está ativo no servidor de banco de dados, selecione **Bancos de dados SQL** e verifique se o novo banco de dados está **Online**.

## <a name="using-sqlpackage"></a>Usando o SqlPackage

Para importar um Banco de Dados do SQL Server usando o utilitário de linha de comando [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage), confira [Importar parâmetros e propriedades](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties). O SqlPackage tem as versões mais recentes do [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) e do [SQL Server Data Tools para Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Você também pode fazer o download da versão mais recente do [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) do Centro de Download da Microsoft.

Para escala e desempenho, recomendamos usar o SqlPackage na maioria dos ambientes de produção em vez de usar no portal do Azure. Para ler uma postagem de blog da Equipe de Consultoria ao Cliente do SQL Server sobre a migração usando arquivos `BACPAC`, confira [Migrando do SQL Server para o Banco de Dados SQL do Azure usando arquivos BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

O seguinte comando SqlPackage importa o banco de dados **AdventureWorks2008R2** do armazenamento local para um servidor do Banco de Dados SQL do Azure chamado **mynewserver20170403**. Ele cria um novo banco de dados chamado **myMigratedDatabase** com uma camada de serviço **Premium** e um Objetivo de serviço **P6**. Altere esses valores conforme apropriado para o seu ambiente.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=<migratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Para se conectar a um servidor do Banco de Dados SQL gerenciando um banco de dados individual por trás de um firewall corporativo, o firewall precisa estar com a porta 1433 aberta. Para se conectar a uma instância gerenciada, você deve ter uma [conexão ponto a site](sql-database-managed-instance-configure-p2s.md) ou uma conexão de rota expressa.

Este exemplo mostra como importar um banco de dados usando o SqlPackage com a Autenticação Universal do Active Directory.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="using-powershell"></a>Usando o PowerShell

> [!NOTE]
> Atualmente, [uma instância gerenciada](sql-database-managed-instance.md) não dá suporte à migração de um banco de dados para um banco de dados de instância de um arquivo BACPAC usando Azure PowerShell. Para importar para uma instância gerenciada, use o SQL Server Management Studio ou o SQLPackage.

> [!NOTE]
> As máquinas que processam solicitações de importação/exportação enviadas por meio do portal ou do PowerShell precisam armazenar o arquivo bacpac, bem como arquivos temporários gerados pela estrutura de aplicativo da camada de dados (DacFX). O espaço em disco necessário varia significativamente entre os bancos de dados com o mesmo tamanho e pode levar até três vezes do tamanho do banco de dados. Os computadores que executam a solicitação de importação/exportação só têm 450GB espaço em disco local. Como resultado, algumas solicitações podem falhar com o erro "não há espaço suficiente no disco". Nesse caso, a solução alternativa é executar SqlPackage. exe em um computador com espaço em disco local suficiente. Ao importar/exportar bancos de dados maiores do que 150 GB, use SqlPackage para evitar esse problema.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell (RM) ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. O módulo AzureRM continuará a receber correções de bugs até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos. Para obter mais informações sobre sua compatibilidade, consulte [apresentando o novo módulo Azure PowerShell AZ](/powershell/azure/new-azureps-module-az).

Use o cmdlet [New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport) para enviar uma solicitação de importação de banco de dados para o serviço de banco de dados SQL do Azure. Dependendo do tamanho do banco de dados, a importação pode levar algum tempo para ser concluída.

```powershell
$importRequest = New-AzSqlDatabaseImport -ResourceGroupName "<resourceGroupName>" `
    -ServerName "<serverName>" -DatabaseName "<databaseName>" `
    -DatabaseMaxSizeBytes "<databaseSizeInBytes>" -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzStorageAccountKey `
        -ResourceGroupName "<resourceGroupName>" -StorageAccountName "<storageAccountName>").Value[0] `
        -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
        -Edition "Standard" -ServiceObjectiveName "P6" `
        -AdministratorLogin "<userId>" `
        -AdministratorLoginPassword $(ConvertTo-SecureString -String "<password>" -AsPlainText -Force)
```

Você pode usar o cmdlet [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) para verificar o progresso da importação. Executar o cmdlet imediatamente após a solicitação geralmente retorna `Status: InProgress`. A importação será concluída quando você vir `Status: Succeeded`.

```powershell
$importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink

[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress") {
    $importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}

[Console]::WriteLine("")
$importStatus
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Use o comando [AZ-SQL-DB-Import](/cli/azure/sql/db#az-sql-db-import) para enviar uma solicitação de importação de banco de dados para o serviço de banco de dados SQL do Azure. Dependendo do tamanho do banco de dados, a importação pode levar algum tempo para ser concluída.

```azurecli
# get the storage account key
az storage account keys list --resource-group "<resourceGroup>" --account-name "<storageAccount>"

az sql db import --resource-group "<resourceGroup>" --server "<server>" --name "<database>" `
    --storage-key-type "StorageAccessKey" --storage-key "<storageAccountKey>" `
    --storage-uri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -u "<userId>" -p "<password>"
```

* * *

> [!TIP]
> Para outro exemplo de script, confira [Importar um banco de dados de um arquivo BACPAC](scripts/sql-database-import-from-bacpac-powershell.md).

## <a name="limitations"></a>Limitações

Não há suporte para a importação de um banco de dados no pool elástico. Você pode importar dados para um banco de dados individual e, em seguida, mover o banco de dados para um pool elástico.

## <a name="import-using-wizards"></a>Importar usando assistentes

Você também pode usar esses assistentes.

- [Importar o Assistente de Aplicativo da Camada de Dados no SQL Server Management Studio](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [Assistente de Importação e Exportação do SQL Server](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Próximas etapas

- Para saber como se conectar a um banco de dados SQL importado e consultá-lo, consulte [início rápido: Azure SQL Server Management Studio SQL](sql-database-connect-query-ssms.md)
- Para ler uma postagem de blog da Equipe de Consultoria ao Cliente do SQL Server sobre a migração usando arquivos BACPAC, confira [Migrando do SQL Server para o Banco de Dados SQL do Azure usando arquivos BACPAC](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407).
- Para ver uma discussão sobre todo o processo de migração do banco de dados do SQL Server, incluindo as recomendações de desempenho, consulte [Migração de um banco de dados do SQL Server para o Banco de Dados SQL do Azure](sql-database-single-database-migrate.md).
- Para aprender como gerenciar e compartilhar chaves de armazenamento e assinaturas de acesso compartilhado com segurança, consulte [Guia de Segurança do Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
