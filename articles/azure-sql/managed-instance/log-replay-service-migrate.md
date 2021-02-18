---
title: Migrar bancos de dados para o SQL Instância Gerenciada usando o serviço de reprodução de log
description: Saiba como migrar bancos de dados do SQL Server para o SQL Instância Gerenciada usando o serviço de reprodução de log
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: sstein
ms.date: 02/17/2021
ms.openlocfilehash: 07da1d5dbfd6384751e01f5becccd7b7b4c97e99
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095223"
---
# <a name="migrate-databases-from-sql-server-to-sql-managed-instance-using-log-replay-service"></a>Migrar bancos de dados do SQL Server para o SQL Instância Gerenciada usando o serviço de reprodução de log
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este artigo explica como configurar manualmente a migração de banco de dados do SQL Server 2008-2019 para o SQL Instância Gerenciada usando o serviço de reprodução de log (LRS). Esse é um serviço de nuvem habilitado para Instância Gerenciada com base na tecnologia de envio de logs do SQL Server. LRS deve ser usado nos casos em que o serviço de migração de dados do Azure (DMS) não pode ser usado, quando mais controle é necessário ou quando existe pouca tolerância para tempo de inatividade.

## <a name="when-to-use-log-replay-service"></a>Quando usar o serviço de reprodução de log

Nos casos em que o [Azure DMS](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance) não pode ser usado para migração, o serviço de nuvem LRS pode ser usado diretamente com o PowerShell, cmdlets da CLI ou API para criar e orquestrar manualmente as migrações de banco de dados para o SQL instância gerenciada. 

Talvez você queira considerar o uso do serviço de nuvem LRS em alguns dos seguintes casos:
- Mais controle é necessário para seu projeto de migração de banco de dados
- Existe uma pequena tolerância para o tempo de inatividade na transferência de migração
- O executável DMS não pode ser instalado em seu ambiente
- O executável DMS não tem acesso a arquivos para backups de banco de dados
- Nenhum acesso ao sistema operacional do host está disponível ou nenhum privilégio de administrador

> [!NOTE]
> A maneira automatizada recomendada de migrar bancos de dados do SQL Server para o SQL Instância Gerenciada está usando o Azure DMS. Este serviço está usando o mesmo serviço de nuvem LRS no back-end com o envio de logs no modo NORECOVERY. Você deve considerar manualmente o uso de LRS para orquestrar as migrações em casos em que o Azure DMS não dá suporte completo aos seus cenários.

## <a name="how-does-it-work"></a>Como ele funciona

Criar uma solução personalizada usando o LRS para migrar um banco de dados para a nuvem requer várias etapas de orquestração mostradas no diagrama e descritas na tabela a seguir.

A migração envolve fazer backups de banco de dados completos em SQL Server e copiar arquivos de backup para o armazenamento de BLOBs do Azure. LRS é usado para restaurar arquivos de backup do armazenamento de BLOBs do Azure para o SQL Instância Gerenciada. O armazenamento de BLOBs do Azure é usado como um armazenamento intermediário entre SQL Server e Instância Gerenciada do SQL.

O LRS monitorará o armazenamento de BLOBs do Azure para qualquer novo diferencial ou backups de log adicionados após a restauração do backup completo e irá restaurar automaticamente os novos arquivos adicionados. O progresso dos arquivos de backup que estão sendo restaurados no SQL Instância Gerenciada pode ser monitorado usando o serviço, e o processo também pode ser anulado, se necessário. Os bancos de dados que estão sendo restaurados durante o processo de migração estarão em um modo de restauração e não poderão ser usados para leitura ou gravação até que o processo seja concluído.

LRS pode ser iniciado em modo de preenchimento automático ou contínuo. Quando iniciado no modo de preenchimento automático, a migração será concluída automaticamente quando o último arquivo de backup especificado tiver sido restaurado. Quando iniciado no modo contínuo, o serviço irá restaurar continuamente todos os novos arquivos de backup adicionados e a migração será concluída apenas na transferência manual. A etapa final de transferência tornará os bancos de dados disponíveis para uso de leitura e gravação no SQL Instância Gerenciada. 

  ![Etapas de orquestração do serviço de reprodução de log explicadas para o SQL Instância Gerenciada](./media/log-replay-service-migrate/log-replay-service-conceptual.png)

| Operação | Detalhes |
| :----------------------------- | :------------------------- |
| **1. copiar backups de banco de dados de SQL Server para o armazenamento de BLOBs do Azure**. | -Copie backups completos, diferenciais e de log de SQL Server para o contêiner de armazenamento de BLOBs do Azure usando [Azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10) ou [Gerenciador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/). <br />-Em migrar vários bancos de dados, é necessária uma pasta separada para cada banco de dados. |
| **2. Inicie o serviço LRS na nuvem**. | -O serviço pode ser iniciado com uma opção de cmdlets: <br /> PowerShell [Start-azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay) <br /> [Cmdlets az_sql_midb_log_replay_start](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start)da CLI. <br /><br />-Depois de iniciado, o serviço fará backups do contêiner de armazenamento de BLOBs do Azure e começará a restaurá-los na instância sqlmanaged. <br /> -Depois que todos os backups inicialmente carregados forem restaurados, o serviço observará os novos arquivos carregados na pasta e aplicará continuamente os logs com base na cadeia LSN, até que o serviço seja interrompido. |
| **2,1. Monitore o progresso da operação**. | -O progresso da operação de restauração pode ser monitorado com uma opção ou cmdlets: <br /> [Get-azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay) do PowerShell <br /> [Cmdlets az_sql_midb_log_replay_show](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show)da CLI. |
| **2,2. Stop\abort a operação, se necessário**. | -No caso de o processo de migração precisar ser anulado, a operação poderá ser interrompida com uma opção de cmdlets: <br /> PowerShell [Stop-azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/stop-azsqlinstancedatabaselogreplay) <br /> Cmdlets [az_sql_midb_log_replay_stop](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop) da CLI. <br /><br />-Isso fará com que a exclusão do banco de dados seja restaurada no SQL Instância Gerenciada. <br />-Depois de interrompido, LRS não pode ser continuado para um banco de dados. O processo de migração precisa ser reiniciado do zero. |
| **3. transferência para a nuvem quando estiver pronto**. | -Depois que todos os backups tiverem sido restaurados para a instância do SQL mnaged, conclua a transferência iniciando a operação de conclusão LRS com uma opção de chamada à API ou cmdlets: <br />PowerShell [concluído-azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay) <br /> Cmdlets [az_sql_midb_log_replay_complete](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete) da CLI. <br /><br />-Isso fará com que o serviço LRS seja interrompido e o banco de dados na instância gerenciada será recuperado. <br />-Redirecionar a cadeia de conexão do aplicativo do SQL Server para o SQL Instância Gerenciada. <br />-No banco de dados de conclusão de operação está disponível para operações de R/W na nuvem. |

## <a name="requirements-for-getting-started"></a>Requisitos para introdução

### <a name="sql-server-side"></a>Lado SQL Server
- SQL Server 2008-2019
- Backup completo de bancos de dados (um ou vários arquivos)
- Backup diferencial (um ou vários arquivos)
- Backup de log (não dividido para o arquivo de log de transações)
- A **soma de verificação deve ser habilitada** para backups (obrigatório)

### <a name="azure-side"></a>Lado do Azure
- Módulo AZ. SQL do PowerShell versão 2.16.0 ou superior ([instalar](https://www.powershellgallery.com/packages/Az.Sql/)ou usar o [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/)do Azure)
- CLI versão 2.19.0 ou superior ([instalar](https://docs.microsoft.com/cli/azure/install-azure-cli))
- Contêiner de armazenamento de BLOBs do Azure provisionado
- Token de segurança SAS com permissões somente **leitura** e **lista** geradas para o contêiner de armazenamento de BLOBs

## <a name="best-practices"></a>Melhores práticas

Os itens a seguir são altamente recomendados como práticas recomendadas:
- Executar [Assistente de migração de dados](https://docs.microsoft.com/sql/dma/dma-overview) para validar seus bancos de dados não terá nenhum problema migrado para o SQL instância gerenciada. 
- Divida os backups completos e diferenciais em vários arquivos, em vez de um único arquivo.
- Habilite a compactação de backup.
- Use Cloud Shell para executar scripts, pois eles sempre serão atualizados para os cmdlets mais recentes lançados.
- Planeje concluir a migração dentro de 47 horas desde que o serviço LRS foi iniciado.

> [!IMPORTANT]
> - O banco de dados que está sendo restaurado usando LRS não pode ser usado até que o processo de migração tenha sido concluído. Isso ocorre porque a tecnologia subjacente é o envio de logs no modo NORECOVERY.
> - O modo de espera para envio de logs não é suportado pelo LRS devido às diferenças de versão entre o SQL Instância Gerenciada e a versão mais recente do SQL Server no mercado.

## <a name="steps-to-execute"></a>Etapas para executar

## <a name="copy-backups-from-sql-server-to-azure-blob-storage"></a>Copiar backups de SQL Server para o armazenamento de BLOBs do Azure

As duas abordagens a seguir podem ser utilizadas para copiar backups para o armazenamento de BLOBs na migração de bancos de dados para a instância gerenciada usando o LRS:
- Usando SQL Server o [backup nativo para a funcionalidade de URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url) .
- Copiar os backups para o contêiner de BLOB usando [Azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)ou [Gerenciador de armazenamento do Azure](https://azure.microsoft.com/en-us/features/storage-explorer). 

## <a name="create-azure-blob-and-sas-authentication-token"></a>Criar um token de autenticação SAS e BLOB do Azure

O armazenamento de BLOBs do Azure é usado como um armazenamento intermediário para arquivos de backup entre SQL Server e Instância Gerenciada SQL. Siga estas etapas para criar o contêiner de armazenamento de BLOBs do Azure:

1. [Criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal)
2. [Criar um contêiner de blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) dentro da conta de armazenamento

Depois que um contêiner de blob tiver sido criado, gere o token de autenticação SAS com permissões de leitura e lista somente seguindo estas etapas:

1. Acessar a conta de armazenamento usando portal do Azure
2. Navegue até Gerenciador de Armazenamento
3. Expanda Contêineres de Blob
4. Clique com o botão direito do mouse no contêiner de BLOB
5. Selecione obter assinatura de acesso compartilhado
6. Selecione o período de expiração do token. Verifique se o token é válido para duração da sua migração.
7. Verifique se as permissões somente leitura e lista estão selecionadas
8. Clicar em Criar
9. Copie o token começando com "va =" no URI para uso em seu código

> [!IMPORTANT]
> As permissões para o token SAS para o armazenamento de BLOBs do Azure precisam ser somente leitura e lista. No caso de qualquer outra permissão concedida para o token de autenticação SAS, iniciar o serviço LRS falhará. Esses requisitos de segurança são por design.

## <a name="log-in-to-azure-and-select-subscription"></a>Faça logon no Azure e selecione assinatura

Use o seguinte cmdlet do PowerShell para fazer logon no Azure:

```powershell
Login-AzAccount
```

Selecione a assinatura apropriada em que o SQL Instância Gerenciada reside usando o seguinte cmdlet do PowerShell:

```powershell
Select-AzSubscription -SubscriptionId <subscription ID>
```

## <a name="start-the-migration"></a>Inicie a migração

A migração é iniciada iniciando o serviço LRS. O serviço pode ser iniciado em modo de preenchimento automático ou contínuo. Quando iniciado no modo de preenchimento automático, a migração será concluída automaticamente quando o último arquivo de backup especificado tiver sido restaurado. Essa opção requer que o comando Iniciar especifique o nome do arquivo do último backup. Quando o LRS for iniciado no modo contínuo, o serviço irá restaurar continuamente todos os novos arquivos de backup adicionados e a migração será concluída somente na transferência manual. 

### <a name="start-lrs-in-autocomplete-mode"></a>Iniciar o LRS no modo de preenchimento automático

Para iniciar o serviço LRS no modo de preenchimento automático, use os comandos do PowerShell ou da CLI a seguir. Especifique o nome do último arquivo de backup com o parâmetro-LastBackupName. Após a restauração do último nome de arquivo de backup especificado, o serviço iniciará automaticamente uma transferência.

Inicie o LRS no modo de preenchimento automático – exemplo do PowerShell:

```powershell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" `
    -StorageContainerUri "https://test.blob.core.windows.net/testing" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D" `
    -AutoComplete -LastBackupName "last_backup.bak"
```

Inicie o LRS no modo de preenchimento automático – exemplo da CLI:

```cli
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb -a --last-bn "backup.bak"
    --storage-uri "https://test.blob.core.windows.net/testing"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="start-lrs-in-continuous-mode"></a>Iniciar LRS no modo contínuo

Inicie o LRS no modo contínuo – exemplo do PowerShell:

```powershell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" -StorageContainerUri "https://test.blob.core.windows.net/testing" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

Inicie o LRS no modo contínuo – exemplo da CLI:

```cli
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb
    --storage-uri "https://test.blob.core.windows.net/testing"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

> [!IMPORTANT]
> Depois que o LRS tiver sido iniciado, todos os patches de software gerenciado pelo sistema serão interrompidos pelas próximas 47 horas. Após a aprovação desta janela, o próximo patch de software automatizado interromperá automaticamente o LRS em andamento. Nesse caso, a migração não pode ser retomada e precisa ser reiniciada do zero. 

## <a name="monitor-the-migration-progress"></a>Monitorar o progresso da migração

Para monitorar o andamento da operação de migração, use o seguinte comando do PowerShell:

```powershell
Get-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Para monitorar o andamento da operação de migração, use o seguinte comando da CLI:

```cli
az sql midb log-replay show -g mygroup --mi myinstance -n mymanageddb
```

## <a name="stop-the-migration"></a>Parar a migração

Caso você precise interromper a migração, use os cmdlets a seguir. Parar a migração excluirá o banco de dados de restauração no SQL Instância Gerenciada devido a qual não será possível retomar a migração.

Para stop\abort o processo de migração, use o seguinte comando do PowerShell:

```powershell
Stop-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Para stop\abort o processo de migração, use o seguinte comando da CLI:

```cli
az sql midb log-replay stop -g mygroup --mi myinstance -n mymanageddb
```

## <a name="complete-the-migration-continuous-mode"></a>Concluir a migração (modo contínuo)

Caso o LRS seja iniciado no modo contínuo, depois de garantir que todos os backups foram restaurados, iniciar a transferência concluirá a migração. Após a conclusão da transferência, o banco de dados será migrado e estará pronto para acesso de leitura e gravação.

Para concluir o processo de migração no modo contínuo do LRS, use o seguinte comando do PowerShell:

```powershell
Complete-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
-InstanceName "ManagedInstance01" `
-Name "ManagedDatabaseName" `
-LastBackupName "last_backup.bak"
```

Para concluir o processo de migração no modo contínuo do LRS, use o seguinte comando da CLI:

```cli
az sql midb log-replay complete -g mygroup --mi myinstance -n mymanageddb --last-backup-name "backup.bak"
```

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre como [migrar SQL Server para instância gerenciada do SQL](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md).
- Saiba mais sobre as [diferenças entre SQL Server e instância gerenciada do Azure SQL](transact-sql-tsql-differences-sql-server.md).
- Saiba mais sobre [as práticas recomendadas para cargas de trabalho de custo e de tamanho migradas para o Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs).
