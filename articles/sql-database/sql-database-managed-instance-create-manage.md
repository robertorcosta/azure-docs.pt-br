---
title: Referência de API de gerenciamento para instância gerenciada
description: Saiba mais sobre como criar e gerenciar Instâncias Gerenciadas do Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 713217a933c646cc4d04759f5697bbc0312827ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268854"
---
# <a name="managed-api-reference-for-azure-sql-database-managed-instances"></a>Gerenciamento da referência de API para Instâncias Gerenciadas do Banco de Dados SQL do Azure

É possível criar e gerenciar Instâncias Gerenciadas do Banco de Dados SQL do Azure usando o portal do Azure, o PowerShell, a CLI do Azure, a API REST e o Transact-SQL. Neste artigo, você encontra uma visão geral das funções e API que você pode usar para criar e configurar a instância gerenciada.

## <a name="azure-portal-create-a-managed-instance"></a>Portal Azure: Crie uma instância gerenciada

Para obter um início rápido mostrando como criar uma instância gerenciada do banco de dados SQL do Azure, consulte [Quickstart: Crie uma instância gerenciada do banco de dados SQL do Azure](sql-database-managed-instance-get-started.md).

## <a name="powershell-create-and-manage-managed-instances"></a>PowerShell: Crie e gerencie instâncias gerenciadas

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pelo Banco de Dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para obter esses cmdlets, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

Para criar e gerenciar instâncias gerenciadas com o Azure PowerShell, use os seguintes cmdlets do PowerShell. Se você precisa instalar ou atualizar o PowerShell, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps).

> [!TIP]
> Para scripts de exemplo do PowerShell, consulte [Script de início rápido: Crie a instância gerenciada do Azure SQL usando a biblioteca PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../quick-start-script-create-azure-sql-managed-instance-using-powershell/).

| Cmdlet | Descrição |
| --- | --- |
|[New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)|Cria uma Instância Gerenciada do Banco de Dados SQL do Azure |
|[Get-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstance)|Retorna informações sobre a Instância Gerenciada do SQL do Azure|
|[Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)|Define propriedades para uma Instância Gerenciada do Banco de Dados SQL do Azure|
|[Remove-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstance)|Remove uma Instância Gerenciada do Banco de Dados SQL do Azure|
|[Banco de dados do New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstancedatabase)|Cria um banco de dados de Instância Gerenciada do Banco de Dados SQL do Azure|
|[Banco de dados Get-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabase)|Retorna informações sobre o banco de dados de Instância Gerenciada do SQL do Azure|
|[Remove-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabase)|Remove um banco de dados de Instância Gerenciada do Banco de Dados SQL do Azure|
|[Restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)|Restaura um banco de dados de Instância Gerenciada do Banco de Dados SQL do Azure|

## <a name="azure-cli-create-and-manage-managed-instances"></a>Cli azure: crie e gerencie instâncias gerenciadas

Para criar e gerenciar instâncias gerenciadas com a [CLI do Azure](/cli/azure), use os seguintes comandos da [Instância Gerenciada do SQL da CLI do Azure](/cli/azure/sql/mi). Use o [Cloud Shell](/azure/cloud-shell/overview) para executar a CLI no seu navegador ou [instale-o](/cli/azure/install-azure-cli) no macOS, Linux ou Windows.

> [!TIP]
> Para ver um início rápido da CLI do Azure, consulte [Trabalhar com a Instância Gerenciada do SQL usando a CLI do Azure](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).

| Cmdlet | Descrição |
| --- | --- |
|[az sql mi create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) |Cria uma instância gerenciada|
|[az sql mi list](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-list)|Lista as instâncias gerenciadas disponíveis|
|[az sql mi show](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-show)|Obtém os detalhes de uma instância gerenciada|
|[az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)|Atualiza uma instância gerenciada|
|[az sql mi delete](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-delete)|Remove uma instância gerenciada|
|[az sql midb create](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-create) |Cria um banco de dados gerenciado|
|[az sql midb list](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-list)|Lista os bancos de dados gerenciados disponíveis|
|[az sql midb restore](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-restore)|Restaura um banco de dados gerenciado|
|[az sql midb delete](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-delete)|Remove um banco de dados gerenciado|

## <a name="transact-sql-create-and-manage-instance-databases"></a>Transact-SQL: Crie e gerencie bancos de dados de instâncias

Para criar e gerenciar o banco de dados de instância após a criação da Instância Gerenciada, use os seguintes comandos T-SQL. É possível emitir esses comandos usando o portal do Azure, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is). [Visual Studio Code](https://code.visualstudio.com/docs), ou qualquer outro programa que possa conectar a um servidor do Banco de Dados SQL do Azure e passar comandos Transact-SQL.

> [!TIP]
> Para iniciar rapidamente, para mostrar que você tem que configurar e conectar a uma instância gerenciada usando o SQL Server Management Studio no Microsoft Windows, consulte [Quickstart: Configure a Azure VM para se conectar a uma instância gerenciada do banco de dados Azure SQL](sql-database-managed-instance-configure-vm.md) e [ao Quickstart: Configure uma conexão ponto a ponto com uma instância gerenciada do Banco de Dados SQL do Azure a partir de locais.](sql-database-managed-instance-configure-p2s.md)
> [!IMPORTANT]
> Não é possível criar ou excluir uma instância gerenciada usando o Transact-SQL.

| Comando | Descrição |
| --- | --- |
|[CRIAR BANCO DE DADOS](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current)|Cria um novo banco de dados de instância gerenciada. Você deve estar conectado ao banco de dados mestre para criar um novo banco de dados.|
| [ALTERAR BANCO DE DADOS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) |Modifica o banco de dados de Instância Gerenciada do SQL do Azure.|

## <a name="rest-api-create-and-manage-managed-instances"></a>API REST: Crie e gerencie instâncias gerenciadas

Para criar e gerenciar Instâncias Gerenciadas, use estas solicitações de API REST.

| Comando | Descrição |
| --- | --- |
|[Managed Instances - Create Or Update](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)|Cria ou atualiza uma instância gerenciada.|
|[Managed Instances - Delete](https://docs.microsoft.com/rest/api/sql/managedinstances/delete)|Exclui uma instância gerenciada.|
|[Managed Instances - Get](https://docs.microsoft.com/rest/api/sql/managedinstances/get)|Obtém uma instância gerenciada.|
|[Managed Instances - List](https://docs.microsoft.com/rest/api/sql/managedinstances/list)|Retorna uma lista de instâncias gerenciadas em uma assinatura.|
|[Managed Instances - List By Resource Group](https://docs.microsoft.com/rest/api/sql/managedinstances/listbyresourcegroup)|Retorna uma lista de instâncias gerenciadas em um grupo de recursos.|
|[Managed Instances - Update](https://docs.microsoft.com/rest/api/sql/managedinstances/update)|Atualiza uma instância gerenciada.|

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre como migrar um banco de dados do SQL Server para o Azure, confira [Migrar para o Banco de Dados SQL do Azure](sql-database-single-database-migrate.md).
- Para obter informações sobre os recursos com suporte, consulte [Recursos](sql-database-features.md).
