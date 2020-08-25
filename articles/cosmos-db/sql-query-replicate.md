---
title: REPLICAr na linguagem de consulta do Azure Cosmos DB
description: Saiba mais sobre a replicação de função do sistema SQL no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: aea29cfff6b3827cfb9169722e48120e3a5a3709
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88794317"
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

  O comprimento máximo do resultado é de 10.000 caracteres, ou seja, (length (*str_expr*) *  *num_expr*) <= 10.000. Essa função do sistema não usará o índice.

## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como usar `REPLICATE` em uma consulta.
  
```sql
SELECT REPLICATE("a", 3) AS replicate
```  
  
 Este é o conjunto de resultados.
  
```json
[{"replicate": "aaa"}]
```  

## <a name="next-steps"></a>Próximas etapas

- [Funções de cadeia de caracteres do Azure Cosmos DB](sql-query-string-functions.md)
- [Funções de sistema do Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
