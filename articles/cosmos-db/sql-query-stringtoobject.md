---
title: StringToObject no idioma de consulta do Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL StringToObject no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c3e61d1efe20910d84ef4ff583d74982b3ea9f3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296374"
---
# <a name="stringtoobject-azure-cosmos-db"></a>StringToObject (Azure Cosmos DB)
 Retorna a expressão traduzida para um Objeto. Se a expressão não puder ser traduzida, retorna indefinida.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
StringToObject(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de string a ser analisado como uma expressão de objeto JSON. Observe que os valores de seqüência aninhados devem ser escritos com aspas duplas para serem válidos. Para obter detalhes sobre o formato JSON, consulte [json.org](https://json.org/)  
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão de objeto ou indefinida.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a `StringToObject` seguir mostra como se comporta entre diferentes tipos. 
  
 A seguir, exemplos com entrada válida.

```sql
SELECT 
    StringToObject("{}") AS obj1, 
    StringToObject('{"A":[1,2,3]}') AS obj2,
    StringToObject('{"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]}') AS obj3, 
    StringToObject("{\"C\":[{\"c1\":[5,6,7]},{\"c2\":8},{\"c3\":9}]}") AS obj4
``` 

Este é o conjunto de resultados.

```json
[{"obj1": {}, 
  "obj2": {"A": [1,2,3]}, 
  "obj3": {"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]},
  "obj4": {"C":[{"c1":[5,6,7]},{"c2":8},{"c3":9}]}}]
```

 A seguir, exemplos com entrada inválida.
Mesmo que eles sejam válidos dentro de uma consulta, eles não analisarão objetos válidos. As strings dentro da seqüência\\de\\objeto\\saem "{ "{ a ": "str\\"}" ou a citação ao redor deve ser simples '{"a": "str"}'.

As paspas únicas em torno de nomes de propriedades não são JSON válidos.

```sql
SELECT 
    StringToObject("{'a':[1,2,3]}")
```

Este é o conjunto de resultados.

```json
[{}]
```  

Os nomes de propriedade sem aspas ao redor não são JSON válidos.

```sql
SELECT 
    StringToObject("{a:[1,2,3]}")
```

Este é o conjunto de resultados.

```json
[{}]
``` 

A seguir, exemplos com entrada inválida.

 A expressão passada será analisado como um objeto JSON; essas entradas não avaliam para digitar objeto e, portanto, retornam indefinidas.

```sql
SELECT 
    StringToObject("}"),
    StringToObject("{"),
    StringToObject("1"),
    StringToObject(NaN), 
    StringToObject(false), 
    StringToObject(undefined)
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
