---
title: Tutorial de analista de dados – Usar o SQL sob demanda (versão prévia) para analisar os Azure Open Datasets no Azure Synapse Studio (versão prévia)
description: Neste tutorial, você aprenderá a executar facilmente a análise de dados exploratória combinando diferentes Azure Open Datasets usando o SQL sob demanda (versão prévia) e a visualizar os resultados no Azure Synapse Studio.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 1080001cb222f91503080914d7fb253e5ee82626
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81426275"
---
# <a name="use-sql-on-demand-preview-to-analyze-azure-open-datasets-and-visualize-the-results-in-azure-synapse-studio-preview"></a>Usar o SQL sob demanda (versão prévia) para analisar os Azure Open Datasets e visualizar os resultados no Azure Synapse Studio (versão prévia)

Neste tutorial, você aprenderá a executar a análise de dados exploratória combinando diferentes Azure Open Datasets usando o SQL sob demanda e a visualizar os resultados no Azure Synapse Studio.

Especificamente, você analisará o [conjunto de dados de Táxi de Nova York](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/), que inclui datas/horas de partida e chegada, locais de partida e de chegada, distâncias de corridas, tarifas discriminadas, tipos de taxa, tipos de pagamento e contagens de passageiros relatadas pelo motorista.

O foco da análise é encontrar tendências em alterações do número de corridas de táxi ao longo do tempo. Você analisa outros dois Azure Open Datasets ([Feriados Públicos](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) e [Dados Meteorológicos](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)) para entender os valores atípicos no número de corridas de táxi.

## <a name="create-credentials"></a>Criar credenciais

```sql
-- There is no secret. We are using public storage account which doesn't need a secret.
CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/nyctlc]
WITH IDENTITY='SHARED ACCESS SIGNATURE',
SECRET = ''
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer]
WITH IDENTITY='SHARED ACCESS SIGNATURE',
SECRET = ''
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer]
WITH IDENTITY='SHARED ACCESS SIGNATURE',
SECRET = ''
GO
```

## <a name="automatic-schema-inference"></a>Inferência de esquema automática

Já que os dados são armazenados no formato de arquivo Parquet, a inferência de esquema automática está disponível, portanto, é possível consultar facilmente os dados sem necessidade de listar os tipos de dados de todas as colunas nos arquivos. Além disso, é possível utilizar o mecanismo de coluna virtual e a função filepath para filtrar um determinado subconjunto de arquivos.

Primeiro, vamos nos familiarizar com os dados de táxi de Nova York executando a seguinte consulta:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
```

Temos abaixo o snippet de resultado para os dados de táxi de Nova York:

![snippet de resultado](./media/tutorial-data-analyst/1.png)

Da mesma forma, podemos consultar o conjunto de dados de feriados públicos usando a seguinte consulta:

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
        FORMAT='PARQUET'
    ) AS [holidays]
```

Temos abaixo o snippet de resultado para o conjunto de dados de feriados públicos:

![snippet de resultado 2](./media/tutorial-data-analyst/2.png)

Por fim, também podemos consultar o conjunto de dados meteorológicos usando a seguinte consulta:

```sql
SELECT
    TOP 100 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
```

Temos abaixo o snippet de resultado para o conjunto de dados meteorológicos:

![snippet de resultado 3](./media/tutorial-data-analyst/3.png)

Você pode saber mais sobre o significado das colunas individuais nas descrições dos conjunto de dados [Táxi de Nova York](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/), [Feriados Públicos](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/)e [Dados Meteorológicos](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/).

## <a name="time-series-seasonality-and-outlier-analysis"></a>Análise de série temporal, sazonalidade e exceções

Você pode resumir facilmente o número anual de corridas de táxi usando a seguinte consulta:

```sql
SELECT
    YEAR(tpepPickupDateTime) AS current_year,
    COUNT(*) AS rides_per_year
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) >= '2009' AND nyc.filepath(1) <= '2019'
GROUP BY YEAR(tpepPickupDateTime)
ORDER BY 1 ASC
```

Temos abaixo o snippet de resultado para o número anual de corridas de táxi:

![snippet de resultado 4](./media/tutorial-data-analyst/4.png)

Os dados podem ser visualizados no Synapse Studio, alternando da exibição de tabela para a exibição de gráfico. Você pode escolher entre diferentes tipos de gráfico (área, barras, colunas, linha, pizza e dispersão). Neste caso, vamos plotar o gráfico de colunas com a coluna Categoria definida para "current_year":

![visualização do resultado 5](./media/tutorial-data-analyst/5.png)

Nessa visualização, é possível ver claramente uma tendência de um número decrescente de corridas ao longo dos anos, supostamente devido a uma popularidade mais recente das empresas de transporte solidário.

> [!NOTE]
> No momento da elaboração deste tutorial, os dados de 2019 estão incompletos, portanto, há uma queda enorme no número de corridas para esse ano.

Em seguida, vamos concentrar nossa análise em um ano, por exemplo, 2016. A seguinte consulta retorna o número diário de corridas durante esse ano:

```sql
SELECT
    CAST([tpepPickupDateTime] AS DATE) AS [current_day],
    COUNT(*) as rides_per_day
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) = '2016'
GROUP BY CAST([tpepPickupDateTime] AS DATE)
ORDER BY 1 ASC
```

Temos abaixo o snippet de resultado para essa consulta:

![snippet de resultado 6](./media/tutorial-data-analyst/6.png)

Novamente, podemos visualizar os dados facilmente, plotando o gráfico de colunas com a coluna Categoria "current_day" e a coluna Legenda (série) "rides_per_day".

![visualização do resultado 7](./media/tutorial-data-analyst/7.png)

Da plotagem, pode ser observado que há um padrão semanal, com o pico no sábado. Durante os meses de verão, há menos corridas de táxi devido ao período de férias. No entanto, há também algumas quedas significativas no número de corridas de táxi sem um padrão claro de quando e por que elas ocorrem.

Veremos abaixo se essas quedas estão potencialmente correlacionadas com feriados públicos unindo as corridas de táxi de Nova York com o conjunto de dados de feriados públicos:

```sql
WITH taxi_rides AS
(
    SELECT
        CAST([tpepPickupDateTime] AS DATE) AS [current_day],
        COUNT(*) as rides_per_day
    FROM  
        OPENROWSET(
            BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT='PARQUET'
        ) AS [nyc]
    WHERE nyc.filepath(1) = '2016'
    GROUP BY CAST([tpepPickupDateTime] AS DATE)
),
public_holidays AS
(
    SELECT
        holidayname as holiday,
        date
    FROM
        OPENROWSET(
            BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
            FORMAT='PARQUET'
        ) AS [holidays]
    WHERE countryorregion = 'United States' AND YEAR(date) = 2016
)
SELECT
*
FROM taxi_rides t
LEFT OUTER JOIN public_holidays p on t.current_day = p.date
ORDER BY current_day ASC
```

![visualização do resultado 8](./media/tutorial-data-analyst/8.png)

Desta vez, queremos realçar o número de corridas de táxi durante os feriados públicos. Para essa finalidade, escolheremos "nenhum" para a coluna Categoria e "rides_per_day" e "holiday" como as colunas de Legenda (série).

![visualização do resultado 9](./media/tutorial-data-analyst/9.png)

Na plotagem, é possível ver claramente que, durante feriados públicos, o número de corridas de táxi é menor. No entanto, ainda há uma queda enorme não explicada em 23 de janeiro. Vamos verificar o clima em Nova York nesse dia consultando o conjunto de dados meteorológicos:

```sql
SELECT
    AVG(windspeed) AS avg_windspeed,
    MIN(windspeed) AS min_windspeed,
    MAX(windspeed) AS max_windspeed,
    AVG(temperature) AS avg_temperature,
    MIN(temperature) AS min_temperature,
    MAX(temperature) AS max_temperature,
    AVG(sealvlpressure) AS avg_sealvlpressure,
    MIN(sealvlpressure) AS min_sealvlpressure,
    MAX(sealvlpressure) AS max_sealvlpressure,
    AVG(precipdepth) AS avg_precipdepth,
    MIN(precipdepth) AS min_precipdepth,
    MAX(precipdepth) AS max_precipdepth,
    AVG(snowdepth) AS avg_snowdepth,
    MIN(snowdepth) AS min_snowdepth,
    MAX(snowdepth) AS max_snowdepth
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
WHERE countryorregion = 'US' AND CAST([datetime] AS DATE) = '2016-01-23' AND stationname = 'JOHN F KENNEDY INTERNATIONAL AIRPORT'
```

![visualização do resultado 10](./media/tutorial-data-analyst/10.png)

Os resultados da consulta indicam que a queda no número de corridas de táxi foi devido:

- à nevasca naquele dia em Nova York, pois nevou intensamente (aprox. 30 cm)
- ao clima frio daquele dia (temperatura inferior a zero graus Celsius)
- e ao vento (aprox. 10 m/s)

Este tutorial mostrou como o analista de dados pode executar rapidamente a análise de dados exploratórios, combinar facilmente diferentes conjuntos de dados usando o SQL sob demanda e visualizar os resultados usando o Azure Synapse Studio.

## <a name="next-steps"></a>Próximas etapas

Examine o artigo [Conectar o SQL sob demanda ao Power BI Desktop e criar um relatório](tutorial-connect-power-bi-desktop.md) artigo para saber como conectar o SQL sob demanda ao Power BI Desktop e criar relatórios.
 
