---
title: UPPER no azure Cosmos DB linguagem de consulta
description: Saiba mais sobre a função do sistema SQL UPPER no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5129b4fffafb6918f655263cac2f5564635acf36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303963"
---
# <a name="upper-azure-cosmos-db"></a>UPPER (Azure Cosmos DB)
 Retorna uma expressão de cadeia de caracteres depois de converter dados de caracteres minúsculos em maiúsculos.  

A função do sistema UPPER não utiliza o índice. Se você planeja fazer comparações insensíveis de casos frequentes, a função do sistema UPPER pode consumir uma quantidade significativa de RU's. Se este for o caso, em vez de usar a função do sistema UPPER para normalizar os dados cada vez para comparações, você pode normalizar o invólucro após a inserção. Em seguida, uma consulta como SELECT * FROM c WHERE UPPER(c.name) = 'BOB' simplesmente se torna SELECT * FROM c ONDE c.name = 'BOB'.

## <a name="syntax"></a>Sintaxe
  
```sql
UPPER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de cordas.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão de cadeia de caracteres.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir `UPPER` mostra como usar em uma consulta  
  
```sql
SELECT UPPER("Abc") AS upper  
```  
  
 Este é o conjunto de resultados.  
  
```json
[{"upper": "ABC"}]  
```

## <a name="remarks"></a>Comentários

Esta função do sistema não utilizará o índice.

## <a name="next-steps"></a>Próximas etapas

- [Funções de string Azure Cosmos DB](sql-query-string-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
