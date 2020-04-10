---
title: Funções definidas pelo usuário (UDFs) no Azure Cosmos DB
description: Saiba mais sobre funções definidas pelo usuário no Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: tisande
ms.openlocfilehash: 455f44fb365152b75a3811563b646c6243f686db
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011116"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Funções definidas pelo usuário (UDFs) no Azure Cosmos DB

A API SQL fornece suporte para funções definidas pelo usuário (UDFs). Com UDFs escalares, você pode passar em zero ou muitos argumentos e retornar um único resultado de argumento. A API verifica cada argumento por serem valores JSON legais.  

## <a name="udf-use-cases"></a>Casos de uso da UDF

A API estende a sintaxe SQL para suportar a lógica de aplicativos personalizados usando UDFs. Você pode registrar UDFs com a API SQL e referencia-los em consultas SQL. Ao contrário dos procedimentos e gatilhos armazenados, os UDFs são somente leitura.

Usando UDFs, você pode estender a linguagem de consulta do Azure Cosmos DB. As UDFs são uma ótima maneira de expressar uma lógica de negócios complexa na projeção de uma consulta.

No entanto, recomendamos evitar UDFs quando:

- Uma [função](sql-query-system-functions.md) de sistema equivalente já existe no Azure Cosmos DB. As funções do sistema sempre usarão menos RU's do que o UDF equivalente.
- O UDF é o `WHERE` único filtro na cláusula da sua consulta. Os UDF's não utilizam o índice, portanto, avaliar o UDF exigirá o carregamento de documentos. A combinação de predicados adicionais de filtro que usam o `WHERE` índice, em combinação com um UDF, na cláusula reduzirá o número de documentos processados pela UDF.

Se você deve usar o mesmo UDF várias vezes em uma consulta, você deve referenciar o UDF em uma [subconsulta,](sql-query-subquery.md#evaluate-once-and-reference-many-times)permitindo que você use uma expressão JOIN para avaliar o UDF uma vez, mas referencia-lo muitas vezes.

## <a name="examples"></a>Exemplos

O exemplo a seguir registra um UDF em um contêiner de itens no banco de dados Cosmos. O exemplo cria um UDF cujo nome é `REGEX_MATCH`. Ele aceita dois valores de `input` `pattern`seqüência JSON e , e verifica se o `string.match()` primeiro corresponde ao padrão especificado no segundo usando a função JavaScript.

```javascript
       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) {
                      return input.match(pattern) !== null;
                   };",
       };

       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
           regexMatchUdf).Result;  
```

Agora, use este UDF em uma projeção de consulta. Você deve qualificar uDFs com `udf.` o prefixo sensível a maiúsculas e minúsculas ao chamá-los de consultas internas.

```sql
    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families
```

Os resultados são:

```json
    [
      {
        "$1": true
      },
      {
        "$1": false
      }
    ]
```

Você pode usar o UDF qualificado com o `udf.` prefixo dentro de um filtro, como no exemplo a seguir:

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")
```

Os resultados são:

```json
    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]
```

Em essência, UDFs são expressões escalares válidas que você pode usar tanto em projeções quanto em filtros.

Para expandir o poder das UDFs, veja outro exemplo com lógica condicional:

```javascript
       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                   switch (city) {
                       case 'Seattle':
                           return 520;
                       case 'NY':
                           return 410;
                       case 'Chicago':
                           return 673;
                       default:
                           return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
                seaLevelUdf);
```

O exemplo a seguir exerce o UDF:

```sql
    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f
```

Os resultados são:

```json
     [
      {
        "city": "Seattle",
        "seaLevel": 520
      },
      {
        "city": "NY",
        "seaLevel": 410
      }
    ]
```

Se as propriedades referidas pelos parâmetros UDF não estão disponíveis no valor JSON, o parâmetro é considerado indefinido e a invocação do UDF é ignorada. Da mesma forma, se o resultado do UDF é indefinido, ele não está incluído no resultado.

Como mostram os exemplos anteriores, os UDFs integram o poder da linguagem JavaScript com a API SQL. Os UDFs fornecem uma interface programável rica para fazer uma lógica complexa e condicional com a ajuda de recursos de tempo de execução JavaScript incorporados. A API SQL fornece os argumentos aos UDFs para cada item de origem na fase atual de cláusula SI ou SELECT de processamento. O resultado é perfeitamente incorporado no pipeline de execução geral. Em resumo, os UDFs são ótimas ferramentas para fazer lógica de negócios complexa como parte de consultas.

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure Cosmos DB](introduction.md)
- [Funções do sistema](sql-query-system-functions.md)
- [Agregações](sql-query-aggregates.md)