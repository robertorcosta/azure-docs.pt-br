---
title: Criar uma conta do Azure Cosmos com pontos de extremidade de serviço de rede virtual
description: Criar uma conta do Azure Cosmos com pontos de extremidade de serviço de rede virtual
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 4d1a56c80cab58e98121ae35c98a086d16dfe02b
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2020
ms.locfileid: "87432231"
---
# <a name="create-an-azure-cosmos-account-with-virtual-network-service-endpoints-using-azure-cli"></a>Criar uma conta do Azure Cosmos com pontos de extremidade de serviço de rede virtual usando a CLI do Azure

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este comando exigirá que a versão 2.9.1 ou posterior da CLI do Azure esteja em execução. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exemplo de script

Este exemplo cria uma rede virtual com uma sub-rede de front e de back-end e habilita pontos de extremidade de serviço para `Microsoft.AzureCosmosDB`. Ele recupera a ID do recurso dessa sub-rede e a aplica à conta do Azure Cosmos e habilita pontos de extremidade de serviço para a conta.

> [!NOTE]
> Esta amostra descreve como usar uma conta da API do Core (SQL). Para usar este exemplo para outras APIs, aplique os parâmetros `enable-virtual-network` e `virtual-network-rules` no script abaixo ao script específico da sua API.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/service-endpoints.sh "Create an Azure Cosmos account with service endpoints.")]

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
| [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) | Cria uma rede virtual do Azure. |
| [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) | Cria uma sub-rede para uma rede virtual do Azure. |
| [az network vnet subnet show](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-show) | Retorna uma sub-rede para uma rede virtual do Azure. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Cria uma conta do Banco de Dados Cosmos do Azure. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a CLI do Azure Cosmos DB, confira [documentação da CLI do Azure Cosmos DB](/cli/azure/cosmosdb).

Todos os exemplos de scripts da CLI do Azure Cosmos DB podem ser encontrados no [Repositório GitHub da CLI do Azure Cosmos DB](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
