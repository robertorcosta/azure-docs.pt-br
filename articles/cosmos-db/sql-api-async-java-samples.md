---
title: 'O Azure Cosmos DB: Exemplos de Java Assíncrono para a API do SQL'
description: Encontre exemplos de Async Java no GitHub para tarefas comuns usando a API do SQL do Azure Cosmos DB, incluindo as operações do CRUD.
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: sample
ms.date: 12/03/2018
ms.author: sngun
ms.openlocfilehash: 0a9b58a78ee9de48b721511646728bd8140ef980
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170172"
---
# <a name="azure-cosmos-db-async-java-examples-for-the-sql-api"></a>O Azure Cosmos DB: Exemplos de Java Assíncrono para a API do SQL

> [!div class="op_single_selector"]
> * [Exemplos de SDK do .NET V2](sql-api-dotnet-samples.md)
> * [Exemplos de SDK do .NET V3](sql-api-dotnet-v3sdk-samples.md)
> * [Exemplos de Java](sql-api-java-samples.md)
> * [Exemplos de Async Java](sql-api-async-java-samples.md)
> * [Exemplos do Node.js](sql-api-nodejs-samples.md)
> * [Exemplos do Python](sql-api-python-samples.md)
> * [Galeria de Exemplos de Código do Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Os aplicativos de exemplo mais recentes que executam operações CRUD e outras operações comuns nos recursos do Azure Cosmos DB estão incluídos no repositório GitHub [azure-comsosdb-java](https://github.com/Azure/azure-cosmosdb-java). Esse artigo fornece:

* Links para as tarefas em cada um dos arquivos de exemplo do projeto de Async Java. 
* Links para o conteúdo de referência da API relacionada.

**Pré-requisitos**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- Você pode [ativar benefícios de assinante do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Todos os meses, sua Assinatura do Visual Studio lhe oferece créditos que podem ser usados para serviços pagos do Azure.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Para executar esse aplicativo de exemplo, serão necessários:

* Java Development Kit 8
* Java SDK do Microsoft Azure Cosmos DB

Opcionalmente, é possível utilizar o Maven para obter os últimos binários do Java SDK do Microsoft Azure Cosmos DB para usar em seu projeto. Você pode escolher a versão mais recente [aqui](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb). O Maven adiciona automaticamente todas as dependências necessárias. Caso contrário, você poderá baixar diretamente as dependências listadas no arquivo pom.xml e adicioná-las ao seu caminho de compilação.

```bash
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-cosmosdb</artifactId>
    <version>${LATEST}</version>
</dependency>
```

**Executar aplicativos de exemplo**

Clone o repositório do exemplo:
```bash
$ git clone https://github.com/Azure/azure-cosmosdb-java.git

$ cd azure-cosmosdb-java
```

Você pode executar os exemplos, utilizando o Eclipse ou a partir da linha de comando usando o Maven.

Para executar do Eclipse:
* Carregue o arquivo pom.xml do projeto principal no Eclipse. Ele deverá carregar azure-cosmosdb-examples automaticamente.
* Para executar os exemplos, será necessário um ponto de extremidade do Azure Cosmos válido. Os pontos de extremidade são lidos do `src/test/java/com/microsoft/azure/cosmosdb/rx/examples/TestConfigurations.java`.
* É possível passar suas credenciais de ponto de extremidade como Argumentos de VM na Configuração de Execução JUnit do Eclipse ou colocar suas credenciais de ponto de extremidade em TestConfigurations.java.
    ```bash
    -DACCOUNT_HOST=<Fill your Azure Cosmos DB account name> -DACCOUNT_KEY=<Fill your Azure Cosmos DB primary key>
    ```
* Agora você pode executar os exemplos como testes JUnit no Eclipse.

Executar a partir da linha de comando:
* A outra maneira de executar exemplos é usar Maven:
* Execute o Maven e passe suas credenciais do Ponto de Extremidade do Azure Cosmos DB:
    ```bash
    mvn test -DACCOUNT_HOST=<Fill your Azure Cosmos DB account name> -DACCOUNT_KEY=<Fill your Azure Cosmos DB primary key>
    ```

   > [!NOTE]
   > Cada exemplo é autossuficiente, eles se configuram e fazem a limpeza sozinhos. Os exemplos emitem várias chamadas para [DocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createcollection). Cada vez que isso é feito, sua assinatura é cobrada por 1 hora de uso por nível de desempenho da coleção criada. 
   > 
   > 

## <a name="database-examples"></a>Exemplos de banco de dados
O arquivo [DatabaseCrudAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java) mostra como executar as tarefas a seguir. Para saber mais sobre os bancos de dados Azure Cosmos antes de executar os exemplos a seguir, veja o artigo conceitual [Trabalhando com bancos de dados, contêineres e itens](databases-containers-items.md). 

| Tarefa | Referência de API |
| --- | --- |
| [Criar um banco de dados](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java#L115-L132) | [AsyncDocumentClient.createDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createdatabase) |
| [Falha ao criar um banco de dados que já existe](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java#L189-L204) | |
| [Criar e ler um banco de dados](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java#L228-L249) | [AsyncDocumentClient.readDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.readdatabase) |
| [Criar e excluir um banco de dados](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java#L255-L276) | [AsyncDocumentClient.deleteDatabase](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.deletedatabase) |
| [Criar e consultar um banco de dados](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DatabaseCRUDAsyncAPITest.java#L282-L312) | [AsyncDocumentClient.queryDatabases](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.querydatabases) |

## <a name="collection-examples"></a>Exemplos de coleção
O arquivo [CollectionCrudAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java) mostra como executar as tarefas a seguir. Para saber mais sobre as coleções do Azure Cosmos antes de executar os exemplos a seguir, veja o artigo conceitual [Trabalhando com bancos de dados, contêineres e itens](databases-containers-items.md). 

| Tarefa | Referência de API |
| --- | --- |
| [Criar uma coleção de partição única](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L134-L153) | [AsyncDocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createcollection) |
| [Criar uma coleção personalizada de várias partições](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L164-L184) | [DocumentCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.documentcollection)<br>[PartitionKeyDefinition](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.partitionkeydefinition)<br>[RequestOptions](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.requestoptions) |
| [Falha ao criar uma coleção que já existe](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L241-L256) | |
| [Criar e ler uma coleção](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L281-L304) | [AsyncDocumentClient.readCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.readcollection) |
| [Criar e excluir uma coleção](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L310-L333) | [AsyncDocumentClient.deleteCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.deletecollection) |
| [Criar e consultar uma coleção](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L339-L372) | [AsyncDocumentClient.queryCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.querycollections) |

## <a name="document-examples"></a>Exemplos de documento
O arquivo [DocumentCrudAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java) mostra como executar as tarefas a seguir. Para saber mais sobre os documentos do Azure Cosmos antes de executar os exemplos a seguir, veja o artigo conceitual [Trabalhando com bancos de dados, contêineres e itens](databases-containers-items.md). 

| Tarefa | Referência de API |
| --- | --- |
| [Criar um documento](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L137-L156) | [AsyncDocumentClient.createDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createdocument) |
| [Criar um documento com uma definição de documento programável](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L213-L242) | [Documento](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.document)<br>[Resource.setId](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.resource.setid) |
| [Criar documentos e localizar o custo total de RU](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L248-L280) | [ResourceResponse.getRequestCharge](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.resourceresponse.getrequestcharge) |
| [Falha ao criar um documento que já existe](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L316-L336) | |
| [Criar e substituir um documento](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L342-L365) | [AsyncDocumentClient.replaceDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.replacedocument) |
| [Criar e fazer upsert de um documento](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L371-L393) | [AsyncDocumentClient.upsertDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.upsertdocument) |
| [Criar e excluir um documento](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L399-L431) | [AsyncDocumentClient.deleteDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.deletedocument) |
| [Criar e ler um documento](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentCRUDAsyncAPITest.java#L437-L458) | [AsyncDocumentClient.readDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.readdocument) |

## <a name="indexing-examples"></a>Exemplos de indexação
O arquivo [CollectionCrudAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java) mostra como executar as tarefas a seguir.  Para saber mais sobre a indexação no Azure Cosmos DB antes de executar os exemplos a seguir, confira os artigos conceituais [políticas de indexação](index-policy.md), [tipos de indexação](index-types.md) e [caminhos de indexação](index-paths.md). 

| Tarefa | Referência de API |
| --- | --- |
| [Criar um índice e definir política de indexação](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/CollectionCRUDAsyncAPITest.java#L394-L410) | [Índice](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.index)<br>[IndexingPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.indexingpolicy) |

Para obter mais informações sobre indexação, consulte [Políticas de indexação do Azure Cosmos DB](index-policy.md).

## <a name="query-examples"></a>Exemplos de consulta
O arquivo [DocumentQuerySamples](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java) mostra como executar as tarefas a seguir. Para saber mais sobre a referência de consulta SQL no Azure Cosmos DB antes de executar os exemplos a seguir, confira o artigo conceitual [Exemplos de consulta SQL](how-to-sql-query.md). 

| Tarefa | Referência de API |
| --- | --- |
| [Executar uma consulta simples do documento](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java#L154-L190) | [AsyncDocumentClient.queryDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.querydocuments) |
| [Executar uma consulta simples de documento e localizar o custo total de RU](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java#L249-L268) | [FeedResponse.getRequestCharge](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.feedresponse.getrequestcharge) |
| [Executar uma consulta simples de documento, ler uma página e cancelar a inscrição do observável retornado](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java#L274-L312) | |
| [Executar uma consulta simples de documento e filtrar os resultados](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java#L318-L368) | |
| [Executar uma consulta de documento de partição cruzada por ordem](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/DocumentQueryAsyncAPITest.java#L410-L457) | [FeedOptions.setEnableCrossPartitionQuery](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.feedoptions.setenablecrosspartitionquery) |

Para obter mais informações sobre como escrever consultas, confia [Consulta SQL no Azure Cosmos DB](how-to-sql-query.md).

## <a name="offer-examples"></a>Exemplos de oferta
O arquivo [OfferCRUDAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/OfferCRUDAsyncAPITest.java) mostra como executar as tarefas a seguir:

| Tarefa | Referência de API |
| --- | --- |
| [Criar uma coleção e definir taxa de transferência](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/OfferCRUDAsyncAPITest.java#L106-L113) | [AsyncDocumentClient.createCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createcollection)<br>[RequestOptions.setOfferThroughput](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.requestoptions.setofferthroughput) |
| [Ler uma coleção para localizar oferta associada](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/OfferCRUDAsyncAPITest.java#L118-L130) | [Offer.getContent](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.offer.getContent)<br>[DocumentClient.queryOffers](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.queryoffers) |
| [Atualizar a taxa de transferência de uma coleção, substituindo sua oferta](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/OfferCRUDAsyncAPITest.java#L101-L153) | [AsyncDocumentClient.replaceOffer](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.replaceoffer) |

## <a name="stored-procedure-examples"></a>Exemplos de procedimentos armazenados
O arquivo [StoredProcedureAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/StoredProcedureAsyncAPITest.java) mostra como executar as tarefas a seguir. Para saber mais sobre a programação do lado do servidor no Azure Cosmos DB antes de executar os exemplos a seguir, confira o artigo conceitual [Procedimentos armazenados, gatilhos e funções definidas pelo usuário](stored-procedures-triggers-udfs.md). 

| Tarefa | Referência de API |
| --- | --- |
| [Criar e executar um procedimento armazenado](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/StoredProcedureAsyncAPITest.java#L108-L155) | [StoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.storedprocedure)<br>[DocumentClient.createStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.createstoredprocedure)<br>[AsyncDocumentClient.executeStoredProcedure](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient.executestoredprocedure) |
| [Criar e executar um procedimento armazenado com argumentos](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/StoredProcedureAsyncAPITest.java#L161-L195) | |
| [Criar e executar um procedimento armazenado com um argumento de objeto](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/StoredProcedureAsyncAPITest.java#L201-L241) | |

## <a name="unique-key"></a>Chave exclusiva
O arquivo [UniqueIndexAsyncAPITest](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/UniqueIndexAsyncAPITest.java) mostra como executar as tarefas a seguir. Para saber mais sobre as chaves exclusivas no Azure Cosmos DB antes de executar os exemplos a seguir, veja o artigo conceitual [Restrições de chaves exclusivas](unique-keys.md). 

| Tarefa | Referência de API |
| --- | --- |
| [Criar uma coleção com uma chave exclusiva](https://github.com/Azure/azure-cosmosdb-java/blob/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples/UniqueIndexAsyncAPITest.java#L65-L97) | [UniqueKey](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.uniquekey)<br>[UniqueKeyPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.uniquekeypolicy)<br>[DocumentCollection.setUniqueKeyPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.documentcollection.setuniquekeypolicy) |
