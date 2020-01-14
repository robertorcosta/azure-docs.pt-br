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
ms.date: 12/16/2019
ms.author: diberry
ms.openlocfilehash: f7d6e98205afad2ed2c4aea30e635254f79acaa1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448082"
---
# <a name="label-machine-learned-entity-in-an-example-utterance"></a>Rotular uma entidade de aprendizado de máquina em um enunciado de exemplo

A rotulagem de uma entidade em um enunciado de exemplo dá ao LUIS um exemplo do que é a entidade e de em que parte do enunciado a entidade poderá ser exibida.

## <a name="labeling-machine-learned-entity"></a>Rotular a entidade de aprendizado de máquina

Considere a seguinte frase: `hi, please I want a cheese pizza in 20 minutes`.

1. Selecione o texto mais à esquerda, depois selecione o texto mais à direita da entidade e, em seguida, escolha a entidade com a qual você deseja rotular, neste caso, Pedido Completo. O _pedido concluído_ está rotulado na imagem a seguir.

    > [!div class="mx-imgBorder"]
    > ![Rotular uma entidade de aprendizado de máquina concluída](media/label-utterances/example-1-label-machine-learned-entity-complete-order.png)

1. Escolha a entidade na janela pop-up. A entidade de pedido de pizza concluído rotulada inclui todas as palavras (da esquerda para a direita em inglês) rotuladas.

## <a name="review-labeled-text"></a>Examinar texto rotulado

Após a rotulagem, examine o enunciado de exemplo e verifique se o intervalo de texto selecionado foi sublinhado com a entidade escolhida. A linha sólida indica que o texto foi rotulado.

> [!div class="mx-imgBorder"]
> ![Entidade de aprendizado de máquina concluída e rotulada](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="confirm-predicted-entity"></a>Confirmar a entidade prevista

Se houver uma caixa com linhas pontilhadas ao longo do intervalo de texto e o nome da entidade estiver acima do enunciado, isso indicará que o texto está previsto, mas não _ainda não rotulado_. Para transformar a previsão em um rótulo, selecione a linha do enunciado e, em seguida, escolha **Confirmar previsões de entidade**.

> [!div class="mx-imgBorder"]
> ![Prever entidade de aprendizado de máquina concluída](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted.png)

Como alternativa, você pode selecionar o nome da entidade acima do texto e, em seguida, selecionar **Confirmar Previsão** no menu que aparece.

> [!div class="mx-imgBorder"]
> ![Prever entidade com aprendizado de máquina completa usando o menu](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted-menu.png)

## <a name="label-entity-by-painting-with-entity-palette-cursor"></a>Rotular a entidade pintando com o curso da paleta de entidades

A paleta de entidades oferece uma alternativa à experiência de rotulagem anterior. Ele permite que você focalize o texto para rotulá-lo instantaneamente com uma entidade.

1. Abra a paleta de entidades selecionando o ícone de realce na parte superior direita da tabela de enunciado.

    > [!div class="mx-imgBorder"]
    > ![Paleta de entidades de uma entidade de aprendizado de máquina](media/label-utterances/example-1-label-machine-learned-entity-palette.png)

2. Selecione o componente da entidade. Essa ação é visualmente indicada com um novo cursor. O cursor segue o mouse à medida que você se move no portal.

    > [!div class="mx-imgBorder"]
    > ![Paleta de entidades de uma entidade de aprendizado de máquina](media/label-utterances/example-1-label-machine-learned-entity-palette-menu.png)

3. No enunciado de exemplo, _pinte_ a entidade com o cursor.

    > [!div class="mx-imgBorder"]
    > ![Paleta de entidades de uma entidade de aprendizado de máquina](media/label-utterances/example-1-label-machine-learned-entity-palette-label-action.png)

## <a name="labeling-subcomponents-of-a-machine-learned-entity"></a>Como rotular subcomponentes de uma entidade aprendida por máquina

Subcomponentes em entidades são rotulados exatamente do mesmo modo que as entidades de nível superior. Ao selecionar texto, as entidades disponíveis na janela pop-up referem-se ao contexto no qual o texto é exibido. Por exemplo, se você tiver uma entidade de aprendizado de máquina de cinco níveis e estiver selecionando texto que foi rotulado no primeiro e no segundo níveis (indicado por um nome de entidade rotulada no enunciado de exemplo), as entidades disponíveis na janela pop-up ficarão limitadas ao contexto de componentes do terceiro nível. Para rotular o texto com outras entidades, selecione a opção **Rotular como outra entidade**.

> [!div class="mx-imgBorder"]
> ![Paleta de entidades de uma entidade de aprendizado de máquina](media/label-utterances/example-1-label-machine-learned-entity-subcomponent.png)

Os subcomponentes podem ser rotulados somente se o pai também é rotulado.

## <a name="labeling-entity-roles"></a>Rotular funções de entidade

As funções de entidade são rotuladas usando a paleta da entidades.

1. Na página Detalhes da intenção, selecione a **paleta de entidades** na barra de ferramentas de contexto.
1. Depois que a Paleta de entidades for aberta, selecione a entidade na lista de entidades.
1. Mova para o **Inspetor de entidade** e, em seguida, selecione uma função existente ou crie uma função.
1. No texto do enunciado de exemplo, rotule o texto com a função de entidade. 

## <a name="unlabel-entities"></a>Remover o rótulo de entidades

Para remover o rótulo de uma entidade, selecione o nome da entidade abaixo do texto e selecione **Remover rótulo**. Se a entidade cujo rótulo você está tentando remover tiver subcomponentes rotulados, os rótulos dos subcomponentes deverão ser removidos primeiro.

## <a name="editing-labels-using-the-entity-palette"></a>Como editar rótulos usando a paleta da entidade

Caso cometa um erro ao rotular, a paleta da entidade é uma ferramenta fácil e permite edições rápidas. Por exemplo, se um rótulo de entidade abranger uma palavra extra por engano e já tiver subcomponentes rotulados, você poderá usar a paleta da entidade para passar o pincel sobre o intervalo de palavras menor necessário.

Por exemplo:

1. O subcomponente do tipo pizza se estende por "pizza de queijo com", que inclui uma palavra extra incorreta:"com"

    > [!div class="mx-imgBorder"]
    > ![Paleta de entidades de uma entidade de aprendizado de máquina](media/label-utterances/edit-label-with-palette-1.png)

2. Use a paleta de entidades para escolher o tipo de pizza e usar o pincel sobre "pizza de queijo". O resultado é que agora apenas "pizza de queijo" é usado como rótulo para o tipo de pizza.

    > [!div class="mx-imgBorder"]
    > ![Paleta de entidades de uma entidade de aprendizado de máquina](media/label-utterances/edit-label-with-palette-2.png)

## <a name="labels-for-matching-text-entities"></a>Rótulos para entidades de texto correspondentes

As entidades com texto correspondente incluem entidades predefinidas, entidades de expressão regular, entidades de lista e entidades pattern.any. Elas são rotuladas automaticamente pelo LUIS, então não precisam ser rotuladas manualmente pelos usuários.

## <a name="entity-prediction-errors"></a>Erros de previsão da entidade

Erros de previsão de entidade indicam que a entidade prevista não corresponde à entidade rotulada. Isso é visualizado com auxílio de um indicador de cuidado ao lado do enunciado.

> [!div class="mx-imgBorder"]
> ![Paleta de entidades de uma entidade de aprendizado de máquina](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>Próximas etapas

Use o [painel](luis-how-to-use-dashboard.md) e os [enunciados de revisão de pontos de extremidade](luis-how-to-review-endpoint-utterances.md) para melhorar a qualidade de previsão de seu aplicativo.