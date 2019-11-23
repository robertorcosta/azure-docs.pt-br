---
title: Read queries on replicas
description: The Azure SQL Database provides the ability to load-balance read-only workloads using the capacity of read-only replicas - called Read Scale-Out.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
ms.date: 06/03/2019
ms.openlocfilehash: f111b19eb07c218a9f3250ef3ffdb8a97cf07542
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420718"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>Use read-only replicas to load-balance read-only query workloads

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

As part of the [High Availability architecture](./sql-database-high-availability.md#premium-and-business-critical-service-tier-availability), each database in the Premium and Business Critical service tier is automatically provisioned with a primary replica and several secondary replicas. The secondary replicas are provisioned with the same compute size as the primary replica. The **Read Scale-Out** feature allows you to load-balance SQL Database read-only workloads using the capacity of one of the read-only replicas instead of sharing the read-write replica. Dessa forma, a carga de trabalho somente leitura serão isoladas da carga de trabalho principal de leitura/gravação e não afetarão o desempenho. The feature is intended for the applications that include logically separated read-only workloads, such as analytics. In the Premium and Business Critical service tiers, applications could gain performance benefits using this additional capacity at no extra cost.

The **Read Scale-Out** feature is also available in the Hyperscale service tier when at least one secondary replica is created. Multiple secondary replicas can be used if read-only workloads require more resources than available on one secondary replica. The High Availability architecture of Basic, Standard, and General Purpose service tiers does not include any replicas. The **Read Scale-Out** feature is not available in these service tiers.

The following diagram illustrates it using a Business Critical database.

![Réplicas somente leitura](media/sql-database-read-scale-out/business-critical-service-tier-read-scale-out.png)

The Read Scale-Out feature is enabled by default on new Premium,  Business Critical, and Hyperscale databases. For Hyperscale, one secondary replica is created by default for new databases. If your SQL connection string is configured with `ApplicationIntent=ReadOnly`, the application will be redirected by the gateway to a read-only replica of that database. For information on how to use the `ApplicationIntent` property, see [Specifying Application Intent](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

If you wish to ensure that the application connects to the primary replica regardless of the `ApplicationIntent` setting in the SQL connection string, you must explicitly disable read scale-out when creating the database or when altering its configuration. For example, if you upgrade your database from Standard or General Purpose tier to Premium, Business Critical or Hyperscale tier and want to make sure all your connections continue to go to the primary replica, disable Read Scale-out. For details on how to disable it, see [Enable and disable Read Scale-Out](#enable-and-disable-read-scale-out).

> [!NOTE]
> Query Data Store, Extended Events, SQL Profiler and Audit features are not supported on the read-only replicas.

## <a name="data-consistency"></a>Consistência de dados

Um dos benefícios das réplicas é que as réplicas estão sempre no estado transacionalmente consistente, mas em diferentes momentos, pode haver uma pequena latência entre diferentes réplicas. A Expansão de leitura oferece suporte a consistência em nível de sessão. It means, if the read-only session reconnects after a connection error caused by replica unavailability, it may be redirected to a replica that is not 100% up-to-date with the read-write replica. Likewise, if an application writes data using a read-write session and immediately reads it using a read-only session, it is possible that the latest updates are not immediately visible on the replica. The latency is caused by an asynchronous transaction log redo operation.

> [!NOTE]
> As latências de replicação na região são baixas e essa situação é rara.

## <a name="connect-to-a-read-only-replica"></a>Conectar-se a uma réplica somente leitura

Quando você habilita a expansão de leitura para um banco de dados, a opção `ApplicationIntent` na cadeia de conexão fornecida pelo cliente determina se a conexão é roteada para a réplica de gravação ou para uma réplica somente leitura. Especificamente, se o valor `ApplicationIntent` é `ReadWrite` (o valor padrão), a conexão será direcionada para a réplica de leitura-gravação do banco de dados. Isso é idêntico ao comportamento existente. Se o valor de `ApplicationIntent` é `ReadOnly`, a conexão é roteada para uma réplica somente leitura.

Por exemplo, a seguinte cadeia de conexão conecta o cliente a uma réplica somente leitura (substituindo os itens nos chevrons pelos valores corretos para o seu ambiente e descartando os chevrons):

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Qualquer uma das seguintes cadeias de conexão conecta o cliente a uma réplica de leitura-gravação (substituindo os itens nos chevrons pelos valores corretos para o seu ambiente e descartando os chevrons):

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="verify-that-a-connection-is-to-a-read-only-replica"></a>Verifique se uma conexão está pronta para réplica somente leitura

Você pode verificar se você está conectado a uma réplica somente leitura ao executar a consulta a seguir. Ela retornará READ_ONLY quando conectado a uma réplica somente leitura.

```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability')
```

> [!NOTE]
> Em um determinado momento qualquer apenas uma das réplicas de AlwaysON fica acessível por sessões somente leitura.

## <a name="monitoring-and-troubleshooting-read-only-replica"></a>Monitoring and troubleshooting read-only replica

When connected to a read-only replica, you can access the performance metrics using the `sys.dm_db_resource_stats` DMV. To access query plan statistics, use the `sys.dm_exec_query_stats`, `sys.dm_exec_query_plan` and `sys.dm_exec_sql_text` DMVs.

> [!NOTE]
> The DMV `sys.resource_stats` in the logical master database returns CPU usage and storage data of the primary replica.

## <a name="enable-and-disable-read-scale-out"></a>Ativar e desativar o Escalonamento de Leitura

Read Scale-Out is enabled by default on Premium, Business Critical and Hyperscale service tiers. Read Scale-Out cannot be enabled in Basic, Standard, or General Purpose service tiers. Read Scale-Out is automatically disabled on Hyperscale databases configured with 0 replicas.

You can disable and re-enable Read Scale-Out on single databases and elastic pool databases in Premium or Business Critical service tier using the following methods.

> [!NOTE]
> The ability to disable Read Scale-Out is provided for backward compatibility.

### <a name="azure-portal"></a>Portal do Azure

You can manage the Read Scale-out setting on the **Configure** database blade.

### <a name="powershell"></a>PowerShell

> [!IMPORTANT]
> The PowerShell Azure Resource Manager (RM) module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. The AzureRM module will continue to receive bug fixes until at least December 2020.  The arguments for the commands in the Az module and in the AzureRm modules are substantially identical. For more about their compatibility, see [Introducing the new Azure PowerShell Az module](/powershell/azure/new-azureps-module-az).

Gerenciar a Expansão de leitura no Microsoft Azure PowerShell requer a versão de dezembro de 2016 do Microsoft Azure PowerShell ou mais recente. Para a versão mais recente do PowerShell, consulte [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

You can disable or re-enable Read Scale-Out in Azure PowerShell by invoking the [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) cmdlet and passing in the desired value – `Enabled` or `Disabled` -- for the `-ReadScale` parameter.

To disable read scale-out on an existing database (replacing the items in the angle brackets with the correct values for your environment and dropping the angle brackets):

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled
```

To disable read scale-out on a new database (replacing the items in the angle brackets with the correct values for your environment and dropping the angle brackets):

```powershell
New-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled -Edition Premium
```

To re-enable read scale-out on an existing database (replacing the items in the angle brackets with the correct values for your environment and dropping the angle brackets):

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Enabled
```

### <a name="rest-api"></a>API REST

To create a database with read scale-out disabled, or to change the setting for an existing database, use the following method with the `readScale` property set to `Enabled` or `Disabled` as in the below sample request.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body: {
   "properties": {
      "readScale":"Disabled"
   }
}
```

Para obter mais informações, consulte [Bancos de dados - Criar ou Atualizar](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

## <a name="using-tempdb-on-read-only-replica"></a>Using TempDB on read-only replica

The TempDB database is not replicated to the read-only replicas. Each replica has its own version of TempDB database that is created when the replica is created. It ensures that TempDB is updateable and can be modified during your query execution. If your read-only workload depends on using TempDB objects, you should create these objects as part of your query script.

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Usar escala de leitura com bancos de dados replicados geograficamente

If you are using Read Scale-Out to load-balance read-only workloads on a database that is geo-replicated (for example, as a member of a failover group), make sure that read scale-out is enabled on both the primary and the geo-replicated secondary databases. This configuration will ensure that the same load-balancing experience continues when your application connects to the new primary after failover. Se estiver se conectando ao banco de dados secundário geograficamente replicado com escala de leitura habilitada, suas sessões com `ApplicationIntent=ReadOnly` serão roteadas para uma das réplicas da mesma forma que roteamos conexões no banco de dados primário.  As sessões sem `ApplicationIntent=ReadOnly` serão roteadas para a réplica primária do secundário replicado geograficamente, que também é somente leitura. Because geo-replicated secondary database has a different endpoint than the primary database, historically to access the secondary it wasn't required to set `ApplicationIntent=ReadOnly`. Para garantir a compatibilidade com versões anteriores, o `sys.geo_replication_links` DMV mostra `secondary_allow_connections=2` (qualquer conexão de cliente é permitida).

> [!NOTE]
> Round-robin or any other load-balanced routing between the local replicas of the secondary database is not supported.

## <a name="next-steps"></a>Próximos passos

- For information about SQL Database Hyperscale offering, see [Hyperscale service tier](./sql-database-service-tier-hyperscale.md).
