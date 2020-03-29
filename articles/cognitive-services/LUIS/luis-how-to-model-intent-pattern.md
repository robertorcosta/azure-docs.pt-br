---
title: Padrões adicionam precisão - LUIS
titleSuffix: Azure Cognitive Services
description: Adicione modelos de padrão para melhorar a precisão de previsão em aplicações de Compreensão de Linguagem (LUIS).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76311709"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Como adicionar padrões para melhorar a precisão da previsão
Depois que um aplicativo LUIS recebe declarações de ponto final, use um [padrão](luis-concept-patterns.md) para melhorar a precisão da previsão para declarações que revelam um padrão na ordem das palavras e na escolha da palavra. Os padrões utilizam [sintaxe](luis-concept-patterns.md#pattern-syntax) específica para indicar a localização de: [entidades,](luis-concept-entity-types.md) [funções de](luis-concept-roles.md)entidadee texto opcional.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

> [!CAUTION]
> Os padrões incluem apenas pais de entidades aprendidas por máquina, não subcomponentes.

## <a name="adding-example-utterances-as-pattern"></a>Adicionando expressões de exemplo como padrão

Se você quiser adicionar um padrão para uma entidade, a maneira _mais fácil_ é criar o padrão a partir da página de detalhes da Intenção. Isso garante que sua sintaxe corresponda ao exemplo de expressão.

1. No [portal DE pré-visualização LUIS](https://preview.luis.ai), selecione o aplicativo na página **Meus Aplicativos.**
1. Na página da lista **Intenções,** selecione o nome de intenção do enunciado de exemplo que deseja criar uma expressão de modelo.
1. Na página Detalhes da Intenção, selecione a linha para o enunciado de exemplo que deseja usar como enunciado do modelo e selecione **+ Adicione como padrão** da barra de ferramentas de contexto.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela de selecionar enunciado de exemplo como um padrão de modelo na página Dedetalhes da Intenção.](./media/luis-how-to-model-intent-pattern/add-example-utterances-as-pattern-template-utterance-from-intent-detail-page.png)

1. Na caixa pop-up, selecione **'Feito** na página **Confirmar padrões'.** Você não precisa definir os subcomponentes, restrições ou descritores das entidades. Você só precisa listar a entidade aprendida pela máquina.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela de confirmação de enunciado de exemplo como um padrão de modelo na página de detalhes da Intenção.](./media/luis-how-to-model-intent-pattern/confirm-patterns-from-example-utterance-intent-detail-page.png)

1. Se você precisar editar o modelo, como selecionar texto `[]` como opcional, com os suportes (quadrados), você precisa fazer esta edição a partir da página **Padrões.**

1. Na barra de navegação, selecione **Treinar** para treinar o aplicativo com o novo padrão.

## <a name="add-template-utterance-using-correct-syntax"></a>Adicionar enunciado de modelo usando sintaxe correta

1. Abra o aplicativo selecionando seu nome na página **Meus Aplicativos** e, em seguida, selecione **Padrões** no painel esquerdo, sob **Melhorar o desempenho do aplicativo**.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela da lista de Padrões](./media/luis-how-to-model-intent-pattern/patterns-1.png)

1. Selecione a intenção correta para o padrão.

1. Na caixa de texto do modelo, digite a declaração modelo e selecione Enter. Quando quiser inserir o nome da entidade, use a sintaxe de entidade de padrão correta. Comece a sintaxe de entidade com `{`. A lista de entidades será exibida. Selecione a entidade correta.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela de entidade do padrão](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Se a sua entidade incluir uma [função,](luis-concept-roles.md)indique a função com um único cólon, `:`após o nome da entidade, tais como `{Location:Origin}`. A lista de funções das entidades é exibida em uma lista. Selecione a função e, depois, selecione Enter.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela da entidade com a função](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    Depois de selecionar a entidade correta, termine de inserir o padrão e, em seguida, selecione Enter. Quando você terminar de inserir padrões, [treine](luis-how-to-train.md) seu aplicativo.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela do padrão inserido com os dois tipos de entidades](./media/luis-how-to-model-intent-pattern/patterns-5.png)

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

## <a name="use-contextual-toolbar"></a>Use a barra de ferramentas contextual

A barra de ferramentas contextual acima da lista de padrões permite:

* Busca por padrões
* Editar um padrão
* Reatribuir padrão individual a uma intenção diferente
* Reatribuir vários padrões a uma intenção diferente
* Padrão de exclusão-um-único
* Excluir vários padrões
* Filtrar a lista de padrões por entidade
* Filtro-padrão-lista por intenção
* Remover o filtro de entidade ou intenção
* Adicionar padrão de declaração existente na página de intenção ou de entidade

## <a name="next-steps"></a>Próximas etapas

* Aprenda a [construir um padrão](luis-tutorial-pattern.md) com um padrão.any e funções com um tutorial.
* Saiba como [treinar](luis-how-to-train.md) seu aplicativo.
