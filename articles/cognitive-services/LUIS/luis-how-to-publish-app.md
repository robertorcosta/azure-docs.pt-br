---
title: Publish app - LUIS
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
ms.openlocfilehash: f213c1d43930075c78cf81de345f612e46bbfb1c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74221716"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>Publicar seu aplicativo ativo, treinado em um ponto de extremidade de preparo ou produção

When you finish building, training, and testing your active LUIS app, make it available to your client application by publishing it to the endpoint. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="publishing"></a>Publicação

1. Para publicar no ponto de extremidade, selecione **Publicar** no painel superior direito. 

    ![Publish button in top, right nav bar](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

1. Select your settings for the published prediction endpoint, then select **Publish**.

    ![Select publish settings then select Publish button](./media/luis-how-to-publish-app/publish-pop-up.png)

### <a name="publishing-slots"></a>Publishing slots

Select the correct slot when the pop-up window displays: 

* Preparação
* Produção 

By using both publishing slots, this allows you to have two different versions of your app available at the published endpoints or the same version on two different endpoints. 

### <a name="publishing-regions"></a>Regiões de publicação

The app is published to all regions associated with the LUIS prediction endpoint resources added in the LUIS portal from the **Manage** ->  **[Azure Resources](/luis-how-to-azure-subscription.md#assign-a-resource-to-an-app)** page. 

For example, for an app created on [www.luis.ai](https://www.luis.ai), if you create a LUIS resource in two regions, **westus** and **eastus**, and add these to the app as resources, the app is published in both regions. Para obter mais informações sobre as regiões do LUIS, consulte [Regiões](luis-reference-regions.md).

> [!TIP]
> There are 3 authoring regions. You must author in the region you intend to publish to. If you need to publish to all regions, you need to manage your authoring process and the resulting trained model in all 3 authoring regions. 


## <a name="configuring-publish-settings"></a>Definindo configurações de publicação

After you select the slot, configure the publish settings for:

* Análise de sentimento
* Spelling correction - v2 prediction endpoint only
* Preparação da fala 

After you publish, these settings are available for review from the **Manage** section's **Publish settings** page. You can change the settings with every publish. If you cancel a publish, any changes you made during the publish are also canceled. 

### <a name="when-your-app-is-published"></a>When your app is published

When your app is successfully published, a success notification appears at the top of the browser. The notification also includes a link to the endpoints. 

Se você precisar da URL do ponto de extremidade, selecione o link. You can also get to the endpoint URLs by selecting **Manage** in the top menu, then select **Azure Resources** in the left menu. 

## <a name="sentiment-analysis"></a>Análise de sentimento

<a name="enable-sentiment-analysis"></a>

A análise de sentimento permite ao LUIS fazer a integração com a [Análise de Texto](https://azure.microsoft.com/services/cognitive-services/text-analytics/) para fornecer análise de frase-chave e sentimento. 

Você não precisa fornecer uma chave de Análise de Texto e não é cobrando nenhum encargo para esse serviço na sua conta do Azure. 

Dados de sentimento são uma pontuação entre 1 e 0 indicando o sentimento positivo (mais próximo de 1) ou negativo (mais próximo de 0) dos dados. O rótulo de sentimento de `positive`, `neutral` e `negative` é por cultura com suporte. Atualmente, somente inglês dá suporte a rótulos de sentimento. 

Para obter mais informações sobre a resposta do ponto de extremidade JSON com análise de sentimento, veja [Análise de sentimento](luis-concept-data-extraction.md#sentiment-analysis)

## <a name="spelling-correction"></a>Spelling correction

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]

Corrections to spelling are made before the LUIS user utterance prediction. You can see any changes to the original utterance, including spelling, in the response.

## <a name="speech-priming"></a>Preparação da fala

Speech priming is the process of using sending the LUIS model to Speech services prior to conversion of text to speech. This allows the speech service to provide speech conversion more accurately for your model. This allows bot Speech and LUIS requests and responses in one call by making one speech call and getting back a LUIS response. It provides less latency overall.

## <a name="next-steps"></a>Próximos passos

* Consulte [Gerenciar chaves](./luis-how-to-azure-subscription.md) para adicionar a chave de assinatura do Azure ao LUIS e saber como definir a chave de Verificação Ortográfica do Bing e incluir todas as intenções nos resultados.
* Veja [Treinar e testar seu aplicativo](luis-interactive-test.md) para obter instruções sobre como testar seu aplicativo publicado no console de teste.

