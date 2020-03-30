---
title: MENOR no idioma de consulta do Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL inferior no Azure Cosmos DB para retornar uma expressão de seqüência depois de converter dados de caracteres maiúsculos em minúsculas
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 80dba57d4fe05630eb5ae4f8fc96bd0aa214c6d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302263"
---
# <a name="lower-azure-cosmos-db"></a>INFERIOR (Azure Cosmos DB)
 Retorna uma expressão de cadeia de caracteres depois de converter dados de caracteres maiúsculos em minúsculos.  

A função do sistema LOWER não utiliza o índice. Se você planeja fazer comparações insensíveis de casos frequentes, a função do sistema INFERIOR pode consumir uma quantidade significativa de RU's. Se este for o caso, em vez de usar a função do sistema LOWER para normalizar os dados cada vez para comparações, você pode normalizar o invólucro após a inserção. Em seguida, uma consulta como SELECT * FROM c WHERE Lower(c.name) = 'bob' simplesmente torna-se SELECT * FROM c ONDE c.name = 'bob'.

## <a name="syntax"></a>Sintaxe
  
```sql
LOWER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de cordas.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão de cadeia de caracteres.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir `LOWER` mostra como usar em uma consulta.  
  
```sql
SELECT LOWER("Abc") AS lower
```  
  
 Este é o conjunto de resultados.  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="remarks"></a>Comentários

Esta função do sistema não utilizará o índice.

## <a name="next-steps"></a>Próximas etapas

- [Funções de string Azure Cosmos DB](sql-query-string-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
