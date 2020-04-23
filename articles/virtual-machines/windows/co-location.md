---
title: Co-localizar VMs para melhor latência
description: Saiba como a co-localização dos recursos do Azure VM pode melhorar a latência.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: 066b6d10dbe07b902abfd15565c5ccf1a5e9c115
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82083165"
---
# <a name="co-locate-resource-for-improved-latency"></a>Co-localizar recurso para melhor latência

Ao implantar seu aplicativo no Azure, espalhar instâncias entre regiões ou zonas de disponibilidade cria latência de rede, o que pode afetar o desempenho geral do seu aplicativo. 


## <a name="proximity-placement-groups"></a>Grupos de posicionamento de proximidade 

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Próximas etapas

Implante uma VM para um [grupo de colocação de proximidade](proximity-placement-groups.md) usando o Azure PowerShell.

Aprenda a testar a [latência da rede](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Saiba como otimizar o [throughput da rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-optimize-network-bandwidth?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  

Saiba como [usar grupos de colocação de proximidade com aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).