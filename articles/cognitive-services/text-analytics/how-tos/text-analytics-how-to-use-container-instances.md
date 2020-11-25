---
title: Executar instâncias de contêiner do Azure-Análise de Texto
titleSuffix: Azure Cognitive Services
description: Implante os contêineres de análise de texto na instância de contêiner do Azure e teste-os em um navegador da Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: be43d04672dcefe368eb4052b4d1a929e25327ab
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009839"
---
# <a name="deploy-a-text-analytics-container-to-azure-container-instances"></a>Implantar um contêiner de Análise de Texto nas instâncias de contêiner do Azure

Saiba como implantar os serviços cognitivas [análise de texto][install-and-run-containers] contêiner nas [instâncias de contêiner][container-instances]do Azure. Esse procedimento exemplifica a criação de um recurso de Análise de Texto, a criação de uma imagem de Análise de Sentimento associada e a capacidade de exercitar essa orquestração dos dois em um navegador. O uso de contêineres pode mudar a atenção dos desenvolvedores para longe de gerenciar a infraestrutura para se concentrar no desenvolvimento de aplicativos.

## <a name="prerequisites"></a>Pré-requisitos

* Use uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services) antes de começar.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](../../containers/includes/create-container-instances-resource.md)]

#### <a name="key-phrase-extraction"></a>[Extração de Frases-chave](#tab/keyphrase)

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detection"></a>[Detecção de Idioma](#tab/language)

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysis"></a>[Análise de Sentimento](#tab/sentiment)

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

#### <a name="text-analytics-for-health"></a>[Análise de Texto para integridade](#tab/health)

[!INCLUDE [Verify the health container instance](../includes/verify-health-container.md)]

***

## <a name="next-steps"></a>Próximas etapas 

* Use mais [Contêineres de Serviços Cognitivos](../../cognitive-services-container-support.md)
* Usar o [serviço conectado do análise de texto](../index.yml)

[install-and-run-containers]: ./text-analytics-how-to-install-containers.md
[container-instances]: https://docs.microsoft.com/azure/container-instances