---
title: Armazenar os resultados da consulta no armazenamento
description: Neste artigo, você aprenderá a armazenar os resultados da consulta no armazenamento usando o SQL sob demanda (versão prévia).
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: dd7666bb9f22214fb4701e6be9edc171912d9bf9
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691859"
---
# <a name="store-query-results-to-storage-using-sql-on-demand-preview-using-azure-synapse-analytics"></a>Armazenar os resultados da consulta no armazenamento usando o SQL sob demanda (versão prévia) e o Azure Synapse Analytics

Neste artigo, você aprenderá a armazenar os resultados da consulta no armazenamento usando o SQL sob demanda (versão prévia).

## <a name="prerequisites"></a>Pré-requisitos

A primeira etapa é examinar os artigos abaixo e verificar se você atendeu aos pré-requisitos:

- [Configuração inicial](query-data-storage.md#first-time-setup)
- [Pré-requisitos](query-data-storage.md#prerequisites)

## <a name="create-external-table-as-select"></a>Create external table as select

Você pode usar a instrução CETAS (CREATE EXTERNAL TABLE AS SELECT) para armazenar os resultados da consulta no armazenamento.

> [!NOTE]
> Altere a primeira linha da consulta, ou seja, [mydbname], de modo que você esteja usando o banco de dados criado. Caso você não tenha criado um banco de dados, leia [Configuração inicial](query-data-storage.md#first-time-setup). Você precisa alterar a LOCALIZAÇÃO para que a fonte de dados externa MyDataSource aponte para a localização para a qual você tem permissão de gravação. 

```sql
USE [mydbname];
GO

CREATE EXTERNAL DATA SOURCE [MyDataSource] WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net/csv'
);
GO

CREATE EXTERNAL FILE FORMAT [ParquetFF] WITH (
    FORMAT_TYPE = PARQUET,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
);
GO

CREATE EXTERNAL TABLE [dbo].[PopulationCETAS] WITH (
        LOCATION = 'populationParquet/',
        DATA_SOURCE = [MyDataSource],
        FILE_FORMAT = [ParquetFF]
) AS
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population-unix/population.csv',
        FORMAT='CSV'
    ) WITH (
        CountryCode varchar(4),
        CountryName varchar(64),
        Year int,
        PopulationCount int
    ) AS r;

```

## <a name="use-a-external-table-created"></a>Usar uma tabela externa criada

Você pode usar a tabela externa criada por meio de CETAS como uma tabela externa normal.

> [!NOTE]
> Altere a primeira linha da consulta, ou seja, [mydbname], de modo que você esteja usando o banco de dados criado. Caso você não tenha criado um banco de dados, leia [Configuração inicial](query-data-storage.md#first-time-setup).

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM PopulationCETAS
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre como consultar diferentes tipos de arquivo, veja os artigos [Consultar um arquivo CSV individual](query-single-csv-file.md), [Consultar arquivos Parquet](query-parquet-files.md) e [Consultar arquivos JSON](query-json-files.md).
