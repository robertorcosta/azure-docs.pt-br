---
title: Proteção avançada contra ameaças - Banco de dados Azure para PostgreSQL - Servidor Único
description: Saiba como usar o Advanced Threat Protection para detectar atividades anômalas de banco de dados que indiquem possíveis ameaças à segurança no banco de dados.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 3d86c76472580567c95d285924761e1714465d6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768734"
---
# <a name="advanced-threat-protection-in-azure-database-for-postgresql---single-server"></a>Proteção avançada contra ameaças no banco de dados Azure para PostgreSQL - Servidor Único

A Proteção Avançada contra Ameaças para o Banco de Dados do Azure para PostgreSQL detecta atividades anômalas, indicando tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados.

> [!NOTE]
> O Advanced Threat Protection está em pré-visualização pública.

A Proteção contra Ameaças faz parte da oferta de ATP (Proteção Avançada contra Ameaças), que é um pacote unificado para funcionalidades avançadas de segurança. O Advanced Threat Protection pode ser acessado e gerenciado através do [portal Azure](https://portal.azure.com) ou usando a [API REST](/rest/api/postgresql/serversecurityalertpolicies). O recurso está disponível para servidores gerais otimizados para propósitos e memória.

> [!NOTE]
> A funcionalidade de Proteção Avançada contra Ameaças **não** está disponível nestas regiões de nuvem soberana e do Azure Governamental: US Gov – Texas, US Gov – Arizona, US Gov Iowa, US Gov – Virgínia, Leste do US DoD, Região Central do US DoD, Região Central da Alemanha, Norte da Alemanha, Leste da China e Leste da China 2. Visite [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/) para ver a disponibilidade geral do produto.

## <a name="what-is-advanced-threat-protection"></a>O que é a Proteção Avançada contra Ameaças?

A Proteção Avançada contra Ameaças para o Banco de Dados do Azure para PostgreSQL fornece uma nova camada de segurança que possibilita aos clientes detectar e responder a possíveis ameaças conforme elas ocorrem, apresentando alertas de segurança sobre atividades anômalas. Os usuários receberão um alerta em caso de atividades suspeitas em bancos de dados, possíveis vulnerabilidades, bem como padrões anômalos de consultas e acesso a banco de dados. A Proteção Avançada contra Ameaças para o Banco de Dados do Azure para PostgreSQL integra alertas à [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center/), que fornece detalhes de atividades suspeitas e recomenda ações sobre como investigar e atenuar a ameaça. A Proteção Avançada contra Ameaças para o Banco de Dados do Azure para PostgreSQL simplifica a abordagem contra possíveis ameaças no banco de dados, sem a necessidade de ser um especialista em segurança ou de gerenciar sistemas avançados de monitoramento de segurança. 

![Conceito da Proteção Avançada contra Ameaças](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>Alertas da Proteção Avançada contra Ameaças 
A Proteção Avançada contra Ameaças para o Banco de Dados do Azure para PostgreSQL detecta atividades anômalas que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados e pode disparar os seguintes alertas:
- **Acesso de um local incomum**: este alerta é disparado quando há uma alteração no padrão de acesso ao servidor do Banco de Dados do Azure para PostgreSQL, em que alguém fez logon nele de um local geográfico incomum. Em alguns casos, o alerta detecta uma ação legítima (um novo aplicativo ou manutenção do desenvolvedor). Em outros casos, o alerta detecta uma ação mal-intencionada (funcionário antigo, invasor externo).
- **Acesso de um data center incomum do Azure**: este alerta é disparado quando há uma alteração no padrão de acesso ao servidor do Banco de Dados do Azure para PostgreSQL, no local em que alguém fez logon no servidor de um data center incomum do Azure que foi visto neste servidor durante um período recente. Em alguns casos, o alerta detecta uma ação legítima (o novo aplicativo no Azure, no Power BI e no Editor de Consultas do Banco de Dados do Azure para PostgreSQL). Em outros casos, o alerta detecta uma ação mal-intencionada de um recurso/serviço do Azure (funcionário antigo, invasor externo).
- **Acesso de uma entidade de segurança não familiar**: este alerta é disparado quando há uma alteração no padrão de acesso ao servidor do Banco de Dados do Azure para PostgreSQL, no local em que alguém fez logon no servidor usando uma entidade de segurança incomum (usuário do Banco de Dados do Azure para PostgreSQL). Em alguns casos, o alerta detecta uma ação legítima (novo aplicativo ou manutenção do desenvolvedor). Em outros casos, o alerta detecta uma ação mal-intencionada (funcionário antigo, invasor externo).
- **Acesso a partir de um aplicativo potencialmente prejudicial**: Este alerta é disparado quando um aplicativo potencialmente prejudicial é usado para acessar o banco de dados. Em alguns casos, o alerta detecta um teste de segurança que está sendo executado. Em outros casos, o alerta detecta um ataque usando ferramentas comuns de ataque.
- **Credenciais do Banco de Dados do Azure para PostgreSQL de força bruta**: este alerta é disparado quando há um número alto anormal de logons com falha com credenciais diferentes. Em alguns casos, o alerta detecta um teste de segurança que está sendo executado. Em outros casos, o alerta detecta ataques de força bruta.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre a [Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Para saber mais sobre preço, consulte a página [Preço do Banco de Dados do Azure para PostgreSQL](https://azure.microsoft.com/pricing/details/postgresql/) 
* Configurar a [Proteção Avançada contra Ameaças do Banco de Dados do Azure para PostgreSQL](howto-database-threat-protection-portal.md) usando o Portal do Azure  
