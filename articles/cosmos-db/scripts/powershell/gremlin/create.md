---
title: Script do PowerShell para criar um banco de dados e um grafo da API do Gremlin do Azure Cosmos DB
description: Script do Azure PowerShell – Criar um banco de dados e um grafo do Azure Cosmos DB para a API do Gremlin
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 05/13/2020
ms.author: mjbrown
ms.openlocfilehash: 38ed741401740c60e9bdbe0869caa0f8a6a3c76d
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93099293"
---
# <a name="create-a-database-and-graph-for-azure-cosmos-db---gremlin-api"></a>Criar um banco de dados e um grafo do Azure Cosmos DB – API do Gremlin
[!INCLUDE[appliesto-gremlin-api](../../../includes/appliesto-gremlin-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Exemplo de script

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/gremlin/ps-gremlin-create.ps1 "Create a database and graph for Gremlin API")]

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
| [New-AzCosmosDBAccount](/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | Cria uma Conta do Cosmos DB. |
| [New-AzCosmosDBGremlinDatabase](/powershell/module/az.cosmosdb/new-azcosmosdbgremlindatabase) | Cria um Banco de Dados da API do Gremlin. |
| [New-AzCosmosDBGremlinConflictResolutionPolicy](/powershell/module/az.cosmosdb/new-azcosmosdbgremlinconflictresolutionpolicy) | Cria uma Política de Resolução de Conflito de Gravação da API do Gremlin. |
| [New-AzCosmosDBGremlinGraph](/powershell/module/az.cosmosdb/new-azcosmosdbgremlingraph) | Cria um Grafo da API do Gremlin. |
|**Grupos de recursos do Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira a [Documentação do Azure PowerShell](/powershell/).