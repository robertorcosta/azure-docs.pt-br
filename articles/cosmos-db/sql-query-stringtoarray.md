---
title: StringToArray no idioma de consulta do Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL StringToArray no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 18acbd94fa3d717fc20b9e1020b9bf7c6db7744d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302909"
---
# <a name="stringtoarray-azure-cosmos-db"></a>StringToArray (Azure Cosmos DB)
 Retorna a expressão traduzida para uma Matriz. Se a expressão não puder ser traduzida, retorna indefinida.  
  
## <a name="syntax"></a>Sintaxe
  
```sql  
StringToArray(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de string a ser analisado como uma expressão JSON Array. 
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão de matriz ou indefinida. 
  
## <a name="remarks"></a>Comentários
  Os valores de seqüência aninhados devem ser escritos com aspas duplas para serem JSON válidos. Para obter detalhes sobre o formato JSON, consulte [json.org](https://json.org/)
  
## <a name="examples"></a>Exemplos
  
  O exemplo a `StringToArray` seguir mostra como se comporta entre diferentes tipos. 
  
 A seguir, exemplos com entrada válida.

```sql
SELECT 
    StringToArray('[]') AS a1, 
    StringToArray("[1,2,3]") AS a2,
    StringToArray("[\"str\",2,3]") AS a3,
    StringToArray('[["5","6","7"],["8"],["9"]]') AS a4,
    StringToArray('[1,2,3, "[4,5,6]",[7,8]]') AS a5
```

Este é o conjunto de resultados.

```json
[{"a1": [], "a2": [1,2,3], "a3": ["str",2,3], "a4": [["5","6","7"],["8"],["9"]], "a5": [1,2,3,"[4,5,6]",[7,8]]}]
```

O seguinte é um exemplo de entrada inválida. 
   
 Aspas únicas dentro da matriz não são JSON válidos.
Mesmo que eles sejam válidos dentro de uma consulta, eles não analisarão para matrizes válidas. As strings dentro da seqüência de matrizes devem ser escapadas "[ "\\"]"\\ou a citação circundante deve ser única '["]".

```sql
SELECT
    StringToArray("['5','6','7']")
```

Este é o conjunto de resultados.

```json
[{}]
```

A seguir, exemplos de entrada inválida.
   
 A expressão aprovada será analisado como uma matriz JSON; a seguir, não avaliam a matriz de digitações e, portanto, retornam indefinidas.
   
```sql
SELECT
    StringToArray("["),
    StringToArray("1"),
    StringToArray(NaN),
    StringToArray(false),
    StringToArray(undefined)
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
