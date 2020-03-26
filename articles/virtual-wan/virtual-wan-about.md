---
title: Visão geral da WAN Virtual do Azure | Microsoft Docs
description: Saiba mais sobre conectividade de ramificação por ramificação escalonável automatizada de WAN Virtual, regiões disponíveis e parceiros.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 02/05/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 9ac70252ce7c818ccbdecfd996b9970f011aa967
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "79290184"
---
# <a name="about-azure-virtual-wan"></a>Sobre a WAN Virtual do Azure

WAN Virtual do Azure é um serviço de rede que fornece conectividade de ramificação otimizada e automatizada para e por meio do Azure. As regiões do Azure servem como hubs para os quais é possível optar por conectar suas ramificações. Você pode aproveitar o backbone do Azure para também conectar as ramificações e aproveitar a conectividade de ramificação a VNet. Temos uma lista de parceiros que oferecem suporte à automação de conectividade com VPN da WAN Virtual do Azure. Para saber mais, veja o artigo [Parceiros e localizações de WAN Virtual](virtual-wan-locations-partners.md).

A WAN Virtual do Azure reúne muitos serviços de conectividade de nuvem do Azure, como VPN site a site, ExpressRoute e VPN de usuário (ponto a site) em uma única interface operacional. A conectividade com VNets do Azure é estabelecida usando conexões de rede virtual. Ele permite a [arquitetura de rede de trânsito global](virtual-wan-global-transit-network-architecture.md) com base em um modelo de conectividade de Hub e spoke clássica em que o “hub” da rede hospedada em nuvem permite a conectividade transitiva entre pontos de extremidade que podem ser distribuídos entre diferentes tipos de “spokes”.

![Diagrama de WAN virtual](./media/virtual-wan-about/virtualwan1.png)

Este artigo fornece uma visão rápida da conectividade de rede na WAN Virtual do Azure. A WAN Virtual oferece as seguintes vantagens:

* **Soluções de conectividade integradas no hub e spoke:** Automatize a configuração site a site e a conectividade entre sites locais e um hub do Azure.
* **Definição e configuração do spoke automatizado:** Conecte as redes virtuais e cargas de trabalho ao hub do Azure diretamente.
* **Solução de problemas intuitiva:** É possível ver o fluxo de ponta a ponta no Azure e usar essas informações para executar as ações necessárias.

## <a name="basic-and-standard-virtual-wans"></a><a name="basicstandard"></a>WANs virtuais básicas e Standard

Há dois tipos de WANs virtuais: Básico e Standard. A tabela a seguir mostra as configurações disponíveis para cada tipo.

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

Para obter as etapas para atualizar uma WAN virtual, consulte [atualizar uma WAN virtual de básico para Standard](upgrade-virtual-wan.md).

## <a name="architecture"></a><a name="architecture"></a>Arquitetura

Para obter informações sobre a arquitetura da WAN Virtual e como fazer a migração para a WAN Virtual, confira os seguintes artigos:

* [Arquitetura da WAN Virtual](migrate-from-hub-spoke-topology.md)
* [Arquitetura de rede de trânsito global](virtual-wan-global-transit-network-architecture.md)

## <a name="virtual-wan-resources"></a><a name="resources"></a>Recursos da WAN Virtual

Para configurar uma WAN virtual de ponta a ponta, crie os seguintes recursos:

* **virtualWAN:** O recurso virtualWAN representa uma sobreposição virtual da rede do Azure e é uma coleção de vários recursos. Ele contém links para todos os seus hubs virtuais que você gostaria de ter dentro da WAN Virtual. Os recursos da WAN Virtual ficam isolados uns dos outros e não podem conter um hub comum. Os hubs virtuais na WAN Virtual não se comunicam entre si.

* **Hub:** Um hub virtual é uma rede virtual gerenciada pela Microsoft. O hub contém vários pontos de extremidade de serviço para habilitar a conectividade. De sua rede local (vpnsite), você pode se conectar a um gateway de VPN dentro do hub virtual, conectar circuitos do ExpressRoute a um hub virtual ou até mesmo conectar usuários móveis a um gateway ponto a site no hub virtual. O hub é o núcleo da sua rede em uma região. Pode haver apenas um hub por região do Azure.

  Um gateway de hub não é o mesmo que um gateway de rede virtual que você pode usar para ExpressRoute e Gateway de VPN. Por exemplo, ao usar WAN Virtual, você não cria uma conexão de site a site do site local diretamente para a rede virtual. Em vez disso, você cria uma conexão site a site com o hub. O tráfego sempre passa pelo gateway do hub. Isso significa que suas VNets não precisam de um gateway de rede virtual próprio. A WAN Virtual permite que suas VNets tirem proveito do dimensionamento fácil por meio do hub virtual e do gateway do hub virtual.

* **Conexão de rede virtual do hub:** O recurso de conexão de rede virtual do Hub é usado para conectar o hub à rede virtual.

* **(Apresentação) Conexão hub a hub** - Os hubs estão todos conectados entre si em uma WAN virtual. Isso quer dizer que uma ramificação, um usuário ou uma VNet conectada a um hub local pode se comunicar com outro branch ou VNet usando a arquitetura de malha completa dos hubs conectados. Você também pode conectar VNets em um hub que passa pelo hub virtual, bem como VNets ao longo do hub, usando a estrutura conectada de hub para hub.

* **Tabela de rotas de hub:**  Você pode criar uma rota de hub virtual e aplicar a rota à tabela de rotas do hub virtual. Você pode aplicar várias rotas à tabela de rotas do hub virtual.

**Recursos adicionais de WAN Virtual**

  * **Site:** Esse recurso é usado apenas para conexões site a site. O recurso do site é **vpnsite**. Ele representa o dispositivo VPN local e as respectivas configurações. Ao trabalhar com um parceiro de WAN Virtual, você terá uma solução interna para exportar automaticamente essas informações para o Azure.

## <a name="types-of-connectivity"></a><a name="connectivity"></a>Tipos de conectividade

A WAN Virtual permite os dois tipos de conectividade a seguir: VPN Site a Site, VPN do usuário (Ponto a Site) e ExpressRoute.

### <a name="site-to-site-vpn-connections"></a><a name="s2s"></a>Conexões VPN site a site

![Diagrama de WAN virtual](./media/virtual-wan-about/virtualwan.png)

Ao criar uma conexão site a site de WAN Virtual, você poderá trabalhar com um parceiro disponível. Se você não quiser usar um parceiro, poderá configurar a conexão manualmente. Para obter mais informações, consulte [Criar uma conexão site a site usando WAN Virtual](virtual-wan-site-to-site-portal.md).

#### <a name="virtual-wan-partner-workflow"></a><a name="s2spartner"></a>Fluxo de trabalho do parceiro da WAN Virtual

Ao trabalhar com um parceiro da WAN Virtual, o fluxo de trabalho será:

1. O controlador do dispositivo de branch (VPN/SDWAN) é autenticado para exportar informações do site no Azure usando uma [Entidade de Serviço do Azure](../active-directory/develop/howto-create-service-principal-portal.md).
2. O controlador do dispositivo de branch (VPN/SDWAN) obtém a configuração de conectividade do Azure e atualiza o dispositivo local. Isso automatiza o download da configuração, a edição e a atualização do dispositivo VPN local.
3. Quando o dispositivo tiver a configuração do Azure correta, uma conexão site a site (dois túneis ativos) será estabelecida com a WAN do Azure. O Azure é compatível com IKEv1 e IKEv2. BGP é opcional.

#### <a name="partners-for-site-to-site-virtual-wan-connections"></a><a name="partners"></a>Parceiros para conexões da WAN Virtual site a site

Para obter uma lista dos parceiros e localizações disponíveis, consulte o artigo [Parceiros e localizações de WAN Virtual](virtual-wan-locations-partners.md).

### <a name="user-vpn-point-to-site-connections"></a><a name="uservpn"></a>Conexões (ponto a site) de VPN do usuário

Você pode conectar seus recursos no Azure por meio de uma conexão de IPsec/IKE (IKEv2) ou OpenVPN. Esse tipo de conexão exige que um cliente VPN seja configurado no computador cliente. Para obter mais informações, consulte [Criar uma conexão ponto a site](virtual-wan-point-to-site-portal.md).

### <a name="expressroute-connections"></a><a name="er"></a>Conexões do ExpressRoute
O ExpressRoute permite conectar a rede local ao Azure em uma conexão privada. Para criar a conexão, consulte [Criar uma conexão ExpressRoute usando WAN Virtual](virtual-wan-expressroute-portal.md).

## <a name="locations"></a><a name="locations"></a>Localizações

Para obter informações de localização, consulte o artigo [Parceiros e localizações de WAN Virtual](virtual-wan-locations-partners.md).

## <a name="faq"></a><a name="faq"></a>Perguntas frequentes

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Próximas etapas

[Criar uma conexão site a site usando WAN Virtual](virtual-wan-site-to-site-portal.md)
