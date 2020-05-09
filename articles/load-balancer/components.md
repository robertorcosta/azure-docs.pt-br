---
title: Componentes do Azure Load Balancer
description: Visão geral dos componentes do Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2020
ms.author: allensu
ms.openlocfilehash: 4a84c43b57ec4f632a2bfabb10d112e4975249bf
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/03/2020
ms.locfileid: "82733100"
---
# <a name="azure-load-balancer-components"></a>Componentes do Azure Load Balancer

O Azure Load Balancer contém vários componentes importantes para sua operação. Esses componentes podem ser configurados em sua assinatura por meio do portal do Azure, da CLI do Azure, do Azure PowerShell ou de modelos.

## <a name="frontend-ip-configurations"></a>Configuração do IP de front-end

o endereço IP do Load Balancer. Trata-se do ponto de contato para os clientes. Esses endereços podem ser:

- **Endereço IP público**
- **Endereço IP privado**

A seleção do endereço IP determina o **tipo** do balanceador de carga criado. A seleção de endereço IP privado cria um balanceador de carga interno. A seleção de endereço IP público cria um balanceador de carga público.

|  | Balanceador de carga público  | Balanceador de Carga Interno |
| ---------- | ---------- | ---------- |
| Configuração do IP front-end| Endereço IP público | Endereço IP privado|
| Descrição | O balanceador de carga público mapeia o IP público e a porta do tráfego de entrada para o IP privado e a porta da VM. O balanceador de carga mapeia o tráfego ao contrário para o tráfego de resposta da VM. Você pode distribuir tipos específicos de tráfego entre vários serviços ou VMs aplicando regras de balanceamento de carga. Por exemplo, você pode difundir a carga de tráfego de solicitação da web em vários servidores web.| Um balanceador de carga interno distribui o tráfego para recursos que estão dentro de uma rede virtual. O Azure restringe o acesso aos endereços IP de front-end de uma rede virtual com balanceamento de carga. Os endereços IP de front-end e as redes virtuais nunca são expostos diretamente a um ponto de extremidade da Internet. Os aplicativos de linha de negócios interno são executados no Azure e acessados de dentro do Azure ou de recursos locais. |
| SKUs compatíveis | Básico, Standard | Básico, Standard |

![Exemplo de balanceador de carga em camadas](./media/load-balancer-overview/load-balancer.png)

## <a name="backend-pool"></a>Pool de back-end

O grupo de máquinas virtuais ou instâncias em um conjunto de dimensionamento de máquinas virtuais que está atendendo à solicitação de entrada. Para dimensionar de maneira econômica para atender a grandes volumes de tráfego de entrada, as diretrizes de computação geralmente recomendam adicionar mais instâncias ao pool de back-end. 

O Load Balancer reconfigura-se instantaneamente por meio da reconfiguração automática quando você escala ou reduz as instâncias verticalmente. Adicionar ou remover VMs do pool de back-end reconfigura o balanceador de carga sem operações adicionais. O escopo do pool de back-end é qualquer máquina virtual na rede virtual. 

Ao considerar como projetar seu pool de back-end, projete o menor número de recursos do pool de back-end individuais para otimizar a duração de operações de gerenciamento. Não há nenhuma diferença no desempenho do plano de dados ou escala.

## <a name="health-probes"></a>Investigações de integridade

uma investigação de integridade é usada para determinar a integridade das instâncias no pool de back-end. Você pode definir o limite não íntegro para suas investigações de integridade. Quando uma investigação não responde, o balanceador de carga interrompe o envio de novas conexões para as instâncias não íntegras. Uma falha de investigação não afeta as conexões existentes. A conexão continua até o aplicativo:

- encerrar o fluxo
- o tempo limite de ociosidade ser atingido
- a VM ser desligada

O Load Balancer fornece diferentes tipos de investigação de integridade para os pontos de extremidade:

- TCP
- HTTP
- HTTPS

O balanceador de carga básico não é compatível com investigações HTTPS. O balanceador de carga básico fecha todas as conexões TCP (incluindo as conexões estabelecidas).

## <a name="load-balancing-rules"></a>Regras de balanceamento de carga

As regras de balanceamento de carga informam ao balanceador de carga o que fazer. Uma regra de balanceamento de carga mapeia uma determinada configuração de porta e IP de front-end para várias portas e endereços IP de back-end.

## <a name="inbound-nat-rules"></a>Regras NAT de entrada

As regras NAT de entrada encaminham o tráfego do endereço IP de front-end para uma instância de back-end dentro da rede virtual. O encaminhamento de porta é realizado pela mesma distribuição baseada em hash que o balanceamento de carga. 

Exemplo de uso são sessões protocolo RDP ou SSH (Secure Shell) para separar instâncias de VM dentro de uma rede virtual. É possível mapear vários pontos de extremidade internos para portas no mesmo endereço IP de front-end. Você pode usar os endereços IP de front-end para administrar remotamente suas VMs sem um jumpbox adicional.

## <a name="outbound-rules"></a>Regras de saída

uma regra de saída configura a NAT (conversão de endereços de rede) de saída para todas as máquinas virtuais identificadas pelo pool de back-end.

O balanceador de carga básico não é compatível com regras de saída.
![Balanceador de carga do Azure](./media/load-balancer-overview/load-balancer-overview.png)

## <a name="next-steps"></a>Próximas etapas

- Confira [Criar um Standard Load Balancer público](quickstart-load-balancer-standard-public-portal.md) para começar a usar um Load Balancer.
- Saiba mais sobre o [Azure Load Balancer](load-balancer-overview.md).
- Saiba mais sobre [Endereço IP público](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)
- Saiba mais sobre [Endereço IP privado](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)
- Saiba mais sobre usar o [Standard Load Balancer e Zonas de Disponibilidade](load-balancer-standard-availability-zones.md).
- Saiba mais sobre o [Diagnóstico do Load Balancer Standard](load-balancer-standard-diagnostics.md).
- Saiba mais sobre a [Redefinição de TCP quando ocioso](load-balancer-tcp-reset.md).
- Saiba mais sobre [o Standard Load Balancer com as regras de balanceamento das Portas de Alta Disponibilidade](load-balancer-ha-ports-overview.md).
- Saiba mais sobre como usar o [Azure Load Balancer com vários Front-ends](load-balancer-multivip-overview.md).
- Saiba mais sobre [Grupos de Segurança de Rede](../virtual-network/security-overview.md).
- Saiba mais sobre [Tipos de Investigação](load-balancer-custom-probe-overview.md#types).
- Saiba mais sobre os [Limites do balanceador de carga](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer).
- Aprenda a usar [Encaminhamento de porta](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal).
- Saiba mais sobre as [Regras de saída do balanceador de carga](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview).
