---
title: Migrar de CouchBase para Azure Cosmos DB SQL API
description: Orientação passo a passo para migrar de CouchBase para Azure Cosmos DB SQL API
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: mansha
author: manishmsfte
ms.openlocfilehash: 9713d963978e34ad874dc032676a6e1f14e4657c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77210938"
---
# <a name="migrate-from-couchbase-to-azure-cosmos-db-sql-api"></a>Migrar de CouchBase para Azure Cosmos DB SQL API

O Azure Cosmos DB é um banco de dados escalável, distribuído globalmente e totalmente gerenciado. Ele fornece acesso garantido de baixa latência aos seus dados. Para saber mais sobre o Azure Cosmos DB, consulte o artigo [de visão geral.](introduction.md) Este artigo fornece instruções para migrar aplicativos Java que estão conectados ao Couchbase para uma conta API SQL no Azure Cosmos DB.

## <a name="differences-in-nomenclature"></a>Diferenças na nomenclatura

A seguir estão as principais características que funcionam de forma diferente no Azure Cosmos DB quando comparado com Couchbase:

|   Couchbase     |   Azure Cosmos DB   |
| ---------------|-------------------|
|Servidor Couchbase| Conta       |
|Balde           | Banco de dados      |
|Balde           | Contêiner/Coleta |
|Documento JSON    | Item / Documento |

## <a name="key-differences"></a>Principais diferenças

* O Azure Cosmos DB tem um campo "ID" dentro do documento, enquanto o Couchbase tem o ID como parte do balde. O campo "ID" é único em toda a partição.

* Aescalas azure Cosmos DB usando a técnica de particionamento ou fragmentação. O que significa que divide os dados em vários fragmentos/partições. Essas partições/fragmentos são criados com base na propriedade de chave de partição que você fornece. Você pode selecionar a chave de partição para otimizar as operações de leitura, bem como gravar ou ler/gravar otimizado também. Para saber mais, consulte o artigo [de particionamento.](./partition-data.md)

* No Azure Cosmos DB, não é necessário que a hierarquia de nível superior denote a coleção porque o nome da coleção já existe. Esse recurso torna a estrutura JSON muito mais simples. A seguir, um exemplo que mostra diferenças no modelo de dados entre Couchbase e Azure Cosmos DB:

   **Base de**sofá : Identificação do documento = "99FF4444"

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

   **Azure Cosmos DB**: Consulte "ID" dentro do documento como mostrado abaixo

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
         
## <a name="java-sdk-support"></a>Suporte a Java SDK

O Azure Cosmos DB tem seguindo SDKs para suportar diferentes frameworks Java:

* SDK Assíncrono
* SDK de bota de mola

As seções a seguir descrevem quando usar cada um desses SDKs. Considere um exemplo onde temos três tipos de cargas de trabalho:

## <a name="couchbase-as-document-repository--spring-data-based-custom-queries"></a>Couchbase como repositório de documentos & consultas personalizadas baseadas em dados de primavera

Se a carga de trabalho que você está migrando for baseada no SDK baseado em inicialização de mola, então você poderá usar as seguintes etapas:

1. Adicionar pai ao arquivo POM.xml:

   ```java
   <parent>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-parent</artifactId>
      <version>2.1.5.RELEASE</version>
      <relativePath/>
   </parent>
   ```

1. Adicionar propriedades ao arquivo POM.xml:

   ```java
   <azure.version>2.1.6</azure.version>
   ```

1. Adicionar dependências ao arquivo POM.xml:

   ```java
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
       <version>2.1.6</version>
   </dependency>
   ```

1. Adicione propriedades do aplicativo em recursos e especifique o seguinte. Certifique-se de substituir os parâmetros de nome de URL, chave e banco de dados:

   ```java
      azure.cosmosdb.uri=<your-cosmosDB-URL>
      azure.cosmosdb.key=<your-cosmosDB-key>
      azure.cosmosdb.database=<your-cosmosDB-dbName>
   ```

1. Defina o nome da coleção no modelo. Você também pode especificar outras anotações. Por exemplo, ID, chave de partição para denotá-los explicitamente:

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

### <a name="insert-and-update-operations"></a>Inserir e atualizar operações

Onde *_repo* é o objeto do repositório e *doc* é o objeto da classe POJO. Você pode `.save` usar para inserir ou upsert (se documento com ID especificado encontrado). O seguinte trecho de código mostra como inserir ou atualizar um objeto doc:

```_repo.save(doc);```

### <a name="delete-operation"></a>Operação de Exclusão

Considere o seguinte trecho de código, onde o objeto doc terá id e chave de partição obrigatória para localizar e excluir o objeto:

```_repo.delete(doc);```

### <a name="read-operation"></a>Operação de Leitura

Você pode ler o documento com ou sem especificar a chave de partição. Se você não especificar a chave de partição, ela será tratada como uma consulta de partição cruzada. Considere as seguintes amostras de código, a primeira executará a operação usando o campo de chave de id e partição. O segundo exemplo usa um campo regular & sem especificar o campo de chave de partição.

* ```_repo.findByIdAndName(objDoc.getId(),objDoc.getName());```
* ```_repo.findAllByStatus(objDoc.getStatus());```

É isso, agora você pode usar seu aplicativo com o Azure Cosmos DB. A amostra de código completa para o exemplo descrito neste doc está disponível no repo [CouchbaseToCosmosDB-SpringCosmos](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/SpringCosmos) GitHub.

## <a name="couchbase-as-a-document-repository--using-n1ql-queries"></a>Couchbase como um repositório de documentos & usando consultas N1QL

Consultas N1QL é a maneira de definir consultas no Couchbase.

|Consulta N1QL | Consulta Azure CosmosDB|
|-------------------|-------------------|
|SELECIONE`TravelDocument`META( .id `TravelDocument`AS id, .* DE `TravelDocument` ONDE `_type` = "com.xx.xx.xxx.xxx.xxxx " e país = 'Índia' e QUALQUER m em Visas SATISFIES m.type == ` Validity` 'Multi-Entry' e m.Country IN ['Índia', Butão'] ORDEM POR DESC LIMITE 25 OFFSET 0   | SELECT c.id,c FROM c JOIN m in c.country='India' WHERE c._type = " com.xx.xx.xxx.xxx.xxxx" e c.country = 'India' e m.type = 'Multi-Entry' e m.Country IN ('Índia', 'Butão') ORDEM POR c.Validity DESC OFFSET 0 LIMITE 25 |

Você pode notar as seguintes alterações em suas consultas N1QL:

* Você não precisa usar a palavra-chave META ou consultar o documento de primeiro nível. Em vez disso, você pode criar sua própria referência ao contêiner. Neste exemplo, consideramos como "c" (pode ser qualquer coisa). Esta referência é usada como um prefixo para todos os campos de primeiro nível. Exemplo, c.id, c.country etc.

* Em vez de "ANY" agora você pode fazer uma adesão no subdocumento e encaminhá-lo com um alias dedicado como "m". Depois de criar alias para um subdocumento, você precisa usar alias. Por exemplo, m.Country.

* A seqüência de OFFSET é diferente na consulta Azure Cosmos DB, primeiro você precisa especificar OFFSET e depois LIMIT. Recomenda-se não usar o Spring Data SDK se você estiver usando consultas máximas definidas medida, pois pode ter sobrecargas desnecessárias no lado do cliente enquanto passa a consulta para o Azure Cosmos DB. Em vez disso, temos um SDK Java Async direto, que pode ser utilizado de forma muito eficiente neste caso.

### <a name="read-operation"></a>Operação de leitura

Use o Async Java SDK com as seguintes etapas:

1. Configure a seguinte dependência no arquivo POM.xml:

   ```java
   <!-- https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb -->
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmos</artifactId>
       <version>3.0.0</version>
   </dependency>
   ```

1. Crie um objeto de conexão para o `ConnectionBuilder` Azure Cosmos DB usando o método como mostrado no exemplo a seguir. Certifique-se de colocar esta declaração no feijão de tal forma que o seguinte código deve ser executado apenas uma vez:

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

Agora, com a ajuda do método acima, você pode passar várias consultas e executar sem qualquer incômodo. No caso de você ter o requisito para executar uma consulta grande, que pode ser dividida em várias consultas, tente o seguinte trecho de código em vez do anterior:

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

Com o código anterior, você pode executar consultas em paralelo e aumentar as execuções distribuídas para otimizar. Além disso, você pode executar as operações de inserção e atualização também:

### <a name="insert-operation"></a>Operação de Inserção

Para inserir o documento, execute o seguinte código:

```java 
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

Em seguida, assine o Mono como:

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

A operação upsert exige que você especifique o documento que precisa ser atualizado. Para obter o documento completo, você pode usar o trecho mencionado na operação de leitura de títulos e, em seguida, modificar os campos necessários. O seguinte trecho de código upserts o documento:

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
Em seguida, assine o mono. Consulte o trecho de assinatura mono em operação de inserção.

### <a name="delete-operation"></a>Operação de exclusão

O seguinte trecho será a operação de exclusão:

```java     
CosmosItem objItem= container.getItem(doc.Id, doc.Tenant);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

Em seguida, assine o mono, insira o trecho de assinatura mono na operação de inserção. A amostra completa de código está disponível no repo [CouchbaseToCosmosDB-AsyncInSpring](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncInSpring) GitHub.

## <a name="couchbase-as-a-keyvalue-pair"></a>Base de sofá como um par de chave/valor

Este é um tipo simples de carga de trabalho em que você pode realizar consultas em vez de consultas. Use as seguintes etapas para pares de chaves/valor:

1. Considere ter "/ID" como chave principal, o que garantirá que você possa executar a operação de procurar diretamente na partição específica. Crie uma coleção e especifique "/ID" como chave de partição.

1. Desligue completamente a indexação. Como você executará operações de procurar, não há razão para carregar a sobrecarga de indexação. Para desativar a indexação, entre no portal Azure, acesse a conta azure Cosmos DB. Abra o **Data Explorer,** selecione seu **banco de dados** e o **contêiner**. Abra a guia **'Configurações de &** escala' e selecione a **Política de Indexação**. Atualmente, a política de indexação parece o seguinte:
    
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

1. Use o seguinte trecho de código para criar o objeto de conexão. Objeto de conexão @Bean (a ser colocado ou torná-lo estático):

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

Agora você pode executar as operações CRUD da seguinte forma:

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
Em seguida, assine o mono, insira o trecho de assinatura mono na operação de inserção.

### <a name="delete-operation"></a>Operação de exclusão

Use o seguinte trecho para executar a operação de exclusão:

```java     
CosmosItem objItem= container.getItem(id, id);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

Em seguida, assine o mono, insira o trecho de assinatura mono na operação de inserção. A amostra completa de código está disponível no repo [CouchbaseToCosmosDB-AsyncKeyValue](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncKeyValue) GitHub.

## <a name="data-migration"></a>Migração de Dados

Há duas maneiras de migrar dados.

* **Use a fábrica de dados do Azure:** Este é o método mais recomendado para migrar os dados. Configure a fonte como Couchbase e afunde como Azure Cosmos DB SQL API, consulte o artigo do conector Azure [Cosmos DB Data Factory](../data-factory/connector-azure-cosmos-db.md) para obter etapas detalhadas.

* **Use a ferramenta de importação de dados Azure Cosmos DB:** Recomenda-se migrar usando VMs com menor quantidade de dados. Para obter etapas detalhadas, consulte o artigo [do importador de dados.](./import-data.md)

## <a name="next-steps"></a>Próximas etapas

* Para fazer testes de desempenho, consulte testes de desempenho e escala com o artigo [do Azure Cosmos DB.](./performance-testing.md)
* Para otimizar o código, consulte dicas de desempenho para o artigo [do Azure Cosmos DB.](./performance-tips-async-java.md)
* Explore o Java Async V3 SDK, o repo gitHub [de referência SDK.](https://github.com/Azure/azure-cosmosdb-java/tree/v3)
