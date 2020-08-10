---
title: Exemplos do Azure PowerShell para o BD Cosmos do Azure
description: Obtenha os exemplos de Azure PowerShell para executar tarefas comuns no Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/30/2020
ms.author: mjbrown
ms.openlocfilehash: 678e9490fe52a329365174e1a2283a475f3312e5
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87505009"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Exemplos do Azure PowerShell para o BD Cosmos do Azure

A tabela a seguir inclui links para scripts do Azure PowerShell comumente usados no Azure Cosmos DB. Use os links à direita para navegar para exemplos específicos de API. Exemplos comuns são os mesmos em todas as APIs. As páginas de referência de todos os cmdlets do PowerShell do Azure Cosmos DB estão disponíveis na [Referência do Azure PowerShell](/powershell/module/az.cosmosdb). Verifique regularmente se há atualizações ao `Az.CosmosDB`. Também é possível criar fork desses exemplos do PowerShell para o Cosmos DB em nosso repositório GitHub. Para ver como, confira [Amostras do PowerShell para o Cosmos DB no GitHub](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

## <a name="common-samples"></a>Exemplos comuns

|Tarefa | Descrição |
|---|---|
|[Atualizar uma conta](scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Atualize o nível de consistência padrão de uma conta do Cosmos DB. |
|[Atualizar as regiões de uma conta](scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Atualizar as regiões de uma conta do Cosmos DB. |
|[Alterar a prioridade de failover ou disparar um failover](scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Altere a prioridade de failover regional de uma conta do Azure Cosmos ou dispare um failover manual. |
|[Chaves de conta ou cadeias de conexão](scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Obtenha chaves primárias e secundárias, cadeias de conexão ou regenere uma chave de conta de uma conta do Azure Cosmos DB. |
|[Criar uma conta do Cosmos com o firewall de IP](scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crie uma conta do Azure Cosmos DB com o firewall de IP habilitado. |
|||

## <a name="core-sql-api-samples"></a>Exemplos de API do Core (SQL)

|Tarefa | Descrição |
|---|---|
|[Criar uma conta, um banco de dados e um contêiner](scripts/powershell/sql/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crie uma conta, um banco de dados e um contêiner do Azure Cosmos DB. |
|[Criar uma conta, um banco de dados e um contêiner com dimensionamento automático](scripts/powershell/sql/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crie uma conta, um banco de dados e um contêiner do Azure Cosmos DB com dimensionamento automático. |
|[Criar um contêiner com uma chave de partição grande](scripts/powershell/sql/create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crie um contêiner com uma chave de partição grande. |
|[Criar um contêiner sem a política de índice](scripts/powershell/sql/create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crie um contêiner do Azure Cosmos com uma política de índice desativada.|
|[Listar ou obter bancos de dados ou contêineres](scripts/powershell/sql/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Liste ou obtenha um banco de dados ou contêineres. |
|[Obter taxa de transferência](scripts/powershell/sql/throughput-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Obtenha a taxa de transferência de um banco de dados ou contêiner. |
|[Atualizar a taxa de transferência](scripts/powershell/sql/throughput-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Atualize a taxa de transferência de um banco de dados ou contêiner. |
|[Bloquear recursos da exclusão](scripts/powershell/sql/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Impedir que os recursos sejam excluídos com bloqueios de recursos. |
|||

## <a name="cassandra-api-samples"></a>Exemplos de API do Cassandra

|Tarefa | Descrição |
|---|---|
|[Criar uma conta, um keyspace e uma tabela](scripts/powershell/cassandra/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma conta, um keyspace e uma tabela do Azure Cosmos. |
|[Criar uma conta, um keyspace e uma tabela com dimensionamento automático](scripts/powershell/cassandra/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crie uma conta, um keyspace e uma tabela do Azure Cosmos com dimensionamento automático. |
|[Listar ou obter keyspaces ou tabelas](scripts/powershell/cassandra/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Liste ou obtenha keyspaces ou tabelas. |
|[Obter taxa de transferência](scripts/powershell/cassandra/throughput-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Obtenha a taxa de transferência de um keyspace ou uma tabela. |
|[Atualizar a taxa de transferência](scripts/powershell/cassandra/throughput-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Atualize a taxa de transferência de um keyspace ou uma tabela. |
|[Bloquear recursos da exclusão](scripts/powershell/cassandra/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Impedir que os recursos sejam excluídos com bloqueios de recursos. |
|||

## <a name="mongo-db-api-samples"></a>Exemplos de API do Mongo DB

|Tarefa | Descrição |
|---|---|
|[Criar uma conta, um banco de dados e uma coleção](scripts/powershell/mongodb/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma conta, um banco de dados e uma coleção do Azure Cosmos. |
|[Criar uma conta, um banco de dados e uma coleção com dimensionamento automático](scripts/powershell/mongodb/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crie uma conta, um banco de dados e uma coleção do Azure Cosmos com dimensionamento automático. |
|[Listar ou obter bancos de dados ou coleções](scripts/powershell/mongodb/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Liste ou obtenha um banco de dados ou uma coleção. |
|[Obter taxa de transferência](scripts/powershell/mongodb/throughput-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Obtenha a taxa de transferência de um banco de dados ou coleção. |
|[Atualizar a taxa de transferência](scripts/powershell/mongodb/throughput-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Atualize a taxa de transferência de um banco de dados ou coleção. |
|[Bloquear recursos da exclusão](scripts/powershell/mongodb/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Impedir que os recursos sejam excluídos com bloqueios de recursos. |
|||

## <a name="gremlin-api-samples"></a>Exemplos de API do Gremlin

|Tarefa | Descrição |
|---|---|
|[Criar uma conta, um banco de dados e um grafo](scripts/powershell/gremlin/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma conta, um banco de dados e um grafo do Azure Cosmos. |
|[Criar uma conta, um banco de dados e um grafo com dimensionamento automático](scripts/powershell/gremlin/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crie uma conta, um banco de dados e um grafo do Azure Cosmos com dimensionamento automático. |
|[Listar ou obter bancos de dados ou grafos](scripts/powershell/gremlin/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Liste ou obtenha um banco de dados ou um grafo. |
|[Obter taxa de transferência](scripts/powershell/gremlin/throughput-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Obtenha a taxa de transferência de um banco de dados ou grafo. |
|[Atualizar a taxa de transferência](scripts/powershell/gremlin/throughput-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Atualize a taxa de transferência de um banco de dados ou grafo. |
|[Bloquear recursos da exclusão](scripts/powershell/gremlin/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Impedir que os recursos sejam excluídos com bloqueios de recursos. |
|||

## <a name="table-api-samples"></a>Exemplos de API de Tabela

|Tarefa | Descrição |
|---|---|
|[Criar uma conta e uma tabela](scripts/powershell/table/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma conta e uma tabela do Azure Cosmos. |
|[Criar uma conta e uma tabela com dimensionamento automático](scripts/powershell/table/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Crie uma conta e uma tabela do Azure Cosmos com dimensionamento automático. |
|[Listar ou obter tabelas](scripts/powershell/table/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Liste ou obtenha tabelas. |
|[Obter taxa de transferência](scripts/powershell/table/throughput-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Obtenha a taxa de transferência de uma tabela. |
|[Atualizar a taxa de transferência](scripts/powershell/table/throughput-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Atualize a taxa de transferência de uma tabela. |
|[Bloquear recursos da exclusão](scripts/powershell/table/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Impedir que os recursos sejam excluídos com bloqueios de recursos. |
|||
