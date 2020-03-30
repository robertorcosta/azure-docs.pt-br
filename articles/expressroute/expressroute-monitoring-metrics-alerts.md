---
title: 'Azure ExpressRoute: Monitoramento, métricas e alertas'
description: Esta página fornece informações sobre o monitoramento do ExpressRoute
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: cherylmc
ms.openlocfilehash: 268a7e7c94285d3c4fdcb0c5fb91b685c09b58c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436906"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Monitoramento, alertas e métricas do ExpressRoute

Este artigo ajuda você a entender o monitoramento do ExpressRoute, métricas e alertas usando o Azure Monitor. O Azure Monitor é uma opção completa para todas as métricas, alertas e logs de diagnóstico em todo o Azure.
 
>[!NOTE]
>Usar **Métricas Clássicas** não é recomendado.
>

## <a name="expressroute-metrics"></a>Métricas do ExpressRoute

Para exibir **Métricas,** navegue até a página *do Monitor do Azure* e clique *em Métricas*. Para visualizar as métricas **do ExpressRoute,** filtre por *circuitos do*tipo de recurso ExpressRoute . Para visualizar as métricas **do Global Reach,** filtre por *circuitos do* Tipo de Recurso ExpressRoute e selecione um recurso de circuito ExpressRoute que tenha o Alcance Global ativado. Para visualizar as métricas **do ExpressRoute Direct,** filtre o tipo de recurso por *portas ExpressRoute*. 

Uma vez selecionada uma métrica, a agregação padrão será aplicada. Opcionalmente, você pode aplicar a divisão, que mostrará a métrica com diferentes dimensões.

### <a name="available-metrics"></a>Métricas disponíveis
|**Métrica**|**Categoria**|**Dimensões(s)**|**Características(s)**|
| --- | --- | --- | --- |
|Disponibilidade de ARP|Disponibilidade|<ui><li>Peer (roteador ExpressRoute primário/secundário)</ui></li><ui><li> Tipo de peering (Privado/Público/Microsoft)</ui></li>|ExpressRoute|
|Disponibilidade do BGP|Disponibilidade|<ui><li> Peer (roteador ExpressRoute primário/secundário)</ui></li><ui><li> Tipo de peering</ui></li>|ExpressRoute|
|BitsInPerSecond|Tráfego|<ui><li> Tipo de peering (ExpressRoute)</ui></li><ui><li>Link (ExpressRoute Direct)</ui></li>| <li> ExpressRoute</li><li>ExpressRoute Direct|
|BitsOutPerSecond|Tráfego| <ui><li>Tipo de peering (ExpressRoute)</ui></li><ui><li> Link (ExpressRoute Direct) | <ui><li>ExpressRoute<ui><li>ExpressRoute Direct</ui></li> |
|GlobalReachBitsPerSegundo|Tráfego|<ui><li>Skey de circuito peered (chave de serviço)</ui></li>|Alcance Global|
|GlobalReachBitsOutSegundo|Tráfego|<ui><li>Skey de circuito peered (chave de serviço)</ui></li>|Alcance Global|
|AdminState|Conectividade Física|Link|ExpressRoute Direct|
|Protocolo de linha|Conectividade Física|Link|ExpressRoute Direct|
|RxLightLevel|Conectividade Física|<ui><li>Link</ui></li><ui><li>Lane</ui></li>|ExpressRoute Direct|
|TxLightLevel|Conectividade Física|<ui><li>Link</ui></li><ui><li>Lane</ui></li>|ExpressRoute Direct|
>[!NOTE]
>O uso *do GlobalGlobalReachBitsPerPerSecond* e *do GlobalGlobalGlobalReachBitsOutPerSecond* só será visível se pelo menos uma conexão Global Reach for estabelecida.
>

## <a name="circuits-metrics"></a>Métricas de circuitos

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>Bits Dentro e Fora - Métricas em todos os peerings

Você pode visualizar métricas em todos os peerings em um determinado circuito ExpressRoute.

![métricas de circuito](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

### <a name="bits-in-and-out---metrics-per-peering"></a>Bits Dentro e Fora - Métricas por peering

Você pode exibir métricas para emparelhamento privado, público e da Microsoft em bits por segundo.

![métricas por emparelhamento](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

### <a name="bgp-availability---split-by-peer"></a>Disponibilidade do BGP - Dividido por Peer  

Você pode visualizar a disponibilidade próxima do BGP em tempo real entre peerings e pares (roteadores ExpressRoute primários e secundários). Este painel mostra a sessão BGP primária para peering privado e a segunda sessão BGP para peering privado. 

![Disponibilidade de BGP por par](./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg) 

### <a name="arp-availability---split-by-peering"></a>Disponibilidade de ARP - Dividida por Peering  

Você pode visualizar a disponibilidade próxima de [ARP](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager) em tempo real entre peerings e pares (roteadores ExpressRoute primários e secundários). Este painel mostra a sessão ARP de peering privada em ambos os pares, mas completa para baixo para a Microsoft olhando entre peerings. A agregação padrão (Média) foi utilizada entre ambos os pares.  

![Disponibilidade de ARP por par](./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg) 

## <a name="expressroute-direct-metrics"></a>Métricas diretas do ExpressRoute

### <a name="admin-state---split-by-link"></a>Estado de Admin - Dividido por link
Você pode visualizar o estado de admin para cada link do par de portas ExpressRoute Direct.

![er estado de admin direto](./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg)

### <a name="bits-in-per-second---split-by-link"></a>Bits em por segundo - Dividido por link
Você pode visualizar os bits em por segundo em ambos os links do par de portas ExpressRoute Direct. 

![er bits diretoem por segundo](./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg)

### <a name="bits-out-per-second---split-by-link"></a>Bits Out Por Segundo - Dividido por link
Você também pode visualizar os bits por segundo em ambos os links do par de portas ExpressRoute Direct. 

![er bits direto saem por segundo](./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg)

### <a name="line-protocol---split-by-link"></a>Protocolo de Linha - Dividido por link
Você pode visualizar o protocolo de linha em cada link do par de portas ExpressRoute Direct.

![er protocolo de linha direta](./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg)

### <a name="rx-light-level---split-by-link"></a>Rx Light Level - Dividido por link
Você pode visualizar o nível de luz Rx (o nível de luz que a porta ExpressRoute Direct está **recebendo)** para cada porta. Níveis saudáveis de luz Rx geralmente caem dentro de uma faixa de -10 a 0 dBm

![er linha direta Rx Nível de luz](./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg)

### <a name="tx-light-level---split-by-link"></a>Nível de Luz Tx - Dividido por link
Você pode visualizar o nível de luz Tx (o nível de luz que a porta ExpressRoute Direct está **transmitindo)** para cada porta. Níveis saudáveis de luz Tx geralmente caem dentro de uma faixa de -10 a 0 dBm

![er linha direta Rx Nível de luz](./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg)

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>Conexões de gateway do ExpressRoute em bits por segundos

![conexões de gateway](./media/expressroute-monitoring-metrics-alerts/erconnections.jpg )

## <a name="alerts-for-expressroute-gateway-connections"></a>Alertas para conexões de gateway do ExpressRoute

1. Para configurar alertas, navegue até **Azure Monitor** e clique em **Alertas**.

   ![alertas](./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg)

2. Clique em **+ Selecionar destino** e selecione o recurso de conexão de gateway do ExpressRoute.

   ![destino]( ./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg)
3. Defina os detalhes do alerta.

   ![grupo de ações](./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg)

4. Defina e adicione o grupo de ações.

   ![adicionar grupo de ações](./media/expressroute-monitoring-metrics-alerts/actiongroup.png)

## <a name="alerts-based-on-each-peering"></a>Alertas com base em cada emparelhamento

 ![o quê](./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg)

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>Configurar alertas para os logs de atividade em circuitos

Nos **Critérios de Alerta**, você pode selecionar **Log de Atividades** para o tipo de sinal e selecione o sinal.

  ![outro](./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg)
  
## <a name="next-steps"></a>Próximas etapas

Configurar sua conexão do ExpressRoute.
  
  * [Criar e modificar um circuito](expressroute-howto-circuit-arm.md)
  * [Criar e modificar a configuração de emparelhamento](expressroute-howto-routing-arm.md)
  * [Vincular uma rede virtual a um circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)
