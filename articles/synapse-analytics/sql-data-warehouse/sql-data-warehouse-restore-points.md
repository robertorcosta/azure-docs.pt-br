---
title: Pontos de restauração definidos pelo usuário
description: Como criar um ponto de restauração para pool SQL.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 07/03/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: d056b30c44ced5f3e8ce9041e2366290bee485da
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350140"
---
# <a name="user-defined-restore-points"></a>Pontos de restauração definidos pelo usuário

Neste artigo, você aprenderá a criar um novo ponto de restauração definido pelo usuário para um pool SQL no Azure Synapse Analytics usando o PowerShell e o portal Azure.

## <a name="create-user-defined-restore-points-through-powershell"></a>Crie pontos de restauração definidos pelo usuário através do PowerShell

Para criar um ponto de restauração definido pelo usuário, use o cmdlet [PowerShell do New-AzSqlDatabaseRestorePoint.](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint?view=azps-2.4.0)

1. Antes de começar, certifique-se [de instalar o Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
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

6. Veja a lista de todos os pontos de restauração existentes.

```Powershell
# List all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName
```

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Crie pontos de restauração definidos pelo usuário através do portal Azure

Os pontos de restauração definidos pelo usuário também podem ser criados através do portal Azure.

1. Faça login na sua conta [do portal Azure.](https://portal.azure.com/)

2. Navegue até o pool SQL para o que você deseja criar um ponto de restauração.

3. Selecione **Visão geral** no painel esquerdo, selecione **+ Novo ponto de restauração**. Se o botão Novo Ponto de Restauração não estiver habilitado, certifique-se de que o pool SQL não esteja pausado.

    ![Novo Ponto de Restauração](./media/sql-data-warehouse-restore-points/creating-restore-point-01.png)

4. Especifique um nome para o ponto de restauração definido pelo usuário e clique **em Aplicar**. Os pontos de restauração definidos pelo usuário têm um período de retenção padrão de sete dias.

    ![Nome do ponto de restauração](./media/sql-data-warehouse-restore-points/creating-restore-point-11.png)

## <a name="next-steps"></a>Próximas etapas

- [Restaurar um pool SQL existente](sql-data-warehouse-restore-active-paused-dw.md)
- [Restaurar um pool SQL excluído](sql-data-warehouse-restore-deleted-dw.md)
- [Restaurar a partir de um pool SQL de backup geográfico](sql-data-warehouse-restore-from-geo-backup.md)

