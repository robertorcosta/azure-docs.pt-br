---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 02/08/2018
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: 8b08e0ced0c7094890a80c37452c7f1b001fe511
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888682"
---
| | SKU Standard | SKU Básico |
| --- | --- | --- |
| Tamanho do pool de back-end | Dá suporte a até 1.000 instâncias. | Dá suporte a até 100 instâncias. |
| Pontos de extremidade de pool de back-end | Qualquer máquina virtual em uma rede virtual única, incluindo a combinação de máquinas virtuais, conjuntos de disponibilidade e conjuntos de dimensionamento de máquinas virtuais. | Máquinas virtuais em um conjunto de disponibilidade ou conjunto de dimensionamento da máquina virtual. |
| [Investigações de integridade](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Comportamento de investigação de integridade inoperante](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | As conexões TCP permanecem ativas com uma instância de investigação inoperante __e__ todas as investigações inoperantes. | As conexões TCP permanecem ativas com uma instância de investigação inoperante. Todas as conexões TCP terminam com todas as investigações inoperantes. |
| Zonas de Disponibilidades | Front-ends com zonas e redundância de zonas para tráfego de entrada e de saída. Os mapeamentos de fluxos de saída permanecem em vigor em caso de falha da zona. Balanceamento de carga entre zonas. | Não disponível |
| Diagnósticos | Azure Monitor. Métricas multidimensionais, incluindo contadores de bytes e pacotes. Status de investigação de integridade. Tentativas de conexão (TCP SYN). Integridade da conexão de saída (fluxos SNAT com êxito e com falha). Medições de planos de dados ativos. | Análise de Logs do Azure somente para Balanceador de Carga público. Alerta de esgotamento de SNAT. Contagem de integridade do pool de back-end. |
| Portas de alta disponibilidade | Balanceador de Carga Interno | Não disponível |
| Segurança por padrão | O IP público, os pontos de extremidade do Balanceador de Carga público e os pontos de extremidade do Balanceador de Carga interno estão fechados para fluxos de entrada, a menos que sejam permitidos por um grupo de segurança de rede. | Abertos por padrão. Grupo de segurança de rede opcional. |
| [Conexões de saída](../articles/load-balancer/load-balancer-outbound-connections.md) | Você pode definir explicitamente com base no pool de NAT de saída com [regras de saída](../articles/load-balancer/load-balancer-outbound-rules-overview.md). Você pode usar vários front-ends com recusa de regra de balanceamento de carga. Um cenário de saída _precisa_ ser explicitamente criado para a máquina virtual, o conjunto de disponibilidade ou o conjunto de dimensionamento de máquinas virtuais para usar a conectividade de saída. Os pontos de extremidade de serviço de rede virtual podem ser acessados sem definir a conectividade de saída e não são considerados dados processados. Qualquer endereço IP público, incluindo serviços de PaaS do Azure que não estão disponíveis como pontos de extremidade de serviço de rede virtual, devem ser acessados por meio da conectividade de saída e são considerados como dados processados. Quando apenas um Balanceador de Carga interno estiver atendendo a uma máquina virtual, um conjunto de disponibilidade ou um conjunto de dimensionamento de máquinas virtuais, as conexões de saída via SNAT padrão não estarão disponíveis. Nesse caso, use as [regras de saída](../articles/load-balancer/load-balancer-outbound-rules-overview.md). A programação de SNAT de saída depende do protocolo de transporte da regra de balanceamento de carga de entrada. | Front-end único, selecionado aleatoriamente quando vários front-ends estão presentes. Quando somente um Balanceador de Carga interno estiver atendendo a uma máquina virtual, um conjunto de disponibilidade ou um conjunto de dimensionamento de máquinas virtuais, um SNAT padrão será usado. |
| [Regras de saída](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | Configuração de NAT de saída declarativa, usando endereços IP públicos, prefixos de IP públicos ou ambos. Tempo limite de ociosidade de saída configurável (4 a 120 minutos). Alocação da porta SNAT personalizada | Não disponível |
| [Redefinição de TCP quando ocioso](../articles/load-balancer/load-balancer-tcp-reset.md) | Habilitar a Redefinição de TCP (TCP RST) quando o tempo limite de ociosidade for atingido em qualquer regra | Não disponível |
| [Vários front-ends](../articles/load-balancer/load-balancer-multivip-overview.md) | Entrada e [saída](../articles/load-balancer/load-balancer-outbound-connections.md) | Somente entrada |
| Operações de Gerenciamento | Maioria das operações < 30 segundos | 60-90+ segundos típicos |
| Contrato de Nível de Serviço | 99,99% para o caminho de dados com duas máquinas virtuais íntegras. | Não aplicável | 
| Preços | Cobrado com base no número de regras, dados processados de entrada e saída associados ao recurso. | Sem encargos |
|  |  |  |
