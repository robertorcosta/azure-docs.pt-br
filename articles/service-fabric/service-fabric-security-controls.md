---
title: Controles de segurança para tecido de serviço azure
description: Saiba mais sobre controles de segurança para o Azure Service Fabric. Inclui uma lista de verificação de controles de segurança incorporados.
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: a8bb49e20ec5812a4882966c6918cf2bd59f36a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645422"
---
# <a name="security-controls-for-azure-service-fabric"></a>Controles de segurança para tecido de serviço azure

Este artigo documenta os controles de segurança incorporados no Azure Service Fabric. 

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controle de segurança | Sim/Não | Observações |
|---|---|--|
| Suporte a ponto final de serviço| Sim |  |
| Suporte à injeção VNet| Sim |  |
| Suporte de isolamento de rede e firewall| Sim | Usando NSGs (Grupos de Segurança de Rede). |
| Suporte forçado de tunelamento| Sim | A rede do Azure fornece túnel forçado. |

## <a name="monitoring--logging"></a>Monitoramento & registro

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao monitoramento do Azure (análises de log, insights de aplicativos, etc.)| Sim | Usando o suporte de monitoramento do Azure e o suporte de terceiros. |
| Registro e auditoria de plano de controle e gerenciamento| Sim | Todas as operações de plano de controle são executadas por meio de processos para auditoria e aprovações. |
| Registro e auditoria de data plan| N/D | O cliente é proprietário do cluster.  |

## <a name="identity"></a>Identidade

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Autenticação| Sim | A autenticação é feita pelo Azure Active Directory. |
| Autorização| Sim | IAM (Gerenciamento de identidade e de acesso) para chamadas via SFRP. Chamadas diretamente para o ponto final do cluster suportam duas funções: Usuário e Administrador. O cliente pode mapear as APIs para qualquer função. |

## <a name="data-protection"></a>Proteção de dados

| Controle de segurança | Sim/Não | Observações |
|---|---|--|
| Criptografia do lado do servidor em repouso: chaves gerenciadas pela Microsoft | Sim | O cliente possui o cluster e a escala de máquina virtual definida na qual o cluster é construído. A criptografia de disco do Azure pode ser ativada no conjunto de escalas da máquina virtual. |
| Criptografia do lado do servidor em repouso: chaves gerenciadas pelo cliente (BYOK) | Sim | O cliente possui o cluster e a escala de máquina virtual definida na qual o cluster é construído. A criptografia de disco do Azure pode ser ativada no conjunto de escalas da máquina virtual. |
| Criptografia de nível de coluna (Azure Data Services)| N/D |  |
| Criptografia em trânsito (como criptografia ExpressRoute, criptografia VNet e criptografia VNet-VNet)| Sim |  |
| Chamadas criptografadas à API| Sim | Chamadas à API do Service Fabric são feitas por meio do Azure Resource Manager. Um JWT (token Web JSON) válido é necessário. |

## <a name="configuration-management"></a>Gerenciamento de configuração

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao gerenciamento de configuração (versão de configuração, etc.)| Sim | |

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [controles de segurança incorporados nos serviços do Azure.](../security/fundamentals/security-controls.md)