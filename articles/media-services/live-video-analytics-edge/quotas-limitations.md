---
title: Análise de vídeo ao vivo em cotas de IoT Edge – Azure
description: Este artigo descreve a análise de vídeo ao vivo em IoT Edge cotas e limitações.
ms.topic: conceptual
ms.date: 05/22/2020
ms.openlocfilehash: 90141fa850c9ab3e3abbea15001249da0736ac45
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87091803"
---
# <a name="quotas-and-limitations"></a>Cotas e limitações

Este artigo enumera as cotas e limitações da análise de vídeo ao vivo no módulo IoT Edge.

## <a name="maximum-period-of-disconnected-use"></a>Período máximo de uso desconectado

O módulo de borda pode sustentar uma perda temporária de conectividade de rede. Se o módulo permanecer desconectado por mais de 36 horas, ele desativará todas as instâncias de grafo em execução e outras chamadas de método diretas serão bloqueadas.

Para retomar o módulo de borda para um estado operacional, você precisará restaurar a conectividade de rede e o módulo precisa ser capaz de se comunicar com êxito com a conta de serviço de mídia do Azure.

## <a name="maximum-number-of-graph-instances"></a>Número máximo de instâncias de grafo

Você pode ter no máximo 1000 instâncias de grafo por módulo (criadas via GraphInstanceSet).

## <a name="maximum-number-of-graph-topologies"></a>Número máximo de topologias de grafo

Você pode ter no máximo 50 topologias de grafo por módulo (criadas via GraphTopologySet).

## <a name="limitations-on-graph-topologies-at-preview"></a>Limitações em topologias de grafo na visualização

Com a versão de visualização, há limitações em nós diferentes que podem ser conectados juntos em uma topologia de grafo de mídia.

* Fonte RTSP
   * Somente uma fonte RTSP é permitida por topologia de grafo.
* Processador de filtro de taxa de quadros
   * Deve ser imediatamente downstream da fonte RTSP ou do processador de detecção de movimento.
   * Não pode ser usado downstream de um processador de extensão HTTP.
   * Não é possível fazer upstream a partir de um processador de detecção de movimento.
* Processador de extensão HTTP
   * Pode haver no máximo uma topologia de processador por grafo.
* Processador de detecção de movimento
   * Deve ser imediatamente downstream da origem RTSP.
   * Pode haver no máximo uma topologia de processador por grafo.
   * Não pode ser usado downstream de um processador de extensão HTTP.
* Processador da porta do sinal
   * Deve ser imediatamente downstream da origem RTSP.
* Coletor de ativos 
   * Deve ser imediatamente downstream da fonte RTSP ou do processador de porta de sinal.
* Coletor de arquivos
   * Deve ser imediatamente downstream do processador da porta do sinal.
   * Não pode ser o downstream imediatamente do processador de extensão HTTP ou do processador de detecção de movimento
* Coletor do Hub IoT
   * Não pode ser o downstream imediatamente de uma origem do Hub IoT.

Se os nós de processador de taxa de filtro e de detecção de movimento forem usados, eles deverão estar na mesma cadeia de nós que levam ao nó de origem RTSP.

## <a name="limitations-on-media-service-operations-at-preview"></a>Limitações nas operações do serviço de mídia na versão prévia

No momento da versão de visualização, a análise de vídeo ao vivo no IoT Edge não oferece suporte ao seguinte:

* A capacidade de migrar a conta de serviço de mídia de uma assinatura para outra sem interrupção.
* A capacidade de usar mais de uma conta de armazenamento com a conta de serviço de mídia.
* A capacidade de alterar as informações da entidade de serviço nas propriedades desejadas do módulo dinamicamente, sem uma reinicialização.

## <a name="next-steps"></a>Próximas etapas

[Visão geral](overview.md)
