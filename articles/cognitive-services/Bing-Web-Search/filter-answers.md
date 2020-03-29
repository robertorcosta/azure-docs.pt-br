---
title: Como filtrar os resultados da pesquisa - API de pesquisa do Bing na Web
titleSuffix: Azure Cognitive Services
description: Você pode filtrar os tipos de respostas que Bing inclui na resposta (por exemplo, imagens, vídeos e notícias) usando o parâmetro de consulta 'responseFilter'.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 8B837DC2-70F1-41C7-9496-11EDFD1A888D
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: scottwhi
ms.openlocfilehash: 6fa022f181e2061c6a7f3e08d1f2f501ddd9cac3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220265"
---
# <a name="filtering-the-answers-that-the-search-response-includes"></a>Filtrar as respostas que a resposta da pesquisa inclui  

Quando você realiza uma consulta na Web, o Bing retorna todo o conteúdo relevante que ele encontra para a pesquisa. Por exemplo, se a consulta de pesquisa for "vela+barcos", a resposta poderá conter as seguintes respostas:

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43C...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    },
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=sail...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA5CA6464E5D...",
        "isFamilyFriendly" : true,
        "value" : [...]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [...]
        }
    }
}    
```

## <a name="query-parameters"></a>Parâmetros de consulta

Para filtrar as respostas retornadas por Bing, use os parâmetros de consulta abaixo ao ligar para a API.  

### <a name="responsefilter"></a>Filtro de resposta

Você pode filtrar os tipos de respostas que Bing inclui na resposta (por exemplo, imagens, vídeos e notícias) usando o parâmetro de consulta [responseFilter,](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter) que é uma lista de respostas delimitada sem vírgulas. Uma resposta será incluída na resposta se Bing encontrar conteúdo relevante para ele. 

Para excluir respostas específicas da resposta, como `-` imagens, prepare um caractere para o tipo de resposta. Por exemplo: 

```
&responseFilter=-images,-videos
```

A seguir, mostramos como usar `responseFilter` para solicitar imagens, vídeos e notícias sobre barcos a vela. Quando você codifica a cadeia de caracteres de consulta, as vírgulas são alteradas para %2C.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&responseFilter=images%2Cvideos%2Cnews&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

O exemplo a seguir mostra a resposta à solicitação anterior. Como o Bing não encontrou resultados relevantes de vídeo e notícias a resposta não os incluiu.

```json
{
    "_type" : "SearchResponse",
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=sail...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3AD78B183C56456C...",
        "isFamilyFriendly" : true,
        "value" : [...]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "Images",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images"
                }
            }]
        }
    }
}
```

Embora o Bing não tenha retornado os resultados de vídeo e notícias na resposta anterior, isso não significa que o conteúdo de vídeo e de notícias não exista. Isso significa apenas que a página não os inclui. No entanto, se você percorresse as [páginas](./paging-webpages.md) através de mais resultados, as páginas subsequentes provavelmente os incluiriam. Além disso, se você chamar os pontos de extremidade [API de Pesquisa de Vídeo](../bing-video-search/search-the-web.md) e [API de Pesquisa de Notícias](../bing-news-search/search-the-web.md) diretamente, a resposta provavelmente conterá resultados.

Não é recomendável usar `responseFilter` para obter os resultados de uma única API. Se desejar o conteúdo de uma única API do Bing, chame essa API diretamente. Por exemplo, para receber apenas imagens, envie uma solicitação para o ponto de extremidade da API de Pesquisa de Imagens, `https://api.cognitive.microsoft.com/bing/v7.0/images/search` ou um dos outros pontos de extremidade [Imagens](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#endpoints). Chamar a única API é importante não apenas por motivos de desempenho, mas porque as APIs específicas do conteúdo oferecem resultados mais úteis. Por exemplo, você pode usar filtros que não estão disponíveis para a API de Pesquisa na Web para filtrar os resultados.  

### <a name="site"></a>Site

Para obter resultados de pesquisa de `site:` um domínio específico, inclua o parâmetro consulta na seqüência de consultas.  

```
https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us
```

> [!NOTE]
> Dependendo da consulta, se você usar o operador de consulta `site:`, haverá a possibilidade de que a resposta contenha conteúdo adulto, independentemente da configuração da [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#safesearch). Só use `site:` se estiver ciente sobre o conteúdo do site e se seu cenário permitir a possibilidade de obtenção de conteúdo adulto.

### <a name="freshness"></a>Atualização

Para limitar os resultados da resposta web às páginas da Web que Bing descobriu durante um período específico, defina o parâmetro de consulta de [frescor](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#freshness) como um dos seguintes valores insensíveis a casos:

* `Day`— Páginas web de retorno que Bing descobriu nas últimas 24 horas
* `Week`— Páginas web de retorno que Bing descobriu nos últimos 7 dias
* `Month`— Páginas web de retorno que descobriram nos últimos 30 dias

Você também pode definir este parâmetro para um `YYYY-MM-DD..YYYY-MM-DD`intervalo de datas personalizado no formulário, . 

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-01..2019-05-30`

Para limitar os resultados a uma única data, defina o parâmetro de frescor para uma data específica:

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-04`

Os resultados podem incluir páginas da Web que estão fora do período especificado se o número de páginas da Web que bing corresponde aos seus critérios de filtro for menor do que o número de páginas da Web que você solicitou (ou o número padrão que Bing retorna).

## <a name="limiting-the-number-of-answers-in-the-response"></a>Limite o número de itens na resposta

Bing pode retornar vários tipos de resposta na resposta JSON. Por exemplo, se você consultar *veleiros+dinghies,* Bing poderá `webpages`retornar, `images`e `videos` `relatedSearches`.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "relatedSearches" : {...},
    "videos" : {...},
    "rankingResponse" : {...}
}
```

Para limitar o número de respostas que o Bing retorna para as duas principais respostas (páginas da Web e imagens), defina o parâmetro de consulta [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) como 2.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

A resposta incluirá apenas `webPages` e `images`.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "rankingResponse" : {...}
}
```

Se você adicionar o parâmetro de consulta `responseFilter` à consulta anterior e defini-lo como páginas da Web e notícias, a resposta conterá apenas páginas da Web, pois as notícias não serão classificadas.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "rankingResponse" : {...}
}
```

## <a name="promoting-answers-that-are-not-ranked"></a>Promova respostas que não estão classificadas

Se as respostas mais bem classificadas retornadas pelo Bing para uma consulta fossem páginas da Web, imagens, vídeos e pesquisas relacionadas, a resposta incluiria essas respostas. Se você definir [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) como dois (2), o Bing retornará as duas principais respostas classificadas: páginas da Web e imagens. Se você quiser que o Bing inclua imagens e vídeos na resposta, especifique o parâmetro de consulta [promover](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote) e defina-o como imagens e vídeos.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&promote=images%2Cvideos&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

O exemplo a seguir é a resposta da solicitação anterior. O Bing retorna as duas principais respostas, páginas da Web e imagens e promove vídeos para a resposta.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailiing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "videos" : {...},
    "rankingResponse" : {...}
}
```

Se você definir `promote` como notícias, a resposta não incluirá a resposta de notícias por essa não ser uma resposta de classificação &mdash;você só pode promover respostas classificadas.

As respostas que você deseja promover não contam em relação ao limite `answerCount`. Por exemplo, se as respostas classificadas forem notícias, imagens e vídeos, e você definir `answerCount` como 1 e `promote` para notícias, a resposta conterá notícias e imagens. Ou, se as respostas classificadas forem vídeos, imagens e notícias, a resposta conterá vídeos e notícias.

Você só poderá usar `promote` se especificar o parâmetro de consulta `answerCount`.
