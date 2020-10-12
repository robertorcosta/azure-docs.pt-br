---
title: Usar loops T-SQL
description: Dicas para usar loops T-SQL, substituir cursores e desenvolver soluções relacionadas com o pool SQL no Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 33e1ebc2269ef1db6bb0646f845b09be1a01c724
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91289048"
---
# <a name="use-t-sql-loops-in-synapse-sql"></a>Usar loops T-SQL no Synapse SQL
Este artigo fornece dicas essenciais para o uso de loops T-SQL, a substituição de cursores e o desenvolvimento de soluções relacionadas com o pool do SQL no Synapse SQL.

## <a name="purpose-of-while-loops"></a>Finalidade de loops WHILE

Synapse SQL dá suporte ao loop [while](https://docs.microsoft.com/sql/t-sql/language-elements/while-transact-sql?view=sql-server-ver15&preserve-view=true) para executar repetidamente blocos de instrução. Esse loop WHILE continuará desde que as condições especificadas sejam verdadeiras ou até que o código especificamente encerre o loop usando a palavra-chave BREAK. 

Os loops no pool do SQL são úteis para substituir cursores definidos no código SQL. Felizmente, quase todos os cursores que são escritos em código SQL são do tipo somente leitura de avanço rápido. Portanto, os loops WHILE são uma ótima alternativa para substituir cursores.

## <a name="replace-cursors-in-sql-pool"></a>Substituir cursores no pool SQL

Antes de mergulharmos no, a seguinte pergunta deve ser considerada: "o cursor poderia ser reescrito para usar operações baseadas em conjunto?" Em muitos casos, a resposta é sim e geralmente é a melhor abordagem. Uma operação baseada em conjunto geralmente é executada mais rapidamente do que uma abordagem iterativa, linha por linha.

Os cursores somente leitura de avanço rápido são facilmente substituídos por uma construção de looping. O código a seguir é um exemplo simples. Este exemplo de código atualiza as estatísticas para cada tabela no banco de dados. Iterando sobre as tabelas no loop, cada comando é executado em sequência.

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
