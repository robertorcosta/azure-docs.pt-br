---
title: Sobre o roteamento de hub virtual
titleSuffix: Azure Virtual WAN
description: Este artigo descreve o roteamento de Hub virtual
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 6809429b26eea30ca0569044634308d2e4dff9f7
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86025974"
---
# <a name="about-virtual-hub-routing"></a>Sobre o roteamento de hub virtual

Os recursos de roteamento em um hub virtual são fornecidos por um roteador que gerencia todo o roteamento entre gateways usando Border Gateway Protocol (BGP). Um hub virtual pode conter vários gateways, como um gateway de VPN site a site, gateway de ExpressRoute, gateway de ponto a site, firewall do Azure. Esse roteador também fornece conectividade de trânsito entre redes virtuais que se conectam a um hub virtual e podem dar suporte a uma taxa de transferência agregada de 50 Gbps. Esses recursos de roteamento se aplicam aos clientes de WAN virtual padrão.

Para configurar o roteamento, consulte [como configurar o roteamento de Hub virtual](how-to-virtual-hub-routing.md).

## <a name="routing-concepts"></a><a name="concepts"></a>Conceitos de roteamento

As seções a seguir descrevem os principais conceitos no roteamento de Hub virtual.

### <a name="hub-route-table"></a><a name="hub-route"></a>Tabela de rotas do Hub

Uma tabela de rotas do Hub virtual pode conter uma ou mais rotas. Uma rota inclui seu nome, um rótulo, um tipo de destino, uma lista de prefixos de destino e informações do próximo salto para um pacote a ser roteado. Uma **conexão** normalmente terá uma configuração de roteamento associada ou propagada para uma tabela de rotas

### <a name="connection"></a><a name="connection"></a>Conexão

Conexões são recursos do Resource Manager que têm uma configuração de roteamento. Os quatro tipos de conexões são:

* **Conexão VPN**: conecta um site VPN a um gateway de VPN de Hub virtual.
* **Conexão do expressroute**: conecta um circuito do expressroute a um gateway de expressroute do Hub virtual.
* **Conexão de configuração do P2S**: conecta uma configuração de VPN de usuário (ponto a site) a um gateway de VPN de usuário de Hub virtual (ponto a site).
* **Conexão de rede virtual do Hub**: conecta redes virtuais a um hub virtual.

Você pode definir a configuração de roteamento para uma conexão de rede virtual durante a instalação. Por padrão, todas as conexões são associadas e propagadas para a tabela de rotas padrão.

### <a name="association"></a><a name="association"></a>Associação

Cada conexão é associada a uma tabela de rotas. Associar uma conexão a uma tabela de rotas permite que o tráfego seja enviado para o destino indicado como rotas na tabela de rotas. A configuração de roteamento da conexão mostrará a tabela de rotas associada.  Várias conexões podem ser associadas à mesma tabela de rotas. Todas as conexões VPN, ExpressRoute e VPN de usuário são associadas à mesma tabela de rotas (padrão).

Por padrão, todas as conexões são associadas a uma **tabela de rotas padrão** em um hub virtual. Cada Hub virtual tem sua própria tabela de rotas padrão, que pode ser editada para adicionar uma rota estática (s). As rotas adicionadas estaticamente têm precedência sobre rotas aprendidas dinamicamente para os mesmos prefixos.

:::image type="content" source="./media/about-virtual-hub-routing/concepts-association.png" alt-text="Associação":::

### <a name="propagation"></a><a name="propagation"></a>Propagação

As conexões propagam dinamicamente as rotas para uma tabela de rotas. Com uma conexão VPN, conexão do ExpressRoute ou conexão de configuração P2S, as rotas são propagadas do Hub virtual para o roteador local usando o BGP. As rotas podem ser propagadas para uma ou várias tabelas de rotas.

Uma **tabela de rota None** também está disponível para cada Hub virtual. A propagação para a tabela de rotas None implica que nenhuma rota deve ser propagada da conexão. As conexões VPN, ExpressRoute e VPN de usuário propagam rotas para o mesmo conjunto de tabelas de rotas.

:::image type="content" source="./media/about-virtual-hub-routing/concepts-propagation.png" alt-text="Propagação":::

### <a name="configuring-static-routes-in-a-virtual-network-connection"></a><a name="static"></a>Configurando rotas estáticas em uma conexão de rede virtual

A configuração de rotas estáticas fornece um mecanismo para direcionar o tráfego por meio de um IP do próximo salto, que pode ser de uma NVA (solução de virtualização de rede) provisionada em uma VNet do spoke conectada a um hub virtual. A rota estática é composta por um nome de rota, uma lista de prefixos de destino e um IP do próximo salto.

> [!NOTE]
> Alguns desses novos conceitos para associação, propagação e rotas estáticas em uma conexão de rede virtual ainda podem ser distribuídos e devem ser concluídos na semana de agosto.
>

## <a name="route-tables-in-basic-and-standard-virtual-wans-prior-to-the-feature-set-of-association-and-propagation"></a><a name="route"></a>Direcionar tabelas em WANs virtuais básicas e padrão antes do conjunto de recursos de associação e propagação

As tabelas de rotas agora têm recursos para associação e propagação. Uma tabela de rotas pré-existente é uma tabela de rotas que não tem esses recursos. Se você tiver rotas pré-existentes no roteamento de Hub e quiser usar os novos recursos, considere o seguinte:

* **Clientes de WAN virtual padrão com rotas pré-existentes no Hub virtual**:

Para usar novos recursos de tabela de rotas, aguarde até a semana de 3 de agosto para que a implantação seja concluída no Azure. Se você tiver rotas pré-existentes na seção de roteamento para o Hub no portal do Azure, você precisará primeiro excluí-las e, em seguida, tentar criar novas tabelas de rotas (disponíveis na seção de tabelas de rotas para o Hub em portal do Azure)

* **Clientes básicos de WAN virtual com rotas pré-existentes no Hub virtual**: para usar os novos recursos de tabela de rotas, aguarde até semana de agosto para a conclusão do Azure para concluir. Se você tiver rotas pré-existentes na seção de roteamento para o Hub no portal do Azure, será necessário primeiro excluí-las e, em seguida, **Atualizar** sua Wan virtual básica para a WAN virtual padrão. Consulte [atualizar uma WAN virtual do básico para o Standard](upgrade-virtual-wan.md).

## <a name="next-steps"></a>Próximas etapas

Para configurar o roteamento, consulte [como configurar o roteamento de Hub virtual](how-to-virtual-hub-routing.md).

Para obter mais informações sobre a WAN Virtual, veja as [Perguntas frequentes](virtual-wan-faq.md).
