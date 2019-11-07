---
title: Proteção avançada contra ameaças-banco de dados SQL do Azure
description: A proteção avançada contra ameaças detecta atividades anormais de banco de dados que indicam possíveis ameaças à segurança no banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: monhaber
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 03/31/2019
ms.openlocfilehash: fca68fbc1ee30b1df100255e463eca2fc895980b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686961"
---
# <a name="advanced-threat-protection-for-azure-sql-database"></a>Proteção Avançada contra Ameaças para o Banco de Dados SQL do Azure

A proteção avançada contra ameaças para o [banco de dados SQL do Azure](sql-database-technical-overview.md) e o [SQL data warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados.

A proteção avançada contra ameaças faz parte da oferta do ADS ( [segurança de dados avançada](sql-database-advanced-data-security.md) ), que é um pacote unificado para recursos avançados de segurança do SQL. A proteção avançada contra ameaças pode ser acessada e gerenciada por meio do portal central de anúncios do SQL.

> [!NOTE]
> Este tópico aplica-se ao servidor SQL do Azure e aos bancos de dados SQL e SQL Data Warehouse criados no servidor do SQL do Azure. Para simplificar, o banco de dados SQL é usado quando se refere ao Banco de Dados SQL e ao SQL Data Warehouse.

## <a name="what-is-advanced-threat-protection"></a>O que é proteção avançada contra ameaças

 A proteção avançada contra ameaças fornece uma nova camada de segurança, que permite que os clientes detectem e respondam a ameaças potenciais à medida que ocorrem, fornecendo alertas de segurança em atividades anormais. Os usuários receberão um alerta em caso de atividades suspeitas em bancos de dados, possíveis vulnerabilidades e ataques de injeção de SQL, bem como padrões anômalos de consultas e acesso a banco de dados. A proteção avançada contra ameaças integra alertas com a [central de segurança do Azure](https://azure.microsoft.com/services/security-center/), que inclui detalhes de atividades suspeitas e recomendação de ações sobre como investigar e atenuar a ameaça. A proteção avançada contra ameaças simplifica a endereçamento de ameaças potenciais ao banco de dados sem a necessidade de ser um especialista em segurança ou gerenciar sistemas de monitoramento de segurança avançados.

Para uma experiência de investigação completa, é recomendável habilitar a [Auditoria de Banco de Dados SQL](sql-database-auditing.md), que grava eventos de banco de dados em um log de auditoria na conta de armazenamento do Azure.  

## <a name="advanced-threat-protection-alerts"></a>Alertas da Proteção Avançada contra Ameaças

A proteção avançada contra ameaças para o banco de dados SQL do Azure detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados e pode disparar os seguintes alertas:

- **Vulnerabilidade à injeção de SQL**: esse alerta é disparado quando um aplicativo gera uma instrução SQL com falha no banco de dados. Esse alerta pode indicar uma possível vulnerabilidade a ataques de injeção de SQL. Há dois motivos possíveis para a geração de uma instrução com erro:

  - Um defeito no código do aplicativo que cria a instrução SQL com erro
  - O código do aplicativo ou procedimentos armazenados não limpam a entrada do usuário ao construir a instrução SQL com erro, o que pode ser explorado para injeção de SQL
- **Potencial injeção de SQL**: Este alerta é disparado quando acontece uma exploração ativa em uma vulnerabilidade do aplicativo identificada para injeção de SQL. Isso significa que o invasor está tentando inserir instruções SQL maliciosas usando o código de aplicativo ou procedimentos armazenados vulneráveis.
- **Acesso a partir de um local incomum**: Este alerta é disparado quando há uma alteração no padrão de acesso ao SQL Server, em que alguém fez logon no SQL Server a partir de um local geográfico incomum. Em alguns casos, o alerta detecta uma ação legítima (um novo aplicativo ou manutenção do desenvolvedor). Em outros casos, o alerta detecta uma ação mal-intencionada (funcionário antigo, invasor externo).
- **Acesso a partir de um data center do Azure incomum**: Este alerta é disparado quando há uma alteração no padrão de acesso ao SQL Server, onde alguém fez logon no SQL Server a partir de um data center do Azure incomum que foi visto neste servidor durante um período recente. Em alguns casos, o alerta detecta uma ação legítima (seu novo aplicativo no Azure, o Power BI, Azure SQL Query Editor). Em outros casos, o alerta detecta uma ação mal-intencionada de um recurso/serviço do Azure (funcionário antigo, invasor externo).
- **Acesso a partir de uma entidade de segurança não familiar**: Este alerta é disparado quando há uma alteração no padrão de acesso ao SQL Server, onde alguém fez logon no SQL Server a partir de uma entidade de segurança incomum (usuário SQL). Em alguns casos, o alerta detecta uma ação legítima (novo aplicativo ou manutenção do desenvolvedor). Em outros casos, o alerta detecta uma ação mal-intencionada (funcionário antigo, invasor externo).
- **Acesso a partir de um aplicativo potencialmente prejudicial**: Este alerta é disparado quando um aplicativo potencialmente prejudicial é usado para acessar o banco de dados. Em alguns casos, o alerta detecta um teste de segurança que está sendo executado. Em outros casos, o alerta detecta um ataque usando ferramentas comuns de ataque.
- **Credenciais SQL de força bruta**: Este alerta é disparado quando há um número alto anormal de logons com falha com credenciais diferentes. Em alguns casos, o alerta detecta um teste de segurança que está sendo executado. Em outros casos, o alerta detecta ataques de força bruta.

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>Explore as atividades anormais do banco de dados na detecção de um evento suspeito

Você receberá uma notificação por email na detecção das atividades anormais do banco de dados. O email fornecerá informações sobre o evento de segurança suspeito, incluindo a natureza das atividades anômalas, o nome do banco de dados, o nome do servidor, o nome do aplicativo e a hora do evento. Além disso, o email fornecerá informações sobre as possíveis causas e as ações recomendadas para investigar e atenuar a ameaça em potencial no banco de dados.

![Relatórios de atividades anômalas](./media/sql-database-threat-detection/anomalous_activity_report.png)

1. Clique em **Exibir alertas recentes do SQL** no email para iniciar o Portal do Azure e mostrar a página de alertas da Central de Segurança do Azure, que fornece uma visão geral das ameaças ativas detectadas no banco de dados SQL.

   ![Ameaças de atividade](./media/sql-database-threat-detection/active_threats.png)

2. Clique em um alerta específico para obter os detalhes e as ações adicionais para investigar essa ameaça e corrigir ameaças futuras.

   Por exemplo, a injeção de SQL é um dos problemas mais comuns de segurança do aplicativo da Web na Internet, usada para atacar os aplicativos orientados a dados. Os invasores aproveitam as vulnerabilidades do aplicativo para inserir instruções SQL mal-intencionadas nos campos de entrada do aplicativo, violando ou modificando os dados no banco de dados. Para os alertas de Injeção de SQL, os detalhes do alerta incluem a instrução SQL vulnerável que foi explorada.

   ![Alerta específico](./media/sql-database-threat-detection/specific_alert.png)

## <a name="explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal"></a>Explore os alertas de proteção avançada contra ameaças para seu banco de dados no portal do Azure

A proteção avançada contra ameaças integra seus alertas à [central de segurança do Azure](https://azure.microsoft.com/services/security-center/). Blocos de proteção avançada contra ameaças do SQL Live no banco de dados e nas folhas de anúncios do SQL no portal do Azure acompanhar o status das ameaças ativas.

Clique em **alerta de proteção avançada contra ameaças** para iniciar a página de alertas da central de segurança do Azure e obtenha uma visão geral das ameaças do SQL ativas detectadas no banco de dados ou data warehouse.

   ![Alerta de proteção avançada contra ameaças](./media/sql-database-threat-detection/threat_detection_alert.png)

   ![Alert2 de proteção avançada contra ameaças](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [proteção avançada contra ameaças em bancos de dados individuais e em pool](sql-database-threat-detection.md).
- Saiba mais sobre a [proteção avançada contra ameaças na instância gerenciada](sql-database-managed-instance-threat-detection.md).
- Saiba mais sobre a [segurança de dados avançada](sql-database-advanced-data-security.md).
- Saiba mais sobre a [auditoria do Banco de Dados SQL do Azure](sql-database-auditing.md)
- Saiba mais sobre a [Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Para saber mais sobre preços, visite a [página de preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/)  
