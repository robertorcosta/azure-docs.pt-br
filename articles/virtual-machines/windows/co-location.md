---
title: Colocalizar VMs para melhorar a latência
description: Saiba como a colocalização de recursos de VM do Azure pode melhorar a latência.
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: 9c981e9b829fa10f094370e1e1ffd8b6df967d4b
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91965770"
---
# <a name="co-locate-resource-for-improved-latency"></a>Co-localizar recurso para maior latência

Ao implantar seu aplicativo no Azure, a difusão de instâncias entre regiões ou zonas de disponibilidade cria a latência de rede, o que pode afetar o desempenho geral do seu aplicativo. 


## <a name="proximity-placement-groups"></a>Grupos de posicionamento de proximidade 

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Próximas etapas

Implante uma VM em um [grupo de posicionamento de proximidade](proximity-placement-groups.md) usando Azure PowerShell.

Saiba como [testar a latência de rede](../../virtual-network/virtual-network-test-latency.md?toc=%252fazure%252fvirtual-machines%252fwindows%252ftoc.json).

Saiba como [otimizar a taxa de transferência de rede](../../virtual-network/virtual-network-optimize-network-bandwidth.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  

Saiba como [usar grupos de posicionamento de proximidade com aplicativos SAP](../workloads/sap/sap-proximity-placement-scenarios.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).