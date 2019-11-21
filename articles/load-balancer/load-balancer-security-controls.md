---
title: Security controls for Azure Load Balancer
description: A checklist of security controls for evaluating Load Balancer
services: load-balancer
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: allensu
ms.openlocfilehash: 6043e574697489b6566641c352bc21a2b6d87f51
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74214892"
---
# <a name="security-controls-for-azure-load-balancer"></a>Security controls for Azure Load Balancer

This article documents the security controls built into Azure Load Balancer.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Security control | Sim/Não | Notas |
|---|---|--|
| Service endpoint support| N/D | |
| VNet injection support| N/D | |
| Network Isolation and Firewalling support| N/D |  |
| Forced tunneling support| N/D | |

## <a name="monitoring--logging"></a>Monitoring & logging

| Security control | Sim/Não | Notas|
|---|---|--|
| Azure monitoring support (Log analytics, App insights, etc.)| SIM | See [Azure Monitor logs for public Basic Load Balancer](load-balancer-monitor-log.md). |
| Control and management plane logging and audit| SIM | See [Azure Monitor logs for public Basic Load Balancer](load-balancer-monitor-log.md). |
| Data plane logging and audit | N/D |  |

## <a name="identity"></a>Identidade

| Security control | Sim/Não | Notas|
|---|---|--|
| Authentication| N/D |  |
| Autorização| N/D |  |

## <a name="data-protection"></a>Proteção de dados

| Security control | Sim/Não | Notas |
|---|---|--|
| Server-side encryption at rest: Microsoft-managed keys | N/D | |
| Encryption in transit (such as ExpressRoute encryption, in VNet encryption, and VNet-VNet encryption )| N/D | |
| Server-side encryption at rest: customer-managed keys (BYOK) | N/D | |
| Column level encryption (Azure Data Services)| N/D | |
| Chamadas criptografadas à API| SIM | Via the [Azure Resource Manager](../azure-resource-manager/index.yml). |

## <a name="configuration-management"></a>Gerenciamento de configuração

| Security control | Sim/Não | Notas|
|---|---|--|
| Configuration management support (versioning of configuration, etc.)| N/D |  | 

## <a name="next-steps"></a>Próximos passos

- Learn more about the [built-in security controls across Azure services](../security/fundamentals/security-controls.md).