---
title: Amostra da CLI do Azure – Copiar um instantâneo para uma conta de armazenamento em outra região
description: Amostra de script da CLI do Azure – Exportar/copiar um instantâneo como VHD para uma conta de armazenamento na mesma ou em outra região.
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc,seodec18
ms.openlocfilehash: e9390d6854ecfa43542d1713bae7d7e8fd8439ee
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038251"
---
# <a name="exportcopy-a-snapshot-to-a-storage-account-in-different-region-with-cli"></a>Exportar/copiar um instantâneo para uma conta de armazenamento em outa região com a CLI

Esse script exporta um instantâneo gerenciado para uma conta de armazenamento em outra região. Primeiro, ele gera o URI de SAS do instantâneo e, em seguida, usa-o para copiá-lo para uma conta de armazenamento em outra região. Use esse script para manter o backup dos discos gerenciados em outra região para a recuperação de desastre. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshots-to-storage-account/copy-snapshots-to-storage-account.sh "Copy snapshot")]


## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os comandos a seguir para gerar o URI de SAS para um instantâneo gerenciado e copia o instantâneo para uma conta de armazenamento usando o URI de SAS. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az snapshot grant-access](https://docs.microsoft.com/cli/azure/snapshot) | Gera o SAS somente leitura usado para copiar o arquivo VHD subjacente para uma conta de armazenamento ou o baixa no local  |
| [az storage blob copy start](https://docs.microsoft.com/cli/azure/storage/blob/copy) | Copia um blob de forma assíncrona de uma conta de armazenamento para outra |

## <a name="next-steps"></a>Próximas etapas

[Criar um disco gerenciado com base em um VHD](virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json)

[Criar uma máquina virtual com base em um disco gerenciado](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Os exemplos adicionais de script da CLI de máquina virtual e discos gerenciados podem ser encontrados na [documentação da VM Linux do Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
