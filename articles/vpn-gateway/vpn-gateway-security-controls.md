---
title: Controles de segurança para Gateway Azure VPN
description: Uma lista de verificação de controles de segurança para avaliar o Azure VPN Gateway
services: sql-database
author: msmbaldwin
manager: rkarlin
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: mbaldwin
ms.openlocfilehash: cdf616b29a93e786ef26af83b5d3b3541f94d67c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972291"
---
# <a name="security-controls-for-azure-vpn-gateway"></a>Controles de segurança para Gateway Azure VPN

Este artigo documenta os controles de segurança incorporados no Azure VPN Gateway.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controle de segurança | Sim/Não | Observações |
|---|---|--|
| Suporte a ponto final de serviço| N/D | |
| Suporte à injeção VNet| N/D | |
| Suporte a isolamento de rede e firewall| Sim | Os gateways VPN são instâncias de VM dedicadas para cada rede virtual do cliente  |
| Suporte forçado de tunelamento| Sim |  |

## <a name="monitoring--logging"></a>Monitoramento & registro

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao monitoramento do Azure (análises de log, insights de aplicativos, etc.)| Sim | Consulte &  [Logs/alerta sustais/alerta sustais do Monitor do Azure](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).[Azure Monitor Metrics/alert](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)  |
| Registro e auditoria de plano de controle e gerenciamento| Sim | Log de atividades do Gerenciador de Recursos do Azure. |
| Registro e auditoria de data plan | Sim | [Azure Monitor Logs de diagnóstico](../azure-resource-manager/management/view-activity-logs.md) para registro e auditoria de conectividade VPN. |

## <a name="identity"></a>Identidade

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Autenticação| Sim | [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) para gerenciar o serviço e configurar o gateway Azure VPN. |
| Autorização| Sim | Autorização de suporte via [RBAC](../role-based-access-control/overview.md). |

## <a name="data-protection"></a>Proteção de dados

| Controle de segurança | Sim/Não | Observações |
|---|---|--|
| Criptografia do lado do servidor em repouso: chaves gerenciadas pela Microsoft | N/D | OS dados do cliente do gateway VPN, NÃO armazenam dados do cliente |
| Criptografia em trânsito (como criptografia ExpressRoute, criptografia VNet e criptografia VNet-VNet)| Sim | O gateway VPN criptografa pacotes de clientes entre gateways Azure VPN e clientes on-premises VPN devices (S2S) ou VPN (P2S). Os gateways VPN também suportam a criptografia VNet-to-VNet. |
| Criptografia do lado do servidor em repouso: chaves gerenciadas pelo cliente (BYOK) | Não | As chaves pré-compartilhadas especificadas pelo cliente são criptografadas em repouso; mas ainda não está integrado com a CMK. |
| Criptografia de nível de coluna (Azure Data Services)| N/D | |
| Chamadas criptografadas à API| Sim | Através [do Azure Resource Manager](../azure-resource-manager/index.yml) e HTTPS  |

## <a name="configuration-management"></a>Gerenciamento de configuração

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao gerenciamento de configuração (versão de configuração, etc.)| Sim | Para operações de gerenciamento, o estado de uma configuração de gateway Azure VPN pode ser exportado como um modelo do Azure Resource Manager e versão ao longo do tempo. |

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [controles de segurança incorporados nos serviços do Azure.](../security/fundamentals/security-controls.md)
