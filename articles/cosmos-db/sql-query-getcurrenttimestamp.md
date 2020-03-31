---
title: ObtenhaCurrentTimestamp no idioma de consulta do Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL GetCurrentTimestamp no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b816c63da56025fe6e9cbaece2cde5dcd01585d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "71351000"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>GetCurrentTimestamp (Azure Cosmos DB)
 Retorna o número de milissegundos que se passaram desde 00:00:00 quinta-feira, 1 de janeiro de 1970. 
  
## <a name="syntax"></a>Sintaxe
  
```sql
GetCurrentTimestamp ()  
```  
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna um valor numérico, o número atual de milissegundos que se passaram desde a época do Unix, ou seja, o número de milissegundos que se passaram desde 00:00:00 quinta-feira, 1 de janeiro de 1970.

## <a name="remarks"></a>Comentários

  GetCurrentTimestamp() é uma função não determinística.
  
  O resultado devolvido é UTC (Horário Universal Coordenado).

## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como obter o carimbo de tempo atual usando a função incorporada GetCurrentTimestamp().
  
```sql
SELECT GetCurrentTimestamp() AS currentUtcTimestamp
```  
  
 Aqui está um exemplo de conjunto de resultados.
  
```json
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a name="next-steps"></a>Próximas etapas

- [Data e hora funciona Azure Cosmos DB](sql-query-date-time-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
