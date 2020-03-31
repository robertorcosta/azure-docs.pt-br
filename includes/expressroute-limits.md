---
title: incluir arquivo
description: incluir arquivo
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 07/25/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d1402bcc0c46003429e1809e8d09e9662218cd82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334581"
---
| Recurso | Limite |
| --- | --- |
| Circuitos do ExpressRoute por assinatura |10 |
| Circuitos ExpressRoute por região por assinatura, com o Azure Resource Manager |10 |
| Número máximo de rotas anunciadas para peering privado do Azure com O Padrão ExpressRoute |4.000 |
| Número máximo de rotas anunciadas para peering privado do Azure com complemento ExpressRoute Premium |10.000 |
| Número máximo de rotas anunciadas a partir do peering privado do Azure a partir do espaço de endereço VNet para uma conexão ExpressRoute |200 |
| Número máximo de rotas anunciadas para o peering da Microsoft com o ExpressRoute Standard |200 |
| Número máximo de rotas anunciadas para o peering da Microsoft com complemento ExpressRoute Premium |200 |
| Número máximo de circuitos ExpressRoute vinculados à mesma rede virtual no mesmo local de peering |4 |
| Número máximo de circuitos do ExpressRoute vinculados à mesma rede virtual em diferentes locais de emparelhamento |4 |
| Número de links de rede virtual permitido por circuito do ExpressRoute |Consulte o número de redes virtuais na tabela [do circuito ExpressRoute.](#vnetpercircuit)  |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a><a name="vnetpercircuit"></a>Número de redes virtuais por circuito ExpressRoute
| **Tamanho do circuito** | **Número de links de rede virtuais para Padrão** | **Número de links de rede virtual com complemento Premium** |
| --- | --- | --- |
| 50 Mbps |10 |20 |
| 100 Mbps |10 |25 |
| 200 Mbps |10 |25 |
| 500 Mbps |10 |40 |
| 1 Gbps |10 |50 |
| 2 Gbps |10 |60 |
| 5 Gbps |10 |75 |
| 10 Gbps |10 |100 |
| 40 Gbps* |10 |100 |
| 100 Gbps* |10 |100 |

**100 Gbps ExpressRoute Direct Somente*

> [!NOTE]
> As conexões Global Reach contam com o limite de conexões de rede virtuais por Circuito ExpressRoute. Por exemplo, um Circuito Premium de 10 Gbps permitiria 5 conexões global de alcance e 95 conexões para os Gateways ExpressRoute ou 95 conexões Global Reach e 5 conexões para os Gateways ExpressRoute ou qualquer outra combinação até o limite de 100 conexões para o circuito.
