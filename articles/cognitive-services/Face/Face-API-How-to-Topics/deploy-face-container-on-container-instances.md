---
title: Executar o contêiner facial em instâncias de contêiner do Azure
titleSuffix: Azure Cognitive Services
description: Implante o contêiner facial em uma instância de contêiner do Azure e teste-o em um navegador da Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: aahi
ms.openlocfilehash: 5fbe316580cfa2ca280a2587536df037146e8d02
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86538115"
---
# <a name="deploy-the-face-container-to-azure-container-instances"></a>Implantar o contêiner facial nas instâncias de contêiner do Azure

> [!IMPORTANT]
> O limite para usuários de contêiner facial foi atingido. No momento, não estamos aceitando novos aplicativos para o contêiner facial.

Saiba como implantar o contêiner [facial](../face-how-to-install-containers.md) de serviços cognitivas nas [instâncias de contêiner](https://docs.microsoft.com/azure/container-instances/)do Azure. Este procedimento demonstra a criação de um recurso de face do Azure. Em seguida, discutimos a extração da imagem de contêiner associada. Por fim, destacamos a capacidade de exercitar a orquestração dos dois em um navegador. O uso de contêineres pode mudar a atenção dos desenvolvedores para longe de gerenciar a infraestrutura para se concentrar no desenvolvimento de aplicativos.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

[!INCLUDE [Create a Cognitive Services Face resource](../includes/create-face-resource.md)]

[!INCLUDE [Create an Face container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../../containers/includes/containers-next-steps.md)]