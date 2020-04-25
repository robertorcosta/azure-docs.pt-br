---
title: Controles de segurança para o gateway de VPN do Azure
description: Uma lista de verificação de controles de segurança para avaliar o gateway de VPN do Azure
services: sql-database
author: msmbaldwin
manager: rkarlin
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: mbaldwin
ms.openlocfilehash: 6fc5b4c901254decdb2d34281a10ababd4d79d45
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82127864"
---
# <a name="security-controls-for-azure-vpn-gateway"></a>Controles de segurança para o gateway de VPN do Azure

Este artigo documenta os controles de segurança incorporados ao gateway de VPN do Azure.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controle de segurança | Sim/Não | Observações |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| N/D | |
| Suporte à injeção de VNet| N/D | |
| Isolamento de rede e suporte de firewall| Sim | Gateways de VPN são instâncias de VM dedicadas para cada rede virtual de cliente  |
| Suporte a túnel forçado| Sim |  |

## <a name="monitoring--logging"></a>Monitorando & log

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | Consulte [Azure monitor alerta de log](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & [Azure monitor alertas de métricas](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).  |
| Registro e auditoria do plano de gerenciamento e controle| Sim | Log de atividades Azure Resource Manager. |
| Log e auditoria do plano de dados | Sim | [Azure monitor logs de atividades](../azure-resource-manager/management/view-activity-logs.md) para log e auditoria de conectividade de VPN. |

## <a name="identity"></a>Identidade

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Autenticação| Sim | [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) para gerenciar o serviço e configurar o gateway de VPN do Azure. |
| Autorização| Sim | Suporte à autorização via [RBAC](../role-based-access-control/overview.md). |

## <a name="data-protection"></a>Proteção de dados

| Controle de segurança | Sim/Não | Observações |
|---|---|--|
| Criptografia no lado do servidor em repouso: chaves gerenciadas pela Microsoft | N/D | Dados de cliente de trânsito de gateway de VPN, não armazena dados do cliente |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet)| Sim | O gateway de VPN criptografa os pacotes do cliente entre os gateways de VPN do Azure e os dispositivos de VPN locais do cliente (S2S) ou clientes VPN (P2S). Os gateways de VPN também dão suporte à criptografia de VNet para VNet. |
| Criptografia no lado do servidor em repouso: chaves gerenciadas pelo cliente (BYOK) | Não | As chaves pré-compartilhadas especificadas pelo cliente são criptografadas em repouso; Mas não integrado com o CMK ainda. |
| Criptografia em nível de coluna (serviços de dados do Azure)| N/D | |
| Chamadas criptografadas à API| Sim | Por meio de [Azure Resource Manager](../azure-resource-manager/index.yml) e https  |

## <a name="configuration-management"></a>Gerenciamento de configuração

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim | Para operações de gerenciamento, o estado de uma configuração de gateway de VPN do Azure pode ser exportado como um modelo de Azure Resource Manager e com controle de versão ao longo do tempo. |

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [controles de segurança internos nos serviços do Azure](../security/fundamentals/security-controls.md).
