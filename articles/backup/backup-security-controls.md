---
title: Controles de segurança
description: Saiba mais sobre os controles de segurança usados no serviço de backup do Azure. Esses controles ajudam o serviço a prevenir, detectar e responder a vulnerabilidades de segurança.
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: 0e3f5ce942ea8aef9bf5eb98883ae1e72a7ab239
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172132"
---
# <a name="security-controls-for-azure-backup"></a>Controles de segurança para o backup do Azure

Este artigo documenta os controles de segurança incorporados ao backup do Azure.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controle de segurança | Sim/Não | Observações | Documentação
|---|---|--|
| Suporte ao ponto de extremidade de serviço| Não |  |  |
| Suporte à injeção de VNet| Não |  |  |
| Isolamento de rede e suporte de firewall| sim | Há suporte para túnel forçado no backup de VM. Não há suporte para túnel forçado em cargas de trabalho em execução dentro de VMs. |  |
| Suporte a túnel forçado| Não |  |  |

## <a name="monitoring--logging"></a>Monitorando & log

| Controle de segurança | Sim/Não | Observações| | Documentação
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| sim | Há suporte para Log Analytics por meio de logs de recursos. Para obter mais informações, consulte [monitorar cargas de trabalho protegidas de backup do Azure usando o log Analytics](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/). |  |
| Registro e auditoria do plano de gerenciamento e controle| sim | Todas as ações disparadas pelo cliente no portal do Azure são registradas em logs de atividades. |  |
| Log e auditoria do plano de dados| Não | O plano de dados do Backup do Azure não pode ser acessado diretamente.  |  |

## <a name="identity"></a>Identidade

| Controle de segurança | Sim/Não | Observações| | Documentação
|---|---|--|
| Autenticação| sim | A autenticação é feita pelo Azure Active Directory. |  |
| Autorização| sim | São usadas regras de RBAC internas e criadas pelo cliente. Para obter mais informações, consulte [usar o controle de acesso baseado em função para gerenciar pontos de recuperação do backup do Azure](/azure/backup/backup-rbac-rs-vault). |  |

## <a name="data-protection"></a>Proteção de dados

| Controle de segurança | Sim/Não | Observações | | Documentação
|---|---|--|
| Criptografia no lado do servidor em repouso: chaves gerenciadas pela Microsoft | sim | Usar a criptografia do serviço de armazenamento para contas de armazenamento. |  |
| Criptografia no lado do servidor em repouso: chaves gerenciadas pelo cliente (BYOK) | Não |  |  |
| Criptografia em nível de coluna (serviços de dados do Azure)| Não |  |  |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet)| Não | Usando HTTPS. |  |
| Chamadas à API criptografadas| sim |  |  |

## <a name="configuration-management"></a>Gerenciamento de configuração

| Controle de segurança | Sim/Não | Observações| | Documentação
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| sim|  |  |

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [controles de segurança internos nos serviços do Azure](../security/fundamentals/security-controls.md).
