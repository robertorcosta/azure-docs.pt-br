---
title: Exemplo de script da CLI do Azure – Importar para um repositório de Configuração de Aplicativos
titleSuffix: Azure App Configuration
description: Usar script da CLI do Azure – Importar configuração para a Configuração de Aplicativos do Azure
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: lcozzens
ms.custom: devx-track-azurecli
ms.openlocfilehash: fe4c22042c31d55575320d9c3823783902700f9f
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87494767"
---
# <a name="import-to-an-azure-app-configuration-store"></a>Importação para um repositório de configurações de aplicativo do Azure

Este script de exemplo importa configurações de pares chave-valor para um repositório de Configuração de Aplicativos do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI do Azure localmente, este artigo exigirá que você esteja executando a CLI do Azure versão 2.0 ou posterior. Execute `az --version` para encontrar a versão. Para instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exemplo de script

```azurecli-interactive
#!/bin/bash

# Import key-values from a file
az appconfig kv import --name myTestAppConfigStore --source file --path ~/Import.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os comandos a seguir para importar para um repositório de Configuração de Aplicativos. Cada comando na tabela redireciona para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [az appconfig kv import](/cli/azure/appconfig/kv#az-appconfig-kv-import) | Importa para um recurso do repositório de Configuração de Aplicativos. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, confira a [Documentação da CLI do Azure](/cli/azure).

Outros exemplos de script da CLI da Configuração de Aplicativos podem ser encontrados nos [Exemplos da CLI da Configuração de Aplicativos do Azure](../cli-samples.md).
