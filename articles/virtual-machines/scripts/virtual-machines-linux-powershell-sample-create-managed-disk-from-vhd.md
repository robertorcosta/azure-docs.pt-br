---
title: Amostra de script do Azure PowerShell – Criar um disco gerenciado com base em um arquivo VHD em uma conta de armazenamento na mesma assinatura ou em outra assinatura | Microsoft Docs
description: Amostra de script do Azure PowerShell – Criar um disco gerenciado com base em um arquivo VHD em uma conta de armazenamento na mesma assinatura ou em outra assinatura
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 77d68763bac37a35b070bb7c95e2c16df7f67a27
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299342"
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-same-or-different-subscription-with-powershell"></a>Criar um disco gerenciado com base em um arquivo VHD em uma conta de armazenamento na mesma assinatura ou em outra assinatura com o PowerShell

Esse script cria um disco gerenciado com base em um arquivo VHD em uma conta de armazenamento na mesma assinatura ou em outra assinatura. Use esse script para importar um VHD especializado (não generalizado/do Sysprep) para um disco do sistema operacional gerenciado para criar uma máquina virtual. Além disso, use-o para importar um VHD de dados para um disco de dados gerenciados.

Não crie vários discos gerenciados idênticos com base em um arquivo VHD em um curto período. Para criar discos gerenciados com base em um arquivo VHD, o instantâneo de blob do arquivo VHD é criado e, em seguida, é usado para criar discos gerenciados. Apenas um único instantâneo de blob pode ser criado em um minuto, o que causa falhas na criação do disco devido à limitação. Para evitar essa limitação, crie um [instantâneo gerenciado com base no arquivo VHD](virtual-machines-linux-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e, em seguida, use o instantâneo gerenciado para criar vários discos gerenciados em um curto período.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disks-from-vhd-in-different-subscription/create-managed-disks-from-vhd-in-different-subscription.ps1 "Create managed disk from VHD")]

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os comandos a seguir para criar um disco gerenciado com base em um VHD em outra assinatura. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzDiskConfig) | Cria a configuração do disco que é usada para criação do disco. Ele inclui o tipo de armazenamento, o local, a ID do recurso da conta de armazenamento em que o VHD pai está armazenado e o URI VHD do VHD pai. |
| [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Cria um disco utilizando a configuração do disco, o nome do disco e o nome do grupo de recursos passados como parâmetros. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o módulo do Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/overview).

Amostras de script do PowerShell da máquina virtual adicionais podem ser encontrados na [documentação da VM Linux do Azure](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
