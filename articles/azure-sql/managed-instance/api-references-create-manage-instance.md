---
title: Referência de API de gerenciamento para Azure SQL Instância Gerenciada
description: Saiba mais sobre como criar e configurar instâncias gerenciadas do Azure SQL Instância Gerenciada.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 3f0eb2c1ecb26475b25cd5ca41c9c61d5e2b47a3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84695565"
---
# <a name="managed-api-reference-for-azure-sql-managed-instance"></a>Referência de API gerenciada para o Azure SQL Instância Gerenciada
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Você pode criar e configurar instâncias gerenciadas do Azure SQL Instância Gerenciada usando o portal do Azure, o PowerShell, o CLI do Azure, a API REST e o Transact-SQL. Neste artigo, você pode encontrar uma visão geral das funções e da API que você pode usar para criar e configurar instâncias gerenciadas.

## <a name="azure-portal-create-a-managed-instance"></a>Portal do Azure: criar uma instância gerenciada

Para obter um guia de início rápido mostrando como criar uma instância gerenciada, consulte [início rápido: criar uma instância gerenciada](instance-create-quickstart.md).

## <a name="powershell-create-and-configure-managed-instances"></a>PowerShell: criar e configurar instâncias gerenciadas

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do Banco de Dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para esses cmdlets, confira [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo AZ e nos módulos AzureRM são substancialmente idênticos.

Para criar e gerenciar instâncias gerenciadas com o Azure PowerShell, use os seguintes cmdlets do PowerShell. Se você precisar instalar ou atualizar o PowerShell, consulte [instalar o Azure PowerShell Module](/powershell/azure/install-az-ps).

> [!TIP]
> Para scripts de exemplo do PowerShell, consulte [script de início rápido: criar uma instância gerenciada usando uma biblioteca do PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../quick-start-script-create-azure-sql-managed-instance-using-powershell/).

| Cmdlet | Descrição |
| --- | --- |
|[New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)|Cria uma instância gerenciada. |
|[Get-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstance)|Retorna informações sobre uma instância gerenciada.|
|[Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)|Define propriedades para uma instância gerenciada.|
|[Remove-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstance)|Remove uma instância gerenciada.|
|[New-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstancedatabase)|Cria um banco de dados do SQL Instância Gerenciada.|
|[Get-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabase)|Retorna informações sobre um banco de dados do SQL Instância Gerenciada.|
|[Remove-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabase)|Remove um banco de dados SQL Instância Gerenciada.|
|[Restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)|Restaura um banco de dados do SQL Instância Gerenciada.|

## <a name="azure-cli-create-and-configure-managed-instances"></a>CLI do Azure: criar e configurar instâncias gerenciadas

Para criar e configurar instâncias gerenciadas com [CLI do Azure](/cli/azure), use os seguintes [comandos CLI do Azure para SQL instância gerenciada](/cli/azure/sql/mi). Use [Azure cloud Shell](/azure/cloud-shell/overview) para executar a CLI no seu navegador ou [instalá](/cli/azure/install-azure-cli) -lo no MacOS, Linux ou Windows.

> [!TIP]
> Para ver um início rápido da CLI do Azure, consulte [Trabalhar com a Instância Gerenciada do SQL usando a CLI do Azure](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).

| Cmdlet | Descrição |
| --- | --- |
|[az sql mi create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) |Cria uma instância gerenciada.|
|[az sql mi list](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-list)|Lista as instâncias gerenciadas disponíveis.|
|[az sql mi show](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-show)|Obtém os detalhes de uma instância gerenciada.|
|[az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)|Atualiza uma instância gerenciada.|
|[az sql mi delete](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-delete)|Remove uma instância gerenciada.|
|[az sql midb create](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-create) |Cria um banco de dados gerenciado.|
|[az sql midb list](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-list)|Lista os bancos de dados gerenciados disponíveis.|
|[az sql midb restore](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-restore)|Restaura um banco de dados gerenciado.|
|[az sql midb delete](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-delete)|Remove um banco de dados gerenciado.|

## <a name="transact-sql-create-and-configure-instance-databases"></a>Transact-SQL: criar e configurar bancos de dados de instância

Para criar e configurar bancos de dados de instância após a criação da instância gerenciada, use os comandos T-SQL a seguir. Você pode emitir esses comandos usando o portal do Azure, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is), [Visual Studio Code](https://code.visualstudio.com/docs)ou qualquer outro programa que possa se conectar a um servidor e passar comandos Transact-SQL.

> [!TIP]
> Para obter guias de início rápido mostrando como configurar e conectar-se a uma instância gerenciada usando SQL Server Management Studio no Microsoft Windows, consulte [início rápido: configurar a VM do Azure para se conectar ao Azure sql instância gerenciada](connect-vm-instance-configure.md) e [início rápido: configurar uma conexão ponto a site com o instância gerenciada do SQL do Azure do local](point-to-site-p2s-configure.md).

> [!IMPORTANT]
> Você não pode criar ou excluir uma instância gerenciada usando o Transact-SQL.

| Comando | Descrição |
| --- | --- |
|[CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current)|Cria um novo banco de dados de instância no SQL Instância Gerenciada. Você deve estar conectado ao banco de dados mestre para criar um novo banco de dados.|
| [ALTERAR BANCO DE DADOS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) |Modifica um banco de dados de instância no SQL Instância Gerenciada.|

## <a name="rest-api-create-and-configure-managed-instances"></a>API REST: criar e configurar instâncias gerenciadas

Para criar e configurar instâncias gerenciadas, use essas solicitações da API REST.

| Comando | Descrição |
| --- | --- |
|[Managed Instances - Create Or Update](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)|Cria ou atualiza uma instância gerenciada.|
|[Managed Instances - Delete](https://docs.microsoft.com/rest/api/sql/managedinstances/delete)|Exclui uma instância gerenciada.|
|[Managed Instances - Get](https://docs.microsoft.com/rest/api/sql/managedinstances/get)|Obtém uma instância gerenciada.|
|[Managed Instances - List](https://docs.microsoft.com/rest/api/sql/managedinstances/list)|Retorna uma lista de instâncias gerenciadas em uma assinatura.|
|[Managed Instances - List By Resource Group](https://docs.microsoft.com/rest/api/sql/managedinstances/listbyresourcegroup)|Retorna uma lista de instâncias gerenciadas em um grupo de recursos.|
|[Managed Instances - Update](https://docs.microsoft.com/rest/api/sql/managedinstances/update)|Atualiza uma instância gerenciada.|

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre como migrar um banco de dados do SQL Server para o Azure, confira [Migrar para o Banco de Dados SQL do Azure](../database/migrate-to-database-from-sql-server.md).
- Para obter informações sobre os recursos com suporte, consulte [Recursos](../database/features-comparison.md).
