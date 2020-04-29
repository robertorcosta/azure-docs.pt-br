---
title: Tabelas temporárias
description: Diretrizes essenciais para o uso de tabelas temporárias no pool do SQL Synapse, destacando os princípios das tabelas temporárias em nível de sessão.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 56d8ab81fcf9200fec2cfb4a741724b8f79db820
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81408036"
---
# <a name="temporary-tables-in-synapse-sql-pool"></a>Tabelas temporárias no pool do SQL Synapse
Este artigo contém as diretrizes essenciais de como usar as tabelas temporárias e destaca os princípios das tabelas temporárias no nível da sessão. 

O uso das informações neste artigo pode ajudá-lo a modularizar seu código, melhorando a capacidade de reutilização e a facilidade de manutenção.

## <a name="what-are-temporary-tables"></a>O que são tabelas temporárias?
As tabelas temporárias são úteis durante o processamento de dados, especialmente durante a transformação em que os resultados intermediários são transitórios. No pool SQL, existem tabelas temporárias no nível da sessão.  

As tabelas temporárias são visíveis apenas para a sessão na qual foram criadas e são descartadas automaticamente quando essa sessão faz logoff.  

As tabelas temporárias oferecem um benefício de desempenho, pois seus resultados são gravados no local, em vez do armazenamento remoto.

As tabelas temporárias são úteis durante o processamento de dados, especialmente durante a transformação em que os resultados intermediários são transitórios. Com a análise de SQL, existem tabelas temporárias no nível da sessão.  Eles são visíveis apenas para a sessão na qual foram criados. Dessa forma, eles são automaticamente descartados quando a sessão faz logoff. 

## <a name="temporary-tables-in-sql-pool"></a>Tabelas temporárias no pool do SQL

No recurso de pool do SQL, as tabelas temporárias oferecem um benefício de desempenho porque seus resultados são gravados em local, em vez de armazenamento remoto.

### <a name="create-a-temporary-table"></a>Criar uma tabela temporária

As tabelas temporárias são criadas simplesmente prefixando o nome da tabela com um `#`.  Por exemplo:

```sql
CREATE TABLE #stats_ddl
(
    [schema_name]        NVARCHAR(128) NOT NULL
,    [table_name]            NVARCHAR(128) NOT NULL
,    [stats_name]            NVARCHAR(128) NOT NULL
,    [stats_is_filtered]     BIT           NOT NULL
,    [seq_nmbr]              BIGINT        NOT NULL
,    [two_part_name]         NVARCHAR(260) NOT NULL
,    [three_part_name]       NVARCHAR(400) NOT NULL
)
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
```

As tabelas temporárias também podem ser criadas usando `CTAS` com a mesma abordagem:

```sql
CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
;
```

> [!NOTE]
> `CTAS` é um comando potente com a vantagem extra de ser muito eficiente em seu uso do espaço de log das transações. 
> 
> 

## <a name="dropping-temporary-tables"></a>Descartando tabelas temporárias
Quando uma nova sessão é criada, não deve haver nenhuma tabela temporária.  

Se você estiver chamando o mesmo procedimento armazenado, que cria um temporário com o mesmo nome, para garantir que suas `CREATE TABLE` instruções sejam bem-sucedidas, uma simples verificação de existência com um `DROP` pode ser usada como no exemplo a seguir:

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Para a consistência de codificação, é uma boa prática usar esse padrão para tabelas e tabelas temporárias.  Também é uma boa ideia usar `DROP TABLE` para remover tabelas temporárias quando você terminar com elas em seu código.  

No desenvolvimento de procedimentos armazenados, é comum ver os comandos de remoção agrupados no final de um procedimento para garantir que esses objetos sejam limpos.

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>Modularizar o código
Como as tabelas temporárias podem ser vistas em qualquer lugar em uma sessão de usuário, esse recurso pode ser utilizado para ajudá-lo a modularizar o código do aplicativo.  

Por exemplo, o procedimento armazenado a seguir gera DDL para atualizar todas as estatísticas no banco de dados pelo nome da estatística:

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
    ,@sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END

CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
GO
```

Nesse estágio, a única ação que ocorreu é a criação de um procedimento armazenado que gera uma tabela temporária, #stats_ddl, com instruções DDL.  

Esse procedimento armazenado remove uma #stats_ddl existente para garantir que ela não falhe se executada mais de uma vez em uma sessão.  

No entanto, já que não há nenhum `DROP TABLE` no final do procedimento armazenado, quando o procedimento armazenado for concluído, ele deixará a tabela criada para que possa ser lida de fora do procedimento armazenado.  

No pool do SQL, ao contrário de outros bancos de dados do SQL Server, é possível usar a tabela temporária fora do procedimento que o criou.  As tabelas temporárias do pool do SQL podem ser usadas **em qualquer lugar** dentro da sessão. Esse recurso pode levar a um código mais modular e gerenciável, como no exemplo a seguir:

```sql
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

## <a name="temporary-table-limitations"></a>Limitações da tabela temporária
O pool do SQL impõe algumas limitações ao implementar tabelas temporárias.  Atualmente, somente a sessão com o escopo das tabelas temporárias é suportada.  Não há suporte para tabelas temporárias globais.  

Além disso, as exibições não podem ser criadas em tabelas temporárias.  As tabelas temporárias só podem ser criadas com a distribuição hash ou round robin.  Não há suporte para a distribuição de tabela temporária replicada. 

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o desenvolvimento de tabelas, consulte o artigo [projetando tabelas usando os recursos de análise de SQL](sql-data-warehouse-tables-overview.md) .

