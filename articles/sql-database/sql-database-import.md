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
ms.openlocfilehash: 7db3f6f50745526876ef2ca6e3253f1931420f0f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683258"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database"></a>Quickstart: Importe um arquivo BACPAC para um banco de dados no Banco de Dados SQL do Azure

Você pode importar um banco de dados do SQL Server para um banco de dados no Banco de Dados SQL do Azure usando um arquivo [BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac). Você pode importar dados de um arquivo `BACPAC` armazenado no Armazenamento de Blobs do Azure (apenas no Armazenamento Standard) ou de uma determinada localização do armazenamento local. Para maximizar a velocidade de importação fornecendo mais recursos e mais rápidos, dimensione seu banco de dados para uma camada de serviço superior e o tamanho da computação durante o processo de importação. Em seguida, você poderá reduzir verticalmente após a importação ser realizada.

> [!NOTE]
> O nível de compatibilidade do banco de dados se baseia no nível de compatibilidade do banco de dados de origem.

> [!IMPORTANT]
> Após a importação dos dados, é possível operar o banco de dados em seu nível de compatibilidade atual (nível 100 para o banco de dados AdventureWorks2008R2) ou em um nível superior. Para obter mais informações sobre as implicações e as opções para a operação de um banco de dados em um nível de compatibilidade específico, consulte [nível de compatibilidade de ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level). Consulte também [ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) para obter informações sobre configurações de nível de banco de dados adicionais relacionadas aos níveis de compatibilidade.

## <a name="using-azure-portal"></a>Usando o Portal do Azure

Assista a este vídeo para ver como importar de um arquivo BACPAC no portal do Azure ou continuar lendo abaixo:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Its-just-SQL-Restoring-a-database-to-Azure-SQL-DB-from-backup/player?WT.mc_id=dataexposed-c9-niner]

O [portal do Azure](https://portal.azure.com) *somente* oferece suporte à criação de um banco de dados individual no Banco de Dados SQL do Azure e *somente* de um arquivo BACPAC armazenado no Armazenamento de Blobs do Azure.

A migração de um banco de dados para uma [instância gerenciada](sql-database-managed-instance.md) a partir de um arquivo BACPAC usando o Azure PowerShell não é suportada no momento. Use o SQL Server Management Studio ou o SQLPackage.

> [!NOTE]
> As solicitações de importação/exportação de máquinas enviadas através do portal Azure ou do PowerShell precisam armazenar o arquivo BACPAC, bem como arquivos temporários gerados pelo Data-Tier Application Framework (DacFX). O espaço em disco necessário varia significativamente entre bancos de dados com o mesmo tamanho e pode exigir espaço em disco até 3 vezes o tamanho do banco de dados. As máquinas que executam a solicitação de importação/exportação têm apenas 450GB de espaço em disco local. Como resultado, algumas solicitações podem `There is not enough space on the disk`falhar com o erro . Neste caso, a solução é executar sqlpackage.exe em uma máquina com espaço em disco local suficiente. Nós encorajamos o uso do SqlPackage para importar/exportar bancos de dados maiores que 150GB para evitar esse problema.

1. Para importar de arquivo BACPAC para um novo banco de dados individual usando o portal do Azure, abra a página do servidor de banco de dados apropriada e, em seguida, na barra de ferramentas, selecione **Importar banco de dados**.  

   ![Banco de dados import1](./media/sql-database-import/sql-server-import-database.png)

1. Selecione a conta de armazenamento e o contêiner para o arquivo BACPAC e, em seguida, selecione o arquivo BACPAC do qual importar.

1. Especifique o tamanho do novo banco de dados (normalmente, o mesmo que o original) e forneça as credenciais do SQL Server de destino. Para obter uma lista de valores possíveis para um novo Banco de Dados SQL do Azure, confira [Criar banco de dados](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current).

   ![Banco de dados import2](./media/sql-database-import/sql-server-import-database-settings.png)

1. Clique em **OK**.

1. Para monitorar o progresso de uma importação, abra a página do servidor do banco de dados e, em **Configurações**, selecione **Histórico de importação/exportação**. Quando obtiver êxito, a importação terá o status **Concluído**.

   ![Status de importação do banco de dados](./media/sql-database-import/sql-server-import-database-history.png)

1. Para verificar se o banco de dados está ativo no servidor de banco de dados, selecione **Bancos de dados SQL** e verifique se o novo banco de dados está **Online**.

## <a name="using-sqlpackage"></a>Usando SqlPackage

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
> [Uma instância gerenciada](sql-database-managed-instance.md) não suporta atualmente a migração de um banco de dados para um banco de dados de instâncias de um arquivo BACPAC usando o Azure PowerShell. Para importar para uma instância gerenciada, use o SQL Server Management Studio ou o SQLPackage.

> [!NOTE]
> As máquinas que processam solicitações de importação/exportação enviadas através do portal ou do Powershell precisam armazenar o arquivo bacpac, bem como arquivos temporários gerados pelo Data-Tier Application Framework (DacFX). O espaço em disco necessário varia significativamente entre os DBs com o mesmo tamanho e pode levar até 3 vezes o tamanho do banco de dados. As máquinas que executam a solicitação de importação/exportação têm apenas 450GB de espaço em disco local. Como resultado, algumas solicitações podem falhar com o erro "Não há espaço suficiente no disco". Neste caso, a solução é executar sqlpackage.exe em uma máquina com espaço em disco local suficiente. Ao importar/exportar bancos de dados maiores que 150GB, use o SqlPackage para evitar esse problema.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> O módulo RM (PowerShell Azure Resource Manager, gerente de recursos do Azure) ainda é suportado pelo Banco de Dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo Az.Sql. O módulo AzureRM continuará recebendo correções de bugs até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. Para obter mais informações sobre sua compatibilidade, consulte [Introduzindo o novo módulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

Use o [cmdlet New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport) para enviar uma solicitação de banco de dados de importação ao serviço de banco de dados SQL do Azure. Dependendo do tamanho do banco de dados, a importação pode levar algum tempo para ser concluída.

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

Você pode usar o [cmdlet Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) para verificar o progresso da importação. Executando o cmdlet imediatamente após `Status: InProgress`a solicitação geralmente retorna . A importação é completa `Status: Succeeded`quando você vê .

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

Use o comando [az-sql-db-import](/cli/azure/sql/db#az-sql-db-import) para enviar uma solicitação de banco de dados de importação ao serviço Azure SQL Database. Dependendo do tamanho do banco de dados, a importação pode levar algum tempo para ser concluída.

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

- Não há suporte para a importação de um banco de dados no pool elástico. Você pode importar dados para um banco de dados individual e, em seguida, mover o banco de dados para um pool elástico.
- O Serviço de Exportação de Importação não funciona quando o permitir que o acesso aos serviços do Azure seja definido como OFF. No entanto, você pode contornar o problema executando manualmente sqlpackage.exe a partir de um VM Azure ou realizando a exportação diretamente em seu código usando a API DACFx.

## <a name="import-using-wizards"></a>Importar usando assistentes

Você também pode usar esses assistentes.

- [Importar o Assistente de Aplicativo da Camada de Dados no SQL Server Management Studio](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [Assistente de importação e exportação do servidor SQL](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Próximas etapas

- Para saber como se conectar e consultar um banco de dados SQL importado, consulte [Quickstart: Azure SQL Database: Use o SQL Server Management Studio para conectar e consultar dados](sql-database-connect-query-ssms.md).
- Para ler uma postagem de blog da Equipe de Consultoria ao Cliente do SQL Server sobre a migração usando arquivos BACPAC, confira [Migrando do SQL Server para o Banco de Dados SQL do Azure usando arquivos BACPAC](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407).
- Para ver uma discussão sobre todo o processo de migração do banco de dados do SQL Server, incluindo as recomendações de desempenho, consulte [Migração de um banco de dados do SQL Server para o Banco de Dados SQL do Azure](sql-database-single-database-migrate.md).
- Para aprender como gerenciar e compartilhar chaves de armazenamento e assinaturas de acesso compartilhado com segurança, consulte [Guia de Segurança do Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
