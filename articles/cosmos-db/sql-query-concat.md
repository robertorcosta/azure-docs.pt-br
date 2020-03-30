---
title: CONCAT no azure Cosmos DB linguagem de consulta
description: Saiba como funciona o sistema CONCAT SQL no Azure Cosmos DB retorna uma string que é o resultado da concatenação de dois ou mais valores de seqüência
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c8a0941376ed74d7f8cb819d78df43eb9f0b7bd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302603"
---
# <a name="concat-azure-cosmos-db"></a>CONCAT (Azure Cosmos DB)
 Retorna uma cadeia de caracteres que é o resultado da concatenação de dois ou mais valores de cadeia de caracteres.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
CONCAT(<str_expr1>, <str_expr2> [, <str_exprN>])  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de string para concatenar aos outros valores. A `CONCAT` função requer pelo menos dois *argumentos str_expr.*  
  
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

Esta função do sistema não utilizará o índice.

## <a name="next-steps"></a>Próximas etapas

- [Funções de string Azure Cosmos DB](sql-query-string-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
