---
title: Métricas e alertas para a NAT da Rede Virtual do Azure
titleSuffix: Azure Virtual Network
description: Entenda as métricas e os alertas do Azure Monitor disponíveis para a NAT da Rede Virtual.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to understand available Azure Monitor metrics and alerts for Virtual Network NAT.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2020
ms.author: allensu
ms.openlocfilehash: 268f5f5b7bf1bed27a01085974bea72af6a84f3b
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78304048"
---
# <a name="azure-virtual-network-nat-metrics"></a>Métricas da NAT da Rede Virtual do Azure

Os recursos do Gateway da NAT da Rede Virtual do Azure fornecem métricas multidimensionais. Você pode usar essas métricas para observar a operação e para [solução de problemas](nat-metrics.md).  Os alertas podem ser configurados para problemas críticos, como esgotamento de SNAT.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="NAT de Rede Virtual para saída à Internet">
</p>

*Figura: NAT de Rede Virtual para saída à Internet*

>[!NOTE] 
>A NAT de Rede Virtual está disponível atualmente como versão prévia pública. No momento, ela só está disponível em um conjunto limitado de [regiões](nat-overview.md#region-availability). Essa visualização é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms) para obter detalhes.

## <a name="metrics"></a>Métricas

Os recursos do Gateway da NAT fornecem as seguintes métricas multidimensionais no Azure Monitor:

| Métrica | Descrição | Agregação recomendada | Dimensões |
|---|---|---|---|
| Bytes | Bytes processados de entrada e de saída | SUM | Direção (entrada; saída), protocolo (6 TCP; 17 UDP) |
| Pacotes | Pacotes processados de entrada e de saída | SUM | Direção (entrada; saída), protocolo (6 TCP; 17 UDP) |
| Pacotes removidos | Pacotes removidos pelo Gateway da NAT | SUM | / |
| Contagem de Conexões SNAT | Transições de estado por intervalo | SUM | Estado da conexão, protocolo (6 TCP; 17 UDP) |
| Contagem total de conexões SNAT | Conexões SNAT ativas atuais (~ portas SNAT em uso) | SUM | Protocolo (6 TCP; 17 UDP) |


## <a name="alerts"></a>Alertas

Os alertas das métricas podem ser configurados no Azure Monitor para cada uma das [métricas](#metrics) anteriores.

## <a name="limitations"></a>Limitações

Não há suporte para o Resource Health.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [NAT de Rede Virtual](nat-overview.md)
- Saiba mais sobre o [recurso do Gateway da NAT](nat-gateway-resource.md)
- Saiba mais sobre o [Azure Monitor](../azure-monitor/overview.md)
- Saiba mais sobre a [solução de problemas de recursos do Gateway da NAT](troubleshoot-nat.md).

