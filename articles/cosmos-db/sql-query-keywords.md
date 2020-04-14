---
title: Palavras-chave SQL para Azure Cosmos DB
description: Saiba mais sobre as palavras-chave SQL para Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: tisande
ms.openlocfilehash: 069548b9b69ef6f7f6bde85ede830d97f3d312db
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261560"
---
# <a name="keywords-in-azure-cosmos-db"></a>Palavras-chave no Azure Cosmos DB

Este artigo detalha palavras-chave que podem ser usadas em consultas Azure Cosmos DB SQL.

## <a name="between"></a>BETWEEN

Você pode `BETWEEN` usar a palavra-chave para expressar consultas contra intervalos de strings ou valores numéricos. Por exemplo, a consulta a seguir retorna todos os itens em que a nota da primeira criança é de 1 a 5 anos, inclusive.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

Você também pode `BETWEEN` usar a `SELECT` palavra-chave na cláusula, como no exemplo a seguir.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

Na API SQL, ao contrário do ANSI SQL, você pode expressar consultas de intervalo contra propriedades de tipos mistos. Por exemplo, `grade` pode ser `5` um número como em `grade4` alguns itens e uma seqüência como em outros. Nestes casos, como em JavaScript, a comparação `Undefined`entre os dois tipos diferentes resulta em , de modo que o item é ignorado.

> [!TIP]
> Para tempos de execução de consulta mais rápidos, crie uma política de indexação `BETWEEN` que use um tipo de índice de intervalo em relação a quaisquer propriedades numéricas ou caminhos que a cláusula filtra.

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

Você também pode projetar objetos únicos. Neste caso, o campo lastName não existe em um dos dois documentos, então a consulta retorna um objeto vazio.

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

DISTINCT também pode ser usado na projeção dentro de uma subconsulta:

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

Esta consulta projeta uma matriz que contém o nome dado de cada criança com duplicatas removidas. Esta matriz é aliased como ChildNames e projetada na consulta externa.

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

Consultas com uma função de sistema agregado `DISTINCT` e uma subconsulta com não são suportadas. Por exemplo, a seguinte consulta não é suportada:

```sql
SELECT COUNT(1) FROM (SELECT DISTINCT f.lastName FROM f)
```

## <a name="in"></a>IN

Use a palavra-chave IN para verificar se um valor especificado corresponde a algum valor em uma lista. Por exemplo, a consulta a seguir retorna `id` todos `WakefieldFamily` `AndersenFamily`os itens da família onde está ou .

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

O exemplo a seguir retorna todos os itens onde o estado é qualquer um dos valores especificados:

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

A API SQL fornece suporte para [iteração sobre matrizes JSON,](sql-query-object-array.md#Iteration)com uma nova construção adicionada através da palavra-chave in na fonte FROM.

Se você incluir sua `IN` chave de partição no filtro, sua consulta será filtrada automaticamente apenas para as partições relevantes.

## <a name="top"></a>INÍCIO

A palavra-chave TOP `N` retorna o primeiro número de resultados de consulta em uma ordem indefinida. Como melhor prática, use `ORDER BY` TOP com a cláusula `N` para limitar os resultados ao primeiro número de valores pedidos. A combinação dessas duas cláusulas é a única maneira de indicar previsivelmente quais linhas TOP afeta.

Você pode usar TOP com um valor constante, como no exemplo a seguir, ou com um valor variável usando consultas parametrizadas.

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

- [Introdução](sql-query-getting-started.md)
- [Junções](sql-query-join.md)
- [Subconsultas](sql-query-subquery.md)