---
title: Funções de agregação no Azure Cosmos DB
description: Saiba mais sobre a sintaxe da função de agregação SQL, tipos de funções de agregação com suporte pelo Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 1ce3b18dd31944a1a4d4e6fad8fb49e63996dace
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871832"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Funções de agregação no Azure Cosmos DB

As funções de agregação executam um cálculo em um conjunto de valores na cláusula SELECT e retornam um único valor. Por exemplo, a consulta a seguir retorna a contagem de itens dentro do contêiner de `Families`:

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

Você também pode combinar agregações com filtros. Por exemplo, a consulta a seguir retorna a contagem de itens com o estado de endereço de `WA`.

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

A API do SQL dá suporte às funções de agregação a seguir. SUM e AVG operam em valores numéricos, e o trabalho de contagem, mínimo e máximo em números, cadeias de caracteres, Boolianos e nulos.

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

## <a name="next-steps"></a>Próximos passos

- [Introdução ao Azure Cosmos DB](introduction.md)
- [Funções do sistema](sql-query-system-functions.md)
- [Funções definidas pelo usuário](sql-query-udfs.md)