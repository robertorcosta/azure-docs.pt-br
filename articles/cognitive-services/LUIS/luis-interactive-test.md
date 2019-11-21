---
title: Testar o aplicativo no portal do LUIS
titleSuffix: Azure Cognitive Services
description: Use o LUIS (Serviço Inteligente de Reconhecimento Vocal) para trabalhar continuamente em seu aplicativo para refiná-lo e melhorar seu reconhecimento vocal.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 628547e8254bb0055cf1f09af50e79b68311a759
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74221768"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>Testar seu aplicativo LUIS no portal do LUIS

[Testing](luis-concept-test.md) an app is an iterative process. Após treinar seu aplicativo LUIS, teste-o com declarações de exemplo para ver se as intenções e as entidades são reconhecidas corretamente. Caso não sejam, faça atualizações ao aplicativo de LUIS, treine e teste novamente. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

<!-- anchors for H2 name changes -->
<a name="train-your-app"></a>
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="train-before-testing"></a>Train before testing

In order to test against the most recent version of the active app, select **Train** from the top menu, before testing. 

## <a name="test-an-utterance"></a>Testar uma declaração

The test utterance should not be exactly the same as any example utterances in the app. The test utterance should include word choice, phrase length, and entity usage you expect for a user. 

1. Acesse seu aplicativo selecionando seu nome na página **Meus Aplicativos**. 

1. Para acessar o painel extensível **Testar**, selecione **Testar** no painel superior do seu aplicativo.

    > [!div class="mx-imgBorder"]
    > ![Train & Test App page](./media/luis-how-to-interactive-test/test.png)

1. Insira uma declaração na caixa de texto e selecione Enter. É possível digitar quantas declarações de teste você desejar no **Teste**, mas apenas uma declaração por vez.

1. A declaração, sua principal intenção e a pontuação são adicionadas à lista de declarações embaixo da caixa de texto.

    ![O teste interativo identifica a intenção incorreta](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-score"></a>Inspecionar pontuação

Inspecione detalhes do resultado do teste no painel **Inspecionar**. 
 
1. Com o painel extensível **Testar** aberto, selecione **Inspecionar** para uma declaração que você deseja comparar. 

    ![Selecione o botão Inspecionar para ver mais detalhes sobre os resultados do teste](./media/luis-how-to-interactive-test/inspect.png)

1. O painel **Inspeção** é exibido. O painel inclui a principal intenção de pontuação e as entidades identificadas. O painel mostra o resultado da declaração selecionada.

    ![O painel inclui a principal intenção de pontuação e as entidades identificadas. O painel mostra o resultado da declaração selecionada.](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="correct-top-scoring-intent"></a>Corrigir principal intenção de pontuação

1. Se a principal intenção de pontuação estiver incorreta, selecione o botão **Editar**.

1.  Na lista suspensa, selecione a intenção correta para a declaração.

    ![Selecionar intenção correta](./media/luis-how-to-interactive-test/intent-select.png)

## <a name="view-sentiment-results"></a>Exibir resultados de sentimento

Se a **Análise de sentimento** estiver configurada na página **[Publicar](luis-how-to-publish-app.md#enable-sentiment-analysis)** , os resultados de teste incluirão o sentimento encontrado na declaração. 

![Imagem do painel de Teste com análise de sentimento](./media/luis-how-to-interactive-test/sentiment.png)

## <a name="correct-matched-patterns-intent"></a>Corrigir intenção do padrão correspondente

Se estiver usando [Padrões](luis-concept-patterns.md) e a declaração tiver correspondido a um padrão, mas a intenção incorreta estava prevista, selecione o link **Editar** pelo padrão e selecione a intenção correta.

## <a name="compare-with-published-version"></a>Comparar com a versão publicada

É possível testar a versão ativa do seu aplicativo com a versão do [ponto de extremidade](luis-glossary.md#endpoint) publicada. No painel **Inspecionar**, selecione **Comparar com publicado**. Qualquer teste com relação ao modelo publicado é deduzido do saldo da sua cota de assinatura do Azure. 

![Comparar com o publicado](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>Exibir JSON de ponto de extremidade no painel de teste
É possível exibir o JSON do ponto de extremidade retornado para a comparação selecionando **Mostrar exibição JSON**.

![Resposta JSON publicada](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

<!--Service name is 'Bing Spell Check v7 API' in the portal-->
## <a name="additional-settings-in-test-panel"></a>Configurações adicionais no painel de teste

### <a name="luis-endpoint"></a>Ponto de extremidade LUIS

Se tiver vários pontos de extremidade LUIS, use o link **Configurações Adicionais** no painel Publicado do Teste para alterar o ponto de extremidade usado para teste. Se não tiver certeza sobre qual ponto de extremidade usar, selecione o **Starter_Key** padrão. 

> [!div class="mx-imgBorder"]
> ![Test panel with Additional Settings link highlighted](media/luis-how-to-interactive-test/additional-settings-v3-settings.png)

<!--
###  View Bing Spell Check corrections in test panel

Requirements to view the spelling corrections: 

* Published app
* Bing Spell Check [service key](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api). The service key is not stored and needs to be reset for each browser session. 

Use the following procedure to include the [Bing Spell Check v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) service  in the Test pane results. 

1. In the **Test** pane, enter an utterance. When the utterance is predicted, select **[Inspect](#inspect-score)** underneath the utterance you entered. 

1. When the **Inspect** panel opens, select **[Compare with Published](#compare-with-published-version)**. 

1. When the **Published** panel opens, select **[Additional Settings](#additional-settings-in-test-panel)**.

1. In the pop-up dialog, check **Enable Bing Spell Check** and enter the key, then select **Done**. 
    ![Enter Bing Spell Check service key](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key-text.png)

1. Enter a query with an incorrect spelling such as `book flite to seattle` and select enter. The incorrect spelling of the word `flite` is replaced in the query sent to LUIS and the resulting JSON shows both the original query, as `query`, and the corrected spelling in the query, as `alteredQuery`.

<a name="json-file-with-no-duplicates"></a>
<a name="import-a-dataset-file-for-batch-testing"></a>
<a name="export-rename-delete-or-download-dataset"></a>
<a name="run-a-batch-test-on-your-trained-app"></a>
<a name="access-batch-test-result-details-in-a-visualized-view"></a>
<a name="filter-chart-results-by-intent-or-entity"></a>
<a name="investigate-false-sections"></a>
<a name="view single-point utterance data"></a>
<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>
-->

## <a name="batch-testing"></a>Teste em lote
Confira os [conceitos](luis-concept-batch-test.md) de teste do lote e saiba [como](luis-how-to-batch-test.md) testar um lote de declarações.

## <a name="next-steps"></a>Próximos passos

Se o teste indica que seu aplicativo LUIS não reconhece as intenções e entidades corretas, é possível trabalhar para melhorar a precisão do seu aplicativo LUIS rotulando mais declarações ou adicionando recursos. 

* [Rotular enunciados sugeridos com o LUIS](luis-how-to-review-endpoint-utterances.md) 
* [Usar recursos para melhorar o desempenho do aplicativo de LUIS](luis-how-to-add-features.md) 
