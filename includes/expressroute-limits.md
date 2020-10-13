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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89147765"
---
| Recurso | Limite |
| --- | --- |
| Circuitos do ExpressRoute por assinatura |10 |
| Circuitos do ExpressRoute por região por assinatura, com o Azure Resource Manager |10 |
| Número máximo de rotas anunciadas para o emparelhamento privado do Azure com o ExpressRoute Standard |4.000 |
| Número máximo de rotas anunciadas para o emparelhamento privado do Azure com o complemento ExpressRoute Premium |10.000 |
| Número máximo de rotas anunciadas do emparelhamento privado do Azure do espaço de endereço da VNet para uma conexão do ExpressRoute |200 |
| Número máximo de rotas anunciadas para emparelhamento do Microsoft com o Standard do ExpressRoute |200 |
| Número máximo de rotas anunciadas para o emparelhamento da Microsoft com o complemento ExpressRoute Premium |200 |
| Número máximo de circuitos do ExpressRoute vinculados à mesma rede virtual no mesmo local de emparelhamento |4 |
| Número máximo de circuitos do ExpressRoute vinculados à mesma rede virtual em diferentes locais de emparelhamento |4 |
| Número de links de rede virtual permitido por circuito do ExpressRoute |Confira a tabela [Número de redes virtuais por circuito do ExpressRoute](#vnetpercircuit).  |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a><a name="vnetpercircuit"></a> Número de redes virtuais por circuito do ExpressRoute
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
| 40 Gbps* |10 |100 |
| 100 Gbps* |10 |100 |

**Somente o ExpressRoute Direct de 100 Gbps*

> [!NOTE]
> Conexões de Alcance Global são contadas para o limite de conexões de rede virtual por circuito do ExpressRoute. Por exemplo, um Circuito Premium de 10 Gbps permitiria 5 conexões de Alcance Global e 95 conexões com os Gateways do ExpressRoute ou 95 conexões de Alcance Global e 5 conexões com os Gateways do ExpressRoute ou qualquer outra combinação até o limite de 100 conexões para o circuito.
