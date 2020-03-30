---
title: Usando o Azure ExpressRoute para suportar usuários remotos
description: Esta página descreve como você pode aproveitar o Azure ExpressRoute para permitir o funcionamento remoto devido à pandemia COVID-19.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/22/2020
ms.author: ajitbhu
ms.openlocfilehash: 2fe990fd9c5922dd2e059dbb212cd8bb1cd3726e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336661"
---
# <a name="using-azure-expressroute-to-create-hybrid-connectivity-to-support-remote-users"></a>Usando o Azure ExpressRoute para criar conectividade híbrida para suportar usuários remotos

Este artigo descreve como você pode aproveitar a rede ExpressRoute, Azure, Microsoft e o ecossistema de parceiros do Azure para trabalhar remotamente.

## <a name="connecting-to-azure-services-with-expressroute"></a>Conectando-se aos serviços do Azure com o ExpressRoute

>[!NOTE]
>Este artigo descreve como você pode aproveitar a rede ExpressRoute, Azure, Microsoft e o ecossistema de parceiros do Azure para trabalhar remotamente e mitigar problemas de rede que você está enfrentando por causa da crise do COVID-19.
>

[ExpressRoute](expressroute-introduction.md) é um serviço do Azure que permite conectividade privada entre data centers da Microsoft e infra-estrutura que está em suas instalações ou em uma instalação de colocation. As conexões de ExpressRoute não passam pela Internet pública. Eles oferecem conectividade, confiabilidade e velocidades seguras, com latências mais baixas e consistentes do que as conexões típicas pela Internet.

## <a name="useful-links"></a>Links úteis

* [Como aumentar a largura de banda para o circuito ExpressRoute existente](expressroute-howto-circuit-portal-resource-manager.md#modify)
* [Monitoramento expressroute, métricas e alertas](expressroute-monitoring-metrics-alerts.md#expressroute-gateway-connections-in-bitsseconds)
* [Otimização de rotas sobre ExpressRoute](expressroute-optimize-routing.md)
* [Azure ExpressRoute para O365](https://docs.microsoft.com/office365/enterprise/azure-expressroute?redirectSourcePath=%252farticle%252f6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
* [Considerações de roteamento assimétrico](expressroute-asymmetric-routing.md)
* [Como abrir uma solicitação de suporte no portal Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

## <a name="troubleshoot"></a>Solução de problemas

* [Verificar conectividade do ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* Obtendo tabela ARP no [Gerenciador de Recursos](expressroute-troubleshooting-arp-resource-manager.md) e [Classic](expressroute-troubleshooting-arp-classic.md)
* [Redefinir um circuito com falha](reset-circuit.md)
* [Solucionar problemas no desempenho da rede](expressroute-troubleshooting-network-performance.md)

## <a name="next-steps"></a>Próximas etapas

* [Conheça os modelos de conectividade ExpressRoute](expressroute-connectivity-models.md)
* Saiba mais sobre conexões e domínios de roteamento do ExpressRoute. Veja [circuitos expressroute e domínios de roteamento](expressroute-circuit-peerings.md)
* Encontrar um provedor de serviços. Consulte [os parceiros expressroute e locais de peering](expressroute-locations.md)
* Certifique-se que todos os pré-requisitos foram atendidos. Consulte [Pré-requisitos do ExpressRoute](expressroute-prerequisites.md).
* [Criar e modificar um circuito do ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md)
* [Criar e modificar o emparelhamento de um circuito do ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Conectar uma rede virtual a um circuito do ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
