---
title: Criar, gerenciar servidores e bancos de dados únicos
description: Saiba como criar e gerenciar servidores do Banco de Dados SQL e bancos de dados individuais.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 02c4d7ba545282e3654f3889dd8000af33c728c7
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820915"
---
# <a name="create-and-manage-sql-database-servers-and-single-databases-in-azure-sql-database"></a>Criar e gerenciar servidores do Banco de Dados SQL e bancos de dados individuais no Banco de Dados SQL do Azure

Você pode criar e gerenciar servidores do Banco de Dados SQL e bancos de dados individuais usando o portal do Azure, o PowerShell, a CLI do Azure, a API REST e o Transact-SQL.

## <a name="azure-portal-manage-sql-database-servers-and-single-databases"></a>Portal do Azure: gerenciar servidores e bancos de dados individuais

Você pode criar um grupo de recursos do banco de dados SQL do Azure antecipadamente ou ao criar o próprio servidor. Há vários métodos para obter um novo formulário de servidor SQL, seja criando um novo servidor SQL ou como parte da criação de um novo banco de dados.

### <a name="create-a-blank-sql-database-server"></a>Criar um servidor do Banco de Dados SQL em branco

Para criar um servidor do Banco de Dados SQL usando o [portal do Azure](https://portal.azure.com), navegue até um formulário em branco de servidor (servidor lógico) SQL.  

### <a name="create-a-blank-or-sample-sql-single-database"></a>Criar um Banco de Dados SQL individual em branco ou de exemplo

Para criar um Banco de Dados SQL do Azure individual usando o [portal do Azure](https://portal.azure.com), navegue até um formulário em branco do Banco de Dados SQL e forneça as informações solicitadas. Você pode criar um servidor do Banco de Dados SQL e um grupo de recursos do Banco de Dados SQL do Azure antecipadamente ou ao criar o banco de dados individual propriamente dito. Você pode criar um banco de dados em branco ou um banco de dados de exemplo com base no Adventure Works LT.

  ![criar database-1](./media/sql-database-get-started-portal/create-database-1.png)

> [!IMPORTANT]
> Para obter informações sobre como selecionar o tipo de preço do banco de dados, consulte [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) e [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md).

Para criar uma Instância Gerenciada, consulte [Criar uma Instância Gerenciada](sql-database-managed-instance-get-started.md)

## <a name="manage-an-existing-sql-database-server"></a>Gerenciar um servidor do Banco de Dados SQL existente

Para gerenciar um servidor do Banco de Dados SQL existente, navegue até o servidor usando vários métodos, por exemplo, na página do Banco de Dados SQL específica, na página dos **servidores SQL** ou na página **Todos os recursos**.

Para gerenciar um banco de dados existente, navegue até a página **bancos de dados SQL** e clique no banco de dados que você deseja gerenciar. A captura de tela a seguir mostra como começar a configurar um firewall de nível de servidor para um banco de dados na página **Visão geral** de um banco de dados.

   ![regra de firewall do servidor](./media/sql-database-get-started-portal/server-firewall-rule.png)

> [!IMPORTANT]
> Para configurar propriedades de desempenho para um banco de dados, consulte [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) e [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md).
> [!TIP]
> Para um início rápido do Portal do Azure, consulte [Criar um Banco de Dados SQL do Azure no Portal do Azure](sql-database-single-database-get-started.md).

## <a name="powershell-manage-sql-database-servers-and-single-databases"></a>PowerShell: gerenciar servidores e bancos de dados individuais

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, consulte [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos.

Para criar e gerenciar os servidores de Banco de Dados SQL do Azure, bancos de dados individuais e em pool e firewalls do servidor do Banco de Dados SQL com o Azure PowerShell, use os cmdlet do PowerShell a seguir. Se você precisa instalar ou atualizar o PowerShell, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps).

> [!TIP]
> Para scripts de exemplo do PowerShell, confira [Usar o PowerShell para criar um Banco de Dados SQL do Azure individual e configurar uma regra de firewall de servidor do Banco de Dados SQL](scripts/sql-database-create-and-configure-database-powershell.md) e [Monitorar e escalar um Banco de Dados SQL individual usando o PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md).

| Cmdlet | DESCRIÇÃO |
| --- | --- |
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Cria um banco de dados |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Obtém um ou mais bancos de dados|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Define propriedades para um banco de dados ou move um banco de dados existente para um pool, elástico|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Remove um banco de dados|
|[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)|Cria um grupos de recursos|
|[New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver)|Cria um servidor|
|[Get-AzSqlServer](/powershell/module/az.sql/get-azsqlserver)|Retorna informações sobre servidores|
|[Set-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserver)|Modifica as propriedades de um servidor|
|[Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)|Remove um servidor|
|[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)|Cria uma regra de firewall no nível de servidor |
|[Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule)|Obtém as regras de firewall para um servidor|
|[Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule)|Modifica uma regra de firewall em um servidor|
|[Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule)|Exclui uma regra de firewall de um servidor.|
| New-AzSqlServerVirtualNetworkRule | Cria um [*regra da rede virtual*](sql-database-vnet-service-endpoint-rule-overview.md), com base em uma sub-rede que é um ponto de extremidade de serviço de rede virtual. |

## <a name="azure-cli-manage-sql-database-servers-and-single-databases"></a>CLI do Azure: gerenciar servidores e bancos de dados individuais

Para criar e gerenciar servidores, bancos de dados e firewalls do SQL Azure com a [CLI do Azure](/cli/azure), use os comandos do [Banco de Dados SQL da CLI do Azure](/cli/azure/sql/db). Use o [Cloud Shell](/azure/cloud-shell/overview) para executar a CLI no seu navegador ou [instale-o](/cli/azure/install-azure-cli) no macOS, Linux ou Windows. Para criar e gerenciar pools elásticos, consulte [Pools elásticos](sql-database-elastic-pool.md).

> [!TIP]
> Para um início rápido da CLI do Azure, consulte [Criar um Banco de Dados SQL do Azure individual usando a CLI do Azure](sql-database-cli-samples.md). Para ver scripts de exemplo da CLI do Azure, consulte [Usar a CLI para criar um Banco de Dados SQL do Azure individual e configurar uma regra de firewall do Banco de Dados SQL](scripts/sql-database-create-and-configure-database-cli.md) e [Usar a CLI para monitorar e escalar um único Banco de Dados SQL do Azure](scripts/sql-database-monitor-and-scale-database-cli.md).
>

| Cmdlet | DESCRIÇÃO |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az-sql-db-create) |Cria um banco de dados|
|[az sql db list](/cli/azure/sql/db#az-sql-db-list)|Lista todos os bancos de dados e data warehouses em um servidor, ou todos os bancos de dados em um pool elástico|
|[az sql db list-editions](/cli/azure/sql/db#az-sql-db-list-editions)|Lista os objetivos de serviço disponíveis e os limites de armazenamento|
|[az sql db list-usages](/cli/azure/sql/db#az-sql-db-list-usages)|Retorna os usos do banco de dados|
|[az sql db show](/cli/azure/sql/db#az-sql-db-show)|Obtém um banco de dados ou data warehouse|
|[az sql db update](/cli/azure/sql/db#az-sql-db-update)|Atualiza um banco de dados|
|[az sql db delete](/cli/azure/sql/db#az-sql-db-delete)|Remove um banco de dados|
|[az group create](/cli/azure/group#az-group-create)|Cria um grupos de recursos|
|[az sql server create](/cli/azure/sql/server#az-sql-server-create)|Cria um servidor|
|[az sql server list](/cli/azure/sql/server#az-sql-server-list)|Lista servidores|
|[az sql server list-usages](/cli/azure/sql/server#az-sql-server-list-usages)|Retorna os usos do servidor|
|[az sql server show](/cli/azure/sql/server#az-sql-server-show)|Obtém um servidor|
|[az sql server update](/cli/azure/sql/server#az-sql-server-update)|Atualiza um servidor|
|[az sql server delete](/cli/azure/sql/server#az-sql-server-delete)|Exclui um servidor|
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Cria uma regra de firewall de servidor|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Lista as regras de firewall em um servidor|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Mostra os detalhes de uma regra de firewall|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Atualiza uma regra de firewall|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Exclui uma regra de firewall|

## <a name="transact-sql-manage-sql-database-servers-and-single-databases"></a>Transact-SQL: gerenciar servidores e bancos de dados únicos

Para criar e gerenciar servidores, bancos de dados e firewalls do Azure SQL com o Transact-SQL, use os comandos T-SQL a seguir. Você pode emitir esses comandos usando o Portal do Azure, o [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), o [Visual Studio Code](https://code.visualstudio.com/docs) ou qualquer outro programa que pode se conectar a um servidor de Banco de Dados SQL do Azure e passar comandos Transact-SQL. Para gerenciar pools elásticos, consulte [Pools elásticos](sql-database-elastic-pool.md).

> [!TIP]
> Para um início rápido que usa o SQL Server Management Studio no Microsoft Windows, consulte [Banco de Dados SQL do Azure: usar o SQL Server Management Studio para conectar e consultar dados](sql-database-connect-query-ssms.md). Para um início rápido que usa o Visual Studio Code no macOS, Linux ou Windows, consulte [Banco de Dados SQL do Azure: usar o Visual Studio Code para se conectar e consultar dados](sql-database-connect-query-vscode.md).
> [!IMPORTANT]
> Não é possível criar ou excluir um servidor usando o Transact-SQL.

| Command | DESCRIÇÃO |
| --- | --- |
|[CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current)|Cria um novo banco de dados individual. Você deve estar conectado ao banco de dados mestre para criar um novo banco de dados.|
| [ALTER DATABASE (Banco de Dados SQL do Azure)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Modifica um Banco de Dados SQL do Azure. |
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Exclui um banco de dados.|
|[sys.database_service_objectives (Banco de Dados SQL do Azure)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Retorna a edição (camada de serviço), o objetivo do serviço (tipo de preço) e o nome do pool elástico, se houver um, para um Banco de Dados SQL do Azure ou um SQL Data Warehouse do Azure. Se estiver conectado ao banco de dados mestre em um servidor do Banco de Dados SQL do Azure, retornará informações sobre todos os bancos de dados. Para o SQL Data Warehouse do Azure, você deve estar conectado ao banco de dados mestre.|
|[sys.dm_db_resource_stats (Banco de Dados SQL do Azure)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Retorna o consumo de CPU, E/S e memória para um banco de dados do Banco de Dados SQL do Azure. Existe uma linha para cada 15 segundos, mesmo se não houver nenhuma atividade no banco de dados.|
|[sys.resource_stats (Banco de Dados SQL do Azure)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Retorna o uso de CPU e dados de armazenamento para um Banco de Dados SQL do Azure. Os dados são coletados e agregados em intervalos de cinco minutos.|
|[sys.database_connection_stats (Banco de Dados SQL do Azure)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Contém estatísticas para eventos de conectividade de banco de dados do Banco de Dados SQL, fornecendo uma visão geral da conexão de banco de dados e das falhas. |
|[sys.event_log (Banco de Dados SQL do Azure)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Retorna as conexões de banco de dados do Banco de Dados SQL do Azure com êxito, as falhas de conexão e os deadlocks. Você pode usar essas informações para acompanhar ou solucionar problemas da atividade de banco de dados com o Banco de Dados SQL.|
|[sp_set_firewall_rule (Banco de Dados SQL do Azure)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Cria ou atualiza as configurações de firewall no nível do servidor para o servidor do Banco de Dados SQL. Esse procedimento armazenado só está disponível no banco de dados mestre para o logon principal no nível de servidor. Uma regra de firewall de nível de servidor só pode ser criada usando Transact-SQL depois que a primeira regra de firewall no nível do servidor foi criada por um usuário com permissões no nível do Azure|
|[sys.firewall_rules (Banco de Dados SQL do Azure)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Retorna informações sobre as configurações de firewall no nível do servidor associadas ao seu Banco de Dados SQL do Microsoft Azure.|
|[sp_delete_firewall_rule (Banco de Dados SQL do Azure)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Remove as configurações de firewall no nível do servidor do servidor do Banco de Dados SQL. Esse procedimento armazenado só está disponível no banco de dados mestre para o logon principal no nível de servidor.|
|[sp_set_database_firewall_rule (Banco de Dados SQL do Azure)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Cria ou atualiza as regras de firewall no nível do banco de dados para o Banco de Dados SQL do Azure ou o SQL Data Warehouse. As regras de firewall do banco de dados podem ser configuradas para o banco de dados mestre e para bancos de dados de usuário no Banco de Dados SQL. As regras de firewall do banco de dados são úteis quando você usa usuários de banco de dados independentes. |
|[sys.database_firewall_rules (Banco de Dados SQL do Azure)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Retorna informações sobre as configurações de firewall no nível do banco de dados associadas ao seu Banco de Dados SQL do Microsoft Azure. |
|[sp_delete_database_firewall_rule (Banco de Dados SQL do Azure)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Remove a configuração de firewall no nível do banco de dados para o Banco de Dados SQL do Azure ou o SQL Data Warehouse. |

## <a name="rest-api-manage-sql-database-servers-and-single-databases"></a>API REST: gerenciar servidores e bancos de dados individuais

Para criar e gerenciar servidores, bancos de dados e firewalls do SQL Server do Azure, use estas solicitações de API REST.

| Command | DESCRIÇÃO |
| --- | --- |
|[Servidores – criar ou atualizar](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Cria ou atualiza um novo servidor.|
|[Servidores - Excluir](https://docs.microsoft.com/rest/api/sql/servers/delete)|Exclui um servidor SQL.|
|[Servidores - Obter](https://docs.microsoft.com/rest/api/sql/servers/get)|Obtém um servidor.|
|[Servidores - Listar](https://docs.microsoft.com/rest/api/sql/servers/list)|Retorna uma lista de servidores em uma assinatura.|
|[Servidores – listar por grupo de recursos](https://docs.microsoft.com/rest/api/sql/servers/listbyresourcegroup)|Retorna uma lista de servidores em um grupo de recursos.|
|[Servidores - Atualizar](https://docs.microsoft.com/rest/api/sql/servers/update)|Atualiza um servidor existente.|
|[Banco de dados – criar ou atualizar](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|Cria um novo banco de dados ou atualiza um banco de dados existente.|
|[Bancos de dados - Delete](https://docs.microsoft.com/rest/api/sql/databases/delete)|Exclui um banco de dados.|
|[Bancos de Dados – Obter](https://docs.microsoft.com/rest/api/sql/databases/get)|Obtém um banco de dados.|
|[Bancos de dados – listar por pool elástico](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|Retorna uma lista de bancos de dados em um pool elástico.|
|[Bancos de dados – listar por servidor](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|Retorna uma lista de bancos de dados em um servidor.|
|[Bancos de Dados – Atualizar](https://docs.microsoft.com/rest/api/sql/databases/update)|Atualiza um banco de dados existente.|
|[Regras de Firewall – criar ou atualizar](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate)|Cria ou atualiza uma regra de firewall.|
|[Regras de firewall – excluir](https://docs.microsoft.com/rest/api/sql/firewallrules/delete)|Exclui uma regra de firewall.|
|[Regras de firewall – obter](https://docs.microsoft.com/rest/api/sql/firewallrules/get)|Obtêm uma regra de firewall.|
|[Regras de firewall – listar por servidor](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver)|Retorna uma lista de regras de firewall.|

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre como migrar um banco de dados do SQL Server para o Azure, confira [Migrar para o Banco de Dados SQL do Azure](sql-database-single-database-migrate.md).
- Para obter informações sobre os recursos com suporte, consulte [Recursos](sql-database-features.md).
