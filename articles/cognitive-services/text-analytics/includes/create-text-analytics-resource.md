---
title: Crie um recurso de análise de texto de serviços cognitivos
titleSuffix: Azure Cognitive Services
description: Saiba como criar um recurso de Análise de Texto de Serviços Cognitivos.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: d3b65174f3d161e0b8780a890f297d9d1c812b44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74383417"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Crie um recurso de análise de texto de serviços cognitivos

1. Faça login no [portal Azure](https://portal.azure.com).
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
    |**Chaves**|Chave de API|Copie uma das duas chaves. É uma corda alfanumérica de 32 caracteres sem espaços `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` ou traços: <>.|
