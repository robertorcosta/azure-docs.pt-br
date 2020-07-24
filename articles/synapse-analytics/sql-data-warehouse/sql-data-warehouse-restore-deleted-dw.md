---
title: Restaurar um pool do SQL excluído
description: Guia de instruções para restaurar um pool de SQL excluído.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: fc21dc67dd6c686eedf307660a8d8b7afd60db71
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075844"
---
# <a name="restore-a-deleted-sql-pool-using-azure-synapse-analytics"></a>Restaurar um pool do SQL excluído usando o Azure Synapse Analytics

Neste artigo, você aprenderá a restaurar um SQL usando o portal do Azure ou o PowerShell.

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Verifique sua capacidade de DTU.** Cada pool do SQL é hospedado por um [SQL Server lógico](../../azure-sql/database/logical-servers.md) (por exemplo, MyServer.Database.Windows.net) que tem uma cota de DTU padrão.  Verifique se o servidor tem a cota de DTU suficiente restante para o banco de dados que está sendo restaurado. Para saber como calcular a DTU necessária ou para solicitar mais DTU, veja [Solicitar uma alteração de cota de DTU](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-a-deleted-data-warehouse-through-powershell"></a>Restaurar um data warehouse excluído por meio do PowerShell

Para restaurar um pool do SQL excluído, use o cmdlet [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) . Se o servidor correspondente também tiver sido excluído, você não poderá restaurar esse data warehouse.

1. Antes de começar, certifique-se de [instalar Azure PowerShell](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Abra o PowerShell.
3. Conecte-se à sua conta do Azure e liste todas as assinaturas associadas à sua conta.
4. Selecione a assinatura que contém o pool de SQL excluído a ser restaurado.
5. Obtenha o data warehouse excluído específico.
6. Restaurar o pool de SQL excluído
    1. Para restaurar o pool do SQL excluído para um servidor diferente, certifique-se de especificar o nome do outro servidor.  Esse servidor também pode estar em um grupo de recursos e região diferentes.
    1. Para restaurar para uma assinatura diferente, use o botão [mover](../../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#use-the-portal) para mover o servidor para outra assinatura.
7. Verifique se o data warehouse restaurado está online.
8. Depois que a restauração for concluída, você poderá configurar o data warehouse recuperado seguindo [configurar seu banco de dados após a recuperação](../../azure-sql/database/disaster-recovery-guidance.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery).

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
#$TargetResourceGroupName="<YourTargetResourceGroupName>" # uncomment to restore to a different server.
#$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Use the following command to restore deleted data warehouse to a different server
#$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

## <a name="restore-a-deleted-database-using-the-azure-portal"></a>Restaurar um banco de dados excluído usando o portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Navegue até o servidor no qual o data warehouse excluído foi hospedado.
3. Selecione o ícone **bancos de dados excluídos** no sumário.

    ![Bancos de dados excluídos](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-01.png)

4. Selecione o SQL Data Warehouse excluído que você deseja restaurar.

    ![Selecione Bancos de Dados Excluídos](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-11.png)

5. Especifique um novo **nome de banco de dados** e clique em **OK**

    ![Especifique o Nome do Banco de Dados](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-21.png)

## <a name="next-steps"></a>Próximas etapas

- [Restaurar um pool SQL existente](sql-data-warehouse-restore-active-paused-dw.md)
- [Restaurar de um pool SQL de backup geográfico](sql-data-warehouse-restore-from-geo-backup.md)
