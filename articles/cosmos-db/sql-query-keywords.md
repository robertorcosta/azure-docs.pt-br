---
title: Palavras-chave do SQL para Azure Cosmos DB
description: Saiba mais sobre palavras-chave do SQL para Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mjbrown
ms.openlocfilehash: a9de9435c0e2fb2b67733a995ff412978ea02d89
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2020
ms.locfileid: "78250303"
---
# <a name="keywords-in-azure-cosmos-db"></a>Palavras-chave no Azure Cosmos DB
Este artigo fornece detalhes sobre palavras-chave que podem ser usadas em Azure Cosmos DB consultas SQL.

## <a name="between"></a>BETWEEN

Como no ANSI SQL, você pode usar a palavra-chave BETWEEN para expressar consultas em intervalos de cadeias de caracteres ou valores numéricos. Por exemplo, a consulta a seguir retorna todos os itens nos quais a classificação do primeiro filho é 1-5, inclusive.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

Ao contrário do SQL ANSI, você também pode usar a cláusula BETWEEN na cláusula FROM, como no exemplo a seguir.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

Na API do SQL, ao contrário do SQL ANSI, você pode expressar consultas de intervalo em Propriedades de tipos mistos. Por exemplo, `grade` pode ser um número como `5` em alguns itens e uma cadeia de caracteres como `grade4` em outros. Nesses casos, como no JavaScript, a comparação entre os dois tipos diferentes resulta em `Undefined`, portanto, o item é ignorado.

> [!TIP]
> Para tempos de execução de consulta mais rápidos, crie uma política de indexação que usa um tipo de índice de intervalo em qualquer propriedade numérica ou caminho que a cláusula BETWEEN filtra.

## <a name="distinct"></a>DISTINTO

A palavra-chave DISTINCT elimina duplicatas na projeção da consulta.

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

DISTINCT também pode ser usado na projeção dentro de uma subconsulta:

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

Não há suporte para consultas com uma função de sistema agregada e uma subconsulta com DISTINCT. Por exemplo, não há suporte para a seguinte consulta:

```sql
SELECT COUNT(1) FROM (SELECT DISTINCT f.lastName FROM f)
```

## <a name="in"></a>NO

Use a palavra-chave IN para verificar se um valor especificado corresponde a qualquer valor em uma lista. Por exemplo, a consulta a seguir retorna todos os itens da família em que a `id` é `WakefieldFamily` ou `AndersenFamily`.

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

## <a name="top"></a>INÍCIO

A palavra-chave TOP retorna o primeiro `N` número de resultados da consulta em uma ordem indefinida. Como prática recomendada, use TOP com a cláusula ORDER BY para limitar os resultados ao primeiro `N` número de valores ordenados. Combinar essas duas cláusulas é a única maneira de indicar de forma previsível quais linhas afetam os principais.

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