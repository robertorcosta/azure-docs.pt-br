---
title: Termos de pesquisa de sugestão automática - API de Pesquisa na Web do Bing
titleSuffix: Azure Cognitive Services
description: Emparelhe a API de Pesquisa na Web do Bing com a API de Sugestão Automática do Bing para fornecer aos usuários uma experiência de pesquisa aprimorada.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/03/2019
ms.author: aahi
ms.openlocfilehash: 0fb62966c78eb19c1daf9294efba786a267ae200
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "66384853"
---
# <a name="autosuggest-bing-search-terms-in-your-application"></a>Termos de pesquisa de Sugestão Automática do Bing no aplicativo

Se você fornecer uma caixa de pesquisa em que o usuário insere seu termo de pesquisa, use a [API de Sugestão Automática do Bing](../bing-autosuggest/get-suggested-search-terms.md) para melhorar a experiência. A API retorna cadeias de caracteres de consulta sugeridas com base em termos de pesquisa parciais como os tipos de usuário.

Depois que o usuário insere um termo de pesquisa, ele deve ser codificado como URL antes que o parâmetro de consulta [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query) seja definido. Por exemplo, se o usuário inserir *sailing dinghies* (bote à vela), defina `q` como `sailing+dinghies` ou `sailing%20dinghies`.

Se o termo de consulta contiver um erro de ortografia, a resposta da pesquisa incluirá um objeto [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#querycontext). O objeto mostra a ortografia original e a ortografia corrigida usada pelo Bing para a pesquisa.

```json
"queryContext": {
    "originalQuery": "sialing dingy for sale",
    "alteredQuery": "sailing dinghy for sale",
    "alterationOverrideQuery": "+sialing +dingy for sale"
}
```

Você pode usar essas informações para, ao exibir os resultados da pesquisa, informar o usuário de que você modificou a cadeia de caracteres de consulta fornecida por ele.

![Exemplo de experiência do usuário do contexto de consulta](./media/cognitive-services-bing-web-api/bing-query-context.PNG)  

## <a name="next-steps"></a>Próximas etapas  

* Revise o exemplo [Respostas da API de Pesquisa na Web do Bing](search-responses.md).  

## <a name="see-also"></a>Confira também  

* [Referência da API de Pesquisa na Web do Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
