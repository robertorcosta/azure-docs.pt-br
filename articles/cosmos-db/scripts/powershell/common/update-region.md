---
title: Script do PowerShell para atualizar as regiões da conta do Azure Cosmos
description: Amostra de script do Azure PowerShell – Atualizar as regiões da conta do Azure Cosmos
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/01/2020
ms.author: mjbrown
ms.openlocfilehash: 496e9ba375e1c83b0e5c06d5bd746ea7e02b3b14
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93099475"
---
# <a name="update-an-azure-cosmos-accounts-regions-using-powershell"></a>Atualizar as regiões da conta do Azure Cosmos usando o PowerShell
[!INCLUDE[appliesto-all-apis](../../../includes/appliesto-all-apis.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Exemplo de script

> [!NOTE]
> Não é possível modificar regiões e alterar outras propriedades da conta do Cosmos na mesma operação. Isso precisa ser feito como duas operações separadas.
> [!NOTE]
> Esta amostra descreve como usar uma conta da API do SQL (Core). Para usar essa amostra em outras APIs, copie as propriedades relacionadas e aplique-as ao script específico da API.

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/common/ps-account-update-region.ps1 "Update Azure Cosmos account regions")]

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
| [New-AzCosmosDBLocationObject](/powershell/module/az.cosmosdb/new-azcosmosdblocationobject) | Cria um objeto do tipo PSLocation para ser usado como um parâmetro para Update-AzCosmosDBAccountRegion. |
| [Update-AzCosmosDBAccountRegion](/powershell/module/az.cosmosdb/update-azcosmosdbaccountregion) | Atualize as regiões de uma conta do Cosmos DB. |
|**Grupos de recursos do Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira a [Documentação do Azure PowerShell](/powershell/).