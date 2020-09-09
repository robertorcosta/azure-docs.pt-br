---
title: Criar um disco gerenciado com base em um arquivo VHD em uma conta de armazenamento em uma assinatura – Amostra do PowerShell
description: Amostra de script do Azure PowerShell – Criar um disco gerenciado com base em um arquivo VHD em uma conta de armazenamento na mesma assinatura ou em outra assinatura
documentationcenter: storage
author: ramankumarlive
manager: kavithag
ms.service: virtual-machines
ms.topic: sample
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 1d743d840afc4505cdc39b258b21856b6c3e7aa9
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89322629"
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-same-or-different-subscription-with-powershell"></a>Criar um disco gerenciado com base em um arquivo VHD em uma conta de armazenamento na mesma assinatura ou em outra assinatura com o PowerShell

Esse script cria um disco gerenciado com base em um arquivo VHD em uma conta de armazenamento na mesma assinatura ou em outra assinatura. Use esse script para importar um VHD especializado (não generalizado/do Sysprep) para um disco do sistema operacional gerenciado para criar uma máquina virtual. Além disso, use-o para importar um VHD de dados para um disco de dados gerenciados. 

Não crie vários discos gerenciados idênticos com base em um arquivo VHD em um curto período. Para criar discos gerenciados com base em um arquivo VHD, o instantâneo de blob do arquivo VHD é criado e, em seguida, é usado para criar discos gerenciados. Apenas um único instantâneo de blob pode ser criado em um minuto, o que causa falhas na criação do disco devido à limitação. Para evitar essa limitação, crie um [instantâneo gerenciado com base no arquivo VHD](virtual-machines-powershell-sample-create-snapshot-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) e, em seguida, use o instantâneo gerenciado para criar vários discos gerenciados em um curto período. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


 

## <a name="sample-script"></a>Exemplo de script

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disks-from-vhd-in-different-subscription/create-managed-disks-from-vhd-in-different-subscription.ps1 "Create managed disk from VHD")]


## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os comandos a seguir para criar um disco gerenciado com base em um VHD em outra assinatura. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) | Cria a configuração do disco que é usada para criação do disco. Essa configuração inclui o tipo de armazenamento, a localização, a ID do recurso da conta de armazenamento em que o VHD pai está armazenado e o URI VHD do VHD pai. |
| [New-AzDisk](/powershell/module/az.compute/new-azdisk) | Cria um disco utilizando a configuração do disco, o nome do disco e o nome do grupo de recursos passados como parâmetros. |

## <a name="next-steps"></a>Próximas etapas

[Criar uma máquina virtual anexando um disco gerenciado como disco do SO](virtual-machines-powershell-sample-create-vm-from-managed-os-disks.md)

Para obter mais informações sobre o módulo do Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/).

Amostras de script do PowerShell da máquina virtual adicionais podem ser encontrados na [documentação da VM Windows do Azure](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
