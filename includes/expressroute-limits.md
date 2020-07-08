---
title: arquivo de inclusão
description: incluir arquivo
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 07/25/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d1402bcc0c46003429e1809e8d09e9662218cd82
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80334581"
---
| Recurso | Limite |
| --- | --- |
| Circuitos do ExpressRoute por assinatura |10 |
| Circuitos de ExpressRoute por região por assinatura, com Azure Resource Manager |10 |
| Número máximo de rotas anunciadas para o emparelhamento privado do Azure com o padrão de ExpressRoute |4.000 |
| Número máximo de rotas anunciadas para o emparelhamento privado do Azure com o complemento Premium do ExpressRoute |10.000 |
| Número máximo de rotas anunciadas do emparelhamento privado do Azure do espaço de endereço da VNet para uma conexão do ExpressRoute |200 |
| Número máximo de rotas anunciadas para o emparelhamento da Microsoft com o padrão de ExpressRoute |200 |
| Número máximo de rotas anunciadas para o emparelhamento da Microsoft com o complemento Premium do ExpressRoute |200 |
| Número máximo de circuitos do ExpressRoute vinculados à mesma rede virtual no mesmo local de emparelhamento |4 |
| Número máximo de circuitos do ExpressRoute vinculados à mesma rede virtual em diferentes locais de emparelhamento |4 |
| Número de links de rede virtual permitido por circuito do ExpressRoute |Consulte o [número de redes virtuais por tabela de circuito do ExpressRoute](#vnetpercircuit) .  |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a><a name="vnetpercircuit"></a>Número de redes virtuais por circuito de ExpressRoute
| **Tamanho do circuito** | **Número de links de rede virtual para Standard** | **Número de links de rede virtual com complemento Premium** |
| --- | --- | --- |
| 50 Mbps |10 |20 |
| 100 Mbps |10 |25 |
| 200 Mbps |10 |25 |
| 500 Mbps |10 |40 |
| 1 Gbps |10 |50 |
| 2 Gbps |10 |60 |
| 5 Gbps |10 |75 |
| 10 Gbps |10 |100 |
| 40 Gbps * |10 |100 |
| 100 Gbps * |10 |100 |

**100 Gbps ExpressRoute somente direto*

> [!NOTE]
> Alcance Global as conexões contam com o limite de conexões de rede virtual por circuito de ExpressRoute. Por exemplo, um circuito Premium de 10 Gbps permitiria conexões de 5 Alcance Global e 95 para os gateways de ExpressRoute ou 95 conexões de Alcance Global e 5 conexões com os gateways de ExpressRoute ou qualquer outra combinação até o limite de 100 conexões para o circuito.
