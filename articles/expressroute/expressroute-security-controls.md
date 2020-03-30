---
title: 'Azure ExpressRoute: Controles de segurança'
description: Uma lista de verificação de controles de segurança para avaliar o Azure ExpressRoute
services: expressroute
ms.service: expressroute
author: msmbaldwin
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: a288b44c07bc2df8529f07264dcee648f3af379a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74079925"
---
# <a name="security-controls-for-azure-expressroute"></a>Controles de segurança para Azure ExpressRoute

Este artigo documenta os controles de segurança incorporados no Azure ExpressRoute.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controle de segurança | Sim/Não | Observações |
|---|---|--|
| Suporte a ponto final de serviço| N/D |  |
| Suporte à injeção VNet| N/D | |
| Suporte de isolamento de rede e firewall| Sim | Cada cliente está contido em seu próprio domínio de roteamento e encapsulado para sua própria VNet |
| Suporte forçado de tunelamento| N/D | Via Border Gateway Protocol (BGP). |

## <a name="monitoring--logging"></a>Monitoramento & registro

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao monitoramento do Azure (análises de log, insights de aplicativos, etc.)| Sim | Consulte [o monitoramento, métricas e alertas do ExpressRoute](expressroute-monitoring-metrics-alerts.md).|
| Registro e auditoria de plano de controle e gerenciamento| Sim |  |
| Registro e auditoria de data plan| Não |   |

## <a name="identity"></a>Identidade

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Autenticação| Sim | Conta de serviço para Gateway for Microsoft (GWM) (controlador); Acesso just in Time (JIT) para Dev e OP. |
| Autorização|  Sim |Conta de serviço para Gateway for Microsoft (GWM) (controlador); Acesso just in Time (JIT) para Dev e OP. |

## <a name="data-protection"></a>Proteção de dados

| Controle de segurança | Sim/Não | Observações |
|---|---|--|
| Criptografia do lado do servidor em repouso: chaves gerenciadas pela Microsoft |  N/D | O ExpressRoute não armazena dados de clientes. |
| Criptografia do lado do servidor em repouso: chaves gerenciadas pelo cliente (BYOK) | N/D |  |
| Criptografia de nível de coluna (Azure Data Services)| N/D | |
| Criptografia em trânsito (como criptografia ExpressRoute, criptografia VNet e criptografia VNet-VNet)| Não | |
| Chamadas criptografadas à API| Sim | Através [do Azure Resource Manager](../azure-resource-manager/index.yml) e HTTPS. |


## <a name="configuration-management"></a>Gerenciamento de configuração

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao gerenciamento de configuração (versão de configuração, etc.)| Sim | Via NRP (Network Resource Provider, provedor de recursos de rede). |

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [controles de segurança incorporados nos serviços do Azure.](../security/fundamentals/security-controls.md)