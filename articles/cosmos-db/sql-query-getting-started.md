---
title: Começando com consultas SQL no Azure Cosmos DB
description: Saiba como usar consultas SQL para consultar dados do Azure Cosmos DB. Você pode carregar dados de amostra para um contêiner no Azure Cosmos DB e consulta-los.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 1d24261edea843fa928ad00e3ce7babcb84acd3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873328"
---
# <a name="getting-started-with-sql-queries"></a>Guia de Introdução a consultas SQL

As contas api do Azure Cosmos DB SQL suportam a consulta de itens usando o SQL (Structured Query Language, linguagem de consulta estruturada) como uma linguagem de consulta JSON. Os objetivos de design da linguagem de consulta azure Cosmos DB são:

* Suporte sql, um dos idiomas de consulta mais familiares e populares, em vez de inventar uma nova linguagem de consulta. O SQL fornece um modelo formal de programação para consultas ricas sobre itens JSON.  

* Use o modelo de programação do JavaScript como base para a linguagem de consulta. O sistema de tipo javaScript, avaliação de expressão e invocação de função são as raízes da API SQL. Essas raízes fornecem um modelo natural de programação para recursos como projeções relacionais, navegação hierárquica em itens JSON, auto-adesão, consultas espaciais e invocação de funções definidas pelo usuário (UDFs) escritas inteiramente em JavaScript.

## <a name="upload-sample-data"></a>Carregar dados de amostra

Em sua conta SQL API Cosmos `Families`DB, crie um contêiner chamado . Crie dois itens JSON simples no recipiente. Você pode executar a maioria das consultas de exemplo nos docs de consulta do Azure Cosmos DB usando este conjunto de dados.

### <a name="create-json-items"></a>Criar itens JSON

O código a seguir cria dois itens JSON simples sobre famílias. Os itens json simples para as famílias Andersen e Wakefield incluem pais, crianças e seus animais de estimação, endereço e informações de registro. O primeiro item tem strings, números, booleanos, matrizes e propriedades aninhadas.


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

O segundo `givenName` item `familyName` usa `firstName` `lastName`e em vez de e .

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

Tente algumas consultas com os dados JSON para entender alguns dos principais aspectos da linguagem de consulta SQL do Azure Cosmos DB.

A consulta a seguir retorna os `id` itens `AndersenFamily`onde o campo corresponde . Como é uma `SELECT *` consulta, a saída da consulta é o item JSON completo. Para obter mais informações sobre a sintaxe SELECT, consulte [a declaração SELECT](sql-query-select.md). 

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

A consulta a seguir reformata a saída JSON em uma forma diferente. A consulta projeta um novo `Family` objeto JSON `Name` `City`com dois campos selecionados, e , quando o endereço da cidade é o mesmo que o estado. "NY, NY" combina com este caso.

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

A consulta a seguir retorna todos os nomes `id` `WakefieldFamily`dadas de crianças da família cujos fósforos , ordenados pela cidade.

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

* Como a API SQL funciona com valores JSON, ela lida com entidades em forma de árvore em vez de linhas e colunas. Você pode se referir aos nódulos de árvore `Node1.Node2.Node3…..Nodem`em qualquer profundidade arbitrária, `<table>.<column>` como , semelhante à referência em duas partes do ANSI SQL.

* Como a linguagem de consulta funciona com dados sem esquema, o sistema de tipo deve ser vinculado dinamicamente. A mesma expressão pode obter tipos diferentes em itens diferentes. O resultado de uma consulta é um valor JSON válido, mas não é garantido ser de um esquema fixo.  

* O Azure Cosmos DB dá suporte somente a itens JSON estritos. O sistema de tipo e as expressões são restritos a lidar apenas com tipos JSON. Para obter mais informações, consulte a [especificação JSON](https://www.json.org/).  

* Um contêiner Cosmos é uma coleção sem esquemas de itens JSON. As relações dentro e entre os itens de contêineres são implicitamente capturadas pela contenção, não por chave primária e relações de chave estrangeira. Este recurso é importante para as junções intra-item discutidas mais tarde neste artigo.

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure Cosmos DB](introduction.md)
- [Amostras do .NET no Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Cláusula SELECT](sql-query-select.md)
