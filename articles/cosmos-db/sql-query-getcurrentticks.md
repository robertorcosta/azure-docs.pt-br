---
title: GetCurrentTicks na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL GetCurrentTicks no Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 610c545bf25822d27e0a641a1b2631f899502420
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340151"
---
# <a name="getcurrentticks-azure-cosmos-db"></a>GetCurrentTicks (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Retorna o número de tiques 100-nanossegundos que foram decorridos desde 00:00:00 quinta-feira, 1 de janeiro de 1970.
  
## <a name="syntax"></a>Sintaxe
  
```sql
GetCurrentTicks ()
```

## <a name="return-types"></a>Tipos de retorno

Retorna um valor numérico assinado, o número atual de tiques de 100-nanossegundos que foram decorridos desde a época do UNIX. Em outras palavras, GetCurrentTicks retorna o número de tiques de 100 nanossegundos decorridos desde 00:00:00 quinta-feira, 1 de janeiro de 1970.

## <a name="remarks"></a>Comentários

GetCurrentTicks () é uma função não determinística. O resultado retornado é UTC (tempo Universal Coordenado).

Essa função do sistema não usará o índice.

## <a name="examples"></a>Exemplos

Veja um exemplo que retorna a hora atual, medida em tiques:

```sql
SELECT GetCurrentTicks() AS CurrentTimeInTicks
```

```json
[
    {
        "CurrentTimeInTicks": 15973607943002652
    }
]
```

## <a name="next-steps"></a>Próximas etapas

- [Funções de data e hora Azure Cosmos DB](sql-query-date-time-functions.md)
- [Funções de sistema do Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
