---
title: TAN no azure Cosmos DB linguagem de consulta
description: Saiba mais sobre a função do sistema SQL TAN no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9d7187ba116067445e835769fc33aa70677ef80b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78301974"
---
# <a name="tan-azure-cosmos-db"></a>TAN (Azure Cosmos DB)
 Retorna a tangente do ângulo especificado, em radianos, na expressão especificada.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
TAN (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*numeric_expr*  
   É uma expressão numérica.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão numérica.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir calcula a tangente de PI()/2.  
  
```sql
SELECT TAN(PI()/2) AS tan 
```  
  
 Este é o conjunto de resultados.  
  
```json
[{"tan": 16331239353195370 }]  
```  

## <a name="remarks"></a>Comentários

Esta função do sistema não utilizará o índice.

## <a name="next-steps"></a>Próximas etapas

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
