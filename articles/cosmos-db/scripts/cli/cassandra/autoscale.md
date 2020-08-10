---
title: Criar um keyspace e uma tabela do Cassandra com dimensionamento automático para o Azure Cosmos DB
description: Criar um keyspace e uma tabela do Cassandra com dimensionamento automático para o Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 7/29/2020
ms.openlocfilehash: 0f11d022567a5e221a306cd7c34f31cae0ef48aa
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2020
ms.locfileid: "87433329"
---
# <a name="create-an-azure-cosmos-cassandra-api-account-keyspace-and-table-with-autoscale-using-azure-cli"></a>Crie uma conta, um keyspace e uma tabela da API do Cassandra para o Azure Cosmos com dimensionamento automático usando a CLI do Azure

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tópico exigirá a execução da CLI do Azure versão 2.9.1 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exemplo de script

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/cassandra/autoscale.sh "Create an Azure Cosmos DB Cassandra API account, keyspace, and table with autoscale.")]

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
| [az cosmosdb cassandra keyspace create](/cli/azure/cosmosdb/cassandra/keyspace#az-cosmosdb-cassandra-keyspace-create) | Cria um keyspace do Cassandra do Azure Cosmos. |
| [az cosmosdb cassandra table create](/cli/azure/cosmosdb/cassandra/table#az-cosmosdb-cassandra-table-create) | Cria uma tabela do Cassandra do Azure Cosmos. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a CLI do Azure Cosmos DB, confira [documentação da CLI do Azure Cosmos DB](/cli/azure/cosmosdb).

Todos os exemplos de scripts da CLI do Azure Cosmos DB podem ser encontrados no [Repositório GitHub da CLI do Azure Cosmos DB](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
