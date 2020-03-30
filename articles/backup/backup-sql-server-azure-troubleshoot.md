---
title: Solucionando problemas backup do banco de dados do SQL Server
description: Informações de solução de problemas para fazer backup de bancos de dados do SQL Server em execução em VMs do Azure com o Backup do Azure.
ms.topic: troubleshooting
ms.date: 06/18/2019
ms.openlocfilehash: 8d49adb0ab741903ccb2989cfeb4ceaef2e8a38d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408609"
---
# <a name="troubleshoot-sql-server-database-backup-by-using-azure-backup"></a>Solucionar problemas no backup do banco de dados do SQL Server usando o Backup do Azure

Este artigo fornece informações de solução de problemas para bancos de dados SQL Server em execução em máquinas virtuais do Azure.

Para obter mais informações sobre o processo de backup e limitações, consulte [Sobre o backup do SQL Server em VMs do Azure](sql-support-matrix.md#feature-consideration-and-limitations).

## <a name="sql-server-permissions"></a>Permissões do SQL Server

Para configurar a proteção para um banco de dados SQL Server em uma máquina virtual, você deve instalar a extensão **AzureBackupWindowsWorkload** nessa máquina virtual. Se você tiver o erro **UserErrorSQLNoSysadminMembership,** isso significa que a instância do SQL Server não tem as permissões de backup necessárias. Para corrigir esse erro, siga as etapas do [Set VM permissões](backup-azure-sql-database.md#set-vm-permissions).

## <a name="troubleshoot-discover-and-configure-issues"></a>Solucionar problemas para descobrir e configurar problemas

Depois de criar e configurar um cofre de Serviços de Recuperação, descobrir bancos de dados e configurar backup é um processo em duas etapas.<br>

![sql](./media/backup-azure-sql-database/sql.png)

Durante a configuração de backup, se o VM SQL e suas instâncias não estiverem visíveis nos **DBs de detecção em VMs** e **configurar backup** (consulte a imagem acima) certifique-se de que:

### <a name="step-1-discovery-dbs-in-vms"></a>Passo 1: Discovery DBs em VMs

- Se a VM não estiver listada na lista de VM descoberta e também não estiver registrada para backup SQL em outro cofre, siga as etapas de backup do [Discovery SQL Server.](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#discover-sql-server-databases)

### <a name="step-2-configure-backup"></a>Passo 2: Configurar backup

- Se o cofre no qual o VM SQL estiver registrado no mesmo cofre usado para proteger os bancos de dados, siga as etapas [configurar backup.](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#configure-backup)

Se o SQL VM precisa ser registrado no novo cofre, então ele deve não ser registrado no cofre antigo.  O cancelamento do registro de um VM SQL do cofre requer que todas as fontes de dados protegidas sejam paradas protegidas e, em seguida, você pode excluir os dados de backup. Excluir dados de backup é uma operação destrutiva.  Depois de revisar e tomar todas as precauções para cancelar o registro do VM SQL, registre esta mesma VM com um novo cofre e tente novamente a operação de backup.

## <a name="troubleshoot-backup-and-recovery-issues"></a>Solucionar problemas de backup e recuperação  

Às vezes, ou falhas aleatórias podem acontecer em operações de backup e restauração ou essas operações podem ficar presas. Isso pode ser por causa de programas antivírus em sua VM. Como uma prática recomendada, sugerimos os seguintes passos:

1. Exclua as seguintes pastas da varredura de antivírus:

    `C:\Program Files\Azure Workload Backup` `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.RecoveryServices.WorkloadBackup.Edp.AzureBackupWindowsWorkload`

    Substitua pela `C:\` letra do *systemDrive*.

1. Exclua os três processos a seguir executados em uma VM da varredura antivírus:

    - IaasWLPluginSvc.exe
    - IaasWorkloadCoordinaorService.exe
    - TriggerExtensionJob.exe

1. O SQL também oferece algumas diretrizes prestes a trabalhar com programas antivírus. Veja [este artigo](https://support.microsoft.com/help/309422/choosing-antivirus-software-for-computers-that-run-sql-server) para mais detalhes.

## <a name="error-messages"></a>Mensagens de erro

### <a name="backup-type-unsupported"></a>Tipo de backup sem suporte

| Severity | Descrição | Possíveis causas | Ação recomendada |
|---|---|---|---|
| Aviso | As configurações atuais para este banco de dados não suportam certos tipos de backup presentes na diretiva associada. | <li>Apenas uma operação completa de backup de banco de dados pode ser realizada no banco de dados principal. Nem o backup diferencial nem o backup do registro de transações são possíveis. </li> <li>Qualquer banco de dados no modelo de recuperação simples não permite o backup de registros de transações.</li> | Modifica as configurações de banco de dados, de modo que todos os tipos na política de backup têm suporte. Ou, altere a política atual para incluir apenas os tipos de backup suportados. Caso contrário, os tipos de backup não suportados serão ignorados durante o backup programado ou o trabalho de backup falhará para backup demanda.

### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| Este banco de dados SQL não dá suporte para o tipo de backup solicitado. | Ocorre quando o modelo de recuperação de banco de dados não permite o tipo de backup solicitado. O erro pode ocorrer nas seguintes situações: <br/><ul><li>Um banco de dados que está usando um modelo de recuperação simples não permite backup de log.</li><li>Backups diferenciais e de log não são permitidos para um banco de dados mestre.</li></ul>Para obter mais detalhes, consulte a documentação dos modelos de recuperação do [SQL Server.](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server) | Se o backup de log falhar para o banco de dados no modelo de recuperação simples, tente uma dessas opções:<ul><li>Se o banco de dados estiver no modo de recuperação simples, desabilite os backups de log.</li><li>Use a documentação do [SQL Server](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server) para alterar o modelo de recuperação do banco de dados para logatcompleto ou em massa. </li><li> Se você não quiser alterar o modelo de recuperação e tiver uma política padrão para fazer backup de vários bancos de dados que não podem ser alterados, ignore o erro. Os backups diferenciais e completos funcionarão por agendamento. Os backups de log serão ignorados, o que é esperado neste caso.</li></ul>Se for um banco de dados mestre e você tiver configurado backup diferencial ou log, use qualquer uma das seguintes etapas:<ul><li>Use o portal para alterar o cronograma de política de backup para o banco de dados mestre, para completo.</li><li>Se você tiver uma política padrão para fazer backup de vários bancos de dados que não podem ser alterados, ignore o erro. O backup completo funcionará por agendamento. Backups de log ou diferenciais não ocorrerão, o que é esperado neste caso.</li></ul> |
| Operação cancelada como uma operação conflitante já em execução no mesmo banco de dados. | Consulte a entrada do [blog sobre as limitações de backup e restauração](https://deep.data.blog/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database/) que são executadas simultaneamente.| [Use o SQL Server Management Studio (SSMS) para monitorar os trabalhos de backup](manage-monitor-sql-database-backup.md). Depois que a operação conflitante falhar, reinicie a operação.|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| Banco de dados SQL não existe. | O banco de dados foi excluído ou renomeado. | Verifique se o banco de dados foi excluído ou renomeado acidentalmente.<br/><br/> Se o banco de dados foi excluído acidentalmente, para continuar os backups, restaure o banco de dados para o local original.<br/><br/> Se você excluiu o banco de dados e não precisa de backups futuros, então no cofre serviços de recuperação, **selecione Parar backup** com **Reter dados de backup** ou excluir dados de **backup**. Para obter mais informações, consulte [Gerenciar e monitorar bancos de dados SQL Server](manage-monitor-sql-database-backup.md)de backup .

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| A cadeia de logs está desfeita. | O banco de dados ou a VM são copiados através de outra solução de backup, que trunca a cadeia de log.|<ul><li>Verifique se outra solução de backup ou script está em uso. Nesse caso, pare a outra solução de backup. </li><li>Se o backup for um backup de log demanda, acione um backup completo para iniciar uma nova cadeia de log. Para backups de log agendados, nenhuma ação é necessária porque o serviço de backup do Azure acionará automaticamente um backup completo para corrigir esse problema.</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| O Backup do Azure não pode conectar a instância do SQL. | O Backup do Azure não pode se conectar à instância do SQL Server. | Use os detalhes adicionais no menu de erro do portal Azure para reduzir as causas básicas. Consulte [Solucionar problemas de backup do SQL](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine) para corrigir o erro.<br/><ul><li>Se as configurações sql padrão não permitirem conexões remotas, altere as configurações. Consulte os seguintes artigos para obter informações sobre a alteração das configurações:<ul><li>[MSSQLSERVER_-1](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-1-database-engine-error?view=sql-server-ver15)</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>Se houver problemas de login, use esses links para corrigi-los:<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| O primeiro backup completo está faltando para essa fonte de dados. | O backup completo está faltando para o banco de dados. Log e backups diferenciais são pais de um backup completo, por isso não deixe de fazer backups completos antes de acionar backups diferenciais ou log. | Acione um backup completo demanda.   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| Não é possível fazer backup como log de transações para a fonte de dados que está completo. | O espaço de log transacional do banco de dados está completo. | Para corrigir esse problema, consulte a [documentação do SQL Server](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error). |

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| Banco de dados com o mesmo nome já existe no local de destino | O destino de restauração de destino já possui um banco de dados com o mesmo nome.  | <ul><li>Mude o nome do banco de dados de destino.</li><li>Ou, use a opção de sobregravação de força na página de restauração.</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| A restauração falhou porque o banco de dados não pôde ficar offline. | Enquanto você está fazendo uma restauração, o banco de dados de destino precisa ser desligado. O Azure Backup não pode desligar esses dados. | Use os detalhes adicionais no menu de erro do portal Azure para reduzir as causas básicas. Para obter mais informações, consulte a [documentação do SQL Server](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms). |

### <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| Não é possível localizar o certificado do servidor com impressão digital no destino. | O banco de dados mestre na instância de destino não tem uma impressão digital de criptografia válida. | Importe a impressão digital do certificado válido usada na instância de origem para a instância de destino. |

### <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| O backup de log usado para a recuperação contém alterações bulk-logged. Ele não pode ser usado para parar em um ponto arbitrário de acordo com as diretrizes do SQL. | Quando um banco de dados está no modo de recuperação em massa, os dados entre uma transação em massa e a próxima transação de log não podem ser recuperados. | Escolha um ponto diferente no tempo para recuperação. [Saiba mais](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server?view=sql-server-ver15).

### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| A preferência de backup para o Grupo de Disponibilidade AlwaysOn do SQL não pode ser atendida, pois alguns nós do Grupo de Disponibilidade não estão registrados. | Os nós necessários para executar backups não estão registrados ou estão inacessíveis. | <ul><li>Certifique-se de que todos os nós necessários para realizar backups deste banco de dados sejam registrados e saudáveis e, em seguida, tente novamente a operação.</li><li>Alterar a preferência de backup para o grupo de disponibilidade SQL Server Always On.</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| A VM do SQL Server está desligada e inacessível para serviço de Backup do Azure. | A VM está fechada. | Certifique-se de que a instância do SQL Server está sendo executado. |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| O serviço de Backup do Azure usa agente convidado de VM do Azure para fazer backup, mas o agente convidado não está disponível no servidor de destino. | O agente convidado não está habilitado ou não está saudável. | [Instalar o agente convidado de VM](../virtual-machines/extensions/agent-windows.md) manualmente. |

### <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| Intenção de proteção automática foi removida ou não é mais válida. | Quando você habilita ristarizar a proteção automática em uma instância do SQL Server, **configure** trabalhos de backup executados para todos os bancos de dados nesse caso. Se você desabilitar a proteção automática, enquanto os trabalhos estão em execução, os trabalhos **Em andamento** são cancelados com esse código de erro. | Habilite a proteção automática mais uma vez para ajudar a proteger todos os bancos de dados restantes. |

### <a name="clouddosabsolutelimitreached"></a>ClouddosAbsoluteLimitreached

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
A operação está bloqueada, pois você atingiu o limite de número de operações permitidas em 24 horas. | Quando você atingiu o limite máximo permitido para uma operação em um período de 24 horas, este erro vem. <br> Por exemplo: Se você tiver atingido o limite para o número de trabalhos de backup configurados que podem ser acionados por dia e tentar configurar backup em um novo item, verá esse erro. | Normalmente, tentar novamente a operação após 24 horas resolve esse problema. No entanto, se o problema persistir, você pode entrar em contato com o suporte da Microsoft para obter ajuda.

### <a name="clouddosabsolutelimitreachedwithretry"></a>CloudDosAbsoluteLimitreachedWithRetry

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
A operação está bloqueada, pois o cofre atingiu seu limite máximo para tais operações permitidas em um período de 24 horas. | Quando você atingiu o limite máximo permitido para uma operação em um período de 24 horas, este erro vem. Esse erro geralmente ocorre quando há operações em escala, como modificar a política ou a proteção automática. Ao contrário do caso do CloudDosAbsoluteLimitReached, não há muito que você possa fazer para resolver este estado, na verdade, o serviço de backup do Azure irá tentar novamente as operações internamente para todos os itens em questão.<br> Por exemplo: Se você tiver um grande número de fontes de dados protegidas com uma política e tentar modificar essa diretiva, ela acionará trabalhos de proteção configurados para cada um dos itens protegidos e, às vezes, poderá atingir o limite máximo permitido para tais operações por dia.| O serviço de backup do Azure irá automaticamente tentar essa operação após 24 horas.

### <a name="usererrorvminternetconnectivityissue"></a>UserErrorVMInternetConnectivityIssue

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
A VM não pode entrar em contato com o serviço de backup do Azure devido a problemas de conectividade com a internet. | A VM precisa de conectividade de saída para os serviços azure backup service, azure storage ou a azure Active Directory.| - Se você usar o NSG para restringir a conectividade, então você deve usar a tag de serviço AzureBackup para permitir o acesso de saída aos serviços de backup do Azure para o Azure Backup Service, Azure Storage ou Azure Active Directory. Siga [estas etapas](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#allow-access-using-nsg-tags) para conceder acesso.<br>- Certifique-se de que o DNS está resolvendo os pontos finais do Azure.<br>- Verifique se a VM está por trás de um balanceador de carga que bloqueia o acesso à internet. Ao atribuir IP público às VMs, a descoberta funcionará.<br>- Verifique se não há firewall/antivírus/proxy que bloqueie chamadas para os três serviços-alvo acima.

## <a name="re-registration-failures"></a>Falhas de reregistro

Verifique se há um ou mais dos seguintes sintomas antes de acionar a operação de re-registro:

* Todas as operações (como backup, restauração e configuração de backup) estão falhando na VM com um dos seguintes códigos de erro: **WorkloadExtensionNotReachable**, **UserErrorWorkloadExtensionNotInstalled,** **WorkloadExtensionNotPresent,** **WorkloadExtensionDidn**
* Se a área **de status de backup** do item de backup estiver mostrando não **acessível,** exclua todas as outras causas que possam resultar no mesmo status:

  * Falta de permissão para executar operações relacionadas ao backup na VM.
  * Desligamento da VM, portanto, os backups não podem ocorrer.
  * Problemas de rede.

   ![recadastramento VM](./media/backup-azure-sql-database/re-register-vm.png)



* No caso de um grupo de disponibilidade Always On, os backups começaram a falhar depois que você alterou a preferência de backup ou após um failover.

Esses sintomas podem surgir por uma ou mais das seguintes razões:

* Uma extensão foi excluída ou desinstalada do portal.
* Uma extensão foi desinstalada do Painel de **Controle** na VM em **Desinstalar ou Alterar um Programa**.
* A VM foi restaurada no tempo através da restauração do disco no local.
* A VM foi desligada por um período prolongado, então a configuração de extensão nele expirou.
* A VM foi excluída, e outra VM foi criada com o mesmo nome e no mesmo grupo de recursos da VM excluída.
* Um dos nós do grupo de disponibilidade não recebeu a configuração completa de backup. Isso pode acontecer quando o grupo de disponibilidade é registrado no cofre ou quando um novo nó é adicionado.

Nos cenários anteriores, recomendamos que você acione uma operação de re-registro na VM. Veja [aqui](https://docs.microsoft.com/azure/backup/backup-azure-sql-automation#enable-backup) as instruções sobre como executar esta tarefa no PowerShell.

## <a name="size-limit-for-files"></a>Limite de tamanho para arquivos

O tamanho total da seqüência de arquivos depende não apenas do número de arquivos, mas também de seus nomes e caminhos. Para cada arquivo de banco de dados, obtenha o nome do arquivo lógico e o caminho físico. Você pode usar esta consulta SQL:

```sql
SELECT mf.name AS LogicalName, Physical_Name AS Location FROM sys.master_files mf
               INNER JOIN sys.databases db ON db.database_id = mf.database_id
               WHERE db.name = N'<Database Name>'"
```

Agora organize-os no seguinte formato:

```json
[{"path":"<Location>","logicalName":"<LogicalName>","isDir":false},{"path":"<Location>","logicalName":"<LogicalName>","isDir":false}]}
```

Aqui está um exemplo:

```json
[{"path":"F:\\Data\\TestDB12.mdf","logicalName":"TestDB12","isDir":false},{"path":"F:\\Log\\TestDB12_log.ldf","logicalName":"TestDB12_log","isDir":false}]}
```

Se o tamanho da seqüência do conteúdo exceder 20.000 bytes, os arquivos do banco de dados serão armazenados de forma diferente. Durante a recuperação, você não será capaz de definir o caminho do arquivo de destino para restauração. Os arquivos serão restaurados para o caminho SQL padrão fornecido pelo SQL Server.

### <a name="override-the-default-target-restore-file-path"></a>Substituir o caminho de arquivo de restauração de destino padrão

Você pode substituir o caminho do arquivo de restauração de destino durante a operação de restauração, colocando um arquivo JSON que contém o mapeamento do arquivo do banco de dados para o caminho de restauração de destino. Crie `database_name.json` um arquivo e coloque-o no local *C:\Arquivos do programa\Azure Workload Backup\bin\plugins\SQL*.

O conteúdo do arquivo deve estar neste formato:

```json
[
  {
    "Path": "<Restore_Path>",
    "LogicalName": "<LogicalName>",
    "IsDir": "false"
  },
  {
    "Path": "<Restore_Path>",
    "LogicalName": "LogicalName",
    "IsDir": "false"
  },  
]
```

Aqui está um exemplo:

```json
[
  {
   "Path": "F:\\Data\\testdb2_1546408741449456.mdf",
   "LogicalName": "testdb7",
   "IsDir": "false"
  },
  {
    "Path": "F:\\Log\\testdb2_log_1546408741449456.ldf",
    "LogicalName": "testdb7_log",
    "IsDir": "false"
  },  
]
```

No conteúdo anterior, você pode obter o nome lógico do arquivo de banco de dados usando a seguinte consulta SQL:

```sql
SELECT mf.name AS LogicalName FROM sys.master_files mf
                INNER JOIN sys.databases db ON db.database_id = mf.database_id
                WHERE db.name = N'<Database Name>'"
  ```

Este arquivo deve ser colocado antes de iniciar a operação de restauração.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure Backup for SQL Server VMs (visualização pública), consulte [O Backup do Azure para VMs SQL](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup).
