---
title: Integre-se com outras aplicações - QnA Maker
description: O QnA Maker integra-se a aplicativos clientes, como bots de bate-papo, bem como a outros serviços de processamento de linguagem natural, como o Language Understanding (LUIS).
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 38b275aa2b8cf5768a2bc95634a7ff4892893eda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78300512"
---
# <a name="design-knowledge-base-for-client-applications"></a>Projetar base de conhecimento para aplicações de clientes

O QnA Maker integra-se a aplicativos clientes, como bots de bate-papo, bem como a outros serviços de processamento de linguagem natural, como o Language Understanding (LUIS).

## <a name="integration-with-a-conversational-client"></a>Integração com um cliente conversador

O QnA Maker integra-se a aplicativos clientes conversacionais, como [o Microsoft Bot Framework](https://dev.botframework.com/). O texto enviado ao QnA Maker não precisa ser limpo ou transformado. QnA Maker aceita línguas naturais e retorna a melhor resposta.

## <a name="create-a-bot-without-writing-any-code"></a>Crie um bot sem escrever nenhum código

Depois de publicar sua base de conhecimento, crie um bot a partir da página **Publicar,** selecionando o botão **Criar bot.** Use o tutorial do [bot](../Quickstarts/create-publish-knowledge-base.md) para saber o que acontece depois de selecionar o botão.

## <a name="providing-multi-turn-conversations"></a>Fornecendo conversas em vários turnos

Um cliente de bot fornece a melhor resposta selecionada de sua base de conhecimento e pode fornecer solicitações de acompanhamento se a resposta for parte de um conjunto de QnA de várias voltas. Aprenda [a](../how-to/multiturn-conversation.md) adicionar conjuntos de perguntas e respostas de vários turnos à sua base de conhecimento.

## <a name="natural-language-processing"></a>Processamento de idioma natural

Embora o QnA Maker processe questões que usam processamento de linguagem natural, ele também pode ser usado uma parte de um sistema maior que responde perguntas de múltiplas bases de conhecimento. Você pode combinar o QnA Maker com outro Serviço Cognitivo, o Entendimento de Linguagem (LUIS), para fornecer processamento de linguagem natural antes de chegar a uma base de conhecimento específica. Saiba mais sobre quando e como usar [o LUIS e o QnA Maker](../../luis/choose-natural-language-processing-service.md?toc=/azure/cognitive-services/qnamaker/toc.json) juntos.

## <a name="next-steps"></a>Próximas etapas

Aprenda conceitos do ciclo [de](development-lifecycle-knowledge-base.md) desenvolvimento para QnA Maker.