---
title: Usar tabelas externas com Synapse SQL
description: Lendo ou gravando arquivos de dados com o Synapse SQL
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 4d13d15fe950c89687acfca355d4ed183756536a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419970"
---
# <a name="use-external-tables-with-synapse-sql"></a>Usar tabelas externas com Synapse SQL

Uma tabela externa aponta para dados localizados no Hadoop, no Azure Storage Blob ou no Azure Data Lake Storage. As tabelas externas são usadas para ler dados de arquivos ou gravar dados em arquivos no Armazenamento do Azure. Com o Synapse SQL, você pode usar tabelas externas para ler e gravar dados no pool de SQL ou no SQL sob demanda (versão prévia).

## <a name="external-tables-in-sql-pool"></a>Tabelas externas no pool de SQL

No pool de SQL, você pode usar uma tabela externa para:

- Consultar o Armazenamento de Blobs do Azure e o Azure Data Lake Gen2 com instruções Transact-SQL.
- Importar e armazenar dados do Armazenamento de Blobs do Azure e do Azure Data Lake Storage no pool de SQL.

Quando usada em conjunto com a instrução [CREATE TABLE AS SELECT](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), a seleção em uma tabela externa importa dados para uma tabela no pool de SQL. Além da [instrução COPY](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), as tabelas externas são úteis para carregar dados. Para ver um tutorial de carregamento, confira [Usar o PolyBase para carregar dados do Armazenamento de Blobs do Azure](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="external-tables-in-sql-on-demand-preview"></a>Tabelas externas no SQL sob demanda (versão prévia)

Para o SQL sob demanda, você usará uma tabela externa para:

- Consultar dados no Armazenamento de Blobs do Azure ou no Azure Data Lake Storage com instruções Transact-SQL
- Armazene resultados da consulta SQL sob demanda em arquivos no Armazenamento de Blobs do Azure ou no Azure Data Lake Storage usando [CETAS](develop-tables-cetas.md).

Você pode criar tabelas externas usando o SQL sob demanda por meio das seguintes etapas:

1. CREATE EXTERNAL DATA SOURCE
2. CREATE EXTERNAL FILE FORMAT
3. CREATE EXTERNAL TABLE

## <a name="create-external-data-source"></a>CREATE EXTERNAL DATA SOURCE

Fontes de dados externas são usadas para se conectar a contas de armazenamento. A documentação completa está delineada [aqui](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="syntax-for-create-external-data-source"></a>Sintaxe de CREATE EXTERNAL DATA SOURCE

```syntaxsql
CREATE EXTERNAL DATA SOURCE <data_source_name>
WITH
(    LOCATION         = '<prefix>://<path>' )
[;]
```

## <a name="arguments-for-create-external-data-source"></a>Argumentos de CREATE EXTERNAL DATA SOURCE

data_source_name – Especifica o nome da fonte de dados definido pelo usuário. O nome deve ser exclusivo no banco de dados.

LOCATION = `'<prefix>://<path>'` – Fornece o protocolo de conectividade e o caminho para a fonte de dados externa. O caminho pode incluir um contêiner na forma `'<prefix>://<path>/container'` e uma pasta na forma `'<prefix>://<path>/container/folder'`.

| Fonte de dados externa        | Prefixo de local | Caminho de local                                         |
| --------------------------- | --------------- | ----------------------------------------------------- |
| Armazenamento do Blobs do Azure          | `wasb[s]`       | `<container>@<storage_account>.blob.core.windows.net` |
| Azure Data Lake Storage Gen 1 | `adl`           | `<storage_account>.azuredatalake.net`                 |
| Azure Data Lake Storage Gen 2 | `abfs[s]`       | `<container>@<storage_account>.dfs.core.windows.net`  |

## <a name="example-for-create-external-data-source"></a>Exemplo de CREATE EXTERNAL DATA SOURCE

O seguinte exemplo cria uma fonte de dados externa para o Azure Data Lake Gen2 apontando para o conjunto de dados de Nova York:

```sql
CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
WITH
  -- Please note the abfss endpoint when your account has secure transfer enabled
  ( LOCATION = 'abfss://newyorktaxidataset.azuredatalakestore.net' ,
    CREDENTIAL = ADLS_credential ,
    TYPE = HADOOP
  ) ;
```

## <a name="create-external-file-format"></a>CREATE EXTERNAL FILE FORMAT

Cria um objeto com formato de arquivo externo que define dados externos armazenados no Armazenamento de Blobs do Azure ou no Azure Data Lake Storage. Criar um formato de arquivo externo é um pré-requisito para criar uma tabela externa. A documentação completa está [aqui](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

Ao criar um formato de arquivo externo, você especificará o layout real dos dados referenciados por uma tabela externa.

## <a name="syntax-for-create-external-file-format"></a>Sintaxe de CREATE EXTERNAL FILE FORMAT

```syntaxsql
-- Create an external file format for PARQUET files.  
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = PARQUET  
    [ , DATA_COMPRESSION = {  
        'org.apache.hadoop.io.compress.SnappyCodec'  
      | 'org.apache.hadoop.io.compress.GzipCodec'      }  
    ]);  

--Create an external file format for DELIMITED TEXT files
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = DELIMITEDTEXT  
    [ , FORMAT_OPTIONS ( <format_options> [ ,...n  ] ) ]  
    );  

<format_options> ::=  
{  
    FIELD_TERMINATOR = field_terminator  
    | STRING_DELIMITER = string_delimiter
    | First_Row = integer
    | USE_TYPE_DEFAULT = { TRUE | FALSE }
    | Encoding = {'UTF8' | 'UTF16'}
}
```

## <a name="arguments-for-create-external-file-format"></a>Argumentos de CREATE EXTERNAL FILE FORMAT

file_format_name – Especifica um nome para o formato de arquivo externo.

FORMAT_TYPE = [ PARQUET | DELIMITEDTEXT] – Especifica o formato dos dados externos.

- PARQUET – Especifica um formato Parquet.
- DELIMITEDTEXT – Especifica um formato de texto com delimitadores de coluna, também chamado de terminadores de campo.

FIELD_TERMINATOR = *field_terminator* – Aplica-se somente a arquivos de texto delimitados. O terminador de campo especifica um ou mais caracteres que marcam o final de cada campo (coluna) no arquivo de texto delimitado. O padrão é o caractere barra vertical ('|').

Exemplos:

- FIELD_TERMINATOR = '|'
- FIELD_TERMINATOR = ' '
- FIELD_TERMINATOR = ꞌ\tꞌ

STRING_DELIMITER = *string_delimiter* – Especifica o terminador de campo dos dados da cadeia de caracteres de tipo no arquivo de texto delimitado. O delimitador de cadeia de caracteres tem um ou mais caracteres de comprimento e está entre aspas simples. O padrão é a cadeia de caracteres vazia ("").

Exemplos:

- STRING_DELIMITER = '"'
- STRING_DELIMITER = '*'
- STRING_DELIMITER = ꞌ,ꞌ

FIRST_ROW = *First_row_int* – Especifica o número da linha que é lido primeiro e se aplica a todos os arquivos. A definição do valor como dois faz com que a primeira linha em todos os arquivos (linha de cabeçalho) seja ignorada quando os dados forem carregados. As linhas são ignoradas com base na existência de terminadores de linhas (/r/n, /r, /n).

USE_TYPE_DEFAULT = { TRUE | **FALSE** } – Especifica como tratar valores ausentes em arquivos de texto delimitados ao recuperar dados do arquivo de texto.

TRUE – Se estiver recuperando dados do arquivo de texto, armazene cada valor ausente, usando o tipo de dados do valor padrão da coluna correspondente na definição da tabela externa. Por exemplo, substitua um valor ausente por:

- 0 se a coluna for definida como uma coluna numérica. Colunas decimais não são compatíveis e causarão um erro.
- Cadeia de caracteres vazia ("") se a coluna for uma coluna de cadeia de caracteres.
- 1900-01-01 se a coluna for uma coluna de data.

FALSE – Armazene todos os valores ausentes como NULL. Todos os valores NULL que estão armazenados usando a palavra NULL no arquivo de texto delimitado são importados como a cadeia de caracteres 'NULL'.

Encoding = {'UTF8' | 'UTF16'} – O SQL sob demanda pode ler arquivos de texto delimitados de codificação UTF8 e UTF16.

DATA_COMPRESSION = *data_compression_method* – Esse argumento especifica o método de compactação de dados para os dados externos. Ao ler em tabelas externas, ele é ignorado. Ele é usado apenas ao gravar em tabelas externas usando [CETAS](develop-tables-cetas.md).

O tipo de formato de arquivo PARQUET é compatível com este método de compactação:

- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'

## <a name="example-for-create-external-file-format"></a>Exemplo de CREATE EXTERNAL FILE FORMAT

O seguinte exemplo cria um formato de arquivo externo para arquivos de censo:

```sql
CREATE EXTERNAL FILE FORMAT census_file_format
WITH
(  
    FORMAT_TYPE = PARQUET,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
)
```

## <a name="create-external-table"></a>CREATE EXTERNAL TABLE

O comando CREATE EXTERNAL TABLE cria uma tabela externa para o Synapse SQL acessar os dados armazenados no Armazenamento de Blobs do Azure ou no Azure Data Lake Storage. 

## <a name="syntax-for-create-external-table"></a>Sintaxe de CREATE EXTERNAL TABLE

```syntaxsql
CREATE EXTERNAL TABLE { database_name.schema_name.table_name | schema_name.table_name | table_name }
    ( <column_definition> [ ,...n ] )  
    WITH (
        LOCATION = 'folder_or_filepath',  
        DATA_SOURCE = external_data_source_name,  
        FILE_FORMAT = external_file_format_name
    )  
[;]  

<column_definition> ::=
column_name <data_type>
    [ COLLATE collation_name ]
```

## <a name="arguments-create-external-table"></a>Argumentos de CREATE EXTERNAL TABLE

*{ database_name.schema_name.table_name | schema_name.table_name | table_name }*

O nome de uma a três partes da tabela a ser criada. Para uma tabela externa, o SQL sob demanda armazena apenas os metadados da tabela. Nenhum dado real é movido ou armazenado no SQL sob demanda.

<column_definition>, ...*n* ]

O CREATE EXTERNAL TABLE é compatível com a configuração de nome de coluna, tipo de dados, nulidade e ordenação. Não é possível usar a DEFAULT CONSTRAINT em tabelas externas.

>[!IMPORTANT]
>As definições de coluna, incluindo os tipos de dados e o número de colunas, devem corresponder aos dados nos arquivos externos. Se houver uma incompatibilidade, as linhas do arquivo serão rejeitadas ao consultar os dados propriamente ditos.

Ao ler em arquivos Parquet, você pode especificar apenas as colunas que deseja ler e ignorar o restante.

LOCATION = '*folder_or_filepath*'

Especifica a pasta ou o caminho do arquivo e o nome de arquivo para os dados reais no Armazenamento de Blobs do Azure. O local inicia da pasta raiz. A pasta raiz é o local de dados especificado na fonte de dados externa.

Se você especificar uma LOCALIZAÇÃO de pasta, uma consulta SQL sob demanda será selecionada na tabela externa e recuperará os arquivos da pasta.

> [!NOTE]
> Ao contrário do Hadoop e do PolyBase, o SQL sob demanda não retorna subpastas. Ele retorna arquivos cujos nomes de arquivo começam com um sublinhado (_) ou um ponto final (.).

Neste exemplo, se 'LOCATION='/webdata/', uma consulta SQL sob demanda retornará linhas de mydata.txt e _hidden.txt. Ele não retornará mydata2.txt e mydata3.txt porque eles estão localizados em uma subpasta.

![Dados recursivos para tabelas externas](./media/develop-tables-external-tables/folder-traversal.png)

DATA_SOURCE = *external_data_source_name* – Especifica o nome da fonte de dados externa que contém a localização dos dados externos. Para criar uma fonte de dados externa, use [CREATE EXTERNAL DATA SOURCE](#create-external-data-source).

FILE_FORMAT = *external_file_format_name* – Especifica o nome do objeto com formato de arquivo externo que armazena o tipo de arquivo e o método de compactação dos dados externos. Para criar um formato de arquivo externo, use [CREATE EXTERNAL FILE FORMAT](#create-external-file-format).

## <a name="permissions-create-external-table"></a>Permissões CREATE EXTERNAL TABLE

Para selecionar em uma tabela externa, você precisa de credenciais adequadas com permissões de lista e leitura.

## <a name="example-create-external-table"></a>Exemplo de CREATE EXTERNAL TABLE

O exemplo a seguir cria uma tabela externa. Ele retorna a primeira linha:

```sql
CREATE EXTERNAL TABLE census_external_table
(
    decennialTime varchar(20),
    stateName varchar(100),
    countyName varchar(100),
    population int,
    race varchar(50),
    sex    varchar(10),
    minAge int,
    maxAge int
)  
WITH (
    LOCATION = '/parquet/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)
GO

SELECT TOP 1 * FROM census_external_table
```

## <a name="create-and-query-external-tables-from-a-file-in-azure-data-lake"></a>Criar e consultar tabelas externas de um arquivo no Azure Data Lake

Usando os recursos de exploração do Data Lake, agora você pode criar e consultar uma tabela externa usando o pool de SQL ou o SQL sob demanda com um simples clique com o botão direito do mouse no arquivo.

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter acesso ao workspace com, pelo menos, a função de acesso de Colaborador de Dados do Blob de Armazenamento do ARM na conta do ADLS Gen2

- Você deve ter pelo menos [permissões para criar](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#permissions-2) e consultar tabelas externas no pool de SQL ou no SQL OD (SQL sob demanda)

- O serviço vinculado associado à conta do ADLS Gen2 **deve ter acesso ao arquivo**. Por exemplo, se o mecanismo de autenticação do serviço vinculado for uma Identidade Gerenciada, a identidade gerenciada do workspace deverá ter, pelo menos, a permissão Leitor de blob de armazenamento na conta de armazenamento

No painel Dados, selecione o arquivo do qual você gostaria de criar a tabela externa:
> [!div class="mx-imgBorder"]
>![externaltable1](./media/develop-tables-external-tables/external-table-1.png)

Uma janela de diálogo será aberta. Selecione pool de SQL ou SQL sob demanda, dê um nome à tabela e selecione abrir script:

> [!div class="mx-imgBorder"]
>![externaltable2](./media/develop-tables-external-tables/external-table-2.png)

O Script do SQL é gerado automaticamente inferindo o esquema do arquivo:
> [!div class="mx-imgBorder"]
>![externaltable3](./media/develop-tables-external-tables/external-table-3.png)

Execute o script. O script executará automaticamente Select Top 100 *:
> [!div class="mx-imgBorder"]
>![externaltable4](./media/develop-tables-external-tables/external-table-4.png)

Agora a tabela externa está criada; para exploração futura do conteúdo dessa tabela externa, o usuário poderá consultá-la diretamente no painel Dados:
> [!div class="mx-imgBorder"]
>![externaltable5](./media/develop-tables-external-tables/external-table-5.png)

## <a name="next-steps"></a>Próximas etapas

Confira o artigo [CETAS](develop-tables-cetas.md) para saber como salvar os resultados da consulta em uma tabela externa no Armazenamento do Azure. Ou você pode começar a consultar [Tabelas Spark](develop-storage-files-spark-tables.md).
