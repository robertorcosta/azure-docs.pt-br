---
title: GetCurrentTimestamp na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL GetCurrentTimestamp no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: acdc598107228d8dbebca3dccb3361348ca06432
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93098494"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>GetCurrentTimestamp (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retorna o número de milissegundos decorridos desde 00:00:00 quinta-feira, 1 de janeiro de 1970.
  
## <a name="syntax"></a>Sintaxe
  
```sql
GetCurrentTimestamp ()  
```  
  
## <a name="return-types"></a>Tipos de retorno
  
Retorna um valor numérico assinado, o número atual de milissegundos decorridos desde a época do UNIX, ou seja, o número de milissegundos decorridos desde 00:00:00 quinta-feira, 1 de janeiro de 1970.

## <a name="remarks"></a>Comentários

GetCurrentTimestamp () é uma função não determinística. O resultado retornado é UTC (tempo Universal Coordenado).

Essa função do sistema não usará o índice.

## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como obter o carimbo de data/hora atual usando a função interna GetCurrentTimestamp ().
  
```sql
SELECT GetCurrentTimestamp() AS currentUtcTimestamp
```  
  
 Veja um exemplo de conjunto de resultados.
  
```json
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a name="next-steps"></a>Próximas etapas

- [Funções de data e hora Azure Cosmos DB](sql-query-date-time-functions.md)
- [Funções de sistema do Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
