---
title: Configure alertas nas métricas do Gateway Azure VPN
description: Etapas para configurar alertas em métricas do VPN Gateway
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: d57663f683ba4e2107ec6813a19fac7b2dcdd26a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67605222"
---
# <a name="set-up-alerts-on-vpn-gateway-metrics"></a>Configure alertas nas métricas do VPN Gateway

Este artigo ajuda você a configurar alertas nas métricas do Azure VPN Gateway. O Azure Monitor oferece a capacidade de configurar alertas para os recursos do Azure. Você pode configurar alertas para gateways de rede virtuais do tipo "VPN".


|**Métrica**   | **Unidade** | **Granularidade** | **Descrição** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| Bytes/s  | 5 minutos| Utilização média de largura de banda combinada de todas as conexões site-site no gateway.     |
|**P2SBandwidth**| Bytes/s  | 1 minuto  | Utilização média de largura de banda combinada de todas as conexões ponto a local no gateway.    |
|**P2SConnectionCount**| Contagem  | 1 minuto  | Contagem de conexões ponto a ponto no gateway.   |
|**TunnelAverageBandwidth** | Bytes/s    | 5 minutos  | Utilização média da largura de banda de túneis criados no gateway. |
|**TunnelEgressBytes** | Bytes | 5 minutos | Tráfego de saída em túneis criados no portal.   |
|**TunnelEgressPackets** | Contagem | 5 minutos | Contagem de pacotes de saída em túneis criados no gateway.   |
|**TunnelEgressPacketDropTSMismatch** | Contagem | 5 minutos | Contagem de pacotes de saída jogados em túneis causados por incompatibilidade de seletor de tráfego. |
|**TunnelIngressBytes** | Bytes | 5 minutos | Tráfego de entrada em túneis criados no portal.   |
|**TunnelIngressPackets** | Contagem | 5 minutos | Contagem de pacotes de entrada em túneis criados no gateway.   |
|**TunnelIngressPacketDropTSMismatch** | Contagem | 5 minutos | Contagem de pacotes de entrada jogados em túneis causados por incompatibilidade de seletor de tráfego. |


## <a name="set-up-azure-monitor-alerts-based-on-metrics-by-using-the-azure-portal"></a><a name="setup"></a>Configure alertas do Azure Monitor com base em métricas usando o portal Azure

As etapas de exemplo a seguir criarão um alerta em um gateway para:

- **Métrica:** Largura de banda média do túnel
- **Condição:** Largura de banda > 10 bytes/segundo
- **Janela:** 5 minutos
- **Ação de alerta:** Email



1. Vá para o recurso de gateway de rede virtual e selecione **Alertas** na guia **Monitoramento.** Em seguida, crie uma nova regra de alerta ou edite uma regra de alerta existente.

   ![Seleções para criar uma regra de alerta](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "Criar")

2. Selecione seu gateway VPN como recurso.

   ![O botão Selecionar e o gateway VPN na lista de recursos](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "Selecionar")

3. Selecione uma métrica para configurar para o alerta.

   ![Métrica selecionada na lista de métricas](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "Selecionar")
4. Configure a lógica do sinal. Há três componentes para ele:

    a. **Dimensões**: Se a métrica tiver dimensões, você pode selecionar valores de dimensão específicos para que o alerta avalie apenas dados dessa dimensão. São opcionais.

    b. **Condição**: Esta é a operação para avaliar o valor métrico.

    c. **Tempo**: Especifique a granularidade dos dados métricos e o período de tempo para avaliar o alerta.

   ![Detalhes para configurar a lógica do sinal](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "Selecionar")

5. Para exibir as regras configuradas, selecione **Gerenciar regras de alerta**.

   ![Botão para o gerenciamento de regras de alerta](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "Selecionar")

## <a name="next-steps"></a>Próximas etapas

Para configurar alertas em registros de diagnóstico de túneis, consulte [Configurar alertas nos registros de diagnóstico do VPN Gateway](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).
