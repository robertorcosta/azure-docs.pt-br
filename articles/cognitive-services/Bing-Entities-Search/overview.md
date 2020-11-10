---
title: O que é a API de Pesquisa de Entidade do Bing?
titleSuffix: Azure Cognitive Services
description: Conheça os detalhes sobre a API da Pesquisa de Entidade do Bing e aprenda a extrair e pesquisar entidades e locais nas consultas de pesquisa.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: f6f816f6fb4d422a97c98994996967e71f6a1e07
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93084894"
---
# <a name="what-is-bing-entity-search-api"></a>O que é a API de Pesquisa de Entidade do Bing?

> [!WARNING]
> As APIs de Pesquisa do Bing estão migrando dos Serviços Cognitivos para os Serviços de Pesquisa do Bing. A partir de **30 de outubro de 2020** , todas as novas instâncias da Pesquisa do Bing precisam ser provisionadas seguindo o processo documentado [aqui](https://aka.ms/cogsvcs/bingmove).
> As APIs de Pesquisa do Bing provisionadas por meio dos Serviços Cognitivos terão suporte nos próximos três anos ou até o final do seu Contrato Enterprise, o que ocorrer primeiro.
> Para obter instruções sobre a migração, confira [Serviços de Pesquisa do Bing](https://aka.ms/cogsvcs/bingmigration).

A API de Pesquisa de Entidade do Bing envia uma consulta de pesquisa para o Bing e obtém os resultados que incluem entidades e locais. Os resultados de local incluem restaurantes, hotel ou outras empresas locais. O Bing retorna locais se a consulta especifica o nome da empresa local ou solicita um tipo de negócio (por exemplo, restaurantes nas proximidades do meu local). O Bing retorna entidades se a consulta especifica pessoas, locais (atrações turísticas, estados, países, regiões etc.) ou coisas conhecidas.

|Recurso  |Descrição  |
|---------|---------|
|[Sugestões de pesquisa em tempo real](concepts/search-for-entities.md#suggest-search-terms-with-the-bing-autosuggest-api)     | Fornece sugestões de pesquisa que podem ser exibidas como uma lista suspensa conforme os usuários digitam.       | 
| [Desambiguidade de entidade](concepts/search-for-entities.md#the-bing-entity-search-api-response)  | Obter várias entidades para consultas com vários significados possíveis. |
| [Encontrar locais](concepts/search-for-entities.md#find-places) | Pesquisar e retornar informações sobre entidades e empresas locais  |

## <a name="workflow"></a>Fluxo de trabalho

A API de Pesquisa de Entidade do Bing é um serviço Web RESTful, facilitando a chamada em qualquer linguagem de programação que possa fazer solicitações HTTP e analisar JSON. É possível usar o serviço usando a API REST ou o SDK.

1. Crie uma [Conta da API dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com acesso às APIs de Pesquisa do Bing. Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/cognitive-services/) gratuitamente.
2. Envie uma solicitação para a API, com uma consulta de pesquisa válida.
3. Processe a resposta da API analisando a mensagem JSON retornada.

## <a name="next-steps"></a>Próximas etapas

* Experimente a [demonstração interativa](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) para a API de Pesquisa de Entidade do Bing. 
* Para uma introdução rápida à primeira solicitação, experimente um [Início Rápido](quickstarts/csharp.md).
* A seção de referência da [API de Pesquisa de Entidade do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference).
* Os [Requisitos de exibição e uso do Bing](./use-display-requirements.md) especificam os usos aceitáveis do conteúdo e das informações obtidas por meio das APIs de pesquisa do Bing.
* Visite a [página do hub da API de Pesquisa do Bing](../bing-web-search/search-the-web.md) para explorar as outras APIs disponíveis.