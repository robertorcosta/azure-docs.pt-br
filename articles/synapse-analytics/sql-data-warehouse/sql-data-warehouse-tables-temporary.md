---
title: Tabelas temporárias
description: Orientação essencial para o uso de tabelas temporárias no pool Synapse SQL, destacando os princípios das tabelas temporárias de nível de sessão.
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408036"
---
# <a name="temporary-tables-in-synapse-sql-pool"></a>Mesas temporárias no pool Synapse SQL
Este artigo contém as diretrizes essenciais de como usar as tabelas temporárias e destaca os princípios das tabelas temporárias no nível da sessão. 

O uso das informações deste artigo pode ajudá-lo a modular seu código, melhorando tanto a reutilização quanto a facilidade de manutenção.

## <a name="what-are-temporary-tables"></a>O que são tabelas temporárias?
Tabelas temporárias são úteis no processamento de dados, especialmente durante a transformação onde os resultados intermediários são transitórios. No pool SQL, existem tabelas temporárias no nível da sessão.  

As tabelas temporárias só são visíveis à sessão em que foram criadas e são automaticamente descartadas quando essa sessão é desaferida.  

As tabelas temporárias oferecem um benefício de desempenho, pois seus resultados são gravados no local, em vez do armazenamento remoto.

Tabelas temporárias são úteis no processamento de dados, especialmente durante a transformação onde os resultados intermediários são transitórios. Com o SQL Analytics, existem tabelas temporárias no nível da sessão.  Eles só são visíveis para a sessão em que foram criados. Como tal, eles são automaticamente descartados quando a sessão é desligada. 

## <a name="temporary-tables-in-sql-pool"></a>Tabelas temporárias no pool SQL

No recurso de pool SQL, as tabelas temporárias oferecem um benefício de desempenho porque seus resultados são escritos para armazenamento local e não remoto.

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

Se você estiver chamando o mesmo procedimento armazenado, que cria um `CREATE TABLE` temporário com o mesmo nome, `DROP` para garantir que suas declarações sejam bem sucedidas, uma simples verificação de pré-existência com um pode ser usada como no exemplo a seguir:

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Para a consistência da codificação, é uma boa prática usar este padrão tanto para tabelas quanto para mesas temporárias.  Também é uma boa idéia `DROP TABLE` usar para remover mesas temporárias quando você terminar com elas em seu código.  

No desenvolvimento do procedimento armazenado, é comum ver os comandos de queda empacotados no final de um procedimento para garantir que esses objetos sejam limpos.

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>Modularizar o código
Uma vez que tabelas temporárias podem ser vistas em qualquer lugar em uma sessão de usuário, esse recurso pode ser aproveitado para ajudá-lo a modular seu código de aplicativo.  

Por exemplo, o procedimento armazenado a seguir gera DDL para atualizar todas as estatísticas no banco de dados por nome estatístico:

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

Nesta fase, a única ação que ocorreu é a criação de um procedimento armazenado que gere uma tabela temporária, #stats_ddl, com declarações de DDL.  

Este procedimento armazenado descarta uma #stats_ddl existente para garantir que ele não falhe se for executado mais de uma vez dentro de uma sessão.  

No entanto, já que não há nenhum `DROP TABLE` no final do procedimento armazenado, quando o procedimento armazenado for concluído, ele deixará a tabela criada para que possa ser lida de fora do procedimento armazenado.  

No pool SQL, ao contrário de outros bancos de dados do SQL Server, é possível usar a tabela temporária fora do procedimento que a criou.  As mesas temporárias da piscina SQL podem ser usadas **em qualquer lugar** dentro da sessão. Esse recurso pode levar a um código mais modular e gerenciável, como no exemplo a seguir:

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
O pool SQL impõe algumas limitações ao implementar tabelas temporárias.  Atualmente, somente a sessão com o escopo das tabelas temporárias é suportada.  As Tabelas Temporárias Globais não são suportadas.  

Além disso, as visualizações não podem ser criadas em tabelas temporárias.  Tabelas temporárias só podem ser criadas com distribuição de hash ou round robin.  A distribuição temporária da tabela replicada não é suportada. 

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o desenvolvimento de tabelas, consulte as tabelas de design usando o artigo [de recursos do SQL Analytics.](sql-data-warehouse-tables-overview.md)

