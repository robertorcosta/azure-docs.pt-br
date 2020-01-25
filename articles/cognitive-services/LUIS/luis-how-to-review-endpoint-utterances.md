---
title: Examinar usuário declarações-LUIS
titleSuffix: Azure Cognitive Services
description: Examine o declarações capturado pelo aprendizado ativo para selecionar entidades de intenção e marca para declarações do mundo de leitura; aceitar alterações, treinar e publicar.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: diberry
ms.openlocfilehash: ed0b9450217b06ff145641d9e268ccee28ee49b2
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76710590"
---
# <a name="how-to-improve-the-luis-app-by-reviewing-endpoint-utterances"></a>Como melhorar o aplicativo LUIS examinando o ponto de extremidade declarações

O processo de revisão do ponto de extremidade declarações para previsões corretas é chamado de [aprendizado ativo](luis-concept-review-endpoint-utterances.md). O aprendizado ativo captura consultas de ponto de extremidade e seleciona o ponto de extremidade do usuário declarações que não tem certeza de. Revise essas declarações para selecionar as entidades de intenção e marca para essas declarações de leitura do mundo. Aceite essas alterações em seu exemplo declarações, em seguida, treine e publique. LUIS, em seguida, identifica declarações com mais precisão.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="enable-active-learning"></a>Habilitar o aprendizado ativo

Para habilitar o aprendizado ativo, você deve registrar as consultas de usuário. Isso é feito chamando a [consulta de ponto de extremidade](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) com o parâmetro e valor `log=true` QueryString.

## <a name="correct-intent-predictions-to-align-utterances"></a>Corrigir previsões de intenção para alinhar declarações

Cada declaração tem uma intenção sugerida exibida na coluna **Intenção alinhada**.

> [!div class="mx-imgBorder"]
> [![revisar ponto de extremidade declarações que LUIS não tem certeza de](./media/label-suggested-utterances/review-endpoint-utterances.png)](./media/label-suggested-utterances/review-endpoint-utterances.png#lightbox)

Se você concordar com essa intenção, selecione a marca de seleção. Se você não concordar com a sugestão, selecione a intenção correta na lista suspensa de intenções alinhadas, e marque a caixa de seleção à direita da intenção alinhada. Depois de selecionar na marca de seleção, o expressão é movido para a intenção e removido da lista **examinar ponto de extremidade declarações** .

> [!TIP]
> É importante ir até a página de detalhes da intenção para revisar e corrigir as previsões de entidade de todos os exemplos declarações da lista **examinar ponto de extremidade declarações** .

## <a name="delete-utterance"></a>Excluir a declaração

Cada declaração pode ser excluída da lista de revisão. Depois de excluída, ela não aparecerá na lista novamente. Isso será verdadeiro mesmo se o usuário inserir a mesma declaração do ponto de extremidade.

Se você não tiver certeza se deve excluir o expressão, mova-o para a intenção nenhum ou crie uma nova intenção, como `miscellaneous` e mova o expressão para essa intenção.

## <a name="disable-active-learning"></a>Desabilitar o aprendizado ativo

Para desabilitar o aprendizado ativo, não faça log de consultas de usuário. Isso é feito definindo a [consulta de ponto de extremidade](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) com o `log=false` parâmetro e o valor de QueryString ou não usando o valor de QueryString porque o valor padrão é false.

## <a name="next-steps"></a>Próximos passos

Para testar como o desempenho melhora depois que você rotula as declarações sugeridas, você poderá acessar o console de teste selecionando **Teste** no painel superior. Para obter instruções sobre como testar seu aplicativo usando o console de teste, consulte [Treinar e testar seu aplicativo](luis-interactive-test.md).
