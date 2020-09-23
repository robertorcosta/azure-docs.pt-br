---
title: Suporte a contêiner
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 8f93e0ffe74ade79059fc845788faf36ab5b4f13
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "91025106"
---
## <a name="create-an-anomaly-detector-resource"></a>Criar um recurso do Detector de Anomalias

1. Entre no [Portal do Azure](https://portal.azure.com)
1. Clique em criar recurso de [ **detector de anomalias** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector)
1. Insira todas as configurações necessárias:

    |Configuração|Valor|
    |--|--|
    |Nome|Nome desejado (2 a 64 caracteres)|
    |Subscription|Selecione a assinatura apropriada|
    |Location|Selecione qualquer localização próxima e disponível|
    |Camada de preços|`F0` – o tipo de preço mínimo|
    |Grupo de recursos|Selecione um grupo de recursos disponível|

1. Clique em **Criar** e aguarde até que o recurso seja criado. Depois de criado, navegue até a página de recursos
1. Coletar configurado `endpoint` e uma chave de API:

    |Guia de recursos no portal|Configuração|Valor|
    |--|--|--|
    |**Visão geral**|Ponto de extremidade|Copie o ponto de extremidade. Ele é semelhante a `https://westus2.api.cognitive.microsoft.com/`|
    |**Chaves**|Chave de API|Cópia 1 das duas chaves. É uma cadeia de caracteres alfanuméricos 32 sem espaços ou traços `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` .|



