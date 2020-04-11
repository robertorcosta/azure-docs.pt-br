---
title: 'Arquitetura: Arquitetura de rede de trânsito global'
titleSuffix: Azure Virtual WAN
description: Conheça a arquitetura global da rede de trânsito para WAN Virtual
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 02/06/2020
ms.author: cherylmc
ms.openlocfilehash: 9515058bc78a2d56dc1734c046dac5d5b04f68d9
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113173"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>Arquitetura de rede de trânsito global e WAN virtual

As empresas modernas exigem conectividade onipresente entre aplicativos, dados e usuários hiperdistribuídos em toda a nuvem e no local. A arquitetura de rede de trânsito global está sendo adotada pelas empresas para consolidar, conectar e controlar a pegada de TI corporativa moderna e global centrada na nuvem.

A arquitetura de rede de trânsito global é baseada em um modelo clássico de conectividade de hub e spoke, onde o 'hub' de rede hospedada em nuvem permite conectividade transitiva entre pontos finais que podem ser distribuídos entre diferentes tipos de 'raios'.

Neste modelo, um discurso pode ser:
* Rede virtual (VNets)
* Site de ramificação física
* Usuário remoto
* Internet

![hub e falou](./media/virtual-wan-global-transit-network-architecture/figure1.png)

**Figura 1: Rede global de hub-and-spoke de trânsito**

A Figura 1 mostra a visão lógica da rede de trânsito global onde usuários geograficamente distribuídos, sites físicos e VNets são interconectados através de um hub de rede hospedado na nuvem. Essa arquitetura permite conectividade lógica de trânsito de um salto entre os pontos finais da rede.

## <a name="global-transit-network-with-virtual-wan"></a><a name="globalnetworktransit"></a>Rede de trânsito global com WAN Virtual

O Azure Virtual WAN é um serviço de rede em nuvem gerenciado pela Microsoft. Todos os componentes de rede dos que este serviço é composto são hospedados e gerenciados pela Microsoft. Para obter mais informações sobre a WAN virtual, consulte o artigo Visão Geral da [WAN Virtual.](virtual-wan-about.md)

O Azure Virtual WAN permite uma arquitetura de rede de trânsito global, permitindo conectividade onipresente e qualquer-a-qualquer entre conjuntos distribuídos globalmente de cargas de trabalho em nuvem em VNets, filiais, aplicativos SaaS e PaaS e usuários.

![WAN Virtual do Azure](./media/virtual-wan-global-transit-network-architecture/figure2.png)

**Figura 2: Rede de trânsito global e WAN Virtual**

Na arquitetura WAN virtual do Azure, os hubs WAN virtuais são provisionados em regiões do Azure, para as quais você pode optar por conectar suas filiais, VNets e usuários remotos. As filiais físicas são conectadas ao hub por Premium ExpressRoute ou site-to-site-VPNs, as VNets são conectadas ao hub por conexões VNet e os usuários remotos podem se conectar diretamente ao hub usando o User VPN (VPNs ponto a local). A WAN virtual também suporta conexão VNet entre regiões, onde um VNet em uma região pode ser conectado a um hub WAN virtual em uma região diferente.

Você pode estabelecer uma WAN virtual criando um único hub wan virtual na região que tem o maior número de raios (filiais, VNets, usuários) e, em seguida, conectando os raios que estão em outras regiões para o hub. Esta é uma boa opção quando uma pegada corporativa está principalmente em uma região com alguns raios remotos.  
  
## <a name="hub-to-hub-connectivity-preview"></a><a name="hubtohub"></a>Conectividade hub-to-hub (Preview)

Uma pegada de nuvem Enterprise pode abranger várias regiões de nuvem e é ideal (em termos de latência) acessar a nuvem de uma região mais próxima de seu site físico e usuários. Um dos princípios fundamentais da arquitetura de rede de trânsito global é permitir a conectividade entre todas as regiões e pontos finais da rede local. Isso significa que o tráfego de um ramo conectado à nuvem em uma região pode atingir outro ramo ou um VNet em uma região diferente usando conectividade hub-to-hub habilitada pela [Azure Global Network](https://azure.microsoft.com/global-infrastructure/global-network/).

![região transversal](./media/virtual-wan-global-transit-network-architecture/figure3.png)

**Figura 3: Conectividade virtual de wan entre regiões**

Quando vários hubs são habilitados em uma única WAN virtual, os hubs são automaticamente interconectados através de links hub-to-hub, permitindo assim a conectividade global entre filiais e Vnets que estão distribuídas em várias regiões. 

Além disso, os hubs que fazem parte da mesma WAN virtual, podem ser associados a diferentes políticas regionais de acesso e segurança. Para obter mais informações, consulte [Segurança e controle de políticas](#security) mais tarde neste artigo.

## <a name="any-to-any-connectivity"></a><a name="anytoany"></a>Conectividade de qualquer a qualquer

A arquitetura de rede de trânsito global permite qualquer conectividade através de hubs WAN virtuais. Esta arquitetura elimina ou reduz a necessidade de conectividade de malha completa ou parcial entre os raios, que são mais complexos de construir e manter. Além disso, o controle de roteamento em redes hub-and-spoke vs. mesh é mais fácil de configurar e manter.

Qualquer conectividade (no contexto de uma arquitetura global) permite que uma empresa com usuários, filiais, data centers, VNets e aplicativos globalmente distribuídos se conectem entre si através dos hubs de "trânsito". O Azure Virtual WAN atua como o sistema de trânsito global.

![qualquer a qualquer](./media/virtual-wan-global-transit-network-architecture/figure4.png)

**Figura 4: Caminhos de tráfego WAN virtuais**

O Azure Virtual WAN suporta os seguintes caminhos globais de conectividade de trânsito. As letras entre parênteses mapeiam a Figura 4.

* Branch-to-VNet (a)
* Ramo para ramo (b)
  * Alcance global expressroute e WAN virtual
* Usuário remoto para VNet (c)
* Usuário remoto para ramo (d)
* VNet-para-VNet (e)
* Branch-to-hub-hub-to-Branch (f)
* Branch-to-hub-hub-to-VNet (g)
* VNet-to-hub-hub-to-VNet (h)

### <a name="branch-to-vnet-a-and-branch-to-vnet-cross-region-g"></a>Branch-to-VNet (a) e Branch-to-VNet Cross-region (g)

Branch-to-VNet é o principal caminho suportado pelo Azure Virtual WAN. Esse caminho permite conectar ramificações às cargas de trabalho corporativas Do Zure IAAS que são implantadas no Azure VNets. As filiais podem ser conectadas à WAN virtual via ExpressRoute ou VPN site-to-site. O tráfego transita para VNets que estão conectados aos hubs WAN virtuais via VNet Connections. O trânsito de [gateway](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) explícito não é necessário para a WAN Virtual porque a WAN virtual habilita automaticamente o trânsito de gateway para o site da filial. Consulte o artigo [do Virtual WAN Partners](virtual-wan-configure-automation-providers.md) sobre como conectar um CPE SD-WAN à WAN virtual.

### <a name="expressroute-global-reach-and-virtual-wan"></a>Alcance global expressroute e WAN virtual

ExpressRoute é uma maneira privada e resiliente de conectar suas redes locais ao Microsoft Cloud. A WAN virtual suporta conexões de circuito Express Route. Conectar um site de filial à WAN virtual com a Rota Expressa requer 1) Circuito Premium 2) Circuito Premium 2) Circuito para estar em um local habilitado para Alcance Global.

ExpressRoute Global Reach é um recurso adicional para ExpressRoute. Com a Global Reach, você pode conectar circuitos ExpressRoute para fazer uma rede privada entre suas redes locais. As filiais que estão conectadas ao Azure Virtual WAN usando o ExpressRoute exigem que o ExpressRoute Global Reach se comunique entre si.

Neste modelo, cada ramo conectado ao hub WAN virtual usando o ExpressRoute pode se conectar a VNets usando o caminho branch-to-VNet. O tráfego de ramificação a filial não transita pelo hub porque o ExpressRoute Global Reach permite um caminho mais ideal sobre o Azure WAN.

### <a name="branch-to-branch-b-and-branch-to-branch-cross-region-f"></a>Ramificação a ramo (b) e transse ramal (f)

As filiais podem ser conectadas a um hub WAN virtual do Azure usando circuitos ExpressRoute e/ou conexões VPN site-to-site. Você pode conectar as ramificações ao hub WAN virtual que está na região mais próxima do ramo.

Essa opção permite que as empresas aproveitem o backbone do Azure para conectar ramificações. No entanto, mesmo que esse recurso esteja disponível, você deve pesar os benefícios de conectar ramificações sobre o Azure Virtual WAN vs. usando uma WAN privada.  

### <a name="remote-user-to-vnet-c"></a>Usuário remoto para VNet (c)

Você pode habilitar acesso remoto direto e seguro ao Azure usando conexão ponto a ponto de um cliente de usuário remoto para uma WAN virtual. Os usuários remotos corporativos não precisam mais se apegar à nuvem usando uma VPN corporativa.

### <a name="remote-user-to-branch-d"></a>Usuário remoto para ramo (d)

O caminho remoto usuário-para-ramo permite que usuários remotos que estão usando uma conexão ponto a local ao acesso do Azure a cargas de trabalho e aplicativos no local, transitando pela nuvem. Esse caminho dá aos usuários remotos a flexibilidade para acessar cargas de trabalho que são implantadas tanto no Azure quanto no local. As empresas podem habilitar o serviço de acesso remoto seguro baseado em nuvem central no Azure Virtual WAN.

### <a name="vnet-to-vnet-transit-e-and-vnet-to-vnet-cross-region-h"></a>VNet-to-VNet transit (e) e VNet-to-VNet cross-region (h)

O trânsito VNet-to-VNet permite que os VNets se conectem entre si para interconectar aplicativos multiníveis que são implementados em vários VNets. Opcionalmente, você pode conectar VNets entre si através do VNet Peering e isso pode ser adequado para alguns cenários onde o trânsito através do hub VWAN não é necessário.

## <a name="security-and-policy-control"></a><a name="security"></a>Segurança e controle de políticas

Os hubs Wan virtuais do Azure interconectam todos os pontos finais de rede em toda a rede híbrida e potencialmente vêem todo o tráfego da rede de trânsito. Os hubs WAN virtuais podem ser convertidos em Hubs Virtuais Protegidos, implantando o Firewall Azure dentro de hubs VWAN para permitir a segurança, o acesso e o controle de políticas baseados na nuvem. A orquestração de Firewalls Azure em hubs WAN virtuais pode ser executada pelo Azure Firewall Manager.

[O Azure Firewall Manager](https://go.microsoft.com/fwlink/?linkid=2107683) fornece os recursos para gerenciar e dimensionar a segurança para redes de trânsito globais. O Azure Firewall Manager oferece capacidade de gerenciar centralmente roteamento, gerenciamento global de políticas, serviços avançados de segurança da Internet por meio de terceiros, juntamente com o Firewall Do Azure.

![hub virtual protegido com Firewall Azure](./media/virtual-wan-global-transit-network-architecture/figure5.png)

**Figura 5: Hub virtual protegido com Firewall Azure**

O Azure Firewall para o WAN virtual suporta os seguintes caminhos globais de conectividade de trânsito protegido. As letras entre parênteses mapeiam a Figura 5.

* VNet-to-VNet trânsito seguro (e)
* VNet-para-Internet ou serviço de segurança de terceiros (i)
* Serviço de segurança de ramificação para Internet ou de terceiros (j)

### <a name="vnet-to-vnet-secured-transit-e"></a>VNet-to-VNet segurotrânsito (e)

O transporte seguro VNet-to-VNet permite que os VNets se conectem entre si através do Firewall Azure no hub WAN virtual.

### <a name="vnet-to-internet-or-third-party-security-service-i"></a>VNet-para-Internet ou serviço de segurança de terceiros (i)

O vnet-to-Internet ou transporte seguro de terceiros permite que os VNets se conectem à internet ou a serviços de segurança de terceiros suportados através do Firewall Azure no hub WAN virtual.

### <a name="branch-to-internet-or-third-party-security-service-j"></a>Serviço de segurança de ramificação para Internet ou de terceiros (j)
O transporte seguro de filiais para Internet ou de terceiros permite que as filiais se conectem à internet ou a serviços de segurança de terceiros suportados através do Firewall Azure no hub WAN virtual.

## <a name="next-steps"></a>Próximas etapas

Crie uma conexão usando wan virtual e implante o Firewall Do Zure em hubs VWAN.

* [Conexões site-a-site usando WAN virtual](virtual-wan-site-to-site-portal.md)
* [Conexões ExpressRoute usando WAN virtual](virtual-wan-expressroute-portal.md)
* [Azure Firewall Manager implantará o Azure FW no VWAN](https://go.microsoft.com/fwlink/?linkid=2107683)
