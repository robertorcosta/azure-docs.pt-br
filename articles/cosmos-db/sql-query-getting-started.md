---
title: Introdução às consultas SQL no Azure Cosmos DB
description: Saiba como usar consultas SQL para consultar dados de Azure Cosmos DB. Você pode carregar dados de exemplo em um contêiner em Azure Cosmos DB e consultá-los.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/24/2020
ms.author: tisande
ms.openlocfilehash: 7a4b2a778fc3d520c0ce85bed5bec0b49fc14384
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341902"
---
# <a name="getting-started-with-sql-queries"></a>Guia de Introdução a consultas SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Em Azure Cosmos DB contas da API do SQL, há duas maneiras de ler os dados:

**Leituras de ponto** – você pode fazer uma pesquisa de chave/valor em uma única *ID de item* e chave de partição. A combinação de *ID de item* e chave de partição é a chave e o próprio item é o valor. Para um documento de 1 KB, as leituras pontuais normalmente custam 1 [unidade de solicitação](request-units.md) com uma latência inferior a 10 ms. As leituras de ponto retornam um único item.

**Consultas SQL** – você pode consultar dados escrevendo consultas usando o linguagem SQL (SQL) como uma linguagem de consulta JSON. As consultas sempre custam pelo menos 2,3 unidades de solicitação e, em geral, terão uma latência maior e mais variável do que as leituras de ponto. As consultas podem retornar muitos itens.

A maioria das cargas de trabalho de leitura pesada no Azure Cosmos DB usar uma combinação de leituras de ponto e consultas SQL. Se você só precisa ler um único item, as leituras de ponto são mais baratas e mais rápidas do que as consultas. As leituras de ponto não precisam usar o mecanismo de consulta para acessar dados e podem ler os dados diretamente. É claro que não é possível que todas as cargas de trabalho leiam dados exclusivamente usando leituras pontuais, de modo que o suporte ao SQL como linguagem de consulta e [indexação independente de esquema](index-overview.md) fornece uma maneira mais flexível de acessar seus dados.

Aqui estão alguns exemplos de como fazer leituras de ponto com cada SDK:

- [SDK .NET](/dotnet/api/microsoft.azure.cosmos.container.readitemasync?preserve-view=true&view=azure-dotnet)
- [Java SDK](/java/api/com.azure.cosmos.cosmoscontainer.readitem?preserve-view=true&view=azure-java-stable#com_azure_cosmos_CosmosContainer__T_readItem_java_lang_String_com_azure_cosmos_models_PartitionKey_com_azure_cosmos_models_CosmosItemRequestOptions_java_lang_Class_T__)
- [SDK do Node.js](/javascript/api/@azure/cosmos/item?preserve-view=true&view=azure-node-latest#read-requestoptions-)
- [SDK do Python](/python/api/azure-cosmos/azure.cosmos.containerproxy?preserve-view=true&view=azure-python#read-item-item--partition-key--populate-query-metrics-none--post-trigger-include-none----kwargs-)

O restante deste documento mostra como começar a escrever consultas SQL no Azure Cosmos DB. As consultas SQL podem ser executadas por meio do SDK ou do portal do Azure.

## <a name="upload-sample-data"></a>Carregar dados de exemplo

Na sua API do SQL Cosmos DB conta, crie um contêiner chamado `Families` . Crie dois itens JSON simples no contêiner. Você pode executar a maioria das consultas de exemplo na documentação de consulta do Azure Cosmos DB usando esse conjunto de dados.

### <a name="create-json-items"></a>Criar itens JSON

O código a seguir cria dois itens JSON simples sobre famílias. Os itens JSON simples para as famílias Andersen e Barros incluem pais, filhos e seus animais de estimação, endereço e informações de registro. O primeiro item tem cadeias de caracteres, números, Boolianos, matrizes e propriedades aninhadas.

```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

O segundo item usa `givenName` e `familyName` em vez de `firstName` e `lastName` .

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

### <a name="query-the-json-items"></a>Consultar os itens JSON

Experimente algumas consultas em relação aos dados JSON para entender alguns dos principais aspectos da linguagem de consulta SQL do Azure Cosmos DB.

A consulta a seguir retorna os itens nos quais o `id` campo corresponde `AndersenFamily` . Como é uma `SELECT *` consulta, a saída da consulta é o item JSON completo. Para obter mais informações sobre a sintaxe SELECT, consulte [Select Statement](sql-query-select.md).

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Os resultados da consulta são:

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

A consulta a seguir reformata a saída JSON em uma forma diferente. A consulta projeta um novo `Family` objeto JSON com dois campos selecionados `Name` e `City` , quando a cidade do endereço é igual ao estado. "NY, NY" corresponde a esse caso.

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

Os resultados da consulta são:

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

A consulta a seguir retorna todos os nomes de filhos na família cujas `id` correspondências são `WakefieldFamily` ordenadas por cidade.

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC
```

Os resultados são:

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

## <a name="remarks"></a>Comentários

Os exemplos anteriores mostram vários aspectos da linguagem de consulta Cosmos DB:  

* Como a API do SQL funciona em valores JSON, ela lida com entidades em formato de árvore em vez de linhas e colunas. Você pode consultar os nós de árvore em qualquer profundidade arbitrária, como `Node1.Node2.Node3…..Nodem` , semelhante à referência de duas partes do `<table>.<column>` no SQL ANSI.

* Como a linguagem de consulta funciona com dados sem esquema, o sistema de tipos deve ser vinculado dinamicamente. A mesma expressão pode obter tipos diferentes em itens diferentes. O resultado de uma consulta é um valor JSON válido, mas não é garantido que seja de um esquema fixo.  

* O Azure Cosmos DB dá suporte somente a itens JSON estritos. As expressões e sistema de tipos são restritos para lidar somente com tipos JSON. Para obter mais informações, consulte as [especificações do JSON](https://www.json.org/).  

* Um contêiner cosmos é uma coleção sem esquemas de itens JSON. As relações dentro e entre os itens de contêiner são capturadas implicitamente por contenção, não pelas relações de chave primária e chave estrangeira. Esse recurso é importante para as junções intra-item discutidas posteriormente neste artigo.

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure Cosmos DB](introduction.md)
- [Amostras do .NET no Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Cláusula SELECT](sql-query-select.md)