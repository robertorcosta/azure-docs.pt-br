---
title: 'Copiar discos gerenciados para uma assinatura: amostra da CLI'
description: Exemplo de script da CLI do Azure – Copiar (mover) discos gerenciados para a mesma assinatura ou outra assinatura
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
ms.openlocfilehash: 4b1516e492b13426bed6cae8637abd5c713bcd63
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87010114"
---
# <a name="copy-managed-disks-to-same-or-different-subscription-with-cli"></a>Copiar um disco gerenciado para a mesma assinatura ou outra assinatura com a CLI

Esse script copia um disco gerenciado para a mesma assinatura ou outra assinatura, mas na mesma região. A cópia funciona apenas quando as assinaturas fazem parte do mesmo locatário do AAD.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemplo de script

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.sh "Copy managed disk")]

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os comandos a seguir para criar um novo disco gerenciado na assinatura de destino usando a ID do disco gerenciado de origem. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az disk show](/cli/azure/disk) | Obtém todas as propriedades de um disco gerenciado usando o nome e as propriedades do grupo de recursos do disco gerenciado. A propriedade de ID é usada para copiar o disco gerenciado para assinatura diferente.  |
| [az disk create](/cli/azure/disk) | Copia um disco gerenciado criando um novo disco gerenciado em uma assinatura diferente usando a Id e o nome do disco gerenciado pai.  |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Os exemplos adicionais de script da CLI de máquina virtual e discos gerenciados podem ser encontrados na [documentação da VM Windows do Azure](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
