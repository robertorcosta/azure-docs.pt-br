---
title: Criando, atualizando estatísticas
description: Recomendações e exemplos para criar e atualizar estatísticas de otimização de consultas no Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 798fec4dacb33a9f16de319062baf12adaffdbd0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428739"
---
# <a name="statistics-in-synapse-sql"></a>Estatísticas em Synapse SQL

Neste artigo estão recomendações e exemplos para criar e atualizar estatísticas de otimização de consultas usando os recursos Synapse SQL: Pool SQL e SQL sob demanda (visualização).

## <a name="statistics-in-sql-pool"></a>Estatísticas no pool SQL

### <a name="why-use-statistics"></a>Por que usar estatísticas

Quanto mais o recurso de pool SQL souber sobre seus dados, mais rápido ele poderá executar consultas. Depois de carregar dados no pool SQL, coletar estatísticas sobre seus dados é uma das coisas mais importantes que você pode fazer para a otimização de consultas.  

O otimizador de consulta de pool SQL é um otimizador baseado em custos. Ele compara o custo de vários planos de consulta, e então escolhe o plano com o menor custo. Na maioria dos casos, ele escolhe o plano que executará o mais rápido.

Por exemplo, se o otimizador estimar que a data em que sua consulta está filtrando retornará uma linha, ela escolherá um plano. Se estimar que a data selecionada retornará 1 milhão de linhas, retornará um plano diferente.

### <a name="automatic-creation-of-statistics"></a>Criação automática de estatísticas

O pool SQL analisará as consultas de usuário recebidas `ON`para estatísticas ausentes quando a opção de AUTO_CREATE_STATISTICS de banco de dados estiver definida como .  Se faltam estatísticas, o otimizador de consulta cria estatísticas em colunas individuais no predicado de consulta ou condição de adesão. Esta função é usada para melhorar as estimativas de cardinalidade para o plano de consulta.

> [!IMPORTANT]
> Criação automática de estatísticas está atualmente ativada por padrão.

Você pode verificar se o seu data warehouse AUTO_CREATE_STATISTICS configurado executando o seguinte comando:

```sql
SELECT name, is_auto_create_stats_on
FROM sys.databases
```

Se o seu data warehouse não tiver AUTO_CREATE_STATISTICS ativado, recomendamos que você habilite essa propriedade executando o seguinte comando:

```sql
ALTER DATABASE <yourdatawarehousename>
SET AUTO_CREATE_STATISTICS ON
```

Essas declarações desencadearão a criação automática de estatísticas:

- SELECT
- INSERT-SELECT
- CTAS
- UPDATE
- Delete (excluir)
- EXPLIQUE quando se contém uma junta ou a presença de um predicado é detectada

> [!NOTE]
> A criação automática de estatísticas não é gerada em tabelas temporárias ou externas.

A criação automática de estatísticas é feita de forma sincronizada. Assim, você pode incorrer em um desempenho de consulta ligeiramente degradado se suas colunas estiverem faltando estatísticas. O tempo para criar estatísticas para uma única coluna depende do tamanho da tabela.

Para evitar a degradação de desempenho mensurável, você deve garantir que as estatísticas tenham sido criadas primeiro executando a carga de trabalho de referência antes de traçar o perfil do sistema.

> [!NOTE]
> A criação de estatísticas é registrada em [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) sob um contexto de usuário diferente.

Quando as estatísticas automáticas forem criadas, elas tomarão o formulário: _WA_Sys_<identificação de coluna de 8 dígitos em Hex>_<identificação de tabela de 8 dígitos em Hex>. Você pode visualizar estatísticas já criadas executando o comando [DBCC SHOW_STATISTICS:](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

```sql
DBCC SHOW_STATISTICS (<table_name>, <target>)
```

O table_name é o nome da tabela que contém as estatísticas a serem exibidas, que não podem ser uma tabela externa. O alvo é o nome do índice de destino, estatísticas ou coluna para o qual exibir informações estatísticas.

### <a name="update-statistics"></a>Atualizar estatísticas

Uma prática recomendada é atualizar as estatísticas em colunas de data por dia à medida que novas datas são adicionadas. Sempre que há um carregamento de novas linhas no data warehouse, novas datas de carga ou datas de transação são adicionadas. Essas adições alteram a distribuição de dados e tornam as estatísticas desatualizadas.

As estatísticas sobre uma coluna de país ou região em uma tabela de clientes podem nunca precisar ser atualizadas porque a distribuição dos valores geralmente não muda. Supondo que a distribuição seja constante entre os clientes, adicionar novas linhas à variação de tabela não alterará a distribuição dos dados.

No entanto, quando seu data warehouse contém apenas um país ou região e você traz dados de um novo país ou região, então você precisa atualizar estatísticas sobre a coluna do país ou região.

O seguinte são recomendações atualizando estatísticas:

|||
|-|-|
| **Frequência de atualizações de estatísticas**  | Conservadora: diária </br> Depois de carregar ou transformar os dados |
| **amostragem** |  Menos de 1 bilhão de linhas, use amostragem padrão (20%). </br> Com mais de 1 bilhão de linhas, use amostragem de 2%. |

### <a name="determine-last-statistics-update"></a>Determine a última atualização estatística

Uma das primeiras perguntas a serem feitas quando você estiver solucionando problemas em uma consulta é, **"As estatísticas estão atualizadas?"**

Esta pergunta não é aquela que pode ser respondida pela idade dos dados. Um objeto de estatísticas atualizado pode ser antigo se não houver nenhuma alteração importante nos dados subjacentes. Quando o número de linhas mudou substancialmente, ou uma mudança material na distribuição de valores para uma coluna ocorre, *então* é hora de atualizar as estatísticas.

Não há uma visão de gerenciamento dinâmica disponível para determinar se os dados dentro da tabela mudaram desde a última vez que as estatísticas foram atualizadas. Saber a idade de suas estatísticas pode lhe fornecer parte do quadro. Você pode usar a seguinte consulta para determinar a última vez que suas estatísticas foram atualizadas em cada tabela.

> [!NOTE]
> Se houver uma mudança material na distribuição de valores para uma coluna, você deve atualizar as estatísticas independentemente da última vez que foram atualizadas.

```sql
SELECT
    sm.[name] AS [schema_name],
    tb.[name] AS [table_name],
    co.[name] AS [stats_column_name],
    st.[name] AS [stats_name],
    STATS_DATE(st.[object_id],st.[stats_id]) AS [stats_last_updated_date]
FROM
    sys.objects ob
    JOIN sys.stats st
        ON  ob.[object_id] = st.[object_id]
    JOIN sys.stats_columns sc
        ON  st.[stats_id] = sc.[stats_id]
        AND st.[object_id] = sc.[object_id]
    JOIN sys.columns co
        ON  sc.[column_id] = co.[column_id]
        AND sc.[object_id] = co.[object_id]
    JOIN sys.types  ty
        ON  co.[user_type_id] = ty.[user_type_id]
    JOIN sys.tables tb
        ON  co.[object_id] = tb.[object_id]
    JOIN sys.schemas sm
        ON  tb.[schema_id] = sm.[schema_id]
WHERE
    st.[user_created] = 1;
```

**Colunas de data** em um data warehouse, por exemplo, geralmente precisam de atualizações estatísticas frequentes. Sempre que há um carregamento de novas linhas no data warehouse, novas datas de carga ou datas de transação são adicionadas. Essas adições alteram a distribuição de dados e tornam as estatísticas desatualizadas.

Estatísticas sobre uma coluna de gênero em uma tabela de clientes podem nunca precisar ser atualizadas. Supondo que a distribuição seja constante entre os clientes, adicionar novas linhas à variação de tabela não alterará a distribuição dos dados.

Mas, se o seu data warehouse contém apenas um gênero e um novo requisito resulta em vários sexos, então você precisa atualizar estatísticas na coluna de gênero. Para mais informações, revise o artigo [estatística.](/sql/relational-databases/statistics/statistics)

### <a name="implementing-statistics-management"></a>Implementação do gerenciamento de estatísticas

Muitas vezes é uma boa idéia estender seu processo de carregamento de dados para garantir que as estatísticas sejam atualizadas no final da carga. A carga de dados é quando as tabelas mudam mais frequentemente seu tamanho, distribuição de valores ou ambos. Como tal, o processo de carga é um lugar lógico para implementar alguns processos de gestão.

Os seguintes princípios orientadores são fornecidos para atualizar suas estatísticas durante o processo de carregamento:

- Certifique-se de que cada tabela carregada tenha pelo menos um objeto de estatísticas atualizado. Este processo atualiza as informações sobre o tamanho da tabela (contagem de linhas e contagem de páginas) como parte da atualização das estatísticas.
- Concentre-se em colunas que participam de cláusulas JOIN, GROUP BY, ORDER BY e DISTINCT.
- Considere atualizar colunas de "chave ascendente", como datas de transação com mais freqüência, porque esses valores não serão incluídos no histograma de estatísticas.
- Considere atualizar as colunas de distribuição estática com menos frequência.
- Lembre-se, cada objeto estatístico é atualizado em sequência. Simplesmente implementar `UPDATE STATISTICS <TABLE_NAME>` nem sempre é ideal, especialmente para tabelas amplas com muitos objetos de estatística.

Para obter mais informações, consulte [Estimativa de cardinalidade](/sql/relational-databases/performance/cardinality-estimation-sql-server).

### <a name="examples-create-statistics"></a>Exemplos: criar estatísticas

Estes exemplos mostram como usar várias opções para a criação de estatísticas. As opções usadas para cada coluna dependem das características dos dados e de como a coluna será usada em consultas.

#### <a name="create-single-column-statistics-with-default-options"></a>Criar estatísticas de coluna única com opções padrão

Para criar estatísticas em uma coluna, forneça um nome para o objeto estatístico e o nome da coluna.
Esta sintaxe usa todas as opções padrão. Por padrão, o pool SQL amostra **20%** da tabela quando cria estatísticas.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Por exemplo:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Criar estatísticas de coluna única examinando cada linha

A taxa de amostragem padrão de 20 por cento é suficiente para a maioria das situações. No entanto, você pode ajustar essa taxa de amostragem. Para usar toda a tabela como amostragem, use a seguinte sintaxe:

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Por exemplo:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Criar estatísticas de coluna única, especificando o tamanho da amostra

Outra opção que você tem é especificar o tamanho da amostra como um porcento:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

#### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Criar estatísticas de coluna única em apenas algumas das linhas

Você também pode criar estatísticas em uma parte das linhas em sua tabela, que é chamada de estatística filtrada.

Por exemplo, é possível usar estatísticas filtradas quando você planeja consultar uma partição específica de uma tabela particionada grande. Ao criar estatísticas apenas sobre os valores de partição, a precisão das estatísticas melhorará. Você também experimentará uma melhoria no desempenho da consulta.

Este exemplo cria estatísticas em um intervalo de valores. Os valores podem ser facilmente definidos para corresponder ao intervalo de valores em uma partição.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> Para que o otimizador de consulta considere usar estatísticas filtradas ao escolher o plano de consulta distribuída, a consulta deve ser adequada à definição do objeto de estatísticas. Usando o exemplo anterior, a cláusula WHERE da consulta precisa especificar valores col1 entre 2000101 e 20001231.

#### <a name="create-single-column-statistics-with-all-the-options"></a>Criar estatísticas de coluna única com todas as opções

Também é possível combinar as opções juntas. O exemplo a seguir cria um objeto estatístico filtrado com um tamanho de amostra personalizado:

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

Para obter a referência completa, consulte [CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

#### <a name="create-multi-column-statistics"></a>Criar estatísticas de várias colunas

Para criar um objeto de estatística de várias colunas, use os exemplos anteriores, mas especifique mais colunas.

> [!NOTE]
> O histograma, que é usado para estimar o número de linhas no resultado da consulta, está disponível apenas para a primeira coluna listada na definição do objeto estatístico.

Neste exemplo, o histograma está em *product\_category*. As estatísticas entre colunas são calculadas sobre *a categoria do produto\_* e sub_category do *produto:\_*

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Como existe uma correlação entre *a categoria do produto\_* e a *subcategoria\_\_do produto,* um objeto estatístico de várias colunas pode ser útil se essas colunas forem acessadas ao mesmo tempo.

#### <a name="create-statistics-on-all-columns-in-a-table"></a>Criar estatísticas em todas as coluna em uma tabela

É uma maneira de criar estatísticas é emitir comandos CREATE STATISTICS depois de criar a tabela:

```sql
CREATE TABLE dbo.table1
(
   col1 int
,  col2 int
,  col3 int
)
WITH
  (
    CLUSTERED COLUMNSTORE INDEX
  )
;

CREATE STATISTICS stats_col1 on dbo.table1 (col1);
CREATE STATISTICS stats_col2 on dbo.table2 (col2);
CREATE STATISTICS stats_col3 on dbo.table3 (col3);
```

#### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>Use um procedimento armazenado para criar estatísticas em todas as colunas em um banco de dados

O pool SQL não tem um procedimento armazenado no sistema equivalente a sp_create_stats no SQL Server. Esse procedimento armazenado cria um objeto de estatísticas de coluna única em todas as colunas do banco de dados que ainda não tenham estatísticas.
O exemplo a seguir ajudará você a começar o projeto do banco de dados. Fique à vontade para adaptá-lo às suas necessidades:

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default 2 Fullscan 3 Sample
,   @sample_pct     tinyint
)
AS

IF @create_type IS NULL
BEGIN
    SET @create_type = 1;
END;

IF @create_type NOT IN (1,2,3)
BEGIN
    THROW 151000,'Invalid value for @stats_type parameter. Valid range 1 (default), 2 (fullscan) or 3 (sample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN;
    DROP TABLE #stats_ddl;
END;

CREATE TABLE #stats_ddl
WITH    (   DISTRIBUTION    = HASH([seq_nmbr])
        ,   LOCATION        = USER_DB
        )
AS
WITH T
AS
(
SELECT      t.[name]                        AS [table_name]
,           s.[name]                        AS [table_schema_name]
,           c.[name]                        AS [column_name]
,           c.[column_id]                   AS [column_id]
,           t.[object_id]                   AS [object_id]
,           ROW_NUMBER()
            OVER(ORDER BY (SELECT NULL))    AS [seq_nmbr]
FROM        sys.[tables] t
JOIN        sys.[schemas] s         ON  t.[schema_id]       = s.[schema_id]
JOIN        sys.[columns] c         ON  t.[object_id]       = c.[object_id]
LEFT JOIN   sys.[stats_columns] l   ON  l.[object_id]       = c.[object_id]
                                    AND l.[column_id]       = c.[column_id]
                                    AND l.[stats_column_id] = 1
LEFT JOIN    sys.[external_tables] e    ON    e.[object_id]        = t.[object_id]
WHERE       l.[object_id] IS NULL
AND            e.[object_id] IS NULL -- not an external table
)
SELECT  [table_schema_name]
,       [table_name]
,       [column_name]
,       [column_id]
,       [object_id]
,       [seq_nmbr]
,       CASE @create_type
        WHEN 1
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+')' AS VARCHAR(8000))
        WHEN 2
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH FULLSCAN' AS VARCHAR(8000))
        WHEN 3
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+CONVERT(varchar(4),@sample_pct)+' PERCENT' AS VARCHAR(8000))
        END AS create_stat_ddl
FROM T
;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''
;

WHILE @i <= @t
BEGIN
    SET @s=(SELECT create_stat_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

Para criar estatísticas em todas as colunas da tabela usando os padrões, execute o procedimento armazenado.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 1, NULL;
```

Para criar estatísticas em todas as colunas da tabela usando uma verificação completa, chame este procedimento:

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```

Para criar estatísticas de amostra em todas as colunas da tabela, insira 3 e o percentual da amostra. O procedimento abaixo usa uma taxa amostral de 20%.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```

### <a name="examples-update-statistics"></a>Exemplos: atualizar as estatísticas

Para atualizar as estatísticas, você pode:

- Atualizar um objeto de estatísticas. Especifique o nome do objeto de estatísticas que você deseja atualizar.
- Atualizar todos os objetos de estatísticas em uma tabela. Especifique o nome da tabela em vez de um objeto de estatísticas específico.

#### <a name="update-one-specific-statistics-object"></a>Atualizar um objeto de estatísticas específico

Use a sintaxe a seguir para atualizar um objeto de estatísticas específico:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Por exemplo:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Ao atualizar objetos de estatísticas específicos, você pode minimizar o tempo e os recursos necessários para o gerenciamento de estatísticas. Esta ação requer algum pensamento para selecionar os melhores objetos estatísticos para atualizar.

#### <a name="update-all-statistics-on-a-table"></a>Atualizar todas as estatísticas de uma tabela

Um método simples para atualizar todos os objetos estatísticos em uma tabela é:

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Por exemplo:

```sql
UPDATE STATISTICS dbo.table1;
```

A declaração UPDATE STATISTICS é fácil de usar. Basta lembrar que ele atualiza *todas as* estatísticas sobre a tabela, solicitando mais trabalho do que o necessário. Se o desempenho não é um problema, este método é a maneira mais fácil e completa de garantir que as estatísticas estejam atualizadas.

> [!NOTE]
> Ao atualizar todas as estatísticas em uma tabela, o pool SQL faz uma varredura para mostrar a tabela para cada objeto estatístico. Se a tabela for grande e tiver muitas colunas e muitas estatísticas, talvez seja mais eficiente atualizar estatísticas individuais com base na necessidade.

Para a implementação de um `UPDATE STATISTICS` procedimento, consulte [Tabelas temporárias](develop-tables-temporary.md). O método de implementação é ligeiramente diferente do procedimento anterior `CREATE STATISTICS`, mas o resultado é o mesmo.
Para obter a sintaxe completa, consulte [Atualizar estatísticas](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="statistics-metadata"></a>Metadados de estatísticas

Há várias exibições e funções do sistema que podem ser utilizadas para localizar informações sobre estatísticas. Por exemplo, você pode ver se um objeto estatístico pode estar desatualizado usando a função STATS_DATE() STATS_DATE() permite ver quando as estatísticas foram criadas ou atualizadas pela última vez.

#### <a name="catalog-views-for-statistics"></a>Exibições de catálogo para as estatísticas

Essas exibições do sistema fornecem informações sobre estatísticas:

| Exibição de catálogo | Descrição |
|:--- |:--- |
| [sys.columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Uma linha para cada coluna. |
| [sys.objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Uma linha para cada objeto no banco de dados. |
| [sys.schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Uma linha para cada esquema no banco de dados. |
| [sys.stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Uma linha para cada objeto de estatísticas. |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Uma linha para cada coluna no objeto de estatísticas. Conecta novamente a sys.columns. |
| [sys.tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Uma linha para cada tabela (inclui tabelas externas). |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Uma linha para cada tipo de dados. |

#### <a name="system-functions-for-statistics"></a>Funções de sistema para estatísticas

Essas funções de sistema são úteis para trabalhar com estatísticas:

| Função do sistema | Descrição |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Data da última atualização do objeto de estatísticas. |
| [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |Nível de resumo e informações detalhadas sobre a distribuição de valores conforme entendido pelo objeto de estatísticas. |

#### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Combinar colunas de estatísticas e funções em uma exibição

Essa exibição une as colunas relacionadas às estatísticas e os resultados da função STATS_DATE() em conjunto.

```sql
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_definition]
,       st.[has_filter]                     AS [stats_is_filtered]
,       STATS_DATE(st.[object_id],st.[stats_id])
                                            AS [stats_last_updated_date]
,       co.[name]                           AS [stats_column_name]
,       ty.[name]                           AS [column_type]
,       co.[max_length]                     AS [column_max_length]
,       co.[precision]                      AS [column_precision]
,       co.[scale]                          AS [column_scale]
,       co.[is_nullable]                    AS [column_is_nullable]
,       co.[collation_name]                 AS [column_collation_name]
,       QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS two_part_name
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS three_part_name
FROM    sys.objects                         AS ob
JOIN    sys.stats           AS st ON    ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc ON    st.[stats_id]       = sc.[stats_id]
                            AND         st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co ON    sc.[column_id]      = co.[column_id]
                            AND         sc.[object_id]      = co.[object_id]
JOIN    sys.types           AS ty ON    co.[user_type_id]   = ty.[user_type_id]
JOIN    sys.tables          AS tb ON  co.[object_id]        = tb.[object_id]
JOIN    sys.schemas         AS sm ON  tb.[schema_id]        = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

### <a name="dbcc-show_statistics-examples"></a>Exemplos de DBCC SHOW_STATISTICS()

DBCC SHOW_STATISTICS() mostra os dados contidos em um objeto de estatísticas. Esses dados estão divididos em três partes:

- Cabeçalho
- Vetor de densidade
- Histograma

O cabeçalho é o metadados sobre as estatísticas. O histograma exibe a distribuição de valores na primeira coluna de chave do objeto de estatísticas. O vetor de densidade mede a correlação entre colunas. O pool SQL calcula as estimativas de cardinalidade com qualquer um dos dados no objeto estatístico.

#### <a name="show-header-density-and-histogram"></a>Mostrar cabeçalho, densidade e histograma

Este exemplo simples mostra as três partes de um objeto de estatísticas:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Por exemplo:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

#### <a name="show-one-or-more-parts-of-dbcc-show_statistics"></a>Mostrar uma ou mais partes de DBCC SHOW_STATISTICS()

Se você estiver interessado apenas em visualizar partes específicas, use a cláusula `WITH` e especifique quais partes deseja ver:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Por exemplo:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

### <a name="dbcc-show_statistics-differences"></a>Diferenças do DBCC SHOW_STATISTICS()

O DBCC SHOW_STATISTICS() é mais rigorosamente implementado no pool SQL em comparação com o SQL Server:

- Recursos não documentados não são suportados.
- Não posso usar Stats_stream.
- Não é possível juntar resultados para subconjuntos específicos de dados estatísticos. Por exemplo, STAT_HEADER DENSITY_VECTOR JOIN.
- NO_INFOMSGS não pode ser configurado para supressão de mensagens.
- Colchetes quadrados em torno de nomes estatísticos não podem ser usados.
- Não pode usar nomes de colunas para identificar objetos estatísticos.
- O erro personalizado 2767 não é suportado.

### <a name="next-steps"></a>Próximas etapas

Para melhorar ainda mais o desempenho da consulta, veja [Monitorar sua carga de trabalho](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="statistics-in-sql-on-demand-preview"></a>Estatísticas em SQL sob demanda (visualização)

As estatísticas são criadas por coluna específica para conjunto de dados específico (caminho de armazenamento).

### <a name="why-use-statistics"></a>Por que usar estatísticas

Quanto mais SQL sob demanda (visualização) souber sobre seus dados, mais rápido ele poderá executar consultas contra eles. Coletar estatísticas sobre seus dados é uma das coisas mais importantes que você pode fazer para otimizar suas consultas. O otimizador de consulta sob demanda SQL é um otimizador baseado em custos. Ele compara o custo de vários planos de consulta, e então escolhe o plano com o menor custo. Na maioria dos casos, ele escolhe o plano que executará o mais rápido. Por exemplo, se o otimizador estimar que a data em que sua consulta está filtrando retornará uma linha, ela escolherá um plano. Se estimar que a data selecionada retornará 1 milhão de linhas, retornará um plano diferente.

### <a name="automatic-creation-of-statistics"></a>Criação automática de estatísticas

O SQL sob demanda analisa as consultas de usuário recebidas para estatísticas ausentes. Se faltam estatísticas, o otimizador de consulta cria estatísticas em colunas individuais no predicado de consulta ou condição de adesão para melhorar as estimativas de cardinalidade para o plano de consulta.

A declaração SELECT desencadeará a criação automática de estatísticas.

> [!NOTE]
> A criação automática de estatísticas é ligada para arquivos do Parquet. Para arquivos CSV, você precisa criar estatísticas manualmente até que a criação automática de estatísticas de arquivos CSV seja suportada.

A criação automática de estatísticas é feita de forma sincronizada para que você possa incorrer em um desempenho de consulta ligeiramente degradado se suas colunas estiverem faltando estatísticas. O tempo para criar estatísticas para uma única coluna depende do tamanho dos arquivos direcionados.

### <a name="manual-creation-of-statistics"></a>Criação manual de estatísticas

O SQL sob demanda permite que você crie estatísticas manualmente. Para arquivos CSV, você tem que criar estatísticas manualmente porque a criação automática de estatísticas não é ligada para arquivos CSV. Veja os exemplos abaixo para obter instruções sobre como criar estatísticas manualmente.

### <a name="updating-statistics"></a>Atualização de estatísticas

Alterações nos dados em arquivos, exclusão e adição de arquivos resultam em alterações na distribuição de dados e tornam as estatísticas desatualizadas. Nesse caso, as estatísticas precisam ser atualizadas.

O SQL sob demanda recria automaticamente as estatísticas se os dados forem alterados significativamente. Toda vez que as estatísticas são criadas automaticamente, o estado atual do conjunto de dados também é salvo: caminhos de arquivo, tamanhos, datas de última modificação.

Quando as estatísticas estiverem obsoletas, novas serão criadas. O algoritmo passa pelos dados e os compara ao estado atual do conjunto de dados. Se o tamanho das alterações for maior do que o limite específico, então as estatísticas antigas serão excluídas e serão recriadas sobre o novo conjunto de dados.

Estatísticas manuais nunca são declaradas obsoletas.

> [!NOTE]
> A recriação automática das estatísticas é ligada para arquivos do Parquet. Para arquivos CSV, você precisa soltar e criar estatísticas manualmente até que a criação automática de estatísticas de arquivos CSV seja suportada. Confira os exemplos abaixo sobre como soltar e criar estatísticas.

Uma das primeiras perguntas a serem feitas quando você estiver solucionando problemas em uma consulta é, **"As estatísticas estão atualizadas?"**

Quando o número de linhas mudou substancialmente, ou há uma mudança material na distribuição de valores para uma coluna, *então* é hora de atualizar as estatísticas.

> [!NOTE]
> Se houver uma mudança material na distribuição de valores para uma coluna, você deve atualizar as estatísticas independentemente da última vez que foram atualizadas.

### <a name="implementing-statistics-management"></a>Implementação do gerenciamento de estatísticas

Você pode querer estender seu pipeline de dados para garantir que as estatísticas sejam atualizadas quando os dados são significativamente alterados através de adição, exclusão ou alteração de arquivos.

Os seguintes princípios orientadores são fornecidos para atualizar suas estatísticas:

- Certifique-se de que o conjunto de dados tenha pelo menos um objeto estatístico atualizado. Essas atualizações de tamanho (contagem de linhas e contagem de páginas) como parte da atualização das estatísticas.
- Concentre-se em colunas que participam de cláusulas JOIN, GROUP BY, ORDER BY e DISTINCT.
- Atualize colunas de "chave ascendente", como datas de transação com mais freqüência, porque esses valores não serão incluídos no histograma de estatísticas.
- Atualize colunas de distribuição estática com menos freqüência.

Para obter mais informações, consulte [Estimativa de cardinalidade](/sql/relational-databases/performance/cardinality-estimation-sql-server).

### <a name="examples-create-statistics-for-column-in-openrowset-path"></a>Exemplos: Crie estatísticas para coluna no caminho OPENROWSET

Os exemplos a seguir mostram como usar várias opções para criar estatísticas. As opções usadas para cada coluna dependem das características dos dados e de como a coluna será usada em consultas.

> [!NOTE]
> Você pode criar estatísticas de uma única coluna apenas neste momento.

O procedimento armazenado a seguir é usado para criar estatísticas:

```sql
sys.sp_create_file_statistics [ @stmt = ] N'statement_text'
```

Argumentos: @stmt [ = ] N'statement_text' - Especifica uma declaração Transact-SQL que devolverá os valores das colunas a serem usados para estatísticas. Você pode usar TABLESAMPLE para especificar amostras de dados a serem usados. Se a TABLESAMPLE não for especificada, o FULLSCAN será usado.

```sql
<tablesample_clause> ::= TABLESAMPLE ( sample_number PERCENT )
```

> [!NOTE]
> A amostragem csv não funciona neste momento, apenas fullscan é suportado para CSV.

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Criar estatísticas de coluna única examinando cada linha

Para criar estatísticas em uma coluna, forneça uma consulta que retorne a coluna para a qual você precisa de estatísticas.

Por padrão, se você não especificar o contrário, o SQL sob demanda usa 100% dos dados fornecidos no conjunto de dados quando ele cria estatísticas.

Por exemplo, para criar estatísticas com opções padrão (FULLSCAN) para uma coluna de um ano do conjunto de dados com base no arquivo population.csv:

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_file_statistics N'SELECT year
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv'',
        FORMAT = ''CSV'',
        FIELDTERMINATOR ='','',
        ROWTERMINATOR = ''\n''
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
'
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Criar estatísticas de coluna única, especificando o tamanho da amostra

Você pode especificar o tamanho da amostra como um porcento:

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_file_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
```

### <a name="examples-update-statistics"></a>Exemplos: atualizar as estatísticas

Para atualizar as estatísticas, você precisa cair e criar estatísticas. O seguinte procedimento armazenado é usado para soltar estatísticas:

```sql
sys.sp_drop_file_statistics [ @stmt = ] N'statement_text'
```

Argumentos: @stmt [ = ] N'statement_text' - Especifica a mesma declaração Transact-SQL usada quando as estatísticas foram criadas.

Para atualizar as estatísticas da coluna do ano no conjunto de dados, que é baseado no arquivo population.csv, você precisa soltar e criar estatísticas:

```sql
EXEC sys.sp_drop_file_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
GO

/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_file_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
```

### <a name="examples-create-statistics-for-external-table-column"></a>Exemplos: Criar estatísticas para coluna de tabela externa

Os exemplos a seguir mostram como usar várias opções para criar estatísticas. As opções usadas para cada coluna dependem das características dos dados e de como a coluna será usada em consultas.

> [!NOTE]
> Você pode criar estatísticas de uma única coluna apenas neste momento.

Para criar estatísticas em uma coluna, forneça um nome para o objeto estatístico e o nome da coluna.

```sql
CREATE STATISTICS statistics_name
ON { external_table } ( column )
    WITH
        { FULLSCAN
          | [ SAMPLE number PERCENT ] }
        , { NORECOMPUTE }
```

Argumentos: external_table Especifica tabela externa que as estatísticas devem ser criadas.

Estatísticas do FULLSCAN Compute digitalizando todas as linhas. FULLSCAN e SAMPLE 100 PERCENT têm os mesmos resultados. FULLSCAN não pode ser usado com a opção SAMPLE.

PERCENTUAL DO NÚMERO DA AMOSTRA Especifica a porcentagem aproximada ou o número de linhas na tabela ou exibição indexada para o otimizador de consulta a ser usado quando ele cria estatísticas. O número pode ser de 0 a 100.

SAMPLE não pode ser usado com a opção FULLSCAN.

> [!NOTE]
> A amostragem csv não funciona neste momento, apenas fullscan é suportado para CSV.

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Criar estatísticas de coluna única examinando cada linha

```sql
CREATE STATISTICS sState on census_external_table (STATENAME) WITH FULLSCAN, NORECOMPUTE
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Criar estatísticas de coluna única, especificando o tamanho da amostra

```sql
-- following sample creates statistics with sampling 20%
CREATE STATISTICS sState on census_external_table (STATENAME) WITH SAMPLE 5 percent, NORECOMPUTE
```

### <a name="examples-update-statistics"></a>Exemplos: atualizar as estatísticas

Para atualizar as estatísticas, você precisa cair e criar estatísticas. Estatísticas de queda primeiro:

```sql
DROP STATISTICS census_external_table.sState
```

E criar estatísticas:

```sql
CREATE STATISTICS sState on census_external_table (STATENAME) WITH FULLSCAN, NORECOMPUTE
```

## <a name="next-steps"></a>Próximas etapas

Para obter melhorias adicionais no desempenho da consulta, consulte [As melhores práticas para pool SQL](best-practices-sql-pool.md#maintain-statistics).
