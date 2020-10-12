---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 32e4658af48a0ae3bde08de18cf1d8204878d671
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91025023"
---
### <a name="is-bgp-supported-on-all-azure-vpn-gateway-skus"></a>O BGP tem suporte em todas as SKUs de Gateway de VPN do Azure?
O BGP tem suporte em todos os SKUs de Gateway de VPN do Azure, exceto o SKU Básico.

### <a name="can-i-use-bgp-with-azure-policy-based-vpn-gateways"></a>Posso usar o BGP com gateways de VPN Baseados em Política do Azure?
Não, há suporte ao o BGP somente em gateways de VPN Baseados em Rota.

### <a name="what-asn-autonomous-system-numbers-can-i-use"></a>Quais ASN (Números de Sistema Autônomo) posso usar?
Você pode usar os próprios ASNs públicos ou privados para suas redes locais e para redes virtuais do Azure, **exceto** os intervalos reservados pelo Azure ou pela IANA:

> [!IMPORTANT]
>
> Os seguintes ASNs são reservados pelo Azure ou pela IANA:
> * ASNs reservados pelo Azure:
>    * ASNs públicos: 8074, 8075, 12076
>    * ASNs privados: 65515, 65517, 65518, 65519, 65520
> * ASNs [reservados pela IANA](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml)
>    * 23456, 64496-64511, 65535-65551 e 429496729
>
> Não é possível especificar esses ASNs para seus dispositivos VPN locais ao conectar-se a gateways de VPN do Azure.
>

### <a name="can-i-use-32-bit-4-byte-asns-autonomous-system-numbers"></a>Posso usar ASNs (Números do Sistema Autônomo) de 32 bits (4 bytes)?
Sim, os Gateways de VPN do Azure agora dão suporte a ASNs de 32 bits (4 bytes). Use o PowerShell/a CLI/o SDK para fazer a configuração usando o ASN no formato decimal.

### <a name="what-private-asns-can-i-use"></a>Quais ASNs Privados posso usar?
O intervalo utilizável dos ASNs Privados que podem ser usados são:

* 64512 a 65514, 65521 a 65534

Esses ASNs não são reservados pelo IANA ou pelo Azure para uso e, portanto, podem ser usados para atribuição ao Gateway de VPN do Azure.

### <a name="what-address-does-azure-vpn-gateway-use-for-bgp-peer-ip"></a>Que endereço o gateway de VPN do Azure usa para o IP de Par de BGP?

* Por padrão, o gateway de VPN do Azure alocará um endereço IP do intervalo de GatewaySubnet para gateways de VPN ativos-em espera ou dois endereços IP para gateways de VPN ativos-ativos. Esses endereços são alocados automaticamente quando você cria o gateway de VPN. Você pode obter os endereços IP de BGP reais alocados usando o PowerShell (em Get-AzVirtualNetworkGateway, procure a propriedade “bgpPeeringAddress”) ou no portal do Azure (na propriedade “Configurar o ASN do BGP” na página Configuração do Gateway).

* Se os roteadores VPN locais usarem endereços IP **APIPA** (169.254.x.x) como os endereços IP BGP, você precisará especificar um **endereço IP BGP APIPA do Azure** adicional no gateway de VPN do Azure. O gateway de VPN do Azure selecionará o endereço APIPA a ser usado com o par no nível de protocolo BGP APIPA local especificado no gateway de rede local ou o endereço IP privado para o par no nível de protocolo BGP local não pertencente ao APIPA. Para obter mais informações, confira [Configurar o BGP](../articles/vpn-gateway/bgp-howto.md).

### <a name="what-are-the-requirements-for-the-bgp-peer-ip-addresses-on-my-vpn-device"></a>Quais são os requisitos para os endereços IP de Par de BGP em meu dispositivo VPN?
O seu endereço de par no nível de protocolo BGP local **NÃO PODE** ser o mesmo que o endereço IP público do dispositivo VPN nem do espaço de endereço da VNet do Gateway de VPN. Use um endereço IP diferente no dispositivo VPN para o IP de Par de BGP. Ele pode ser um endereço atribuído à interface de loopback no dispositivo, seja um endereço IP regular ou um endereço APIPA. Se o dispositivo usar o endereço APIPA para BGP, você precisará especificar um endereço IP BGP APIPA em seu gateway de VPN do Azure, conforme descrito em [Configurar o BGP](../articles/vpn-gateway/bgp-howto.md). Especifique esse endereço no Gateway de Rede Local correspondente que representa o local.

### <a name="what-should-i-specify-as-my-address-prefixes-for-the-local-network-gateway-when-i-use-bgp"></a>O que devo especificar como meus prefixos de endereço para o Gateway de Rede Local ao usar o BGP?

> [!IMPORTANT]
>
>Essa é uma alteração do requisito documentado anteriormente. Se você usar o BGP para uma conexão, deverá deixar o campo **Espaço de endereço** ***vazio*** para o recurso de gateway de rede local correspondente. O gateway de VPN do Azure adicionará internamente uma rota de host ao IP do par no nível de protocolo BGP local pelo túnel IPsec. **NÃO** adicione a rota/32 no campo Espaço de endereço. Ele é redundante e, se você usar um endereço APIPA como o IP BGP do dispositivo VPN local, ele não poderá ser adicionado a esse campo. Se você adicionar outros prefixos no campo Espaço de endereço, eles serão adicionados como **rotas estáticas** no gateway de VPN do Azure, além das rotas aprendidas por meio do BGP.
>

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

Por exemplo, se você tiver dois túneis redundantes entre o gateway de VPN do Azure e uma de suas redes locais, elas consumirão dois túneis da cota total de seu gateway de VPN do Azure.

### <a name="can-i-have-multiple-tunnels-between-two-azure-vnets-with-bgp"></a>Posso ter vários túneis entre duas VNets do Azure com o BGP?
Sim, mas pelo menos um dos gateways de rede virtual deve estar na configuração ativo-ativo.

### <a name="can-i-use-bgp-for-s2s-vpn-in-an-expressroutes2s-vpn-coexistence-configuration"></a>Posso usar BGP para VPN S2S em uma configuração de coexistência de VPN S2S/ExpressRoute?
Sim. 

### <a name="what-should-i-add-to-my-on-premises-vpn-device-for-the-bgp-peering-session"></a>O que devo adicionar a meu dispositivo VPN local para a sessão de emparelhamento de BGP?
Você deve adicionar uma rota de host do endereço IP de Par de BGP do Azure em seu dispositivo VPN apontando para o túnel de VPN S2S IPsec. Por exemplo, se o IP de Par de VPN do Azure for "10.12.255.30", você deverá adicionar uma rota de host para "10.12.255.30" com uma interface de nexthop da interface de túnel IPsec correspondente em seu dispositivo VPN.
