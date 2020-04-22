---
title: CETAS no Synapse SQL
description: Usando CETAS com Synapse SQL
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 71bc20680467d270436e28190bb49db5b9313ca0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420040"
---
# <a name="cetas-with-synapse-sql"></a>CETAS com Synapse SQL

Em um pool de SQL ou SQL sob demanda (versão prévia), você pode usar CETAS (CREATE EXTERNAL TABLE AS SELECT) para realizar as seguintes tarefas:  

- Criar uma tabela externa
- Exportar, em paralelo, os resultados de uma instrução SELECT do Transact-SQL para

  - O Hadoop
  - Blob de Armazenamento do Azure
  - Azure Data Lake Storage Gen2

## <a name="cetas-in-sql-pool"></a>CETAS no pool de SQL

Para o uso e a sintaxe do CETAS no pool de SQL, veja o artigo [CREATE EXTERNAL TABLE AS SELECT](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Além disso, para obter diretrizes sobre o CTAS usando o pool de SQL, confira o artigo [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="cetas-in-sql-on-demand"></a>CETAS no SQL sob demanda

No recurso SQL sob demanda, o CETAS é usado para criar uma tabela externa e exportar os resultados da consulta para o Azure Storage Blob ou o Azure Data Lake Storage Gen2.

## <a name="syntax"></a>Sintaxe

```syntaxsql
CREATE EXTERNAL TABLE [ [database_name  . [ schema_name ] . ] | schema_name . ] table_name
    WITH (
        LOCATION = 'path_to_folder',  
        DATA_SOURCE = external_data_source_name,  
        FILE_FORMAT = external_file_format_name  
)
    AS <select_statement>  
[;]

<select_statement> ::=  
    [ WITH <common_table_expression> [ ,...n ] ]  
    SELECT <select_criteria>
```

## <a name="arguments"></a>Argumentos

*[ [ *database_name* . [ *schema_name* ] . ] | *schema_name* . ] *table_name**

O nome de uma a três partes da tabela a ser criada. Para uma tabela externa, o SQL sob demanda armazena apenas os metadados da tabela. Nenhum dado real é movido ou armazenado no SQL sob demanda.

LOCATION = *'path_to_folder'*

Especifica o local em que gravar os resultados da instrução SELECT na fonte de dados externa. A pasta raiz é o local de dados especificado na fonte de dados externa. LOCATION deve apontar para uma pasta e ter uma / à direita. Exemplo: aggregated_data/

DATA_SOURCE = *external_data_source_name*

Especifica o nome do objeto de fonte de dados externa que contém a localização em que os dados externos serão armazenados. Para criar uma fonte de dados externa, use [CREATE EXTERNAL DATA SOURCE (Transact-SQL)](develop-tables-external-tables.md#create-external-data-source).

FILE_FORMAT = *external_file_format_name*

Especifica o nome do objeto de formato de arquivo externo que contém o formato do arquivo de dados externo. Para criar um formato de arquivo externo, use [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](develop-tables-external-tables.md#create-external-file-format). Somente formatos de arquivo externo com FORMAT='PARQUET' são compatíveis no momento.

WITH *<common_table_expression>*

Especifica um conjunto de resultados nomeado temporário, conhecido como uma CTE (expressão de tabela comum). Para obter mais informações, confira [WITH common_table_expression (Transact-SQL)](/sql/t-sql/queries/with-common-table-expression-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

SELECT <select_criteria>

Popula a nova tabela com os resultados de uma instrução SELECT. *select_criteria* é o corpo da instrução SELECT que determina quais dados serão copiados para a nova tabela. Para obter informações sobre as instruções SELECT, confira [SELECT (Transact-SQL)](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="permissions"></a>Permissões

Você precisa de permissões para listar o conteúdo da pasta e gravar na pasta LOCATION para que o CETAS funcione.

## <a name="examples"></a>Exemplos

Esses exemplos usam CETAS para salvar a população total agregada por ano e estado em uma pasta aggregated_data que está localizada na fonte de dados population_ds.

Este exemplo conta com a credencial, a fonte de dados e o formato de arquivo externo criados anteriormente. Veja o documento [tabelas externas](develop-tables-external-tables.md). Para salvar os resultados da consulta em uma pasta diferente na mesma fonte de dados, altere o argumento LOCATION. Para salvar os resultados em uma conta de armazenamento diferente, crie e use uma fonte de dados diferente para o argumento DATA_SOURCE.

> [!NOTE]
> Os exemplos a seguir usam uma conta pública de armazenamento de Open Data do Azure. Ela é somente leitura. Para executar essas consultas, você precisa fornecer a fonte de dados para a qual você tem permissões de gravação.

```sql
-- use CETAS to export select statement with OPENROWSET result to  storage
CREATE EXTERNAL TABLE population_by_year_state
WITH (
    LOCATION = 'aggregated_data/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)  
AS
SELECT decennialTime, stateName, SUM(population) AS population
FROM
    OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=*/*.parquet',
    FORMAT='PARQUET') AS [r]
GROUP BY decennialTime, stateName
GO

-- you can query created external table
SELECT * FROM population_by_year_state
```

O exemplo a seguir usa uma tabela externa como a fonte para o CETAS. Ele conta com a credencial, a fonte de dados, o formato de arquivo externo e a tabela externa criados anteriormente. Veja o documento [tabelas externas](develop-tables-external-tables.md).

```sql
-- use CETAS with select from external table
CREATE EXTERNAL TABLE population_by_year_state
WITH (
    LOCATION = 'aggregated_data/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)  
AS
SELECT decennialTime, stateName, SUM(population) AS population
FROM census_external_table
GROUP BY decennialTime, stateName
GO

-- you can query created external table
SELECT * FROM population_by_year_state
```

## <a name="supported-data-types"></a>Tipos de dados com suporte

O CETAS pode ser usado para armazenar conjuntos de resultados com os seguintes tipos de dados SQL:

- binary
- varbinary
- char
- varchar
- date
- time
- datetime2
- decimal
- numeric
- FLOAT
- real
- BIGINT
- INT
- SMALLINT
- TINYINT
- bit

LOBs não podem ser usados com o CETAS.

Os seguintes tipos de dados não podem ser usados na parte SELECT do CETAS:

- NCHAR
- NVARCHAR
- DATETIME
- smalldatetime
- datetimeoffset
- money
- SMALLMONEY
- UNIQUEIDENTIFIER

## <a name="next-steps"></a>Próximas etapas

Você pode tentar consultar [tabelas do Spark](develop-storage-files-spark-tables.md).
