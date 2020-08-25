---
title: RAND na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função RAND do sistema SQL no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ff098da778221868b0eddc17c426d2bf36eec0fe
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88794333"
---
# <a name="rand-azure-cosmos-db"></a>RAND (Azure Cosmos DB)
 Retorna um valor numérico gerado aleatoriamente de [0, 1).
 
## <a name="syntax"></a>Sintaxe
  
```sql
RAND ()  
```  

## <a name="return-types"></a>Tipos de retorno

  Retorna uma expressão numérica.

## <a name="remarks"></a>Comentários

  `RAND` é uma função não determinística. As chamadas repetitivas do não `RAND` retornam os mesmos resultados. Essa função do sistema não usará o índice.


## <a name="examples"></a>Exemplos
  
  O exemplo a seguir retorna um valor numérico gerado aleatoriamente.
  
```sql
SELECT RAND() AS rand 
```  
  
 Este é o conjunto de resultados.  
  
```json
[{"rand": 0.87860053195618093}]  
``` 

## <a name="next-steps"></a>Próximas etapas

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funções de sistema do Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
