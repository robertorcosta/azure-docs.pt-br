---
title: Conectando o Azure às nuvens públicas | Microsoft Docs
description: Descreva várias maneiras de conectar o Azure a outras nuvens públicas
services: expressroute
author: osamazia
ms.service: expressroute
ms.topic: article
ms.date: 07/24/2019
ms.author: osamaz
ms.openlocfilehash: b8a454c2a104dfe8545cf734bf0b020b8f749bb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73889636"
---
# <a name="connecting-azure-with-public-clouds"></a>Conectando o Azure com nuvens públicas

Muitas empresas estão buscando uma estratégia multi-nuvem por causa de objetivos técnicos e de negócios. Estes incluem custo, flexibilidade, disponibilidade de recursos, redundância, soberania de dados etc. Essa estratégia os ajuda a aproveitar melhor as duas nuvens. 

Essa abordagem também coloca desafios para a empresa em termos de arquitetura de rede e aplicativos. Alguns desses desafios são a latência e o throughput de dados. Para enfrentar esses desafios, os clientes estão procurando se conectar diretamente a várias nuvens. Alguns provedores de serviços fornecem uma solução para conectar vários provedores de nuvem para os clientes. Em outros casos, o cliente pode implantar seu próprio roteador para conectar várias nuvens públicas.
## <a name="connectivity-via-expressroute"></a>Conectividade via ExpressRoute
O ExpressRoute permite que os clientes ampliem suas redes locais na nuvem da Microsoft por uma conexão privada facilitada por um provedor de conectividade. Com o ExpressRoute, os clientes podem estabelecer conexões com os serviços de nuvem da Microsoft.

Existem três maneiras de se conectar via ExpressRoute.

1. Provedor de camada3
2. Provedor de camada2
3. Conexão direta

### <a name="layer3-provider"></a>Provedor de camada3

Os provedores de Layer3 são comumente conhecidos como provedores de VPN IP ou MPLS VPN. Os clientes aproveitam esses provedores para conectividade multiponto entre seus data centers, filiais e a nuvem. Os clientes se conectam ao provedor L3 via BGP ou via rota padrão estática. O provedor de serviços anuncia rotas entre os sites dos clientes, data centers e nuvem pública. 
 
Ao se conectar através do provedor layer3, a Microsoft anunciará as rotas vnet do cliente para o provedor de serviços em vez de BGP. O provedor pode ter duas implementações diferentes.

![](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l3.png)

O provedor pode estar pousando cada provedor de nuvem em um VRF separado, se o tráfego de todos os provedores de nuvem alcançar no roteador do cliente. Se o cliente estiver executando o BGP com o provedor de serviços, essas rotas serão reanunciadas para outros provedores de nuvem por padrão. 

Se o provedor de serviços estiver desembarcando todos os provedores de nuvem no mesmo VRF, então as rotas serão anunciadas diretamente para outros provedores de nuvem do provedor de serviços. Isso está assumindo a operação BGP padrão onde as rotas eBGP são anunciadas para outros vizinhos eBGP por padrão.

Cada nuvem pública tem um limite de prefixo diferente, portanto, ao distribuir o provedor de serviços de rotas deve ter cuidado na distribuição das rotas.

### <a name="layer2-provider-and-direct-connection"></a>Provedor de camada2 e conexão direta

Embora a conectividade física em ambos os modelos seja diferente, mas na layer3 o BGP é estabelecido diretamente entre o MSEE e o roteador do cliente. Para o ExpressRoute Direct, o cliente se conecta diretamente ao MSEE. No caso da Layer2, o provedor de serviços estende a VLAN das instalações do cliente para a nuvem. Os clientes executam o BGP em cima da rede layer2 para conectar seus DCs à nuvem.
![](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l2.png)
Em ambos os casos, o cliente terá conexões ponto a ponto com cada uma das nuvens públicas. O cliente estabelecerá uma conexão BGP separada para cada nuvem pública. As rotas recebidas por um provedor de nuvem serão anunciadas para outro provedor de nuvem por padrão. Cada provedor de nuvem tem um limite de prefixo diferente, portanto, ao anunciar as rotas, o cliente deve cuidar desses limites. O cliente pode usar botões BGP usuais com a Microsoft enquanto anuncia rotas de outras nuvens públicas.

## <a name="direct-connection-with-expressroute"></a>Conexão direta com o ExpressRoute

Os clientes podem optar por conectar o ExpressRoute diretamente à oferta de conectividade direta do provedor de nuvem. Dois provedores de nuvem serão conectados de volta para trás e o BGP será estabelecido diretamente entre seus roteadores. Este tipo de conexão está disponível com a Oracle hoje.

## <a name="site-to-site-vpn"></a>VPN site a site

Os clientes podem aproveitar a Internet para conectar suas instâncias no Azure com outras nuvens públicas. Quase todos os provedores de nuvem oferecem recursos de VPN de site para local. No entanto, pode haver incompatibilidades devido à falta de certas variantes. Por exemplo, alguns provedores de nuvem só suportam ikev1, então há um ponto final de terminação de VPN necessário nessa nuvem. Para os provedores de nuvem que suportam o IKEv2, um túnel direto pode ser estabelecido entre gateways VPN em ambos os provedores de nuvem.

Vpn site-to-site não é considerada uma solução de alto throughput e baixa latência. No entanto, ele pode ser usado como um backup para a conectividade física.

## <a name="next-steps"></a>Próximas etapas
Consulte [o ExpressRoute FAQ][ER-FAQ] para obter mais perguntas sobre expressroute e conectividade de rede virtual.

Veja [Configurar conexão direta entre Azure e Oracle Cloud][ER-OCI] para conectividade entre Azure e Oracle

<!--Link References-->
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[ER-OCI]: https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-azure-oci-networking



