---
title: Criar uma VM anexando um disco gerenciado como um disco do sistema operacional (Windows)– PowerShell
description: Exemplo de script do Azure PowerShell- Criar uma VM anexando um disco gerenciado como disco do sistema operacional
services: virtual-machines-windows
documentationcenter: virtual-machines
author: ramankumarlive
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 68a7bb8fa826e025afd911911ddd6e41dfe88962
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89077875"
---
# <a name="create-a-virtual-machine-using-an-existing-managed-os-disk-with-powershell-windows"></a>Criar uma máquina virtual usando um disco do sistema operacional gerenciado com o PowerShell (Windows)

Esse script cria uma máquina virtual anexando um disco gerenciado existente como disco do sistema operacional. Use esse script em cenários anteriores:
* Criar uma VM de um disco de sistema operacional gerenciado existente que foi copiado de um disco gerenciado em uma assinatura diferente
* Criar uma VM de um disco gerenciado existente que foi criado de um arquivo VHD especializado 
* Criar uma VM de um disco do sistema operacional gerenciado existente que foi criado de um instantâneo 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Exemplo de script

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.ps1 "Create VM from snapshot")]

## <a name="clean-up-deployment"></a>Limpar a implantação 

Execute o comando a seguir para remover o grupo de recursos, a VM e todos os recursos relacionados.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos para obter as propriedades do disco gerenciado, anexar um disco gerenciado em uma nova VM e criar uma VM. Cada item em que a tabela contém links para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [Get-AzDisk](/powershell/module/az.compute/get-azdisk) | Obtém o objeto de disco com base no nome e no grupo de recursos de um disco. A propriedade de ID do objeto de disco retornado é usada para anexar o disco a uma nova VM |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Cria uma configuração de VM. Essa configuração inclui informações como nome da VM, sistema operacional e credenciais administrativas. A configuração é usada durante a criação da VM. |
| [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) | Anexa um disco gerenciado usando a propriedade de ID do disco como disco do sistema operacional a uma nova máquina virtual |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Cria um endereço IP público. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Cria um adaptador de rede. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Crie uma máquina virtual. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos e todos os recursos contidos nele. |

Para imagens do marketplace, use [Set-AzVMPlan](/powershell/module/az.compute/set-azvmplan) para definir as informações do plano.

```powershell
Set-AzVMPlan -VM $VirtualMachine -Publisher $Publisher -Product $Product -Name $Bame
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o módulo do Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/).

Amostras de script do PowerShell da máquina virtual adicionais podem ser encontrados na [documentação da VM Windows do Azure](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
