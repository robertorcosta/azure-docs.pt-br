---
title: Exemplo de Script da CLI do Azure – Criar conta do Lote – assinatura do usuário
description: O script cria uma conta de Lote do Azure no modo de assinatura do usuário. Essa conta aloca nós de computação em sua assinatura.
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1c60b9c3de5f2370d7bbc557d36db61d23d32ccf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87494384"
---
# <a name="cli-example-create-a-batch-account-in-user-subscription-mode"></a>Exemplo da CLI: criar uma conta de Lote no modo de assinatura do usuário

O script cria uma conta de Lote do Azure no modo de assinatura do usuário. Uma conta que aloca nós de computação em sua assinatura deve ser autenticada por meio de um token do Azure Active Directory. Os nós de computação alocados contam para a cota de vCPU (núcleo) de sua assinatura. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Caso opte por instalar e usar a CLI localmente, este artigo exige que seja executada a CLI do Azure versão 2.0.20 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account-user-subscription.sh "Create Account using user subscription")]

## <a name="clean-up-deployment"></a>Limpar a implantação

Execute o comando a seguir para remover o grupo de recursos e todos os recursos associados a ele.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir. Cada comando na tabela redireciona para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [az role assignment create](/cli/azure/role) | Crie uma nova atribuição de função para um usuário, grupo ou entidade de serviço. |
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az keyvault create](/cli/azure/keyvault#az-keyvault-create) | Cria um cofre de chave. |
| [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy) | Atualize a política de segurança do cofre de chaves especificado. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Cria a conta do Lote.  |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Autentica na conta do Lote especificada para interação adicional com a CLI.  |
| [az group delete](/cli/azure/group#az-group-delete) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).
