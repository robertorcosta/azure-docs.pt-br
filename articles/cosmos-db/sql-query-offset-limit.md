---
title: Cláusula limite de compensação no Azure Cosmos DB
description: Aprenda a usar a cláusula LIMITE DE DESLOCAMENTO para pular e levar alguns valores ao consultar no Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 3d23676885323e370cee1e9cc9e98c7128faf2e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76771565"
---
# <a name="offset-limit-clause-in-azure-cosmos-db"></a>Cláusula limite de compensação no Azure Cosmos DB

A cláusula LIMITE DE DESLOCAMENTO é uma cláusula opcional a ser pulada e, em seguida, tirar algum número de valores da consulta. A contagem OFFSET e a contagem LIMITE são exigidas na cláusula LIMITE DE DESLOCAMENTO.

Quando o LIMITE DE DESLOCAMENTO é usado em conjunto com uma cláusula ORDER BY, o conjunto de resultados é produzido fazendo pular e assumir os valores ordenados. Se nenhuma cláusula DE ORDEM POR FOR USADA, resultará em uma ordem determinista de valores.

## <a name="syntax"></a>Sintaxe
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>Argumentos

- `<offset_amount>`

   Especifica o número inteiro de itens que os resultados da consulta devem ignorar.

- `<limit_amount>`
  
   Especifica o número inteiro de itens que os resultados da consulta devem incluir

## <a name="remarks"></a>Comentários
  
  Tanto `OFFSET` a contagem quanto `LIMIT` a `OFFSET LIMIT` contagem são exigidas na cláusula. Se uma `ORDER BY` cláusula opcional for usada, o conjunto de resultados será produzido fazendo o skip over dos valores ordenados. Caso contrário, a consulta retornará uma ordem fixa de valores.

  A carga RU de `OFFSET LIMIT` uma consulta com aumentará à medida que o número de termos sendo compensados aumentar. Para consultas que têm várias páginas de resultados, normalmente recomendamos o uso de tokens de continuação. Os tokens de continuação são um "marcador" para o lugar onde a consulta pode ser retomada posteriormente. Se você `OFFSET LIMIT`usar, não há "marcador". Se você quisesse retornar a próxima página da consulta, você teria que começar do início.
  
  Você deve `OFFSET LIMIT` usar para casos em que você gostaria de pular documentos inteiramente e economizar recursos do cliente. Por exemplo, você `OFFSET LIMIT` deve usar se quiser pular para o resultado da consulta 1000 e não tiver necessidade de visualizar os resultados de 1 a 999. No backend, `OFFSET LIMIT` ainda carrega cada documento, incluindo aqueles que são ignorados. A vantagem de desempenho é uma economia nos recursos do cliente, evitando processar documentos que não são necessários.

## <a name="examples"></a>Exemplos

Por exemplo, aqui está uma consulta que pula o primeiro valor e retorna o segundo valor (por ordem do nome da cidade residente):

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
    OFFSET 1 LIMIT 1
```

Os resultados são:

```json
    [
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

Aqui está uma consulta que ignora o primeiro valor e retorna o segundo valor (sem pedido):

```sql
   SELECT f.id, f.address.city
    FROM Families f
    OFFSET 1 LIMIT 1
```

Os resultados são:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "Seattle"
      }
    ]
```

## <a name="next-steps"></a>Próximas etapas

- [Começando](sql-query-getting-started.md)
- [Cláusula SELECT](sql-query-select.md)
- [ORDEM POR cláusula](sql-query-order-by.md)
