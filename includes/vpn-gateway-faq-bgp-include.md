---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 608b148dc3929065df44530da65e695df19be03e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79486037"
---
### <a name="is-bgp-supported-on-all-azure-vpn-gateway-skus"></a>O BGP tem suporte em todas as SKUs de Gateway de VPN do Azure?
O BGP é suportado em todas as SKUs Azure VPN Gateawy, exceto Basic SKU.

### <a name="can-i-use-bgp-with-azure-policy-based-vpn-gateways"></a>Posso usar o BGP com gateways de VPN Baseados em Política do Azure?
Não, há suporte ao o BGP somente em gateways de VPN Baseados em Rota.

### <a name="can-i-use-private-asns-autonomous-system-numbers"></a>Posso usar ASNs (Números de Sistema Autônomo) privados?
Sim, você pode usar seu próprio ASNs públicos ou privados para suas redes locais e para redes virtuais do Azure.

### <a name="can-i-use-32-bit-4-byte-asns-autonomous-system-numbers"></a>Posso usar ASNs (4 bytes) de 32 bits (Números do Sistema Autônomo)?
Sim, os Gateways Azure VPN agora suportam ASNs de 32 bits (4 bytes). Por favor, use o PowerShell/CLI/SDK para configurar usando ASN em formato decimal.

### <a name="are-there-asns-reserved-by-azure"></a>Há ASNs reservados pelo Azure?
Sim, os seguintes ASNs estão reservados pelo Azure para emparelhamentos internos e externos:

* ASNs públicos: 8074, 8075, 12076
* ASNs privados: 65515, 65517, 65518, 65519, 65520

Não é possível especificar esses ASNs para seus dispositivos VPN locais ao conectar-se a gateways de VPN do Azure.

### <a name="are-there-any-other-asns-that-i-cant-use"></a>Há outros ASNs que eu não posso usar?
Sim, os ASNs a seguir são [reservados pelo IANA](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml) e não podem ser configurados em seu Gateway de VPN do Azure:

23456, 64496-64511, 65535-65551 e 429496729

### <a name="what-private-asns-can-i-use"></a>Quais ASNs privados posso usar?
A gama utilizável de ASNs privadas que podem ser usadas são:

* 64512-65514, 65521-65534

Essas ASNs não são reservadas pela IANA ou a Azure para uso e, portanto, podem ser usadas para atribuir ao seu Gateway Azure VPN.

### <a name="can-i-use-the-same-asn-for-both-on-premises-vpn-networks-and-azure-vnets"></a>Posso usar o mesmo ASN para redes de VPN locais e VNets do Azure?
Não, você deverá atribuir ASNs diferentes entre suas redes locais e as VNets do Azure se os estiver conectando junto com o BGP. Os Gateways de VPN do Azure têm um ASN padrão de 65515 atribuído, quer o BGP esteja habilitado ou não para a conectividade entre locais. Você pode substituir esse padrão atribuindo um ASN diferente ao criar o gateway de VPN ou alterar o ASN depois de criar o gateway. Você precisará atribuir ASNs locais aos Gateways de Rede Local do Azure correspondentes.

### <a name="what-address-prefixes-will-azure-vpn-gateways-advertise-to-me"></a>Quais prefixos de endereço os gateways de VPN do Azure anunciarão para mim?
O Gateway de VPN do Azure anunciará as seguintes rotas para seus dispositivos de BGP locais:

* Seus prefixos de endereços de VNet
* Prefixos de endereços para cada Gateway de Rede Local conectado ao gateway de VPN do Azure
* As rotas obtidas de outras sessões de emparelhamento de BGP conectadas ao gateway de VPN do Azure, **exceto rotas padrão sobrepostas com qualquer prefixo de VNet**.

### <a name="how-many-prefixes-can-i-advertise-to-azure-vpn-gateway"></a>Quantos prefixos posso anunciar para o gateway de VPN do Azure?
Damos suporte a até 4000 prefixos. A sessão BGP é descartada se o número de prefixos exceder o limite.

### <a name="can-i-advertise-default-route-00000-to-azure-vpn-gateways"></a>Posso anunciar a rota padrão (0.0.0.0/0) para gateways de VPN do Azure?
Sim.

Observe que isso forçará qualquer tráfego de saída da VNet para o site local e impedirá que as VMs da VNet aceitem uma comunicação pública da Internet diretamente, como o RDP ou SSH da Internet para as VMs.

### <a name="can-i-advertise-the-exact-prefixes-as-my-virtual-network-prefixes"></a>Posso anunciar os prefixos exatamente como meus prefixos de Rede Virtual?

Não, o anúncio dos mesmos prefixos como qualquer um de seus prefixos de endereço de sua Rede Virtual será bloqueado ou filtrado pela plataforma do Azure. No entanto, você pode anunciar um prefixo que é um superconjunto do que você tem em sua Rede Virtual. 

Por exemplo, se sua rede virtual tiver usado o espaço de endereço 10.0.0.0/16, você poderia anunciar 10.0.0.0/8. Mas você não pode anunciar 10.0.0.0/16 ou 10.0.0.0/24.

### <a name="can-i-use-bgp-with-my-vnet-to-vnet-connections"></a>Posso usar o BGP com minhas conexões VNet para VNet?
Sim, você pode usar o BGP para conexões entre locais e conexões de VNet para VNet.

### <a name="can-i-mix-bgp-with-non-bgp-connections-for-my-azure-vpn-gateways"></a>Posso combinar o BGP a conexões não BGP para meu gateways de VPN do Azure?
Sim, você pode combinar conexões BGP e não BGP para o mesmo gateway de VPN do Azure.

### <a name="does-azure-vpn-gateway-support-bgp-transit-routing"></a>O gateway de VPN do Azure dá suporte ao roteamento de trânsito de BGP?
Sim, o roteamento de trânsito de BGP tem suporte, com a exceção de que os gateways de VPN do Azure **NÃO** anunciarão rotas padrão para outros pares de BGP. Para habilitar o roteamento de trânsito entre vários gateways de VPN do Azure, você deve habilitar o BGP em todas as conexões de VNet para VNet intermediárias. Para obter mais informações, confira [Sobre o BGP](../articles/vpn-gateway/vpn-gateway-bgp-overview.md).

### <a name="can-i-have-more-than-one-tunnel-between-azure-vpn-gateway-and-my-on-premises-network"></a>Posso ter mais de um túnel entre meu gateway de VPN do Azure e minha rede local?
Sim, você pode estabelecer mais de um túnel de VPN S2S entre um gateway de VPN do Azure e sua rede local. Observe que todos esses túneis serão contados em relação ao número total de túneis para seus gateways de VPN do Azure e você deve habilitar o BGP em ambos os túneis.

Por exemplo, se você tiver dois túneis redundantes entre o gateway de VPN do Azure e uma de suas redes locais, elas consumirão dois túneis da cota total de seu gateway de VPN do Azure (10 para Standard e 30 para HighPerformance).

### <a name="can-i-have-multiple-tunnels-between-two-azure-vnets-with-bgp"></a>Posso ter vários túneis entre duas VNets do Azure com o BGP?
Sim, mas pelo menos um dos gateways de rede virtual deve estar na configuração ativo-ativo.

### <a name="can-i-use-bgp-for-s2s-vpn-in-an-expressroutes2s-vpn-co-existence-configuration"></a>Posso usar BGP para VPN S2S em uma configuração de coexistência de VPN S2S/ExpressRoute?
Sim. 

### <a name="what-address-does-azure-vpn-gateway-use-for-bgp-peer-ip"></a>Que endereço o gateway de VPN do Azure usa para o IP de Par de BGP?
O gateway Azure VPN alocará um único endereço IP da gama GatewaySubnet para gateways VPN ativos ou dois endereços IP para gateways VPN ativos. Você pode obter o endereço IP(es) real alocado usando o PowerShell (Get-AzVirtualNetworkGateway, procure a propriedade "bgpPeeringAddress" ou no portal Azure (na propriedade "Configurar BGP ASN" na página Configuração do Gateway).

### <a name="what-are-the-requirements-for-the-bgp-peer-ip-addresses-on-my-vpn-device"></a>Quais são os requisitos para os endereços IP de Par de BGP em meu dispositivo VPN?
Seu endereço bgp no local **NÃO deve** ser o mesmo que o endereço IP público do seu dispositivo VPN ou o espaço de endereço Vnet do Gateway VPN. Use um endereço IP diferente no dispositivo VPN para o IP de Par de BGP. Ele pode ser um endereço atribuído à interface de loopback no dispositivo, mas observe que ele não pode ser um endereço APIPA (169.254.x.x). Especifique esse endereço no Gateway de Rede Local correspondente que representa o local.

### <a name="what-should-i-specify-as-my-address-prefixes-for-the-local-network-gateway-when-i-use-bgp"></a>O que devo especificar como meus prefixos de endereço para o Gateway de Rede Local ao usar o BGP?
O Gateway de Rede Local do Azure especifica os prefixos de endereços iniciais para a rede local. Com o BGP, você deve alocar o prefixo de host (prefixo /32) de seu endereço IP de Par de BGP como o espaço de endereço da rede local. Se o IP de Par de BGP for 10.52.255.254, você deverá especificar "10.52.255.254/32" como localNetworkAddressSpace do Gateway de Rede Local que representa essa rede local. Isso é para garantir que o gateway de VPN do Azure estabeleça a sessão de BGP através do túnel de VPN S2S.

### <a name="what-should-i-add-to-my-on-premises-vpn-device-for-the-bgp-peering-session"></a>O que devo adicionar a meu dispositivo VPN local para a sessão de emparelhamento de BGP?
Você deve adicionar uma rota de host do endereço IP de Par de BGP do Azure em seu dispositivo VPN apontando para o túnel de VPN S2S IPsec. Por exemplo, se o IP de Par de VPN do Azure for "10.12.255.30", você deverá adicionar uma rota de host para "10.12.255.30" com uma interface de nexthop da interface de túnel IPsec correspondente em seu dispositivo VPN.
