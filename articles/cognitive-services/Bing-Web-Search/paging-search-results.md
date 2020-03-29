---
title: Como fazer a página através dos resultados da pesquisa - Bing Search APIs
titleSuffix: Azure Cognitive Services
description: Aprenda a fazer a página através dos resultados de pesquisa das APIs de Pesquisa de Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: aahi
ms.openlocfilehash: ea883bb294a8769b3c9be1e0eafc2e3e7c811b48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73481743"
---
# <a name="how-to-page-through-results-from-the-bing-search-apis"></a>Como fazer a página através dos resultados das APIs de Pesquisa de Bing

Quando você envia uma chamada para as APIs de pesquisa de bing, custom, image, news ou video search, Bing retorna um subconjunto do número total de resultados que podem ser relevantes para a consulta. Para obter o número total estimado de resultados `totalEstimatedMatches` disponíveis, acesse o campo do objeto de resposta. 

Por exemplo:  

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    }
}  
```

## <a name="paging-through-search-results"></a>Paginando através dos resultados da pesquisa

Para fazer a página através `count` `offset` dos resultados disponíveis, use os parâmetros e consulta ao enviar sua solicitação.  

> [!NOTE]
>
> * A paginação com as APIs bing video, image`/video/search`e news`/news/search`aplica-se`/image/search`apenas às pesquisas de vídeo geral ( ), notícias ( ) e imagem ( ) . A paginação através de tópicos e categorias de tendências não é suportada.  
> * O `TotalEstimatedMatches` campo é uma estimativa do número total de resultados de pesquisa para a consulta atual. Quando você `count` define `offset` os parâmetros, essa estimativa pode mudar.

| Parâmetro | Descrição                                                                                                                                                                |
|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `count`   | Especifica o número de resultados a serem retornados na resposta. Observe que o `count`valor padrão de , e o número máximo de resultados que você pode solicitar varia de acordo com a API. Você pode encontrar esses valores na documentação de referência em [Próximaetapas](#next-steps). |
| `offset`  | Especifica o número de resultados para ignorar. O `offset` é baseado em zero e deve ser menor que (`totalEstimatedMatches` - `count`).                                           |

Como exemplo, se você quiser exibir 15 resultados `count` por página, `offset` você definiria para 15 e para 0 para obter a primeira página de resultados. Para cada chamada de API `offset` subseqüente, você aumentaria em 15. O exemplo a seguir solicita 15 páginas da Web começando no deslocamento 45.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Se você usar `count` o valor padrão, `offset` você só precisa especificar o parâmetro de consulta em suas chamadas de API.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Ao usar as APIs de imagem e `nextOffset` vídeo bing, você pode usar o valor para evitar resultados de pesquisa duplicados. Obtenha o valor `Images` `Videos` dos objetos ou resposta e `offset` use-o em suas solicitações com o parâmetro.  

> [!NOTE]
> A API de pesquisa da Web Bing retorna resultados de pesquisa que podem incluir páginas da web, imagens, vídeos e notícias. Quando você faz a página através dos resultados de pesquisa da API de pesquisa da Bing Web Search, você está paginando apenas [Páginas da Web,](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage)e não outros tipos de resposta, como imagens ou notícias. Os resultados `WebPage` da pesquisa em objetos podem incluir resultados que aparecem em outros tipos de resposta também.
>
> Se você `responseFilter` usar o parâmetro de consulta sem especificar `count` quaisquer `offset` valores de filtro, não use os parâmetros e parâmetros. 

## <a name="next-steps"></a>Próximas etapas

* [O que são as APIs de Pesquisa na Web do Bing?](bing-api-comparison.md)
* [Referência da API de Pesquisa na Web do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
* [Referência a API v7 de pesquisa personalizada de bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
* [Referência da API v7 da Bing News Search](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)
* [Referência a API v7 de pesquisa de vídeo de bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference)
* [Referência a API v7 de pesquisa de imagem de bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
