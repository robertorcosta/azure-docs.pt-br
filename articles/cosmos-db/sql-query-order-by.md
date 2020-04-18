---
title: ORDEM POR cláusula no Azure Cosmos DB
description: Saiba mais sobre a cláusula SQL ORDER BY para Azure Cosmos DB. Use o SQL como uma linguagem de consulta Azure Cosmos DB JSON.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: tisande
ms.openlocfilehash: 70702ee4a77e8b3c46de4354f3394bca4080d837
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641390"
---
# <a name="order-by-clause-in-azure-cosmos-db"></a>ORDEM POR cláusula no Azure Cosmos DB

A `ORDER BY` cláusula opcional especifica a ordem de classificação dos resultados retornados pela consulta.

## <a name="syntax"></a>Sintaxe
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>Argumentos
  
- `<sort_specification>`  
  
   Especifica uma propriedade ou expressão pela qual classificar o conjunto de resultados da consulta. Uma coluna de classificação pode ser especificada como um nome ou alias de propriedade.  
  
   Várias propriedades podem ser especificadas. Os nomes das propriedades devem ser únicos. A seqüência das `ORDER BY` propriedades do tipo na cláusula define a organização do conjunto de resultados ordenados. Ou seja, o conjunto de resultados é classificado pela primeira propriedade e, em seguida, essa lista ordenada é classificada pela segunda propriedade e assim por diante.  
  
   Os nomes de propriedade `ORDER BY` mencionados na cláusula devem corresponder a uma propriedade na lista de `FROM` seleção ou a uma propriedade definida na coleção especificada na cláusula sem qualquer ambiguidade.  
  
- `<sort_expression>`  
  
   Especifica uma ou mais propriedades ou expressões sobre as quais classificar o conjunto de resultados de consulta.  
  
- `<scalar_expression>`  
  
   Consulte a seção [Expressões escalares](sql-query-scalar-expressions.md) para obter detalhes.  
  
- `ASC | DESC`  
  
   Define que os valores na coluna especificada devem ser classificados em ordem crescente ou decrescente. `ASC`classifica do menor valor ao maior valor. `DESC`tipos de maior valor para menor valor. `ASC`é a ordem de classificação padrão. Valores nulos são tratados como os menores valores possíveis.  
  
## <a name="remarks"></a>Comentários  
  
   A `ORDER BY` cláusula exige que a política de indexação inclua um índice para os campos que estão sendo classificados. O tempo de execução da consulta Azure Cosmos DB suporta a classificação contra um nome de propriedade e não contra propriedades computadas. O Azure Cosmos `ORDER BY` DB suporta várias propriedades. Para executar uma consulta com várias propriedades ORDER BY, você deve definir um [índice composto](index-policy.md#composite-indexes) nos campos que estão sendo classificados.

> [!Note]
> Se as propriedades que estão sendo classificadas podem ser indefinidas para alguns documentos e você deseja recuperá-los em uma consulta ORDER BY, você deve incluir explicitamente esse caminho no índice. A política de indexação padrão não permitirá a recuperação dos documentos em que a propriedade de classificação é indefinida. [Revise as consultas de exemplo em documentos com alguns campos ausentes](#documents-with-missing-fields).

## <a name="examples"></a>Exemplos

Por exemplo, aqui está uma consulta que recupera famílias em ordem crescente do nome da cidade residente:

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

Os resultados são:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

A consulta a seguir `id`recupera a família por ordem da data de criação do item. Item `creationDate` é um número que representa o *tempo de época*, ou tempo decorrido desde 1 de janeiro de 1970 em segundos.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

Os resultados são:

```json
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472
      }
    ]
```

Além disso, você pode encomendar por várias propriedades. Uma consulta que encomenda por várias propriedades requer um [índice composto](index-policy.md#composite-indexes). Considere a consulta a seguir.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

Esta consulta recupera a `id` família em ordem ascendente do nome da cidade. Se vários itens tiverem o mesmo nome da cidade, `creationDate` a consulta será encomendada pela ordem decrescente.

## <a name="documents-with-missing-fields"></a>Documentos com campos perdidos

As consultas `ORDER BY` com as que são executadas contra contêineres com a política de indexação padrão não retornarão documentos onde a propriedade de classificação está indefinida. Se você quiser incluir documentos onde a propriedade de classificação está indefinida, você deve incluir explicitamente essa propriedade na política de indexação.

Por exemplo, aqui está um contêiner com uma política de indexação que não inclui explicitamente quaisquer caminhos além `"/*"`de :

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

Se você executar uma consulta `lastName` que `Order By` inclua na cláusula, os resultados `lastName` incluirão apenas documentos que tenham uma propriedade definida. Não definimos um caminho `lastName` incluído explícito para `lastName` que quaisquer documentos sem um não apareçam nos resultados da consulta.

Aqui está uma consulta `lastName` que classifica em dois documentos, `lastName` um dos quais não tem uma definição:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

Os resultados incluem apenas o `lastName`documento que tem um definido:

```json
    [
        {
            "id": "AndersenFamily",
            "lastName": "Andersen"
        }
    ]
```

Se atualizarmos a política de indexação do contêiner `lastName`para incluir explicitamente um caminho para, incluiremos documentos com uma propriedade de classificação indefinida nos resultados da consulta. Você deve definir explicitamente o caminho para levar a esse valor escalar (e não além dele). Você deve `?` usar o caractere em sua definição de caminho na política `lastName` de indexação para garantir que você indexe explicitamente a propriedade e nenhum caminho aninhado adicional além dele. Se `Order By` a consulta usar um [índice composto,](index-policy.md#composite-indexes)os resultados sempre incluirão documentos com uma propriedade de classificação indefinida nos resultados da consulta.

Aqui está uma política de indexação de amostra `lastName` que permite que você tenha documentos com um aparecimento indefinido nos resultados da consulta:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/lastName/?"
        },
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

Se você executar a mesma consulta novamente, os documentos que estão faltando `lastName` aparecerão primeiro nos resultados da consulta:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

Os resultados são:

```json
[
    {
        "id": "WakefieldFamily"
    },
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    }
]
```

Se você modificar a `DESC`ordem de `lastName` classificação para , documentos que estão faltando aparecer ão por último nos resultados da consulta:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName DESC
```

Os resultados são:

```json
[
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    },
    {
        "id": "WakefieldFamily"
    }
]
```

## <a name="next-steps"></a>Próximas etapas

- [Começando](sql-query-getting-started.md)
- [Políticas de indexação no Azure Cosmos DB](index-policy.md)
- [Cláusula LIMITE DE DESLOCAMENTO](sql-query-offset-limit.md)
