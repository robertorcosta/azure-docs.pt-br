---
title: COT no idioma de consulta do Azure Cosmos DB
description: Saiba como o sistema Cotangent(COT) SQL funciona no Azure Cosmos DB retorna o cotangent trigonométrico do ângulo especificado, em radianos, na expressão numérica especificada
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 25c907644f58ee40ea08e5636d68dc0e84564a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299480"
---
# <a name="cot-azure-cosmos-db"></a>COT (Azure Cosmos DB)
 Retorna a cotangente trigonométrica do ângulo especificado, em radianos, na expressão numérica especificada.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
COT(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*numeric_expr*  
   É uma expressão numérica.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão numérica.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a `COT` seguir calcula o ângulo especificado.  
  
```sql
SELECT COT(124.1332) AS cot  
```  
  
 Este é o conjunto de resultados.  
  
```json
[{"cot": -0.040311998371148884}]  
```  

## <a name="remarks"></a>Comentários

Esta função do sistema não utilizará o índice.

## <a name="next-steps"></a>Próximas etapas

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
