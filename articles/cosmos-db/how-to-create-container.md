---
title: Criar um contêiner no Azure Cosmos DB
description: Aprenda a criar um contêiner no Azure Cosmos DB usando o portal Azure, .Net, Java, Python, Node.js e outros SDKs.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 4eaa2974817bfcd8bef83e5139d75a2d4c2ec107
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873702"
---
# <a name="create-an-azure-cosmos-container"></a>Criar um contêiner do Azure Cosmos

Este artigo explica as diferentes maneiras de criar um contêiner do Azure Cosmos (coleção, tabela ou grafo). Para esse fim, você pode usar o portal do Azure, a CLI do Azure ou SDKs compatíveis. Este artigo demonstra como criar um contêiner, especificar a chave de partição e a taxa de transferência de provisionamento.

## <a name="create-a-container-using-azure-portal"></a>Criar um contêiner usando o portal do Azure

### <a name="sql-api"></a><a id="portal-sql"></a>SQL API

1. Faça login no [portal Azure](https://portal.azure.com/).

1. [Crie uma conta do Azure Cosmos](create-sql-api-dotnet.md#create-account) ou selecione uma existente.

1. Abra o painel **data explorer** e selecione **Novo contêiner**. Em seguida, forneça os seguintes detalhes:

   * Indique se você está criando um banco de dados ou usando um existente.
   * Digite um id de contêiner.
   * Insira uma chave de partição.
   * Insira uma taxa de transferência a ser provisionada (por exemplo, 1.000 RUs).
   * Selecione **OK**.

    ![Captura de tela do painel Data Explorer, com novo contêiner destacado](./media/how-to-create-container/partitioned-collection-create-sql.png)

### <a name="azure-cosmos-db-api-for-mongodb"></a><a id="portal-mongodb"></a>API do Azure Cosmos DB para MongoDB

1. Faça login no [portal Azure](https://portal.azure.com/).

1. [Crie uma nova conta do Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account)ou selecione uma conta existente.

1. Abra o painel **data explorer** e selecione **Novo contêiner**. Em seguida, forneça os seguintes detalhes:

   * Indique se você está criando um banco de dados ou usando um existente.
   * Digite um id de contêiner.
   * Insira uma chave de fragmento.
   * Insira uma taxa de transferência a ser provisionada (por exemplo, 1.000 RUs).
   * Selecione **OK**.

    ![Captura de tela da API Azure Cosmos DB para MongoDB, Adicionar caixa de diálogo contêiner](./media/how-to-create-container/partitioned-collection-create-mongodb.png)

### <a name="cassandra-api"></a><a id="portal-cassandra"></a>Cassandra API

1. Faça login no [portal Azure](https://portal.azure.com/).

1. [Crie uma nova conta do Azure Cosmos](create-cassandra-dotnet.md#create-a-database-account)ou selecione uma conta existente.

1. Abra o painel **data explorer** e selecione **Nova tabela**. Em seguida, forneça os seguintes detalhes:

   * Indique se você está criando um keyspace ou usando um existente.
   * Insira um nome de tabela.
   * Insira as propriedades e especifique uma chave primária.
   * Insira uma taxa de transferência a ser provisionada (por exemplo, 1.000 RUs).
   * Selecione **OK**.

    ![Captura de tela da API do Cassandra, caixa de diálogo Adicionar tabela](./media/how-to-create-container/partitioned-collection-create-cassandra.png)

> [!NOTE]
> Para a API do Cassandra, a chave primária é usada como a chave de partição.

### <a name="gremlin-api"></a><a id="portal-gremlin"></a>API do Gremlin

1. Faça login no [portal Azure](https://portal.azure.com/).

1. [Crie uma nova conta do Azure Cosmos](create-graph-dotnet.md#create-a-database-account)ou selecione uma conta existente.

1. Abra o painel **data explorer** e selecione **Novo gráfico**. Em seguida, forneça os seguintes detalhes:

   * Indique se você está criando um banco de dados ou usando um existente.
   * Insira uma ID do grafo.
   * Selecione a capacidade de armazenamento **Ilimitada**.
   * Insira uma chave de partição para vértices.
   * Insira uma taxa de transferência a ser provisionada (por exemplo, 1.000 RUs).
   * Selecione **OK**.

    ![Captura de tela da API do Gremlin, caixa de diálogo Adicionar grafo](./media/how-to-create-container/partitioned-collection-create-gremlin.png)

### <a name="table-api"></a><a id="portal-table"></a>API de Tabela

1. Faça login no [portal Azure](https://portal.azure.com/).

1. [Crie uma nova conta do Azure Cosmos](create-table-dotnet.md#create-a-database-account)ou selecione uma conta existente.

1. Abra o painel **data explorer** e selecione **Nova tabela**. Em seguida, forneça os seguintes detalhes:

   * Insira uma ID da tabela.
   * Insira uma taxa de transferência a ser provisionada (por exemplo, 1.000 RUs).
   * Selecione **OK**.

    ![Captura de tela da API de Tabela, caixa de diálogo Adicionar tabela](./media/how-to-create-container/partitioned-collection-create-table.png)

> [!Note]
> Para a API de Tabela, a chave de partição é especificada sempre que você adiciona uma nova linha.

## <a name="create-a-container-using-azure-cli"></a>Crie um contêiner usando o Azure CLI<a id="cli-sql"></a><a id="cli-mongodb"></a><a id="cli-cassandra"></a><a id="cli-gremlin"></a><a id="cli-table"></a>

Os links abaixo mostram como criar recursos de contêiner para o Azure Cosmos DB usando o Azure CLI.

Para uma listagem de todas as amostras de Azure CLI em todas as APIs do Azure Cosmos DB consulte, [SQL API](cli-samples.md), [Cassandra API,](cli-samples-cassandra.md) [MongoDB API,](cli-samples-mongodb.md) [Gremlin API](cli-samples-gremlin.md)e [Table API](cli-samples-table.md)

* [Crie um contêiner com a Cli do Azure](manage-with-cli.md#create-a-container)
* [Crie uma coleção para Azure Cosmos DB para API MongoDB com Azure CLI](./scripts/cli/mongodb/create.md)
* [Crie uma tabela Cassandra com a Cli do Azure](./scripts/cli/cassandra/create.md)
* [Crie um gráfico Gremlin com a Cli do Azure](./scripts/cli/gremlin/create.md)
* [Crie uma tabela de API de tabela com o Azure CLI](./scripts/cli/table/create.md)

## <a name="create-a-container-using-powershella-idps-mongodba-idps-gremlin"></a>Crie um contêiner usando o PowerShell<a id="ps-sql"></a><a id="ps-mongodb"><a id="ps-cassandra"></a><a id="ps-gremlin"><a id="ps-table"></a>

Os links abaixo mostram como criar recursos de contêiner para o Azure Cosmos DB usando o PowerShell.

Para uma listagem de todas as amostras de Azure CLI em todas as APIs do Azure Cosmos DB consulte, [SQL API](powershell-samples-sql.md), [Cassandra API,](powershell-samples-cassandra.md) [MongoDB API,](powershell-samples-mongodb.md) [Gremlin API](powershell-samples-gremlin.md)e [Table API](powershell-samples-table.md)

* [Crie um recipiente com powershell](manage-with-powershell.md#create-container)
* [Crie uma coleção para Azure Cosmos DB para API MongoDB com Powershell](./scripts/powershell/mongodb/ps-mongodb-create.md)
* [Crie uma tabela Cassandra com powershell](./scripts/powershell/cassandra/ps-cassandra-create.md)
* [Crie um gráfico Gremlin com powershell](./scripts/powershell/gremlin/ps-gremlin-create.md)
* [Crie uma tabela de API de tabela com o Powershell](./scripts/powershell/table/ps-table-create.md)

## <a name="create-a-container-using-net-sdk"></a>Criar um contêiner usando o SDK do .NET

### <a name="sql-api-and-gremlin-api"></a><a id="dotnet-sql-graph"></a>API de SQL e API do Gremlin

```csharp
// Create a container with a partition key and provision 1000 RU/s throughput.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

### <a name="azure-cosmos-db-api-for-mongodb"></a><a id="dotnet-mongodb"></a>API do Azure Cosmos DB para MongoDB

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> O protocolo de transmissão do MongoDB não compreende o conceito de [Unidades de Solicitação](request-units.md). Para criar uma coleção contendo taxa de transferência provisionada, use o portal do Azure ou os SDKs do Cosmos DB para a API do SQL.

### <a name="cassandra-api"></a><a id="dotnet-cassandra"></a>Cassandra API

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>Próximas etapas

* [Particionamento no BD Cosmos do Azure](partitioning-overview.md)
* [Unidades de solicitação no Azure Cosmos DB](request-units.md)
* [Provisionar a taxa de transferência para contêineres e bancos de dados](set-throughput.md)
* [Como trabalhar com a conta do Azure Cosmos](account-overview.md)
