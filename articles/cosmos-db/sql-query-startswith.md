---
title: COMEÇACOM no azure Cosmos DB linguagem de consulta
description: Saiba mais sobre a função do sistema SQL STARTSWITH no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9ed49c067946186f8b79f67bad0a460113eacb73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78295701"
---
# <a name="startswith-azure-cosmos-db"></a>STARTSWITH (Azure Cosmos DB)
 Retorna um valor booliano que indica se a primeira expressão de cadeia de caracteres começa com a segunda.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
STARTSWITH(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr1*  
   É uma expressão de cordas.
  
*str_expr2*  
   É uma expressão de corda a ser comparada com o início de *str_expr1*.

## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão booliana.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir verifica se a cadeia de caracteres "abc" começa com "b" e "a".  
  
```sql
SELECT STARTSWITH("abc", "b") AS s1, STARTSWITH("abc", "a") AS s2  
```  
  
 Este é o conjunto de resultados.  
  
```json
[{"s1": false, "s2": true}]  
```  

## <a name="remarks"></a>Comentários

Esta função do sistema beneficiará de um [índice de intervalo](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Próximas etapas

- [Funções de string Azure Cosmos DB](sql-query-string-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
