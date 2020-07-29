---
title: StringEquals na linguagem de consulta do Azure Cosmos DB
description: Saiba mais sobre como a função de sistema StringEquals do SQL no Azure Cosmos DB retorna um booliano que indica se a primeira expressão de cadeia de caracteres corresponde à segunda
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 57d124421082e1c38fab4d982687a8e6c970505e
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83853688"
---
# <a name="stringequals-azure-cosmos-db"></a>STRINGEQUALS (Azure Cosmos DB)

 Retorna um valor booliano que indica se a primeira expressão de cadeia de caracteres corresponde à segunda.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
STRINGEQUALS(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr1*  
   É a primeira expressão de cadeia de caracteres a ser comparada.  
  
*str_expr2*  
   É a segunda expressão de cadeia de caracteres a ser comparada.  

*bool_expr* Valor opcional para ignorar maiúsculas e minúsculas. Quando definido como true, StringEquals fará uma pesquisa que não diferencia maiúsculas de minúsculas. Quando não especificado, esse valor é false.
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão booliana.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir verifica se "abc" corresponde a "abc" e se "abc" corresponde a "ABC".  
  
```sql
SELECT STRINGEQUALS("abc", "abc", false) AS c1, STRINGEQUALS("abc", "ABC", false) AS c2,  STRINGEQUALS("abc", "ABC", true) AS c3
```  
  
 Este é o conjunto de resultados.  
  
```json
[
    {
        "c1": true,
        "c2": false,
        "c3": true
    }
]
```  

## <a name="remarks"></a>Comentários

Essa função do sistema se beneficiará de um [índice de intervalo](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Próximas etapas

- [Funções de cadeia de caracteres do Azure Cosmos DB](sql-query-string-functions.md)
- [Funções do sistema do Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
