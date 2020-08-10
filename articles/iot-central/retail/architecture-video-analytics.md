---
title: Detecção de animação e objeto de análise de vídeo do Azure IoT Central | Microsoft Docs
description: Aprenda a criar um aplicativo IoT Central usando o modelo de aplicativo análise de vídeo-objeto e detecção de movimento no IoT Central. Este modelo usa a análise de vídeo ao vivo e câmeras conectadas.
author: KishorIoT
ms.author: nandab
ms.date: 07/27/2020
ms.topic: conceptual
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
ms.openlocfilehash: 087c5e5716a4c8c640faebd7470905fde85d0136
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88038166"
---
# <a name="video-analytics---object-and-motion-detection-application-architecture"></a>Análise de vídeo – arquitetura de aplicativo de detecção de objetos e de movimento

O modelo de aplicativo **análise de vídeo – objeto e detecção de movimento** permite que você crie soluções de IOT, incluindo recursos de análise de vídeo ao vivo.

:::image type="content" source="media/architecture-video-analytics/architecture.png" alt-text="Arquitetura":::

Os principais componentes da solução de análise de vídeo incluem:

## <a name="live-video-analytics-lva"></a>LVA (análise de vídeo ao vivo)

O LVA fornece uma plataforma para você criar aplicativos de vídeo inteligentes que abrangem a borda e a nuvem. A plataforma permite que você crie aplicativos de vídeo inteligentes que abrangem a borda e a nuvem. A plataforma oferece a capacidade de capturar, gravar, analisar vídeos ao vivo e publicar os resultados, que podem ser análises de vídeo ou vídeo, para os serviços do Azure. Os serviços do Azure podem estar em execução na nuvem ou na borda. A plataforma pode ser usada para aprimorar soluções de IoT com análise de vídeo.

Para obter mais informações, consulte [Live Video Analytics](https://github.com/Azure/live-video-analytics) no github.

## <a name="iot-edge-lva-gateway-module"></a>Módulo de gateway IoT Edge LVA

O módulo de gateway IoT Edge LVA instancia câmeras como novos dispositivos e os conecta diretamente ao IoT Central usando o SDK do cliente do dispositivo IoT.

Nessa implementação de referência, os dispositivos se conectam à solução usando chaves simétricas da borda. Para obter mais informações sobre conectividade de dispositivo, consulte [conectar-se ao Azure IOT central](../core/concepts-get-connected.md)

## <a name="media-graph"></a>Grafo de mídia

O grafo de mídia permite definir para onde capturar a mídia, como processá-la e onde entregar os resultados. Você configura o grafo de mídia Conectando componentes, ou nós, da maneira desejada. Para obter mais informações, consulte [grafo de mídia](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph) no github.

## <a name="next-steps"></a>Próximas etapas

A próxima etapa sugerida é aprender a [criar um aplicativo de análise de vídeo no Azure IOT central](tutorial-video-analytics-create-app.md).
