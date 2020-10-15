---
title: Exemplo de script da CLI do Azure – Criar uma VM com um VHD
description: Amostra de script da CLI do Azure – criar uma VM usando um disco rígido virtual.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/09/2017
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 6f5e30b0d6a072f9a40ae57ebe325461cdfe5bb1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87479584"
---
# <a name="create-a-vm-with-a-virtual-hard-disk"></a>Criar uma VM com um disco rígido virtual

Este exemplo cria uma máquina virtual usando um VHD.
Ele cria um grupo de recursos, uma conta de armazenamento e um contêiner, então cria uma VM carregando o VHD no contêiner.
Ele substitui a chave pública de SSH pela sua chave pública, de modo que você tem acesso à VM.

Você precisará de um VHD inicializável. O script procura `~/sample.vhd`.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemplo de script

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-vhd/create-vm-vhd.sh "Create VM using a VHD")]

## <a name="clean-up-deployment"></a>Limpar a implantação 

Execute o comando a seguir para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli-interactive 
az group delete -n az-cli-vhd
```

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos para criar um grupo de recursos, uma máquina virtual, um conjunto de disponibilidade, um balanceador de carga e todos os recursos relacionados. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](/cli/azure/group) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az storage account list](/cli/azure/storage/account) | Lista contas de armazenamento |
| [az storage account check-name](/cli/azure/storage/account) | Verifica se um nome de conta de armazenamento é válido e se já não existe |
| [az storage account keys list](/cli/azure/storage/account/keys) | Lista as chaves das contas de armazenamento |
| [az storage blob exists](/cli/azure/storage/blob) | Verifica se o blob existe |
| [az storage container create](/cli/azure/storage/container) | Cria um contêiner em uma conta de armazenamento. |
| [az storage blob upload](/cli/azure/storage/blob) | Cria um blob no contêiner carregando o VHD. |
| [az vm list](/cli/azure/vm) | Usado com `--query`, verifica se o nome da VM está em uso. | 
| [az vm create](/cli/azure/vm/availability-set) | Cria as máquinas virtuais. |
| [az vm list-ip-addresses](/cli/azure/vm#az-vm-list-ip-addresses) | Obtém o endereço IP da VM que foi criada. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Os exemplos de script da CLI de máquina virtual adicionais podem ser encontrados na [documentação da VM Linux do Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
