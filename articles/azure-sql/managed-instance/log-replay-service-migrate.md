---
title: Migrar bancos de dados para o SQL Instância Gerenciada usando o serviço de reprodução de log
description: Saiba como migrar bancos de dados do SQL Server para o SQL Instância Gerenciada usando o serviço de reprodução de log
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: sstein
ms.date: 03/01/2021
ms.openlocfilehash: 74403b7ec1469ce7cdaadc9931eb5ac95f55f6f5
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096829"
---
# <a name="migrate-databases-from-sql-server-to-sql-managed-instance-using-log-replay-service-preview"></a>Migrar bancos de dados do SQL Server para o SQL Instância Gerenciada usando o serviço de reprodução de log (versão prévia)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este artigo explica como configurar manualmente a migração de banco de dados do SQL Server 2008-2019 para o SQL Instância Gerenciada usando o LRS (serviço de reprodução de log) atualmente em visualização pública. Esse é um serviço de nuvem habilitado para Instância Gerenciada com base na tecnologia de envio de logs do SQL Server. LRS deve ser usado em casos em que existam migrações personalizadas complexas e arquiteturas híbridas, quando mais controle for necessário, quando houver pouca tolerância para tempo de inatividade ou quando o serviço de migração de dados do Azure (DMS) não puder ser usado.

O DMS e o LRS usam a mesma tecnologia de migração subjacente e as mesmas APIs. Com o lançamento do LRS, estamos habilitando ainda mais migrações personalizadas complexas e arquitetura híbrida entre o local. Instâncias gerenciadas do SQL Server e do SQL.

## <a name="when-to-use-log-replay-service"></a>Quando usar o serviço de reprodução de log

Nos casos em que o [Azure DMS](/azure/dms/tutorial-sql-server-to-managed-instance.md) não pode ser usado para migração, o serviço de nuvem LRS pode ser usado diretamente com o PowerShell, cmdlets da CLI ou API para criar e orquestrar manualmente as migrações de banco de dados para o SQL instância gerenciada. 

Talvez você queira considerar o uso do serviço de nuvem LRS em alguns dos seguintes casos:
- Mais controle é necessário para seu projeto de migração de banco de dados
- Existe uma pequena tolerância para o tempo de inatividade na transferência de migração
- O executável DMS não pode ser instalado em seu ambiente
- O executável DMS não tem acesso a arquivos para backups de banco de dados
- Nenhum acesso ao sistema operacional do host está disponível ou nenhum privilégio de administrador
- Não é possível abrir as portas de rede do seu ambiente para o Azure
- Os backups são armazenados diretamente no armazenamento de BLOBs do Azure usando a opção para URL
- Existe uma necessidade de usar backups diferenciais

> [!NOTE]
> A maneira automatizada recomendada de migrar bancos de dados do SQL Server para o SQL Instância Gerenciada está usando o Azure DMS. Este serviço está usando o mesmo serviço de nuvem LRS no back-end com o envio de logs no modo NORECOVERY. Você deve considerar manualmente o uso de LRS para orquestrar as migrações em casos em que o Azure DMS não dá suporte completo aos seus cenários.

## <a name="how-does-it-work"></a>Como ele funciona

Criar uma solução personalizada usando o LRS para migrar bancos de dados para a nuvem requer várias etapas de orquestração mostradas no diagrama e descritas na tabela a seguir.

A migração consiste em fazer backups de banco de dados completos no SQL Server com a soma de verificação habilitada e copiar arquivos de backup para o armazenamento de blob do Azure. LRS é usado para restaurar arquivos de backup do armazenamento de BLOBs do Azure para o SQL Instância Gerenciada. O armazenamento de BLOBs do Azure é usado como um armazenamento intermediário entre SQL Server e Instância Gerenciada do SQL.

O LRS monitorará o armazenamento de BLOBs do Azure para qualquer novo diferencial ou backups de log adicionados após a restauração do backup completo e irá restaurar automaticamente os novos arquivos adicionados. O progresso dos arquivos de backup que estão sendo restaurados no SQL Instância Gerenciada pode ser monitorado usando o serviço, e o processo também pode ser anulado, se necessário.

O LRS não requer uma Convenção de nomenclatura de arquivo de backup específica, pois verifica todos os arquivos colocados no armazenamento de BLOBs do Azure e constrói a cadeia de backup da leitura somente dos cabeçalhos de arquivo. Os bancos de dados estão no estado "restaurando" durante o processo de migração, pois eles são restaurados no modo [NORECOVERY](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql#comparison-of-recovery-and-norecovery) e não podem ser usados para leitura ou gravação até que o processo de migração tenha sido totalmente concluído. 

Na migração de vários bancos de dados, os backups de cada banco precisam ser colocados em uma pasta separada no armazenamento de BLOBs do Azure. O LRS precisa ser iniciado separadamente para cada banco de dados e caminhos diferentes para separar as pastas do armazenamento de BLOBs do Azure precisam ser especificados. 

LRS pode ser iniciado em modo de preenchimento automático ou contínuo. Quando iniciado no modo de preenchimento automático, a migração será concluída automaticamente quando o último nome de arquivo de backup especificado tiver sido restaurado. Quando iniciado no modo contínuo, o serviço irá restaurar continuamente todos os novos arquivos de backup adicionados e a migração será concluída apenas na transferência manual. É recomendável que a transferência manual seja executada somente depois que o backup final da cauda do log tiver sido realizado e exibido como restaurado no SQL Instância Gerenciada. A etapa final de transferência fará com que o banco de dados fique online e disponível para uso de leitura e gravação no SQL Instância Gerenciada.

Depois que o LRS for interrompido, automaticamente no preenchimento automático ou manualmente na transferência, o processo de restauração não poderá ser retomado para um banco de dados que foi colocado online no SQL Instância Gerenciada. Para restaurar arquivos de backup adicionais depois que a migração for concluída por meio do preenchimento automático ou manualmente na transferência, o banco de dados precisará ser excluído e toda a cadeia de backup precisará ser restaurada do zero reiniciando o LRS.

   :::image type="content" source="./media/log-replay-service-migrate/log-replay-service-conceptual.png" alt-text="Etapas de orquestração do serviço de reprodução de log explicadas para o SQL Instância Gerenciada" border="false":::
    
| Operação | Detalhes |
| :----------------------------- | :------------------------- |
| **1. copiar backups de banco de dados de SQL Server para o armazenamento de BLOBs do Azure**. | -Copie backups completos, diferenciais e de log de SQL Server para o contêiner de armazenamento de BLOBs do Azure usando [Azcopy](/azure/storage/common/storage-use-azcopy-v10)ou [Gerenciador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/). <br />-Use qualquer nome de arquivo, pois LRS não requer uma Convenção de nomenclatura de arquivo específica.<br />-Em migrar vários bancos de dados, é necessária uma pasta separada para cada banco de dados. |
| **2. Inicie o serviço LRS na nuvem**. | -O serviço pode ser iniciado com uma opção de cmdlets: <br /> PowerShell [Start-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay) <br /> [Cmdlets az_sql_midb_log_replay_start](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start)da CLI. <br /> -Inicie o LRS separadamente para cada banco de dados diferente apontando para uma pasta de backup diferente no armazenamento de BLOBs do Azure. <br />-Uma vez iniciada, o serviço fará backups do contêiner de armazenamento de BLOBs do Azure e começará a restaurá-los no SQL Instância Gerenciada.<br /> -No caso de o LRS ter sido iniciado no modo contínuo, quando todos os backups inicialmente carregados forem restaurados, o serviço observará os novos arquivos carregados na pasta e aplicará continuamente os logs com base na cadeia LSN, até que o serviço seja interrompido. |
| **2,1. Monitore o progresso da operação**. | -O progresso da operação de restauração pode ser monitorado com uma opção ou cmdlets: <br /> [Get-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay) do PowerShell <br /> [Cmdlets az_sql_midb_log_replay_show](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show)da CLI. |
| **2,2. Stop\abort a operação, se necessário**. | -No caso de o processo de migração precisar ser anulado, a operação poderá ser interrompida com uma opção de cmdlets: <br /> PowerShell [Stop-azsqlinstancedatabaselogreplay]/PowerShell/Module/AZ.SQL/Stop-azsqlinstancedatabaselogreplay) <br /> Cmdlets [az_sql_midb_log_replay_stop](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop) da CLI. <br /><br />-Isso resultará na exclusão do banco de dados que está sendo restaurado no SQL Instância Gerenciada. <br />-Depois de interrompido, LRS não pode ser retomado para um banco de dados. O processo de migração deve ser reiniciado do zero. |
| **3. transferência para a nuvem quando estiver pronto**. | – Pare o aplicativo e a carga de trabalho. Faça o último backup da parte final do log e faça upload para o armazenamento de BLOBs do Azure.<br /> -Conclua a transferência iniciando a operação de conclusão LRS com uma opção de cmdlets: <br />PowerShell [concluído-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay) <br /> Cmdlets [az_sql_midb_log_replay_complete](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete) da CLI. <br /><br />-Isso fará com que o serviço LRS seja interrompido e o banco de dados fique online para uso de leitura e gravação no SQL Instância Gerenciada.<br /> -Redirecionar a cadeia de conexão do aplicativo do SQL Server para o SQL Instância Gerenciada. |

## <a name="requirements-for-getting-started"></a>Requisitos para introdução

### <a name="sql-server-side"></a>Lado SQL Server
- SQL Server 2008-2019
- Backup completo de bancos de dados (um ou vários arquivos)
- Backup diferencial (um ou vários arquivos)
- Backup de log (não dividido para o arquivo de log de transações)
- A **soma de verificação deve ser habilitada** para backups (obrigatório)

### <a name="azure-side"></a>Lado do Azure
- Módulo AZ. SQL do PowerShell versão 2.16.0 ou superior ([instalar](https://www.powershellgallery.com/packages/Az.Sql/)ou usar o [Cloud Shell](/azure/cloud-shell/)do Azure)
- CLI versão 2.19.0 ou superior ([instalar](/cli/azure/install-azure-cli))
- Contêiner de armazenamento de BLOBs do Azure provisionado
- Token de segurança SAS com permissões somente **leitura** e **lista** geradas para o contêiner de armazenamento de BLOBs

### <a name="migrating-multiple-databases"></a>Migrando vários bancos de dados
- Os arquivos de backup para bancos de dados diferentes devem ser colocados em pastas separadas no armazenamento de BLOBs do Azure.
- LRS precisa ser iniciado separadamente para cada banco de dados que aponta para uma pasta apropriada no armazenamento de BLOBs do Azure.
- O LRS pode dar suporte a até 100 processos de restauração simultâneas por Instância Gerenciada único do SQL.

### <a name="azure-rbac-permissions-required"></a>Permissões do RBAC do Azure necessárias
Executar o LRS por meio dos clientes fornecidos requer uma das seguintes funções do Azure:
- Função de proprietário da assinatura ou
- [Instância gerenciada função colaborador](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) ou
- Função personalizada com a seguinte permissão:
  - `Microsoft.Sql/managedInstances/databases/*`

## <a name="best-practices"></a>Melhores práticas

Os itens a seguir são altamente recomendados como práticas recomendadas:
- Execute [Assistente de migração de dados](/sql/dma/dma-overview) para validar se os bancos de dados estão prontos para serem migrados para o SQL instância gerenciada. 
- Divida os backups completos e diferenciais em vários arquivos, em vez de um único arquivo.
- Habilite a compactação de backup.
- Use Cloud Shell para executar scripts, pois eles sempre serão atualizados para os cmdlets mais recentes lançados.
- Planeje concluir a migração dentro de 47 horas desde que o serviço LRS foi iniciado. Este é um período de carência que impede patches de software gerenciado pelo sistema depois que o LRS tiver sido iniciado.

> [!IMPORTANT]
> - O banco de dados que está sendo restaurado usando LRS não pode ser usado até que o processo de migração tenha sido concluído.
> - O acesso somente leitura aos bancos de dados durante a migração não é suportado pelo LRS.
> - Após a conclusão da migração, o processo de migração é finalizado, pois o LRS não dá suporte à retomada da restauração.

## <a name="steps-to-execute"></a>Etapas para executar

### <a name="make-backups-on-the-sql-server"></a>Fazer backups no SQL Server

Os backups no SQL Server podem ser feitos com uma das duas opções a seguir:

- Faça backup no armazenamento em disco local e, em seguida, carregue arquivos no armazenamento de BLOBs do Azure, caso seu ambiente seja restritivo de backup direto para o armazenamento de BLOBs do Azure.
- Faça backup diretamente no armazenamento de BLOBs do Azure com a opção "para URL" no T-SQL, caso o ambiente e os procedimentos de segurança permitam fazer isso. 

Defina os bancos de dados que você deseja migrar para o modo de recuperação completa para permitir backups de log.

```SQL
-- To permit log backups, before the full database backup, modify the database to use the full recovery
USE master
ALTER DATABASE SampleDB
SET RECOVERY FULL
GO
```

Para fazer manualmente o backup completo, de comparação e de log do seu banco de dados no armazenamento local, use os scripts T-SQL de exemplo fornecidos abaixo. Verifique se a opção CHECKSUM está habilitada, pois esse é um requisito obrigatório para LRS.

```SQL
-- Example on how to make full database backup to the local disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO

-- Example on how to make differential database backup to the locak disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_diff.bak'
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example on how to make the transactional log backup to the local disk
BACKUP LOG [SampleDB]
TO DISK='C:\BACKUP\SampleDB_log.trn'
WITH COMPRESSION, CHECKSUM
GO
```

### <a name="create-azure-blob-storage"></a>Criar armazenamento de BLOBs do Azure

O armazenamento de BLOBs do Azure é usado como um armazenamento intermediário para arquivos de backup entre SQL Server e Instância Gerenciada SQL. Para criar uma nova conta de armazenamento e um contêiner de blob dentro da conta de armazenamento, siga estas etapas:

1. [Criar uma conta de armazenamento](../../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Criar um contêiner de blob](../../storage/blobs/storage-quickstart-blobs-portal.md) dentro da conta de armazenamento

### <a name="copy-backups-from-sql-server-to-azure-blob-storage"></a>Copiar backups de SQL Server para o armazenamento de BLOBs do Azure

Algumas das abordagens a seguir podem ser utilizadas para carregar backups no armazenamento de BLOBs na migração de bancos de dados para a instância gerenciada usando o LRS:
- Usando [Azcopy](/azure/storage/common/storage-use-azcopy-v10)ou [Gerenciador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer) para carregar backups em um contêiner de BLOB.
- Usando Gerenciador de Armazenamento no portal do Azure.

### <a name="make-backups-from-sql-server-directly-to-azure-blob-storage"></a>Fazer backups de SQL Server diretamente para o armazenamento de BLOBs do Azure

Caso sua política corporativa e de rede permita, a maneira alternativa é fazer backups de SQL Server diretamente para o armazenamento de BLOBs do Azure usando a opção SQL Server [backup nativo para URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) . Se você puder buscar essa opção, não será necessário fazer backups no armazenamento local e carregá-los no armazenamento de BLOBs do Azure.

Como a primeira etapa, essa operação requer que o token de autenticação SAS seja gerado para o armazenamento de BLOBs do Azure, e o token precisa ser importado para o SQL Server. A segunda etapa é fazer backups com a opção "para URL" no T-SQL. Verifique se todos os backups são feitos com a opção CHEKSUM habilitada.

Para referência, o código de exemplo para fazer backups para o armazenamento de BLOBs do Azure é fornecido abaixo. Este exemplo não inclui instruções sobre como importar o token SAS. Instruções detalhadas, incluindo como gerar e importar o token SAS para SQL Server são fornecidas no seguinte tutorial: usar o [serviço de armazenamento de BLOBs do Azure com SQL Server](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#1---create-stored-access-policy-and-shared-access-storage). 

```SQL
-- Example on how to make full database backup to URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO

-- Example on how to make differential database backup to URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_diff.bak'  
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example on how to make the transactional log backup to URL
BACKUP LOG [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_log.trn'  
WITH COMPRESSION, CHECKSUM
```

### <a name="generate-azure-blob-storage-sas-authentication-for-lrs"></a>Gerar autenticação de SAS do armazenamento de BLOBs do Azure para LRS

O armazenamento de BLOBs do Azure é usado como um armazenamento intermediário para arquivos de backup entre SQL Server e Instância Gerenciada SQL. O token de autenticação SAS com permissões de lista e somente leitura precisa ser gerado para uso pelo serviço LRS. Isso permitirá que o serviço LRS acesse o armazenamento de BLOBs do Azure e use os arquivos de backup para restaurá-los no SQL Instância Gerenciada. Siga estas etapas para gerar a autenticação SAS para uso do LRS:

1. Acessar o Gerenciador de Armazenamento de portal do Azure

2. Expanda Contêineres de Blob

3. Clique com o botão direito do mouse no contêiner de BLOB e selecione obter assinatura de acesso compartilhado

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-01.png" alt-text="Serviço de reprodução de log-obter assinatura de acesso compartilhado":::

4. Selecione o período de expiração do token. Verifique se o token é válido para duração da sua migração.

5. Selecione o fuso horário para o token-UTC ou sua hora local

   - O fuso horário do token e o SQL Instância Gerenciada podem não ser compatíveis. Verifique se o token SAS tem a validade de tempo apropriada levando os fusos horários em consideração. Se possível, defina o fuso horário para uma hora anterior e posterior da sua janela de migração planejada.

6. Selecionar somente permissões de leitura e listar somente

   - Nenhuma outra permissão deve ser selecionada ou, caso contrário, LRS não será capaz de iniciar. Esse requisito de segurança é por design.

7. Clique no botão criar

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-02.png" alt-text="Serviço de reprodução de log-gerar token de autenticação SAS":::

   A autenticação SAS será gerada com a validade de tempo especificada anteriormente. Você precisará da versão de URI do token gerado-conforme mostrado na captura de tela abaixo.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-generated-uri-token.png" alt-text="Serviço de reprodução de log – copiar a assinatura de acesso compartilhado de URI":::

### <a name="copy-parameters-from-sas-token-generated"></a>Copiar parâmetros do token SAS gerado

Para poder usar corretamente o token SAS para iniciar o LRS, precisamos entender sua estrutura. O URI do token SAS gerado consiste em duas partes:
- StorageContainerUri e 
- StorageContainerSasToken, separados por um ponto de interrogação (?), conforme mostrado na imagem abaixo.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-structure.png" alt-text="Exemplo de URI de autenticação SAS gerado pelo serviço de reprodução de log" border="false":::

- A primeira parte começando com "https://" até que o ponto de interrogação (?) seja usado para o parâmetro StorageContainerURI que é alimentado como entrada em LRS. Isso fornece informações de LRS sobre a pasta em que os arquivos de backup de banco de dados são armazenados.
- A segunda parte, começando após o ponto de interrogação (?), no exemplo "SP =" e até o final da cadeia de caracteres é o parâmetro StorageContainerSasToken. Esse é o token de autenticação assinado real, válido durante o tempo especificado. Essa parte não precisa necessariamente começar com "SP =", conforme mostrado, e que seu caso pode ser diferente.

Copie os parâmetros da seguinte maneira:

1. Copie a primeira parte do token começando do https://até o ponto de interrogação (?) e use-o como parâmetro StorageContainerUri no PowerShell ou CLI para iniciar o LRS, conforme mostrado na captura de tela abaixo.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-01.png" alt-text="Parâmetro StorageContainerUri de cópia do serviço de reprodução de log":::

2. Copie a segunda parte do token começando do ponto de interrogação (?), até o final da cadeia de caracteres, e use-o como parâmetro StorageContainerSasToken no PowerShell ou na CLI para iniciar o LRS, conforme mostrado na captura de tela abaixo.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-02.png" alt-text="Parâmetro StorageContainerSasToken de cópia do serviço de reprodução de log":::

> [!IMPORTANT]
> - As permissões para o token SAS para o armazenamento de BLOBs do Azure precisam ser somente leitura e lista. Se qualquer outra permissão for concedida para o token de autenticação SAS, a inicialização do serviço LRS falhará. Esses requisitos de segurança são por design.
> - O token deve ter a validade de tempo apropriada. Verifique se os fusos horários entre o token e a instância gerenciada são levados em consideração.
> - Verifique se o parâmetro StorageContainerUri do PowerShell ou da CLI é copiado do URI do token gerado, Iniciando em https://até que o sinal de perguntas (?). Não inclua o ponto de interrogação.
> Verifique se o parâmetro StorageContainerSasToken do PowerShell da CLI é copiado do URI do token gerado, começando do ponto de interrogação (?), até o final da cadeia de caracteres. Não inclua o ponto de interrogação.

### <a name="log-in-to-azure-and-select-subscription"></a>Faça logon no Azure e selecione assinatura

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

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" `
    -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D" `
    -AutoCompleteRestore `
    -LastBackupName "last_backup.bak"
```

Inicie o LRS no modo de preenchimento automático – exemplo da CLI:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb -a --last-bn "backup.bak"
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="start-lrs-in-continuous-mode"></a>Iniciar LRS no modo contínuo

Inicie o LRS no modo contínuo – exemplo do PowerShell:

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

Inicie o LRS no modo contínuo – exemplo da CLI:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="scripting-lrs-start-in-continuous-mode"></a>Script de LRS iniciar no modo contínuo

Os clientes do PowerShell e da CLI para iniciar o LRS no modo contínuo são síncronos. Isso significa que os clientes aguardarão a resposta da API para relatar o êxito ou falha ao iniciar o trabalho. Durante essa espera, o comando não retornará o controle de volta para o prompt de comando. Caso você esteja criando um script da experiência de migração e exija o comando LRS Start para dar o controle de volta imediatamente para continuar com o restante do script, você pode executar o PowerShell como um trabalho em segundo plano com a opção-AsJob. Por exemplo: 

```PowerShell
$lrsjob = Start-AzSqlInstanceDatabaseLogReplay <required parameters> -AsJob
```

Quando você inicia um trabalho em segundo plano, um objeto de trabalho retorna imediatamente, mesmo se o trabalho levar um tempo estendido para ser concluído. É possível continuar a trabalhar na sessão sem interrupção enquanto o trabalho é executado. Para obter detalhes sobre como executar o PowerShell como um trabalho em segundo plano, consulte a documentação [Iniciar-trabalho do PowerShell](/powershell/module/microsoft.powershell.core/start-job#description) .

Da mesma forma, para iniciar um comando da CLI no Linux como um processo em segundo plano, use o sinal de e comercial (&) no final do comando LRS Start.

```CLI
az sql midb log-replay start <required parameters> &
```

> [!IMPORTANT]
> Depois que o LRS tiver sido iniciado, todos os patches de software gerenciado pelo sistema serão interrompidos pelas próximas 47 horas. Após a aprovação desta janela, o próximo patch de software automatizado interromperá automaticamente o LRS em andamento. Nesse caso, a migração não pode ser retomada e precisa ser reiniciada do zero. 

## <a name="monitor-the-migration-progress"></a>Monitorar o progresso da migração

Para monitorar o andamento da operação de migração, use o seguinte comando do PowerShell:

```PowerShell
Get-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Para monitorar o andamento da operação de migração, use o seguinte comando da CLI:

```CLI
az sql midb log-replay show -g mygroup --mi myinstance -n mymanageddb
```

## <a name="stop-the-migration"></a>Parar a migração

Caso você precise interromper a migração, use os cmdlets a seguir. Parar a migração excluirá o banco de dados de restauração no SQL Instância Gerenciada devido a qual não será possível retomar a migração.

Para stop\abort o processo de migração, use o seguinte comando do PowerShell:

```PowerShell
Stop-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Para stop\abort o processo de migração, use o seguinte comando da CLI:

```CLI
az sql midb log-replay stop -g mygroup --mi myinstance -n mymanageddb
```

## <a name="complete-the-migration-continuous-mode"></a>Concluir a migração (modo contínuo)

Caso o LRS seja iniciado no modo contínuo, depois de garantir que todos os backups foram restaurados, iniciar a transferência concluirá a migração. Após a conclusão da transferência, o banco de dados será migrado e estará pronto para acesso de leitura e gravação.

Para concluir o processo de migração no modo contínuo do LRS, use o seguinte comando do PowerShell:

```PowerShell
Complete-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
-InstanceName "ManagedInstance01" `
-Name "ManagedDatabaseName" `
-LastBackupName "last_backup.bak"
```

Para concluir o processo de migração no modo contínuo do LRS, use o seguinte comando da CLI:

```CLI
az sql midb log-replay complete -g mygroup --mi myinstance -n mymanageddb --last-backup-name "backup.bak"
```

## <a name="functional-limitations"></a>Limitações funcionais

As limitações funcionais do serviço de reprodução de log (LRS) são:
- O banco de dados que está sendo restaurado não pode ser usado para acesso somente leitura durante o processo de migração.
- Os patches de software gerenciado pelo sistema serão bloqueados por 47 horas desde o início do LRS. Após a expiração desta janela de tempo, a próxima atualização de software irá parar o LRS. Nesse caso, o LRS precisa ser reiniciado do zero.
- LRS exige que os bancos de dados no SQL Server seja feito backup com a opção de soma de verificação habilitada.
- O token SAS para uso pelo LRS precisa ser gerado para todo o contêiner de armazenamento de BLOBs do Azure e deve ter somente permissões de leitura e lista.
- Os arquivos de backup para bancos de dados diferentes devem ser colocados em pastas separadas no armazenamento de BLOBs do Azure.
- LRS precisa ser iniciado separadamente para cada banco de dados que aponta para pastas separadas com arquivos de backup no armazenamento de BLOBs do Azure.
- O LRS pode dar suporte a até 100 processos de restauração simultâneas por Instância Gerenciada único do SQL.

## <a name="troubleshooting"></a>Solução de problemas

Depois de iniciar o LRS, use os cmdlets de monitoramento (Get-azsqlinstancedatabaselogreplay ou az_sql_midb_log_replay_show) para ver o status da operação. Se, após algum tempo, o LRS falhar ao iniciar com um erro, verifique se há alguns dos problemas mais comuns:
- Já existe um banco de dados com o mesmo nome no SQL MI que você está tentando migrar de SQL Server? Resolva esse conflito renomeando um dos bancos de dados.
- O backup do banco de dados no SQL Server feito usando a opção **checksum** ?
- As permissões no token SAS são **lidas** e **listadas** somente para o serviço lRS?
- O token SAS para LRS foi copiado a partir do ponto de interrogação "?" com conteúdo que é semelhante a este "SV = 2020-02-10..."? 
- O tempo de **validade do token** SAS é aplicável para a janela de tempo de iniciar e concluir a migração? Pode haver incompatibilidades devido aos diferentes **fusos horários** usados para o SQL instância gerenciada e o token SAS. Tente regenerar o token SAS com a extensão da validade do token da janela de tempo antes e depois da data atual.
- O nome do banco de dados, o nome do grupo de recursos e o nome da instância gerenciada estão escritos corretamente?
- Se LRS foi iniciado no modo de preenchimento automático, um nome de arquivo válido para o último backup foi especificado?

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre como [migrar SQL Server para instância gerenciada do SQL](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md).
- Saiba mais sobre as [diferenças entre SQL Server e instância gerenciada do Azure SQL](transact-sql-tsql-differences-sql-server.md).
- Saiba mais sobre [as práticas recomendadas para cargas de trabalho de custo e de tamanho migradas para o Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs).
