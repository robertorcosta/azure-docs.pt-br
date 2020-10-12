---
title: Executar contêiner de detector de anomalias em instâncias de contêiner do Azure
titleSuffix: Azure Cognitive Services
description: Implante o contêiner do detector de anomalias em uma instância de contêiner do Azure e teste-o em um navegador da Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 975efa9fdaff9175317794d8b6df7afbfc73cba8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "83585668"
---
# <a name="deploy-an-anomaly-detector-container-to-azure-container-instances"></a>Implantar um contêiner de detector de anomalias nas instâncias de contêiner do Azure

Saiba como implantar o contêiner do [detector de anomalias](../anomaly-detector-container-howto.md) dos serviços cognitivas nas [instâncias de contêiner](https://docs.microsoft.com/azure/container-instances/)do Azure. Este procedimento demonstra a criação de um recurso de detector de anomalias. Em seguida, discutimos a extração da imagem de contêiner associada. Por fim, destacamos a capacidade de exercitar a orquestração dos dois em um navegador. O uso de contêineres pode mudar a atenção dos desenvolvedores para longe de gerenciar a infraestrutura para se concentrar no desenvolvimento de aplicativos.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Próximas etapas

* Examine [instalar e executar contêineres](../anomaly-detector-container-configuration.md) para extrair a imagem de contêiner e executar o contêiner
* Revise [Configurar contêineres](../anomaly-detector-container-configuration.md) para configurações
* [Saiba mais sobre o serviço de API do detector de anomalias](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
