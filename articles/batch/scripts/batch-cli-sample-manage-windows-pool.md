---
title: Exemplo de Script da CLI do Azure – Pool do Windows em Lote
description: Esse script demonstra alguns dos comandos disponíveis na CLI do Azure para criar e gerenciar um pool dos nós de computação do Windows no Lote do Azure.
ms.topic: sample
ms.date: 12/12/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8863831d236581f07df85acda26268659e3a7d8b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87500258"
---
# <a name="cli-example-create-and-manage-a-windows-pool-in-azure-batch"></a>Exemplo da CLI: criar e gerenciar um pool do Windows em Lote do Azure

Esse script demonstra alguns dos comandos disponíveis na CLI do Azure para criar e gerenciar um pool dos nós de computação do Windows no Lote do Azure. Um pool do Windows pode ser configurado de duas maneiras, com uma configuração de Serviços de Nuvem ou com uma configuração de Máquina Virtual. Esse exemplo mostra como criar um pool do Windows com a configuração de Serviços de Nuvem.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Caso opte por instalar e usar a CLI localmente, este artigo exige que seja executada a CLI do Azure versão 2.0.20 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/batch/manage-pool/manage-pool-windows.sh "Manage Windows Cloud Services Pool")]

## <a name="clean-up-deployment"></a>Limpar a implantação

Execute o comando a seguir para remover o grupo de recursos e todos os recursos associados a ele.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir. Cada comando na tabela redireciona para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Cria a conta do Lote. |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Autentica na conta do Lote especificada para interação adicional com a CLI. |
| [az batch pool create](/cli/azure/batch/pool#az-batch-pool-create) | Cria um pool de nós de computação.  |
| [az batch pool set](/cli/azure/batch/pool#az-batch-pool-set) | Atualiza as propriedades de um pool.  |
| [az batch pool autoscale enable](/cli/azure/batch/pool/autoscale#az-batch-pool-autoscale-enable) | Habilita o dimensionamento automático em um pool e aplica uma fórmula.  |
| [az batch pool show](/cli/azure/batch/pool#az-batch-pool-show) | Exibe as propriedades de um pool.  |
| [az batch pool autoscale disable](/cli/azure/batch/pool/autoscale#az-batch-pool-autoscale-disable) | Desabilita o dimensionamento automático em um pool. |
| [az group delete](/cli/azure/group#az-group-delete) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).
