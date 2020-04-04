---
title: Restaurar um data warehouse a partir de um geo-backup
description: Guia de como restaurar georestauração de uma piscina SQL.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 07/12/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 68d53d2a33b7ab705dfa88f03618a5d5a3d1bced
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633037"
---
# <a name="geo-restore-for-sql-pool"></a>Geo-restauração para pool SQL

Neste artigo, você aprende a restaurar seu pool SQL a partir de um geobackup através do portal Azure e do PowerShell.

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Verifique sua capacidade de DTU.** Cada pool SQL é hospedado por um servidor SQL (por exemplo, myserver.database.windows.net) que tem uma cota De DTU padrão. Verifique se o servidor SQL tem cota DTU suficiente para o banco de dados que está sendo restaurado. Para saber como calcular a DTU necessária ou para solicitar mais DTU, veja [Solicitar uma alteração de cota de DTU](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>Restaurar de uma região geográfica do Azure através do PowerShell

Para restaurar a partir de um geo-backup, use o [cmdlet Get-AzSqlDatabaseGeoBackup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasegeobackup) e [Restore-AzSqlDatabase.](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)

> [!NOTE]
> Você pode executar uma restauração geográfica para Gen2! Para fazer isso, especifique um ServiceObjectiveName de Gen2 (por exemplo, DW1000**c**) como parâmetro opcional.
>

1. Antes de começar, certifique-se [de instalar o Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
2. Abra o PowerShell.
3. Conecte-se à sua conta do Azure e liste todas as assinaturas associadas à sua conta.
4. Selecione a assinatura que contém o data warehouse a ser restaurado.
5. Pegue o data warehouse que deseja recuperar.
6. Crie a solicitação de recuperação para o data warehouse.
7. Verifique o status do data warehouse geo-restaurado.
8. Para configurar o data warehouse após a conclusão da restauração, veja [Configurar o banco de dados após a recuperação]( ../../sql-database/sql-database-disaster-recovery.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery).

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$TargetResourceGroupName="<YourTargetResourceGroupName>" # Restore to a different logical server.
$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"  
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"
$TargetServiceObjective="<YourTargetServiceObjective-DWXXXc>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName
Get-AzureSqlDatabase -ServerName $ServerName

# Get the data warehouse you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Recover data warehouse
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName $TargetServiceObjective

# Verify that the geo-restored data warehouse is online
$GeoRestoredDatabase.status
```

O banco de dados recuperado será habilitado para TDE se o banco de dados de origem for habilitado para TDE.

## <a name="restore-from-an-azure-geographical-region-through-azure-portal"></a>Restaurar de uma região geográfica do Azure através do portal Azure

Siga as etapas descritas abaixo para restaurar um pool SQL a partir de um geo-backup:

1. Faça login na sua conta [do portal Azure.](https://portal.azure.com/)
2. Clique em **+ Criar um recurso**.

   ![Novo DW](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)

3. Clique **em Bancos de Dados** e, em seguida, **Azure Synapse Analytics (anteriormente SQL DW) **.

   ![Novo DW 2](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new-02.png)

4. Preencha as informações solicitadas na guia **Noções básicas** e clique em **Seguir: Configurações adicionais**.

   ![Noções básicas](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)

5. Para **Usar parâmetro de dados existente,** selecione **Backup** e selecione o backup apropriado nas opções de rolagem para baixo. Clique **em Revisar + Criar**.

   ![backup](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)

6. Uma vez restaurado o data warehouse, verifique se o **Status** está on-line.

## <a name="next-steps"></a>Próximas etapas

- [Restaurar um pool SQL existente](sql-data-warehouse-restore-active-paused-dw.md)
- [Restaurar um pool SQL excluído](sql-data-warehouse-restore-deleted-dw.md)
