---
title: Plan your app - LUIS
titleSuffix: Azure Cognitive Services
description: Descreva intenções e entidades de aplicativo relevantes e crie seus planos de aplicativo no LUIS (Serviço Inteligente de Reconhecimento Vocal).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: 6a155f4c43da03ccdc40d289742918973aa6da7b
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326785"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>Plan your LUIS app schema with subject domain and data extraction

A LUIS app schema contains [intents](luis-glossary.md#intent) and [entities](luis-glossary.md#entity) relevant to your subject [domain](luis-glossary.md#domain). The intents classify user [utterances](luis-glossary.md#utterance), and the entities extract data from the user utterances.

## <a name="identify-your-domain"></a>Identificar seu domínio

A LUIS app is centered around a subject domain. For example, you may have a travel app that handles booking of tickets, flights, hotels, and rental cars. Outro aplicativo pode fornecer conteúdo relacionado a exercícios, acompanhamento de esforços fitness e definição de metas. Identifying the domain helps you find words or phrases that are relevant to your domain.

> [!TIP]
> O LUIS oferece [domínios predefinidos](luis-how-to-use-prebuilt-domains.md) para muitos cenários comuns. Verifique para ver se é possível usar um domínio predefinido como ponto de partida para seu aplicativo.

## <a name="identify-your-intents"></a>Identificar suas intenções

Think about the [intents](luis-concept-intent.md) that are important to your application's task.

Vamos usar o exemplo de um aplicativo de viagem, com funções para reservar um voo e verificar o clima no destino do usuário. You can define the `BookFlight` and `GetWeather` intents for these actions.

In a more complex app with more functions, you have more intents, and you should define them carefully so the intents aren't too specific. For example, `BookFlight` and `BookHotel` may need to be separate intents, but `BookInternationalFlight` and `BookDomesticFlight` may be too similar.

> [!NOTE]
> É uma melhor prática usar apenas as intenções de que você precisar para executar as funções do seu aplicativo. Se você definir intenções demais, ficará mais difícil para o LUIS classificar declarações corretamente. If you define too few, they may be so general that they overlap.

If you don't need to identify overall user intention, add all the example user utterances to the `None` intent. If your app grows into needing more intents, you can create them later.

## <a name="create-example-utterances-for-each-intent"></a>Crie exemplos de declarações para cada intenção

To begin with, avoid creating too many utterances for each intent. Once you have determined the intents, create 15 to 30 example utterances per intent. Each utterance should be different from the previously provided utterances. A good variety in utterances include overall word count, word choice, verb tense, and punctuation.

For more information, see [understanding good utterances for LUIS apps](luis-concept-utterance.md).

## <a name="identify-your-entities"></a>Identificar suas entidades

Nos exemplos de declarações, identifique as entidades que você deseja extrair. To book a flight, you need information like the destination, date, airline, ticket category, and travel class. Create entities for these data types and then mark the [entities](luis-concept-entity-types.md) in the example utterances. Entities are important for accomplishing an intent.

When determining which entities to use in your app, keep in mind that there are different types of entities for capturing relationships between object types. [Entidades no LUIS](luis-concept-entity-types.md) oferece mais detalhes sobre os tipos diferentes.

> [!TIP]
> LUIS offers [prebuilt entities](luis-prebuilt-entities.md) for common, conversational user scenarios. Consider using prebuilt entities as a starting point for your application development.

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Learning the LUIS development lifecylce](luis-concept-app-iteration.md)

