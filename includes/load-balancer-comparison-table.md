---
title: incluir arquivo
description: incluir arquivo
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 02/21/2020
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: 33e6fecafa2b9748a27794800365974ebc975d0e
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78202540"
---
| | Standard Load Balancer | Load Balancer Básico |
| --- | --- | --- |
| [Tamanho do pool de back-end](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer) | Dá suporte a até 1.000 instâncias. | Dá suporte a até 300 instâncias. |
| Pontos de extremidade de pool de back-end | Máquinas virtuais ou conjuntos de dimensionamento de máquinas virtuais em uma rede virtual única. | Máquinas virtuais em um conjunto de disponibilidade ou conjunto de dimensionamento da máquina virtual. |
| [Investigações de integridade](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Comportamento de investigação de integridade inoperante](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | As conexões TCP permanecem ativas com uma instância de investigação inoperante __e__ todas as investigações inoperantes. | As conexões TCP permanecem ativas com uma instância de investigação inoperante. Todas as conexões TCP terminam com todas as investigações inoperantes. |
| Zonas de Disponibilidades | Front-ends com zonas e redundância de zonas para tráfego de entrada e de saída. | Não disponível |
| Diagnósticos | [Métricas multidimensionais do Azure Monitor](../articles/load-balancer/load-balancer-standard-diagnostics.md) | [Logs do Azure Monitor](../articles/load-balancer/load-balancer-monitor-log.md) |
| Portas de alta disponibilidade | [Disponível para o Balanceador de Carga Interno](../articles/load-balancer/load-balancer-ha-ports-overview.md) | Não disponível |
| Segurança por padrão | Fechado para fluxos de entrada, a menos que permitido por um grupo de segurança de rede. Observe que o tráfego interno da VNet para o balanceador de carga interno é permitido. | Abertos por padrão. Grupo de segurança de rede opcional. |
| Regras de saída | [Configuração de NAT de saída declarativa](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | Não disponível |
| Redefinição de TCP quando ocioso | [Disponível em qualquer regra](../articles/load-balancer/load-balancer-tcp-reset.md) | Não disponível |
| [Vários front-ends](../articles/load-balancer/load-balancer-multivip-overview.md) | Entrada e [saída](../articles/load-balancer/load-balancer-outbound-connections.md) | Somente entrada |
| Operações de Gerenciamento | Maioria das operações < 30 segundos | 60-90+ segundos típicos |
| Contrato de Nível de Serviço | [99.99%](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/) | Não disponível | 
