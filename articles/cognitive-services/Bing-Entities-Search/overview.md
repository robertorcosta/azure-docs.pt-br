---
title: O que é a API de Pesquisa de Entidade do Bing?
titleSuffix: Azure Cognitive Services
description: Use a API de Pesquisa de Entidade do Bing para extrair e pesquisar entidades e locais nas consultas de pesquisa.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: 2f374e29f4dc5406956cd56d1bb0bd1466e65773
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75384512"
---
# <a name="what-is-bing-entity-search-api"></a>O que é a API de Pesquisa de Entidade do Bing?

A API de Pesquisa de Entidade do Bing envia uma consulta de pesquisa para o Bing e obtém os resultados que incluem entidades e locais. Os resultados de local incluem restaurantes, hotel ou outras empresas locais. O Bing retorna locais se a consulta especifica o nome da empresa local ou solicita um tipo de negócio (por exemplo, restaurantes nas proximidades do meu local). O Bing retorna entidades se a consulta especifica pessoas, locais (atrações turísticas, estados, países, regiões etc.) ou coisas conhecidas.

|Recurso  |DESCRIÇÃO  |
|---------|---------|
|[Sugestões de pesquisa em tempo real](concepts/search-for-entities.md#suggest-search-terms-with-the-bing-autosuggest-api)     | Fornece sugestões de pesquisa que podem ser exibidas como uma lista suspensa conforme os usuários digitam.       | 
| [Desambiguidade de entidade](concepts/search-for-entities.md#the-bing-entity-search-api-response)  | Obter várias entidades para consultas com vários significados possíveis. |
| [Encontrar locais](concepts/search-for-entities.md#find-places) | Pesquisar e retornar informações sobre entidades e empresas locais  |

## <a name="workflow"></a>Fluxo de trabalho

A API de Pesquisa de Entidade do Bing é um serviço Web RESTful, facilitando a chamada em qualquer linguagem de programação que possa fazer solicitações HTTP e analisar JSON. É possível usar o serviço usando a API REST ou o SDK.

1. Crie uma [Conta da API dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com acesso às APIs de Pesquisa do Bing. Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) gratuitamente.
2. Envie uma solicitação para a API, com uma consulta de pesquisa válida.
3. Processe a resposta da API analisando a mensagem JSON retornada.

## <a name="next-steps"></a>Próximas etapas

* Experimente a [demonstração interativa](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) para a API de Pesquisa de Entidade do Bing. 
* Para uma introdução rápida à primeira solicitação, experimente um [Início Rápido](quickstarts/csharp.md).
* A seção de referência da [API de Pesquisa de Entidade do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference).
* Os [Requisitos de exibição e uso do Bing](./use-display-requirements.md) especificam os usos aceitáveis do conteúdo e das informações obtidas por meio das APIs de pesquisa do Bing.
* Visite a [página do hub da API de Pesquisa do Bing](../bing-web-search/search-the-web.md) para explorar as outras APIs disponíveis.