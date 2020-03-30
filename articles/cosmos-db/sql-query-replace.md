---
title: SUBSTITUIR no idioma de consulta do Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL REPLACE no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 758ac13530752df481d27e7e253f025f5c8d6430
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302195"
---
# <a name="replace-azure-cosmos-db"></a>REPLACE (Azure Cosmos DB)
 Substitui todas as ocorrências de um valor da cadeia de caracteres especificado por outro valor de cadeia de caracteres.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
REPLACE(<str_expr1>, <str_expr2>, <str_expr3>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr1*  
   É a expressão da cadeia de caracteres a ser pesquisada.  
  
*str_expr2*  
   É a expressão de corda a ser encontrada.  
  
*str_expr3*  
   É a expressão de string para substituir ocorrências de *str_expr2* em *str_expr1*.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão de cadeia de caracteres.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir `REPLACE` mostra como usar em uma consulta.  
  
```sql
SELECT REPLACE("This is a Test", "Test", "desk") AS replace
```  
  
 Este é o conjunto de resultados.  
  
```json
[{"replace": "This is a desk"}]  
```  

## <a name="remarks"></a>Comentários

Esta função do sistema não utilizará o índice.

## <a name="next-steps"></a>Próximas etapas

- [Funções de string Azure Cosmos DB](sql-query-string-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
