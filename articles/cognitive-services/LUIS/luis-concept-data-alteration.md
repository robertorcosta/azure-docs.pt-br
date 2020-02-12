---
title: Alteração de dados-LUIS
description: Saiba como os dados podem ser alterados antes das previsões no LUIS (Reconhecimento vocal)
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 5547724a6333d248a7ba4e9aeecaaa8f331feb7d
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77148259"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>Alterar os dados de declaração antes ou durante a previsão
O LUIS fornece maneiras de manipular a declaração antes ou durante a previsão. Isso inclui a [correção ortográfica](luis-tutorial-bing-spellcheck.md)e a correção de problemas de fuso horário para [datetimeV2](luis-reference-prebuilt-datetimev2.md)predefinidos.

## <a name="correct-spelling-errors-in-utterance"></a>Corrigir erros de ortografia na declaração


### <a name="v3-runtime"></a>Tempo de execução v3

Pré-processar texto para correções ortográficas antes de enviar o expressão para LUIS. Use o exemplo declarações com a grafia correta para garantir que você obtenha as previsões corretas.

Use [verificação ortográfica do Bing](../bing-spell-check/overview.md) para corrigir o texto antes de enviá-lo para Luis.

### <a name="prior-to-v3-runtime"></a>Tempo de execução anterior ao v3

O LUIS usa a [API de Verificação Ortográfica do Bing V7](../Bing-Spell-Check/overview.md) para corrigir erros de ortografia na declaração. O LUIS precisa da chave associada a esse serviço. Crie a chave e adicione-a como um parâmetro querystring no [ponto de extremidade](https://go.microsoft.com/fwlink/?linkid=2092356).

O ponto de extremidade requer dois parâmetros para as correções ortográfica funcionarem:

|Param|Valor|
|--|--|
|`spellCheck`|booleano|
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

### <a name="list-of-allowed-words"></a>Lista de palavras permitidas
A API de verificação ortográfica do Bing usada em LUIS não dá suporte a uma lista de palavras a serem ignoradas durante as alterações de verificação ortográfica. Se você precisar permitir uma lista de palavras ou acrônimos, processe o expressão no aplicativo cliente antes de enviar o expressão para LUIS para previsão de intenção.

## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>Alterar o fuso horário da entidade datetimeV2 predefinida
Quando um aplicativo LUIS usa a entidade [datetimeV2](luis-reference-prebuilt-datetimev2.md) predefinida, um valor DateTime pode ser retornado na resposta de previsão. O fuso horário da solicitação é usado para determinar o datetime correto a ser retornado. Se a solicitação for proveniente de um bot ou de outro aplicativo centralizado antes de chegar ao LUIS, corrija o fuso horário que o LUIS usa.

### <a name="endpoint-querystring-parameter"></a>Parâmetro querystring do ponto de extremidade
O fuso horário é corrigido adicionando o fuso horário do usuário ao [ponto de extremidade](https://go.microsoft.com/fwlink/?linkid=2092356) usando o parâmetro `timezoneOffset`. O valor de `timezoneOffset` deve ser o número positivo ou negativo, em minutos, para alterar a hora.

|Param|Valor|
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

https://{região}. API. cognitiva. Microsoft. com/Luis/v 3.0-Preview/apps/{appId}/Slots/produção/previsão? consulta = ativar as luzes? **timezoneOffset = 60**& verificação ortográfica = {booliano} & Bing-grafia-check-Subscription-Key = {string} & log = {Boolean}

Remover 60 minutos:

https://{região}. API. cognitiva. Microsoft. com/Luis/v 3.0-Preview/apps/{appId}/Slots/produção/previsão? consulta = ativar as luzes? **timezoneOffset =-60**& verificação ortográfica = {booliano} & Bing-grafia-check-Subscription-Key = {string} & log = {Boolean}

Saiba mais sobre o [ponto de extremidade de previsão V3](luis-migration-api-v3.md).

* * *

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>O código em C# determina o valor correto de timezoneOffset
O código em C# a seguir usa o método [FindSystemTimeZoneById](https://docs.microsoft.com/dotnet/api/system.timezoneinfo) da classe [TimeZoneInfo](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples) para determinar o `timezoneOffset` correto com base na hora do sistema:

```csharp
// Get CST zone id
TimeZoneInfo targetZone = TimeZoneInfo.FindSystemTimeZoneById("Central Standard Time");

// Get local machine's value of Now
DateTime utcDatetime = DateTime.UtcNow;

// Get Central Standard Time value of Now
DateTime cstDatetime = TimeZoneInfo.ConvertTimeFromUtc(utcDatetime, targetZone);

// Find timezoneOffset
int timezoneOffset = (int)((cstDatetime - utcDatetime).TotalMinutes);
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Corrigir erros de ortografia com este tutorial](luis-tutorial-bing-spellcheck.md)
