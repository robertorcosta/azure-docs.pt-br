---
title: GetCurrentDateTime na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL GetCurrentDateTime no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 03a3183fe3001008cdd3f3caae1b8c3af81668fe
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340198"
---
# <a name="getcurrentdatetime-azure-cosmos-db"></a>GetCurrentDateTime (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Retorna a data e a hora UTC atuais (tempo Universal Coordenado) como uma cadeia de caracteres ISO 8601.
  
## <a name="syntax"></a>Sintaxe
  
```sql
GetCurrentDateTime ()
```

## <a name="return-types"></a>Tipos de retorno
  
  Retorna o valor de cadeia de caracteres atual de data e 8601 hora UTC no formato em `YYYY-MM-DDThh:mm:ss.fffffffZ` que:
  
  |Formatar|Descrição|
  |-|-|
  |AAAA|ano de quatro dígitos|
  |MM|mês de dois dígitos (01 = Janeiro, etc.)|
  |DD|dia de dois dígitos do mês (01 a 31)|
  |T|signifier para o início dos elementos de hora|
  |hh|hora de dois dígitos (00 a 23)|
  |mm|minutos de dois dígitos (00 a 59)|
  |ss|segundos de dois dígitos (00 a 59)|
  |. fffffff|segundos fracionários de sete dígitos|
  |Z|Designador UTC (tempo Universal Coordenado)||
  
  Para obter mais informações sobre o formato ISO 8601, consulte [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>Comentários

GetCurrentDateTime () é uma função não determinística. O resultado retornado é UTC. A precisão é de 7 dígitos, com uma precisão de 100 nanossegundos.

Essa função do sistema não usará o índice.

## <a name="examples"></a>Exemplos
  
O exemplo a seguir mostra como obter a data e hora UTC atuais usando a função interna GetCurrentDateTime ().
  
```sql
SELECT GetCurrentDateTime() AS currentUtcDateTime
```  
  
 Veja um exemplo de conjunto de resultados.
  
```json
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.1234567Z"
}]  
```  

## <a name="next-steps"></a>Próximas etapas

- [Funções de data e hora Azure Cosmos DB](sql-query-date-time-functions.md)
- [Funções de sistema do Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
