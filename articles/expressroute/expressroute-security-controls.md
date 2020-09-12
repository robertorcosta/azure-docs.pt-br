---
title: 'Azure ExpressRoute: controles de segurança'
description: Saiba mais sobre os controles de segurança no Azure ExpressRoute, que são qualidades ou recursos que ajudam a prevenir, detectar e responder a vulnerabilidades de segurança.
services: expressroute
ms.service: expressroute
author: duongau
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: duau
ms.openlocfilehash: 24057de44f3d28df96bcb93e89af9c3afa6fa3c6
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89394900"
---
# <a name="security-controls-for-azure-expressroute"></a>Controles de segurança para o Azure ExpressRoute

Este artigo documenta os controles de segurança internos do Azure ExpressRoute.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controle de segurança | Sim/Não | Observações |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| N/D |  |
| Suporte à injeção de VNet| N/D | |
| Isolamento de rede e suporte de firewall| Sim | Cada cliente está contido em seu próprio domínio de roteamento e encapsulado para sua própria VNet |
| Suporte a túnel forçado| N/D | Via Border Gateway Protocol (BGP). |

## <a name="monitoring--logging"></a>Monitorando & log

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | Consulte [monitoramento, métricas e alertas do ExpressRoute](expressroute-monitoring-metrics-alerts.md).|
| Registro e auditoria do plano de gerenciamento e controle| Sim |  |
| Log e auditoria do plano de dados| Não |   |

## <a name="identity"></a>Identidade

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Autenticação| Sim | Conta de serviço do gateway para Microsoft (GWM) (controlador); Acesso JIT (just in time) para desenvolvimento e OP. |
| Autorização|  Sim |Conta de serviço do gateway para Microsoft (GWM) (controlador); Acesso JIT (just in time) para desenvolvimento e OP. |

## <a name="data-protection"></a>Proteção de dados

| Controle de segurança | Sim/Não | Observações |
|---|---|--|
| Criptografia no lado do servidor em repouso: chaves gerenciadas pela Microsoft |  N/D | O ExpressRoute não armazena dados do cliente. |
| Criptografia no lado do servidor em repouso: chaves gerenciadas pelo cliente (BYOK) | N/D |  |
| Criptografia em nível de coluna (serviços de dados do Azure)| N/D | |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet)| Não | |
| Chamadas criptografadas à API| Sim | Por meio de [Azure Resource Manager](../azure-resource-manager/index.yml) e HTTPS. |


## <a name="configuration-management"></a>Gerenciamento de configuração

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim | Por meio do NRP (provedor de recursos de rede). |

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [controles de segurança internos nos serviços do Azure](../security/fundamentals/security-controls.md).