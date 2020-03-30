---
title: PowerShell & REST APIs
description: Encontre os principais cmdlets do PowerShell para o pool SqL do Azure Synapse Analytics, incluindo como pausar e retomar um banco de dados.
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
ms.openlocfilehash: 5f22de08c4eabd3f9a3d6ee29cad3f0a9e8509e0
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351379"
---
# <a name="powershell--rest-apis-for-azure-synapse-analytics-sql-pool"></a>PowerShell & APIs REST para o pool Synapse Analytics Do Azure Synapse

Muitas tarefas administrativas do pool SQL do Azure Synapse Analytics podem ser gerenciadas usando cmdlets Do Azure PowerShell ou APIs REST.  Abaixo estão alguns exemplos de como usar os comandos PowerShell para automatizar tarefas comuns em seu pool SQL.  Para obter alguns bons exemplos de REST, consulte o artigo [Gerenciar escalabilidade com REST](sql-data-warehouse-manage-compute-rest-api.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="get-started-with-azure-powershell-cmdlets"></a>Introdução aos cmdlets do Azure PowerShell

1. Abra o Windows PowerShell.
2. No prompt do PowerShell, execute estes comandos para entrar no Azure Resource Manager e selecione sua assinatura.
   
    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Select-AzSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-data-warehouse-example"></a>Exemplo de data warehouse pausa
Pause um banco de dados denominado "Database02" hospedado em um servidor denominado "Server01."  O servidor está em um grupo de recursos do Azure denominado "ResourceGroup1".

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```

Uma variação, este exemplo, canaliza o objeto recuperado para [Suspend-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase).  Como resultado, o banco de dados é pausado. O comando final mostra os resultados.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="start-data-warehouse-example"></a>Iniciar exemplo de data warehouse

Retome a operação de um banco de dados denominado "Database02" hospedado em um servidor denominado "Server01." O servidor está contido em um grupo de recursos denominado "ResourceGroup1".

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

Uma variação, este exemplo recupera um banco de dados denominado “Database02” de um servidor chamado “Server01” que está contido em um grupo de recursos denominado “ResourceGroup1”. Ele canaliza o objeto recuperado para [Resume-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase).

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
```

> [!NOTE]
> Observe que, se o servidor for foo.database.windows.net, use "foo" como o -ServerName nos cmdlets do PowerShell.
> 
> 

## <a name="other-supported-powershell-cmdlets"></a>Outros cmdlets do PowerShell com suporte
Esses cmdlets powershell são suportados com o data warehouse Azure Synapse Analytics.

* [Get-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabase)
* [Get-AzSqlDeletedDatabaseBackup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup)
* [Get-AzSqlDatabaseRestorePoint](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserestorepoint)
* [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
* [Remove-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabase)
* [Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)
* [Resume-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/resume-azsqldatabase)
* [Assinatura Select-Az](https://msdn.microsoft.com/library/dn722499.aspx)
* [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
* [Banco de dados suspend-AzSql](https://docs.microsoft.com/powershell/module/az.sql/suspend-azsqldatabase)

## <a name="next-steps"></a>Próximas etapas
Para obter mais exemplos do PowerShell, consulte:

* [Crie um data warehouse usando o PowerShell](create-data-warehouse-powershell.md)
* [Restauração do banco de dados](sql-data-warehouse-restore-points.md)

Para outras tarefas que podem ser automatizadas com o PowerShell, consulte [cmdlets do Banco de Dados SQL do Azure](https://docs.microsoft.com/powershell/module/az.sql). Nem todos os cmdlets do Banco de Dados Azure SQL são suportados para o data warehouse Dozure Synapse Analytics.  Para obter uma lista de tarefas que podem ser automatizadas com rest, consulte [Operations for Azure SQL Database](/rest/api/sql/).
