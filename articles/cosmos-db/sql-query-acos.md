---
title: ACOS na linguagem de consulta Azure Cosmos DB
description: Saiba como a função do sistema SQL de ACOS (arccosice) no Azure Cosmos DB retorna o ângulo, em radianos, cujo cosseno é a expressão numérica especificada
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: fd5baa3ffefb7039c40706253e633661a51150f2
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873515"
---
# <a name="acos-azure-cosmos-db"></a>ACOS (Azure Cosmos DB)
 Retorna o ângulo, em radianos, cujo cosseno é a expressão numérica especificada (também chamado de arco cosseno).  
  
## <a name="syntax"></a>Sintaxe
  
```sql
ACOS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*numeric_expr*  
   É uma expressão numérica.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão numérica.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir retorna o `ACOS` de-1.  
  
```sql
SELECT ACOS(-1) AS acos 
```  
  
 Este é o conjunto de resultados.  
  
```json
[{"acos": 3.1415926535897931}]  
```  

## <a name="next-steps"></a>Próximos passos

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
