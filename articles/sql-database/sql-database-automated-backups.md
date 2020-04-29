---
title: Backups automáticos com redundância geográfica
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80061770"
---
# <a name="automated-backups"></a>Backups automatizados

O banco de dados SQL do Azure cria automaticamente os backups de banco de dados mantidos durante o período de retenção configurado. Ele usa o [armazenamento com redundância geográfica com acesso de leitura do Azure (ra-grs)](../storage/common/storage-redundancy.md) para garantir que os backups sejam preservados mesmo se o datacenter estiver indisponível.

Os backups de banco de dados são uma parte essencial de qualquer estratégia de recuperação de desastre e continuidade dos negócios, porque eles protegem seus dados contra exclusão ou corrupção acidentais. Se suas regras de segurança exigirem que os backups estejam disponíveis por um tempo estendido (até 10 anos), você poderá configurar a [retenção de longo prazo](sql-database-long-term-retention.md) em bancos de dados singleton e pools de banco de dado elástico.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>O que é um backup de Banco de Dados SQL?

O banco de dados SQL usa a tecnologia SQL Server para criar [backups completos](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) toda semana, [backups diferenciais](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) a cada 12 horas e [backups de log de transações](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) a cada 5 a 10 minutos. Os backups são armazenados em [blobs de armazenamento ra-grs](../storage/common/storage-redundancy.md) que são replicados para um [datacenter emparelhado](../best-practices-availability-paired-regions.md) para proteção contra uma interrupção do datacenter. Quando você restaura um banco de dados, o serviço determina quais backups completos, diferenciais e de log de transações precisam ser restaurados.

Use esses backups para:

- **Restaure um banco de dados existente para um ponto no tempo no passado** dentro do período de retenção usando o portal do Azure, Azure PowerShell, CLI do Azure ou a API REST. Para bancos de dados individuais e pools de banco de dados elástico, essa operação criará um novo banco de dados no mesmo servidor que o banco de dados original. Na instância gerenciada, essa operação pode criar uma cópia do banco de dados ou a mesma instância gerenciada ou diferente na mesma assinatura.
- **Restaure um banco de dados excluído no momento da exclusão** ou a qualquer momento dentro do período de retenção. O banco de dados excluído pode ser restaurado somente no mesmo servidor lógico ou instância gerenciada em que o banco de dados original foi criado.
- **Restaure um banco de dados para outra região geográfica**. A restauração geográfica permite que você se recupere de um desastre geográfico quando não é possível acessar o servidor e o banco de dados. Ele cria um novo banco de dados em qualquer servidor existente, em qualquer lugar do mundo.
- **Restaure um banco de dados de um backup de longo prazo específico** em um único banco de dados ou pool de banco de dados elástico, se o banco de dados estiver configurado com uma EPD (política de retenção de longo prazo). EPD permite que você restaure uma versão antiga do banco de dados usando [o portal do Azure](sql-database-long-term-backup-retention-configure.md#using-azure-portal) ou [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#using-powershell) para atender a uma solicitação de conformidade ou para executar uma versão antiga do aplicativo. Para obter mais informações, consulte [Retenção de longo prazo](sql-database-long-term-retention.md).

Para executar uma restauração, consulte [restaurar banco de dados de backups](sql-database-recovery-using-backups.md).

> [!NOTE]
> No armazenamento do Azure, o termo *replicação* refere-se à cópia de arquivos de um local para outro. SQL Server *replicação de banco de dados* refere-se a manter vários bancos de dados secundários sincronizados com um banco de dados primário.

Você pode experimentar algumas dessas operações usando os seguintes exemplos:

| | O Portal do Azure | Azure PowerShell |
|---|---|---|
| Alterar retenção de backup | [Banco de dados individual](sql-database-automated-backups.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) <br/> [Instância gerenciada](sql-database-automated-backups.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) | [Banco de dados individual](sql-database-automated-backups.md#change-the-pitr-backup-retention-period-by-using-powershell) <br/>[Instância gerenciada](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| Alterar retenção de backup de longo prazo | [Banco de dados individual](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Instância gerenciada-N/A  | [Banco de dados individual](sql-database-long-term-backup-retention-configure.md)<br/>Instância gerenciada-N/A  |
| Restaurar um banco de dados de um ponto no tempo | [Banco de dados individual](sql-database-recovery-using-backups.md#point-in-time-restore) | [Banco de dados individual](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Instância gerenciada](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| Restaurar um banco de dados excluído | [Banco de dados individual](sql-database-recovery-using-backups.md) | [Banco de dados individual](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Instância gerenciada](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Restaurar um banco de dados do armazenamento de BLOBs do Azure | Banco de dados único-N/A <br/>Instância gerenciada-N/A  | Banco de dados único-N/A <br/>[Instância gerenciada](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |


## <a name="backup-frequency"></a>Frequência de backup

### <a name="point-in-time-restore"></a>Restauração em um momento determinado

O banco de dados SQL dá suporte a autoatendimento para PITR (restauração pontual) criando automaticamente backups completos, backups diferenciais e backups de log de transações. Backups de banco de dados completos são criados semanalmente e backups de banco de dados diferenciais geralmente são criados a cada 12 horas. Os backups de log de transações geralmente são criados a cada 5 a 10 minutos. A frequência dos backups de log de transações é baseada no tamanho da computação e na quantidade de atividade do banco de dados. 

O primeiro backup completo é agendado imediatamente após a criação de um banco de dados. Esse backup geralmente é concluído em 30 minutos, mas pode levar mais tempo quando o banco de dados é grande. Por exemplo, o backup inicial pode levar mais tempo para um banco de dados restaurado ou uma cópia do banco de dados. Após o primeiro backup completo, todos os outros backups são agendados automaticamente e gerenciados de forma silenciosa em segundo plano. O tempo exato de todos os backups de banco de dados é determinado pelo serviço do Banco de Dados SQL, pois ele equilibra a carga de trabalho geral do sistema. Você não pode alterar ou desabilitar os trabalhos de backup.

Os backups do PITR são protegidos com armazenamento com redundância geográfica. Para mais informações, confira [Redundância do Armazenamento do Microsoft Azure](../storage/common/storage-redundancy.md).

Para obter mais informações sobre PITR, consulte [restauração pontual](sql-database-recovery-using-backups.md#point-in-time-restore).

### <a name="long-term-retention"></a>Retenção de longo prazo

Para bancos de dados individuais e em pool, você pode configurar a EPD (retenção de longo prazo) de backups completos por até 10 anos no armazenamento de BLOBs do Azure. Se você habilitar a política EPD, os backups completos semanais serão copiados automaticamente para um contêiner de armazenamento RA-GRS diferente. Para atender a vários requisitos de conformidade, você pode selecionar diferentes períodos de retenção para backups semanais, mensais e/ou anuais. O consumo de armazenamento depende da frequência selecionada de backups e do período de retenção ou períodos. Você pode usar a [Calculadora de preços de LTR](https://azure.microsoft.com/pricing/calculator/?service=sql-database) para estimar o custo do armazenamento de LTR.

Assim como os backups do PITR, os backups EPD são protegidos com o armazenamento com redundância geográfica. Para mais informações, confira [Redundância do Armazenamento do Microsoft Azure](../storage/common/storage-redundancy.md).

Para obter mais informações sobre EPD, consulte [retenção de backup de longo prazo](sql-database-long-term-retention.md).

## <a name="backup-storage-consumption"></a>Consumo de armazenamento de backup

Para bancos de dados individuais, esta equação é usada para calcular o uso do armazenamento de backup total:

`Total backup storage size = (size of full backups + size of differential backups + size of log backups) – database size`

Para pools de banco de dados elástico, o tamanho total de armazenamento de backup é agregado no nível do pool e é calculado da seguinte maneira:

`Total backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - allocated pool data storage`

Os backups que ocorrem antes do período de retenção são limpos automaticamente com base em seu carimbo de data/hora. Como backups diferenciais e backups de log exigem um backup completo anterior para serem úteis, eles são limpos juntos em partes semanais.

O banco de dados SQL do Azure computa o armazenamento de backup total na retenção como um valor cumulativo. A cada hora, esse valor é relatado para o pipeline de cobrança do Azure, que é responsável por agregar esse uso por hora para calcular seu consumo no final de cada mês. Depois que o banco de dados é Descartado, o consumo diminui como a idade dos backups. Após os backups serem mais antigos do que o período de retenção, a cobrança é interrompida.

   > [!IMPORTANT]
   > Os backups de um banco de dados são retidos durante o período de retenção especificado, mesmo que o banco de dados tenha sido descartado. Embora o descarte e recriação de um banco de dados possa ser salvo com frequência em custos de computação e armazenamento, ele pode aumentar os custos de armazenamento de backup porque a Microsoft retém um backup para o período de retenção especificado (que tem 7 dias no mínimo) para cada banco de dados descartado, sempre que é Descartado.

### <a name="monitor-consumption"></a>Monitorar o consumo

Cada tipo de backup (completo, diferencial e log) é relatado na folha monitoramento do banco de dados como uma métrica separada. O diagrama a seguir mostra como monitorar o consumo de armazenamento de backup de um único banco de dados. Este recurso não está disponível no momento para instâncias gerenciadas.

![Monitorar o consumo de backup de banco de dados no portal do Azure](media/sql-database-automated-backup/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>Ajuste fino do consumo de armazenamento de backup

O consumo de armazenamento de backup excessivo dependerá da carga de trabalho e do tamanho dos bancos de dados individuais. Considere algumas das técnicas de ajuste a seguir para reduzir o consumo de armazenamento de backup:

* Reduza o [período de retenção de backup](#change-the-pitr-backup-retention-period-by-using-the-azure-portal) para o mínimo possível para suas necessidades.
* Evite fazer grandes operações de gravação, como recompilações de índice, com mais frequência do que o necessário.
* Para operações de carregamento de dados grandes, considere o uso de [índices columnstore clusterizados](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes), reduza o número de índices não clusterizados e considere operações de carregamento em massa com contagem de linhas em cerca de 1 milhão.
* Na camada de serviço de uso geral, o armazenamento de dados provisionado é mais barato do que o preço do armazenamento de backup em excesso. Se você estiver sempre com alto excesso de custos de armazenamento de backup, considere aumentar o armazenamento de dados para salvar no armazenamento de backup.
* Use TempDB em vez de tabelas permanentes em sua lógica ETL para armazenar resultados temporários. (Aplicável somente à instância gerenciada.)
* Considere a desativação da criptografia TDE para bancos de dados que não contenham um dado confidencial (como, por exemplo). Os backups de bancos de dados não criptografados normalmente são compactados com uma taxa de compactação mais alta.

> [!IMPORTANT]
> Para cargas de trabalho de data mart analíticos \ data warehouse, é altamente recomendável usar [índices columnstore clusterizados](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes), reduzir o número de índices não clusterizados e considerar operações de carregamento em massa com contagem de linhas cerca de 1 milhão para reduzir o consumo excessivo de armazenamento de backup.

## <a name="storage-costs"></a>Custos de armazenamento

O preço do armazenamento varia dependendo se você estiver usando o modelo de DTU ou o modelo vCore.

### <a name="dtu-model"></a>Modelo de CPU

Não há nenhum custo adicional para o armazenamento de backup para bancos de dados e pools de banco de dados elástico se você estiver usando o modelo de DTU.

### <a name="vcore-model"></a>Modelo vCore

Para bancos de dados individuais, um valor de armazenamento de backup mínimo igual a 100 por cento do tamanho do banco de dados é fornecido sem custo adicional. Para pools de banco de dados elástico e instâncias gerenciadas, um valor de armazenamento de backup mínimo igual a 100% do armazenamento de dados alocado para o tamanho do pool ou da instância, respectivamente, é fornecido sem nenhum custo adicional. O consumo adicional de armazenamento de backup será cobrado em GB/mês. Esse consumo adicional dependerá da carga de trabalho e do tamanho dos bancos de dados individuais.

O banco de dados SQL do Azure calculará o armazenamento de backup total na retenção como um valor cumulativo. A cada hora, esse valor é relatado para o pipeline de cobrança do Azure, que é responsável por agregar esse uso por hora para obter seu consumo no final de cada mês. Depois que o banco de dados for descartado, a Microsoft diminuirá o consumo como a idade dos backups. Após os backups serem mais antigos do que o período de retenção, a cobrança é interrompida. Como todos os backups de log e backups diferenciais são mantidos pelo período de retenção completo, bancos de dados com muita modificação terão encargos de backup maiores.

Suponha que um banco de dados tenha acumulado 744 GB de armazenamento de backup e que esse valor permaneça constante ao longo de um mês inteiro. Para converter esse consumo de armazenamento cumulativo para o uso por hora, divida-o por 744,0 (31 dias por mês * 24 horas por dia). Portanto, o banco de dados SQL relatará que o banco de dados consumiu 1 GB de backup PITR a cada hora. A cobrança do Azure agregará esse consumo e mostrará um uso de 744 GB para o mês inteiro. O custo será baseado na taxa de $/GB/month em sua região.

Agora, um exemplo mais complexo. Suponha que o banco de dados tenha sua retenção aumentada para 14 dias no meio do mês. Suponha que esse aumento (hipotéticomente) resulte na duplicação total de armazenamento de backup para 1.488 GB. O banco de dados SQL relataria 1 GB de uso para as horas 1 a 372. Ele relataria o uso como 2 GB para as horas de 373 a 744. Esse uso seria agregado a uma fatura final de 1.116 GB/mês.

### <a name="monitor-costs"></a>Monitorar custos

Para entender os custos de armazenamento de backup, vá para **Gerenciamento de custos + cobrança** no portal do Azure, selecione **Gerenciamento de custos**e, em seguida, selecione análise de **custo**. Selecione a assinatura desejada como o **escopo**e, em seguida, filtre o período de tempo e o serviço em que você está interessado.

Adicione um filtro para **nome do serviço**e, em seguida, selecione **banco de dados SQL** na lista suspensa. Use o filtro **subcategoria de medidor** para escolher o contador de cobrança para seu serviço. Para um único banco de dados ou um pool de banco de dados elástico, selecione **pool único/elástico pitr armazenamento de backup**. Para uma instância gerenciada, selecione **pitr armazenamento de backup de mi**. As subcategorias de **armazenamento** e **computação** também podem ser interessantes, mas não estão associadas a custos de armazenamento de backup.

![Análise de custo de armazenamento de backup](./media/sql-database-automated-backup/check-backup-storage-cost-sql-mi.png)

## <a name="backup-retention"></a>Retenção de backup

Todos os bancos de dados SQL do Azure (bancos de dados de instância única, em pool e gerenciados) têm um período de retenção de backup padrão de 7 dias. Você pode [alterar o período de retenção de backup](#change-the-pitr-backup-retention-period) para desde 35 dias.

Se você excluir um banco de dados, o Banco de Dados SQL manterá os backups da mesma maneira que em um banco de dados online. Por exemplo, se você excluir um banco de dados básico que tem um período de retenção de sete dias, um backup com quatro dias de idade será salvo por mais três dias.

Se você precisar manter os backups por mais tempo que o período máximo de retenção, modifique as propriedades de backup para adicionar um ou mais períodos de retenção de longo prazo para o banco de dados. Para obter mais informações, consulte [Retenção de longo prazo](sql-database-long-term-retention.md).

> [!IMPORTANT]
> Se você excluir o SQL Server do Azure que hospeda bancos de dados SQL, todos os pools de banco de dados elásticos e os bancos que pertencem ao servidor também serão excluídos. Eles não podem ser recuperados. Não é possível restaurar um servidor excluído. Mas se você configurou a retenção de longo prazo, os backups dos bancos de dados com EPD não serão excluídos e esses bancos de dados poderão ser restaurados.

## <a name="encrypted-backups"></a>Backups criptografados

Se o banco de dados for criptografado com TDE, os backups serão criptografados automaticamente em repouso, incluindo backups LTR. Quando a TDE está habilitada para um banco de dados SQL do Azure, os backups também são criptografados. Todos os novos bancos de dados SQL do Azure são configurados com TDE habilitada por padrão. Para obter mais informações sobre a TDE, confira [Transparent Data Encryption com o Banco de Dados SQL do Azure](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="backup-integrity"></a>Integridade do backup

Em uma base contínua, a equipe de engenharia do banco de dados SQL do Azure testa automaticamente a restauração de backups automatizados de banco de dados de bancos de dados colocados em servidores lógicos e pools de bancos elásticos. (Esse teste não está disponível na instância gerenciada.) Na restauração pontual, os bancos de dados também recebem verificações de integridade do DBCC CHECKDB.

A instância gerenciada usa o backup `CHECKSUM` inicial automático com bancos de dados restaurados `RESTORE` com o comando nativo ou com o serviço de migração de data do Azure após a conclusão da migração.

Os problemas encontrados durante a verificação de integridade resultarão em um alerta para a equipe de engenharia. Para obter mais informações, consulte [integridade de dados no banco de dados SQL do Azure](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

## <a name="compliance"></a>Conformidade

Quando você migra seu banco de dados de uma camada de serviço baseada em DTU para uma camada de serviço baseada em vCore, a retenção de PITR é preservada para garantir que a política de recuperação de dados do aplicativo não seja comprometida. Se a retenção padrão não atender aos seus requisitos de conformidade, você poderá alterar o período de retenção do PITR usando o PowerShell ou a API REST. Para obter mais informações, consulte [alterar o período de retenção do backup PITR](#change-the-pitr-backup-retention-period).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-pitr-backup-retention-period"></a>Alterar o período de retenção de backup PITR

Você pode alterar o período de retenção do backup PITR padrão usando o portal do Azure, o PowerShell ou a API REST. Os exemplos a seguir ilustram como alterar a retenção de PITR para 28 dias.

> [!WARNING]
> Se você reduzir o período de retenção atual, todos os backups existentes anteriores ao novo período de retenção não estarão mais disponíveis. Se você aumentar o período de retenção atual, o banco de dados SQL manterá os backups existentes até que o final do período de retenção mais longo seja atingido.

> [!NOTE]
> Essas APIs afetarão apenas o período de retenção do PITR. Se você configurou EPD para seu banco de dados, ele não será afetado. Para obter informações sobre como alterar períodos de retenção EPD, consulte [retenção de longo prazo](sql-database-long-term-retention.md).

### <a name="change-the-pitr-backup-retention-period-by-using-the-azure-portal"></a>Alterar o período de retenção de backup PITR usando o portal do Azure

Para alterar o período de retenção de backup PITR usando o portal do Azure, vá para o objeto de servidor cujo período de retenção você deseja alterar no Portal. Em seguida, selecione a opção apropriada com base no objeto de servidor que você está modificando.

#### <a name="single-database-and-elastic-database-pools"></a>[Banco de dados individual e pools de banco de dados elástico](#tab/single-database)

As alterações na retenção de backup PITR para bancos de dados SQL individuais do Azure são feitas no nível do servidor. As alterações feitas no nível do servidor se aplicam a bancos de dados no servidor. Para alterar a retenção de PITR de um servidor de banco de dados SQL do Azure da portal do Azure, vá para a folha visão geral do servidor. Selecione **gerenciar backups** no painel esquerdo e, em seguida, selecione **Configurar retenção** na parte superior da tela:

![Alterar a retenção de PITR, nível de servidor](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="managed-instance"></a>[Instância gerenciada](#tab/managed-instance)

Alterações na retenção de backup PITR para instâncias gerenciadas do banco de dados SQL são feitas em um nível de banco de dados individual. Para alterar a retenção de backup PITR de um banco de dados de instância do portal do Azure, vá para a folha visão geral do banco de dados individual. Em seguida, selecione **Configurar retenção de backup** na parte superior da tela:

![Alterar a retenção de PITR, instância gerenciada](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

---

### <a name="change-the-pitr-backup-retention-period-by-using-powershell"></a>Alterar o período de retenção de backup PITR usando o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo AzureRM do PowerShell ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para obter mais informações, consulte [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo AZ são substancialmente idênticos aos dos módulos AzureRm.

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-the-pitr-backup-retention-period-by-using-the-rest-api"></a>Alterar o período de retenção de backup PITR usando a API REST

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
- Obtenha mais informações sobre como [restaurar um banco de dados para um ponto no tempo usando o portal do Azure](sql-database-recovery-using-backups.md).
- Obtenha mais informações sobre como [restaurar um banco de dados para um ponto no tempo usando o PowerShell](scripts/sql-database-restore-database-powershell.md).
- Para obter informações sobre como configurar, gerenciar e restaurar de retenção de longo prazo de backups automatizados no armazenamento de BLOBs do Azure usando o portal do Azure, consulte [gerenciar retenção de backup de longo prazo usando o portal do Azure](sql-database-long-term-backup-retention-configure.md).
- Para obter informações sobre como configurar, gerenciar e restaurar de retenção de longo prazo de backups automatizados no armazenamento de BLOBs do Azure usando o PowerShell, consulte [gerenciar a retenção de backup de longo prazo usando o PowerShell](sql-database-long-term-backup-retention-configure.md).
