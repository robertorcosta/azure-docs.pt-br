---
title: Criar um bloqueio de recurso para um keyspace e uma tabela do Cassandra para o Azure Cosmos DB
description: Criar um bloqueio de recurso para um keyspace e uma tabela do Cassandra para o Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 06/03/2020
ms.openlocfilehash: abc1ed88c09c132bae4a7652b2d10ac95c85e186
ms.sourcegitcommit: 5504d5a88896c692303b9c676a7d2860f36394c1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2020
ms.locfileid: "84514620"
---
# <a name="create-a-resource-lock-for-azure-cosmos-cassandra-api-keyspace-and-table-using-azure-cli"></a>Criar um bloqueio de recurso para um keyspace e uma tabela da API do Cassandra para o Azure Cosmos usando a CLI do Azure

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tópico exigirá a execução da CLI do Azure versão 2.6.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

> [!IMPORTANT]
> Os bloqueios de recursos não funcionam para as alterações feitas por usuários que se conectam usando qualquer SDK do Cassandra, o Shell do CQL ou o portal do Azure, a menos que a conta do Cosmos DB seja bloqueada primeiro com a propriedade `disableKeyBasedMetadataWriteAccess` habilitada. Para saber mais sobre como habilitar essa propriedade, confira [Como impedir alterações por meio dos SDKs](../../../role-based-access-control.md#preventing-changes-from-cosmos-sdk).

## <a name="sample-script"></a>Exemplo de script

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/cassandra/lock.sh "Create a resource lock for an Azure Cosmos DB Cassandra API keyspace, and table.")]

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az lock create](/cli/azure/lock#az-lock-create) | Cria um bloqueio. |
| [az lock list](/cli/azure/lock#az-lock-list) | Lista informações sobre o bloqueio. |
| [az lock show](/cli/azure/lock#az-lock-show) | Mostra as propriedades de um bloqueio. |
| [az lock delete](/cli/azure/lock#az-lock-delete) | Exclui um bloqueio. |

## <a name="next-steps"></a>Próximas etapas

-[Bloquear recursos para prevenir alterações inesperadas](../../../../azure-resource-manager/management/lock-resources.md)

-[Documentação da CLI do Azure Cosmos DB](/cli/azure/cosmosdb).

-[Repositório GitHub da CLI do Azure Cosmos DB](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
