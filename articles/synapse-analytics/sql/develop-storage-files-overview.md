---
title: Consultar arquivos de armazenamento usando o SQL sob demanda (versão prévia) no Synapse SQL
description: Descreve a consulta de arquivos de armazenamento usando recursos de SQL sob demanda (versão prévia) no Synapse SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/19/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: bfea79fe232fbb6f1b39c03a5cc8e9fe06bee867
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85204932"
---
# <a name="query-storage-files-using-sql-on-demand-preview-resources-within-synapse-sql"></a>Consultar arquivos de armazenamento usando recursos de SQL sob demanda (versão prévia) no Synapse SQL

O SQL sob demanda (versão prévia) permite que você consulte dados em seu data lake. Ele oferece uma área de superfície de consulta T-SQL que acomoda consultas de dados semiestruturados e não estruturados.

Para consulta, os seguintes aspectos do T-SQL são compatíveis:

- Área de superfície de [SELECT](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) completa, incluindo a maioria das funções e operadores SQL e assim por diante.
- [CETAS](develop-tables-cetas.md) (CREATE EXTERNAL TABLE AS SELECT) cria uma [tabela externa](develop-tables-external-tables.md) e, em seguida, exporta em paralelo os resultados de uma instrução SELECT de Transact-SQL para o Armazenamento do Azure.

Para obter mais informações sobre o que é e o que não é compatível atualmente, leia o artigo [Visão geral do SQL sob demanda](on-demand-workspace-overview.md).

Quando os usuários do Azure AD executam consultas, o padrão é que as contas de armazenamento sejam acessadas usando o protocolo de autenticação de passagem do Azure AD. Assim, os usuários serão representados e as permissões verificadas no nível do armazenamento. Você pode [controlar o acesso ao armazenamento](develop-storage-files-storage-access-control.md) para atender às suas necessidades.

## <a name="extensions"></a>Extensões

Para dar suporte a uma experiência tranquila para a consulta de dados localizados em arquivos do Armazenamento do Azure, o SQL sob demanda usa a função [OPENROWSET](develop-openrowset.md) com recursos adicionais:

- [Consultar vários arquivos ou pastas](#query-multiple-files-or-folders)
- [Formato de arquivo PARQUET](#parquet-file-format)
- [Opções adicionais para trabalhar com texto delimitado (terminador de campo, terminador de linha, caractere de escape)](#additional-options-for-working-with-delimited-text)
- [Ler um subconjunto escolhido de colunas](#read-a-chosen-subset-of-columns)
- [Inferência de esquema](#schema-inference)
- [Função filename](#filename-function)
- [Função filepath](#filepath-function)
- [Trabalhar com tipos complexos e estruturas de dados aninhadas ou repetidas](#work-with-complex-types-and-nested-or-repeated-data-structures)

### <a name="query-multiple-files-or-folders"></a>Consultar vários arquivos ou pastas

Para executar uma consulta T-SQL em um conjunto de arquivos dentro de uma pasta ou conjunto de pastas e tratá-los como uma entidade ou um conjunto de linhas, forneça um caminho para uma pasta ou um padrão (usando curingas) em um conjunto de arquivos ou pastas.

As seguintes regras se aplicam:

- Os padrões podem aparecer em parte de um caminho de diretório ou em um nome de arquivo.
- Vários padrões podem aparecer na mesma etapa de diretório ou nome de arquivo.
- Se houver vários curingas, os arquivos em todos os caminhos correspondentes serão incluídos no conjunto de arquivos resultante.

```
N'https://myaccount.blob.core.windows.net/myroot/*/mysubfolder/*.csv'
```

Veja [Consultar pastas e vários arquivos](query-folders-multiple-csv-files.md) para obter exemplos de uso.

### <a name="parquet-file-format"></a>Formato de arquivo PARQUET

Para consultar dados de origem Parquet, use FORMAT = 'PARQUET'

```syntaxsql
OPENROWSET
(
    { BULK 'data_file' ,
    { FORMATFILE = 'format_file_path' [ <bulk_options>] } }
)
AS table_alias(column_alias,...n)
<bulk_options> ::=
...
[ , FORMAT = {'CSV' | 'PARQUET'} ]
```

Examine o artigo [Consultar arquivos Parquet](query-parquet-files.md) para obter exemplos de uso.

### <a name="additional-options-for-working-with-delimited-text"></a>Opções adicionais para trabalhar com texto delimitado

Esses parâmetros adicionais são introduzidos para trabalhar com arquivos CSV (texto delimitado):

```syntaxsql
<bulk_options> ::=
...
[ , FIELDTERMINATOR = 'char' ]
[ , ROWTERMINATOR = 'char' ]
[ , ESCAPE_CHAR = 'char' ]
...
```

- ESCAPE_CHAR = 'char' especifica o caractere que é usado no arquivo para escapar a si mesmo e todos os valores de delimitador no arquivo. Se o caractere de escape for seguido por um valor diferente dele mesmo ou de qualquer um dos valores delimitadores, o caractere de escape será descartado durante a leitura do valor.
O parâmetro ESCAPE_CHAR será aplicado quer FIELDQUOTE esteja habilitado ou não. Ele não será usado para fazer escape do caractere de aspas. O caractere de aspas deve ter escape com outro caractere de aspas. O caractere de aspas poderá aparecer no valor da coluna somente se o valor for encapsulado com caracteres de aspas.
- FIELDTERMINATOR ='field_terminator' especifica o terminador de campo a ser usado. O terminador de campo padrão é uma vírgula (" **,** ")
- ROWTERMINATOR ='row_terminator' especifica o terminador de linha a ser usado. O terminador de linha padrão um caractere de nova linha: **\r\n**.

### <a name="read-a-chosen-subset-of-columns"></a>Ler um subconjunto escolhido de colunas

Para especificar as colunas que você deseja ler, você pode fornecer uma cláusula WITH opcional dentro da instrução OPENROWSET.

- Se houver arquivos de dados CSV para ler todas as colunas, forneça nomes de coluna e seus tipos de dados. Se você quiser um subconjunto de colunas, use números ordinais para escolher as colunas dos arquivos de dados de origem por ordinal. As colunas serão vinculadas à designação ordinal.
- Se houver arquivos de dados Parquet, forneça nomes de coluna que correspondam aos nomes de coluna nos arquivos de dados de origem. As colunas serão vinculadas ao nome.

```syntaxsql
OPENROWSET
...
| BULK 'data_file',
{ FORMATFILE = 'format_file_path' [ <bulk_options>] } }
) AS table_alias(column_alias,...n) | WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })
```

Para obter exemplos, veja [Ler arquivos CSV sem especificar todas as colunas](query-single-csv-file.md#returning-subset-of-columns).

### <a name="schema-inference"></a>Inferência de esquema

Ao omitir a cláusula WITH da instrução OPENROWSET, você pode instruir o serviço a detectar automaticamente (inferir) o esquema dos arquivos subjacentes.

> [!NOTE]
> Atualmente, isso funciona apenas para o formato de arquivo PARQUET.

```sql
OPENROWSET(
BULK N'path_to_file(s)', FORMAT='PARQUET');
```

Verifique se os [tipos de dados inferidos apropriados](best-practices-sql-on-demand.md#check-inferred-data-types) são usados para um desempenho ideal. 

### <a name="filename-function"></a>Função filename

Essa função retorna o nome do arquivo de origem da linha. 

Para consultar arquivos específicos, leia a seção Filename no artigo [Consultar arquivos específicos](query-specific-files.md#filename).

O tipo de dados retornado é nvarchar (1024). Para obter um desempenho ideal, sempre converta o resultado da função filename para o tipo de dados apropriado. Se você usar o tipo de dados de caractere, use o comprimento apropriado.

### <a name="filepath-function"></a>Função filepath

Essa função retorna um caminho completo ou uma parte do caminho:

- Quando chamada sem parâmetro, retorna o caminho de arquivo completo da origem de uma linha.
- Quando chamada com parâmetro, ela retorna parte do caminho que corresponda ao curinga na posição especificada no parâmetro. Por exemplo, o valor de parâmetro 1 retornaria a parte do caminho que corresponde ao primeiro caractere curinga.

Para obter informações adicionais, leia a seção Filepath do artigo [Consultar arquivos específicos](query-specific-files.md#filepath).

O tipo de dados retornado é nvarchar (1024). Para obter um desempenho ideal, sempre converta o resultado da função filepath para o tipo de dados apropriado. Se você usar o tipo de dados de caractere, use o comprimento apropriado.

### <a name="work-with-complex-types-and-nested-or-repeated-data-structures"></a>Trabalhar com tipos complexos e estruturas de dados aninhadas ou repetidas

Para permitir uma experiência tranquila ao trabalhar com os dados armazenados em tipos de dados aninhados ou repetidos, como em arquivos [Parquet](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#nested-types), o SQL sob demanda adicionou as extensões abaixo.

#### <a name="project-nested-or-repeated-data"></a>Dados de projeto aninhados ou repetidos

Para projetar dados, execute uma instrução SELECT no arquivo Parquet que contém colunas de tipos de dados aninhados. Na saída, os valores aninhados serão serializados em JSON e retornados como um tipo de dados SQL varchar (8000).

```sql
    SELECT * FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

Para obter informações mais detalhadas, veja a seção Dados de projeto aninhados ou repetidos do artigo [Consulta de tipos aninhados de Parquet](query-parquet-nested-types.md#project-nested-or-repeated-data).

#### <a name="access-elements-from-nested-columns"></a>Elementos de acesso de colunas aninhadas

Para acessar elementos aninhados de uma coluna aninhada, como Struct, use "notação de ponto" para concatenar nomes de campo no caminho. Forneça o caminho como column_name na cláusula WITH da função OPENROWSET.

O exemplo de fragmento da sintaxe fica como segue:

```syntaxsql
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    WITH ({'column_name' 'column_type',})
    [AS alias]
    'column_name' ::= '[field_name.] field_name'
```

Por padrão, a função OPENROWSET corresponde ao nome e ao caminho do campo de origem com os nomes de coluna fornecidos na cláusula WITH. Os elementos contidos em diferentes níveis de aninhamento dentro do mesmo arquivo Parquet de origem podem ser acessados por meio da cláusula WITH.

**Valores retornados**

- A função retorna um valor escalar como int, decimal e varchar, do elemento especificado e no caminho especificado, para todos os tipos de Parquet que não estão no grupo Tipo Aninhado.
- Se o caminho aponta para um elemento que é de um Tipo Aninhado, a função retorna um fragmento JSON começando do elemento superior no caminho especificado. O fragmento JSON é do tipo varchar (8000).
- Se a propriedade não puder ser encontrada na column_name especificada, a função retornará um erro.
- Se a propriedade não puder ser encontrada no column_path especificado, dependendo do [Modo de caminho](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#PATHMODE), a função retornará um erro quando estiver no modo estrito ou nulo quando estiver no modo lax.

Para obter exemplos de consulta, examine a seção Elementos de acesso de colunas aninhadas no artigo [Consultar tipos aninhados de Parquet](query-parquet-nested-types.md#access-elements-from-nested-columns).

#### <a name="access-elements-from-repeated-columns"></a>Acessar elementos de colunas repetidas

Para acessar elementos de uma coluna repetida, como um elemento de uma Matriz ou um Mapa, use a função [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) para cada elemento escalar que você precisa projetar e fornecer:

- Coluna aninhada ou repetida, como o primeiro parâmetro
- Um [caminho JSON](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) que especifica o elemento ou a propriedade a ser acessada, como um segundo parâmetro

Para acessar elementos não escalares de uma coluna repetida, use a função [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) para cada elemento não escalar que você precisa projetar e fornecer:

- Coluna aninhada ou repetida, como o primeiro parâmetro
- Um [caminho JSON](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) que especifica o elemento ou a propriedade a ser acessada, como um segundo parâmetro

Veja o fragmento de sintaxe abaixo:

```syntaxsql
    SELECT
       { JSON_VALUE (column_name, path_to_sub_element), }
       { JSON_QUERY (column_name [ , path_to_sub_element ]), )
    FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

Você pode encontrar exemplos de consulta para acessar elementos de colunas repetidas no artigo [Consultar tipos aninhados Parquet](query-parquet-nested-types.md#access-elements-from-repeated-columns).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como consultar diferentes tipos de arquivo e criar e usar exibições, confira os seguintes artigos:

- [Consultar um arquivo CSV individual](query-single-csv-file.md)
- [Consultar arquivos Parquet](query-parquet-files.md)
- [Consultar arquivos JSON](query-json-files.md)
- [Consultar tipos aninhados Parquet](query-parquet-nested-types.md)
- [Consultar pastas e vários arquivos CSV](query-folders-multiple-csv-files.md)
- [Usar metadados de arquivo em consultas](query-specific-files.md)
- [Criar e usar modos de exibição](create-use-views.md)
