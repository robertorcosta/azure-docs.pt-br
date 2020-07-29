---
title: Funções de agregação no Azure Cosmos DB
description: Saiba mais sobre a sintaxe da função de agregação SQL, tipos de funções de agregação com suporte pelo Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: tisande
ms.openlocfilehash: 24acd1e9c13320244ff4c27abd13abeda6f70b2b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "79464454"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Funções de agregação no Azure Cosmos DB

As funções de agregação executam um cálculo em um conjunto de valores na `SELECT` cláusula e retornam um único valor. Por exemplo, a consulta a seguir retorna a contagem de itens dentro do `Families` contêiner:

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

Você também pode retornar apenas o valor escalar da agregação usando a palavra-chave VALUE. Por exemplo, a consulta a seguir retorna a contagem de valores como um único número:

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

Os resultados são:

```json
    [ 2 ]
```

Você também pode combinar agregações com filtros. Por exemplo, a consulta a seguir retorna a contagem de itens com o estado de endereço de `WA` .

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

Os resultados são:

```json
    [ 1 ]
```

## <a name="types-of-aggregate-functions"></a>Tipos de funções de agregação

A API do SQL dá suporte às funções de agregação a seguir. `SUM`e `AVG` operar em valores numéricos, e `COUNT` , `MIN` e `MAX` trabalhar em números, cadeias de caracteres, Boolianos e nulos.

| Função | Descrição |
|-------|-------------|
| COUNT | Retorna o número de itens na expressão. |
| SUM   | Retorna a soma de todos os valores na expressão. |
| MÍN.   | Retorna o valor mínimo na expressão. |
| MÁX.   | Retorna o valor máximo na expressão. |
| AVG   | Retorna a média dos valores na expressão. |

Você também pode agregar os resultados de uma iteração de matriz.

> [!NOTE]
> No Data Explorer do portal do Azure, as consultas de agregação podem agregar resultados parciais em apenas uma página de consulta. O SDK produz um único valor cumulativo em todas as páginas. Para executar consultas de agregação usando código, você precisa do SDK do .NET 1.12.0, SDK do .NET Core 1.1.0 ou Java SDK 1.9.5 ou superior.

## <a name="remarks"></a>Comentários

Essas funções de sistema agregadas se beneficiarão de um [índice de intervalo](index-policy.md#includeexclude-strategy). Se você pretende fazer um `COUNT` , `SUM` ,, `MIN` `MAX` ou `AVG` em uma propriedade, deve [incluir o caminho relevante na política de indexação](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure Cosmos DB](introduction.md)
- [Funções do sistema](sql-query-system-functions.md)
- [Funções definidas pelo usuário](sql-query-udfs.md)