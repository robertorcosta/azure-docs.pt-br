---
title: Usando loops T-SQL
description: Dicas para usar os loops do T-SQL e para substituir cursores no SQL Data Warehouse do Azure para desenvolver soluções.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: b57358e32bda83ef51fe67aa1057411d51773fa6
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685833"
---
# <a name="using-t-sql-loops-in-sql-data-warehouse"></a>Usando loops T-SQL no SQL Data Warehouse
Dicas para usar os loops do T-SQL e para substituir cursores no SQL Data Warehouse do Azure para desenvolver soluções.

## <a name="purpose-of-while-loops"></a>Finalidade de loops WHILE

O SQL Data Warehouse oferece suporte a loop [WHILE](/sql/t-sql/language-elements/while-transact-sql) para executar repetidamente blocos de instrução. Esse loop WHILE continuará desde que as condições especificadas sejam verdadeiras ou até que o código especificamente encerre o loop usando a palavra-chave BREAK. A execução de loops é útil para a substituição de cursores definidos no código SQL. Felizmente, quase todos os cursores que são escritos em código SQL são do tipo somente leitura de avanço rápido. Portanto, loops [WHILE] são uma excelente alternativa para a substituição de cursores.

## <a name="replacing-cursors-in-sql-data-warehouse"></a>Substituindo cursores no SQL Data Warehouse
No entanto, antes de mergulhar na memória, primeiro você deve fazer a seguinte pergunta: “Esse cursor poderia ser reescrito para usar operações baseadas em conjunto?” Em muitos casos, a resposta é “Sim” e geralmente esta é a melhor abordagem. Uma operação baseada em conjunto geralmente terá um desempenho mais rápido do que uma abordagem iterativa de linha por linha.

Os cursores somente leitura de avanço rápido podem ser facilmente substituídos por um constructo de looping. A seguir há um exemplo simples. Este exemplo de código atualiza as estatísticas para cada tabela no banco de dados. Iterando sobre as tabelas no loop, cada comando é executado em sequência.

Primeiro, crie uma tabela temporária que contém um número de linha exclusivo usado para identificar as instruções individuais:

```
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

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Agora, execute um loop sobre as instruções para executar uma por vez:

```
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Finalmente, descarte a tabela temporária criada na primeira etapa

```
DROP TABLE #tbl;
```

## <a name="next-steps"></a>Próximas etapas
Para obter mais dicas de desenvolvimento, consulte [Visão geral de desenvolvimento](sql-data-warehouse-overview-develop.md).

