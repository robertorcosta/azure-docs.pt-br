---
title: Restaurar um data warehouse existente
description: Guia de instruções para restaurar um pool existente do SQL.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: bf83de481cf15711da45edf00e63262a05489247
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78199507"
---
# <a name="restore-an-existing-sql-pool"></a>Restaurar um pool SQL existente

Neste artigo, você aprenderá a restaurar um pool SQL existente no Azure Synapse Analytics usando o portal do Azure e o PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Verifique sua capacidade de DTU.** Cada pool é hospedado por um SQL Server (por exemplo, myserver.database.windows.net) que tem uma cota de DTU padrão. Verifique se o SQL Server tem cota de DTU suficiente restante para o banco de dados que está sendo restaurado. Para saber como calcular a DTU necessária ou para solicitar mais DTU, veja [Solicitar uma alteração de cota de DTU](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="before-you-begin"></a>Antes de começar

1. Certifique-se de [instalar Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
2. Ter um ponto de restauração existente do qual você deseja restaurar. Se você quiser criar uma nova restauração, consulte [o tutorial para criar um novo ponto de restauração definido pelo usuário](sql-data-warehouse-restore-points.md).

## <a name="restore-an-existing-sql-pool-through-powershell"></a>Restaurar um pool SQL existente por meio do PowerShell

Para restaurar um pool existente do SQL de um ponto de restauração, use o cmdlet do PowerShell [Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) .

1. Abra o PowerShell.

2. Conecte-se à sua conta do Azure e liste todas as assinaturas associadas à sua conta.

3. Selecione a assinatura que contém o banco de dados a ser restaurado.

4. Liste os pontos de restauração para o pool do SQL.

5. Selecione o ponto de restauração desejado usando o RestorePointCreationDate.

6. Restaure o pool SQL para o ponto de restauração desejado usando o cmdlet do PowerShell [Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) .
        1. Para restaurar o pool SQL para um servidor lógico diferente, certifique-se de especificar o outro nome de servidor lógico.  Esse servidor lógico também pode estar em um grupo de recursos e região diferentes.
        2. Para restaurar para uma assinatura diferente, use o botão ' mover ' para mover o servidor lógico para outra assinatura.

7. Verifique se o pool do SQL restaurado está online.

8. Depois que a restauração for concluída, você poderá configurar o pool do SQL recuperado seguindo [configurar seu banco de dados após a recuperação](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).

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

# Or list all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate "xx/xx/xxxx xx:xx:xx xx"
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Use the following command to restore to a different logical server
#$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceTargetGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

## <a name="restore-an-existing-sql-pool-through-the-azure-portal"></a>Restaurar um pool SQL existente por meio do portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Navegue até o pool SQL do qual você deseja restaurar.
3. Na parte superior da folha Visão Geral, selecione **Restaurar**.

    ![ Visão Geral de Restauração](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. Selecione **Pontos de Restauração Automática** ou **Pontos de Restauração Definido Pelo Usuário**. Se o pool do SQL não tiver pontos de restauração automáticos, aguarde algumas horas ou crie um ponto de restauração definido pelo usuário antes de restaurar. Para pontos de restauração definidos pelo usuário, selecione um existente ou crie um novo. Para o **servidor**, você pode escolher um servidor lógico em um grupo de recursos e uma região diferentes ou criar um novo. Depois de fornecer todos os parâmetros, clique em **revisar + restaurar**.

    ![Pontos de restauração automática](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>Próximas etapas
- [Restaurar um pool do SQL excluído](sql-data-warehouse-restore-deleted-dw.md)
- [Restaurar de um pool SQL de backup geográfico](sql-data-warehouse-restore-from-geo-backup.md)

 