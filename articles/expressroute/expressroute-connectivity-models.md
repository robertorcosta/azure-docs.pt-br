---
title: 'Azure ExpressRoute: modelos de conectividade'
description: Este artigo descreve os diferentes modos de conectividade entre a rede do cliente e Microsoft Azure e os serviços do Office 365. Os clientes podem usar provedores de MPLS, trocas de nuvem e provedores de Ethernet.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: cherylmc
ms.openlocfilehash: 375d2f9d3b455c0495c69f2b23d62b1ab6522710
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79280879"
---
# <a name="expressroute-connectivity-models"></a>Modelos de conectividade do ExpressRoute
Você pode criar uma conexão entre sua rede local e a nuvem da Microsoft de três maneiras diferentes, [Colocalização do CloudExchange](#CloudExchange), [Conexão de Ethernet Ponto a Ponto](#Ethernet) e [Conexão Qualquer para Qualquer (IPVPN)](#IPVPN). Os provedores de conectividade podem oferecer um ou mais modelos de conectividade. Converse com seu provedor de conectividade a fim de escolher o modelo mais adequado a você.
<br><br>

![Diagrama de modelo de conectividade do ExpressRoute](./media/expressroute-connectivity-models/expressroute-connectivity-models-diagram.png)

## <a name="co-located-at-a-cloud-exchange"></a><a name="CloudExchange"></a>Colocalizada em uma troca de nuvem
Se você estiver colocalizado em uma instalação que possua uma troca de nuvem, poderá solicitar conexões cruzadas virtuais para a nuvem da Microsoft por meio da troca Ethernet do provedor da colocalização. Os provedores da colocalização podem oferecer conexões cruzadas de Camada 2 ou conexões cruzadas gerenciadas de Camada 3 entre sua infraestrutura na instalação de colocalização e a nuvem da Microsoft.

## <a name="point-to-point-ethernet-connections"></a><a name="Ethernet"></a>Conexões Ethernet ponto a ponto
Você pode conectar seus data centers/escritórios locais à nuvem da Microsoft por meio de links de Ethernet ponto a ponto. Os provedores de Ethernet ponto a ponto podem oferecer conexões de Camada 2 ou conexões gerenciadas de Camada 3 entre seu site e a nuvem da Microsoft.

## <a name="any-to-any-ipvpn-networks"></a><a name="IPVPN"></a>Redes qualquer para qualquer (IPVPN)
É possível integrar sua WAN à nuvem da Microsoft. Os Provedores de IPVPN (normalmente, VPN MPLS) oferecem conectividade “qualquer para qualquer” entre suas filiais e datacenters. A nuvem da Microsoft pode ser interconectada à sua WAN para que ela fique exatamente igual a qualquer outra filial. Normalmente, os provedores de rede WAN oferecem conectividade gerenciada de Camada 3. Os recursos do ExpressRoute são idênticos em todos os modelos de conectividade mencionados acima. 

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre conexões e domínios de roteamento do ExpressRoute. Consulte [Circuitos e domínios de roteamento do ExpressRoute](expressroute-circuit-peerings.md).
* Saiba mais sobre recursos do ExpressRoute. Veja a [Visão geral técnica do ExpressRoute](expressroute-introduction.md)
* Encontrar um provedor de serviços. Consulte [Parceiros e locais de emparelhamento do ExpressRoute](expressroute-locations.md).
* Certifique-se que todos os pré-requisitos foram atendidos. Consulte [Pré-requisitos do ExpressRoute](expressroute-prerequisites.md).
* Consulte os requisitos para [Roteamento](expressroute-routing.md), [NAT](expressroute-nat.md)e [QoS](expressroute-qos.md).
* Configurar sua conexão do ExpressRoute.
  * [Criar um circuito do ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md)
  * [Configurar o roteamento](expressroute-howto-routing-portal-resource-manager.md)
  * [Vincular uma rede virtual a um circuito do ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)