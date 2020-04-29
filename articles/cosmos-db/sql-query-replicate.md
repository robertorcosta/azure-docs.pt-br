---
title: REPLICAr na linguagem de consulta do Azure Cosmos DB
description: Saiba mais sobre a replicação de função do sistema SQL no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 19fcde522c5cb0355e53a5616145f27fada7dad9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "78302178"
---
# <a name="replicate-azure-cosmos-db"></a>REPLICAte (Azure Cosmos DB)
 Repete um valor da cadeia de caracteres um número especificado de vezes.
  
## <a name="syntax"></a>Sintaxe
  
```sql
REPLICATE(<str_expr>, <num_expr>)
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de cadeia de caracteres.
  
*num_expr*  
   É uma expressão numérica. Se *num_expr* for negativo ou não finito, o resultado será indefinido.
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão de cadeia de caracteres.
  
## <a name="remarks"></a>Comentários
  O comprimento máximo do resultado é de 10.000 caracteres, ou seja, (length (*str_expr*) * *num_expr*) <= 10.000.

## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como usar `REPLICATE` em uma consulta.
  
```sql
SELECT REPLICATE("a", 3) AS replicate
```  
  
 Este é o conjunto de resultados.
  
```json
[{"replicate": "aaa"}]
```  

## <a name="remarks"></a>Comentários

Essa função do sistema não usará o índice.

## <a name="next-steps"></a>Próximas etapas

- [Funções de cadeia de caracteres Azure Cosmos DB](sql-query-string-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
