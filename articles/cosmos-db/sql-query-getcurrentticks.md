---
title: GetCurrentTicks na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL GetCurrentTicks no Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 2ca76d75edba6688dbe93f11a51a0ad67942677a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88606934"
---
# <a name="getcurrentticks-azure-cosmos-db"></a>GetCurrentTicks (Azure Cosmos DB)

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
