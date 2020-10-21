---
title: Exemplos de CLI do Azure para API de Azure Cosmos DB para MongoDB
description: Exemplos de CLI do Azure para API de Azure Cosmos DB para MongoDB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5cb8155f541aa6a6eb6c52e1e0bafeb0844e9689
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92276922"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-api-for-mongodb"></a>Exemplos de CLI do Azure para API de Azure Cosmos DB para MongoDB

A tabela a seguir inclui links para exemplos de scripts de CLI do Azure para o Azure Cosmos DB. Use os links à direita para navegar para exemplos específicos de API. Exemplos comuns são os mesmos em todas as APIs. As páginas de referência de todos os comandos do Azure Cosmos DB CLI estão disponíveis na [Referência de CLI do Azure](/cli/azure/cosmosdb). Exemplos de scripts da CLI do Azure Cosmos DB também podem ser encontrados no [Repositório GitHub da CLI do Azure Cosmos DB](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

Esses exemplos exigem CLI do Azure versão 2.12.1 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli)

## <a name="common-samples"></a>Exemplos comuns

Esses exemplos se aplicam a todas as APIs do Azure Cosmos DB

|Tarefa | Descrição |
|---|---|
| [Adicionar ou fazer failover de regiões](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Adicione uma região, altere a prioridade do failover, dispare um failover manual.|
| [Chaves da conta e cadeias de conexão](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | Liste chaves da conta, chaves somente leitura, regenerar chaves e listar cadeias de conexão.|
| [Proteger com o firewall do IP](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| Crie uma conta do Cosmos com o firewall do IP configurado.|
| [Proteger a nova conta com pontos de extremidade de serviço](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Crie uma conta do Cosmos e proteja com pontos de extremidade de serviço.|
| [Proteger a conta existente com pontos de extremidade de serviço](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Atualize uma conta do Cosmos para ser protegida com pontos de extremidade de serviço quando a sub-rede estiver finalmente configurada.|
|||

## <a name="mongodb-api-samples"></a>Exemplos da API do MongoDB

|Tarefa | Descrição |
|---|---|
| [Criar uma conta, um banco de dados e uma coleção do Azure Cosmos](scripts/cli/mongodb/create.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma conta, um banco de dados e uma coleção do Azure Cosmos DB para API do MongoDB. |
| [Criar uma conta do Azure Cosmos, um banco de dados com dimensionamento automático e duas coleções com taxa de transferência compartilhada](scripts/cli/mongodb/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Criar uma conta do Azure Cosmos DB, um banco de dados com dimensionamento automático e duas coleções com taxa de transferência compartilhada para a API do MongoDB. |
| [Operações de taxa de transferência](scripts/cli/mongodb/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Leia, atualize e migre entre o dimensionamento automático e a taxa de transferência padrão em um banco de dados e uma coleção.|
| [Bloquear recursos da exclusão](scripts/cli/mongodb/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Impedir que os recursos sejam excluídos com bloqueios de recursos.|
|||
