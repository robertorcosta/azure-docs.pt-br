---
title: Rotular o enunciado de exemplo da entidade
titleSuffix: Azure Cognitive Services
description: Saiba como rotular uma entidade de aprendizado de máquina com subcomponentes em um enunciado de exemplo em uma página de detalhes da intenção do portal do LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 87b7375b2da0effbc18fff6a7e9d67383c93a403
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74134117"
---
# <a name="label-machine-learned-entity-in-an-example-utterance"></a>Rotular uma entidade de aprendizado de máquina em um enunciado de exemplo

A rotulagem de uma entidade em um enunciado de exemplo mostra que o LUIS tem um exemplo de entidade e onde a entidade poderá ser exibida no enunciado. 

## <a name="labeling-machine-learned-entity"></a>Rotular a entidade de aprendizado de máquina

Considere a seguinte frase: `hi, please I want a cheese pizza in 20 minutes`. 

1. Escolha o texto mais à esquerda e, em seguida, escolha o texto mais à direita da entidade. O _pedido concluído_ está rotulado na imagem a seguir.

    > [!div class="mx-imgBorder"]
    > ![Rotular uma entidade de aprendizado de máquina concluída](media/label-utterances/example-1-label-machine-learned-entity-complete-order.png)

1. Escolha a entidade na janela pop-up. A entidade de pedido de pizza concluído rotulada inclui todas as palavras (da esquerda para a direita em inglês) rotuladas. 

> [!TIP]
> As entidades disponíveis na janela pop-up referem-se ao contexto no qual o texto é exibido. Por exemplo, se você tiver uma entidade de aprendizado de máquina de cinco níveis e estiver selecionando texto no terceiro nível (indicado por um nome de entidade rotulada no enunciado de exemplo), as entidades disponíveis na janela pop-up ficarão limitadas ao contexto de subcomponentes do terceiro nível (subcomponentes do 4º nível). 

## <a name="review-labeled-text"></a>Examinar texto rotulado

Depois de rotular, examine o enunciado de exemplo. O LUIS aplica o modelo atual ao enunciado de exemplo após a rotulagem. A linha sólida indica que o texto foi rotulado. 

> [!div class="mx-imgBorder"]
> ![Entidade de aprendizado de máquina concluída e rotulada](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="when-to-train"></a>Quando treinar

Treine seu aplicativo se o modelo atual tiver que dar suporte à entidade rotulada, mas o enunciado de exemplo continuar mostrando o texto como previsto, mas não rotulado.  

## <a name="confirm-predicted-entity"></a>Confirmar a entidade prevista

Se o indicador visual estiver acima do enunciado, isso indicará que o texto está previsto, mas _ainda não foi rotulado_. Para transformar a previsão em um rótulo, escolha o enunciado e, em seguida, escolha **Confirmar previsões de entidade**.

> [!div class="mx-imgBorder"]
> ![Prever entidade de aprendizado de máquina concluída](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted.png)

## <a name="label-subcomponent-entity-by-painting-with-entity-palette-cursor"></a>Rotular a entidade do subcomponente pintando com o curso da paleta de entidades

1. Para corrigir previsões (entidades, exibidas acima do enunciado de exemplo), abra a paleta de entidades. 

    > [!div class="mx-imgBorder"]
    > ![Paleta de entidades de uma entidade de aprendizado de máquina](media/label-utterances/pizza-entity-palette-with-pizza-type-selected.png)

1. Escolha o subcomponente da entidade. Essa ação é visualmente indicada com um novo cursor. O cursor segue o mouse à medida que você se move no portal. 

    > [!div class="mx-imgBorder"]
    > ![Paleta de entidades de uma entidade de aprendizado de máquina](media/label-utterances/pizza-type-entity-palette-cursor.png)

1. No enunciado de exemplo, _pinte_ a entidade com o cursor. 

    > [!div class="mx-imgBorder"]
    > ![Paleta de entidades de uma entidade de aprendizado de máquina](media/label-utterances/paint-subcomponent-with-entity-palette-cursor.png)

## <a name="labeling-matching-text-entities-to-a-machine-learned-entity"></a>Rotular entidades com texto correspondente para uma entidade de aprendizado de máquina

As entidades com texto correspondente incluem entidades predefinidas, entidades de expressão regular e entidades de lista. Você adiciona essas entidades a uma entidade de aprendizado de máquina, como restrições para um subcomponente, quando cria ou edita a entidade de aprendizado de máquina. 

**Após as restrições serem adicionadas, não será preciso rotular o texto correspondente no enunciado de exemplo.**

## <a name="entity-prediction-errors"></a>Erros de previsão da entidade

Os erros de previsão da entidade mostram um indicador de cuidado. Isso indica que a entidade prevista não corresponde à entidade rotulada. 

> [!div class="mx-imgBorder"]
> ![Paleta de entidades de uma entidade de aprendizado de máquina](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>Próximas etapas

Use o [painel](luis-how-to-use-dashboard.md) e os [enunciados de revisão de pontos de extremidade](luis-how-to-review-endpoint-utterances.md) para melhorar a qualidade de previsão de seu aplicativo.
