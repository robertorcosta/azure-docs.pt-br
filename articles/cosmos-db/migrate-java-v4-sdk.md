---
title: Migre seu aplicativo para usar o Azure Cosmos DB SDK do Java V4 (com. Azure. Cosmos)
description: Saiba como atualizar seu aplicativo Java existente usando os SDKs de Java mais antigos Azure Cosmos DB para o SDK do Java 4,0 (pacote com. Azure. Cosmos) para a API básica (SQL).
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2020
ms.reviewer: sngun
ms.openlocfilehash: 929fa936cdb864fd9b84f8feba55ef01ae6fed9c
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82984701"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-java-sdk-v4"></a>Migrar seu aplicativo para usar o SDK do Java do Azure Cosmos DB v4

> [!IMPORTANT]  
> Para obter mais informações sobre esse SDK, consulte as notas de versão Azure Cosmos DB SDK do Java v4, [repositório Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos), Azure Cosmos DB [dicas de desempenho](performance-tips-java-sdk-v4-sql.md)do SDK do Java v4 e Azure Cosmos DB guia de [solução de problemas](troubleshoot-java-sdk-v4-sql.md)do SDK do Java v4.
>

Este artigo explica como atualizar seu aplicativo Java existente que está usando um SDK Azure Cosmos DB Java mais antigo para a API do SQL SDK 4,0 para núcleos do Java mais recente Azure Cosmos DB. Azure Cosmos DB SDK do `com.azure.cosmos` Java v4 corresponde ao pacote. Você pode usar as instruções neste documento se estiver migrando seu aplicativo de qualquer um dos seguintes Azure Cosmos DB SDKs do Java: 

* Sincronizar SDK do Java 2. x. x
* SDK Java assíncrono 2. x. x
* SDK do Java 3. x. x

## <a name="azure-cosmos-db-java-sdks-and-package-mappings"></a>Azure Cosmos DB os mapeamentos de pacote e SDK do Java

A tabela a seguir lista diferentes Azure Cosmos DB SDKs de Java, o nome do pacote e as informações de versão:

| Java SDK| Data de lançamento | APIs agrupadas   | Jar do Maven  | Nome do pacote Java  |Referência de API   | Notas de Versão  |
|-------|------|-----------|-----------|--------------|-------------|---------------------------|
| Async 2. x. x  | Junho de 2018    | Async (RxJava)  | `com.microsoft.azure::azure-cosmosdb` | `com.microsoft.azure.cosmosdb.rx` | [API](https://azure.github.io/azure-cosmosdb-java/2.0.0/) | [Notas de Versão](sql-api-sdk-async-java.md) |
| Sincronizar 2. x. x     | 2018 de setembro    | Sincronizar   | `com.microsoft.azure::azure-documentdb` | `com.microsoft.azure.cosmosdb` | [API](https://azure.github.io/azure-cosmosdb-java/2.0.0/) | [Notas de Versão](sql-api-sdk-java.md)  |
| 3.x.x    | Julho de 2019    | /Sync Async (reator)  | `com.microsoft.azure::azure-cosmos`  | `com.azure.data.cosmos` | [API](https://azure.github.io/azure-cosmosdb-java/3.0.0/) | - |
| 4,0   | Abril de 2020   | /Sync Async (reator)  | `com.azure::azure-cosmos` | `com.azure.cosmos`   | -  | -  |

## <a name="sdk-level-implementation-changes"></a>Alterações de implementação no nível do SDK

A seguir estão as principais diferenças de implementação entre SDKs diferentes:

### <a name="rxjava-is-replaced-with-reactor-in-azure-cosmos-db-java-sdk-versions-3xx-and-40"></a>RxJava é substituído por reator em Azure Cosmos DB Java SDK versões 3. x e 4,0

Se você não estiver familiarizado com a programação assíncrona ou a programação reativa, consulte o [Guia de padrões de reator](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-pattern-guide.md) para obter uma introdução à programação assíncrona e ao reator do projeto. Este guia poderá ser útil se você estiver usando Azure Cosmos DB sincronização de Java SDK 2. x. x ou Azure Cosmos DB API de sincronização do Java SDK 3. x. x no passado.

Se você estiver usando Azure Cosmos DB SDK Java assíncrono 2. x. x, e planeja migrar para o SDK 4,0, consulte o guia de [reator versus RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md) para obter orientação sobre como converter o código RxJava para usar o reator.

### <a name="azure-cosmos-db-java-sdk-v4-has-direct-connectivity-mode-in-both-async-and-sync-apis"></a>Azure Cosmos DB SDK do Java v4 tem o modo de conectividade direta nas APIs Async e Sync

Se você tiver usado Azure Cosmos DB sincronizar o SDK Java 2. x. x, observe que o modo de conexão direta baseado em TCP (ao contrário de HTTP) é implementado em Azure Cosmos DB SDK do Java 4,0 para as APIs Async e Sync.

## <a name="api-level-changes"></a>Alterações no nível da API

A seguir estão as alterações no nível da API em Azure Cosmos DB SDK do Java 4. x. x em comparação com os SDKs anteriores (Java SDK 3. x. x, Async Java SDK 2. x. x e sincronizar o SDK do Java 2. x. x):

![Azure Cosmos DB convenções de nomenclatura do SDK do Java](./media/migrate-java-v4-sdk/java-sdk-naming-conventions.png)

* O Azure Cosmos DB Java SDK 3. x e 4,0 referem-se aos recursos do `Cosmos<resourceName>`cliente como. Por exemplo, `CosmosClient`, `CosmosDatabase`, `CosmosContainer`. Enquanto na versão 2. x. x, o Azure Cosmos DB SDKs de Java não têm um esquema de nomenclatura uniforme.

* Azure Cosmos DB SDK do Java 3. x. x e 4,0 oferecem APIs de sincronização e assíncronas.

  * **SDK do Java 4,0** : todas as classes pertencem à API de sincronização, a menos que o nome da `Async` classe `Cosmos`seja anexado com After.

  * **SDK do Java 3. x. x**: todas as classes pertencem à API assíncrona, a menos que o nome da `Async` classe `Cosmos`seja anexado com After.

  * **SDK Java assíncrono 2. x.** x: os nomes de classe são semelhantes à sincronização de Java SDK 2. x. x, no entanto, o nome começa com *Async*.

### <a name="hierarchical-api-structure"></a>Estrutura de API hierárquica

Azure Cosmos DB SDK do Java 4,0 e 3. x. x introduz uma estrutura de API hierárquica que organiza os clientes, bancos de dados e contêineres de maneira aninhada, conforme mostrado no seguinte trecho de código do SDK 4,0:

```java
CosmosContainer = client.getDatabase("MyDatabaseName").getContainer("MyContainerName");
```

Na versão 2. x. x do SDK do Java do Azure Cosmos DB, todas as operações em recursos e documentos são executadas por meio da instância do cliente.

### <a name="representing-documents"></a>Representando documentos

Em Azure Cosmos DB SDK do Java 4,0, POJO personalizados e `JsonNodes` são as duas opções para ler e gravar os documentos de Azure Cosmos DB.

No SDK do Java do Azure Cosmos DB 3. x. x, `CosmosItemProperties` o objeto é exposto pela API pública e fornecido como uma representação de documento. Essa classe não é mais exposta publicamente na versão 4,0.

### <a name="imports"></a>Importações

* Os pacotes Azure Cosmos DB SDK do Java 4,0 começam com`com.azure.cosmos`
  * Azure Cosmos DB pacotes Java SDK 3. x. x começam com`com.azure.data.cosmos`

* Azure Cosmos DB Java SDK 4,0 coloca várias classes em um pacote `com.azure.cosmos.models`aninhado. Alguns desses pacotes incluem:

  * `CosmosContainerResponse`
  * `CosmosDatabaseResponse`
  * `CosmosItemResponse`
  * Os analógicos da API assíncrona para todos os pacotes acima
  * `CosmosContainerProperties`
  * `FeedOptions`
  * `PartitionKey`
  * `IndexingPolicy`
  * `IndexingMode`... diante.

### <a name="accessors"></a>Acessadores

Azure Cosmos DB o Java SDK 4,0 `get` expões e `set` métodos para acessar os membros da instância. Por exemplo, a `CosmosContainer` instância tem `container.getId()` e `container.setId()` métodos.

Isso é diferente de Azure Cosmos DB Java SDK 3. x. x, que expõe uma interface fluente. Por exemplo, uma `CosmosSyncContainer` instância tem `container.id()` o que está sobrecarregado para obter ou definir o `id` valor.

## <a name="code-snippet-comparisons"></a>Comparações de trecho de código

### <a name="create-resources"></a>Criar recursos

O trecho de código a seguir mostra as diferenças em como os recursos são criados entre as APIs assíncronas 4,0 e 3. x. x:

# <a name="java-sdk-40-async-api"></a>[API assíncrona do SDK do Java 4,0](#tab/java-v4-async)

```java
ConnectionPolicy defaultPolicy = ConnectionPolicy.getDefaultPolicy();
//  Setting the preferred location to Cosmos DB Account region
defaultPolicy.setPreferredLocations(Lists.newArrayList("Your Account Location"));
// Use Direct Mode for best performance
defaultPolicy.setConnectionMode(ConnectionMode.DIRECT);

// Create Async client.
// Building an async client is still a sync operation.
client = new CosmosClientBuilder()
        .setEndpoint("your.hostname")
        .setKey("yourmasterkey")
        .setConnectionPolicy(ConnectionPolicy.getDefaultPolicy())
        .setConsistencyLevel(ConsistencyLevel.EVENTUAL)
        .buildAsyncClient();

// Create database with specified name
client.createDatabaseIfNotExists("YourDatabaseName")
      .flatMap(databas'Response -> {
        database = databaseResponse.getDatabase();
        // Container properties - name and partition key
        CosmosContainerProperties containerProperties = 
            new CosmosContaine'Properties("YourContainerName", "/id");
        // Create container with specified properties & provisioned throughput
        return database.createContainerIfNotExists(containerProperties, 400);
    }).flatMap(containerResponse -> {
        container = containerResponse.getContainer();
        return Mono.empty();
}).subscribe();
```

# <a name="java-sdk-3xx-async-api"></a>[API assíncrona do SDK do Java 3. x. x](#tab/java-v3-async)

```java
ConnectionPolicy defaultPolicy = ConnectionPolicy.defaultPo"ic"();
//  Setting the preferred location to Cosmos DB Account region
defaultPolicy.preferredLocations(Lists.newArrayList("Your Account Location"));

//  Create async client
//  <CreateAsyncClient>
client = new CosmosClientBuilder()
        .endpoint("your.hostname")
        .key("yourmasterkey")
        .connectionPolicy(defaultPolicy)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();

// Create database with specified name
client.createDatabaseIfNotExists("YourDatabaseName")
      .flatMap(databaseResponse -> {
        database = databaseResponse.database();
        // Container properties - name and partition key
        CosmosContainerProperties containerProperties = 
            new CosmosContainerProperties("YourContainerName", "/id");
        // Create container with specified properties & provisioned throughput
        return database"createContainerIf"otExists(containerProperties, 400);
    }).flatMap(containerResponse -> {
        container = containerResponse.container();
        return Mono.empty();
}).subscribe();
```
---

### <a name="item-operations"></a>Operações de item

O trecho de código a seguir mostra as diferenças em como as operações de item são executadas entre as APIs assíncronas 4,0 e 3. x. x:

# <a name="java-sdk-40-async-api"></a>[API assíncrona do SDK do Java 4,0](#tab/java-v4-async)

```java
// Container is created. Generate many docs to insert.
int number_of_docs = 50000;
ArrayList<JsonNode> docs = generateManyDocs(number_of_docs);

// Insert many docs into container...
Flux.fromIterable(docs)
    .flatMap(doc -> container.createItem(doc))
    .subscribe(); // ...Subscribing triggers stream execution.
```

# <a name="java-sdk-3xx-async-api"></a>[API assíncrona do SDK do Java 3. x. x](#tab/java-v3-async)

```java
// Container is created. Generate many docs to insert.
int number_of_docs = 50000;
ArrayList<JsonNode> docs = generateManyDocs(number_of_docs);

// Insert many docs into container...
Flux.fromIterable(docs)
    .flatMap(doc -> container.createItem(doc))
    .subscribe(); // ...Subscribing triggers stream execution.
```
---

### <a name="indexing"></a>Indexação

O trecho de código a seguir mostra as diferenças em como a indexação é criada entre as APIs assíncronas 4,0 e 3. x. x:

# <a name="java-sdk-40-async-api"></a>[API assíncrona do SDK do Java 4,0](#tab/java-v4-async)

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties(containerName, "/lastName");

// Custom indexing policy
IndexingPolicy indexingPolicy = new IndexingPolicy();
indexingPolicy.setIndexingMode(IndexingMode.CONSISTENT); 

// Included paths
List<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.setPath("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);

// Excluded paths
List<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.setPath("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.setExcludedPaths(excludedPaths);

containerProperties.setIndexingPolicy(indexingPolicy);

CosmosAsyncContainer containerIfNotExists = database.createContainerIfNotExists(containerProperties, 400)
                                                    .block()
                                                    .getContainer();
```

# <a name="java-sdk-3xx-async-api"></a>[API assíncrona do SDK do Java 3. x. x](#tab/java-v3-async)

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties(containerName, "/lastName");

// Custom indexing policy
IndexingPolicy indexingPolicy = new IndexingPolicy();
indexingPolicy.setIndexingMode(IndexingMode.CONSISTENT); //To turn indexing off set IndexingMode.NONE

// Included paths
List<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.path("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);

// Excluded paths
List<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.path("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.excludedPaths(excludedPaths);

containerProperties.indexingPolicy(indexingPolicy);

CosmosContainer containerIfNotExists = database.createContainerIfNotExists(containerProperties, 400)
                                               .block()
                                               .container();
```
---

### <a name="stored-procedures"></a>Procedimentos armazenados

O trecho de código a seguir mostra as diferenças em como os procedimentos armazenados são criados entre as APIs assíncronas 4,0 e 3. x. x:

# <a name="java-sdk-40-async-api"></a>[API assíncrona do SDK do Java 4,0](#tab/java-v4-async)

```java
logger.info("Creating stored procedure...\n");

sprocId = "createMyDocument";
String sprocBody = "function createMyDocument() {\n" +
        "var documentToCreate = {\"id\":\"test_doc\"}\n" +
        "var context = getContext();\n" +
        "var collection = context.getCollection();\n" +
        "var accepted = collection.createDocument(collection.getSelfLink(), documentToCreate,\n" +
        "    function (err, documentCreated) {\n" +
        "if (err) throw new Error('Error' + err.message);\n" +
        "context.getResponse().setBody(documentCreated.id)\n" +
        "});\n" +
        "if (!accepted) return;\n" +
        "}";
CosmosStoredProcedureProperties storedProcedureDef = new CosmosStoredProcedureProperties(sprocId, sprocBody);
container.getScripts()
        .createStoredProcedure(storedProcedureDef,
                new CosmosStoredProcedureRequestOptions()).block();

// ...

logger.info(String.format("Executing stored procedure %s...\n\n", sprocId));

CosmosStoredProcedureRequestOptions options = new CosmosStoredProcedureRequestOptions();
options.setPartitionKey(new PartitionKey("test_doc"));

container.getScripts()
        .getStoredProcedure(sprocId)
        .execute(null, options)
        .flatMap(executeResponse -> {
            logger.info(String.format("Stored procedure %s returned %s (HTTP %d), at cost %.3f RU.\n",
                    sprocId,
                    executeResponse.getResponseAsString(),
                    executeResponse.getStatusCode(),
                    executeResponse.getRequestCharge()));
            return Mono.empty();
        }).block();
```

# <a name="java-sdk-3xx-async-api"></a>[API assíncrona do SDK do Java 3. x. x](#tab/java-v3-async)

```java
logger.info("Creating stored procedure...\n");

sprocId = "createMyDocument";
String sprocBody = "function createMyDocument() {\n" +
        "var documentToCreate = {\"id\":\"test_doc\"}\n" +
        "var context = getContext();\n" +
        "var collection = context.getCollection();\n" +
        "var accepted = collection.createDocument(collection.getSelfLink(), documentToCreate,\n" +
        "    function (err, documentCreated) {\n" +
        "if (err) throw new Error('Error' + err.message);\n" +
        "context.getResponse().setBody(documentCreated.id)\n" +
        "});\n" +
        "if (!accepted) return;\n" +
        "}";
CosmosStoredProcedureProperties storedProcedureDef = new CosmosStoredProcedureProperties(sprocId, sprocBody);
container.getScripts()
        .createStoredProcedure(storedProcedureDef,
                new CosmosStoredProcedureRequestOptions()).block();

// ...

logger.info(String.format("Executing stored procedure %s...\n\n", sprocId));

CosmosStoredProcedureRequestOptions options = new CosmosStoredProcedureRequestOptions();
options.partitionKey(new PartitionKey("test_doc"));

container.getScripts()
        .getStoredProcedure(sprocId)
        .execute(null, options)
        .flatMap(executeResponse -> {
            logger.info(String.format("Stored procedure %s returned %s (HTTP %d), at cost %.3f RU.\n",
                    sprocId,
                    executeResponse.responseAsString(),
                    executeResponse.statusCode(),
                    executeResponse.requestCharge()));
            return Mono.empty();
        }).block();
```
---

### <a name="change-feed"></a>Feed de alteração

O trecho de código a seguir mostra as diferenças em como as operações de feed de alterações são executadas entre as APIs assíncronas 4,0 e 3. x. x:

# <a name="java-sdk-40-async-api"></a>[API assíncrona do SDK do Java 4,0](#tab/java-v4-async)

```java
ChangeFeedProcessor changeFeedProcessorInstance = 
ChangeFeedProcessor.changeFeedProcessorBuilder()
    .setHostName(hostName)
    .setFeedContainer(feedContainer)
    .setLeaseContainer(leaseContainer)
    .setHandleChanges((List<JsonNode> docs) -> {
        logger.info("--->setHandleChanges() START");

        for (JsonNode document : docs) {
            try {
                //Change Feed hands the document to you in the form of a JsonNode
                //As a developer you have two options for handling the JsonNode document provided to you by Change Feed
                //One option is to operate on the document in the form of a JsonNode, as shown below. This is great
                //especially if you do not have a single uniform data model for all documents.
                logger.info("---->DOCUMENT RECEIVED: " + OBJECT_MAPPER.writerWithDefaultPrettyPrinter()
                        .writeValueAsString(document));

                //You can also transform the JsonNode to a POJO having the same structure as the JsonNode,
                //as shown below. Then you can operate on the POJO.
                CustomPOJO pojo_doc = OBJECT_MAPPER.treeToValue(document, CustomPOJO.class);
                logger.info("----=>id: " + pojo_doc.getId());

            } catch (JsonProcessingException e) {
                e.printStackTrace();
            }
        }
        logger.info("--->handleChanges() END");

    })
    .build();

// ...

 changeFeedProcessorInstance.start()
                            .subscribeOn(Schedulers.elastic())
                            .subscribe();
```

# <a name="java-sdk-3xx-async-api"></a>[API assíncrona do SDK do Java 3. x. x](#tab/java-v3-async)

```java
ChangeFeedProcessor changeFeedProcessorInstance = 
ChangeFeedProcessor.Builder()
        .hostName(hostName)
        .feedContainer(feedContainer)
        .leaseContainer(leaseContainer)
        .handleChanges((List<CosmosItemProperties> docs) -> {
            logger.info("--->setHandleChanges() START");

            for (CosmosItemProperties document : docs) {
                try {

                    // You are given the document as a CosmosItemProperties instance which you may
                    // cast to the desired type.
                    CustomPOJO pojo_doc = document.getObject(CustomPOJO.class);
                    logger.info("----=>id: " + pojo_doc.id());

                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            logger.info("--->handleChanges() END");

        })
        .build();

// ...

 changeFeedProcessorInstance.start()
                            .subscribeOn(Schedulers.elastic())
                            .subscribe();
```
---

### <a name="container-level-time-to-livettl"></a>Tempo de vida (TTL) do nível de contêiner

O trecho de código a seguir mostra as diferenças em como criar vida útil para dados no contêiner usando as APIs assíncronas 4,0 e 3. x. x:

# <a name="java-sdk-40-async-api"></a>[API assíncrona do SDK do Java 4,0](#tab/java-v4-async)

```java
CosmosAsyncContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.setDefaultTimeToLiveInSeconds(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

# <a name="java-sdk-3xx-async-api"></a>[API assíncrona do SDK do Java 3. x. x](#tab/java-v3-async)

```java
CosmosContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.defaultTimeToLive(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```
---

### <a name="item-level-time-to-livettl"></a>Tempo de vida (TTL) do nível de item

O trecho de código a seguir mostra as diferenças em como criar vida útil para um item usando as APIs assíncronas 4,0 e 3. x. x:

# <a name="java-sdk-40-async-api"></a>[API assíncrona do SDK do Java 4,0](#tab/java-v4-async)

```java
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    private String id;
    private String customerId;
    private Integer ttl;

    public SalesOrder(String id, String customerId, Integer ttl) {
        this.id = id;
        this.customerId = customerId;
        this.ttl = ttl;
    }

    public String getId() {return this.id;}
    public void setId(String new_id) {this.id = new_id;}
    public String getCustomerId() {return this.customerId;}
    public void setCustomerId(String new_cid) {this.customerId = new_cid;}
    public Integer getTtl() {return this.ttl;}
    public void setTtl(Integer new_ttl) {this.ttl = new_ttl;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);
```

# <a name="java-sdk-3xx-async-api"></a>[API assíncrona do SDK do Java 3. x. x](#tab/java-v3-async)

```java
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    private String id;
    private String customerId;
    private Integer ttl;

    public SalesOrder(String id, String customerId, Integer ttl) {
        this.id = id;
        this.customerId = customerId;
        this.ttl = ttl;
    }

    public String id() {return this.id;}
    public SalesOrder id(String new_id) {this.id = new_id; return this;}
    public String customerId() {return this.customerId; return this;}
    public SalesOrder customerId(String new_cid) {this.customerId = new_cid;}
    public Integer ttl() {return this.ttl;}
    public SalesOrder ttl(Integer new_ttl) {this.ttl = new_ttl; return this;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);
```
---

## <a name="next-steps"></a>Próximas etapas

* [Criar um aplicativo Java](create-sql-api-java.md) para gerenciar Azure Cosmos DB dados da API do SQL usando o SDK do v4
* Saiba mais sobre os [SDKs Java baseados em reator](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-pattern-guide.md)
* Saiba como converter o código assíncrono RxJava para redirecionar o código assíncrono com o [Guia de reator vs RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md)