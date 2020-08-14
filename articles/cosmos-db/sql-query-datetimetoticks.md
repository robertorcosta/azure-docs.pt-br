---
title: DateTimeToTicks na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL DateTimeToTicks no Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: a6fcff691ee5278ed0a9e1c132f8ecebbee9431c
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88226987"
---
# <a name="datetimetoticks-azure-cosmos-db"></a>DateTimeToTicks (Azure Cosmos DB)

Converte o DateTime especificado em tiques. Um único tique representa 100 nanossegundos ou 1 10-milionésimo de um segundo.
  
## <a name="syntax"></a>Sintaxe
  
```sql
DateTimeToTicks (<DateTime>)
```

## <a name="arguments"></a>Argumentos
  
*DateTime*  
   Valor de cadeia de caracteres UTC de data e hora do ISO 8601 no formato `YYYY-MM-DDThh:mm:ss.fffffffZ`

## <a name="return-types"></a>Tipos de retorno

Retorna um valor inteiro positivo.

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
