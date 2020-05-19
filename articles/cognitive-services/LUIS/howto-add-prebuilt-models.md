---
title: Modelos predefinidos para Reconhecimento vocal
titleSuffix: Azure Cognitive Services
description: O LUIS inclui um conjunto de modelos predefinidos para adicionar rapidamente cenários de usuário de conversação comuns.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: diberry
ms.openlocfilehash: 653882db4b62d7731123faf7b177da44dbd74e3f
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83584987"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>Adicionar modelos predefinidos para cenários de uso comuns

O LUIS inclui um conjunto de modelos predefinidos para adicionar rapidamente cenários de usuário de conversação comuns. Essa é uma maneira rápida e fácil de adicionar habilidades ao seu aplicativo cliente de conversa sem ter que projetar os modelos para essas habilidades.

## <a name="add-a-prebuilt-domain"></a>Adicionar um domínio predefinido

1. Entre no portal do [Luis](https://www.luis.ai)e selecione sua **assinatura** e recurso de **criação** para ver os aplicativos atribuídos a esse recurso de criação.
1. Abra seu aplicativo selecionando seu nome na página **meus aplicativos** .

1. Selecione **domínios predefinidos** na barra de ferramentas à esquerda.

1. Localize o domínio que você deseja adicionar ao aplicativo e selecione botão **Adicionar domínio** .

    > [!div class="mx-imgBorder"]
    > ![Adicionar domínio predefinido do Calendário](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

## <a name="add-a-prebuilt-intent"></a>Adicionar uma intenção predefinida

1. Entre no portal do [Luis](https://www.luis.ai)e selecione sua **assinatura** e recurso de **criação** para ver os aplicativos atribuídos a esse recurso de criação.
1. Abra seu aplicativo selecionando seu nome na página **meus aplicativos** .

1. Na página **tentativas** , selecione **Adicionar intenção de domínio predefinido** na barra de ferramentas acima da lista de intenções.

1. Selecione a intenção **Utilities.Cancel** na caixa de diálogo pop-up.

    > [!div class="mx-imgBorder"]
    > ![Adicionar intenção predefinida](./media/luis-prebuilt-domains/add-prebuilt-domain-intents.png)

1. Selecione o botão **Concluído**.

## <a name="add-a-prebuilt-entity"></a>Adicionar uma entidade predefinida
1. Entre no portal do [Luis](https://www.luis.ai)e selecione sua **assinatura** e recurso de **criação** para ver os aplicativos atribuídos a esse recurso de criação.
1. Abra seu aplicativo selecionando seu nome na página **meus aplicativos** .
1. Selecione **entidades** no lado esquerdo.

1. Na página **entidades** , selecione **Adicionar entidade predefinida**.

1. Na caixa de diálogo **adicionar entidades predefinidas** , selecione a entidade predefinida.

    > [!div class="mx-imgBorder"]
    > ![Adicionar caixa de diálogo de entidade predefinida](./media/luis-prebuilt-domains/add-prebuilt-entity.png)

1. Selecione **Concluído**. Depois que a entidade é adicionada, você precisa treinar o aplicativo.

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>Publicar para exibir o modelo predefinido do ponto de extremidade de previsão

A maneira mais fácil de exibir o valor de um modelo predefinido é consultar o ponto de extremidade publicado.

## <a name="entities-containing-a-prebuilt-entity-token"></a>Entidades que contêm um token de entidade predefinida

Se você tiver uma entidade aprendida por computador que precisa de um recurso necessário de uma entidade predefinida, adicione um subcomponente à entidade aprendida por computador e adicione um recurso _necessário_ de uma entidade predefinida.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Compilar modelo a partir de. csv com APIs REST](./luis-tutorial-node-import-utterances-csv.md)
