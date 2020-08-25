---
title: Análise de vídeo ao vivo em notas de versão IoT Edge – Azure
description: Este tópico fornece notas de versão de análise de vídeo ao vivo sobre versões IoT Edge, melhorias, correções de bugs e problemas conhecidos.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: bad21297c5174c107a8eb03da0e2ed57ee7355d3
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750204"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>Análise de vídeo ao vivo em notas de versão IoT Edge

>Seja notificado sobre quando revisitar esta página para atualizações copiando e colando esta URL `https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us` no leitor de feed de RSS.

Este artigo fornece informações sobre:

* As versões mais recentes
* Problemas conhecidos
* Correções de bug
* Funcionalidades preteridas

## <a name="august-19-2020"></a>19 de agosto de 2020

Esta marca de versão para a atualização de agosto de 2020 do módulo é:

```
mcr.microsoft.com/media/live-video-analytics:1.0.3
```

> [!NOTE]
> Nos guias de início rápido e tutoriais, os manifestos de implantação usam uma marca de 1 (Live-Video-Analytics: 1). Portanto, simplesmente reimplantar esses manifestos deve atualizar o módulo em seus dispositivos de > de borda.

## <a name="new-features"></a>Novos recursos 

* Agora você pode obter alto desempenho de transferência de conteúdo de dados entre a análise de vídeo ao vivo no IoT Edge e sua extensão personalizada usando a estrutura gRPC. Consulte [isso](analyze-live-video-use-your-grpc-model-quickstart.md) para começar.
* Implantação regional mais ampla da análise de vídeo ao vivo e apenas o serviço de nuvem foi atualizado.  
* A análise de vídeo ao vivo agora está disponível em 25 regiões adicionais em todo o mundo. Aqui está a [lista](https://azure.microsoft.com/global-infrastructure/services/?products=media-services) de todas as regiões disponíveis.  
* A [configuração](https://aka.ms/lva-edge/setup-resources-for-samples) para início rápido foi atualizada também com novas regiões com suporte.
    * Não há nenhuma chamada para ação para qualquer pessoa que já tenha os recursos configurados

## <a name="bug-fixes"></a>Correções de bug 

* Remover o uso de uma extensão do Azure preterida no script de configuração

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
