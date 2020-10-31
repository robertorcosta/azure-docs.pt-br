---
title: GRAUS na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função de sistema SQL de graus em Azure Cosmos DB para retornar o ângulo correspondente em graus para um ângulo especificado em radianos
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 991d83a329603342975a8186fe704afc53813e08
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095757"
---
# <a name="degrees-azure-cosmos-db"></a>GRAUS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retorna o ângulo correspondente, em graus, para um ângulo especificado em radianos.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
DEGREES (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*numeric_expr*  
   É uma expressão numérica.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão numérica.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir retorna o número de graus em um ângulo de radiano PI/2.  
  
```sql
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 Este é o conjunto de resultados.  
  
```json
[{"degrees": 90}]  
```  

## <a name="remarks"></a>Comentários

Essa função do sistema não usará o índice.

## <a name="next-steps"></a>Próximas etapas

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funções de sistema do Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
