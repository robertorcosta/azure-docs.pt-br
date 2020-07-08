---
title: RAND na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função RAND do sistema SQL no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e44878b6d65725f08aeca4eb07088315ae2bb78a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "78302212"
---
# <a name="rand-azure-cosmos-db"></a>RAND (Azure Cosmos DB)
 Retorna um valor numérico gerado aleatoriamente de [0, 1).
 
## <a name="syntax"></a>Syntax
  
```sql
RAND ()  
```  

## <a name="return-types"></a>Tipos de retorno

  Retorna uma expressão numérica.

## <a name="remarks"></a>Comentários

  `RAND` é uma função não determinística. As chamadas repetitivas do não `RAND` retornam os mesmos resultados.

## <a name="examples"></a>Exemplos
  
  O exemplo a seguir retorna um valor numérico gerado aleatoriamente.
  
```sql
SELECT RAND() AS rand 
```  
  
 Este é o conjunto de resultados.  
  
```json
[{"rand": 0.87860053195618093}]  
``` 

## <a name="remarks"></a>Comentários

Essa função do sistema não usará o índice.

## <a name="next-steps"></a>Próximas etapas

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funções de sistema do Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
