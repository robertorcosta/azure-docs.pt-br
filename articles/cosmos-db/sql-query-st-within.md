---
title: ST_WITHIN no idioma de consulta do Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL ST_WITHIN no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 07a339d82f5e4bea1ea0412a5d5b19522611b54a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296109"
---
# <a name="st_within-azure-cosmos-db"></a>ST_WITHIN (Azure Cosmos DB)
 Retorna uma expressão booliana que indica se o objeto GeoJSON (Ponto, Polígono ou LineString) especificado no primeiro argumento está dentro do GeoJSON (Ponto, Polígono ou LineString) no segundo argumento.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*spatial_expr*  
   É uma expressão de objeto GeoJSON Point, Polygon ou LineString.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna um valor booliano.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como encontrar todos `ST_WITHIN`os documentos familiares dentro de um polígono usando .  
  
```sql
SELECT f.id
FROM Families f
WHERE ST_WITHIN(f.location, {  
    'type':'Polygon',
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Este é o conjunto de resultados.  
  
```json
[{ "id": "WakefieldFamily" }]  
```  

## <a name="remarks"></a>Comentários

Esta função do sistema beneficiará de um [índice geoespacial.](index-policy.md#spatial-indexes)

## <a name="next-steps"></a>Próximas etapas

- [Funções espaciais Azure Cosmos DB](sql-query-spatial-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
