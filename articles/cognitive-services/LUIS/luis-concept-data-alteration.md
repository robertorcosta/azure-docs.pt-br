---
title: Data alteration - LUIS
titleSuffix: Azure Cognitive Services
description: Saiba como os dados podem ser alterados antes das previsões no LUIS (Reconhecimento vocal)
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: a199821c4db7fd8131ec54700b8c999dfe604a6e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74222017"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>Alterar os dados de declaração antes ou durante a previsão
O LUIS fornece maneiras de manipular a declaração antes ou durante a previsão. These include [fixing spelling](luis-tutorial-bing-spellcheck.md), and fixing timezone issues for prebuilt [datetimeV2](luis-reference-prebuilt-datetimev2.md). 

## <a name="correct-spelling-errors-in-utterance"></a>Corrigir erros de ortografia na declaração

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]


O LUIS usa a [API de Verificação Ortográfica do Bing V7](../Bing-Spell-Check/overview.md) para corrigir erros de ortografia na declaração. O LUIS precisa da chave associada a esse serviço. Crie a chave e adicione-a como um parâmetro querystring no [ponto de extremidade](https://go.microsoft.com/fwlink/?linkid=2092356). 

<!--
You can also correct spelling errors in the **Test** panel by [entering the key](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key is kept as a session variable in the browser for the Test panel. Add the key to the Test panel in each browser session you want spelling corrected. 

Usage of the key in the test panel and at the endpoint count toward the [key usage](https://azure.microsoft.com/pricing/details/cognitive-services/spellcheck-api/) quota. LUIS implements Bing Spell Check limits for text length. 

-->

O ponto de extremidade requer dois parâmetros para as correções ortográfica funcionarem:

|Param|Value|
|--|--|
|`spellCheck`|Booliano|
|`bing-spell-check-subscription-key`|Chave de ponto de extremidade de [API de Verificação Ortográfica do Bing V7](https://azure.microsoft.com/services/cognitive-services/spell-check/)|

Quando a [API de Verificação Ortográfica do Bing V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) detecta um erro, a declaração original e a declaração corrigida são retornadas junto com as previsões do ponto de extremidade.

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Resposta de ponto de extremidade de previsão V2](#tab/V2)

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Resposta de ponto de extremidade de previsão V3](#tab/V3)
 
```JSON
{
    "query": "Book a flite to London?",
    "prediction": {
        "normalizedQuery": "book a flight to london?",
        "topIntent": "BookFlight",
        "intents": {
            "BookFlight": {
                "score": 0.780123
            }
        },
        "entities": {},
    }
}
```

* * * 

### <a name="list-of-allowed-words"></a>List of allowed words
The Bing spell check API used in LUIS does not support a list of words to ignore during the spell check alterations. If you need to allow a list of words or acronyms, process the utterance in the client application before sending the utterance to LUIS for intent prediction.

## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>Alterar o fuso horário da entidade datetimeV2 predefinida
When a LUIS app uses the prebuilt [datetimeV2](luis-reference-prebuilt-datetimev2.md) entity, a datetime value can be returned in the prediction response. O fuso horário da solicitação é usado para determinar o datetime correto a ser retornado. Se a solicitação for proveniente de um bot ou de outro aplicativo centralizado antes de chegar ao LUIS, corrija o fuso horário que o LUIS usa. 

### <a name="endpoint-querystring-parameter"></a>Parâmetro querystring do ponto de extremidade
O fuso horário é corrigido adicionando o fuso horário do usuário ao [ponto de extremidade](https://go.microsoft.com/fwlink/?linkid=2092356) usando o parâmetro `timezoneOffset`. O valor de `timezoneOffset` deve ser o número positivo ou negativo, em minutos, para alterar a hora.  

|Param|Value|
|--|--|
|`timezoneOffset`|O número positivo ou negativo, em minutos|

### <a name="daylight-savings-example"></a>Exemplo de horário de verão
Se você precisar que o datetimeV2 predefinido ajuste o horário de verão, deverá usar o parâmetro querystring `timezoneOffset` com um valor +/- em minutos para a consulta de [ponto de extremidade](https://go.microsoft.com/fwlink/?linkid=2092356).

#### <a name="v2-prediction-endpoint-requesttabv2"></a>[Solicitação de ponto de extremidade de previsão V2](#tab/V2)

Adicionar 60 minutos: 

https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?**timezoneOffset=60**&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}

Remover 60 minutos: 

https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?**timezoneOffset=-60**&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}

#### <a name="v3-prediction-endpoint-requesttabv3"></a>[Solicitação de ponto de extremidade de previsão V3](#tab/V3)

Adicionar 60 minutos:

https://{region}.api.cognitive.microsoft.com/luis/v3.0-preview/apps/{appId}/slots/production/predict?query=Turn the lights on?**timezoneOffset=60**&spellCheck={boolean}&bing-spell-check-subscription-key={string}&log={boolean}

Remover 60 minutos: 

https://{region}.api.cognitive.microsoft.com/luis/v3.0-preview/apps/{appId}/slots/production/predict?query=Turn the lights on?**timezoneOffset=-60**&spellCheck={boolean}&bing-spell-check-subscription-key={string}&log={boolean}

Saiba mais sobre o [ponto de extremidade de previsão V3](luis-migration-api-v3.md).

* * * 

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>O código em C# determina o valor correto de timezoneOffset
O código em C# a seguir usa o método [FindSystemTimeZoneById](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples) da classe [TimeZoneInfo](https://docs.microsoft.com/dotnet/api/system.timezoneinfo) para determinar o `timezoneOffset` correto com base na hora do sistema:

```CSharp
// Get CST zone id
TimeZoneInfo targetZone = TimeZoneInfo.FindSystemTimeZoneById("Central Standard Time");

// Get local machine's value of Now
DateTime utcDatetime = DateTime.UtcNow;

// Get Central Standard Time value of Now
DateTime cstDatetime = TimeZoneInfo.ConvertTimeFromUtc(utcDatetime, targetZone);

// Find timezoneOffset
int timezoneOffset = (int)((cstDatetime - utcDatetime).TotalMinutes);
```

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Corrigir erros de ortografia com este tutorial](luis-tutorial-bing-spellcheck.md)
