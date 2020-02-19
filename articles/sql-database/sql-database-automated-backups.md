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
ms.openlocfilehash: 16ee8c1e271f0aa3e6565322f9a4a422dd90b8b8
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461758"
---
# <a name="automated-backups"></a>Backups automatizados

O banco de dados SQL cria automaticamente os backups de banco de dados que são mantidos durante o período de retenção configurado e usa o [armazenamento com redundância geográfica com acesso de leitura do Azure (ra-grs)](../storage/common/storage-redundancy.md) para garantir que eles sejam preservados, mesmo que o Data Center não esteja disponível. Esses backups são criados automaticamente. Os backups de banco de dados são uma parte essencial de qualquer estratégia de recuperação de desastre e continuidade dos negócios, porque eles protegem seus dados contra exclusão ou corrupção acidentais. Se suas regras de segurança exigirem que seus backups estejam disponíveis por um longo período de tempo (até 10 anos), você poderá configurar uma [retenção de longo prazo](sql-database-long-term-retention.md) em bancos de dados singleton e pools elásticos.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>O que é um backup de banco de dados SQL

O banco de dados SQL usa a tecnologia SQL Server para criar [backups completos](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) toda semana, [backups diferenciais](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) a cada 12 horas e [backups de log de transações](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) a cada 5-10 minutos. Os backups são armazenados em [blobs de armazenamento ra-grs](../storage/common/storage-redundancy.md) que são replicados para um [Data Center emparelhado](../best-practices-availability-paired-regions.md) para proteção contra uma interrupção Data Center. Quando você restaura um banco de dados, o serviço descobre quais backups completos, diferenciais e de log de transações precisam ser restaurados.

Use esses backups para:

- **Restaure um banco de dados existente para um ponto no tempo no passado** dentro do período de retenção usando o portal do Azure, Azure PowerShell, CLI do Azure ou a API REST. Em um banco de dados individual e pools elásticos, essa operação criará um novo banco de dados no mesmo servidor que o banco de dados original. No Instância Gerenciada, essa operação pode criar uma cópia do banco de dados ou o mesmo Instância Gerenciada ou diferente na mesma assinatura.
- **Restaure um banco de dados excluído para a hora em que ele foi excluído** ou a qualquer momento dentro do período de retenção. O banco de dados excluído só pode ser restaurado no mesmo servidor lógico ou Instância Gerenciada em que o banco de dados original foi criado.
- **Restaurar um banco de dados para outra região geográfica**. A restauração geográfica permite a recuperação de um desastre geográfico quando você não consegue acessar o servidor nem o banco de dados. Ele cria um novo banco de dados em qualquer servidor existente do mundo.
- **Restaure um banco de dados de um backup específico de longo prazo** no Banco de Dados Individual ou pool elástico se o banco de dados tiver sido configurado com uma EPD (política de retenção de longo prazo). EPD permite que você restaure uma versão antiga do banco de dados usando [o portal do Azure](sql-database-long-term-backup-retention-configure.md#using-azure-portal) ou [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#using-powershell) para atender a uma solicitação de conformidade ou para executar uma versão antiga do aplicativo. Para obter mais informações, consulte [Retenção de longo prazo](sql-database-long-term-retention.md).
- Para executar uma restauração, consulte [Restaurar um banco de dados de backups](sql-database-recovery-using-backups.md).

> [!NOTE]
> No armazenamento do Azure, o termo *replicação* refere-se a copiar arquivos de uma localização para outra. *Replicação de banco de dados* do SQL significa manter vários bancos de dados secundários sincronizados com um banco de dados primário.

Você pode experimentar algumas dessas operações usando os seguintes exemplos:

| | O Portal do Azure | Azure PowerShell |
|---|---|---|
| Alterar retenção de backup | [Banco de Dados Individual](sql-database-automated-backups.md?tabs=managed-instance#change-pitr-backup-retention-period-using-azure-portal) <br/> [Instância Gerenciada](sql-database-automated-backups.md?tabs=managed-instance#change-pitr-backup-retention-period-using-azure-portal) | [Banco de Dados Individual](sql-database-automated-backups.md#change-pitr-backup-retention-period-using-powershell) <br/>[Instância Gerenciada](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| Alterar retenção de backup de longo prazo | [Banco de dados individual](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Instância Gerenciada-N/A  | [Banco de Dados Individual](sql-database-long-term-backup-retention-configure.md)<br/>Instância Gerenciada-N/A  |
| Restaurar o banco de dados do ponto no tempo | [Banco de dados individual](sql-database-recovery-using-backups.md#point-in-time-restore) | [Banco de dados individual](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Instância Gerenciada](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| Restaurar banco de dados excluído | [Banco de dados individual](sql-database-recovery-using-backups.md) | [Banco de dados individual](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Instância Gerenciada](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Restaurar banco de dados do armazenamento de BLOBs do Azure | Banco de dados único-N/A <br/>Instância Gerenciada-N/A  | Banco de dados único-N/A <br/>[Instância Gerenciada](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |


## <a name="backup-frequency"></a>Frequência de backup

### <a name="point-in-time-restore"></a>Restauração em um momento determinado

O Banco de Dados SQL permite o autoatendimento para PITR (Recuperação Pontual) ao criar automaticamente o backup completo, backups diferenciais e backups de log de transações. Os backups completos de banco de dados são criados semanalmente, os backups diferenciais de banco de dados geralmente são criados a cada 12 horas e os backups de log de transações geralmente são criados a cada 5 a 10 minutos, com a frequência baseada no tamanho da computação e na quantidade de atividade do banco de dados. O primeiro backup completo é agendado imediatamente após a criação de um banco de dados. Normalmente ele é concluído em 30 minutos, mas pode levar mais tempo quando o banco de dados tem um tamanho significativo. Por exemplo, o backup inicial pode levar mais tempo para um banco de dados restaurado ou uma cópia do banco de dados. Após o primeiro backup completo, todos os outros backups são agendados automaticamente e gerenciados de forma silenciosa em segundo plano. O tempo exato de todos os backups de banco de dados é determinado pelo serviço do Banco de Dados SQL, pois ele equilibra a carga de trabalho geral do sistema. Você não pode alterar ou desabilitar os trabalhos de backup.

Os backups do PITR são protegidos com armazenamento com redundância geográfica. Para obter mais informações, consulte [redundância de armazenamento do Azure](../storage/common/storage-redundancy.md).

Para obter mais informações, consulte [Restauração pontual](sql-database-recovery-using-backups.md#point-in-time-restore)

### <a name="long-term-retention"></a>Retenção de longo prazo

Bancos de dados individuais e em pool oferecem a opção de configurar LTR (retenção de longo prazo) de backups completos por até 10 anos no Armazenamento de Blobs do Azure. Quando a política de LTR está habilitada, os backups completos semanais são copiados automaticamente para um contêiner de armazenamento de RA-GRS diferente. Para atender a diferentes requisitos de conformidade, é possível selecionar diferentes períodos de retenção para backups semanais, mensais e/ou anuais. O consumo do armazenamento depende da frequência selecionada para os backups e dos períodos de retenção. Você pode usar a [Calculadora de preços de LTR](https://azure.microsoft.com/pricing/calculator/?service=sql-database) para estimar o custo do armazenamento de LTR.

Como PITR, os backups EPD são protegidos com armazenamento com redundância geográfica. Para obter mais informações, consulte [redundância de armazenamento do Azure](../storage/common/storage-redundancy.md).

Para obter mais informações, confira [Retenção de backup de longo prazo](sql-database-long-term-retention.md).

## <a name="backup-storage-consumption"></a>Consumo de armazenamento de backup 

Para bancos de dados individuais, o uso de armazenamento de backup total é calculado da seguinte maneira:   
`Total backup storage size = (size of full backups + size of differential backups + size of log backups) – database size`.

Para pools elásticos, o tamanho total do armazenamento de backup é agregado no nível do pool e é calculado da seguinte maneira:   
`Total backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - allocated pool data storage`. 

Os backups anteriores ao período de retenção são limpos automaticamente com base em seu carimbo de data/hora. Como os backups diferenciais e backups de log exigem um backup completo anterior para serem úteis, eles são limpos juntos em partes semanais. 

O banco de dados SQL do Azure calculará o armazenamento de backup total na retenção como um valor cumulativo. A cada hora, esse valor é relatado para o pipeline de cobrança do Azure, que é responsável por agregar esse uso por hora para calcular seu consumo no final de cada mês. Depois que o banco de dados é Descartado, o consumo diminui como a idade dos backups. Depois que os backups se tornarem mais antigos que o período de retenção, a cobrança será interrompida. 

   > [!IMPORTANT]
   > Os backups de um banco de dados são retidos durante o período de retenção especificado, mesmo que o banco de dados tenha sido descartado. Embora a remoção e a recriação de um banco de dados muitas vezes possam economizar em custos de armazenamento e de computação, isso poderá aumentar os custos de armazenamento de backup à medida que mantivermos um backup para o período de retenção especificado (que é de sete dias no mínimo) para cada banco de dados descartado, sempre que for retirado. 



### <a name="monitor-consumption"></a>Monitorar o consumo

Cada tipo de backup (completo, diferencial e log) é relatado na folha de monitoramento do banco de dados como uma métrica separada. O diagrama a seguir mostra como monitorar o consumo de armazenamento de backups de um único banco de dados. Este recurso não está disponível no momento para instâncias gerenciadas.

![Monitore o consumo de backup de banco de dados na folha monitoramento de banco de dados do portal do Azure](media/sql-database-automated-backup/backup-metrics.png)

### <a name="fine-tune-the-backup-storage-consumption"></a>Ajuste fino do consumo de armazenamento de backup

O consumo de armazenamento de backup excessivo dependerá da carga de trabalho e do tamanho dos bancos de dados individuais. Você pode considerar a implementação de algumas das técnicas de ajuste a seguir para reduzir ainda mais o consumo de armazenamento de backup:

* Reduza o [período de retenção de backup](#change-pitr-backup-retention-period-using-azure-portal) para o mínimo possível para suas necessidades.
* Evite executar operações de gravação grandes com mais frequência do que o necessário, como recompilações de índice.
* Para operações de carregamento de dados grandes, considere o uso de [índices columnstore clusterizados](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes), reduza o número de índices não clusterizados e considere também as operações de carregamento em massa com a contagem de linhas em cerca de 1 milhão.
* Na camada de serviço Uso Geral, o armazenamento de dados provisionado é mais barato do que o preço do armazenamento de backup excessivo, devido a quais clientes com altos custos de armazenamento de backup contínuos podem levar em consideração o aumento do armazenamento de dados para salvar no armazenamento de backup.
* Use TempDB em sua lógica ETL para armazenar resultados temporários, em vez de tabelas permanentes (aplicável somente à instância gerenciada).
* Considere a desativação da criptografia TDE para bancos de dados que não contenham informações confidenciais (bancos de dado de teste ou desenvolvimento, por exemplo). Os backups de bancos de dados não criptografados normalmente são compactados com uma taxa de compactação mais alta.

> [!IMPORTANT]
> Para cargas de trabalho analíticas, data mart \ data warehouse é altamente recomendável usar [índices columnstore clusterizados](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes), reduzir o número de índices não clusterizados e também considerar operações de carregamento em massa com contagem de linhas em cerca de 1 milhão para reduzir o consumo de armazenamento de backup em excesso.


## <a name="storage-costs"></a>Custos de armazenamento

O preço do armazenamento varia se você estiver usando o modelo de DTU ou o modelo vCore. 

### <a name="dtu-model"></a>Modelo de DTU

Não há nenhum custo adicional para o armazenamento de backup para bancos de dados e pools elásticos usando o modelo de DTU. 

### <a name="vcore-model"></a>Modelo vCore

Para bancos de dados individuais, um valor de armazenamento de backup mínimo igual a 100% do tamanho do banco de dados é fornecido sem custo adicional. Para pools elásticos e instâncias gerenciadas, um valor de armazenamento de backup mínimo igual a 100% do armazenamento de dados alocado para o tamanho do pool ou da instância, respectivamente, é fornecido sem nenhum custo adicional. O consumo adicional de armazenamento de backup será cobrado em GB/mês. Esse consumo adicional dependerá da carga de trabalho e do tamanho dos bancos de dados individuais.

O BD SQL do Azure calculará o armazenamento de backup total na retenção como um valor cumulativo. A cada hora, esse valor é relatado para o pipeline de cobrança do Azure, que é responsável por agregar esse uso por hora para obter seu consumo no final de cada mês. Depois que o banco de dados é Descartado, reduzimos o consumo como a idade dos backups. Quando eles se tornarem mais antigos do que o período de retenção, a cobrança será interrompida. Como todos os backups de log e backups diferenciais são mantidos por todo o período de retenção, os bancos de dados que são muito modificados terão encargos de backup maiores. 

Vamos supor que o banco de dados acumulau 744 GB de armazenamento de backup e esse valor permanece constante em um mês inteiro. Para converter esse consumo de armazenamento cumulativo em um uso por hora, dividiremos-o por 744,0 (31 dias por mês * 24 horas por dia). Portanto, o BD SQL relatará que o banco de dados consumiu 1 GB de backup PITR a cada hora. A cobrança do Azure agregará isso e mostrará um uso de 744 GB para o mês inteiro e o custo com base na taxa de $/GB/mo em sua região. 

Agora, um exemplo mais complexo. Suponha que o banco de dados tenha sua retenção aumentada para 14 dias no meio do mês e isso (hipotéticomente) resulta na duplicação total do armazenamento de backup para 1488 GB. O BD SQL relataria 1 GB de uso por horas 1-372 e, em seguida, relataria o uso como 2 GB para horas 373-744. Isso seria agregado para ser uma lista final de 1116 GB/mês. 

### <a name="monitor-costs"></a>Monitorar custos

Para entender os custos de armazenamento de backup, vá para **Gerenciamento de custos + cobrança** do portal do Azure, selecione **Gerenciamento de custos**e, em seguida, selecione análise de **custo**. Selecione a assinatura desejada como o **escopo**e, em seguida, filtre o período de tempo e o serviço em que você está interessado. 

Adicione um filtro para **nome do serviço**e, em seguida, escolha **banco de dados SQL** na lista suspensa. Use o filtro **subcategoria de medidor** para escolher o contador de cobrança para seu serviço. Para um banco de dados individual ou um pool elástico, escolha **pool único/elástico pitr armazenamento de backup**. Para uma instância gerenciada, escolha **mi pitr backup Storage**. As subcategorias de **armazenamento** e **computação** também podem ser interessantes, embora não estejam associadas aos custos de armazenamento de backup. 

![Análise de custo de armazenamento de backup](./media/sql-database-automated-backup/check-backup-storage-cost-sql-mi.png)


## <a name="backup-retention"></a>Retenção de backup

Todos os bancos de dados SQL do Azure (bancos de dados de instância única, em pool e gerenciados) têm um período de retenção de backup padrão de **sete** dias. Você pode [alterar o período de retenção de backup de até 35 dias](#change-pitr-backup-retention-period).

Se você excluir um banco de dados, o Banco de Dados SQL manterá os backups da mesma maneira que em um banco de dados online. Por exemplo, se você excluir um banco de dados Básico que tenha um período de retenção de sete dias, um backup de quatro dias será salvo por mais três dias.

Se você precisar manter os backups por mais tempo que o período máximo de retenção, modifique as propriedades de backup para adicionar um ou mais períodos de retenção de longo prazo para o banco de dados. Para obter mais informações, consulte [Retenção de longo prazo](sql-database-long-term-retention.md).

> [!IMPORTANT]
> Se você excluir o SQL Server do Azure que hospeda os bancos de dados SQL, todos os pools elásticos e bancos de dados que pertencem ao servidor também serão excluídos e não poderão ser recuperados. Você não pode restaurar um servidor excluído. Mas, se você tiver configurado a retenção de longo prazo, os backups dos bancos de dados com LTR não serão excluídos e esses bancos de dados poderão ser restaurados.

## <a name="encrypted-backups"></a>Backups criptografados

Quando o banco de dados é criptografado com TDE, os backups são criptografados automaticamente em repouso, incluindo os backups de LTR. Quando a TDE está habilitada para um banco de dados SQL do Azure, os backups também são criptografados. Todos os novos bancos de dados SQL do Azure são configurados com TDE habilitada por padrão. Para obter mais informações sobre a TDE, confira [Transparent Data Encryption com o Banco de Dados SQL do Azure](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="backup-integrity"></a>Integridade do backup

Em uma base contínua, a equipe de engenharia do banco de dados SQL do Azure testa automaticamente a restauração de backups automatizados de banco de dados de bancos que são colocados em servidores lógicos e pools elásticos (isso não está disponível no Instância Gerenciada). Na restauração pontual, os bancos de dados também recebem verificações de integridade usando DBCC CHECKDB.

Instância Gerenciada usa o backup inicial automático com `CHECKSUM` dos bancos de dados restaurados usando o comando `RESTORE` nativo ou o serviço de migração de serviços quando a migração é concluída.

Os problemas encontrados durante a verificação de integridade resultarão em um alerta para a equipe de engenharia. Para obter mais informações sobre a integridade dos dados no Banco de Dados SQL do Azure, confira [Integridade dos dados no Banco de Dados SQL do Azure](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

## <a name="compliance"></a>Conformidade

Quando você migra seu banco de dados de uma camada de serviço baseada em DTU para uma camada de serviço baseada em vCore, a retenção de PITR é preservada para garantir que a política de recuperação de dados do seu aplicativo não seja comprometida. Se a retenção padrão não atender aos seus requisitos de conformidade, você poderá alterar o período de retenção de PITR usando o PowerShell ou a API REST. Para obter mais informações, veja [Alterar o período de retenção de backup](#change-pitr-backup-retention-period).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="change-pitr-backup-retention-period"></a>Alterar o período de retenção de backup PITR

Você pode alterar o período de retenção de backup PITR padrão usando o portal do Azure, o PowerShell ou a API REST. Os exemplos a seguir ilustram como alterar a retenção de PITR para 28 dias.

> [!WARNING]
> Se você reduzir o período de retenção atual, todos os backups existentes mais antigos que o novo período de retenção não estarão mais disponíveis. Se você aumentar o período de retenção atual, o Banco de Dados SQL manterá os backups existentes até que o período de retenção mais longo seja atingido.

> [!NOTE]
> Essas APIs afetarão somente o período de retenção de PITR. Se você tiver configurado a LTR para o banco de dados, ela não será afetada. Para obter mais informações sobre como alterar o(s) período(s) de retenção de LTR, confira [Retenção de longo prazo](sql-database-long-term-retention.md).

### <a name="change-pitr-backup-retention-period-using-azure-portal"></a>Alterar o período de retenção de backup PITR usando portal do Azure

Para alterar o período de retenção de backup PITR usando o portal do Azure, navegue até o objeto de servidor cujo período de retenção você deseja alterar no portal e, em seguida, selecione a opção apropriada com base no objeto de servidor que você está modificando.

#### <a name="single-database--elastic-pools"></a>[Banco de dados individual e pools elásticos](#tab/single-database)

A alteração da retenção de backup PITR para bancos de dados SQL individuais do Azure é executada no nível do servidor. A alteração feita no nível do servidor se aplica a bancos de dados nesse servidor. Para alterar o PITR do servidor de banco de dados SQL do Azure de portal do Azure, navegue até a folha visão geral do servidor, clique em gerenciar backups no menu de navegação e, em seguida, clique em configurar retenção na barra de navegação.

![Alterar PITR no portal do Azure](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="managed-instance"></a>[Instância Gerenciada](#tab/managed-instance)

A alteração da retenção de backup PITR para instância gerenciada do banco de dados SQL é executada em um nível de banco de dados individual. Para alterar a retenção de backup do PITR de um banco de dados de instância do portal do Azure, navegue até a folha de visão geral do banco de dados individual e clique em configurar retenção de backup na barra de navegação.

![Alterar PITR no portal do Azure](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

---

### <a name="change-pitr-backup-retention-period-using-powershell"></a>Alterar o período de retenção de backup de PITR usando o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, consulte [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos.

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-pitr-retention-period-using-rest-api"></a>Alterar o período de retenção de PITR usando a API REST

#### <a name="sample-request"></a>Exemplo de solicitação

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

#### <a name="sample-response"></a>Exemplo de resposta

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

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

- Os backups de banco de dados são uma parte essencial de qualquer estratégia de recuperação de desastre e continuidade dos negócios, porque eles protegem seus dados contra exclusão ou corrupção acidentais. Para saber mais sobre as outras soluções de continuidade dos negócios do Banco de Dados SQL do Azure, consulte [Visão geral da continuidade dos negócios](sql-database-business-continuity.md).
- Para restaurar para um determinado ponto no tempo usando o Portal do Azure, consulte [Restaurar um banco de dados para um ponto no tempo usando o Portal do Azure](sql-database-recovery-using-backups.md).
- Para restaurar para um determinado ponto no tempo usando o PowerShell, consulte [Restaurar um banco de dados para um ponto no tempo usando o PowerShell](scripts/sql-database-restore-database-powershell.md).
- Para configurar, gerenciar e restaurar a retenção de longo prazo de backups automatizados no Armazenamento de Blobs do Azure usando o portal do Azure, consulte [Gerenciar retenção de backup de longo prazo usando o portal do Azure](sql-database-long-term-backup-retention-configure.md).
- Para configurar, gerenciar e restaurar de retenção de longo prazo de backups automatizados no armazenamento de BLOBs do Azure usando o PowerShell, consulte [gerenciar retenção de backup de longo prazo usando o PowerShell](sql-database-long-term-backup-retention-configure.md).
