---
title: Data Warehouse Units (DWUs) in Azure Synapse Analytics (formerly SQL DW)
description: Recommendations on choosing the ideal number of data warehouse units (DWUs) to optimize price and performance, and how to change the number of units.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/22/2019
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 7cd6a037f339f193f63cbe152f0ea9964679c231
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420488"
---
# <a name="data-warehouse-units-dwus"></a>Data Warehouse Units (DWUs)

Recommendations on choosing the ideal number of data warehouse units (DWUs) to optimize price and performance, and how to change the number of units.

## <a name="what-are-data-warehouse-units"></a>What are Data Warehouse Units

A [SQL pool](sql-data-warehouse-overview-what-is.md#sql-analytics-and-sql-pool-in-azure-synapse) represents a collection of analytic resources that are being provisioned when using [SQL Analytics](sql-data-warehouse-overview-what-is.md#sql-analytics-and-sql-pool-in-azure-synapse). Analytic resources are defined as a combination of CPU, memory and IO. These three resources are bundled into units of compute scale called Data Warehouse Units (DWUs). Uma DWU representa uma medida abstrata normalizada de recursos de computação e desempenho. A change to your service level alters the number of DWUs that are available to the system, which in turn adjusts the performance, and the cost, of your system.

For higher performance, you can increase the number of data warehouse units. For less performance, reduce data warehouse units. Os custos de armazenamento e computação são cobrados separadamente, para que as mudanças de unidades de data warehouse não afetem os custos de armazenamento.

Performance for data warehouse units is based on these workload metrics:

- How fast a standard data warehousing query can scan a large number of rows and then perform a complex aggregation. Essa operação tem uso intensivo de E/S e CPU.
- How fast the data warehouse can ingest data from Azure Storage Blobs or Azure Data Lake. Essa operação tem uso intensivo de rede e CPU.
- How fast the [`CREATE TABLE AS SELECT`](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) T-SQL command can copy a table. Essa operação envolve a leitura de dados do armazenamento, distribuindo-os em todos os nós do dispositivo e gravando novamente no armazenamento. Essa operação é de uso intensivo de CPU, rede e E/S.

Aumento de DWUs:

- Altera de maneira linear o desempenho do sistema para verificações, agregações e instruções de CTAS
- Aumenta o número de leitores e gravadores para operações de carregamento do PolyBase
- Aumenta o número máximo de consultas simultâneas e slots de simultaneidade.

## <a name="service-level-objective"></a>Objetivo de nível de serviço

O Objetivo de nível de serviço (SLO) é a configuração de escalabilidade que determina o nível de custo e desempenho do data warehouse. The service levels for Gen2 SQL pool are measured in data warehouse units (DWU), for example DW2000c.

In T-SQL, the SERVICE_OBJECTIVE setting determines the service level for your SQL pool.

```sql
CREATE DATABASE mySQLDW
( EDITION = 'Datawarehouse'
 ,SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

## <a name="capacity-limits"></a>Limites de capacidade

Cada servidor SQL (por exemplo, myserver.database.windows.net) tem uma cota de [Unidade de Transação de Banco de Dados (DTU)](../sql-database/sql-database-what-is-a-dtu.md) que permite um número específico de unidades de depósito de dados. Para mais informações, consulte o [limites de capacidade de gerenciamento de carga de trabalho](sql-data-warehouse-service-capacity-limits.md#workload-management).

## <a name="how-many-data-warehouse-units-do-i-need"></a>How many data warehouse units do I need

O número ideal de unidades de data warehouse depende muito de sua carga de trabalho e da quantidade de dados carregados no sistema.

Etapas para encontrar a melhor DWU para sua carga de trabalho:

1. Comece selecionando um DWU menor.
2. Monitore o desempenho do seu aplicativo à medida que testa as cargas de dados no sistema, observando o número de DWUs selecionadas comparado ao desempenho que você observar.
3. Identifique quaisquer requisitos adicionais para períodos de atividade de pico temporários. Workloads that show significant peaks and troughs in activity may need to be scaled frequently.

SQL Analytics is a scale-out system that can provision vast amounts of compute and query sizeable quantities of data. Para ver seus verdadeiros recursos para dimensionamento, especialmente em DWUs maiores, é recomendável dimensionar o conjunto de dados ao dimensionar para garantir que você tenha dados suficientes para alimentar as CPUs. Para testar o dimensionamento, é recomendável usar pelo menos 1 TB.

> [!NOTE]
>
> O desempenho de consulta só aumentará com mais paralelização se o trabalho puder ser dividido entre nós de computação. Se você achar que o dimensionamento não está alterando o desempenho, será necessário ajustar o design da tabela e/ou de suas consultas. Para obter diretrizes de ajuste de consulta, consulte [Gerenciar consultas de usuário](sql-data-warehouse-overview-manage-user-queries.md).

## <a name="permissions"></a>Permissões

Alterar as unidades de data warehouse exige as permissões descritas em [ALTERAR BANCO DE DADOS](/sql/t-sql/statements/alter-database-transact-sql).

Funções internas para recursos do Azure, como Colaborador do BD SQL e Colaborador do SQL Server, podem alterar as configurações da DWU.

## <a name="view-current-dwu-settings"></a>Exibir configurações atuais de DWU

Para exibir a configuração atual de DWU:

1. Abra o Pesquisador de Objetos do SQL Server no Visual Studio.
2. Conecte-se ao banco de dados mestre associado ao servidor lógico do Banco de Dados SQL.
3. Selecione do modo de exibição de gerenciamento dinâmico sys.database_service_objectives. Veja um exemplo:

```sql
SELECT  db.name [Database]
,       ds.edition [Edition]
,       ds.service_objective [Service Objective]
FROM    sys.database_service_objectives   AS ds
JOIN    sys.databases                     AS db ON ds.database_id = db.database_id
;
```

## <a name="change-data-warehouse-units"></a>Alterar unidades do data warehouse

### <a name="azure-portal"></a>Portal do Azure

To change DWUs:

1. Abra o [Portal do Azure](https://portal.azure.com), abra seu banco de dados e clique em **Escala**.

2. Em **Escala**, mova o controle deslizante para a esquerda ou direita para alterar a configuração de DWU.

3. Clique em **Save** (Salvar). Será exibida uma mensagem de confirmação. Clique em **sim** para confirmar ou em **não** para cancelar.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

To change the DWUs, use the [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) PowerShell cmdlet. The following example sets the service level objective to DW1000c for the database MySQLDW that is hosted on server MyServer.

```Powershell
Set-AzSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000c"
```

Para obter mais informações, consulte [cmdlets do PowerShell para SQL Data Warehouse](sql-data-warehouse-reference-powershell-cmdlets.md)

### <a name="t-sql"></a>T-SQL

With T-SQL you can view the current DWU settings, change the settings, and check the progress.

Para alterar as DWUs:

1. Conecte-se ao banco de dados mestre associado ao seu servidor lógico do Banco de Dados SQL.
2. Use a instrução TSQL [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql). The following example sets the service level objective to DW1000c for the database MySQLDW.

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000c')
;
```

### <a name="rest-apis"></a>APIs REST

Para alterar as DWUs, use a API REST [Criar ou Atualizar Banco de Dados](/rest/api/sql/databases/createorupdate). The following example sets the service level objective to DW1000c for the database MySQLDW, which is hosted on server MyServer. O servidor está em um grupo de recursos do Azure chamado ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000c
    }
}
```

Para obter mais exemplos de API REST, consulte [APIs REST para o SQL Data Warehouse](sql-data-warehouse-manage-compute-rest-api.md).

## <a name="check-status-of-dwu-changes"></a>Verificar o status das alterações de DWU

As alterações de DWU podem levar vários minutos para concluir. Se você estiver dimensionando automaticamente, considere implementar a lógica para assegurar que determinadas operações tenham sido concluídas antes de prosseguir com outra ação.

Verificar o estado do banco de dados por meio de vários pontos de extremidade permitirá que você implemente corretamente a automação. O portal fornece uma notificação após a conclusão de uma operação e o estado atual do bancos de dados, mas não permite a verificação de estado programática.

Não é possível verificar o estado do banco de dados para operações de dimensionamento horizontal com o portal do Azure.

Para verificar o status de alterações de DWU:

1. Conecte-se ao banco de dados mestre associado ao seu servidor lógico do Banco de Dados SQL.

1. Envie a seguinte consulta para verificar o estado do banco de dados.

    ```sql
    SELECT    *
    FROM      sys.databases
    ;
    ```
    
1. Envie a seguinte consulta para verificar o estado da operação

    ```sql
    SELECT    *
    FROM      sys.dm_operation_status
    WHERE     resource_type_desc = 'Database'
    AND       major_resource_id = 'MySQLDW'
    ;
    ```
    
This DMV returns information about various management operations on your SQL pool such as the operation and the state of the operation, which is either IN_PROGRESS or COMPLETED.

## <a name="the-scaling-workflow"></a>O fluxo de trabalho do dimensionamento

When you start a scale operation, the system first kills all open sessions, rolling back any open transactions to ensure a consistent state. Para operações de dimensionamento, o dimensionamento ocorrerá somente após a conclusão dessa reversão de transação.  

- For a scale-up operation, the system detaches all compute nodes, provisions the additional compute nodes, and then reattaches to the storage layer.
- For a scale-down operation, the system detaches all compute nodes and then reattaches only the needed nodes to the storage layer.

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre como gerenciamento do desempenho, consulte [Classes de recursos para gerenciamento de carga de trabalho](resource-classes-for-workload-management.md) e [Limites de simultaneidade e memória](memory-concurrency-limits.md).
