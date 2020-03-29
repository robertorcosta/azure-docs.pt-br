---
title: Adicionar intenções - LUIS
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73904297"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>Adicionar intenções para determinar a intenção do usuário de expressões

Adicione [intents](luis-concept-intent.md) ao seu aplicativo LUIS para identificar grupos de perguntas ou comandos que tenham a mesma intenção. 

Os objetivos são gerenciados a partir da seção **Compilação** da barra de navegação superior e, a seguir, das **solicitações** do painel esquerdo. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-intent"></a>Adicionar intenção

1. No [portal de visualização LUIS,](https://preview.luis.ai)selecione **Construir** para visualizar intenções. 
1. Na página **Intenções,** selecione **+ Criar**.
1. Na caixa de diálogo **Criar novas intenções,** digite o nome da intenção, por exemplo, `ModifyOrder`e selecione **Feito**.

    > [!div class="mx-imgBorder"]
    > ![Adicionar intenção](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    A intenção precisa de expressões de exemplo.

## <a name="add-an-example-utterance"></a>Adicionar uma expressão de exemplo

Enunciados de exemplo são exemplos de texto de comandos ou perguntas do usuário. Para ensinar o Entendimento da Linguagem (LUIS) quando prever essa intenção, você precisa adicionar expressões de exemplo a uma intenção. Luis precisa na faixa de 15 a 30 enunciados exemplos para começar a entender a intenção. Não adicione expressões de exemplo em massa. Cada enunciado deve ser cuidadosamente escolhido para como ele é diferente dos exemplos já na intenção. 

1. Na página de detalhes de intenção, digite um `Deliver a large cheese pizza` enunciado relevante que você espera de seus usuários, como na caixa de texto abaixo do nome da intenção e, em seguida, pressione Enter.
 
    > [!div class="mx-imgBorder"]
    > ![Captura de tela da página Detalhes de intenções, com o enunciado realçado](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

    LUIS converte todas as expressões em minúsculas e adiciona espaços em torno [de tokens](luis-language-support.md#tokenization) como hífens.

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>Erros de previsão de intenção 

Um exemplo de expressão em uma intenção pode ter um erro de previsão de intenção entre a intenção em que o exemplo está atualmente e a intenção determinada durante o treinamento. 

Para encontrar erros de previsão de enunciados e corrigi-los, use as opções **Filter** de Incorrect e Unclear combinadas com a opção **Exibir** de **exibição detalhada**. 

![Para encontrar erros de previsão de enunciados e corrigi-los, use a opção Filtro.](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

Quando os filtros e a exibição são aplicados, e há expressões de exemplo com erros, a lista de frases de exemplo mostra as expressões e os problemas.

> [!div class="mx-imgBorder"]
> ![! [Quando os filtros e a exibição são aplicados e há expressões de exemplo com erros, a lista de declarações de exemplo mostra as expressões e os problemas.] (./mídia/luis-como-adicionar-intenções/find-errors-in-enunciados.png)](./media/luis-how-to-add-intents/find-errors-in-utterances.png#lightbox)

Cada linha mostra a pontuação de previsão do treinamento atual para o exemplo de enunciado, a pontuação do rival mais próximo, que é a diferença nessas duas pontuações. 

### <a name="fixing-intents"></a>Fixação de intenções

Para saber como corrigir erros de previsão de intenções, use o [Painel de Resumo](luis-how-to-use-dashboard.md). O painel de resumo fornece análise para o último treinamento da versão ativa e oferece as principais sugestões para corrigir o seu modelo.  

## <a name="using-the-contextual-toolbar"></a>Usando a barra de ferramentas contextual

A barra de ferramentas de contexto fornece outras ações:

* Editar ou excluir o enunciado do exemplo
* Reatribuir o exemplo de expressão para uma intenção diferente
* Filtros e visualizações: apenas mostre expressões contendo entidades filtradas ou visualize detalhes opcionais
* Pesquise através de enunciados de exemplo

## <a name="train-your-app-after-changing-model-with-intents"></a>Treinar seu aplicativo após alterar o modelo com intenções

Depois de adicionar, editar ou remover intenções, [train](luis-how-to-train.md) e [publique](luis-how-to-publish-app.md) seu aplicativo para que as alterações sejam aplicadas às consultas de ponto de extremidade. Não treine depois de cada mudança. Treine depois de um grupo de mudanças. 

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como adicionar [declarações de exemplo](luis-how-to-add-example-utterances.md) com entidades. 
