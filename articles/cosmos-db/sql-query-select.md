---
title: Cláusula SELECT no Azure Cosmos DB
description: Saiba mais sobre a cláusula SQL SELECT para Azure Cosmos DB. Use SQL como uma linguagem de consulta JSON Azure Cosmos DB.
author: ginarobinson
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: girobins
ms.openlocfilehash: 013ebdcdbac41825c10a1362f73ab4c94052400d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77469928"
---
# <a name="select-clause-in-azure-cosmos-db"></a>Cláusula SELECT no Azure Cosmos DB

Cada consulta consiste em uma cláusula SELECT e em cláusulas opcional from e [Where](sql-query-where.md) , por padrões [de](sql-query-from.md) SQL ANSI. Normalmente, a origem na cláusula FROM é enumerada e a cláusula WHERE aplica um filtro na origem para recuperar um subconjunto de itens JSON. Em seguida, a cláusula SELECT projeta os valores JSON solicitados na lista de seleção.

## <a name="syntax"></a>Sintaxe

```sql
SELECT <select_specification>  

<select_specification> ::=   
      '*'   
      | [DISTINCT] <object_property_list>   
      | [DISTINCT] VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=   
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
  
```  
  
## <a name="arguments"></a>Argumentos
  
- `<select_specification>`  

  Propriedades ou valor a ser selecionado para o conjunto de resultados.  
  
- `'*'`  

  Especifica que o valor deve ser recuperado sem fazer alterações. Especificamente, se o valor processado for um objeto, todas as propriedades serão recuperadas.  
  
- `<object_property_list>`  
  
  Especifica a lista de propriedades a serem recuperadas. Cada valor retornado será um objeto com as propriedades especificadas.  
  
- `VALUE`  

  Especifica que o valor JSON deve ser recuperado em vez do objeto JSON completo. Isso, ao contrário de `<property_list>`, não encapsula o valor projetado em um objeto.  
 
- `DISTINCT`
  
  Especifica que duplicatas de propriedades projetadas devem ser removidas.  

- `<scalar_expression>`  

  Expressão que representa o valor a ser calculado. Consulte a seção [Expressões escalares](sql-query-scalar-expressions.md) para obter detalhes.  

## <a name="remarks"></a>Comentários

A sintaxe `SELECT *` só será válida se a cláusula FROM tiver declarado exatamente um alias. `SELECT *` fornece uma projeção de identidade, que pode ser útil se nenhuma projeção é necessária. SELECT * só é válida se a cláusula FROM é especificada e introduzida uma única fonte de entrada.  
  
Ambos `SELECT <select_list>` e `SELECT *` são "açúcar sintático" e podem ser expressos, como alternativa, usando instruções SELECT simples, conforme mostrado abaixo.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   é equivalente a:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   é equivalente a:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>Exemplos

O exemplo de consulta SELECT a `address` seguir `Families` retorna `id` de `AndersenFamily`cujas correspondências:

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Os resultados são:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

### <a name="quoted-property-accessor"></a>Acessador de propriedade entre aspas
Você pode acessar as propriedades usando o operador de propriedade entre aspas []. Por exemplo: `SELECT c.grade` and `SELECT c["grade"]` são equivalentes. Essa sintaxe é útil para escapar de uma propriedade que contém espaços, caracteres especiais ou tem o mesmo nome que uma palavra-chave SQL ou reservada.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

### <a name="nested-properties"></a>Propriedades aninhadas

O exemplo a seguir projeta duas propriedades `f.address.state` aninhadas `f.address.city`e.

```sql
    SELECT f.address.state, f.address.city
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Os resultados são:

```json
    [{
      "state": "WA",
      "city": "Seattle"
    }]
```
### <a name="json-expressions"></a>Expressões JSON

A projeção também oferece suporte a expressões JSON, conforme mostrado no exemplo a seguir:

```sql
    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Os resultados são:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle",
        "name": "AndersenFamily"
      }
    }]
```

No exemplo anterior, a cláusula SELECT precisa criar um objeto JSON e, como o exemplo não fornece nenhuma chave, a cláusula usa o nome `$1`da variável de argumento implícito. A consulta a seguir retorna duas variáveis de argumento `$1` implícitas: e `$2`.

```sql
    SELECT { "state": f.address.state, "city": f.address.city },
           { "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Os resultados são:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]
```
## <a name="reserved-keywords-and-special-characters"></a>Palavras-chave reservadas e caracteres especiais

Se seus dados contiverem Propriedades com os mesmos nomes de palavras-chave reservadas, como "Order" ou "Group", as consultas nesses documentos resultarão em erros de sintaxe. Você deve incluir explicitamente a propriedade no `[]` caractere para executar a consulta com êxito.

Por exemplo, aqui está um documento com uma propriedade chamada `order` e uma propriedade `price($)` que contém caracteres especiais:

```json
{
  "id": "AndersenFamily",
  "order": [
     {
         "orderId": "12345",
         "productId": "A17849",
         "price($)": 59.33
     }
  ],
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Se você executar uma consulta que inclui a `order` propriedade ou `price($)` Propriedade, receberá um erro de sintaxe.

```sql
SELECT * FROM c where c.order.orderid = "12345"
```
```sql
SELECT * FROM c where c.order.price($) > 50
```
O resultado é:

`
Syntax error, incorrect syntax near 'order'
`

Você deve reescrever as mesmas consultas abaixo:

```sql
SELECT * FROM c WHERE c["order"].orderId = "12345"
```

```sql
SELECT * FROM c WHERE c["order"]["price($)"] > 50
```

## <a name="next-steps"></a>Próximas etapas

- [Introdução](sql-query-getting-started.md)
- [Amostras do .NET no Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Cláusula WHERE](sql-query-where.md)
