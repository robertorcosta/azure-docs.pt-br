---
title: Crie um recurso de análise de texto de serviços cognitivos
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
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876380"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Crie um recurso de análise de texto de serviços cognitivos

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione **Criar um recurso**e, em seguida, vá para **AI + Machine Learning** > **Text Analytics**.
   Ou, vá para [Criar Análise de Texto](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Digite todas as configurações necessárias:

    |Configuração|Valor|
    |--|--|
    |Nome|Digite um nome (2-64 caracteres).|
    |Subscription|Selecione a assinatura apropriada.|
    |Location|Selecione um local próximo.|
    |Tipo de preço| Digite **S**, o nível de preços padrão.|
    |Resource group|Selecione um grupo de recursos disponível.|

1. Selecione **Criar**e aguarde a criação do recurso. Seu navegador redireciona automaticamente para a página de recursos recém-criada.
1. Coletar a `endpoint` chave configurada e uma API:

    |Guia de recursos no portal|Configuração|Valor|
    |--|--|--|
    |**Visão geral**|Ponto de extremidade|Copie o ponto final. Parece semelhante `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`a .|
    |**simétricas**|Chave de API|Copie uma das duas chaves. É uma corda alfanumérica de 32 caracteres sem espaços `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` ou traços: <>.|
