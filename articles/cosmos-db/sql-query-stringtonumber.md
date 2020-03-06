---
title: StringToNumber na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL StringToNumber no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5ca8d0c4a6d244823dda6f0f79a3cf5c743a12a9
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78296415"
---
# <a name="stringtonumber-azure-cosmos-db"></a>StringToNumber (Azure Cosmos DB)
 Retorna a expressão convertida em um número. Se a expressão não puder ser convertida, retornará indefinido.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
StringToNumber(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de cadeia de caracteres a ser analisada como uma expressão de número JSON. Os números em JSON devem ser um número inteiro ou um ponto flutuante. Para obter detalhes sobre o formato JSON, consulte [JSON.org](https://json.org/)  
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão de número ou indefinido.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como `StringToNumber` se comporta entre diferentes tipos. 

O espaço em branco é permitido somente antes ou depois do número.

```sql
SELECT 
    StringToNumber("1.000000") AS num1, 
    StringToNumber("3.14") AS num2,
    StringToNumber("   60   ") AS num3, 
    StringToNumber("-1.79769e+308") AS num4
```  
  
 Este é o conjunto de resultados.  
  
```json
{{"num1": 1, "num2": 3.14, "num3": 60, "num4": -1.79769e+308}}
```  

Em JSON, um número válido deve ser um número inteiro ou um ponto flutuante.

```sql
SELECT   
    StringToNumber("0xF")
```  
  
 Este é o conjunto de resultados.  
  
```json
{{}}
```  

A expressão passada será analisada como uma expressão numérica; essas entradas não são avaliadas como número de tipo e, portanto, retornam indefinidamente. 

```sql
SELECT 
    StringToNumber("99     54"),   
    StringToNumber(undefined),
    StringToNumber("false"),
    StringToNumber(false),
    StringToNumber(" "),
    StringToNumber(NaN)
```  
  
 Este é o conjunto de resultados.  
  
```json
{{}}
```  

## <a name="remarks"></a>Comentários

Essa função do sistema não usará o índice.

## <a name="next-steps"></a>Próximas etapas

- [Funções de cadeia de caracteres Azure Cosmos DB](sql-query-string-functions.md)
- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
