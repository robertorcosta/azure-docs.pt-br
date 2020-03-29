---
title: Publicar app - LUIS
titleSuffix: Azure Cognitive Services
description: Quando terminar de compilar e testar seu aplicativo LUIS ativo, disponibilize-o ao seu aplicativo cliente publicando-o no ponto de extremidade.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: c91a3ca73d70dd5fd2848bed0f43f14a817087d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053428"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>Publicar seu aplicativo ativo, treinado em um ponto de extremidade de preparo ou produção

Quando você terminar de construir, treinar e testar seu aplicativo LUIS ativo, disponibilize-o para o aplicativo do cliente publicando-o no ponto final. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="publishing"></a>Publicação

1. Para publicar no ponto de extremidade, selecione **Publicar** no painel superior direito. 

    ![Publicar botão na parte superior, barra de navegação direita](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

1. Selecione suas configurações para o ponto final de previsão publicado e selecione **Publicar**.

    ![Selecione as configurações de publicação e selecione Publicar botão Publicar](./media/luis-how-to-publish-app/publish-pop-up.png)

### <a name="publishing-slots"></a>Slots de publicação

Selecione o slot correto quando a janela pop-up for exibida: 

* Staging
* Produção 

Usando ambos os slots de publicação, isso permite que você tenha duas versões diferentes do seu aplicativo disponíveis nos pontos finais publicados ou na mesma versão em dois pontos finais diferentes. 

### <a name="publishing-regions"></a>Regiões de publicação

O aplicativo é publicado em todas as regiões associadas aos recursos de ponto final de previsão do LUIS adicionados no portal LUIS a partir da página **Gerenciar** -> recursos do**[Azure.](luis-how-to-azure-subscription.md#assign-a-resource-to-an-app)** 

Por exemplo, para um aplicativo criado em [www.luis.ai](https://www.luis.ai), se você criar um recurso LUIS em duas regiões, **westus** e **eastus**, e adicioná-los ao aplicativo como recursos, o aplicativo é publicado em ambas as regiões. Para obter mais informações sobre as regiões do LUIS, consulte [Regiões](luis-reference-regions.md).

> [!TIP]
> Existem 3 regiões autorais. Você deve escrever na região que pretende publicar. Se você precisa publicar para todas as regiões, você precisa gerenciar seu processo de autoria e o modelo treinado resultante em todas as 3 regiões autorais. 


## <a name="configuring-publish-settings"></a>Definindo configurações de publicação

Depois de selecionar o slot, configure as configurações de publicação para:

* Análise de sentimento
* Correção ortográfica - somente ponto final de previsão v2
* Preparação da fala 

Após a publicação, essas configurações estarão disponíveis para revisão na página **'Publicar's'** da seção **Gerenciar.** Você pode alterar as configurações a cada publicação. Se você cancelar uma publicação, quaisquer alterações feitas durante a publicação também serão canceladas. 

### <a name="when-your-app-is-published"></a>Quando seu aplicativo é publicado

Quando seu aplicativo é publicado com sucesso, uma notificação de sucesso aparece na parte superior do navegador. A notificação também inclui um link para os pontos finais. 

Se você precisar da URL do ponto de extremidade, selecione o link. Você também pode chegar aos URLs do ponto final selecionando **Gerenciar** no menu superior e, em seguida, selecione **Recursos Azure** no menu esquerdo. 

## <a name="sentiment-analysis"></a>Análise de sentimento

<a name="enable-sentiment-analysis"></a>

A análise de sentimento permite ao LUIS fazer a integração com a [Análise de Texto](https://azure.microsoft.com/services/cognitive-services/text-analytics/) para fornecer análise de frase-chave e sentimento. 

Você não precisa fornecer uma chave de Análise de Texto e não é cobrando nenhum encargo para esse serviço na sua conta do Azure. 

Dados de sentimento são uma pontuação entre 1 e 0 indicando o sentimento positivo (mais próximo de 1) ou negativo (mais próximo de 0) dos dados. O rótulo de sentimento de `positive`, `neutral` e `negative` é por cultura com suporte. Atualmente, somente inglês dá suporte a rótulos de sentimento. 

Para obter mais informações sobre a resposta do ponto de extremidade JSON com análise de sentimento, veja [Análise de sentimento](luis-concept-data-extraction.md#sentiment-analysis)

## <a name="spelling-correction"></a>Correção ortográfica

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]

Correções à ortografia são feitas antes da previsão de pronunciamento do usuário LUIS. Você pode ver quaisquer alterações no enunciado original, incluindo a ortografia, na resposta.

## <a name="speech-priming"></a>Preparação da fala

O escoramento da fala é o processo de utilização do modelo LUIS para os serviços de Fala antes da conversão de texto para fala. Isso permite que o serviço de fala forneça conversão de fala com mais precisão para o seu modelo. Isso permite que o bot Speech e luis solicitem e respostas em uma chamada, fazendo uma chamada de fala e recebendo de volta uma resposta LUIS. Ele fornece menos latência em geral.

## <a name="next-steps"></a>Próximas etapas

* Consulte [Gerenciar chaves](./luis-how-to-azure-subscription.md) para adicionar a chave de assinatura do Azure ao LUIS e saber como definir a chave de Verificação Ortográfica do Bing e incluir todas as intenções nos resultados.
* Veja [Treinar e testar seu aplicativo](luis-interactive-test.md) para obter instruções sobre como testar seu aplicativo publicado no console de teste.

