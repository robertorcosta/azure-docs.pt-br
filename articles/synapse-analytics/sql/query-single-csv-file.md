---
title: Consultar arquivos CSV usando o SQL sob demanda (versão prévia)
description: Neste artigo, você aprenderá a consultar arquivos CSV únicos com formatos de arquivo diferentes usando o SQL sob demanda (versão prévia).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 3d09692c06bcdffbb070f545950092592e417838
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431586"
---
# <a name="query-csv-files"></a>Consultar arquivos CSV

Neste artigo, você aprenderá a consultar um único arquivo CSV usando o SQL sob demanda (versão prévia) no Azure Synapse Analytics. Arquivos CSV podem ter formatos diferentes: 

- Com e sem uma linha de cabeçalho
- Valores de vírgula e delimitado por tabulação
- Terminações de linha de estilo do Windows e UNIX
- Valores não citados e entre aspas e caracteres de escape

Todas as variações acima serão abordadas abaixo.

## <a name="prerequisites"></a>Pré-requisitos

Antes de ler o restante deste artigo, examine os seguintes artigos:

- [Configuração inicial](query-data-storage.md#first-time-setup)
- [Pré-requisitos](query-data-storage.md#prerequisites)

## <a name="windows-style-new-line"></a>Nova linha do estilo Windows

A consulta a seguir mostra como ler um arquivo CSV sem uma linha de cabeçalho, com uma nova linha do estilo do Windows e colunas delimitadas por vírgula.

Visualização do arquivo:

![Dez primeiras linhas do arquivo CSV sem cabeçalho e uma nova linha no estilo do Windows.](./media/query-single-csv-file/population.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv',
         FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="unix-style-new-line"></a>Nova linha do estilo UNIX

A consulta a seguir mostra como ler um arquivo sem uma linha de cabeçalho, com uma nova linha em estilo UNIX e colunas delimitadas por vírgulas. Observe o local diferente do arquivo em comparação com os outros exemplos.

Visualização do arquivo:

![Primeiras 10 linhas do arquivo CSV sem a linha de cabeçalho e com a nova linha do estilo UNIX.](./media/query-single-csv-file/population-unix.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix/population.csv',
        FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="header-row"></a>Linha de cabeçalho

A consulta a seguir mostra como ler um arquivo com uma linha de cabeçalho, com uma nova linha em estilo UNIX e colunas delimitadas por vírgulas. Observe o local diferente do arquivo em comparação com os outros exemplos.

Visualização do arquivo:

![Primeiras 10 linhas do arquivo CSV com a linha de cabeçalho e com a nova linha em estilo UNIX.](./media/query-single-csv-file/population-unix-hdr.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr/population.csv',
        FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="custom-quote-character"></a>Caractere de aspas personalizada

A consulta a seguir mostra como ler um arquivo com uma linha de cabeçalho, com uma nova linha em estilo UNIX, colunas delimitadas por vírgula e valores entre aspas. Observe o local diferente do arquivo em comparação com os outros exemplos.

Visualização do arquivo:

![Primeiras 10 linhas do arquivo CSV com a linha de cabeçalho e com a nova linha e os valores entre estilo UNIX.](./media/query-single-csv-file/population-unix-hdr-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr-quoted/population.csv',
        FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        FIELDQUOTE = '"'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

> [!NOTE]
> Essa consulta retornaria os mesmos resultados se você omitisse o parâmetro FIELDQUOTE, pois o valor padrão para FIELDQUOTE é uma aspa dupla.

## <a name="escaping-characters"></a>Caracteres de escape

A consulta a seguir mostra como ler um arquivo com uma linha de cabeçalho, com uma nova linha em estilo UNIX, colunas delimitadas por vírgula e um caractere de escape usado para o delimitador de campo (vírgula) dentro dos valores. Observe o local diferente do arquivo em comparação com os outros exemplos.

Visualização do arquivo:

![Primeiras 10 linhas do arquivo CSV com a linha de cabeçalho e com a nova linha e o caractere de escape do estilo UNIX usados para delimitador de campo.](./media/query-single-csv-file/population-unix-hdr-escape.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr-escape/population.csv',
        FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        ESCAPECHAR = '\\'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Slov,enia';
```

> [!NOTE]
> Essa consulta falharia se ESCAPECHAR não for especificado, pois a vírgula em "Slov, Enia" seria tratada como delimitador de campo em vez de parte do nome do país. "Slov, Enia" seria tratado como duas colunas. Portanto, a linha específica teria uma coluna mais do que as outras linhas e uma coluna mais do que você definiu na cláusula WITH.

## <a name="tab-delimited-files"></a>Arquivos delimitados por tabulação

A consulta a seguir mostra como ler um arquivo com uma linha de cabeçalho, com uma nova linha em estilo UNIX e colunas delimitadas por tabulação. Observe o local diferente do arquivo em comparação com os outros exemplos.

Visualização do arquivo:

![As primeiras 10 linhas do arquivo CSV com a linha de cabeçalho e com o novo delimitador de linha e tabulação do estilo UNIX.](./media/query-single-csv-file/population-unix-hdr-tsv.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix-hdr-tsv/population.csv',
        FORMAT = 'CSV',
        FIELDTERMINATOR ='\t',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017
```

## <a name="returning-subset-of-columns"></a>Retornando subconjunto de colunas

Até agora, você especificou o esquema de arquivo CSV usando com e listando todas as colunas. Você só pode especificar as colunas que realmente precisa em sua consulta usando um número ordinal para cada coluna necessária. Você também omitirá colunas sem interesse.

A consulta a seguir retorna o número de nomes de país distintos em um arquivo, especificando apenas as colunas que são necessárias:

> [!NOTE]
> Dê uma olhada na cláusula WITH na consulta abaixo e observe que há "2" (sem aspas) no final da linha em que você define a coluna *[country_name]* . Isso significa que a coluna *[country_name]* é a segunda coluna no arquivo. A consulta irá ignorar todas as colunas no arquivo, exceto a segunda.

```sql
SELECT
    COUNT(DISTINCT country_name) AS countries
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv',
         FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    --[country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2 2
    --[year] smallint,
    --[population] bigint
) AS [r]
```

## <a name="next-steps"></a>Próximas etapas

Os próximos artigos lhe mostrarão como:

- [Consultando arquivos parquet](query-parquet-files.md)
- [Consultando pastas e vários arquivos](query-folders-multiple-csv-files.md)
