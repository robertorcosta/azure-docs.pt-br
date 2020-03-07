---
title: Testar o aplicativo no portal do LUIS
description: Use o LUIS (Serviço Inteligente de Reconhecimento Vocal) para trabalhar continuamente em seu aplicativo para refiná-lo e melhorar seu reconhecimento vocal.
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: ba7e23a72cd308dd4393bf9a581571e2bc9f5fa0
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361063"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>Testar seu aplicativo LUIS no portal do LUIS

[Testar](luis-concept-test.md) um aplicativo é um processo iterativo. Após treinar seu aplicativo LUIS, teste-o com declarações de exemplo para ver se as intenções e as entidades são reconhecidas corretamente. Caso não sejam, faça atualizações ao aplicativo de LUIS, treine e teste novamente.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

<!-- anchors for H2 name changes -->
<a name="train-your-app"></a>
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="train-before-testing"></a>Treinar antes do teste

Para testar a versão mais recente do aplicativo ativo, selecione **treinar** no menu superior, antes de testar.

## <a name="test-an-utterance"></a>Testar uma declaração

O expressão de teste não deve ser exatamente o mesmo que qualquer declarações de exemplo no aplicativo. O expressão de teste deve incluir a opção de palavra, o comprimento da frase e o uso da entidade que você espera para um usuário.

1. Acesse seu aplicativo selecionando seu nome na página **Meus Aplicativos**.

1. Para acessar o painel extensível **Testar**, selecione **Testar** no painel superior do seu aplicativo.

    > [!div class="mx-imgBorder"]
    > página do aplicativo ![Train & Test](./media/luis-how-to-interactive-test/test.png)

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

## <a name="additional-settings-in-test-panel"></a>Configurações adicionais no painel de teste

### <a name="luis-endpoint"></a>Ponto de extremidade LUIS

Se tiver vários pontos de extremidade LUIS, use o link **Configurações Adicionais** no painel Publicado do Teste para alterar o ponto de extremidade usado para teste. Se não tiver certeza sobre qual ponto de extremidade usar, selecione o **Starter_Key** padrão.

> [!div class="mx-imgBorder"]
> ![painel de teste com configurações adicionais link realçado](media/luis-how-to-interactive-test/additional-settings-v3-settings.png)


## <a name="batch-testing"></a>Teste em lote
Confira os [conceitos](luis-concept-batch-test.md) de teste do lote e saiba [como](luis-how-to-batch-test.md) testar um lote de declarações.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Se o teste indica que seu aplicativo LUIS não reconhece as intenções e entidades corretas, é possível trabalhar para melhorar a precisão do seu aplicativo LUIS rotulando mais declarações ou adicionando recursos.

* [Rotular enunciados sugeridos com o LUIS](luis-how-to-review-endpoint-utterances.md)
* [Usar recursos para melhorar o desempenho do aplicativo de LUIS](luis-how-to-add-features.md)
