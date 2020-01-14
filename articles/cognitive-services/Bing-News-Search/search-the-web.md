---
title: O que é a API de Pesquisa de Notícias do Bing?
titleSuffix: Azure Cognitive Services
description: Saiba como usar a API de Pesquisa de Notícias do Bing para pesquisar na Web por manchetes atuais em várias categorias, incluindo manchetes e tópicos populares.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: e0e99c7f677173c64afad3109b2f4accd7cb3cb9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448439"
---
# <a name="what-is-the-bing-news-search-api"></a>O que é a API de Pesquisa de Notícias do Bing?

A API de Pesquisa de Notícias do Bing facilita a integração das funcionalidades de pesquisa cognitiva de notícias do Bing aos seus aplicativos. A API fornece uma experiência semelhante ao [Bing Notícias](https://www.bing.com/news), permitindo que você envie consultas de pesquisa e receba artigos de notícias relevantes.

Lembre-se de que a API de Pesquisa de Notícias do Bing fornece apenas resultados da pesquisa de notícias. Use a [API de Pesquisa na Web do Bing](../bing-web-search/search-the-web.md), a [API de Pesquisa de Vídeo do Bing](../bing-video-search/search-the-web.md) e a [API de Pesquisa de Imagem do Bing](../bing-image-search/overview.md) para outros tipos de conteúdo da Web.

## <a name="bing-news-search-api-features"></a>Recursos da API de Pesquisa de Notícias do Bing

Embora a API de Pesquisa de Notícias do Bing encontre e retorne principalmente artigos de notícias relevantes, ela fornece vários recursos para a recuperação inteligente e focalizada de notícias na Web.

|Recurso  |DESCRIÇÃO  |
|---------|---------|
|[Sugerindo e usando termos de pesquisa](concepts/search-for-news.md#suggest-and-use-search-terms)     | Aprimore sua experiência de pesquisa usando a [API de Sugestão Automática do Bing](../bing-autosuggest/get-suggested-search-terms.md) para exibir os termos de pesquisa sugeridos à medida que eles são digitados.         |
|[Obter notícias gerais](concepts/search-for-news.md#get-general-news)     | Encontre notícias enviando uma consulta de pesquisa para a API de Pesquisa de Notícias do Bing e obtendo uma lista de artigos de notícias relevantes.           |
|[Principais notícias de hoje](concepts/search-for-news.md#get-todays-top-news)      | Obtenha as principais reportagens do dia em todas as categorias.       |
|[Notícias por categoria](concepts/search-for-news.md)     | Pesquise notícias em categorias específicas.        | 
|[Manchetes](concepts/search-for-news.md)     | Pesquise as principais manchetes em todas as categorias.         |

## <a name="workflow"></a>Fluxo de trabalho

A API de Pesquisa de Notícias do Bing é um serviço Web RESTful, facilitando a chamada em qualquer linguagem de programação que possa fazer solicitações HTTP e analisar JSON. É possível usar o serviço usando a API REST ou o SDK.

1. Crie uma conta da API dos Serviços Cognitivos com acesso às APIs de Pesquisa do Bing. Caso não tenha uma assinatura do Azure, [crie uma conta gratuitamente](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-news-api).

2. Envie uma solicitação para a API, com uma consulta de pesquisa válida.

3. Processe a resposta da API analisando a mensagem JSON retornada.

## <a name="next-steps"></a>Próximas etapas

Primeiro, experimente a [demonstração interativa](https://azure.microsoft.com/services/cognitive-services/bing-news-search-api/) para a API de Pesquisa de Notícias do Bing. Essa demonstração explica como você pode personalizar rapidamente uma consulta de pesquisa e encontrar notícias na Web.

Para começar rapidamente com sua primeira solicitação de API, experimente um início rápido para a [API REST](quickstart.md) ou um dos [SDKs](sdk.md).

## <a name="see-also"></a>Confira também

* A seção de referência da [API de Pesquisa de Notícias do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) contém definições e informações sobre os pontos de extremidade, os cabeçalhos, as respostas de API e os parâmetros de consulta que podem ser usados para solicitar resultados da pesquisa baseada em imagem.
* Os [Requisitos de exibição e uso do Bing](./useanddisplayrequirements.md) especificam os usos aceitáveis do conteúdo e das informações obtidas por meio das APIs de pesquisa do Bing.
* Visite a [página do hub da API de Pesquisa do Bing](../bing-web-search/search-the-web.md) para explorar as outras APIs disponíveis.