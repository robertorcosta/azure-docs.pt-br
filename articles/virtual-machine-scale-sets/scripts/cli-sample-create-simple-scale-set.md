---
title: Exemplos da CLI do Azure – Criar um conjunto de dimensionamento de máquinas virtuais
description: Esse script cria um conjunto de dimensionamento de máquinas virtuais do Azure com sistema operacional Ubuntu e recursos de rede relacionados, incluindo um balanceador de carga.
author: mimckitt
ms.author: mimckitt
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.subservice: cli
ms.date: 06/25/2020
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: c2c1ea09d10ee177a722a56f6825db0f37da7e9b
ms.sourcegitcommit: dfa5f7f7d2881a37572160a70bac8ed1e03990ad
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/25/2020
ms.locfileid: "85374655"
---
# <a name="create-a-virtual-machine-scale-set-with-the-azure-cli"></a>Criar um conjunto de dimensionamento de máquinas virtuais com a CLI do Azure
Esse script cria um conjunto de dimensionamento de máquinas virtuais do Azure com sistema operacional Ubuntu e recursos de rede relacionados, incluindo um balanceador de carga. Depois de executar o script, é possível acessar instâncias de VM via SSH.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemplo de script
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/simple-scale-set/simple-scale-set.sh "Create a simple virtual machine scale set")]

## <a name="clean-up-deployment"></a>Limpar a implantação
Execute o comando a seguir para remover o grupo de recursos, o conjunto de dimensionamento e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script
Este script usa os comandos a seguir para criar um grupo de recursos, um conjunto de dimensionamento de máquinas virtuais e todos os recursos relacionados. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](/cli/azure/ad/group) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az vmss create](/cli/azure/vmss) | Cria um conjunto de dimensionamento de máquinas virtuais e a conecta à rede virtual, à sub-rede e ao grupo de segurança de rede. Um balanceador de carga também é criado para distribuir o tráfego para as diversas instâncias de VM. Esse comando também especifica a imagem da VM a ser usada e as credenciais administrativas.  |
| [az group delete](/cli/azure/ad/group) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).
