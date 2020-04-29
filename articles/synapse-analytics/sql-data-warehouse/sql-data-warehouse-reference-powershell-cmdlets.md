---
title: APIs REST do PowerShell &
description: Encontre os principais cmdlets do PowerShell para o pool do SQL do Azure Synapse Analytics, incluindo como pausar e retomar um banco de dados.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: f3d6d0c1f71e2262e943998cdc08717291903365
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80743829"
---
# <a name="powershell--rest-apis-for-azure-synapse-analytics-sql-pool"></a>PowerShell & APIs REST para o pool SQL do Azure Synapse Analytics

Muitas tarefas administrativas do pool SQL do Azure Synapse Analytics podem ser gerenciadas usando cmdlets Azure PowerShell ou APIs REST.  Abaixo estão alguns exemplos de como usar comandos do PowerShell para automatizar tarefas comuns em seu pool do SQL.  Para obter alguns bons exemplos de REST, consulte o artigo [Gerenciar escalabilidade com REST](sql-data-warehouse-manage-compute-rest-api.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="get-started-with-azure-powershell-cmdlets"></a>Introdução aos cmdlets do Azure PowerShell

1. Abra o Windows PowerShell.
2. No prompt do PowerShell, execute estes comandos para entrar no Azure Resource Manager e selecione sua assinatura.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Select-AzSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-data-warehouse-example"></a>Pausar data warehouse exemplo

Pause um banco de dados denominado "Database02" hospedado em um servidor denominado "Server01."  O servidor está em um grupo de recursos do Azure denominado "ResourceGroup1".

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```

Uma variação, este exemplo canaliza o objeto recuperado para [Suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).  Como resultado, o banco de dados é pausado. O comando final mostra os resultados.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="start-data-warehouse-example"></a>Iniciar data warehouse exemplo

Retome a operação de um banco de dados denominado "Database02" hospedado em um servidor denominado "Server01." O servidor está contido em um grupo de recursos denominado "ResourceGroup1".

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

Uma variação, este exemplo recupera um banco de dados denominado “Database02” de um servidor chamado “Server01” que está contido em um grupo de recursos denominado “ResourceGroup1”. Ele canaliza o objeto recuperado para [resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> Observe que, se o servidor for foo.database.windows.net, use "foo" como o -ServerName nos cmdlets do PowerShell.

## <a name="other-supported-powershell-cmdlets"></a>Outros cmdlets do PowerShell com suporte

Esses cmdlets do PowerShell têm suporte com o Azure Synapse Analytics data warehouse.

* [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Get-AzSqlDatabaseRestorePoint](/powershell/module/az.sql/get-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Restore-AzSqlDatabase]/PowerShell/Module/AZ.SQL/RESTORE-azsqldatabase? TOC =/Azure/Synapse-Analytics/SQL-data-warehouse/TOC.JSON&BC =/Azure/Synapse-Analytics/SQL-data-warehouse/breadcrumb/TOC.JSON)
* [Retomar-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
* [Suspender-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

## <a name="next-steps"></a>Próximas etapas

Para obter mais exemplos do PowerShell, consulte:

* [Criar um data warehouse usando o PowerShell](create-data-warehouse-powershell.md)
* [Restauração do banco de dados](sql-data-warehouse-restore-points.md)

Para outras tarefas que podem ser automatizadas com o PowerShell, consulte [cmdlets do banco de dados SQL do Azure]/PowerShell/Module/AZ.SQL? TOC =/Azure/Synapse-Analytics/SQL-data-warehouse/TOC.JSON&BC =/Azure/Synapse-Analytics/SQL-data-warehouse/breadcrumb/TOC.JSON). Nem todos os cmdlets do banco de dados SQL do Azure têm suporte para o Azure Synapse Analytics data warehouse. Para obter uma lista de tarefas que podem ser automatizadas com o REST, consulte [operações para o banco de dados SQL do Azure](/rest/api/sql/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
