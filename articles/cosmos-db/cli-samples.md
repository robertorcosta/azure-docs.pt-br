---
title: Exemplos da CLI do Azure para o Azure Cosmos DB
description: Exemplos da CLI do Azure para o Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 07/29/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 954215f04525e850151fdad93af6e7272b41b3df
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87498456"
---
# <a name="azure-cli-samples-for-azure-cosmos-db"></a>Exemplos da CLI do Azure para o Azure Cosmos DB

A tabela a seguir inclui links para exemplos de scripts de CLI do Azure para o Azure Cosmos DB. Use os links à direita para navegar para exemplos específicos de API. Exemplos comuns são os mesmos em todas as APIs. As páginas de referência de todos os comandos do Azure Cosmos DB CLI estão disponíveis na [Referência de CLI do Azure](/cli/azure/cosmosdb). Exemplos de scripts da CLI do Azure Cosmos DB também podem ser encontrados no [Repositório GitHub da CLI do Azure Cosmos DB](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

Esses exemplos exigem a CLI do Azure versão 2.9.1 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli)

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

## <a name="core-sql-api-samples"></a>Exemplos de API do Core (SQL)

|Tarefa | Descrição |
|---|---|
| [Crie uma conta, um banco de dados e um contêiner do Azure Cosmos](scripts/cli/sql/create.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma conta, um banco de dados e um contêiner do Azure Cosmos DB para a API do Core (SQL). |
| [Criar uma conta, um banco de dados e um contêiner do Azure Cosmos com dimensionamento automático](scripts/cli/sql/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma conta, um banco de dados e um contêiner do Azure Cosmos DB com dimensionamento automático para a API do Core (SQL). |
| [Alterar taxa de transferência](scripts/cli/sql/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Atualizar a RU/s em um banco de dados e em um contêiner.|
| [Bloquear recursos da exclusão](scripts/cli/sql/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Impedir que os recursos sejam excluídos com bloqueios de recursos.|
|||

## <a name="mongodb-api-samples"></a>Exemplos da API do MongoDB

|Tarefa | Descrição |
|---|---|
| [Criar uma conta, um banco de dados e uma coleção do Azure Cosmos](scripts/cli/mongodb/create.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma conta, um banco de dados e uma coleção do Azure Cosmos DB para API do MongoDB. |
| [Criar uma conta do Azure Cosmos, um banco de dados com dimensionamento automático e duas coleções com taxa de transferência compartilhada](scripts/cli/mongodb/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Criar uma conta do Azure Cosmos DB, um banco de dados com dimensionamento automático e duas coleções com taxa de transferência compartilhada para a API do MongoDB. |
| [Alterar taxa de transferência](scripts/cli/mongodb/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Atualizar a RU/s em um banco de dados e em uma coleção.|
| [Bloquear recursos da exclusão](scripts/cli/mongodb/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Impedir que os recursos sejam excluídos com bloqueios de recursos.|
|||

## <a name="cassandra-api-samples"></a>Exemplos de API do Cassandra

|Tarefa | Descrição |
|---|---|
| [Criar uma conta, um keyspace e uma tabela do Azure Cosmos](scripts/cli/cassandra/create.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma conta, um keyspace e uma tabela do Azure Cosmos DB para a API do Cassandra. |
| [Criar uma conta, um keyspace e uma tabela do Azure Cosmos com dimensionamento automático](scripts/cli/cassandra/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma conta, um keyspace e uma tabela do Azure Cosmos DB com dimensionamento automático para a API do Cassandra. |
| [Alterar taxa de transferência](scripts/cli/cassandra/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Atualizar a RU/s em um keyspace e em uma tabela.|
| [Bloquear recursos da exclusão](scripts/cli/cassandra/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Impedir que os recursos sejam excluídos com bloqueios de recursos.|
|||

## <a name="gremlin-api-samples"></a>Exemplos de API do Gremlin

|Tarefa | Descrição |
|---|---|
| [Criar uma conta, um banco de dados e um grafo do Azure Cosmos](scripts/cli/gremlin/create.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma conta, um banco de dados e um grafo do Azure Cosmos DB para a API do Gremlin. |
| [Criar uma conta, um banco de dados e um grafo do Azure Cosmos com dimensionamento automático](scripts/cli/gremlin/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma conta, um banco de dados e um grafo do Azure Cosmos DB com dimensionamento automático para a API do Gremlin. |
| [Alterar taxa de transferência](scripts/cli/gremlin/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Atualizar a RU/s em um banco de dados e em um grafo.|
| [Bloquear recursos da exclusão](scripts/cli/gremlin/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Impedir que os recursos sejam excluídos com bloqueios de recursos.|
|||

## <a name="table-api-samples"></a>Exemplos de API de Tabela

|Tarefa | Descrição |
|---|---|
| [Criar uma conta e uma tabela do Azure Cosmos](scripts/cli/table/create.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma conta e uma tabela do Azure Cosmos DB para a API de Tabela. |
| [Criar uma conta e uma tabela do Azure Cosmos com dimensionamento automático](scripts/cli/table/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Cria uma conta e uma tabela do Azure Cosmos DB com dimensionamento automático para a API de Tabela. |
| [Alterar taxa de transferência](scripts/cli/table/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Atualize a RU/s em uma tabela.|
| [Bloquear recursos da exclusão](scripts/cli/table/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Impedir que os recursos sejam excluídos com bloqueios de recursos.|
|||
