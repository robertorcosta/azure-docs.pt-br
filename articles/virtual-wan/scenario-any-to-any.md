---
title: 'Cenário: qualquer-para-qualquer'
titleSuffix: Azure Virtual WAN
description: Cenários para roteamento-qualquer para qualquer
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: ecc2b3cf236cb2a78fd595189649e7f6b176d709
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567002"
---
# <a name="scenario-any-to-any"></a>Cenário: qualquer-para-qualquer

Ao trabalhar com o roteamento de Hub virtual de WAN virtual, há alguns cenários disponíveis. Em um cenário qualquer para qualquer, qualquer spoke pode alcançar outro spoke. Quando existem vários hubs, o roteamento de Hub para Hub (também conhecido como inter-Hub) é habilitado por padrão na WAN virtual padrão. 

Nesse cenário, as conexões VPN, ExpressRoute e VPN de usuário são associadas à mesma tabela de rotas. Todas as conexões VPN, ExpressRoute e VPN de usuário propagam rotas para o mesmo conjunto de tabelas de rotas. Para obter mais informações sobre roteamento de Hub virtual, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).

## <a name="scenario-architecture"></a><a name="architecture"></a>Arquitetura de cenário

Na **Figura 1**, todos os VNets e branches (VPN, EXPRESSROUTE, P2S) podem alcançar uns aos outros. Em um hub virtual, as conexões funcionam da seguinte maneira:

* Uma conexão VPN conecta um site VPN a um gateway de VPN.
* Uma conexão de rede virtual conecta uma rede virtual a um hub virtual. O roteador do Hub virtual fornece a funcionalidade de trânsito entre VNets.
* Uma conexão do ExpressRoute conecta um circuito do ExpressRoute a um gateway de ExpressRoute.

Essas conexões (por padrão, na criação) são associadas à tabela de rotas padrão, a menos que você defina a configuração de roteamento da conexão como **nenhuma**ou uma tabela de rotas personalizada. Essas conexões também propagam rotas, por padrão, para a tabela de rotas padrão. Isso é o que permite que um cenário de qualquer a qualquer em que qualquer spoke (VNet, VPN, ER, P2S) possa alcançar um ao outro.

**Figura 1**

:::image type="content" source="./media/routing-scenarios/any-any/figure-1.png" alt-text="Figura 1":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Fluxo de trabalho do cenário

Esse cenário é habilitado por padrão para a WAN virtual padrão. Se a configuração de Branch para Branch estiver desabilitada na configuração de WAN, isso não permitirá a conectividade entre spokes de ramificação. VPN/ExpressRoute/VPN de usuário são considerados como spokes de ramificação na WAN virtual

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre a WAN Virtual, veja as [Perguntas frequentes](virtual-wan-faq.md).
* Para obter mais informações sobre roteamento de Hub virtual, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).
