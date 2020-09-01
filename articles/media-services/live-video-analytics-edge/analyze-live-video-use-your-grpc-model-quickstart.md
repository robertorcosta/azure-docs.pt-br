---
title: Análise Dinâmica de Vídeo usando o próprio modelo gRPC – Azure
description: Neste guia de início rápido, você aplicará a pesquisa visual computacional para analisar o feed de vídeo ao vivo de uma câmera IP (simulada).
ms.topic: quickstart
ms.date: 08/14/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: ac11ced68ab8463da26b9978a5b0b02c4cd1a402
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88687102"
---
# <a name="quickstart-analyze-live-video-by-using-your-own-grpc-model"></a>Início Rápido: Análise Dinâmica de Vídeo usando o próprio modelo gRPC

Este início rápido mostra como usar a Análise Dinâmica de Vídeo no IoT Edge para analisar um feed de vídeo ao vivo de uma câmera IP (simulada). Você verá como aplicar um modelo pesquisa visual computacional para detectar objetos. Um subconjunto dos quadros no feed de vídeo ao vivo é enviado para um serviço de inferência. Os resultados são enviados ao Hub do IoT Edge.

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/analyze-live-video-your-grpc-model-quickstart/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/analyze-live-video-your-grpc-model-quickstart/python/header.md)]
::: zone-end

## <a name="prerequisites"></a>Pré-requisitos

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/analyze-live-video-your-grpc-model-quickstart/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/analyze-live-video-your-grpc-model-quickstart/python/prerequisites.md)]
::: zone-end

## <a name="review-the-sample-video"></a>Examinar o vídeo de exemplo

::: zone pivot="programming-language-csharp"
[!INCLUDE [review sample video](includes/analyze-live-video-your-grpc-model-quickstart/csharp/review-sample-video.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [review sample video](includes/analyze-live-video-your-grpc-model-quickstart/python/review-sample-video.md)]
::: zone-end

## <a name="overview"></a>Visão geral

::: zone pivot="programming-language-csharp"
[!INCLUDE [overview](includes/analyze-live-video-your-grpc-model-quickstart/csharp/overview.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [overview](includes/analyze-live-video-your-grpc-model-quickstart/python/overview.md)]
::: zone-end

## <a name="create-and-deploy-the-media-graph"></a>Criar e implantar o grafo de mídia

::: zone pivot="programming-language-csharp"
[!INCLUDE [create and deply the media graph](includes/analyze-live-video-your-grpc-model-quickstart/csharp/create-deploy-media-graph.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [create and deply the media graph](includes/analyze-live-video-your-grpc-model-quickstart/python/create-deploy-media-graph.md)]
::: zone-end

## <a name="interpret-results"></a>Interpretar os resultados

::: zone pivot="programming-language-csharp"
[!INCLUDE [interpret results](includes/analyze-live-video-your-grpc-model-quickstart/csharp/interpret-results.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [interpret results](includes/analyze-live-video-your-grpc-model-quickstart/python/interpret-results.md)]
::: zone-end

## <a name="clean-up-resources"></a>Limpar os recursos

Se você pretende experimentar outros inícios rápidos, mantenha os recursos criados. Caso contrário, vá até o portal do Azure, navegue até seus grupos de recursos, selecione o grupo de recursos no qual você executou este início rápido e exclua todos os recursos.

## <a name="next-steps"></a>Próximas etapas

Tente executar diferentes topologias de grafo de mídia usando o protocolo gRPC.
