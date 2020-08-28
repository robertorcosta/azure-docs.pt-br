---
title: Modelo de dados para eventos de diagnóstico de backup do Azure
description: Esse modelo de dados está em referência ao modo específico de recurso de envio de eventos de diagnóstico para Log Analytics (LA).
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: adc1442b674b9a6e947ef65967a2c2f1359e7d8a
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89017576"
---
# <a name="data-model-for-azure-backup-diagnostics-events"></a>Modelo de dados para eventos de diagnóstico de backup do Azure

## <a name="coreazurebackup"></a>CoreAzureBackup

Esta tabela fornece informações sobre entidades de backup de núcleo, como cofres e itens de backup.

| **Campo**                         | **Tipo de Dados** | **Descrição**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                        | Texto          | Identificador de recurso para os dados sendo coletados. Por exemplo, ID de recurso do cofre dos serviços de recuperação. |
| OperationName                     | Texto          | Este campo representa o nome da operação atual-BackupItem, BackupItemAssociation ou ProtectedContainer. |
| Categoria                          | Texto          | Este campo representa a categoria de dados de diagnóstico enviados por push aos logs de Azure Monitor. Por exemplo, CoreAzureBackup. |
| AgentVersion                      | Text          | Número de versão do backup do agente ou do agente de proteção (no caso do SC DPM e MABS) |
| AzureBackupAgentVersion           | Texto          | Versão do agente de Backup do Azure no servidor de gerenciamento de backup |
| AzureDataCenter                   | Text          | Data Center em que o cofre está localizado                       |
| BackupItemAppVersion              | Texto          | Versão do aplicativo do item de backup                       |
| BackupItemFriendlyName            | Text          | Nome amigável do item de backup                             |
| BackupItemName                    | Text          | Nome do item de backup                                      |
| BackupItemProtectionState         | Texto          | Estado de proteção do item de backup                          |
| BackupItemFrontEndSize            | Texto          | Tamanho de front-end do item de backup                            |
| BackupItemType                    | Text          | Tipo de item de backup. Por exemplo: VM, fileFolder             |
| BackupItemUniqueId                | Texto          | Identificador único do item de backup                         |
| BackupManagementServerType        | Text          | Tipo de servidor de gerenciamento de backup, como em MABS, SC DPM     |
| BackupManagementServerUniqueId    | Texto          | Campo para identificar exclusivamente o servidor de gerenciamento de backup      |
| BackupManagementType              | Text          | Tipo de provedor para o servidor executando o trabalho de backup. Por exemplo, IaaSVM, FileFolder |
| BackupManagementServerName        | Texto          | Nome do servidor de gerenciamento de backup                         |
| BackupManagementServerOSVersion   | Texto          | Versão do SO do servidor de gerenciamento de backup                   |
| BackupManagementServerVersion     | Texto          | Versão do servidor de gerenciamento de backup                      |
| LatestRecoveryPointLocation       | Texto          | Local do ponto de recuperação mais recente para o item de backup    |
| LatestRecoveryPointTime           | Datetime      | Data e hora do ponto de recuperação mais recente para o item de backup   |
| OldestRecoveryPointLocation       | Texto          | Local do ponto de recuperação mais antigo para o item de backup    |
| OldestRecoveryPointTime           | Datetime      | Data e hora do ponto de recuperação mais recente para o item de backup   |
| PolicyUniqueId                    | Texto          | ID exclusiva para identificar a política                             |
| ProtectedContainerFriendlyName    | Text          | Nome amigável do servidor protegido                        |
| ProtectedContainerLocation        | Text          | Se o contêiner protegido está localizado no local ou no Azure |
| ProtectedContainerName            | Text          | Nome do contêiner protegido                            |
| ProtectedContainerOSType          | Text          | Tipo de so do contêiner protegido                          |
| ProtectedContainerOSVersion       | Texto          | Versão do SO do contêiner protegido                        |
| ProtectedContainerProtectionState | Texto          | Estado de proteção do contêiner protegido                  |
| ProtectedContainerType            | Texto          | Se o contêiner protegido é um servidor ou um contêiner  |
| ProtectedContainerUniqueId        | Text          | ID exclusiva usada para identificar o contêiner protegido para tudo, exceto VMs com backup usando o DPM, MABS |
| ProtectedContainerWorkloadType    | Text          | Tipo de backup do contêiner protegido. Por exemplo, IaaSVMContainer |
| ProtectionGroupName               | Text          | Nome do grupo de proteção no qual o item de backup está protegido, para SC DPM e MABS, se aplicável |
| ResourceGroupName                 | Text          | Grupo de recursos do recurso (por exemplo, cofre dos serviços de recuperação) para dados que estão sendo coletados |
| schemaVersion                     | Text          | Este campo denota a versão atual do esquema. É **v2** |
| SecondaryBackupProtectionState    | Texto          | Se a proteção secundária está habilitada para o item de backup  |
| Estado                             | Text          | Estado do objeto de item de backup. Por exemplo, Ativo, Excluído |
| StorageReplicationType            | Text          | Tipo de replicação de armazenamento para o cofre. Por exemplo, georedundante |
| SubscriptionId                    | Texto          | Identificador de assinatura do recurso (por exemplo, cofre dos serviços de recuperação) para o qual os dados são coletados |
| VaultName                         | Texto          | Nome do cofre                                            |
| VaultTags                         | Text          | Marcas associadas ao recurso do cofre                    |
| VaultUniqueId                     | Text          | Identificador exclusivo do cofre                             |
| SourceSystem                      | Texto          | Sistema de origem dos dados atuais-Azure                  |

## <a name="addonazurebackupalerts"></a>AddonAzureBackupAlerts

Essa tabela fornece detalhes sobre campos relacionados ao alerta.

| **Campo**                      | **Tipo de Dados** | **Descrição**                                              |
| :----------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Texto          | Identificador exclusivo para o recurso sobre quais dados são coletados. Por exemplo, uma ID de recurso do cofre dos serviços de recuperação |
| OperationName                  | Texto          | Nome da operação atual. Por exemplo, alerta            |
| Categoria                       | Texto          | Categoria de dados de diagnóstico enviados por push para logs de Azure Monitor-AddonAzureBackupAlerts |
| AlertCode                      | Text          | Código para identificar exclusivamente um tipo de alerta                     |
| AlertConsolidationStatus       | Texto          | Identificar se o alerta é um alerta consolidado ou não         |
| AlertOccurrenceDateTime        | Datetime      | Data e hora em que o alerta foi criado                     |
| AlertRaisedOn                  | Texto          | Tipo de entidade em que o alerta é gerado                        |
| AlertSeverity                  | Text          | Severidade do alerta. Por exemplo, crítico                 |
| AlertStatus                    | Text          | Status do alerta. Por exemplo, ativo                     |
| AlertTimeToResolveInMinutes    | Número        | Tempo necessário para resolver um alerta. Em branco para alertas ativos.     |
| AlertType                      | Text          | Tipo de alerta. Por exemplo, backup                           |
| AlertUniqueId                  | Text          | Identificador exclusivo do alerta gerado                    |
| BackupItemUniqueId             | Text          | Identificador exclusivo do item de backup associado ao alerta |
| BackupManagementServerUniqueId | Text          | Campo para identificar exclusivamente o servidor de gerenciamento de backup no qual o item de backup está protegido, se aplicável |
| BackupManagementType           | Text          | Tipo de provedor para o servidor executando o trabalho de backup, por exemplo, IaaSVM, fileFolder |
| CountOfAlertsConsolidated      | Número        | Número de alertas consolidados se for um alerta consolidado  |
| ProtectedContainerUniqueId     | Text          | Identificador exclusivo do servidor protegido associado ao alerta |
| Recomendado              | Texto          | Ação recomendada para resolver o alerta                      |
| schemaVersion                  | Texto          | Versão atual do esquema, por exemplo, **V2**            |
| Estado                          | Texto          | Estado atual do objeto de alerta, por exemplo, Ativo, Excluído |
| StorageUniqueId                | Texto          | ID exclusiva usada para identificar a entidade de armazenamento                |
| VaultUniqueId                  | Text          | ID exclusiva usada para identificar o cofre relacionado ao alerta    |
| SourceSystem                   | Texto          | Sistema de origem dos dados atuais - Azure                    |

## <a name="addonazurebackupprotectedinstance"></a>AddonAzureBackupProtectedInstance

Esta tabela apresenta campos relacionados a instâncias protegidas básicas.

| **Campo**                      | **Tipo de Dados** | **Descrição**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Texto          | Identificador exclusivo do recurso sobre o qual os dados são coletados. Por exemplo, uma ID do recurso do cofre dos Serviços de Recuperação |
| OperationName                  | Texto          | Nome da operação, por exemplo ProtectedInstance         |
| Categoria                       | Texto          | Categoria de dados de diagnóstico enviados por push para logs de Azure Monitor-AddonAzureBackupProtectedInstance |
| BackupItemUniqueId             | Texto          | ID exclusiva do item de backup                                 |
| BackupManagementServerUniqueId | Text          | Campo para identificar exclusivamente o servidor de gerenciamento de backup no qual o item de backup está protegido, se aplicável |
| BackupManagementType           | Text          | Tipo de provedor para o servidor executando o trabalho de backup, por exemplo, IaaSVM, fileFolder |
| ProtectedContainerUniqueId     | Text          | ID exclusiva para identificar o contêiner protegido no qual o trabalho é executado |
| ProtectedInstanceCount         | Text          | Contagem de instâncias protegidas para o item de backup associado ou o contêiner protegido nessa data e hora |
| schemaVersion                  | Texto          | Versão atual do esquema, por exemplo, **V2**            |
| Estado                          | Text          | Estado do objeto de item de backup, por exemplo, ativo, excluído |
| VaultUniqueId                  | Text          | Identificador exclusivo do cofre protegido associado à instância protegida |
| SourceSystem                   | Texto          | Sistema de origem dos dados atuais - Azure                    |

## <a name="addonazurebackupjobs"></a>AddonAzureBackupJobs

Esta tabela fornece detalhes sobre campos relacionados ao trabalho.

| **Campo**                      | **Tipo de Dados** | **Descrição**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Texto          | Identificador de recurso para os dados sendo coletados. Por exemplo, ID do recurso do cofre dos Serviços de Recuperação |
| OperationName                  | Texto          | Este campo representa o nome da operação atual - Trabalho    |
| Categoria                       | Texto          | Este campo representa a categoria de dados de diagnóstico enviados por push para logs de Azure Monitor-AddonAzureBackupJobs |
| AdhocOrScheduledJob            | Texto          | Campo para especificar se o trabalho é ad hoc ou agendado           |
| BackupItemUniqueId             | Texto          | ID exclusiva usada para identificar o item de backup relacionado à entidade de armazenamento |
| BackupManagementServerUniqueId | Texto          | ID exclusiva usada para identificar o serviço de gerenciamento de backup relacionado à entidade de armazenamento |
| BackupManagementType           | Text          | Tipo de provedor para executar o backup, por exemplo, IaaSVM, FileFolder para qual esse trabalho pertence |
| DataTransferredInMB            | Número        | Dados transferidos em MB para este trabalho                          |
| JobDurationInSecs              | Número        | Duração total do trabalho em segundos                                |
| JobFailureCode                 | Texto          | Cadeia de caracteres de código de falha pela qual a falha no trabalho ocorreu    |
| JobOperation                   | Text          | Operação para a qual o trabalho é executado, por exemplo, backup, restauração, configurar backup |
| JobOperationSubType            | Text          | Subtipo da operação de trabalho. Por exemplo, ' log ', no caso do trabalho de backup de log |
| JobStartDateTime               | Datetime      | Data e hora em que o trabalho iniciou a execução                       |
| JobStatus                      | Texto          | Status do trabalho concluído, por exemplo, Concluído, Com Falha   |
| JobUniqueId                    | Texto          | ID exclusiva para identificar o trabalho                                |
| ProtectedContainerUniqueId     | Text          | Identificador exclusivo do servidor protegido associado ao trabalho |
| RecoveryJobDestination         | Texto          | Destino de um trabalho de recuperação, em que os dados são recuperados   |
| RecoveryJobRPDateTime          | Datetime      | A data, a hora em que o ponto de recuperação que está sendo recuperado foi criado |
| RecoveryJobLocation            | Text          | O local onde o ponto de recuperação que está sendo recuperado foi armazenado |
| RecoveryLocationType           | Text          | Tipo do local de recuperação                                |
| schemaVersion                  | Texto          | Versão atual do esquema, por exemplo, **V2**            |
| Estado                          | Text          | Estado atual do objeto de trabalho, por exemplo, ativo, excluído |
| VaultUniqueId                  | Text          | Identificador exclusivo do cofre protegido associado ao trabalho |
| SourceSystem                   | Texto          | Sistema de origem dos dados atuais - Azure                    |

## <a name="addonazurebackuppolicy"></a>AddonAzureBackupPolicy

Esta tabela fornece detalhes sobre campos relacionados a políticas.

| **Campo**                       | **Tipo de Dados**  | **Descrição**                                              |
| ------------------------------- | -------------- | ------------------------------------------------------------ |
| ResourceId                      | Texto           | Identificador exclusivo do recurso sobre o qual os dados são coletados. Por exemplo, uma ID do recurso do cofre dos Serviços de Recuperação |
| OperationName                   | Texto           | Nome da operação, por exemplo, política ou PolicyAssociation |
| Categoria                        | Texto           | Categoria de dados de diagnóstico enviados por push para logs de Azure Monitor-AddonAzureBackupPolicy |
| BackupDaysOfTheWeek             | Texto           | Dias da semana nos quais os backups foram agendados            |
| BackupFrequency                 | Text           | Frequência com que os backups são executados. Por exemplo, diário, semanal |
| BackupManagementType            | Text           | Tipo de provedor para o servidor executando o trabalho de backup. Por exemplo, IaaSVM, fileFolder |
| BackupManagementServerUniqueId  | Text           | Campo para identificar exclusivamente o servidor de gerenciamento de backup no qual o item de backup está protegido, se aplicável |
| BackupTimes                     | Texto           | Data e hora quando os backups são agendados                     |
| DailyRetentionDuration          | Número Inteiro   | Duração total da retenção em dias para backups configurados      |
| DailyRetentionTimes             | Texto           | Data e hora quando a retenção diária foi configurada            |
| DiffBackupDaysOfTheWeek         | Text           | Dias da semana para backups diferenciais para SQL no backup de VM do Azure |
| DiffBackupFormat                | Text           | Formato para backups diferenciais para SQL no backup da VM do Azure   |
| DiffBackupRetentionDuration     | Número Decimal | Duração da retenção para backups diferenciais para SQL no backup de VM do Azure |
| DiffBackupTime                  | Hora           | Horário para backups diferenciais para SQL no backup de VM do Azure     |
| LogBackupFrequency              | Número Decimal | Frequência de backups de log de transações para SQL                            |
| LogBackupRetentionDuration      | Número Decimal | Duração da retenção para backups de log para SQL no backup de VM do Azure |
| MonthlyRetentionDaysOfTheMonth  | Text           | Semanas do mês em que a retenção mensal é configurada.  Por exemplo, primeiro, último |
| MonthlyRetentionDaysOfTheMonth   | Texto           | Dias da semana selecionados para a retenção mensal              |
| MonthlyRetentionDuration        | Text           | Duração total da retenção em meses para backups configurados    |
| MonthlyRetentionFormat          | Text           | Tipo de configuração para retenção mensal. Por exemplo, diariamente com base no dia, semanal para a semana com base |
| MonthlyRetentionTimes           | Texto           | Data e hora quando a retenção mensal foi configurada           |
| MonthlyRetentionWeeksOfTheMonth | Text           | Semanas do mês em que a retenção mensal é configurada.   Por exemplo, primeiro, último |
| PolicyName                      | Texto           | Nome da política definido                                   |
| PolicyUniqueId                  | Texto           | ID exclusiva para identificar a política                             |
| PolicyTimeZone                  | Text           | Fuso horário no qual os campos de hora da política são especificados nos logs |
| RetentionDuration               | Text           | Duração de retenção para backups configurados                    |
| RetentionType                   | Text           | Tipo de retenção                                            |
| schemaVersion                   | Text           | Este campo denota a versão atual do esquema, é **v2** |
| Estado                           | Text           | Estado atual do objeto de política. Por exemplo, ativo, excluído |
| SynchronisationFrequencyPerDay  | Número Inteiro   | Número de vezes em um dia em que um backup de arquivo é sincronizado para SC DPM e MABS |
| VaultUniqueId                   | Text           | ID exclusiva do cofre ao qual essa política pertence          |
| WeeklyRetentionDaysOfTheWeek    | Texto           | Dias da semana selecionados para a retenção semanal               |
| WeeklyRetentionDuration         | Número Decimal | Duração da retenção semanal total em semanas para backups configurados |
| WeeklyRetentionTimes            | Texto           | Data e hora quando a retenção semanal foi configurada            |
| YearlyRetentionDaysOfTheMonth   | Texto           | Dias do mês selecionados para a retenção anual             |
| YearlyRetentionDaysOfTheMonth    | Text           | Dias da semana selecionados para a retenção anual               |
| YearlyRetentionDuration         | Número Decimal | Duração total da retenção em ano para backups configurados     |
| YearlyRetentionFormat           | Text           | Tipo de configuração para retenção anual, por exemplo, diária para o dia, semanal para a semana com base |
| YearlyRetentionMonthsOfTheYear  | Texto           | Meses do ano selecionados para a retenção anual             |
| YearlyRetentionTimes            | Texto           | Data e hora quando a retenção anual foi configurada            |
| YearlyRetentionWeeksOfTheMonth  | Text           | Semanas do mês selecionadas para retenção anual             |
| SourceSystem                    | Texto           | Sistema de origem dos dados atuais - Azure                    |

## <a name="addonazurebackupstorage"></a>AddonAzureBackupStorage

Esta tabela fornece detalhes sobre campos relacionados ao armazenamento.

| **Campo**                      | **Tipo de Dados** | **Descrição**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Texto          | Identificador de recurso para os dados sendo coletados. Por exemplo, ID de recurso do cofre dos serviços de recuperação |
| OperationName                  | Texto          | Este campo representa o nome da operação atual-armazenamento ou StorageAssociation |
| Categoria                       | Texto          | Este campo representa a categoria de dados de diagnóstico enviados por push para logs de Azure Monitor-AddonAzureBackupStorage |
| BackupItemUniqueId             | Text          | ID exclusiva usada para identificar o item de backup para VMs com backup usando o DPM, MABS |
| BackupManagementServerUniqueId | Text          | Campo para identificar exclusivamente o servidor de gerenciamento de backup no qual o item de backup está protegido, se aplicável |
| BackupManagementType           | Text          | Tipo de provedor para o servidor executando o trabalho de backup. Por exemplo, IaaSVM, fileFolder |
| PreferredWorkloadOnVolume      | Text          | Carga de trabalho para a qual este volume é o armazenamento preferencial      |
| ProtectedContainerUniqueId     | Text          | Identificador exclusivo do contêiner protegido associado ao item de backup |
| schemaVersion                  | Text          | Versão do esquema. Por exemplo, **v2**                   |
| Estado                          | Text          | Estado do objeto de item de backup. Por exemplo, ativo, excluído |
| StorageAllocatedInMBs          | Número        | Tamanho do armazenamento alocado pelo item de backup correspondente no armazenamento correspondente do tipo disco |
| StorageConsumedInMBs           | Número        | Tamanho do armazenamento consumido pelo item de backup correspondente no armazenamento correspondente |
| StorageName                    | Text          | Nome da entidade de armazenamento. Por exemplo, E:\                      |
| StorageTotalSizeInGBs          | Texto          | Tamanho total do armazenamento, em GB, consumido pela entidade de armazenamento     |
| StorageType                    | Texto          | Tipo de armazenamento, por exemplo, Nuvem, Volume, Disco             |
| StorageUniqueId                | Texto          | ID exclusiva usada para identificar a entidade de armazenamento                |
| VaultUniqueId                  | Text          | ID exclusiva usada para identificar o cofre relacionado à entidade de armazenamento |
| VolumeFriendlyName             | Text          | Nome amigável do volume de armazenamento                          |
| SourceSystem                   | Texto          | Sistema de origem dos dados atuais - Azure                    |

## <a name="next-steps"></a>Próximas etapas

- [Saiba como enviar dados de diagnóstico para Log Analytics](./backup-azure-diagnostic-events.md)
- [Saiba como escrever consultas em tabelas específicas de recursos](./backup-azure-monitoring-use-azuremonitor.md#sample-kusto-queries)
