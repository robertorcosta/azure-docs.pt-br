---
title: Padrões-adição de precisão-LUIS
titleSuffix: Azure Cognitive Services
description: Adicione modelos de padrão para melhorar a precisão da previsão em aplicativos Reconhecimento vocal (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: diberry
ms.openlocfilehash: 21afb12bf2464218119ebf52ebd980745e3d731d
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76311709"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Como adicionar padrões para melhorar a precisão da previsão
Depois que um aplicativo LUIS recebe o ponto de extremidade declarações, use um [padrão](luis-concept-patterns.md) para melhorar a precisão da previsão para declarações que revela um padrão em ordem de palavras e escolha de palavra. Padrões usam [sintaxe](luis-concept-patterns.md#pattern-syntax) específica para indicar o local de: [entidades](luis-concept-entity-types.md), [funções](luis-concept-roles.md)de entidade e texto opcional.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

> [!CAUTION]
> Os padrões incluem apenas pais de entidade aprendidas por computador, não subcomponentes.

## <a name="adding-example-utterances-as-pattern"></a>Adicionando declarações de exemplo como padrão

Se você quiser adicionar um padrão para uma entidade, a maneira _mais fácil_ é criar o padrão na página de detalhes da intenção. Isso garante que a sintaxe corresponda ao exemplo expressão.

1. No [portal do Luis de visualização](https://preview.luis.ai), selecione o aplicativo na página **meus aplicativos** .
1. Na página lista de **tentativas** , selecione o nome da intenção do exemplo expressão para o qual você deseja criar um modelo expressão.
1. Na página detalhes da intenção, selecione a linha do exemplo expressão que você deseja usar como o modelo expressão e, em seguida, selecione **+ Adicionar como padrão** na barra de ferramentas de contexto.

    > [!div class="mx-imgBorder"]
    > ![captura de tela da seleção de exemplo expressão como um padrão de modelo na página de detalhes da intenção.](./media/luis-how-to-model-intent-pattern/add-example-utterances-as-pattern-template-utterance-from-intent-detail-page.png)

1. Na caixa pop-up, selecione **concluído** na página **confirmar padrões** . Você não precisa definir os subcomponentes, restrições ou descritores das entidades. Você só precisa listar a entidade aprendida por computador.

    > [!div class="mx-imgBorder"]
    > ![captura de tela de confirmando o exemplo expressão como um padrão de modelo na página de detalhes da intenção.](./media/luis-how-to-model-intent-pattern/confirm-patterns-from-example-utterance-intent-detail-page.png)

1. Se você precisar editar o modelo, como a seleção de texto como opcional, com os colchetes `[]` (quadrado), será necessário fazer essa edição na página **padrões** .

1. Na barra de navegação, selecione **treinar** para treinar o aplicativo com o novo padrão.

## <a name="add-template-utterance-using-correct-syntax"></a>Adicionar modelo expressão usando a sintaxe correta

1. Abra o aplicativo selecionando seu nome na página **Meus Aplicativos** e, em seguida, selecione **Padrões** no painel esquerdo, sob **Melhorar o desempenho do aplicativo**.

    > [!div class="mx-imgBorder"]
    > ![captura de tela da lista de padrões](./media/luis-how-to-model-intent-pattern/patterns-1.png)

1. Selecione a intenção correta para o padrão.

1. Na caixa de texto do modelo, digite a declaração modelo e selecione Enter. Quando quiser inserir o nome da entidade, use a sintaxe de entidade de padrão correta. Comece a sintaxe de entidade com `{`. A lista de entidades será exibida. Selecione a entidade correta.

    > [!div class="mx-imgBorder"]
    > Captura de tela de ![da entidade para o padrão](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Se sua entidade incluir uma [função](luis-concept-roles.md), indique a função com um único sinal de dois-pontos, `:`, após o nome da entidade, como `{Location:Origin}`. A lista de funções das entidades é exibida em uma lista. Selecione a função e, depois, selecione Enter.

    > [!div class="mx-imgBorder"]
    > ![captura de tela da entidade com a função](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    Depois de selecionar a entidade correta, termine de inserir o padrão e, em seguida, selecione Enter. Quando você terminar de inserir padrões, [treine](luis-how-to-train.md) seu aplicativo.

    > [!div class="mx-imgBorder"]
    > ![captura de tela do padrão inserido com os dois tipos de entidades](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>Treinar seu aplicativo após alterar o modelo com os padrões
Depois de adicionar, editar, remover ou reatribuir um padrão, [treine](luis-how-to-train.md) e [publique](luis-how-to-publish-app.md) seu aplicativo para que as suas alterações afetem as consultas de ponto de extremidade.

<a name="search-patterns"></a>
<a name="edit-a-pattern"></a>
<a name="reassign-individual-pattern-to-different-intent"></a>
<a name="reassign-several-patterns-to-different-intent"></a>
<a name="delete-a-single-pattern"></a>
<a name="delete-several-patterns"></a>
<a name="filter-pattern-list-by-entity"></a>
<a name="filter-pattern-list-by-intent"></a>
<a name="remove-entity-or-intent-filter"></a>
<a name="add-pattern-from-existing-utterance-on-intent-or-entity-page"></a>

## <a name="use-contextual-toolbar"></a>Usar barra de ferramentas contextual

A barra de ferramentas contextual acima da lista de padrões permite que você:

* Pesquisar padrões
* Editar um padrão
* Reatribuir padrão individual a uma intenção diferente
* Reatribuir vários padrões a uma intenção diferente
* Excluir um padrão único
* Excluir vários padrões
* Filtrar a lista de padrões por entidade
* Filtro-padrão-listar por intenção
* Remover o filtro de entidade ou intenção
* Adicionar padrão de declaração existente na página de intenção ou de entidade

## <a name="next-steps"></a>Próximos passos

* Saiba como [criar um padrão](luis-tutorial-pattern.md) com um padrão. qualquer função e com um tutorial.
* Saiba como [treinar](luis-how-to-train.md) seu aplicativo.
