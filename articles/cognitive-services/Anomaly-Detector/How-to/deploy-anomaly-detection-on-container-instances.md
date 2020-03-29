---
title: Executar o detector de anomalias em instâncias de contêiner azure
titleSuffix: Azure Cognitive Services
description: Implante o recipiente do Detector de Anomalias em uma instância de contêiner do Azure e teste-o em um navegador da Web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 2fba0a0d64502a30b6dfbc9f4f109bca65cca8b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76716355"
---
# <a name="deploy-an-anomaly-detector-container-to-azure-container-instances"></a>Implantar um recipiente detector de anomalias em instâncias de contêineres do Azure

Saiba como implantar o detector de [anomalias](../anomaly-detector-container-howto.md) de serviços cognitivos em instâncias [de contêineres](https://docs.microsoft.com/azure/container-instances/)do Azure . Este procedimento demonstra a criação de um recurso do Detector de Anomalias. Em seguida, discutimos puxar a imagem do contêiner associado. Finalmente, destacamos a capacidade de exercitar a orquestração dos dois a partir de um navegador. O uso de contêineres pode desviar a atenção dos desenvolvedores do gerenciamento da infra-estrutura para, em vez disso, focar no desenvolvimento de aplicativos.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Solicitar acesso ao registro de contêiner privado

Primeiro, você deve preencher e enviar o formulário de solicitação de [contêiner do detector de anomalias](https://aka.ms/adcontainer) para solicitar acesso ao contêiner.

[!INCLUDE [Request access](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Próximas etapas

* Revisar [Instalar e executar recipientes](../anomaly-detector-container-configuration.md) para puxar a imagem do recipiente e executar o recipiente
* Revise [Configurar contêineres](../anomaly-detector-container-configuration.md) para configurações
* [Saiba mais sobre o serviço de API do Detector de Anomalias](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
