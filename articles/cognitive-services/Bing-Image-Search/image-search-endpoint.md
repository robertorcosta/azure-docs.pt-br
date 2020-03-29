---
title: Pontos de extremidade da API de Pesquisa de Imagem do Bing
titleSuffix: Azure Cognitive Services
description: A API de pesquisa de imagens inclui três pontos finais. O endpoint 1 retorna imagens da web. O ponto de extremidade 2 retorna ImageInsights. O ponto de extremidade 3 retorna imagens populares.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 38416f6a580d270aefc287de0c198bd418a44db9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74072636"
---
# <a name="endpoints-for-the-bing-image-search-api"></a>Pontos de extremidade da API de Pesquisa de Imagem do Bing

A **API de Pesquisa de Imagem**  inclui três pontos de extremidade.  O ponto de extremidade 1 retorna imagens da Web com base em uma consulta. O ponto de extremidade 2 retorna [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse).  O ponto de extremidade 3 retorna imagens populares.

## <a name="endpoints"></a>Pontos de extremidade

Para obter resultados de imagem usando a API do Bing, envie uma solicitação para um dos pontos de extremidade a seguir. Use os cabeçalhos e parâmetros de URL para definir mais especificações.

**Ponto de extremidade 1:** retorna imagens que são relevantes para a consulta de pesquisa do usuário definida pelo `?q=""`.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search
```

**Ponto de extremidade 2:** retorna insights sobre uma imagem, usando um `GET` ou `POST`.
```
 GET or POST https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
Uma solicitação GET retorna informações sobre uma imagem, como páginas da Web que incluem a imagem. Inclua o parâmetro [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) com uma solicitação `GET`.

Ou, você pode incluir uma imagem binária no corpo de uma solicitação `POST` e definir o parâmetro [módulos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) para `RecognizedEntities`. Isso retornará um [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v5-reference#insightstoken) para usar como um parâmetro em uma solicitação `GET` subsequente, que retorna informações sobre as pessoas na imagem.  Defina `modules` para `All` para obter todas as informações, exceto `RecognizedEntities` nos resultados de `POST` sem fazer outra chamada usando `insightsToken`.


**Ponto de extremidade 3:** retorna imagens que são populares com base nas solicitações de pesquisa feitas por outros usuários. As imagens são separadas em categorias diferentes, por exemplo, com base em pessoas ou eventos notáveis.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending
```

Para obter uma lista de mercados que dão suporte a imagens populares, consulte [Imagens Populares](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images).

Para obter detalhes sobre cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros, etc., consulte a referência [API de Pesquisa de Imagem do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference).
## <a name="response-json"></a>JSON de resposta
A resposta a uma solicitação de pesquisa de imagem inclui resultados como objetos JSON. Para obter exemplos de como analisar os resultados, confira o [tutorial](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app) e o [código-fonte](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app-source).

## <a name="next-steps"></a>Próximas etapas
As APIs do **Bing** dão suporte a ações de pesquisa que retornam os resultados de acordo com seu tipo.Todos os pontos de extremidade de pesquisa retornam os resultados como objetos de resposta JSON. Todos os pontos de extremidade dão suporte a consultas que retornam um idioma e/ou um local específico por longitude, latitude e raio de pesquisa.

Para obter informações completas sobre os parâmetros compatíveis com cada ponto de extremidade, confira as páginas de referência de cada tipo.
Para obter exemplos de solicitações básicas usando a API de Pesquisa de Imagem, consulte [Inícios rápidos de Pesquisa de Imagem](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/search-the-web).
