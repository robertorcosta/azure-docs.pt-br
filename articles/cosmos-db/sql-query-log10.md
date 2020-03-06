---
title: LOG10 na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL LOG10 no Azure Cosmos DB para retornar o logaritmo de base 10 da expressão numérica especificada
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6f47200f6978d91f46c010640bb9c2bb26e9b7d5
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302484"
---
# <a name="log10-azure-cosmos-db"></a>LOG10 (Azure Cosmos DB)
 Retorna o logaritmo de base 10 da expressão numérica especificada.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
LOG10 (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*numeric_expression*  
   É uma expressão numérica.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão numérica.  
  
## <a name="remarks"></a>Comentários
  
  As funções LOG10 e POWER estão inversamente relacionadas uma à outra. Por exemplo, 10 ^ LOG10(n) = n.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir declara uma variável e retorna o valor LOG10 da variável especificada (100).  
  
```sql
SELECT LOG10(100) AS log10 
```  
  
 Este é o conjunto de resultados.  
  
```json
[{log10: 2}]  
```  

## <a name="remarks"></a>Comentários

Essa função do sistema não usará o índice.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
