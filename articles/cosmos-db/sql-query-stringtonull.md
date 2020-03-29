---
title: StringToNull no azure Cosmos DB linguagem de consulta
description: Saiba mais sobre a função do sistema SQL StringToNull no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7e17547f88465103b61eabec04978ea806ffa2c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296432"
---
# <a name="stringtonull-azure-cosmos-db"></a>StringToNull (Azure Cosmos DB)
 Retorna expressão traduzida para nulo. Se a expressão não puder ser traduzida, retorna indefinida.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
StringToNull(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de corda a ser analisado como uma expressão nula.
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão nula ou indefinida.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a `StringToNull` seguir mostra como se comporta entre diferentes tipos. 

A seguir, exemplos com entrada válida.

 O espaço em branco só é permitido antes ou depois de "nulo".

```sql
SELECT 
    StringToNull("null") AS n1, 
    StringToNull("  null ") AS n2,
    IS_NULL(StringToNull("null   ")) AS n3
```  
  
 Este é o conjunto de resultados.  
  
```json
[{"n1": null, "n2": null, "n3": true}]
```  

A seguir, exemplos com entrada inválida.

Nulo é sensível ao caso e deve ser escrito com todos os caracteres minúsculos, ou seja, "nulo".

```sql
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 Este é o conjunto de resultados.  
  
```json
[{}]
```  

A expressão aprovada será analisado como uma expressão nula; essas entradas não avaliam para digitar nulo e, portanto, retornam indefinidos.

```sql
SELECT    
    StringToNull("true"), 
    StringToNull(false), 
    StringToNull(undefined),
    StringToNull(NaN) 
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
