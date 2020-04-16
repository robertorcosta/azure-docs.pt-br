---
title: Usando loops T-SQL
description: Dicas para usar loops T-SQL, substituir cursores e desenvolver soluções relacionadas com pool SQL no Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: baff2806b1a8c3c99546365c2496238c24b2b243
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429948"
---
# <a name="using-t-sql-loops-in-synapse-sql"></a>Usando loops T-SQL em Synapse SQL
Este artigo fornece dicas essenciais para usar loops T-SQL, substituir cursores e desenvolver soluções relacionadas com pool SQL no Synapse SQL.

## <a name="purpose-of-while-loops"></a>Finalidade de loops WHILE

O Synapse SQL suporta o loop [WHILE](https://docs.microsoft.com/sql/t-sql/language-elements/while-transact-sql?view=sql-server-ver15) para executar repetidamente blocos de declaração. Esse loop WHILE continuará desde que as condições especificadas sejam verdadeiras ou até que o código especificamente encerre o loop usando a palavra-chave BREAK. 

Loops no pool SQL são úteis para substituir cursores definidos em código SQL. Felizmente, quase todos os cursores que são escritos em código SQL são do tipo somente leitura de avanço rápido. Assim, os loops [WHILE] são uma ótima alternativa para substituir cursores.

## <a name="replacing-cursors-in-sql-pool"></a>Substituição de cursores no pool SQL

Antes de mergulhar, a seguinte pergunta deve ser considerada: "Este cursor poderia ser reescrito para usar operações baseadas em conjunto?" Em muitos casos, a resposta é sim e é freqüentemente a melhor abordagem. Uma operação baseada em conjunto geralmente executa mais rápido do que uma abordagem iterativa, linha por linha.

Os cursores somente de leitura para a frente são facilmente substituídos por uma construção de looping. O código a seguir é um exemplo simples. Este exemplo de código atualiza as estatísticas para cada tabela no banco de dados. Iterando sobre as tabelas no loop, cada comando é executado em sequência.

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

Para obter mais dicas de desenvolvimento, confira [visão geral de desenvolvimento](develop-overview.md).
