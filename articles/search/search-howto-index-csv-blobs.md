---
title: Pesquisar em BLOBs CSV
titleSuffix: Azure Cognitive Search
description: Extraia e importe CSV do armazenamento de BLOBs do Azure usando o modo de análise delimitedText, atualmente em visualização pública.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 2166e100f03f21c218618d19dc37ee70c6ab29ef
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113038"
---
# <a name="how-to-index-csv-blobs-using-delimitedtext-parsing-mode-and-blob-indexers-in-azure-cognitive-search"></a>Como indexar BLOBs CSV usando o modo de análise de delimitedText e indexadores de blob no Azure Pesquisa Cognitiva 

> [!IMPORTANT] 
> O modo de análise delimitedText está atualmente em visualização pública. A funcionalidade de versão prévia é fornecida sem um Contrato de Nível de Serviço e, portanto, não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [API REST versão 2019-05-06-versão prévia](search-api-preview.md) fornece esse recurso. No momento, não há suporte para Portal ou SDK do .NET.

Por padrão, o [indexador de blob pesquisa cognitiva do Azure](search-howto-indexing-azure-blob-storage.md) analisa blobs de texto delimitados como uma única parte do texto. No entanto, com blobs contendo dados CSV, o ideal é tratar cada linha no blob como um documento separado. Por exemplo, considerando o seguinte texto delimitado, você pode querer analisá-lo em dois documentos, cada um contendo os campos "id", "datePublished" e "tags": 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

Neste artigo, você aprenderá a analisar BLOBs CSV com um indexador de blob Pesquisa Cognitiva do Azure definindo o modo de análise de `delimitedText`. 

> [!NOTE]
> Siga as recomendações de configuração do indexador em [indexação de um para muitos](search-howto-index-one-to-many-blobs.md) para produzir vários documentos de pesquisa de um blob do Azure.

## <a name="setting-up-csv-indexing"></a>Configurando a indexação de CSV
Para indexar BLOBs CSV, crie ou atualize uma definição de indexador com o modo de análise de `delimitedText` em uma solicitação [criar indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer) :

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

`firstLineContainsHeaders` indica que a primeira linha (não vazia) de cada blob contém cabeçalhos.
Se os blobs não contêm uma linha de cabeçalho inicial, os cabeçalhos devem ser especificados na configuração do indexador: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

É possível personalizar o caractere delimitador usando a configuração `delimitedTextDelimiter`. Por exemplo:

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> Atualmente, apenas a codificação UTF-8 tem suporte. Se você precisar de suporte para outras codificações, vote nele em [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> Quando você usa o modo de análise de texto delimitado, o Azure Pesquisa Cognitiva pressupõe que todos os BLOBs em sua fonte de dados serão CSV. Se você precisar dar suporte a uma combinação de blobs CSV e não CSV na mesma fonte de dados, vote nela em [UserVoice](https://feedback.azure.com/forums/263029-azure-search).
> 
> 

## <a name="request-examples"></a>Exemplos de solicitação
Reunindo tudo isso, estes são os exemplos de carga completa. 

Fonte de dados: 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

Indexador:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-cognitive-search-better"></a>Ajude-nos a tornar o Azure Pesquisa Cognitiva melhor
Caso você tenha solicitações de recursos ou ideias para melhorias, dê sua opinião em [site UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

