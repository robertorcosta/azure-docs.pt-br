---
title: APIs REST do PowerShell &
description: Encontre os principais cmdlets do PowerShell para o pool do SQL do Azure Synapse Analytics, incluindo como pausar e retomar um banco de dados.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c0c8b1e9b7526bd45d037f053715613b53ec163f
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78198449"
---
# <a name="powershell--rest-apis-for-azure-synapse-analytics-sql-pool"></a>PowerShell & APIs REST para o pool SQL do Azure Synapse Analytics

Muitas tarefas administrativas do pool SQL do Azure Synapse Analytics podem ser gerenciadas usando cmdlets Azure PowerShell ou APIs REST.  Abaixo estão alguns exemplos de como usar comandos do PowerShell para automatizar tarefas comuns em seu pool do SQL.  Para obter alguns bons exemplos de REST, consulte o artigo [Gerenciar escalabilidade com REST](sql-data-warehouse-manage-compute-rest-api.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

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

Uma variação, este exemplo canaliza o objeto recuperado para [Suspend-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase).  Como resultado, o banco de dados é pausado. O comando final mostra os resultados.

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

Uma variação, este exemplo recupera um banco de dados denominado “Database02” de um servidor chamado “Server01” que está contido em um grupo de recursos denominado “ResourceGroup1”. Ele canaliza o objeto recuperado para [resume-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase).

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> Observe que, se o servidor for foo.database.windows.net, use "foo" como o -ServerName nos cmdlets do PowerShell.
> 
> 

## <a name="other-supported-powershell-cmdlets"></a>Outros cmdlets do PowerShell com suporte
Esses cmdlets do PowerShell têm suporte com o Azure Synapse Analytics data warehouse.

* [Get-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase)
* [Get-AzSqlDeletedDatabaseBackup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup)
* [Get-AzSqlDatabaseRestorePoint](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserestorepoint)
* [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
* [Remove-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabase)
* [Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)
* [Retomar-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase)
* [Select-AzSubscription](https://msdn.microsoft.com/library/dn722499.aspx)
* [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
* [Suspender-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase)

## <a name="next-steps"></a>Próximas etapas
Para obter mais exemplos do PowerShell, consulte:

* [Criar um data warehouse usando o PowerShell](create-data-warehouse-powershell.md)
* [Restauração do banco de dados](sql-data-warehouse-restore-database-powershell.md)

Para outras tarefas que podem ser automatizadas com o PowerShell, consulte [cmdlets do banco de dados SQL do Azure](https://docs.microsoft.com/powershell/module/az.sql). Nem todos os cmdlets do banco de dados SQL do Azure têm suporte para o Azure Synapse Analytics data warehouse.  Para obter uma lista de tarefas que podem ser automatizadas com o REST, consulte [operações para o banco de dados SQL do Azure](/rest/api/sql/).
