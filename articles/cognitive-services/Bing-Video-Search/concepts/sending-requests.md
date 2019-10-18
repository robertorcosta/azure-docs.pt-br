---
title: Enviar solicitações de pesquisa para o API de Pesquisa de Vídeo do Bing
titleSuffix: Azure Cognitive Services
description: Saiba mais sobre como enviar consultas de pesquisa para o API de Pesquisa de Vídeo do Bing.
services: cognitive-services
author: aahi
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: aahill
ms.openlocfilehash: 96f92f0dc1e9e0c6c409fe17680fc57412e2b7d3
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72512521"
---
# <a name="sending-search-requests-to-the-bing-video-search-api"></a>Envio de solicitações de pesquisa para o API de Pesquisa de Vídeo do Bing

Este artigo descreve os parâmetros e atributos das solicitações enviadas ao API de Pesquisa de Vídeo do Bing, bem como o objeto de resposta JSON retornado. 

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="suggest-search-terms-with-the-bing-autosuggest-api"></a>Sugira termos de pesquisa com o API de Sugestão Automática do Bing

Se você fornecer uma caixa de pesquisa na qual o usuário insere o termo de pesquisa, use o [API de sugestão automática do Bing](../../bing-autosuggest/get-suggested-search-terms.md) para melhorar a experiência. A API retorna cadeias de caracteres de consulta sugeridas com base em termos de pesquisa parciais conforme o usuário digita.

Depois que o usuário inserir seu termo de pesquisa, codifique a URL antes de definir o parâmetro de consulta [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query) . Por exemplo, se o usuário inserir *velejando Dinghies*, defina `q` como `sailing+dinghies` ou `sailing%20dinghies`.

## <a name="sending-a-request"></a>Enviando uma solicitação

Para obter os resultados da pesquisa de vídeo, você deve enviar uma solicitação GET para o seguinte ponto de extremidade:  
  
```
https://api.cognitive.microsoft.com/bing/v7.0/videos/search
```
   
A solicitação deve usar o protocolo HTTPS.

Recomendamos que todas as solicitações sejam originadas de um servidor. Distribuir a chave como parte de um aplicativo cliente fornece mais oportunidades para que terceiros mal-intencionados o acessem. Fazer chamadas de um servidor também fornece um único ponto de atualização para versões futuras da API.

  
A solicitação deve especificar o parâmetro de consulta [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query) , que contém o termo de pesquisa do usuário. Embora seja opcional, a solicitação também deve especificar o parâmetro de consulta [MKT](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#mkt) , que identifica o mercado do qual você deseja que os resultados sejam provenientes. Para obter uma lista de parâmetros de consulta opcionais, como `pricing`, consulte [parâmetros de consulta](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query-parameters). Todos os valores de parâmetro de consulta devem ser codificados em URL.  
  
A solicitação deve especificar o cabeçalho [OCP-APIM-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#subscriptionkey) . Embora opcional, você também é incentivado a especificar os seguintes cabeçalhos:  
  
-   [Agente do usuário](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#clientid)  
-   [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#clientip)  
-   [Localização de pesquisa X](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#location)  

Os cabeçalhos de IP e de local do cliente são importantes para retornar conteúdo com reconhecimento de local.  

Para obter uma lista de todos os cabeçalhos de solicitação e resposta, consulte [cabeçalhos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#headers).

## <a name="example-search-request"></a>Exemplo de solicitação de pesquisa

O seguinte mostra uma solicitação de pesquisa que inclui todos os parâmetros e cabeçalhos de consulta sugeridos. Se for a primeira vez que você chamar qualquer uma das APIs do Bing, não inclua o cabeçalho de ID do cliente. Inclua somente a ID do cliente se você já tiver chamado uma API do Bing e o Bing retornou uma ID do cliente para a combinação de usuário e dispositivo. 
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

## <a name="example-json-response"></a>Exemplo de resposta JSON

O seguinte mostra a resposta à solicitação anterior. O exemplo também mostra os cabeçalhos de resposta específicos ao Bing.

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D5694...",
    "totalEstimatedMatches" : 1000,
    "value" : [
        {
            "name" : "How to sail - What to Wear for Dinghy Sailing",
            "description" : "An informative video on what to wear when...",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56...",
            "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.DYWCvh...",
            "datePublished" : "2014-03-04T11:51:53",
            "publisher" : [
                {
                    "name" : "Fabrikam"
                }
            ],
            "creator" : {
                "name" : "Marcus Appel"
            },
            "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjHZ--g",
            "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56944...",
            "encodingFormat" : "h264",
            "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjBZ--g",
            "width" : 1280,
            "height" : 720,
            "duration" : "PT2M47S",
            "motionThumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OM.Y6...",
            "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"https:...><\/iframe>",
            "allowHttpsEmbed" : true,
            "viewCount" : 8743,
            "thumbnail" : {
                "width" : 300,
                "height" : 168
            },
            "videoId" : "6DB795E11A6E3CBAAD636DB795E11E3CBAAD63",
            "allowMobileEmbed" : true,
            "isSuperfresh" : false
        },
        . . .
    ],
    "nextOffset" : 0,
    "pivotSuggestions" : [
        {
            "pivot" : "sailing",
            "suggestions" : []
        },
        {
            "pivot" : "dinghies",
            "suggestions" : [
                {
                    "text" : "Sailing Cruising",
                    "displayText" : "Cruising",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF754...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Sailing..."
                    }
                },
                . . .
            ]
        }
    ]
}
```

## <a name="next-steps"></a>Próximos passos

Experimente a API. Vá para [pesquisa de vídeo console de teste de API](https://dev.cognitive.microsoft.com/docs/services/56b43f3ccf5ff8098cef3809/operations/58113fe5e31dac0a1ce6b0a8). 

Para obter detalhes sobre como consumir os objetos de resposta, consulte [pesquisando vídeos na Web](../search-the-web.md).

Para obter detalhes sobre como obter informações sobre um vídeo como pesquisas relacionadas, consulte [informações de vídeo](../video-insights.md).  
  
Para obter detalhes sobre vídeos que são uma tendência de mídia social, consulte [vídeos de tendências](../trending-videos.md).  
