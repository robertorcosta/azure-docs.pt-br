---
title: Sobre o Azure ExpressRoute FastPath
description: Saiba mais sobre o Azure ExpressRoute FastPath para enviar tráfego de rede contornando o gateway
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: cherylmc
ms.openlocfilehash: 265004b1171d1df95b3090676d5836b951c28a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282844"
---
# <a name="about-expressroute-fastpath"></a>Sobre o ExpressRoute FastPath

O gateway de rede virtual ExpressRoute foi projetado para trocar rotas de rede e direcionar o tráfego da rede. O FastPath foi projetado para melhorar o desempenho do caminho de dados entre sua rede local e sua rede virtual. Quando ativado, o FastPath envia o tráfego de rede diretamente para máquinas virtuais na rede virtual, contornando o gateway.

## <a name="requirements"></a>Requisitos

### <a name="circuits"></a>Circuitos

O FastPath está disponível em todos os circuitos do ExpressRoute.

### <a name="gateways"></a>Gateways

O FastPath ainda requer que um gateway de rede virtual seja criado para trocar rotas entre a rede virtual e a rede local. Para obter mais informações sobre gateways de rede virtuais e ExpressRoute, incluindo informações de desempenho e SKUs de gateway, consulte [gateways de rede virtual ExpressRoute](expressroute-about-virtual-network-gateways.md).

Para configurar o FastPath, o gateway de rede virtual deve ser:

* Desempenho ultra
* ErGw3AZ

## <a name="supported-features"></a>Recursos compatíveis

Embora o FastPath suporte a maioria das configurações, ele não suporta os seguintes recursos:

* UDR na sub-rede gateway: Se você aplicar um UDR na sub-rede gateway de sua rede virtual, o tráfego de rede da sua rede local continuará a ser enviado para o gateway de rede virtual.

* VNet Peering: Se você tiver outras redes virtuais com a que está conectada ao ExpressRoute, o tráfego de rede da sua rede local para as outras redes virtuais (ou seja, os chamados VNets "Spoke") continuará a ser enviado para a rede virtual Gateway. A solução é conectar todas as redes virtuais diretamente ao circuito ExpressRoute.

* Balanceador de carga básico: Se você implantar um balanceador de carga interno básico em sua rede virtual ou o serviço Azure PaaS que você implanta em sua rede virtual, use um balanceador de carga interno básico, o tráfego de rede da sua rede local para os IPs virtuais hospedados no O balanceador de carga básico será enviado para o gateway de rede virtual. A solução é atualizar o balanceador de carga Básico para um [balanceador de carga Padrão](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview).

* Link privado: Se você se conectar a um [ponto final privado](../private-link/private-link-overview.md) em sua rede virtual a partir de sua rede local, a conexão passará pelo gateway de rede virtual.
 
## <a name="next-steps"></a>Próximas etapas

Para habilitar o FastPath, consulte [Vincular uma rede virtual ao ExpressRoute](expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).
