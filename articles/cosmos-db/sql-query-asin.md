---
title: Asen na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre como a função de sistema do codeseno (Asen) do SQL em Azure Cosmos DB retorna o ângulo, em radianos, cujo seno é a expressão numérica especificada
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8b70738a439b6c64a84a63adf63c83995530e92e
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302688"
---
# <a name="asin-azure-cosmos-db"></a>Asen (Azure Cosmos DB)
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

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
