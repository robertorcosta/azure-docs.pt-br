---
title: Tipo de verificação de funções no idioma de consulta Azure Cosmos DB
description: Saiba mais sobre as funções do sistema SQL de verificação de tipos no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: cac8be4c39737ca209653532d00375c8c9bee48e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "71349068"
---
# <a name="type-checking-functions-azure-cosmos-db"></a>Funções de verificação de tipo (Azure Cosmos DB)

As funções de verificação de tipo permitem verificar o tipo de expressão dentro de uma consulta SQL. Você pode usar funções de verificação de tipo para determinar os tipos de propriedades dentro de itens na mosca, quando eles são variáveis ou desconhecidos. 

## <a name="functions"></a>Funções

Aqui está uma tabela de funções de verificação de tipo incorporadas suportadas:

As funções a seguir dão suporte a verificação de tipo em relação aos valores de entrada e retornam, cada um, um valor booliano.  
  
||||  
|-|-|-|  
|[IS_ARRAY](sql-query-is-array.md)|[IS_BOOL](sql-query-is-bool.md)|[IS_DEFINED](sql-query-is-defined.md)|  
|[Is_null](sql-query-is-null.md)|[IS_NUMBER](sql-query-is-number.md)|[IS_OBJECT](sql-query-is-object.md)|  
|[IS_PRIMITIVE](sql-query-is-primitive.md)|[IS_STRING](sql-query-is-string.md)||  
  

## <a name="next-steps"></a>Próximas etapas

- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
- [Funções definidas pelo usuário](sql-query-udfs.md)
- [Agregações](sql-query-aggregates.md)
