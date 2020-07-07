---
title: Usar SQL dinâmico no Synapse SQL
description: Dicas para usar SQL dinâmico no Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: b546e6ba6967edcf41e2830a639e69d436827c40
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "81429597"
---
# <a name="dynamic-sql-in-synapse-sql"></a>SQL dinâmico no Synapse SQL
Neste artigo, você encontrará dicas para usar o SQL dinâmico e desenvolver soluções usando o SQL Synapse.

## <a name="dynamic-sql-example"></a>Exemplo SQL dinâmico

Ao desenvolver o código do aplicativo, talvez seja necessário usar o SQL dinâmico para ajudar a fornecer soluções flexíveis, genéricas e modulares.

> [!NOTE]
> O pool SQL não oferece suporte a tipos de dados de blob neste momento. O não suporte a tipos de dados blob pode limitar o tamanho de suas cadeias de caracteres desde que os tipos de dados blobs incluam tipos varchar (máx) e nvarchar (máx). Se você usou estes tipos no código do seu aplicativo ao compilar cadeias de caracteres muito grandes, você precisará dividir o código em partes e usar a instrução EXEC em seu lugar.

Um exemplo simples:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Se a cadeia de caracteres for curta, você poderá usar [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) normalmente.

> [!NOTE]
> As instruções executadas como SQL dinâmico ainda estarão sujeitas a todas as regras de validação do T-SQL.

## <a name="next-steps"></a>Próximas etapas

Para obter mais dicas de desenvolvimento, confira [visão geral de desenvolvimento](develop-overview.md).
