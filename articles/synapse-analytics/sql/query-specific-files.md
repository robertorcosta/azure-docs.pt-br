---
title: Usando metadados de arquivos em consultas
description: A função OPENROWSET fornece informações de arquivo e caminho sobre cada arquivo usado na consulta para filtrar ou analisar dados com base no nome do arquivo e/ou no caminho da pasta.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 40a8e2c153ec3d8e7b4007340b9433a38f9ccc89
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431547"
---
# <a name="using-file-metadata-in-queries"></a>Usando metadados de arquivos em consultas

O serviço de consulta sob demanda SQL pode abordar vários arquivos e pastas conforme descrito nas [pastas de Consulta e](query-folders-multiple-csv-files.md) no artigo de vários arquivos. Neste artigo, você aprende a usar informações de metadados sobre nomes de arquivos e pastas nas consultas.

Às vezes, talvez você precise saber qual arquivo ou fonte de pasta se correlaciona com uma linha específica no conjunto de resultados.

Você pode `filepath` usar `filename` a função e retornar nomes de arquivos e/ou o caminho no conjunto de resultados. Ou você pode usá-los para filtrar dados com base no nome do arquivo e/ou no caminho da pasta. Essas funções são descritas na [função filename](develop-storage-files-overview.md#filename-function) da seção de sintaxe e [na função filepath](develop-storage-files-overview.md#filepath-function). Abaixo você encontrará descrições curtas ao longo das amostras.

## <a name="prerequisites"></a>Pré-requisitos

Antes de ler o resto deste artigo, revise os seguintes pré-requisitos:

- [Configuração pela primeira vez](query-data-storage.md#first-time-setup)
- [Pré-requisitos](query-data-storage.md#prerequisites)

## <a name="functions"></a>Funções

### <a name="filename"></a>Nome de arquivo

Esta função retorna o nome do arquivo de onde a linha é originária.

A amostra a seguir lê os arquivos de dados do NYC Yellow Taxi para os últimos três meses de 2017 e retorna o número de passeios por arquivo. A parte OPENROWSET da consulta especifica quais arquivos serão lidos.

```sql
SELECT
    r.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET') AS [r]
GROUP BY
    r.filename()
ORDER BY
    [filename];
```

O exemplo a seguir mostra como *o nome do arquivo()* pode ser usado na cláusula WHERE para filtrar os arquivos a serem lidos. Ele acessa toda a pasta na parte OPENROWSET da consulta e filtra arquivos na cláusula WHERE.

Seus resultados serão os mesmos do exemplo anterior.

```sql
SELECT
    r.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
    FORMAT='PARQUET') AS [r]
WHERE
    r.filename() IN ('yellow_tripdata_2017-10.parquet', 'yellow_tripdata_2017-11.parquet', 'yellow_tripdata_2017-12.parquet')
GROUP BY
    r.filename()
ORDER BY
    [filename];
```

### <a name="filepath"></a>Filepath

A função filepath retorna um caminho completo ou parcial:

- Quando chamado sem parâmetro, ele retorna o caminho completo do arquivo do que a linha se origina.
- Quando chamado com um parâmetro, ele retorna parte do caminho que corresponde ao curinga na posição especificada no parâmetro. Por exemplo, o valor do parâmetro 1 retornaria parte do caminho que corresponde ao primeiro curinga.

A amostra a seguir lê os arquivos de dados do NYC Yellow Taxi para os últimos três meses de 2017. Ele retorna o número de passeios por caminho de arquivo. A parte OPENROWSET da consulta especifica quais arquivos serão lidos.

```sql
SELECT
    r.filepath() AS filepath
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/yellow_tripdata_2017-1*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 2
    )
    WITH (
        vendor_id INT,
        pickup_datetime DATETIME2,
        dropoff_datetime DATETIME2,
        passenger_count SMALLINT,
        trip_distance FLOAT,
        rate_code SMALLINT,
        store_and_fwd_flag SMALLINT,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type SMALLINT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS [r]
GROUP BY
    r.filepath()
ORDER BY
    filepath;
```

O exemplo a seguir mostra como *filepath()* pode ser usado na cláusula WHERE para filtrar os arquivos a serem lidos.

Você pode usar os curingas na parte OPENROWSET da consulta e filtrar os arquivos na cláusula WHERE. Seus resultados serão os mesmos do exemplo anterior.

```sql
SELECT
    r.filepath() AS filepath
    ,r.filepath(1) AS [year]
    ,r.filepath(2) AS [month]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/yellow_tripdata_*-*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 2
    )
WITH (
    vendor_id INT,
    pickup_datetime DATETIME2,
    dropoff_datetime DATETIME2,
    passenger_count SMALLINT,
    trip_distance FLOAT,
    rate_code SMALLINT,
    store_and_fwd_flag SMALLINT,
    pickup_location_id INT,
    dropoff_location_id INT,
    payment_type SMALLINT,
    fare_amount FLOAT,
    extra FLOAT,
    mta_tax FLOAT,
    tip_amount FLOAT,
    tolls_amount FLOAT,
    improvement_surcharge FLOAT,
    total_amount FLOAT
) AS [r]
WHERE
    r.filepath(1) IN ('2017')
    AND r.filepath(2) IN ('10', '11', '12')
GROUP BY
    r.filepath()
    ,r.filepath(1)
    ,r.filepath(2)
ORDER BY
    filepath;
```

## <a name="next-steps"></a>Próximas etapas

No próximo artigo, você aprenderá como [consultar arquivos do Parquet](query-parquet-files.md).
