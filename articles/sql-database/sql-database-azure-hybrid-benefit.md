---
title: Benefício Híbrido do Azure
description: Use as licenças existentes do SQL Server para descontos no Banco de Dados SQL.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/13/2019
ms.openlocfilehash: d1a59e7ad86191bcc30b7d898d00f327c20fbc5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75945615"
---
# <a name="azure-hybrid-benefit"></a>Benefício Híbrido do Azure

No nível de computação provisionado do modelo de compras baseado em vCore, você pode trocar suas licenças existentes por taxas com desconto no Banco de Dados SQL usando [o Azure Hybrid Benefit para SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Este benefício do Azure permite que você economize até 30% ou até mais no Banco de Dados SQL do Azure usando suas licenças sql server no local com garantia de software. Por favor, use a Calculadora de Benefícios Híbridos Do Azure usando o link mencionado anteriormente para valores corretos. 

> [!NOTE]
> A alteração para O Benefício Híbrido Do Azure não requer qualquer tempo de inatividade.

![preços](./media/sql-database-service-tiers/pricing.png)

## <a name="choose-a-license-model"></a>Escolha um modelo de licença

Com o Azure Hybrid Benefit, você pode optar por pagar apenas pela infra-estrutura azure subjacente usando sua licença sql server existente para o próprio mecanismo de banco de dados SQL (preços de Base Compute), ou você pode pagar tanto pela infra-estrutura subjacente quanto pelo SQL Server licença (preços incluídos na licença).

Você pode escolher ou alterar seu modelo de licenciamento usando o portal Azure ou usando uma das seguintes APIs:

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Para definir ou atualizar o tipo de licença usando o PowerShell:

- [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)
- [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)
- [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)
- [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Para definir ou atualizar o tipo de licença usando o Azure CLI:

- [az sql db create](/cli/azure/sql/db#az-sql-db-create)
- [az sql db update](/cli/azure/sql/db#az-sql-db-update)
- [az sql mi create](/cli/azure/sql/mi#az-sql-mi-create)
- [az sql mi update](/cli/azure/sql/mi#az-sql-mi-update)

# <a name="rest-api"></a>[REST API](#tab/rest)

Para definir ou atualizar o tipo de licença usando a API REST:

- [Banco de Dados – Criar ou Atualizar](/rest/api/sql/databases/createorupdate)
- [Bancos de Dados – Atualizar](/rest/api/sql/databases/update)
- [Managed Instances - Create Or Update](/rest/api/sql/managedinstances/createorupdate)
- [Managed Instances - Update](/rest/api/sql/managedinstances/update)

* * *

## <a name="next-steps"></a>Próximas etapas

- Para escolher entre as opções de implantação do Banco de Dados SQL, consulte [Escolha a opção de implantação certa no Azure SQL](sql-database-paas-vs-sql-server-iaas.md).
- Para obter uma comparação dos recursos do Banco de Dados SQL, consulte [Azure SQL Database Features](sql-database-features.md).
