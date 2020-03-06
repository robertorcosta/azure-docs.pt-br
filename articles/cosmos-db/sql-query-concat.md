---
title: CONCAT na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre como a função de sistema SQL CONCAT no Azure Cosmos DB retorna uma cadeia de caracteres que é o resultado da concatenação de dois ou mais valores de cadeia de caracteres
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c8a0941376ed74d7f8cb819d78df43eb9f0b7bd1
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302603"
---
# <a name="concat-azure-cosmos-db"></a>CONCAT (Azure Cosmos DB)
 Retorna uma cadeia de caracteres que é o resultado da concatenação de dois ou mais valores.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
CONCAT(<str_expr1>, <str_expr2> [, <str_exprN>])  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de cadeia de caracteres para concatenar aos outros valores. A função `CONCAT` requer pelo menos dois argumentos de *str_expr* .  
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão de cadeia de caracteres.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir retorna a cadeia de caracteres concatenada do valor especificado.  
  
```sql
SELECT CONCAT("abc", "def") AS concat  
```  
  
 Este é o conjunto de resultados.  
  
```json
[{"concat": "abcdef"}]  
```  
  
## <a name="remarks"></a>Comentários

Essa função do sistema não usará o índice.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

- [Funções de cadeia de caracteres Azure Cosmos DB](sql-query-string-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
