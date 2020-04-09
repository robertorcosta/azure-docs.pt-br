---
title: Executar o contêiner Face em instâncias de contêiner do Azure
titleSuffix: Azure Cognitive Services
description: Implante o recipiente Face em uma instância de contêiner do Azure e teste-o em um navegador da Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 4df5949e24e3fa59d37379c058a777c93dda2c44
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878350"
---
# <a name="deploy-the-face-container-to-azure-container-instances"></a>Implantar o recipiente Face para instâncias de contêiner do Azure

Saiba como implantar o contêiner [Face](../face-how-to-install-containers.md) serviços cognitivos em [instâncias de contêineres do](https://docs.microsoft.com/azure/container-instances/)Azure . Este procedimento demonstra a criação de um recurso Azure Face. Em seguida, discutimos puxar a imagem do contêiner associado. Finalmente, destacamos a capacidade de exercitar a orquestração dos dois a partir de um navegador. O uso de contêineres pode desviar a atenção dos desenvolvedores do gerenciamento da infra-estrutura para, em vez disso, focar no desenvolvimento de aplicativos.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Solicitar acesso ao registro de contêiner privado

[!INCLUDE [Request access to private container registry](../../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Create a Cognitive Services Face resource](../includes/create-face-resource.md)]

[!INCLUDE [Create an Face container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../../containers/includes/containers-next-steps.md)]