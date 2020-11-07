---
title: O que é a API da Pesquisa de Vídeo do Bing?
titleSuffix: Azure Cognitive Services
description: Saiba como pesquisar vídeos na Web usando a API de Pesquisa de Vídeo do Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: 6f5da1d17722f89bee88a4b69177583e095eeac3
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93094278"
---
# <a name="what-is-the-bing-video-search-api"></a>O que é a API da Pesquisa de Vídeo do Bing?

> [!WARNING]
> As APIs de Pesquisa do Bing estão migrando dos Serviços Cognitivos para os Serviços de Pesquisa do Bing. A partir de **30 de outubro de 2020** , todas as novas instâncias da Pesquisa do Bing precisaram ser provisionadas seguindo o processo documentado [aqui](https://aka.ms/cogsvcs/bingmove).
> As APIs de Pesquisa do Bing provisionadas por meio dos Serviços Cognitivos terão suporte nos próximos três anos ou até o final do seu Contrato Enterprise, o que ocorrer primeiro.
> Para obter instruções sobre a migração, confira [Serviços de Pesquisa do Bing](https://aka.ms/cogsvcs/bingmigration).

A API de Pesquisa de Vídeo do Bing torna mais fácil adicionar recursos de pesquisa de vídeos a seus serviços e aplicativos. Ao enviar consultas de pesquisa de usuário com a API, você pode obter e exibir vídeos relevantes e de alta qualidade semelhantes ao [Vídeo do Bing](https://www.bing.com/video). Use essa API para resultados da pesquisa que contenham apenas vídeos. A [API de Pesquisa na Web do Bing](../bing-web-search/search-the-web.md) pode retornar outros tipos de conteúdo da Web, incluindo páginas da Web, vídeos, notícias e imagens.

## <a name="bing-video-search-api-features"></a>Recursos da API de Pesquisa de Vídeo do Bing

| Recurso                                                                                                                                                                                 | Descrição                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Sugerir os termos de pesquisa em tempo real](concepts/sending-requests.md#suggest-search-terms-with-the-bing-autosuggest-api) | Aprimore sua experiência com aplicativos usando a [API de Sugestão Automática do Bing](../bing-autosuggest/get-suggested-search-terms.md) para exibir os termos de pesquisa sugeridos à medida que eles são digitados. |
| [Filtrar e restringir os resultados de vídeos](concepts/get-videos.md#filtering-videos)                      | Filtre os vídeos retornados editando parâmetros de consulta.                                                                                                       |
| [Cortar, redimensionar e exibir miniaturas](../bing-web-search/resize-and-crop-thumbnails.md)                                                | Edite e exiba as visualizações de miniatura para os vídeos retornados pela API de Pesquisa de Vídeo do Bing.                                                                                      |
| [Obter vídeos populares](trending-videos.md) | Pesquisar os vídeos mais populares no mundo.                                                                                                          |
| [Obter insights para vídeos](video-insights.md) | Personalize uma pesquisa de vídeos populares em todo o mundo.                                                                                                          |

## <a name="workflow"></a>Fluxo de trabalho

A API da Pesquisa de Vídeo do Bing é um serviço Web RESTful, facilitando a chamada de qualquer linguagem de programação que pode fazer solicitações HTTP e analisar JSON. Você pode usar o serviço usando a [API REST](csharp.md), ou o [SDK](video-search-sdk-quickstart.md).

1. Crie uma [Conta da API dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com acesso às APIs de Pesquisa do Bing. Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/cognitive-services/) gratuitamente.
2. Envie uma solicitação para a API, com uma consulta de pesquisa válida.
3. Processe a resposta da API analisando a mensagem JSON retornada.


## <a name="next-steps"></a>Próximas etapas

A [demonstração interativa](https://azure.microsoft.com/services/cognitive-services/bing-video-search-api/) da API de Pesquisa de Vídeo do Bing mostra como você pode personalizar uma consulta de pesquisa e pesquisar vídeos na Web.

Use o [início rápido](csharp.md) para se familiarizar rapidamente com sua primeira solicitação de API.

## <a name="see-also"></a>Confira também

* A página de referência da [API de Pesquisa de Vídeo do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference) contém a lista de pontos de extremidade, cabeçalhos e parâmetros de consulta usados para solicitar os resultados da pesquisa.

* Os [Requisitos de exibição e uso do Bing](./useanddisplayrequirements.md) especificam os usos aceitáveis do conteúdo e das informações obtidas por meio das APIs de pesquisa do Bing.

* Visite a [página do hub da API de Pesquisa do Bing](../bing-web-search/search-the-web.md) para explorar as outras APIs disponíveis.