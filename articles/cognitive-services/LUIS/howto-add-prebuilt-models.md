---
title: Modelos pré-construídos para compreensão da linguagem
titleSuffix: Azure Cognitive Services
description: Luis inclui um conjunto de modelos pré-construídos para adicionar rapidamente cenários comuns de usuários conversacionais.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: 0a733aff14b71c6378971c0b72a9ca1f3cd4cfc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74013551"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>Adicionar modelos pré-construídos para cenários de uso comum 

Luis inclui um conjunto de modelos pré-construídos para adicionar rapidamente cenários comuns de usuários conversacionais. Esta é uma maneira rápida e fácil de adicionar habilidades ao seu aplicativo cliente conversacional sem ter que projetar os modelos para essas habilidades. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-a-prebuilt-domain"></a>Adicionar um domínio predefinido

1. Na página **Meus aplicativos**, selecione o aplicativo. Isso abre o aplicativo na seção **Compilar** do aplicativo. 

1. Selecione **domínios pré-construídos** na barra de ferramentas esquerda. 

1. Encontre o domínio que deseja adicionado ao aplicativo e selecione Adicionar botão **de domínio.**

    > [!div class="mx-imgBorder"]
    > ![Adicionar domínio predefinido do Calendário](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

## <a name="add-a-prebuilt-intent"></a>Adicionar uma intenção predefinida

1. Na página **Meus aplicativos**, selecione o aplicativo. Isso abre o aplicativo na seção **Compilar** do aplicativo. 

1. Na página **Intenções,** selecione Adicionar intenção de **domínio pré-construída** na barra de ferramentas acima da lista de intenções. 

1. Selecione a intenção **Utilities.Cancel** na caixa de diálogo pop-up. 

    > [!div class="mx-imgBorder"]
    > ![Adicionar intenção predefinida](./media/luis-prebuilt-domains/add-prebuilt-domain-intents.png)

1. Selecione o botão **Concluído**.

## <a name="add-a-prebuilt-entity"></a>Adicionar uma entidade predefinida

1. Abra seu aplicativo clicando no nome na página **Meus Aplicativos** e, em seguida, clicando em **Entidades** no lado esquerdo. 

1. Na página **Entidades**, clique em **Adicionar entidades predefinidas**.

1. Em Adicionar caixa de diálogo **entidades pré-construídas,** selecione a entidade pré-construída. 

    > [!div class="mx-imgBorder"]
    > ![Adicionar caixa de diálogo de entidade predefinida](./media/luis-prebuilt-domains/add-prebuilt-entity.png)

1. Selecione **Feito**. Depois que a entidade é adicionada, você precisa treinar o aplicativo. 

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>Publicar para exibir modelo pré-construído a partir do ponto final da previsão

A maneira mais fácil de visualizar o valor de um modelo pré-construído é consultar a partir do ponto final publicado. 

## <a name="entities-containing-a-prebuilt-entity-token"></a>Entidades que contêm um token de entidade pré-construída
 
Se você tiver uma entidade aprendida por máquina que é constrangida por uma entidade pré-construída, adicione um subcomponente à entidade aprendida pela máquina e adicione uma restrição de uma entidade pré-construída.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Construir modelo a partir de .csv com APIs REST](./luis-tutorial-node-import-utterances-csv.md)
