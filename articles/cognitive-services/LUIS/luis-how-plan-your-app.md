---
title: Planejar seu aplicativo-LUIS
description: Descreva intenções e entidades de aplicativo relevantes e crie seus planos de aplicativo no LUIS (Serviço Inteligente de Reconhecimento Vocal).
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: dfed27a05973a2ea2e9a97eaa1c233b847b33d87
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "81382298"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>Planejar o esquema do aplicativo LUIS com a extração de dados e domínio do assunto

Um esquema de aplicativo LUIS contém [intenções](luis-glossary.md#intent) e [entidades](luis-glossary.md#entity) relevantes para seu [domínio](luis-glossary.md#domain)de assunto. As intenções classificam [declarações](luis-glossary.md#utterance)do usuário e as entidades extraem dados do declarações do usuário.

## <a name="identify-your-domain"></a>Identificar seu domínio

Um aplicativo LUIS é centralizado em um domínio de assunto. Por exemplo, você pode ter um aplicativo de viagem que lide com a reserva de ingressos, vôos, hotéis e carros de aluguel. Outro aplicativo pode fornecer conteúdo relacionado a exercícios, acompanhamento de esforços fitness e definição de metas. Identificar o domínio ajuda a localizar palavras ou frases relevantes para seu domínio.

> [!TIP]
> O LUIS oferece [domínios predefinidos](luis-how-to-use-prebuilt-domains.md) para muitos cenários comuns. Verifique para ver se é possível usar um domínio predefinido como ponto de partida para seu aplicativo.

## <a name="identify-your-intents"></a>Identificar suas intenções

Pense nas [intenções](luis-concept-intent.md) que são importantes para a tarefa do seu aplicativo.

Vamos usar o exemplo de um aplicativo de viagem, com funções para reservar um voo e verificar o clima no destino do usuário. Você pode definir as `BookFlight` tentativas `GetWeather` e para essas ações.

Em um aplicativo mais complexo com mais funções, você tem mais intenções e deve defini-las com cuidado para que as intenções não sejam muito específicas. Por exemplo, `BookFlight` e `BookHotel` talvez precisem de tentativas separadas, mas `BookInternationalFlight` `BookDomesticFlight` pode ser muito semelhante.

> [!NOTE]
> É uma melhor prática usar apenas as intenções de que você precisar para executar as funções do seu aplicativo. Se você definir intenções demais, ficará mais difícil para o LUIS classificar declarações corretamente. Se você definir poucos, eles poderão ser tão gerais que se sobrepõem.

Se você não precisar identificar a intenção geral do usuário, adicione todos os declarações de usuário de `None` exemplo à intenção. Se seu aplicativo aumentar a necessidade de mais intenções, você poderá criá-los mais tarde.

## <a name="create-example-utterances-for-each-intent"></a>Crie exemplos de declarações para cada intenção

Para começar, Evite criar muitas declarações para cada tentativa. Depois de determinar as intenções, crie um exemplo de 15 a 30 declarações por tentativa. Cada expressão deve ser diferente do declarações fornecido anteriormente. Uma boa variedade no declarações inclui contagem geral de palavras, opções de palavras, conjugação de verbo e [Pontuação](luis-reference-application-settings.md#punctuation-normalization).

Para obter mais informações, consulte [Understanding Good declarações for Luis apps](luis-concept-utterance.md).

## <a name="identify-your-entities"></a>Identificar suas entidades

Nos exemplos de declarações, identifique as entidades que você deseja extrair. Para reservar um vôo, você precisa de informações como o destino, a data, a companhia aérea, a categoria de tíquete e a classe de viagem. Crie entidades para esses tipos de dados e marque as [entidades](luis-concept-entity-types.md) no exemplo declarações. As entidades são importantes para realizar uma intenção.

Ao determinar quais entidades usar em seu aplicativo, tenha em mente que existem diferentes tipos de entidades para capturar relações entre tipos de objeto. [Entidades no LUIS](luis-concept-entity-types.md) oferece mais detalhes sobre os tipos diferentes.

> [!TIP]
> O LUIS oferece [entidades predefinidas](luis-prebuilt-entities.md) para cenários de usuário comuns e de conversação. Considere o uso de entidades predefinidas como um ponto de partida para o desenvolvimento de aplicativos.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aprendendo o lifecylce de desenvolvimento do LUIS](luis-concept-app-iteration.md)

