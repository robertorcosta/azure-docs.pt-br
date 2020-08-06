---
title: Montar um compartilhamento de arquivo para um aplicativo de funções Python – CLI do Azure
description: Crie um aplicativo de funções Python sem servidor e monte um compartilhamento de arquivo existente usando a CLI do Azure.
ms.topic: sample
ms.date: 03/01/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 63ab9ba3219dc600187e73bbf124d62d3f51317a
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87498201"
---
# <a name="mount-a-file-share-to-a-python-function-app-using-azure-cli"></a>Montar um compartilhamento de arquivo para um aplicativo de funções Python usando a CLI do Azure

Este script de exemplo do Azure Functions cria um aplicativo de funções e um compartilhamento nos Arquivos do Azure. Ele monta o compartilhamento para que os dados possam ser acessados por suas funções.  

>[!NOTE]
>O aplicativo de funções criado é executado no Python versão 3.7. O Azure Functions também [dá suporte às versões 3.6 e 3.8 do Python](../functions-reference-python.md#python-version).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este artigo exigirá que seja executada a CLI do Azure versão 2.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). Os exemplos são escritos para o shell Bash e devem ser modificados para serem executados em um prompt de comando do Windows. 

## <a name="sample-script"></a>Exemplo de script

Esse script cria um Aplicativo de funções do Azure usando o [Plano de consumo](../functions-scale.md#consumption-plan).

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/functions-cli-mount-files-storage-linux/functions-cli-mount-files-storage-linux.sh "Create an Azure Function on a Consumption plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação sobre o script

Cada comando da tabela é vinculado à documentação específica do comando. Este script usa os seguintes comandos:

| Comando | Observações |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Cria uma conta de armazenamento do Azure. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Cria um aplicativo de funções. |
| [az storage share create](/cli/azure/storage/share#az-storage-share-create) | Cria um compartilhamento de arquivos do Azure na conta de armazenamento. | 
| [az storage directory create](/cli/azure/storage/directory#az-storage-directory-create) | Cria um diretório no compartilhamento. |
| [az webapp config storage-account add](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) | Monta o compartilhamento para o aplicativo de funções. |
| [az webapp config storage-account list](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-list) | Mostra os compartilhamentos de arquivos montados no aplicativo de funções. | 

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Exemplos adicionais de scripts da CLI do Azure Functions podem ser encontrados na [Documentação do Azure Functions](../functions-cli-samples.md).
