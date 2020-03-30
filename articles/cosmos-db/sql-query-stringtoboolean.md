---
title: StringToBoolean no azure Cosmos DB linguagem de consulta
description: Saiba mais sobre a função do sistema SQL StringToBoolean no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ce11db91eff51e669f0917fbf34b1d560d0e9f07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296534"
---
# <a name="stringtoboolean-azure-cosmos-db"></a>StringToBoolean (Azure Cosmos DB)
 Retorna expressão traduzida para um booleano. Se a expressão não puder ser traduzida, retorna indefinida.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
StringToBoolean(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de corda a ser analisado como uma expressão booleana.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão booleana ou indefinida.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a `StringToBoolean` seguir mostra como se comporta entre diferentes tipos. 
 
 A seguir, exemplos com entrada válida.

O espaço em branco só é permitido antes ou depois de "verdadeiro"/"falso".

```sql
SELECT 
    StringToBoolean("true") AS b1, 
    StringToBoolean("    false") AS b2,
    StringToBoolean("false    ") AS b3
```  
  
 Este é o conjunto de resultados.  
  
```json
[{"b1": true, "b2": false, "b3": false}]
```  

A seguir, exemplos com entrada inválida.

 Os booleanos são sensíveis a maiúsculas e devem ser escritos com todos os caracteres minúsculos, ou seja, "verdadeiros" e "falsos".

```sql
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

Este é o conjunto de resultados.  
  
```json
[{}]
``` 

A expressão passada será analisado como uma expressão booleana; essas entradas não avaliam para digitar booleano e, assim, retornam indefinidos.

```sql
SELECT 
    StringToBoolean("null"),
    StringToBoolean(undefined),
    StringToBoolean(NaN), 
    StringToBoolean(false), 
    StringToBoolean(true)
```  

Este é o conjunto de resultados.  
  
```json
[{}]
```  

## <a name="remarks"></a>Comentários

Esta função do sistema não utilizará o índice.

## <a name="next-steps"></a>Próximas etapas

- [Funções de string Azure Cosmos DB](sql-query-string-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
