---
title: Consultar arquivos Parquet usando o SQL sob demanda (versão prévia)
description: Neste artigo, você aprenderá a consultar arquivos Parquet usando o SQL sob demanda (versão prévia).
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 4bab1ef4588a705f0dd6cdb34be8272868f826e9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85207559"
---
# <a name="query-parquet-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Consultar arquivos Parquet usando o SQL sob demanda (versão prévia) no Azure Synapse Analytics

Neste artigo, você aprenderá a gravar uma consulta usando o SQL sob demanda (versão prévia) que lerá os arquivos Parquet.

## <a name="prerequisites"></a>Pré-requisitos

A primeira etapa é criar um **banco de dados** com uma fonte de dados que faça referência à conta de armazenamento [Táxis amarelos em NYC](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Em seguida, inicialize os objetos executando o [script de instalação](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) nesse banco de dados. Esse script de instalação criará as fontes de dados, as credenciais no escopo do banco de dados e os formatos de arquivo externos que são usados nestas amostras.

## <a name="dataset"></a>Dataset

Neste exemplo, é usado o conjunto dados de [Táxis amarelos em NYC](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). A consulta a arquivos Parquet pode ser feita da mesma maneira que os [arquivos CSV](query-parquet-files.md) são lidos. A única diferença é que o parâmetro `FILEFORMAT` deve ser definido como `PARQUET`. Os exemplos neste artigo mostram as especificidades de leitura de arquivos Parquet.

## <a name="query-set-of-parquet-files"></a>Conjunto de consultas de arquivos Parquet

É possível especificar somente as colunas desejadas ao consultar arquivos Parquet.

```sql
SELECT
        YEAR(tpepPickupDateTime),
        passengerCount,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'puYear=2018/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) WITH (
        tpepPickupDateTime DATETIME2,
        passengerCount INT
    ) AS nyc
GROUP BY
    passengerCount,
    YEAR(tpepPickupDateTime)
ORDER BY
    YEAR(tpepPickupDateTime),
    passengerCount;
```

## <a name="automatic-schema-inference"></a>Inferência de esquema automática

Não é necessário usar a cláusula OPENROWSET WITH para realizar a leitura de arquivos Parquet. Os nomes de coluna e os tipos de dados são lidos automaticamente por meio dos arquivos Parquet.

O exemplo a seguir mostra os recursos de inferência de esquema automática para arquivos Parquet. Ele retorna o número de linhas em Setembro de 2017 sem especificar um esquema.

> [!NOTE]
> Não é necessário especificar colunas na cláusula OPENROWSET WITH ao realizar a leitura de arquivos Parquet. Nesse caso, o serviço de consulta do SQL sob demanda utilizará metadados no arquivo Parquet e associará as colunas por nome.

```sql
SELECT TOP 10 *
FROM  
    OPENROWSET(
        BULK 'puYear=2018/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) AS nyc
```

### <a name="query-partitioned-data"></a>Consultar dados particionados

O conjunto de dados fornecido neste exemplo foi dividido (particionado) em subpastas separadas. Com a função de linguagem é possível destinar partições específicas. Este exemplo mostra os valores das tarifas por ano, mês e tipo de pagamento nos três primeiros meses de 2017.

> [!NOTE]
> A consulta SQL sob demanda é compatível com o esquema de particionamento do Hive/Hadoop.

```sql
SELECT
        YEAR(tpepPickupDateTime),
        passengerCount,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'puYear=*/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) nyc
WHERE
    nyc.filepath(1) = 2017
    AND nyc.filepath(2) IN (1, 2, 3)
    AND tpepPickupDateTime BETWEEN CAST('1/1/2017' AS datetime) AND CAST('3/31/2017' AS datetime)
GROUP BY
    passengerCount,
    YEAR(tpepPickupDateTime)
ORDER BY
    YEAR(tpepPickupDateTime),
    passengerCount;
```

## <a name="type-mapping"></a>Mapeamento de tipo

Os arquivos Parquet contêm descrições de tipo para cada coluna. A tabela a seguir descreve como os tipos Parquet são mapeados para tipos nativos do SQL.

| Tipo do Parquet | Tipo lógico do Parquet (anotação) | Tipo de dados SQL |
| --- | --- | --- |
| BOOLEAN | | bit |
| BINARY/BYTE_ARRAY | | varbinary |
| DOUBLE | | FLOAT |
| FLOAT | | real |
| INT32 | | INT |
| INT64 | | BIGINT |
| INT96 | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |binary |
| BINARY |UTF8 |varchar \*(ordenação UTF8) |
| BINARY |STRING |varchar \*(ordenação UTF8) |
| BINARY |ENUM|varchar \*(ordenação UTF8) |
| BINARY |UUID |UNIQUEIDENTIFIER |
| BINARY |DECIMAL |decimal |
| BINARY |JSON |varchar (max) \*(ordenação UTF8) |
| BINARY |BSON |varbinary(max) |
| FIXED_LEN_BYTE_ARRAY |DECIMAL |decimal |
| BYTE_ARRAY |INTERVAL |varchar (max), serializado em formato padronizado |
| INT32 |INT(8, true) |SMALLINT |
| INT32 |INT(16, true) |SMALLINT |
| INT32 |INT(32, true) |INT |
| INT32 |INT(8, false) |TINYINT |
| INT32 |INT(16, false) |INT |
| INT32 |INT(32, false) |BIGINT |
| INT32 |DATE |date |
| INT32 |DECIMAL |decimal |
| INT32 |TIME (MILLIS)|time |
| INT64 |INT(64, true) |BIGINT |
| INT64 |INT(64, false) |decimal(20,0) |
| INT64 |DECIMAL |decimal |
| INT64 |TIME (MICROS/NANOS) |time |
|INT64 |TIMESTAMP (MILLIS/MICROS/NANOS) |datetime2 |
|[Tipo complexo](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |LISTA |varchar (max), serializado em JSON |
|[Tipo complexo](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|MAP|varchar (max), serializado em JSON |

## <a name="next-steps"></a>Próximas etapas

Acesse o próximo artigo para saber como [Consultar tipos aninhados de Parquet](query-parquet-nested-types.md).
