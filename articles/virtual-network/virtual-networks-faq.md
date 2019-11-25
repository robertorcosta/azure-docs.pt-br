---
title: Perguntas Frequentes sobre a Rede Virtual do Azure
titlesuffix: Azure Virtual Network
description: Respostas para as perguntas mais frequentes sobre as redes virtuais do Microsoft Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2019
ms.author: kumud
ms.openlocfilehash: 5fae340ae933b8165a2ea9bb9f6337189fd576d6
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457047"
---
# <a name="azure-virtual-network-frequently-asked-questions-faq"></a>Perguntas frequentes sobre a rede virtual do Azure (FAQ)

## <a name="virtual-network-basics"></a>Noções básicas sobre redes virtuais

### <a name="what-is-an-azure-virtual-network-vnet"></a>O que é uma Rede Virtual (VNet) do Azure?
Uma Rede Virtual do Azure (VNet) é uma representação da sua própria rede na nuvem. É um isolamento lógico da nuvem do Azure dedicada à sua assinatura. Você pode usar redes virtuais para provisionar e gerenciar VPNs (redes virtuais privadas) no Azure e, se desejar, vincular as redes virtuais no Azure a outras redes virtuais no Azure ou à sua infraestrutura de TI local para criar soluções híbridas e entre locais. Each VNet you create has its own CIDR block and can be linked to other VNets and on-premises networks as long as the CIDR blocks do not overlap. Você também controla as configurações do servidor DNS para redes virtuais e a segmentação da VNet em sub-redes.

Use redes virtuais para:

* Create a dedicated private cloud-only VNet. Às vezes, você não precisa de uma configuração entre locais para sua solução. Quando você cria uma redes virtuais, seus serviços e VMs na rede virtual podem se comunicar de forma direta e segura entre si na nuvem. Adicionalmente, é possível configurar conexões de ponto de extremidade para VMs e serviços que exigem comunicação via Internet, como parte de sua solução.

* Securely extend your data center. Com as redes virtuais, você pode criar VPNs S2S (site a site) tradicionais para escalar com segurança a capacidade do data center. As VPNs S2S usam IPSEC para fornecer uma conexão segura entre o gateway de VPN corporativo e o Azure.

* Enable hybrid cloud scenarios. As redes virtuais proporcionam a flexibilidade para oferecer suporte a uma variedade de cenários de nuvem híbrida. É possível conectar com segurança aplicativos baseados em nuvem a qualquer tipo de sistema local, como mainframes e sistemas Unix.

### <a name="how-do-i-get-started"></a>Como posso começar?
Acesse a [documentação da Rede Virtual](https://docs.microsoft.com/azure/virtual-network/) para saber como começar a usar. Este conteúdo fornece informações gerais e de implantação para todos os recursos de VNet.

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>Posso usar redes virtuais sem conectividade entre locais?
Sim. É possível utilizar uma VNet sem conectá-la às suas instalações. Por exemplo, você pode executar controladores de domínio do Microsoft Windows Server Active Directory e farms do SharePoint exclusivamente em uma VNet do Azure.

### <a name="can-i-perform-wan-optimization-between-vnets-or-a-vnet-and-my-on-premises-data-center"></a>Posso realizar uma otimização de WAN entre redes virtuais ou entre uma rede virtual e meu data center local?
Sim. Você pode implantar um [dispositivo virtual de rede de otimização de WAN](https://azuremarketplace.microsoft.com/en-us/marketplace/?term=wan%20optimization) de vários fornecedores por meio do Marketplace do Azure.

## <a name="configuration"></a>Configuração

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>Quais ferramentas eu uso para criar uma redes virtuais?
Você pode usar as seguintes ferramentas para criar ou configurar uma rede virtual:

* Portal do Azure
* PowerShell
* Azure CLI
* Um arquivo de configuração de rede (netcfg - somente para Redes Virtuais clássicas). Consulte o artigo [Configurar uma VNet usando um arquivo de configuração de rede](virtual-networks-using-network-configuration-file.md).

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>Quais intervalos de endereço posso usar em minhas redes virtuais?
Qualquer intervalo de endereços IP definido na [RFC 1918](https://tools.ietf.org/html/rfc1918). Por exemplo, 10.0.0.0/16. Não é possível adicionar os seguintes intervalos de endereços:
* 224.0.0.0/4 (Multicast)
* 255.255.255.255/32 (Broadcast)
* 127.0.0.0/8 (Loopback)
* 169.254.0.0/16 (Link-local)
* 168.63.129.16/32 (DNS interno)

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>Posso ter endereços IP públicos em minhas redes virtuais?
Sim. Para obter mais informações sobre intervalos de endereços IP públicos, consulte [Criar uma rede virtual](manage-virtual-network.md#create-a-virtual-network). Os endereços IP públicos não são acessíveis diretamente da Internet.

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-vnet"></a>Há um limite para o número de sub-redes na minha VNet?
Sim. Consulte [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) para obter detalhes. Espaços de endereço de sub-rede não podem se sobrepor.

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>Existem restrições quanto ao uso de endereços IP dentro dessas sub-redes?
Sim. O Azure reserva cinco endereços IP dentro de cada sub-rede. These are x.x.x.0-x.x.x.3 and the last address of the subnet. x.x.x.1-x.x.x.3 is reserved in each subnet for Azure services.   
- x.x.x.0: Network address
- x.x.x.1: Reserved by Azure for the default gateway
- x.x.x.2, x.x.x.3: Reserved by Azure to map the Azure DNS IPs to the VNet space
- x.x.x.255: Network broadcast address

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>Que tamanho, máximo e mínimo, as redes virtuais e sub-redes podem ter?
The smallest supported IPv4 subnet is /29, and the largest is /8 (using CIDR subnet definitions).  IPv6 subnets must be exactly /64 in size.  

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>Posso levar minhas VLANs para o Azure usando redes virtuais?
Não. As v são sobreposições da Camada 3. O Azure não oferece suporte a nenhuma semântica da Camada 2.

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>Posso especificar políticas de roteamento personalizadas nas minhas redes virtuais e sub-redes?
Sim. É possível criar uma tabela de rota e associá-la a uma sub-rede. Para obter mais informações sobre o roteamento no Azure, consulte [Visão geral de roteamento](virtual-networks-udr-overview.md#custom-routes).

### <a name="do-vnets-support-multicast-or-broadcast"></a>As redes virtuais oferecem suporte ao multicast ou à difusão?
Não. Não há suporte para difusão ou multicast.

### <a name="what-protocols-can-i-use-within-vnets"></a>Quais protocolos posso usar nas redes virtuais?
Você pode usar protocolos TCP, UDP e ICMP TCP/IP em redes virtuais. O Unicast tem suporte em VNets, com exceção do Protocolo DHCP via Unicast (porta de origem UDP/68 / porta de destino UDP/67). Pacotes encapsulados de IP em IP, multicast, difusão e pacotes de Encapsulamento de Roteamento Genérico (GRE) são bloqueados nas redes virtuais. 

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>Posso executar ping em meus roteadores padrão em uma rede virtual?
Não.

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>Posso usar tracert para diagnosticar a conectividade?
Não.

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>Posso adicionar sub-redes depois que a rede virtual é criada?
Sim. As sub-redes podem ser adicionadas às VNets a qualquer momento, desde que o alcance do endereço de sub-rede não faça parte de outra sub-rede e tenha espaço disponível no intervalo de endereços da rede virtual.

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>Posso modificar o tamanho da minha sub-rede depois de criá-la?
Sim. Você poderá adicionar, remover, expandir ou reduzir uma sub-rede se não houver VMs ou serviços implantados nela.

### <a name="can-i-modify-subnets-after-i-created-them"></a>Posso modificar sub-redes depois de criá-las?
Sim. Você pode adicionar, remover e modificar os blocos CIDR usados por uma rede virtual.

### <a name="if-i-am-running-my-services-in-a-vnet-can-i-connect-to-the-internet"></a>Se eu estiver executando meus serviços em uma VNet, posso conectar-me à internet?
Sim. Todos os serviços implantados dentro de uma VNet podem conectar-se à internet. Para saber mais sobre as conexões de internet de saída no Azure, consulte [Conexões de Saída](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Se você quiser conectar a entrada a um recurso implantado através do Gerenciador de Recursos, o recurso deverá ter um endereço IP público atribuído a ele. Para saber mais sobre endereços IP públicos, consulte [Endereços IP públicos](virtual-network-public-ip-address.md). Cada Serviço de Nuvem do Azure implantado no Azure tem um VIP publicamente endereçável atribuído a ele. Você define pontos de extremidade de entrada para funções de PaaS e pontos de extremidade para máquinas virtuais para permitir que esses serviços aceitem conexões da Internet.

### <a name="do-vnets-support-ipv6"></a>As redes virtuais oferecem suporte ao IPv6?
Yes, VNets can be IPv4-only or dual stack (IPv4+IPv6).  For details, see [Overview of IPv6 for Azure Virtual Networks](./ipv6-overview.md).

### <a name="can-a-vnet-span-regions"></a>Uma rede virtual pode abranger regiões?
Não. A rede virtual é limitada a uma única região. Todavia, uma rede virtual abrange zonas de disponibilidade. Para saber mais sobre as zonas de disponibilidade, consulte [Visão geral das zonas de disponibilidade](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). É possível conectar redes virtuais em diferentes regiões com emparelhamento de rede virtual. Para obter detalhes, consulte [Visão geral do emparelhamento de rede virtual](virtual-network-peering-overview.md)

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>Posso conectar uma rede virtual a outra rede virtual no Azure?
Sim. É possível conectar uma VNet a outra VNet, utilizando:
- **Emparelhamento de rede virtual**: para obter detalhes, consulte [Visão geral do emparelhamento de VNet](virtual-network-peering-overview.md)
- **Um Gateway de VPN do Azure**: Para obter detalhes, consulte [Configurar uma conexão VNet a VNet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

## <a name="name-resolution-dns"></a>Resolução de nomes (DNS)

### <a name="what-are-my-dns-options-for-vnets"></a>Quais são minhas opções DNS para redes virtuais?
Use a tabela de decisão na página [Resolução de nome para VMs e instâncias de função](virtual-networks-name-resolution-for-vms-and-role-instances.md) , que orientará você por todas as opções DNS disponíveis.

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>Posso especificar servidores DNS para uma rede virtual?
Sim. Você pode especificar endereços IP do servidor DNS nas configurações da rede virtual. A configuração é aplicada como o(s) servidor(es) DNS padrão a todas as VMs na VNet.

### <a name="how-many-dns-servers-can-i-specify"></a>Quantos servidores DNS posso especificar?
Referência [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits).

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>Posso modificar meus servidores DNS depois de ter criado a rede?
Sim. Você pode alterar a lista de servidores DNS para sua rede virtual a qualquer momento. If you change your DNS server list, you need to perform a DHCP lease renewal on all affected VMs in the VNet, for the new DNS settings to take effect. For VMs running Windows OS you can do this by typing `ipconfig /renew` directly on the VM. For other OS types, refer to the DHCP lease renewal documentation for the specific OS type. 

### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>O que é o DNS fornecido pelo Azure? Ele funciona com redes virtuais?
O DNS fornecido pelo Azure é um serviço DNS multilocatário oferecido pela Microsoft. O Azure registra todas as VMs e instâncias de função do serviço de nuvem nesse serviço. Esse serviço fornece resolução de nome por nome de host para VMs e instâncias de função contidas no mesmo serviço de nuvem e por FQDN para VMs e instâncias de função na mesma rede virtual. Para saber mais sobre DNS, consulte [Resolução de nomes para instâncias da função dos Serviços de Nuvem e VMs](virtual-networks-name-resolution-for-vms-and-role-instances.md).

Existe uma limitação para os primeiros 100 serviços de nuvem em uma VNet para resolução de nome entre locatários usando DNS fornecido pelo Azure. Se você estiver usando seu próprio servidor DNS, essa limitação não se aplicará.

### <a name="can-i-override-my-dns-settings-on-a-per-vm-or-cloud-service-basis"></a>Eu posso substituir minhas configurações DNS com base em serviço de nuvem ou por VM?
Sim. É possível configurar servidores DNS por VM ou serviço de nuvem para substituir as configurações de rede padrão. No entanto, é recomendável utilizar o DNS em toda a rede tanto quanto possível.

### <a name="can-i-bring-my-own-dns-suffix"></a>Posso colocar meu próprio sufixo DNS?
Não. Você não pode especificar um sufixo DNS personalizado para suas redes virtuais.

## <a name="connecting-virtual-machines"></a>Conexão de máquinas virtuais

### <a name="can-i-deploy-vms-to-a-vnet"></a>Posso implantar VMs em uma rede virtual?
Sim. Todas as interfaces de rede (NIC) anexadas a uma VM implantada por meio do modelo de implantação do Gerenciador de Recursos devem estar conectadas a uma rede virtual. VMs implantadas por meio do modelo de implantação clássico podem ser conectadas a uma rede virtual.

### <a name="what-are-the-different-types-of-ip-addresses-i-can-assign-to-vms"></a>Quais são os diferentes tipos de endereços IP que posso atribuir a VMs?
* **Privado:** atribuído a cada NIC em cada VM. O endereço é atribuído usando o método estático ou dinâmico. Os endereços IP privados são atribuídos do intervalo que você especificou nas configurações de sub-rede da VNet. Recursos implantados por meio do modelo de implantação clássico recebem endereços IP privados mesmo que não estejam conectados a uma VNet. O comportamento do método de alocação é diferente, dependendo se um recurso foi implantado com o modelo de implantação do Azure Resource Manager ou clássico: 

  - **Azure Resource Manager**: um endereço IP privado atribuído com o método dinâmico ou estático permanece atribuído a uma máquina virtual (Gerenciador de Recursos) até que o recurso seja excluído. A diferença é que você seleciona o endereço a ser atribuído quando usar estático e o Azure escolhe quando usar dinâmico. 
  - **Clássico**: um endereço IP privado atribuído com o método dinâmico poderá alterar quando uma VM da máquina virtual (clássica) é reiniciada após ter ficado no estado parado (desalocado). Se você precisar garantir que o endereço IP privado de um recurso implantado através do modelo de implantação clássico nunca altere, atribua um endereço IP privado com o método estático.

* **Público:** opcionalmente atribuído a NICs anexadas a VMs implantadas por meio do modelo de implantação do Azure Resource Manager. O endereço pode ser atribuído com o método de alocação estática ou dinâmica. Todas as instâncias de função de VMs e Serviços de Nuvem implantadas por meio do modelo de implantação clássico existem dentro de um serviço de nuvem, ao qual é atribuído um endereço IP endereço virtual IP (VIP) *dinâmico* e público. Um endereço IP público e *estático*, chamado de [endereço IP Reservado](virtual-networks-reserved-public-ip.md), pode ser atribuído como VIP. Você pode atribuir endereços IP públicos a instâncias de função de VMs ou serviços de nuvem individuais implantados por meio do modelo de implantação clássico. Eles são chamados de [endereços IP públicos em nível de instância (ILPIP)](virtual-networks-instance-level-public-ip.md) e podem ser atribuídos dinamicamente.

### <a name="can-i-reserve-a-private-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>Posso reservar um endereço IP privado para uma VM que vou criar mais tarde?
Não. Não é possível reservar um endereço IP privado. Se um endereço IP privado estiver disponível, será atribuído a uma VM ou instância de função pelo servidor DHCP. A VM pode ou não ser aquela à qual você deseja que o endereço IP privado seja atribuído. No entanto, você pode alterar o endereço IP privado de uma VM já criada para qualquer endereço IP privado disponível.

### <a name="do-private-ip-addresses-change-for-vms-in-a-vnet"></a>Os endereços IP privado mudam para VMs em uma rede virtual?
Depende. Se a VM foi implantada por meio do Resource Manager, não, independentemente se o endereço IP foi atribuído com o método de alocação estática ou dinâmica. Se a VM foi implantada através do modelo de implantação clássico, os endereços IP dinâmicos poderão alterar quando uma VM for iniciada após ter ficado no estado parado (desalocado). O endereço é liberado de uma VM implantada através de um modelo de implantação quando a VM é excluída.

### <a name="can-i-manually-assign-ip-addresses-to-nics-within-the-vm-operating-system"></a>Posso atribuir endereços IP a NICs manualmente dentro do sistema operacional da VM?
Sim, mas não é recomendado, a menos que seja necessário, como quando atribuir vários endereços IP a uma máquina virtual. Para obter detalhes, consulte [Adicionar vários endereços IP a uma máquina virtual](virtual-network-multiple-ip-addresses-portal.md#os-config). Se o endereço IP atribuído a um NIC do Azure anexado a uma VM alterar, e o endereço IP dentro do sistema operacional da VM for diferente, você perderá conectividade com a VM.

### <a name="if-i-stop-a-cloud-service-deployment-slot-or-shutdown-a-vm-from-within-the-operating-system-what-happens-to-my-ip-addresses"></a>Se eu parar um slot de implantação no Serviço de Nuvem ou desligar uma VM do sistema operacional, o que acontecerá com os endereços IP?
Nada. Os endereços IP (VIP público, público e privado) permanecem atribuídos ao slot de implantação do Serviço de Nuvem ou à VM.

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-redeploying"></a>Eu posso mover VMs de uma sub-rede para outra sub-rede em uma VNet sem reimplantação?
Sim. Você pode encontrar mais informações no artigo [Como mover uma VM ou instância de função para uma sub-rede diferente](virtual-networks-move-vm-role-to-subnet.md).

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>Posso configurar um endereço MAC estático para minha VM?
Não. Um endereço MAC não pode ser configurado estaticamente.

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-its-created"></a>O endereço MAC permanecerá o mesmo para a minha VM depois de criado?
Sim, o endereço MAC continua o mesmo para uma VM implantada por meio do Gerenciador de Recursos e dos modelos de implantação clássicos até que esta seja excluída. Anteriormente, o endereço MAC era lançado se a máquina virtual fosse interrompida (desalocada), mas agora o endereço MAC é mantido mesmo quando a VM está no estado desalocado. O endereço MAC permanece atribuído ao adaptador de rede até que o mesmo seja excluído ou o endereço IP privado atribuído à configuração de IP primário do adaptador de rede primário seja alterado. 

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>Posso me conectar à internet de uma VM em uma rede virtual?
Sim. Todas as instâncias de função de VMs e Serviços de Nuvem implantados em uma rede virtual podem se conectar à internet.

## <a name="azure-services-that-connect-to-vnets"></a>Serviços do Azure que se conectam a redes virtuais

### <a name="can-i-use-azure-app-service-web-apps-with-a-vnet"></a>Posso usar os Aplicativos Web do Serviço de Aplicativo do Azure em uma rede virtual?
Sim. You can deploy Web Apps inside a VNet using an ASE (App Service Environment), connect the backend of your apps to your VNets with VNet Integration, and lock down inbound traffic to your app with service endpoints. Para obter mais informações, consulte os seguintes artigos:

* [App Service networking features](../app-service/networking-features.md)
* [Criando Aplicativos Web em um Ambiente do Serviço de Aplicativo](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Integrar seu aplicativo a uma Rede Virtual do Azure](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [App Service access restrictions](../app-service/app-service-ip-restrictions.md)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>Posso implantar Serviços de Nuvem com funções web e de trabalho (PaaS) em uma rede virtual?
Sim. Você pode implantar instâncias de função de Serviços de Nuvem em redes virtuais. Para fazer isso, especifique o nome e os mapeamentos de função/sub-rede na seção de configuração de rede da sua configuração de serviço. Não é preciso atualizar nenhum dos binários.

### <a name="can-i-connect-a-virtual-machine-scale-set-to-a-vnet"></a>Can I connect a virtual machine scale set to a VNet?
Sim. You must connect a virtual machine scale set to a VNet.

### <a name="is-there-a-complete-list-of-azure-services-that-can-i-deploy-resources-from-into-a-vnet"></a>Há uma lista completa dos serviços do Azure, dos quais eu posso implantar recursos em uma VNet?
Sim. Para obter detalhes, consulte [Integração de rede virtual para os serviços do Azure](virtual-network-for-azure-services.md).

### <a name="how-can-i-restrict-access-to-azure-paas-resources-from-a-vnet"></a>How can I restrict access to Azure PaaS resources from a VNet?

Resources deployed through some Azure PaaS services (such as Azure Storage and Azure SQL Database), can restrict network access to VNet through the use of virtual network service endpoints or Azure Private Link. For details, see [Virtual network service endpoints overview](virtual-network-service-endpoints-overview.md), [Azure Private Link overview](../private-link/private-link-overview.md)

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>Posso mover meus serviços para dentro e fora das redes virtuais?
Não. Não é possível mover serviços para dentro e fora das redes virtuais. Para mover um recurso para outra VNet, você deverá excluir e reimplantar o recurso.

## <a name="security"></a>Segurança

### <a name="what-is-the-security-model-for-vnets"></a>Qual é o modelo de segurança para redes virtuais?
As VNets são isoladas uma da outra e outros serviços hospedados na infraestrutura do Azure. Uma rede virtual é um limite de confiança.

### <a name="can-i-restrict-inbound-or-outbound-traffic-flow-to-vnet-connected-resources"></a>Posso restringir o fluxo de tráfego de entrada ou saída para recursos conectados à rede virtual?
Sim. Você pode aplicar [Grupos de Segurança de Rede](security-overview.md) a sub-redes individuais em uma rede virtual, NICs conectados a uma rede virtual ou ambos.

### <a name="can-i-implement-a-firewall-between-vnet-connected-resources"></a>Posso implementar um firewall entre os recursos conectados à rede virtual?
Sim. Você pode implantar [dispositivos virtuais de rede de firewall](https://azure.microsoft.com/marketplace/?term=firewall) de vários fornecedores por meio do Marketplace do Azure.

### <a name="is-there-information-available-about-securing-vnets"></a>Existem informações disponíveis sobre como proteger redes virtuais?
Sim. Para obter detalhes, consulte [Visão geral da segurança de rede do Azure](../security/fundamentals/network-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="apis-schemas-and-tools"></a>APIs, esquemas e ferramentas

### <a name="can-i-manage-vnets-from-code"></a>Posso gerenciar redes virtuais usando código?
Sim. You can use REST APIs for VNets in the [Azure Resource Manager](/rest/api/virtual-network) and [classic](https://go.microsoft.com/fwlink/?LinkId=296833) deployment models.

### <a name="is-there-tooling-support-for-vnets"></a>Há suporte a ferramentas para redes virtuais?
Sim. Saiba mais sobre como usar:
- O Portal do Azure para implantar redes virtuais por meio dos modelos de implantação do [Azure Resource Manager](manage-virtual-network.md#create-a-virtual-network) e [clássico](virtual-networks-create-vnet-classic-pportal.md).
- O PowerShell para gerenciar redes virtuais implantadas por meio dos modelos de implantação do [Gerenciador de Recursos](/powershell/module/az.network) e [clássico](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0).
- A CLI (interface de linha de comando) do Azure para implantar e gerenciar VNets implantadas através dos modelos de implantação do [Azure Resource Manager](/cli/azure/network/vnet) e [clássico](../virtual-machines/azure-cli-arm-commands.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-commands-to-manage-network-resources).  

## <a name="vnet-peering"></a>Emparelhamento de VNet

### <a name="what-is-vnet-peering"></a>O que é emparelhamento VNet?
O emparelhamento VNet (ou emparelhamento de rede virtual) permite que você conecte redes virtuais. Uma conexão de emparelhamento VNet entre redes virtuais permite rotear o tráfego entre elas de modo privado por meio de endereços IPv4. As máquinas virtuais nas VNets emparelhadas podem se comunicar entre si, como se elas estivessem na mesma rede. Essas redes virtuais podem estar na mesma região ou em regiões diferentes (também conhecido como Emparelhamento VNet Global). As conexões de emparelhamento VNet também podem ser criadas entre assinaturas do Azure.

### <a name="can-i-create-a-peering-connection-to-a-vnet-in-a-different-region"></a>É possível criar uma conexão de emparelhamento para uma VNet em uma região diferente?
Sim. O emparelhamento VNet global permite emparelhar VNets em diferentes regiões. Global VNet peering is available in all Azure public regions, China cloud regions, and Government cloud regions. You cannot globally peer from Azure public regions to national cloud regions.

### <a name="what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers"></a>What are the constraints related to Global VNet Peering and Load Balancers?
If the two virtual networks in two different regions are peered over Global VNet Peering, you cannot connect to resources that are behind a Basic Load Balancer through the Front End IP of the Load Balancer. This restriction does not exist for a Standard Load Balancer.
The following resources can use Basic Load Balancers which means you cannot reach them through the Load Balancer's Front End IP over Global VNet Peering. You can however use Global VNet peering to reach the resources directly through their private VNet IPs, if permitted. 
- VMs behind Basic Load Balancers
- Virtual machine scale sets with Basic Load Balancers 
- Cache Redis 
- Application Gateway (v1) SKU
- Malha de Serviço
- SQL MI
- Gerenciamento de API
- Active Directory Domain Service (ADDS)
- aplicativos Lógicos
- HDInsight
-   Lote do Azure
- Ambiente do Serviço de Aplicativo

You can connect to these resources via ExpressRoute or VNet-to-VNet through VNet Gateways.

### <a name="can-i-enable-vnet-peering-if-my-virtual-networks-belong-to-subscriptions-within-different-azure-active-directory-tenants"></a>É possível habilitar emparelhamento VNet se as redes virtuais pertencerem a assinaturas em diferentes locatários do Azure Active Directory?
Sim. É possível estabelecer Emparelhamento de VNet (local ou global) se as assinaturas pertencerem a diferentes locatários do Azure Active Directory. Você pode fazer isso via PowerShell ou CLI. Ainda não há suporte para o portal.

### <a name="my-vnet-peering-connection-is-in-initiated-state-why-cant-i-connect"></a>A conexão de emparelhamento VNet está no estado *Iniciado*, por que não consigo conectar?
If your peering connection is in an *Initiated* state, this means you have created only one link. Um link bidirecional deve ser criado para estabelecer uma conexão com êxito. Por exemplo, para emparelhar VNet A a VNet B, um vínculo deve ser criado da VNetA para VNetB e da VNetB para VNetA. Creating both links will change the state to *Connected*.

### <a name="my-vnet-peering-connection-is-in-disconnected-state-why-cant-i-create-a-peering-connection"></a>Minha conexão de emparelhamento VNet está no estado *Desconectado*, por que não consigo criar uma conexão de emparelhamento?
If your VNet peering connection is in a *Disconnected* state, it means one of the links created was deleted. In order to re-establish a peering connection, you will need to delete the link and recreate it.

### <a name="can-i-peer-my-vnet-with-a-vnet-in-a-different-subscription"></a>Eu posso emparelhar minha VNet com uma VNet em uma assinatura diferente?
Sim. É possível emparelhar VNets entre assinaturas e entre regiões.

### <a name="can-i-peer-two-vnets-with-matching-or-overlapping-address-ranges"></a>Eu posso emparelhar duas VNets com intervalos de endereços sobrepostos ou correspondentes?
Não. Espaços de endereço não devem sobrepor para habilitar o Emparelhamento VNET.

### <a name="how-much-do-vnet-peering-links-cost"></a>Quanto custa para fazer vínculos de emparelhamento VNet?
Não há encargos para criar uma conexão de emparelhamento VNet. É cobrada a transferência de dados entre as conexões de emparelhamento. [Consulte aqui](https://azure.microsoft.com/pricing/details/virtual-network/).

### <a name="is-vnet-peering-traffic-encrypted"></a>O tráfego de emparelhamento VNet é criptografado?
Não. O tráfego entre recursos em VNets emparelhadas é privado e isolado. Ele permanece completamente no backbone da Microsoft.

### <a name="why-is-my-peering-connection-in-a-disconnected-state"></a>Why is my peering connection in a *Disconnected* state?
As conexões de emparelhamento VNet entram no estado *Desconectado* quando um vínculo de emparelhamento VNet é excluído. É necessário excluir ambos os vínculos para restabelecer uma conexão de emparelhamento com êxito.

### <a name="if-i-peer-vneta-to-vnetb-and-i-peer-vnetb-to-vnetc-does-that-mean-vneta-and-vnetc-are-peered"></a>Se emparelhar VNetA para VNetB e emparelhar VNetB para VNetC, isso significa que VNetA e VNetC estão emparelhadas?
Não. Não há suporte para emparelhamento transitivo. É necessário emparelhar a VNetA e VNetC para que isso seja realizado.

### <a name="are-there-any-bandwidth-limitations-for-peering-connections"></a>Há alguma limitação de largura de banda para conexões de emparelhamento?
Não. O emparelhamento VNet, seja local ou global, não impõe restrições de largura de banda. A largura de banda é limitada apenas pela VM ou pelo recurso de computação.

### <a name="how-can-i-troubleshoot-vnet-peering-issues"></a>How can I troubleshoot VNet Peering issues?
Here is a [troubleshooter guide](https://support.microsoft.com/en-us/help/4486956/troubleshooter-for-virtual-network-peering-issues) you can try.

## <a name="virtual-network-tap"></a>TAP de rede virtual

### <a name="which-azure-regions-are-available-for-virtual-network-tap"></a>Quais regiões do Azure estão disponíveis para o TAP de rede virtual?
Virtual network TAP preview is available in all Azure regions. Os adaptadores de rede monitorados, o recurso do TAP de rede virtual e a solução de análise ou coletor devem ser implantados na mesma região.

### <a name="does-virtual-network-tap-support-any-filtering-capabilities-on-the-mirrored-packets"></a>O TAP de rede virtual é compatível com alguma funcionalidade de filtragem nos pacotes espelhados?
Não há suporte para funcionalidades de filtragem com a versão prévia do TAP da rede virtual. Quando uma configuração TAP é adicionada a um adaptador de rede, uma cópia profunda de todo o tráfego de entrada e de saída no adaptador de rede é transmitida para o destino do TAP.

### <a name="can-multiple-tap-configurations-be-added-to-a-monitored-network-interface"></a>Várias configurações TAP podem ser adicionadas a um adaptador de rede monitorado?
Um adaptador de rede monitorado pode ter apenas uma configuração TAP. Check with the individual [partner solution](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions) for the capability to stream multiple copies of the TAP traffic to the analytics tools of your choice.

### <a name="can-the-same-virtual-network-tap-resource-aggregate-traffic-from-monitored-network-interfaces-in-more-than-one-virtual-network"></a>O mesmo recurso de TAP de rede virtual pode agregar tráfego de adaptadores de rede monitorados em mais de uma rede virtual?
Sim. O mesmo recurso de TAP de rede virtual pode ser usado para agregar tráfego espelhado dos adaptadores de rede monitorados em redes virtuais emparelhadas na mesma assinatura ou em uma assinatura diferente. O recurso de TAP de rede virtual e o balanceador de carga de destino ou adaptador de rede de destino devem estar na mesma assinatura. Todas as assinaturas deve estar no mesmo locatário do Azure Active Directory.

### <a name="are-there-any-performance-considerations-on-production-traffic-if-i-enable-a-virtual-network-tap-configuration-on-a-network-interface"></a>Haverá alguma consideração de desempenho sobre o tráfego da produção se eu habilitar uma configuração do TAP de rede virtual em um adaptador de rede?

Virtual network TAP is in preview. Durante a versão prévia, não há nenhum contrato de nível de serviço. A funcionalidade não deve ser usada para cargas de trabalho de produção. When a virtual machine network interface is enabled with a TAP configuration, the same resources on the Azure host allocated to the virtual machine to send the production traffic is used to perform the mirroring function and send the mirrored packets. Selecione o tamanho de máquina virtual [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Windows](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) correto para garantir que os recursos suficientes estejam disponíveis para a máquina virtual enviar o tráfego de produção e o tráfego espelhado.

### <a name="is-accelerated-networking-for-linuxcreate-vm-accelerated-networking-climd-or-windowscreate-vm-accelerated-networking-powershellmd-supported-with-virtual-network-tap"></a>Há suporte para a rede acelerada para [Linux](create-vm-accelerated-networking-cli.md) ou [Windows](create-vm-accelerated-networking-powershell.md) com o TAP de rede virtual?

Será possível adicionar uma configuração TAP em um adaptador de rede anexado a uma máquina virtual habilitada com uma rede acelerada. Mas o desempenho e a latência na máquina virtual serão afetados ao adicionar a configuração TAP, porque, no momento, hão há suporte para o descarregamento do tráfego de espelhamento pela rede acelerada do Azure.

## <a name="virtual-network-service-endpoints"></a>Pontos de extremidade de serviço de rede virtual

### <a name="what-is-the-right-sequence-of-operations-to-set-up-service-endpoints-to-an-azure-service"></a>Qual é a sequência correta de operações para configurar os pontos de extremidade de serviço em um serviço do Azure?
There are two steps to secure an Azure service resource through service endpoints:
1. Ativar os pontos de extremidade de serviço para o serviço do Azure.
2. Configurar ACLs da VNet no serviço do Azure.

A primeira etapa é uma operação de rede; a segunda é uma operação do recurso do serviço. Ambas as etapas podem ser executadas pelo mesmo administrador ou administradores diferentes com base nas permissões RBAC concedidas à função de administrador. É recomendável ativar os pontos de extremidade de serviço da rede virtual antes de configurar as ACLs da VNet no serviço do Azure. Hence, the steps must be performed in the sequence listed above to set up VNet service endpoints.

>[!NOTE]
> As duas operações descritas anteriormente precisam ser concluídas antes de limitar o acesso ao serviço do Azure para a VNet e a sub-rede permitidas. Ativar somente os pontos de extremidade de serviço do Azure na rede não oferece acesso limitado. Além disso, também é necessário configurar as ACLs da VNet no serviço do Azure.

Certain services (such as SQL and CosmosDB) allow exceptions to the above sequence through the **IgnoreMissingVnetServiceEndpoint** flag. Once the flag is set to **True**, VNet ACLs can be set on the Azure service side prior to setting up the service endpoints on the network side. Esse sinalizador é oferecido para ajudar os clientes nos casos em que firewalls de IPs específicos estão configurados nos serviços do Azure e ativar os pontos de extremidade de serviço na rede pode gerar uma queda na conectividade, pois o IP de origem é alterado de um endereço IPv4 público para um endereço privado. Configurar as ACLs da VNet no serviço do Azure antes de configurar os pontos de extremidade de serviço na rede pode ajudar a evitar quedas na conectividade.

### <a name="do-all-azure-services-reside-in-the-azure-virtual-network-provided-by-the-customer-how-does-vnet-service-endpoint-work-with-azure-services"></a>Todos os serviços do Azure residem na rede virtual do Azure fornecida pelo cliente? Como o ponto de extremidade de serviço da VNet funciona com os serviços do Azure?

Não. Nem todos os serviços do Azure residem na rede virtual do cliente. The majority of Azure data services such as Azure Storage, Azure SQL, and Azure Cosmos DB, are multi-tenant services that can be accessed over public IP addresses. Saiba mais sobre a integração de rede virtual para os serviços do Azure [aqui](virtual-network-for-azure-services.md). 

Ao usar o recurso de ponto de extremidade de serviço da VNet (ativando-o na rede e configurando as ACLs de VNet corretas no serviço do Azure), o acesso a um serviço do Azure fica restrito em uma VNet e uma sub-rede permitidas.

### <a name="how-does-vnet-service-endpoint-provide-security"></a>Como o ponto de extremidade de serviço da VNet oferece segurança?

The VNet service endpoint feature (turning on VNet service endpoint on the network side and setting up appropriate VNet ACLs on the Azure service side) limits the Azure service access to the allowed VNet and subnet, thus providing a network level security and isolation of the Azure service traffic. Todo o tráfego que usa os pontos de extremidade de serviço da VNet passa pelo backbone da Microsoft. Dessa forma, oferece mais uma camada de isolamento da Internet pública. Além disso, os clientes podem escolher remover completamente o acesso à Internet pública dos recursos do serviço do Azure e permitir tráfego somente da rede virtual, por meio de uma combinação de firewall de IP e ACLs de VNet. Desse modo, os recursos do serviço do Azure ficam protegidos de acessos não autorizados.      

### <a name="what-does-the-vnet-service-endpoint-protect---vnet-resources-or-azure-service"></a>O que o ponto de extremidade de serviço da VNet protege: os recursos da VNet ou o serviço do Azure?
Os pontos de extremidade de serviço da VNet ajudam a proteger os recursos do serviço do Azure. Os recursos da VNet são protegidos por meio dos NSGs (Grupos de Segurança de Rede).

### <a name="is-there-any-cost-for-using-vnet-service-endpoints"></a>Há algum custo para usar os pontos de extremidade de serviço da VNet?

Não há custos adicionais para usar os pontos de extremidade de serviço da VNet.

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-subscriptions"></a>É possível ativar os pontos de extremidade de serviço e configurar as ACLs da VNet se a rede virtual e os recursos do serviço do Azure pertencerem a assinaturas diferentes?

Sim, isso é possível. As redes virtuais e os recursos do serviço do Azure podem estar na mesma assinatura ou em assinaturas diferentes. O único requisito é que tanto a rede virtual quanto os recursos do serviço do Azure precisam estar no mesmo locatário do AD (Active Directory).

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-ad-tenants"></a>É possível ativar os pontos de extremidade de serviço e configurar as ACLs da VNet se a rede virtual e os recursos do serviço do Azure pertencerem a locatários do AD diferentes?
Não, os pontos de extremidade de serviço e as ACLs da VNet não são compatíveis com todos os locatários do AD.

### <a name="can-an-on-premises-devices-ip-address-that-is-connected-through-azure-virtual-network-gateway-vpn-or-expressroute-gateway-access-azure-paas-service-over-vnet-service-endpoints"></a>Can an on-premises device’s IP address that is connected through Azure Virtual Network gateway (VPN) or ExpressRoute gateway access Azure PaaS Service over VNet service endpoints?
Por padrão, os recursos do serviço do Azure garantidos para redes virtuais não podem ser acessados nas redes locais. Se você quiser permitir o tráfego do local, também deverá permitir endereços IP públicos (normalmente, NAT) do seu local ou ExpressRoute. Esses endereços IP podem ser adicionados por meio da configuração de firewall de IP dos recursos de serviço do Azure.

### <a name="can-i-use-vnet-service-endpoint-feature-to-secure-azure-service-to-multiple-subnets-within-a-virtual-network-or-across-multiple-virtual-networks"></a>Can I use VNet Service Endpoint feature to secure Azure service to multiple subnets within a virtual network or across multiple virtual networks?
To secure Azure services to multiple subnets within a virtual network or across multiple virtual networks, enable service endpoints on the network side on each of the subnets independently and then secure Azure service resources to all of the subnets by setting up appropriate VNet ACLs on the Azure service side.
 
### <a name="how-can-i-filter-outbound-traffic-from-a-virtual-network-to-azure-services-and-still-use-service-endpoints"></a>Como filtrar o tráfego de saída de uma rede virtual dos serviços do Azure e ainda usar os pontos de extremidade de serviço?
Se deseja verificar ou filtrar o tráfego destinado a um serviço do Azure por meio de uma rede virtual, você pode implantar um dispositivo de rede virtual na rede virtual. Em seguida, você poderá aplicar os pontos de extremidade de serviço à sub-rede em que o dispositivo de rede virtual está implantado e proteger os recursos do serviço do Azure apenas nessa sub-rede por meio das ACLs da VNet. Esse cenário pode ser útil se você deseja restringir o acesso do serviço do Azure da sua rede virtual somente a recursos específicos do Azure usando a filtragem por dispositivo de rede virtual. Para obter mais informações, consulte [Saída com dispositivos de rede virtual](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha).

### <a name="what-happens-when-you-access-an-azure-service-account-that-has-a-virtual-network-access-control-list-acl-enabled-from-outside-the-vnet"></a>What happens when you access an Azure service account that has a virtual network access control list (ACL) enabled from outside the VNet?
Os erros HTTP 403 e HTTP 404 serão retornados.

### <a name="are-subnets-of-a-virtual-network-created-in-different-regions-allowed-to-access-an-azure-service-account-in-another-region"></a>As sub-redes de uma rede virtual criada em regiões diferentes podem acessar uma conta de serviço do Azure em outra região? 
Sim. Na maioria dos serviços do Azure, as redes virtuais criadas em regiões diferentes podem acessá-los em outra região por meio dos pontos de extremidade de serviço da VNet. Por exemplo, se uma conta do Azure Cosmos DB estiver no Oeste dos EUA ou no Leste dos EUA e as redes virtuais estiverem em várias regiões, a rede virtual conseguirá acessar o Azure Cosmos DB. O armazenamento e o SQL são exceções e são regionais por natureza. A rede virtual e o serviço do Azure precisam estar na mesma região.
  
### <a name="can-an-azure-service-have-both-a-vnet-acl-and-an-ip-firewall"></a>Can an Azure service have both a VNet ACL and an IP firewall?
Yes, a VNet ACL and an IP firewall can co-exist. Os dois recursos se complementam para garantir a segurança e o isolamento.
 
### <a name="what-happens-if-you-delete-a-virtual-network-or-subnet-that-has-service-endpoint-turned-on-for-azure-service"></a>O que acontecerá se você excluir uma rede virtual ou uma sub-rede que tenha o ponto de extremidade de serviço ativado para o serviço do Azure?
A exclusão de VNets e sub-redes é uma operação independente, compatível até mesmo quando os pontos de extremidade de serviço estão ativados para os serviços do Azure. In cases where the Azure services have VNet ACLs set up, for those VNets and subnets, the VNet ACL information associated with that Azure service is disabled when a VNet or subnet that has VNet service endpoint turned on is deleted.
 
### <a name="what-happens-if-an-azure-service-account-that-has-a-vnet-service-endpoint-enabled-is-deleted"></a>What happens if an Azure service account that has a VNet Service endpoint enabled is deleted?
The deletion of an Azure service account is an independent operation and is supported even when the service endpoint is enabled on the network side and VNet ACLs are set up on Azure service side. 

### <a name="what-happens-to-the-source-ip-address-of-a-resource-like-a-vm-in-a-subnet-that-has-vnet-service-endpoint-enabled"></a>O que acontece com o endereço IP de origem do recurso (como a VM em uma sub-rede) que tem o ponto de extremidade de serviço habilitado?
Quando pontos de extremidade de serviço de rede virtual estão habilitados, os endereços IP de origem dos recursos na sub-rede da rede virtual alternarão do uso de endereços IPV4 públicos para endereços privados da Rede Virtual do Azure do tráfego para o serviço do Azure. Note that this can cause specific IP firewalls that are set to public IPV4 address earlier on the Azure services to fail. 

### <a name="does-the-service-endpoint-route-always-take-precedence"></a>Does the service endpoint route always take precedence?
Service endpoints add a system route which takes precedence over BGP routes and provides optimum routing for the service endpoint traffic. Os pontos de extremidade sempre usam o tráfego do serviço diretamente da sua rede virtual para o serviço na rede de backbone do Microsoft Azure. For more information about how Azure selects a route, see [Azure Virtual network traffic routing](virtual-networks-udr-overview.md).
 
### <a name="how-does-nsg-on-a-subnet-work-with-service-endpoints"></a>Como o NSG de uma sub-rede funciona com os pontos de extremidade de serviço?
Para alcançar o serviço do Azure, os NSGs precisam permitir a conectividade de saída. Se os NSGs estiverem abertos para todo o tráfego de saída da Internet, o tráfego do ponto de extremidade de serviço funcionará. Também é possível limitar o tráfego de saída aos IPs de serviço usando somente as tags do Serviço.  
 
### <a name="what-permissions-do-i-need-to-set-up-service-endpoints"></a>Quais permissões preciso para configurar os pontos de extremidade de serviço?
Os pontos de extremidade de serviço podem ser configurados em redes virtuais de forma independente por um usuário com acesso de gravação à rede virtual. To secure Azure service resources to a VNet, the user must have permission **Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action** for the subnets being added. Essa permissão está incluída nas funções de administrador de serviços internas por padrão e pode ser modificada com a criação de funções personalizadas. Saiba mais sobre funções internas e como atribuir permissões específicas a [funções personalizadas](https://docs.microsoft.com/azure/role-based-access-control/custom-roles?toc=%2fazure%2fvirtual-network%2ftoc.json).
 

### <a name="can-i-filter-virtual-network-traffic-to-azure-services-allowing-only-specific-azure-service-resources-over-vnet-service-endpoints"></a>É possível filtrar o tráfego de rede virtual para serviços do Azure, permitindo apenas recursos específicos do serviço do Azure, em pontos de extremidade de serviço da VNet? 

As políticas de ponto de extremidade de serviço de VNet (Rede Virtual) permitem filtrar o tráfego de rede virtual para serviços do Azure, permitindo apenas recursos específicos do serviço do Azure em pontos de extremidade de serviço. As políticas de ponto de extremidade de serviço fornecem controle de acesso granular para tráfego de rede virtual para serviços do Azure. Saiba mais sobre as políticas do ponto de extremidade de serviço [aqui](virtual-network-service-endpoint-policies-overview.md).

### <a name="does-azure-active-directory-azure-ad-support-vnet-service-endpoints"></a>Does Azure Active Directory (Azure AD) support VNet service endpoints?

Azure Active Directory (Azure AD) doesn't support service endpoints natively. Complete list of Azure Services supporting VNet service endpoints can be seen [here](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). Note that the "Microsoft.AzureActiveDirectory" tag listed under services supporting service endpoints is used for supporting service endpoints to ADLS Gen 1. For ADLS Gen 1, virtual network integration for Azure Data Lake Storage Gen1 makes use of the virtual network service endpoint security between your virtual network and Azure Active Directory (Azure AD) to generate additional security claims in the access token. Essas declarações, em seguida, são usadas para autenticar sua rede virtual na conta do Data Lake Storage Gen1 e permitir o acesso. Learn more about [Azure Data Lake Store Gen 1 VNet Integration](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

### <a name="are-there-any-limits-on-how-many-vnet-service-endpoints-i-can-set-up-from-my-vnet"></a>Há limites de pontos de extremidade de serviço de VNet que podem ser configurados em uma VNet?
Não há limite para o número total de pontos de extremidade de serviço de VNet em uma rede virtual. For an Azure service resource (such as an Azure Storage account), services may enforce limits on the number of subnets used for securing the resource. A tabela a seguir mostra limites de exemplo: 

|||
|---|---|
|Serviço do Azure| Limites de regras de VNet|
|Armazenamento do Azure| 100|
|SQL do Azure| 128|
|Azure SQL Data Warehouse|  128|
|Azure KeyVault|    127|
|Azure Cosmos DB|   64|
|Hub de Eventos do Azure|   128|
|Service Bus do Azure| 128|
|Azure Data Lake Storage V1|  100|
 
>[!NOTE]
> Os limites estão sujeitos a alterações a critério do serviço do Azure. Veja a documentação do respectivo serviço para saber detalhes. 




  



