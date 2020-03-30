---
title: Operadores de consulta SQL para Azure Cosmos DB
description: Saiba mais sobre operadores SQL, como igualdade, comparação e operadores lógicos suportados pelo Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: tisande
ms.openlocfilehash: 8ef41edb687a5df39243880c897d12e83c008ec9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063569"
---
# <a name="operators-in-azure-cosmos-db"></a>Operadores em Azure Cosmos DB

Este artigo detalha os vários operadores apoiados pelo Azure Cosmos DB.

## <a name="equality-and-comparison-operators"></a>Operadores de Igualdade e Comparação

A tabela a seguir mostra o resultado de comparações de igualdade na API do SQL entre dois tipos JSON quaisquer.

| **Op** | **Indefinido** | **Null** | **Boolean** | **Número** | **Cadeia de caracteres** | **Objeto** | **Matriz** |
|---|---|---|---|---|---|---|---|
| **Indefinido** | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido |
| **Null** | Indefinido | **Okey** | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido |
| **Boolean** | Indefinido | Indefinido | **Okey** | Indefinido | Indefinido | Indefinido | Indefinido |
| **Número** | Indefinido | Indefinido | Indefinido | **Okey** | Indefinido | Indefinido | Indefinido |
| **Cadeia de caracteres** | Indefinido | Indefinido | Indefinido | Indefinido | **Okey** | Indefinido | Indefinido |
| **Objeto** | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido | **Okey** | Indefinido |
| **Matriz** | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido | **Okey** |

Para operadores de `>` `>=`comparação `!=` `<`tais `<=`como , , , , e , `Undefined`comparação entre tipos ou entre dois objetos ou matrizes produz .  

Se o resultado da expressão `Undefined`escalar for , o item não `Undefined` está incluído `true`no resultado, porque não é igual .

## <a name="logical-and-or-and-not-operators"></a>Operadores lógicos (AND, OR e NOT)

Operadores lógicos funcionam em valores boolianos. As tabelas a seguir mostram as tabelas de verdade lógicas para esses operadores:

**Operador OU**

Retorna `true` quando qualquer uma `true`das condições é .

|  | **True** | **Falso** | **Indefinido** |
| --- | --- | --- | --- |
| **True** |True |True |True |
| **Falso** |True |Falso |Indefinido |
| **Indefinido** |True |Indefinido |Indefinido |

**E o operador**

Retorna `true` quando ambas `true`as expressões são .

|  | **True** | **Falso** | **Indefinido** |
| --- | --- | --- | --- |
| **True** |True |Falso |Indefinido |
| **Falso** |Falso |Falso |Falso |
| **Indefinido** |Indefinido |Falso |Indefinido |

**NÃO operador**

Inverte o valor de qualquer expressão booleana.

|  | **NOT** |
| --- | --- |
| **True** |Falso |
| **Falso** |True |
| **Indefinido** |Indefinido |

**Precedência de operador**

Os operadores `OR` `AND`lógicos `NOT` e têm o nível de precedência abaixo:

| **Operador** | **Priority** |
| --- | --- |
| **NOT** |1 |
| **E** |2 |
| **Ou** |3 |

## <a name="-operator"></a>Operador *

O operador especial * projeta todo o item como está. Quando usado, ele deve ser o único campo projetado. Uma consulta `SELECT * FROM Families f` como é `SELECT VALUE * FROM Families f` válida, mas e `SELECT *, f.id FROM Families f` não é válida.

## <a name="-and--operators"></a>? E?? operadores

Você pode usar os operadores Ternary (?) e Coalesce (??) para construir expressões condicionais, como em linguagens de programação como C# e JavaScript.

Você pode usar o? operador para construir novas propriedades JSON em tempo real. Por exemplo, a consulta a seguir `elementary` classifica `other`os níveis de nota em ou:

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

Você também pode fazer chamadas para o? operador, como na seguinte consulta: 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

Como com outros operadores de consulta, o ? operador exclui itens se as propriedades referenciadas estão faltando ou os tipos que estão sendo comparados são diferentes.

Use o ?? operador para verificar eficientemente se há uma propriedade em um item ao consultar dados semiestruturados ou de tipo misto. Por exemplo, a consulta `lastName` a seguir `surname` `lastName` retorna se estiver presente ou se não estiver presente.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>Próximas etapas

- [Amostras do .NET no Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Palavras-chave](sql-query-keywords.md)
- [Cláusula SELECT](sql-query-select.md)
