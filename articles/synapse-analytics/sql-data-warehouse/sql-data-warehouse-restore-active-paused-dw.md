---
title: Restaurar um data warehouse existente
description: Guia de como restaurar um pool SQL existente.
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
ms.openlocfilehash: 635b65d16ae9a59816506023d323243f043ce7da
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350384"
---
# <a name="restore-an-existing-sql-pool"></a>Restaurar um pool SQL existente

Neste artigo, você aprende como restaurar um pool SQL existente no Azure Synapse Analytics usando o portal Azure e o PowerShell.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Verifique sua capacidade de DTU.** Cada pool é hospedado por um servidor SQL (por exemplo, myserver.database.windows.net) que tem uma cota de DTU padrão. Verifique se o servidor SQL tem cota DTU suficiente para o banco de dados que está sendo restaurado. Para saber como calcular a DTU necessária ou para solicitar mais DTU, veja [Solicitar uma alteração de cota de DTU](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="before-you-begin"></a>Antes de começar

1. Certifique-se de [instalar o Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
2. Tenha um ponto de restauração existente do que você deseja restaurar. Se você quiser criar uma nova restauração, consulte [o tutorial para criar um novo ponto de restauração definido pelo usuário](sql-data-warehouse-restore-points.md).

## <a name="restore-an-existing-sql-pool-through-powershell"></a>Restaurar um pool SQL existente através do PowerShell

Para restaurar um pool SQL existente a partir de um ponto de restauração, use o [cmdlet Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) PowerShell.

1. Abra o PowerShell.

2. Conecte-se à sua conta do Azure e liste todas as assinaturas associadas à sua conta.

3. Selecione a assinatura que contém o banco de dados a ser restaurado.

4. Liste os pontos de restauração para o pool SQL.

5. Selecione o ponto de restauração desejado usando o RestorePointCreationDate.

6. Restaurar o pool SQL ao ponto de restauração desejado usando o cmdlet [Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) PowerShell.
        1. Para restaurar o pool SQL para um servidor lógico diferente, certifique-se de especificar o outro nome lógico do servidor.  Este servidor lógico também pode estar em um grupo de recursos diferente e região.
        2. Para restaurar uma assinatura diferente, use o botão 'Mover' para mover o servidor lógico para outra assinatura.

7. Verifique se o pool SQL restaurado está on-line.

8. Depois que a restauração estiver concluída, você poderá configurar o pool SQL recuperado seguindo [a configuração do banco de dados após a recuperação](../../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).

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

## <a name="restore-an-existing-sql-pool-through-the-azure-portal"></a>Restaurar um pool SQL existente através do portal Azure

1. Faça login no [portal Azure](https://portal.azure.com/).
2. Navegue até o pool SQL do que deseja restaurar.
3. Na parte superior da folha Visão Geral, selecione **Restaurar**.

    ![ Visão Geral de Restauração](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. Selecione **Pontos de Restauração Automática** ou **Pontos de Restauração Definido Pelo Usuário**. Se o pool SQL não tiver nenhum ponto de restauração automático, espere algumas horas ou crie um ponto de restauração definido pelo usuário antes de restaurar. Para pontos de restauração definidos pelo usuário, selecione um existente ou crie um novo. Para **server,** você pode escolher um servidor lógico em um grupo e região de recursos diferentes ou criar um novo. Depois de fornecer todos os parâmetros, clique em **Revisar + Restaurar**.

    ![Pontos de restauração automática](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>Próximas etapas
- [Restaurar um pool SQL excluído](sql-data-warehouse-restore-deleted-dw.md)
- [Restaurar a partir de um pool SQL de backup geográfico](sql-data-warehouse-restore-from-geo-backup.md)

 