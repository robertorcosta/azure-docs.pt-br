---
title: Usando loops T-SQL
description: Dicas para o desenvolvimento de soluções usando loops T-SQL e substituindo cursores no pool Synapse SQL.
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
ms.openlocfilehash: 72a39804931c0834233e91190aacffa8d35912df
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633470"
---
# <a name="using-t-sql-loops-in-synapse-sql-pool"></a>Usando loops T-SQL no pool Synapse SQL

Incluem-se neste artigo dicas para o desenvolvimento de soluções de pool SQL usando loops T-SQL e substituição de cursores.

## <a name="purpose-of-while-loops"></a>Finalidade de loops WHILE

O pool Synapse SQL suporta o loop [WHILE](/sql/t-sql/language-elements/while-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) para executar repetidamente blocos de declaração. Esse loop WHILE continuará desde que as condições especificadas sejam verdadeiras ou até que o código especificamente encerre o loop usando a palavra-chave BREAK.

A execução de loops é útil para a substituição de cursores definidos no código SQL. Felizmente, quase todos os cursores que são escritos em código SQL são do tipo somente leitura de avanço rápido. Assim, os loops WHILE são uma ótima alternativa para substituir os cursores.

## <a name="replacing-cursors-in-synapse-sql-pool"></a>Substituição de cursores na piscina Synapse SQL

No entanto, antes de mergulhar de cabeça primeiro, você deve se perguntar a seguinte pergunta: "Este cursor poderia ser reescrito para usar operações baseadas em conjunto?"

Em muitos casos, a resposta é sim e é freqüentemente a melhor abordagem. Uma operação baseada em conjunto geralmente terá um desempenho mais rápido do que uma abordagem iterativa de linha por linha.

Os cursores somente leitura de avanço rápido podem ser facilmente substituídos por um constructo de looping. O exemplo a seguir é simples. Este exemplo de código atualiza as estatísticas para cada tabela no banco de dados. Iterando sobre as tabelas no loop, cada comando é executado em sequência.

Primeiro, crie uma tabela temporária que contém um número de linha exclusivo usado para identificar as instruções individuais:

```sql
CREATE TABLE #tbl
WITH
( DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS Sequence
,       [name]
,       'UPDATE STATISTICS '+QUOTENAME([name]) AS sql_code
FROM    sys.tables
;
```

Em segundo lugar, inicialize as variáveis necessárias para executar o loop:

```sql
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Agora, execute um loop sobre as instruções para executar uma por vez:

```sql
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Finalmente, descarte a tabela temporária criada na primeira etapa

```sql
DROP TABLE #tbl;
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais dicas de desenvolvimento, confira [visão geral de desenvolvimento](sql-data-warehouse-overview-develop.md).
