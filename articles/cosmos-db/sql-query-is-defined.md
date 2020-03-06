---
title: IS_DEFINED na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL IS_DEFINED no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a5fcaf52d8e9e6b942a95f0b0c43f3f654c5d5d2
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303844"
---
# <a name="is_defined-azure-cosmos-db"></a>IS_DEFINED (Azure Cosmos DB)
 Retorna um valor booliano que indica se um valor foi atribuído à propriedade.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
IS_DEFINED(<expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*expr*  
   É qualquer expressão.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão booliana.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir verifica a presença de uma propriedade no documento JSON especificado. O primeiro retorna true, já que "a" está presente, mas o segundo retorna false porque "b" está ausente.  
  
```sql
SELECT IS_DEFINED({ "a" : 5 }.a) AS isDefined1, IS_DEFINED({ "a" : 5 }.b) AS isDefined2 
```  
  
 Este é o conjunto de resultados.  
  
```json
[{"isDefined1":true,"isDefined2":false}]  
```  

## <a name="remarks"></a>Comentários

Essa função de sistema se beneficiará de um [índice de intervalo](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

- [Funções de verificação de tipo Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
