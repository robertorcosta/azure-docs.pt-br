---
title: Usando SQL dinâmico
description: Dicas para usar SQL dinâmico no SQL Data Warehouse do Azure para desenvolvimento de soluções.
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
ms.openlocfilehash: a44bec72029a50c2ef348bcdda497803e35f586d
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350548"
---
# <a name="dynamic-sql-in-sql-data-warehouse"></a>SQL dinâmico no SQL Data Warehouse
Dicas para usar SQL dinâmico no SQL Data Warehouse do Azure para desenvolvimento de soluções.

## <a name="dynamic-sql-example"></a>Exemplo SQL dinâmico

Ao desenvolver o código do aplicativo para o SQL Data Warehouse, talvez seja preciso usar um sql dinâmico para ajudar a fornecer soluções flexíveis, genéricas e modulares. No momento, o SQL Data Warehouse não dá suporte a tipos de dados de blob. O não suporte a tipos de dados blob pode limitar o tamanho de suas cadeias de caracteres desde que os tipos de dados blobs incluam tipos varchar (máx) e nvarchar (máx). Se você usou estes tipos no código do seu aplicativo ao compilar cadeias de caracteres muito grandes, você precisará dividir o código em partes e usar a instrução EXEC em seu lugar.

Um exemplo simples:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Se a cadeia de caracteres for curta, você poderá usar [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql) normalmente.

> [!NOTE]
> Instruções executadas como SQL dinâmico ainda estarão sujeitas a todas as regras de validação de TSQL.
> 
> 

## <a name="next-steps"></a>Próximas etapas
Para obter mais dicas de desenvolvimento, confira [visão geral de desenvolvimento](sql-data-warehouse-overview-develop.md).

