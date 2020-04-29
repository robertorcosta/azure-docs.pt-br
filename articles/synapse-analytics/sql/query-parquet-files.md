---
title: Consultar arquivos parquet usando o SQL sob demanda (versão prévia)
description: Neste artigo, você aprenderá a consultar os arquivos do parquet usando o SQL sob demanda (versão prévia).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0b272a8c8ce81fc40585014e5930f5d7b1b5f2c0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431690"
---
# <a name="query-parquet-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Consultar arquivos parquet usando o SQL sob demanda (versão prévia) no Azure Synapse Analytics

Neste artigo, você aprenderá a escrever uma consulta usando o SQL sob demanda (versão prévia) que lerá os arquivos parquet.

## <a name="prerequisites"></a>Pré-requisitos

Antes de ler o restante deste artigo, examine os seguintes artigos:

- [Configuração inicial](query-data-storage.md#first-time-setup)
- [Pré-requisitos](query-data-storage.md#prerequisites)

## <a name="dataset"></a>Dataset

Você pode consultar arquivos parquet da mesma maneira que lê arquivos CSV. A única diferença é que o parâmetro FileFormat deve ser definido como PARQUET. Os exemplos neste artigo mostram as especificidades de leitura de arquivos parquet.

> [!NOTE]
> Você não precisa especificar colunas na cláusula OPENROWSET WITH ao ler arquivos parquet. O SQL sob demanda usará metadados no arquivo parquet e associará colunas por nome.

Você usará a pasta *parquet/táxi* para as consultas de exemplo. Ele contém dados de NYC táxi-amarelo de táxi, de julho de 2016. a junho de 2018.

Os dados são particionados por ano e mês e a estrutura de pastas é a seguinte:

- ano = 2016
  - mês = 6
  - ...
  - mês = 12
- ano = 2017
  - mês = 1
  - ...
  - mês = 12
- year = 2018
  - mês = 1
  - ...
  - mês = 6

## <a name="query-set-of-parquet-files"></a>Conjunto de consultas de arquivos parquet

Você pode especificar apenas as colunas de interesse ao consultar arquivos parquet.

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

## <a name="automatic-schema-inference"></a>Inferência de esquema automática

Você não precisa usar a cláusula OPENROWSET WITH ao ler arquivos parquet. Os nomes de coluna e os tipos de dados são lidos automaticamente de arquivos parquet.

O exemplo a seguir mostra as funcionalidades de inferência de esquema automática para arquivos parquet. Ele retorna o número de linhas em setembro de 2017 sem especificar um esquema.

> [!NOTE]
> Você não precisa especificar colunas na cláusula OPENROWSET WITH ao ler arquivos parquet. Nesse caso, o serviço de consulta do SQL sob demanda utilizará metadados no arquivo parquet e associará colunas por nome.

```sql
SELECT
    COUNT_BIG(*)
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc;
```

### <a name="query-partitioned-data"></a>Consultar dados particionados

O conjunto de dados fornecido neste exemplo é dividido (particionado) em subpastas separadas. Você pode direcionar partições específicas usando a função FilePath. Este exemplo mostra valores de tarifa por ano, mês e payment_type para os três primeiros meses de 2017.

> [!NOTE]
> A consulta SQL sob demanda é compatível com o esquema de particionamento do hive/Hadoop.

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

Os arquivos parquet contêm descrições de tipo para cada coluna. A tabela a seguir descreve como os tipos parquet são mapeados para tipos nativos do SQL.

| Tipo de parquet | Tipo lógico parquet (anotação) | Tipo de dados SQL |
| --- | --- | --- |
| BOOLEAN | | bit |
| BINÁRIO/BYTE_ARRAY | | varbinary |
| DOUBLE | | FLOAT |
| FLOAT | | real |
| INT32 | | INT |
| INT64 | | BIGINT |
| INT96 | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |binary |
| BINARY |UTF8 |varchar \*(agrupamento UTF8) |
| BINARY |STRING |varchar \*(agrupamento UTF8) |
| BINARY |ENUMERA|varchar \*(agrupamento UTF8) |
| BINARY |UUID |UNIQUEIDENTIFIER |
| BINARY |DECIMAL |decimal |
| BINARY |JSON |varchar (max) \*(agrupamento UTF8) |
| BINARY |BSON |varbinary(max) |
| FIXED_LEN_BYTE_ARRAY |DECIMAL |decimal |
| BYTE_ARRAY |INTERVAL |varchar (max), serializado em formato padronizado |
| INT32 |INT (8, true) |SMALLINT |
| INT32 |INT (16, true) |SMALLINT |
| INT32 |INT (32, true) |INT |
| INT32 |INT (8, false) |TINYINT |
| INT32 |INT (16, false) |INT |
| INT32 |INT (32, false) |BIGINT |
| INT32 |DATE |date |
| INT32 |DECIMAL |decimal |
| INT32 |TEMPO (MILIS)|time |
| INT64 |INT (64, true) |BIGINT |
| INT64 |INT (64, false) |decimal (20, 0) |
| INT64 |DECIMAL |decimal |
| INT64 |TEMPO (MICROS/NANOS) |time |
|INT64 |CARIMBO DE DATA/HORA (MILIS/MICROS/NANOS) |datetime2 |
|[Tipo complexo](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |LISTA |varchar (max), serializado em JSON |
|[Tipo complexo](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|MAP|varchar (max), serializado em JSON |

## <a name="next-steps"></a>Próximas etapas

Avance para o próximo artigo para saber como [consultar tipos aninhados do parquet](query-parquet-nested-types.md).
