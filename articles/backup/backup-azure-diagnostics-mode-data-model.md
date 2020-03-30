---
title: Modelo de dados de logs do Azure Monitor
description: Neste artigo, conheça os detalhes do modelo de dados do Azure Monitor Log Analytics para dados do Azure Backup.
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: d14634c5e317682462e77e0549f064c75059f15c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586369"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Modelo de dados do Log Analytics para dados de Backup do Azure

Use o modelo de dados do Log Analytics para criar alertas personalizados do Log Analytics.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
>
> Este modelo de dados está em referência ao Modo Diagnóstico Do Azure de envio de eventos diagnósticos para o Log Analytics (LA). Para saber o modelo de dados do novo modo específico de recursos, você pode consultar o seguinte artigo: [Data Model for Azure Backup Diagnostic Events](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)

## <a name="using-azure-backup-data-model"></a>Usando o modelo de dados de Backup do Azure

Você pode usar os seguintes campos fornecidos como parte do modelo de dados para criar visuais, consultas personalizadas e painel de acordo com seus requisitos.

### <a name="alert"></a>Alerta

Essa tabela fornece detalhes sobre campos relacionados ao alerta.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| AlertUniqueId_s |Texto |Identificador exclusivo do alerta gerado |
| AlertType_s |Texto |Tipo de alerta, por exemplo, Backup |
| AlertStatus_s |Texto |Status do alerta, por exemplo, Ativo |
| AlertOccurrenceDateTime_s |Data/hora |Data e hora em que o alerta foi criado |
| AlertSeverity_s |Texto |Gravidade do alerta, por exemplo, Crítico |
|AlertTimeToResolveInMinutes_s    | Número        |Tempo levado para resolver um alerta. Em branco para alertas ativos.         |
|AlertConsolidationStatus_s   |Texto         |Identifique se o alerta é um alerta consolidado ou não         |
|CountOfAlertsConsolidated_s     |Número         |Número de alertas consolidados se for um alerta consolidado          |
|AlertRaisedOn_s     |Texto         |Tipo de entidade em que o alerta é levantado         |
|AlertCode_s     |Texto         |Código para identificar exclusivamente um tipo de alerta         |
|RecommendedAction_s   |Texto         |Ação recomendada para resolver o alerta         |
| EventName_s |Texto |O nome do evento. Sempre AzureBackupCentralReport |
| BackupItemUniqueId_s |Texto |Identificador exclusivo do item backup associado ao alerta |
| SchemaVersion_s |Texto |Versão atual do esquema, por exemplo **V2** |
| State_s |Texto |Estado atual do objeto de alerta, por exemplo, Ativo, Excluído |
| BackupManagementType_s |Texto |Tipo de provedor para executar o backup, por exemplo, IaaSVM, FileFolder ao qual esse alerta pertence |
| OperationName |Texto |Nome da operação atual, por exemplo, alerta |
| Categoria |Texto |Categoria de dados de diagnóstico suspressionados para registros do Monitor do Azure. Sempre AzureBackupReport |
| Recurso |Texto |Este é o recurso para o qual os dados estão sendo coletados, ele mostra o nome do cofre dos Serviços de Recuperação |
| ProtectedContainerUniqueId_s |Texto |Identificador exclusivo do servidor protegido associado ao alerta (foi ProtectedServerUniqueId_s em V1)|
| VaultUniqueId_s |Texto |Identificador exclusivo do cofre protegido associado ao alerta |
| SourceSystem |Texto |Sistema de origem dos dados atuais - Azure |
| ResourceId |Texto |Identificador exclusivo para o recurso sobre quais dados são coletados. Por exemplo, um ID de recurso do cofre do Recovery Services |
| SubscriptionId |Texto |Identificador de assinatura do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceGroup |Texto |grupo de Recursos do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceProvider |Texto |Provedor de recursos para o qual os dados são coletados. Por exemplo, Microsoft.RecoveryServices |
| ResourceType |Texto |Tipo de recursos para o qual os dados são coletados. Por exemplo, Cofres |

### <a name="backupitem"></a>BackupItem

Esta tabela fornece detalhes sobre os campos relacionados ao item de backup.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| EventName_s |Texto |O nome do evento. Sempre AzureBackupCentralReport |  
| BackupItemUniqueId_s |Texto |Identificador único do item de backup |
| BackupItemId_s |Texto |Identificador do item de backup (Este campo é apenas para esquema v1) |
| BackupItemName_s |Texto |ID do item de backup |
| BackupItemFriendlyName_s |Texto |Nome amigável do item de backup |
| BackupItemType_s |Texto |Tipo de item de backup, por exemplo, VM, FileFolder |
| BackupItemProtectionState_s |Texto |Estado de proteção do item de backup |
| BackupItemAppVersion_s |Texto |Versão do aplicativo do item de backup |
| ProtectionState_s |Texto |Estado de proteção atual do item de backup, por exemplo, Protegido, ProtectionStopped |
| ProtectionGroupName_s |Texto | Nome do Grupo de Proteção em que o Item de Backup é protegido, para DPM SC e MABS, se aplicável|
| SecondaryBackupProtectionState_s |Texto |Se a proteção secundária está habilitada para o item de backup|
| SchemaVersion_s |Texto |Versão do esquema, por exemplo, **V2** |
| State_s |Texto |Estado do objeto do item de backup, por exemplo, Ativo, Excluído |
| BackupManagementType_s |Texto |Tipo de provedor para executar o backup, por exemplo, IaaSVM, FileFolder para qual esse item de backup pertence |
| OperationName |Texto |Nome da operação, por exemplo, BackupItem |
| Categoria |Texto |Categoria de dados de diagnóstico suspressionados para registros do Monitor do Azure. Sempre AzureBackupReport |
| Recurso |Texto |Recursos para os quais dados são coletados, por exemplo, nome do cofre dos serviços de recuperação |
| SourceSystem |Texto |Sistema de origem dos dados atuais - Azure |
| ResourceId |Texto |ID de recursos para dados que estão sendo coletados, por exemplo, ID de recurso do cofre do Recovery Services |
| SubscriptionId |Texto |Identificador de assinatura do recurso (por exemplo cofre de Serviços de Recuperação) para dados sendo coletados |
| ResourceGroup |Texto |grupo de Recursos do recurso (por exemplo cofre de Serviços de Recuperação) para dados sendo coletados |
| ResourceProvider |Texto |fornecedor de Recurso para dados sendo coletados, por exemplo Microsoft.RecoveryServices |
| ResourceType |Texto |Tipo de recurso para os dados que estão sendo coletados, por exemplo, os cofres |

### <a name="backupitemassociation"></a>BackupItemAssociation

Esta tabela fornece detalhes sobre associações de itens de backup com várias entidades.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| EventName_s |Texto |Este campo representa o nome desse evento, é sempre AzureBackupCentralReport |  
| BackupItemUniqueId_s |Texto |ID exclusivo do item de backup |
| SchemaVersion_s |Texto |Este campo denota a versão atual do esquema, é **V2** |
| State_s |Texto |Estado atual do objeto de associação de item de backup, por exemplo, Ativo, Excluído |
| BackupManagementType_s |Texto |Tipo de fornecedor para servidor fazendo trabalho de backup, por exemplo, IaaSVM, FileFolder |
| BackupItemSourceSize_s |Texto | Tamanho frontal do item de backup |
| BackupManagementServerUniqueId_s |Texto | Campo para identificar exclusivamente o servidor de gerenciamento de backup que o item de backup está protegido, se aplicável |
| Categoria |Texto |Este campo representa a categoria dos dados de diagnóstico enviados por push para o Log Analytics, é AzureBackupReport |
| OperationName |Texto |Este campo representa o nome da operação atual - BackupItemAssociation |
| Recurso |Texto |Este é o recurso para o qual os dados estão sendo coletados, ele mostra o nome do cofre dos Serviços de Recuperação |
| ProtectedContainerUniqueId_s |Texto |Identificador exclusivo do servidor protegido associado ao item de backup (foi ProtectedServerUniqueId_s em V1) |
| VaultUniqueId_s |Texto |Identificador exclusivo do cofre que contém o item de backup |
| SourceSystem |Texto |Sistema de origem dos dados atuais - Azure |
| ResourceId |Texto |Identificador de recurso para os dados sendo coletados. Por exemplo, ID de recurso do cofre do Recovery Services |
| SubscriptionId |Texto |Identificador de assinatura do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados estão sendo coletados |
| ResourceGroup |Texto |grupo de Recursos do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados estão sendo coletados |
| ResourceProvider |Texto |fornecedor de Recurso para dados sendo coletados, por exemplo Microsoft.RecoveryServices |
| ResourceType |Texto |Tipo de recurso para os dados que estão sendo coletados, por exemplo, os cofres |

### <a name="backupmanagementserver"></a>BackupManagementServer

Esta tabela fornece detalhes sobre associações de itens de backup com várias entidades.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
|BackupManagementServerName_s     |Texto         |Nome do servidor de gerenciamento de backup        |
|AzureBackupAgentVersion_s     |Texto         |Versão do Azure Backup Agent no servidor de gerenciamento de backup          |
|BackupManagementServerVersion_s     |Texto         |Versão do servidor de gerenciamento de backup|
|BackupManagementServerOSVersion_s    |Texto            |Versão do SISTEMA OPERACIONAL do Servidor de Gerenciamento de Backup|
|BackupManagementServerType_s     |Texto         |Tipo do servidor de gerenciamento de backup, como MABS, SC DPM|
|BackupManagementServerUniqueId_s     |Texto         |Campo para identificar exclusivamente o servidor de gerenciamento de backup       |
| SourceSystem |Texto |Sistema de origem dos dados atuais - Azure |
| ResourceId |Texto |Identificador de recurso para os dados sendo coletados. Por exemplo, ID de recurso do cofre do Recovery Services |
| SubscriptionId |Texto |Identificador de assinatura do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados estão sendo coletados |
| ResourceGroup |Texto |grupo de Recursos do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados estão sendo coletados |
| ResourceProvider |Texto |fornecedor de Recurso para dados sendo coletados, por exemplo Microsoft.RecoveryServices |
| ResourceType |Texto |Tipo de recurso para os dados que estão sendo coletados, por exemplo, os cofres |

### <a name="job"></a>Trabalho

Esta tabela fornece detalhes sobre campos relacionados ao trabalho.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| EventName_s |Texto |O nome do evento. Sempre AzureBackupCentralReport |
| BackupItemUniqueId_s |Texto |Identificador único do item de backup |
| SchemaVersion_s |Texto |Versão do esquema, por exemplo, **V2** |
| State_s |Texto |Estado atual do objeto de trabalho, por exemplo, Ativo, Excluído |
| BackupManagementType_s |Texto |Tipo de fornecedor para servidor fazendo trabalho de backup, por exemplo, IaaSVM, FileFolder |
| OperationName |Texto |Este campo representa o nome da operação atual - Trabalho |
| Categoria |Texto |Este campo representa a categoria de dados de diagnóstico sustais empurrados para logs do Azure Monitor, é o AzureBackupReport |
| Recurso |Texto |Este é o recurso para o qual os dados estão sendo coletados, ele mostra o nome do cofre dos Serviços de Recuperação |
| ProtectedServerUniqueId_s |Texto |Identificador exclusivo do servidor protegido associado ao trabalho |
| ProtectedContainerUniqueId_s |Texto | ID exclusivo para identificar o contêiner protegido em que o trabalho é executado |
| VaultUniqueId_s |Texto |Identificador exclusivo do cofre protegido |
| JobOperation_s |Texto |Operação para a qual o trabalho é executado, por exemplo, backup, restauração, backup de configuração |
| JobStatus_s |Texto |Status do trabalho concluído, por exemplo, Concluído, Com Falha |
| JobFailureCode_s |Texto |Cadeia de caracteres de código de falha pela qual a falha no trabalho ocorreu |
| JobStartDateTime_s |Data/hora |Data e hora em que o trabalho iniciou a execução |
| BackupStorageDestination_s |Texto |Destino do armazenamento de backup, por exemplo, Nuvem, Disco  |
| AdHocOrScheduledJob_s |Texto | Campo para especificar se o trabalho é Ad Hoc ou Agendado |
| JobDurationInSecs_s | Número |Duração total do trabalho em segundos |
| DataTransferredInMB_s | Número |Dados transferidos em MB para este trabalho|
| JobUniqueId_g |Texto |Identificação única para identificar o trabalho |
| RecoveryJobDestination_s |Texto | Destino de um trabalho de recuperação, onde os dados são recuperados |
| RecoveryJobRPDateTime_s |Datetime | A data, hora em que o ponto de recuperação que está sendo recuperado foi criado |
| RecoveryJobRPLocation_s |Texto | O local onde o ponto de recuperação que está sendo recuperado foi armazenado|
| SourceSystem |Texto |Sistema de origem dos dados atuais - Azure |
| ResourceId |Texto |Identificador de recurso para os dados sendo coletados. Por exemplo, ID de recurso do cofre do Recovery Services|
| SubscriptionId |Texto |Identificador de assinatura do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceGroup |Texto |grupo de Recursos do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceProvider |Texto |Provedor de recursos para o qual os dados são coletados. Por exemplo, Microsoft.RecoveryServices |
| ResourceType |Texto |Tipo de recursos para o qual os dados são coletados. Por exemplo, Cofres |

### <a name="policy"></a>Política

Esta tabela fornece detalhes sobre campos relacionados a políticas.

| Campo | Tipo de Dados | Versões aplicáveis | Descrição |
| --- | --- | --- | --- |
| EventName_s |Texto ||Este campo representa o nome desse evento, é sempre AzureBackupCentralReport |
| SchemaVersion_s |Texto ||Este campo denota a versão atual do esquema, é **V2** |
| State_s |Texto ||Estado atual do objeto de política, por exemplo, Ativo, Excluído |
| BackupManagementType_s |Texto ||Tipo de fornecedor para servidor fazendo trabalho de backup, por exemplo, IaaSVM, FileFolder |
| OperationName |Texto ||Este campo representa o nome da operação atual - Política |
| Categoria |Texto ||Este campo representa a categoria de dados de diagnóstico sustais empurrados para logs do Azure Monitor, é o AzureBackupReport |
| Recurso |Texto ||Este é o recurso para o qual os dados estão sendo coletados, ele mostra o nome do cofre dos Serviços de Recuperação |
| PolicyUniqueId_g |Texto ||ID único para identificar a política |
| PolicyName_s |Texto ||Nome da política definido |
| BackupFrequency_s |Texto ||Frequência com que os backups são executados, por exemplo, diária, semanal |
| BackupTimes_s |Texto ||Data e hora quando os backups são agendados |
| BackupDaysOfTheWeek_s |Texto ||Dias da semana nos quais os backups foram agendados |
| RetentionDuration_s |Número Inteiro ||Duração de retenção para backups configurados |
| DailyRetentionDuration_s |Número Inteiro ||Duração total da retenção em dias para backups configurados |
| DailyRetentionTimes_s |Texto ||Data e hora quando a retenção diária foi configurada |
| WeeklyRetentionDuration_s |Número Decimal ||Duração total da retenção em semanas para backups configurados |
| WeeklyRetentionTimes_s |Texto ||Data e hora quando a retenção semanal foi configurada |
| WeeklyRetentionDaysOfTheWeek_s |Texto ||Dias da semana selecionados para a retenção semanal |
| MonthlyRetentionDuration_s |Número Decimal ||Duração total da retenção em meses para backups configurados |
| MonthlyRetentionTimes_s |Texto ||Data e hora quando a retenção mensal foi configurada |
| MonthlyRetentionFormat_s |Texto ||Tipo de configuração de retenção mensal, por exemplo, diário, semanal |
| MonthlyRetentionDaysOfTheWeek_s |Texto ||Dias da semana selecionados para a retenção mensal |
| MonthlyRetentionWeeksOfTheMonth_s |Texto ||Semanas do mês quando a retenção mensal é configurada, por exemplo, Primeira, Última, etc. |
| YearlyRetentionDuration_s |Número Decimal ||Duração total da retenção em ano para backups configurados |
| YearlyRetentionTimes_s |Texto ||Data e hora quando a retenção anual foi configurada |
| YearlyRetentionMonthsOfTheYear_s |Texto ||Meses do ano selecionados para a retenção anual |
| YearlyRetentionFormat_s |Texto ||Tipo de configuração de retenção anual, por exemplo, diário, semanal | |
| YearlyRetentionDaysOfTheMonth_s |Texto ||Dias do mês selecionados para a retenção anual |
| SynchronisationFrequencyPerDay_s |Número Inteiro |v2|Número de vezes em um dia um backup de arquivo é sincronizado para SC DPM e MABS |
| DiffBackupFormat_s |Texto |v2|Formato para backups diferenciais para SQL no backup do Azure VM |
| DiffBackupTime_s |Hora |v2|Tempo para backups diferenciais para SQL no Backup Azure VM|
| DiffBackupRetentionDuration_s |Número Decimal |v2|Duração da retenção para backups diferenciais para SQL no Backup Azure VM|
| LogBackupFrequency_s |Número Decimal |v2|Freqüência para backups de log para SQL|
| LogBackupRetentionDuration_s |Número Decimal |v2|Duração da retenção dos backups log para SQL no backup do Azure VM|
| DiffBackupDaysofTheWeek_s |Texto |v2|Dias da semana para backups diferenciais para SQL no Azure VM Backup|
| SourceSystem |Texto ||Sistema de origem dos dados atuais - Azure |
| ResourceId |Texto ||Identificador de recurso para os dados sendo coletados. Por exemplo, ID de recurso do cofre do Recovery Services |
| SubscriptionId |Texto ||Identificador de assinatura do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceGroup |Texto ||grupo de Recursos do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceProvider |Texto ||Provedor de recursos para o qual os dados são coletados. Por exemplo, Microsoft.RecoveryServices |
| ResourceType |Texto ||Tipo de recursos para o qual os dados são coletados. Por exemplo, Cofres |

### <a name="policyassociation"></a>PolicyAssociation

Esta tabela fornece detalhes sobre associações de políticas com várias entidades.

| Campo | Tipo de Dados | Versões aplicáveis | Descrição |
| --- | --- | --- | --- |
| EventName_s |Texto ||Este campo representa o nome desse evento, é sempre AzureBackupCentralReport |
| SchemaVersion_s |Texto ||Este campo denota a versão atual do esquema, é **V2** |
| State_s |Texto ||Estado atual do objeto de política, por exemplo, Ativo, Excluído |
| BackupManagementType_s |Texto ||Tipo de fornecedor para servidor fazendo trabalho de backup, por exemplo, IaaSVM, FileFolder |
| OperationName |Texto ||Este campo representa o nome da operação atual - PolicyAssociation |
| Categoria |Texto ||Este campo representa a categoria de dados de diagnóstico sustais empurrados para logs do Azure Monitor, é o AzureBackupReport |
| Recurso |Texto ||Este é o recurso para o qual os dados estão sendo coletados, ele mostra o nome do cofre dos Serviços de Recuperação |
| PolicyUniqueId_g |Texto ||ID único para identificar a política |
| VaultUniqueId_s |Texto ||Identificação única do cofre ao qual esta política pertence |
| BackupManagementServerUniqueId_s |Texto |v2 |Campo para identificar exclusivamente o servidor de gerenciamento de backup que o item de backup está protegido, se aplicável        |
| SourceSystem |Texto ||Sistema de origem dos dados atuais - Azure |
| ResourceId |Texto ||Identificador de recurso para os dados sendo coletados. Por exemplo, ID de recurso do cofre do Recovery Services |
| SubscriptionId |Texto ||Identificador de assinatura do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceGroup |Texto ||grupo de Recursos do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceProvider |Texto ||Provedor de recursos para o qual os dados são coletados. Por exemplo, Microsoft.RecoveryServices |
| ResourceType |Texto ||Tipo de recursos para o qual os dados são coletados. Por exemplo, Cofres |

### <a name="protected-container"></a>Recipiente protegido

Esta tabela fornece campos básicos sobre recipientes protegidos. (Foi protegidoServidor em v1)

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| ProtectedContainerUniqueId_s |Texto | Campo para identificar exclusivamente um contêiner protegido |
| ProtectedContainerOSType_s |Texto |Tipo de SISTEMA OPERACIONAL do Recipiente Protegido |
| ProtectedContainerOSVersion_s |Texto |Versão do SISTEMA OPERACIONAL do recipiente protegido |
| AgentVersion_s |Texto |Número da versão do Agent Backup ou do Agente de Proteção (No caso do SC DPM e Do MABS) |
| BackupManagementType_s |Texto |Tipo de provedor para realizar backup. Por exemplo, IaaSVM, FileFolder |
| EntityState_s |Texto |Estado atual do objeto servidor protegido. Por exemplo, Ativo, Excluído |
| ProtectedContainerFriendlyName_s |Texto |Nome amigável do servidor protegido |
| ProtectedContainerName_s |Texto |Nome do contêiner protegido |
| ProtectedContainerWorkloadType_s |Texto |Tipo do recipiente protegido apoiado. Por exemplo, IaaSVMContainer |
| ProtectedContainerLocation_s |Texto |Se o Contêiner Protegido está localizado no local ou no Azure |
| ProtectedContainerType_s |Texto |Se o Contêiner Protegido é um servidor ou um contêiner |
| ProtectedContainerProtectionState_s'  |Texto |Estado de proteção do recipiente protegido |

### <a name="storage"></a>Armazenamento

Esta tabela fornece detalhes sobre campos relacionados ao armazenamento.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| CloudStorageInBytes_s |Número Decimal |Armazenamento de backup em nuvem usado por backups, calculado com base no valor mais recente (Este campo é apenas para esquema v1)|
| ProtectedInstances_s |Número Decimal |Número de instâncias protegidas utilizadas para calcular o armazenamento de front-end na cobrança, calculada baseada no valor mais recente |
| EventName_s |Texto |Este campo representa o nome desse evento, é sempre AzureBackupCentralReport |
| SchemaVersion_s |Texto |Este campo denota a versão atual do esquema, é **V2** |
| State_s |Texto |Estado atual do objeto de armazenamento, por exemplo, Ativo, Excluído |
| BackupManagementType_s |Texto |Tipo de fornecedor para servidor fazendo trabalho de backup, por exemplo, IaaSVM, FileFolder |
| OperationName |Texto |Este campo representa o nome da operação atual - Armazenamento |
| Categoria |Texto |Este campo representa a categoria de dados de diagnóstico sustais empurrados para logs do Azure Monitor, é o AzureBackupReport |
| Recurso |Texto |Este é o recurso para o qual os dados estão sendo coletados, ele mostra o nome do cofre dos Serviços de Recuperação |
| ProtectedServerUniqueId_s |Texto |ID exclusivo do servidor protegido para o qual o armazenamento é calculado |
| VaultUniqueId_s |Texto |O ID exclusivo do cofre para armazenamento é calculado |
| SourceSystem |Texto |Sistema de origem dos dados atuais - Azure |
| ResourceId |Texto |Identificador de recurso para os dados sendo coletados. Por exemplo, ID de recurso do cofre do Recovery Services |
| SubscriptionId |Texto |Identificador de assinatura do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceGroup |Texto |grupo de Recursos do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceProvider |Texto |Provedor de recursos para o qual os dados são coletados. Por exemplo, Microsoft.RecoveryServices |
| ResourceType |Texto |Tipo de recursos para o qual os dados são coletados. Por exemplo, Cofres |
| StorageUniqueId_s |Texto |ID exclusivo usado para identificar a entidade de armazenamento |
| StorageType_s |Texto |Tipo de armazenamento, por exemplo Nuvem, Volume, Disco |
| StorageName_s |Texto |Nome da entidade de armazenamento, por exemplo E:\ |
| StorageTotalSizeInGBs_s |Texto |Tamanho total do armazenamento, em GB, consumido pela entidade de armazenamento|

### <a name="storageassociation"></a>Associação de Armazenamento

Esta tabela fornece campos básicos relacionados ao armazenamento que conectam o armazenamento a outras entidades.

| Campo | Tipo de Dados | Descrição |
| --- | --- |  --- |
| StorageUniqueId_s |Texto |ID exclusivo usado para identificar a entidade de armazenamento |
| SchemaVersion_s |Texto |Este campo denota a versão atual do esquema, é **V2** |
| BackupItemUniqueId_s |Texto |ID exclusivo usado para identificar o item de backup relacionado à entidade de armazenamento |
| BackupManagementServerUniqueId_s |Texto |ID exclusivo usado para identificar o servidor de gerenciamento de backup relacionado à entidade de armazenamento|
| VaultUniqueId_s |Texto |ID exclusivo usado para identificar o cofre relacionado à entidade de armazenamento|
| StorageConsumedInMBs_s |Número|Tamanho do armazenamento consumido pelo item de backup correspondente no armazenamento correspondente |
| StorageAllocatedInMBs_s |Número |Tamanho do armazenamento alocado pelo item de backup correspondente no armazenamento correspondente do disco do tipo|

### <a name="vault"></a>Cofre

Esta tabela fornece detalhes sobre campos relacionados ao cofre.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| EventName_s |Texto |Este campo representa o nome desse evento, é sempre AzureBackupCentralReport |
| SchemaVersion_s |Texto |Este campo denota a versão atual do esquema, é **V2** |
| State_s |Texto |Estado atual do objeto do sofre, por exemplo, Ativo, Excluído |
| OperationName |Texto |Este campo representa o nome da operação atual - Cofre |
| Categoria |Texto |Este campo representa a categoria de dados de diagnóstico sustais empurrados para logs do Azure Monitor, é o AzureBackupReport |
| Recurso |Texto |Este é o recurso para o qual os dados estão sendo coletados, ele mostra o nome do cofre dos Serviços de Recuperação |
| VaultUniqueId_s |Texto |Identificação única do cofre |
| VaultName_s |Texto |Nome do cofre |
| AzureDataCenter_s |Texto |Data center onde o cofre está localizado |
| StorageReplicationType_s |Texto |Tipo de replicação de armazenamento para o cofre, por exemplo, GeoRedundant |
| SourceSystem |Texto |Sistema de origem dos dados atuais - Azure |
| ResourceId |Texto |Identificador de recurso para os dados sendo coletados. Por exemplo, ID de recurso do cofre do Recovery Services |
| SubscriptionId |Texto |Identificador de assinatura do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceGroup |Texto |grupo de Recursos do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceProvider |Texto |Provedor de recursos para o qual os dados são coletados. Por exemplo, Microsoft.RecoveryServices |
| ResourceType |Texto |Tipo de recursos para o qual os dados são coletados. Por exemplo, Cofres |

### <a name="backup-management-server"></a>Servidor de gerenciamento de backup

Esta tabela fornece campos básicos sobre servidores de gerenciamento de backup.

|Campo  |Tipo de Dados  | Descrição  |
|---------|---------|----------|
|BackupManagementServerName_s     |Texto         |Nome do servidor de gerenciamento de backup        |
|AzureBackupAgentVersion_s     |Texto         |Versão do Azure Backup Agent no servidor de gerenciamento de backup          |
|BackupManagementServerVersion_s     |Texto         |Versão do servidor de gerenciamento de backup|
|BackupManagementServerOSVersion_s     |Texto            |Versão do SISTEMA OPERACIONAL do Servidor de Gerenciamento de Backup|
|BackupManagementServerType_s     |Texto         |Tipo do servidor de gerenciamento de backup, como MABS, SC DPM|
|BackupManagementServerUniqueId_s     |Texto         |Campo para identificar exclusivamente o servidor de gerenciamento de backup       |

### <a name="preferredworkloadonvolume"></a>Carga de trabalho preferencialOnVolume

Esta tabela especifica a carga de trabalho com a que um volume está associado.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| StorageUniqueId_s |Texto |ID exclusivo usado para identificar a entidade de armazenamento |
| BackupItemType_s |Texto |As cargas de trabalho para as quais este volume é o armazenamento preferido|

### <a name="protectedinstance"></a>Instância protegida

Esta tabela fornece campos básicos relacionados a instâncias protegidas.

| Campo | Tipo de Dados |Versões aplicáveis | Descrição |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |Texto |v2|ID exclusivo usado para identificar o item de backup para VMs backup usando DPM, MABS|
| ProtectedContainerUniqueId_s |Texto |v2|ID exclusivo usado para identificar o recipiente protegido para tudo, exceto VMs backup usando DPM, MABS|
| ProtectedInstanceCount_s |Texto |v2|Contagem de instâncias protegidas para o item de backup associado ou contêiner protegido nesse período de data|

### <a name="recoverypoint"></a>RecoveryPoint

Esta tabela fornece campos relacionados a pontos de recuperação básicos.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| BackupItemUniqueId_s |Texto |ID exclusivo usado para identificar o item de backup para VMs backup usando DPM, MABS|
| OldestRecoveryPointTime_s |Texto |Data de data do ponto de recuperação mais antigo para o item de backup|
| OldestRecoveryPointLocation_s |Texto |Localização do ponto de recuperação mais antigo para o item de backup|
| LatestRecoveryPointTime_s |Texto |Data de data do último ponto de recuperação para o item de backup|
| LatestRecoveryPointLocation_s |Texto |Localização do último ponto de recuperação para o item de backup|

## <a name="sample-kusto-queries"></a>Amostra de consultas kusto

Abaixo estão algumas amostras para ajudá-lo a escrever consultas nos dados de backup do Azure que residem na tabela Azure Diagnostics:

- Todos os trabalhos de backup bem sucedidos

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Completed"
    ````

- Todos os trabalhos de backup fracassados

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Failed"
    ````

- Todos os trabalhos de backup bem-sucedidos do Azure VM

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "VM" and BackupManagementType_s == "IaaSVM"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

- Todos os trabalhos de backup de log sql bem-sucedidos

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s == "Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "SQLDataBase" and BackupManagementType_s == "AzureWorkload"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

- Todos os trabalhos bem sucedidos de agente de backup do Azure

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "FileFolder" and BackupManagementType_s == "MAB"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````
    
## <a name="next-steps"></a>Próximas etapas

Depois de revisar o modelo de dados, você pode começar a [criar consultas personalizadas](../azure-monitor/learn/tutorial-logs-dashboards.md) nos logs do Azure Monitor para construir seu próprio painel de controle.
