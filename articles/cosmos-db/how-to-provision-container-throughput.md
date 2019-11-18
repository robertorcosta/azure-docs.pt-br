---
title: Provisionar taxa de transferência de contêiner no Azure Cosmos DB
description: Saiba como provisionar a taxa de transferência no nível do contêiner no Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 0b48652f7b181f1254a4b20af75b83593c2aba05
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74147599"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>Provisionar a taxa de transferência em um contêiner do Azure Cosmos

Este artigo explica como provisionar a taxa de transferência em um contêiner (coleção, grafo, tabela) no Azure Cosmos DB. É possível provisionar a taxa de transferência em um único contêiner ou [provisionar a taxa de transferência em um banco de dados](how-to-provision-database-throughput.md) e compartilhá-la entre os contêineres no banco de dados. É possível provisionar a taxa de transferência em um contêiner usando o portal do Azure, a CLI do Azure ou SDKs do Azure Cosmos DB.

## <a name="azure-portal"></a>Portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com/).

1. [Crie uma conta do Azure Cosmos](create-sql-api-dotnet.md#create-account) ou selecione uma existente.

1. Abra o painel **Data Explorer** e selecione **Nova Coleção**. Em seguida, forneça os seguintes detalhes:

   * Indique se você está criando um banco de dados ou usando um existente.
   * Insira uma ID de contêiner (ou de tabela ou de grafo).
   * Insira um valor de chave de partição (por exemplo, `/userid`).
   * Insira uma taxa de transferência que você deseja provisionar (por exemplo, 1.000 RUs).
   * Selecione **OK**.

    ![Captura de tela do Data Explorer, com Nova coleção realçado](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="azure-cli-or-powershell"></a>CLI do Azure ou PowerShell

Para criar um contêiner com taxa de transferência dedicada, consulte

* [Criar um contêiner usando CLI do Azure](manage-with-cli.md#create-a-container)
* [Criar um contêiner usando o PowerShell](manage-with-powershell.md#create-container)

> [!Note]
> Se você estiver provisionando a taxa de transferência em um contêiner em uma conta do Azure Cosmos configurada com a API do Azure Cosmos DB para MongoDB, use `/myShardKey` para o caminho da chave de partição. Se você estiver provisionando a taxa de transferência em um contêiner em uma conta do Azure Cosmos configurada com a API do Cassandra, use `/myPrimaryKey` para o caminho da chave de partição.

## <a name="net-sdk"></a>SDK .NET

> [!Note]
> Use a API dos SDKs do Cosmos para SQL para provisionar a taxa de transferência de todas as APIs do Cosmos DB, exceto da API do Cassandra.

### <a id="dotnet-most"></a>SQL, MongoDB, Gremlin e APIs de Tabela
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
id: "contaierId ",
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

### <a id="dotnet-cassandra"></a>API do Cassandra

Comandos semelhantes podem ser emitidos por meio de qualquer driver compatível com CQL.

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute("CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400");

```
### <a name="alter-or-change-throughput-for-cassandra-table"></a>Alterar ou alterar a taxa de transferência para a tabela Cassandra

```csharp
// Altering the throughput too can be done through code by issuing following command
session.Execute("ALTER TABLE myKeySpace.myTable WITH cosmosdb_provisioned_throughput=5000");
```


## <a name="next-steps"></a>Próximas etapas

Confira os seguintes artigos para saber mais sobre o provisionamento de taxa de transferência no Azure Cosmos DB:

* [Como provisionar a taxa de transferência em um banco de dados](how-to-provision-database-throughput.md)
* [Unidades de solicitação e taxa de transferência no Azure Cosmos DB](request-units.md)
