---
title: COS na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre como a função de sistema do cosseno (COS) do SQL no Azure Cosmos DB retorna o cosseno trigonométrica do ângulo especificado, em radianos, na expressão especificada
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 40d277ff38691e2cb74bd4d5d78a666c304acfcd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "78304014"
---
# <a name="cos-azure-cosmos-db"></a>COS (Azure Cosmos DB)
 Retorna o cosseno trigonométrico do ângulo especificado, em radianos, na expressão especificada.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
COS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*numeric_expr*  
   É uma expressão numérica.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão numérica.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir calcula o `COS` do ângulo especificado.  
  
```sql
SELECT COS(14.78) AS cos  
```  
  
 Este é o conjunto de resultados.  
  
```json
[{"cos": -0.59946542619465426}]  
```  

## <a name="remarks"></a>Comentários

Essa função do sistema não usará o índice.

## <a name="next-steps"></a>Próximas etapas

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funções de sistema do Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
