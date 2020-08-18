---
title: Copiar um instantâneo de um disco gerenciado para uma assinatura – Amostra da CLI, VM do Windows
description: Amostra de script da CLI do Azure – Copiar (mover) o instantâneo de um disco gerenciado para a mesma ou outra assinatura com a CLI em uma VM do Windows
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
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 05b73bf62bfce456e65fde153379690bc9823e6a
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056730"
---
# <a name="copy-snapshot-of-a-managed-disk-to-same-or-different-subscription-with-cli-on-a-windows-vm"></a>Copiar o instantâneo de um disco gerenciado para a mesma ou outra assinatura com a CLI em uma VM do Windows

Esse script copia um instantâneo de um disco gerenciado para a mesma assinatura ou outra assinatura. Use este script para os cenários a seguir:

1. Migre uma captura de tela no armazenamento Premium (Premium_LRS) para o armazenamento padrão (Standard_LRS ou Standard_ZRS) para reduzir os custos.
1. Migre um instantâneo do armazenamento com redundância local (Premium_LRS, Standard_LRS) para o armazenamento com redundância de zona (Standard_ZRS) para se beneficiar da maior confiabilidade do armazenamento ZRS.
1. Mova um instantâneo para uma assinatura diferente na mesma região para retenção mais longa.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemplo de script

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.sh "Copy snapshot")]

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os comandos a seguir para criar um instantâneo na assinatura de destino usando a ID do instantâneo de origem. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az snapshot show](/cli/azure/snapshot) | Obtém todas as propriedades de um instantâneo usando o nome e as propriedades do grupo de recursos do instantâneo. A propriedade de ID é usada para copiar o instantâneo para uma assinatura diferente.  |
| [az snapshot create](/cli/azure/snapshot) | Copia um instantâneo criando um instantâneo na assinatura diferente usando a Id e o nome do instantâneo pai.  |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Os exemplos adicionais de script da CLI de máquina virtual e discos gerenciados podem ser encontrados na [documentação da VM Windows do Azure](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
