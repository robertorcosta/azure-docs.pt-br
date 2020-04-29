---
title: Usar tabelas temporárias com Synapse SQL
description: Diretrizes essenciais para o uso de tabelas temporárias no SQL Synapse.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 090f453771dba6f537ad60605c6e9b96f3ca9957
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81428752"
---
# <a name="temporary-tables-in-synapse-sql"></a>Tabelas temporárias no SQL Synapse

Este artigo contém diretrizes essenciais para o uso de tabelas temporárias e realça os princípios de tabelas temporárias em nível de sessão no Synapse SQL. 

Os recursos do pool do SQL e do SQL sob demanda (versão prévia) podem utilizar tabelas temporárias. O SQL sob demanda tem limitações que são discutidas no final deste artigo. 

## <a name="what-are-temporary-tables"></a>O que são tabelas temporárias?

As tabelas temporárias são úteis durante o processamento de dados, especialmente durante a transformação em que os resultados intermediários são transitórios. Com o Synapse SQL, as tabelas temporárias existem no nível da sessão.  Eles são visíveis apenas para a sessão na qual foram criados. Dessa forma, eles são automaticamente descartados quando a sessão faz logoff. 

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

### <a name="dropping-temporary-tables"></a>Descartando tabelas temporárias
Quando uma nova sessão é criada, não deve haver nenhuma tabela temporária.  No entanto, se você estiver chamando o mesmo procedimento armazenado que cria um temporário com o mesmo nome, para garantir `CREATE TABLE` que suas instruções sejam bem-sucedidas, use uma verificação de pré-instalação simples `DROP`com: 

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Para a consistência de codificação, é uma boa prática usar esse padrão para tabelas e tabelas temporárias.  Também é uma boa ideia usar o para `DROP TABLE` remover tabelas temporárias quando você terminar de usá-las.  

No desenvolvimento de procedimentos armazenados, é comum ver os comandos de remoção agrupados no final de um procedimento para garantir que esses objetos sejam limpos.

```sql
DROP TABLE #stats_ddl
```

### <a name="modularizing-code"></a>Modularizar o código
As tabelas temporárias podem ser usadas em qualquer lugar em uma sessão de usuário. Esse recurso pode ser explorado para ajudá-lo a modularizar o código do aplicativo.  Para demonstrar, o procedimento armazenado a seguir gera DDL para atualizar todas as estatísticas no banco de dados pelo nome da estatística:

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

Nesse estágio, a única ação que ocorreu é a criação de um procedimento armazenado que gera a #stats_ddl tabela temporária.  O procedimento armazenado descartará #stats_ddl se ele já existir. Essa queda garante que ela não falhe se executada mais de uma vez em uma sessão.  

Como não há um `DROP TABLE` no final do procedimento armazenado, quando o procedimento armazenado for concluído, a tabela criada permanecerá e poderá ser lida fora do procedimento armazenado.  

Ao contrário de outros bancos de dados SQL Server, o SQL Synapse permite que você use a tabela temporária fora do procedimento que o criou.  As tabelas temporárias criadas por meio do pool SQL podem ser usadas **em qualquer lugar** dentro da sessão. Como resultado, você terá um código mais modular e gerenciável, como demonstrado no exemplo a seguir:

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

### <a name="temporary-table-limitations"></a>Limitações da tabela temporária

O pool SQL tem algumas limitações de implementação para tabelas temporárias:

- Somente tabelas temporárias com escopo de sessão têm suporte.  Não há suporte para tabelas temporárias globais.
- As exibições não podem ser criadas em tabelas temporárias.
- As tabelas temporárias só podem ser criadas com a distribuição hash ou round robin.  Não há suporte para a distribuição de tabela temporária replicada. 

## <a name="temporary-tables-in-sql-on-demand-preview"></a>Tabelas temporárias no SQL sob demanda (versão prévia)

As tabelas temporárias no SQL sob demanda têm suporte, mas seu uso é limitado. Eles não podem ser usados em consultas que são arquivos de destino. 

Por exemplo, você não pode unir uma tabela temporária com dados de arquivos no armazenamento. O número de tabelas temporárias é limitado a 100, e seu tamanho total é limitado a 100 MB.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o desenvolvimento de tabelas, consulte o artigo [projetando tabelas usando os recursos do SQL Synapse](develop-tables-overview.md) .

