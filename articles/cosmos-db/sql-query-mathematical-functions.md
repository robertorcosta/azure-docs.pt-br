---
title: Funções matemáticas na linguagem de consulta Azure Cosmos DB
description: Aprenda sobre as funções matemáticas no Azure Cosmos DB para realizar um cálculo, com base nos valores de entrada fornecidos como argumentos, e retorne um valor numérico.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a04867d356aaf2f55dbe900d2e35b42f74206851
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873260"
---
# <a name="mathematical-functions-azure-cosmos-db"></a>Funções matemáticas (Azure Cosmos DB)  

As funções matemáticas executam um cálculo, com base em valores de entrada fornecidos como argumentos e retornam um valor numérico.

Você pode executar consultas como o seguinte exemplo:

```sql
    SELECT VALUE ABS(-4)
```

O resultado é:

```json
    [4]
```

## <a name="functions"></a>Funções

As seguintes funções matemáticas incorporadas suportadas realizam um cálculo, geralmente baseado em argumentos de entrada, e retornam uma expressão numérica.
  
||||  
|-|-|-|  
|[ABS](sql-query-abs.md)|[Acos](sql-query-acos.md)|[Asin](sql-query-asin.md)|  
|[Atan](sql-query-atan.md)|[ATN2](sql-query-atn2.md)|[CEILING](sql-query-ceiling.md)|  
|[COS](sql-query-cos.md)|[COT](sql-query-cot.md)|[Graus](sql-query-degrees.md)|  
|[Exp](sql-query-exp.md)|[Chão](sql-query-floor.md)|[Log](sql-query-log.md)|  
|[LOG10](sql-query-log10.md)|[PI](sql-query-pi.md)|[POWER](sql-query-power.md)|  
|[Radianos](sql-query-radians.md)|[RAND](sql-query-rand.md)|[Rodada](sql-query-round.md)|
|[SIGN](sql-query-sign.md)|[Pecado](sql-query-sin.md)|[Sqrt](sql-query-sqrt.md)|
|[Praça](sql-query-square.md)|[Tan](sql-query-tan.md)|[TRUNC](sql-query-trunc.md)||  
  
Todas as funções matemáticas, com exceção de RAND, são funções deterministas. Isso significa que elas retornam os mesmos resultados sempre que são chamadas com um conjunto específico de valores de entrada.

## <a name="next-steps"></a>Próximas etapas

- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
- [Funções definidas pelo usuário](sql-query-udfs.md)
- [Agregações](sql-query-aggregates.md)
