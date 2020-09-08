---
title: Disco gerenciado do arquivo VHD na mesma conta – Exemplo da CLI
description: Amostra de script da CLI do Azure – Criar um disco gerenciado com base em um arquivo VHD em uma conta de armazenamento na mesma assinatura
documentationcenter: storage
author: ramankumarlive
manager: kavithag
ms.service: virtual-machines
ms.subservice: disks
ms.devlang: azurecli
ms.topic: sample
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 4d979830e622e68cd795f0137b96442a762daade
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89322635"
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-the-same-subscription-with-cli-linux"></a>Criar um disco gerenciado com base em um arquivo VHD em uma conta de armazenamento na mesma assinatura com a CLI (Linux)

Este script cria um disco gerenciado com base em um arquivo VHD em uma conta de armazenamento na mesma assinatura. Use esse script para importar um VHD especializado (não generalizado/do Sysprep) para um disco do sistema operacional gerenciado para criar uma máquina virtual. Ou, use-o para importar um VHD de dados para um disco de dados gerenciados. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemplo de script

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-managed-data-disks-from-vhd/create-managed-data-disks-from-vhd.sh "Create managed disk from VHD")]


## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os comandos a seguir para criar um disco gerenciado com base em um VHD. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az disk create](/cli/azure/disk) | Cria um disco gerenciado usando o URI de um arquivo VHD em uma conta de armazenamento na mesma assinatura |

## <a name="next-steps"></a>Próximas etapas

[Criar uma máquina virtual anexando um disco gerenciado como disco do SO](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Os exemplos adicionais de script da CLI de máquina virtual e discos gerenciados podem ser encontrados na [documentação da VM Linux do Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
