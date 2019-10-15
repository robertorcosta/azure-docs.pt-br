---
title: Script do Azure PowerShell – Atualizar uma conta do Azure Cosmos
description: Amostra de script do Azure PowerShell – Atualizar uma conta do Azure Cosmos ou modificar regiões
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 09/20/2019
ms.author: mjbrown
ms.openlocfilehash: ec160f03fa5a8b9415dc33885fca512d4289f37b
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71969702"
---
# <a name="update-an-azure-cosmos-account-or-modify-regions-using-powershell"></a>Atualizar uma conta do Azure Cosmos ou modificar regiões usando o PowerShell

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Script de exemplo

> [!NOTE]
> Não é possível modificar regiões e alterar outras propriedades da conta do Cosmos na mesma operação. Isso precisa ser feito como duas operações separadas.
> [!NOTE]
> Esta amostra descreve como usar uma conta da API do SQL (Core). Para usar essa amostra em outras APIs, copie as propriedades relacionadas e aplique-as ao script específico da API.

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/common/ps-account-update.ps1 "Add a region to an Azure Cosmos account")]

## <a name="clean-up-deployment"></a>Limpar a implantação

Após a execução do script de exemplo, o comando a seguir pode ser usado para remover o grupo de recursos e todos os recursos associados a ele.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
|**Recursos do Azure**| |
| [New-AzResource](https://docs.microsoft.com/powershell/module/az.resources/new-azresource) | Cria um recurso. |
| [Set-AzResource](https://docs.microsoft.com/powershell/module/az.resources/set-azresource) | Atualiza um recurso. |
|**Grupos de recursos do Azure**| |
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira a [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Exemplos adicionais de scripts do PowerShell do Banco de Dados Cosmos do Azure podem ser encontrados nos [Scripts do PowerShell do Banco de Dados Cosmos do Azure](../../../powershell-samples.md).
