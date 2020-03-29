---
title: Suporte a contêiner
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/07/2019
ms.author: dapine
ms.openlocfilehash: 2cb2cfbdfbac5d496f109d85977b41a050766ab0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73499094"
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
