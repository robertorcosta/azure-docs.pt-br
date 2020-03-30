---
title: GetCurrentDateTime no idioma de consulta do Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL GetCurrentDateTime no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d50b08ab85c7e299c465c3eb6f34e867d6634006
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303895"
---
# <a name="getcurrentdatetime-azure-cosmos-db"></a>GetCurrentDateTime (Azure Cosmos DB)
 Retorna a data e a hora atuais UTC (Coordinated Universal Time) como uma seqüência ISO 8601.
  
## <a name="syntax"></a>Sintaxe
  
```sql
GetCurrentDateTime ()
```
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna a data e a hora atual da LINHA ISO 8601 no formato `YYYY-MM-DDThh:mm:ss.fffffffZ` onde:
  
  |||
  |-|-|
  |AAAA|ano de quatro dígitos|
  |MM|mês de dois dígitos (01 = janeiro, etc.)|
  |DD|dia de dois dígitos do mês (01 a 31)|
  |T|signatário para o início dos elementos do tempo|
  |hh|hora de dois dígitos (00 a 23)|
  |MM|minutos de dois dígitos (00 a 59)|
  |ss|segundos de dois dígitos (00 a 59)|
  |.fffffff|sete dígitos fracionais segundos|
  |Z|Designador UTC (Coordinated Universal Time)||
  
  Para obter mais informações sobre o formato ISO 8601, consulte [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>Comentários

  GetCurrentDateTime() é uma função não determinística. 
  
  O resultado devolvido é UTC.

  Precisão é de 7 dígitos, com uma precisão de 100 nanossegundos.

## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como obter a data de data UTC atual usando a função incorporada GetCurrentDateTime().
  
```sql
SELECT GetCurrentDateTime() AS currentUtcDateTime
```  
  
 Aqui está um exemplo de conjunto de resultados.
  
```json
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.1234567Z"
}]  
```  

## <a name="next-steps"></a>Próximas etapas

- [Data e hora funciona Azure Cosmos DB](sql-query-date-time-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
