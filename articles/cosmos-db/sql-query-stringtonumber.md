---
title: StringToNumber no idioma de consulta do Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL StringToNumber no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5ca8d0c4a6d244823dda6f0f79a3cf5c743a12a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296415"
---
# <a name="stringtonumber-azure-cosmos-db"></a>StringToNumber (Azure Cosmos DB)
 Retorna a expressão traduzida para um número. Se a expressão não puder ser traduzida, retorna indefinida.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
StringToNumber(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   É uma expressão de string a ser analisado como uma expressão número JSON. Os números em JSON devem ser um inteiro ou um ponto flutuante. Para obter detalhes sobre o formato JSON, consulte [json.org](https://json.org/)  
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão Número ou indefinida.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a `StringToNumber` seguir mostra como se comporta entre diferentes tipos. 

O espaço em branco só é permitido antes ou depois do número.

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

Em JSON, um número válido deve ser inteiro ou um número de ponto flutuante.

```sql
SELECT   
    StringToNumber("0xF")
```  
  
 Este é o conjunto de resultados.  
  
```json
{{}}
```  

A expressão passada será analisado como uma expressão número; essas entradas não avaliam o número de digitados e, portanto, retornam indefinidas. 

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

Esta função do sistema não utilizará o índice.

## <a name="next-steps"></a>Próximas etapas

- [Funções de string Azure Cosmos DB](sql-query-string-functions.md)
- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
