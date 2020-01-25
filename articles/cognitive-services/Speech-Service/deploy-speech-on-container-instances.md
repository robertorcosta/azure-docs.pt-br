---
title: Executar instâncias de contêiner do Azure-serviço de fala
titleSuffix: Azure Cognitive Services
description: Implante o contêiner do serviço de fala em uma instância de contêiner do Azure e teste-o em um navegador da Web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: e5d9143b0c84b579945b283ccae1cce36a7a4291
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717415"
---
# <a name="deploy-the-speech-service-container-to-azure-container-instances"></a>Implantar o contêiner do serviço de fala nas instâncias de contêiner do Azure

Saiba como implantar o contêiner de [serviço de fala](speech-container-howto.md) dos serviços cognitivas nas [instâncias de contêiner](https://docs.microsoft.com/azure/container-instances/)do Azure. Este procedimento demonstra a criação de um recurso do serviço de fala do Azure. Em seguida, discutimos a extração da imagem de contêiner associada. Por fim, destacamos a capacidade de exercitar a orquestração dos dois em um navegador. O uso de contêineres pode mudar a atenção dos desenvolvedores para longe de gerenciar a infraestrutura para se concentrar no desenvolvimento de aplicativos.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Solicitar acesso ao Registro de contêiner

Você deve primeiro concluir e enviar o [formulário de solicitação de contêineres de fala dos serviços cognitivas](https://aka.ms/speechcontainerspreview/) para solicitar acesso ao contêiner. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Speech service resource](includes/create-speech-resource.md)]

[!INCLUDE [Create a Speech service container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
