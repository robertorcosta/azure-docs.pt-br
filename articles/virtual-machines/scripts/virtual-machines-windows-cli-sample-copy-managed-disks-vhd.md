---
title: Copiar um disco gerenciado para uma conta de armazenamento – Exemplo da CLI do Windows
description: Exemplo da CLI do Azure – Exporte ou copie discos gerenciados para uma conta de armazenamento.
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/17/2018
ms.author: ramankum
ms.custom: mvc,seodec18, devx-track-azurecli
ms.openlocfilehash: 00b6f0d79427cf1f4af2c1669f514aaf6fe7558a
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87500327"
---
# <a name="exportcopy-a-managed-disk-to-a-storage-account-using-the-azure-cli"></a>Exportar/copiar um disco gerenciado para uma conta de armazenamento usando a CLI do Azure

Esse script exporta o VHD subjacente de um disco gerenciado para uma conta de armazenamento na mesma região ou em outra região. Ele primeiro gera o URI do SAS do disco gerenciado e, em seguida, usa-o para copiar o VHD para uma conta de armazenamento. Use esse script para copiar os discos gerenciados para expansão regional.

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

[Criar um disco gerenciado com base em um VHD](virtual-machines-windows-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json)

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Os exemplos adicionais de script da CLI de máquina virtual e discos gerenciados podem ser encontrados na [documentação da VM Windows do Azure](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
