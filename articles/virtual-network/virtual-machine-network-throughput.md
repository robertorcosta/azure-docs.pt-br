---
title: Taxa de transferência de rede da máquina virtual do Azure | Microsoft Docs
description: Saiba mais sobre a taxa de transferência de rede da máquina virtual do Azure.
services: virtual-network
documentationcenter: na
author: steveesp
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/26/2019
ms.author: steveesp
ms.reviewer: kumud, mareat
ms.openlocfilehash: 47f58b25b082784177910d14ab95d8d242fda71a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245428"
---
# <a name="virtual-machine-network-bandwidth"></a>Largura de banda de rede da máquina virtual

O Azure oferece uma variedade de tamanhos e tipos de VM, cada um com uma combinação diferente de funcionalidades de desempenho. Uma funcionalidade é a taxa de transferência (ou largura de banda) de rede, medida em megabits por segundo (Mbps). Como as máquinas virtuais são hospedadas em um hardware compartilhado, a capacidade de rede deve ser compartilhada adequadamente entre as máquinas virtuais que compartilham o mesmo hardware. Máquinas virtuais maiores recebem relativamente mais largura de banda que máquinas virtuais menores.
 
A largura de banda de rede alocada a cada máquina virtual é limitada no tráfego de saída (saída) da máquina virtual. Todo o tráfego de rede que deixa a máquina virtual é contado para o limite alocado, independentemente do destino. Por exemplo, se uma máquina virtual tem um limite de 1.000 Mbps, esse limite se aplica se o tráfego de saída é destinado a outra máquina virtual na mesma rede virtual ou fora do Azure.
 
A entrada não é limitada diretamente. No entanto, há outros fatores, como limites de CPU e armazenamento, que podem afetar a capacidade de uma máquina virtual de processar dados de entrada.

Rede acelerada é um recurso projetado para melhorar o desempenho de rede, incluindo latência, taxa de transferência e utilização da CPU. Embora a rede acelerada possa melhorar a taxa de transferência de uma máquina virtual, ela poderá fazer isso somente até a largura de banda alocada da máquina virtual. Para saber mais sobre a Rede Acelerada, consulte Rede acelerada para máquinas virtuais do [Windows](create-vm-accelerated-networking-powershell.md) ou do [Linux](create-vm-accelerated-networking-cli.md).
 
As máquinas virtuais do Azure devem ter um, mas podem ter vários adaptadores de rede anexados a elas. A largura de banda alocada a uma máquina virtual é a soma de todo o tráfego de saída em todos os adaptadores de rede anexados a uma máquina virtual. Em outras palavras, a largura de banda alocada é por máquina virtual, independentemente de quantos adaptadores de rede estão anexados à máquina virtual. Para saber para quantos adaptadores de rede diferentes tamanhos de VM do Azure dão suporte, consulte Tamanhos de VM [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Azure. 

## <a name="expected-network-throughput"></a>Taxa de transferência de rede esperada

A taxa de transferência de saída esperada e o número de adaptadores de rede com suporte em cada tamanho de VM são detalhados em Tamanhos de VM [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Azure. Selecione um tipo, como Uso geral e, em seguida, uma série de tamanho na página resultante, como a série Dv2. Cada série tem uma tabela com as especificações de rede na última coluna intitulada **Máx. de NICs/Desempenho de rede esperado (Mbps)**. 

O limite de taxa de transferência se aplica à máquina virtual. A taxa de transferência não é afetada pelos seguintes fatores:
- **Número de adaptadores de rede**: o limite de largura de banda é cumulativo de todo o tráfego de saída da máquina virtual.
- **Rede acelerada**: embora o recurso possa ser útil para alcançar o limite publicado, ele não altera o limite.
- **Destino do tráfego**: todos os destinos contam para o limite de saída.
- **Protocolo**: todo o tráfego de saída em todos os protocolos contam para o limite.

## <a name="network-flow-limits"></a>Limites de fluxo de rede

Além da largura de banda, o número de conexões de rede presentes em uma VM a qualquer momento pode afetar o desempenho da rede. A pilha de rede Azure mantém o estado para cada direção de uma conexão TCP/UDP em estruturas de dados chamadas 'fluxos'. Uma conexão TCP/UDP típica terá 2 fluxos criados, um para a entrada e outro para a direção de saída. 

A transferência de dados entre pontos finais requer a criação de vários fluxos, além daqueles que realizam a transferência de dados. Alguns exemplos são fluxos criados para resolução de DNS e fluxos criados para testes de saúde balanceadores de carga. Observe também que os aparelhos virtuais de rede (NVAs), como gateways, proxies, firewalls, verão fluxos sendo criados para conexões terminadas no aparelho e originadas pelo aparelho. 

![Contagem de fluxo para conversa tcp através de um aparelho de encaminhamento](media/virtual-machine-network-throughput/flow-count-through-network-virtual-appliance.png)

## <a name="flow-limits-and-recommendations"></a>Limites de fluxo e recomendações

Hoje, a pilha de rede Do Zure suporta fluxos de rede totais de 250K com bom desempenho para VMs com mais de 8 núcleos de CPU e fluxos totais de 100k com bom desempenho para VMs com menos de 8 núcleos de CPU. Além desse limite, o desempenho da rede se degrada graciosamente para fluxos adicionais até um limite rígido de fluxos totais de 500K, entrada de 250K e saída de 250K, após os quais os fluxos adicionais são descartados.

||VMs com <8 núcleos de CPU|VMs com mais de 8 núcleos de CPU|
|---|---|---|
|<b>Bom Desempenho</b>|Fluxos de 100K |Fluxos de 250K|
|<b>Desempenho degradado</b>|Fluxos acima de 100k|Acima de 250 k fluxos|
|<b>Limite de Fluxo</b>|Fluxos de 500K|Fluxos de 500K|

As métricas estão disponíveis no [Azure Monitor](../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines) para rastrear o número de fluxos de rede e a taxa de criação de fluxo em suas instâncias VM ou VMSS.

![azure-monitor-flow-metrics.png](media/virtual-machine-network-throughput/azure-monitor-flow-metrics.png)

As taxas de estabelecimento e terminação de conexão também podem afetar o desempenho da rede à medida que o estabelecimento de conexão e o término compartilham a CPU com rotinas de processamento de pacotes. Recomendamos que você faça benchmark de cargas de trabalho em relação aos padrões de tráfego esperados e dimensione as cargas de trabalho adequadamente para corresponder às suas necessidades de desempenho. 

## <a name="next-steps"></a>Próximas etapas

- [Otimizar a taxa de transferência de rede para um sistema operacional da máquina virtual](virtual-network-optimize-network-bandwidth.md)
- [Testar a taxa de transferência de rede](virtual-network-bandwidth-testing.md) de uma máquina virtual.
