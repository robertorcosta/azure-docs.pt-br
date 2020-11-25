---
title: Executar Pesquisa Visual Computacional contêiner em instâncias de contêiner do Azure
titleSuffix: Azure Cognitive Services
description: Implante o contêiner Pesquisa Visual Computacional em uma instância de contêiner do Azure e teste-o em um navegador da Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: b734f54f39b0f442f398a60ad25a846b0f9a6322
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "96021802"
---
# <a name="deploy-the-computer-vision-container-to-azure-container-instances"></a>Implantar o contêiner de Pesquisa Visual Computacional em instâncias de contêiner do Azure

Saiba como implantar os serviços cognitivas [Pesquisa Visual computacional](computer-vision-how-to-install-containers.md) contêiner nas [instâncias de contêiner](../../container-instances/index.yml)do Azure. Este procedimento demonstra a criação do recurso de Pesquisa Visual Computacional. Em seguida, discutimos a extração da imagem de contêiner associada. Por fim, destacamos a capacidade de exercitar a orquestração dos dois em um navegador. O uso de contêineres pode mudar a atenção dos desenvolvedores para longe de gerenciar a infraestrutura para se concentrar no desenvolvimento de aplicativos.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Solicitar acesso ao registro de contêiner privado

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Create a Cognitive Services Computer Vision resource](includes/create-computer-vision-resource.md)]

[!INCLUDE [Create the Computer Vision on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]