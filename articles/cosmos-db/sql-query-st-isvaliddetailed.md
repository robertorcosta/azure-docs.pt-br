---
title: ST_ISVALIDDETAILED na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL ST_ISVALIDDETAILED no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: df2d4265393085a58699b3576ce2461ed63de317
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93080032"
---
# <a name="st_isvaliddetailed-azure-cosmos-db"></a>ST_ISVALIDDETAILED (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retorna um valor JSON que contém um valor Booliano caso a expressão especificada de Ponto, Polígono ou LineString GeoJSON é válida e, se for inválida, adicionalmente o motivo como um valor de cadeia de caracteres.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
ST_ISVALIDDETAILED(<spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*spatial_expr*  
   É uma expressão de ponto ou polígono geojson.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna um valor JSON que contém um valor Booliano caso a expressão de ponto ou polígono GeoJSON especificada é válida e, se for inválida, adicionalmente o motivo como um valor de cadeia de caracteres.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir como verificar a validade (com detalhes) usando `ST_ISVALIDDETAILED` .  
  
```sql
SELECT ST_ISVALIDDETAILED({   
  "type": "Polygon",   
  "coordinates": [[ [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] ]]  
}) AS b 
```  
  
 Este é o conjunto de resultados.  
  
```json
[{  
  "b": {
    "valid": false,
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  

## <a name="next-steps"></a>Próximas etapas

- [Funções espaciais Azure Cosmos DB](sql-query-spatial-functions.md)
- [Funções de sistema do Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
