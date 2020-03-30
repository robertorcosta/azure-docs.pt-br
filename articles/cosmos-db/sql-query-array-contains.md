---
title: ARRAY_CONTAINS no idioma de consulta do Azure Cosmos DB
description: Saiba como o Array contém a função do sistema SQL no Azure Cosmos DB retorna um Booleano indicando se a matriz contém o valor especificado
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 47fe20888aa546e414b268b30c2e03580750a040
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303470"
---
# <a name="array_contains-azure-cosmos-db"></a>ARRAY_CONTAINS (Azure Cosmos DB)
Retorna um valor booliano que indica se a matriz contém o valor especificado. Você pode verificar uma correspondência parcial ou completa de um objeto usando uma expressão booliana no comando. 

## <a name="syntax"></a>Sintaxe
  
```sql
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
## <a name="arguments"></a>Argumentos
  
*arr_expr*  
   É a expressão de matriz a ser pesquisada.  
  
*Expr*  
   É a expressão a ser encontrada.  

*bool_expr*  
   É uma expressão booleana. Se ele avaliar como 'verdadeiro' e se o valor de pesquisa especificado for um objeto, o comando será verificado para uma correspondência parcial (o objeto de pesquisa é um subconjunto de um dos objetos). Se ele avaliar como 'falso', o comando verifica se há uma correspondência completa de todos os objetos dentro da matriz. O valor padrão, se não especificado, é false. 
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna um valor booliano.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir de como `ARRAY_CONTAINS`verificar a adesão em uma matriz usando .  
  
```sql
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples") AS b1,  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes") AS b2  
```  
  
 Este é o conjunto de resultados.  
  
```json
[{"b1": true, "b2": false}]  
```  

O exemplo a seguir mostra como verificar em busca de correspondência parcial de um JSON em uma matriz usando ARRAY_CONTAINS.  
  
```sql
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true) AS b1, 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}) AS b2,
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) AS b3 
```  
  
 Este é o conjunto de resultados.  
  
```json
[{
  "b1": true,
  "b2": false,
  "b3": false
}]
```

## <a name="remarks"></a>Comentários

Esta função do sistema beneficiará de um [índice de intervalo](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Próximas etapas

- [Funções de matriz Azure Cosmos DB](sql-query-array-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
