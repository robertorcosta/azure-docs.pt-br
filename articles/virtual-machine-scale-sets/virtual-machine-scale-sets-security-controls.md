---
title: Controles de segurança para conjuntos de dimensionamento de máquinas virtuais do Azure
description: Uma lista de verificação de controles de segurança para avaliar os conjuntos de dimensionamento de máquinas virtuais do Azure
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: security
ms.date: 09/05/2019
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 4480ad425d9a3953fd5779f99d27b5b6b037e61e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87029409"
---
# <a name="security-controls-for-azure-virtual-machine-scale-sets"></a>Controles de segurança para conjuntos de dimensionamento de máquinas virtuais do Azure

Este artigo documenta os controles de segurança criados nos conjuntos de dimensionamento de máquinas virtuais do Azure.

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controle de segurança | Sim/Não | Observações |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| Sim | |
| Suporte à injeção de VNet| Sim | |
| Isolamento de rede e suporte de firewall| Sim |  |
| Suporte a túnel forçado| Sim | Consulte [Configurar o túnel forçado usando o modelo de implantação Azure Resource Manager](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md). |

## <a name="monitoring--logging"></a>Monitorando & log

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | Consulte [monitorar e atualizar uma máquina virtual Linux no Azure](../virtual-machines/linux/tutorial-monitor.md) e [monitorar e atualizar uma máquina virtual do Windows no Azure](../virtual-machines/windows/tutorial-monitor.md). |
| Registro e auditoria do plano de gerenciamento e controle| Sim |  |
| Log e auditoria do plano de dados | Não |  |

## <a name="identity"></a>Identidade

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Autenticação| Sim |  |
| Autorização| Sim |  |

## <a name="data-protection"></a>Proteção de dados

| Controle de segurança | Sim/Não | Observações |
|---|---|--|
| Criptografia no lado do servidor em repouso: chaves gerenciadas pela Microsoft | Sim | Consulte [Azure Disk Encryption para conjuntos de dimensionamento de máquinas virtuais](disk-encryption-overview.md). |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de VNet e criptografia de VNet-VNet)| Sim | As máquinas virtuais do Azure dão suporte à criptografia de [ExpressRoute](../expressroute/index.yml) e VNet. Consulte [criptografia em trânsito em VMs](../security/fundamentals/encryption-overview.md#in-transit-encryption-in-vms). |
| Criptografia no lado do servidor em repouso: chaves gerenciadas pelo cliente (BYOK) | Sim | As chaves gerenciadas pelo cliente são um cenário de criptografia do Azure com suporte; consulte Confira [Azure Disk Encryption para conjuntos de dimensionamento de máquinas virtuais](disk-encryption-overview.md)|
| Criptografia em nível de coluna (serviços de dados do Azure)| N/D | |
| Chamadas criptografadas à API| Sim | Via HTTPS e TLS. |

## <a name="configuration-management"></a>Gerenciamento de configuração

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim |  | 

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [controles de segurança internos nos serviços do Azure](../security/fundamentals/security-controls.md).
