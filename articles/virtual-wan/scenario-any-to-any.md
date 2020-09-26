---
title: 'Cenário: qualquer-para-qualquer'
titleSuffix: Azure Virtual WAN
description: Cenários para roteamento-qualquer para qualquer
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 6115ca375c3e5bf2be3335fe2231628ec7bf309f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91267730"
---
# <a name="scenario-any-to-any"></a>Cenário: qualquer-para-qualquer

Ao trabalhar com o roteamento de Hub virtual de WAN virtual, há alguns cenários disponíveis. Em um cenário qualquer para qualquer, qualquer spoke pode alcançar outro spoke. Quando existem vários hubs, o roteamento de Hub para Hub (também conhecido como inter-Hub) é habilitado por padrão na WAN virtual padrão. Para obter mais informações sobre roteamento de Hub virtual, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Design

Para descobrir quantas tabelas de rotas serão necessárias em um cenário de WAN virtual, você pode criar uma matriz de conectividade, em que cada célula representa se uma origem (linha) pode se comunicar com um destino (coluna). A matriz de conectividade nesse cenário é trivial, mas incluímos isso para ser consistente com outros cenários.

| De |   Para |  *VNets* | *Branches* |
| -------------- | -------- | ---------- | ---|
| VNets     | &#8594;|      X     |     X    |
| Branches   | &#8594;|    X     |     X    |

Cada uma das células na tabela anterior descreve se uma conexão de WAN virtual (o lado "de" do fluxo, os cabeçalhos de linha na tabela) aprende um prefixo de destino (o lado "para" do fluxo, os cabeçalhos de coluna em itálico na tabela) para um fluxo de tráfego específico, em que um "X" significa que a conectividade é fornecida pela WAN virtual.

Como todas as conexões de VNets e branches (VPN, ExpressRoute e VPN de usuário) têm os mesmos requisitos de conectividade, uma única tabela de rotas é necessária. Como resultado, todas as conexões serão associadas e propagadas para a mesma tabela de rotas, a tabela de rotas padrão:

* Redes virtuais:
  * Tabela de rotas associada: **padrão**
  * Propagando para tabelas de rotas: **padrão**
* Filia
  * Tabela de rotas associada: **padrão**
  * Propagando para tabelas de rotas: **padrão**

Para obter mais informações sobre roteamento de Hub virtual, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).

## <a name="architecture"></a><a name="architecture"></a>Arquitetura

Na **Figura 1**, todos os VNets e branches (VPN, EXPRESSROUTE, P2S) podem alcançar uns aos outros. Em um hub virtual, as conexões funcionam da seguinte maneira:

* Uma conexão VPN conecta um site VPN a um gateway de VPN.
* Uma conexão de rede virtual conecta uma rede virtual a um hub virtual. O roteador do Hub virtual fornece a funcionalidade de trânsito entre VNets.
* Uma conexão do ExpressRoute conecta um circuito do ExpressRoute a um gateway de ExpressRoute.

Essas conexões (por padrão, na criação) são associadas à tabela de rotas padrão, a menos que você defina a configuração de roteamento da conexão como **nenhuma**ou uma tabela de rotas personalizada. Essas conexões também propagam rotas, por padrão, para a tabela de rotas padrão. Isso é o que permite que um cenário de qualquer a qualquer em que qualquer spoke (VNet, VPN, ER, P2S) possa alcançar um ao outro.

**Figura 1**

:::image type="content" source="./media/routing-scenarios/any-any/figure-1.png" alt-text="Figura 1":::

## <a name="workflow"></a><a name="workflow"></a>Fluxo de trabalho

Esse cenário é habilitado por padrão para a WAN virtual padrão. Se a configuração de Branch para Branch estiver desabilitada na configuração de WAN, isso não permitirá a conectividade entre spokes de ramificação. VPN/ExpressRoute/VPN de usuário são considerados como spokes de ramificação na WAN virtual

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre a WAN Virtual, veja as [Perguntas frequentes](virtual-wan-faq.md).
* Para obter mais informações sobre roteamento de Hub virtual, consulte [sobre roteamento de Hub virtual](about-virtual-hub-routing.md).
