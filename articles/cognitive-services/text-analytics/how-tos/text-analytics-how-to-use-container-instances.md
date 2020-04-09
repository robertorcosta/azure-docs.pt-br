---
title: Executar instâncias de contêiner do Azure - Análise de texto
titleSuffix: Azure Cognitive Services
description: Implante os recipientes de análise de texto na Instância de Contêiner do Azure e teste-os em um navegador da Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: e4b61c6fe2f62745d0f5268221cbb5c84803eb10
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876383"
---
# <a name="deploy-a-text-analytics-container-to-azure-container-instances"></a>Implantar um contêiner de análise de texto para instâncias de contêiner do Azure

Saiba como implantar o contêiner Cognitive Services [Text Analytics][install-and-run-containers] no Azure [Container Instances][container-instances]. Este procedimento exemplifica a criação de um recurso de Text Analytics, a criação de uma imagem de Análise de Sentimento associada e a capacidade de exercer essa orquestração dos dois a partir de um navegador. O uso de contêineres pode desviar a atenção dos desenvolvedores do gerenciamento da infra-estrutura para, em vez disso, focar no desenvolvimento de aplicativos.

## <a name="prerequisites"></a>Pré-requisitos

* Use uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](../../containers/includes/create-container-instances-resource.md)]

#### <a name="key-phrase-extraction"></a>[Extração de Frases-Chave](#tab/keyphrase)

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detection"></a>[Detecção de Idioma](#tab/language)

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysis"></a>[Análise de Sentimentos](#tab/sentiment)

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

***

## <a name="next-steps"></a>Próximas etapas 

* Use mais [Contêineres de Serviços Cognitivos](../../cognitive-services-container-support.md)
* Use o [serviço conectado ao text analytics](../vs-text-connected-service.md)

[install-and-run-containers]: ./text-analytics-how-to-install-containers.md
[container-instances]: https://docs.microsoft.com/azure/container-instances