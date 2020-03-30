---
title: ONDE cláusula no Azure Cosmos DB
description: Saiba mais sobre a cláusula SQL WHERE para Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: tisande
ms.openlocfilehash: 483a0533eafc81ef8698d260a753062ae074f6d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898760"
---
# <a name="where-clause-in-azure-cosmos-db"></a>ONDE cláusula no Azure Cosmos DB

A cláusula OPCIONAL`WHERE <filter_condition>`WHERE ( ) especifica as condições(s) que os itens JSON de origem devem satisfazer para que a consulta os inclua nos resultados. Um item JSON deve avaliar as `true` condições especificadas a serem consideradas para o resultado. A camada de índice usa a cláusula WHERE para determinar o menor subconjunto de itens de origem que podem fazer parte do resultado.
  
## <a name="syntax"></a>Sintaxe
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>Argumentos

- `<filter_condition>`  
  
   Especifica a condição a ser atendida para que os documentos sejam retornados.  
  
- `<scalar_expression>`  
  
   Expressão que representa o valor a ser calculado. Consulte [expressões Escalar para](sql-query-scalar-expressions.md) obter detalhes.  
  
## <a name="remarks"></a>Comentários
  
  Para que o documento seja retornado, uma expressão especificada como condição de filtro deve ser avaliada como verdadeira. Apenas o `true` valor booleano satisfará a condição, qualquer outro valor: indefinido, nulo, falso, Número, Matriz ou Objeto não satisfará a condição.

  Se você incluir sua `WHERE` chave de partição na cláusula como parte de um filtro de igualdade, sua consulta será filtrada automaticamente apenas para as partições relevantes.

## <a name="examples"></a>Exemplos

A consulta a seguir solicita itens `id` que contenham uma propriedade cujo valor é `AndersenFamily`. Exclui qualquer item que não `id` tenha um imóvel ou `AndersenFamily`cujo valor não corresponda.

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Os resultados são:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

### <a name="scalar-expressions-in-the-where-clause"></a>Expressões escalares na cláusula WHERE

O exemplo anterior mostrou uma consulta de igualdade simples. A API SQL também suporta várias [expressões escalares](sql-query-scalar-expressions.md). As expressões mais usadas são as binárias e unárias. Referências de propriedade do objeto JSON fonte também são expressões válidas.

Você pode usar os seguintes operadores binários suportados:  

|**Tipo de operador**  | **Valores** |
|---------|---------|
|Aritmético | +,-,*,/,% |
|Bit a bit    | \|, &, ^, <<, >>, >>> (deslocamento à direita com preenchimento com zero) |
|Lógico    | AND, OR, NOT      |
|Comparação | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|String     |  \|\| (concatenar) |

As seguintes consultas usam operadores binários:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5    -- matching grades == 5
```

Você também pode usar os operadores não-ary +,-, ~, e NÃO em consultas, como mostrado nos exemplos a seguir:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

Você também pode usar referências de propriedade em consultas. Por exemplo, `SELECT * FROM Families f WHERE f.isRegistered` devolve o item JSON contendo a propriedade `isRegistered` com valor igual a `true`. Qualquer outro valor, `false` `null`como, , `<object>`, `<array>` `Undefined` `<number>`, `<string>`, , ou , ou , exclui o item do resultado.

## <a name="next-steps"></a>Próximas etapas

- [Começando](sql-query-getting-started.md)
- [Palavra-chave IN](sql-query-keywords.md#in)
- [Cláusula de cláusula](sql-query-from.md)