---
title: Funções de verificação de tipo no Azure Cosmos DB linguagem de consulta
description: Saiba mais sobre a verificação de tipo das funções do sistema SQL no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: cac8be4c39737ca209653532d00375c8c9bee48e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "71349068"
---
# <a name="type-checking-functions-azure-cosmos-db"></a>Funções de verificação de tipo (Azure Cosmos DB)

As funções de verificação de tipo permitem verificar o tipo de uma expressão em uma consulta SQL. Você pode usar funções de verificação de tipo para determinar os tipos de propriedades em itens imediatamente, quando elas são variáveis ou desconhecidas. 

## <a name="functions"></a>Funções

Aqui está uma tabela de funções internas de verificação de tipo com suporte:

As funções a seguir dão suporte a verificação de tipo em relação aos valores de entrada e retornam, cada um, um valor booliano.  
  
||||  
|-|-|-|  
|[IS_ARRAY](sql-query-is-array.md)|[IS_BOOL](sql-query-is-bool.md)|[IS_DEFINED](sql-query-is-defined.md)|  
|[IS_NULL](sql-query-is-null.md)|[IS_NUMBER](sql-query-is-number.md)|[IS_OBJECT](sql-query-is-object.md)|  
|[IS_PRIMITIVE](sql-query-is-primitive.md)|[IS_STRING](sql-query-is-string.md)||  
  

## <a name="next-steps"></a>Próximas etapas

- [Funções do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
- [Funções definidas pelo usuário](sql-query-udfs.md)
- [Agregações](sql-query-aggregates.md)
