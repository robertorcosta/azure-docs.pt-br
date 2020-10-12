---
title: DateTimePart na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL DateTimePart no Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 87663c18ddaa5da6740a0f54aa5f2812cbb06af8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88227011"
---
# <a name="datetimepart-azure-cosmos-db"></a>DateTimePart (Azure Cosmos DB)

Retorna o valor do DateTimePart especificado entre o DateTime especificado.
  
## <a name="syntax"></a>Sintaxe
  
```sql
DateTimePart (<DateTimePart> , <DateTime>)
```

## <a name="arguments"></a>Argumentos
  
*DateTimePart*  
   A parte da data para a qual DateTimePart retornará o valor. Esta tabela lista todos os argumentos DateTimePart válidos:

| DateTimePart | abreviações        |
| ------------ | -------------------- |
| Year         | "Year", "yyyy", "AA" |
| Mês        | "month", "mm", "m"   |
| Dia          | "Day", "dd", "d"     |
| Hora         | "hora", "HH"         |
| Minuto       | "minuto", "mi", "n"  |
| Segundo       | "segundo", "SS", "s"  |
| Milissegundos  | "milissegundo", "MS"  |
| Microssegundos  | "microssegundo", "MCS" |
| 100 nanossegundos   | "nanossegundos", "NS"   |

*DateTime*  
   Valor de cadeia de caracteres UTC de data e hora do ISO 8601 no formato `YYYY-MM-DDThh:mm:ss.fffffffZ`

## <a name="return-types"></a>Tipos de retorno

Retorna um valor inteiro positivo.

## <a name="remarks"></a>Comentários

DateTimePart retornará `undefined` pelos seguintes motivos:

- O valor de DateTimePart especificado é inválido
- DateTime não é um DateTime ISO 8601 válido

Essa função do sistema não usará o índice.

## <a name="examples"></a>Exemplos

Aqui está um exemplo que retorna o valor inteiro do mês:

```sql
SELECT DateTimePart("m", "2020-01-02T03:04:05.6789123Z") AS MonthValue
```

```json
[
    {
        "MonthValue": 1
    }
]
```

Aqui está um exemplo que retorna o número de microssegundos:

```sql
SELECT DateTimePart("mcs", "2020-01-02T03:04:05.6789123Z") AS MicrosecondsValue
```

```json
[
    {
        "MicrosecondsValue": 678912
    }
]
```

## <a name="next-steps"></a>Próximas etapas

- [Funções de data e hora Azure Cosmos DB](sql-query-date-time-functions.md)
- [Funções de sistema do Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
