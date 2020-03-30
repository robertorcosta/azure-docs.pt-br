---
title: Visão geral dos serviços de rede do Azure
description: Conheça os serviços de rede no Azure e seus recursos - serviços de conectividade, serviços de proteção de aplicativos, serviços de entrega de aplicativos e monitoramento de rede.
services: networking
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 03/12/2020
ms.author: kumud
ms.openlocfilehash: 90eddea839d2f6ae5235ac6a391b40dd667ab401
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257828"
---
# <a name="azure-networking-services-overview"></a>Visão geral dos serviços de rede do Azure

Os serviços de rede no Azure fornecem uma variedade de recursos de rede que podem ser usados em conjunto ou separadamente. Clique em qualquer um dos seguintes recursos principais para saber mais sobre eles:
- [**Serviços de conectividade**](#connect): Conecte os recursos do Azure e os recursos locais usando qualquer ou uma combinação desses serviços de rede no Azure - Virtual Network (VNet), Virtual WAN, ExpressRoute, VPN Gateway, Virtual Network NAT Gateway, Azure DNS, Peering service e Azure Bastion.
- [**Serviços de proteção de aplicativos**](#protect) Proteja seus aplicativos usando qualquer ou uma combinação desses serviços de rede no Azure - Private Link, proteção DDoS, Firewall, Grupos de Segurança de Rede, Firewall de Aplicativos da Web e Endpoints de Rede Virtual.
- [**Serviços de entrega de aplicativos**](#deliver) Ofereça aplicativos na rede Azure usando qualquer ou uma combinação desses serviços de rede no Azure - Content Delivery Network (CDN), Azure Front Door Service, Traffic Manager, Application Gateway, Internet Analyzer e Load Balancer.
- [**Monitoramento de rede**](#monitor) – Monitore seus recursos de rede usando qualquer ou uma combinação desses serviços de rede no Azure - Network Watcher, ExpressRoute Monitor, Azure Monitor ou VNet Terminal Access Point (TAP).

## <a name="connectivity-services"></a><a name="connect"></a>Serviços de conectividade
 
Esta seção descreve serviços que fornecem conectividade entre os recursos do Azure, conectividade de uma rede local aos recursos do Azure e conectividade de ramificação em Azure - Virtual Network (VNet), Virtual WAN, ExpressRoute, VPN Gateway, Rede virtual NAT Gateway, Azure DNS, serviço de peering Azure e Bastião Azure.

|Serviço|Por que usar?|Cenários|
|---|---|---|
|[Rede virtual](#vnet)|Permite que os recursos do Azure se comuniquem com segurança entre si, com a internet e as redes locais.| <p>[Filtrar tráfego de rede](../virtual-network/tutorial-filter-network-traffic.md)</p> <p>[Rotear o tráfego de rede](../virtual-network/tutorial-create-route-table-portal.md)</p> <p>[Restringir o acesso à rede para os recursos](../virtual-network/tutorial-restrict-network-access-to-resources.md)</p> <p>[Conectar redes virtuais](../virtual-network/tutorial-connect-virtual-networks-portal.md)</p>|
|[ExpressRoute](#expressroute)|Estende suas redes locais para a nuvem da Microsoft por uma conexão privada facilitada por um provedor de conectividade.|<p>[Criar e modificar um circuito do ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)</p> <p>[Criar e modificar o emparelhamento de um circuito do ExpressRoute](../expressroute/expressroute-howto-routing-portal-resource-manager.md)</p> <p>[Vincular uma rede virtual a um circuito do ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)</p> <p>[Configure e gerencie filtros de rota para circuitos ExpressRoute](../expressroute/how-to-routefilter-portal.md)</p>|
|[VPN Gateway](#vpngateway)|Envia tráfego criptografado entre uma rede virtual Do Zure e um local local no local pela Internet pública.|<p>[Conexões site-a-site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)</p> <p>[Conexões VNet-para-VNet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)</p> <p>[Conexões ponto a ponto](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)</p>|
|[WAN Virtual](#virtualwan)|Otimiza e automatiza a conectividade de ramificações para, e através, azure. As regiões do Azure servem como hubs para os quais é possível optar por conectar suas ramificações.|<p>[Conexões site-to-site,](../virtual-wan/virtual-wan-site-to-site-portal.md) [conexões ExpressRoute](../virtual-wan/virtual-wan-expressroute-portal.md)</p>|
|[Azure DNS](#dns)|Hospeda domínios DNS que fornecem resolução de nomes usando a infra-estrutura do Microsoft Azure.|<p>[Hospede seu domínio no Azure DNS](../dns/dns-delegate-domain-azure-dns.md)</p><p>[Crie registros DeDNS para um aplicativo web](../dns/dns-web-sites-custom-domain.md)</p> <p>[Crie um registro de alias para gerenciador de tráfego](../dns/tutorial-alias-tm.md)</p> <p>[Crie um registro de alias para endereço IP público](../dns/tutorial-alias-pip.md)</p> <p>[Crie um registro de alias para registro de recursos de zona](../dns/tutorial-alias-rr.md)</p>|
|[Azure Bastion](#bastion)|Ele fornece conectividade de RDP/SSH contínua e segura a suas máquinas virtuais, diretamente no portal do Azure, usando SSL. Quando você se conecta via Azure Bastion, suas máquinas virtuais não precisam de um endereço IP público|<p>[Crie um host Azure Bastion](../bastion/bastion-create-host-portal.md)</p><p>[Conecte-se usando SSH a um VM Linux](../bastion/bastion-connect-vm-ssh.md)</p><p>[Conecte-se usando RDP a uma VM do Windows](../bastion/bastion-connect-vm-rdp.md)</p>|
|[Gateway NAT de rede virtual](#nat)|Crie um gateway NAT para fornecer conectividade de saída para uma máquina virtual.|<p>[Criar um Gateway NAT](../virtual-network/quickstart-create-nat-gateway-portal.md)</p>|
|[Serviço de peering do Azure (Visualização)](#azurepeeringservice)|Colabore com provedores de serviços para um roteamento ideal e confiável para a nuvem da Microsoft na rede pública.|<p>[Registre o serviço de peering do Azure](../peering-service/azure-portal.md)</p>|
||||


### <a name="virtual-network"></a><a name="vnet"></a>Rede virtual

A Rede Virtual do Azure (VNet) é o bloco de construção fundamental de sua rede privada no Azure. Você pode usar um VNets para:
- **Comunique-se entre os recursos do Azure**: Você pode implantar VMs e vários outros tipos de recursos do Azure para uma rede virtual, como ambientes de serviço de aplicativos do Azure, o Azure Kubernetes Service (AKS) e os conjuntos de escala de máquinas virtuais do Azure. Para exibir uma lista completa de recursos do Azure que podem ser implantados em uma rede virtual, confira [Integração de serviços de rede virtual](../virtual-network/virtual-network-for-azure-services.md).
- **Comuniquem-se entre si**: Você pode conectar redes virtuais entre si, permitindo que recursos em qualquer rede virtual se comuniquem entre si, usando peering de rede virtual. As redes virtuais que você conecta podem estar na mesma região ou em regiões diferentes do Azure. Para obter mais informações, consulte [Emparelhamento de rede virtual do Azure](../virtual-network/virtual-network-peering-overview.md).
- **Comunique-se com a internet**: Todos os recursos em um VNet podem se comunicar de saída para a internet, por padrão. Você pode se comunicar na entrada com um recurso, atribuindo um endereço IP público ou um Load Balancer público. Você também pode usar [endereços IP públicos](../virtual-network/virtual-network-public-ip-address.md) ou [balanceador](../load-balancer/load-balancer-overview.md) de carga público para gerenciar suas conexões de saída.
- **Comunique-se com redes locais**: Você pode conectar seus computadores e redes locais a uma rede virtual usando [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou [ExpressRoute](../expressroute/expressroute-introduction.md).

Para obter mais informações, consulte [O que é a Rede Virtual Do Azure?](../virtual-network/virtual-networks-overview.md).

### <a name="expressroute"></a><a name="expressroute"></a>ExpressRoute
O ExpressRoute permite que você amplie suas redes locais na nuvem da Microsoft por uma conexão privada facilitada por um provedor de conectividade. Essa conexão é privada. O tráfego não passa pela Internet. Com o ExpressRoute, você pode estabelecer conexões com os serviços de nuvem da Microsoft, como o Microsoft Azure, o Office 365 e o Dynamic 365.  Para obter mais informações, consulte [O que é ExpressRoute?](../expressroute/expressroute-introduction.md).

![Azure ExpressRoute](./media/networking-overview/expressroute-connection-overview.png)

### <a name="vpn-gateway"></a><a name="vpngateway"></a>VPN Gateway
O VPN Gateway ajuda você a criar conexões entre instalações criptografadas para sua rede virtual a partir de locais locais locais ou criar conexões criptografadas entre VNets. Existem diferentes configurações disponíveis para conexões VPN Gateway, tais como, site-to-site, ponto a site ou VNet para VNet.
O diagrama a seguir ilustra várias conexões VPN de site para site para a mesma rede virtual.

![Conexões do Gateway Azure VPN site-to-site](./media/networking-overview/vpngateway-multisite-connection-diagram.png)

Para obter mais informações sobre diferentes tipos de conexões VPN, consulte [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="virtual-wan"></a><a name="virtualwan"></a>WAN Virtual
WAN Virtual do Azure é um serviço de rede que fornece conectividade de ramificação otimizada e automatizada para e por meio do Azure. As regiões do Azure servem como hubs para os quais é possível optar por conectar suas ramificações. Você pode aproveitar o backbone do Azure para também conectar as ramificações e aproveitar a conectividade de ramificação a VNet. O Azure Virtual WAN reúne muitos serviços de conectividade em nuvem do Azure, como VPN local-a-site, ExpressRoute, VPN de usuário ponto a ponto em uma única interface operacional. A conectividade com VNets do Azure é estabelecida usando conexões de rede virtual. Para obter mais informações, consulte [O que é WAN virtual do Azure?](../virtual-wan/virtual-wan-about.md).

![Diagrama de WAN virtual](./media/networking-overview/virtualwan1.png)

### <a name="azure-dns"></a><a name="dns"></a>Azure DNS
O DNS do Azure é um serviço de hospedagem para domínios DNS que fornece a resolução de nomes usando a infraestrutura do Microsoft Azure. Ao hospedar seus domínios no Azure, você pode gerenciar seus registros DNS usando as mesmas credenciais, APIs, ferramentas e faturamento que os outros serviços do Azure. Para obter mais informações, consulte [O que é O Azure DNS?](../dns/dns-overview.md).

### <a name="azure-bastion"></a><a name="bastion"></a>Azure Bastion
O serviço Azure Bastion é um novo serviço de PaaS, totalmente gerenciado pela plataforma e provisionado dentro de sua rede virtual. Ele fornece conectividade de RDP/SSH contínua e segura a suas máquinas virtuais, diretamente no portal do Azure, usando SSL. Quando você se conecta usando o Azure Bastion, suas máquinas virtuais não precisam de um endereço IP público. Para obter mais informações, consulte [O que é O Bastião do Azure?](../bastion/bastion-overview.md).

![Arquitetura Azure Bastion](./media/networking-overview/architecture.png)

### <a name="virtual-network-nat-gateway"></a><a name="nat"></a>Gateway NAT de rede virtual
A Rede Virtual NAT (tradução de endereços de rede) simplifica a conectividade de Internet somente de saída para redes virtuais. Quando configurado em uma sub-rede, toda a conectividade de saída usa seus endereços IP públicos estáticos especificados. A conectividade de saída é possível sem balanceador de carga ou endereços IP públicos diretamente ligados a máquinas virtuais. Para obter mais informações, consulte [O que é gateway NAT de rede virtual?](../virtual-network/nat-overview.md) 

![Gateway NAT de rede virtual](./media/networking-overview/flow-map.png)

### <a name="azure-peering-service"></a><a name="azurepeeringservice"></a>Serviço de peering azure
O serviço Azure Peering melhora a conectividade do cliente com serviços de nuvem da Microsoft, como Office 365, Dynamics 365, software as a service (SaaS), Azure ou quaisquer serviços da Microsoft acessíveis via internet pública. Para obter mais informações, consulte [O que é o Serviço de Peering do Azure?](../peering-service/about.md).

## <a name="application-protection-services"></a><a name="protect"></a>Serviços de proteção de aplicativos

Esta seção descreve serviços de rede no Azure que ajudam a proteger seus recursos de rede - Proteja seus aplicativos usando qualquer ou uma combinação desses serviços de rede no Azure - Private Link, proteção DDoS, Firewall, Grupos de Segurança de Rede, Web Firewall de aplicativos e pontos finais de rede virtual.

|Serviço|Por que usar?|Cenário|
|---|---|---|
|[Proteção contra DDOS](#ddosprotection) |Alta disponibilidade para seus aplicativos com proteção contra excesso de taxas de tráfego IP|[Gerenciar a proteção DDoS do Azure](../virtual-network/manage-ddos-protection.md)|
|[Firewall de aplicativos web](#waf)|<p>[O Azure WAF com o Application Gateway](../web-application-firewall/ag/ag-overview.md) oferece proteção regional a entidades em espaço de endereços públicos e privados</p><p>[O Azure WAF com porta frontal](../web-application-firewall/afds/afds-overview.md) fornece proteção na borda da rede para pontos finais públicos.</p>|<p>[Configurar regras de proteção de bots](../frontdoor/waf-front-door-policy-configure-bot-protection.md)</p> <p>[Configurar o código de resposta personalizada](../frontdoor/waf-front-door-configure-custom-response-code.md)</p> <p>[Configure regras de restrição de IP](../frontdoor/waf-front-door-configure-ip-restriction.md)</p> <p>[Configurar regra de limite de taxa](../frontdoor/waf-front-door-rate-limit-powershell.md)</p> |
|[Firewall do Azure](#firewall)|Firewall do Azure é um serviço de segurança de rede gerenciado e baseado em nuvem que protege seus recursos de Rede Virtual do Azure. É um firewall como serviço totalmente com estado com alta disponibilidade interna e escalabilidade de nuvem irrestrita.|<p>[Implantar um Firewall Azure em uma Vnet](../firewall/tutorial-firewall-deploy-portal.md)</p> <p>[- Implantar um Firewall Azure em uma rede híbrida](../firewall/tutorial-hybrid-ps.md)</p> <p>[Filtrar o tráfego de entrada com o DNAT do Firewall Do Azure](../firewall/tutorial-firewall-dnat.md)</p>|
|[Grupos de segurança de rede](#nsg)|Controle total do nó final distribuído granular na VM/sub-rede para todos os fluxos de tráfego de rede|[Filtrar o tráfego de rede usando grupos de segurança de rede](../virtual-network/tutorial-filter-network-traffic.md)|
|[Pontos finais de serviço de rede virtual](#serviceendpoints)|Permite limitar o acesso à rede a alguns recursos de serviço do Azure a uma sub-rede virtual|[Restringir o acesso à rede para recursos PaaS](../virtual-network/tutorial-restrict-network-access-to-resources-powershell.md)|
[Link Privado](#privatelink)|Permite que você acesse os Serviços Azure PaaS (por exemplo, O Armazenamento do Azure e o Banco de Dados SQL) e o Azure hospedou serviços de propriedade do cliente/parceiro em um ponto final privado em sua rede virtual.|<p>[Crie um ponto final privado](../private-link/create-private-endpoint-portal.md)</p><p>[Crie um serviço de Link Privado](../private-link/create-private-link-service-portal.md)</p>|
|||
### <a name="ddos-protection"></a><a name="ddosprotection"></a>Proteção DDoS 
[O Azure DDoS Protection](../virtual-network/manage-ddos-protection.md) fornece contramedidas contra as ameaças DDoS mais sofisticadas. O serviço fornece recursos aprimorados de mitigação DDoS para seus aplicativos e recursos implantados em suas redes virtuais. Além disso, os clientes que usam o Azure DDoS Protection têm acesso ao suporte ao DDoS Rapid Response para contratar especialistas em DDoS durante um ataque ativo.

![Proteção contra DDoS](./media/networking-overview/ddos-protection.png)

### <a name="web-application-firewall"></a><a name="waf"></a>Firewall de aplicativos web

[O WaF (Azure Web Application Firewall, firewall de aplicativos da Web)](../web-application-firewall/overview.md) fornece proteção aos seus aplicativos web contra explorações e vulnerabilidades comuns da Web, como injeção de SQL e scripting entre sites. O Azure WAF fornece proteção fora da caixa das 10 principais vulnerabilidades do OWASP através de regras gerenciadas. Além disso, os clientes também podem configurar regras personalizadas, que são regras gerenciadas pelo cliente para fornecer proteção adicional com base no intervalo de IP de origem e solicitar atributos como cabeçalhos, cookies, campos de dados de formulário ou parâmetros de seqüência de consultas.

Os clientes podem optar por implantar [o Azure WAF com o Application Gateway,](../application-gateway/waf-overview.md) que fornece proteção regional para entidades em espaço de endereços públicos e privados. Os clientes também podem optar por implantar [o Azure WAF com a Porta Frontal,](../frontdoor/waf-overview.md) que fornece proteção na borda da rede para pontos finais públicos.

![Firewall do Aplicativo Web](./media/networking-overview/waf-overview.png)


### <a name="azure-firewall"></a><a name="firewall"></a>Firewall do Azure
Firewall do Azure é um serviço de segurança de rede gerenciado e baseado em nuvem que protege seus recursos de Rede Virtual do Azure. Usando o Firewall Do Azure, você pode criar, impor e registrar políticas de conectividade de aplicativos e redes de aplicativos e aplicativos em assinaturas e redes virtuais. O Firewall do Azure usa um endereço IP público estático para seus recursos de rede virtual, permitindo que firewalls externos identifiquem o tráfego originário de sua rede virtual. 

Para obter mais informações sobre o Azure Firewall, consulte a documentação do [Firewall do Azure](../firewall/overview.md).

![Visão geral do firewall](./media/networking-overview/firewall-threat.png)

### <a name="network-security-groups"></a><a name="nsg"></a>Grupos de segurança de rede
Você pode filtrar o tráfego de rede de e para recursos do Azure em uma rede virtual do Azure com um grupo de segurança de rede. Para obter mais informações, consulte [Visão geral de segurança](../virtual-network/security-overview.md).

### <a name="service-endpoints"></a><a name="serviceendpoints"></a>Pontos finais de serviço
Os pontos de extremidade de serviço de VNet (rede virtual) estendem o espaço de endereço privado e a identidade da sua rede virtual para os serviços do Azure por meio de uma conexão direta. Os pontos de extremidade permitem que você possa garantir os recursos essenciais do serviço do Azure somente para suas redes virtuais. O tráfego de sua rede virtual para o serviço do Azure sempre permanece na rede de backbone do Microsoft Azure. Para obter mais informações, consulte [Pontos de extremidade de serviço de rede virtual](../virtual-network/virtual-network-service-endpoints-overview.md).

![Pontos de extremidade de serviço de rede virtual](./media/networking-overview/vnet-service-endpoints-overview.png)

### <a name="azure-private-link"></a><a name="privatelink"></a>Link Privado do Azure
[O Azure Private Link](../private-link/private-link-overview.md) permite que você acesse os Serviços Azure PaaS (por exemplo, O Banco de Armazenamento do Azure e O Banco de Dados SQL) e o Azure hospedou serviços de propriedade/parceiro do cliente em um ponto final privado em sua rede virtual.
O tráfego entre sua rede virtual e o serviço viaja pela rede backbone da Microsoft. Expor seu serviço à internet pública não é mais necessário. Você pode criar seu próprio serviço de link privado em sua rede virtual e entregá-lo aos seus clientes.

![Visão geral do ponto de extremidade privado](./media/networking-overview/private-endpoint.png)


## <a name="application-delivery-services"></a><a name="deliver"></a>Serviços de entrega de aplicativos

Esta seção descreve serviços de rede no Azure que ajudam a fornecer aplicativos - Network Watcher, ExpressRoute Monitor, Azure Monitor ou VNet Terminal Access Point (TAP).

|Serviço|Por que usar?|Cenário|
|---|---|---|
|[Rede de entrega de conteúdo](#cdn)|Fornece conteúdo de alta largura de banda aos usuários. Os CDNs armazenam conteúdo armazenado em cache em servidores de borda em locais de ponto de presença (POP) próximos aos usuários finais, para minimizar a latência|<p>[Adicionar CDN a um aplicativo web](../cdn/cdn-add-to-web-app.md)</p> <p>[- Acessar bolhas de armazenamento usando um domínio personalizado Do CDN do Azure sobre HTTPS](..//cdn/cdn-storage-custom-domain-https.md)</p> <p>[Adicione um domínio personalizado ao seu ponto final do CDN do Azure](../cdn/cdn-map-content-to-custom-domain.md)</p> <p>[Configure HTTPS em um domínio personalizado do CDN do Azure](../cdn/cdn-custom-ssl.md?tabs=option-1-default-enable-https-with-a-cdn-managed-certificate)</p>|
|[Azure Front Door Service](#frontdoor)|Permite que você defina, gerencie e monitore o roteamento global para o tráfego da Web, otimizando para o melhor desempenho e failover global instantâneo para alta disponibilidade.|<p>[Adicione um domínio personalizado ao seu Serviço de Porta Frontal do Azure](../frontdoor/front-door-custom-domain.md)</p> <p>[Configurar HTTPS em um domínio personalizado do Front Door](../frontdoor/front-door-custom-domain-https.md)</p><p>[Configure a política de firewall de aplicativos web de filtragem geo-filtrante](../frontdoor/front-door-tutorial-geo-filtering.md)|
|[Gerente de Tráfego](#trafficmanager)|Distribui tráfego baseado em DNS para serviços em todas as regiões globais do Azure, ao mesmo tempo em que oferece alta disponibilidade e capacidade de resposta|<p> [Rotear tráfego de baixa latência](../traffic-manager/tutorial-traffic-manager-improve-website-response.md)</p><p>[Rotear tráfego para um ponto de extremidade de prioridade](../traffic-manager/traffic-manager-configure-priority-routing-method.md)</p><p> [Controlar o tráfego com pontos de extremidade ponderados](../traffic-manager/tutorial-traffic-manager-weighted-endpoint-routing.md)</p><p>[Tráfego de rota com base na localização geográfica do ponto final](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)</p> <p> [Rotear o tráfego com base na sub-rede do usuário](../traffic-manager/tutorial-traffic-manager-subnet-routing.md)</p>|
|[Balanceador de carga](#loadbalancer)|Fornece balanceamento de carga regional roteando tráfego através de zonas de disponibilidade e em seus VNets. Fornece balanceamento interno de carga roteando tráfego entre e entre seus recursos para construir seu aplicativo regional.|<p> [Balancear tráfego de carga da internet para VMs](../load-balancer/tutorial-load-balancer-standard-manage-portal.md)</p> <p>[Tráfego de equilíbrio de carga em VMs dentro de uma rede virtual](../load-balancer/tutorial-load-balancer-basic-internal-portal.md)<p>[Encaminhar tráfego porto para uma porta específica em VMs específicos](../load-balancer/tutorial-load-balancer-port-forwarding-portal.md)</p><p> [Configure regras de balanceamento e saída de carga](../load-balancer/configure-load-balancer-outbound-cli.md)</p>|
|[Gateway de aplicativo](#applicationgateway)|O Gateway de Aplicativo do Azure é um balanceador de carga do tráfego da Web que permite que você gerencie o tráfego para seus aplicativos Web.|<p>[Direcionar o tráfego da Web com o Gateway de Aplicativo do Azure](../application-gateway/quick-create-portal.md)</p><p>[Configurar um gateway de aplicativo com terminação SSL](../application-gateway/create-ssl-portal.md)</p><p>[Criar um gateway de aplicativo com o redirecionamento baseado em caminhos de URL](../application-gateway/create-url-route-portal.md) </p>|
|

### <a name="content-delivery-network"></a><a name="cdn"></a>Rede de entrega de conteúdo
A CDN oferece aos desenvolvedores uma solução global de fornecimento rápido de conteúdo de alta largura de banda para usuários armazenando em cache o conteúdo em nós físicos estrategicamente posicionados em todo o mundo. Para obter mais informações sobre o Azure CDN, consulte [Azure Content Delivery Network](../cdn/cdn-overview.md)

![CDN do Azure](./media/networking-overview/cdn-overview.png)

### <a name="azure-front-door-service"></a><a name="frontdoor"></a>Serviço de porta da frente do Azure
O Azure Front Door Service permite que você defina, gerencie e monitore o roteamento global para seu tráfego da Web otimizando para melhor desempenho e failover global instantâneo para ter alta disponibilidade. Com o Front Door, é possível transformar seus aplicativos consumidores e empresariais globais (de várias regiões) em modernos aplicativos robustos altamente personalizados e com alto desempenho, APIs e conteúdo que alcançam um público global com o Azure. Para obter mais informações, consulte [Azure Front Door](../frontdoor/front-door-overview.md).


### <a name="traffic-manager"></a><a name="trafficmanager"></a>Gerente de Tráfego

O Gerenciador de tráfego do Azure é um balanceador de carga de tráfego baseado em DNS que permite que você distribua o tráfego de maneira ideal para serviços em todas as regiões globais do Azure, fornecendo alta disponibilidade e capacidade de resposta. O Traffic Manager fornece uma série de métodos de roteamento de tráfego para distribuir tráfego, como prioridade, ponderado, desempenho, geográfico, multivalor ou sub-rede. Para obter mais informações sobre métodos de roteamento de tráfego, consulte [métodos de roteamento do Gerenciador de Tráfego](../traffic-manager/traffic-manager-routing-methods.md).

O diagrama a seguir mostra roteamento baseado em prioridade de ponto final com o Gerenciador de Tráfego:

![Método de roteamento de tráfego por “Prioridade” do Gerenciador de Tráfego do Azure](./media/networking-overview/priority.png)

Para obter mais informações sobre o Traffic Manager, consulte [O que é o Gerente de Tráfego do Azure?](../traffic-manager/traffic-manager-overview.md)

### <a name="load-balancer"></a><a name="loadbalancer"></a>Balanceador de carga
O Azure Load Balancer fornece balanceamento de carga de Camada 4 de baixa latência e alto desempenho para todos os protocolos TCP e UDP. Ele gerencia conexões de entrada e saída. Você pode configurar pontos de extremidade com balanceamento de carga públicos e internos. Você também pode definir regras para mapear conexões de entrada para destinos de pool de back-end usando opções de investigação de integridade TCP e HTTP para gerenciar a disponibilidade do serviço. Para saber mais sobre o balanceador de carga, leia o artigo [Visão geral sobre o balanceador de carga](../load-balancer/load-balancer-overview.md).

A imagem a seguir mostra um aplicativo de várias camadas voltado para a Internet que usa balanceadores de carga internos e externos:

![Exemplo do Balancer de Carga Azul](./media/networking-overview/load-balancer.png)


### <a name="application-gateway"></a><a name="applicationgateway"></a>Gateway de aplicativo
O Gateway de Aplicativo do Azure é um balanceador de carga do tráfego da Web que permite que você gerencie o tráfego para seus aplicativos Web. É um ADC (Application Delivery Controller, controlador de entrega de aplicativos) como um serviço, oferecendo vários recursos de balanceamento de carga de camada 7 para seus aplicativos. Para obter mais informações, consulte [O que é gateway de aplicativo do Azure?](../application-gateway/overview.md)

O diagrama a seguir mostra roteamento baseado em caminho de URL com o Application Gateway.

![Exemplo do Gateway de aplicativos](./media/networking-overview/figure1-720.png)

## <a name="network-monitoring-services"></a><a name="monitor"></a>Serviços de monitoramento de rede
Esta seção descreve serviços de rede no Azure que ajudam a monitorar seus recursos de rede - Network Watcher, ExpressRoute Monitor, Azure Monitor e Virtual Network TAP.

|Serviço|Por que usar?|Cenário|
|---|---|---|
|[Observador de Rede](#networkwatcher)|Ajuda a monitorar e solucionar problemas de conectividade, ajuda a diagnosticar problemas de VPN, NSG e roteamento, capturar pacotes em sua VM, automatiza ferramentas de diagnóstico de acionamento usando funções do Azure e aplicativos lógicos|<p>[Diagnosticar problema de filtro de tráfego da VM](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md)</p><p>[Diagnosticar problemas de roteamento de VM](../network-watcher/diagnose-vm-network-routing-problem.md)</p><p>[Monitorar as comunicações entre VMs](../network-watcher/connection-monitor.md)</p><p>[Diagnosticar um problema de comunicação entre redes](../network-watcher/diagnose-communication-problem-between-networks.md)</p><p>[Registrar em log o tráfego de rede bidirecionalmente em uma VM](../network-watcher/network-watcher-nsg-flow-logging-portal.md)</p>|
|[ExpressRoute Monitor](#expressroutemonitor)|Fornece monitoramento em tempo real do desempenho, disponibilidade e utilização da rede, ajuda na descoberta automática da topologia da rede, fornece isolamento mais rápido de falhas, detecta problemas transitórios de rede, ajuda a analisar o desempenho histórico da rede características, suporta multi-assinatura|<p>[Configurar o Monitor de Desempenho de Rede para ExpressRoute](../expressroute/how-to-npm.md)</p><p>[Monitoramento, alertas e métricas do ExpressRoute](../expressroute/expressroute-monitoring-metrics-alerts.md)</p>|
|[Azure Monitor](#azuremonitor)|Ajuda você a entender como seus aplicativos estão funcionando e identifica proativamente os problemas que os afetam e os recursos de que dependem.|<p>[Alertas e métricas do Gerenciador de Tráfego](../traffic-manager/traffic-manager-metrics-alerts.md)</p><p>[Diagnósticos do monitor do Azure para o Balanceador de Carga Padrão](../load-balancer/load-balancer-standard-diagnostics.md)</p><p>[Monitorar os logs e as métricas do Firewall do Azure](../firewall/tutorial-diagnostics.md)</p><p>[Firewall do aplicativo web do Azure de monitoramento e registro em log](../frontdoor/waf-front-door-monitor.md)</p>|
|[TAP da rede virtual](#vnettap)|Fornece streaming contínuo de tráfego de rede de máquinas virtuais para coletor de pacotes, permite soluções de gerenciamento de desempenho de rede e aplicativos e ferramentas de análise de segurança|[Crie um recurso VNet TAP](../virtual-network/tutorial-tap-virtual-network-cli.md)|
|

### <a name="network-watcher"></a><a name="networkwatcher"></a>Observador de Rede
Observador de Rede do Azure fornece ferramentas para monitorar, diagnosticar, exibir métricas e ativar ou desativar os logs de recursos em uma rede virtual do Azure. Para obter mais informações, consulte [O que é O Observador de Rede?](../network-watcher/network-watcher-monitoring-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="expressroute-monitor"></a><a name="expressroutemonitor"></a>ExpressRoute Monitor
Para saber como visualizar métricas do circuito ExpressRoute, registros de diagnóstico e alertas, consulte [monitoramento, métricas e alertas do ExpressRoute.](../expressroute/expressroute-monitoring-metrics-alerts.md?toc=%2fazure%2fnetworking%2ftoc.json)
### <a name="azure-monitor"></a><a name="azuremonitor"></a>Azure Monitor
O Azure Monitor maximiza a disponibilidade e o desempenho de seus aplicativos fornecendo uma solução abrangente para coletar, analisar e agir em relação a dados telemétricos de seus ambientes locais e de nuvem. Ele ajuda a entender o desempenho de seus aplicativos, além de identificar de maneira proativa os problemas que os estão afetando e os recursos dos quais eles dependem. Para obter mais informações, consulte [Azure Monitor Overview](../azure-monitor/overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="virtual-network-tap"></a><a name="vnettap"></a>TAP da rede virtual
O TAP (Ponto de Acesso do Terminal) de rede virtual do Azure permite que você transmita o tráfego de rede por streaming continuamente da máquina virtual para uma ferramenta de coleta de pacotes ou de análise de rede. A ferramenta de análise ou de coleta é fornecida por um parceiro de [solução de virtualização de rede](https://azure.microsoft.com/solutions/network-appliances/). 

A imagem seguir mostra como o TAP de rede virtual funciona. 

![Como o TAP de rede virtual funciona](./media/networking-overview/virtual-network-tap-architecture.png)

Para obter mais informações, consulte [O que é rede virtual TAP](../virtual-network/virtual-network-tap-overview.md).

## <a name="next-steps"></a>Próximas etapas

- Criar sua primeira VNet e conecte algumas VMs a ela executando as etapas no artigo [Criar sua primeira rede virtual](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).
- Conecte seu computador a um VNet completando as etapas no [Configure um artigo de conexão ponto a local](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).
- Faça o balanceamento de carga do tráfego de Internet para servidores públicos concluindo as etapas em [Criar um balanceador de carga voltado para a Internet](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).
 
 
   
