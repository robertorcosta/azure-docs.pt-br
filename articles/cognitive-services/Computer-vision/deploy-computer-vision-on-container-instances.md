---
title: Executar o contêiner Computer Vision em instâncias de contêiner do Azure
titleSuffix: Azure Cognitive Services
description: Implante o contêiner Computer Vision em uma instância de contêiner do Azure e teste-o em um navegador da Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: ecb0b6a6577161a5349d9d701dd6c116f69411f0
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876060"
---
# <a name="deploy-the-computer-vision-container-to-azure-container-instances"></a>Implantar o contêiner Visão de Computador em Instâncias de Contêiner do Azure

Saiba como implantar o contêiner Cognitive Services [Computer Vision](computer-vision-how-to-install-containers.md) em Instâncias [de Contêiner](https://docs.microsoft.com/azure/container-instances/)do Azure . Este procedimento demonstra a criação do recurso Visão computacional. Em seguida, discutimos puxar a imagem do contêiner associado. Finalmente, destacamos a capacidade de exercitar a orquestração dos dois a partir de um navegador. O uso de contêineres pode desviar a atenção dos desenvolvedores do gerenciamento da infra-estrutura para, em vez disso, focar no desenvolvimento de aplicativos.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Solicitar acesso ao registro de contêiner privado

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Create a Cognitive Services Computer Vision resource](includes/create-computer-vision-resource.md)]

[!INCLUDE [Create the Computer Vision on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]