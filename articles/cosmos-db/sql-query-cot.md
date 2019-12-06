---
title: COT na linguagem de consulta Azure Cosmos DB
description: Saiba como a função de sistema SQL cotangente (COT) no Azure Cosmos DB retorna a cotangente trigonométrica do ângulo especificado, em radianos, na expressão numérica especificada
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f049d1295eef3e6a45abeaafe8c22d376f90abe1
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871492"
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
  
  O exemplo a seguir calcula a `COT` do ângulo especificado.  
  
```sql
SELECT COT(124.1332) AS cot  
```  
  
 Este é o conjunto de resultados.  
  
```json
[{"cot": -0.040311998371148884}]  
```  
  

## <a name="next-steps"></a>Próximos passos

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
