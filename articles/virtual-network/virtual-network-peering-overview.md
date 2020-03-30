---
title: Emparelhamento de Rede Virtual do Azure
titlesuffix: Azure Virtual Network
description: Saiba mais sobre o emparelhamento de rede virtual no Azure.
services: virtual-network
documentationcenter: na
author: anavinahar
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2019
ms.author: anavin
ms.openlocfilehash: 5fb54e812e72b9393ffdf632085d0f32ab8b1988
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279540"
---
# <a name="virtual-network-peering"></a>Emparelhamento de rede virtual

O peering de rede virtual permite conectar perfeitamente redes na [Rede Virtual Do Azure](virtual-networks-overview.md). As redes virtuais aparecem como uma para fins de conectividade. O tráfego entre máquinas virtuais usa a infra-estrutura backbone da Microsoft. Como o tráfego entre máquinas virtuais na mesma rede, o tráfego é roteado apenas pela rede *privada* da Microsoft.

O Azure suporta os seguintes tipos de peering:

* Peering de rede virtual: conecte redes virtuais dentro da mesma região do Azure.
* Peering de rede virtual global: Conectando redes virtuais em regiões do Azure.

Os benefícios do uso do emparelhamento de rede virtual, seja local ou global, incluem:

* Baixa latência, conexão com largura de banda alta entre os recursos em redes virtuais diferentes.
* A capacidade de recursos em uma rede virtual para se comunicar com recursos em uma rede virtual diferente.
* A capacidade de transferir dados entre redes virtuais através de assinaturas do Azure, inquilinos do Azure Active Directory, modelos de implantação e regiões Azure.
* A capacidade de peer redes virtuais criadas através do Azure Resource Manager.
* A capacidade de peer de uma rede virtual criada através do Resource Manager para uma criada através do modelo clássico de implantação. Para saber mais sobre os modelos de implantação do Azure, confira [Entender os modelos de implantação do Azure](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Nenhum tempo de inatividade para recursos em qualquer rede virtual ao criar o emparelhamento ou depois que o emparelhamento é criado.

O tráfego de rede entre redes virtuais emparelhadas é particular. O tráfego entre as redes virtuais é mantido na rede de backbone da Microsoft. Não são necessários a Internet pública, os gateways ou a criptografia na comunicação entre as redes virtuais.

## <a name="connectivity"></a>Conectividade

Para redes virtuais peered, os recursos em qualquer rede virtual podem se conectar diretamente com recursos na rede virtual peered.

A latência de rede entre as máquinas virtuais em redes virtuais emparelhadas na mesma região é a mesma latência de uma única rede virtual. A taxa de transferência da rede tem base na largura de banda permitida para a máquina virtual, que é proporcional ao seu tamanho. Não existe restrição adicional quanto à largura de banda no emparelhamento.

O tráfego entre as máquinas virtuais nas redes virtuais emparelhadas é roteado diretamente pela infraestrutura de backbone da Microsoft, não por um gateway ou pela Internet pública.

Você pode aplicar grupos de segurança de rede em rede virtual para bloquear o acesso a outras redes virtuais ou sub-redes.
Ao configurar peering de rede virtual, abra ou feche as regras do grupo de segurança da rede entre as redes virtuais. Se você abrir conectividade total entre redes virtuais peered, poderá aplicar grupos de segurança de rede para bloquear ou negar acesso específico. Conectividade completa é a opção padrão. Para saber mais sobre grupos de segurança de rede, consulte [grupos de segurança](security-overview.md).

## <a name="service-chaining"></a>Encadeamento de serviços

O encadeamento de serviços permite direcionar o tráfego de uma rede virtual para um dispositivo virtual ou gateway em uma rede peered através de rotas definidas pelo usuário.

Para habilitar o encadeamento de serviços, configure rotas definidas pelo usuário que apontam para máquinas virtuais em redes virtuais peered como o próximo endereço IP *de hop.* Rotas definidas pelo usuário também podem apontar para gateways de rede virtuais para permitir o encadeamento de serviços.

Você pode implantar redes de hub-and-spoke, onde a rede virtual do hub hospeda componentes de infra-estrutura, como um dispositivo virtual de rede ou gateway VPN. Todas as redes virtuais contadas podem emparelhar com a rede virtual do hub. O tráfego flui através de dispositivos virtuais de rede ou gateways VPN na rede virtual do hub.

O emparelhamento de redes virtuais permite que o próximo salto em uma rota definida pelo usuário seja o endereço IP de uma máquina virtual na rede virtual emparelhada, ou o gateway de VPN. Você não pode fazer uma rota entre redes virtuais com uma rota definida pelo usuário que especifica um gateway Azure ExpressRoute como o próximo tipo de hop. Para saber mais sobre rotas definidas pelo usuário, consulte visão geral das [rotas definidas pelo usuário](virtual-networks-udr-overview.md#user-defined). Para saber como criar um hub e falar topologia de rede, consulte topologia de rede com fala hub [no Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="gateways-and-on-premises-connectivity"></a>Gateways e conectividade local

Cada rede virtual, incluindo uma rede virtual peered, pode ter seu próprio gateway. Uma rede virtual pode usar seu gateway para se conectar a uma rede local. Você também pode configurar [conexões de rede virtual para virtuais](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) usando gateways, mesmo para redes virtuais peered.

Quando você configura ambas as opções para interconectividade de rede virtual, o tráfego entre as redes virtuais flui através da configuração de peering. O tráfego usa a espinha dorsal do Azure.

Você também pode configurar o gateway na rede virtual peered como um ponto de trânsito para uma rede local. Neste caso, a rede virtual que está usando um gateway remoto não pode ter seu próprio gateway. Uma rede virtual tem apenas um gateway. O gateway é um gateway local ou remoto na rede virtual peered, conforme mostrado no diagrama a seguir:

![trânsito de emparelhamento de rede virtual](./media/virtual-networks-peering-overview/local-or-remote-gateway-in-peered-virual-network.png)

Tanto o peering de rede virtual quanto o gerenciamento global de gateway sustais de rede virtual.

O gateway transit entre redes virtuais criadas através de diferentes modelos de implantação é suportado. O gateway deve estar na rede virtual no modelo Gerenciador de recursos. Para saber mais sobre como usar um gateway para o trânsito, consulte [Configure um gateway VPN para trânsito em uma rede virtual .](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

Quando você peer redes virtuais que compartilham uma única conexão Azure ExpressRoute, o tráfego entre elas passa pela relação de peering. Esse tráfego usa a rede backbone do Azure. Você ainda pode usar gateways locais em cada rede virtual para se conectar ao circuito local. Caso contrário, você pode usar um gateway compartilhado e configurar o trânsito para conectividade no local.

## <a name="troubleshoot"></a>Solução de problemas

Para confirmar se as redes virtuais são verificadas, você pode verificar rotas eficazes. Verifique rotas para uma interface de rede em qualquer sub-rede em uma rede virtual. Se um emparelhamento de rede virtual existir, todas as sub-redes na rede virtual terão rotas com o tipo de próximo salto *Emparelhamento VNet*, para cada espaço de endereço em cada rede virtual emparelhada. Para obter mais informações, consulte [Diagnosticar um problema de roteamento de máquina virtual](diagnose-network-routing-problem.md).

Você também pode solucionar problemas de conectividade a uma máquina virtual em uma rede virtual peered usando o Azure Network Watcher. Uma verificação de conectividade permite ver como o tráfego é roteado da interface de rede de uma máquina virtual de origem para a interface de rede de uma máquina virtual de destino. Para obter mais informações, consulte [Conexões de solução de problemas com o Azure Network Watcher usando o portal Azure](../network-watcher/network-watcher-connectivity-portal.md#check-connectivity-to-a-virtual-machine).

Você também pode experimentar os [problemas de peering de rede virtual](virtual-network-troubleshoot-peering-issues.md)de solução de problemas .

## <a name="constraints-for-peered-virtual-networks"></a>Restrições para redes virtuais peered<a name="requirements-and-constraints"></a>

As seguintes restrições se aplicam somente quando as redes virtuais são emparelhadas globalmente:

* Os recursos em uma rede virtual não podem se comunicar com o endereço IP front-end de um ILB (Basic Internal Load Balancer, balancer de carga interna básica) em uma rede virtual com peer globalmente.
* Alguns serviços que usam um balanceador de carga básico não funcionam em rede virtual global. Para obter mais informações, consulte [Quais são as restrições relacionadas ao Global VNet Peering and Load Balancers?](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers).

Para saber mais, confira [Requisitos e restrições](virtual-network-manage-peering.md#requirements-and-constraints). Para saber mais sobre o número de peerings suportados, consulte [os limites de rede](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="permissions"></a>Permissões

Para saber sobre as permissões necessárias para criar um peering de rede virtual, consulte [Permissões](virtual-network-manage-peering.md#permissions).

## <a name="pricing"></a>Preços

Há uma taxa nominal para o tráfego de ingestão e saída que usa uma conexão de peering de rede virtual. Para obter mais informações, consulte [preços da Rede Virtual](https://azure.microsoft.com/pricing/details/virtual-network).

O Gateway Transit é uma propriedade de peering que permite que uma rede virtual utilize um gateway VPN/ExpressRoute em uma rede virtual peered. O transporte de gateway funciona tanto para locais cruzados quanto para conectividade de rede para rede. O tráfego para o gateway (entrada ou saída) na rede virtual peering de rede peering na VNet falada (ou VNet não gateway). Para obter mais informações, consulte [os preços do VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway/) para taxas de gateway VPN e os preços do ExpressRoute Gateway para taxas de gateway ExpressRoute.

>[!NOTE]
> Uma versão anterior deste documento afirmava que as taxas de peering de rede virtual não se aplicariam no VNet (ou vNet não gateway) com o Gateway Transit. Agora reflete preços precisos por página de preços.

## <a name="next-steps"></a>Próximas etapas

* Você pode criar um peering entre duas redes virtuais. As redes podem pertencer à mesma assinatura, diferentes modelos de implantação na mesma assinatura ou assinaturas diferentes. Conclua um tutorial para um dos seguintes cenários:

    |Modelo de implantação do Azure             | Subscription  |
    |---------                          |---------|
    |Ambos Resource Manager              |[Mesmo](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[Diferente](create-peering-different-subscriptions.md)|
    |Um Resource Manager, um clássico  |[Mesmo](create-peering-different-deployment-models.md)|
    |                                   |[Diferente](create-peering-different-deployment-models-subscriptions.md)|

* Para saber como criar um hub e falar topologia de rede, consulte topologia de rede com fala hub [no Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Para saber mais sobre todas as configurações de peering de rede virtual, consulte [Criar, alterar ou excluir uma rede virtual peering](virtual-network-manage-peering.md).
* Para obter respostas às perguntas de peering de rede virtual comum e de peering de rede virtual global, consulte [VNet Peering](virtual-networks-faq.md#vnet-peering).
