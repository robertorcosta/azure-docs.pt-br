---
title: Controles de segurança para o Azure Máquinas Virtuais do Windows
description: Uma lista de verificação de controles de segurança para avaliar o Azure Máquinas Virtuais do Windows
ms.service: virtual-machines
ms.subservice: security
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: mbaldwin
ms.openlocfilehash: ac1ed9ac25d65d0391175fc6d43b48048da74926
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82101579"
---
# <a name="security-controls-for-windows-virtual-machines"></a>Controles de segurança para Máquinas Virtuais do Windows

Este artigo documenta os controles de segurança internos do Máquinas Virtuais do Windows.

[!INCLUDE [Security controls header](../../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controle de segurança | Sim/Não | Anotações |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| Sim | |
| Suporte à injeção de VNet| Sim | |
| Isolamento de rede e suporte de firewall| Sim |  |
| Suporte a túnel forçado| Sim | Consulte [Configurar o túnel forçado usando o modelo de implantação Azure Resource Manager](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm). |

## <a name="monitoring--logging"></a>Monitorando & log

| Controle de segurança | Sim/Não | Anotações|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | [Monitorar e atualizar uma máquina virtual do Windows no Azure](tutorial-monitoring.md). |
| Registro e auditoria do plano de gerenciamento e controle| Sim |  |
| Log e auditoria do plano de dados | Não |  |

## <a name="identity"></a>Identidade

| Controle de segurança | Sim/Não | Anotações|
|---|---|--|
| Autenticação| Sim |  |
| Autorização| Sim |  |

## <a name="data-protection"></a>Proteção de dados

| Controle de segurança | Sim/Não | Anotações |
|---|---|--|
| Criptografia no lado do servidor em repouso: chaves gerenciadas pela Microsoft | Sim | Consulte [criptografar discos virtuais em uma VM do Windows](/azure/virtual-machines/windows/disk-encryption-overview). |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet)| Sim | As máquinas virtuais do Azure dão suporte à criptografia de [ExpressRoute](/azure/expressroute) e VNet. Consulte [criptografia em trânsito em VMs](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Criptografia no lado do servidor em repouso: chaves gerenciadas pelo cliente (BYOK) | Sim | As chaves gerenciadas pelo cliente são um cenário de criptografia do Azure com suporte; consulte [visão geral da criptografia do Azure](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms).|
| Criptografia em nível de coluna (serviços de dados do Azure)| N/D | |
| Chamadas criptografadas à API| Sim | Via HTTPS e TLS. |



## <a name="configuration-management"></a>Gerenciamento de configuração

| Controle de segurança | Sim/Não | Anotações|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim |  | 

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [controles de segurança internos nos serviços do Azure](../../security/fundamentals/security-controls.md).
