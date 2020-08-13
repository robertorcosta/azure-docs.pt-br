---
title: 'Azure ExpressRoute: controles de segurança'
description: Saiba mais sobre os controles de segurança no Azure ExpressRoute, que são qualidades ou recursos que ajudam a prevenir, detectar e responder a vulnerabilidades de segurança.
services: expressroute
ms.service: expressroute
author: msmbaldwin
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: 6a4589d1aa768548f2ae9ffa01f289c823c0b2e5
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192417"
---
# <a name="security-controls-for-azure-expressroute"></a>Controles de segurança para o Azure ExpressRoute

Este artigo documenta os controles de segurança internos do Azure ExpressRoute.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controle de segurança | Sim/Não | Observações |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| N/D |  |
| Suporte à injeção de VNet| N/D | |
| Isolamento de rede e suporte de firewall| Yes | Cada cliente está contido em seu próprio domínio de roteamento e encapsulado para sua própria VNet |
| Suporte a túnel forçado| N/D | Via Border Gateway Protocol (BGP). |

## <a name="monitoring--logging"></a>Monitorando & log

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Yes | Consulte [monitoramento, métricas e alertas do ExpressRoute](expressroute-monitoring-metrics-alerts.md).|
| Registro e auditoria do plano de gerenciamento e controle| Yes |  |
| Log e auditoria do plano de dados| No |   |

## <a name="identity"></a>Identidade

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Autenticação| Yes | Conta de serviço do gateway para Microsoft (GWM) (controlador); Acesso JIT (just in time) para desenvolvimento e OP. |
| Autorização|  Yes |Conta de serviço do gateway para Microsoft (GWM) (controlador); Acesso JIT (just in time) para desenvolvimento e OP. |

## <a name="data-protection"></a>Proteção de dados

| Controle de segurança | Sim/Não | Observações |
|---|---|--|
| Criptografia no lado do servidor em repouso: chaves gerenciadas pela Microsoft |  N/D | O ExpressRoute não armazena dados do cliente. |
| Criptografia no lado do servidor em repouso: chaves gerenciadas pelo cliente (BYOK) | N/D |  |
| Criptografia em nível de coluna (serviços de dados do Azure)| N/D | |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet)| No | |
| Chamadas criptografadas à API| Yes | Por meio de [Azure Resource Manager](../azure-resource-manager/index.yml) e HTTPS. |


## <a name="configuration-management"></a>Gerenciamento de configuração

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Yes | Por meio do NRP (provedor de recursos de rede). |

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [controles de segurança internos nos serviços do Azure](../security/fundamentals/security-controls.md).