---
title: Controles de segurança para o Azure Service Fabric
description: Saiba mais sobre os controles de segurança do Azure Service Fabric. Inclui uma lista de verificação de controles de segurança internos.
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: a8bb49e20ec5812a4882966c6918cf2bd59f36a0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "75645422"
---
# <a name="security-controls-for-azure-service-fabric"></a>Controles de segurança para o Azure Service Fabric

Este artigo documenta os controles de segurança criados no Azure Service Fabric. 

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controle de segurança | Sim/Não | Observações |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| Sim |  |
| Suporte à injeção de VNet| Sim |  |
| Isolamento de rede e suporte de firewall| Sim | Usando NSGs (Grupos de Segurança de Rede). |
| Suporte a túnel forçado| Sim | A rede do Azure fornece túnel forçado. |

## <a name="monitoring--logging"></a>Monitorando & log

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Sim | Usando o suporte ao monitoramento do Azure e o suporte de terceiros. |
| Registro e auditoria do plano de gerenciamento e controle| Sim | Todas as operações de plano de controle são executadas por meio de processos para auditoria e aprovações. |
| Log e auditoria do plano de dados| N/D | O cliente é proprietário do cluster.  |

## <a name="identity"></a>Identidade

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Autenticação| Sim | A autenticação é feita pelo Azure Active Directory. |
| Autorização| Sim | IAM (Gerenciamento de identidade e de acesso) para chamadas via SFRP. Chamadas diretamente para o ponto de extremidade de cluster dão suporte a duas funções: usuário e administrador. O cliente pode mapear as APIs para qualquer função. |

## <a name="data-protection"></a>Proteção de dados

| Controle de segurança | Sim/Não | Observações |
|---|---|--|
| Criptografia no lado do servidor em repouso: chaves gerenciadas pela Microsoft | Sim | O cliente possui o cluster e o conjunto de dimensionamento de máquinas virtuais no qual o cluster foi criado. A criptografia de disco do Azure pode ser habilitada no conjunto de dimensionamento de máquinas virtuais. |
| Criptografia no lado do servidor em repouso: chaves gerenciadas pelo cliente (BYOK) | Sim | O cliente possui o cluster e o conjunto de dimensionamento de máquinas virtuais no qual o cluster foi criado. A criptografia de disco do Azure pode ser habilitada no conjunto de dimensionamento de máquinas virtuais. |
| Criptografia em nível de coluna (serviços de dados do Azure)| N/D |  |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet)| Sim |  |
| Chamadas criptografadas à API| Sim | Chamadas à API do Service Fabric são feitas por meio do Azure Resource Manager. Um JWT (token Web JSON) válido é necessário. |

## <a name="configuration-management"></a>Gerenciamento de configuração

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| Sim | |

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [controles de segurança internos nos serviços do Azure](../security/fundamentals/security-controls.md).