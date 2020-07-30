---
title: Criar um disco gerenciado com base em um instantâneo (Windows) – Exemplo do PowerShell
description: Amostra de script do Azure PowerShell – Criar um disco gerenciado com base em um instantâneo
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: c3b429e9aab26313ace9dbc3672b08d03ec78c15
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87088607"
---
# <a name="create-a-managed-disk-from-a-snapshot-with-powershell-windows"></a>Criar um disco gerenciado com base em um instantâneo com o PowerShell (Windows)

Esse script cria um disco gerenciado com base em um instantâneo. Use-o para restaurar uma máquina virtual de instantâneos do sistema operacional e de discos de dados. Crie discos gerenciados do sistema operacional e de dados com base nos respectivos instantâneos e, em seguida, crie uma nova máquina virtual anexando os discos gerenciados. Restaure também discos de dados de uma VM existente anexando os discos de dados criados com base em instantâneos.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Exemplo de script

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disk-from-snapshot/create-managed-disk-from-snapshot.ps1 "Create managed disk from snapshot")]


## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os comandos a seguir para criar um disco gerenciado com base em um instantâneo. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [Get-AzSnapshot](/powershell/module/az.compute/get-azsnapshot) | Obtém as propriedades do instantâneo.  |
| [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) | Cria a configuração do disco que é usada para criação do disco. Inclui a ID do recurso do instantâneo pai, o local que é o mesmo local do instantâneo pai e o tipo de armazenamento.  |
| [New-AzDisk](/powershell/module/az.compute/new-azdisk) | Cria um disco utilizando a configuração do disco, o nome do disco e o nome do grupo de recursos passados como parâmetros. |


## <a name="next-steps"></a>Próximas etapas

[Criar uma máquina virtual com base em um disco gerenciado](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Para obter mais informações sobre o módulo do Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/).

Amostras de script do PowerShell da máquina virtual adicionais podem ser encontrados na [documentação da VM Windows do Azure](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
