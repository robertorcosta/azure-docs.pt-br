---
title: Usando SQL dinâmico
description: Dicas para soluções de desenvolvimento usando SQL dinâmico no pool Synapse SQL.
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
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633535"
---
# <a name="dynamic-sql-in-synapse-sql-pool"></a>SQL dinâmico no pool Synapse SQL

Incluem-se neste artigo dicas para soluções de desenvolvimento usando SQL dinâmico no pool SQL.

## <a name="dynamic-sql-example"></a>Exemplo SQL dinâmico

Ao desenvolver o código de aplicação para pool SQL, você pode precisar usar sql dinâmico para ajudar a fornecer soluções flexíveis, genéricas e modulares. O pool SQL não suporta tipos de dados blob no momento.

O não suporte a tipos de dados blob pode limitar o tamanho de suas cadeias de caracteres desde que os tipos de dados blobs incluam tipos varchar (máx) e nvarchar (máx).

Se você usou esses tipos em seu código de aplicativo para construir grandes strings, você precisa quebrar o código em pedaços e usar a declaração EXEC em vez disso.

Um exemplo simples:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Se a cadeia de caracteres for curta, você poderá usar [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) normalmente.

> [!NOTE]
> As declarações executadas como SQL dinâmico ainda estarão sujeitas a todas as regras de validação do T-SQL.

## <a name="next-steps"></a>Próximas etapas

Para obter mais dicas de desenvolvimento, confira [visão geral de desenvolvimento](sql-data-warehouse-overview-develop.md).
