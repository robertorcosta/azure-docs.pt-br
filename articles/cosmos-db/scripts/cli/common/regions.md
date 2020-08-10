---
title: Adicionar regiões, alterar a prioridade do failover, disparar failover para uma conta do Azure Cosmos
description: Adicionar regiões, alterar a prioridade do failover, disparar failover para uma conta do Azure Cosmos
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 4d404b1530e58788e5a34eb552d810c1ee40579a
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2020
ms.locfileid: "87432273"
---
# <a name="add-regions-change-failover-priority-trigger-failover-for-an-azure-cosmos-account-using-azure-cli"></a>Adicionar regiões, alterar a prioridade do failover, disparar failover para uma conta do Azure Cosmos usando a CLI do Azure

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tópico exigirá a execução da CLI do Azure versão 2.9.1 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exemplo de script

Esse script demonstra três operações.

- Adicione uma região a uma conta existente do Azure Cosmos.
- Alterar a prioridade de failover regional (aplica-se às contas que usam failover automático)
- Disparar um failover manual de regiões primárias para secundárias (aplica-se a contas com failover manual)

> [!NOTE]
> Adicionar e remover operações de região em uma conta do Cosmos não pode ser feito ao alterar outras propriedades.

> [!NOTE]
> Este exemplo demonstra como usar uma conta de API do SQL (Core), mas essas operações são idênticas entre todas as APIs de banco de dados no Cosmos DB.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/regions.sh "Regional operations for Cosmos DB.")]

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
| [az cosmosdb update](/cli/azure/cosmosdb#az-cosmosdb-update) | Atualiza uma conta do Azure Cosmos DB (adicionar ou remover região). |
| [az cosmosdb failover-priority-change](/cli/azure/cosmosdb#az-cosmosdb-failover-priority-change) | Atualize a prioridade de failover ou dispare um failover em uma conta do Azure Cosmos DB. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a CLI do Azure Cosmos DB, confira [documentação da CLI do Azure Cosmos DB](/cli/azure/cosmosdb).

Todos os exemplos de scripts da CLI do Azure Cosmos DB podem ser encontrados no [Repositório GitHub da CLI do Azure Cosmos DB](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
