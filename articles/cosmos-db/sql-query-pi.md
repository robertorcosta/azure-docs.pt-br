---
title: PI na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL PI no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 27832008e8922e339a648985192a58b111555bc9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "71349657"
---
# <a name="pi-azure-cosmos-db"></a>PI (Azure Cosmos DB)
 Retorna o valor constante de PI.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
PI ()  
```  
   
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão numérica.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir retorna o valor `PI`.  
  
```sql
SELECT PI() AS pi 
```  
  
 Este é o conjunto de resultados.  
  
```json
[{"pi": 3.1415926535897931}]  
```  

## <a name="next-steps"></a>Próximas etapas

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
