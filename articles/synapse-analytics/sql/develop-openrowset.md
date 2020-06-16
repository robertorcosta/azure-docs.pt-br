---
title: Como usar OPENROWSET no SQL sob demanda (versão prévia)
description: Este artigo descreve a sintaxe de OPENROWSET no SQL sob demanda (versão prévia) e explica como usar argumentos.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 05/07/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 9c2a2d7059e24b37b0f47d0b568a3929f296d8c6
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84560865"
---
# <a name="how-to-use-openrowset-with-sql-on-demand-preview"></a>Como usar OPENROWSET com o SQL sob demanda (versão prévia)

A função `OPENROWSET(BULK...)` permite que você acesse arquivos no Armazenamento do Azure. A função `OPENROWSET` lê o conteúdo de uma fonte de dados remota (por exemplo, o arquivo) e retorna o conteúdo como um conjunto de linhas. No recurso do SQL sob demanda (versão prévia), o provedor de conjuntos de linhas em massa OPENROWSET é acessado por meio de uma chamada à função OPENROWSET e a especificação da opção BULK.  

A função `OPENROWSET` pode ser referenciada na cláusula `FROM` de uma consulta como se fosse um nome de tabela `OPENROWSET`. Ela também dá suporte a operações em massa por meio de um provedor BULK interno que permite que os dados de um arquivo sejam lidos e retornados como um conjunto de linhas.

## <a name="data-source"></a>Fonte de dados

A função OPENROWSET no SQL do Synapse lê o conteúdo dos arquivos de uma fonte de dados. A fonte de dados é uma conta de armazenamento do Azure e pode ser explicitamente referenciada na função `OPENROWSET` ou dinamicamente inferida com base na URL dos arquivos que você deseja ler.
A função `OPENROWSET` pode opcionalmente conter um parâmetro `DATA_SOURCE` para especificar a fonte de dados que contém arquivos.
- `OPENROWSET` sem `DATA_SOURCE` pode ser usado para ler diretamente o conteúdo dos arquivos do local da URL especificado como opção `BULK`:

    ```sql
    SELECT *
    FROM OPENROWSET(BULK 'http://storage..../container/folder/*.parquet',
                    TYPE = 'PARQUET') AS file
    ```

essa é uma forma rápida e fácil de ler o conteúdo dos arquivos sem pré-configuração. Essa opção permite usar a opção de autenticação básica para acessar o armazenamento (passagem do Azure AD para logons do Azure AD e token SAS para logons do SQL). 

- `OPENROWSET` com `DATA_SOURCE` pode ser usado para acessar arquivos na conta de armazenamento especificada:

    ```sql
    SELECT *
    FROM OPENROWSET(BULK '/folder/*.parquet',
                    DATA_SOURCE='storage', --> Root URL is in LOCATION of DATA SOURCE
                    TYPE = 'PARQUET') AS file
    ```


    essa opção permite que você configure o local da conta de armazenamento na fonte de dados e especifique o método de autenticação que deve ser usado para acessar o armazenamento. 
    
    > [!IMPORTANT]
    > `OPENROWSET` sem `DATA_SOURCE` oferece uma forma rápida e fácil de acessar os arquivos de armazenamento, mas oferece opções de autenticação limitadas. Por exemplo, a entidade de segurança do Azure AD pode acessar arquivos somente usando a [identidade do Azure AD](develop-storage-files-storage-access-control.md?tabs=user-identity#force-azure-ad-pass-through) dela e não pode acessar arquivos publicamente disponíveis. Se você precisar de opções de autenticação mais avançadas, use a opção `DATA_SOURCE` e defina a credencial que deseja usar para acessar o armazenamento.


## <a name="security"></a>Segurança

Um usuário de banco de dados deve ter a permissão `ADMINISTER BULK OPERATIONS` para usar a função `OPENROWSET`.

O administrador de armazenamento também deve permitir que um usuário acesse os arquivos fornecendo um token SAS válido ou habilitando a entidade de segurança do Azure AD para acessar os arquivos de armazenamento. Saiba mais sobre o controle de acesso de armazenamento [neste artigo](develop-storage-files-storage-access-control.md).

`OPENROWSET` usa as regras a seguir para determinar como se autenticar no armazenamento:
- No `OPENROWSET` sem `DATA_SOURCE`, o mecanismo de autenticação depende do tipo de chamador.
  - Os logons do Azure AD só poderão acessar arquivos usando as próprias [identidade do Azure AD](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types) se o armazenamento do Azure permitir que o usuário do Azure AD acesse arquivos subjacentes (por exemplo, se o chamador tiver permissão de Leitor de Armazenamento no armazenamento) e se você [habilitar a autenticação de passagem do Azure AD](develop-storage-files-storage-access-control.md#force-azure-ad-pass-through) no serviço do SQL do Synapse.
  - Os logons do SQL também podem usar `OPENROWSET` sem `DATA_SOURCE` para acessar arquivos publicamente disponíveis, arquivos protegidos usando o token SAS ou a Identidade Gerenciada do workspace do Synapse. Você precisaria [criar uma credencial com escopo de servidor](develop-storage-files-storage-access-control.md#examples) para permitir o acesso a arquivos de armazenamento. 
- Em `OPENROWSET` com `DATA_SOURCE`, o mecanismo de autenticação é definido na credencial no escopo do banco de dados atribuída à fonte de dados referenciada. Essa opção permite que você acesse o armazenamento disponível publicamente ou o armazenamento usando o token SAS, a Identidade Gerenciada do workspace ou a [identidade do Azure AD do chamador](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types) (se o chamador for a entidade de segurança do Azure AD). Se `DATA_SOURCE` referenciar ao armazenamento do Azure que não é público, você precisará [criar uma credencial com escopo de banco de dados](develop-storage-files-storage-access-control.md#examples) e referenciá-la no `DATA SOURCE` para permitir o acesso aos arquivos de armazenamento.

O chamador deve ter a permissão `REFERENCES` na credencial para usá-la para autenticar-se no armazenamento.

## <a name="syntax"></a>Sintaxe

```syntaxsql
--OPENROWSET syntax for reading Parquet files
OPENROWSET  
( { BULK 'unstructured_data_path' , [DATA_SOURCE = <data source name>, ]
    FORMAT='PARQUET' }  
)  
[WITH ( {'column_name' 'column_type' }) ]
[AS] table_alias(column_alias,...n)

--OPENROWSET syntax for reading delimited text files
OPENROWSET  
( { BULK 'unstructured_data_path' , [DATA_SOURCE = <data source name>, ] 
    FORMAT = 'CSV'
    [ <bulk_options> ] }  
)  
WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })  
[AS] table_alias(column_alias,...n)
 
<bulk_options> ::=  
[ , FIELDTERMINATOR = 'char' ]    
[ , ROWTERMINATOR = 'char' ] 
[ , ESCAPE_CHAR = 'char' ] 
[ , FIRSTROW = 'first_row' ]     
[ , FIELDQUOTE = 'quote_characters' ]
[ , DATA_COMPRESSION = 'data_compression_method' ]
[ , PARSER_VERSION = 'parser_version' ]
```

## <a name="arguments"></a>Argumentos

Você tem duas opções para arquivos de entrada que contêm os dados de destino para consulta. Os valores válidos são:

- 'CSV': inclui qualquer arquivo de texto delimitado com separadores de linha/coluna. Qualquer caractere pode ser usado como um separador de campo, como TSV: FIELDTERMINATOR = guia.

- 'PARQUET': arquivo binário no formato Parquet 

**'unstructured_data_path'**

O unstructured_data_path que estabelece um caminho para os dados pode ser um caminho absoluto ou relativo:
- O caminho absoluto no formato '\<prefix>://\<storage_account_path>/\<storage_path>' permite que um usuário leia os arquivos diretamente.
- Caminho relativo no formato '<caminho_de_armazenamento>' que deve ser usado com o parâmetro `DATA_SOURCE` e descreve o padrão de arquivo dentro do local <caminho_da_conta_de_armazenamento> definido no `EXTERNAL DATA SOURCE`. 

 Abaixo você encontrará os valores <storage account path> relevantes que serão vinculados à fonte de dados externa específica. 

| Fonte de dados externa       | Prefixo | Caminho da conta de armazenamento                                 |
| -------------------------- | ------ | ---------------------------------------------------- |
| Armazenamento do Blobs do Azure         | HTTPS  | \<storage_account>.blob.core.windows.net             |
| Azure Data Lake Storage Gen1 | HTTPS  | \<storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Storage Gen2 | HTTPS  | \<storage_account>.dfs.core.windows.net              |
||||

'\<storage_path>'

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
    >
    
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

Especifica o terminador de linha a ser usado. Se o terminador de linha não for especificado, um dos terminadores padrão será usado. Os terminadores padrão para PARSER_VERSION = '1.0' são \r\n, \n e \r. Os terminadores padrão para PARSER_VERSION = '2.0' são \r\n e \n.

ESCAPE_CHAR = 'char'

Especifica o caractere no arquivo que é usado para fazer escape dele mesmo e de todos os valores delimitadores no arquivo. Se o caractere de escape for seguido por um valor diferente dele mesmo ou por um dos valores delimitadores, o caractere de escape será removido durante a leitura do valor. 

O parâmetro ESCAPE_CHAR será aplicado independentemente de FIELDQUOTE estar ou não habilitado. Ele não será usado para fazer escape do caractere de aspas. O escape do caractere de aspas é feito com aspas duplas em alinhamento com o comportamento CSV do Excel.

FIRSTROW = 'first_row' 

Especifica o número da primeira linha a carregar. O padrão é 1. Indica a primeira linha no arquivo de dados especificado. Os números de linhas são determinados pela contagem dos terminadores de linha. FIRSTROW tem base 1.

FIELDQUOTE = 'field_quote' 

Especifica um caractere que será usado como o caractere de aspas no arquivo CSV. Se não for especificado, o caractere de aspas (") será usado. 

DATA_COMPRESSION = 'data_compression_method'

Especifica o método de compactação. Há suporte para o seguinte método de compactação:

- org.apache.hadoop.io.compress.GzipCodec

PARSER_VERSION = 'parser_version'

Especifica a versão do analisador a ser usada ao ler arquivos. As versões do analisador CSV com suporte no momento são 1.0 e 2.0:

- PARSER_VERSION = '1.0'
- PARSER_VERSION = '2.0'

O analisador CSV versão 1.0 é padrão e repleta de recursos, enquanto a 2.0 é criada para desempenho e não dá suporte a todas as opções e codificações. 

Especificações do analisador CSV versão 2.0:

- não há suporte para todos os tipos de dados.
- O limite de tamanho máximo de linha é de 8 MB.
- As opções a seguir não têm suporte: DATA_COMPRESSION.
- A cadeia de caracteres vazia entre aspas ("") é interpretada como cadeia de caracteres vazia.

## <a name="examples"></a>Exemplos

O exemplo a seguir retorna apenas duas colunas com números ordinais 1 e 4 dos arquivos population*.csv. Como não há nenhuma linha de cabeçalho nos arquivos, ele inicia a leitura na primeira linha:

```sql
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
SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    ) AS [r]
```

Se você estiver recebendo um erro informando que os arquivos não podem ser listados, será necessário habilitar o acesso ao armazenamento público no SQL do Synapse sob demanda:
- Se você estiver usando um logon do SQL, precisará [criar uma credencial com escopo de servidor que permita acesso ao armazenamento público](develop-storage-files-storage-access-control.md#examples).
- Se você estiver usando uma entidade de segurança do Azure AD para acessar o armazenamento público, precisará [criar uma credencial com escopo de servidor que permita o acesso ao armazenamento público](develop-storage-files-storage-access-control.md#examples) e desabilitar a [autenticação de passagem do Azure AD](develop-storage-files-storage-access-control.md#disable-forcing-azure-ad-pass-through).

## <a name="next-steps"></a>Próximas etapas

Para ver mais exemplos, confira o [início rápido para consultar armazenamento de dados](query-data-storage.md) para saber como usar o `OPENROWSET para ler formatos de arquivo [CSV](query-single-csv-file.md), [PARQUET](query-parquet-files.md) e [JSON](query-json-files.md). Você também pode aprender a salvar os resultados de sua consulta no Armazenamento do Azure usando [CETAS](develop-tables-cetas.md).
