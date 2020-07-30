---
title: Consultar tipos aninhados do Parquet usando SQL sob demanda (versão prévia)
description: Neste artigo, você aprenderá a consultar os tipos aninhados do Parquet.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 60f2a1992750950b50995fcf36513e44e377004d
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87386598"
---
# <a name="query-nested-types-in-parquet-and-json-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Consultar tipos aninhados em arquivos parquet e JSON usando o SQL sob demanda (versão prévia) na análise de Synapse do Azure

Neste artigo, você aprenderá a escrever uma consulta usando o SQL sob demanda (versão prévia) no Azure Synapse Analytics. Essa consulta lerá os tipos aninhados do Parquet.
Tipos aninhados são estruturas complexas que representam objetos ou matrizes. Tipos aninhados podem ser armazenados em: 
- [PARQUET](query-parquet-files.md) , em que você pode ter várias colunas complexas contendo matrizes e objetos.
- [Arquivos JSON](query-json-files.md) hierárquicos em que você pode ler documentos JSON complexos como uma única coluna.
- Coleção CosmosDB em que cada documento pode conter propriedades aninhadas complexas (atualmente em visualização pública restrita).

O Synapse SQL sob demanda formata todos os tipos aninhados como objetos JSON e matrizes, para que você possa [extrair ou modificar objetos complexos usando funções JSON](https://docs.microsoft.com/sql/relational-databases/json/validate-query-and-change-json-data-with-built-in-functions-sql-server) ou [analisar dados JSON usando a função OPENJSON](https://docs.microsoft.com/sql/relational-databases/json/convert-json-data-to-rows-and-columns-with-openjson-sql-server). 

Um exemplo de consulta que extrai valores escalares e objetos do [COVID-19 Open Research DataSet](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) File JSON com objetos aninhados é mostrado abaixo. 

```sql
SELECT
    title = JSON_VALUE(doc, '$.metadata.title'),
    first_author = JSON_QUERY(doc, '$.metadata.authors[0]'),
    first_author_name = JSON_VALUE(doc, '$.metadata.authors[0].first'),
    complex_object = doc
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json',
        FORMAT='CSV', FIELDTERMINATOR ='0x0b', FIELDQUOTE = '0x0b', ROWTERMINATOR = '0x0b'
    )
    WITH ( doc varchar(MAX) ) AS docs;
```

`JSON_VALUE`a função retorna um valor escalar do campo no caminho especificado. `JSON_QUERY`a função retorna um objeto formatado como JSON a partir do campo no caminho especificado.

> [!IMPORTANT]
> Este exemplo usa um arquivo do [conjunto de COVID-19 Open Research](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/). Consulte licença do desse e a estrutura de dados nesta página.

## <a name="prerequisites"></a>Pré-requisitos

A primeira etapa é **criar um banco de dados** com uma fonte de dados que faça referências. Em seguida, inicialize os objetos executando o [script de instalação](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) nesse banco de dados. Esse script de instalação criará as fontes de dados, as credenciais no escopo do banco de dados e os formatos de arquivo externos que são usados nestas amostras.

## <a name="project-nested-or-repeated-data"></a>Dados de projeto aninhados ou repetidos

O arquivo PARQUET pode ter várias colunas com tipos complexos. Os valores dessas colunas são formatados como texto JSON e retornados como uma coluna VARCHAR. A consulta a seguir lê o arquivo *structExample. parquet* e mostra como ler os valores das colunas aninhadas: 

```sql
SELECT
    DateStruct, TimeStruct, TimestampStruct, DecimalStruct, FloatStruct
FROM
    OPENROWSET(
        BULK 'parquet/nested/structExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    )
    WITH (
        DateStruct VARCHAR(8000),
        TimeStruct VARCHAR(8000),
        TimestampStruct VARCHAR(8000),
        DecimalStruct VARCHAR(8000),
        FloatStruct VARCHAR(8000)
    ) AS [r];
```

Essa consulta retornará o seguinte resultado, em que o conteúdo de cada objeto aninhado é retornado como texto JSON:

| DateStruct    | Timestruct    | TimestampStruct   | DecimalStruct | FloatStruct |
| --- | --- | --- | --- | --- |
|{"Date": "2009-04-25"}| {"Hora": "20:51:54.3598000"}|    {"Timestamp": "5501-04-08 12:13:57.4821000"}|    {"Decimal": 11143412.25350}| {"Float": 0.5}|
|{"Date": "1916-04-29"}| {"Hora": "00:16:04.6778000"}|    {"Timestamp": "1990-06-30 20:50:52.6828000"}|    {"Decimal": 1963545.62800}|  {"Float":-2,125}|

A consulta a seguir lê o arquivo *justSimpleArray.parquet*. Ela projeta todas as colunas do arquivo Parquet, incluindo dados aninhados ou repetidos.

```sql
SELECT
    SimpleArray
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

Essa consulta retornará o seguinte resultado:

|SimpleArray|
| --- |
|[11, 12, 13]|
|[21, 22, 23]|

## <a name="read-properties-from-nested-object-columns"></a>Ler propriedades de colunas de objeto aninhado

`JSON_VALUE`a função permite retornar valores da coluna formatada como texto JSON:

```sql
SELECT
    title = JSON_VALUE(complex_column, '$.metadata.title'),
    first_author_name = JSON_VALUE(complex_column, '$.metadata.authors[0].first'),
    body_text = JSON_VALUE(complex_column, '$.body_text.text'),
    complex_column
FROM
    OPENROWSET( BULK 'https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json',
                FORMAT='CSV', FIELDTERMINATOR ='0x0b', FIELDQUOTE = '0x0b', ROWTERMINATOR = '0x0b' ) WITH ( complex_column varchar(MAX) ) AS docs;
```

O resultado é mostrado na tabela a seguir:

|título  | first_author_name | body_text | complex_column |
| --- | --- | --- | --- |
| Informações complementares de um epidemiolo de eco... | Julien   | -Figura S1: Phylogeny de... | `{    "paper_id": "000b7d1517ceebb34e1e3e817695b6de03e2fa78",    "metadata": {        "title": "Supplementary Information An eco-epidemiological study of Morbilli-related paramyxovirus infection in Madagascar bats reveals host-switching as the dominant macro-evolutionary mechanism",        "authors": [            {                "first": "Julien"` |

Ao contrário dos arquivos JSON que, na maioria dos casos, retornam uma única coluna contendo um objeto JSON complexo. Os arquivos PARQUET podem ter vários complexos. Você pode ler as propriedades de coluna aninhada usando a `JSON_VALUE` função em cada coluna. `OPENROWSET`permite que você especifique diretamente os caminhos da cláusula aninhada Properties in `WITH` . Os caminhos podem ser definidos como um nome da coluna ou você pode adicionar uma [expressão de caminho JSON](https://docs.microsoft.com/sql/relational-databases/json/json-path-expressions-sql-server) após o tipo de coluna.

A consulta a seguir lê o arquivo *structExample. parquet* e mostra como Surface elementos de uma coluna aninhada. Você tem duas maneiras de referenciar o valor aninhado:
- Especificando a expressão de caminho de valor aninhado após especificação de tipo.
- Formatando o nome da coluna como um caminho aninhado usando "." para fazer referência aos campos.

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'parquet/nested/structExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    )
    WITH (
        [DateValue] DATE '$.DateStruct.Date',
        [TimeStruct.Time] TIME,
        [TimestampStruct.Timestamp] DATETIME2,
        DecimalValue DECIMAL(18, 5) '$.DecimalStruct.Decimal',
        [FloatStruct.Float] FLOAT
    ) AS [r];
```

## <a name="access-elements-from-repeated-columns"></a>Acessar elementos de colunas repetidas

A consulta a seguir lê o arquivo *justSimpleArray.parquet* e usa [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) para recuperar um elemento **escalar** de uma coluna repetida, como uma Matriz ou Mapa:

```sql
SELECT
    *,
    JSON_VALUE(SimpleArray, '$[0]') AS FirstElement,
    JSON_VALUE(SimpleArray, '$[1]') AS SecondElement,
    JSON_VALUE(SimpleArray, '$[2]') AS ThirdElement
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

O resultado é mostrado na tabela a seguir:

|SimpleArray    | Nome do primeiro  | Segundoelement | Terceiroelement |
| --- | --- | --- | --- |
| [11, 12, 13] | 11   | 12 | 13 |
| [21, 22, 23] | 21   | 22 | 23 |

## <a name="access-sub-objects-from-complex-columns"></a>Acessar subobjetos de colunas complexas

A consulta a seguir lê o arquivo *mapExample.parquet* e usa [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) para recuperar um elemento **não escalar** de uma coluna repetida, como uma Matriz ou Mapa:

```sql
SELECT
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

Você também pode referenciar explicitamente as colunas que deseja retornar na `WITH` cláusula:

```sql
SELECT DocId,
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) 
    WITH (DocId bigint, MapOfPersons VARCHAR(max)) AS [r];
```

A estrutura `MapOfPersons` é retornada como `VARCHAR` coluna e formatada como cadeia de caracteres JSON.

## <a name="projecting-values-from-repeated-columns"></a>Projetando valores de colunas repetidas

Se você tiver uma matriz de valores escalares (por exemplo `[1,2,3]` ) em algumas colunas, poderá expandi-los facilmente e associá-los com a linha principal usando o seguinte script:

```sql
SELECT
    SimpleArray, Element
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS arrays
    CROSS APPLY OPENJSON (SimpleArray) WITH (Element int '$') as array_values
```

## <a name="next-steps"></a>Próximas etapas

O próximo artigo mostrará como [Consultar arquivos JSON](query-json-files.md).
