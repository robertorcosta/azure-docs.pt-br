---
title: Exemplo de script da CLI do Azure - Criar uma máquina virtual do Windows Server
description: Exemplo de script da CLI do Azure - Criar uma máquina virtual do Windows Server
services: virtual-machines-Windows
documentationcenter: virtual-machines
author: rickstercdn
manager: gwallace
tags: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-Windows
ms.workload: infrastructure
ms.date: 02/23/2017
ms.author: rclaus
ms.custom: devx-track-azurecli
ms.openlocfilehash: bcee06dbd330dbed2f682c31266477df0a3f173b
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87497895"
---
# <a name="create-a-virtual-machine-with-the-azure-cli"></a>Criar uma máquina virtual com a CLI do Azure

Esse script cria uma Máquina Virtual do Azure que executa o Windows Server 2016. Após a execução do script, é possível acessar a máquina virtual por meio de uma conexão de Área de Trabalho Remota.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemplo de script

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-detailed/create-windows-vm-detailed.sh "Quick Create VM")]

## <a name="clean-up-deployment"></a>Limpar a implantação 

Execute o comando a seguir para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir para criar um grupo de recursos, uma máquina virtual e todos os recursos relacionados. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](/cli/azure/group) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az network vnet create](/cli/azure/network/vnet) | Cria uma sub-rede e uma rede virtual do Azure. |
| [az network public-ip create](/cli/azure/network/public-ip) | Cria um endereço IP público com um endereço IP estático e um nome DNS associado. |
| [az network nsg create](/cli/azure/network/nsg) | Cria um grupo de segurança de rede (NSG), que é um limite de segurança entre a Internet e a máquina virtual. |
| [az network nic create](/cli/azure/network/nic) | Cria uma placa de rede virtual e a anexa à rede virtual, à sub-rede e ao NSG. |
| [az vm create](/cli/azure/vm) | Cria a máquina virtual e a conecta a placa de rede, a rede virtual, a sub-rede e o NSG. Este comando também especifica a imagem de máquina virtual a ser usada e as credenciais administrativas.  |
| [az group delete](/cli/azure/vm/extension) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Exemplos de script CLI máquinas virtuais adicionais podem ser encontrados na [documentação da VM do Windows do Azure](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
