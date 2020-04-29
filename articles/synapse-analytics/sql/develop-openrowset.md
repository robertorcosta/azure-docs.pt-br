---
title: Como usar OPENROWSET no SQL sob demanda (versão prévia)
description: Este artigo descreve a sintaxe de OPENROWSET no SQL sob demanda (versão prévia) e explica como usar argumentos.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 6325d5555b01373b148dce69731ec64896d6e1fd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680502"
---
# <a name="how-to-use-openrowset-with-sql-on-demand-preview"></a>Como usar OPENROWSET com o SQL sob demanda (versão prévia)

A função OPENROWSET(BULK...) permite que você acesse arquivos no Armazenamento do Azure. No recurso do SQL sob demanda (versão prévia), o provedor de conjuntos de linhas em massa OPENROWSET é acessado por meio de uma chamada à função OPENROWSET e a especificação da opção BULK.  

A função OPENROWSET pode ser referenciada na cláusula FROM de uma consulta como se fosse um nome de tabela OPENROWSET. Ela também dá suporte a operações em massa por meio de um provedor BULK interno que permite que os dados de um arquivo sejam lidos e retornados como um conjunto de linhas.

Atualmente, não há suporte para OPENROWSET no pool de SQL.

## <a name="syntax"></a>Sintaxe

```syntaxsql
--OPENROWSET syntax for reading Parquet files
OPENROWSET  
( { BULK 'unstructured_data_path' , 
    FORMAT='PARQUET' }  
)  
[WITH ( {'column_name' 'column_type' }) ]
[AS] table_alias(column_alias,...n)

--OPENROWSET syntax for reading delimited text files
OPENROWSET  
( { BULK 'unstructured_data_path' , 
    FORMAT = 'CSV'
    [ <bulk_options> ] }  
)  
WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })  
[AS] table_alias(column_alias,...n)
 
<bulk_options> ::=  
[ , FIELDTERMINATOR = 'char' ]    
[ , ROWTERMINATOR = 'char' ] 
[ , ESCAPE_CHAR = 'char' ] 
[ , FIRSTROW = 'first_row'  ]     
[ , FIELDQUOTE = 'quote_characters']
```

## <a name="arguments"></a>Argumentos

Você tem duas opções para arquivos de entrada que contêm os dados de destino para consulta. Os valores válidos são:

- 'CSV': inclui qualquer arquivo de texto delimitado com separadores de linha/coluna. Qualquer caractere pode ser usado como um separador de campo, como TSV: FIELDTERMINATOR = guia.

- 'PARQUET': arquivo binário no formato Parquet 

**'unstructured_data_path'**

O unstructured_data_path que estabelece um caminho para os dados é estruturado da seguinte maneira:  
'\<prefixo>://\<caminho_da_conta_de_armazenamento>/\<caminho_de_armazenamento>'
 
 
 Abaixo você encontrará os caminhos da conta de armazenamento relevantes que serão vinculados à fonte de dados externa específica. 

| Fonte de dados externa       | Prefixo | Caminho da conta de armazenamento                                 |
| -------------------------- | ------ | ---------------------------------------------------- |
| Armazenamento do Blobs do Azure         | HTTPS  | \<conta_de_armazenamento>.blob.core.windows.net             |
| Azure Data Lake Storage Gen1 | HTTPS  | \<conta_de_armazenamento>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Storage Gen2 | HTTPS  | \<conta_de_armazenamento>.dfs.core.windows.net              |
||||

'\<caminho_de_armazenamento>'

 Especifica um caminho no armazenamento que aponta para a pasta ou o arquivo que você deseja ler. Se o caminho apontar para um contêiner ou uma pasta, todos os arquivos serão lidos nessa pasta ou nesse contêiner específico. Os arquivos nas subpastas não serão incluídos. 
 
 Use curingas para direcionar vários arquivos ou várias pastas. O uso de vários curingas não consecutivos é permitido.
Veja abaixo um exemplo que lê todos os arquivos *csv* que começam com *population* de todas as pastas que começam com */csv/population*:  
`https://sqlondemandstorage.blob.core.windows.net/csv/population*/population*.csv`

Se você especificar o unstructured_data_path para ser uma pasta, uma consulta do SQL sob demanda recuperará os arquivos dessa pasta. 

> [!NOTE]
> Ao contrário do Hadoop e do PolyBase, o SQL sob demanda não retorna subpastas. Além disso, ao contrário do Hadoop e do PolyBase, o SQL sob demanda retorna arquivos para os quais o nome do arquivo começa com um sublinhado (_) ou um ponto final (.).

No exemplo abaixo, se o unstructured_data_path=`https://mystorageaccount.dfs.core.windows.net/webdata/`, uma consulta SQL sob demanda retornará linhas de mydata.txt e _hidden.txt. Ela não retornará mydata2.txt e mydata3.txt, porque eles estão localizados em uma subpasta.

![Dados recursivos para tabelas externas](./media/develop-openrowset/folder-traversal.png)

`[WITH ( {'column_name' 'column_type' [ 'column_ordinal'] }) ]`

A cláusula WITH permite que você especifique as colunas que deseja ler dos arquivos.

- Em arquivos de dados CSV, para ler todas as colunas, forneça nomes de colunas e os respectivos tipos de dados. Se você quiser um subconjunto de colunas, use números ordinais para escolher as colunas dos arquivos de dados de origem por ordinal. As colunas serão vinculadas à designação ordinal. 

> [!IMPORTANT]
> A cláusula WITH é obrigatória para arquivos CSV.
- Em arquivos de dados Parquet, forneça nomes de colunas que correspondam aos nomes de colunas nos arquivos de dados de origem. As colunas serão vinculadas ao nome. Se a cláusula WITH for omitida, todas as colunas de arquivos Parquet serão retornadas.

column_name = nome da coluna de saída. Se for fornecido, esse nome substituirá o nome da coluna no arquivo de origem.

column_type = tipo de dados da coluna de saída. A conversão de tipo de dados implícita ocorrerá aqui.

column_ordinal = número ordinal da coluna nos arquivos de origem. Esse argumento é ignorado em arquivos Parquet, pois a associação é feita por nome. O seguinte exemplo retornará uma segunda coluna somente de um arquivo CSV:

```sql
WITH (
    --[country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2 2
    --[year] smallint,
    --[population] bigint
)
```

**\<bulk_options>**

FIELDTERMINATOR ='field_terminator'

Especifica o terminador de campo a ser usado. O terminador de campo padrão é uma vírgula (" **,** ").

ROWTERMINATOR ='row_terminator'`

Especifica o terminador de linha a ser usado. O terminador de linha padrão é um caractere de nova linha como \r\n.

ESCAPE_CHAR = 'char'

Especifica o caractere no arquivo que é usado para fazer escape dele mesmo e de todos os valores delimitadores no arquivo. Se o caractere de escape for seguido por um valor diferente dele mesmo ou por um dos valores delimitadores, o caractere de escape será removido durante a leitura do valor. 

O parâmetro ESCAPE_CHAR será aplicado independentemente de FIELDQUOTE estar ou não habilitado. Ele não será usado para fazer escape do caractere de aspas. O escape do caractere de aspas é feito com aspas duplas em alinhamento com o comportamento CSV do Excel.

FIRSTROW = 'first_row' 

Especifica o número da primeira linha a carregar. O padrão é 1. Indica a primeira linha no arquivo de dados especificado. Os números de linhas são determinados pela contagem dos terminadores de linha. FIRSTROW tem base 1.

FIELDQUOTE = 'field_quote' 

Especifica um caractere que será usado como o caractere de aspas no arquivo CSV. Se não for especificado, o caractere de aspas (") será usado. 

## <a name="examples"></a>Exemplos

O exemplo a seguir retorna apenas duas colunas com números ordinais 1 e 4 dos arquivos population*.csv. Como não há nenhuma linha de cabeçalho nos arquivos, ele inicia a leitura na primeira linha:

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

SELECT * 
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 1
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2 1,
    [population] bigint 4
) AS [r]
```



O seguinte exemplo retorna todas as colunas da primeira linha do conjunto de dados de censo no formato Parquet sem especificar nomes de colunas nem tipos de dados: 

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

SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    ) AS [r]
```



## <a name="next-steps"></a>Próximas etapas

Para obter mais amostras, acesse os [guias de início rápido](query-data-storage.md) ou salve os resultados da consulta no Armazenamento do Azure usando a instrução [CETAS](develop-tables-cetas.md).
