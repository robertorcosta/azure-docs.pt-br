---
title: Descritores - LUIS
titleSuffix: Azure Cognitive Services
description: Use o LUIS (Serviço Inteligente de Reconhecimento Vocal) para adicionar recursos de aplicativos que podem melhorar a detecção ou previsão de intenções e entidades que as categorias e padrões
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: diberry
ms.openlocfilehash: 2b5046bb61dcafbba0b0540935e08777fbd747a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74123131"
---
# <a name="use-descriptors-to-boost-signal-of-word-list"></a>Use descritores para aumentar o sinal da lista de palavras

É possível adicionar recursos ao aplicativo de LUIS para melhorar a precisão. Os recursos ajudam o LUIS fornecendo dicas de que certas palavras e frases fazem parte de um vocabulário do domínio do aplicativo. 

Um [descritor](luis-concept-feature.md) (lista de frases) inclui um grupo de valores (palavras ou frases) que pertencem à mesma classe e devem ser tratados da mesma forma (por exemplo, nomes de cidades ou produtos). O que o LUIS aprende sobre um deles é aplicado automaticamente aos outros também. Esta lista não é a mesma coisa que uma [entidade de lista](reference-entity-list.md) (correspondências de texto exatos) de palavras combinadas.

Um descritor adiciona ao vocabulário do domínio do aplicativo como um segundo sinal para LUIS sobre essas palavras.

Revise [conceitos de recursos](luis-concept-feature.md) para entender quando e por que usar um descritor. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-descriptor"></a>Adicionar descritor

1. Abra seu aplicativo clicando em seu nome na página **Meus aplicativos** e clique em **Construir**e clique em **Descritores** no painel esquerdo do aplicativo. 

1. Na página **Descritores,** clique **em + Adicionar Descritor**. 
 
1. Na caixa de diálogo **Criar nova lista de frases,** digite um nome como `Cities` para o descritor. Na caixa **Valor,** digite os valores dos `Seattle`descritores, tais como . É possível digitar um valor por vez ou um conjunto de valores separados por vírgulas e, em seguida, pressionar **Enter**.

    > [!div class="mx-imgBorder"]
    > ![Adicionar cidades descritores](./media/luis-add-features/add-phrase-list-cities.png)

    Uma vez que você inseriu valores suficientes para LUIS, sugestões aparecem. Você pode **+ Adicionar todos os** valores propostos ou selecionar termos individuais.

1. Mantenha **Esses valores são intercambiáveis** se os valores adicionados do descritor são alternativas que podem ser usadas de forma intercambiável.

1. Selecione **Feito**. O novo descritor é adicionado à página **Descritores.**

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> Você pode excluir ou desativar um descritor da barra de ferramentas contextual na página **Descritores.**

## <a name="next-steps"></a>Próximas etapas

Depois de adicionar, editar, excluir ou desativar um descritor, [treinar e testar o aplicativo](luis-interactive-test.md) novamente para ver se o desempenho melhora.
