---
author: lucygoldbergmicrosoft
ms.service: data-explorer
ms.topic: include
ms.date: 11/28/2019
ms.author: lugoldbe
ms.openlocfilehash: 9598724db630db7fc48545b58f2b0c1cad464aa4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74667841"
---
## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implantados excluindo o grupo de recursos. 

### <a name="clean-up-resources-using-the-azure-portal"></a>Limpar recursos usando o portal Azure

Exclua os recursos no portal Azure seguindo as etapas de [limpeza de recursos](../articles/data-explorer/create-cluster-database-portal.md#clean-up-resources).

### <a name="clean-up-resources-using-powershell"></a>Limpar recursos usando o PowerShell

Se o Cloud Shell ainda estiver aberto, você não precisará copiar/executar a primeira linha (Read-Host).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```