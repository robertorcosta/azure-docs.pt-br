---
title: Controles de segurança para O Balanceador de Carga Azure
description: Uma lista de verificação de controles de segurança para avaliar o Balanceador de Carga
services: load-balancer
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: allensu
ms.openlocfilehash: 6043e574697489b6566641c352bc21a2b6d87f51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74214892"
---
# <a name="security-controls-for-azure-load-balancer"></a>Controles de segurança para O Balanceador de Carga Azure

Este artigo documenta os controles de segurança incorporados ao Azure Load Balancer.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controle de segurança | Sim/Não | Observações |
|---|---|--|
| Suporte a ponto final de serviço| N/D | |
| Suporte à injeção VNet| N/D | |
| Suporte a isolamento de rede e firewall| N/D |  |
| Suporte forçado de tunelamento| N/D | |

## <a name="monitoring--logging"></a>Monitoramento & registro

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao monitoramento do Azure (análises de log, insights de aplicativos, etc.)| Sim | Consulte [os registros do Monitor Do Azure para o balanceador de carga básico público](load-balancer-monitor-log.md). |
| Registro e auditoria de plano de controle e gerenciamento| Sim | Consulte [os registros do Monitor Do Azure para o balanceador de carga básico público](load-balancer-monitor-log.md). |
| Registro e auditoria de data plan | N/D |  |

## <a name="identity"></a>Identidade

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Autenticação| N/D |  |
| Autorização| N/D |  |

## <a name="data-protection"></a>Proteção de dados

| Controle de segurança | Sim/Não | Observações |
|---|---|--|
| Criptografia do lado do servidor em repouso: chaves gerenciadas pela Microsoft | N/D | |
| Criptografia em trânsito (como criptografia ExpressRoute, criptografia VNet e criptografia VNet-VNet)| N/D | |
| Criptografia do lado do servidor em repouso: chaves gerenciadas pelo cliente (BYOK) | N/D | |
| Criptografia de nível de coluna (Azure Data Services)| N/D | |
| Chamadas criptografadas à API| Sim | Através do [Azure Resource Manager](../azure-resource-manager/index.yml). |

## <a name="configuration-management"></a>Gerenciamento de configuração

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao gerenciamento de configuração (versão de configuração, etc.)| N/D |  | 

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [controles de segurança incorporados nos serviços do Azure.](../security/fundamentals/security-controls.md)