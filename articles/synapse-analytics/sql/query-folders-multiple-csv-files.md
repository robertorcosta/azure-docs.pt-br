---
title: Pastas de consulta e vários arquivos CSV usando SQL sob demanda (visualização)
description: SQL on-demand (preview) suporta a leitura de vários arquivos/pastas usando curingas, que são semelhantes aos curingas usados no sistema operacional Windows.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 070574f0eec40c27eb05cec3bf0a84282b85050f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431794"
---
# <a name="query-folders-and-multiple-csv-files"></a>Pastas de consulta e vários arquivos CSV  

Neste artigo, você aprenderá a escrever uma consulta usando SQL sob demanda (visualização) no Azure Synapse Analytics.

O SQL sob demanda suporta a leitura de vários arquivos/pastas usando curingas, que são semelhantes aos curingas usados no sistema operacional Windows. No entanto, uma maior flexibilidade está presente, uma vez que vários curingas são permitidos.

## <a name="prerequisites"></a>Pré-requisitos

Antes de ler o resto deste artigo, certifique-se de revisar os artigos listados abaixo:

- [Configuração pela primeira vez](query-data-storage.md#first-time-setup)
- [Pré-requisitos](query-data-storage.md#prerequisites)

## <a name="read-multiple-files-in-folder"></a>Leia vários arquivos na pasta

Você usará a pasta *csv/taxi* para acompanhar as consultas de amostra. Contém dados da NYC Taxi - Yellow Taxi Trip Records de julho de 2016 a junho de 2018.

Os arquivos em *csv/taxi* são nomeados após ano e mês:

- yellow_tripdata_2016-07.csv
- yellow_tripdata_2016-08.csv
- yellow_tripdata_2016-09.csv
- ...
- yellow_tripdata_2018-04.csv
- yellow_tripdata_2018-05.csv
- yellow_tripdata_2018-06.csv

Cada arquivo tem a seguinte estrutura:
        
    [First 10 rows of the CSV file](./media/querying-folders-and-multiple-csv-files/nyc-taxi.png)

## <a name="read-all-files-in-folder"></a>Leia todos os arquivos na pasta
    
O exemplo abaixo lê todos os arquivos de dados do NYC Yellow Taxi da pasta *csv/taxi* e retorna o número total de passageiros e passeios por ano. Também mostra o uso de funções agregadas.

```sql
SELECT 
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/*.*',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
           trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
           pickup_location_id INT,'
        dropoff_location_id INT,
           payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Todos os arquivos acessados com o único OPENROWSET devem ter a mesma estrutura (ou seja, número de colunas e seus tipos de dados).

### <a name="read-subset-of-files-in-folder"></a>Leia subconjunto de arquivos na pasta

O exemplo abaixo lê os arquivos de dados do NYC Yellow Taxi de 2017 da pasta *csv/taxi* usando um curinga e devolve o valor total da tarifa por tipo de pagamento.

```sql
SELECT 
    payment_type,  
    SUM(fare_amount) AS fare_total
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/yellow_tripdata_2017-*.csv',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY payment_type
ORDER BY payment_type;
```

> [!NOTE]
> Todos os arquivos acessados com o único OPENROWSET devem ter a mesma estrutura (ou seja, número de colunas e seus tipos de dados).

## <a name="read-folders"></a>Ler pastas

O caminho que você fornece para OPENROWSET também pode ser um caminho para uma pasta. As seções a seguir incluem esses tipos de consulta.

### <a name="read-all-files-from-specific-folder"></a>Leia todos os arquivos de pasta específica

Você pode ler todos os arquivos em uma pasta usando o curinga nível de arquivo como mostrado em [Ler todos os arquivos na pasta](#read-all-files-in-folder). Mas, há uma maneira de consultar uma pasta e consumir todos os arquivos dentro dessa pasta.

Se o caminho fornecido no OPENROWSET aponta para uma pasta, todos os arquivos dessa pasta serão usados como fonte para sua consulta. A consulta a seguir lerá todos os arquivos na pasta *csv/taxi.*

> [!NOTE]
> Observe a existência do / no final do caminho na consulta abaixo. Denota uma pasta. Se o / for omitido, a consulta terá como alvo um arquivo chamado *taxi.*

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Todos os arquivos acessados com o único OPENROWSET devem ter a mesma estrutura (ou seja, número de colunas e seus tipos de dados).

### <a name="read-all-files-from-multiple-folders"></a>Leia todos os arquivos de várias pastas

É possível ler arquivos de várias pastas usando um curinga. A consulta a seguir lerá todos os arquivos de todas as pastas localizadas na pasta *csv* que têm nomes começando com *t* e terminando com *i*.

> [!NOTE]
> Observe a existência do / no final do caminho na consulta abaixo. Denota uma pasta. Se o / for omitido, a consulta terá como destino arquivos chamados *&ast;t i.*

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/t*i/', 
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Todos os arquivos acessados com o único OPENROWSET devem ter a mesma estrutura (ou seja, número de colunas e seus tipos de dados).

Como você tem apenas uma pasta que corresponde aos critérios, o resultado da consulta é o mesmo que [Ler todos os arquivos na pasta](#read-all-files-in-folder).

## <a name="multiple-wildcards"></a>Vários curingas

Você pode usar vários curingas em diferentes níveis de caminho. Por exemplo, você pode enriquecer consulta anterior para ler arquivos apenas com dados de 2017, de todas as pastas quais nomes começam com *t* e terminam com *i*.

> [!NOTE]
> Observe a existência do / no final do caminho na consulta abaixo. Denota uma pasta. Se o / for omitido, a consulta terá como destino arquivos chamados *&ast;t i.*
> Há um limite máximo de 10 curingas por consulta.

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/t*i/yellow_tripdata_2017-*.csv',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> Todos os arquivos acessados com o único OPENROWSET devem ter a mesma estrutura (ou seja, número de colunas e seus tipos de dados).

Como você tem apenas uma pasta que corresponde aos critérios, o resultado da consulta é o mesmo que [ler subconjunto de arquivos na pasta](#read-subset-of-files-in-folder) e Ler todos os arquivos da pasta [específica](#read-all-files-from-specific-folder). Cenários de uso de curinga mais complexos são cobertos em [arquivos do Parquet de Consulta](query-parquet-files.md).

## <a name="next-steps"></a>Próximas etapas

Mais informações podem ser encontradas no artigo de arquivos específicos do In [Consulta.](query-specific-files.md)
