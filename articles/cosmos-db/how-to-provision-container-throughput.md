---
title: Provisionar taxa de transferência de contêiner no Azure Cosmos DB
description: Aprenda a provisionar o throughput no nível de contêiner no Azure Cosmos DB usando o portal Azure, CLI, PowerShell e vários outros SDKs.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: mjbrown
ms.openlocfilehash: e416501cb3c532b3ba0a262442b35b236875a463
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273288"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>Provisionar a taxa de transferência em um contêiner do Azure Cosmos

Este artigo explica como provisionar a taxa de transferência em um contêiner (coleção, grafo, tabela) no Azure Cosmos DB. Você pode provisionramento de throughput em um único contêiner ou [provisionamento de throughput em um banco de dados](how-to-provision-database-throughput.md) e compartilhá-lo entre os contêineres dentro do banco de dados. É possível provisionar a taxa de transferência em um contêiner usando o portal do Azure, a CLI do Azure ou SDKs do Azure Cosmos DB.

## <a name="azure-portal"></a>Portal do Azure

1. Faça login no [portal Azure](https://portal.azure.com/).

1. [Crie uma conta do Azure Cosmos](create-sql-api-dotnet.md#create-account) ou selecione uma existente.

1. Abra o painel **data explorer** e selecione **Nova coleção**. Em seguida, forneça os seguintes detalhes:

   * Indique se você está criando um banco de dados ou usando um existente.
   * Insira uma ID de contêiner (ou de tabela ou de grafo).
   * Insira um valor de chave de partição (por exemplo, `/userid`).
   * Insira uma taxa de transferência que você deseja provisionar (por exemplo, 1.000 RUs).
   * Selecione **OK**.

    ![Captura de tela do Data Explorer, com Nova coleção realçado](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="azure-cli-or-powershell"></a>Azure CLI ou PowerShell

Para criar um contêiner com throughput dedicado ver,

* [Criar um contêiner usando a CLI do Azure](manage-with-cli.md#create-a-container)
* [Crie um recipiente usando o Powershell](manage-with-powershell.md#create-container)

> [!Note]
> Se você estiver provisionando a taxa de transferência em um contêiner em uma conta do Azure Cosmos configurada com a API do Azure Cosmos DB para MongoDB, use `/myShardKey` para o caminho da chave de partição. Se você estiver provisionando a taxa de transferência em um contêiner em uma conta do Azure Cosmos configurada com a API do Cassandra, use `/myPrimaryKey` para o caminho da chave de partição.

## <a name="net-sdk"></a>SDK .NET

> [!Note]
> Use a API dos SDKs do Cosmos para SQL para provisionar a taxa de transferência de todas as APIs do Cosmos DB, exceto da API do Cassandra.

### <a name="sql-mongodb-gremlin-and-table-apis"></a><a id="dotnet-most"></a>SQL, MongoDB, Gremlin e APIs de Tabela
### <a name="net-v2-sdk"></a>SDK do .NET V2

```csharp
// Create a container with a partition key and provision throughput of 400 RU/s
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 400 });
```

### <a name="net-v3-sdk"></a>SDK do .NET V3

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs?name=ContainerCreateWithThroughput)]

## <a name="javascript-sdk"></a>SDK do JavaScript

```javascript
// Create a new Client
const client = new CosmosClient({ endpoint, key });

// Create a database
const { database } = await client.databases.createIfNotExists({ id: "databaseId" });

// Create a container with the specified throughput
const { resource } = await database.containers.createIfNotExists({
id: "containerId",
throughput: 1000
});

// To update an existing container or databases throughput, you need to user the offers API
// Get all the offers
const { resources: offers } = await client.offers.readAll().fetchAll();

// Find the offer associated with your container or the database
const offer = offers.find((_offer) => _offer.offerResourceId === resource._rid);

// Change the throughput value
offer.content.offerThroughput = 2000;

// Replace the offer.
await client.offer(offer.id).replace(offer);
```

### <a name="cassandra-api"></a><a id="dotnet-cassandra"></a>Cassandra API

Comandos semelhantes podem ser emitidos através de qualquer driver compatível com CQL.

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute("CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400");

```
### <a name="alter-or-change-throughput-for-cassandra-table"></a>Alterar ou alterar o throughput para a tabela de Cassandra

```csharp
// Altering the throughput too can be done through code by issuing following command
session.Execute("ALTER TABLE myKeySpace.myTable WITH cosmosdb_provisioned_throughput=5000");
```


## <a name="next-steps"></a>Próximas etapas

Confira os seguintes artigos para saber mais sobre o provisionamento de taxa de transferência no Azure Cosmos DB:

* [Como provisionar a taxa de transferência em um banco de dados](how-to-provision-database-throughput.md)
* [Unidades de solicitação e taxa de transferência no Azure Cosmos DB](request-units.md)
