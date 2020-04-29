---
title: Integre-se a outros aplicativos-QnA Maker
description: O QnA Maker se integra a aplicativos cliente, como bots de bate-papo, bem como a outros serviços de processamento de idioma natural, como o Reconhecimento vocal (LUIS).
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: c1edbfb6badfb73ce08a99709da0f8bfb61b7dc3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "80804180"
---
# <a name="design-knowledge-base-for-client-applications"></a>Criar base de dados de conhecimento para aplicativos cliente

O QnA Maker se integra a aplicativos cliente, como bots de bate-papo, bem como a outros serviços de processamento de idioma natural, como o Reconhecimento vocal (LUIS).

## <a name="integration-with-a-conversational-client"></a>Integração com um cliente de conversação

O QnA Maker integra-se a aplicativos cliente de conversação, como [o Microsoft bot Framework](https://dev.botframework.com/). O texto enviado para QnA Maker não precisa ser limpo ou transformado. QnA Maker aceita idiomas naturais e retorna a melhor resposta.

## <a name="create-a-bot-without-writing-any-code"></a>Criar um bot sem escrever nenhum código

Depois de publicar sua base de dados de conhecimento, crie um bot na página **publicar** selecionando o botão **criar bot** . Use o [tutorial de bot](../Quickstarts/create-publish-knowledge-base.md) para saber o que acontece depois de selecionar o botão.

## <a name="providing-multi-turn-conversations"></a>Fornecendo conversas com vários folheios

Um cliente de bot fornece a melhor resposta selecionada de sua base de dados de conhecimento e pode fornecer prompts de acompanhamento se a resposta fizer parte de um par de QnA de vários folheios. Saiba [como](../how-to/multiturn-conversation.md) Adicionar conjuntos de perguntas de conversa e de resposta para sua base de dados de conhecimento.

## <a name="natural-language-processing"></a>Processamento de idioma natural

Enquanto o QnA Maker processa perguntas que usam o processamento de idioma natural, ele também pode ser usado como parte de um sistema maior que responde a perguntas de várias bases de dados de conhecimento. Você pode combinar QnA Maker com outro serviço cognitiva, Reconhecimento vocal (LUIS), para fornecer processamento de idioma natural antes de chegar a uma base de dados de conhecimento específica. Saiba mais sobre quando e como usar o [Luis e o QnA Maker](../../luis/choose-natural-language-processing-service.md?toc=/azure/cognitive-services/qnamaker/toc.json) juntos.

## <a name="next-steps"></a>Próximas etapas

Aprenda os [conceitos](development-lifecycle-knowledge-base.md) do ciclo de desenvolvimento para QnA Maker.