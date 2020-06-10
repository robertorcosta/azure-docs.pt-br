---
title: 'CLI do Azure: Adicionar um banco de dados a um grupo de failover'
description: Use o script de exemplo da CLI do Azure para criar um banco de dados no Banco de Dados SQL do Azure, adicioná-lo a um grupo de failover automático e testar o failover.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: azurecli
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: 09c98f626d9f32a2a6f9f3e31e0db0e4751adacb
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/29/2020
ms.locfileid: "84196967"
---
# <a name="use-the-azure-cli-to-add-a-database-to-a-failover-group"></a>Usar a CLI do Azure para adicionar um banco de dados a um grupo de failover

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Este exemplo de script da CLI do Azure cria um banco de dados no Banco de Dados SQL do Azure, cria um grupo de failover, adiciona o banco de dados a ele e testa o failover.

Se você optar por instalar e usar a CLI localmente, este tópico exigirá que você esteja executando a CLI do Azure versão 2.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exemplo de script

### <a name="sign-in-to-azure"></a>Entrar no Azure

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Executar o script

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add Azure SQL Database to failover group")]

### <a name="clean-up-deployment"></a>Limpar a implantação

Use o comando a seguir para remover o grupo de recursos e todos os recursos associados a ele.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Exemplo de referência

Este script usa os comandos a seguir. Cada comando na tabela redireciona para a documentação específica do comando.

| | |
|---|---|
| [az sql db](/cli/azure/sql/db) | Comandos de banco de dados. |
| [az sql failover-group](/cli/azure/sql/failover-group) | Comandos do grupo de failover. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Os exemplos de script da CLI do Banco de Dados SQL adicionais podem ser encontrados na [documentação do Banco de Dados SQL do Azure](../az-cli-script-samples-content-guide.md).
