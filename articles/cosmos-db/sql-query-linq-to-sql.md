---
title: TRADUÇÃO LINQ para SQL no Azure Cosmos DB
description: Aprenda os operadores LINQ suportados e como as consultas LINQ são mapeadas para consultas SQL no Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: d43f95b91df7d0c9c442339de51936200f4688e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441246"
---
# <a name="linq-to-sql-translation"></a>Tradução de LINQ em SQL

O provedor de consulta Azure Cosmos DB realiza um mapeamento de melhor esforço de uma consulta LINQ em uma consulta Cosmos DB SQL. A descrição a seguir assume uma familiaridade básica com linq.

O sistema de tipo de provedor de consulta suporta apenas os tipos primitivos JSON: numérico, booleano, string e nulo.

O provedor de consulta suporta as seguintes expressões escalares:

- Valores constantes, incluindo valores constantes dos tipos de dados primitivos no tempo de avaliação da consulta.
  
- Expressões de índice de propriedade/matriz que se referem à propriedade de um objeto ou de um elemento de matriz. Por exemplo: 
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- Expressões aritméticas, incluindo expressões aritméticas comuns em valores numéricos e booleanos. Para obter a lista completa, consulte a [especificação Azure Cosmos DB SQL](https://go.microsoft.com/fwlink/p/?LinkID=510612).
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- Expressões de comparação de cordas, que incluem comparar um valor de string com algum valor de seqüência constante.  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- Expressões de criação de objeto/matriz, que retornam um objeto de tipo de valor composto ou tipo anônimo, ou uma matriz de tais objetos. Você pode aninhar esses valores.
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

## <a name="supported-linq-operators"></a><a id="SupportedLinqOperators"></a>Operadores LINQ suportados

O provedor LINQ incluído no SQL .NET SDK suporta os seguintes operadores:

- **Selecione**: Projeções se traduzem em SQL SELECT, incluindo construção de objetos.
- **Onde**: Filtros se traduzem para SQL WHERE, e suportam tradução entre `&&`, `||`e `!` para os operadores SQL
- **SelectMany**: permite o desenrolamento de matrizes à cláusula SQL JOIN. Use para acorrentar ou aninhar expressões para filtrar os elementos da matriz.
- **OrderBy** e **OrderByDescending**: Traduzir para ordem por com ASC ou DESC.
- Operadores **Count**, **Sum**, **Min**, **Max** e **Average** para agregação e os seus equivalentes assíncronos, **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync** e **AverageAsync**.
- **CompareTo**: é convertido em comparações de intervalo. Comumente usado para strings, uma vez que eles não são comparáveis em .NET.
- **Skip** and **Take**: Traduz-se para SQL OFFSET e LIMIT para limitar resultados de uma consulta e fazer paginação.
- **Funções matemáticas**: Suporta `Abs`a `Acos` `Asin`tradução `Atan` `Ceiling`de `Cos` `Exp`.NET , , , , , , , , , `Floor`, , `Log`, , `Log10` `Pow` `Round`, `Sign` `Sin`, `Sqrt`, `Tan`, , , , , e `Truncate` para as funções incorporadas SQL equivalentes.
- **Funções das cordas**: Suporta `Concat` `Contains`a `Count` `EndsWith`tradução`IndexOf` `Replace`de `Reverse` `StartsWith`.NET , , , , , , , , `SubString`, `ToLower`, `ToUpper`, , `TrimEnd`, , e `TrimStart` para as funções incorporadas SQL equivalentes.
- **Funções do array**: Suporta `Concat` `Contains`tradução `Count` de .NET , e para as funções incorporadas sql equivalentes.
- **Funções de extensão geoespacial**: `Distance`Suporta `IsValid` `IsValidDetailed`a `Within` tradução dos métodos stub , , e para as funções incorporadas sql equivalentes.
- **Função de extensão de função definida**pelo `UserDefinedFunctionProvider.Invoke` usuário : Suporta a tradução do método stub para a função correspondente definida pelo usuário.
- **Diversos :** Suporta tradução de `Coalesce` operadores e operadores condicionais. Pode `Contains` ser traduzido para String CONTAINS, ARRAY_CONTAINS ou SQL IN, dependendo do contexto.

## <a name="examples"></a>Exemplos

Os exemplos a seguir ilustram como alguns dos operadores de consulta linq padrão se traduzem em consultas do Cosmos DB.

### <a name="select-operator"></a>Selecionar operador

A sintaxe é `input.Select(x => f(x))`, em que `f` é uma expressão escalar.

**Selecione o operador, exemplo 1:**

- **Expressão lambda do LINQ**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**Selecione o operador, exemplo 2:** 

- **Expressão lambda do LINQ**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**Selecione o operador, exemplo 3:**

- **Expressão lambda do LINQ**
  
  ```csharp
    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });
  ```
  
- **SQL** 
  
  ```sql
      SELECT VALUE {"name":f.children[0].familyName,
                    "grade": f.children[0].grade + 3 }
      FROM Families f
  ```

### <a name="selectmany-operator"></a>Operador SelectMany

A sintaxe é `input.SelectMany(x => f(x))`, em que `f` é uma expressão escalar que retorna um tipo de contêiner.

- **Expressão lambda do LINQ**
  
  ```csharp
      input.SelectMany(family => family.children);
  ```
  
- **SQL**

  ```sql
      SELECT VALUE child
      FROM child IN Families.children
  ```

### <a name="where-operator"></a>Operador Where

A sintaxe é `input.Where(x => f(x))`, em que `f` é uma expressão escalar que retorna um valor booliano.

**Onde operador, exemplo 1:**

- **Expressão lambda do LINQ**
  
  ```csharp
      input.Where(family=> family.parents[0].familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```
  
**Onde operador, exemplo 2:**

- **Expressão lambda do LINQ**
  
  ```csharp
      input.Where(
          family => family.parents[0].familyName == "Wakefield" &&
          family.children[0].grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
      AND f.children[0].grade < 3
  ```

## <a name="composite-sql-queries"></a>Consultas SQL composta

Você pode compor os operadores anteriores para formar consultas mais poderosas. Como o Cosmos DB suporta recipientes aninhados, você pode concatenar ou aninhar a composição.

### <a name="concatenation"></a>Concatenação

A sintaxe é `input(.|.SelectMany())(.Select()|.Where())*`. Uma consulta concatenada pode começar `SelectMany` com uma consulta `Select` opcional, seguida por vários ou `Where` operadores.

**Concatenação, exemplo 1:**

- **Expressão lambda do LINQ**
  
  ```csharp
      input.Select(family => family.parents[0])
          .Where(parent => parent.familyName == "Wakefield");
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```

**Concatenação, exemplo 2:**

- **Expressão lambda do LINQ**
  
  ```csharp
      input.Where(family => family.children[0].grade > 3)
          .Select(family => family.parents[0].familyName);
  ```

- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
      WHERE f.children[0].grade > 3
  ```

**Concatenação, exemplo 3:**

- **Expressão lambda do LINQ**
  
  ```csharp
      input.Select(family => new { grade=family.children[0].grade}).
          Where(anon=> anon.grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE ({grade: f.children[0].grade}.grade > 3)
  ```

**Concatenação, exemplo 4:**

- **Expressão lambda do LINQ**
  
  ```csharp
      input.SelectMany(family => family.parents)
          .Where(parent => parents.familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM p IN Families.parents
      WHERE p.familyName = "Wakefield"
  ```

### <a name="nesting"></a>Aninhamento

A sintaxe `Q` é `Select` `input.SelectMany(x=>x.Q())` `SelectMany`onde `Where` está um , ou operador.

Uma consulta aninhada aplica a consulta interna a cada elemento do recipiente externo. Uma característica importante é que a consulta interna pode se referir aos campos dos elementos no recipiente externo, como uma auto-adesão.

**Aninhamento, exemplo 1:**

- **Expressão lambda do LINQ**
  
  ```csharp
      input.SelectMany(family=>
          family.parents.Select(p => p.familyName));
  ```

- **SQL**
  
  ```sql
      SELECT VALUE p.familyName
      FROM Families f
      JOIN p IN f.parents
  ```

**Aninhamento, exemplo 2:**

- **Expressão lambda do LINQ**
  
  ```csharp
      input.SelectMany(family =>
          family.children.Where(child => child.familyName == "Jeff"));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = "Jeff"
  ```

**Aninhamento, exemplo 3:**

- **Expressão lambda do LINQ**
  
  ```csharp
      input.SelectMany(family => family.children.Where(
          child => child.familyName == family.parents[0].familyName));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = f.parents[0].familyName
  ```


## <a name="next-steps"></a>Próximas etapas

- [Amostras do .NET no Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Dados de documento modelo](modeling-data.md)
