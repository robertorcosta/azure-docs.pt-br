---
title: Criar um aplicativo de funções com a implantação do DevOps – CLI do Azure
description: Criar um aplicativo de funções e implantar o código da função do Azure DevOps
ms.date: 07/03/2018
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 773a08646f7a69e1ed828621bad48a6c6729eb88
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87498510"
---
# <a name="create-a-function-in-azure-that-is-deployed-from-azure-devops"></a>Criar uma função no Azure que é implantada usando o Azure DevOps

Este tópico mostra como usar o Azure Functions para criar um aplicativo de função [sem servidor](https://azure.microsoft.com/solutions/serverless/) usando o [Plano de consumo](../functions-scale.md#consumption-plan). O aplicativo de funções, que é um contêiner para suas funções, é continuamente implantado de um repositório do Azure DevOps. 

Para concluir este tópico, você deve ter:

* Um repositório do Azure DevOps que contém o projeto de aplicativo de função e ao qual você tem permissões administrativas.
* Um [PAT (token de acesso pessoal)](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate) para acessar o repositório do Azure DevOps.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você preferir usar a CLI do Azure localmente, será necessário usar a versão 2.0 ou uma versão posterior. Para determinar a versão da CLI do Azure, execute `az --version`. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exemplo de script

Este exemplo cria um aplicativo de funções do Azure e implanta o código da função do Azure DevOps.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/deploy-function-app-with-function-vsts/deploy-function-app-with-function-vsts.sh?highlight=3-4 "Azure Service")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos para criar um grupo de recursos, conta de armazenamento, aplicativo de função e todos os recursos relacionados. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Cria a conta de armazenamento necessária para o aplicativo de funções. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Cria um aplicativo de funções no [Plano de consumo](../functions-scale.md#consumption-plan) sem servidor. |
| [az functionapp deployment source config](/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config) | Associa um aplicativo de funções a um repositório Git ou Mercurial. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Exemplos adicionais de scripts da CLI do Azure Functions podem ser encontrados na [Documentação do Azure Functions](../functions-cli-samples.md).
