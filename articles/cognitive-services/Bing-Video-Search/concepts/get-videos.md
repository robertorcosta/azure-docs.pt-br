---
title: Pesquisar vídeos usando o API de Pesquisa de Vídeo do Bing
titleSuffix: Azure Cognitive Services
description: Saiba mais sobre como enviar consultas de pesquisa para o API de Pesquisa de Vídeo do Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: e3314efe0ea0367eb852fd17aadece17026ff4bb
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72512407"
---
# <a name="search-for-videos-with-the-bing-video-search-api"></a>Pesquisar vídeos com o API de Pesquisa de Vídeo do Bing

O API de Pesquisa de Vídeo do Bing facilita a integração dos recursos de pesquisa de notícias cognitivas do Bing em seus aplicativos. Embora a API Localize principalmente e retorne vídeos relevantes da Web, ela fornece vários recursos para recuperação de vídeo inteligente e focada na Web.

## <a name="getting-videos"></a>Obtendo vídeos

Para obter vídeos relacionados ao termo de pesquisa do usuário na Web, envie a seguinte solicitação GET:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Todas as solicitações devem ser feitas de um servidor.

Se for a primeira vez que você chamar qualquer uma das APIs do Bing, não inclua o cabeçalho de ID do cliente. Inclua somente a ID do cliente se você já tiver chamado uma API do Bing e o Bing retornou uma ID do cliente para a combinação de usuário e dispositivo.

Para obter vídeos de um domínio específico, use o operador [site:](https://msdn.microsoft.com/library/ff795613.aspx) Query.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

A resposta contém uma resposta de [vídeos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) que contém uma lista de vídeos que o Bing pensou que foram relevantes para a consulta. Cada objeto de [vídeo](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video) na lista inclui a URL do vídeo, sua duração, suas dimensões e seu formato de codificação entre outros atributos. O objeto de vídeo também inclui a URL de uma miniatura do vídeo e as dimensões da miniatura.

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545...",
    "totalEstimatedMatches" : 1000,
    "value" : [
        {
            "name" : "How to sail - What to Wear for Dinghy Sailing",
            "description" : "An informative video on what to wear when...",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7...",
            "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.DYW...",
            "datePublished" : "2014-03-04T11:51:53",
            "publisher" : [
                {
                    "name" : "Fabrikam"
                }
            ],
            "creator" : 
            {
                "name" : "Marcus Appel"
            },
            "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjZ--g",
            "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D569...",
            "encodingFormat" : "h264",
            "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjZ--g",
            "width" : 1280,
            "height" : 720,
            "duration" : "PT2M47S",
            "motionThumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OM.Y62...",
            "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"https:...><\/iframe>",
            "allowHttpsEmbed" : true,
            "viewCount" : 8743,
            "thumbnail" : 
            {
                "width" : 300,
                "height" : 168
            },
            "videoId" : "6DB795E11A6E3CBAAD636DB795E113CBAAD63",
            "allowMobileEmbed" : true,
            "isSuperfresh" : false
        },
        ...
    ],
    "queryExpansions" : [...],
    "nextOffsetAddCount" : 0,
    "pivotSuggestions" : [...]
}
```

## <a name="video-thumbnails"></a>Miniaturas de vídeo

Você pode exibir todos ou um subconjunto das miniaturas de vídeo retornadas pelo API de Pesquisa de Vídeo do Bing. Se você exibir um subconjunto, forneça ao usuário uma opção para exibir os vídeos restantes. como parte dos [requisitos de uso e exibição](../UseAndDisplayRequirements.md)da API do Bing, você deve exibir os vídeos na ordem fornecida na resposta. Para obter informações sobre como redimensionar a miniatura, consulte [redimensionar e cortar miniaturas](../../bing-web-search/resize-and-crop-thumbnails.md). 

À medida que o usuário passa o mouse sobre a miniatura, você pode usar [motionThumbnailUrl](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-motionthumbnailurl) para reproduzir uma versão em miniatura do vídeo. Certifique-se de atribuir a miniatura de movimento ao exibi-la.

<!-- Removing until the images can be sanitized.
![Motion thumbnail of a video](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

Quando uma miniatura é clicada, há três opções para exibir o vídeo:

- Use [hostPageUrl](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-hostpageurl) para exibir o vídeo no site do host (por exemplo, YouTube)
- Use [webSearchUrl](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-websearchurl) para exibir o vídeo no navegador de vídeo do Bing
- Use o [embdedHtml](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-embedhtml) para inserir o vídeo em sua própria experiência 

Certifique-se de usar o editor e o criador para atribuir o vídeo ao reproduzi-lo.

Para obter detalhes sobre como usar [videoid](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-videoid) para obter informações sobre o vídeo, consulte [informações de vídeo](../video-insights.md).

## <a name="filtering-videos"></a>Filtrando vídeos

Por padrão, a API Pesquisa de Vídeo retorna todos os vídeos que são relevantes para a consulta. Se você quiser apenas vídeos gratuitos ou vídeos com menos de cinco minutos de comprimento, você usaria os seguintes parâmetros de consulta de filtro:

- [preços](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#pricing) &mdash;Filter vídeos por preço (por exemplo, vídeos gratuitos ou para os quais você precisa pagar)
- [resolução](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#resolution) &mdash;Filter vídeos por resolução (por exemplo, vídeos com uma resolução 720p ou superior)
- [videoLength](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videolength) &mdash;Filter vídeos por tamanho de vídeo (por exemplo, vídeos com menos de cinco minutos de comprimento)
- [atualização](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#freshness) &mdash;Filter vídeos por idade (por exemplo, vídeos descobertos pelo Bing na última semana)

Para obter vídeos de um domínio específico, inclua o operador [site:](https://msdn.microsoft.com/library/ff795613.aspx) Query na cadeia de caracteres de consulta.

> [!NOTE]
> Dependendo da consulta, se você usar o operador de consulta `site:`, haverá a chance de que a resposta contenha conteúdo somente para adultos, independentemente da configuração do [filtro adulto](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#safesearch) . Você deve usar `site:` somente se estiver ciente do conteúdo no site e seu cenário oferecer suporte à possibilidade de conteúdo adulto.

O exemplo a seguir mostra como obter vídeos gratuitos do ContosoSailing.com que têm uma resolução de 720p ou melhor e que o Bing descobriu no último mês.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies+site:contososailing.com&pricing=free&freshness=month&resolution=720p&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

## <a name="expanding-the-query"></a>Expandindo a consulta

Se o Bing puder expandir a consulta para restringir a pesquisa original, o objeto [vídeos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) conterá o campo `queryExpansions`. Por exemplo, se a consulta estivesse *limpando medianizes*, as consultas expandidas podem ser: **ferramentas**de limpeza de medianiz, limpeza de medianizes **do zero**, **computador**de limpeza de medianiz e limpeza **fácil** de medianiz.

O exemplo a seguir mostra as consultas expandidas para *limpeza de medianizes*.

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC5...",
    "totalEstimatedMatches" : 1000,
    "value" : [...],
    "nextOffsetAddCount" : 4,
    "queryExpansions" : [
        {
            "text" : "Gutter Cleaning Tools",
            "displayText" : "Tools",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FB....",
            "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5...",
            "thumbnail" : {
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Gutter..."
            }
        },
        ...
    ]
    "pivotSuggestions" : [...],
}
```

O campo `queryExpansions` contém uma lista de objetos de [consulta](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query_obj) . O campo `text` contém a consulta expandida e o campo `displayText` contém o termo de expansão. Você pode usar os campos de texto e miniatura para exibir as cadeias de consulta expandidas para o usuário, caso a cadeia de caracteres de consulta expandida seja realmente o que eles estão procurando. Torne a miniatura e o texto clicáveis usando a URL de `webSearchUrl` ou `searchLink` URL. Use `webSearchUrl` para enviar o usuário aos resultados da pesquisa do Bing ou `searchLink` se você fornecer sua própria página de resultados.

## <a name="pivoting-the-query"></a>Dinamizando a consulta

Se o Bing puder segmentar a consulta de pesquisa original, o objeto [vídeos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) conterá o campo `pivotSuggestions`. Por exemplo, se a consulta original estava *limpando as medianizes*, o Bing pode segmentar a consulta em *limpeza* e *medianizes*.

O exemplo a seguir mostra as sugestões dinâmicas para a *limpeza de medianizes*.

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC...",
    "totalEstimatedMatches" : 1000,
    "value" : [...],
    "nextOffsetAddCount" : 0,
    "queryExpansions" : [...],
    "pivotSuggestions" : [
        {
            "pivot" : "cleaning",
            "suggestions" : [
                {
                    "text" : "Gutter Repair",
                    "displayText" : "Repair",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5\/videos...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=Gutter..."
                    }
                },
                ...
            ]
        },
        {
            "pivot" : "gutters",
            "suggestions" : [
                {
                    "text" : "Window Cleaning",
                    "displayText" : "Window",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC59...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?q=Window..."
                    }
                },
                ...
            ]
        }
    ]
}
```

Para cada tabela dinâmica, a resposta contém uma lista de objetos de [consulta](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query_obj) que contêm consultas sugeridas. O campo `text` contém a consulta sugerida e o campo `displayText` contém o termo que substitui o pivô na consulta original. Por exemplo, limpeza de janela.

Você pode usar os campos `text` e `thumbnail` para exibir as cadeias de consulta expandidas para o usuário, caso a cadeia de caracteres de consulta expandida seja realmente o que eles estão procurando. Torne a miniatura e o texto clicáveis usando a URL de `webSearchUrl` ou `searchLink` URL. Use `webSearchUrl` para enviar o usuário aos resultados da pesquisa do Bing ou `searchLink` se você fornecer sua própria página de resultados.

## <a name="throttling-requests"></a>Solicitações de limitação

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]
