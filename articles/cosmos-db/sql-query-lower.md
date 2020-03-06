---
title: INFERIOR na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função de sistema SQL inferior no Azure Cosmos DB para retornar uma expressão de cadeia de caracteres após converter dados de caracteres maiúsculos em minúsculas
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 80dba57d4fe05630eb5ae4f8fc96bd0aa214c6d4
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302263"
---
# <a name="lower-azure-cosmos-db"></a>INFERIOR (Azure Cosmos DB)
 Retorna uma expressão de cadeia de caracteres depois de converter dados de caracteres maiúsculos em minúsculos.  

A função de sistema inferior não utiliza o índice. Se você planeja fazer comparações que não diferenciam maiúsculas de minúsculas, a função de sistema inferior pode consumir uma quantidade significativa de RU. Se esse for o caso, em vez de usar a função de sistema inferior para normalizar os dados a cada vez para comparações, você poderá normalizar a capitalização após a inserção. Em seguida, uma consulta como SELECT * de c, em que LOWER (c. Name) = ' Bob ' simplesmente se torna SELECT * de c onde c.name = ' Bob '.

## <a name="syntax"></a>Sintaxe
  
```sql
LOWER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de cadeia de caracteres.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão de cadeia de caracteres.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como usar `LOWER` em uma consulta.  
  
```sql
SELECT LOWER("Abc") AS lower
```  
  
 Este é o conjunto de resultados.  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="remarks"></a>Comentários

Essa função do sistema não usará o índice.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

- [Funções de cadeia de caracteres Azure Cosmos DB](sql-query-string-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
