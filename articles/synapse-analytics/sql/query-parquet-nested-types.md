---
title: Consultar tipos aninhados do Parquet usando SQL sob demanda (versão prévia)
description: Neste artigo, você aprenderá a consultar os tipos aninhados do Parquet.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: bf2dbf501b5cd3b6cd0ab6b0e9bbbc2208c98a58
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85478443"
---
# <a name="query-parquet-nested-types-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Consultar tipos aninhados do Parquet usando o SQL sob demanda (versão prévia) no Azure Synapse Analytics

Neste artigo, você aprenderá a escrever uma consulta usando o SQL sob demanda (versão prévia) no Azure Synapse Analytics.  Essa consulta lerá os tipos aninhados do Parquet.

## <a name="prerequisites"></a>Pré-requisitos

A primeira etapa é **criar um banco de dados** com uma fonte de dados que faça referências. Em seguida, inicialize os objetos executando o [script de instalação](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) nesse banco de dados. Esse script de instalação criará as fontes de dados, as credenciais no escopo do banco de dados e os formatos de arquivo externos que são usados nestas amostras.

## <a name="project-nested-or-repeated-data"></a>Dados de projeto aninhados ou repetidos

A consulta a seguir lê o arquivo *justSimpleArray.parquet*. Ela projeta todas as colunas do arquivo Parquet, incluindo dados aninhados ou repetidos.

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

## <a name="access-elements-from-nested-columns"></a>Elementos de acesso de colunas aninhadas

A consulta a seguir lê o arquivo *structExample. parquet* e mostra como Surface elementos de uma coluna aninhada. Você tem duas maneiras de referenciar o valor aninhado:
- Especificando a expressão de caminho de valor aninhado após especificação de tipo.
- Formatando o nome da coluna como um caminho aninhado usando "." para fazer referência aos campos.

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'parquet/nested/structExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    )
    WITH (
        -- you can see original nested columns values by uncommenting lines below
        --DateStruct VARCHAR(8000),
        [DateValue] DATE '$.DateStruct.Date',
        --TimeStruct VARCHAR(8000),
        [TimeStruct.Time] TIME,
        --TimestampStruct VARCHAR(8000),
        [TimestampStruct.Timestamp] DATETIME2,
        --DecimalStruct VARCHAR(8000),
        DecimalValue DECIMAL(18, 5) '$.DecimalStruct.Decimal',
        --FloatStruct VARCHAR(8000),
        [FloatStruct.Float] FLOAT
    ) AS [r];
```

## <a name="access-elements-from-repeated-columns"></a>Acessar elementos de colunas repetidas

A consulta a seguir lê o arquivo *justSimpleArray.parquet* e usa [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) para recuperar um elemento **escalar** de uma coluna repetida, como uma Matriz ou Mapa:

```sql
SELECT
    *,
    JSON_VALUE(SimpleArray, '$[0]') AS FirstElement,
    JSON_VALUE(SimpleArray, '$[1]') AS SecondElement,
    JSON_VALUE(SimpleArray, '$[2]') AS ThirdElement
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

A consulta a seguir lê o arquivo *mapExample.parquet* e usa [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) para recuperar um elemento **não escalar** de uma coluna repetida, como uma Matriz ou Mapa:

```sql
SELECT
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

Você também pode referenciar explicitamente as colunas que deseja retornar na `WITH` cláusula:

```sql
SELECT DocId,
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) 
    WITH (DocId bigint, MapOfPersons VARCHAR(max)) AS [r];
```

A estrutura `MakOfPersons` é retornada como `VARCHAR` coluna e formatada como cadeia de caracteres JSON.

## <a name="projecting-values-from-repeated-columns"></a>Projetando valores de colunas repetidas

Se você tiver uma matriz de valores escalares (por exemplo `[1,2,3]` ) em algumas colunas, poderá expandi-los facilmente e associá-los com a linha principal usando o seguinte script:

```sql
SELECT
    SimpleArray, Element
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS arrays
    CROSS APPLY OPENJSON (SimpleArray) WITH (Element int '$') as array_values
```

## <a name="next-steps"></a>Próximas etapas

O próximo artigo mostrará como [Consultar arquivos JSON](query-json-files.md).
