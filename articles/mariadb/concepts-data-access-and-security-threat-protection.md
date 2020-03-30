---
title: Proteção avançada contra ameaças - Banco de dados Azure para MariaDB
description: A Proteção Avançada contra Ameaças detecta as atividades anormais do banco de dados que indicam possíveis ameaças de segurança ao banco de dados.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 8485907eba75f80c8f0ed4fd0cc7368c6147b9fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532171"
---
# <a name="azure-database-for-mariadb-advanced-threat-protection"></a>Banco de dados Azure para Proteção Avançada de Ameaças MariaDB

O Banco de Dados Avançado de Proteção contra Ameaças para O Azure para O VaDb detecta atividades anômalas indicando tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados.

> [!IMPORTANT]
> O Advanced Threat Protection está em pré-visualização pública.

A Proteção Avançada contra Ameaças faz parte da oferta de Segurança Avançada de Dados, que é um pacote unificado para funcionalidades avançadas de segurança. O Advanced Threat Protection pode ser acessado e gerenciado através do [portal Azure](https://portal.azure.com). O recurso está disponível para servidores gerais otimizados para propósitos e memória.

> [!NOTE]
> A funcionalidade de Proteção Avançada contra Ameaças **não** está disponível nestas regiões de nuvem soberana e do Azure Governamental: US Gov – Texas, US Gov – Arizona, US Gov Iowa, US Gov – Virgínia, Leste do US DoD, Região Central do US DoD, Região Central da Alemanha, Norte da Alemanha, Leste da China e Leste da China 2. Visite [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/) para ver a disponibilidade geral do produto.


## <a name="what-is-advanced-threat-protection"></a>O que é a Proteção Avançada contra Ameaças?

O Banco de Dados Avançado de Proteção contra Ameaças para O Azure para O Azure fornece uma nova camada de segurança, que permite que os clientes detectem e respondam a possíveis ameaças à medida que ocorrem, fornecendo alertas de segurança sobre atividades anômalas. Os usuários receberão um alerta em caso de atividades suspeitas em bancos de dados, possíveis vulnerabilidades, bem como padrões anômalos de consultas e acesso a banco de dados. O Banco de Dados Avançado de Proteção contra Ameaças para O Azure para O Azure integra alertas com [o Azure Security Center](https://azure.microsoft.com/services/security-center/), que inclui detalhes de atividades suspeitas e recomenda ações sobre como investigar e mitigar a ameaça. O Banco de Dados Avançado de Proteção contra Ameaças para O Azure para O VaDb facilita o enfrentamento de possíveis ameaças ao banco de dados sem a necessidade de ser um especialista em segurança ou gerenciar sistemas avançados de monitoramento de segurança. 

![Conceito da Proteção Avançada contra Ameaças](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>Alertas da Proteção Avançada contra Ameaças 
O Banco de Dados Avançado de Proteção contra Ameaças para O Azure para O Azure detecta atividades anômalas indicando tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados e pode desencadear os seguintes alertas:
- **Acesso a partir de localização incomum**: Este alerta é acionado quando há uma alteração no padrão de acesso ao banco de dados Azure para servidor MariaDB, onde alguém fez logon no Banco de Dados Do Azure para servidor MariaDB a partir de uma localização geográfica incomum. Em alguns casos, o alerta detecta uma ação legítima (um novo aplicativo ou manutenção do desenvolvedor). Em outros casos, o alerta detecta uma ação mal-intencionada (funcionário antigo, invasor externo).
- **Acesso a partir de um data center azure incomum**: Este alerta é acionado quando há uma alteração no padrão de acesso ao banco de dados Azure para servidor MariaDB, onde alguém fez logon no servidor a partir de um data center azure incomum que foi visto neste servidor durante o período recente. Em alguns casos, o alerta detecta uma ação legítima (seu novo aplicativo no Azure, Power BI). Em outros casos, o alerta detecta uma ação mal-intencionada de um recurso/serviço do Azure (funcionário antigo, invasor externo).
- **Acesso a partir de principal desconhecido**: Este alerta é acionado quando há uma alteração no padrão de acesso ao banco de dados Azure para servidor MariaDB, onde alguém fez logon no servidor usando um principal incomum (Banco de Dados Azure para usuário Do MariaDB). Em alguns casos, o alerta detecta uma ação legítima (novo aplicativo ou manutenção do desenvolvedor). Em outros casos, o alerta detecta uma ação mal-intencionada (funcionário antigo, invasor externo).
- **Acesso a partir de um aplicativo potencialmente prejudicial**: Este alerta é disparado quando um aplicativo potencialmente prejudicial é usado para acessar o banco de dados. Em alguns casos, o alerta detecta um teste de segurança que está sendo executado. Em outros casos, o alerta detecta um ataque usando ferramentas comuns de ataque.
- **Força bruta Banco de dados Azure para credenciais MariaDB**: Este alerta é acionado quando há um número anormal de logins com falhas com diferentes credenciais. Em alguns casos, o alerta detecta um teste de segurança que está sendo executado. Em outros casos, o alerta detecta ataques de força bruta.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre a [Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Para obter mais informações sobre preços, consulte a [página de preços do Azure para MariaDB](https://azure.microsoft.com/pricing/details/mariadb/) 
* Configure [o Banco de Dados Azure para Proteção Avançada de Ameaças MariaDB](howto-database-threat-protection-portal.md) usando o portal Azure  
