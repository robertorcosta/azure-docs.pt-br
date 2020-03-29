---
title: Consultas sql JOIN para Azure Cosmos DB
description: Saiba como JUNTAR várias tabelas no Azure Cosmos DB para consultar os dados
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: 38e80f1597a08b8db7cbfa852d1bcf38ac768b1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74871135"
---
# <a name="joins-in-azure-cosmos-db"></a>Junta-se ao Azure Cosmos DB

Em um banco de dados relacional, junta-se através de tabelas são o corolário lógico para projetar esquemas normalizados. Em contraste, a API SQL usa o modelo de dados desnormalizado de itens livres de esquemas, que é o equivalente lógico de uma *auto-adesão*.

Junções internas resultam em um produto completo cruzando os conjuntos associados à junção. O resultado de uma junção de N maneiras é um conjunto de tuplas com N elementos, em que cada valor na tupla é associado ao alias do conjunto membro da junção e pode ser acessado pela referência desse alias em outras cláusulas.

## <a name="syntax"></a>Sintaxe

A linguagem suporta a `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`sintaxe. Esta consulta retorna um conjunto de `N` tuplas com valores. Cada tupla possui valores produzidos pela iteração de todos os aliases de contêiner sobre seus respectivos conjuntos. 

Vamos começar com a seguinte cláusula FROM: `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 Permite que cada fonte defina `input_alias1, input_alias2, …, input_aliasN`. Essa cláusula FROM retorna um conjunto de tuplas N (tupla com valores N). Cada tupla possui valores produzidos pela iteração de todos os aliases de contêiner sobre seus respectivos conjuntos.  
  
**Exemplo 1** - 2 fontes  
  
- Permite que `<from_source1>` tenha escopo de contêiner e represente o conjunto {A, B, C}.  
  
- Permite que `<from_source2>` tenha escopo de documento, referenciando input_alias1 e represente os conjuntos:  
  
    {1,2} para `input_alias1 = A,`  
  
    {3} para `input_alias1 = B,`  
  
    {4,5} para`input_alias1 = C,`  
  
- A cláusula FROM `<from_source1> JOIN <from_source2>` resultará nas tuplas abaixo:  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
**Exemplo 2** - 3 fontes  
  
- Permite que `<from_source1>` tenha escopo de contêiner e represente o conjunto {A, B, C}.  
  
- Permite que `<from_source2>` tenha escopo de documento, referenciando `input_alias1` e represente os conjuntos:  
  
    {1,2} para `input_alias1 = A,`  
  
    {3} para `input_alias1 = B,`  
  
    {4,5} para`input_alias1 = C,`  
  
- Permite que `<from_source3>` tenha escopo de documento, referenciando `input_alias2` e represente os conjuntos:  
  
    {100, 200} para`input_alias2 = 1,`  
  
    {300} para `input_alias2 = 3,`  
  
- A cláusula FROM `<from_source1> JOIN <from_source2> JOIN <from_source3>` resultará nas tuplas abaixo:  
  
    (input_alias1, input_alias2, input_alias3):  
  
    (A, 1, 100), (A, 1, 200), (B, 3, 300)  
  
  > [!NOTE]
  > Falta de tuplas para outros valores de `input_alias1`, `input_alias2`, para os quais `<from_source3>` não retornou nenhum valor.  
  
**Exemplo 3** - 3 fontes  
  
- Permite que <from_source1> tenha escopo de contêiner e represente o conjunto {A, B, C}.  
  
- Permite que `<from_source1>` tenha escopo de contêiner e represente o conjunto {A, B, C}.  
  
- Permite que <from_source2> tenha escopo de documento, referenciando input_alias1 e represente os conjuntos:  
  
    {1,2} para `input_alias1 = A,`  
  
    {3} para `input_alias1 = B,`  
  
    {4,5} para`input_alias1 = C,`  
  
- Permite que `<from_source3>` tenha escopo `input_alias1` e represente os conjuntos:  
  
    {100, 200} para`input_alias2 = A,`  
  
    {300} para `input_alias2 = C,`  
  
- A cláusula FROM `<from_source1> JOIN <from_source2> JOIN <from_source3>` resultará nas tuplas abaixo:  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1, 100), (A, 1, 200), (A, 2, 100), (A, 2, 200),  (C, 4, 300) , (C, 5, 300)  
  
  > [!NOTE]
  > Isso resultou em produto cruzado entre `<from_source2>` e `<from_source3>` porque ambos têm o escopo para a mesma `<from_source1>`.  Isso resultou em 4 (2 x 2) tuplas com valor A, 0 tuplas com valor B (1 x 0) e 2 (2 x 1) tuplas com valor C.  
  
## <a name="examples"></a>Exemplos

Os exemplos a seguir mostram como a cláusula junção funciona. Antes de executar esses exemplos, faça upload dos [dados da família](sql-query-getting-started.md#upload-sample-data)de amostras . No exemplo a seguir, o resultado está vazio, uma vez que o produto cruzado de cada item da origem e um conjunto vazio está vazio:

```sql
    SELECT f.id
    FROM Families f
    JOIN f.NonExistent
```

O resultado é:

```json
    [{
    }]
```

No exemplo a seguir, a adesão é um produto cruzado `id` entre `children` dois objetos JSON, a raiz do item e a subraiz. O fato `children` de ser uma matriz não é eficaz na adesão, `children` porque lida com uma única raiz que é a matriz. O resultado contém apenas dois resultados, porque o produto cruzado de cada item com a matriz rende exatamente apenas um item.

```sql
    SELECT f.id
    FROM Families f
    JOIN f.children
```

Os resultados são:

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

O exemplo a seguir mostra uma junção mais convencional:

```sql
    SELECT f.id
    FROM Families f
    JOIN c IN f.children
```

Os resultados são:

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

A fonte FROM da cláusula JOIN é um iterizador. Assim, o fluxo no exemplo anterior é:  

1. Expanda cada `c` elemento filho na matriz.
2. Aplique um produto cruzado com a `f` raiz do `c` item com cada elemento filho que o primeiro passo achatado.
3. Finalmente, projete `f` `id` a propriedade do objeto raiz sozinho.

O primeiro item contém `AndersenFamily` `children` apenas um elemento, de modo que o conjunto de resultados contém apenas um único objeto. O segundo item, `WakefieldFamily` `children`contém dois , então o produto transversal produz dois objetos, um para cada `children` elemento. Os campos raiz em ambos os itens são os mesmos, exatamente como você esperaria em um produto cruzado.

A utilidade real da cláusula JOIN é formar tuplas do produto cruzado em uma forma que de outra forma é difícil de projetar. O exemplo abaixo filtra a combinação de uma tupla que permite ao usuário escolher uma condição satisfeita pelas tuplas em geral.

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
```

Os resultados são:

```json
    [
      {
        "familyName": "AndersenFamily",
        "childFirstName": "Henriette Thaulow",
        "petName": "Fluffy"
      },
      {
        "familyName": "WakefieldFamily",
        "childGivenName": "Jesse",
        "petName": "Goofy"
      }, 
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

A extensão a seguir do exemplo anterior realiza uma dupla adesão. Você pode ver o produto cruzado como o seguinte pseudo-código:

```
    for-each(Family f in Families)
    {
        for-each(Child c in f.children)
        {
            for-each(Pet p in c.pets)
            {
                return (Tuple(f.id AS familyName,
                  c.givenName AS childGivenName,
                  c.firstName AS childFirstName,
                  p.givenName AS petName));
            }
        }
    }
```

`AndersenFamily`tem uma criança que tem um animal de estimação, então o produto cruzado rende uma linha (1\*111)\*dessa família. `WakefieldFamily`tem dois filhos, apenas um dos quais tem animais de estimação, mas essa criança tem dois animais de estimação. O produto cruzado para\*esta\*família rende 1 1 2 = 2 linhas.

No exemplo seguinte, há um `pet`filtro adicional ligado, que exclui todas as `Shadow`tuplas onde o nome do animal de estimação não está . Você pode construir tuplas a partir de matrizes, filtrar qualquer um dos elementos da tupla e projetar qualquer combinação dos elementos.

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"
```

Os resultados são:

```json
    [
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

## <a name="next-steps"></a>Próximas etapas

- [Começando](sql-query-getting-started.md)
- [Amostras do .NET no Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Subconsultas](sql-query-subquery.md)