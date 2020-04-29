---
title: Operadores de consulta SQL para Azure Cosmos DB
description: Saiba mais sobre os operadores SQL, como igualdade, comparação e operadores lógicos com suporte pelo Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: tisande
ms.openlocfilehash: 8ef41edb687a5df39243880c897d12e83c008ec9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80063569"
---
# <a name="operators-in-azure-cosmos-db"></a>Operadores no Azure Cosmos DB

Este artigo fornece detalhes sobre os vários operadores com suporte pelo Azure Cosmos DB.

## <a name="equality-and-comparison-operators"></a>Operadores de igualdade e comparação

A tabela a seguir mostra o resultado de comparações de igualdade na API do SQL entre dois tipos JSON quaisquer.

| **Parar** | **Indefinido** | **Null** | **Booliano** | **Número** | **Cadeia de caracteres** | **Objeto** | **Variedade** |
|---|---|---|---|---|---|---|---|
| **Indefinido** | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido |
| **Null** | Indefinido | **Okey** | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido |
| **Booliano** | Indefinido | Indefinido | **Okey** | Indefinido | Indefinido | Indefinido | Indefinido |
| **Número** | Indefinido | Indefinido | Indefinido | **Okey** | Indefinido | Indefinido | Indefinido |
| **Cadeia de caracteres** | Indefinido | Indefinido | Indefinido | Indefinido | **Okey** | Indefinido | Indefinido |
| **Objeto** | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido | **Okey** | Indefinido |
| **Variedade** | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido | **Okey** |

Para operadores de comparação como `>`, `>=` `!=` `<`,, e `<=`, a comparação entre os tipos ou entre dois objetos ou matrizes produz `Undefined`.  

Se o resultado da expressão escalar for `Undefined`, o item não será incluído no resultado, porque `Undefined` não é igual `true`a.

## <a name="logical-and-or-and-not-operators"></a>Operadores lógicos (AND, OR e NOT)

Operadores lógicos funcionam em valores boolianos. As tabelas a seguir mostram as tabelas lógicas da verdade para esses operadores:

**Operador OR**

Retorna `true` quando uma das condições é `true`.

|  | **verdadeiro** | **For** | **Indefinido** |
| --- | --- | --- | --- |
| **verdadeiro** |verdadeiro |verdadeiro |verdadeiro |
| **For** |verdadeiro |Falso |Indefinido |
| **Indefinido** |verdadeiro |Indefinido |Indefinido |

**Operador AND**

Retorna `true` quando ambas as expressões `true`são.

|  | **verdadeiro** | **For** | **Indefinido** |
| --- | --- | --- | --- |
| **verdadeiro** |verdadeiro |Falso |Indefinido |
| **For** |Falso |Falso |Falso |
| **Indefinido** |Indefinido |Falso |Indefinido |

**Operador NOT**

Reverte o valor de qualquer expressão booleana.

|  | **NOT** |
| --- | --- |
| **verdadeiro** |Falso |
| **For** |verdadeiro |
| **Indefinido** |Indefinido |

**Precedência de operador**

Os operadores `OR`lógicos `AND`, e `NOT` têm o nível de precedência mostrado abaixo:

| **Operador** | **Priority** |
| --- | --- |
| **NOT** |1 |
| **E** |2 |
| **OR** |3 |

## <a name="-operator"></a>Operador *

O operador especial * projeta o item inteiro como está. Quando usado, ele deve ser o único campo projetado. Uma consulta como `SELECT * FROM Families f` é válida, mas `SELECT VALUE * FROM Families f` não `SELECT *, f.id FROM Families f` é válida.

## <a name="-and--operators"></a>? e?? operadores

Você pode usar os operadores ternário (?) e de União (??) para criar expressões condicionais, como em linguagens de programação, como C# e JavaScript.

Você pode usar o? operador para construir novas propriedades JSON em tempo real. Por exemplo, a consulta a seguir classifica os níveis `elementary` de `other`nível em ou:

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

Você também pode aninhar chamadas para o? como na consulta a seguir: 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

Como ocorre com outros operadores de consulta, o? o operador exclui itens se as propriedades referenciadas estiverem ausentes ou os tipos que estão sendo comparados forem diferentes.

Usar o?? para verificar com eficiência uma propriedade em um item ao consultar dados semiestruturados ou de tipo misto. Por exemplo, a consulta a seguir `lastName` retorna se presente, `surname` ou `lastName` se não estiver presente.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>Próximas etapas

- [Amostras do .NET no Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Palavras-chave](sql-query-keywords.md)
- [Cláusula SELECT](sql-query-select.md)
