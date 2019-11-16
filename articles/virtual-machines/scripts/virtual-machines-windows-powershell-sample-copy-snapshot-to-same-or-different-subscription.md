---
title: Amostra de script do Azure PowerShell – Copiar (mover) um instantâneo de um disco gerenciado para a mesma assinatura ou outra assinatura
description: Amostra de script do Azure PowerShell – Copiar (mover) um instantâneo de um disco gerenciado para a mesma assinatura ou outra assinatura
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
ms.date: 02/28/2019
ms.author: ramankum
ms.openlocfilehash: 13add6614af8d3ad76e820228b748c0084a29876
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039811"
---
# <a name="copy-snapshot-of-a-managed-disk-in-same-subscription-or-different-subscription-with-powershell"></a>Copiar um instantâneo de um disco gerenciado para a mesma assinatura ou outra assinatura com o PowerShell

Esse script copia um instantâneo de um disco gerenciado para a mesma assinatura ou outra assinatura. Use este script para os cenários a seguir:

1. Migre uma captura de tela no armazenamento Premium (Premium_LRS) para o armazenamento padrão (Standard_LRS ou Standard_ZRS) para reduzir os custos.
1. Migre um instantâneo do armazenamento com redundância local (Premium_LRS, Standard_LRS) para o armazenamento com redundância de zona (Standard_ZRS) para se beneficiar da maior confiabilidade do armazenamento ZRS.
1. Mova um instantâneo para uma assinatura diferente na mesma região para retenção mais longa.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.ps1 "Copy snapshot")]

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os comandos a seguir para criar um instantâneo na assinatura de destino usando a ID do instantâneo de origem. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [New-AzSnapshotConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzSnapshotConfig) | Cria uma configuração de instantâneo que é usada para a criação do instantâneo. Inclui a ID do recurso do instantâneo pai e o local que é o mesmo local do instantâneo pai.  |
| [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Cria um instantâneo usando uma configuração de instantâneo, o nome do instantâneo e o nome do grupo de recursos passados como parâmetros. |

## <a name="next-steps"></a>Próximas etapas

[Criar uma máquina virtual com base em um instantâneo](./virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

Para obter mais informações sobre o módulo do Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/overview).

Amostras de script do PowerShell da máquina virtual adicionais podem ser encontrados na [documentação da VM Windows do Azure](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
