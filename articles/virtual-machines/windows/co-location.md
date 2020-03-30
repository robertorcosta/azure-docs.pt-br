---
title: Co-localizar VMs do Windows Azure
description: Saiba como a co-localização dos recursos do Azure VM pode melhorar a latência.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: b61a84f2b284177b7443be00efeea65e7eb8aaef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266787"
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