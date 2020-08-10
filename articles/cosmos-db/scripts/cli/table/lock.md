---
title: Criar bloqueio de recurso para uma tabela de API de Tabela do Azure Cosmos DB
description: Criar bloqueio de recurso para uma tabela de API de Tabela do Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: b6041bf493cf3cb6dcff5f52bdb0950afbbc5108
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2020
ms.locfileid: "87431481"
---
# <a name="create-resource-lock-for-a-azure-cosmos-db-table-api-table-using-azure-cli"></a>Criar bloqueio de recurso para uma tabela de API de Tabela do Azure Cosmos DB usando a CLI do Azure

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tópico exigirá a execução da CLI do Azure versão 2.9.1 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

> [!IMPORTANT]
> Os bloqueios de recursos não funcionam para as alterações feitas por usuários que se conectam com SDK de Tabela do Cosmos DB, SDK de Tabela do Armazenamento do Azure, qualquer ferramenta que se conecte por meio de chaves de conta ou o portal do Azure, a menos que a conta do Cosmos DB seja bloqueada primeiro com a propriedade `disableKeyBasedMetadataWriteAccess` habilitada. Para saber mais sobre como habilitar essa propriedade, confira [Como impedir alterações por meio dos SDKs](../../../role-based-access-control.md#prevent-sdk-changes).

## <a name="sample-script"></a>Exemplo de script

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/table/lock.sh "Create a resource lock for an Azure Cosmos DB Table API table.")]

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
