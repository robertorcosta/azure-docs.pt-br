---
title: Script do PowerShell para listar e obter recursos da API do SQL do Azure Cosmos DB
description: Script do Azure PowerShell – Listar e obter operações do Azure Cosmos DB para a API do SQL
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 03/17/2020
ms.author: mjbrown
ms.openlocfilehash: 030cca261066025c8f2ae6b2254f40dce9c9b39d
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93074515"
---
# <a name="list-and-get-databases-and-containers-for-azure-cosmos-db---sql-core-api"></a>Listar e obter bancos de dados e contêineres do Azure Cosmos DB – API do SQL (Core)
[!INCLUDE[appliesto-sql-api](../../../includes/appliesto-sql-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Exemplo de script

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-list-get.ps1 "List and get databases and containers for SQL API")]

## <a name="clean-up-deployment"></a>Limpar a implantação

Após a execução do script de exemplo, o comando a seguir pode ser usado para remover o grupo de recursos e todos os recursos associados a ele.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
|**Azure Cosmos DB**| |
| [Get-AzCosmosDBAccount](/powershell/module/az.cosmosdb/get-azcosmosdbaccount) | Lista contas do Cosmos DB ou obtém uma conta do Cosmos DB especificada. |
| [Get-AzCosmosDBSqlDatabase](/powershell/module/az.cosmosdb/get-azcosmosdbsqldatabase) | Lista Bancos de Dados do Cosmos DB em uma Conta ou obtém um Banco de Dados do Cosmos DB especificado em uma conta. |
| [Get-AzCosmosDBSqlContainer](/powershell/module/az.cosmosdb/get-azcosmosdbsqlcontainer) | Lista Contêineres do Cosmos DB em um Banco de Dados ou obtém um Contêiner do Cosmos DB especificado em um Banco de Dados. |
|**Grupos de recursos do Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira a [Documentação do Azure PowerShell](/powershell/).