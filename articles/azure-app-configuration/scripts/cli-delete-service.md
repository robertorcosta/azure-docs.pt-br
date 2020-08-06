---
title: Amostra de script da CLI do Azure – Excluir um repositório de Configuração de Aplicativos do Azure
titleSuffix: Azure App Configuration
description: Usar script da CLI do Azure para excluir um repositório da Configuração de Aplicativos do Azure
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: lcozzens
ms.custom: devx-track-azurecli
ms.openlocfilehash: ba04b1a9aefa942c862b89f853d015762579937d
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87494835"
---
# <a name="delete-an-azure-app-configuration-store"></a>Excluir um repositório de configurações de aplicativo do Azure

Este script de exemplo exclui uma instância da Configuração de Aplicativo do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este artigo exigirá que seja executada a CLI do Azure versão 2.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exemplo de script

```azurecli-interactive
#/bin/bash

# Delete an App Configuration store named myTestAppConfigStore from the Resource Group myResourceGroup
az appconfig delete --name myTestAppConfigStore --resource-group myResourceGroup
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os comandos a seguir para excluir um repositório de Configuração de Aplicativos. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az appconfig delete](/cli/azure/appconfig#az-appconfig-delete) | Exclui um recurso do repositório de Configuração de Aplicativos. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Outros exemplos de script da CLI da Configuração de Aplicativos podem ser encontrados nos [Exemplos da CLI da Configuração de Aplicativos do Azure](../cli-samples.md).
