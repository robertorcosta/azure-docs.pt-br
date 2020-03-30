---
title: Proteção Avançada contra Ameaças
description: O Advanced Threat Protection detecta atividades anômalas de banco de dados indicando potenciais ameaças à segurança no Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: monhaber
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 02/05/2020
tags: azure-synapse
ms.openlocfilehash: 17ca8cbb7a55e9c0d44af099f4884f71b1cd457a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124768"
---
# <a name="advanced-threat-protection-for-azure-sql-database"></a>Proteção Avançada contra Ameaças para o Banco de Dados SQL do Azure

O Advanced Threat Protection for [Azure SQL Database](sql-database-technical-overview.md) e [o Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) detectam atividades anômalas indicando tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados.

O Advanced Threat Protection faz parte da oferta [de segurança de dados Avançada](sql-database-advanced-data-security.md) (ADS), que é um pacote unificado para recursos avançados de segurança SQL. A Proteção Avançada contra Ameaças pode ser acessada e gerenciada por meio do portal central da ADS do SQL.

> [!NOTE]
> Este tópico se aplica ao servidor Azure SQL e ao SQL Database e ao Azure Synapse que são criados no servidor SQL do Azure. Para simplificar, o Banco de Dados SQL é usado quando se refere tanto ao Banco de Dados SQL quanto ao Sinapse Azure.

## <a name="what-is-advanced-threat-protection"></a>O que é proteção avançada contra ameaças

 O Advanced Threat Protection fornece uma nova camada de segurança, que permite que os clientes detectem e respondam a possíveis ameaças à medida que ocorrem, fornecendo alertas de segurança sobre atividades anômalas. Os usuários receberão um alerta em caso de atividades suspeitas em bancos de dados, possíveis vulnerabilidades e ataques de injeção de SQL, bem como padrões anômalos de consultas e acesso a banco de dados. O Advanced Threat Protection integra alertas ao [Azure Security Center](https://azure.microsoft.com/services/security-center/), que incluem detalhes de atividades suspeitas e recomendam ações sobre como investigar e mitigar a ameaça. O Advanced Threat Protection torna mais simples lidar com possíveis ameaças ao banco de dados sem a necessidade de ser um especialista em segurança ou gerenciar sistemas avançados de monitoramento de segurança.

Para uma experiência de investigação completa, é recomendável habilitar a [Auditoria de Banco de Dados SQL](sql-database-auditing.md), que grava eventos de banco de dados em um log de auditoria na conta de armazenamento do Azure.  

## <a name="advanced-threat-protection-alerts"></a>Alertas da Proteção Avançada contra Ameaças

O Banco de Dados Avançado de Proteção contra Ameaças para O Azure SQL detecta atividades anômalas que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados e pode desencadear os seguintes alertas:

- **Vulnerabilidade à injeção SQL**: Este alerta é acionado quando um aplicativo gera uma declaração SQL defeituosa no banco de dados. Esse alerta pode indicar uma possível vulnerabilidade a ataques de injeção de SQL. Há dois motivos possíveis para a geração de uma instrução com erro:

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

## <a name="explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal"></a>Explore alertas avançados de proteção contra ameaças para seu banco de dados no portal Azure

O Advanced Threat Protection integra seus alertas com [o centro de segurança Do Zure.](https://azure.microsoft.com/services/security-center/) As telhas de proteção de ameaças avançadas sql ao vivo dentro do banco de dados e lâminas SQL ADS no portal Azure rastreiam o status de ameaças ativas.

Clique **no alerta de proteção de ameaças avançadas** para iniciar a página de alertas do Azure Security Center e obtenha uma visão geral das ameaças SQL ativas detectadas no banco de dados.

   ![Alerta avançado de proteção contra ameaças](./media/sql-database-threat-detection/threat_detection_alert.png)

   ![Alerta avançado de proteção contra ameaças2](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [o Advanced Threat Protection em bancos de dados únicos e agrupados.](sql-database-threat-detection.md)
- Saiba mais sobre [o Advanced Threat Protection na instância gerenciada](sql-database-managed-instance-threat-detection.md).
- Saiba mais sobre [a segurança avançada de dados.](sql-database-advanced-data-security.md)
- Saiba mais sobre [a auditoria do Banco de Dados Azure SQL](sql-database-auditing.md)
- Saiba mais sobre [o centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Para obter mais informações sobre preços, consulte a página de preços do [Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/)  
