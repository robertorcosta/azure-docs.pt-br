---
title: Intenções e entidades - LUIS
titleSuffix: Azure Cognitive Services
description: Uma única intenção representa uma tarefa ou ação que o usuário deseja executar. É uma finalidade ou uma meta expressa no enunciado de um usuário. Defina um conjunto de intenções que corresponda às ações que os usuários desejem executar em seu aplicativo.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: 309a2592dbac2918aeb532fbe91e33d296f4e5a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220969"
---
# <a name="intents-in-your-luis-app"></a>Intenções em seu aplicativo LUIS

Uma intenção representa uma tarefa ou ação que o usuário deseja executar. É uma finalidade ou meta expressa na [declaração](luis-concept-utterance.md) de um usuário.

Defina um conjunto de intenções que corresponda às ações que os usuários desejem executar em seu aplicativo. Por exemplo, um aplicativo de viagem define várias intenções:

Intenções do aplicativo de viagem   |   Exemplo de enunciados   | 
------|------|
 BookFlight     |   "Reserve um voo para mim para o Rio na semana que vem" <br/> "Coloque-me num voo para o Rio no dia 24" <br/> "Preciso de uma passagem de avião no próximo domingo para o Rio de Janeiro"    |
 Saudação     |   "Oi" <br/>"Olá" <br/>"Bom dia"  |
 CheckWeather | "Como está o clima em Boston?" <br/> "Mostre-me a previsão para este fim de semana" |
 Nenhum         | "Encontre uma receita de biscoitos para mim"<br>"Os Lakers venceram?" |

Todas as aplicações vêm com a intenção predefinida, "[None](#none-intent)", que é a intenção de recuo. 

## <a name="prebuilt-domains-provide-intents"></a>Domínios predefinidos fornecem intenções
Além das intenções que você define, você pode usar intenções pré-construídas de um dos [domínios pré-construídos](luis-how-to-use-prebuilt-domains.md). 

## <a name="return-all-intents-scores"></a>Retornar pontuações de todas as intenções
Atribua uma declaração a uma única intenção. Quando LUIS recebe uma declaração no ponto final, por padrão, ele retorna a intenção superior para essa expressão. 

Se você quiser as pontuações para todas as intenções para o enunciado, você pode fornecer uma bandeira na seqüência de consulta da API de previsão. 

|Versão de API de previsão|Sinalizador|
|--|--|
|V2|`verbose=true`|
|V3|`show-all-intents=true`|

## <a name="intent-compared-to-entity"></a>Intenção comparada com a entidade
A intenção representa a ação que o aplicativo deve tomar para o usuário e é baseada em toda a expressão. Uma declaração pode ter apenas uma intenção de pontuação principal, mas pode ter muitas entidades. 

<a name="how-do-intents-relate-to-entities"></a>

 Crie uma intenção quando a _intenção_ do usuário disparar uma ação em seu aplicativo cliente, como uma chamada à função checkweather(). Em seguida, crie entidades para representar parâmetros necessários para executar a ação. 

|Intencional   | Entidade | Exemplo de enunciado   | 
|------------------|------------------------------|------------------------------|
| CheckWeather | { "type": "location", "entity": "seattle" }<br>{ "type": "builtin.datetimeV2.date","entity": "tomorrow","resolution":"2018-05-23" } | Como está o `Seattle` `tomorrow`tempo? |
| CheckWeather | { "type": "date_range", "entity": "this weekend" } | Mostre-me a previsão para `this weekend` | 
||||

## <a name="prebuilt-domain-intents"></a>Intenções de domínio predefinidas

[Domínios pré-construídos](luis-how-to-use-prebuilt-domains.md) fornecem intenções com expressões. 

## <a name="none-intent"></a>Intenção None

A intenção **None** é criada, mas deixada vazia de propósito. A intenção **None** é uma intenção necessária e não pode ser excluída nem renomeada. Preencha-a com declarações que estejam fora de seu domínio.

A intenção **de Nenhum** é a intenção de recuo, importante em cada aplicativo, e deve ter 10% do total de declarações. Ela é usada para ensinar ao LUIS declarações que não são importantes no domínio de aplicativo (área de assunto). Se você não adicionar nenhuma declaração para a intenção **None**, o LUIS forçará uma declaração que está fora do domínio para uma das intenções do domínio. Isso distorcerá as pontuações de previsão ensinando ao LUIS a intenção incorreta para a declaração. 

Quando uma declaração é prevista como a intenção De Nenhum, o aplicativo cliente pode fazer mais perguntas ou fornecer um menu para direcionar o usuário para escolhas válidas. 

## <a name="negative-intentions"></a>Intenções negativas 
Se desejar determinar intenções negativas e positivas, como "Eu **quero** um carro" e "Eu **não** quero um carro", será possível criar duas intenções (uma positiva e uma negativa) e adicionar declarações adequadas para cada uma. Ou é possível criar uma única intenção e marcar os dois termos positivos e negativos diferentes como uma entidade.  

## <a name="intents-and-patterns"></a>Intenções e padrões

Se você tiver expressões de exemplo, que podem ser definidas em parte ou no todo como uma expressão regular, considere usar a [entidade de expressão regular](luis-concept-entity-types.md#regular-expression-entity) emparelhada com um [padrão](luis-concept-patterns.md). 

O uso de uma entidade de expressão regular garante a extração de dados para que o padrão seja compatível. A correspondência de padrão garante que uma intenção exata seja devolvida. 

## <a name="intent-balance"></a>Equilíbrio de intenções
As intenções do domínio de aplicativo devem ter um equilíbrio de declarações em cada intenção. Não tenha uma intenção com 10 declarações e outra intenção com 500 declarações. Isso não está equilibrado. Se você tiver essa situação, examine a intenção com 500 declarações para ver se muitas intenções podem ser reorganizadas em um [padrão](luis-concept-patterns.md). 

A intenção **None** não está incluída no equilíbrio. A intenção deve conter 10% das declarações totais no aplicativo.

## <a name="intent-limits"></a>Limites de intenção
Examine os [limites](luis-boundaries.md#model-boundaries) para entender a quantidade de intenções que você pode adicionar a um modelo. 

### <a name="if-you-need-more-than-the-maximum-number-of-intents"></a>Se precisar de mais do que o número máximo de intenções 
Primeiro, considere se o sistema está usando um número excessivo de intenções. 

### <a name="can-multiple-intents-be-combined-into-single-intent-with-entities"></a>Várias intenções poderão ser combinadas em uma única intenção com entidades 
Intenções muito semelhantes poderão dificultar sua diferenciação pelo LUIS. As intenções devem ser variadas o suficiente para capturar as principais tarefas que o usuário está solicitando, mas elas não precisam capturar todo caminho que seu código usa. Por exemplo, BookFlight e FlightCustomerService podem ser intenções separadas em um aplicativo de viagem, mas BookInternationalFlight e BookDomesticFlight são semelhantes demais. Se o sistema precisar diferenciá-las, use entidades ou outra lógica, em vez de intenções. 

### <a name="dispatcher-model"></a>Modelo de Dispatcher
Saiba como combinar aplicativos LUIS e QnA Maker com o [modelo de expedição](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps). 

### <a name="request-help-for-apps-with-significant-number-of-intents"></a>Solicite ajuda para aplicativos com quantidades significativas de intenções
Se a redução do número de intenções ou a divisão das suas intenções em vários aplicativos não funcionar para você, contate o suporte. Se a assinatura do Azure incluir serviços de suporte, contate o [suporte técnico do Azure](https://azure.microsoft.com/support/options/). 

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [entidades](luis-concept-entity-types.md), que são palavras importantes relevantes para intenções
* Saiba como [adicionar e gerenciar intenções](luis-how-to-add-intents.md) em seu aplicativo LUIS.
* Examine as [melhores práticas](luis-concept-best-practices.md) da intenção
