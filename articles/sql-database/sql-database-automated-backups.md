---
title: Backups automáticos e geo-redundantes
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
ms.date: 12/13/2019
ms.openlocfilehash: 9ac6927df63d51830a58773e32ad0968920c0867
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061770"
---
# <a name="automated-backups"></a>Backups automatizados

O Azure SQL Database cria automaticamente os backups do banco de dados mantidos durante o período de retenção configurado. Ele usa o armazenamento geo-redundante de acesso de leitura do Azure [(RA-GRS)](../storage/common/storage-redundancy.md) para garantir que os backups sejam preservados mesmo que o data center não esteja disponível.

Os backups de banco de dados são uma parte essencial de qualquer estratégia de recuperação de desastre e continuidade dos negócios, porque eles protegem seus dados contra exclusão ou corrupção acidentais. Se suas regras de segurança exigirem que seus backups estejam disponíveis por um tempo prolongado (até 10 anos), você pode configurar [a retenção a longo prazo](sql-database-long-term-retention.md) em bancos de dados singleton e pools de bancos de dados elásticos.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>O que é um backup de Banco de Dados SQL?

O SQL Database usa a tecnologia SQL Server para criar [backups completos](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) toda semana, [backups diferenciais](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) a cada 12 horas e [backups de log de transações](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) a cada 5 a 10 minutos. Os backups são armazenados em [blobs de armazenamento RA-GRS](../storage/common/storage-redundancy.md) que são replicados em um [data center emparelhado](../best-practices-availability-paired-regions.md) para proteção contra uma paralisação do data center. Quando você restaura um banco de dados, o serviço determina quais backups completos, diferenciais e de registro de transações precisam ser restaurados.

Use esses backups para:

- **Restaure um banco de dados existente a um ponto no passado no período de** retenção usando o portal Azure, Azure PowerShell, Azure CLI ou a API REST. Para bancos de dados únicos e grupos de banco de dados elásticos, essa operação criará um novo banco de dados no mesmo servidor do banco de dados original. Em caso de gerenciamento, essa operação pode criar uma cópia do banco de dados ou a mesma ou uma instância gerenciada diferente a mesma assinatura.
- **Restaure um banco de dados excluído no momento da exclusão** ou a qualquer momento dentro do período de retenção. O banco de dados excluído só pode ser restaurado no mesmo servidor lógico ou instância gerenciada onde o banco de dados original foi criado.
- **Restaurar um banco de dados para outra região geográfica**. A restauração geográfica permite que você se recupere de um desastre geográfico quando você não pode acessar seu servidor e banco de dados. Ele cria um novo banco de dados em qualquer servidor existente, em qualquer lugar do mundo.
- **Restaurar um banco de dados a partir de um backup específico de longo prazo** em um único banco de dados ou um pool de banco de dados elástico, se o banco de dados estiver configurado com uma política de retenção de longo prazo (LTR). A LTR permite que você restaure uma versão antiga do banco de dados usando [o portal Azure](sql-database-long-term-backup-retention-configure.md#using-azure-portal) ou [o Azure PowerShell](sql-database-long-term-backup-retention-configure.md#using-powershell) para satisfazer uma solicitação de conformidade ou executar uma versão antiga do aplicativo. Para obter mais informações, consulte [Retenção de longo prazo](sql-database-long-term-retention.md).

Para executar uma restauração, consulte [Restaurar banco de dados a partir de backups](sql-database-recovery-using-backups.md).

> [!NOTE]
> No Azure Storage, o termo *replicação* refere-se à cópia de arquivos de um local para outro. A *replicação do banco de dados* SQL Server refere-se a manter vários bancos de dados secundários sincronizados com um banco de dados principal.

Você pode tentar algumas dessas operações usando os seguintes exemplos:

| | O Portal do Azure | Azure PowerShell |
|---|---|---|
| Alterar a retenção de backup | [Banco de dados individual](sql-database-automated-backups.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) <br/> [Instância gerenciada](sql-database-automated-backups.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) | [Banco de dados individual](sql-database-automated-backups.md#change-the-pitr-backup-retention-period-by-using-powershell) <br/>[Instância gerenciada](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| Alterar a retenção de backup a longo prazo | [Banco de dados individual](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Instância gerenciada - N/A  | [Banco de dados individual](sql-database-long-term-backup-retention-configure.md)<br/>Instância gerenciada - N/A  |
| Restaurar um banco de dados a partir de um ponto no tempo | [Banco de dados individual](sql-database-recovery-using-backups.md#point-in-time-restore) | [Banco de dados individual](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Instância gerenciada](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| Restaurar um banco de dados excluído | [Banco de dados individual](sql-database-recovery-using-backups.md) | [Banco de dados individual](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Instância gerenciada](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Restaurar um banco de dados do armazenamento Azure Blob | Banco de dados único - N/A <br/>Instância gerenciada - N/A  | Banco de dados único - N/A <br/>[Instância gerenciada](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |


## <a name="backup-frequency"></a>Frequência de backup

### <a name="point-in-time-restore"></a>Restauração em um momento determinado

O SQL Database oferece suporte ao autoatendimento para restauração point-in-time (PITR), criando automaticamente backups completos, backups diferenciados e backups de log de transações. Backups completos do banco de dados são criados semanalmente, e backups diferenciais de banco de dados são geralmente criados a cada 12 horas. Os backups de registro de transações são geralmente criados a cada 5 a 10 minutos. A freqüência de backups de log de transação é baseada no tamanho da computação e na quantidade de atividade do banco de dados. 

O primeiro backup completo é agendado imediatamente após a criação de um banco de dados. Esse backup geralmente é concluído em 30 minutos, mas pode levar mais tempo quando o banco de dados é grande. Por exemplo, o backup inicial pode levar mais tempo para um banco de dados restaurado ou uma cópia do banco de dados. Após o primeiro backup completo, todos os outros backups são agendados automaticamente e gerenciados de forma silenciosa em segundo plano. O tempo exato de todos os backups de banco de dados é determinado pelo serviço do Banco de Dados SQL, pois ele equilibra a carga de trabalho geral do sistema. Você não pode alterar ou desabilitar os trabalhos de backup.

Os backups PITR são protegidos com armazenamento geo-redundante. Para mais informações, confira [Redundância do Armazenamento do Microsoft Azure](../storage/common/storage-redundancy.md).

Para obter mais informações sobre o PITR, consulte [restaurar point-in-time](sql-database-recovery-using-backups.md#point-in-time-restore).

### <a name="long-term-retention"></a>Retenção de longo prazo

Para bancos de dados únicos e agrupados, você pode configurar a retenção de longo prazo (LTR) de backups completos por até 10 anos no armazenamento Azure Blob. Se você habilitar a política LTR, os backups completos semanais serão automaticamente copiados para um recipiente de armazenamento RA-GRS diferente. Para atender a vários requisitos de conformidade, você pode selecionar diferentes períodos de retenção para backups semanais, mensais e/ou anuales. O consumo de armazenamento depende da freqüência selecionada de backups e do período de retenção ou períodos. Você pode usar a [Calculadora de preços de LTR](https://azure.microsoft.com/pricing/calculator/?service=sql-database) para estimar o custo do armazenamento de LTR.

Como backups PITR, os backups LTR são protegidos com armazenamento geo-redundante. Para mais informações, confira [Redundância do Armazenamento do Microsoft Azure](../storage/common/storage-redundancy.md).

Para obter mais informações sobre a LTR, consulte [retenção de backup a longo prazo](sql-database-long-term-retention.md).

## <a name="backup-storage-consumption"></a>Consumo de armazenamento de backup

Para bancos de dados únicos, esta equação é usada para calcular o uso total de armazenamento de backup:

`Total backup storage size = (size of full backups + size of differential backups + size of log backups) – database size`

Para pools de banco de dados elásticos, o tamanho total do armazenamento de backup é agregado ao nível do pool e é calculado da seguinte forma:

`Total backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - allocated pool data storage`

Os backups que ocorrem antes do período de retenção são automaticamente eliminados com base no carimbo de tempo. Como backups diferenciais e backups de log exigem que um backup completo anterior seja útil, eles são eliminados juntos em pedaços semanais.

O Azure SQL Database calcula seu armazenamento total de backup em retenção como um valor cumulativo. A cada hora, esse valor é reportado ao pipeline de faturamento do Azure, que é responsável por agregar esse uso por hora para calcular seu consumo no final de cada mês. Depois que o banco de dados é descartado, o consumo diminui à medida que os backups envelhecem. Depois que os backups ficam mais antigos do que o período de retenção, o faturamento pára.

   > [!IMPORTANT]
   > Os backups de um banco de dados são retidos para o período de retenção especificado, mesmo que o banco de dados tenha sido descartado. Embora a queda e a recriação de um banco de dados possam frequentemente economizar em custos de armazenamento e computação, isso pode aumentar os custos de armazenamento de backup porque a Microsoft retém um backup para o período de retenção especificado (que é de 7 dias no mínimo) para cada banco de dados descartado, cada vez que caiu.

### <a name="monitor-consumption"></a>Monitorar o consumo

Cada tipo de backup (completo, diferencial e log) é relatado na lâmina de monitoramento do banco de dados como uma métrica separada. O diagrama a seguir mostra como monitorar o consumo de armazenamento de backup para um único banco de dados. No momento, esse recurso não está disponível para instâncias gerenciadas.

![Monitore o consumo de backup do banco de dados no portal Azure](media/sql-database-automated-backup/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>Ajuste fino do consumo de armazenamento de backup

O consumo excessivo de armazenamento de backup dependerá da carga de trabalho e do tamanho dos bancos de dados individuais. Considere algumas das seguintes técnicas de ajuste para reduzir o consumo de armazenamento de backup:

* Reduza o [período de retenção](#change-the-pitr-backup-retention-period-by-using-the-azure-portal) de backup ao mínimo possível para suas necessidades.
* Evite fazer grandes operações de gravação, como reconstruções de índice, com mais freqüência do que você precisa.
* Para grandes operações de carga de dados, considere usar [índices de columnstore agrupados,](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)reduzir o número de índices não agrupados e considerar operações de carga a granel com contagem de linhas em torno de 1 milhão.
* No nível de serviço de uso geral, o armazenamento de dados provisionado é mais barato do que o preço do armazenamento de backup em excesso. Se você tiver custos de armazenamento de backup em excesso continuamente altos, você pode considerar aumentar o armazenamento de dados para economizar no armazenamento de backup.
* Use TempDB em vez de tabelas permanentes em sua lógica ETL para armazenar resultados temporários. (Aplicável apenas à instância gerenciada.)
* Considere desativar a criptografia TDE para bancos de dados que não contenham dados confidenciais (desenvolvimento ou bancos de dados de teste, por exemplo). Os backups para bancos de dados não criptografados são normalmente compactados com uma taxa de compressão mais alta.

> [!IMPORTANT]
> Para cargas de trabalho analíticas do data mart \ data warehouse, recomendamos fortemente que você use [índices de columnstore em cluster,](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)reduza o número de índices não agrupados e considere operações de carga a granel com contagem de linhas em torno de 1 milhão para reduzir o consumo excessivo de armazenamento de backup.

## <a name="storage-costs"></a>Custos de armazenamento

O preço do armazenamento varia dependendo se você está usando o modelo DTU ou o modelo vCore.

### <a name="dtu-model"></a>Modelo de CPU

Não há nenhuma taxa adicional para armazenamento de backup para bancos de dados e pools de banco de dados elásticos se você estiver usando o modelo DTU.

### <a name="vcore-model"></a>Modelo vCore

Para bancos de dados únicos, um valor mínimo de armazenamento de backup igual a 100% do tamanho do banco de dados é fornecido sem custo adicional. Para pools de banco de dados elásticos e instâncias gerenciadas, um valor mínimo de armazenamento de backup igual a 100% do armazenamento de dados alocado para o tamanho do pool ou da instância, respectivamente, é fornecido sem custo adicional. O consumo adicional de armazenamento de backup será cobrado em GB/mês. Esse consumo adicional dependerá da carga de trabalho e do tamanho das bases de dados individuais.

O Azure SQL Database calculará seu armazenamento total de backup em retenção como um valor cumulativo. A cada hora, esse valor é reportado ao pipeline de faturamento do Azure, que é responsável por agregar esse uso por hora para obter seu consumo no final de cada mês. Depois que o banco de dados é descartado, a Microsoft diminui o consumo à medida que os backups envelhecem. Depois que os backups ficam mais antigos do que o período de retenção, o faturamento pára. Como todos os backups de log e backups diferenciais são mantidos durante o período de retenção total, bancos de dados fortemente modificados terão taxas de backup mais altas.

Suponha que um banco de dados tenha acumulado 744 GB de armazenamento de backup e que esse valor permaneça constante durante um mês inteiro. Para converter esse consumo acumulado de armazenamento em uso por hora, divida-o por 744,0 (31 dias por mês * 24 horas por dia). Assim, o SQL Database informará que o banco de dados consumiu 1 GB de backup PITR a cada hora. O faturamento do Azure agregará esse consumo e mostrará um uso de 744 GB durante todo o mês. O custo será baseado na taxa de $/GB/mês em sua região.

Agora, um exemplo mais complexo. Suponha que o banco de dados tenha sua retenção aumentada para 14 dias no meio do mês. Suponha que esse aumento (hipoteticamente) resulte no armazenamento total de backup dobrando para 1.488 GB. Banco de dados SQL relataria 1 GB de uso por horas 1 a 372. Ele relataria o uso como 2 GB por horas 373 a 744. Esse uso seria agregado a uma conta final de 1.116 GB/mês.

### <a name="monitor-costs"></a>Monitorar custos

Para entender os custos de armazenamento de backup, vá para **Gerenciamento de Custos + Faturamento** no portal Azure, selecione Gerenciamento de **Custos**e selecione Análise **de custos**. Selecione a assinatura desejada como **escopo**e, em seguida, filtre para o período de tempo e serviço que você está interessado.

Adicione um filtro para **nome de serviço**e, em seguida, selecione o banco de dados **sql** na lista de desígeração. Use o filtro **de subcategoria do medidor** para escolher o contador de faturamento para o seu serviço. Para um único banco de dados ou um pool de banco de dados elástico, selecione **o armazenamento de backup de um único/elástico .** Para uma instância gerenciada, selecione **o armazenamento de backup mi pitr**. As subcategorias **de armazenamento** e **computação** podem lhe interessar também, mas elas não estão associadas aos custos de armazenamento de backup.

![Análise de custos de armazenamento de backup](./media/sql-database-automated-backup/check-backup-storage-cost-sql-mi.png)

## <a name="backup-retention"></a>Retenção de backup

Todos os bancos de dados Azure SQL (bancos de dados de instâncias únicas, agrupadas e gerenciadas) têm um período de retenção de backup padrão de 7 dias. Você pode [alterar o período de retenção de backup](#change-the-pitr-backup-retention-period) para até 35 dias.

Se você excluir um banco de dados, o Banco de Dados SQL manterá os backups da mesma maneira que em um banco de dados online. Por exemplo, se você excluir um banco de dados Básico que tenha um período de retenção de sete dias, um backup de quatro dias de idade será salvo por mais três dias.

Se você precisar manter os backups por mais tempo que o período máximo de retenção, modifique as propriedades de backup para adicionar um ou mais períodos de retenção de longo prazo para o banco de dados. Para obter mais informações, consulte [Retenção de longo prazo](sql-database-long-term-retention.md).

> [!IMPORTANT]
> Se você excluir o servidor SQL do Azure que hospeda bancos de dados SQL, todos os pools de bancos de dados elásticos e bancos de dados que pertencem ao servidor também serão excluídos. Eles não podem ser recuperados. Você não pode restaurar um servidor excluído. Mas se você configurou a retenção a longo prazo, os backups dos bancos de dados com LTR não serão excluídos e esses bancos de dados poderão ser restaurados.

## <a name="encrypted-backups"></a>Backups criptografados

Se seu banco de dados for criptografado com TDE, os backups serão criptografados automaticamente em repouso, incluindo backups LTR. Quando a TDE está habilitada para um banco de dados SQL do Azure, os backups também são criptografados. Todos os novos bancos de dados SQL do Azure são configurados com TDE habilitada por padrão. Para obter mais informações sobre a TDE, confira [Transparent Data Encryption com o Banco de Dados SQL do Azure](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="backup-integrity"></a>Integridade de backup

Continuamente, a equipe de engenharia do Banco de Dados SQL do Azure testa automaticamente a restauração de backups automatizados de bancos de dados de bancos de dados colocados em servidores lógicos e pools de bancos de dados elásticos. (Este teste não está disponível na instância gerenciada.) Após a restauração point-in-time, os bancos de dados também recebem verificações de integridade do DBCC CHECKDB.

A instância gerenciada faz `CHECKSUM` backup inicial automático `RESTORE` com bancos de dados restaurados com o comando nativo ou com o Azure Data Migration Service após a migração ser concluída.

Os problemas encontrados durante a verificação de integridade resultarão em um alerta para a equipe de engenharia. Para obter mais informações, consulte [Integridade de dados no Banco de Dados Do Azure SQL](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

## <a name="compliance"></a>Conformidade

Quando você migra seu banco de dados de um nível de serviço baseado em DTU para um nível de serviço baseado em vCore, a retenção pitr é preservada para garantir que a política de recuperação de dados do aplicativo não seja comprometida. Se a retenção padrão não atender aos requisitos de conformidade, você pode alterar o período de retenção pitr usando o PowerShell ou a API REST. Para obter mais informações, consulte [Alterar o período de retenção de backup PITR](#change-the-pitr-backup-retention-period).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-pitr-backup-retention-period"></a>Alterar o período de retenção de backup pitr

Você pode alterar o período de retenção de backup PITR padrão usando o portal Azure, powershell ou a API REST. Os exemplos a seguir ilustram como alterar a retenção pitr para 28 dias.

> [!WARNING]
> Se você reduzir o período de retenção atual, todos os backups existentes que são mais antigos do que o novo período de retenção não estarão mais disponíveis. Se você aumentar o período de retenção atual, o Banco de Dados SQL manterá os backups existentes até que o fim do período de retenção mais longo seja atingido.

> [!NOTE]
> Essas APIs afetarão apenas o período de retenção pitr. Se você configurou LTR para seu banco de dados, ele não será afetado. Para obter informações sobre como alterar os períodos de retenção de LTR, consulte [retenção a longo prazo](sql-database-long-term-retention.md).

### <a name="change-the-pitr-backup-retention-period-by-using-the-azure-portal"></a>Alterar o período de retenção de backup pitr usando o portal Azure

Para alterar o período de retenção de backup pitr usando o portal Azure, vá para o objeto do servidor cujo período de retenção você deseja alterar no portal. Em seguida, selecione a opção apropriada com base no objeto do servidor que você está modificando.

#### <a name="single-database-and-elastic-database-pools"></a>[Banco de dados único e pools de banco de dados elásticos](#tab/single-database)

Alterações na retenção de backup pitr para bancos de dados SQL único do Azure são feitas no nível do servidor. As alterações feitas no nível do servidor se aplicam aos bancos de dados do servidor. Para alterar a retenção pitr para um servidor de banco de dados SQL do portal Azure, vá para a lâmina de visão geral do servidor. Selecione **Gerenciar backups** no painel esquerdo e, em seguida, **selecione Configurar retenção** na parte superior da tela:

![Alterar retenção pitr, nível de servidor](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="managed-instance"></a>[Instância gerenciada](#tab/managed-instance)

Alterações na retenção de backup pitr para instâncias gerenciadas do Banco de Dados SQL são feitas em um nível de banco de dados individual. Para alterar a retenção de backup pitr para um banco de dados de instâncias do portal Azure, vá para a lâmina de visão geral do banco de dados individual. Em seguida, **selecione Configurar retenção de backup** na parte superior da tela:

![Alterar retenção pitr, instância gerenciada](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

---

### <a name="change-the-pitr-backup-retention-period-by-using-powershell"></a>Altere o período de retenção de backup pitr usando o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo PowerShell AzureRM ainda é suportado pelo Banco de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para obter mais informações, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az são substancialmente idênticos aos dos módulos AzureRm.

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-the-pitr-backup-retention-period-by-using-the-rest-api"></a>Altere o período de retenção de backup pitr usando a API REST

#### <a name="sample-request"></a>Solicitação de exemplo

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

#### <a name="sample-response"></a>Resposta de exemplo

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

## <a name="next-steps"></a>Próximas etapas

- Os backups de banco de dados são uma parte essencial de qualquer estratégia de recuperação de desastre e continuidade dos negócios, porque eles protegem seus dados contra exclusão ou corrupção acidentais. Para saber mais sobre as outras soluções de continuidade dos negócios do Banco de Dados SQL do Azure, consulte [Visão geral da continuidade dos negócios](sql-database-business-continuity.md).
- Obtenha mais informações sobre como [restaurar um banco de dados a um ponto no tempo usando o portal Azure](sql-database-recovery-using-backups.md).
- Obtenha mais informações sobre como [restaurar um banco de dados a um ponto no tempo usando o PowerShell](scripts/sql-database-restore-database-powershell.md).
- Para obter informações sobre como configurar, gerenciar e restaurar a partir da retenção a longo prazo de backups automatizados no armazenamento Azure Blob usando o portal Azure, consulte [Gerenciar a retenção de backup a longo prazo usando o portal Azure](sql-database-long-term-backup-retention-configure.md).
- Para obter informações sobre como configurar, gerenciar e restaurar a partir da retenção a longo prazo de backups automatizados no armazenamento Azure Blob usando o PowerShell, consulte [Gerenciar a retenção de backup a longo prazo usando o PowerShell](sql-database-long-term-backup-retention-configure.md).
