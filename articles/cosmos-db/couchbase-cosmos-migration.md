---
title: Migrar do CouchBase para a API do SQL Azure Cosmos DB
description: Diretrizes passo a passo para migrar do CouchBase para a API do SQL Azure Cosmos DB
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: mansha
author: manishmsfte
ms.openlocfilehash: 9713d963978e34ad874dc032676a6e1f14e4657c
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210938"
---
# <a name="migrate-from-couchbase-to-azure-cosmos-db-sql-api"></a>Migrar do CouchBase para a API do SQL Azure Cosmos DB

O Azure Cosmos DB é um banco de dados escalonável, distribuído globalmente e totalmente gerenciado. Ele fornece acesso garantido de baixa latência aos seus dados. Para saber mais sobre Azure Cosmos DB, consulte o artigo [visão geral](introduction.md) . Este artigo fornece instruções para migrar aplicativos Java que estão conectados ao Couchbase a uma conta da API do SQL no Azure Cosmos DB.

## <a name="differences-in-nomenclature"></a>Diferenças em nomenclatura

A seguir estão os principais recursos que funcionam de maneira diferente em Azure Cosmos DB em comparação com Couchbase:

|   Couchbase     |   Azure Cosmos DB   |
| ---------------|-------------------|
|Servidor Couchbase| Conta       |
|Sequencia           | Banco de dados      |
|Sequencia           | Contêiner/coleção |
|Documento JSON    | Item/documento |

## <a name="key-differences"></a>Principais diferenças

* Azure Cosmos DB tem um campo "ID" dentro do documento, enquanto Couchbase tem a ID como parte do Bucket. O campo "ID" é exclusivo na partição.

* Azure Cosmos DB escalas usando a técnica de particionamento ou fragmentação. Isso significa que ele divide os dados em vários fragmentos/partições. Essas partições/fragmentos são criados com base na propriedade de chave de partição que você fornece. Você pode selecionar a chave de partição para otimizar também as operações de leitura e gravação ou otimizado para leitura/gravação. Para saber mais, confira o artigo [particionamento](./partition-data.md) .

* No Azure Cosmos DB, não é necessário que a hierarquia de nível superior denotasse a coleção porque o nome da coleção já existe. Esse recurso torna a estrutura JSON muito mais simples. Veja a seguir um exemplo que mostra as diferenças no modelo de dados entre Couchbase e Azure Cosmos DB:

   **Couchbase**: ID do documento = "99FF4444"

    ```json
    {
      "TravelDocument":
      {
       "Country":"India",
      "Validity" : "2022-09-01",
        "Person":
        {
          "Name": "Manish",
          "Address": "AB Road, City-z"
        },
        "Visas":
        [
          {
          "Country":"India",
          "Type":"Multi-Entry",
          "Validity":"2022-09-01"
          },
          {
          "Country":"US",
          "Type":"Single-Entry",
          "Validity":"2022-08-01"
          }
        ]
      }
    }
   ```

   **Azure Cosmos DB**: consulte "ID" dentro do documento, conforme mostrado abaixo

    ```json
    {
      "id" : "99FF4444",
    
      "Country":"India",
       "Validity" : "2022-09-01",
        "Person":
        {
          "Name": "Manish",
          "Address": "AB Road, City-z"
        },
        "Visas":
        [
          {
          "Country":"India",
          "Type":"Multi-Entry",
          "Validity":"2022-09-01"
          },
          {
          "Country":"US",
          "Type":"Single-Entry",
          "Validity":"2022-08-01"
          }
        ]
      }
    
    ```
         
## <a name="java-sdk-support"></a>Suporte do SDK do Java

Azure Cosmos DB tem os SDKs a seguir para dar suporte a estruturas de Java diferentes:

* SDK assíncrono
* SDK do Spring boot

As seções a seguir descrevem quando usar cada um desses SDKs. Considere um exemplo em que temos três tipos de cargas de trabalho:

## <a name="couchbase-as-document-repository--spring-data-based-custom-queries"></a>Couchbase como repositório de documentos & consultas personalizadas baseadas em Spring data

Se a carga de trabalho que você está migrando for baseada no SDK baseado em Spring boot, você poderá usar as seguintes etapas:

1. Adicionar pai ao arquivo POM. xml:

   ```java
   <parent>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-parent</artifactId>
      <version>2.1.5.RELEASE</version>
      <relativePath/>
   </parent>
   ```

1. Adicione Propriedades ao arquivo POM. xml:

   ```java
   <azure.version>2.1.6</azure.version>
   ```

1. Adicione dependências ao arquivo POM. xml:

   ```java
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
       <version>2.1.6</version>
   </dependency>
   ```

1. Adicione as propriedades do aplicativo em recursos e especifique o seguinte. Certifique-se de substituir os parâmetros URL, chave e nome do banco de dados:

   ```java
      azure.cosmosdb.uri=<your-cosmosDB-URL>
      azure.cosmosdb.key=<your-cosmosDB-key>
      azure.cosmosdb.database=<your-cosmosDB-dbName>
   ```

1. Defina o nome da coleção no modelo. Você também pode especificar outras anotações. Por exemplo, ID, chave de partição para denotar explicitamente:

   ```java
   @Document(collection = "mycollection")
       public class User {
           @id
           private String id;
           private String firstName;
           @PartitionKey
           private String lastName;
       }
   ```

A seguir estão os trechos de código para operações CRUD:

### <a name="insert-and-update-operations"></a>Operações de inserção e atualização

Em que *_repo* é o objeto de Repository e *Doc* é o objeto da classe POJO. Você pode usar `.save` para inserir ou Upsert (se o documento com a ID especificada for encontrado). O trecho de código a seguir mostra como inserir ou atualizar um objeto doc:

```_repo.save(doc);```

### <a name="delete-operation"></a>Operação de Exclusão

Considere o seguinte trecho de código, em que o objeto doc terá a ID e a chave de partição obrigatórias para localizar e excluir o objeto:

```_repo.delete(doc);```

### <a name="read-operation"></a>Operação de Leitura

Você pode ler o documento com ou sem especificar a chave de partição. Se você não especificar a chave de partição, ela será tratada como uma consulta entre partições. Considere os exemplos de código a seguir, primeiro ele executará a operação usando a ID e o campo de chave de partição. O segundo exemplo usa um campo comum & sem especificar o campo de chave de partição.

* ```_repo.findByIdAndName(objDoc.getId(),objDoc.getName());```
* ```_repo.findAllByStatus(objDoc.getStatus());```

É isso, agora você pode usar seu aplicativo com Azure Cosmos DB. O exemplo de código completo para o exemplo descrito neste documento está disponível no repositório GitHub [CouchbaseToCosmosDB-SpringCosmos](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/SpringCosmos) .

## <a name="couchbase-as-a-document-repository--using-n1ql-queries"></a>Couchbase como um repositório de documentos & usando consultas N1QL

N1QL consultas é a maneira de definir consultas no Couchbase.

|Consulta N1QL | Consulta do Azure CosmosDB|
|-------------------|-------------------|
|Selecione META (`TravelDocument`). ID como ID, `TravelDocument`. * de `TravelDocument` em que `_type` = "com. XX. XX. XX. xxx. xxx. xxxx" e Country = ' Índia ' e qualquer m em visas atende m. Type = = ' multientry ' e m. Country em [' Índia ', Butão '] ORDER BY ` Validity` DESC limite 25 deslocamento 0   | Selecione c. ID, c de c JOIN m em c. Country = ' Índia ', em que c. _type = "com. XX. XX. XX. xxx. xxx. xxxx" e c. Country = ' Índia ' e m. Type = ' multientry ' e m. Country em (' Índia ', ' Butão ') ORDENAr por c. validade DESC deslocamento 0 limite 25 |

Você pode observar as seguintes alterações em suas consultas do N1QL:

* Você não precisa usar a palavra-chave META ou consultar o documento de primeiro nível. Em vez disso, você pode criar sua própria referência ao contêiner. Neste exemplo, consideramos como "c" (pode ser qualquer coisa). Essa referência é usada como um prefixo para todos os campos de primeiro nível. Exemplo de fr, c.id, c. Country etc.

* Em vez de "ANY", agora você pode fazer uma junção no subdocumento e consultá-lo com um alias dedicado, como "m". Depois de criar o alias para um subdocumento, você precisa usar o alias. Por exemplo, m. Country.

* A sequência de deslocamento é diferente na consulta Azure Cosmos DB, primeiro você precisa especificar o deslocamento e o limite. É recomendável não usar o SDK do Spring data se você estiver usando consultas máximas definidas personalizadas, pois ela pode ter sobrecarga desnecessária no lado do cliente ao passar a consulta para Azure Cosmos DB. Em vez disso, temos um SDK Java assíncrono direto, que pode ser utilizado com muito eficiência nesse caso.

### <a name="read-operation"></a>Operação de leitura

Use o SDK do Java assíncrono com as seguintes etapas:

1. Configure a seguinte dependência no arquivo POM. xml:

   ```java
   <!-- https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb -->
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmos</artifactId>
       <version>3.0.0</version>
   </dependency>
   ```

1. Crie um objeto de conexão para Azure Cosmos DB usando o método `ConnectionBuilder`, conforme mostrado no exemplo a seguir. Certifique-se de colocar essa declaração no bean de modo que o código a seguir seja executado apenas uma vez:

   ```java
   ConnectionPolicy cp=new ConnectionPolicy();
   cp.connectionMode(ConnectionMode.DIRECT);
    
   if(client==null)
    client= CosmosClient.builder()
        .endpoint(Host)//(Host, MasterKey, dbName, collName).Builder()
        .connectionPolicy(cp)
        .key(MasterKey)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();   
   
   container = client.getDatabase(_dbName).getContainer(_collName);
   ```

1. Para executar a consulta, você precisa executar o seguinte trecho de código:

   ```java
   Flux<FeedResponse<CosmosItemProperties>> objFlux= container.queryItems(query, fo);
   ```

Agora, com a ajuda do método acima, você pode passar várias consultas e executá-las sem complicações. Caso você tenha a necessidade de executar uma consulta grande, que pode ser dividida em várias consultas, tente o seguinte trecho de código em vez do anterior:

```java
for(SqlQuerySpec query:queries)
{
    objFlux= container.queryItems(query, fo);
    objFlux .publishOn(Schedulers.elastic())
            .subscribe(feedResponse->
                {
                    if(feedResponse.results().size()>0)
                    {
                        _docs.addAll(feedResponse.results());
                    }
                
                },
                Throwable::printStackTrace,latch::countDown);
    lstFlux.add(objFlux);
}
                        
        Flux.merge(lstFlux);
        latch.await();
}
```

Com o código anterior, você pode executar consultas em paralelo e aumentar as execuções distribuídas para otimizar. Além disso, você pode executar as operações INSERT e Update também:

### <a name="insert-operation"></a>Operação de Inserção

Para inserir o documento, execute o seguinte código:

```java 
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

Em seguida, assine o mono como:

```java
CountDownLatch latch=new CountDownLatch(1);
objMono .subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
            if(resourceResponse.statusCode()!=successStatus)
                {
                    throw new RuntimeException(resourceResponse.toString());
                }
            },
        Throwable::printStackTrace,latch::countDown);
latch.await();              
```

### <a name="upsert-operation"></a>Operação Upsert

A operação Upsert exige que você especifique o documento que precisa ser atualizado. Para buscar o documento completo, você pode usar o trecho de código mencionado em operação de leitura de cabeçalho e modificar os campos necessários. O trecho de código a seguir upserts o documento:

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
Em seguida, assine o mono. Consulte o trecho de assinatura do mono na operação de inserção.

### <a name="delete-operation"></a>Operação de exclusão

O trecho a seguir fará a operação de exclusão:

```java     
CosmosItem objItem= container.getItem(doc.Id, doc.Tenant);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

Em seguida, assine o mono, consulte trecho de assinatura mono na operação de inserção. O exemplo de código completo está disponível no repositório GitHub [CouchbaseToCosmosDB-AsyncInSpring](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncInSpring) .

## <a name="couchbase-as-a-keyvalue-pair"></a>Couchbase como um par de chave/valor

Esse é um tipo simples de carga de trabalho no qual você pode executar pesquisas em vez de consultas. Use as seguintes etapas para pares de chave/valor:

1. Considere ter "/ID" como chave primária, o que garantirá que você possa executar a operação de pesquisa diretamente na partição específica. Crie uma coleção e especifique "/ID" como chave de partição.

1. Desative completamente a indexação. Como você executará operações de pesquisa, não há nenhum ponto de carregar a sobrecarga de indexação. Para desativar a indexação, entre portal do Azure, vá Azure Cosmos DB conta. Abra o **Data Explorer**, selecione o **banco de dados** e o **contêiner**. Abra a guia **configurações de & de escala** e selecione a política de **indexação**. A política de indexação no momento é semelhante ao seguinte:
    
   ```json
   {
       "indexingMode": "consistent",
       "includedPaths": 
       [
           {
            "path": "/*",
            "indexes": 
             [
                {
                  "kind": "Range",
                  "dataType": "Number"
                },
                {
                  "kind": "Range",
                  "dataType": "String"
                },
                {
                   "kind": "Spatial",
                   "dataType": "Point"
                }
             ]
          }
       ],
       "excludedPaths": 
       [
         {
             "path": "/path/to/single/excluded/property/?"
         },
         {
             "path": "/path/to/root/of/multiple/excluded/properties/*"
         }
      ]
   }
   ````

   Substitua a política de indexação acima pela seguinte política:

   ```json
   {
       "indexingMode": "none"
   }
   ```

1. Use o trecho de código a seguir para criar o objeto de conexão. Objeto de conexão (a ser colocado em @Bean ou torná-lo estático):

   ```java
   ConnectionPolicy cp=new ConnectionPolicy();
   cp.connectionMode(ConnectionMode.DIRECT);
   
   if(client==null)
    client= CosmosClient.builder()
        .endpoint(Host)//(Host, MasterKey, dbName, collName).Builder()
        .connectionPolicy(cp)
        .key(MasterKey)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();
    
   container = client.getDatabase(_dbName).getContainer(_collName);
   ```

Agora você pode executar as operações CRUD da seguinte maneira:

### <a name="read-operation"></a>Operação de leitura

Para ler o item, use o seguinte trecho:

```java        
CosmosItemRequestOptions ro=new CosmosItemRequestOptions();
ro.partitionKey(new PartitionKey(documentId));
CountDownLatch latch=new CountDownLatch(1);
        
var objCosmosItem= container.getItem(documentId, documentId);
Mono<CosmosItemResponse> objMono = objCosmosItem.read(ro);
objMono .subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
            if(resourceResponse.item()!=null)
            {
                doc= resourceResponse.properties().toObject(UserModel.class);
            }
        },
        Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="insert-operation"></a>Operação de Inserção

Para inserir um item, você pode executar o seguinte código:

```java 
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

Em seguida, assine o mono como:

```java
CountDownLatch latch=new CountDownLatch(1);
objMono.subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
            if(resourceResponse.statusCode()!=successStatus)
                {
                    throw new RuntimeException(resourceResponse.toString());
                }
            },
        Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="upsert-operation"></a>Operação Upsert

Para atualizar o valor de um item, consulte o trecho de código abaixo:

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
Em seguida, assine o mono, consulte trecho de assinatura mono na operação de inserção.

### <a name="delete-operation"></a>Operação de exclusão

Use o trecho a seguir para executar a operação de exclusão:

```java     
CosmosItem objItem= container.getItem(id, id);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

Em seguida, assine o mono, consulte trecho de assinatura mono na operação de inserção. O exemplo de código completo está disponível no repositório GitHub [CouchbaseToCosmosDB-AsyncKeyValue](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncKeyValue) .

## <a name="data-migration"></a>Migração de Dados

Há duas maneiras de migrar dados.

* **Usar Azure data Factory:** Esse é o método mais recomendado para migrar os dados. Configure a origem como Couchbase e o coletor como Azure Cosmos DB API do SQL, consulte o artigo [conector de data Factory de Cosmos DB](../data-factory/connector-azure-cosmos-db.md) do Azure para obter etapas detalhadas.

* **Use a ferramenta de importação de dados do Azure Cosmos DB:** Essa opção é recomendada para migrar usando VMs com menos quantidade de dados. Para obter etapas detalhadas, consulte o artigo [importador de dados](./import-data.md) .

## <a name="next-steps"></a>Próximas etapas

* Para fazer testes de desempenho, consulte [teste de desempenho e escala com Azure Cosmos DB](./performance-testing.md) artigo.
* Para otimizar o código, consulte [dicas de desempenho para Azure Cosmos DB](./performance-tips-async-java.md) artigo.
* Explore o SDK do Java Async v3, o repositório GitHub de [referência do SDK](https://github.com/Azure/azure-cosmosdb-java/tree/v3) .
