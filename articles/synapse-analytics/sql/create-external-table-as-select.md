---
title: Armazenar os resultados da consulta no armazenamento
description: Neste artigo, você aprenderá a armazenar os resultados da consulta no armazenamento usando o SQL sob demanda (versão prévia).
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 0e5fda5e7a5caf72df3d4c80e37a684232ca5541
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91289354"
---
# <a name="store-query-results-to-storage-using-sql-on-demand-preview-using-azure-synapse-analytics"></a>Armazenar os resultados da consulta no armazenamento usando o SQL sob demanda (versão prévia) e o Azure Synapse Analytics

Neste artigo, você aprenderá a armazenar os resultados da consulta no armazenamento usando o SQL sob demanda (versão prévia).

## <a name="prerequisites"></a>Pré-requisitos

Sua primeira etapa é **criar um banco de dados** em que você executará as consultas. Em seguida, inicialize os objetos executando o [script de instalação](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) nesse banco de dados. Esse script de instalação criará as fontes de dados, as credenciais no escopo do banco de dados e os formatos de arquivo externos que são usados para ler os dados nestas amostras.

Siga as instruções deste artigo para criar fontes de dados, credenciais no escopo do banco de dados e formatos de arquivo externos que são usados para gravar dados no armazenamento de saída.

## <a name="create-external-table-as-select"></a>Create external table as select

Você pode usar a instrução CETAS (CREATE EXTERNAL TABLE AS SELECT) para armazenar os resultados da consulta no armazenamento.

> [!NOTE]
> Altere a primeira linha da consulta, ou seja, [mydbname], de modo que você esteja usando o banco de dados criado.

```sql
USE [mydbname];
GO

CREATE DATABASE SCOPED CREDENTIAL [SasTokenWrite]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
     SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO

CREATE EXTERNAL DATA SOURCE [MyDataSource] WITH (
    LOCATION = 'https://<storage account name>.blob.core.windows.net/csv', CREDENTIAL = [SasTokenWrite]
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
        BULK 'csv/population-unix/population.csv',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT = 'CSV', PARSER_VERSION = '2.0'
    ) WITH (
        CountryCode varchar(4),
        CountryName varchar(64),
        Year int,
        PopulationCount int
    ) AS r;

```

## <a name="use-the-external-table"></a>Usar a tabela externa

Você pode usar a tabela externa criada por meio da CETAS como uma tabela externa normal.

> [!NOTE]
> Altere a primeira linha da consulta, ou seja, [mydbname], de modo que você esteja usando o banco de dados criado.

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

Para obter mais informações sobre como consultar diferentes tipos de arquivo, confira os artigos [Consultar um arquivo CSV individual](query-single-csv-file.md), [Consultar arquivos Parquet](query-parquet-files.md) e [Consultar arquivos JSON](query-json-files.md).
