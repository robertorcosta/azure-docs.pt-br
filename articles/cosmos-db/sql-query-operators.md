---
title: Operadores de consulta SQL para Azure Cosmos DB
description: Saiba mais sobre os operadores SQL, como igualdade, comparação e operadores lógicos com suporte pelo Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: f3efe4bee749f0d3132206ca68a33a60f0e16b81
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74870931"
---
# <a name="operators-in-azure-cosmos-db"></a>Operadores no Azure Cosmos DB

Este artigo fornece detalhes sobre os vários operadores com suporte pelo Azure Cosmos DB.

## <a name="equality-and-comparison-operators"></a>Operadores de igualdade e comparação

A tabela a seguir mostra o resultado de comparações de igualdade na API do SQL entre dois tipos JSON quaisquer.

| **Op** | **Indefinido** | **Nulo** | **Booliano** | **Número** | **Cadeia de caracteres** | **Objeto** | **Matriz** |
|---|---|---|---|---|---|---|---|
| **Indefinido** | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido |
| **Nulo** | Indefinido | **Ok** | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido |
| **Booliano** | Indefinido | Indefinido | **Ok** | Indefinido | Indefinido | Indefinido | Indefinido |
| **Número** | Indefinido | Indefinido | Indefinido | **Ok** | Indefinido | Indefinido | Indefinido |
| **Cadeia de caracteres** | Indefinido | Indefinido | Indefinido | Indefinido | **Ok** | Indefinido | Indefinido |
| **Objeto** | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido | **Ok** | Indefinido |
| **Matriz** | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido | Indefinido | **Ok** |

Para operadores de comparação como `>`, `>=`, `!=`, `<`e `<=`, a comparação entre os tipos ou entre dois objetos ou matrizes produz `Undefined`.  

Se o resultado da expressão escalar for `Undefined`, o item não será incluído no resultado, porque `Undefined` não é igual `true`.

## <a name="logical-and-or-and-not-operators"></a>Operadores lógicos (AND, OR e NOT)

Operadores lógicos funcionam em valores boolianos. As tabelas a seguir mostram as tabelas lógicas da verdade para esses operadores:

**Operador OU**

| OU | True | False | Indefinido |
| --- | --- | --- | --- |
| True |True |True |True |
| False |True |False |Indefinido |
| Indefinido |True |Indefinido |Indefinido |

**E o operador**

| E | True | False | Indefinido |
| --- | --- | --- | --- |
| True |True |False |Indefinido |
| False |False |False |False |
| Indefinido |Indefinido |False |Indefinido |

**NÃO operador**

| NÃO |  |
| --- | --- |
| True |False |
| False |True |
| Indefinido |Indefinido |


## <a name="-operator"></a>Operador *

O operador especial * projeta o item inteiro como está. Quando usado, ele deve ser o único campo projetado. Uma consulta como `SELECT * FROM Families f` é válida, mas `SELECT VALUE * FROM Families f` e `SELECT *, f.id FROM Families f` não são válidos.

## <a name="-and--operators"></a>? e?? operadores

Você pode usar os operadores ternário (?) e de União (??) para criar expressões condicionais, como em linguagens C# de programação como o e o JavaScript. 

Você pode usar o? operador para construir novas propriedades JSON em tempo real. Por exemplo, a consulta a seguir classifica os níveis de nível em `elementary` ou `other`:

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

Usar o?? para verificar com eficiência uma propriedade em um item ao consultar dados semiestruturados ou de tipo misto. Por exemplo, a consulta a seguir retorna `lastName`, se presente, ou `surname` se `lastName` não estiver presente.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>Próximos passos

- [Amostras do .NET no Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Palavras-chave](sql-query-keywords.md)
- [Cláusula SELECT](sql-query-select.md)
