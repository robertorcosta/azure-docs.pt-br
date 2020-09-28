---
title: Pesquisar por blobs de texto sem formatação
titleSuffix: Azure Cognitive Search
description: Configure um indexador de pesquisa para extrair texto sem formatação de BLOBs do Azure para pesquisa de texto completo no Azure Pesquisa Cognitiva.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 2b01b9a3782d25660462d0cc2bf3aec87baf0023
ms.sourcegitcommit: dc68a2c11bae2e9d57310d39fbed76628233fd7f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/28/2020
ms.locfileid: "91403765"
---
# <a name="how-to-index-plain-text-blobs-in-azure-cognitive-search"></a>Como indexar blobs de texto sem formatação no Azure Pesquisa Cognitiva

Ao usar um [indexador de blob](search-howto-indexing-azure-blob-storage.md) para extrair texto pesquisável para pesquisa de texto completo, você pode invocar vários modos de análise para obter resultados de indexação melhores. Por padrão, o indexador analisa blobs de texto delimitados como uma única parte do texto. No entanto, se todos os seus BLOBs contiverem texto sem formatação na mesma codificação, você poderá melhorar significativamente o desempenho da indexação usando o **modo de análise de texto**.

## <a name="set-up-plain-text-indexing"></a>Configurar a indexação de texto sem formatação

Para indexar blobs de texto sem formatação, crie ou atualize uma definição de indexador com a `parsingMode` propriedade de configuração para `text` em uma solicitação [criar indexador](/rest/api/searchservice/create-indexer) :

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }
```

Por padrão, a codificação `UTF-8` será assumida. Para especificar uma codificação diferente, use a propriedade de configuração `encoding`: 

```http
    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }
```

## <a name="request-example"></a>Exemplo de solicitação

Os modos de análise são especificados na definição do indexador.

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-plaintext-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }
```

## <a name="help-us-make-azure-cognitive-search-better"></a>Ajude-nos a tornar o Azure Pesquisa Cognitiva melhor
Caso você tenha solicitações de recursos ou ideias para melhorias, dê sua opinião em [site UserVoice](https://feedback.azure.com/forums/263029-azure-search/). Se precisar de ajuda para usar o recurso existente, poste sua pergunta em [Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/18870).

## <a name="see-also"></a>Confira também

* [Indexadores na Pesquisa Cognitiva do Azure](search-indexer-overview.md)
* [Como configurar um indexador de BLOB](search-howto-indexing-azure-blob-storage.md)
* [Visão geral da indexação de BLOB](search-blob-storage-integration.md)