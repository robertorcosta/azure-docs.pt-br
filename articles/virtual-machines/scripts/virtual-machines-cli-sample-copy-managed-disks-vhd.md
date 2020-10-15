---
title: 'Copiar um disco gerenciado para uma conta de armazenamento: CLI'
description: Exemplo da CLI do Azure – Exporte ou copie discos gerenciados para uma conta de armazenamento.
documentationcenter: storage
author: ramankumarlive
manager: kavithag
ms.service: virtual-machines
ms.subservice: disks
ms.topic: sample
ms.workload: infrastructure
ms.date: 05/09/2019
ms.author: ramankum
ms.custom: mvc,seodec18, devx-track-azurecli
ms.openlocfilehash: c43a18f1dcb4122eb6c1407ca11b7c60653594c4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89322657"
---
# <a name="exportcopy-a-managed-disk-to-a-storage-account-using-the-azure-cli"></a>Exportar/copiar um disco gerenciado para uma conta de armazenamento usando a CLI do Azure

Esse script exporta o VHD subjacente de um disco gerenciado para uma conta de armazenamento na mesma região ou em outra região. Ele primeiro gera o URI do SAS do disco gerenciado e, em seguida, usa-o para copiar o VHD para uma conta de armazenamento. Use esse script para copiar discos gerenciados para outra região para expansão regional. Se você desejar publicar o arquivo VHD de um disco gerenciado no Azure Marketplace, será possível usar este script para copiar o arquivo VHD para a conta de armazenamento e gerar um URI de SAS do VHD copiado para publicá-lo no Marketplace.   


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemplo de script

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-managed-disks-vhd-to-storage-account/copy-managed-disks-vhd-to-storage-account.sh "Copy the VHD of a managed disk")]


## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os comandos a seguir para gerar o URI de SAS para um disco gerenciado e copia o VHD subjacente para uma conta de armazenamento usando o URI de SAS. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az disk grant-access](/cli/azure/disk?view=azure-cli-latest#az-disk-grant-access) | Gera o SAS somente leitura usado para copiar o arquivo VHD subjacente para uma conta de armazenamento ou o baixá-lo localmente  |
| [az storage blob copy start](/cli/azure/storage/blob/copy) | Copia um blob de forma assíncrona de uma conta de armazenamento para outra |

## <a name="next-steps"></a>Próximas etapas

[Criar um disco gerenciado com base em um VHD](virtual-machines-cli-sample-create-managed-disk-from-vhd.md)

[Criar uma máquina virtual com base em um disco gerenciado](virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md)

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Os exemplos adicionais de script da CLI de máquina virtual e discos gerenciados podem ser encontrados na [documentação da VM Linux do Azure](../linux/cli-samples.md).
