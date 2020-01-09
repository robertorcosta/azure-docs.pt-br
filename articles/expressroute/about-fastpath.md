---
title: Sobre o Azure ExpressRoute FastPath
description: Saiba mais sobre o Azure ExpressRoute FastPath para enviar tráfego de rede ignorando o gateway
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: cherylmc
ms.openlocfilehash: c03be46207e7698d0557729c546488412c0cc5dd
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75437093"
---
# <a name="about-expressroute-fastpath"></a>Sobre o ExpressRoute FastPath

O gateway de rede virtual ExpressRoute foi projetado para trocar rotas de rede e rotear o tráfego de rede. O FastPath foi projetado para melhorar o desempenho do caminho de dados entre sua rede local e sua rede virtual. Quando habilitado, o FastPath envia o tráfego de rede diretamente às máquinas virtuais na rede virtual, ignorando o gateway.

## <a name="requirements"></a>Requisitos

### <a name="circuits"></a>Circuitos

O FastPath está disponível em todos os circuitos do ExpressRoute.

### <a name="gateways"></a>Gateways

O FastPath ainda exige que um gateway de rede virtual seja criado para trocar rotas entre a rede virtual e a rede local. Para obter mais informações sobre gateways de rede virtual e ExpressRoute, incluindo informações de desempenho e SKUs de gateway, consulte [gateways de rede virtual do ExpressRoute](expressroute-about-virtual-network-gateways.md).

Para configurar o FastPath, o gateway de rede virtual deve ser:

* Ultra Desempenho
* ErGw3AZ

#### <a name="aggthroughput"></a>Desempenhos estimados por SKU de gateway
A tabela a seguir mostra os tipos de gateway e os desempenhos estimados. Esta tabela aplica-se a ambos os modelos de implantação do Gerenciador de Recursos e clássico.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

> [!IMPORTANT]
> O desempenho do aplicativo depende de vários fatores, como a latência de ponta a ponta e o número de fluxos de tráfego abertos pelo aplicativo. Os números na tabela representam o limite superior que o aplicativo, teoricamente, pode atingir em um ambiente ideal.
>
>

## <a name="supported-features"></a>Recursos compatíveis

Embora o FastPath dê suporte à maioria das configurações, ele não oferece suporte aos seguintes recursos:

* UDR na sub-rede de gateway: se você aplicar um UDR à sub-rede de gateway de sua rede virtual, o tráfego de rede da rede local continuará a ser enviado para o gateway de rede virtual.

* Emparelhamento VNet: se você tiver outras redes virtuais emparelhadas com aquela que está conectada ao ExpressRoute, o tráfego de rede da sua rede local para as outras redes virtuais (ou seja, o chamado de "spoke" VNets) continuará a ser enviado para a rede virtual Gateway. A solução alternativa é conectar todas as redes virtuais ao circuito do ExpressRoute diretamente.

* Load Balancer básica: se você implantar um balanceador de carga interno básico em sua rede virtual ou o serviço de PaaS do Azure que você implantar em sua rede virtual usar um balanceador de carga interno básico, o tráfego de rede da sua rede local para os IPs virtuais hospedados no O Load Balancer básico será enviado para o gateway de rede virtual. A solução é atualizar o balanceador de carga básico para um [balanceador de carga padrão](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview).

* Link privado: se você se conectar a um [ponto de extremidade privado](../private-link/private-link-overview.md) em sua rede virtual a partir de sua rede local, a conexão passará pelo gateway de rede virtual.
 
## <a name="next-steps"></a>Próximos passos

Para habilitar o FastPath, consulte [vincular uma rede virtual ao ExpressRoute](expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).
