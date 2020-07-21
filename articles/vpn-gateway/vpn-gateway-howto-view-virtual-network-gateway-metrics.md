---
title: Exibir métricas de gateway de VPN do Azure
description: Etapas para exibir as métricas do gateway de VPN
services: vpn-gateway
author: kumudD
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 07/12/2020
ms.author: alzam
ms.openlocfilehash: b3a79b8101a55eaf401c20cb118be3b0796b7aca
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86530942"
---
# <a name="view-vpn-gateway-metrics"></a>Exibir métricas de gateway de VPN

Você pode monitorar os gateways de VPN do Azure usando o Azure Monitor. Este artigo discute as métricas que estão disponíveis por meio do Portal. As métricas são leves e podem dar suporte a cenários quase em tempo real, tornando-as úteis para alertas e detecção rápida de problemas.


|**Métrica**   | **Unidade** | **Granularidade** | **Descrição** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| Bytes/s  | 5 minutos| Utilização média da largura de banda combinada de todas as conexões site a site no gateway.     |
|**P2SBandwidth**| Bytes/s  | 1 minuto  | Utilização média da largura de banda combinada de todas as conexões ponto a site no gateway.    |
|**P2SConnectionCount**| Contagem  | 1 minuto  | Contagem de conexões ponto a site no gateway.   |
|**TunnelAverageBandwidth** | Bytes/s    | 5 minutos  | Utilização média de largura de banda de túneis criados no gateway. |
|**TunnelEgressBytes** | Bytes | 5 minutos | Tráfego de saída em túneis criados no gateway.   |
|**TunnelEgressPackets** | Contagem | 5 minutos | Contagem de pacotes de saída em túneis criados no gateway.   |
|**TunnelEgressPacketDropTSMismatch** | Contagem | 5 minutos | Contagem de pacotes de saída descartados em túneis causados por incompatibilidade de seletor de tráfego. |
|**TunnelIngressBytes** | Bytes | 5 minutos | Tráfego de entrada em túneis criados no gateway.   |
|**TunnelIngressPackets** | Contagem | 5 minutos | Contagem de pacotes de entrada em túneis criados no gateway.   |
|**TunnelIngressPacketDropTSMismatch** | Contagem | 5 minutos | Contagem de pacotes de entrada descartados em túneis causados por incompatibilidade de seletor de tráfego. |

## <a name="the-following-steps-help-you-locate-and-view-metrics"></a>As etapas a seguir ajudam a localizar e exibir as métricas:

1. Navegue até o recurso de gateway de rede virtual no portal
2. Selecione **visão geral** para ver as métricas de entrada e saída do túnel total.

   ![Seleções para criar uma regra de alerta](./media/vpn-gateway-howto-view-virtual-network-gateway-metrics/overview.png "Visualizar")

3. Para exibir qualquer uma das outras métricas listadas acima. Clique na seção **métricas** em seu recurso de gateway de rede virtual e selecione a métrica na lista suspensa.

   ![O botão Selecionar e o gateway de VPN na lista de recursos](./media/vpn-gateway-howto-view-virtual-network-gateway-metrics/metrics.png "Selecionar")

## <a name="next-steps"></a>Próximas etapas

Para configurar alertas em métricas de túnel, consulte [configurar alertas em métricas de gateway de VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).

Para configurar alertas em logs de recursos de túnel, consulte [configurar alertas em logs de recursos do gateway de VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).
