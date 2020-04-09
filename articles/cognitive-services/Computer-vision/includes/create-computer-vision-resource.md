---
title: Suporte a contêiner
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 2647c604bebc2885f5c8516eb997792b65751e88
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876059"
---
## <a name="create-an-computer-vision-resource"></a>Crie um recurso de visão computacional

1. Inscreva-se no [portal Azure](https://portal.azure.com).
1. Clique em Criar recurso [ **de visão de computador.** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)
1. Digite todas as configurações necessárias:

    |Configuração|Valor|
    |--|--|
    |Nome|Nome desejado (2 a 64 caracteres)|
    |Subscription|Selecione a assinatura apropriada|
    |Location|Selecione qualquer localização próxima e disponível|
    |Camada de preços|`F0` – o tipo de preço mínimo|
    |Grupo de recursos|Selecione um grupo de recursos disponível|

1. Clique em **Criar** e aguarde até que o recurso seja criado. Após a criação dele, navegue até a página do recurso.
1. Coletar `{ENDPOINT_URI}` configurado `{API_KEY}`e, ver [a coleta de parâmetros necessários](../computer-vision-how-to-install-containers.md#gathering-required-parameters) para detalhes.
