---
title: Painel - Compreensão de Idiomas - LUIS
titleSuffix: Azure Cognitive Services
description: Corrija intenções e entidades com o painel do seu aplicativo treinado. O painel exibe informações gerais do aplicativo, com destaques de intenções que devem ser corrigidas.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.openlocfilehash: d9ae126753f55349f9bf3eefd20bc4d222866af1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73888208"
---
# <a name="how-to-use-the-dashboard-to-improve-your-app"></a>Como usar o Dashboard para melhorar seu aplicativo

Encontre e corrija problemas com as intenções do seu aplicativo treinado quando estiver usando expressões de exemplo. O painel exibe informações gerais do aplicativo, com destaques de intenções que devem ser corrigidas. 

Revisão A análise do painel de instrumentos é um processo iterativo, repita à medida que você muda e melhora seu modelo.

Esta página não terá análises relevantes para aplicativos que não tenham nenhum exemplo de enunciados nas intenções, conhecidos como aplicativos _somente padrão._ 

## <a name="what-issues-can-be-fixed-from-dashboard"></a>Quais problemas podem ser corrigidos no painel?

Os três problemas abordados no painel são:

|Problema|Cor do gráfico|Explicação|
|--|--|--|
|Desequilíbrio de dados|-|Isso ocorre quando a quantidade de enunciados de exemplo varia significativamente. Todas as intenções precisam ter _aproximadamente_ o mesmo número de expressões de exemplo - exceto a intenção De Nenhum. Ele deve ter apenas 10%-15% da quantidade total de expressões no aplicativo.<br><br> Se os dados estão desequilibrados, mas a precisão da intenção está acima de certo limite, esse desequilíbrio não é relatado como um problema.<br><br>**Comece com este problema - pode ser a causa raiz dos outros problemas.**|
|Previsões pouco claras|Laranja|Isso ocorre quando a intenção superior e as pontuações da próxima intenção estão próximas o suficiente para que eles possam virar no próximo treinamento, devido à [amostragem negativa](luis-how-to-train.md#train-with-all-data) ou mais declarações de exemplo adicionadas à intenção. |
|Previsões incorretas|Vermelho|Isso ocorre quando não se prevê um exemplo de expressão para a intenção rotulada (a intenção em que está).|

As previsões corretas são representadas com a cor azul.

O painel mostra esses problemas e informa quais intenções são afetadas e sugere o que você deve fazer para melhorar o aplicativo. 

## <a name="before-app-is-trained"></a>Antes que o aplicativo seja treinado 

Antes de treinar o aplicativo, o painel de instrumentos não contém nenhuma sugestão de correções. Treine seu aplicativo para ver essas sugestões.  

## <a name="check-your-publishing-status"></a>Verifique seu status de publicação

O **cartão de status Publishing** contém informações sobre a última publicação da versão ativa. 

Verifique se a versão ativa é a versão que você deseja corrigir. 

![O dashboard mostra os serviços externos do aplicativo, regiões publicadas e hits de ponto final agregados.](./media/luis-how-to-use-dashboard/analytics-card-1-shows-app-summary-and-endpoint-hits.png)

Isso também mostra quaisquer serviços externos, regiões publicadas e hits de ponto final agregados. 

## <a name="review-training-evaluation"></a>Avaliação do treinamento

O **cartão de avaliação treinamento** contém o resumo agregado da precisão geral do seu aplicativo por área. A pontuação indica qualidade de intenção. 

![O cartão de avaliação de treinamento contém a primeira área de informações sobre a precisão geral do seu aplicativo.](./media/luis-how-to-use-dashboard/analytics-card-2-shows-app-overall-accuracy.png)

O gráfico indica as intenções corretamente previstas e as áreas problemáticas com cores diferentes. À medida que você melhora o aplicativo com as sugestões, essa pontuação aumenta. 

As correções sugeridas são separadas por tipo de problema e são as mais significativas para o seu aplicativo. Se preferir rever e corrigir problemas por intenção, use o **[cartão Intenções com erros](#intents-with-errors)** na parte inferior da página. 

Cada área problemática tem intenções que precisam ser corrigidas. Quando você seleciona o nome da intenção, a página **Intenção** é aberta com um filtro aplicado às expressões. Este filtro permite que você se concentre nas expressões que estão causando o problema.

### <a name="compare-changes-across-versions"></a>Compare as alterações entre as versões

Crie uma nova versão antes de fazer alterações no aplicativo. Na nova versão, faça as alterações sugeridas nas declarações de exemplo da intenção e treine novamente. No cartão de **avaliação treinamento** da página painel, use a **mudança Show da versão treinada** para comparar as alterações. 

![Compare as alterações entre as versões](./media/luis-how-to-use-dashboard/compare-improvement-across-versions.png)

### <a name="fix-version-by-adding-or-editing-example-utterances-and-retraining"></a>Corrigir versão adicionando ou editando expressões de exemplo e retreinamento

O principal método de fixação do seu aplicativo será adicionar ou editar enunciados de exemplo e retreinar. As declarações novas ou alteradas precisam seguir as diretrizes para [expressões variadas.](luis-concept-utterance.md)

Adicionar expressões de exemplo deve ser feito por alguém que:

* tem um alto grau de compreensão do que são as expressões nas diferentes intenções.
* sabe como as declarações em uma intenção podem ser confundidas com outra intenção.
* é capaz de decidir se duas intenções, que são frequentemente confundidas entre si, devem ser colapsadas em uma única intenção. Se esse for o caso, os diferentes dados devem ser retirados com entidades.

### <a name="patterns-and-phrase-lists"></a>Padrões e listas de frases

A página de análise não indica quando usar [padrões](luis-concept-patterns.md) ou [listas de frases](luis-concept-feature.md). Se você adicioná-las, pode ajudar com previsões incorretas ou pouco claras, mas não ajudará com o desequilíbrio de dados. 

### <a name="review-data-imbalance"></a>Revisar o desequilíbrio dos dados

Comece com este problema - pode ser a causa raiz dos outros problemas.

A lista de intenções **de desequilíbrio** de dados mostra intenções que precisam de mais expressões para corrigir o desequilíbrio de dados. 

**Para corrigir esse problema:**

* Adicione mais expressões à intenção e depois treine novamente. 

Não adicione expressões à intenção De Nenhum, a menos que isso seja sugerido no painel.

> [!Tip]
> Use a terceira seção na página, **Expressões por intenção** com a configuração **Enunciados (número),** como um guia visual rápido de quais intenções precisam de mais expressões.  
    ![Use 'Expressões (número)' para encontrar intenções com desequilíbrio de dados.](./media/luis-how-to-use-dashboard/predictions-per-intent-number-of-utterances.png)

### <a name="review-incorrect-predictions"></a>Revisar previsões incorretas

A lista de intenções **de previsão incorreta** mostra intenções que têm expressões, que são usadas como exemplos para uma intenção específica, mas são previstas para diferentes intenções. 

**Para corrigir esse problema:**

* Editar enunciados para ser mais específico para a intenção e treinar novamente.
* Combine intenções se as declarações estiverem muito alinhadas e treine novamente.

### <a name="review-unclear-predictions"></a>Revisar previsões pouco claras

A lista de intenções de **previsão pouco clara** mostra intenções com declarações com pontuações de previsão que não estão longe o suficiente de seu rival mais próximo, que a intenção superior para o enunciado pode mudar no próximo treinamento, devido à [amostragem negativa](luis-how-to-train.md#train-with-all-data).

**Para corrigir esse problema;**

* Editar enunciados para ser mais específico para a intenção e treinar novamente.
* Combine intenções se as declarações estiverem muito alinhadas e treine novamente.

## <a name="utterances-per-intent"></a>Enunciados por intenção

Este cartão mostra a saúde geral do aplicativo em todas as intenções. À medida que você corrige intenções e retreina, continue a olhar para este cartão para problemas.

O gráfico a seguir mostra um aplicativo bem equilibrado, quase sem problemas para corrigir.

![O gráfico a seguir mostra um aplicativo bem equilibrado, quase sem problemas para corrigir.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-balance.png)

O gráfico a seguir mostra um aplicativo mal equilibrado com muitos problemas para corrigir.

![O gráfico a seguir mostra um aplicativo bem equilibrado, quase sem problemas para corrigir.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-imbalance.png)

Paire sobre o bar de cada intenção para obter informações sobre a intenção. 

![O gráfico a seguir mostra um aplicativo bem equilibrado, quase sem problemas para corrigir.](./media/luis-how-to-use-dashboard/utterances-per-intent-with-details-of-errors.png)

Use o **Recurso Classificar por** recurso para organizar as intenções por tipo de problema para que você possa se concentrar nas intenções mais problemáticas com esse problema. 

## <a name="intents-with-errors"></a>Intenções com erros

Este cartão permite que você revise problemas para uma intenção específica. A visão padrão deste cartão são as intenções mais problemáticas para que você saiba onde concentrar seus esforços.

![O cartão Intenções com erros permite que você revise problemas para uma intenção específica. O cartão é filtrado para as intenções mais problemáticas, por padrão, para que você saiba onde concentrar seus esforços.](./media/luis-how-to-use-dashboard/most-problematic-intents-with-errors.png)

O gráfico de donuts superior mostra os problemas com a intenção entre os três tipos de problemas. Se houver problemas nos três tipos de problemas, cada tipo tem seu próprio gráfico abaixo, juntamente com quaisquer intenções rivais. 

### <a name="filter-intents-by-issue-and-percentage"></a>Intenções de filtro por problema e porcentagem

Esta seção do cartão permite que você encontre expressões de exemplo que estão caindo fora do seu limite de erro. O ideal é que as previsões corretas sejam significativas. Essa porcentagem é orientada para negócios e clientes. 

Determine as porcentagens de limiar com as quais você está confortável para o seu negócio. 

O filtro permite encontrar intenções com problemas específicos:

|Filtrar|Porcentagem sugerida|Finalidade|
|--|--|--|
|Intenções mais problemáticas|-|**Comece aqui** - Corrigir as expressões nesta intenção melhorará o aplicativo mais do que outras correções.|
|Previsões corretas abaixo|60%|Esta é a porcentagem de expressões na intenção selecionada que estão corretas, mas têm uma pontuação de confiança abaixo do limiar. |
|Previsões pouco claras acima|15%|Esta é a porcentagem de expressões na intenção selecionada que são confundidas com a intenção rival mais próxima.|
|Previsões incorretas acima|15%|Esta é a porcentagem de expressões na intenção selecionada que são preditas incorretamente. |

### <a name="correct-prediction-threshold"></a>Limite correto de previsão

O que é um índice de confiança de previsão para você? No início do desenvolvimento do aplicativo, 60% pode ser seu alvo. Use as **previsões corretas abaixo** com a porcentagem de 60% para encontrar quaisquer expressões na intenção selecionada que precisam ser corrigidas.

### <a name="unclear-or-incorrect-prediction-threshold"></a>Limite de previsão incerto ou incorreto

Esses dois filtros permitem que você encontre expressões na intenção selecionada além do seu limite. Você pode pensar nessas duas porcentagens como porcentagens de erro. Se você estiver confortável com uma taxa de erro de 10 a 15% para previsões, defina o limite do filtro para 15% para encontrar todas as expressões acima desse valor. 

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar seus recursos do Azure](luis-how-to-azure-subscription.md)
