---
title: Fazer logon Azure Cosmos DB linguagem de consulta
description: Saiba mais sobre a função de sistema SQL de LOG em Azure Cosmos DB para retornar o logaritmo natural da expressão numérica especificada
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ae7812670da836efa326b9224547e4d1b64374c2
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873277"
---
# <a name="log-azure-cosmos-db"></a>LOG (Azure Cosmos DB)
 Retorna o logaritmo natural de expressão numérica especificada.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
LOG (<numeric_expr> [, <base>])  
```  
  
## <a name="arguments"></a>Argumentos
  
*numeric_expr*  
   É uma expressão numérica.  
  
*base*  
   Argumento numérico opcional que define a base para o logaritmo.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão numérica.  
  
## <a name="remarks"></a>Comentários
  
  Por padrão, LOG() retorna o logaritmo natural. Você pode alterar a base do logaritmo para outro valor usando o parâmetro opcional de base.  
  
  O logaritmo natural é o logaritmo de base **e**, em que **e** é uma constante irracional aproximadamente igual a 2,718281828.  
  
  O logaritmo natural do exponencial de um número é o número em si: LOG(EXP(n)) = n. E o exponencial do logaritmo natural de um número é o número em si: EXP(LOG(n)) = n.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir declara uma variável e retorna o valor logarítmico da variável especificada (10).  
  
```sql
SELECT LOG(10) AS log  
```  
  
 Este é o conjunto de resultados.  
  
```json
[{log: 2.3025850929940459}]  
```  
  
 O exemplo a seguir calcula o `LOG` para o exponente de um número.  
  
```sql
SELECT EXP(LOG(10)) AS expLog  
```  
  
 Este é o conjunto de resultados.  
  
```json
[{expLog: 10.000000000000002}]  
```  

## <a name="next-steps"></a>Próximos passos

- [Funções matemáticas Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
