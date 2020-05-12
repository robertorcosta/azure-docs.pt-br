---
title: Consultar arquivos JSON usando o SQL sob demanda (versão prévia)
description: Esta seção explica como ler arquivos JSON usando o SQL sob demanda no Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 4c1fe9ac5d3b2470fb70231a83e57f3e08d0dfb1
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83197600"
---
# <a name="query-json-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Consultar arquivos JSON usando o SQL sob demanda (versão prévia) no Azure Synapse Analytics

Neste artigo, você aprenderá a escrever uma consulta usando o SQL sob demanda (versão prévia) no Azure Synapse Analytics. O objetivo da consulta é ler arquivos JSON. Os formatos com suporte são listados em [OPENROWSET](develop-openrowset.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de ler o restante deste artigo, examine os seguintes artigos:

- [Configuração inicial](query-data-storage.md#first-time-setup)
- [Pré-requisitos](query-data-storage.md#prerequisites)

## <a name="sample-json-files"></a>Arquivos JSON de exemplo

A seção a seguir contém scripts de exemplo para ler arquivos JSON. Os arquivos são armazenados em um contêiner *JSON* , em *livros*de pastas e contêm uma única entrada de livro com a seguinte estrutura:

```json
{
   "_id":"ahokw88",
   "type":"Book",
   "title":"The AWK Programming Language",
   "year":"1988",
   "publisher":"Addison-Wesley",
   "authors":[
      "Alfred V. Aho",
      "Brian W. Kernighan",
      "Peter J. Weinberger"
   ],
   "source":"DBLP"
}
```

## <a name="read-json-files"></a>Ler arquivos JSON

Para processar arquivos JSON usando JSON_VALUE e [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), você precisa ler o arquivo JSON do armazenamento como uma única coluna. O script a seguir lê o arquivo *book1. JSON* como uma única coluna:

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/book1.json',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent varchar(8000)
    ) AS [r];
```

> [!NOTE]
> Você está lendo o arquivo JSON inteiro como uma única linha ou coluna. Portanto, FIELDTERMINATOR, FIELDQUOTE e timeterminator são definidos como 0x0B.

## <a name="query-json-files-using-json_value"></a>Consultar arquivos JSON usando JSON_VALUE

A consulta a seguir mostra como usar [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) para recuperar valores escalares (título, Publicador) de um livro intitulado *probabilística e métodos estatísticos em Cryptology, uma introdução dos artigos selecionados*:

```sql
SELECT
    JSON_VALUE(jsonContent, '$.title') AS title,
    JSON_VALUE(jsonContent, '$.publisher') as publisher,
    jsonContent
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent varchar(8000)
    ) AS [r]
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="query-json-files-using-json_query"></a>Consultar arquivos JSON usando JSON_QUERY

A consulta a seguir mostra como usar [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) para recuperar objetos e matrizes (autores) de um livro intitulado *probabilística e métodos estatísticos no Cryptology, uma introdução dos tópicos selecionados*:

```sql
SELECT
    JSON_QUERY(jsonContent, '$.authors') AS authors,
    jsonContent
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent varchar(8000)
    ) AS [r]
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="query-json-files-using-openjson"></a>Consultar arquivos JSON usando OPENJSON

A consulta a seguir usa [OPENJSON](/sql/t-sql/functions/openjson-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Ele recuperará objetos e propriedades em um livro intitulado *probabilística e métodos estatísticos no Cryptology, uma introdução dos artigos selecionados*:

```sql
SELECT
    j.*
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent NVARCHAR(max) -- Use appropriate length. Make sure JSON fits. 
    ) AS [r]
CROSS APPLY OPENJSON(jsonContent) AS j
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="next-steps"></a>Próximas etapas

Os próximos artigos desta série demonstram como:

- [Consultando pastas e vários arquivos](query-folders-multiple-csv-files.md)
- [Criar e usar modos de exibição](create-use-views.md)
