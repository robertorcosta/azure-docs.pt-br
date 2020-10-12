---
title: Pontos de restauração definidos pelo usuário
description: Como criar um ponto de restauração para o pool do SQL.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 07/03/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 66a2dad9396e8bf7c8ef49db529f7a5486cc8a8f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87089200"
---
# <a name="user-defined-restore-points"></a>Pontos de restauração definidos pelo usuário

Neste artigo, você aprenderá a criar um novo ponto de restauração definido pelo usuário para um pool do SQL no Azure Synapse Analytics usando o PowerShell e o portal do Azure.

## <a name="create-user-defined-restore-points-through-powershell"></a>Criar pontos de restauração definidos pelo usuário por meio do PowerShell

Para criar um ponto de restauração definido pelo usuário, use o cmdlet [New-AzSqlDatabaseRestorePoint](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) do PowerShell.

1. Antes de começar, certifique-se de [instalar Azure PowerShell](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).
2. Abra o PowerShell.
3. Conecte-se à sua conta do Azure e liste todas as assinaturas associadas à sua conta.
4. Selecione a assinatura que contém o banco de dados a ser restaurado.
5. Crie um ponto de restauração para uma cópia imediata do seu data warehouse.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$Label = "<YourRestorePointLabel>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Create a restore point of the original database
New-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -RestorePointLabel $Label

```

6. Consulte a lista de todos os pontos de restauração existentes.

```Powershell
# List all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName
```

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Criar pontos de restauração definidos pelo usuário por meio do portal do Azure

Os pontos de restauração definidos pelo usuário também podem ser criados por meio de portal do Azure.

1. Entre em sua conta do [portal do Azure](https://portal.azure.com/) .

2. Navegue até o pool SQL para o qual você deseja criar um ponto de restauração.

3. Selecione **visão geral** no painel esquerdo, selecione **+ novo ponto de restauração**. Se o botão novo ponto de restauração não estiver habilitado, verifique se o pool do SQL não está pausado.

    ![Novo Ponto de Restauração](./media/sql-data-warehouse-restore-points/creating-restore-point-01.png)

4. Especifique um nome para o ponto de restauração definido pelo usuário e clique em **aplicar**. Os pontos de restauração definidos pelo usuário têm um período de retenção padrão de sete dias.

    ![Nome do ponto de restauração](./media/sql-data-warehouse-restore-points/creating-restore-point-11.png)

## <a name="next-steps"></a>Próximas etapas

- [Restaurar um pool SQL existente](sql-data-warehouse-restore-active-paused-dw.md)
- [Restaurar um pool do SQL excluído](sql-data-warehouse-restore-deleted-dw.md)
- [Restaurar de um pool SQL de backup geográfico](sql-data-warehouse-restore-from-geo-backup.md)

