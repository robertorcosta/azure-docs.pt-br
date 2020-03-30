---
title: Subconsultas SQL para Azure Cosmos DB
description: Conheça as subconsultas SQL e seus casos de uso comum e diferentes tipos de subconsultas no Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 42d9e8b190747a3ffaf0e46ea1eddda33d09bb24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74870557"
---
# <a name="sql-subquery-examples-for-azure-cosmos-db"></a>Exemplos de subquery SQL para Azure Cosmos DB

Uma subconsulta é uma consulta aninhada em outra consulta. Uma subconsulta também é chamada de consulta interna ou seleção interna. A declaração que contém uma subconsulta é tipicamente chamada de consulta externa.

Este artigo descreve subconsultas SQL e seus casos de uso comum no Azure Cosmos DB. Todas as consultas de amostra neste doc podem ser executadas contra um conjunto de dados nutricionais pré-carregado no [Azure Cosmos DB Query Playground](https://www.documentdb.com/sql/demo).

## <a name="types-of-subqueries"></a>Tipos de subconsultas

Existem dois tipos principais de subconsultas:

* **Correlacionado**: Uma subconsulta que faz referência aos valores da consulta externa. A subquery é avaliada uma vez para cada linha que a consulta externa processa.
* **Não correlacionado:** Uma subconsulta independente da consulta externa. Ele pode ser executado por conta própria sem depender da consulta externa.

> [!NOTE]
> O Azure Cosmos DB suporta apenas subconsultas correlacionadas.

As subconsultas podem ser ainda mais classificadas com base no número de linhas e colunas que retornam. Existem três tipos:
* **Tabela**: Retorna várias linhas e várias colunas.
* **Multi-valor**: Retorna várias linhas e uma única coluna.
* **Escalador**: Retorna uma única linha e uma única coluna.

As consultas SQL no Azure Cosmos DB sempre retornam uma única coluna (um valor simples ou um documento complexo). Portanto, apenas subqueries multi-valor e escalar são aplicáveis no Azure Cosmos DB. Você pode usar uma subconsulta de vários valores apenas na cláusula FROM como uma expressão relacional. Você pode usar uma subquery escalar como uma expressão escalar na cláusula SELECT ou WHERE, ou como uma expressão relacional na cláusula FROM.

## <a name="multi-value-subqueries"></a>Subconsultas multi-valor

Subconsultas de vários valores retornam um conjunto de documentos e são sempre usadas dentro da cláusula FROM. Eles são usados para:

* Otimização de expressões JOIN. 
* Avaliando expressões caras uma vez e fazendo referência várias vezes.

## <a name="optimize-join-expressions"></a>Otimizar expressões JOIN

Subconsultas de vários valores podem otimizar expressões DE JOIN pressionando predicados após cada expressão selecionada, em vez de todas as inter-participações na cláusula WHERE.

Considere a consulta a seguir.

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0 
AND n.nutritionValue < 10) AND s.amount > 1
```

Para esta consulta, o índice corresponderá a qualquer documento que tenha uma tag com o nome "fórmula infantil". É um item de nutrientes com um valor entre 0 e 10, e um item de porção com uma quantidade maior que 1. A expressão JOIN aqui executará o produto cruzado de todos os itens de etiquetas, nutrientes e matrizes de porções para cada documento correspondente antes de qualquer filtro ser aplicado. 

A cláusula WHERE aplicará o predicado do filtro em cada <c, t, n, s> tupla. Por exemplo, se um documento correspondente tivesse 10 itens em cada uma das três matrizes, ele se expandiria para 1 x 10 x 10 x 10 (ou seja, 1.000) tuplas. O uso de subconsultas aqui pode ajudar a filtrar itens de matriz juntados antes de se juntar à próxima expressão.

Esta consulta é equivalente à anterior, mas usa subconsultas:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

Suponha que apenas um item na matriz de tags corresponda ao filtro, e há cinco itens para os conjuntos de nutrientes e porções. As expressões JOIN se expandirão para 1 x 1 x 5 x 5 = 25 itens, em oposição a 1.000 itens na primeira consulta.

## <a name="evaluate-once-and-reference-many-times"></a>Avalie uma vez e consulte muitas vezes

As subconsultas podem ajudar a otimizar consultas com expressões caras, como udfs definidos pelo usuário, strings complexas ou expressões aritméticas. Você pode usar uma subconsulta junto com uma expressão JOIN para avaliar a expressão uma vez, mas referencia-la muitas vezes.

A seguinte consulta executa `GetMaxNutritionValue` o UDF duas vezes:

```sql
SELECT c.id, udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue
FROM c
WHERE udf.GetMaxNutritionValue(c.nutrients) > 100
```

Aqui está uma consulta equivalente que executa o UDF apenas uma vez:

```sql
SELECT TOP 1000 c.id, MaxNutritionValue
FROM c
JOIN (SELECT VALUE udf.GetMaxNutritionValue(c.nutrients)) MaxNutritionValue
WHERE MaxNutritionValue > 100
``` 

> [!NOTE] 
> Tenha em mente o comportamento entre produtos das expressões JOIN. Se a expressão UDF puder avaliar como indefinida, você deve garantir que a expressão JOIN sempre produza uma única linha, devolvendo um objeto da subquery em vez do valor diretamente.
>

Aqui está um exemplo semelhante que retorna um objeto em vez de um valor:

```sql
SELECT TOP 1000 c.id, m.MaxNutritionValue
FROM c
JOIN (SELECT udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue) m
WHERE m.MaxNutritionValue > 100
```

A abordagem não se limita às UDFs. Aplica-se a qualquer expressão potencialmente cara. Por exemplo, você pode ter a `avg`mesma abordagem com a função matemática:

```sql
SELECT TOP 1000 c.id, AvgNutritionValue
FROM c
JOIN (SELECT VALUE avg(n.nutritionValue) FROM n IN c.nutrients) AvgNutritionValue
WHERE AvgNutritionValue > 80
```

## <a name="mimic-join-with-external-reference-data"></a>Imitar unir-se com dados de referência externos

Muitas vezes você pode precisar fazer referência a dados estáticos que raramente mudam, como unidades de medição ou códigos de país. É melhor não duplicar esses dados para cada documento. Evitar essa duplicação economizará no armazenamento e melhorará o desempenho de gravação mantendo o tamanho do documento menor. Você pode usar uma subconsulta para imitar a semântica de adesão interna com uma coleção de dados de referência.

Por exemplo, considere este conjunto de dados de referência:

| **Unidade** | **Nome**            | **Multiplicador** | **Unidade base** |
| -------- | ------------------- | -------------- | ------------- |
| Ng       | Nanogram            | 1.00E-09       | Grama          |
| Μg       | Micrograma           | 1.00E-06       | Grama          |
| mg       | Miligrama           | 1.00E-03       | Grama          |
| g        | Grama                | 1,00E+00       | Grama          |
| kg       | Quilograma            | 1,00E+03       | Grama          |
| Mg       | Megagram            | 1,00E+06       | Grama          |
| Gg       | Gigagrama            | 1,00E+09       | Grama          |
| Nj       | Nanojoule           | 1.00E-09       | Joule         |
| μJ       | Microjoule          | 1.00E-06       | Joule         |
| Mj       | Milijoule          | 1.00E-03       | Joule         |
| J        | Joule               | 1,00E+00       | Joule         |
| Kj       | Kilojoule           | 1,00E+03       | Joule         |
| Mj       | Megajoule           | 1,00E+06       | Joule         |
| Gj       | Gigajoule           | 1,00E+09       | Joule         |
| cal      | Calorias             | 1,00E+00       | Calorias       |
| Kcal     | Calorias             | 1,00E+03       | Calorias       |
| Ui       | Unidades internacionais |                |               |


A consulta a seguir imita a junção desses dados para que você adicione o nome da unidade à saída:

```sql
SELECT TOP 10 n.id, n.description, n.nutritionValue, n.units, r.name
FROM food
JOIN n IN food.nutrients
JOIN r IN (
    SELECT VALUE [
        {unit: 'ng', name: 'nanogram', multiplier: 0.000000001, baseUnit: 'gram'},
        {unit: 'µg', name: 'microgram', multiplier: 0.000001, baseUnit: 'gram'},
        {unit: 'mg', name: 'milligram', multiplier: 0.001, baseUnit: 'gram'},
        {unit: 'g', name: 'gram', multiplier: 1, baseUnit: 'gram'},
        {unit: 'kg', name: 'kilogram', multiplier: 1000, baseUnit: 'gram'},
        {unit: 'Mg', name: 'megagram', multiplier: 1000000, baseUnit: 'gram'},
        {unit: 'Gg', name: 'gigagram', multiplier: 1000000000, baseUnit: 'gram'},
        {unit: 'nJ', name: 'nanojoule', multiplier: 0.000000001, baseUnit: 'joule'},
        {unit: 'µJ', name: 'microjoule', multiplier: 0.000001, baseUnit: 'joule'},
        {unit: 'mJ', name: 'millijoule', multiplier: 0.001, baseUnit: 'joule'},
        {unit: 'J', name: 'joule', multiplier: 1, baseUnit: 'joule'},
        {unit: 'kJ', name: 'kilojoule', multiplier: 1000, baseUnit: 'joule'},
        {unit: 'MJ', name: 'megajoule', multiplier: 1000000, baseUnit: 'joule'},
        {unit: 'GJ', name: 'gigajoule', multiplier: 1000000000, baseUnit: 'joule'},
        {unit: 'cal', name: 'calorie', multiplier: 1, baseUnit: 'calorie'},
        {unit: 'kcal', name: 'Calorie', multiplier: 1000, baseUnit: 'calorie'},
        {unit: 'IU', name: 'International units'}
    ]
)
WHERE n.units = r.unit
```

## <a name="scalar-subqueries"></a>Subconsultas escalares

Uma expressão de subquery escalar é uma subquery que avalia a um único valor. O valor da expressão subquery escalar é o valor da projeção (cláusula SELECT) da subquery.  Você pode usar uma expressão de subquery escalar em muitos lugares onde uma expressão escalar é válida. Por exemplo, você pode usar uma subconsulta escalar em qualquer expressão nas cláusulas SELECT e WHERE.

Usar uma subquery escalar nem sempre ajuda a otimizar, no entanto. Por exemplo, passar uma subquery escalar como argumento para um sistema ou funções definidas pelo usuário não oferece nenhum benefício no consumo ou latência da unidade de recursos (RU).

Subperguntas escalares podem ser classificadas como:
* Subqueries escalares de expressão simples
* Subqueries escalares agregados

## <a name="simple-expression-scalar-subqueries"></a>Subqueries escalares de expressão simples

Uma subquery escalar de expressão simples é uma subconsulta correlacionada que tem uma cláusula SELECT que não contém expressões agregadas. Essas subconsultas não oferecem benefícios de otimização porque o compilador os converte em uma expressão simples maior. Não há contexto correlacionado entre as consultas internas e externas.

Veja alguns exemplos:

**Exemplo 1**

```sql
SELECT 1 AS a, 2 AS b
```

Você pode reescrever esta consulta, usando uma subquery escalar de expressão simples, para:

```sql
SELECT (SELECT VALUE 1) AS a, (SELECT VALUE 2) AS b
```

Ambas as consultas produzem esta saída:

```json
[
  { "a": 1, "b": 2 }
]
```

**Exemplo 2**

```sql
SELECT TOP 5 Concat('id_', f.id) AS id
FROM food f
```

Você pode reescrever esta consulta, usando uma subquery escalar de expressão simples, para:

```sql
SELECT TOP 5 (SELECT VALUE Concat('id_', f.id)) AS id
FROM food f
```

Saída de consulta:

```json
[
  { "id": "id_03226" },
  { "id": "id_03227" },
  { "id": "id_03228" },
  { "id": "id_03229" },
  { "id": "id_03230" }
]
```

**Exemplo 3**

```sql
SELECT TOP 5 f.id, Contains(f.description, 'fruit') = true ? f.description : undefined
FROM food f
```

Você pode reescrever esta consulta, usando uma subquery escalar de expressão simples, para:

```sql
SELECT TOP 10 f.id, (SELECT f.description WHERE Contains(f.description, 'fruit')).description
FROM food f
```

Saída de consulta:

```json
[
  { "id": "03230" },
  { "id": "03238", "description":"Babyfood, dessert, tropical fruit, junior" },
  { "id": "03229" },
  { "id": "03226", "description":"Babyfood, dessert, fruit pudding, orange, strained" },
  { "id": "03227" }
]
```

### <a name="aggregate-scalar-subqueries"></a>Subqueries escalares agregados

Uma subquery escalar agregada é uma subquery que tem uma função agregada em sua projeção ou filtro que avalia para um único valor.

**Exemplo 1:**

Aqui está uma subquery com uma única expressão de função agregada em sua projeção:

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg'
) AS count_mg
FROM food f
```

Saída de consulta:

```json
[
  { "id": "03230", "count_mg": 13 },
  { "id": "03238", "count_mg": 14 },
  { "id": "03229", "count_mg": 13 },
  { "id": "03226", "count_mg": 15 },
  { "id": "03227", "count_mg": 19 }
]
```

**Exemplo 2**

Aqui está uma subconsulta com múltiplas expressões de função agregadas:

```sql
SELECT TOP 5 f.id, (
    SELECT Count(1) AS count, Sum(n.nutritionValue) AS sum 
    FROM n IN f.nutrients 
    WHERE n.units = 'mg'
) AS unit_mg
FROM food f
```

Saída de consulta:

```json
[
  { "id": "03230","unit_mg": { "count": 13,"sum": 147.072 } },
  { "id": "03238","unit_mg": { "count": 14,"sum": 107.385 } },
  { "id": "03229","unit_mg": { "count": 13,"sum": 141.579 } },
  { "id": "03226","unit_mg": { "count": 15,"sum": 183.91399999999996 } },
  { "id": "03227","unit_mg": { "count": 19,"sum": 94.788999999999987 } }
]
```

**Exemplo 3**

Aqui está uma consulta com uma subquery agregada tanto na projeção quanto no filtro:

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
FROM food f
WHERE (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') > 20
```

Saída de consulta:

```json
[
  { "id": "03235", "count_mg": 27 },
  { "id": "03246", "count_mg": 21 },
  { "id": "03267", "count_mg": 21 },
  { "id": "03269", "count_mg": 21 },
  { "id": "03274", "count_mg": 21 }
]
```

Uma maneira mais ideal de escrever essa consulta é participar da subquery e referenciar o alias de subquery nas cláusulas SELECT e WHERE. Esta consulta é mais eficiente porque você precisa executar a subconsulta apenas dentro da declaração de adesão, e não tanto na projeção quanto no filtro.

```sql
SELECT TOP 5 f.id, count_mg
FROM food f
JOIN (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
WHERE count_mg > 20
```

## <a name="exists-expression"></a>EXPRESSÃO EXISTE

O Azure Cosmos DB suporta expressões EXISTAs. Esta é uma subquery escalar agregada incorporada à API Azure Cosmos DB SQL. EXISTE é uma expressão booleana que toma uma expressão de subquery e retorna verdadeira se a subquery retornar quaisquer linhas. Caso contrário, ele retorna falso.

Como a API Azure Cosmos DB SQL não diferencia expressões booleanas e outras expressões escalares, você pode usar EXISTIn tanto em cláusulas SELECT quanto WHERE. Isso é diferente do T-SQL, onde uma expressão booleana (por exemplo, EXISTE, ENTRE e DENTRO) é restrita ao filtro.

Se a subquery EXISTE retornar um único valor indefinido, o EXIST avaliará como falso. Por exemplo, considere a seguinte consulta que avalia como falsa:
```sql
SELECT EXISTS (SELECT VALUE undefined)
```   


Se a palavra-chave VALOR na subquery anterior for omitida, a consulta avaliará como verdadeira:
```sql
SELECT EXISTS (SELECT undefined) 
```

A subquery incluirá a lista de valores na lista selecionada em um objeto. Se a lista selecionada não tiver valores, a{}subquery retornará o valor único ' '. Esse valor é definido, então existe avalia a verdade.

### <a name="example-rewriting-array_contains-and-join-as-exists"></a>Exemplo: Reescrever ARRAY_CONTAINS e JUNTAR como EXISTE

Um caso de uso comum de ARRAY_CONTAINS é filtrar um documento pela existência de um item em uma matriz. Neste caso, estamos verificando se a matriz de tags contém um item chamado "laranja".

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE ARRAY_CONTAINS(f.tags, {name: 'orange'})
```

Você pode reescrever a mesma consulta para usar EXISTIs:

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE EXISTS(SELECT VALUE t FROM t IN f.tags WHERE t.name = 'orange')
```

Além disso, ARRAY_CONTAINS só pode verificar se um valor é igual a qualquer elemento dentro de uma matriz. Se precisar de filtros mais complexos nas propriedades do array, use JOIN.

Considere a seguinte consulta que filtra com `nutritionValue` base nas unidades e propriedades na matriz: 

```sql
SELECT VALUE c.description
FROM c
JOIN n IN c.nutrients
WHERE n.units= "mg" AND n.nutritionValue > 0
```

Para cada um dos documentos da coleção, um produto cruzado é realizado com seus elementos de matriz. Esta operação JOIN permite filtrar as propriedades dentro da matriz. No entanto, o consumo de RU desta consulta será significativo. Por exemplo, se 1.000 documentos tivessem 100 itens em cada matriz, ele se expandiria para 1.000 x 100 (ou seja, 100.000) tuplas.

O uso do EXIST pode ajudar a evitar este produto cruzado caro:

```sql
SELECT VALUE c.description
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0
)
```

Neste caso, você filtra elementos de matriz dentro da subquery EXIST. Se um elemento de matriz corresponder ao filtro, então você projetá-lo e EXISTE avalia como verdadeiro.

Você também pode alias EXISTE e referencia-lo na projeção:

```sql
SELECT TOP 1 c.description, EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0) as a
FROM c
```

Saída de consulta:

```json
[
    {
        "description": "Babyfood, dessert, fruit pudding, orange, strained",
        "a": true
    }
]
```

## <a name="array-expression"></a>Expressão ARRAY

Você pode usar a expressão ARRAY para projetar os resultados de uma consulta como uma matriz. Você só pode usar essa expressão dentro da cláusula SELECT da consulta.

```sql
SELECT TOP 1   f.id, ARRAY(SELECT VALUE t.name FROM t in f.tags) AS tagNames
FROM  food f
```

Saída de consulta:

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

Como em outras subconsultas, filtros com a expressão ARRAY são possíveis.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t FROM t in c.tags WHERE t.name != 'infant formula') AS tagNames
FROM c
```

Saída de consulta:

```json
[
    {
        "id": "03226",
        "tagNames": [
            {
                "name": "babyfood"
            },
            {
                "name": "dessert"
            },
            {
                "name": "fruit pudding"
            },
            {
                "name": "orange"
            },
            {
                "name": "strained"
            }
        ]
    }
]
```

Expressões de matriz também podem vir após a cláusula FROM em subconsultas.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t.name FROM t in c.tags) as tagNames
FROM c
JOIN n IN (SELECT VALUE ARRAY(SELECT t FROM t in c.tags WHERE t.name != 'infant formula'))
```

Saída de consulta:

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

## <a name="next-steps"></a>Próximas etapas

- [Amostras do .NET no Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Dados de documento modelo](modeling-data.md)
