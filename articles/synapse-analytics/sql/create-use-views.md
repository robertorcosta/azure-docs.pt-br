---
title: Criar e usar exibições no SQL sob demanda (versão prévia)
description: Nesta seção, você aprenderá a criar e usar exibições para encapsular consultas do SQL sob demanda (versão prévia). As exibições permitirão que você reutilize essas consultas. As exibições também serão necessárias se você quiser usar ferramentas, como o Power BI, em conjunto com o SQL sob demanda.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0f5323193706fdd00739be6c71a4fe12cfedf21b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420770"
---
# <a name="create-and-use-views-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>Criar e usar exibições no SQL sob demanda (versão prévia) usando o Azure Synapse Analytics

Nesta seção, você aprenderá a criar e usar exibições para encapsular consultas do SQL sob demanda (versão prévia). As exibições permitirão que você reutilize essas consultas. As exibições também serão necessárias se você quiser usar ferramentas, como o Power BI, em conjunto com o SQL sob demanda.

## <a name="prerequisites"></a>Pré-requisitos

A primeira etapa é examinar os artigos abaixo e verificar se você atendeu aos pré-requisitos para criar e usar exibições do SQL sob demanda:

- [Configuração inicial](query-data-storage.md#first-time-setup)
- [Pré-requisitos](query-data-storage.md#prerequisites)

## <a name="create-a-view"></a>Criar uma exibição

Você pode criar exibições da mesma maneira que cria exibições convencionais do SQL Server. A consulta abaixo cria uma exibição que lê o arquivo *population.csv*.

> [!NOTE]
> Altere a primeira linha da consulta, ou seja, [mydbname], de modo que você esteja usando o banco de dados criado. Caso você não tenha criado um banco de dados, leia [Configuração inicial](query-data-storage.md#first-time-setup).

```sql
USE [mydbname];
GO

DROP VIEW IF EXISTS populationView;
GO

CREATE VIEW populationView AS
SELECT * 
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv',
         FORMAT = 'CSV', 
        FIELDTERMINATOR =',', 
        ROWTERMINATOR = '\n'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r];
```

## <a name="use-a-view"></a>Usar uma exibição

Você pode usar exibições em suas consultas da mesma maneira que usa exibições em consultas do SQL Server.

A consulta a seguir demonstra o uso da exibição *population_csv* que criamos em [Criar uma exibição](#create-a-view). Ela retorna nomes de países com o respectivo número de habitantes em 2019 em ordem decrescente.

> [!NOTE]
> Altere a primeira linha da consulta, ou seja, [mydbname], de modo que você esteja usando o banco de dados criado. Caso você não tenha criado um banco de dados, leia [Configuração inicial](query-data-storage.md#first-time-setup).

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationView
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre como consultar diferentes tipos de arquivo, veja os artigos [Consultar um arquivo CSV individual](query-single-csv-file.md), [Consultar arquivos Parquet](query-parquet-files.md) e [Consultar arquivos JSON](query-json-files.md).
