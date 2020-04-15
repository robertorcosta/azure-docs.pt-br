---
title: Planeje seu aplicativo - LUIS
description: Descreva intenções e entidades de aplicativo relevantes e crie seus planos de aplicativo no LUIS (Serviço Inteligente de Reconhecimento Vocal).
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: dfed27a05973a2ea2e9a97eaa1c233b847b33d87
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382298"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>Planeje seu esquema de aplicativo LUIS com domínio de assunto e extração de dados

Um esquema de aplicativo LUIS contém [intenções](luis-glossary.md#intent) e [entidades relevantes](luis-glossary.md#entity) para o seu [domínio](luis-glossary.md#domain)de assunto. As intenções classificam [as declarações do](luis-glossary.md#utterance)usuário e as entidades extraem dados das declarações do usuário.

## <a name="identify-your-domain"></a>Identificar seu domínio

Um aplicativo LUIS é centrado em torno de um domínio de assunto. Por exemplo, você pode ter um aplicativo de viagem que lida com reservas de passagens, voos, hotéis e carros de aluguel. Outro aplicativo pode fornecer conteúdo relacionado a exercícios, acompanhamento de esforços fitness e definição de metas. Identificar o domínio ajuda a encontrar palavras ou frases relevantes para o seu domínio.

> [!TIP]
> O LUIS oferece [domínios predefinidos](luis-how-to-use-prebuilt-domains.md) para muitos cenários comuns. Verifique para ver se é possível usar um domínio predefinido como ponto de partida para seu aplicativo.

## <a name="identify-your-intents"></a>Identificar suas intenções

Pense nas [intenções](luis-concept-intent.md) que são importantes para a tarefa do seu aplicativo.

Vamos usar o exemplo de um aplicativo de viagem, com funções para reservar um voo e verificar o clima no destino do usuário. Você pode `BookFlight` definir `GetWeather` as intenções dessas ações.

Em um aplicativo mais complexo com mais funções, você tem mais intenções, e você deve defini-las cuidadosamente para que as intenções não sejam muito específicas. Por `BookFlight` exemplo, `BookHotel` e pode precisar ser `BookInternationalFlight` intenções separadas, mas e `BookDomesticFlight` pode ser muito semelhante.

> [!NOTE]
> É uma melhor prática usar apenas as intenções de que você precisar para executar as funções do seu aplicativo. Se você definir intenções demais, ficará mais difícil para o LUIS classificar declarações corretamente. Se você definir muito poucos, eles podem ser tão gerais que eles se sobrepõem.

Se você não precisar identificar a intenção geral do usuário, `None` adicione todas as expressões de usuário exemplos à intenção. Se o seu aplicativo crescer e precisar de mais intenções, você pode criá-las mais tarde.

## <a name="create-example-utterances-for-each-intent"></a>Crie exemplos de declarações para cada intenção

Para começar, evite criar muitas expressões para cada intenção. Depois de determinar as intenções, crie de 15 a 30 enunciados por intenção. Cada enunciado deve ser diferente das declarações fornecidas anteriormente. Uma boa variedade de expressões incluem contagem geral de palavras, escolha de palavras, verbo tenso e [pontuação](luis-reference-application-settings.md#punctuation-normalization).

Para obter mais informações, consulte [a compreensão de boas expressões para aplicativos LUIS](luis-concept-utterance.md).

## <a name="identify-your-entities"></a>Identificar suas entidades

Nos exemplos de declarações, identifique as entidades que você deseja extrair. Para reservar um voo, você precisa de informações como destino, data, companhia aérea, categoria de passagens e classe de viagem. Crie entidades para esses tipos de dados e marque as [entidades](luis-concept-entity-types.md) nos enunciados de exemplo. Entidades são importantes para realizar uma intenção.

Ao determinar quais entidades usar em seu aplicativo, tenha em mente que existem diferentes tipos de entidades para capturar relações entre tipos de objetos. [Entidades no LUIS](luis-concept-entity-types.md) oferece mais detalhes sobre os tipos diferentes.

> [!TIP]
> A LUIS oferece [entidades pré-construídas](luis-prebuilt-entities.md) para cenários comuns e conversacionais de usuários. Considere o uso de entidades pré-construídas como ponto de partida para o desenvolvimento do seu aplicativo.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aprendendo a vida de desenvolvimento LUIS](luis-concept-app-iteration.md)

