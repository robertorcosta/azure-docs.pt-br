---
title: UPPER na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5129b4fffafb6918f655263cac2f5564635acf36
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303963"
---
# <a name="upper-azure-cosmos-db"></a>UPPER (Azure Cosmos DB)
 Retorna uma expressão de cadeia de caracteres depois de converter dados de caracteres minúsculos em maiúsculos.  

A função de sistema superior não utiliza o índice. Se você planeja fazer comparações que não diferenciam maiúsculas de minúsculas, a função de sistema superior pode consumir uma quantidade significativa de RU. Se esse for o caso, em vez de usar a função de sistema superior para normalizar os dados a cada vez para comparações, você poderá normalizar a capitalização após a inserção. Em seguida, uma consulta como SELECT * de c, em que UPPER (c. Name) = ' BOB ' simplesmente se torna SELECT * de c onde c.name = ' BOB '.

## <a name="syntax"></a>Sintaxe
  
```sql
UPPER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de cadeia de caracteres.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão de cadeia de caracteres.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como usar `UPPER` em uma consulta  
  
```sql
SELECT UPPER("Abc") AS upper  
```  
  
 Este é o conjunto de resultados.  
  
```json
[{"upper": "ABC"}]  
```

## <a name="remarks"></a>Comentários

Essa função do sistema não usará o índice.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

- [Funções de cadeia de caracteres Azure Cosmos DB](sql-query-string-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
