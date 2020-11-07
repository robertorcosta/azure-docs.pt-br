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
ms.openlocfilehash: 9ba749d671e24e86e2cd0a299e98ba03e47cf354
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101673"
---
# <a name="what-is-the-bing-news-search-api"></a>O que é a API de Pesquisa de Notícias do Bing?

> [!WARNING]
> As APIs de Pesquisa do Bing estão migrando dos Serviços Cognitivos para os Serviços de Pesquisa do Bing. A partir de **30 de outubro de 2020** , todas as novas instâncias da Pesquisa do Bing precisaram ser provisionadas seguindo o processo documentado [aqui](https://aka.ms/cogsvcs/bingmove).
> As APIs de Pesquisa do Bing provisionadas por meio dos Serviços Cognitivos terão suporte nos próximos três anos ou até o final do seu Contrato Enterprise, o que ocorrer primeiro.
> Para obter instruções sobre a migração, confira [Serviços de Pesquisa do Bing](https://aka.ms/cogsvcs/bingmigration).

A API de Pesquisa de Notícias do Bing facilita a integração das funcionalidades de pesquisa cognitiva de notícias do Bing aos seus aplicativos. A API fornece uma experiência semelhante ao [Bing Notícias](https://www.bing.com/news), permitindo que você envie consultas de pesquisa e receba artigos de notícias relevantes.

Lembre-se de que a API de Pesquisa de Notícias do Bing fornece apenas resultados da pesquisa de notícias. Use a [API de Pesquisa na Web do Bing](../bing-web-search/search-the-web.md), a [API de Pesquisa de Vídeo do Bing](../bing-video-search/search-the-web.md) e a [API de Pesquisa de Imagem do Bing](../bing-image-search/overview.md) para outros tipos de conteúdo da Web.

## <a name="bing-news-search-api-features"></a>Recursos da API de Pesquisa de Notícias do Bing

Embora a API de Pesquisa de Notícias do Bing encontre e retorne principalmente artigos de notícias relevantes, ela fornece vários recursos para a recuperação inteligente e focalizada de notícias na Web.

|Recurso  |Descrição  |
|---------|---------|
|[Sugerindo e usando termos de pesquisa](concepts/search-for-news.md#suggest-and-use-search-terms)     | Aprimore sua experiência de pesquisa usando a [API de Sugestão Automática do Bing](../bing-autosuggest/get-suggested-search-terms.md) para exibir os termos de pesquisa sugeridos à medida que eles são digitados.         |
|[Obter notícias gerais](concepts/search-for-news.md#get-general-news)     | Encontre notícias enviando uma consulta de pesquisa para a API de Pesquisa de Notícias do Bing e obtendo uma lista de artigos de notícias relevantes.           |
|[Principais notícias de hoje](concepts/search-for-news.md#get-todays-top-news)      | Obtenha as principais reportagens do dia em todas as categorias.       |
|[Notícias por categoria](concepts/search-for-news.md)     | Pesquise notícias em categorias específicas.        | 
|[Manchetes](concepts/search-for-news.md)     | Pesquise as principais manchetes em todas as categorias.         |

## <a name="workflow"></a>Fluxo de trabalho

A API de Pesquisa de Notícias do Bing é um serviço Web RESTful, facilitando a chamada em qualquer linguagem de programação que possa fazer solicitações HTTP e analisar JSON. É possível usar o serviço usando a API REST ou o SDK.

1. Crie uma [Conta da API dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com acesso às APIs de Pesquisa do Bing. Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/cognitive-services/) gratuitamente.
2. Envie uma solicitação para a API, com uma consulta de pesquisa válida.
3. Processe a resposta da API analisando a mensagem JSON retornada.

## <a name="next-steps"></a>Próximas etapas

Primeiro, experimente a [demonstração interativa](https://azure.microsoft.com/services/cognitive-services/bing-news-search-api/) para a API de Pesquisa de Notícias do Bing. Essa demonstração explica como você pode personalizar rapidamente uma consulta de pesquisa e encontrar notícias na Web.

Para começar rapidamente com sua primeira solicitação de API, experimente um início rápido para a [API REST](quickstart.md) ou um dos [SDKs](sdk.md).

## <a name="see-also"></a>Confira também

* A seção de referência da [API de Pesquisa de Notícias do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) contém definições e informações sobre os pontos de extremidade, os cabeçalhos, as respostas de API e os parâmetros de consulta que podem ser usados para solicitar resultados da pesquisa baseada em imagem.
* Os [Requisitos de exibição e uso do Bing](./useanddisplayrequirements.md) especificam os usos aceitáveis do conteúdo e das informações obtidas por meio das APIs de pesquisa do Bing.
* Visite a [página do hub da API de Pesquisa do Bing](../bing-web-search/search-the-web.md) para explorar as outras APIs disponíveis.