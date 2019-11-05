---
title: Examinar usuário expressão-LUIS
titleSuffix: Azure Cognitive Services
description: Com o aprendizado ativo, seus enunciados de ponto de extremidade são analisados para verificar se a intenção e a entidade estão corretas. O LUIS escolhe os enunciados de ponto de extremidade sobre os quais ele não tem certeza.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: 772d2bdff0a5e1c6cd999482ec4ef6be29dc2bb1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73486788"
---
# <a name="concepts-for-enabling-active-learning-by-reviewing-endpoint-utterances"></a>Conceitos para habilitar o aprendizado ativo revisando declarações de ponto de extremidade
O aprendizado ativo é uma das três estratégias para melhorar a precisão da previsão e a mais fácil de implementar. Com o aprendizado ativo, seus enunciados de ponto de extremidade são analisados para verificar se a intenção e a entidade estão corretas. O LUIS escolhe os enunciados de ponto de extremidade sobre os quais ele não tem certeza.

## <a name="what-is-active-learning"></a>O que é o aprendizado ativo
Aprendizado ativo é um processo de duas etapas. Primeiro, o LUIS seleciona declarações que recebe no ponto de extremidade do aplicativo que precisa de validação. A segunda etapa é executada pelo proprietário do aplicativo ou colaborador para validar as declarações selecionadas para [revisão](luis-how-to-review-endpoint-utterances.md), incluindo a intenção correta e todas as entidades dentro da intenção. Após revisar, treine e publique as declarações no aplicativo novamente. 

## <a name="which-utterances-are-on-the-review-list"></a>Quais declarações estão na lista de revisão
O LUIS adiciona declarações à lista de revisão quando a principal intenção de disparo tiver uma pontuação baixa, ou quando as duas pontuações principais de intenção estiverem muito próximas. 

## <a name="single-pool-for-utterances-per-app"></a>Pool único para enunciados por aplicativo
A lista **Examinar enunciados de ponto de extremidade** não altera com base na versão. Há um único conjunto de enunciados para revisar, independentemente de qual versão a expressão está ativamente editando ou qual versão do aplicativo foi publicado no ponto de extremidade. 

## <a name="where-are-the-utterances-from"></a>De onde são as declarações
As declarações de ponto de extremidade são obtidas de consultas do usuário final no ponto de extremidade HTTP do aplicativo. Se o seu aplicativo não estiver publicado ou ainda não tiver acessos, você não terá declarações para examinar. Se nenhuma ocorrência do ponto de extremidade for recebida para uma intenção ou entidade específica, você não terá declarações para revisar que as contenha. 

## <a name="schedule-review-periodically"></a>Agendar revisões periódicas
A revisão de declarações sugeridas não precisa ser feita diariamente, mas deve fazer parte de sua manutenção regular do LUIS. 

## <a name="delete-review-items-programmatically"></a>Excluir itens de revisão programaticamente
Use a API **[Excluir não rotulada do declarações](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9)** . Faça backup dessas declarações antes da exclusão **[exportando os arquivos de log](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)** .

## <a name="next-steps"></a>Próximas etapas

* Saiba como [revisar](luis-how-to-review-endpoint-utterances.md) declarações do ponto de extremidade
