---
title: Pastas de consulta e vários arquivos CSV usando o SQL sob demanda (versão prévia)
description: O SQL sob demanda (versão prévia) dá suporte à leitura de vários arquivos/pastas usando curingas, que são semelhantes aos curingas usados no sistema operacional Windows.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 8f8af7fab7113e38b91c3f5f1bcc41b4e4fba2c1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457358"
---
# <a name="query-folders-and-multiple-csv-files"></a>Consultar pastas e vários arquivos CSV  

Neste artigo, você aprenderá a escrever uma consulta usando o SQL sob demanda (versão prévia) no Azure Synapse Analytics.

O SQL sob demanda dá suporte à leitura de vários arquivos/pastas usando curingas, que são semelhantes aos curingas usados no sistema operacional Windows. No entanto, há maior flexibilidade, já que vários curingas são permitidos.

## <a name="prerequisites"></a>Pré-requisitos

Antes de ler o restante deste artigo, certifique-se de examinar os artigos listados abaixo:

- [Configuração inicial](query-data-storage.md#first-time-setup)
- [Pré-requisitos](query-data-storage.md#prerequisites)

## <a name="read-multiple-files-in-folder"></a>Ler vários arquivos na pasta

Você usará a pasta *CSV/táxi* para seguir as consultas de exemplo. Ele contém dados de NYC táxi-amarelo de táxi, de julho de 2016 a junho de 2018.

Os arquivos em *CSV/táxi* são nomeados após o ano e o mês:

- yellow_tripdata_2016 -07. csv
- yellow_tripdata_2016 -08. csv
- yellow_tripdata_2016 -09. csv
- ...
- yellow_tripdata_2018 -04. csv
- yellow_tripdata_2018 -05. csv
- yellow_tripdata_2018-06. csv

Cada arquivo tem a seguinte estrutura:
        
    [First 10 rows of the CSV file](./media/querying-folders-and-multiple-csv-files/nyc-taxi.png)

## <a name="read-all-files-in-folder"></a>Ler todos os arquivos na pasta
    
O exemplo a seguir lê todos os arquivos de dados de táxi amarelo NYC da pasta *CSV/táxi* e retorna o número total de passageiros e corridas por ano. Ele também mostra o uso de funções de agregação.

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
> Todos os arquivos acessados com o OPENROWSET único devem ter a mesma estrutura (ou seja, número de colunas e seus tipos de dados).

### <a name="read-subset-of-files-in-folder"></a>Ler subconjunto de arquivos na pasta

O exemplo a seguir lê os arquivos de dados de táxi de 2017 NYC da pasta *CSV/táxi* usando um curinga e retorna o valor de tarifa total por tipo de pagamento.

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
> Todos os arquivos acessados com o OPENROWSET único devem ter a mesma estrutura (ou seja, número de colunas e seus tipos de dados).

## <a name="read-folders"></a>Ler pastas

O caminho que você fornece para OPENROWSET também pode ser um caminho para uma pasta. As seções a seguir incluem esses tipos de consulta.

### <a name="read-all-files-from-specific-folder"></a>Ler todos os arquivos de uma pasta específica

Você pode ler todos os arquivos em uma pasta usando o curinga de nível de arquivo, conforme mostrado em [ler todos os arquivos na pasta](#read-all-files-in-folder). No entanto, há uma maneira de consultar uma pasta e consumir todos os arquivos dentro dessa pasta.

Se o caminho fornecido no OPENROWSET apontar para uma pasta, todos os arquivos nessa pasta serão usados como uma fonte para a consulta. A consulta a seguir lerá todos os arquivos na pasta *CSV/táxi* .

> [!NOTE]
> Observe a existência do/no final do caminho na consulta abaixo. Ele denota uma pasta. Se o/for omitido, a consulta irá direcionar um arquivo chamado *táxi* em vez disso.

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
> Todos os arquivos acessados com o OPENROWSET único devem ter a mesma estrutura (ou seja, número de colunas e seus tipos de dados).

### <a name="read-all-files-from-multiple-folders"></a>Ler todos os arquivos de várias pastas

É possível ler arquivos de várias pastas usando um caractere curinga. A consulta a seguir lerá todos os arquivos de todas as pastas localizadas na pasta *CSV* que têm nomes começando com *t* e terminando com *i*.

> [!NOTE]
> Observe a existência do/no final do caminho na consulta abaixo. Ele denota uma pasta. Se o/for omitido, a consulta irá direcionar os arquivos chamados *t&ast;i* .

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
> Todos os arquivos acessados com o OPENROWSET único devem ter a mesma estrutura (ou seja, número de colunas e seus tipos de dados).

Como você tem apenas uma pasta que corresponde aos critérios, o resultado da consulta é o mesmo que [ler todos os arquivos na pasta](#read-all-files-in-folder).

## <a name="multiple-wildcards"></a>Vários curingas

Você pode usar vários curingas em diferentes níveis de caminho. Por exemplo, você pode enriquecer a consulta anterior para ler arquivos somente com 2017 dados, de todas as pastas que os nomes começam com *t* e terminar com *i*.

> [!NOTE]
> Observe a existência do/no final do caminho na consulta abaixo. Ele denota uma pasta. Se o/for omitido, a consulta irá direcionar os arquivos chamados *t&ast;i* .
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
> Todos os arquivos acessados com o OPENROWSET único devem ter a mesma estrutura (ou seja, número de colunas e seus tipos de dados).

Como você tem apenas uma pasta que corresponde aos critérios, o resultado da consulta é o mesmo que [ler o subconjunto de arquivos na pasta](#read-subset-of-files-in-folder) e [ler todos os arquivos de uma pasta específica](#read-all-files-from-specific-folder). Cenários de uso de curinga mais complexos são abordados em [arquivos de consulta parquet](query-parquet-files.md).

## <a name="next-steps"></a>Próximas etapas

Mais informações podem ser encontradas no artigo em [arquivos específicos de consulta](query-specific-files.md) .
