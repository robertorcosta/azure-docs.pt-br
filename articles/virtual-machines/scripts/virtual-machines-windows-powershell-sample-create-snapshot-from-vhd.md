---
title: Exemplo de Script do Azure PowerShell -  Criar um instantâneo de um VHD para criar discos gerenciados idênticos múltiplos em pouco tempo | Microsoft Docs
description: Exemplo de Script do Azure PowerShell -  Criar um instantâneo de um VHD para criar discos gerenciados idênticos múltiplos em pouco tempo
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: bb2afaa3800cfb430ab7888cfa547ad17fc8d975
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72300711"
---
# <a name="create-a-snapshot-from-a-vhd-to-create-multiple-identical-managed-disks-in-small-amount-of-time-with-powershell"></a>Criar um instantâneo de um VHD para criar discos gerenciados idênticos múltiplos em pouco tempo com PowerShell

Esse script cria um instantâneo de um arquivo VHD em uma conta de armazenamento na mesma assinatura ou em uma diferente. Utilize esse script para importar um VHD especializado (não generalizado/de sysprep) para um instantâneo e, em seguida, utilize o instantâneo para criar discos gerenciados idênticos múltiplos em pouco tempo. Além disso, utilize-o para importar um VHD de dados para um instantâneo e, em seguida, use o instantâneo para criar discos gerenciados múltiplos em pouco tempo. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-snapshots-from-vhd-in-different-subscription/create-snapshots-from-vhd-in-different-subscription.ps1 "Create snapshot from VHD")]


## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os comandos a seguir para criar um disco gerenciado com base em um VHD em outra assinatura. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzDiskConfig) | Cria a configuração do disco que é usada para criação do disco. Inclui o tipo de armazenamento, o local, o ID do recurso da conta de armazenamento em que o VHD principal está armazenado e o URI do VHD do VHD pai. |
| [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Cria um disco utilizando a configuração do disco, o nome do disco e o nome do grupo de recursos passados como parâmetros. |

## <a name="next-steps"></a>Próximas etapas

[Criar um disco gerenciado a partir do instantâneo](virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)


[Criar uma máquina virtual anexando um disco gerenciado como disco do SO](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Para obter mais informações sobre o módulo do Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/overview).

Amostras de script do PowerShell da máquina virtual adicionais podem ser encontrados na [documentação da VM Windows do Azure](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
