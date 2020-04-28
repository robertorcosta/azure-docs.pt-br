---
title: 'Azure ExpressRoute: monitoramento, métricas e alertas'
description: Esta página fornece informações sobre o monitoramento do ExpressRoute
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: cherylmc
ms.openlocfilehash: 268a7e7c94285d3c4fdcb0c5fb91b685c09b58c8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "75436906"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Monitoramento, alertas e métricas do ExpressRoute

Este artigo ajuda você a entender o monitoramento do ExpressRoute, métricas e alertas usando o Azure Monitor. O Azure Monitor é uma opção completa para todas as métricas, alertas e logs de diagnóstico em todo o Azure.
 
>[!NOTE]
>Usar **Métricas Clássicas** não é recomendado.
>

## <a name="expressroute-metrics"></a>Métricas de ExpressRoute

Para exibir as **métricas**, navegue até a página *Azure monitor* e clique em *métricas*. Para exibir as métricas de **expressroute** , filtre por tipo de recurso *circuitos de expressroute*. Para exibir **alcance global** métricas, filtre por tipo de recurso *circuitos do expressroute* e selecione um recurso de circuito do expressroute que tenha alcance global habilitado. Para exibir as métricas **diretas do expressroute** , filtre tipo de recurso por *portas do expressroute*. 

Depois que uma métrica for selecionada, a agregação padrão será aplicada. Opcionalmente, você pode aplicar a divisão, que mostrará a métrica com dimensões diferentes.

### <a name="available-metrics"></a>Métricas disponíveis
|**Métrica**|**Categoria**|**Dimensão (ões)**|**Recurso (s)**|
| --- | --- | --- | --- |
|Disponibilidade do ARP|Disponibilidade|<ui><li>Par (roteador de ExpressRoute primário/secundário)</ui></li><ui><li> Tipo de emparelhamento (privado/público/Microsoft)</ui></li>|ExpressRoute|
|Disponibilidade de BGP|Disponibilidade|<ui><li> Par (roteador de ExpressRoute primário/secundário)</ui></li><ui><li> Tipo de emparelhamento</ui></li>|ExpressRoute|
|BitsInPerSecond|Tráfego|<ui><li> Tipo de emparelhamento (ExpressRoute)</ui></li><ui><li>Link (ExpressRoute direto)</ui></li>| <li> ExpressRoute</li><li>ExpressRoute Direct|
|BitsOutPerSecond|Tráfego| <ui><li>Tipo de emparelhamento (ExpressRoute)</ui></li><ui><li> Link (ExpressRoute direto) | <ui><li>ExpressRoute<ui><li>ExpressRoute Direct</ui></li> |
|GlobalReachBitsInPerSecond|Tráfego|<ui><li>Circuito emparelhado skey (chave de serviço)</ui></li>|Alcance Global|
|GlobalReachBitsOutPerSecond|Tráfego|<ui><li>Circuito emparelhado skey (chave de serviço)</ui></li>|Alcance Global|
|Adminstate|Conectividade física|Link|ExpressRoute Direct|
|LineProtocol|Conectividade física|Link|ExpressRoute Direct|
|RxLightLevel|Conectividade física|<ui><li>Link</ui></li><ui><li>Estreita</ui></li>|ExpressRoute Direct|
|TxLightLevel|Conectividade física|<ui><li>Link</ui></li><ui><li>Estreita</ui></li>|ExpressRoute Direct|
>[!NOTE]
>Usar *GlobalGlobalReachBitsInPerSecond* e *GlobalGlobalReachBitsOutPerSecond* só será visível se pelo menos uma conexão alcance global for estabelecida.
>

## <a name="circuits-metrics"></a>Métricas de circuitos

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>Bits de entrada e saída-métricas em todos os emparelhamentos

Você pode exibir as métricas em todos os emparelhamentos em um determinado circuito do ExpressRoute.

![métricas de circuito](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

### <a name="bits-in-and-out---metrics-per-peering"></a>Métricas de entrada e saída de bits por emparelhamento

Você pode exibir métricas para emparelhamento privado, público e da Microsoft em bits por segundo.

![métricas por emparelhamento](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

### <a name="bgp-availability---split-by-peer"></a>Disponibilidade de BGP-divisão por par  

Você pode exibir quase a disponibilidade em tempo real do BGP entre emparelhamentos e pares (roteadores de ExpressRoute primários e secundários). Esse painel mostra a sessão BGP primária para emparelhamento privado e a segunda sessão BGP para emparelhamento privado. 

![Disponibilidade de BGP por ponto](./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg) 

### <a name="arp-availability---split-by-peering"></a>Disponibilidade ARP-divisão por emparelhamento  

Você pode exibir quase a disponibilidade em tempo real do [ARP](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager) entre emparelhamentos e pares (roteadores de ExpressRoute primários e secundários). Este painel mostra a sessão ARP de emparelhamento privado em ambos os pares, mas é concluída para o emparelhamento da Microsoft entre emparelhamentos. A agregação padrão (média) foi utilizada em ambos os pares.  

![Disponibilidade ARP por ponto](./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg) 

## <a name="expressroute-direct-metrics"></a>Métricas diretas do ExpressRoute

### <a name="admin-state---split-by-link"></a>Estado do administrador – dividir por link
Você pode exibir o estado do administrador para cada link do par de porta do ExpressRoute Direct.

![Estado de administrador direto er](./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg)

### <a name="bits-in-per-second---split-by-link"></a>Bits por segundo – divisão por link
Você pode exibir os bits em por segundo em ambos os links do par de portas diretas do ExpressRoute. 

![bits diretos er em por segundo](./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg)

### <a name="bits-out-per-second---split-by-link"></a>Bits de saída por segundo-divisão por link
Você também pode exibir os bits de saída por segundo em ambos os links do par de portas diretas do ExpressRoute. 

![bits diretos de er de saída por segundo](./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg)

### <a name="line-protocol---split-by-link"></a>Protocolo de linha – dividir por link
Você pode exibir o protocolo de linha em cada link do par de porta do ExpressRoute Direct.

![Protocolo de linha direta er](./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg)

### <a name="rx-light-level---split-by-link"></a>Nível de luz Rx-divisão por link
Você pode exibir o nível de luz Rx (o nível de luz que a porta do ExpressRoute Direct está **recebendo**) para cada porta. Os níveis de luz Rx em boas condições geralmente se enquadram em um intervalo de-10 a 0 dBm

![Nível de luz Rx de linha direta er](./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg)

### <a name="tx-light-level---split-by-link"></a>Nível de luz Tx – dividir por link
Você pode exibir o nível de luz Tx (o nível de luz que a porta do ExpressRoute Direct está **transmitindo**) para cada porta. Os níveis de luz Tx íntegros geralmente se enquadram dentro de um intervalo de-10 a 0 dBm

![Nível de luz Rx de linha direta er](./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg)

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
