---
title: SUBSTITUIR na linguagem de consulta do Azure Cosmos DB
description: Saiba mais sobre a substituição da função do sistema SQL no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 758ac13530752df481d27e7e253f025f5c8d6430
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "78302195"
---
# <a name="replace-azure-cosmos-db"></a>SUBSTITUIR (Azure Cosmos DB)
 Substitui todas as ocorrências de um valor da cadeia de caracteres especificado por outro valor de cadeia de caracteres.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
REPLACE(<str_expr1>, <str_expr2>, <str_expr3>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr1*  
   É a expressão da cadeia de caracteres a ser pesquisada.  
  
*str_expr2*  
   É a expressão de cadeia de caracteres a ser encontrada.  
  
*str_expr3*  
   É a expressão de cadeia de caracteres para substituir ocorrências de *str_expr2* em *str_expr1*.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão de cadeia de caracteres.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como usar `REPLACE` em uma consulta.  
  
```sql
SELECT REPLACE("This is a Test", "Test", "desk") AS replace
```  
  
 Este é o conjunto de resultados.  
  
```json
[{"replace": "This is a desk"}]  
```  

## <a name="remarks"></a>Comentários

Essa função do sistema não usará o índice.

## <a name="next-steps"></a>Próximas etapas

- [Funções de cadeia de caracteres do Azure Cosmos DB](sql-query-string-functions.md)
- [Funções de sistema do Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
