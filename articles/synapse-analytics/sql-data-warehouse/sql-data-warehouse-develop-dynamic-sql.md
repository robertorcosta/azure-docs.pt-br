---
title: Usando SQL dinâmico
description: Dicas para soluções de desenvolvimento usando SQL dinâmico no pool de SQL do Synapse.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a9280bb8153204f86096cf8249ff053bee3f71cc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80633535"
---
# <a name="dynamic-sql-in-synapse-sql-pool"></a>SQL dinâmico no pool do SQL Synapse

Estão incluídas neste artigo Dicas para soluções de desenvolvimento usando SQL dinâmico no pool SQL.

## <a name="dynamic-sql-example"></a>Exemplo SQL dinâmico

Ao desenvolver o código do aplicativo para o pool SQL, talvez seja necessário usar o SQL dinâmico para ajudar a fornecer soluções flexíveis, genéricas e modulares. O pool SQL não dá suporte a tipos de dados de blob no momento.

O não suporte a tipos de dados blob pode limitar o tamanho de suas cadeias de caracteres desde que os tipos de dados blobs incluam tipos varchar (máx) e nvarchar (máx).

Se você tiver usado esses tipos no código do aplicativo para criar cadeias de caracteres grandes, será necessário dividir o código em partes e usar a instrução EXEC em vez disso.

Um exemplo simples:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Se a cadeia de caracteres for curta, você poderá usar [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) normalmente.

> [!NOTE]
> As instruções executadas como SQL dinâmico ainda estarão sujeitas a todas as regras de validação do T-SQL.

## <a name="next-steps"></a>Próximas etapas

Para obter mais dicas de desenvolvimento, confira [visão geral de desenvolvimento](sql-data-warehouse-overview-develop.md).
