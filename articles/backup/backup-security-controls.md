---
title: Controles de segurança
description: Conheça os controles de segurança usados no serviço de backup do Azure. Esses controles ajudam o serviço a prevenir, detectar e responder a vulnerabilidades de segurança.
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: 0e3f5ce942ea8aef9bf5eb98883ae1e72a7ab239
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74172132"
---
# <a name="security-controls-for-azure-backup"></a>Controles de segurança para backup do Azure

Este artigo documenta os controles de segurança incorporados no Azure Backup.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controle de segurança | Sim/Não | Observações | Documentação
|---|---|--|
| Suporte a ponto final de serviço| Não |  |  |
| Suporte à injeção VNet| Não |  |  |
| Suporte de isolamento de rede e firewall| Sim | Há suporte para túnel forçado no backup de VM. Não há suporte para túnel forçado em cargas de trabalho em execução dentro de VMs. |  |
| Suporte forçado de tunelamento| Não |  |  |

## <a name="monitoring--logging"></a>Monitoramento & registro

| Controle de segurança | Sim/Não | Observações| | Documentação
|---|---|--|
| Suporte ao monitoramento do Azure (análises de log, insights de aplicativos, etc.)| Sim | O Log Analytics é suportado através de logs de recursos. Para obter mais informações, consulte [Monitor Azure Backup de cargas de trabalho protegidas usando o Log Analytics](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/). |  |
| Registro e auditoria de plano de controle e gerenciamento| Sim | Todas as ações disparadas pelo cliente no portal do Azure são registradas em logs de atividades. |  |
| Registro e auditoria de data plan| Não | O plano de dados do Backup do Azure não pode ser acessado diretamente.  |  |

## <a name="identity"></a>Identidade

| Controle de segurança | Sim/Não | Observações| | Documentação
|---|---|--|
| Autenticação| Sim | A autenticação é feita pelo Azure Active Directory. |  |
| Autorização| Sim | São usadas regras de RBAC internas e criadas pelo cliente. Para obter mais informações, consulte [Usar controle de acesso baseado em função para gerenciar pontos de recuperação do Azure Backup](/azure/backup/backup-rbac-rs-vault). |  |

## <a name="data-protection"></a>Proteção de dados

| Controle de segurança | Sim/Não | Observações | | Documentação
|---|---|--|
| Criptografia do lado do servidor em repouso: chaves gerenciadas pela Microsoft | Sim | Usar a criptografia do serviço de armazenamento para contas de armazenamento. |  |
| Criptografia do lado do servidor em repouso: chaves gerenciadas pelo cliente (BYOK) | Não |  |  |
| Criptografia de nível de coluna (Azure Data Services)| Não |  |  |
| Criptografia em trânsito (como criptografia ExpressRoute, criptografia VNet e criptografia VNet-VNet)| Não | Usar HTTPS. |  |
| Chamadas criptografadas à API| Sim |  |  |

## <a name="configuration-management"></a>Gerenciamento de configuração

| Controle de segurança | Sim/Não | Observações| | Documentação
|---|---|--|
| Suporte ao gerenciamento de configuração (versão de configuração, etc.)| Sim|  |  |

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [controles de segurança incorporados nos serviços do Azure.](../security/fundamentals/security-controls.md)
