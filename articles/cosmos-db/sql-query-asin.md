---
title: Asen na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre como a função de sistema do codeseno (Asen) do SQL em Azure Cosmos DB retorna o ângulo, em radianos, cujo seno é a expressão numérica especificada
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6f2a3e87f464c2b5d0365785fcd7b81623ef09ef
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93089246"
---
# <a name="asin-azure-cosmos-db"></a>Asen (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retorna o ângulo, em radianos, cujo seno é a expressão numérica especificada. Isso também é chamado de arco seno.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
ASIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*numeric_expr*  
   É uma expressão numérica.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão numérica.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir retorna o `ASIN` de-1.  
  
```sql
SELECT ASIN(-1) AS asin  
```  
  
 Este é o conjunto de resultados.  
  
```json
[{"asin": -1.5707963267948966}]  
```  

## <a name="remarks"></a>Comentários

Essa função do sistema não usará o índice.

## <a name="next-steps"></a>Próximas etapas

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funções de sistema do Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
