---
title: Configurar a replicação em um banco de dados de instância gerenciada
description: Aprenda a configurar a replicação transacional entre um editor/distribuidor gerenciado de instâncias gerenciadas do Azure SQL e o assinante de instância gerenciada.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: ferno
ms.reviewer: mathoma
ms.date: 02/07/2019
ms.openlocfilehash: 9af7b471210ca3cc69428e68aef4aafaee159344
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299066"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>Configurar a replicação em um banco de dados de instância gerenciada do Banco de Dados SQL do Azure

A replicação transacional permite que você replique dados em um banco de dados de instância gerenciada do Banco de Dados SQL do Azure de um banco de dados do SQL Server ou de outro banco de dados de instância. 

Este artigo mostra como configurar a replicação entre um editor/distribuidor de instância gerenciada e um assinante de instância gerenciado. 

![Replicar entre duas instâncias gerenciadas](media/replication-with-sql-database-managed-instance/sqlmi-sqlmi-repl.png)

Você também pode usar a replicação transacional para empurrar alterações feitas em um banco de dados de instâncias na instância gerenciada do Azure SQL Database para:

- Um banco de dados sql server.
- Um único banco de dados no Banco de Dados SQL do Azure.
- Um banco de dados agrupado em um grupo elástico do Banco de Dados SQL Do Azure.
 
A replicação transacional está em visualização pública na instância gerenciada do [Azure SQL Database](sql-database-managed-instance.md). Uma instância gerenciada pode hospedar bancos de dados publicadores, distribuidores e assinantes. Consulte [as configurações de replicação transacional](sql-database-managed-instance-transactional-replication.md#common-configurations) para obter configurações disponíveis.

  > [!NOTE]
  > - Este artigo destina-se a orientar um usuário na configuração de replicação com uma instância gerenciada do Azure Database de ponta a ponta, começando pela criação do grupo de recursos. Se você já tiver instâncias gerenciadas implantadas, pule para o [Passo 4](#4---create-a-publisher-database) para criar seu banco de dados de editores ou [etapa 6](#6---configure-distribution) se você já tiver um banco de dados de editores e assinantes e estiver pronto para começar a configurar a replicação.  
  > - Este artigo configura seu editor e distribuidor na mesma instância gerenciada. Para colocar o distribuidor em uma instância manged separada, consulte o tutorial [Configurar a replicação entre um editor de MI e um distribuidor MI](sql-database-managed-instance-configure-replication-tutorial.md). 

## <a name="requirements"></a>Requisitos

Configurar uma instância gerenciada para funcionar como um editor e/ou um distribuidor requer:

- Que a instância gerenciada pelo editor está na mesma rede virtual que o distribuidor e o assinante, ou [o peering vNet](../virtual-network/tutorial-connect-virtual-networks-powershell.md) foi estabelecido entre as redes virtuais de todas as três entidades. 
- A conectividade usa Autenticação SQL entre os participantes da replicação.
- Um compartilhamento da Conta de Armazenamento do Azure para o diretório de trabalho de replicação.
- A porta 445 (saída tcp) está aberta nas regras de segurança do NSG para que as instâncias gerenciadas acessem o compartilhamento de arquivos do Azure.  Se você encontrar o erro "não se \<conectar ao nome da conta de armazenamento do Azure> com o erro do sistema operacional 53", você precisará adicionar uma regra de saída ao NSG da Sub-rede de instância gerenciada SQL apropriada.


 > [!NOTE]
 > Banco de dados individuais e em pool no Banco de Dados SQL do Azure só podem ser assinantes. 


## <a name="features"></a>Recursos

Oferece suporte a:

- Combinação de replicação transacional e de instantâneo das instâncias gerenciadas e locais do SQL Server no Banco de Dados SQL do Azure.
- Os assinantes podem estar em bancos de dados SQL Server no local, bancos de dados únicos/instâncias gerenciadas no Banco de Dados SQL do Azure ou bancos de dados agrupados em pools elásticos do Banco de Dados Azure SQL.
- Replicação unidirecional ou bidirecional.

Não há suporte para os seguintes recursos em uma instância gerenciada no Banco de Dados SQL do Azure:

- [Assinaturas updatable](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication).
- [Geo-replicação ativa](sql-database-active-geo-replication.md) com replicação transacional. Em vez de replicação geográfica ativa, use [grupos de failover automático,](sql-database-auto-failover-group.md)mas observe que a publicação deve ser [excluída manualmente](sql-database-managed-instance-transact-sql-information.md#replication) da instância gerenciada primária e recriada na instância gerenciada secundária após failover.  
 
## <a name="1---create-a-resource-group"></a>1 - Criar um grupo de recursos

Use o [portal Azure](https://portal.azure.com) para criar `SQLMI-Repl`um grupo de recursos com o nome .  

## <a name="2---create-managed-instances"></a>2 - Criar instâncias gerenciadas

Use o [portal Azure](https://portal.azure.com) para criar duas [instâncias gerenciadas](sql-database-managed-instance-create-tutorial-portal.md) na mesma rede virtual e sub-rede. Por exemplo, nomeie as duas instâncias gerenciadas:

- `sql-mi-pub`(juntamente com alguns caracteres para randomização)
- `sql-mi-sub`(juntamente com alguns caracteres para randomização)

Você também precisará [configurar uma VM Azure para se conectar](sql-database-managed-instance-configure-vm.md) às instâncias gerenciadas do Banco de Dados SQL do Azure. 

## <a name="3---create-azure-storage-account"></a>3 - Criar conta de armazenamento azure

[Crie uma conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) para o diretório de trabalho e, em seguida, crie um [compartilhamento de arquivo](../storage/files/storage-how-to-create-file-share.md) na conta de armazenamento. 

Copie o caminho do compartilhamento de arquivo no formato: `\\storage-account-name.file.core.windows.net\file-share-name`

Exemplo: `\\replstorage.file.core.windows.net\replshare`

Copie as teclas de acesso de armazenamento no formato de:`DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

Exemplo: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`

Para obter mais informações, confira [Gerenciar chaves de acesso da conta de armazenamento](../storage/common/storage-account-keys-manage.md). 

## <a name="4---create-a-publisher-database"></a>4 - Criar um banco de dados de editores

Conecte-se à instância `sql-mi-pub` gerenciada usando o SQL Server Management Studio e execute o seguinte código Transact-SQL (T-SQL) para criar seu banco de dados de editores:

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_PUB]
GO

USE [ReplTran_PUB]
GO
CREATE TABLE ReplTest (
    ID INT NOT NULL PRIMARY KEY,
    c1 VARCHAR(100) NOT NULL,
    dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO


USE [ReplTran_PUB]
GO

INSERT INTO ReplTest (ID, c1) VALUES (6, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (2, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (3, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (4, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (5, 'pub')
GO
SELECT * FROM ReplTest
GO
```

## <a name="5---create-a-subscriber-database"></a>5 - Criar um banco de dados de assinantes

Conecte-se à sua `sql-mi-sub` instância gerenciada usando o SQL Server Management Studio e execute o seguinte código T-SQL para criar seu banco de dados de assinantes vazio:

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_SUB]
GO

USE [ReplTran_SUB]
GO
CREATE TABLE ReplTest (
    ID INT NOT NULL PRIMARY KEY,
    c1 VARCHAR(100) NOT NULL,
    dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO
```

## <a name="6---configure-distribution"></a>6 - Configurar distribuição

Conecte-se à instância `sql-mi-pub` gerenciada usando o SQL Server Management Studio e execute o seguinte código T-SQL para configurar seu banco de dados de distribuição. 

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7 - Configurar editor para usar distribuidor 

Na instância `sql-mi-pub`gerenciada pelo editor, altere a execução da consulta para o modo [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) e execute o seguinte código para registrar o novo distribuidor com seu editor. 

```sql
:setvar username loginUsedToAccessSourceManagedInstance
:setvar password passwordUsedToAccessSourceManagedInstance
:setvar file_storage "\\storage-account-name.file.core.windows.net\file-share-name"
-- example: file_storage "\\replstorage.file.core.windows.net\replshare"
:setvar file_storage_key "DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net"
-- example: file_storage_key "DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net"

USE [master]
EXEC sp_adddistpublisher
  @publisher = @@ServerName,
  @distribution_db = N'distribution',
  @security_mode = 0,
  @login = N'$(username)',
  @password = N'$(password)',
  @working_directory = N'$(file_storage)',
  @storage_connection_string = N'$(file_storage_key)'; -- Remove this parameter for on-premises publishers
```

   > [!NOTE]
   > Certifique-se de usar apenas`\`barras traseiras () para o parâmetro file_storage. O uso de uma barra (`/`) pode causar um erro ao se conectar ao compartilhamento de arquivo. 

Esse script configura um editor local na instância gerenciada, adiciona um servidor vinculado e cria um conjunto de trabalhos para o SQL Server Agent. 

## <a name="8---create-publication-and-subscriber"></a>8 - Criar publicação e assinante

Usando o modo [SQLCMD,](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) execute o seguinte script T-SQL para habilitar a replicação do seu banco de dados e configure a replicação entre seu editor, distribuidor e assinante. 

```sql
-- Set variables
:setvar username sourceLogin
:setvar password sourcePassword
:setvar source_db ReplTran_PUB
:setvar publication_name PublishData
:setvar object ReplTest
:setvar schema dbo
:setvar target_server "sql-mi-sub.wdec33262scj9dr27.database.windows.net"
:setvar target_username targetLogin
:setvar target_password targetPassword
:setvar target_db ReplTran_SUB

-- Enable replication for your source database
USE [$(source_db)]
EXEC sp_replicationdboption
  @dbname = N'$(source_db)',
  @optname = N'publish',
  @value = N'true';

-- Create your publication
EXEC sp_addpublication
  @publication = N'$(publication_name)',
  @status = N'active';


-- Configure your log reaer agent
EXEC sp_changelogreader_agent
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the publication snapshot
EXEC sp_addpublication_snapshot
  @publication = N'$(publication_name)',
  @frequency_type = 1,
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the ReplTest table to the publication
EXEC sp_addarticle 
  @publication = N'$(publication_name)',
  @type = N'logbased',
  @article = N'$(object)',
  @source_object = N'$(object)',
  @source_owner = N'$(schema)';

-- Add the subscriber
EXEC sp_addsubscription
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @destination_db = N'$(target_db)',
  @subscription_type = N'Push';

-- Create the push subscription agent
EXEC sp_addpushsubscription_agent
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @subscriber_db = N'$(target_db)',
  @subscriber_security_mode = 0,
  @subscriber_login = N'$(target_username)',
  @subscriber_password = N'$(target_password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Initialize the snapshot
EXEC sp_startpublication_snapshot
  @publication = N'$(publication_name)';
```

## <a name="9---modify-agent-parameters"></a>9 - Modificar parâmetros do agente

A instância gerenciada do Banco de Dados SQL do Azure está atualmente enfrentando alguns problemas de back-end com conectividade com os agentes de replicação. Enquanto este problema está sendo abordado, a solução para aumentar o valor de tempo de login para os agentes de replicação. 

Execute o seguinte comando T-SQL no editor para aumentar o tempo limite de login: 

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Execute novamente o seguinte comando T-SQL para definir o tempo de saída de login de volta ao valor padrão, caso você precise fazê-lo:

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Reinicie os três agentes para aplicar essas alterações. 

## <a name="10---test-replication"></a>10 - Replicação de teste

Depois que a replicação for configurada, teste-a inserindo novos itens no publicador e observando as alterações se propagarem para o assinante. 

Execute o seguinte snippet T-SQL para ver as linhas no assinante:

```sql
select * from dbo.ReplTest
```

Execute o snippet T-SQL a seguir para inserir linhas adicionais no publicador e, em seguida, verifique as linhas novamente no assinante. 

```sql
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Limpar recursos

Para soltar a publicação, execute o seguinte comando T-SQL:

```sql
-- Drops the publication
USE [ReplTran_PUB]
EXEC sp_droppublication @publication = N'PublishData'
GO
```

Para remover a opção de replicação do banco de dados, execute o seguinte comando T-SQL:

```sql
-- Disables publishing of the database
USE [ReplTran_PUB]
EXEC sp_removedbreplication
GO
```

Para desativar a publicação e a distribuição, execute o seguinte comando T-SQL:

```sql
-- Drops the distributor
USE [master]
EXEC sp_dropdistributor @no_checks = 1
GO
```

Você pode limpar seus recursos do Azure [excluindo os recursos de instância gerenciados do grupo de recursos](../azure-resource-manager/management/manage-resources-portal.md#delete-resources) e, em seguida, excluindo o grupo `SQLMI-Repl`de recursos . 

   
## <a name="see-also"></a>Consulte também

- [Replicação transacional](sql-database-managed-instance-transactional-replication.md)
- [Tutorial: Configure a replicação transacional entre um editor de MI e um assinante do SQL Server](sql-database-managed-instance-configure-replication-tutorial.md)
- [O que é uma Instância Gerenciada?](sql-database-managed-instance.md)
