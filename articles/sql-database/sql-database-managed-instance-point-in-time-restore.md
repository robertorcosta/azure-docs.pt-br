---
title: Instância gerenciada - Restauração point-in-time (PITR)
description: Restaurar um banco de dados SQL em uma instância gerenciada até um ponto anterior no tempo.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, mathoma
ms.date: 08/25/2019
ms.openlocfilehash: 27f465e6864d0ff639e825c8a816d86648bd8853
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268802"
---
# <a name="restore-a-sql-database-in-a-managed-instance-to-a-previous-point-in-time"></a>Restaurar um banco de dados SQL em uma instância gerenciada a um ponto anterior

Use o PITR (Point-in-Time Restore, restauração point-in-time) para criar um banco de dados como uma cópia de outro banco de dados de algum tempo no passado. Este artigo descreve como fazer uma restauração point-in-time de um banco de dados em uma instância gerenciada do Azure SQL Database.

A restauração point-in-time é útil em cenários de recuperação, como incidentes causados por erros, dados carregados incorretamente ou exclusão de dados cruciais. Você também pode usá-lo simplesmente para testar ou auditar. Os arquivos de backup são mantidos por 7 a 35 dias, dependendo das configurações do banco de dados.

A restauração point-in-time pode restaurar um banco de dados:

- de um banco de dados existente.
- de um banco de dados excluído.
- para a mesma instância gerenciada, ou para outra instância gerenciada. 

## <a name="limitations"></a>Limitações

A restauração pontual de uma instância gerenciada tem as seguintes limitações:

- Quando você está restaurando de uma instância gerenciada para outra, ambas as instâncias devem estar na mesma assinatura e região. A restauração entre região e assinatura cruzada não é suportada no momento.
- A restauração point-in-time de uma instância gerenciada não é possível. Este artigo explica apenas o que é possível: restauração point-in-time de um banco de dados hospedado em uma instância gerenciada.

> [!WARNING]
> Esteja ciente do tamanho do armazenamento da sua instância gerenciada. Dependendo do tamanho dos dados a serem restaurados, você pode ficar sem armazenamento de instâncias. Se não houver espaço suficiente para os dados restaurados, use uma abordagem diferente.

A tabela a seguir mostra cenários de restauração point-in-time para instâncias gerenciadas:

|           |Restaurar db existente para a mesma instância gerenciada| Restaurar db existente para outra instância gerenciada|Restaurar db caiu para a mesma instância gerenciada|Restaurar db caiu para outra instância gerenciada|
|:----------|:----------|:----------|:----------|:----------|
|**Portal Azure**| Sim|Não |Sim|Não|
|**Azure CLI**|Sim |Sim |Não|Não|
|**Powershell**| Sim|Sim |Sim|Sim|

## <a name="restore-an-existing-database"></a>Restaurar um banco de dados existente

Restaure um banco de dados existente na mesma instância usando o portal Azure, PowerShell ou o Azure CLI. Para restaurar um banco de dados em outra instância, use o PowerShell ou o Azure CLI para que você possa especificar as propriedades para a instância gerenciada de destino e grupo de recursos. Se você não especificar esses parâmetros, o banco de dados será restaurado à instância existente por padrão. O portal Azure não suporta atualmente a restauração para outra instância.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Faça login no [portal Azure](https://portal.azure.com). 
2. Vá para a sua instância gerenciada e selecione o banco de dados que deseja restaurar.
3. Selecione **Restaurar** na página do banco de dados:

    ![Restaurar um banco de dados usando o portal Azure](media/sql-database-managed-instance-point-in-time-restore/restore-database-to-mi.png)

4. Na página **Restaurar,** selecione o ponto para a data e a hora para que deseja restaurar o banco de dados.
5. Selecione **Confirmar** para restaurar seu banco de dados. Essa ação inicia o processo de restauração, que cria um novo banco de dados e o preenche com dados do banco de dados original no ponto especificado no tempo. Para obter mais informações sobre o processo de recuperação, consulte [o tempo de recuperação](sql-database-recovery-using-backups.md#recovery-time).

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Se você ainda não tiver o Azure PowerShell instalado, consulte [Instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Para restaurar o banco de dados usando o PowerShell, especifique seus valores para os parâmetros no comando a seguir. Em seguida, execute o comando:

```powershell-interactive
$subscriptionId = "<Subscription ID>"
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source-database>"
$pointInTime = "2018-06-27T08:51:39.3882806Z"
$targetDatabase = "<Name of new database to be created>"

Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
```

Para restaurar o banco de dados em outra instância gerenciada, também especifique os nomes do grupo de recursos de destino e a instância gerenciada de destino:  

```powershell-interactive
$targetResourceGroupName = "<Resource group of target managed instance>"
$targetInstanceName = "<Target managed instance name>"

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
                              -TargetResourceGroupName $targetResourceGroupName `
                              -TargetInstanceName $targetInstanceName 
```

Para obter detalhes, consulte [Restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Se você ainda não tiver o Azure CLI instalado, consulte [Instalar o Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

Para restaurar o banco de dados usando o Azure CLI, especifique seus valores para os parâmetros no comando a seguir. Em seguida, execute o comando:

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```

Para restaurar o banco de dados em outra instância gerenciada, também especifique os nomes do grupo de recursos de destino e a instância gerenciada:  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

Para obter uma explicação detalhada dos parâmetros disponíveis, consulte a [documentação cli para restaurar um banco](https://docs.microsoft.com/cli/azure/sql/midb?view=azure-cli-latest#az-sql-midb-restore)de dados em uma instância gerenciada .

---

## <a name="restore-a-deleted-database"></a>Restaurar um banco de dados excluído

Restaurar um banco de dados excluído pode ser feito usando o portal PowerShell ou Azure. Para restaurar um banco de dados excluído na mesma instância, use o portal Azure ou o PowerShell. Para restaurar um banco de dados excluído em outra instância, use o PowerShell. 

### <a name="portal"></a>Portal 


Para recuperar um banco de dados gerenciado usando o portal Azure, abra a página de visão geral da instância gerenciada e selecione **Bancos de dados Excluídos**. Escolha um banco de dados excluído que deseja restaurar e digite o nome para o novo banco de dados que será criado com dados restaurados a partir do backup.

  ![Captura de tela do banco de dados de instância sql do Azure excluído](./media/sql-database-recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="powershell"></a>PowerShell

Para restaurar um banco de dados na mesma instância, atualize os valores dos parâmetros e execute o seguinte comando PowerShell: 

```powershell-interactive
$subscriptionId = "<Subscription ID>"
Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$deletedDatabaseName = "<Source database name>"
$targetDatabaseName = "<target database name>"

$deletedDatabase = Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName $resourceGroupName `
-InstanceName $managedInstanceName -DatabaseName $deletedDatabaseName

Restore-AzSqlinstanceDatabase -Name $deletedDatabase.Name `
   -InstanceName $deletedDatabase.ManagedInstanceName `
   -ResourceGroupName $deletedDatabase.ResourceGroupName `
   -DeletionDate $deletedDatabase.DeletionDate `
   -PointInTime UTCDateTime `
   -TargetInstanceDatabaseName $targetDatabaseName
```

Para restaurar o banco de dados em outra instância gerenciada, também especifique os nomes do grupo de recursos de destino e a instância gerenciada de destino:

```powershell-interactive
$targetResourceGroupName = "<Resource group of target managed instance>"
$targetInstanceName = "<Target managed instance name>"

Restore-AzSqlinstanceDatabase -Name $deletedDatabase.Name `
   -InstanceName $deletedDatabase.ManagedInstanceName `
   -ResourceGroupName $deletedDatabase.ResourceGroupName `
   -DeletionDate $deletedDatabase.DeletionDate `
   -PointInTime UTCDateTime `
   -TargetInstanceDatabaseName $targetDatabaseName `
   -TargetResourceGroupName $targetResourceGroupName `
   -TargetInstanceName $targetInstanceName 
```

## <a name="overwrite-an-existing-database"></a>Sobrepor um banco de dados existente

Para substituir um banco de dados existente, você deve:

1. Solte o banco de dados existente que você deseja substituir.
2. Renomeie o banco de dados point-in-time restaurado para o nome do banco de dados que você deixou cair.

### <a name="drop-the-original-database"></a>Solte o banco de dados original

Você pode soltar o banco de dados usando o portal Azure, PowerShell ou o Azure CLI.

Você também pode soltar o banco de dados conectando-se diretamente à instância gerenciada, iniciando o SQL Server Management Studio (SSMS) e, em seguida, executando o seguinte comando Transact-SQL (T-SQL):

```sql
DROP DATABASE WorldWideImporters;
```

Use um dos seguintes métodos para se conectar ao seu banco de dados na instância gerenciada:

- [SSMS/Azure Data Studio através de uma máquina virtual Do Zure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Ponto a site](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Ponto de extremidade público](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

# <a name="portal"></a>[Portal](#tab/azure-portal)

No portal Azure, selecione o banco de dados na instância gerenciada e, em seguida, **selecione Excluir**.

   ![Exclua um banco de dados usando o portal Azure](media/sql-database-managed-instance-point-in-time-restore/delete-database-from-mi.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Use o seguinte comando PowerShell para soltar um banco de dados existente de uma instância gerenciada:

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Use o seguinte comando Azure CLI para soltar um banco de dados existente de uma instância gerenciada:

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---

### <a name="alter-the-new-database-name-to-match-the-original-database-name"></a>Altere o novo nome do banco de dados para corresponder ao nome original do banco de dados

Conecte-se diretamente à instância gerenciada e inicie o SQL Server Management Studio. Em seguida, execute a seguinte consulta Transact-SQL (T-SQL). A consulta mudará o nome do banco de dados restaurado para o do banco de dados descartado que você pretende substituir.

```sql
ALTER DATABASE WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```

Use um dos seguintes métodos para se conectar ao seu banco de dados na instância gerenciada:

- [Máquina virtual azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Ponto a site](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Ponto de extremidade público](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [backups automatizados](sql-database-automated-backups.md).
