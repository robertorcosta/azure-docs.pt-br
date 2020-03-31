---
title: REVERTER no idioma de consulta do Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL REVERSE no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a22e1c8a5f4350bd2f966ee48f96368c648a4a1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302161"
---
# <a name="reverse-azure-cosmos-db"></a>REVERSO (Azure Cosmos DB)
 Retorna a ordem inversa de um valor da cadeia de caracteres.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
REVERSE(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de cordas.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão de cadeia de caracteres.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir `REVERSE` mostra como usar em uma consulta.  
  
```sql
SELECT REVERSE("Abc") AS reverse  
```  
  
 Este é o conjunto de resultados.  
  
```json
[{"reverse": "cbA"}]  
```  

## <a name="remarks"></a>Comentários

Esta função do sistema não utilizará o índice.

## <a name="next-steps"></a>Próximas etapas

- [Funções de string Azure Cosmos DB](sql-query-string-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
