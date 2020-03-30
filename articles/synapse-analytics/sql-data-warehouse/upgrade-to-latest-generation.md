---
title: Atualize para a última geração
description: Atualize o pool SQL do Azure Synapse Analytics para a última geração de arquitetura de hardware e armazenamento do Azure.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/19/2019
ms.author: martinle
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 637e377e469eeb1a82b6c0ad3a845d94ac09c7db
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351206"
---
# <a name="optimize-performance-by-upgrading-azure-synapse-analytics-sql-pool"></a>Otimizar o desempenho atualizando o pool SqL do Azure Synapse Analytics

Atualize o pool SQL para a última geração de arquitetura de hardware e armazenamento do Azure.

## <a name="why-upgrade"></a>Por que atualizar?

Agora você pode atualizar perfeitamente para o nível SQL pool Compute Optimized Gen2 no portal Azure para [regiões suportadas](gen2-migration-schedule.md#automated-schedule-and-region-availability-table). Se a região não der suporte à atualização automática, você poderá atualizar para uma região com suporte ou aguardar que a atualização automática esteja disponível em sua região. Atualize agora para aproveitar a última geração de hardware do Azure e arquitetura de armazenamento aprimorada, incluindo um desempenho mais rápido, escalabilidade mais alta e armazenamento em colunas ilimitado. 

> [!VIDEO https://www.youtube.com/embed/9B2F0gLoyss]

## <a name="applies-to"></a>Aplica-se a

Esta atualização se aplica aos pools SQL de nível Gen1 otimizado da Computação em [regiões suportadas](gen2-migration-schedule.md#automated-schedule-and-region-availability-table).

## <a name="before-you-begin"></a>Antes de começar

1. Verifique se a [região](gen2-migration-schedule.md#automated-schedule-and-region-availability-table) tem suporte para GEN1 para GEN2 para migração. Observe as datas de migração automática. Para evitar conflitos com o processo automatizado, planeje a migração manual antes da data de início do processo automatizado.
2. Se você estiver em uma região que ainda não tenha suporte, continue a verificar sua região a ser adicionada ou [atualize usando restauração](#upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal) para uma região com suporte.
3. Se a região for compatível, [atualize por meio do portal do Azure](#upgrade-in-a-supported-region-using-the-azure-portal)
4. **Selecione o nível de desempenho sugerido** para pool SQL com base no nível de desempenho atual no nível Gen1 otimizado de computação usando o mapeamento abaixo:

   | Camada de Computação Otimizada Gen1 | Camada de Computação Otimizada Gen2 |
   | :-------------------------: | :-------------------------: |
   |            DW100            |           DW100c            |
   |            DW200            |           DW200c            |
   |            DW300            |           DW300c            |
   |            DW400            |           DW400c            |
   |            DW500            |           DW500c            |
   |            DW600            |           DW500c            |
   |           DW1000            |           DW1000c           |
   |           DW1200            |           DW1000c           |
   |           DW1500            |           DW1500c           |
   |           DW2000            |           DW2000c           |
   |           DW3000            |           DW3000c           |
   |           DW6000            |           DW6000c           |

> [!Note]
> Níveis de desempenho sugeridos não são uma conversão direta. Por exemplo, é recomendável ele irem de DW600 a DW500c.

## <a name="upgrade-in-a-supported-region-using-the-azure-portal"></a>Atualizar em uma região com suporte usando o portal do Azure

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!NOTE]
> A migração da Gen1 para a Gen2 através do portal Azure é permanente. Não há um processo para retornar ao Gen1.  

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Faça login no [portal Azure](https://portal.azure.com/).

1. Se o pool SQL de nível Gen1 otimizado de computação a ser atualizado for pausado, [retome o pool SQL](pause-and-resume-compute-portal.md).

   > [!NOTE]
   > O pool SQL deve estar sendo executado para migrar para gen2.

2. Esteja preparado para alguns minutos de tempo de inatividade. 

3. Identifique quaisquer referências de código para os níveis de desempenho de Computação Otimizada Gen1 e modifique-os para o nível de desempenho de Computação Otimizada Gen2. Abaixo, são apresentados dois exemplos para atualizar as referências de código antes da atualização:

   Comando do PowerShell Gen1 original:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
   ```

   Modificado para:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300c"
   ```

   > [!NOTE] 
   > -RequestedServiceObjectiveName "DW300" alterado para - RequestedServiceObjectiveName "DW300**c**"
   >

   Comando T-SQL Gen1 original:

   ```SQL
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300') ;
   ```

   Modificado para:

   ```sql
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300c') ; 
   ```
   > [!NOTE] 
   > SERVICE_OBJECTIVE = 'DW300' alterador para SERVICE_OBJECTIVE = 'DW300**c**'

## <a name="start-the-upgrade"></a>Inicie a atualização

1. Vá para o seu pool De computação Otimizada Gen1 SQL no portal Azure. Se o pool SQL de nível Gen1 otimizado de computação a ser atualizado for pausado, [retome o pool SQL](pause-and-resume-compute-portal.md). 
2. Selecione Atualizar para o cartão ![ **Gen2** na guia Tarefas: Upgrade_1](./media/upgrade-to-latest-generation/upgrade-to-gen2-1.png)
    
    > [!NOTE]
    > Se você não vir o cartão **Fazer upgrade para Gen2** na guia Tarefas, seu tipo de assinatura será limitado na região atual.
    > [Envie um tíquete de suporte](sql-data-warehouse-get-started-create-support-ticket.md) para obter a lista de permissões de assinatura.

3. Certifique-se de que sua carga de trabalho teve sua execução concluída e foi confirmada antes de atualizar. Você experimentará o tempo de inatividade por alguns minutos antes que seu pool SQL esteja de volta on-line como um pool SQL de nível Gen2 otimizado de computação. **Selecione Atualizar**:

   ![Upgrade_2](./media/upgrade-to-latest-generation/upgrade-to-gen2-2.png)

4. **Monitore a atualização** verificando o status no Portal do Azure:

   ![Upgrade3](./media/upgrade-to-latest-generation/upgrade-to-gen2-3.png)

   A primeira etapa do processo de atualização passa pela operação de expansão ("Atualização - Offline") em que todas as sessões serão eliminadas e as conexões serão removidas. 

   A segunda etapa do processo de atualização é a migração de dados ("Atualização - Online"). A migração de dados é um processo lento online em segundo plano. Esse processo lentamente move dados de colunas da arquitetura de armazenamento antiga para a nova arquitetura de armazenamento usando um cache SSD local. Durante esse tempo, seu pool SQL estará on-line para consulta e carregamento. Seus dados estarão disponíveis para consulta, independentemente de se forem migrados ou não. A migração de dados ocorre a uma taxa que varia dependendo do tamanho dos seus dados, do nível de desempenho e do número de segmentos do columnstore. 

5. **Recomendação opcional:** Uma vez que a operação de dimensionamento esteja concluída, você pode acelerar o processo de fundo de migração de dados. Você pode forçar a movimentação de dados executando [Alter Index rebuild](sql-data-warehouse-tables-index.md) em todas as tabelas columnstore principais que você estaria consultando em uma classe maior de SLO e recursos. Essa operação é **offline** em comparação ao processo em segundo plano lento, que pode levar horas para ser concluído, dependendo do número e dos tamanhos de suas tabelas. No entanto, uma vez concluída, a migração de dados será muito mais rápida devido à nova arquitetura de armazenamento aprimorada com rowgroups de alta qualidade.
 
> [!NOTE]
> Alter Index rebuild é uma operação offline e as tabelas não estarão disponíveis até que a recompilação esteja concluída.

A consulta a seguir gera os comandos necessários Alter Index Rebuild para acelerar a migração de dados:

```sql
SELECT 'ALTER INDEX [' + idx.NAME + '] ON [' 
       + Schema_name(tbl.schema_id) + '].[' 
       + Object_name(idx.object_id) + '] REBUILD ' + ( CASE 
                                                         WHEN ( 
                                                     (SELECT Count(*) 
                                                      FROM   sys.partitions 
                                                             part2 
                                                      WHERE  part2.index_id 
                                                             = idx.index_id 
                                                             AND 
                                                     idx.object_id = 
                                                     part2.object_id) 
                                                     > 1 ) THEN 
              ' PARTITION = ' 
              + Cast(part.partition_number AS NVARCHAR(256)) 
              ELSE '' 
                                                       END ) + '; SELECT ''[' + 
              idx.NAME + '] ON [' + Schema_name(tbl.schema_id) + '].[' + 
              Object_name(idx.object_id) + '] ' + ( 
              CASE 
                WHEN ( (SELECT Count(*) 
                        FROM   sys.partitions 
                               part2 
                        WHERE 
                     part2.index_id = 
                     idx.index_id 
                     AND idx.object_id 
                         = part2.object_id) > 1 ) THEN 
              ' PARTITION = ' 
              + Cast(part.partition_number AS NVARCHAR(256)) 
              + ' completed'';' 
              ELSE ' completed'';' 
                                                    END ) 
FROM   sys.indexes idx 
       INNER JOIN sys.tables tbl 
               ON idx.object_id = tbl.object_id 
       LEFT OUTER JOIN sys.partitions part 
                    ON idx.index_id = part.index_id 
                       AND idx.object_id = part.object_id 
WHERE  idx.type_desc = 'CLUSTERED COLUMNSTORE'; 
```

## <a name="upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal"></a>Atualize de uma região geográfica do Azure usando a restauração por meio do portal do Azure

## <a name="create-a-user-defined-restore-point-using-the-azure-portal"></a>Criar um ponto de restauração definido pelo usuário usando o portal do Azure

1. Faça login no [portal Azure](https://portal.azure.com/).

2. Navegue até o pool SQL para o que você deseja criar um ponto de restauração.

3. Na parte superior da seção Visão Geral, selecione **+Novo Ponto de Restauração**.

    ![Novo Ponto de Restauração](./media/upgrade-to-latest-generation/creating_restore_point_0.png)

4. Especifique um nome para seu ponto de restauração.

    ![Nome do ponto de restauração](./media/upgrade-to-latest-generation/creating_restore_point_1.png)

## <a name="restore-an-active-or-paused-database-using-the-azure-portal"></a>Restaurar um banco de dados ativo ou pausado usando o portal do Azure

1. Faça login no [portal Azure](https://portal.azure.com/).
2. Navegue até o pool SQL do que deseja restaurar.
3. Na parte superior da seção Visão Geral, selecione **Restaurar**.

    ![ Visão Geral de Restauração](./media/upgrade-to-latest-generation/restoring_0.png)

4. Selecione **pontos de restauração automáticos** ou **pontos de restauração definidos pelo usuário**. Para pontos de restauração definidos pelo usuário, **selecione um ponto de restauração definido pelo usuário** ou **Crie um novo ponto de restauração definido pelo usuário**. Para o servidor, selecione **Criar novo** e escolha um servidor em uma região geográfica suportada pelo Gen2. 

    ![Pontos de restauração automática](./media/upgrade-to-latest-generation/restoring_1.png)

## <a name="restore-from-an-azure-geographical-region-using-powershell"></a>Restaurar um banco de dados excluído usando o PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para recuperar um banco de dados, use o [cmdlet Restore-AzSqlDatabase.](/powershell/module/az.sql/restore-azsqldatabase)

> [!NOTE]
> Você pode executar uma restauração geográfica para Gen2! Para fazer isso, especifique um ServiceObjectiveName de Gen2 (por exemplo, DW1000**c**) como parâmetro opcional.

1. Abra o Windows PowerShell.
2. Conecte-se à sua conta do Azure e liste todas as assinaturas associadas à sua conta.
3. Selecione a assinatura que contém o banco de dados a ser restaurado.
4. Obtenha o banco de dados que você deseja recuperar.
5. Crie a solicitação de recuperação para o banco de dados, especificando um ServiceObjectiveName Gen2.
6. Verifique o status do banco de dados com restauração geográfica.

```Powershell
Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName "<YourTargetServiceLevel>" -RequestedServiceObjectiveName "DW300c"

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

> [!NOTE]
> Para configurar o banco de dados após a conclusão da restauração, consulte [Configurar o banco de dados após a recuperação](../../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).

O banco de dados recuperado será habilitado para TDE se o banco de dados de origem for habilitado para TDE.


Se você tiver algum problema com seu pool SQL, crie uma [solicitação de suporte](sql-data-warehouse-get-started-create-support-ticket.md) e faça referência a "upgrade gen2" como a possível causa.

## <a name="next-steps"></a>Próximas etapas

Seu pool SQL atualizado está online. Para aproveitar a arquitetura avançada, consulte [Classes de recursos para gerenciamento de carga de trabalho](resource-classes-for-workload-management.md).
