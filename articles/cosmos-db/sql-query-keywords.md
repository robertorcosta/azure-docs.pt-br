---
title: Palavras-chave do SQL para Azure Cosmos DB
description: Saiba mais sobre palavras-chave do SQL para Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: tisande
ms.openlocfilehash: b4a48cd5e8d06fa6346dd6ab94df53cf4985b0b8
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100755"
---
# <a name="keywords-in-azure-cosmos-db"></a>Palavras-chave no Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este artigo fornece detalhes sobre palavras-chave que podem ser usadas em Azure Cosmos DB consultas SQL.

## <a name="between"></a>BETWEEN

Você pode usar a `BETWEEN` palavra-chave para expressar consultas em intervalos de cadeias de caracteres ou valores numéricos. Por exemplo, a consulta a seguir retorna todos os itens nos quais a classificação do primeiro filho é 1-5, inclusive.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

Você também pode usar a `BETWEEN` palavra-chave na `SELECT` cláusula, como no exemplo a seguir.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

Na API do SQL, ao contrário do SQL ANSI, você pode expressar consultas de intervalo em Propriedades de tipos mistos. Por exemplo, `grade` pode ser um número como `5` em alguns itens e uma cadeia de caracteres como `grade4` em outros. Nesses casos, como no JavaScript, a comparação entre os dois tipos diferentes resulta em `Undefined` , portanto, o item é ignorado.

## <a name="distinct"></a>DISTINTO

A `DISTINCT` palavra-chave elimina duplicatas na projeção da consulta.

Neste exemplo, a consulta projeta valores para cada sobrenome:

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

Os resultados são:

```json
[
    "Andersen"
]
```

Você também pode projetar objetos exclusivos. Nesse caso, o campo lastName não existe em um dos dois documentos, portanto, a consulta retorna um objeto vazio.

```sql
SELECT DISTINCT f.lastName
FROM Families f
```

Os resultados são:

```json
[
    {
        "lastName": "Andersen"
    },
    {}
]
```

`DISTINCT` também pode ser usado na projeção dentro de uma subconsulta:

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

Essa consulta projeta uma matriz que contém o especificador de cada filho com duplicatas removidas. Essa matriz tem um alias como Filhonames e projetada na consulta externa.

Os resultados são:

```json
[
    {
        "id": "AndersenFamily",
        "ChildNames": []
    },
    {
        "id": "WakefieldFamily",
        "ChildNames": [
            "Jesse",
            "Lisa"
        ]
    }
]
```

Não há suporte para consultas com uma função de sistema agregada e uma subconsulta com `DISTINCT`. Por exemplo, a seguinte consulta não tem suporte:

```sql
SELECT COUNT(1) FROM (SELECT DISTINCT f.lastName FROM f)
```

## <a name="in"></a>IN

Use a palavra-chave IN para verificar se um valor especificado corresponde a qualquer valor em uma lista. Por exemplo, a consulta a seguir retorna todos os itens da família em que o `id` é `WakefieldFamily` ou `AndersenFamily` .

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

O exemplo a seguir retorna todos os itens em que o estado é qualquer um dos valores especificados:

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

A API do SQL fornece suporte para [iteração em matrizes JSON](sql-query-object-array.md#Iteration), com uma nova construção adicionada por meio da palavra-chave in na fonte from.

Se você incluir sua chave de partição no `IN` filtro, sua consulta será filtrada automaticamente somente para as partições relevantes.

## <a name="top"></a>INÍCIO

A palavra-chave TOP retorna o primeiro `N` número de resultados da consulta em uma ordem indefinida. Como prática recomendada, use TOP com a `ORDER BY` cláusula para limitar os resultados para o primeiro `N` número de valores ordenados. Combinar essas duas cláusulas é a única maneira de indicar de forma previsível quais linhas afetam os principais.

Você pode usar TOP com um valor constante, como no exemplo a seguir, ou com um valor de variável usando consultas parametrizadas.

```sql
    SELECT TOP 1 *
    FROM Families f
```

Os resultados são:

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

## <a name="next-steps"></a>Próximas etapas

- [Guia de Introdução](sql-query-getting-started.md)
- [Junções](sql-query-join.md)
- [Subconsultas](sql-query-subquery.md)