---
title: Modelo de dados para eventos de diagnóstico de backup do Azure
description: Este modelo de dados está em referência ao modo específico de recursos de envio de eventos de diagnóstico para o Log Analytics (LA).
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 267753ee1647739e36d92b64f50d8a8be87537d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77583377"
---
# <a name="data-model-for-azure-backup-diagnostics-events"></a>Modelo de dados para eventos de diagnóstico de backup do Azure

## <a name="coreazurebackup"></a>CoreAzureBackup

Esta tabela fornece informações sobre as principais entidades de backup, como cofres e itens de backup.

| **Campo**                         | **Tipo de dados** | **Descrição**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                        | Texto          | Identificador de recurso para os dados sendo coletados. Por exemplo, ID de recurso do cofre do Recovery Services. |
| OperationName                     | Texto          | Este campo representa o nome da operação atual - BackupItem, BackupItemAssociation ou ProtectedContainer. |
| Categoria                          | Texto          | Este campo representa a categoria de dados de diagnóstico susciados nos registros do Monitor do Azure. Por exemplo, CoreAzureBackup. |
| Versão do agente                      | Texto          | Número da versão do Agent Backup ou do Agente de Proteção (no caso do SC DPM e Do MABS) |
| AzureBackupAgentVersion           | Texto          | Versão do Azure Backup Agent no servidor de gerenciamento de backup |
| AzureDataCenter                   | Texto          | Data center onde o cofre está localizado                       |
| BackupItemAppVersion              | Texto          | Versão do aplicativo do item de backup                       |
| BackupItemFriendlyName            | Texto          | Nome amigável do item de backup                             |
| BackupItemName                    | Texto          | Nome do item de backup                                      |
| BackupItemProtectionState         | Texto          | Estado de proteção do item de backup                          |
| BackupItemFrontEndSize            | Texto          | Tamanho frontal do item de backup                            |
| BackupItemType                    | Texto          | Tipo de item de backup. Por exemplo: VM, Filefolder             |
| BackupItemUniqueId                | Texto          | Identificador único do item de backup                         |
| BackupManagementServerType        | Texto          | Tipo do servidor de gerenciamento de backup, como no MABS, SC DPM     |
| BackupGerenciamentoDegerenciamentoDeservidoresUniqueId    | Texto          | Campo para identificar exclusivamente o servidor de gerenciamento de backup      |
| BackupManagementType              | Texto          | Tipo de provedor para servidor fazendo trabalho de backup. Por exemplo, IaaSVM, FileFolder |
| BackupGerenciamentoGerenciamentoNome do Servidor        | Texto          | Nome do servidor de gerenciamento de backup                         |
| BackupGerenciamentodeservidores   | Texto          | Versão do SISTEMA OPERACIONAL do Servidor de Gerenciamento de Backup                   |
| BackupManagementServerVersion     | Texto          | Versão do servidor de gerenciamento de backup                      |
| Localização mais recentedodoRecoveryPoint       | Texto          | Localização do último ponto de recuperação para o item de backup    |
| Mais recenteRecoveryPointTime           | Datetime      | Data de data do último ponto de recuperação para o item de backup   |
| Localização mais antiga do RecoveryPoint       | Texto          | Localização do ponto de recuperação mais antigo para o item de backup    |
| Mais antigoRecoveryPointTime           | Datetime      | Data de data do último ponto de recuperação para o item de backup   |
| PolicyUniqueId                    | Texto          | ID único para identificar a política                             |
| Nome amigo do contêiner protegido    | Texto          | Nome amigável do servidor protegido                        |
| Localização de contêiner protegido        | Texto          | Se o Contêiner Protegido está localizado no local ou no Azure |
| Nome do contêiner protegido            | Texto          | Nome do contêiner protegido                            |
| ContainerOSType protegido          | Texto          | Tipo de SISTEMA OPERACIONAL do Recipiente Protegido                          |
| Versão protectedcontainerOS       | Texto          | Versão do SISTEMA OPERACIONAL do recipiente protegido                        |
| ProtectedContainerProtectionState | Texto          | Estado de proteção do recipiente protegido                  |
| Tipo de contêiner protegido            | Texto          | Se o Contêiner Protegido é um servidor ou um contêiner  |
| Contêiner protegidoUniqueId        | Texto          | ID exclusivo usado para identificar o recipiente protegido para tudo, exceto VMs backup usando DPM, MABS |
| Tipo de carga de trabalho de contêiner protegido    | Texto          | Tipo do recipiente protegido apoiado. Por exemplo, IaaSVMContainer |
| ProtectionGroupName               | Texto          | Nome do Grupo de Proteção em que o Item de Backup é protegido, para DPM SC e MABS, se aplicável |
| ResourceGroupName                 | Texto          | Grupo de recursos do recurso (por exemplo, cofre de Serviços de Recuperação) para dados que estão sendo coletados |
| schemaVersion                     | Texto          | Este campo denota a versão atual do esquema, é **V2** |
| Estado de proteção de backup secundário    | Texto          | Se a proteção secundária está habilitada para o item de backup  |
| Estado                             | Texto          | Estado do objeto de item de backup. Por exemplo, Ativo, Excluído |
| StorageReplicationType            | Texto          | Tipo de replicação de armazenamento para o cofre. Por exemplo, Georedundant |
| SubscriptionId                    | Texto          | Identificador de assinatura do recurso (por exemplo, cofre de Serviços de Recuperação) para o qual os dados são coletados |
| VaultName                         | Texto          | Nome do cofre                                            |
| VaultTags                         | Texto          | Tags associadas ao recurso do cofre                    |
| VaultUniqueId                     | Texto          | Identificador exclusivo do cofre                             |
| SourceSystem                      | Texto          | Sistema de origem dos dados atuais - Azure                  |

## <a name="addonazurebackupalerts"></a>AddonAzureBackupAlertas

Essa tabela fornece detalhes sobre campos relacionados ao alerta.

| **Campo**                      | **Tipo de dados** | **Descrição**                                              |
| :----------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Texto          | Identificador exclusivo para o recurso sobre quais dados são coletados. Por exemplo, um ID de recurso do cofre do Recovery Services |
| OperationName                  | Texto          | Nome da operação atual. Por exemplo, Alerta            |
| Categoria                       | Texto          | Categoria de dados de diagnósticos empurrados para logs do Monitor do Azure - AddonAzureBackupAlerts |
| Código de alerta                      | Texto          | Código para identificar exclusivamente um tipo de alerta                     |
| AlertConsolidationStatus       | Texto          | Identifique se o alerta é um alerta consolidado ou não         |
| Hora de ocorrência de alerta        | Datetime      | Data e hora em que o alerta foi criado                     |
| AlertaraisedOn                  | Texto          | Tipo de entidade em que o alerta é levantado                        |
| AlertSeverity                  | Texto          | Severidade do alerta. Por exemplo, Crítico                 |
| AlertStatus                    | Texto          | Estado do alerta. Por exemplo, Ativo                     |
| Tempo de alertapararesolverinminutes    | Número        | Tempo levado para resolver um alerta. Em branco para alertas ativos.     |
| AlertType                      | Texto          | Tipo de alerta. Por exemplo, backup                           |
| AlertUniqueId                  | Texto          | Identificador exclusivo do alerta gerado                    |
| BackupItemUniqueId             | Texto          | Identificador exclusivo do item de backup associado ao alerta |
| BackupGerenciamentoDegerenciamentoDeservidoresUniqueId | Texto          | Campo para identificar exclusivamente o servidor de gerenciamento de backup que o item de backup está protegido, se aplicável |
| BackupManagementType           | Texto          | Tipo de provedor para servidor fazendo backup, por exemplo, IaaSVM, FileFolder |
| CountOfAlertsConsolidado      | Número        | Número de alertas consolidados se for um alerta consolidado  |
| Contêiner protegidoUniqueId     | Texto          | Identificador exclusivo do servidor protegido associado ao alerta |
| Ação recomendada              | Texto          | Ação recomendada para resolver o alerta                      |
| schemaVersion                  | Texto          | Versão atual do esquema, por exemplo **V2**            |
| Estado                          | Texto          | Estado atual do objeto de alerta, por exemplo, Ativo, Excluído |
| ArmazenamentoUniqueId                | Texto          | ID exclusivo usado para identificar a entidade de armazenamento                |
| VaultUniqueId                  | Texto          | ID exclusivo usado para identificar o cofre relacionado ao alerta    |
| SourceSystem                   | Texto          | Sistema de origem dos dados atuais - Azure                    |

## <a name="addonazurebackupprotectedinstance"></a>AddonAzureBackupProtectedInstance

Esta tabela fornece campos básicos relacionados a instâncias protegidas.

| **Campo**                      | **Tipo de dados** | **Descrição**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Texto          | Identificador exclusivo para o recurso sobre o qual os dados são coletados. Por exemplo, um ID de recurso do cofre do Recovery Services |
| OperationName                  | Texto          | Nome da operação, por exemplo ProtectedInstance         |
| Categoria                       | Texto          | Categoria de dados de diagnósticos empurrados para logs do Monitor do Azure - AddonAzureBackupProtectedInstance |
| BackupItemUniqueId             | Texto          | ID exclusivo do item de backup                                 |
| BackupGerenciamentoDegerenciamentoDeservidoresUniqueId | Texto          | Campo para identificar exclusivamente o servidor de gerenciamento de backup que o item de backup está protegido, se aplicável |
| BackupManagementType           | Texto          | Tipo de provedor para servidor fazendo backup, por exemplo, IaaSVM, FileFolder |
| Contêiner protegidoUniqueId     | Texto          | ID exclusivo para identificar o contêiner protegido em que o trabalho é executado |
| Contagem de instâncias protegidas         | Texto          | Contagem de instâncias protegidas para o item de backup associado ou contêiner protegido nesse período de data |
| schemaVersion                  | Texto          | Versão atual do esquema, por exemplo **V2**            |
| Estado                          | Texto          | Estado do objeto de item de backup, por exemplo, Ativo, Excluído |
| VaultUniqueId                  | Texto          | Identificador exclusivo do cofre protegido associado à instância protegida |
| SourceSystem                   | Texto          | Sistema de origem dos dados atuais - Azure                    |

## <a name="addonazurebackupjobs"></a>AddonAzureBackupJobs

Esta tabela fornece detalhes sobre campos relacionados ao trabalho.

| **Campo**                      | **Tipo de dados** | **Descrição**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Texto          | Identificador de recurso para os dados sendo coletados. Por exemplo, ID de recurso do cofre do Recovery Services |
| OperationName                  | Texto          | Este campo representa o nome da operação atual - Trabalho    |
| Categoria                       | Texto          | Este campo representa a categoria de dados de diagnóstico sustais empurrados para logs do Monitor do Azure - AddonAzureBackupJobs |
| AdhocOrScheduledjob            | Texto          | Campo para especificar se o trabalho é Ad Hoc ou Agendado           |
| BackupItemUniqueId             | Texto          | ID exclusivo usado para identificar o item de backup relacionado à entidade de armazenamento |
| BackupGerenciamentoDegerenciamentoDeservidoresUniqueId | Texto          | ID exclusivo usado para identificar o servidor de gerenciamento de backup relacionado à entidade de armazenamento |
| BackupManagementType           | Texto          | Tipo de provedor para realizar backup, por exemplo, IaaSVM, FileFolder ao qual esse alerta pertence |
| DadostransferidosInMB            | Número        | Dados transferidos em MB para este trabalho                          |
| JobDurationInSecs              | Número        | Duração total do trabalho em segundos                                |
| JobFailureCode                 | Texto          | Cadeia de caracteres de código de falha pela qual a falha no trabalho ocorreu    |
| JobOperation                   | Texto          | Operação para a qual o trabalho é executado, por exemplo, Backup, Restauração, Configurar Backup |
| JobOperaçãoSubType            | Texto          | Sub Tipo da Operação de Trabalho. Por exemplo, 'Log', no caso do Log Backup Job |
| JobStartDateTime               | Datetime      | Data e hora em que o trabalho iniciou a execução                       |
| JobStatus                      | Texto          | Status do trabalho concluído, por exemplo, Concluído, Com Falha   |
| JobUniqueId                    | Texto          | Identificação única para identificar o trabalho                                |
| Contêiner protegidoUniqueId     | Texto          | Identificador exclusivo do servidor protegido associado ao alerta |
| RecuperaçãoJobDestination         | Texto          | Destino de um trabalho de recuperação, onde os dados são recuperados   |
| RecuperaçãoJobRPDateTime          | Datetime      | A data, hora em que o ponto de recuperação que está sendo recuperado foi criado |
| RecuperaçãoJobLocation            | Texto          | O local onde o ponto de recuperação que está sendo recuperado foi armazenado |
| RecoveryLocationType           | Texto          | Tipo do Local de Recuperação                                |
| schemaVersion                  | Texto          | Versão atual do esquema, por exemplo **V2**            |
| Estado                          | Texto          | Estado atual do objeto de alerta, por exemplo, Ativo, Excluído |
| VaultUniqueId                  | Texto          | Identificador exclusivo do cofre protegido associado ao alerta |
| SourceSystem                   | Texto          | Sistema de origem dos dados atuais - Azure                    |

## <a name="addonazurebackuppolicy"></a>Política de backup do AddonAzure

Esta tabela fornece detalhes sobre campos relacionados a políticas.

| **Campo**                       | **Tipo de dados**  | **Descrição**                                              |
| ------------------------------- | -------------- | ------------------------------------------------------------ |
| ResourceId                      | Texto           | Identificador exclusivo para o recurso sobre o qual os dados são coletados. Por exemplo, um ID de recurso do cofre do Recovery Services |
| OperationName                   | Texto           | Nome da operação, por exemplo, Policy or PolicyAssociation |
| Categoria                        | Texto           | Categoria de dados de diagnósticos empurrados para logs do Monitor do Azure - AddonAzureBackupPolicy |
| BackupDaysOfTheWeek             | Texto           | Dias da semana nos quais os backups foram agendados            |
| BackupFrequency                 | Texto           | Frequência com que os backups são executados. Por exemplo, diariamente, semanalmente |
| BackupManagementType            | Texto           | Tipo de provedor para servidor fazendo trabalho de backup. Por exemplo, IaaSVM, FileFolder |
| BackupGerenciamentoDegerenciamentoDeservidoresUniqueId  | Texto           | Campo para identificar exclusivamente o servidor de gerenciamento de backup que o item de backup está protegido, se aplicável |
| BackupTimes                     | Texto           | Data e hora quando os backups são agendados                     |
| DailyRetentionDuration          | Número Inteiro   | Duração total da retenção em dias para backups configurados      |
| DailyRetentionTimes             | Texto           | Data e hora quando a retenção diária foi configurada            |
| diffbackupdaysoftheweek         | Texto           | Dias da semana para backups diferenciais para SQL no Azure VM Backup |
| DiffBackupFormat                | Texto           | Formato para backups diferenciais para SQL no backup do Azure VM   |
| DiffBackupRetentionDuração     | Número Decimal | Duração da retenção para backups diferenciais para SQL no Backup Azure VM |
| DiffBackupTime                  | Hora           | Tempo para backups diferenciais para SQL no Backup Azure VM     |
| LogBackupFrequency              | Número Decimal | Freqüência para backups de log para SQL                            |
| LogBackupRedurationDuração      | Número Decimal | Duração da retenção dos backups log para SQL no backup do Azure VM |
| MonthlyRetentionDaysOfTheMonth  | Texto           | Semanas do mês em que a retenção mensal é configurada.  Por exemplo, Primeiro, Último, etc. |
| MonthlyRetentionDaysOfTheMonth   | Texto           | Dias da semana selecionados para a retenção mensal              |
| MonthlyRetentionDuration        | Texto           | Duração total da retenção em meses para backups configurados    |
| MonthlyRetentionFormat          | Texto           | Tipo de configuração para retenção mensal. Por exemplo, diariamente para a base diária, semanalmente para a semana |
| MonthlyRetentionTimes           | Texto           | Data e hora quando a retenção mensal foi configurada           |
| MonthlyRetentionWeeksOfTheMonth | Texto           | Semanas do mês em que a retenção mensal é configurada.   Por exemplo, Primeiro, Último, etc. |
| PolicyName                      | Texto           | Nome da política definido                                   |
| PolicyUniqueId                  | Texto           | ID único para identificar a política                             |
| PolicyTimeZone                  | Texto           | Fuso horário no qual os campos de tempo de diretiva são especificados nos logs |
| Duração da retenção               | Texto           | Duração de retenção para backups configurados                    |
| RetentionType                   | Texto           | Tipo de retenção                                            |
| schemaVersion                   | Texto           | Este campo denota a versão atual do esquema, é **V2** |
| Estado                           | Texto           | Estado atual do objeto político. Por exemplo, Ativo, Excluído |
| SincronizaçãoFrequencyPerDay  | Número Inteiro   | Número de vezes em um dia um backup de arquivo é sincronizado para SC DPM e MABS |
| VaultUniqueId                   | Texto           | Identificação única do cofre a que esta política pertence          |
| WeeklyRetentionDaysOfTheWeek    | Texto           | Dias da semana selecionados para a retenção semanal               |
| WeeklyRetentionDuration         | Número Decimal | Duração total da retenção semanal em semanas para backups configurados |
| WeeklyRetentionTimes            | Texto           | Data e hora quando a retenção semanal foi configurada            |
| YearlyRetentionDaysOfTheMonth   | Texto           | Dias do mês selecionados para a retenção anual             |
| YearlyRetentionDaysOfTheMonth    | Texto           | Dias da semana selecionados para a retenção anual               |
| YearlyRetentionDuration         | Número Decimal | Duração total da retenção em ano para backups configurados     |
| YearlyRetentionFormat           | Texto           | Tipo de configuração para retenção anual, por exemplo, diariamente para dia baseado, semanalmente para a semana |
| YearlyRetentionMonthsOfTheYear  | Texto           | Meses do ano selecionados para a retenção anual             |
| YearlyRetentionTimes            | Texto           | Data e hora quando a retenção anual foi configurada            |
| YearlyRetentionWeeksOfTheMonth  | Texto           | Semanas do mês selecionadas para retenção anual             |
| SourceSystem                    | Texto           | Sistema de origem dos dados atuais - Azure                    |

## <a name="addonazurebackupstorage"></a>AddonAzureBackupArmazenamento

Esta tabela fornece detalhes sobre campos relacionados ao armazenamento.

| **Campo**                      | **Tipo de dados** | **Descrição**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Texto          | Identificador de recurso para os dados sendo coletados. Por exemplo, ID de recurso do cofre do Recovery Services |
| OperationName                  | Texto          | Este campo representa o nome da operação atual - Storage or StorageAssociation |
| Categoria                       | Texto          | Este campo representa a categoria de dados de diagnósticos empurrados para logs do Monitor do Azure - AddonAzureBackupStorage |
| BackupItemUniqueId             | Texto          | ID exclusivo usado para identificar o item de backup para VMs backup usando DPM, MABS |
| BackupGerenciamentoDegerenciamentoDeservidoresUniqueId | Texto          | Campo para identificar exclusivamente o servidor de gerenciamento de backup que o item de backup está protegido, se aplicável |
| BackupManagementType           | Texto          | Tipo de provedor para servidor fazendo trabalho de backup. Por exemplo, IaaSVM, FileFolder |
| Carga de trabalho preferencialOnVolume      | Texto          | Carga de trabalho para a qual este volume é o armazenamento preferido      |
| Contêiner protegidoUniqueId     | Texto          | Identificador exclusivo do servidor protegido associado ao alerta |
| schemaVersion                  | Texto          | Versão do esquema. Por exemplo, **V2**                   |
| Estado                          | Texto          | Estado do objeto de item de backup. Por exemplo, Ativo, Excluído |
| ArmazenamentoalocadoInMBs          | Número        | Tamanho do armazenamento alocado pelo item de backup correspondente no armazenamento correspondente do disco do tipo |
| ArmazenamentoconsumidaInMBs           | Número        | Tamanho do armazenamento consumido pelo item de backup correspondente no armazenamento correspondente |
| StorageName                    | Texto          | Nome da entidade de armazenamento. Por exemplo, E:\                      |
| ArmazenamentoTotalSizeInGBs          | Texto          | Tamanho total do armazenamento, em GB, consumido pela entidade de armazenamento     |
| StorageType                    | Texto          | Tipo de armazenamento, por exemplo Nuvem, Volume, Disco             |
| ArmazenamentoUniqueId                | Texto          | ID exclusivo usado para identificar a entidade de armazenamento                |
| VaultUniqueId                  | Texto          | ID exclusivo usado para identificar o cofre relacionado à entidade de armazenamento |
| VolumeFriendlyName             | Texto          | Nome amigável do volume de armazenamento                          |
| SourceSystem                   | Texto          | Sistema de origem dos dados atuais - Azure                    |

## <a name="next-steps"></a>Próximas etapas

- [Saiba como enviar dados de diagnóstico para o Log Analytics](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events)
- [Saiba como escrever consultas em tabelas específicas de recursos](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor#sample-kusto-queries)