---
title: Análise de vídeo ao vivo em notas de versão IoT Edge – Azure
description: Este tópico fornece notas de versão de análise de vídeo ao vivo sobre versões IoT Edge, melhorias, correções de bugs e problemas conhecidos.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 28260728532d9db52b8d36488c2e456bd11803ea
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87091771"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>Análise de vídeo ao vivo em notas de versão IoT Edge

>Seja notificado sobre quando revisitar esta página para atualizações copiando e colando esta URL `https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us` no leitor de feed de RSS.

Este artigo fornece informações sobre:

* As versões mais recentes
* Problemas conhecidos
* Correções de bug
* Funcionalidades preteridas

## <a name="july-13-2020"></a>13 de julho de 2020

Esta marca de versão para a atualização de julho de 2020 do módulo é:

```
     mcr.microsoft.com/media/live-video-analytics:1.0.2
```

> [!NOTE]
> Nos guias de início rápido e tutoriais, os manifestos de implantação usam uma marca de 1 (Live-Video-Analytics: 1). Portanto, simplesmente reimplantar esses manifestos deve atualizar o módulo em seus dispositivos de > de borda.

### <a name="new-features"></a>Novos recursos
* Agora você pode criar topologias de grafo que têm um nó de coletor de ativos, bem como um nó de coletor de arquivos downstream de um nó de processador de porta de sinal. Consulte [isso](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-motion-assets-files) para obter um exemplo.

### <a name="bug-fixes"></a>Correções de bug
* Melhorias na validação das propriedades desejadas

## <a name="june-1-2020"></a>1º de junho de 2020

Esta versão é a primeira versão de visualização pública da análise de vídeo ao vivo no IoT Edge. A marca de versão é

```
     mcr.microsoft.com/media/live-video-analytics:1.0.0
```

### <a name="supported-functionalities"></a>Funcionalidades com suporte
* Analise fluxos de vídeo ao vivo usando os módulos de ia de sua escolha e, opcionalmente, grave vídeo no dispositivo de borda ou na nuvem
* Use em sistemas operacionais Linux AMD64 [com suporte](../../iot-edge/support.md) pelo IOT Edge
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
