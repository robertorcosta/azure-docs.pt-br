---
title: Suporte a contêiner
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/23/2019
ms.author: dapine
ms.openlocfilehash: ecb172aec37fdab21bed8373ac7484dc52a54829
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72821945"
---
## <a name="create-a-luis-resource"></a>Criar um recurso LUIS

1. Entre no [Portal do Azure](https://portal.azure.com)
1. Clique em [criar **reconhecimento vocal** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. Insira todas as configurações necessárias:

    |Configuração|Value|
    |--|--|
    |name|Nome desejado (2-64 caracteres)|
    |Subscription|Selecione a assinatura apropriada|
    |Location|Selecione qualquer local próximo e disponível|
    |Camada de preços|`F0`-o tipo de preço mínimo|
    |Grupo de recursos|Selecionar um grupo de recursos disponível|

1. Clique em **criar** e aguarde até que o recurso seja criado. Depois de criado, navegue até a página de recursos
1. Coletar `endpoint` configuradas e uma chave de API:

    |Guia de recursos no portal|Configuração|Value|
    |--|--|--|
    |**Visão geral**|Ponto de extremidade|Copie o ponto de extremidade. Ele é semelhante a `https://luis.cognitiveservices.azure.com/luis/v2.0`|
    |**Chaves**|Chave de API|Cópia 1 das duas chaves. É uma cadeia de caracteres de caractere alfanumérico 32 sem espaços ou traços, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
