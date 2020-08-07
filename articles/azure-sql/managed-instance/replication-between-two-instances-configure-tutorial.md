---
title: Configurar a replicação entre instâncias gerenciadas
titleSuffix: Azure SQL Managed Instance
description: Este tutorial ensina a configurar a replicação transacional entre um Publicador/distribuidor do Azure SQL Instância Gerenciada e um assinante do SQL Instância Gerenciada.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: ferno
ms.reviewer: mathoma
ms.date: 04/28/2020
ms.openlocfilehash: 114d4f41ad48af3d1e585fcb01eb0794a8e349b5
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87920102"
---
# <a name="tutorial-configure-replication-between-two-managed-instances"></a>Tutorial: configurar a replicação entre duas instâncias gerenciadas

[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

A replicação transacional permite replicar dados de um banco de dado para outro hospedado em SQL Server ou [instância gerenciada do SQL do Azure](sql-managed-instance-paas-overview.md). O SQL Instância Gerenciada pode ser um Publicador, distribuidor ou Assinante na topologia de replicação. Consulte [configurações de replicação transacional](replication-transactional-overview.md#common-configurations) para configurações disponíveis. 

A replicação transacional está atualmente em visualização pública para SQL Instância Gerenciada. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> - Configure uma instância gerenciada como um Publicador e distribuidor de replicação.
> - Configurar uma instância gerenciada como um distribuidor de replicação.

![Replicar entre duas instâncias gerenciadas](./media/replication-between-two-instances-configure-tutorial/sqlmi-sqlmi-repl.png)

Este tutorial destina-se a um público-alvo experiente e pressupõe que o usuário esteja familiarizado com a implantação e a conexão com as instâncias gerenciadas e as VMs do SQL Server no Azure. 


> [!NOTE]
> - Este artigo descreve o uso de [replicação transacional](/sql/relational-databases/replication/transactional/transactional-replication) no Azure SQL instância gerenciada. Ele não está relacionado a [grupos de failover](../database/auto-failover-group-overview.md), um recurso de instância gerenciada do SQL do Azure que permite criar réplicas legíveis completas de instâncias individuais. Há considerações adicionais ao configurar [a replicação transacional com grupos de failover](replication-transactional-overview.md#with-failover-groups).



## <a name="requirements"></a>Requisitos

Configurar o SQL Instância Gerenciada para funcionar como um Publicador e/ou um distribuidor requer:

- Se a instância gerenciada pelo Publicador está na mesma rede virtual que o distribuidor e o Assinante, ou o [emparelhamento de rede virtual](../../virtual-network/tutorial-connect-virtual-networks-powershell.md) foi configurado entre as redes virtuais de todas as três entidades. 
- A conectividade usa Autenticação SQL entre os participantes da replicação.
- Um compartilhamento de conta de armazenamento do Azure para o diretório de trabalho de replicação.
- A porta 445 (TCP de saída) está aberta nas regras de segurança do NSG para as instâncias gerenciadas para acessar o compartilhamento de arquivos do Azure.  Se você encontrar o erro `failed to connect to azure storage \<storage account name> with os error 53` , será necessário adicionar uma regra de saída ao NSG da sub-rede do SQL instância gerenciada apropriada.

## <a name="1---create-a-resource-group"></a>1-criar um grupo de recursos

Use o [portal do Azure](https://portal.azure.com) para criar um grupo de recursos com o nome `SQLMI-Repl` .  

## <a name="2---create-managed-instances"></a>2-criar instâncias gerenciadas

Use o [portal do Azure](https://portal.azure.com) para criar duas [instâncias gerenciadas do SQL](instance-create-quickstart.md) na mesma rede virtual e sub-rede. Por exemplo, nomeie as duas instâncias gerenciadas:

- `sql-mi-pub`(junto com alguns caracteres para randomização)
- `sql-mi-sub`(junto com alguns caracteres para randomização)

Você também precisará [Configurar uma VM do Azure para se conectar](connect-vm-instance-configure.md) às suas instâncias gerenciadas. 

## <a name="3---create-an-azure-storage-account"></a>3-criar uma conta de armazenamento do Azure

[Crie uma conta de armazenamento do Azure](/azure/storage/common/storage-create-storage-account#create-a-storage-account) para o diretório de trabalho e, em seguida, crie um [compartilhamento de arquivo](../../storage/files/storage-how-to-create-file-share.md) na conta de armazenamento. 

Copie o caminho do compartilhamento de arquivo no formato: `\\storage-account-name.file.core.windows.net\file-share-name`

Exemplo: `\\replstorage.file.core.windows.net\replshare`

Copie as chaves de acesso de armazenamento no formato:`DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

Exemplo: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`

Para obter mais informações, confira [Gerenciar chaves de acesso da conta de armazenamento](../../storage/common/storage-account-keys-manage.md). 

## <a name="4---create-a-publisher-database"></a>4-criar um banco de dados do Publicador

Conecte-se à sua `sql-mi-pub` instância gerenciada usando SQL Server Management Studio e execute o seguinte código Transact-SQL (T-SQL) para criar o banco de dados do Publicador:

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

## <a name="5---create-a-subscriber-database"></a>5-criar um banco de dados do assinante

Conecte-se à sua `sql-mi-sub` instância gerenciada usando SQL Server Management Studio e execute o seguinte código T-SQL para criar seu banco de dados de assinante vazio:

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

## <a name="6---configure-distribution"></a>6-configurar a distribuição

Conecte-se à sua `sql-mi-pub` instância gerenciada usando SQL Server Management Studio e execute o código T-SQL a seguir para configurar o banco de dados de distribuição.

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7-configurar o Publicador para usar o distribuidor

No Instância Gerenciada do SQL do Publicador `sql-mi-pub` , altere a execução da consulta para o modo [sqlcmd](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) e execute o código a seguir para registrar o novo distribuidor com o seu editor.

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
   > Certifique-se de usar apenas barras invertidas ( `\` ) para o parâmetro file_storage. O uso de uma barra (`/`) pode causar um erro ao se conectar ao compartilhamento de arquivo.

Esse script configura um Publicador local na instância gerenciada, adiciona um servidor vinculado e cria um conjunto de trabalhos para o agente de SQL Server.

## <a name="8---create-publication-and-subscriber"></a>8-criar publicação e assinante

Usando o modo [sqlcmd](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) , execute o seguinte script T-SQL para habilitar a replicação para seu banco de dados e configure a replicação entre o Publicador, o distribuidor e o Assinante.

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


-- Configure your log reader agent
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

## <a name="9---modify-agent-parameters"></a>9-modificar parâmetros do agente

O Azure SQL Instância Gerenciada está apresentando alguns problemas de back-end no momento com a conectividade com os agentes de replicação. Enquanto esse problema está sendo resolvido, a solução alternativa é aumentar o valor de tempo limite de logon para os agentes de replicação.

Execute o seguinte comando T-SQL no Publicador para aumentar o tempo limite do logon:

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150'
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Execute o seguinte comando T-SQL novamente para definir o tempo limite de logon de volta para o valor padrão, caso você precise fazer isso:

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30'
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Reinicie todos os três agentes para aplicar essas alterações.

## <a name="10---test-replication"></a>replicação de 10 testes

Depois que a replicação for configurada, teste-a inserindo novos itens no publicador e observando as alterações se propagarem para o assinante.

Execute o seguinte snippet T-SQL para ver as linhas no assinante:

```sql
select * from dbo.ReplTest
```

Execute o snippet T-SQL a seguir para inserir linhas adicionais no publicador e, em seguida, verifique as linhas novamente no assinante.

```sql
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Limpar os recursos

Para descartar a publicação, execute o seguinte comando T-SQL:

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

Para desabilitar a publicação e a distribuição, execute o seguinte comando T-SQL:

```sql
-- Drops the distributor
USE [master]
EXEC sp_dropdistributor @no_checks = 1
GO
```

Você pode limpar os recursos do Azure [excluindo os recursos do SQL instância gerenciada do grupo de recursos](../../azure-resource-manager/management/manage-resources-portal.md#delete-resources) e, em seguida, excluindo o grupo de recursos `SQLMI-Repl` . 

## <a name="next-steps"></a>Próximas etapas

Você também pode obter mais informações sobre a [replicação transacional com o Azure sql instância gerenciada](replication-transactional-overview.md) ou aprender a configurar a replicação entre um [Publicador/distribuidor do SQL instância gerenciada e um assinante do SQL no Azure VM](replication-two-instances-and-sql-server-configure-tutorial.md). 
