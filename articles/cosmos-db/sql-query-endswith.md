---
title: ENDSWITH na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função de sistema SQL ENDSWITH em Azure Cosmos DB para retornar um booliano que indica se a primeira expressão de cadeia de caracteres termina com a segunda
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 37c5a8b3c44c5ac46b837e4d851d22f85aeaf39c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "78299441"
---
# <a name="endswith-azure-cosmos-db"></a>ENDSWITH (Azure Cosmos DB)
 Retorna um valor booliano que indica se a primeira expressão de cadeia de caracteres termina com a segunda.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
ENDSWITH(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr1*  
   É uma expressão de cadeia de caracteres.  
  
*str_expr2*  
   É uma expressão de cadeia de caracteres a ser comparada ao final de *str_expr1*.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão booliana.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir retorna "abc" terminando com "b" e "bc".  
  
```sql
SELECT ENDSWITH("abc", "b") AS e1, ENDSWITH("abc", "bc") AS e2 
```  
  
 Este é o conjunto de resultados.  
  
```json
[{"e1": false, "e2": true}]  
```  

## <a name="remarks"></a>Comentários

Essa função do sistema não usará o índice.

## <a name="next-steps"></a>Próximas etapas

- [Funções de cadeia de caracteres Azure Cosmos DB](sql-query-string-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
