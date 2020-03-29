---
title: Criar recurso LUIS
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/23/2019
ms.author: dapine
ms.openlocfilehash: a765ac27936da9da5a2f41464c17491e3561f44b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73465956"
---
## <a name="create-a-luis-resource"></a>Crie um recurso LUIS

1. Inscreva-se no [portal Azure](https://portal.azure.com)
1. Clique em [Criar **Reconhecimento vocal**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. Digite todas as configurações necessárias:

    |Configuração|Valor|
    |--|--|
    |Nome|Nome desejado (2 a 64 caracteres)|
    |Subscription|Selecione a assinatura apropriada|
    |Location|Selecione qualquer localização próxima e disponível|
    |Camada de preços|`F0` – o tipo de preço mínimo|
    |Grupo de recursos|Selecione um grupo de recursos disponível|

1. Clique em **Criar** e aguarde até que o recurso seja criado. Após a criação dele, navegue até a página do recurso.
1. Coletar configurado `endpoint` e uma chave de API, ver [a coleta de parâmetros necessários](#gathering-required-parameters).

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]
