---
title: ST_DISTANCE na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL ST_DISTANCE no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4908d5f9f6eccaaaf71308b868d712f0eb96cb52
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303147"
---
# <a name="st_distance-azure-cosmos-db"></a>ST_DISTANCE (Azure Cosmos DB)
 Retorna a distância entre as duas expressões de ponto GeoJSON, Polígono ou LineString.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*spatial_expr*  
   É qualquer expressão de objeto Ponto GeoJSON, Polígono ou LineString válida.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão numérica que contém a distância. Isso é expresso em metros no sistema de referência padrão.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como retornar todos os documentos da família que estão dentro de 30 km do local especificado usando a função interna `ST_DISTANCE`. .  
  
```sql
SELECT f.id
FROM Families f
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 Este é o conjunto de resultados.  
  
```json
[{  
  "id": "WakefieldFamily"  
}]  
```

## <a name="remarks"></a>Comentários

Essa função de sistema se beneficiará de um [índice geoespacial](index-policy.md#spatial-indexes).

## <a name="next-steps"></a>Próximas etapas

- [Funções espaciais Azure Cosmos DB](sql-query-spatial-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
