---
title: Criar VM com base em instantâneo (Linux) – exemplo do PowerShell
description: Exemplo de script do Azure PowerShell – Criar uma VM de um instantâneo
services: virtual-machines-linux
documentationcenter: virtual-machines
author: ramankumarlive
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 42c9903b783df4b3392b6aab3a69bbb66da90a75
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078155"
---
# <a name="create-a-virtual-machine-from-a-snapshot-with-powershell-linux"></a>Criar uma máquina virtual de um instantâneo com o PowerShell (Linux)

Esse script cria uma máquina virtual de um instantâneo de um disco do sistema operacional.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Exemplo de script

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.ps1 "Create VM from managed os disk")]

## <a name="clean-up-deployment"></a>Limpar a implantação

Execute o comando a seguir para remover o grupo de recursos, a VM e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos para obter as propriedades de instantâneo, criar um disco gerenciado de um instantâneo e criar uma VM. Cada item em que a tabela contém links para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [Get-AzSnapshot](/powershell/module/az.compute/get-azsnapshot) | Obtém um instantâneo usando o nome do instantâneo. |
| [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) | Cria uma configuração de disco. Essa configuração é usada com o processo de criação de disco. |
| [New-AzDisk](/powershell/module/az.compute/new-azdisk) | Cria um disco gerenciado. |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Cria uma configuração de VM. Essa configuração inclui informações como nome da VM, sistema operacional e credenciais administrativas. A configuração é usada durante a criação da VM. |
| [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) | Anexa o disco gerenciado como disco do sistema operacional à máquina virtual |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Cria um endereço IP público. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Cria um adaptador de rede. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Cria uma máquina virtual. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos e todos os recursos contidos nele. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o módulo do Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/).

Amostras de script do PowerShell da máquina virtual adicionais podem ser encontrados na [documentação da VM Linux do Azure](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
