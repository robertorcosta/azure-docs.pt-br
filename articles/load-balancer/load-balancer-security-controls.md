---
title: Controles de segurança para Azure Load Balancer
description: Uma lista de verificação de controles de segurança para avaliar Load Balancer
services: load-balancer
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: allensu
ms.openlocfilehash: 6043e574697489b6566641c352bc21a2b6d87f51
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "74214892"
---
# <a name="security-controls-for-azure-load-balancer"></a>Controles de segurança para Azure Load Balancer

Este artigo documenta os controles de segurança internos do Azure Load Balancer.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controle de segurança | Sim/Não | Observações |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| N/D | |
| Suporte à injeção de VNet| N/D | |
| Isolamento de rede e suporte de firewall| N/D |  |
| Suporte a túnel forçado| N/D | |

## <a name="monitoring--logging"></a>Monitorando & log

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | Consulte [logs de Azure monitor para Load Balancer básica pública](load-balancer-monitor-log.md). |
| Registro e auditoria do plano de gerenciamento e controle| Sim | Consulte [logs de Azure monitor para Load Balancer básica pública](load-balancer-monitor-log.md). |
| Log e auditoria do plano de dados | N/D |  |

## <a name="identity"></a>Identidade

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Autenticação| N/D |  |
| Autorização| N/D |  |

## <a name="data-protection"></a>Proteção de dados

| Controle de segurança | Sim/Não | Observações |
|---|---|--|
| Criptografia no lado do servidor em repouso: chaves gerenciadas pela Microsoft | N/D | |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet)| N/D | |
| Criptografia no lado do servidor em repouso: chaves gerenciadas pelo cliente (BYOK) | N/D | |
| Criptografia em nível de coluna (serviços de dados do Azure)| N/D | |
| Chamadas criptografadas à API| Sim | Por meio do [Azure Resource Manager](../azure-resource-manager/index.yml). |

## <a name="configuration-management"></a>Gerenciamento de configuração

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| N/D |  | 

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [controles de segurança internos nos serviços do Azure](../security/fundamentals/security-controls.md).