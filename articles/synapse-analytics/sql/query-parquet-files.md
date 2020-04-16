---
title: Consultar arquivos do Parquet usando SQL sob demanda (visualização)
description: Neste artigo, você aprenderá como consultar arquivos do Parquet usando SQL sob demanda (visualização).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0b272a8c8ce81fc40585014e5930f5d7b1b5f2c0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431690"
---
# <a name="query-parquet-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Query Parquet arquivos usando SQL on-demand (visualização) no Azure Synapse Analytics

Neste artigo, você aprenderá a escrever uma consulta usando SQL sob demanda (visualização) que lerá arquivos do Parquet.

## <a name="prerequisites"></a>Pré-requisitos

Antes de ler o resto deste artigo, revise os seguintes artigos:

- [Configuração pela primeira vez](query-data-storage.md#first-time-setup)
- [Pré-requisitos](query-data-storage.md#prerequisites)

## <a name="dataset"></a>Dataset

Você pode consultar arquivos parquet da mesma maneira que você lê arquivos CSV. A única diferença é que o parâmetro FILEFORMAT deve ser definido como PARQUET. Exemplos neste artigo mostram as especificidades da leitura dos arquivos do Parquet.

> [!NOTE]
> Não é preciso especificar colunas na cláusula OPENROWSET WITH ao ler arquivos parquet. O SQL sob demanda utilizará metadados no arquivo Parquet e vinculará colunas pelo nome.

Você usará a pasta *parquet/taxi* para as consultas de amostra. Contém dados da NYC Taxi - Yellow Taxi Trip Records de julho de 2016. até junho de 2018.

Os dados são divididos por ano e mês e a estrutura da pasta é a seguinte:

- ano=2016
  - mês=6
  - ...
  - mês=12
- ano=2017
  - mês=1
  - ...
  - mês=12
- ano=2018
  - mês=1
  - ...
  - mês=6

## <a name="query-set-of-parquet-files"></a>Conjunto de consulta de arquivos parquet

Você pode especificar apenas as colunas de interesse quando consultar arquivos do Parquet.

```sql
SELECT
        YEAR(pickup_datetime),
        passenger_count,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*',
        FORMAT='PARQUET'
    ) WITH (
        pickup_datetime DATETIME2,
        passenger_count INT
    ) AS nyc
GROUP BY
    passenger_count,
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime),
    passenger_count;
```

## <a name="automatic-schema-inference"></a>Inferência automática de esquema

Você não precisa usar a cláusula OPENROWSET WITH ao ler arquivos do Parquet. Os nomes das colunas e os tipos de dados são lidos automaticamente a partir de arquivos do Parquet.

A amostra abaixo mostra os recursos automáticos de inferência de esquema para arquivos parquet. Ele retorna o número de linhas em setembro de 2017 sem especificar um esquema.

> [!NOTE]
> Você não precisa especificar colunas na cláusula OPENROWSET WITH ao ler arquivos do Parquet. Nesse caso, o serviço de consulta sob demanda SQL utilizará metadados no arquivo Parquet e vinculará colunas pelo nome.

```sql
SELECT
    COUNT_BIG(*)
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc;
```

### <a name="query-partitioned-data"></a>Consulta dados particionados

O conjunto de dados fornecido nesta amostra é dividido (particionado) em subpastas separadas. Você pode segmentar partições específicas usando a função filepath. Este exemplo mostra valores de tarifas por ano, mês e payment_type para os três primeiros meses de 2017.

> [!NOTE]
> A consulta sql sob demanda é compatível com o esquema de particionamento Hive/Hadoop.

```sql
SELECT
    nyc.filepath(1) AS [year],
    nyc.filepath(2) AS [month],
    payment_type,
    SUM(fare_amount) AS fare_total
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc
WHERE
    nyc.filepath(1) = 2017
    AND nyc.filepath(2) IN (1, 2, 3)
    AND pickup_datetime BETWEEN CAST('1/1/2017' AS datetime) AND CAST('3/31/2017' AS datetime)
GROUP BY
    nyc.filepath(1),
    nyc.filepath(2),
    payment_type
ORDER BY
    nyc.filepath(1),
    nyc.filepath(2),
    payment_type;
```

## <a name="type-mapping"></a>Mapeamento de tipo

Os arquivos parquet contêm descrições de tipo para cada coluna. A tabela a seguir descreve como os tipos de Parquet são mapeados para tipos nativos de SQL.

| Tipo parquet | Tipo lógico do parquet (anotação) | Tipo de dados SQL |
| --- | --- | --- |
| BOOLEAN | | bit |
| BINÁRIO / BYTE_ARRAY | | varbinary |
| DOUBLE | | FLOAT |
| FLOAT | | real |
| Int32 | | INT |
| INT64 | | BIGINT |
| INT96 | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |binary |
| BINARY |UTF8 |varchar \*(colagem UTF8) |
| BINARY |STRING |varchar \*(colagem UTF8) |
| BINARY |Enum|varchar \*(colagem UTF8) |
| BINARY |UUID |UNIQUEIDENTIFIER |
| BINARY |DECIMAL |decimal |
| BINARY |JSON |varchar(máx) \*(colagem UTF8) |
| BINARY |BSON |varbinary(max) |
| FIXED_LEN_BYTE_ARRAY |DECIMAL |decimal |
| BYTE_ARRAY |INTERVAL |varchar(max), serializado em formato padronizado |
| Int32 |INT(8, verdade) |SMALLINT |
| Int32 |INT(16, verdade) |SMALLINT |
| Int32 |INT(32, verdade) |INT |
| Int32 |INT(8, falso) |TINYINT |
| Int32 |INT(16, falso) |INT |
| Int32 |INT(32, falso) |BIGINT |
| Int32 |DATE |date |
| Int32 |DECIMAL |decimal |
| Int32 |TEMPO (MILLIS)|time |
| INT64 |INT(64, verdade) |BIGINT |
| INT64 |INT(64, falso ) |decimal (20,0) |
| INT64 |DECIMAL |decimal |
| INT64 |TEMPO (MICROS / NANOS) |time |
|INT64 |CARIMBO DE TEMPO (MILLIS / MICROS / NANOS) |datetime2 |
|[Tipo complexo](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |LISTA |varchar(max), serializado em JSON |
|[Tipo complexo](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|MAP|varchar(max), serializado em JSON |

## <a name="next-steps"></a>Próximas etapas

Avance para o próximo artigo para saber como [consultar os tipos aninhados do Parquet](query-parquet-nested-types.md).
