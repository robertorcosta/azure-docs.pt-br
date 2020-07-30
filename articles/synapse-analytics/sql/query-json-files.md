---
title: Consultar arquivos JSON usando o SQL sob demanda (versão prévia)
description: Esta seção explica como ler arquivos JSON usando o SQL sob demanda no Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 8b95f6b6eca0f1464a7d09d2810aa66836d76f8f
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87386632"
---
# <a name="query-json-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Consultar arquivos JSON usando o SQL sob demanda (versão prévia) no Azure Synapse Analytics

Neste artigo, você aprenderá a escrever uma consulta usando o SQL sob demanda (versão prévia) no Azure Synapse Analytics. O objetivo da consulta é ler arquivos JSON usando [OPENROWSET](develop-openrowset.md). 
- Arquivos JSON padrão em que vários documentos JSON são armazenados como uma matriz JSON.
- Arquivos JSON delimitados por linha, onde os documentos JSON são separados por caractere de nova linha. As extensões comuns para esses tipos de arquivos são `jsonl` , `ldjson` e `ndjson` .

## <a name="reading-json-documents"></a>Lendo documentos JSON

A maneira mais fácil de ver o conteúdo do arquivo JSON é fornecer a URL do arquivo para `OPENROWSET` funcionar, especificar CSV `FORMAT` e definir valores `0x0b` para `fieldterminator` e `fieldquote` . Se você precisar ler arquivos JSON delimitados por linha, isso será suficiente. Se você tiver um arquivo JSON clássico, precisará definir valores `0x0b` para `rowterminator` . `OPENROWSET`a função analisará o JSON e retornará todos os documentos no seguinte formato:

| doc |
| --- |
|{"date_rep": "2020-07-24", "dia": 24, "mês": 7, "ano": 2020, "casos": 3, "morte": 0, "geo_id": "AF"}|
|{"date_rep": "2020-07-25", "dia": 25, "mês": 7, "ano": 2020, "casos": 7, "morte": 0, "geo_id": "AF"}|
|{"date_rep": "2020-07-26", "dia": 26, "mês": 7, "ano": 2020, "casos": 4, "morte": 0, "geo_id": "AF"}|
|{"date_rep": "2020-07-27", "dia": 27, "mês": 7, "ano": 2020, "casos": 8, "morte": 0, "geo_id": "AF"}|

Se o arquivo estiver publicamente disponível ou se sua identidade do Azure AD puder acessar esse arquivo, você poderá ver o conteúdo do arquivo usando a consulta como a mostrada nos exemplos a seguir.

### <a name="read-json-files"></a>Leitura de arquivos JSON

A consulta de exemplo a seguir lê os arquivos JSON e delimitados por linha e retorna todos os documentos como uma linha separada.

```sql
select top 10 *
from openrowset(
        bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.jsonl',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
go
select top 10 *
from openrowset(
        bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b',
        rowterminator = '0x0b' --> You need to override rowterminator to read classic JSON
    ) with (doc nvarchar(max)) as rows
```

Essa consulta retornará cada documento JSON como uma linha separada do conjunto de resultados. Verifique se você pode acessar esse arquivo. Se o arquivo estiver protegido com chave SAS ou identidade personalizada, você precisará configurar a [credencial no nível do servidor para logon do SQL](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential). 

### <a name="using-data-source"></a>Usando a fonte de dados

O exemplo anterior usa o caminho completo para o arquivo. Como alternativa, você pode criar uma fonte de dados externa com o local que aponta para a pasta raiz do armazenamento e usar essa fonte de dados e o caminho relativo para o arquivo na `OPENROWSET` função:

```sql
create external data source covid
with ( location = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases' );
go
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.jsonl',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
go
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.json',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b',
        rowterminator = '0x0b' --> You need to override rowterminator to read classic JSON
    ) with (doc nvarchar(max)) as rows
```

Se uma fonte de dados estiver protegida com chave SAS ou identidade personalizada, você poderá configurar [a fonte de dados com a credencial no escopo do banco de dado](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential).

Nas seções a seguir, você pode ver como consultar vários tipos de arquivos JSON.

## <a name="parse-json-documents"></a>Analisar documentos JSON

As consultas nos exemplos anteriores retornam cada documento JSON como uma única cadeia de caracteres em uma linha separada do conjunto de resultados. Você pode usar funções `JSON_VALUE` e `OPENJSON` analisar os valores em documentos JSON e retorná-los como valores relacionais, como é mostrado no exemplo a seguir:

| representante de data \_ | cases | \_identificação geográfica |
| --- | --- | --- |
| 2020-07-24 | 3 | AF |
| 2020-07-25 | 7 | AF |
| 2020-07-26 | 4 | AF |
| 2020-07-27 | 8| AF |

### <a name="sample-json-document"></a>Exemplo de documento JSON

Os exemplos de consulta lêem arquivos *JSON* contendo documentos com a seguinte estrutura:

```json
{
    "date_rep":"2020-07-24",
    "day":24,"month":7,"year":2020,
    "cases":13,"deaths":0,
    "countries_and_territories":"Afghanistan",
    "geo_id":"AF",
    "country_territory_code":"AFG",
    "continent_exp":"Asia",
    "load_date":"2020-07-25 00:05:14",
    "iso_country":"AF"
}
```

> [!NOTE]
> Se esses documentos forem armazenados como JSON delimitados por linha, você precisará definir `FIELDTERMINATOR` e `FIELDQUOTE` para 0x0B. Se você tiver um formato JSON padrão, precisará definir `ROWTERMINATOR` como 0x0B.

### <a name="query-json-files-using-json_value"></a>Consultar arquivos JSON usando JSON_VALUE

A consulta a seguir mostra como usar [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) para recuperar valores escalares (título, editor) de documentos JSON:

```sql
select
    JSON_VALUE(doc, '$.date_rep') AS date_reported,
    JSON_VALUE(doc, '$.countries_and_territories') AS country,
    JSON_VALUE(doc, '$.cases') as cases,
    doc
from openrowset(
        bulk 'latest/ecdc_cases.jsonl',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
order by JSON_VALUE(doc, '$.geo_id') desc
```

### <a name="query-json-files-using-openjson"></a>Consultar arquivos JSON usando OPENJSON

A consulta a seguir usa [OPENJSON](/sql/t-sql/functions/openjson-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Ele recuperará as estatísticas de COVID relatadas na Sérvia:

```sql
select
    *
from openrowset(
        bulk 'latest/ecdc_cases.jsonl',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
    cross apply openjson (doc)
        with (  date_rep datetime2,
                cases int,
                fatal int '$.deaths',
                country varchar(100) '$.countries_and_territories')
where country = 'Serbia'
order by country, date_rep desc;
```

## <a name="next-steps"></a>Próximas etapas

Os próximos artigos desta série demonstrarão como:

- [Consultar pastas e arquivos múltiplos](query-folders-multiple-csv-files.md)
- [Criar e usar modos de exibição](create-use-views.md)
