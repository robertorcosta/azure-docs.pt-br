---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8087025810214f3edbb74e628698eb69558f3500
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74085216"
---
Ao criar um gateway de rede virtual, você precisa especificar o SKU do gateway que você deseja usar. Selecione as SKUs que atendem às suas necessidades com base nos tipos de cargas de trabalho, taxas de transferência, recursos e SLAs. Para SKUs de gateway de rede virtual no Zonas de Disponibilidade do Azure, consulte [SKUs de gateway de zonas de disponibilidade do Azure](../articles/vpn-gateway/about-zone-redundant-vnet-gateways.md).

###  <a name="benchmark"></a>SKUs de gateway pelo túnel, a conexão e a taxa de transferência

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

> [!NOTE]
> VpnGw SKUs (VpnGw1, VpnGw1AZ, VpnGw2, VpnGw2AZ, VpnGw3, VpnGw3AZ, VpnGw4, VpnGw4AZ, VpnGw5 e VpnGw5AZ) têm suporte apenas para o modelo de implantação do Resource Manager. As redes virtuais clássicas devem continuar a usar as SKUs antigas (herdadas).
>  * Para obter informações sobre como trabalhar com as SKUs de geteway herdadas (Basic, Standard e HIghPerformance), consulte [Trabalhando com o gateway VPN SKUs (SKUs herdadas)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md).
>  * Para SKUs de gateways do ExpressRoute, confira [Gateways de rede virtual para o ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md).
>

###  <a name="feature"></a>SKUs de gateway pelo conjunto de recursos

As novas SKUs do gateway VPN simplificam os conjuntos de recursos oferecidos nos gateways:

| **SKU**| **Recursos**|
| ---    | ---         |
|**Básico** (\*\*)   | **VPN baseada em rota**: 10 túneis para conexões/S2S; nenhuma autenticação RADIUS para P2S; sem IKEv2 para P2S<br>**VPN baseada em política**: (IKEv1): 1 túnel de conexão/S2S; sem P2S|
| **Todas as SKUs Generation1 e Generation2, exceto as básicas** | **VPN baseada em roteamento**: até 30 túneis ( * ), P2S, BGP, ativo-ativo, política IPsec/IKE personalizada, coexistência ExpressRoute/VPN |
|        |             |

(*) Você pode configurar "PolicyBasedTrafficSelectors" para conectar um gateway de VPN baseado em rota a vários dispositivos de firewall baseados em políticas locais. Confira [Conectar gateways de VPN a vários dispositivos VPN baseados em políticas usando o PowerShell](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md) para obter detalhes.

(\*\*) O SKU básico é considerado um SKU herdado. A SKU básica tem certas limitações de recurso. Você não pode redimensionar um gateway que usa uma SKU básica para novas SKUs de gateway, em vez disso, você deve alterar para uma nova SKU, que envolve excluir e recriar o gateway VPN.

###  <a name="workloads"></a>SKUs de gateway-produção versus desenvolvimento-cargas de trabalho de teste

Devido a diferenças nos SLAs e conjuntos de recursos, é recomendável usar as SKUs a seguir para produção versus desenvolvimento e teste:

| **Carga de trabalho**                       | **SKUs**               |
| ---                                | ---                    |
| **Produção, cargas de trabalho críticas** | Todas as SKUs Generation1 e Generation2, exceto as básicas |
| **Teste de desenvolvimento ou prova de conceito**   | Básico (\*\*)                 |
|                                    |                        |

(\*\*) O SKU básico é considerado um SKU herdado e tem limitações de recursos. Verifique se o recurso que você precisa é compatível antes de usar a SKU básica.

Se você estiver usando as SKUs antigas (herdadas), as recomendações de SKU de produção serão Standard e HighPerformance. Para saber mais sobre as SKUs antigas, confira [SKUs de Gateway (herdadas)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md).
