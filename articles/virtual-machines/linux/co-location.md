---
title: Colocalizar VMs do Linux
description: Saiba como a colocalização de recursos de VM do Azure para Linux pode melhorar a latência.
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: 304623ca50fd030ab6e016b940f8be52819c161a
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500624"
---
# <a name="co-locate-resources-for-improved-latency"></a>Colocalizar recursos para maior latência

Ao implantar seu aplicativo no Azure, a difusão de instâncias entre regiões ou zonas de disponibilidade cria a latência de rede, o que pode afetar o desempenho geral do seu aplicativo. 

## <a name="proximity-placement-groups"></a>Grupos de posicionamento de proximidade

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Próximas etapas

Implante uma VM em um [grupo de posicionamento de proximidade](proximity-placement-groups.md) usando o CLI do Azure.

Saiba como [testar a latência de rede](../../virtual-network/virtual-network-test-latency.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Saiba como [otimizar a taxa de transferência de rede](../../virtual-network/virtual-network-optimize-network-bandwidth.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

Saiba como [usar grupos de posicionamento de proximidade com aplicativos SAP](../workloads/sap/sap-proximity-placement-scenarios.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).