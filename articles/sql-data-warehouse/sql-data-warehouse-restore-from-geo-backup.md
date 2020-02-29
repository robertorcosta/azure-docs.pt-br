---
title: Restaurar um data warehouse de um backup geográfico
description: Guia de instruções para a restauração geográfica de um pool SQL.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 07/12/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 624c6665e70802907be8a41015b78d36cca7df1c
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78198316"
---
# <a name="geo-restore-for-sql-pool"></a>Restauração geográfica para o pool SQL

Neste artigo, você aprende a restaurar o pool SQL de um backup geográfico por meio do portal do Azure e do PowerShell.

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Verifique sua capacidade de DTU.** Cada pool do SQL é hospedado por um SQL Server (por exemplo, myserver.database.windows.net) que tem uma cota de DTU padrão. Verifique se o SQL Server tem uma cota de DTU suficiente restante para o banco de dados que está sendo restaurado. Para saber como calcular a DTU necessária ou para solicitar mais DTU, veja [Solicitar uma alteração de cota de DTU](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>Restaurar de uma região geográfica do Azure por meio do PowerShell

Para restaurar de um backup geográfico, use o cmdlet [Get-AzSqlDatabaseGeoBackup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasegeobackup) e [Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) .

> [!NOTE]
> Você pode executar uma restauração geográfica para Gen2! Para fazer isso, especifique um ServiceObjectiveName de Gen2 (por exemplo, DW1000**c**) como parâmetro opcional.
>

1. Antes de começar, certifique-se de [instalar Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
2. Abra o PowerShell.
2. Conecte-se à sua conta do Azure e liste todas as assinaturas associadas à sua conta.
3. Selecione a assinatura que contém a data warehouse a ser restaurada.
4. Obtenha o data warehouse que você deseja recuperar.
5. Crie a solicitação de recuperação para o data warehouse.
6. Verifique o status do data warehouse restaurado geograficamente.
7. Para configurar o data warehouse após a conclusão da restauração, veja [Configurar o banco de dados após a recuperação]( ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).

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

## <a name="restore-from-an-azure-geographical-region-through-azure-portal"></a>Restaurar de uma região geográfica do Azure por meio de portal do Azure

Siga as etapas descritas abaixo para restaurar um pool SQL de um backup geográfico:

1. Entre em sua conta do [portal do Azure](https://portal.azure.com/) .
1. Clique em **+ Criar um recurso**. 

![Novo DW](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)

3. Clique em **bancos de dados** e * * Azure Synapse Analytics (anteriormente conhecido como SQL DW) * *.

![Novo DW 2](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new-02.png)

4. Preencha as informações solicitadas na guia **noções básicas** e clique em **Avançar: configurações adicionais**.

![Noções básicas](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)

5. Para usar o parâmetro de **dados existente** , selecione **backup** e selecione o backup apropriado nas opções de rolagem para baixo. Clique em **revisar + criar**.
 
![backup](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)

6. Depois que o data warehouse tiver sido restaurado, verifique se o **status** está online.

## <a name="next-steps"></a>Próximas etapas
- [Restaurar um pool SQL existente](sql-data-warehouse-restore-active-paused-dw.md)
- [Restaurar um pool do SQL excluído](sql-data-warehouse-restore-deleted-dw.md)