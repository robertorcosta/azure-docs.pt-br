---
title: O ponto de extremidade da API de Pesquisa de Entidade do Bing
titleSuffix: Azure Cognitive Services
description: A API de Pesquisa de Entidade do Bing possui um ponto de extremidade que retorna entidades da Web com base em uma consulta. Esses resultados de pesquisa são retornados em JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: e7f5af42d0bb0079746cc9e64b621adfebd565d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74072663"
---
# <a name="bing-entity-search-api-endpoint"></a>Ponto de extremidade da API de Pesquisa de Entidade do Bing


A API de Pesquisa de Entidade do Bing possui um ponto de extremidade que retorna entidades da Web com base em uma consulta. Esses resultados de pesquisa são retornados em JSON.

## <a name="get-entity-results-from-the-endpoint"></a>Obter os resultados da entidade do ponto de extremidade

Para obter os resultados da entidade usando a **API do Bing**, envie uma solicitação `GET` para o ponto de extremidade a seguir. Use [cabeçalhos](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers) e [parâmetros de consulta](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters) para personalizar sua solicitação de pesquisa. As solicitações de pesquisa podem ser enviadas usando o parâmetro `?q=`.

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [O que é a API de Pesquisa de Entidade do Bing?](overview.md)

## <a name="see-also"></a>Confira também 

Para obter mais informações sobre cabeçalhos, parâmetros, códigos de mercado, objetos de resposta, erros e outros, confira o artigo de referência da [API de Pesquisa de Entidade do Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference).
