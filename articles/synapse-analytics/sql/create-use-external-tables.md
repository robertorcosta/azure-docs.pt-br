---
title: Criar e usar tabelas externas no SQL sob demanda (versão prévia)
description: Nesta seção, você aprenderá a criar e usar tabelas externas no SQL sob demanda (versão prévia). As tabelas externas são úteis quando você deseja controlar o acesso a dados externos no SQL sob demanda e se deseja usar ferramentas, como o Power BI, em conjunto com o SQL sob demanda.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: a708e5e713e62ab5b7ca70b61235a84830b051cd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420790"
---
# <a name="create-and-use-external-tables-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>Criar e usar tabelas externas no SQL sob demanda (versão prévia) usando o Azure Synapse Analytics

Nesta seção, você aprenderá a criar e usar tabelas externas no SQL sob demanda (versão prévia). As tabelas externas são úteis quando você deseja controlar o acesso a dados externos no SQL sob demanda e se deseja usar ferramentas, como o Power BI, em conjunto com o SQL sob demanda.

## <a name="prerequisites"></a>Pré-requisitos

A primeira etapa é examinar os artigos abaixo e verificar se você atendeu aos pré-requisitos para criar e usar tabelas externas do SQL sob demanda:

- [Configuração inicial](query-data-storage.md#first-time-setup)
- [Pré-requisitos](query-data-storage.md#prerequisites)

## <a name="create-an-external-table"></a>Criar uma tabela externa

Você pode criar tabelas externas da mesma maneira que cria tabelas externas convencionais do SQL Server. A consulta abaixo cria uma tabela externa que lê o arquivo *population.csv*.

> [!NOTE]
> Altere a primeira linha da consulta, ou seja, [mydbname], de modo que você esteja usando o banco de dados criado. Caso você não tenha criado um banco de dados, leia [Configuração inicial](query-data-storage.md#first-time-setup).

```sql
USE [mydbname];
GO

CREATE EXTERNAL DATA SOURCE [CsvDataSource] WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net/csv'
);
GO

CREATE EXTERNAL FILE FORMAT CSVFileFormat
WITH (  
    FORMAT_TYPE = DELIMITEDTEXT,
    FORMAT_OPTIONS (
        FIELD_TERMINATOR = ',',
        STRING_DELIMITER = '"',
        FIRST_ROW = 2
    )
);
GO

CREATE EXTERNAL TABLE populationExternalTable
(
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
);
WITH (
    LOCATION = 'population/population.csv',
    DATA_SOURCE = CsvDataSource,
    FILE_FORMAT = CSVFileFormat
);
GO
```

## <a name="use-a-external-table"></a>Usar uma tabela externa

Você pode usar tabelas externas nas suas consultas da mesma maneira que as usa em consultas do SQL Server.

A consulta a seguir demonstra como usar a tabela externa *population* que criamos na seção [Criar uma tabela externa](#create-an-external-table). Ela retorna nomes de países com o respectivo número de habitantes em 2019 em ordem decrescente.

> [!NOTE]
> Altere a primeira linha da consulta, ou seja, [mydbname], de modo que você esteja usando o banco de dados criado. Caso você não tenha criado um banco de dados, leia [Configuração inicial](query-data-storage.md#first-time-setup).

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationExternalTable
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre como armazenar os resultados de uma consulta no armazenamento, veja [Armazenar os resultados da consulta no armazenamento](../sql/create-external-table-as-select.md).
