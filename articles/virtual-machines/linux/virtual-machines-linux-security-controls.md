---
title: Controles de segurança para Máquinas Virtuais Azure Linux - Linux
description: Uma lista de verificação de controles de segurança para avaliar as Máquinas Virtuais Do Azure Linux
services: virtual-machines
ms.service: virtual-machines
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 300acaf4a9d2a11ef107e19df99452c909257d54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190567"
---
# <a name="security-controls-for-linux-virtual-machines"></a>Controles de segurança para Máquinas Virtuais Linux

Este artigo documenta os controles de segurança incorporados em Máquinas Virtuais Linux.

[!INCLUDE [Security controls header](../../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controle de segurança | Sim/Não | Observações |
|---|---|--|
| Suporte a ponto final de serviço| Sim | |
| Suporte à injeção VNet| Sim | |
| Suporte a isolamento de rede e firewall| Sim |  |
| Suporte forçado de tunelamento| Sim | Consulte [Configurar um túnel forçado usando o modelo de implantação do Azure Resource Manager](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm). |

## <a name="monitoring--logging"></a>Monitoramento & registro

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao monitoramento do Azure (análises de log, insights de aplicativos, etc.)| Sim | Consulte [Monitor e atualize uma máquina virtual Linux no Azure](/azure/virtual-machines/linux/tutorial-monitoring). |
| Registro e auditoria de plano de controle e gerenciamento| Sim |  |
| Registro e auditoria de data plan | Não |  |

## <a name="identity"></a>Identidade

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Autenticação| Sim |  |
| Autorização| Sim |  |

## <a name="data-protection"></a>Proteção de dados

| Controle de segurança | Sim/Não | Observações |
|---|---|--|
| Criptografia do lado do servidor em repouso: chaves gerenciadas pela Microsoft | Sim | Consulte [Azure Disk Encryption para VMs Linux](disk-encryption-overview.md). |
| Criptografia em trânsito (como criptografia ExpressRoute, criptografia VNet e criptografia VNet-VNet)| Sim | O Azure Virtual Machines suporta criptografia [ExpressRoute](/azure/expressroute) e VNet. Consulte [criptografia em trânsito em VMs](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Criptografia do lado do servidor em repouso: chaves gerenciadas pelo cliente (BYOK) | Sim | As chaves gerenciadas pelo cliente são um cenário de criptografia Azure suportado; ver [visão geral da criptografia do Azure](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms).|
| Criptografia de nível de coluna (Azure Data Services)| N/D | |
| Chamadas criptografadas à API| Sim | Via HTTPS e TLS. |

## <a name="configuration-management"></a>Gerenciamento de configuração

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao gerenciamento de configuração (versão de configuração, etc.)| Sim |  | 

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [controles de segurança incorporados nos serviços do Azure.](../../security/fundamentals/security-controls.md)
