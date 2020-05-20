---
title: Rotular o enunciado de exemplo da entidade
description: Saiba como rotular uma entidade de aprendizado de máquina com subcomponentes em um exemplo expressão em uma página de detalhes da intenção do portal do LUIS.
ms.topic: conceptual
ms.date: 05/17/2020
ms.openlocfilehash: 0181057bd693280223806e9b5b7cd8c7f7345f79
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683770"
---
# <a name="label-machine-learning-entity-in-an-example-utterance"></a>Rotular entidade Machine-Learning em um exemplo de expressão

A rotulagem de uma entidade em um enunciado de exemplo dá ao LUIS um exemplo do que é a entidade e de em que parte do enunciado a entidade poderá ser exibida.

## <a name="label-example-utterances-from-the-intent-detail-page"></a>Exemplo de rótulo declarações da página de detalhes da intenção

1. Entre no portal do [Luis](https://www.luis.ai)e selecione sua **assinatura** e recurso de **criação** para ver os aplicativos atribuídos a esse recurso de criação.
1. Abra seu aplicativo selecionando seu nome na página **meus aplicativos** .
1. Selecione a intenção que tem o exemplo declarações que você deseja rotular para extração com uma entidade.
1. Selecione o texto que você deseja rotular e, em seguida, selecione a entidade.

## <a name="label-with-the-entity-palette-visible"></a>Rótulo com a paleta de entidades visível

Depois de planejar seu esquema com entidades, mantenha a **paleta de entidades** visível ao rotular. A **paleta de entidades** é um lembrete do que suas entidades planejam extrair.

Para acessar a **paleta de entidades**, selecione o **@** símbolo na barra de ferramentas contextual acima da lista expressão de exemplo.

> [!div class="mx-imgBorder"]
> ![Captura de tela da paleta de entidades na página de detalhes da intenção.](media/label-utterances/entity-palette-from-tool-bar.png)

## <a name="label-entity-from-in-place-menu"></a>Entidade de rótulo do menu no local

Considere o exemplo expressão, `hi, please I want a cheese pizza in 20 minutes` .

1. Selecione o texto mais à esquerda e, em seguida, selecione o texto mais à direita da entidade e, em seguida, no menu no local, escolha a entidade com a qual você deseja rotular.

    > [!div class="mx-imgBorder"]
    > ![Rotular entidade de aprendizado de máquina completa](media/label-utterances/label-steps-in-place-menu.png)


## <a name="label-entity-from-entity-palette"></a>Entidade de rótulo da paleta de entidades

A paleta de entidades oferece uma alternativa à experiência de rotulagem anterior. Ele permite que você focalize o texto para rotulá-lo instantaneamente com uma entidade.

1. Abra a paleta de entidades selecionando o **@** símbolo no canto superior direito da tabela expressão.

2. Selecione a entidade da paleta que você deseja rotular. Essa ação é visualmente indicada com um novo cursor. O cursor segue o mouse à medida que você move no portal do LUIS.

3. No enunciado de exemplo, _pinte_ a entidade com o cursor.

    > [!div class="mx-imgBorder"]
    > ![Paleta de entidades para entidade de aprendizado de máquina](media/label-utterances/example-1-label-machine-learned-entity-palette-label-action.png)

## <a name="adding-entity-as-a-feature-from-the-entity-palette"></a>Adicionando a entidade como um recurso da paleta de entidades

A seção inferior da paleta de entidades permite que você adicione recursos à entidade atualmente selecionada. Você pode selecionar entre todas as entidades e listas de frases existentes ou criar uma nova lista de frases.

> [!div class="mx-imgBorder"]
> ![Captura de tela da paleta de entidades com a entidade como um recurso](media/label-utterances/entity-palette-entity-as-a-feature.png)

## <a name="labeling-entity-roles"></a>Rotular funções de entidade

As funções de entidade são rotuladas usando a **paleta de entidades**.

1. Na página Detalhes da intenção, selecione a **paleta de entidades** na barra de ferramentas de contexto.
1. Depois que a Paleta de entidades for aberta, selecione a entidade na lista de entidades.
1. Abaixo da lista de entidades, selecione uma função existente.
1. No texto do enunciado de exemplo, rotule o texto com a função de entidade.

## <a name="review-labeled-text"></a>Examinar texto rotulado

Após a rotulagem, examine o enunciado de exemplo e verifique se o intervalo de texto selecionado foi sublinhado com a entidade escolhida. A linha sólida indica que o texto foi rotulado.

> [!div class="mx-imgBorder"]
> ![Rotulada como entidade de aprendizado de máquina completa](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="confirm-predicted-entity"></a>Confirmar a entidade prevista

Se houver uma caixa com linhas pontilhadas ao longo do intervalo de texto, indica que o texto é previsto, mas _não rotulado ainda_. Para transformar a previsão em um rótulo, selecione a linha expressão e, em seguida, selecione **confirmar entidades** na barra de ferramentas contextual.

## <a name="relabeling-over-existing-entities"></a>Rerotulando as entidades existentes

Se você rerotular o texto que já está rotulado, LUIS poderá dividir ou mesclar rótulos existentes.

## <a name="labeling-for-punctuation"></a>Rotulagem para Pontuação

Não é necessário rotular para pontuação. Use [as configurações do aplicativo](luis-reference-application-settings.md) _formulários do Word_ para controlar se a pontuação afeta as previsões expressão.

## <a name="unlabel-entities"></a>Remover o rótulo de entidades

> [!NOTE]
> Somente entidades aprendidas do computador podem ser sem rótulo.

Para desrotular uma entidade, selecione a entidade e selecione **desrotular** no menu no local.

> [!div class="mx-imgBorder"]
> ![Captura de tela mostrando a entidade de desrotulação](media/label-utterances/unlabel-entity-using-in-place-menu.png)

## <a name="automatic-labeling-for-parent-and-child-entities"></a>Rotulamento automático para entidades pai e filho

Se você estiver rotulando uma entidade pai, qualquer subentidade que possa ser prevista com base na versão atualmente treinada será rotulada.

Se você estiver rotulando uma subentidade, o pai será rotulado automaticamente.

## <a name="automatic-labeling-for-non-machine-learned-entities"></a>Rotulamento automático para entidades aprendidas que não sejam da máquina

Entidades aprendidas não Machine incluem entidades predefinidas, entidades de expressão regular, entidades de lista e padrão. quaisquer entidades. Elas são rotuladas automaticamente pelo LUIS, então não precisam ser rotuladas manualmente pelos usuários.

## <a name="entity-prediction-errors"></a>Erros de previsão da entidade

Erros de previsão de entidade indicam que a entidade prevista não corresponde à entidade rotulada. Isso é visualizado com auxílio de um indicador de cuidado ao lado do enunciado.

> [!div class="mx-imgBorder"]
> ![Paleta de entidades para entidade de aprendizado de máquina](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>Próximas etapas

Use o [painel](luis-how-to-use-dashboard.md) e os [enunciados de revisão de pontos de extremidade](luis-how-to-review-endpoint-utterances.md) para melhorar a qualidade de previsão de seu aplicativo.