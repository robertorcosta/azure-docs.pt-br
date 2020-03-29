---
title: Controles de segurança
description: Uma lista de verificação de controles de segurança incorporados para avaliar o serviço do Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: d0a0625153e428a0d261e52d40b31ef5142eddfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485618"
---
# <a name="security-controls-for-azure-resource-manager"></a>Controles de segurança para o Azure Resource Manager

Este artigo documenta os controles de segurança incorporados ao Azure Resource Manager.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Proteção de dados

| Controle de segurança | Sim/Não | Observações |
|---|---|--|
| Criptografia do lado do servidor em repouso: chaves gerenciadas pela Microsoft | Sim |  |
| Criptografia em trânsito (como criptografia ExpressRoute, criptografia VNet e criptografia VNet-VNet)| Sim | HTTPS/TLS. |
| Criptografia do lado do servidor em repouso: chaves gerenciadas pelo cliente (BYOK) | N/D | O Azure Resource Manager não armazena conteúdo do cliente, apenas controla dados. |
| Criptografia de nível de coluna (Azure Data Services)| Sim | |
| Chamadas criptografadas à API| Sim | |

## <a name="network"></a>Rede

| Controle de segurança | Sim/Não | Observações |
|---|---|--|
| Suporte a ponto final de serviço| Não | |
| Suporte à injeção VNet| Sim | |
| Suporte de isolamento de rede e firewall| Não |  |
| Suporte forçado de tunelamento| Não |  |

## <a name="monitoring--logging"></a>Monitoramento & registro

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao monitoramento do Azure (análises de log, insights de aplicativos, etc.)| Não | |
| Registro e auditoria de plano de controle e gerenciamento| Sim | Os registros de atividades expõem todas as operações de gravação (PUT, POST, DELETE) realizadas em seus recursos; veja [Exibir registros de atividades para auditar ações sobre recursos](view-activity-logs.md). |
| Registro e auditoria de data plan| N/D | |

## <a name="identity"></a>Identidade

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Autenticação| Sim | [Azure Active Directory](/azure/active-directory) baseado.|
| Autorização| Sim | |

## <a name="configuration-management"></a>Gerenciamento de configuração

| Controle de segurança | Sim/Não | Observações|
|---|---|--|
| Suporte ao gerenciamento de configuração (versão de configuração, etc.)| Sim |  |

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [controles de segurança incorporados nos serviços do Azure.](../../security/fundamentals/security-controls.md)