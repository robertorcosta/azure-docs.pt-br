---
title: Gerenciamento de espaço de arquivos de bancos de dados únicos/agrupados
description: Esta página descreve como gerenciar o espaço no arquivo com bancos de dados individuais e em pool no Banco de Dados SQL do Azure e fornece exemplos de código para determinar se você precisará reduzir um banco de dados individual ou em pool, além de como executar uma operação de redução do banco de dados.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: jrasnick, carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 007bbffbd7c4fcad339f88eb78991eb39fb829e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74420974"
---
# <a name="manage-file-space-for-single-and-pooled-databases-in-azure-sql-database"></a>Gerenciar o espaço de arquivo para bancos de dados individuais e em pool no Banco de Dados SQL do Azure

Este artigo descreve os diferentes tipos de espaço de armazenamento para bancos de dados individuais e em pool no Banco de Dados SQL do Azure e as etapas que podem ser executadas quando o espaço no arquivo alocado para bancos de dados e pools elásticos precisa ser gerenciado explicitamente.

> [!NOTE]
> Este artigo faz não se aplica à opção de implantação de instância gerenciada no Banco de Dados SQL do Azure.

## <a name="overview"></a>Visão geral

Com bancos de dados individuais e em pool no Banco de Dados SQL do Azure, há padrões de carga de trabalho nos quais a alocação de arquivos de dados subjacentes para bancos de dados pode se tornar maior do que a quantidade de páginas de dados usadas. Essa condição pode ocorrer quando o espaço usado aumenta e os dados são excluídos posteriormente. O motivo é porque o espaço no arquivo alocado não é recuperado automaticamente quando os dados são excluídos.

O monitoramento do uso do espaço no arquivo e a redução dos arquivos de dados podem ser necessários nos seguintes cenários:

- Permitir o crescimento de dados em um pool elástico quando o espaço no arquivo alocado para seus bancos de dados atingir o tamanho máximo do pool.
- Permitir diminuir o tamanho máximo de um único banco de dados ou conjunto elástico.
- Permitir a alteração de um único banco de dados ou conjunto elástico para uma camada de serviço ou camada de desempenho diferente com um tamanho máximo menor.

### <a name="monitoring-file-space-usage"></a>Monitorando o uso de espaço de arquivo

A maioria das métricas de espaço de armazenamento exibido no portal do Azure e as seguintes APIs apenas medir o tamanho das páginas de dados usados:

- APIs de métricas baseadas no Azure Resource Manager, incluindo [get-metrics](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetric) do PowerShell
- T-SQL: [sys.DM db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)

No entanto, as seguintes APIs também medem o tamanho do espaço alocado para os bancos de dados Elástico e de pools:

- T-SQL: [sys. resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)
- T-SQL: [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)

### <a name="shrinking-data-files"></a>Redução de arquivos de dados

O serviço de Banco de Dados SQL não reduz automaticamente os arquivos de banco de dados para recuperar espaço alocado não utilizado devido ao possível impacto no desempenho do banco de dados.  No entanto, os clientes podem reduzir os arquivos de dados por meio de autoatendimento no momento de sua escolha, seguindo as etapas descritas no [reclaim espaço alocado não utilizado](#reclaim-unused-allocated-space).

> [!NOTE]
> Ao contrário dos arquivos de dados, o serviço Banco de Dados SQL reduz automaticamente os arquivos de log, pois essa operação não afeta o desempenho do banco de dados.

## <a name="understanding-types-of-storage-space-for-a-database"></a>Entendendo os tipos de espaço de armazenamento para um banco de dados

Noções básicas sobre as quantidades de espaço de armazenamento a seguir são importantes para gerenciar o espaço de arquivo de banco de dados.

|Quantidade de banco de dados|Definição|Comentários|
|---|---|---|
|**Espaço de dados usado**|A quantidade de espaço usada para armazenar dados do banco de dados em páginas de 8 KB.|Geralmente, esse espaço utilizado aumenta (diminui) em inserções (exclusões). Em alguns casos, o espaço utilizado não é alterado em inserções ou exclusões, dependendo da quantidade e do padrão de dados envolvidos na operação e de qualquer fragmentação. Por exemplo, excluir uma linha de cada página de dados não diminui necessariamente o espaço usado.|
|**Espaço alocado de dados**|A quantidade de espaço no arquivo formatado disponibilizada para armazenar dados do banco de dados.|O quantidade de espaço alocado cresce automaticamente, mas nunca diminui após as exclusões. Esse comportamento garante que as futuras inserções sejam mais rápidas, já que o espaço não precisa ser reformatado.|
|**Espaço de dados alocados, mas não utilizado**|A diferença entre a quantidade de espaço de dados alocado e espaço de dados usado.|Essa quantidade representa a quantidade máxima de espaço livre que pode ser recuperado pela redução de arquivos de dados do banco de dados.|
|**Tamanho máximo dos dados**|A quantidade máxima de espaço para dados que pode ser usada para armazenar dados do banco de dados.|A quantidade do espaço de dados alocados não pode crescer além do tamanho máximo de dados.|

O diagrama a seguir ilustra o relacionamento entre os tipos de espaço diferentes de espaço de armazenamento para um banco de dados.

![relações e tipos de espaço de armazenamento](./media/sql-database-file-space-management/storage-types.png)

## <a name="query-a-single-database-for-storage-space-information"></a>Consultar um banco de dados individual para informações de espaço de armazenamento

As consultas a seguir podem ser usadas para determinar as quantidades de espaço de armazenamento para um banco de dados individual.  

### <a name="database-data-space-used"></a>Espaço de dados do banco de dados usado

Modifique a consulta a seguir para retornar a quantidade de espaço para dados do banco de dados usado.  Unidades do resultado da consulta são em MB.

```sql
-- Connect to master
-- Database data space used in MB
SELECT TOP 1 storage_in_megabytes AS DatabaseDataSpaceUsedInMB
FROM sys.resource_stats
WHERE database_name = 'db1'
ORDER BY end_time DESC
```

### <a name="database-data-space-allocated-and-unused-allocated-space"></a>Espaço de dados de banco de dados alocado e espaço alocado não usado

Use a seguinte consulta para retornar a quantidade de espaço de dados do banco de dados alocado e a quantidade de espaço não utilizado alocada.  Unidades do resultado da consulta são em MB.

```sql
-- Connect to database
-- Database data space allocated in MB and database data space allocated unused in MB
SELECT SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB,
SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB
FROM sys.database_files
GROUP BY type_desc
HAVING type_desc = 'ROWS'
```

### <a name="database-data-max-size"></a>Tamanho máximo de dados de banco de dados

Modifique a consulta a seguir para retornar o tamanho máximo do banco de dados.  Unidades do resultado da consulta são em bytes.

```sql
-- Connect to database
-- Database data max size in bytes
SELECT DATABASEPROPERTYEX('db1', 'MaxSizeInBytes') AS DatabaseDataMaxSizeInBytes
```

## <a name="understanding-types-of-storage-space-for-an-elastic-pool"></a>Noções básicas sobre tipos de espaço de armazenamento para um pool elástico

Noções básicas sobre as quantidades de espaço de armazenamento a seguir são importantes para gerenciar o espaço de arquivo de banco de dados.

|Quantidade de pool elástico|Definição|Comentários|
|---|---|---|
|**Espaço de dados usado**|O resumo de espaço de dados usado por todos os bancos de dados no pool elástico.||
|**Espaço alocado de dados**|O resumo de espaço de dados alocado por todos os bancos de dados no pool elástico.||
|**Espaço de dados alocados, mas não utilizado**|A diferença entre a quantidade de espaço de dados alocado e espaço de dados usado por todos os banco de dados no pool elástico.|Essa quantidade representa a quantidade máxima de espaço alocado para o pool elástico que pode ser recuperado pela redução de arquivo de dados do banco de dados.|
|**Tamanho máximo dos dados**|A quantidade máxima de espaço para dados que pode ser usada pelo pool elástico de todos esses bancos de dados.|O espaço alocado não deve exceder o tamanho máximo do pool elástico.  Se essa condição ocorrer, o espaço alocado que não for utilizado poderá ser recuperado, encolhendo os arquivos de dados do banco de dados.|

## <a name="query-an-elastic-pool-for-storage-space-information"></a>Consultar um pool elástico para informações de espaço de armazenamento

As consultas a seguir podem ser usadas para determinar as quantidades de espaço de armazenamento para um pool elástico.  

### <a name="elastic-pool-data-space-used"></a>Espaço de dados do pool elástico usado

Modifique a consulta a seguir para retornar a quantidade de espaço para dados do conjunto elástico usado.  Unidades do resultado da consulta são em MB.

```sql
-- Connect to master
-- Elastic pool data space used in MB  
SELECT TOP 1 avg_storage_percent / 100.0 * elastic_pool_storage_limit_mb AS ElasticPoolDataSpaceUsedInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

### <a name="elastic-pool-data-space-allocated-and-unused-allocated-space"></a>Espaço de dados de pool elástico alocado e espaço alocado não usado

Modifique os exemplos a seguir para retornar uma tabela listando o espaço alocado e o espaço alocado não utilizado para cada banco de dados em um pool elástico. A tabela ordena bancos de dados desses bancos de dados com a maior quantidade de espaço alocado não utilizado para a menor quantidade de espaço alocado não utilizado.  Unidades do resultado da consulta são em MB.  

Os resultados da consulta para determinar o espaço alocado para cada banco de dados no pool podem ser incluídos juntos para determinar o espaço do pool elástico alocado. O espaço do pool elástico alocado não deve exceder o tamanho máximo do pool elástico.  

> [!IMPORTANT]
> O módulo RM (PowerShell Azure Resource Manager, gerente de recursos do Azure) ainda é suportado pelo Banco de Dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo Az.Sql. O módulo AzureRM continuará recebendo correções de bugs até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. Para obter mais informações sobre sua compatibilidade, consulte [Introduzindo o novo módulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

O script do PowerShell requer o módulo do SQL Server PowerShell - consulte [Download do módulo do PowerShell](https://docs.microsoft.com/sql/powershell/download-sql-server-ps-module) para instalar.

```powershell
$resourceGroupName = "<resourceGroupName>"
$serverName = "<serverName>"
$poolName = "<poolName>"
$userName = "<userName>"
$password = "<password>"

# get list of databases in elastic pool
$databasesInPool = Get-AzSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName `
    -ServerName $serverName -ElasticPoolName $poolName
$databaseStorageMetrics = @()

# for each database in the elastic pool, get space allocated in MB and space allocated unused in MB
foreach ($database in $databasesInPool) {
    $sqlCommand = "SELECT DB_NAME() as DatabaseName, `
    SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, `
    SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB `
    FROM sys.database_files `
    GROUP BY type_desc `
    HAVING type_desc = 'ROWS'"
    $serverFqdn = "tcp:" + $serverName + ".database.windows.net,1433"
    $databaseStorageMetrics = $databaseStorageMetrics + 
        (Invoke-Sqlcmd -ServerInstance $serverFqdn -Database $database.DatabaseName `
            -Username $userName -Password $password -Query $sqlCommand)
}

# display databases in descending order of space allocated unused
Write-Output "`n" "ElasticPoolName: $poolName"
Write-Output $databaseStorageMetrics | Sort -Property DatabaseDataSpaceAllocatedUnusedInMB -Descending | Format-Table
```

A captura de tela a seguir é um exemplo da saída do script:

![espaço alocado de pool elástico e exemplo de espaço alocado não utilizado](./media/sql-database-file-space-management/elastic-pool-allocated-unused.png)

### <a name="elastic-pool-data-max-size"></a>Tamanho máximo dos dados do pool elástico

Modifique a seguinte consulta T-SQL para retornar o tamanho máximo dos dados do pool elástico.  Unidades do resultado da consulta são em MB.

```sql
-- Connect to master
-- Elastic pools max size in MB
SELECT TOP 1 elastic_pool_storage_limit_mb AS ElasticPoolMaxSizeInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

## <a name="reclaim-unused-allocated-space"></a>Recuperar espaço alocado não utilizado

> [!NOTE]
> Esse comando pode afetar o desempenho do banco de dados enquanto está em execução e, se possível, deve ser executado durante períodos de baixa utilização.

### <a name="dbcc-shrink"></a>escolher DBCC

Depois que os bancos de dados foram identificados para recuperar o espaço alocado não utilizado, modifique o nome do banco de dados no comando a seguir para reduzir os arquivos de dados para cada banco de dados.

```sql
-- Shrink database data space allocated.
DBCC SHRINKDATABASE (N'db1')
```

Esse comando pode afetar o desempenho do banco de dados enquanto está em execução e, se possível, deve ser executado durante períodos de baixa utilização.  

Para obter mais informações sobre este comando, consulte [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql).

### <a name="auto-shrink"></a>A redução automática

Como alternativa, a redução automática pode ser ativada para um banco de dados.  A redução automática reduz a complexidade do gerenciamento de arquivos e é menos impactante para o desempenho do banco de dados do que o `SHRINKDATABASE` ou `SHRINKFILE`.  A redução automática pode ser particularmente útil para gerenciar pools elásticos com muitos bancos de dados.  No entanto, a redução automática pode ser menos eficaz na recuperação de espaço no arquivo do que `SHRINKDATABASE` e `SHRINKFILE`.
Para ativar a redução automática, modifique o nome do banco de dados no comando a seguir.

```sql
-- Enable auto-shrink for the database.
ALTER DATABASE [db1] SET AUTO_SHRINK ON
```

Para obter mais informações sobre esse comando, consulte [banco de dados definido](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) opções.

### <a name="rebuild-indexes"></a>Recriar índices

Depois que os arquivos de dados do banco de dados são reduzidos, os índices podem se tornar fragmentados e perder sua eficácia de otimização do desempenho. Se a degradação do desempenho ocorrer, considere reconstruir os índices do banco de dados. Para obter mais informações sobre fragmentação e reconstrução de índices, consulte [Reorganizar e reconstruir índices](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre tamanhos máximos do banco de dados, consulte:
  - [Banco de dados SQL do Azure Limites do modelo de compra baseado em vCore para um único banco de dados](sql-database-vcore-resource-limits-single-databases.md)
  - [Limites de recursos para bancos de dados individuais usando o modelo de compra baseado em DTU](sql-database-dtu-resource-limits-single-databases.md)
  - [Limites do modelo de compra baseado em vCore para Banco de Dados SQL do Azure para pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md)
  - [Limites de recursos para pools elásticos usando o modelo de compra baseado em DTU](sql-database-dtu-resource-limits-elastic-pools.md)
- Para mais informações sobre o `SHRINKDATABASE`comando, consulte [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql).
- Para obter mais informações sobre fragmentação e reconstrução de índices, consulte [Reorganizar e reconstruir índices](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).
