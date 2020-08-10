---
title: Visão geral dos serviços de rede do Azure
description: Saiba mais sobre os serviços de rede no Azure, incluindo conectividade, proteção de aplicativo, entrega de aplicativos e serviços de monitoramento de rede.
services: networking
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 03/12/2020
ms.author: kumud
ms.openlocfilehash: 1110199a8c02b29d70f0c12e1ed1a6341c44e403
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88037245"
---
# <a name="azure-networking-services-overview"></a>Visão geral dos serviços de rede do Azure

Os serviços de rede no Azure fornecem uma variedade de recursos de rede que podem ser usados juntos ou separados. Clique em qualquer um dos seguintes recursos principais para saber mais sobre eles:
- [**Serviços de conectividade**](#connect): Conecte recursos do Azure e recursos locais usando qualquer ou uma combinação desses serviços de rede no Azure – rede virtual (VNet), Wan virtual, ExpressRoute, gateway de VPN, gateway de NAT de rede virtual, DNS do Azure, serviço de emparelhamento e bastiões do Azure.
- [**Serviços de proteção de aplicativo**](#protect): Proteja seus aplicativos usando qualquer ou uma combinação desses serviços de rede no Azure – link privado, proteção contra DDoS, firewall, grupos de segurança de rede, firewall do aplicativo Web e pontos de extremidade de rede virtual.
- [**Serviços de entrega de aplicativos**](#deliver): entregue aplicativos na rede do Azure usando qualquer uma delas ou uma combinação desses serviços de rede no Azure – CDN (rede de distribuição de conteúdo), serviço de porta frontal do Azure, Gerenciador de tráfego, gateway de aplicativo, analisador de Internet e Load Balancer.
- [**Monitoramento de rede**](#monitor): Monitore seus recursos de rede usando qualquer ou uma combinação desses serviços de rede no Azure-observador de rede, monitor de ExpressRoute, Azure monitor ou ponto de acesso de terminal de VNET (toque).

## <a name="connectivity-services"></a><a name="connect"></a>Serviços de conectividade
 
Esta seção descreve os serviços que fornecem conectividade entre os recursos do Azure, a conectividade de uma rede local para recursos do Azure e a conectividade de Branch para Branch no Azure – rede virtual (VNet), WAN virtual, ExpressRoute, gateway de VPN, gateway de NAT de rede virtual, DNS do Azure, serviço de emparelhamento do Azure e bastião do Azure.

|Serviço|Por que usar?|Cenários|
|---|---|---|
|[Rede virtual](#vnet)|Permite que os recursos do Azure se comuniquem com segurança entre si, Internet e redes locais.| <p>[Filtrar tráfego de rede](../virtual-network/tutorial-filter-network-traffic.md)</p> <p>[Rotear tráfego de rede](../virtual-network/tutorial-create-route-table-portal.md)</p> <p>[Restringir o acesso à rede para os recursos](../virtual-network/tutorial-restrict-network-access-to-resources.md)</p> <p>[Conectar redes virtuais](../virtual-network/tutorial-connect-virtual-networks-portal.md)</p>|
|[ExpressRoute](#expressroute)|Estende suas redes locais para a nuvem da Microsoft por uma conexão privada, facilitada por um provedor de conectividade.|<p>[Criar e modificar um circuito do ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)</p> <p>[Criar e modificar o emparelhamento de um circuito do ExpressRoute](../expressroute/expressroute-howto-routing-portal-resource-manager.md)</p> <p>[Vincular uma rede virtual a um circuito do ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)</p> <p>[Configurar e gerenciar filtros de rota para circuitos do ExpressRoute](../expressroute/how-to-routefilter-portal.md)</p>|
|[Gateway de VPN](#vpngateway)|Envia o tráfego criptografado entre uma rede virtual do Azure e uma localização local pela Internet pública.|<p>[Conexões site a site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)</p> <p>[Conexões de rede virtual a rede virtual](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)</p> <p>[Conexões ponto a site](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)</p>|
|[WAN Virtual](#virtualwan)|Otimiza e automatiza a conectividade de ramificação para, e por meio do Azure. As regiões do Azure servem como hubs para os quais é possível optar por conectar suas ramificações.|<p>[Conexões site a site](../virtual-wan/virtual-wan-site-to-site-portal.md), conexões do [ExpressRoute](../virtual-wan/virtual-wan-expressroute-portal.md)</p>|
|[DNS do Azure](#dns)|Hospeda domínios DNS que fornecem resolução de nomes usando Microsoft Azure infraestrutura.|<p>[Hospede seu domínio no DNS do Azure](../dns/dns-delegate-domain-azure-dns.md)</p><p>[Criar registros DNS para um aplicativo Web](../dns/dns-web-sites-custom-domain.md)</p> <p>[Criar um registro de alias para o Gerenciador de tráfego](../dns/tutorial-alias-tm.md)</p> <p>[Criar um registro de alias para o endereço IP público](../dns/tutorial-alias-pip.md)</p> <p>[Criar um registro de alias para o registro de recurso de zona](../dns/tutorial-alias-rr.md)</p>|
|[Azure Bastion](#bastion)|Configure a conectividade RDP/SSH segura e direta para suas máquinas virtuais diretamente no portal do Azure sobre TLS. Quando você se conecta por meio de bastiões do Azure, suas máquinas virtuais não precisam de um endereço IP público|<p>[Criar um Azure Bastion host](../bastion/bastion-create-host-portal.md)</p><p>[Conectar-se usando SSH em uma VM Linux](../bastion/bastion-connect-vm-ssh.md)</p><p>[Conectar-se usando o RDP em uma VM do Windows](../bastion/bastion-connect-vm-rdp.md)</p>|
|[Gateway NAT da rede virtual](#nat)|Crie um gateway NAT para fornecer conectividade de saída para uma máquina virtual.|<p>[Criar um gateway NAT](../virtual-network/quickstart-create-nat-gateway-portal.md)</p>|
|[Serviço de emparelhamento do Azure (versão prévia)](#azurepeeringservice)|Colabore com provedores de serviços para um roteamento ideal e confiável para a nuvem da Microsoft pela rede pública.|<p>[Registrar o serviço de emparelhamento do Azure](../peering-service/azure-portal.md)</p>|
||||


### <a name="virtual-network"></a><a name="vnet"></a>Rede virtual

A Rede Virtual do Azure (VNet) é o bloco de construção fundamental de sua rede privada no Azure. Você pode usar um VNets para:
- **Comunicar-se entre os recursos do Azure**: você pode implantar VMs e vários outros tipos de recursos do Azure em uma rede virtual, como ambientes de serviço Azure app, o AKs (serviço kubernetes do Azure) e conjuntos de dimensionamento de máquinas virtuais do Azure. Para exibir uma lista completa de recursos do Azure que podem ser implantados em uma rede virtual, confira [Integração de serviços de rede virtual](../virtual-network/virtual-network-for-azure-services.md).
- **Comunicar entre**si: você pode conectar redes virtuais entre si, permitindo que os recursos em qualquer rede virtual se comuniquem entre si, usando o emparelhamento de rede virtual. As redes virtuais que você conecta podem estar na mesma região ou em regiões diferentes do Azure. Para obter mais informações, consulte [Emparelhamento de rede virtual do Azure](../virtual-network/virtual-network-peering-overview.md).
- **Comunicar-se com a Internet**: todos os recursos em uma VNet podem comunicar a saída para a Internet, por padrão. Você pode se comunicar na entrada com um recurso, atribuindo um endereço IP público ou um Load Balancer público. Você também pode usar [endereços IP públicos](../virtual-network/virtual-network-public-ip-address.md) ou [Load Balancer](../load-balancer/load-balancer-overview.md) públicos para gerenciar suas conexões de saída.
- **Comunicar-se com redes locais**: você pode conectar seus computadores e redes locais a uma rede virtual usando o [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou o [ExpressRoute](../expressroute/expressroute-introduction.md).

Para obter mais informações, consulte [o que é a rede virtual do Azure?](../virtual-network/virtual-networks-overview.md).

### <a name="expressroute"></a><a name="expressroute"></a>ExpressRoute
O ExpressRoute permite que você estenda suas redes locais para a nuvem da Microsoft por uma conexão privada, facilitada por um provedor de conectividade. Essa conexão é privada. O tráfego não passa pela Internet. Com o ExpressRoute, estabeleça conexões com os serviços em nuvem da Microsoft, como Microsoft Azure, Office 365 e Dynamics 365.  Para obter mais informações, consulte [o que é o ExpressRoute?](../expressroute/expressroute-introduction.md).

![Azure ExpressRoute](./media/networking-overview/expressroute-connection-overview.png)

### <a name="vpn-gateway"></a><a name="vpngateway"></a>Gateway de VPN
O gateway de VPN ajuda a criar conexões criptografadas entre locais com sua rede virtual a partir de locais ou criar conexões criptografadas entre VNets. Há diferentes configurações disponíveis para conexões de gateway de VPN, como site a site, ponto a site ou VNet para VNet.
O diagrama a seguir ilustra várias conexões VPN site a site para a mesma rede virtual.

![Conexões de gateway de VPN do Azure site a site](./media/networking-overview/vpngateway-multisite-connection-diagram.png)

Para obter mais informações sobre diferentes tipos de conexões VPN, consulte [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="virtual-wan"></a><a name="virtualwan"></a>WAN Virtual
WAN Virtual do Azure é um serviço de rede que fornece conectividade de ramificação otimizada e automatizada para e por meio do Azure. As regiões do Azure servem como hubs para os quais é possível optar por conectar suas ramificações. Você pode aproveitar o backbone do Azure para também conectar as ramificações e aproveitar a conectividade de ramificação a VNet. A WAN virtual do Azure reúne muitos serviços de conectividade de nuvem do Azure, como VPN site a site, ExpressRoute, VPN de usuário ponto a site em uma única interface operacional. A conectividade com VNets do Azure é estabelecida usando conexões de rede virtual. Para obter mais informações, consulte [o que é a WAN virtual do Azure?](../virtual-wan/virtual-wan-about.md).

![Diagrama de WAN virtual](./media/networking-overview/virtualwan1.png)

### <a name="azure-dns"></a><a name="dns"></a>DNS do Azure
O DNS do Azure é um serviço de hospedagem para domínios DNS que fornece resolução de nomes usando a infraestrutura do Microsoft Azure. Ao hospedar seus domínios no Azure, você pode gerenciar seus registros DNS usando as mesmas credenciais, APIs, ferramentas e faturamento que os outros serviços do Azure. Para obter mais informações, consulte [o que é o DNS do Azure?](../dns/dns-overview.md).

### <a name="azure-bastion"></a><a name="bastion"></a>Azure Bastion
O serviço Azure Bastion é um novo serviço de PaaS, totalmente gerenciado pela plataforma e provisionado dentro de sua rede virtual. Ele fornece conectividade de RDP/SSH contínua e segura às suas máquinas virtuais, diretamente no portal do Azure, via TLS. Quando você se conecta usando o Azure Bastion, suas máquinas virtuais não precisam de um endereço IP público. Para obter mais informações, consulte [o que é a bastiões do Azure?](../bastion/bastion-overview.md).

![Arquitetura de bastiões do Azure](./media/networking-overview/architecture.png)

### <a name="virtual-network-nat-gateway"></a><a name="nat"></a>Gateway NAT da rede virtual
A NAT de Rede Virtual (conversão de endereços de rede) simplifica a conectividade de Internet somente de saída para redes virtuais. Quando configurado em uma sub-rede, toda a conectividade de saída usa seus endereços IP públicos estáticos especificados. A conectividade de saída é possível sem endereços IP públicos ou de balanceador de carga conectados diretamente a máquinas virtuais. Para obter mais informações, consulte [o que é o gateway NAT da rede virtual?](../virtual-network/nat-overview.md) 

![Gateway NAT da rede virtual](./media/networking-overview/flow-map.png)

### <a name="azure-peering-service"></a><a name="azurepeeringservice"></a>Serviço de emparelhamento do Azure
O serviço de emparelhamento do Azure aprimora a conectividade do cliente com os serviços de nuvem da Microsoft, como Office 365, Dynamics 365, serviços SaaS (software como serviço), Azure ou qualquer serviço da Microsoft acessível pela Internet pública. Para obter mais informações, consulte [o que é o serviço de emparelhamento do Azure?](../peering-service/about.md).

## <a name="application-protection-services"></a><a name="protect"></a>Serviços de proteção de aplicativo

Esta seção descreve os serviços de rede no Azure que ajudam a proteger seus recursos de rede-proteja seus aplicativos usando qualquer uma das combinações desses serviços de rede ou uma combinação deles no link do Azure-privado, proteção contra DDoS, firewall, grupos de segurança de rede, firewall de aplicativo Web e pontos de extremidade de rede virtual.

|Serviço|Por que usar?|Cenário|
|---|---|---|
|[Proteção contra DDoS](#ddosprotection) |Alta disponibilidade para seus aplicativos com proteção contra encargos de tráfego IP em excesso|[Gerenciar a proteção contra DDoS do Azure](../virtual-network/manage-ddos-protection.md)|
|[Firewall do Aplicativo Web](#waf)|<p>O [Azure WAF com o gateway de aplicativo](../web-application-firewall/ag/ag-overview.md) fornece proteção regional para entidades no espaço de endereço público e privado</p><p>O [Azure WAF com a porta frontal](../web-application-firewall/afds/afds-overview.md) fornece proteção na borda da rede para pontos de extremidade públicos.</p>|<p>[Configurar regras de proteção de bot](../frontdoor/waf-front-door-policy-configure-bot-protection.md)</p> <p>[Configurar o código de resposta personalizada](../frontdoor/waf-front-door-configure-custom-response-code.md)</p> <p>[Configurar regras de restrição de IP](../frontdoor/waf-front-door-configure-ip-restriction.md)</p> <p>[Configurar regra de limite de taxa](../frontdoor/waf-front-door-rate-limit-powershell.md)</p> |
|[Firewall do Azure](#firewall)|Firewall do Azure é um serviço de segurança de rede gerenciado e baseado em nuvem que protege seus recursos de Rede Virtual do Azure. É um firewall como serviço totalmente com estado com alta disponibilidade interna e escalabilidade de nuvem irrestrita.|<p>[Implantar um firewall do Azure em uma vnet](../firewall/tutorial-firewall-deploy-portal.md)</p> <p>[-Implantar um firewall do Azure em uma rede híbrida](../firewall/tutorial-hybrid-ps.md)</p> <p>[Filtrar o tráfego de entrada com o Firewall do Azure DNAT](../firewall/tutorial-firewall-dnat.md)</p>|
|[Grupos de segurança de rede](#nsg)|Controle completo de nó final distribuído granular na VM/sub-rede para todos os fluxos de tráfego de rede|[Filtrar o tráfego de rede usando grupos de segurança de rede](../virtual-network/tutorial-filter-network-traffic.md)|
|[Pontos de extremidade de serviço de rede virtual](#serviceendpoints)|Permite limitar o acesso à rede para alguns recursos de serviço do Azure a uma sub-rede de rede virtual|[Restringir o acesso à rede para recursos PaaS](../virtual-network/tutorial-restrict-network-access-to-resources-powershell.md)|
[Link privado](#privatelink)|Permite que você acesse os serviços de PaaS do Azure (por exemplo, armazenamento do Azure e banco de dados SQL) e serviços hospedados de Propriedade do cliente/parceiro do Azure por meio de um ponto de extremidade privado em sua rede virtual.|<p>[Criar um ponto de extremidade privado](../private-link/create-private-endpoint-portal.md)</p><p>[Criar um serviço de vínculo privado](../private-link/create-private-link-service-portal.md)</p>|
|||
### <a name="ddos-protection"></a><a name="ddosprotection"></a>Proteção contra DDoS 
A [proteção contra DDoS do Azure](../virtual-network/manage-ddos-protection.md) fornece medidas defensivas contra as ameaças de DDoS mais sofisticadas. O serviço fornece recursos aprimorados de mitigação de DDoS para seu aplicativo e recursos implantados em suas redes virtuais. Além disso, os clientes que usam a proteção contra DDoS do Azure têm acesso ao suporte de resposta rápida a DDoS para envolver especialistas em DDoS durante um ataque ativo.

![Proteção contra DDoS](./media/networking-overview/ddos-protection.png)

### <a name="web-application-firewall"></a><a name="waf"></a>Firewall do Aplicativo Web

O WAF ( [Firewall do aplicativo Web](../web-application-firewall/overview.md) ) do Azure fornece proteção aos seus aplicativos Web contra explorações e vulnerabilidades comuns da Web, como injeção de SQL e script entre sites. O Azure WAF fornece proteção pronta para uso das 10 principais vulnerabilidades do OWASP por meio de regras gerenciadas. Além disso, os clientes também podem configurar regras personalizadas, que são regras gerenciadas pelo cliente para fornecer proteção adicional com base no intervalo de IP de origem e atributos de solicitação, como cabeçalhos, cookies, campos de dados de formulário ou parâmetros de cadeia de caracteres de consulta.

Os clientes podem optar por implantar o [Azure WAF com o gateway de aplicativo](../application-gateway/waf-overview.md) , que fornece proteção regional para entidades no espaço de endereço público e privado. Os clientes também podem optar por implantar o [Azure WAF com a porta frontal](../frontdoor/waf-overview.md) , que fornece proteção na borda da rede para pontos de extremidade públicos.

![Firewall do Aplicativo Web](./media/networking-overview/waf-overview.png)


### <a name="azure-firewall"></a><a name="firewall"></a>Firewall do Azure
Firewall do Azure é um serviço de segurança de rede gerenciado e baseado em nuvem que protege seus recursos de Rede Virtual do Azure. Usando o Firewall do Azure, você pode criar, impor e registrar políticas de conectividade de aplicativo e de rede centralmente em assinaturas e redes virtuais. O Firewall do Azure usa um endereço IP público estático para seus recursos de rede virtual, permitindo que firewalls externos identifiquem o tráfego originário de sua rede virtual. 

Para obter mais informações sobre o Firewall do Azure, consulte a [documentação do firewall do Azure](../firewall/overview.md).

![Visão geral do firewall](./media/networking-overview/firewall-threat.png)

### <a name="network-security-groups"></a><a name="nsg"></a>Grupos de segurança de rede
Você pode filtrar o tráfego de rede de e para recursos do Azure em uma rede virtual do Azure com um grupo de segurança de rede. Para obter mais informações, consulte [Visão geral de segurança](../virtual-network/security-overview.md).

### <a name="service-endpoints"></a><a name="serviceendpoints"></a>Pontos de extremidade de serviço
Os pontos de extremidade de serviço de VNet (rede virtual) estendem o espaço de endereço privado e a identidade da sua rede virtual para os serviços do Azure por meio de uma conexão direta. Os pontos de extremidade permitem que você possa garantir os recursos essenciais do serviço do Azure somente para suas redes virtuais. O tráfego de sua rede virtual para o serviço do Azure sempre permanece na rede de backbone do Microsoft Azure. Para obter mais informações, consulte [Pontos de extremidade de serviço de rede virtual](../virtual-network/virtual-network-service-endpoints-overview.md).

![Pontos de extremidade de serviço de rede virtual](./media/networking-overview/vnet-service-endpoints-overview.png)

### <a name="azure-private-link"></a><a name="privatelink"></a>Link Privado do Azure
O [link privado do Azure](../private-link/private-link-overview.md) permite que você acesse os serviços de PaaS do Azure (por exemplo, armazenamento do Azure e banco de dados SQL) e serviços de parceiros/clientes do Azure hospedados em um ponto de extremidade privado em sua rede virtual.
O tráfego entre sua rede virtual e o serviço viaja a rede de backbone da Microsoft. Expor seu serviço à Internet pública não é mais necessário. Você pode criar o próprio serviço de link privado em sua rede virtual e oferecê-lo aos seus clientes.

![Visão geral do ponto de extremidade privado](./media/networking-overview/private-endpoint.png)


## <a name="application-delivery-services"></a><a name="deliver"></a>Serviços de entrega de aplicativos

Esta seção descreve os serviços de rede no Azure que ajudam a fornecer aplicativos – rede de distribuição de conteúdo, serviço de porta frontal do Azure, Gerenciador de tráfego, Load Balancer e gateway de aplicativo.

|Serviço|Por que usar?|Cenário|
|---|---|---|
|[Rede de Distribuição de Conteúdo](#cdn)|Fornece conteúdo de alta largura de banda aos usuários. O CDNs armazena o conteúdo em cache em servidores de borda em locais POP (ponto de presença) que estão próximos aos usuários finais, para minimizar a latência|<p>[Adicionar CDN a um aplicativo Web](../cdn/cdn-add-to-web-app.md)</p> <p>[-Acessar blobs de armazenamento usando um domínio personalizado da CDN do Azure por HTTPS](..//cdn/cdn-storage-custom-domain-https.md)</p> <p>[Adicionar um domínio personalizado ao seu ponto de extremidade da CDN do Azure](../cdn/cdn-map-content-to-custom-domain.md)</p> <p>[Configurar HTTPS em um domínio personalizado da CDN do Azure](../cdn/cdn-custom-ssl.md?tabs=option-1-default-enable-https-with-a-cdn-managed-certificate)</p>|
|[Azure Front Door Service](#frontdoor)|Permite que você defina, gerencie e monitore o roteamento global para o tráfego da Web otimizando para melhor desempenho e failover global instantâneo para alta disponibilidade.|<p>[Adicionar um domínio personalizado ao Azure Front Door Service](../frontdoor/front-door-custom-domain.md)</p> <p>[Configurar HTTPS em um domínio personalizado do Front Door](../frontdoor/front-door-custom-domain-https.md)</p><p>[Configurar a política de firewall do aplicativo Web de filtragem geográfica](../frontdoor/front-door-tutorial-geo-filtering.md)|
|[Gerenciador de Tráfego](#trafficmanager)|Distribui o tráfego com base no DNS para serviços em regiões globais do Azure, fornecendo alta disponibilidade e capacidade de resposta|<p> [Rotear tráfego de baixa latência](../traffic-manager/tutorial-traffic-manager-improve-website-response.md)</p><p>[Rotear tráfego para um ponto de extremidade de prioridade](../traffic-manager/traffic-manager-configure-priority-routing-method.md)</p><p> [Controlar o tráfego com pontos de extremidade ponderados](../traffic-manager/tutorial-traffic-manager-weighted-endpoint-routing.md)</p><p>[Rotear o tráfego com base na localização geográfica do ponto de extremidade](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)</p> <p> [Rotear o tráfego com base na sub-rede do usuário](../traffic-manager/tutorial-traffic-manager-subnet-routing.md)</p>|
|[Balanceador de Carga](#loadbalancer)|Fornece balanceamento de carga regional roteando o tráfego entre zonas de disponibilidade e em seu VNets. Fornece balanceamento de carga interno roteando o tráfego entre e entre seus recursos para criar seu aplicativo regional.|<p> [Balancear tráfego de carga da internet para VMs](../load-balancer/tutorial-load-balancer-standard-manage-portal.md)</p> <p>[Balancear a carga de tráfego entre VMs dentro de uma rede virtual](../load-balancer/tutorial-load-balancer-standard-internal-portal.md)<p>[Tráfego de encaminhamento de porta para uma porta específica em VMs específicas](../load-balancer/tutorial-load-balancer-port-forwarding-portal.md)</p><p> [Configurar o balanceamento de carga e as regras de saída](../load-balancer/configure-load-balancer-outbound-cli.md)</p>|
|[Gateway de Aplicativo](#applicationgateway)|O Gateway de Aplicativo do Azure é um balanceador de carga do tráfego da Web que permite que você gerencie o tráfego para seus aplicativos Web.|<p>[Direcionar o tráfego da Web com o Gateway de Aplicativo do Azure](../application-gateway/quick-create-portal.md)</p><p>[Tutorial: Configurar um gateway de aplicativo com o encerramento de TLS usando o portal do Azure](../application-gateway/create-ssl-portal.md)</p><p>[Criar um gateway de aplicativo com o redirecionamento baseado em caminhos de URL](../application-gateway/create-url-route-portal.md) </p>|
|

### <a name="content-delivery-network"></a><a name="cdn"></a>Rede de Distribuição de Conteúdo
A CDN oferece aos desenvolvedores uma solução global de fornecimento rápido de conteúdo de alta largura de banda para usuários armazenando em cache o conteúdo em nós físicos estrategicamente posicionados em todo o mundo. Para obter mais informações sobre a CDN do Azure, consulte [rede de distribuição de conteúdo do Azure](../cdn/cdn-overview.md)

![CDN do Azure](./media/networking-overview/cdn-overview.png)

### <a name="azure-front-door-service"></a><a name="frontdoor"></a>Serviço de porta frontal do Azure
O Azure Front Door Service permite que você defina, gerencie e monitore o roteamento global para seu tráfego da Web otimizando para melhor desempenho e failover global instantâneo para ter alta disponibilidade. Com o Front Door, é possível transformar seus aplicativos consumidores e empresariais globais (de várias regiões) em modernos aplicativos robustos altamente personalizados e com alto desempenho, APIs e conteúdo que alcançam um público global com o Azure. Para obter mais informações, consulte [Azure front door](../frontdoor/front-door-overview.md).


### <a name="traffic-manager"></a><a name="trafficmanager"></a>Gerenciador de tráfego

O Gerenciador de tráfego do Azure é um balanceador de carga de tráfego baseado em DNS que permite que você distribua o tráfego de maneira ideal para serviços em todas as regiões globais do Azure, fornecendo alta disponibilidade e capacidade de resposta. O Gerenciador de tráfego fornece uma variedade de métodos de roteamento de tráfego para distribuir tráfego, como prioridade, peso, desempenho, geográfico, vários valores ou sub-rede. Para obter mais informações sobre métodos de roteamento de tráfego, consulte [métodos de roteamento do Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md).

O diagrama a seguir mostra o roteamento baseado em prioridade de ponto de extremidade com o Gerenciador de tráfego:

![Método de roteamento de tráfego por “Prioridade” do Gerenciador de Tráfego do Azure](./media/networking-overview/priority.png)

Para obter mais informações sobre o Gerenciador de tráfego, consulte [o que é o Gerenciador de tráfego do Azure?](../traffic-manager/traffic-manager-overview.md)

### <a name="load-balancer"></a><a name="loadbalancer"></a>Load Balancer
O Azure Load Balancer fornece balanceamento de carga de Camada 4 de baixa latência e alto desempenho para todos os protocolos TCP e UDP. Ele gerencia conexões de entrada e saída. Você pode configurar pontos de extremidade com balanceamento de carga públicos e internos. Você também pode definir regras para mapear conexões de entrada para destinos de pool de back-end usando opções de investigação de integridade TCP e HTTP para gerenciar a disponibilidade do serviço. Para saber mais sobre o balanceador de carga, leia o artigo [Visão geral sobre o balanceador de carga](../load-balancer/load-balancer-overview.md).

A imagem a seguir mostra um aplicativo de várias camadas voltado para a Internet que usa balanceadores de carga internos e externos:

![Exemplo de Azure Load Balancer](./media/networking-overview/load-balancer.png)


### <a name="application-gateway"></a><a name="applicationgateway"></a>Gateway de Aplicativo
O Gateway de Aplicativo do Azure é um balanceador de carga do tráfego da Web que permite que você gerencie o tráfego para seus aplicativos Web. É um ADC (controlador de entrega de aplicativos) como um serviço, oferecendo vários recursos de balanceamento de carga de camada 7 para seus aplicativos. Para obter mais informações, consulte [o que é aplicativo Azure gateway?](../application-gateway/overview.md).

O diagrama a seguir mostra o roteamento baseado em caminho de URL com o gateway de aplicativo.

![Exemplo de gateway de aplicativo](./media/networking-overview/figure1-720.png)

## <a name="network-monitoring-services"></a><a name="monitor"></a>Serviços de monitoramento de rede
Esta seção descreve os serviços de rede no Azure que ajudam a monitorar seus recursos de rede-observador de rede, monitor de ExpressRoute, Azure Monitor e toque de rede virtual.

|Serviço|Por que usar?|Cenário|
|---|---|---|
|[Observador de Rede](#networkwatcher)|Ajuda a monitorar e solucionar problemas de conectividade, ajuda a diagnosticar problemas de VPN, NSG e roteamento, capturar pacotes em sua VM, automatiza o disparo de ferramentas de diagnóstico usando Azure Functions e aplicativos lógicos|<p>[Diagnosticar problema de filtro de tráfego da VM](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md)</p><p>[Diagnosticar problemas de roteamento de VM](../network-watcher/diagnose-vm-network-routing-problem.md)</p><p>[Monitorar comunicações entre VMs](../network-watcher/connection-monitor.md)</p><p>[Diagnosticar um problema de comunicação entre redes](../network-watcher/diagnose-communication-problem-between-networks.md)</p><p>[Registrar em log o tráfego de rede bidirecionalmente em uma VM](../network-watcher/network-watcher-nsg-flow-logging-portal.md)</p>|
|[Monitor do ExpressRoute](#expressroutemonitor)|Fornece monitoramento em tempo real de desempenho, disponibilidade e utilização de rede, ajuda com a descoberta automática de topologia de rede, fornece um isolamento de falhas mais rápido, detecta problemas de rede transitórios, ajuda a analisar as características históricas de desempenho de rede, dá suporte a várias assinaturas|<p>[Configurar o Monitor de Desempenho de Rede para ExpressRoute](../expressroute/how-to-npm.md)</p><p>[Monitoramento, alertas e métricas do ExpressRoute](../expressroute/expressroute-monitoring-metrics-alerts.md)</p>|
|[Azure Monitor](#azuremonitor)|Ajuda a entender como os aplicativos estão sendo executados e identifica de forma proativa os problemas que os afetam e os recursos dos quais eles dependem.|<p>[Alertas e métricas do Gerenciador de Tráfego](../traffic-manager/traffic-manager-metrics-alerts.md)</p><p>[Diagnóstico do Azure monitor para Standard Load Balancer](../load-balancer/load-balancer-standard-diagnostics.md)</p><p>[Monitorar os logs e as métricas do Firewall do Azure](../firewall/tutorial-diagnostics.md)</p><p>[Firewall do aplicativo web do Azure de monitoramento e registro em log](../frontdoor/waf-front-door-monitor.md)</p>|
|[TOQUE da rede virtual](#vnettap)|Fornece streaming contínuo de tráfego de rede de máquina virtual para coletor de pacotes, permite soluções de gerenciamento de desempenho de aplicativos e de rede e ferramentas de análise de segurança|[Criar um recurso de toque de VNet](../virtual-network/tutorial-tap-virtual-network-cli.md)|
|

### <a name="network-watcher"></a><a name="networkwatcher"></a>Observador de Rede
Observador de Rede do Azure fornece ferramentas para monitorar, diagnosticar, exibir métricas e ativar ou desativar os logs de recursos em uma rede virtual do Azure. Para obter mais informações, consulte [o que é o observador de rede?](../network-watcher/network-watcher-monitoring-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="expressroute-monitor"></a><a name="expressroutemonitor"></a>Monitor do ExpressRoute
Para saber mais sobre como exibir as métricas de circuito do ExpressRoute, logs de recursos e alertas, consulte [monitoramento, métricas e alertas do expressroute](../expressroute/expressroute-monitoring-metrics-alerts.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="azure-monitor"></a><a name="azuremonitor"></a>Azure Monitor
O Azure Monitor maximiza a disponibilidade e o desempenho de seus aplicativos fornecendo uma solução abrangente para coletar, analisar e agir em relação a dados telemétricos de seus ambientes locais e de nuvem. Ele ajuda a entender o desempenho de seus aplicativos, além de identificar de maneira proativa os problemas que os estão afetando e os recursos dos quais eles dependem. Para obter mais informações, consulte [Azure monitor visão geral](../azure-monitor/overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="virtual-network-tap"></a><a name="vnettap"></a>TOQUE da rede virtual
O TAP (Ponto de Acesso do Terminal) de rede virtual do Azure permite que você transmita o tráfego de rede por streaming continuamente da máquina virtual para uma ferramenta de coleta de pacotes ou de análise de rede. A ferramenta de análise ou de coleta é fornecida por um parceiro de [solução de virtualização de rede](https://azure.microsoft.com/solutions/network-appliances/). 

A imagem seguir mostra como o TAP de rede virtual funciona. 

![Como o TAP de rede virtual funciona](./media/networking-overview/virtual-network-tap-architecture.png)

Para obter mais informações, consulte [o que é toque de rede virtual](../virtual-network/virtual-network-tap-overview.md).

## <a name="next-steps"></a>Próximas etapas

- Criar sua primeira VNet e conecte algumas VMs a ela executando as etapas no artigo [Criar sua primeira rede virtual](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).
- Conecte seu computador a uma VNet concluindo as etapas no [artigo configurar uma conexão ponto a site](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).
- Faça o balanceamento de carga do tráfego de Internet para servidores públicos concluindo as etapas em [Criar um balanceador de carga voltado para a Internet](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).
 
 
   
