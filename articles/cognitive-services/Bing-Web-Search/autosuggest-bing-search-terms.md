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
ms.openlocfilehash: 15a2d3b433236d74f3fa3d09e38071bacae02508
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93078825"
---
# <a name="autosuggest-bing-search-terms-in-your-application"></a>Termos de pesquisa de Sugestão Automática do Bing no aplicativo

> [!WARNING]
> APIs de Pesquisa do Bing estão mudando de serviços cognitivas para serviços Pesquisa do Bings. A partir de **30 de outubro de 2020** , todas as novas instâncias do pesquisa do Bing precisam ser provisionadas seguindo o processo documentado [aqui](https://aka.ms/cogsvcs/bingmove).
> APIs de Pesquisa do Bing provisionado usando serviços cognitivas terão suporte nos próximos três anos ou até o final do seu Enterprise Agreement, o que ocorrer primeiro.
> Para obter instruções de migração, consulte [serviços de pesquisa do Bing](https://aka.ms/cogsvcs/bingmigration).

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

## <a name="see-also"></a>Veja também  

* [Referência da API de Pesquisa na Web do Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
