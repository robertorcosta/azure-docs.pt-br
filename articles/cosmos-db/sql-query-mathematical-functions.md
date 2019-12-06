---
title: Funções matemáticas na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre as funções matemáticas no Azure Cosmos DB para executar um cálculo, com base nos valores de entrada fornecidos como argumentos, e retornar um valor numérico.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a04867d356aaf2f55dbe900d2e35b42f74206851
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873260"
---
# <a name="mathematical-functions-azure-cosmos-db"></a>Funções matemáticas (Azure Cosmos DB)  

As funções matemáticas executam um cálculo, com base em valores de entrada fornecidos como argumentos e retornam um valor numérico.

Você pode executar consultas como o exemplo a seguir:

```sql
    SELECT VALUE ABS(-4)
```

O resultado é:

```json
    [4]
```

## <a name="functions"></a>Funções

As seguintes funções matemáticas internas com suporte executam um cálculo, geralmente com base em argumentos de entrada e retornam uma expressão numérica.
  
||||  
|-|-|-|  
|[ABS](sql-query-abs.md)|[ACOS](sql-query-acos.md)|[ASIN](sql-query-asin.md)|  
|[ATAN](sql-query-atan.md)|[ATN2](sql-query-atn2.md)|[CEILING](sql-query-ceiling.md)|  
|[COS](sql-query-cos.md)|[COT](sql-query-cot.md)|[DEGREES](sql-query-degrees.md)|  
|[EXP](sql-query-exp.md)|[FLOOR](sql-query-floor.md)|[LOG](sql-query-log.md)|  
|[LOG10](sql-query-log10.md)|[PI](sql-query-pi.md)|[POWER](sql-query-power.md)|  
|[RADIANS](sql-query-radians.md)|[RAND](sql-query-rand.md)|[ROUND](sql-query-round.md)|
|[SIGN](sql-query-sign.md)|[SIN](sql-query-sin.md)|[SQRT](sql-query-sqrt.md)|
|[SQUARE](sql-query-square.md)|[TAN](sql-query-tan.md)|[TRUNC](sql-query-trunc.md)||  
  
Todas as funções matemáticas, com exceção de RAND, são funções deterministas. Isso significa que elas retornam os mesmos resultados sempre que são chamadas com um conjunto específico de valores de entrada.

## <a name="next-steps"></a>Próximos passos

- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
- [Funções definidas pelo usuário](sql-query-udfs.md)
- [Agregações](sql-query-aggregates.md)
