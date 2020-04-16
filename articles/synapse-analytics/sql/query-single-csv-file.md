---
title: Consultar arquivos CSV usando SQL sob demanda (visualização)
description: Neste artigo, você aprenderá como consultar arquivos CSV únicos com diferentes formatos de arquivo usando SQL sob demanda (visualização).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 3d09692c06bcdffbb070f545950092592e417838
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431586"
---
# <a name="query-csv-files"></a>Consultar arquivos CSV

Neste artigo, você aprenderá como consultar um único arquivo CSV usando SQL sob demanda (visualização) no Azure Synapse Analytics. Os arquivos CSV podem ter formatos diferentes: 

- Com e sem uma linha de cabeçalho
- Valores delimitados de vírgula e guias
- Finais da linha de estilo Windows e Unix
- Valores não citados e citados, e personagens que escapam

Todas as variações acima serão cobertas abaixo.

## <a name="prerequisites"></a>Pré-requisitos

Antes de ler o resto deste artigo, revise os seguintes artigos:

- [Configuração pela primeira vez](query-data-storage.md#first-time-setup)
- [Pré-requisitos](query-data-storage.md#prerequisites)

## <a name="windows-style-new-line"></a>Nova linha estilo Windows

A consulta a seguir mostra como ler um arquivo CSV sem uma linha de cabeçalho, com uma nova linha no estilo Windows e colunas delimitadas por vírgula.

Visualização do arquivo:

![As primeiras 10 linhas do arquivo CSV sem cabeçalho, nova linha estilo Windows.](./media/query-single-csv-file/population.png)

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

## <a name="unix-style-new-line"></a>Nova linha no estilo Unix

A consulta a seguir mostra como ler um arquivo sem uma linha de cabeçalho, com uma nova linha no estilo Unix e colunas delimitadas por vírgula. Observe a localização diferente do arquivo em comparação com os outros exemplos.

Visualização do arquivo:

![As primeiras 10 linhas do arquivo CSV sem linha de cabeçalho e com a nova linha Unix-Style.](./media/query-single-csv-file/population-unix.png)

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

A consulta a seguir mostra como um arquivo de leitura com uma linha de cabeçalho, com uma nova linha no estilo Unix e colunas delimitadas por vírgula. Observe a localização diferente do arquivo em comparação com os outros exemplos.

Visualização do arquivo:

![As primeiras 10 linhas do arquivo CSV com linha de cabeçalho e com a nova linha Unix-Style.](./media/query-single-csv-file/population-unix-hdr.png)

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

## <a name="custom-quote-character"></a>Caractere de citação personalizado

A consulta a seguir mostra como ler um arquivo com uma linha de cabeçalho, com uma nova linha no estilo Unix, colunas delimitadas por vírgula e valores citados. Observe a localização diferente do arquivo em comparação com os outros exemplos.

Visualização do arquivo:

![As primeiras 10 linhas do arquivo CSV com linha de cabeçalho e com a nova linha Unix-Style e valores citados.](./media/query-single-csv-file/population-unix-hdr-quoted.png)

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
> Esta consulta retornaria os mesmos resultados se você omitisse o parâmetro FIELDQUOTE, uma vez que o valor padrão de FIELDQUOTE é uma cotação dupla.

## <a name="escaping-characters"></a>Personagens escapando

A consulta a seguir mostra como ler um arquivo com uma linha de cabeçalho, com uma nova linha no estilo Unix, colunas delimitadas por vírgula e um char de escape usado para o delimitador de campo (vírgula) dentro dos valores. Observe a localização diferente do arquivo em comparação com os outros exemplos.

Visualização do arquivo:

![As primeiras 10 linhas do arquivo CSV com linha de cabeçalho e com a nova linha unix-style e char de escape usado para delimitador de campo.](./media/query-single-csv-file/population-unix-hdr-escape.png)

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
> Esta consulta falharia se o ESCAPECHAR não fosse especificado, uma vez que a comma em "Eslováquia, enia" seria tratada como delimitador de campo em vez de parte do nome do país. "Eslováquia, enia" seria tratada como duas colunas. Portanto, a linha em particular teria uma coluna a mais do que as outras linhas, e uma coluna a mais do que você definiu na cláusula WITH.

## <a name="tab-delimited-files"></a>Arquivos delimitados de guias

A consulta a seguir mostra como ler um arquivo com uma linha de cabeçalho, com uma nova linha no estilo Unix e colunas delimitadas de guias. Observe a localização diferente do arquivo em comparação com os outros exemplos.

Visualização do arquivo:

![As primeiras 10 linhas do arquivo CSV com linha de cabeçalho e com a nova linha unix-style e delimitador de guias.](./media/query-single-csv-file/population-unix-hdr-tsv.png)

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

## <a name="returning-subset-of-columns"></a>Subconjunto de colunas retornando

Até agora, você especificou o esquema de arquivo CSV usando WITH e listando todas as colunas. Você só pode especificar as colunas que realmente precisa em sua consulta usando um número ordinal para cada coluna necessária. Você também omite colunas sem interesse.

A consulta a seguir retorna o número de nomes de país distintos em um arquivo, especificando apenas as colunas necessárias:

> [!NOTE]
> Dê uma olhada na cláusula WITH na consulta abaixo e observe que há "2" (sem aspas) no final da linha onde você define a coluna *[country_name].* Significa que a coluna *[country_name]* é a segunda coluna do arquivo. A consulta ignorará todas as colunas do arquivo, exceto a segunda.

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

Os próximos artigos mostrarão como:

- [Consultando arquivos do Parquet](query-parquet-files.md)
- [Consultando pastas e vários arquivos](query-folders-multiple-csv-files.md)
