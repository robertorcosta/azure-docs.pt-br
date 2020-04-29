---
title: Adicionar tentativas-LUIS
titleSuffix: Azure Cognitive Services
description: Adicione intenções ao seu aplicativo do LUIS para identificar grupos de perguntas ou comandos que têm as mesmas intenções.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: 66a3350dee60772ce706af8995179dcd8c485b64
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "73904297"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>Adicionar intenções para determinar a intenção do usuário de expressões

Adicione [intents](luis-concept-intent.md) ao seu aplicativo LUIS para identificar grupos de perguntas ou comandos que tenham a mesma intenção. 

Os objetivos são gerenciados a partir da seção **Compilação** da barra de navegação superior e, a seguir, das **solicitações** do painel esquerdo. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-intent"></a>Adicionar intenção

1. No [portal de visualização do Luis](https://preview.luis.ai), selecione **Compilar** para exibir tentativas. 
1. Na página **tentativas** , selecione **+ criar**.
1. Na caixa de diálogo **criar nova tentativa** , insira o nome da intenção, por `ModifyOrder`exemplo, e selecione **concluído**.

    > [!div class="mx-imgBorder"]
    > ![Adicionar intenção](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    A intenção precisa de um exemplo de declarações.

## <a name="add-an-example-utterance"></a>Adicionar uma expressão de exemplo

Enunciados de exemplo são exemplos de texto de comandos ou perguntas do usuário. Para ensinar Reconhecimento vocal (LUIS) quando prever essa intenção, você precisa adicionar o declarações de exemplo a uma intenção. O LUIS precisa do intervalo de 15 a 30 exemplos de declarações para começar a entender a intenção. Não adicione o exemplo declarações em massa. Cada expressão deve ser escolhido cuidadosamente para como ele é diferente dos exemplos já existentes. 

1. Na página detalhes da intenção, insira um expressão relevante que você espera de seus usuários, como `Deliver a large cheese pizza` na caixa de texto abaixo do nome da intenção e pressione Enter.
 
    > [!div class="mx-imgBorder"]
    > ![Captura de tela da página Detalhes de intenções, com o enunciado realçado](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

    LUIS converte todas as declarações para minúsculas e adiciona espaços em volta de [tokens](luis-language-support.md#tokenization) como hifens.

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>Erros de previsão de intenção 

Um exemplo de expressão em uma intenção pode ter um erro de previsão de intenção entre a intenção em que o exemplo expressão está atualmente em e a intenção determinada durante o treinamento. 

Para localizar erros de previsão do expressão e corrigi-los, use as opções de **filtro** de incorretas e não claras combinadas com a opção **Exibir** da **exibição detalhada**. 

![Para localizar erros de previsão de expressão e corrigi-los, use a opção de filtro.](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

Quando os filtros e a exibição são aplicados e há um exemplo de declarações com erros, a lista de expressão de exemplo mostra o declarações e os problemas.

> [!div class="mx-imgBorder"]
> ![! [Quando os filtros e a exibição são aplicados e há um exemplo de declarações com erros, a lista de expressão de exemplo mostra o declarações e os problemas.] (./media/luis-how-to-add-intents/find-errors-in-utterances.png)](./media/luis-how-to-add-intents/find-errors-in-utterances.png#lightbox)

Cada linha mostra a pontuação de previsão do treinamento atual para o exemplo expressão, a Pontuação do rival mais próximo, que é a diferença nessas duas pontuações. 

### <a name="fixing-intents"></a>Corrigindo tentativas

Para saber como corrigir erros de previsão de intenção, use o [painel de resumo](luis-how-to-use-dashboard.md). O painel de resumo fornece análise para o último treinamento da versão ativa e oferece as principais sugestões para corrigir seu modelo.  

## <a name="using-the-contextual-toolbar"></a>Usando a barra de ferramentas contextual

A barra de ferramentas de contexto fornece outras ações:

* Editar ou excluir exemplo expressão
* Reatribuir expressão de exemplo a uma intenção diferente
* Filtros e exibições: mostrar somente declarações que contêm entidades filtradas ou exibir detalhes opcionais
* Pesquisar por meio de exemplo declarações

## <a name="train-your-app-after-changing-model-with-intents"></a>Treinar seu aplicativo após alterar o modelo com intenções

Depois de adicionar, editar ou remover intenções, [train](luis-how-to-train.md) e [publique](luis-how-to-publish-app.md) seu aplicativo para que as alterações sejam aplicadas às consultas de ponto de extremidade. Não treine após cada alteração única. Treine após um grupo de alterações. 

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como adicionar [declarações de exemplo](luis-how-to-add-example-utterances.md) com entidades. 
