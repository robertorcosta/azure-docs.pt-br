---
title: SUBSTRING no azure Cosmos DB linguagem de consulta
description: Saiba mais sobre a função do sistema SQL SUBSTRING no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d4462fc407093b23510bddfae4d9f55d68f8c0fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303691"
---
# <a name="substring-azure-cosmos-db"></a>SUBSTRING (Azure Cosmos DB)
 Retorna parte de uma expressão de cadeia de caracteres começando na posição baseada em zero do caractere especificado e continua até o comprimento especificado ou até o final da cadeia de caracteres.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
SUBSTRING(<str_expr>, <num_expr1>, <num_expr2>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de cordas.
  
*num_expr1*  
   É uma expressão numérica para denotar o caractere inicial. Um valor de 0 é o primeiro caractere de *str_expr*.
  
*num_expr2*  
   É uma expressão numérica para denotar o número máximo de caracteres de *str_expr* a serem devolvidos. Um valor de 0 ou menos resulta em cadeia vazia.

## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão de cadeia de caracteres.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir retorna a subcadeia de caracteres de "abc" começando em 1 e com o comprimento de 1 caractere.  
  
```sql
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 Este é o conjunto de resultados.  
  
```json
[{"substring": "b"}]  
```

## <a name="remarks"></a>Comentários

Esta função do sistema beneficiará de um `0`índice de [intervalo](index-policy.md#includeexclude-strategy) se a posição inicial for .

## <a name="next-steps"></a>Próximas etapas

- [Funções de string Azure Cosmos DB](sql-query-string-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
