---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 75152fabfc33dda0494d871fbdf9a388f4260c0d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495756"
---
Ao criar um gateway de rede virtual, você precisa especificar o SKU do gateway que você deseja usar. Selecione as SKUs que atendem às suas necessidades com base nos tipos de cargas de trabalho, taxas de transferência, recursos e SLAs. Para SKUs de gateway de rede virtual no Zonas de Disponibilidade do Azure, consulte [SKUs de gateway de zonas de disponibilidade do Azure](../articles/vpn-gateway/about-zone-redundant-vnet-gateways.md).

###  <a name="benchmark"></a>SKUs de gateway pelo túnel, a conexão e a taxa de transferência

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

[!INCLUDE [classic SKU](./vpn-gateway-classic-sku-support-include.md)]

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
