---
title: Criar um bloqueio de recurso para um banco de dados e uma coleção da API do MongoDB para Azure Cosmos DB
description: Criar um bloqueio de recurso para um banco de dados e uma coleção da API do MongoDB para Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: aa3d14be3a7a9392ca721fa3de52b8f92dbe986e
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93098137"
---
# <a name="create-a-resource-lock-for-azure-cosmos-dbs-api-for-mongodb-using-azure-cli"></a>Criar um bloqueio de recurso da API do Azure Cosmos DB para MongoDB usando a CLI do Azure
[!INCLUDE[appliesto-mongodb-api](../../../includes/appliesto-mongodb-api.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tópico exigirá a execução da CLI do Azure versão 2.9.1 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

> [!IMPORTANT]
> Os bloqueios de recursos não funcionam para as alterações feitas por usuários que se conectam usando qualquer SDK do MongoDB, o Mongoshell, qualquer ferramenta ou o portal do Azure, a menos que a conta do Cosmos DB seja bloqueada primeiro com a propriedade `disableKeyBasedMetadataWriteAccess` habilitada. Para saber mais sobre como habilitar essa propriedade, confira [Como impedir alterações por meio dos SDKs](../../../role-based-access-control.md#prevent-sdk-changes).

## <a name="sample-script"></a>Exemplo de script

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/mongodb/lock.sh "Create a resource lock for an Azure Cosmos DB MongoDB API database and collection.")]

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
