---
title: DateTimeToTicks na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL DateTimeToTicks no Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 47bf8a3a2ffe66e295fcb9d8a2a02891812c6813
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095774"
---
# <a name="datetimetoticks-azure-cosmos-db"></a>DateTimeToTicks (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Converte o DateTime especificado em tiques. Um único tique representa 100 nanossegundos ou 1 10-milionésimo de um segundo. 

## <a name="syntax"></a>Sintaxe
  
```sql
DateTimeToTicks (<DateTime>)
```

## <a name="arguments"></a>Argumentos
  
*DateTime*  
   Valor de cadeia de caracteres UTC de data e hora do ISO 8601 no formato `YYYY-MM-DDThh:mm:ss.fffffffZ`

## <a name="return-types"></a>Tipos de retorno

Retorna um valor numérico assinado, o número atual de tiques de 100-nanossegundos que foram decorridos desde a época do UNIX. Em outras palavras, DateTimeToTicks retorna o número de tiques de 100 nanossegundos que foram decorridos desde 00:00:00 quinta-feira, 1 de janeiro de 1970.

## <a name="remarks"></a>Comentários

DateTimeDateTimeToTicks retornará `undefined` se DateTime não for um DateTime ISO 8601 válido

Essa função do sistema não usará o índice.

## <a name="examples"></a>Exemplos

Aqui está um exemplo que retorna o número de tiques:

```sql
SELECT DateTimeToTicks("2020-01-02T03:04:05.6789123Z") AS Ticks
```

```json
[
    {
        "Ticks": 15779342456789124
    }
]
```

Veja um exemplo que retorna o número de tiques sem especificar o número de segundos fracionários:

```sql
SELECT DateTimeToTicks("2020-01-02T03:04:05Z") AS Ticks
```

```json
[
    {
        "Ticks": 15779342450000000
    }
]
```

## <a name="next-steps"></a>Próximas etapas

- [Funções de data e hora Azure Cosmos DB](sql-query-date-time-functions.md)
- [Funções de sistema do Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
