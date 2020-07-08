---
title: Análise de vídeo ao vivo em notas de versão IoT Edge – Azure
description: Este tópico fornece notas de versão de análise de vídeo ao vivo sobre versões IoT Edge, melhorias, correções de bugs e problemas conhecidos.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: fd607ee027b46cf386180338440e6530da911302
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84260319"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>Análise de vídeo ao vivo em notas de versão IoT Edge

>Seja notificado sobre quando revisitar esta página para atualizações copiando e colando esta URL `https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us` no leitor de feed de RSS.

Este artigo fornece informações sobre:

* As versões mais recentes
* Problemas conhecidos
* Correções de bug
* Funcionalidades preteridas

## <a name="june-1-2020"></a>1º de junho de 2020

Esta versão é a primeira versão de visualização pública da análise de vídeo ao vivo no IoT Edge. A marca de versão é

```
     mcr.microsoft.com/media/live-video-analytics:1.0.0
```

### <a name="supported-functionalities"></a>Funcionalidades com suporte
* Analise fluxos de vídeo ao vivo usando os módulos de ia de sua escolha e, opcionalmente, grave vídeo no dispositivo de borda ou na nuvem
* Use em sistemas operacionais Linux AMD64 [com suporte](https://docs.microsoft.com/azure/iot-edge/support) pelo IOT Edge
* Implantar e configurar o módulo por meio do Hub IoT usando portal do Azure ou Visual Studio Code
* Gerenciar [topologias de grafo e instâncias de grafo](media-graph-concept.md#media-graph-topologies-and-instances) remotamente ou localmente por meio das seguintes chamadas de método diretas

    *   GraphTopologyGet
    *   GraphTopologySet
    *   GraphTopologyDelete
    *   GraphTopologyList
    *   GraphInstanceGet
    *   GraphInstanceSet
    *   GraphInstanceDelete
    *   GraphInstanceList


## <a name="next-steps"></a>Próximas etapas

[Visão geral](overview.md)
