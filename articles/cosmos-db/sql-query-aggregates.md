---
title: Funções agregadas no Azure Cosmos DB
description: Saiba mais sobre a sintaxe de funções agregadas SQL, tipos de funções agregadas suportadas pelo Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: tisande
ms.openlocfilehash: 24acd1e9c13320244ff4c27abd13abeda6f70b2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79464454"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Funções agregadas no Azure Cosmos DB

Funções agregadas realizam um cálculo `SELECT` em um conjunto de valores na cláusula e retornam um único valor. Por exemplo, a seguinte consulta retorna a contagem de itens dentro do `Families` contêiner:

## <a name="examples"></a>Exemplos

```sql
    SELECT COUNT(1)
    FROM Families f
```

Os resultados são:

```json
    [{
        "$1": 2
    }]
```

Você também pode retornar apenas o valor escalar do agregado usando a palavra-chave VALUE. Por exemplo, a consulta a seguir retorna a contagem de valores como um único número:

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

Os resultados são:

```json
    [ 2 ]
```

Você também pode combinar agregações com filtros. Por exemplo, a consulta a seguir retorna a contagem `WA`de itens com o estado de endereço de .

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

Os resultados são:

```json
    [ 1 ]
```

## <a name="types-of-aggregate-functions"></a>Tipos de funções agregadas

A API SQL suporta as seguintes funções agregadas. `SUM`e `AVG` operar em valores `COUNT`numéricos, e , `MIN`e `MAX` trabalhar em números, strings, booleanos e nulos.

| Função | Descrição |
|-------|-------------|
| COUNT | Retorna o número de itens na expressão. |
| SUM   | Retorna a soma de todos os valores na expressão. |
| MÍN.   | Retorna o valor mínimo na expressão. |
| MÁX.   | Retorna o valor máximo na expressão. |
| AVG   | Retorna a média dos valores na expressão. |

Você também pode agregar sobre os resultados de uma iteração de matriz.

> [!NOTE]
> No Data Explorer do portal Azure, as consultas de agregação podem agregar resultados parciais em apenas uma página de consulta. O SDK produz um único valor cumulativo em todas as páginas. Para realizar consultas de agregação usando código, você precisa de .NET SDK 1.12.0, .NET Core SDK 1.1.0 ou Java SDK 1.9.5 ou superior.

## <a name="remarks"></a>Comentários

Essas funções agregadas do sistema se beneficiarão de um [índice de intervalo](index-policy.md#includeexclude-strategy). Se você espera `COUNT`fazer `SUM` `MIN`um `MAX`, `AVG` , , ou em uma propriedade, você deve [incluir o caminho relevante na política de indexação](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure Cosmos DB](introduction.md)
- [Funções do sistema](sql-query-system-functions.md)
- [Funções definidas pelo usuário](sql-query-udfs.md)