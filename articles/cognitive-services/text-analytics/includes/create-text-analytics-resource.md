---
title: Criar um recurso de Análise de Texto de serviços cognitivas
titleSuffix: Azure Cognitive Services
description: Saiba como criar um recurso de Análise de Texto de Serviços Cognitivos.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 6cd653909e26dc5e0484ca289a1d2ab47e20457f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876380"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Criar um recurso de Análise de Texto de serviços cognitivas

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione **criar um recurso**e vá para **ia + Machine Learning**  >  **análise de texto**.
   Ou vá para [criar análise de texto](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Insira todas as configurações necessárias:

    |Configuração|Valor|
    |--|--|
    |Nome|Insira um nome (2-64 caracteres).|
    |Subscription|Selecione a assinatura apropriada.|
    |Location|Selecione um local próximo.|
    |Tipo de preço| Insira **S**, o tipo de preço Standard.|
    |Resource group|Selecione um grupo de recursos disponível.|

1. Selecione **criar**e aguarde até que o recurso seja criado. Seu navegador redireciona automaticamente para a página de recursos recém-criada.
1. Coletar o configurado `endpoint` e uma chave de API:

    |Guia de recursos no portal|Configuração|Valor|
    |--|--|--|
    |**Visão geral**|Ponto de Extremidade|Copie o ponto de extremidade. Ele é semelhante a `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0` .|
    |**Chaves**|Chave de API|Copie uma das duas chaves. É uma cadeia de caracteres alfanumérica de 32 caracteres sem espaços ou traços: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
