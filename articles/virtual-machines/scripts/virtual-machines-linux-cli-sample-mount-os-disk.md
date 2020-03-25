---
title: Exemplo de Script CLI do Azure - montar o disco do sistema operacional
description: Exemplo de Script CLI do Azure - montar o disco do sistema operacional
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 292d67dafa768c82041a2cae8e6d888ee5d9050b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "74037604"
---
# <a name="troubleshoot-a-vms-operating-system-disk"></a>Solucionar problemas de um disco de sistema operacional de VMs

Esse script monta o disco do sistema operacional de uma máquina virtual com falha ou um problema como um disco de dados para uma segunda máquina virtual. Isso pode ser útil ao solucionar problemas de disco problemas ou recuperação de dados.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemplo de script

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/mount-os-disk/mount-os-disk.sh "Quick Create VM")]

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir para criar um grupo de recursos, uma máquina virtual e todos os recursos relacionados. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az vm show](https://docs.microsoft.com/cli/azure/vm) | Retorne a lista de máquinas virtuais. Nesse caso, a opção de consulta é usada para retornar o disco de sistema operacional da máquina virtual. Este valor é adicionado a um nome de variável 'uri'. |
| [az vm delete](https://docs.microsoft.com/cli/azure/vm) | Exclui uma máquina virtual. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm) | Cria uma máquina virtual.  |
| [az vm disk attach](https://docs.microsoft.com/cli/azure/vm/disk) | Anexa um disco a uma máquina virtual. |
| [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm) | Retorna os endereços IP de uma máquina virtual. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Os exemplos de script da CLI de máquina virtual adicionais podem ser encontrados na [documentação da VM Linux do Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
