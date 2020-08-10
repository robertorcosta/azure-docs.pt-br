---
title: Exemplo de script do Azure PowerShell – Emparelhar duas redes virtuais | Microsoft Docs
description: Crie e conecte duas redes virtuais na mesma região. Use o script do Azure para duas redes virtuais de mesmo nível para conectar as redes por meio do Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/16/2017
ms.author: kumud
ms.openlocfilehash: 17224e859dce99a20cd51c2fe9024759c5239c03
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88037228"
---
# <a name="peer-two-virtual-networks"></a>Emparelhar duas redes virtuais

Este script cria e conecta duas redes virtuais na mesma região através da rede do Azure. Depois de executar o script, você criará um emparelhamento entre duas redes virtuais.

Se necessário, instale o Azure PowerShell usando a instrução encontrada no [guia do Azure PowerShell](https://docs.microsoft.com/powershell/azure/) e, em seguida, execute `Connect-AzAccount` para criar uma conexão com o Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemplo de script

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell[main](../../../powershell_scripts/virtual-network/peer-two-virtual-networks/peer-two-virtual-networks.ps1 "Peer two networks")]

## <a name="clean-up-deployment"></a>Limpar a implantação 

Execute o comando a seguir para remover o grupo de recursos, a VM e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir para criar um grupo de recursos, uma máquina virtual e todos os recursos relacionados. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. | 
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)| Cria uma sub-rede e uma rede virtual do Azure. |
| [Add-AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering) | Cria um emparelhamento entre duas redes virtuais.  |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira a [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/azure/).

Exemplos adicionais de script de PowerShell de rede podem ser encontrados na [Documentação de visão geral da rede do Azure](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).
