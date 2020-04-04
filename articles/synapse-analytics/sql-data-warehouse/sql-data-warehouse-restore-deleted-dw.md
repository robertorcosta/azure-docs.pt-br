---
title: Restaurar um pool SQL excluído
description: Como orientar para restaurar um pool SQL excluído.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 251fdb83e848aaac3a5391320df23149ce1bce33
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633058"
---
# <a name="restore-a-deleted-sql-pool-using-azure-synapse-analytics"></a>Restaurar um pool SQL excluído usando o Azure Synapse Analytics

Neste artigo, você aprende a restaurar um SQL usando o portal Azure ou o PowerShell.

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Verifique sua capacidade de DTU.** Cada pool SQL é hospedado por um servidor SQL (por exemplo, myserver.database.windows.net) que tem uma cota De DTU padrão.  Verifique se o servidor SQL tem cota DTU suficiente para o banco de dados que está sendo restaurado. Para saber como calcular a DTU necessária ou para solicitar mais DTU, veja [Solicitar uma alteração de cota de DTU](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-a-deleted-data-warehouse-through-powershell"></a>Restaurar um data warehouse excluído através do PowerShell

Para restaurar um pool SQL excluído, use o [cmdlet Restore-AzSqlDatabase.](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) Se o servidor lógico correspondente também foi excluído, você não poderá restaurar esse data warehouse.

1. Antes de começar, certifique-se [de instalar o Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
2. Abra o PowerShell.
3. Conecte-se à sua conta do Azure e liste todas as assinaturas associadas à sua conta.
4. Selecione a assinatura que contém o data warehouse excluído a ser restaurado.
5. Obtenha o data warehouse apagado específico.
6. Restaurar o data warehouse excluído
    1. Para restaurar o SQL Data Warehouse excluído para um servidor lógico diferente, certifique-se de especificar o outro nome lógico do servidor.  Este servidor lógico também pode estar em um grupo de recursos diferente e região.
    1. Para restaurar uma assinatura diferente, use o botão [Mover](../../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#use-the-portal) para mover o servidor lógico para outra assinatura.
7. Verifique se o data warehouse restaurado está on-line.
8. Depois que a restauração estiver concluída, você pode configurar seu data warehouse recuperado seguindo [configurar seu banco de dados após a recuperação](../../sql-database/sql-database-disaster-recovery.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery).

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
#$TargetResourceGroupName="<YourTargetResourceGroupName>" # uncomment to restore to a different logical server.
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

# Use the following command to restore deleted data warehouse to a different logical server
#$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

## <a name="restore-a-deleted-database-using-the-azure-portal"></a>Restaurar um banco de dados excluído usando o portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Navegue até o servidor SQL em que o data warehouse excluído estava hospedado.
3. Selecione o ícone **de bancos de dados excluídos** na tabela de conteúdo.

    ![Bancos de dados excluídos](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-01.png)

4. Selecione o SQL Data Warehouse excluído que deseja restaurar.

    ![Selecione Bancos de Dados Excluídos](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-11.png)

5. Especifique um novo **nome do banco de dados** e clique em **OK**

    ![Especifique o Nome do Banco de Dados](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-21.png)

## <a name="next-steps"></a>Próximas etapas

- [Restaurar um pool SQL existente](sql-data-warehouse-restore-active-paused-dw.md)
- [Restaurar a partir de um pool SQL de backup geográfico](sql-data-warehouse-restore-from-geo-backup.md)
