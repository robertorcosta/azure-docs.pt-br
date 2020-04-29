---
title: Cláusula WHERE em Azure Cosmos DB
description: Saiba mais sobre a cláusula WHERE do SQL para Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: tisande
ms.openlocfilehash: 483a0533eafc81ef8698d260a753062ae074f6d4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "78898760"
---
# <a name="where-clause-in-azure-cosmos-db"></a>Cláusula WHERE em Azure Cosmos DB

A cláusula WHERE opcional (`WHERE <filter_condition>`) especifica a (s) condição (ões) que os itens JSON de origem devem satisfazer para que a consulta as inclua nos resultados. Um item JSON deve avaliar as condições `true` especificadas a serem consideradas para o resultado. A camada de índice usa a cláusula WHERE para determinar o menor subconjunto de itens de origem que podem fazer parte do resultado.
  
## <a name="syntax"></a>Sintaxe
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>Argumentos

- `<filter_condition>`  
  
   Especifica a condição a ser atendida para que os documentos sejam retornados.  
  
- `<scalar_expression>`  
  
   Expressão que representa o valor a ser calculado. Consulte [expressões escalares](sql-query-scalar-expressions.md) para obter detalhes.  
  
## <a name="remarks"></a>Comentários
  
  Para que o documento seja retornado, uma expressão especificada como condição de filtro deve ser avaliada como verdadeira. Somente o valor `true` booliano atenderá à condição, qualquer outro valor: indefinido, nulo, falso, número, matriz ou objeto não atenderá à condição.

  Se você incluir sua chave de partição na `WHERE` cláusula como parte de um filtro de igualdade, sua consulta filtrará automaticamente somente as partições relevantes.

## <a name="examples"></a>Exemplos

A consulta a seguir solicita itens que contêm `id` uma propriedade cujo valor `AndersenFamily`é. Ele exclui qualquer item que não tenha uma `id` propriedade ou cujo valor não corresponda. `AndersenFamily`

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

O exemplo anterior mostrou uma consulta de igualdade simples. A API do SQL também dá suporte a várias [expressões escalares](sql-query-scalar-expressions.md). As expressões mais usadas são as binárias e unárias. Referências de propriedade do objeto JSON fonte também são expressões válidas.

Você pode usar os seguintes operadores binários com suporte:  

|**Tipo de operador**  | **Valores** |
|---------|---------|
|Aritmético | +,-,*,/,% |
|Bit a bit    | \|, &, ^, <<, >>, >>> (deslocamento à direita com preenchimento com zero) |
|Lógico    | AND, OR, NOT      |
|Comparação | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|Cadeia de caracteres     |  \|\| (concatenar) |

As consultas a seguir usam operadores binários:

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

Você também pode usar os operadores unários +,-, ~, e não em consultas, conforme mostrado nos exemplos a seguir:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

Você também pode usar referências de propriedade em consultas. Por exemplo, `SELECT * FROM Families f WHERE f.isRegistered` retorna o item JSON que contém a `isRegistered` Propriedade com valor igual `true`a. Qualquer outro valor, como `false`, `null` `Undefined` `<number>`,,, `<string>`, `<object>`ou `<array>`, exclui o item do resultado.

## <a name="next-steps"></a>Próximas etapas

- [Introdução](sql-query-getting-started.md)
- [Palavra-chave IN](sql-query-keywords.md#in)
- [Cláusula FROM](sql-query-from.md)