---
title: ATAN na linguagem de consulta Azure Cosmos DB
description: Saiba como a função de sistema SQL do arco tangente (ATAN) em Azure Cosmos DB retorna o ângulo, em radianos, cuja tangente é a expressão numérica especificada
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 899c94a939be7825dca82522eab235bde9252896
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "78302671"
---
# <a name="atan-azure-cosmos-db"></a>ATAN (Azure Cosmos DB)
 Retorna o ângulo, em radianos, cuja tangente é a expressão numérica especificada. Também é chamado de arco-tangente.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
ATAN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*numeric_expr*  
   É uma expressão numérica.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão numérica.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir retorna o `ATAN` do valor especificado.  
  
```sql
SELECT ATAN(-45.01) AS atan  
```  
  
 Este é o conjunto de resultados.  
  
```json
[{"atan": -1.5485826962062663}]  
```  
  
## <a name="remarks"></a>Comentários

Essa função do sistema não usará o índice.

## <a name="next-steps"></a>Próximas etapas

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funções de sistema do Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
