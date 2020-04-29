---
title: Habilitar o ajuste automático
description: Habilite o ajuste automático do Banco de Dados SQL do Azure com facilidade.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 12/03/2019
ms.openlocfilehash: eed839c277156046ff9b7d97c6e87636a0822889
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79299321"
---
# <a name="enable-automatic-tuning-to-monitor-queries-and-improve-workload-performance"></a>Habilite o ajuste automático para monitorar consultas e melhorar o desempenho da carga de trabalho

O Banco de Dados SQL do Azure é um serviço de dados gerenciados automaticamente que monitora as consultas constantemente e identifica a ação que pode ser executada para melhorar o desempenho da carga de trabalho. Examine as recomendações e aplique-as manualmente ou permita que o Banco de Dados SQL do Azure aplique as ações corretivas automaticamente – isso é conhecido como **modo de ajuste automático**.

O ajuste automático pode ser ativado no nível do servidor ou do banco de dados por meio das chamadas do [Portal do Azure](sql-database-automatic-tuning-enable.md#azure-portal), [API REST](sql-database-automatic-tuning-enable.md#rest-api) e dos comandos [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current).

> [!NOTE]
> Por Instância Gerenciada, a opção com suporte FORCE_LAST_GOOD_PLAN pode ser configurada somente por meio [de T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management) . A configuração baseada em portal e as opções de ajuste automático de índice descritas neste artigo não se aplicam a Instância Gerenciada.

> [!NOTE]
> Não há suporte para a configuração de opções de ajuste automático por meio do modelo ARM (Azure Resource Manager) no momento.

## <a name="enable-automatic-tuning-on-server"></a>Habilitar o ajuste automático no servidor

No nível do servidor, você pode escolher entre herdar ou não a configuração de ajuste automático de "Padrões do Azure". Os padrões do Azure são: FORCE_LAST_GOOD_PLAN está habilitado, CREATE_INDEX está habilitado e DROP_INDEX está desabilitado.

> [!IMPORTANT]
> A partir de março, 2020 alterações nos padrões do Azure para o ajuste automático entrarão em vigor da seguinte maneira:
>
> - Os novos padrões do Azure serão FORCE_LAST_GOOD_PLAN = habilitado, CREATE_INDEX = desabilitado e DROP_INDEX = desabilitado.
> - Os servidores existentes sem preferências de ajuste automático configuradas serão automaticamente configurados para HERDAr os novos padrões do Azure. Isso se aplica a todos os clientes que atualmente têm configurações de servidor para ajuste automático em um estado indefinido.
> - Novos servidores criados serão automaticamente configurados para HERDAr os novos padrões do Azure (diferentemente do anterior quando a configuração de ajuste automático estava em um estado indefinido na criação do novo servidor).

### <a name="azure-portal"></a>Portal do Azure

Para habilitar o ajuste automático no **servidor** lógico de Banco de Dados SQL do Azure, navegue até o servidor no portal do Azure e, em seguida, selecione **Ajuste automático** no menu.

![Server (Servidor)](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Observe que a opção **DROP_INDEX** no momento não é compatível com aplicativos que usam dicas de índice e de alternância de partição e não deve ser habilitada nesses casos. Não há suporte para a remoção de índices não utilizados para as camadas de serviço Premium e Comercialmente Crítico.
>

Selecione as opções de ajuste automático que você deseja habilitar e selecione **aplicar**.

As opções de ajuste automático em um servidor são aplicadas a todos os bancos de dados deste servidor. Por padrão, todos os bancos de dados herdam a configuração de seu servidor pai, mas isso pode ser substituído e especificado para cada banco de dados individualmente.

### <a name="rest-api"></a>API REST

Saiba mais sobre o uso da API REST para ativar o Ajuste automático em um servidor, consulte [SQL Server, ajuste automático dos métodos UPDATE e GET HTTP](https://docs.microsoft.com/rest/api/sql/serverautomatictuning).

## <a name="enable-automatic-tuning-on-an-individual-database"></a>Habilitar o ajuste automático em um banco de dados individual

O Banco de Dados SQL do Azure permite especificar a configuração de ajuste automático individualmente para cada banco de dados. No nível do banco de dados, você pode escolher entre herdar ou não a configuração de ajuste automático do servidor pai, "Padrões do Azure". Os padrões do Azure são definidos para FORCE_LAST_GOOD_PLAN está habilitado, CREATE_INDEX está habilitado e DROP_INDEX está desabilitado.

> [!TIP]
> A recomendação geral é gerenciar a configuração de ajuste automático no **nível do servidor** para que as mesmas definições de configuração possam ser aplicadas automaticamente em cada banco de dados. Configure o ajuste automático em um banco de dados individual somente se você precisar que esse banco de dados tenha configurações diferentes de outras configurações herdadas do mesmo servidor.
>

### <a name="azure-portal"></a>Portal do Azure

Para habilitar o ajuste automático em um **banco de dados individual**, navegue para o banco de dados no portal do Azure e selecione **Ajuste automático**.

As configurações de ajustes automático individuais podem ser definidas separadamente para cada banco de dados. Manualmente, você pode configurar uma opção de ajuste automático individual ou especificar que uma opção herde as configurações do servidor.

![Banco de dados](./media/sql-database-automatic-tuning-enable/database.png)

Observe que a opção DROP_INDEX no momento não é compatível com aplicativos que usam dicas de índice e de alternância de partição e não deve ser habilitada nesses casos.

Depois de selecionar a configuração desejada, clique em **Aplicar**.

### <a name="rest-api"></a>API Rest

Saiba mais sobre como usar a API REST para habilitar o ajuste automático em um único banco de dados, consulte [banco de dados ajuste automático do SQL métodos UPDATE e HTTP GET](https://docs.microsoft.com/rest/api/sql/databaseautomatictuning).

### <a name="t-sql"></a>T-SQL

Para habilitar o ajuste automático em um único banco de dados por meio de T-SQL, conecte-se ao banco de dados e execute a seguinte consulta:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
```

Definir o ajuste automático como AUTO aplicará os Padrões do Azure. Definindo-o como INHERIT, a configuração de ajuste automático será herdada do servidor pai. Se escolher CUSTOM, você precisará configurar manualmente o ajuste automático.

Para configurar opções de ajuste automático individuais por meio do T-SQL, conecte-se ao banco de dados e execute a consulta de modo similar a esta:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = DEFAULT, DROP_INDEX = OFF)
```

Definir a opção de ajuste individual como ON substituirá qualquer configuração de banco de dados herdada e habilitará a opção de ajuste. Defini-la a como OFF substituirá qualquer configuração herdada por esse banco de dados e desabilitará a opção de ajuste. A opção de ajuste automático, para a qual o padrão é especificado, herdará a configuração de ajuste automático das configurações de nível de servidor.  

> [!IMPORTANT]
> No caso da [replicação geográfica ativa](sql-database-auto-failover-group.md), o Ajuste automático precisa ser configurado somente no banco de dados primário. As ações de ajuste aplicadas automaticamente, por exemplo, criação ou exclusão de índice, serão replicadas automaticamente para o secundário somente leitura. A tentativa de habilitar o Ajuste automático por meio do T-SQL no secundário somente leitura resultará em uma falha, pois não há suporte para uma configuração de ajuste diferente no secundário somente leitura.
>

Encontre mais sobre como as opções de T-SQL para configurar o ajuste automático, confira [opções ALTER DATABASE SET (Transact-SQL) para o servidor do Banco de Dados SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current).

## <a name="disabled-by-the-system"></a>Desabilitado pelo sistema

O ajuste automático está monitorando todas as ações que ele realiza no banco de dados e, em alguns casos, ele pode determinar que o ajuste automático não pode funcionar adequadamente no banco de dados. Nessa situação, a opção de ajuste será desabilitada pelo sistema. Na maioria dos casos, isso ocorre porque o Repositório de Consultas não está habilitado ou está em estado somente leitura em um banco de dados específico.

## <a name="permissions"></a>Permissões

Como o ajuste automático é o recurso do Azure, para usá-lo, será necessário usar as funções RBAC internas do Azure. Usar a autenticação do SQL somente não será suficiente para usar o recurso do portal do Azure.

Para usar o ajuste automático, a permissão mínima necessária para conceder ao usuário é a função de colaborador do [BD SQL](../role-based-access-control/built-in-roles.md#sql-db-contributor) interna do Azure. Você também pode considerar o uso de funções de privilégio mais alto, SQL Server como colaborador, colaborador e proprietário.

## <a name="configure-automatic-tuning-e-mail-notifications"></a>Configurar o ajuste automático das notificações por email

Consulte Guia de [notificações por email de ajuste automático](sql-database-automatic-tuning-email-notifications.md) .

## <a name="next-steps"></a>Próximas etapas

* Leia o [artigo Ajuste automático](sql-database-automatic-tuning.md) para saber mais sobre o ajuste automático e como ele pode ajudar você a melhorar o desempenho.
* Consulte [Recomendações de desempenho](sql-database-advisor.md) para obter uma visão geral das recomendações de desempenho do Banco de Dados SQL do Azure.
* Consulte [Análise de desempenho de consultas](sql-database-query-performance.md) para saber mais sobre como visualizar o impacto no desempenho de suas principais consultas.
