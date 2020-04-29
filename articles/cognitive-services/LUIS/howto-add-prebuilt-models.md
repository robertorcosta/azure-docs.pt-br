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
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: 0a733aff14b71c6378971c0b72a9ca1f3cd4cfc3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "74013551"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>Adicionar modelos predefinidos para cenários de uso comuns 

O LUIS inclui um conjunto de modelos predefinidos para adicionar rapidamente cenários de usuário de conversação comuns. Essa é uma maneira rápida e fácil de adicionar habilidades ao seu aplicativo cliente de conversa sem ter que projetar os modelos para essas habilidades. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-a-prebuilt-domain"></a>Adicionar um domínio predefinido

1. Na página **Meus aplicativos**, selecione o aplicativo. Isso abre o aplicativo na seção **Compilar** do aplicativo. 

1. Selecione **domínios predefinidos** na barra de ferramentas à esquerda. 

1. Localize o domínio que você deseja adicionar ao aplicativo e selecione botão **Adicionar domínio** .

    > [!div class="mx-imgBorder"]
    > ![Adicionar domínio predefinido do Calendário](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

## <a name="add-a-prebuilt-intent"></a>Adicionar uma intenção predefinida

1. Na página **Meus aplicativos**, selecione o aplicativo. Isso abre o aplicativo na seção **Compilar** do aplicativo. 

1. Na página **tentativas** , selecione **Adicionar intenção de domínio predefinido** na barra de ferramentas acima da lista de intenções. 

1. Selecione a intenção **Utilities.Cancel** na caixa de diálogo pop-up. 

    > [!div class="mx-imgBorder"]
    > ![Adicionar intenção predefinida](./media/luis-prebuilt-domains/add-prebuilt-domain-intents.png)

1. Selecione o botão **Concluído**.

## <a name="add-a-prebuilt-entity"></a>Adicionar uma entidade predefinida

1. Abra seu aplicativo clicando no nome na página **Meus Aplicativos** e, em seguida, clicando em **Entidades** no lado esquerdo. 

1. Na página **Entidades**, clique em **Adicionar entidades predefinidas**.

1. Na caixa de diálogo **adicionar entidades predefinidas** , selecione a entidade predefinida. 

    > [!div class="mx-imgBorder"]
    > ![Adicionar caixa de diálogo de entidade predefinida](./media/luis-prebuilt-domains/add-prebuilt-entity.png)

1. Selecione **Concluído**. Depois que a entidade é adicionada, você precisa treinar o aplicativo. 

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>Publicar para exibir o modelo predefinido do ponto de extremidade de previsão

A maneira mais fácil de exibir o valor de um modelo predefinido é consultar o ponto de extremidade publicado. 

## <a name="entities-containing-a-prebuilt-entity-token"></a>Entidades que contêm um token de entidade predefinida
 
Se você tiver uma entidade aprendida por computador restrita por uma entidade predefinida, adicione um subcomponente à entidade aprendida por computador e, em seguida, adicione uma restrição de uma entidade predefinida.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Compilar modelo a partir de. csv com APIs REST](./luis-tutorial-node-import-utterances-csv.md)
