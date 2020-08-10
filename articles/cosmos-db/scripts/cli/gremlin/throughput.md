---
title: Atualizar RU/s para um banco de dados e um grafo Gremlin para Azure Cosmos DB
description: Atualizar RU/s para um banco de dados e um grafo Gremlin para Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 4774f5b4e74a963a5a4a07873305c79e2e7019f4
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2020
ms.locfileid: "87432182"
---
# <a name="update-rus-for-a-gremlin-database-and-graph-for-azure-cosmos-db-using-azure-cli"></a>Atualizar RU/s para um banco de dados e um grafo Gremlin para Azure Cosmos DB usando a CLI do Azure

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tópico exigirá a execução da CLI do Azure versão 2.9.1 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exemplo de script

Esse script cria um banco de dados do Gremlin com taxa de transferência compartilhada e um grafo do Gremlin com taxa de transferência dedicada e, em seguida, atualiza a taxa de transferência para o banco de dados e o grafo.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/gremlin/throughput.sh "Update RU/s for a Gremlin database and graph.")]

## <a name="clean-up-deployment"></a>Limpar a implantação

Após a execução do script de exemplo, o comando a seguir pode ser usado para remover o grupo de recursos e todos os recursos associados a ele.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Cria uma conta do Banco de Dados Cosmos do Azure. |
| [az cosmosdb gremlin database create](/cli/azure/cosmosdb/gremlin/database#az-cosmosdb-gremlin-database-create) | Cria um banco de dados Gremlin do Azure Cosmos. |
| [az cosmosdb gremlin graph create](/cli/azure/cosmosdb/gremlin/graph#az-cosmosdb-gremlin-graph-create) | Cria um grafo Gremlin do Azure Cosmos. |
| [az cosmosdb gremlin database throughput update](/cli/azure/cosmosdb/gremlin/database/throughput#az-cosmosdb-gremlin-database-throughput-update) | Atualizar RU/s para um banco de dados do Gremlin do Azure Cosmos. |
| [az cosmosdb gremlin graph throughput update](/cli/azure/cosmosdb/gremlin/graph/throughput#az-cosmosdb-gremlin-graph-throughput-update) | Atualizar RU/s para um grafo do Gremlin do Azure Cosmos. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a CLI do Azure Cosmos DB, confira [documentação da CLI do Azure Cosmos DB](/cli/azure/cosmosdb).

Todos os exemplos de scripts da CLI do Azure Cosmos DB podem ser encontrados no [Repositório GitHub da CLI do Azure Cosmos DB](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
