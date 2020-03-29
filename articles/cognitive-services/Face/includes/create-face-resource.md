---
title: Suporte a contêiner
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 3886777b283af35e84683480a59097584b537fea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73522280"
---
## <a name="create-an-face-resource"></a>Criar um recurso Face

1. Inscreva-se no [portal Azure](https://portal.azure.com)
1. Clique [em Criar recurso **Face** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)
1. Digite todas as configurações necessárias:

    |Configuração|Valor|
    |--|--|
    |Nome|Nome desejado (2 a 64 caracteres)|
    |Subscription|Selecione a assinatura apropriada|
    |Location|Selecione qualquer localização próxima e disponível|
    |Camada de preços|`F0` – o tipo de preço mínimo|
    |Grupo de recursos|Selecione um grupo de recursos disponível|

1. Clique em **Criar** e aguarde até que o recurso seja criado. Depois de criado, navegue até a página de recursos
1. Coletar configurado `endpoint` e uma chave de API:

    |Guia de recursos no Portal|Configuração|Valor|
    |--|--|--|
    |**Visão geral**|Ponto de extremidade|Copie o ponto final. Parece com`https://face.cognitiveservices.azure.com/face/v1.0`|
    |**Chaves**|Chave de API|Copie uma das duas chaves. É uma seqüência de caracteres alfanuméricos de `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`32 sem espaços ou traços, .|
