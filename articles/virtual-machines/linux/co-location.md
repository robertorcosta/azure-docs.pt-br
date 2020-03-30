---
title: Co-localizar VMs Linux
description: Saiba como a co-localização dos recursos do Azure VM pode melhorar a latência.
ms.service: virtual-machines
ms.topic: article
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: d2fd8a2cd7dac7b1d3c78691c84a861d924005ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250277"
---
# <a name="co-locate-resources-for-improved-latency"></a>Co-localizar recursos para melhor latência

Ao implantar seu aplicativo no Azure, espalhar instâncias entre regiões ou zonas de disponibilidade cria latência de rede, o que pode afetar o desempenho geral do seu aplicativo. 

## <a name="proximity-placement-groups"></a>Grupos de posicionamento de proximidade

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Próximas etapas

Implante uma VM para um [grupo de colocação de proximidade](proximity-placement-groups.md) usando o Cli do Azure.

Aprenda a testar a [latência da rede](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Saiba como otimizar o [throughput da rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-optimize-network-bandwidth?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

Saiba como [usar grupos de colocação de proximidade com aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
