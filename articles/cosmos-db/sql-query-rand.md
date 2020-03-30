---
title: RAND no azure Cosmos DB linguagem de consulta
description: Saiba mais sobre a função do sistema SQL RAND no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e44878b6d65725f08aeca4eb07088315ae2bb78a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302212"
---
# <a name="rand-azure-cosmos-db"></a>RAND (Azure Cosmos DB)
 Retorna um valor numérico gerado aleatoriamente a partir de [0,1).
 
## <a name="syntax"></a>Sintaxe
  
```sql
RAND ()  
```  

## <a name="return-types"></a>Tipos de retorno

  Retorna uma expressão numérica.

## <a name="remarks"></a>Comentários

  `RAND` é uma função não determinística. Chamadas repetitivas de `RAND` não retornam os mesmos resultados.

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

Esta função do sistema não utilizará o índice.

## <a name="next-steps"></a>Próximas etapas

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
