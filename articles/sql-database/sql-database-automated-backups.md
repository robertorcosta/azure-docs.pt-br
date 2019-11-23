---
title: automatic, geo-redundant backups
description: O Banco de dados SQL cria automaticamente um backup de banco de dados local a cada poucos minutos e usa o armazenamento com redundância geográfica de acesso de leitura do Azure para redundância geográfica.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
manager: craigg
ms.date: 09/26/2019
ms.openlocfilehash: 5d5a2b9527dcb53b0315ac5fd964c6f30961f3e7
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74405754"
---
# <a name="automated-backups"></a>Backups automatizados

SQL Database automatically creates the database backups that are kept between 7 and 35 days, and uses Azure [read-access geo-redundant storage (RA-GRS)](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) to ensure that they are preserved even if the data center is unavailable. These backups are created automatically. Os backups de banco de dados são uma parte essencial de qualquer estratégia de recuperação de desastre e continuidade dos negócios, porque eles protegem seus dados contra exclusão ou corrupção acidentais. If your security rules require that your backups are available for an extended period of time (up to 10 years), you can configure a [long-term retention](sql-database-long-term-retention.md) on Singleton databases and Elastic pools.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>O que é um backup de banco de dados SQL

SQL Database uses SQL Server technology to create [full backups](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) every week, [differential backups](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) every 12 hours, and [transaction log backups](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) every 5-10 minutes. The backups are stored in [RA-GRS storage blobs](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) that are replicated to a [paired data center](../best-practices-availability-paired-regions.md) for protection against a data center outage. Quando você restaura um banco de dados, o serviço descobre quais backups completos, diferenciais e de log de transações precisam ser restaurados.

Use esses backups para:

- **Restore an existing database to a point-in-time in the past** within the retention period using the Azure portal, Azure PowerShell, Azure CLI, or REST API. In Single database and Elastic pools, this operation will create a new database in the same server as the original database. In Managed Instance, this operation can create a copy of the database or same or different Managed Instance under the same subscription.
  - **[Change Backup Retention Period](#how-to-change-the-pitr-backup-retention-period)** between 7 to 35 days to configure your backup policy.
  - **Change long-term retention policy up to 10 years** on Single Database and Elastic Pools using [the Azure portal](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies) or [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#use-powershell-to-manage-long-term-backups).
- **Restore a deleted database to the time it was deleted** or anytime within the retention period. The deleted database can only be restored in the same logical server or Managed Instance where the original database was created.
- **Restore a database to another geographical region**. A restauração geográfica permite a recuperação de um desastre geográfico quando você não consegue acessar o servidor nem o banco de dados. Ele cria um novo banco de dados em qualquer servidor existente do mundo.
- **Restore a database from a specific long-term backup** on Single Database or Elastic Pool if the database has been configured with a long-term retention policy (LTR). LTR allows you to restore an old version of the database using [the Azure portal](sql-database-long-term-backup-retention-configure.md#view-backups-and-restore-from-a-backup-using-azure-portal) or [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#use-powershell-to-manage-long-term-backups) to satisfy a compliance request or to run an old version of the application. Para obter mais informações, consulte [Retenção de longo prazo](sql-database-long-term-retention.md).
- Para executar uma restauração, consulte [Restaurar um banco de dados de backups](sql-database-recovery-using-backups.md).

> [!NOTE]
> No armazenamento do Azure, o termo *replicação* refere-se a copiar arquivos de uma localização para outra. *Replicação de banco de dados* do SQL significa manter vários bancos de dados secundários sincronizados com um banco de dados primário.

You can try some of these operations using the following examples:

| | O Portal do Azure | Azure PowerShell |
|---|---|---|
| Change backup retention | [Single Database](sql-database-automated-backups.md?tabs=managed-instance#change-pitr-backup-retention-period-using-azure-portal) <br/> [Instância Gerenciada](sql-database-automated-backups.md?tabs=managed-instance#change-pitr-backup-retention-period-using-azure-portal) | [Single Database](sql-database-automated-backups.md#change-pitr-backup-retention-period-using-powershell) <br/>[Instância Gerenciada](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| Change Long-term backup retention | [Banco de dados individual](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Managed Instance - N/A  | [Single Database](sql-database-long-term-backup-retention-configure.md#use-powershell-to-manage-long-term-backups)<br/>Managed Instance - N/A  |
| Restore database from point-in-time | [Banco de dados individual](sql-database-recovery-using-backups.md#point-in-time-restore) | [Banco de dados individual](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Instância Gerenciada](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| Restaurar banco de dados excluído | [Banco de dados individual](sql-database-recovery-using-backups.md) | [Banco de dados individual](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Instância Gerenciada](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Restore database from Azure Blob Storage | Single database - N/A <br/>Managed Instance - N/A  | Single database - N/A <br/>[Instância Gerenciada](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |

## <a name="how-long-are-backups-kept"></a>Quanto tempo são backups mantidos

All Azure SQL databases (single, pooled, and managed instance databases) have a default backup retention period of  **seven** days. You can [change backup retention period up to 35 days](#how-to-change-the-pitr-backup-retention-period).

Se você excluir um banco de dados, o Banco de Dados SQL manterá os backups da mesma maneira que em um banco de dados online. Por exemplo, se você excluir um banco de dados Básico que tenha um período de retenção de sete dias, um backup de quatro dias será salvo por mais três dias.

Se você precisar manter os backups por mais tempo que o período máximo de retenção, modifique as propriedades de backup para adicionar um ou mais períodos de retenção de longo prazo para o banco de dados. Para obter mais informações, consulte [Retenção de longo prazo](sql-database-long-term-retention.md).

> [!IMPORTANT]
> Se você excluir o SQL Server do Azure que hospeda os bancos de dados SQL, todos os pools elásticos e bancos de dados que pertencem ao servidor também serão excluídos e não poderão ser recuperados. Você não pode restaurar um servidor excluído. Mas, se você tiver configurado a retenção de longo prazo, os backups dos bancos de dados com LTR não serão excluídos e esses bancos de dados poderão ser restaurados.

## <a name="how-often-do-backups-happen"></a>A frequência com que os backups ocorrem

### <a name="backups-for-point-in-time-restore"></a>Backups para a Recuperação Pontual

O Banco de Dados SQL permite o autoatendimento para PITR (Recuperação Pontual) ao criar automaticamente o backup completo, backups diferenciais e backups de log de transações. Os backups completos de banco de dados são criados semanalmente, os backups diferenciais de banco de dados geralmente são criados a cada 12 horas e os backups de log de transações geralmente são criados a cada 5 a 10 minutos, com a frequência baseada no tamanho da computação e na quantidade de atividade do banco de dados. O primeiro backup completo é agendado imediatamente após a criação de um banco de dados. Normalmente ele é concluído em 30 minutos, mas pode levar mais tempo quando o banco de dados tem um tamanho significativo. Por exemplo, o backup inicial pode levar mais tempo para um banco de dados restaurado ou uma cópia do banco de dados. Após o primeiro backup completo, todos os outros backups são agendados automaticamente e gerenciados de forma silenciosa em segundo plano. O tempo exato de todos os backups de banco de dados é determinado pelo serviço do Banco de Dados SQL, pois ele equilibra a carga de trabalho geral do sistema. You cannot change or disable the backup jobs. 

Os backups de PITR têm redundância geográfica e são protegidos pela [Replicação inter-regional do Armazenamento do Azure](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)

Para obter mais informações, consulte [Restauração pontual](sql-database-recovery-using-backups.md#point-in-time-restore)

### <a name="backups-for-long-term-retention"></a>Backups de retenção de longo prazo

Bancos de dados individuais e em pool oferecem a opção de configurar LTR (retenção de longo prazo) de backups completos por até 10 anos no Armazenamento de Blobs do Azure. Quando a política de LTR está habilitada, os backups completos semanais são copiados automaticamente para um contêiner de armazenamento de RA-GRS diferente. Para atender a diferentes requisitos de conformidade, é possível selecionar diferentes períodos de retenção para backups semanais, mensais e/ou anuais. O consumo do armazenamento depende da frequência selecionada para os backups e dos períodos de retenção. Você pode usar a [Calculadora de preços de LTR](https://azure.microsoft.com/pricing/calculator/?service=sql-database) para estimar o custo do armazenamento de LTR.

Como o PITR, os backups de LTR têm redundância geográfica e são protegidos pela [Replicação inter-regional do Armazenamento do Azure](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage).

Para obter mais informações, confira [Retenção de backup de longo prazo](sql-database-long-term-retention.md).

## <a name="storage-costs"></a>Custos de armazenamento
For single databases and managed instances, a minimum backup storage amount equal to 100% of database size is provided at no extra charge. For elastic pools, a minimum backup storage amount equal to 100% of the allocated data storage for the pool is provided at no extra charge. O consumo adicional de armazenamento de backup será cobrado em GB/mês. This additional consumption will depend on the workload and size of the individual databases.

You can use Azure subscription cost analysis to determine your current spending on backup storage.

![Backup storage cost analysis](./media/sql-database-automated-backup/check-backup-storage-cost-sql-mi.png)

If you go to your subscription and open Cost Analysis blade, you can select meter subcategory **mi pitr backup storage** to see your current backup cost and charge forecast. You can also include other meter subcategories such as **managed instance general purpose - storage** or **managed instance general purpose - compute gen5** to compare backup storage cost with other cost categories.

> [!Note]
> You can [change retention period to 7 days](#change-pitr-backup-retention-period-using-azure-portal) to reduce the backup storage cost.

Para saber mais sobre preços de armazenamento, confira a página de [preços](https://azure.microsoft.com/pricing/details/sql-database/single/). 

## <a name="are-backups-encrypted"></a>Os backups são criptografados

Quando o banco de dados é criptografado com TDE, os backups são criptografados automaticamente em repouso, incluindo os backups de LTR. Quando a TDE está habilitada para um banco de dados SQL do Azure, os backups também são criptografados. Todos os novos bancos de dados SQL do Azure são configurados com TDE habilitada por padrão. Para obter mais informações sobre a TDE, confira [Transparent Data Encryption com o Banco de Dados SQL do Azure](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="how-does-microsoft-ensure-backup-integrity"></a>Como a Microsoft garante a integridade do backup

On an ongoing basis, the Azure SQL Database engineering team automatically tests the restore of automated database backups of databases placed in Logical servers and Elastic pools (this is not available in Managed Instance). Upon point-in-time restore, databases also receive integrity checks using DBCC CHECKDB.

Managed Instance takes automatic initial backup with `CHECKSUM` of the databases restored using native `RESTORE` command or Data Migration Service once the migration is completed.

Os problemas encontrados durante a verificação de integridade resultarão em um alerta para a equipe de engenharia. Para obter mais informações sobre a integridade dos dados no Banco de Dados SQL do Azure, confira [Integridade dos dados no Banco de Dados SQL do Azure](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

## <a name="how-do-automated-backups-impact-compliance"></a>Como os backups automatizados afetam a conformidade

Quando você migra o banco de dados de uma camada de serviço baseada em DTU, com a retenção de PITR padrão de 35 dias, para uma camada de serviço baseada em vCore, a retenção de PITR é preservada para garantir que a política de recuperação de dados do aplicativo não seja comprometida. Se a retenção padrão não atender aos seus requisitos de conformidade, você poderá alterar o período de retenção de PITR usando o PowerShell ou a API REST. Para obter mais informações, veja [Alterar o período de retenção de backup](#how-to-change-the-pitr-backup-retention-period).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="how-to-change-the-pitr-backup-retention-period"></a>Como alterar o período de retenção de backup de PITR

You can change the default PITR backup retention period using the Azure portal, PowerShell, or the REST API. Os valores com suporte são: 7, 14, 21, 28 ou 35 dias. Os exemplos a seguir ilustram como alterar a retenção de PITR para 28 dias.

> [!WARNING]
> If you reduce the current retention period, all existing backups older than the new retention period are no longer available. Se você aumentar o período de retenção atual, o Banco de Dados SQL manterá os backups existentes até que o período de retenção mais longo seja atingido.

> [!NOTE]
> Essas APIs afetarão somente o período de retenção de PITR. Se você tiver configurado a LTR para o banco de dados, ela não será afetada. Para obter mais informações sobre como alterar o(s) período(s) de retenção de LTR, confira [Retenção de longo prazo](sql-database-long-term-retention.md).

### <a name="change-pitr-backup-retention-period-using-azure-portal"></a>Change PITR backup retention period using Azure portal

To change the PITR backup retention period using the Azure portal, navigate to the server object whose retention period you wish to change within the portal and then select the appropriate option based on which server object you're modifying.

#### <a name="single-database--elastic-poolstabsingle-database"></a>[Banco de dados individual e pools elásticos](#tab/single-database)

Change of PITR backup retention for single Azure SQL Databases is performed at the server level. Change made at the server level applies to databases on that server. To change PITR for Azure SQL Database server from Azure portal, navigate to the server overview blade, click on Manage Backups on the navigation menu, and then click on Configure retention at the navigation bar.

![Alterar PITR no portal do Azure](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="managed-instancetabmanaged-instance"></a>[Instância Gerenciada](#tab/managed-instance)

Change of PITR backup retention for SQL Database managed instance is performed at an individual database level. To change PITR backup retention for an instance database from Azure portal, navigate to the individual database overview blade, and then click on Configure backup retention at the navigation bar.

![Alterar PITR no portal do Azure](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

---

### <a name="change-pitr-backup-retention-period-using-powershell"></a>Alterar o período de retenção de backup de PITR usando o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> The PowerShell Azure Resource Manager module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. For these cmdlets, see [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). The arguments for the commands in the Az module and in the AzureRm modules are substantially identical.

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-pitr-retention-period-using-rest-api"></a>Alterar o período de retenção de PITR usando a API REST

#### <a name="sample-request"></a>Amostra de Solicitação

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>Corpo da solicitação

```json
{
  "properties":{
    "retentionDays":28
  }
}
```

#### <a name="sample-response"></a>Amostra de Resposta

Código de status: 200

```json
{
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
  }
}
```

Para obter mais informações, confira [API REST de retenção de backup](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies).

## <a name="next-steps"></a>Próximos passos

- Os backups de banco de dados são uma parte essencial de qualquer estratégia de recuperação de desastre e continuidade dos negócios, porque eles protegem seus dados contra exclusão ou corrupção acidentais. Para saber mais sobre as outras soluções de continuidade dos negócios do Banco de Dados SQL do Azure, consulte [Visão geral da continuidade dos negócios](sql-database-business-continuity.md).
- Para restaurar para um determinado ponto no tempo usando o Portal do Azure, consulte [Restaurar um banco de dados para um ponto no tempo usando o Portal do Azure](sql-database-recovery-using-backups.md).
- Para restaurar para um determinado ponto no tempo usando o PowerShell, consulte [Restaurar um banco de dados para um ponto no tempo usando o PowerShell](scripts/sql-database-restore-database-powershell.md).
- Para configurar, gerenciar e restaurar a retenção de longo prazo de backups automatizados no Armazenamento de Blobs do Azure usando o portal do Azure, consulte [Gerenciar retenção de backup de longo prazo usando o portal do Azure](sql-database-long-term-backup-retention-configure.md).
- To configure, manage, and restore from long-term retention of automated backups in Azure Blob storage using PowerShell, see [Manage long-term backup retention using PowerShell](sql-database-long-term-backup-retention-configure.md).
