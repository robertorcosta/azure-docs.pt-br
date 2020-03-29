---
title: Executar serviço Azure Kubernetes - Análise de texto
titleSuffix: Azure Cognitive Services
description: Implante a imagem do contêiner Text Analytics no Azure Kubernetes Service e teste-a em um navegador da Web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 3264ec5a83277e6bb4befad46cd1337175e911c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74383461"
---
# <a name="deploy-a-text-analytics-container-to-azure-kubernetes-service"></a>Implantar um contêiner Text Analytics no Azure Kubernetes Service

Saiba como implantar a imagem de contêiner Azure Cognitive Services [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) no Azure Kubernetes Service (AKS). Este procedimento mostra como criar um recurso de Text Analytics, como criar uma imagem de análise de sentimento associada e como exercitar essa orquestração dos dois a partir de um navegador. O uso de contêineres pode desviar sua atenção do gerenciamento da infra-estrutura para, em vez disso, focar no desenvolvimento de aplicativos.

## <a name="prerequisites"></a>Pré-requisitos

Este procedimento requer várias ferramentas que devem ser instaladas e executadas localmente. Não use a Azure Cloud Shell. Você precisará do seguinte:

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Um editor de texto, por exemplo, [Visual Studio Code](https://code.visualstudio.com/download).
* O [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) instalado.
* O [Kubernetes CLI](https://kubernetes.io/docs/tasks/tools/install-kubectl/) instalado.
* Um recurso do Azure com o tipo de preço correto. Nem todos os tipos de preços funcionam com esse contêiner:
    * **Recurso do Azure Text Analytics** apenas com níveis f0 ou de preços padrão.
    * **Recurso do Azure Cognitive Services** com a camada de preços S0.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics container on Azure Kubernetes Service (AKS)](../../containers/includes/create-aks-resource.md)]

#### <a name="key-phrase-extraction"></a>[Extração de Frases-chave](#tab/keyphrase)

[!INCLUDE [Key Phrase Extraction Kubernetes config and deploy steps](../includes/key-phrase-extraction-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detection"></a>[Detecção de Idioma](#tab/language)

[!INCLUDE [Language Detection Kubernetes config and deploy steps](../includes/language-detection-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysis"></a>[Análise de Sentimentos](#tab/sentiment)

[!INCLUDE [Sentiment Analysis Kubernetes config and deploy steps](../includes/sentiment-analysis-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

***

## <a name="next-steps"></a>Próximas etapas

* Use mais [contêineres de Serviços Cognitivos](../../cognitive-services-container-support.md)
* Use o [serviço conectado ao text analytics](../vs-text-connected-service.md)
