---
title: Ponto de extremidade da Pesquisa de Notícias do Bing
titleSuffix: Azure Cognitive Services
description: Este artigo fornece um resumo dos pontos finais da API de pesquisa de notícias; notícias, notícias de primeira linha e notícias de tendências.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: dc7d16fe809e3e324f384b0d9e088dd7e6ab261c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74111491"
---
# <a name="bing-news-search-api-endpoints"></a>Pontos de extremidade da API de Pesquisa de Notícias do Bing

A **API de Pesquisa de Notícias** retorna artigos de notícias, páginas da Web, imagens, vídeos e [entidades](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). As entidades contêm informações de resumo sobre uma pessoa, lugar ou tópico.

## <a name="endpoints"></a>Pontos de extremidade

Para obter resultados da pesquisa de notícias usando a API de Pesquisa de Notícias do Bing, envie uma solicitação `GET` para um dos seguintes pontos de extremidade. Os cabeçalhos e parâmetros de URL definem mais especificações.

### <a name="news-items-by-search-query"></a>Itens de notícias por consulta de pesquisa

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

Retorna itens de notícias com base em uma consulta de pesquisa. Se a consulta de pesquisa estiver vazia, a API retornará os principais artigos de notícias de categorias diferentes. Envie uma consulta codificando seu termo de pesquisa em URL e acrescentando-o ao parâmetro `q=""`. Para disponibilidade, consulte [Países/regiões e mercados suportados.](language-support.md#supported-markets-for-news-search-endpoint)

### <a name="top-news-items-by-category"></a>Principais itens de notícia por categoria

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

Retorna os principais itens de notícias por categoria. Você pode solicitar especificamente os principais artigos de negócios, esportes ou entretenimento usando `category=business`, `category=sports`, ou `category=entertainment`. O parâmetro `category` só pode ser usado com a URL `/news`. Há alguns requisitos formais para especificar as categorias; consulte `category` na documentação [parâmetro de consulta](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query-parameters). Envie uma consulta codificando seu termo de pesquisa em URL e acrescentando-o ao parâmetro `q=""`. Para disponibilidade, consulte [Países/regiões e mercados suportados.](language-support.md#supported-markets-for-news-endpoint)

### <a name="trending-news-topics"></a>Tópicos de notícias populares 

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

Retorna tópicos de notícias que são populares atualmente em redes sociais. Quando a opção `/trendingtopics` estiver incluída, a pesquisa do Bing ignora vários outros parâmetros, como `freshness` e `?q=""`. Para disponibilidade, consulte [Países/regiões e mercados suportados.](language-support.md#supported-markets-for-news-trending-endpoint)

## <a name="next-steps"></a>Próximas etapas

Para obter detalhes sobre cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros, etc., consulte a referência [API de Pesquisa de Notícias do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference).

Para obter informações completas sobre os parâmetros compatíveis com cada ponto de extremidade, confira as páginas de referência de cada tipo.
Para obter exemplos de solicitações básicas usando a API de Pesquisa de Notícias, consulte [Início rápido sobre pesquisa de notícias do Bing](https://docs.microsoft.com/azure/cognitive-services/bing-news-search).
