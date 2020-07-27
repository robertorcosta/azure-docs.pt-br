---
title: Criar a VM do instantâneo (Windows) – Exemplo do PowerShell
description: Exemplo de script do Azure PowerShell – Criar uma VM de um instantâneo
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
ms.custom: mvc
ms.openlocfilehash: 7f8e7f5e758c916cf7e6b96ab38607ee722152b1
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86509539"
---
# <a name="create-a-virtual-machine-from-a-snapshot-with-powershell-windows"></a>Criar uma máquina virtual de um instantâneo com o PowerShell (Windows)

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

Para obter mais informações sobre o módulo do Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/overview).

Amostras de script do PowerShell da máquina virtual adicionais podem ser encontrados na [documentação da VM Windows do Azure](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
