---
title: Gerenciar pools elásticos
description: Criar e gerenciar pools elásticos do Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 4ae2e84ab5f6108de4e8236c39371cf9658809d8
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823844"
---
# <a name="manage-elastic-pools-in-azure-sql-database"></a>Gerenciar Pools elásticos no banco de dados SQL do Azure

Com um pool elástico, você pode determinar a quantidade de recursos que o pool elástico requer para lidar com a carga de trabalho de seus bancos de dados e a quantidade de recursos para cada banco de dados em pool.

## <a name="azure-portal-manage-elastic-pools-and-pooled-databases"></a>Portal do Azure: gerenciar pools elásticos e bancos de dados em pool

Todas as configurações de pool podem ser localizadas em um só local: na folha **Configurar pool**. Para chegar até aqui, localize um pool elástico no portal e clique em **Configurar pool** na parte superior da folha ou no menu de recursos esquerdo.

A partir deste ponto, é possível fazer qualquer combinação das alterações a seguir e salvá-las em um lote:

1. Alterar a camada de serviço do pool
2. Escalar o desempenho (DTU ou vCores) e armazenar ou reduzir verticalmente
3. Adicionar ou remover bancos de dados de/para o pool
4. Definir um limite de desempenho mínimo (garantido) e máximo para os bancos de dados nos pools
5. Analise o resumo de custos para visualizar quaisquer alterações na fatura como resultado das novas seleções

![Folha de configuração do pool elástico](./media/sql-database-elastic-pool-manage-portal/configure-pool.png)

## <a name="powershell-manage-elastic-pools-and-pooled-databases"></a>PowerShell: gerenciar pools elásticos e bancos de dados em pool

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, consulte [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos.

Para criar e gerenciar pools elásticos do Banco de Dados SQL e bancos de dados em pool com o Azure PowerShell, use os seguintes cmdlets do PowerShell. Se você precisa instalar ou atualizar o PowerShell, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Para criar e gerenciar os servidores do Banco de Dados SQL para um pool elástico, confira [Criar e gerenciar servidores do Banco de Dados SQL](sql-database-servers.md). Para criar e gerenciar regras de firewall, consulte [criar e gerenciar regras de firewall usando o PowerShell](sql-database-firewall-configure.md#use-powershell-to-manage-server-level-ip-firewall-rules).

> [!TIP]
> Para ver scripts de exemplo do PowerShell, consulte [Criar pools elásticos e mover bancos de dados entre pools e fora de um pool usando o PowerShell](scripts/sql-database-move-database-between-pools-powershell.md) e [Usar o PowerShell para monitorar e dimensionar um pool elástico SQL no Banco de Dados SQL do Azure](scripts/sql-database-monitor-and-scale-pool-powershell.md).
>

| Cmdlet | DESCRIÇÃO |
| --- | --- |
|[New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool)|Cria um pool elástico.|
|[Get-AzSqlElasticPool](/powershell/module/az.sql/get-azsqlelasticpool)|Obtém os pools elásticos e seus valores de propriedade.|
|[Set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool)|Modifica as propriedades de um pool elástico Por exemplo, use a propriedade **StorageMB** para modificar o armazenamento máximo de um pool elástico.|
|[Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool)|Exclui um pool elástico.|
|[Get-AzSqlElasticPoolActivity](/powershell/module/az.sql/get-azsqlelasticpoolactivity)|Obtém o status de operações em um pool elástico|
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Cria um novo banco de dados em um pool existente ou como um Banco de Dados Individual. |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Obtém um ou mais bancos de dados.|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Define as propriedades para um banco de dados ou move um banco de dados existente para um pool elástico, para fora dele ou entre pools elásticos.|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Remove um banco de dados.|

> [!TIP]
> A criação de muitos bancos de dados em um pool elástico pode levar tempo quando feito usando o portal ou cmdlets do PowerShell que criam apenas um banco de dados individual por vez. Para automatizar a criação em um pool elástico, consulte [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).

## <a name="azure-cli-manage-elastic-pools-and-pooled-databases"></a>CLI do Azure: gerenciar pools elásticos e bancos de dados em pool

Para criar e gerenciar pools elásticos do Banco de Dados SQL com a [CLI do Azure](/cli/azure), use os seguintes comandos do [Banco de Dados SQL da CLI do Azure](/cli/azure/sql/db). Use o [Cloud Shell](/azure/cloud-shell/overview) para executar a CLI no seu navegador ou [instale-o](/cli/azure/install-azure-cli) no macOS, Linux ou Windows.

> [!TIP]
> Para scripts de exemplo da CLI do Azure, consulte [Usar a CLI para mover um Banco de Dados SQL do Azure em um pool elástico SQL](scripts/sql-database-move-database-between-pools-cli.md) e [Usar a CLI do Azure para escalar um pool elástico do SQL no Banco de Dados SQL do Azure](scripts/sql-database-scale-pool-cli.md).
>

| Cmdlet | DESCRIÇÃO |
| --- | --- |
|[az sql elastic-pool create](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-create)|Cria um pool elástico.|
|[az sql elastic-pool list](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list)|Retorna uma lista de pools elásticos em um servidor.|
|[az sql elastic-pool list-dbs](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-dbs)|Retorna uma lista de bancos de dados em um pool elástico.|
|[az sql elastic-pool list-editions](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-editions)|Também inclui as configurações DTU do pool disponível, limites de armazenamento e configurações por banco de dados. Para reduzir o detalhamento, os limites de armazenamento adicionais e as configurações por banco de dados ficam ocultos por padrão.|
|[az sql elastic-pool update](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)|Atualiza um pool elástico.|
|[az sql elastic-pool delete](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-delete)|Excluir o pool elástico.|

## <a name="transact-sql-manage-pooled-databases"></a>Transact-SQL: gerenciar bancos de dados em pool

Para criar e mover bancos de dados dentro de pools elásticos existentes ou para retornar informações sobre um pool elástico de Banco de Dados SQL com o Transact-SQL, use os seguintes comandos do T-SQL. Você pode emitir esses comandos usando o Portal do Azure, o [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), o [Visual Studio Code](https://code.visualstudio.com/docs) ou qualquer outro programa que pode se conectar a um servidor de Banco de Dados SQL do Azure e passar comandos Transact-SQL. Para criar e gerenciar regras de firewall usando o T-SQL, consulte [Gerenciar regras de firewall usando o Transact-SQL](sql-database-firewall-configure.md#use-transact-sql-to-manage-ip-firewall-rules).

> [!IMPORTANT]
> Não é possível criar, atualizar ou excluir um pool elástico de Banco de Dados SQL do Azure usando o Transact-SQL. Você pode adicionar ou remover bancos de dados de um pool elástico, bem como pode usar DMVs para retornar informações sobre os pools elásticos existentes.
>

| Command | DESCRIÇÃO |
| --- | --- |
|[CREATE DATABASE (Banco de Dados SQL do Azure)](/sql/t-sql/statements/create-database-azure-sql-database)|Cria um novo banco de dados em um pool existente ou como um Banco de Dados Individual. Você deve estar conectado ao banco de dados mestre para criar um novo banco de dados.|
| [ALTER DATABASE (Banco de Dados SQL do Azure)](/sql/t-sql/statements/alter-database-azure-sql-database) |Move um banco de dados para dentro de um pool elástico, para fora dele ou entre pools elásticos.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Exclui um banco de dados.|
|[sys.elastic_pool_resource_stats (Banco de Dados SQL do Azure)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|Retorna estatísticas de uso de recursos de todos os pools elásticos em um servidor do Banco de Dados SQL. Para cada pool elástico, há uma linha para cada janela de relatórios de 15 segundos (quatro linhas por minuto). Isso inclui a utilização de CPU, E/S, log, consumo de armazenamento e solicitações/sessões simultâneas de todos os bancos de dados no pool.|
|[sys.database_service_objectives (Banco de Dados SQL do Azure)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Retorna a edição (camada de serviço), o objetivo do serviço (tipo de preço) e o nome do pool elástico, se houver um, para um Banco de Dados SQL do Azure ou um SQL Data Warehouse do Azure. Se estiver conectado ao banco de dados mestre em um servidor do Banco de Dados SQL do Azure, retornará informações sobre todos os bancos de dados. Para o SQL Data Warehouse do Azure, você deve estar conectado ao banco de dados mestre.|

## <a name="rest-api-manage-elastic-pools-and-pooled-databases"></a>API REST: gerenciar pools elásticos e bancos de dados em pool

Para criar e gerenciar pools elásticos do Banco de Dados SQL e bancos de dados em pool, use estas solicitações da API REST.

| Command | DESCRIÇÃO |
| --- | --- |
|[Pools elásticos – criar ou atualizar](https://docs.microsoft.com/rest/api/sql/elasticpools/createorupdate)|Cria um novo pool elástico ou atualiza um pool elástico existente.|
|[Pools elásticos – Excluir](https://docs.microsoft.com/rest/api/sql/elasticpools/delete)|Excluir o pool elástico.|
|[Pools elásticos – Obter](https://docs.microsoft.com/rest/api/sql/elasticpools/get)|Obtém um pool elástico.|
|[Pools elásticos – listar por servidor](https://docs.microsoft.com/rest/api/sql/elasticpools/listbyserver)|Retorna uma lista de pools elásticos em um servidor.|
|[Pools elásticos – Atualizar](https://docs.microsoft.com/rest/api/sql/elasticpools/listbyserver)|Atualiza um pool elástico existente.|
|[Atividades do pool elástico](https://docs.microsoft.com/rest/api/sql/elasticpoolactivities)|Retorna as atividades do pool elástico.|
|[Atividades do banco de dados de pool elástico](https://docs.microsoft.com/rest/api/sql/elasticpooldatabaseactivities)|Retorna as atividades nos bancos de dados dentro de um pool elástico.|
|[Banco de dados – criar ou atualizar](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|Cria um novo banco de dados ou atualiza um banco de dados existente.|
|[Bancos de Dados – Obter](https://docs.microsoft.com/rest/api/sql/databases/get)|Obtém um banco de dados.|
|[Bancos de dados – listar por pool elástico](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|Retorna uma lista de bancos de dados em um pool elástico.|
|[Bancos de dados – listar por servidor](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|Retorna uma lista de bancos de dados em um servidor.|
|[Bancos de Dados – Atualizar](https://docs.microsoft.com/rest/api/sql/databases/update)|Atualiza um banco de dados existente.|

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre padrões de design para aplicativos SaaS usando pools elásticos, confira [Padrões de design para aplicativos de SaaS multilocatários com o banco de dados SQL do Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).
* Para ver um tutorial de SaaS usando pools elásticos, consulte [Introdução ao aplicativo Wingtip SaaS](sql-database-wtp-overview.md).
