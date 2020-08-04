---
title: O que há de novo no Azure Load Balancer
description: Conheça as novidades do Azure Load Balancer, como as últimas notas sobre a versão, problemas conhecidos, correções de bug, funcionalidades preteridas e alterações futuras.
services: load-balancer
author: anavinahar
ms.service: load-balancer
ms.topic: overview
ms.date: 07/07/2020
ms.author: anavin
ms.openlocfilehash: 8b44dc230dbee1b29b9889a1b81e35ebe25f6b97
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87078679"
---
# <a name="whats-new-in-azure-load-balancer"></a>O que há de novo no Azure Load Balancer?

O Azure Load Balancer é atualizado regularmente. Mantenha-se atualizado com os comunicados mais recentes. Este artigo fornece informações sobre:

- As versões mais recentes
- Problemas conhecidos
- Correções de bug
- Funcionalidade preterida (se aplicável)

Você também pode encontrar as atualizações mais recentes do Azure Load Balancer e assinar o RSS feed [aqui](https://azure.microsoft.com/updates/?category=networking&query=load%20balancer).

## <a name="recent-releases"></a>Versões recentes

| Type |Nome |Descrição  |Data de adição  |
| ------ |---------|---------|---------|
| Recurso | Suporte para gerenciamento de pool de back-end baseado em IP (versão prévia) | O Azure Load Balancer é compatível com a adição e a remoção de recursos de um pool de back-end por meio de endereços IPv4 ou IPv6. Isso permite o gerenciamento fácil de contêineres, máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais associados ao Load Balancer. Também permitirá que os endereços IP sejam reservados como parte de um pool de back-end antes da criação dos recursos associados. Saiba mais [aqui](backend-pool-management.md)|Julho de 2020 |
| Recurso| Insights do Azure Load Balancer usando o Azure Monitor | Criado como parte do Azure Monitor para Redes, os clientes agora têm mapas topológicos para todas as suas configurações do Load Balancer e painéis de integridade para seus Standard Load Balancers pré-configurados com métricas no portal do Azure. [Comece a usar e saiba mais](https://azure.microsoft.com/blog/introducing-azure-load-balancer-insights-using-azure-monitor-for-networks/) | Junho de 2020 |
| Validação | Adição de validação para portas de HA | Uma validação foi adicionada para que as regras de porta de HA e não HA só sejam configuráveis quando o IP flutuante estiver habilitado. Anteriormente, essa configuração passava, mas não funcionava conforme o esperado. Nenhuma alteração na funcionalidade foi feita. Você pode saber mais [aqui](load-balancer-ha-ports-overview.md#limitations)| Junho de 2020 |
| Recurso| Suporte a IPv6 para o Azure Load Balancer (em disponibilidade geral) | Você pode ter endereços IPv6 como front-end para seus Azure Load Balancers. Saiba como [criar um aplicativo de pilha dupla aqui](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) |Abril de 2020|
| Recurso| Redefinições de TCP no Tempo Limite de Ociosidade (em disponibilidade geral)| Use as redefinições de TCP para criar um comportamento de aplicativo mais previsível. [Saiba mais](load-balancer-tcp-reset.md)| Fevereiro de 2020 |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure Load Balancer, confira [O que é o Azure Load Balancer?](load-balancer-overview.md) e [perguntas frequentes](load-balancer-faqs.md).
