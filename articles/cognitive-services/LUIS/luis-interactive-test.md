---
title: Testar o aplicativo no portal do LUIS
description: Use o LUIS (Serviço Inteligente de Reconhecimento Vocal) para trabalhar continuamente em seu aplicativo para refiná-lo e melhorar seu reconhecimento vocal.
ms.topic: how-to
ms.date: 05/20/2020
ms.openlocfilehash: 86ee90e2d3bb322a4f55439d105941cf43462d3e
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84344145"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>Testar seu aplicativo LUIS no portal do LUIS

[O teste](luis-concept-test.md) de um aplicativo é um processo iterativo. Após treinar seu aplicativo LUIS, teste-o com declarações de exemplo para ver se as intenções e as entidades são reconhecidas corretamente. Caso não sejam, faça atualizações ao aplicativo de LUIS, treine e teste novamente.

<!-- anchors for H2 name changes -->
<a name="train-your-app"></a>
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="train-before-testing"></a>Treinar antes do teste

1. Entre no [portal do LUIS](https://www.luis.ai) selecione sua **Assinatura** e **Recurso de criação** para ver os aplicativos atribuídos a esse recurso.
1. Abra seu aplicativo selecionando seu nome na página **meus aplicativos** .
1. Para testar a versão mais recente do aplicativo ativo, selecione **treinar** no menu superior, antes de testar.

## <a name="test-an-utterance"></a>Testar uma declaração

O expressão de teste não deve ser exatamente o mesmo que qualquer declarações de exemplo no aplicativo. O expressão de teste deve incluir a opção de palavra, o comprimento da frase e o uso da entidade que você espera para um usuário.

1. Entre no [portal do LUIS](https://www.luis.ai) selecione sua **Assinatura** e **Recurso de criação** para ver os aplicativos atribuídos a esse recurso.
1. Abra seu aplicativo selecionando seu nome na página **meus aplicativos** .

1. Para acessar o painel de deslizamento de **teste** , selecione **teste** no painel superior do seu aplicativo.

    > [!div class="mx-imgBorder"]
    > ![Página treinar & testar aplicativo](./media/luis-how-to-interactive-test/test.png)

1. Insira uma declaração na caixa de texto e selecione Enter. É possível digitar quantas declarações de teste você desejar no **Teste**, mas apenas uma declaração por vez.

1. A declaração, sua principal intenção e a pontuação são adicionadas à lista de declarações embaixo da caixa de texto.

    > [!div class="mx-imgBorder"]
    > ![O teste interativo identifica a intenção incorreta](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-the-prediction"></a>Inspecionar a previsão

Você inspeciona detalhes do resultado do teste no painel **inspecionar** .

1. Com o painel extensível **Testar** aberto, selecione **Inspecionar** para uma declaração que você deseja comparar.

    > [!div class="mx-imgBorder"]
    > ![Selecione o botão Inspecionar para ver mais detalhes sobre os resultados do teste](./media/luis-how-to-interactive-test/inspect.png)

1. O painel de **inspeção** é exibido. O painel inclui a principal intenção de pontuação e as entidades identificadas. O painel mostra a previsão do expressão selecionado.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela parcial do painel inspecionar teste](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="add-to-example-utterances"></a>Adicionar ao exemplo declarações

No painel de inspeção, você pode adicionar o expressão de teste a uma intenção selecionando **Adicionar ao exemplo declarações**.

## <a name="disable-required-features"></a>Desabilitar recursos necessários

Selecione essa alternância para ver o que a previsão seria se o recurso da entidade não fosse necessário.

Essa alternância ajuda a determinar se o aplicativo treinado está prevendo corretamente suas entidades com base nos recursos necessários. O aplicativo treinado pode prever incorretamente uma entidade aprendida por máquina com base em rótulos incorretos de exemplo declarações ou o recurso necessário não corresponde ao texto.

## <a name="view-sentiment-results"></a>Exibir resultados de sentimento

Se a **Análise de sentimento** estiver configurada na página **[Publicar](luis-how-to-publish-app.md#enable-sentiment-analysis)**, os resultados de teste incluirão o sentimento encontrado na declaração.

## <a name="correct-matched-patterns-intent"></a>Corrigir intenção do padrão correspondente

Se estiver usando [Padrões](luis-concept-patterns.md) e a declaração tiver correspondido a um padrão, mas a intenção incorreta estava prevista, selecione o link **Editar** pelo padrão e selecione a intenção correta.

## <a name="compare-with-published-version"></a>Comparar com a versão publicada

É possível testar a versão ativa do seu aplicativo com a versão do [ponto de extremidade](luis-glossary.md#endpoint) publicada. No painel **Inspecionar**, selecione **Comparar com publicado**. Qualquer teste com relação ao modelo publicado é deduzido do saldo da sua cota de assinatura do Azure.

> [!div class="mx-imgBorder"]
> ![Comparar com o publicado](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>Exibir JSON de ponto de extremidade no painel de teste
É possível exibir o JSON do ponto de extremidade retornado para a comparação selecionando **Mostrar exibição JSON**.

> [!div class="mx-imgBorder"]
> ![Resposta JSON publicada](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

## <a name="additional-settings-in-test-panel"></a>Configurações adicionais no painel de teste

### <a name="luis-endpoint"></a>Ponto de extremidade LUIS

Se tiver vários pontos de extremidade LUIS, use o link **Configurações Adicionais** no painel Publicado do Teste para alterar o ponto de extremidade usado para teste. Se não tiver certeza sobre qual ponto de extremidade usar, selecione o **Starter_Key** padrão.

> [!div class="mx-imgBorder"]
> ![Testar painel com o link Configurações Adicionais realçado](media/luis-how-to-interactive-test/additional-settings-v3-settings.png)


## <a name="batch-testing"></a>Teste em lote
Confira os [conceitos](luis-concept-batch-test.md) de teste do lote e saiba [como](luis-how-to-batch-test.md) testar um lote de declarações.

## <a name="next-steps"></a>Próximas etapas

Se o teste indica que seu aplicativo LUIS não reconhece as intenções e entidades corretas, é possível trabalhar para melhorar a precisão do seu aplicativo LUIS rotulando mais declarações ou adicionando recursos.

* [Rotular enunciados sugeridos com o LUIS](luis-how-to-review-endpoint-utterances.md)
* [Usar recursos para melhorar o desempenho do aplicativo de LUIS](luis-how-to-add-features.md)
