---
title: Diferenças T-SQL de instância gerenciada
description: Este artigo aborda as diferenças do T-SQL entre uma instância gerenciada no Banco de Dados SQL do Azure e no SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, bonova, danil
ms.date: 03/11/2020
ms.custom: seoapril2019
ms.openlocfilehash: e01c61ca4f415ffbb46c86034d4b7441bc2617d9
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365490"
---
# <a name="managed-instance-t-sql-differences-and-limitations"></a>Diferenças e limitações t-sql de instância gerenciada

Este artigo resume e explica as diferenças de sintaxe e comportamento entre a instância gerenciada do Banco de Dados Azure SQL e o Mecanismo de Banco de Dados SQL Server no local. A opção de implantação de instância gerenciada fornece alta compatibilidade com o Mecanismo de Banco de Dados do SQL Server local. A maioria dos recursos do mecanismo de banco de dados do SQL Server é compatível com uma instância gerenciada.

![Migração](./media/sql-database-managed-instance/migration.png)

Existem algumas limitações do PaaS que são introduzidas na Instância Gerenciada e algumas mudanças de comportamento em comparação com o SQL Server. As diferenças estão divididas nas seguintes categorias:<a name="Differences"></a>

- [A disponibilidade](#availability) inclui as diferenças em [Always On Availability Groups](#always-on-availability-groups) e [backups](#backup).
- [A segurança](#security) inclui as diferenças na [auditoria,](#auditing) [certificados,](#certificates) [credenciais,](#credential) [provedores criptográficos,](#cryptographic-providers) [logins e usuários,](#logins-and-users)e a [chave de serviço e chave mestra de serviço.](#service-key-and-service-master-key)
- [A configuração](#configuration) inclui as diferenças na extensão do [pool de buffer,](#buffer-pool-extension) [colagem,](#collation) [níveis de compatibilidade,](#compatibility-levels) [espelhamento de banco de dados,](#database-mirroring) [opções de banco de dados,](#database-options) [Agente de Servidor SQL](#sql-server-agent)e [opções de tabela.](#tables)
- [As funcionalidades](#functionalities) incluem [INSERT/OPENROWSET](#bulk-insert--openrowset)EM MASSA, [CLR,](#clr) [DBCC,](#dbcc) [transações distribuídas,](#distributed-transactions) [eventos estendidos,](#extended-events) [bibliotecas externas,](#external-libraries) [filestream e FileTable,](#filestream-and-filetable) [pesquisa semântica de texto completo,](#full-text-semantic-search) [servidores vinculados,](#linked-servers) [PolyBase,](#polybase) [Replicação,](#replication) [RESTORE,](#restore-statement) [Service Broker,](#service-broker) [procedimentos armazenados, funções e gatilhos.](#stored-procedures-functions-and-triggers)
- [Configurações do](#Environment) ambiente, como VNets e configurações de sub-rede.

A maioria desses recursos são restrições arquitetônicas e representam recursos de serviço.

Problemas temporários conhecidos que são descobertos em instância gerenciada e que serão resolvidos no futuro são descritos na [página notas de versão](sql-database-release-notes.md).

## <a name="availability"></a>Disponibilidade

### <a name="always-on-availability-groups"></a><a name="always-on-availability-groups"></a>Grupos de Disponibilidade AlwaysOn

[A alta disponibilidade](sql-database-high-availability.md) é incorporada à instância gerenciada e não pode ser controlada pelos usuários. As seguintes declarações não são suportadas:

- [CRIAR ENDPOINT … PARA DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql)
- [CRIAR GRUPO DE DISPONIBILIDADE](/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTERAR GRUPO DE DISPONIBILIDADE](/sql/t-sql/statements/alter-availability-group-transact-sql)
- [DESCARTAR GRUPO DE DISPONIBILIDADE](/sql/t-sql/statements/drop-availability-group-transact-sql)
- A cláusula [SET HADR](/sql/t-sql/statements/alter-database-transact-sql-set-hadr) da declaração [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql)

### <a name="backup"></a>Backup

As instâncias gerenciadas têm backups automáticos, para que os usuários possam criar backups completos do banco de dados. `COPY_ONLY` Os backups diferenciais, de log e de snapshot de arquivos não são suportados.

- Com uma instância gerenciada, você pode fazer backup de um banco de dados de instâncias apenas para uma conta de armazenamento Azure Blob:
  - Apenas `BACKUP TO URL` tem suporte.
  - `FILE`, `TAPE`e os dispositivos de backup não são suportados.
- A maioria `WITH` das opções gerais são suportadas.
  - `COPY_ONLY`é obrigatório.
  - Não há suporte para `FILE_SNAPSHOT`.
  - Opções `REWIND`de `NOREWIND` `UNLOAD`fita: `NOUNLOAD` , , e não são suportadas.
  - Opções específicas `NORECOVERY`de `STANDBY`log: , e `NO_TRUNCATE` não são suportadas.

 Limitações: 

- Com uma instância gerenciada, você pode fazer backup de um banco de dados de instâncias para um backup com até 32 listras, o que é suficiente para bancos de dados de até 4 TB se a compactação de backup for usada.
- Você não pode `BACKUP DATABASE ... WITH COPY_ONLY` executar em um banco de dados criptografado com O TDE (Transparent Data Encryption, criptografia de dados transparentes) gerenciada por serviço. O TDE gerenciado por serviço força os backups a serem criptografados com uma chave TDE interna. A chave não pode ser exportada, então você não pode restaurar o backup. Use backups automáticos e restauração point-in-time ou use [o TDE gerenciado pelo cliente (BYOK).](transparent-data-encryption-azure-sql.md#customer-managed-transparent-data-encryption---bring-your-own-key) Você também pode desativar a criptografia no banco de dados.
- O tamanho máximo da faixa `BACKUP` de backup usando o comando em uma instância gerenciada é de 195 GB, que é o tamanho máximo da bolha. Aumente o número de faixas no comando de backup para reduzir o tamanho da faixa individual e permaneça dentro desse limite.

    > [!TIP]
    > Para contornar essa limitação, ao fazer backup de um banco de dados do SQL Server em um ambiente local ou em uma máquina virtual, você pode:
    >
    > - Fazer backup `DISK` em vez `URL`de fazer backup para .
    > - Faça upload dos arquivos de backup para o armazenamento Blob.
    > - Restaure-se na instância gerenciada.
    >
    > O `Restore` comando em uma instância gerenciada suporta tamanhos maiores de bolhas nos arquivos de backup porque um tipo de bolha diferente é usado para o armazenamento dos arquivos de backup enviados.

Para obter informações sobre backups usando o T-SQL, consulte [BACKUP](/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Segurança

### <a name="auditing"></a>Auditoria

As principais diferenças entre a auditoria em bancos de dados no Banco de Dados SQL do Azure e em bancos de dados no SQL Server são:

- Com a opção de implantação de instância gerenciada no Banco de Dados SQL do Azure, a auditoria funciona no nível do servidor. Os `.xel` arquivos de log são armazenados no armazenamento Azure Blob.
- Com as opções de implantação de banco de dados individual e pool elástico no Banco de Dados SQL do Azure, a auditoria funciona no nível do banco de dados.
- No SQL Server on-premises ou máquinas virtuais, a auditoria funciona no nível do servidor. Os eventos são armazenados no sistema de arquivos ou nos registros de eventos do Windows.
 
A auditoria XEvent na Instância Gerenciada oferece suporte a destinos de armazenamento de blobs do Azure. Os registros de arquivos e windows não são suportados.

As principais diferenças na sintaxe `CREATE AUDIT` para a auditoria do armazenamento de Blobs do Azure são:

- Uma nova sintaxe `TO URL` é fornecida que você pode usar para especificar a `.xel` URL do contêiner de armazenamento Azure Blob onde os arquivos são colocados.
- A sintaxe `TO FILE` não é suportada porque uma instância gerenciada não pode acessar os compartilhamentos de arquivos do Windows.

Para obter mais informações, consulte: 

- [CREATE SERVER AUDIT](/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Auditoria](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Certificados

Uma instância gerenciada não pode acessar compartilhamentos de arquivos e pastas do Windows, de modo que as seguintes restrições se aplicam:

- `CREATE FROM` / O `BACKUP TO` arquivo não é suportado para certificados.
- `CREATE` / O `BACKUP` `FILE` certificado / `ASSEMBLY` não está suportado. Arquivos de chave privada não podem ser usados. 

Consulte [CRIAR CERTIFICADO](/sql/t-sql/statements/create-certificate-transact-sql) e [CERTIFICADO DE BACKUP](/sql/t-sql/statements/backup-certificate-transact-sql). 
 
**Solução de solução**: Em vez de criar backup de certificado e restaurar o backup, [obtenha o conteúdo binário do certificado e a chave privada, armazene-o como arquivo .sql e crie a partir de binário:](/sql/t-sql/functions/certencoded-transact-sql#b-copying-a-certificate-to-another-database)

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Credencial

Somente o Azure Key Vault e identidades `SHARED ACCESS SIGNATURE` têm suporte. Não há suporte para usuários do Windows.

Consulte [CRIAR CREDENCIAL](/sql/t-sql/statements/create-credential-transact-sql) e [ALTERAR CREDENCIAL](/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Provedores criptográficos

Uma instância gerenciada não pode acessar arquivos, então os provedores criptográficos não podem ser criados:

- Não há suporte para `CREATE CRYPTOGRAPHIC PROVIDER`. Consulte [CRIAR PROVEDOR CRIPTOGRÁFICO](/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- Não há suporte para `ALTER CRYPTOGRAPHIC PROVIDER`. Consulte [ALTERAR PROVEDOR CRIPTOGRÁFICO](/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins-and-users"></a>Logons e usuários

- Os logins SQL `FROM CERTIFICATE`criados usando , `FROM ASYMMETRIC KEY`e `FROM SID` são suportados. Consulte [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql).
- Os princípios de servidor do Azure Active Directory (Azure AD) criados com a sintaxe [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) ou a sintaxe DE CRIAR USUÁRIO DE [LOGIN [Azure AD Login]](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) são suportados. Esses logins são criados no nível do servidor.

    A instância gerenciada suporta os princípios do banco de `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER`dados Azure AD com a sintaxe . Esse recurso também é conhecido como Azure AD continha usuários de banco de dados.

- Os logins do `CREATE LOGIN ... FROM WINDOWS` Windows criados com a sintaxe não são suportados. Use logons e usuário do Microsoft Azure Active Directory.
- O usuário Azure AD que criou a instância tem [privilégios de administrador irrestritos](sql-database-manage-logins.md).
- Os usuários de nível de banco de dados Ad `CREATE USER ... FROM EXTERNAL PROVIDER` não-administradores podem ser criados usando a sintaxe. Veja [CRIAR USUÁRIO ... DO PROVEDOR EXTERNO](sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities).
- Os recursos de servidor AD do Azure AD suportam recursos SQL apenas em uma instância gerenciada. Recursos que requerem interação entre instâncias, não importa se estão dentro do mesmo inquilino Azure AD ou inquilinos diferentes, não são suportados para usuários do Azure AD. Exemplos de recursos desse tipo são:

  - Replicação transacional SQL.
  - Servidor de link.

- Não há suporte para logons do Azure AD mapeados para um grupo do Azure AD como proprietário do banco de dados.
- A representação dos princípios de nível de servidor Azure AD usando outros diretores Azure AD é suportada, como a cláusula [EXECUTE AS.](/sql/t-sql/statements/execute-as-transact-sql) Execute AS limitações são:

  - EXECUTE AS USER não é suportado para usuários do Azure AD quando o nome difere do nome de login. Um exemplo é quando o usuário é criado através da sintaxe CREATEjohn@contoso.comUSER [myAadUser] FROM LOGIN [ ] e a personificação é tentada através de EXEC AS USER = _myAadUser_. Ao criar um **USUÁRIO a** partir de um principal (login) do servidor Azure AD, especifique o user_name como o mesmo login_name do **LOGIN**.
  - Apenas os princípios (logins) de nível sql `sysadmin` server que fazem parte da função podem executar as seguintes operações que têm como alvo os principais Ad do Azure:

    - EXECUTE AS USER
    - EXECUTE AS LOGIN

- Exportação/importação de banco de dados usando arquivos bacpac são suportados para usuários azure AD em instância gerenciada usando [SSMS V18.4 ou posterior](/sql/ssms/download-sql-server-management-studio-ssms), ou [SQLPackage.exe](/sql/tools/sqlpackage-download).
  - As seguintes configurações são suportadas usando o arquivo bacpac do banco de dados: 
    - Exportar/importar um banco de dados entre diferentes instâncias de gerenciamento dentro do mesmo domínio Azure AD.
    - Exportar um banco de dados a partir de instância gerenciada e importar para o Banco de Dados SQL dentro do mesmo domínio Azure AD. 
    - Exportar um banco de dados do Banco de Dados SQL e importar para instância gerenciada dentro do mesmo domínio Azure AD.
    - Exportar um banco de dados a partir de instância gerenciada e importar para o SQL Server (versão 2012 ou posterior).
      - Nesta configuração, todos os usuários do Azure AD são criados como principais de banco de dados SQL (usuários) sem logins. O tipo de usuários é listado como SQL (visível como SQL_USER em sys.database_principals). Suas permissões e funções permanecem nos metadados do banco de dados SQL Server e podem ser usadas para personificação. No entanto, eles não podem ser usados para acessar e fazer login no SQL Server usando suas credenciais.

- Somente o login principal em nível de servidor, criado pelo processo de provisionamento de `securityadmin` `sysadmin`instâncias gerenciadas, membros das funções do servidor, como ou , ou outros logins com permissão ALTER QUALQUER LOGIN no nível do servidor podem criar os principais (logins) do servidor Azure AD no banco de dados mestre para instância gerenciada.
- Se o login for um sql principal, apenas `sysadmin` logins que fazem parte da função podem usar o comando criar logins para uma conta AD do Azure.
- O login do Azure AD deve ser um membro de um Ad do Azure dentro do mesmo diretório que é usado para a instância gerenciada do Azure SQL Database.
- Os princípios do servidor Azure AD (logins) são visíveis no Object Explorer, começando com a pré-visualização 5 do SQL Server Management Studio 18.0.
- É permitida a sobreposição de entidades de segurança do servidor (logons) do Azure AD com uma conta de administrador do Azure AD. Os princípios do servidor Azure AD (logins) têm prioridade sobre o administrador do Azure AD quando você resolve o principal e aplica permissões à instância gerenciada.
- Durante a autenticação, a seqüência a seguir é aplicada para resolver o principal autenticador:

    1. Se a conta Azure AD existir como diretamente mapeada para o principal do servidor Azure AD (login), que está presente no sys.server_principals como tipo "E", conceda acesso e solicite permissões do principal servidor Azure AD (login).
    2. Se a conta Azure AD for um membro de um grupo Azure AD que está mapeado para o principal (login) do servidor Azure AD, que está presente no sys.server_principals como tipo "X", conceda acesso e solicite permissões do login do grupo Azure AD.
    3. Se a conta Azure AD for um administrador Adad configurado por portal especial para instância gerenciada, que não existe em exibições de sistema de instância gerenciada, aplique permissões fixas especiais do administrador do Azure AD para instância gerenciada (modo legado).
    4. Se a conta Azure AD existir como diretamente mapeada para um usuário Ad do Azure em um banco de dados, que está presente em sys.database_principals como tipo "E", conceda acesso e solicite permissões do usuário do banco de dados Azure AD.
    5. Se a conta Azure AD for um membro de um grupo Azure AD que foi mapeado para um usuário AD do Azure em um banco de dados, que está presente em sys.database_principals como tipo "X", conceda acesso e solicite permissões do login do grupo Azure AD.
    6. Se houver um login Azure AD mapeado para uma conta de usuário Azure AD ou uma conta do grupo Azure AD, que resolve para o usuário que está autenticando, todas as permissões deste login Do Azure AD serão aplicadas.

### <a name="service-key-and-service-master-key"></a>Chave de serviço e chave mestra de serviço

- [O backup da chave](/sql/t-sql/statements/backup-master-key-transact-sql) mestre não é suportado (gerenciado pelo serviço SQL Database).
- [A restauração da tecla](/sql/t-sql/statements/restore-master-key-transact-sql) mestre não é suportada (gerenciada pelo serviço SQL Database).
- [O backup da chave mestre](/sql/t-sql/statements/backup-service-master-key-transact-sql) do serviço não é suportado (gerenciado pelo serviço SQL Database).
- [A restauração da tecla mestre de](/sql/t-sql/statements/restore-service-master-key-transact-sql) serviço não é suportada (gerenciada pelo serviço SQL Database).

## <a name="configuration"></a>Configuração

### <a name="buffer-pool-extension"></a>Extensão do pool de buffers

- [A extensão](/sql/database-engine/configure-windows/buffer-pool-extension) do pool de buffer não é suportada.
- Não há suporte para `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION`. Consulte [ALTERAR CONFIGURAÇÃO DO SERVIDOR](/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Collation

A ordenação de instância padrão é `SQL_Latin1_General_CP1_CI_AS` e pode ser especificada como um parâmetro de criação. Consulte [Ordenações](/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Níveis de compatibilidade

- Os níveis de compatibilidade suportados são 100, 110, 120, 130, 140 e 150.
- Não há suporte para níveis de compatibilidade abaixo de 100.
- O nível de compatibilidade padrão para novos bancos de dados é 140. Para bancos de dados restaurados, o nível de compatibilidade permanece inalterado se for 100 ou superior.

Consulte [ALTERAR Nível de Compatibilidade do BANCO DE DADOS](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).

### <a name="database-mirroring"></a>Espelhamento de banco de dados

Não há suporte para espelhamento de banco de dados.

- As opções `ALTER DATABASE SET PARTNER` e `SET WITNESS` não são suportadas.
- Não há suporte para `CREATE ENDPOINT … FOR DATABASE_MIRRORING`.

Para obter mais informações, consulte [ALTER DATABASE SET PARTNER e SET WITNESS](/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) e [CREATE ENDPOINT... FOR DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Opções de banco de dados

- Não há suporte para vários arquivos de log.
- Não há suporte para objetos na memória na camada de serviço de Uso Geral. 
- Há um limite de 280 arquivos por instância de Propósito Geral, o que implica um máximo de 280 arquivos por banco de dados. Tanto os dados quanto os arquivos de registro no nível Propósito Geral são contados para esse limite. [O nível Business Critical suporta 32.767 arquivos por banco de dados](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).
- O banco de dados não pode conter grupos de arquivos que contenham dados de fluxo de arquivos. A restauração falha `FILESTREAM` se .bak contiver dados. 
- Cada arquivo é colocado no Armazenamento de Blobs do Azure. A E/S e a taxa de transferência por arquivo dependem do tamanho de cada arquivo individual.

#### <a name="create-database-statement"></a>Instrução CREATE DATABASE

As seguintes limitações se aplicam a: `CREATE DATABASE`

- Arquivos e grupos de arquivos não podem ser definidos. 
- A `CONTAINMENT` opção não é suportada. 
- `WITH`opções não são suportadas. 
   > [!TIP]
   > Como solução de `ALTER DATABASE` solução, use depois `CREATE DATABASE` para definir opções de banco de dados para adicionar arquivos ou definir a contenção. 

- A `FOR ATTACH` opção não é suportada.
- A `AS SNAPSHOT OF` opção não é suportada.

Para saber mais, confira [CRIAR BANCO DE DADOS](/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>instrução ALTER DATABASE

Algumas propriedades de arquivo não podem ser definidas ou alteradas:

- Um caminho de arquivo não pode `ALTER DATABASE ADD FILE (FILENAME='path')` ser especificado na declaração T-SQL. Remova `FILENAME` do script porque uma instância gerenciada coloca os arquivos automaticamente. 
- Um nome de arquivo não pode `ALTER DATABASE` ser alterado usando a declaração.

As opções a seguir são definidas por padrão e não podem ser alteradas:

- `MULTI_USER`
- `ENABLE_BROKER ON`
- `AUTO_CLOSE OFF`

As seguintes opções não podem ser modificadas:

- `AUTO_CLOSE`
- `AUTOMATIC_TUNING(CREATE_INDEX=ON|OFF)`
- `AUTOMATIC_TUNING(DROP_INDEX=ON|OFF)`
- `DISABLE_BROKER`
- `EMERGENCY`
- `ENABLE_BROKER`
- `FILESTREAM`
- `HADR`
- `NEW_BROKER`
- `OFFLINE`
- `PAGE_VERIFY`
- `PARTNER`
- `READ_ONLY`
- `RECOVERY BULK_LOGGED`
- `RECOVERY_SIMPLE`
- `REMOTE_DATA_ARCHIVE` 
- `RESTRICTED_USER`
- `SINGLE_USER`
- `WITNESS`

Para saber mais, confira [ALTERAR BANCO DE DADOS](/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options).

### <a name="sql-server-agent"></a>SQL Server Agent

- Ativar e desativar o SQL Server Agent não é suportado na instância gerenciada. O SQL Agent sempre está em execução.
- As configurações do SQL Server Agent são somente lidas. O `sp_set_agent_properties` procedimento não é suportado em instância gerenciada. 
- Trabalhos
  - As etapas de trabalho T-SQL têm suporte.
  - Os trabalhos de replicação a seguir têm suporte:
    - Leitor do log de transações
    - Instantâneo
    - Distribuidor
  - As etapas de trabalho do SSIS são suportadas.
  - Outros tipos de etapas de trabalho não são suportadas atualmente:
    - O passo do trabalho de replicação de mesclagem não é suportado. 
    - Não há suporte para leitor de fila. 
    - O comando ainda não foi suportado.
  - As instâncias gerenciadas não podem acessar recursos externos, por exemplo, compartilhamentos de rede via robocopy. 
  - Os serviços de análise do servidor SQL não são suportados.
- Há suporte parcial para notificações.
- A notificação por e-mail é suportada, embora exija que você configure um perfil de Correio de Banco de Dados. O SQL Server Agent pode usar apenas um `AzureManagedInstance_dbmail_profile`perfil de Database Mail, e ele deve ser chamado . 
  - Não há suporte para o Pager.
  - Não há suporte para o NetSend.
  - Os alertas ainda não foram suportados.
  - Proxies não são suportados.
- EventLog não é suportado.

Os seguintes recursos do SQL Agent atualmente não são suportados:

- Proxies
- Agendamento de trabalhos em uma CPU ociosa
- Ativando ou desativando um agente
- Alertas

Para obter informações sobre o SQL Server Agent, consulte [SQL Server Agent](/sql/ssms/agent/sql-server-agent).

### <a name="tables"></a>Tabelas

Os seguintes tipos de tabela não são suportados:

- [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server)
- [TABELA DE ARQUIVOS](/sql/relational-databases/blob/filetables-sql-server)
- [TABELA EXTERNA](/sql/t-sql/statements/create-external-table-transact-sql) (Polibase)
- [MEMORY_OPTIMIZED](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables) (não suportado apenas no nível de Propósito Geral)

Para obter informações sobre como criar e alterar tabelas, consulte [CRIAR TABELA](/sql/t-sql/statements/create-table-transact-sql) e [TABELA ALTER](/sql/t-sql/statements/alter-table-transact-sql).

## <a name="functionalities"></a>Funcionalidades

### <a name="bulk-insert--openrowset"></a>Inserção a granel / OPENROWSET

Uma instância gerenciada não pode acessar compartilhamentos de arquivos e pastas do Windows, então os arquivos devem ser importados do armazenamento Do Azure Blob:

- `DATASOURCE`é necessário `BULK INSERT` no comando enquanto você importa arquivos do armazenamento Azure Blob. Consulte [INSERÇÃO EM MASSA](/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE`é necessário `OPENROWSET` na função quando você lê o conteúdo de um arquivo do armazenamento Azure Blob. Consulte [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql).
- `OPENROWSET`pode ser usado para ler dados de outros bancos de dados únicos do Azure SQL, instâncias gerenciadas ou instâncias do SQL Server. Outras fontes, como bancos de dados Oracle ou arquivos Excel, não são suportadas.

### <a name="clr"></a>CLR

Uma instância gerenciada não pode acessar compartilhamentos de arquivos e pastas do Windows, de modo que as seguintes restrições se aplicam:

- Apenas `CREATE ASSEMBLY FROM BINARY` tem suporte. Consulte [CREATE ASSEM BLY FROM BINARY](/sql/t-sql/statements/create-assembly-transact-sql). 
- Não há suporte para `CREATE ASSEMBLY FROM FILE`. Consulte [CRIAR ASSEMBLY A PARTIR DE ARQUIVO](/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` não pode referenciar arquivos. Consulte [ALTERAR ASSEMBLY](/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="database-mail-db_mail"></a>Correio de Banco de Dados (db_mail)
 - `sp_send_dbmail`não pode enviar @file_attachments anexos usando parâmetro. O sistema de arquivos local e as ações externas ou o Azure Blob Storage não estão acessíveis a partir deste procedimento.
 - Veja os problemas `@query` conhecidos relacionados ao parâmetro e à autenticação.
 
### <a name="dbcc"></a>DBCC

As instruções DBCC não documentadas habilitadas no SQL Server não são suportadas em instâncias gerenciadas.

- Apenas um número limitado de bandeiras Global Trace são suportadas. O nível `Trace flags` de sessão não é suportado. Veja [bandeiras trace](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- [DBCC TRACEOFF](/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql) e [DBCC TRACEON](/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) trabalham com o número limitado de sinalizadores globais.
- [DBCC CHECKDB](/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql) com opções REPAIR_ALLOW_DATA_LOSS, REPAIR_FAST e REPAIR_REBUILD não `SINGLE_USER` pode ser usado porque o banco de dados não pode ser definido no modo - veja [diferenças DE BANCO DE DADOS ALTER](#alter-database-statement). Possíveis corrupção no banco de dados são tratadas pela equipe de suporte do Azure. Entre em contato com o suporte do Azure se você estiver notando a corrupção no banco de dados que deve ser corrigida.

### <a name="distributed-transactions"></a>Transações distribuídas

Transações MSDTC e [elásticas](sql-database-elastic-transactions-overview.md) atualmente não são suportadas em instâncias gerenciadas.

### <a name="extended-events"></a>Eventos estendidos

Alguns alvos específicos do Windows para Eventos Estendidos (XEvents) não são suportados:

- O `etw_classic_sync` alvo não é suportado. Armazene `.xel` arquivos no armazenamento Azure Blob. Consulte [etw_classic_sync target](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- O `event_file` alvo não é suportado. Armazene `.xel` arquivos no armazenamento Azure Blob. Consulte [event_file target](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Bibliotecas externas

No banco de dados R e Python, bibliotecas externas ainda não são suportadas. Consulte [Serviços de Machine Learning do SQL Server](/sql/advanced-analytics/r/sql-server-r-services).

### <a name="filestream-and-filetable"></a>Fluxo de arquivos e FileTable

- Os dados do filestream não são suportados.
- O banco de dados não `FILESTREAM` pode conter grupos de arquivos com dados.
- Não há suporte para `FILETABLE`.
- As tabelas não podem ter tipos `FILESTREAM`.
- Não há suporte para as seguintes funções:
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

Para obter mais informações, consulte [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server) e [FileTables](/sql/relational-databases/blob/filetables-sql-server).

### <a name="full-text-semantic-search"></a>Pesquisa semântica de texto completo

Não há suporte para [Pesquisa semântica](/sql/relational-databases/search/semantic-search-sql-server).

### <a name="linked-servers"></a>Servidores vinculados

Os servidores vinculados em instâncias gerenciadas dão suporte a um número limitado de destinos:

- Os alvos suportados são instâncias gerenciadas, bancos de dados únicos e instâncias do SQL Server. 
- Os servidores vinculados não suportam transações de escrita distribuída (MS DTC).
- Os alvos que não são suportados são arquivos, serviços de análise e outros RDBMS. Tente usar a importação nativa de CSV `BULK INSERT` do `OPENROWSET` Azure Blob Storage usando ou como uma alternativa para importação de arquivos.

Operações

- Não há suporte para transações de gravação entre instâncias.
- `sp_dropserver` é compatível com o descarte um servidor vinculado. Consulte [sp_dropserver](/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- A `OPENROWSET` função pode ser usada para executar consultas apenas em instâncias do SQL Server. Eles podem ser gerenciados, no local ou em máquinas virtuais. Consulte [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql).
- A `OPENDATASOURCE` função pode ser usada para executar consultas apenas em instâncias do SQL Server. Eles podem ser gerenciados, no local ou em máquinas virtuais. Apenas `SQLNCLI`os `SQLNCLI11`, `SQLOLEDB` e valores são suportados como um provedor. Um exemplo é `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Consulte [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql).
- Os servidores vinculados não podem ser usados para ler arquivos (Excel, CSV) a partir dos compartilhamentos de rede. Tente usar [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) ou [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) que lê arquivos CSV do Azure Blob Storage. Acompanhe essas solicitações no [item de feedback de instância gerenciada](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)|

### <a name="polybase"></a>PolyBase

As tabelas externas que fazem referência aos arquivos no armazenamento HDFS ou Azure Blob não são suportadas. Para obter informações sobre o PolyBase, consulte [PolyBase](/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Replicação

- Os tipos de replicação instantânea e bidirecional são suportados. A replicação de mesclar, a replicação peer-to-peer e as assinaturas updatable não são suportadas.
- [A Replicação Transacional](sql-database-managed-instance-transactional-replication.md) está disponível para visualização pública na instância gerenciada com algumas restrições:
    - Todos os tipos de participantes de replicação (Publisher, Distributor, Pull Subscriber e Push Subscriber) podem ser colocados em instâncias gerenciadas, mas o editor e o distribuidor devem estar tanto na nuvem quanto no local.
    - As instâncias gerenciadas podem se comunicar com as versões recentes do SQL Server. Consulte a [matriz de versões suportadas](sql-database-managed-instance-transactional-replication.md#supportability-matrix-for-instance-databases-and-on-premises-systems) para obter mais informações.
    - A replicação transacional tem alguns [requisitos adicionais de rede.](sql-database-managed-instance-transactional-replication.md#requirements)

Para obter mais informações sobre como configurar a replicação transacional, consulte os seguintes tutoriais:
- [Replicação entre um editor de MI e um assinante](replication-with-sql-database-managed-instance.md)
- [Replicação entre um editor de MI, um distribuidor de MI e um assinante do SQL Server](sql-database-managed-instance-configure-replication-tutorial.md)

### <a name="restore-statement"></a>Instrução RESTAURAR 

- Sintaxe suportada:
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- Sintaxe sem suporte:
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- Origem: 
  - `FROM URL`(Armazenamento Azure Blob) é a única opção suportada.
  - Não há suporte para `FROM DISK`/`TAPE`/dispositivo de backup.
  - Conjuntos de backup não são compatíveis.
- `WITH`opções não são suportadas, `DIFFERENTIAL` `STATS`como não ou .
- `ASYNC RESTORE`: A restauração continua mesmo se a conexão do cliente quebrar. Se sua conexão for retirada, `sys.dm_operation_status` você poderá verificar a exibição do status de uma operação de restauração e para um banco de dados CREATE e DROP. Consulte [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database). 

As seguintes opções de banco de dados são definidas ou substituídas e não podem ser alteradas posteriormente: 

- `NEW_BROKER`se o corretor não estiver habilitado no arquivo .bak. 
- `ENABLE_BROKER`se o corretor não estiver habilitado no arquivo .bak. 
- `AUTO_CLOSE=OFF`se um banco de dados `AUTO_CLOSE=ON`no arquivo .bak tiver . 
- `RECOVERY FULL`se um banco de dados `SIMPLE` `BULK_LOGGED` no arquivo .bak tiver ou o modo de recuperação.
- Um grupo de arquivos otimizado para memória é adicionado e chamado xTP se não estivesse no arquivo .bak de origem. 
- Qualquer grupo de arquivos otimizado para memória existente é renomeado para XTP. 
- `SINGLE_USER`e `RESTRICTED_USER` as opções `MULTI_USER`são convertidas para .

 Limitações: 

- Os backups dos bancos de dados corrompidos podem ser restaurados dependendo do tipo de corrupção, mas backups automatizados não serão feitos até que a corrupção seja corrigida. Certifique-se de `DBCC CHECKDB` que você é `WITH CHECKSUM` executado na instância de origem e use backup para evitar esse problema.
- A `.BAK` restauração do arquivo de um banco de dados `FILESTREAM` `FILETABLE` que contenha qualquer limitação descrita neste documento (por exemplo, ou objetos) não pode ser restaurada em Instância Gerenciada.
- `.BAK`arquivos que contêm vários conjuntos de backup não podem ser restaurados. 
- `.BAK`arquivos que contêm vários arquivos de log não podem ser restaurados.
- Backups que contenham bancos de dados maiores que 8 TB, objetos OLTP ativos na memória ou número de arquivos que excederiam 280 arquivos por instância não podem ser restaurados em uma instância de Propósito Geral. 
- Os backups que contêm bancos de dados maiores que objetos OLTP de 4 TB ou na memória com o tamanho total maior do que o tamanho descrito nos [limites de recursos](sql-database-managed-instance-resource-limits.md) não podem ser restaurados na instância Business Critical.
Para obter informações sobre instruções de restauração, consulte [instruções DE RESTAURAÇÃO](/sql/t-sql/statements/restore-statements-transact-sql).

 > [!IMPORTANT]
 > As mesmas limitações se aplicam à operação de restauração point-in-time incorporada. Como exemplo, o banco de dados General Purpose maior que 4 TB não pode ser restaurado na instância Business Critical. O banco de dados Business Critical com arquivos OLTP em memória ou mais de 280 arquivos não pode ser restaurado na instância de Propósito Geral.

### <a name="service-broker"></a>Service Broker

Não há suporte para agente de serviços entre instâncias:

- `sys.routes`: Como pré-requisito, você deve selecionar o endereço em sys.routes. O endereço deve ser LOCAL em todas as rotas. Confira [sys.routes](/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE`: Você não `CREATE ROUTE` pode `ADDRESS` usar `LOCAL`com outro que não. Confira [CREATE ROUTE](/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE`: Você não `ALTER ROUTE` pode `ADDRESS` usar `LOCAL`com outro que não. Confira [ALTER ROUTE](/sql/t-sql/statements/alter-route-transact-sql). 

### <a name="stored-procedures-functions-and-triggers"></a>Procedimentos armazenados, funções e gatilhos

- `NATIVE_COMPILATION`não é suportado na camada De Propósito Geral.
- Não há suporte para as seguintes opções [sp_configure](/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql): 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote access`
  - `remote data archive`
  - `remote proc trans`
- Não há suporte para `sp_execute_external_scripts`. Consulte [sp_execute_external_scripts](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- Não há suporte para `xp_cmdshell`. Consulte [xp_cmdshell](/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures`não são suportados, o `sp_addextendedproc`   `sp_dropextendedproc`que inclui e . Consulte [procedimentos armazenados estendidos](/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql).
- Não há suporte para `sp_attach_db`, `sp_attach_single_file_db` e `sp_detach_db`. Consulte [sp_attach_db](/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql) e [sp_detach_db](/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

### <a name="system-functions-and-variables"></a>Funções e variáveis do sistema

As seguintes variáveis, funções e exibições retornam resultados diferentes:

- `SERVERPROPERTY('EngineEdition')`retorna o valor 8. Essa propriedade identifica exclusivamente uma instância gerenciada. Consulte [SERVERPROPERTY](/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')`retorna NULL porque o conceito de instância como existe para o SQL Server não se aplica a uma instância gerenciada. Consulte [SERVERPROPERTY('InstanceName')](/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME`retorna um nome completo de DNS "conectável", por exemplo, my-managed-instance.wcus17662feb9ce98.database.windows.net. Veja [@SERVERNAME@](/sql/t-sql/functions/servername-transact-sql). 
- `SYS.SERVERS`retorna um nome completo de DNS `myinstance.domain.database.windows.net` "conectável", como para as propriedades "nome" e "data_source". Consulte [SYS.SERVERS](/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME`retorna NULL porque o conceito de serviço como ele existe para o SQL Server não se aplica a uma instância gerenciada. Veja [@SERVICENAME@](/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID` é compatível. Ele retorna NULL se o login do Azure AD não estiver em sys.syslogins. Consulte [SUSER_ID](/sql/t-sql/functions/suser-id-transact-sql). 
- Não há suporte para `SUSER_SID`. Os dados errados são devolvidos, o que é um problema temporário conhecido. Consulte [SUSER_SID](/sql/t-sql/functions/suser-sid-transact-sql). 

## <a name="environment-constraints"></a><a name="Environment"></a>Restrições ambientais

### <a name="subnet"></a>Sub-rede
-  Não é possível colocar outros recursos (por exemplo, máquinas virtuais) na sub-rede onde você implantou sua instância gerenciada. Implante esses recursos usando uma sub-rede diferente.
- A sub-rede deve ter número suficiente de [endereços IP](sql-database-managed-instance-connectivity-architecture.md#network-requirements)disponíveis . O mínimo é 16, enquanto a recomendação é ter pelo menos 32 endereços IP na sub-rede.
- Os pontos finais do [serviço não podem ser associados à sub-rede da instância gerenciada](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Certifique-se de que a opção pontos finais de serviço esteja desativada quando você criar a rede virtual.
- O número de vCores e tipos de instâncias que você pode implantar em uma região têm algumas [restrições e limites](sql-database-managed-instance-resource-limits.md#regional-resource-limitations).
- Existem algumas [regras de segurança que devem ser aplicadas na sub-rede.](sql-database-managed-instance-connectivity-architecture.md#network-requirements)

### <a name="vnet"></a>VNET
- O VNet pode ser implantado usando o Resource Model - Classic Model for VNet não é suportado.
- Depois que uma instância gerenciada é criada, a movimentação da instância gerenciada ou do VNet para outro grupo de recursos ou assinatura não é suportada.
- Alguns serviços, como Ambientes de Serviço de Aplicativos, Aplicativos Lógicos e instâncias gerenciadas (usados para georeplicação, replicação transacional ou através de servidores vinculados) não podem acessar instâncias gerenciadas em diferentes regiões se seus VNets estiverem conectados usando [peering global](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers). Você pode se conectar a esses recursos via ExpressRoute ou VNet-to-VNet através do VNet Gateways.

### <a name="tempdb"></a>Tempdb

O tamanho máximo `tempdb` do arquivo não pode ser superior a 24 GB por núcleo em um nível de Propósito Geral. O `tempdb` tamanho máximo em um nível Business Critical é limitado pelo tamanho de armazenamento da ocorrência. `Tempdb`o tamanho do arquivo de registro é limitado a 120 GB no nível De Propósito Geral. Algumas consultas podem retornar um erro se precisarem de `tempdb` mais de 24 GB por núcleo ou se produzirem mais de 120 GB de dados de log.

### <a name="msdb"></a>MSDB

Os seguintes esquemas de MSDB em instância gerenciada devem ser de propriedade de suas respectivas funções predefinidas:

- Papéis gerais
  - TargetServersRole
- [Funções fixas de banco de dados](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles?view=sql-server-ver15)
  - SQLAgentUserRole
  - SQLAgentReaderRole
  - SQLAgentOperatorRole
- [Funções do DatabaseMail](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail-configuration-objects?view=sql-server-ver15#DBProfile):
  - Databasemailuserrole
- [Funções de serviços de integração](https://docs.microsoft.com/sql/integration-services/security/integration-services-roles-ssis-service?view=sql-server-ver15):
  - db_ssisadmin
  - db_ssisltduser
  - db_ssisoperator
  
> [!IMPORTANT]
> A alteração dos nomes de papéis predefinidos, nomes de esquemas e proprietários de esquemas pelos clientes afetará o funcionamento normal do serviço. Quaisquer alterações feitas a estes serão revertidos para os valores predefinidos assim que detectados, ou na próxima atualização de serviço, no mais tardar, para garantir a operação normal do serviço.

### <a name="error-logs"></a>Logs de erros

Uma instância gerenciada coloca informações verbose em registros de erro. Existem muitos eventos internos do sistema que estão conectados no registro de erros. Use um procedimento personalizado para ler registros de erro que filtram algumas entradas irrelevantes. Para obter mais informações, consulte [a instância gerenciada – sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/) ou a [extensão de instância gerenciada (pré-visualização)](/sql/azure-data-studio/azure-sql-managed-instance-extension#logs) para o Azure Data Studio.

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre instâncias gerenciadas, consulte [O que é uma instância gerenciada?](sql-database-managed-instance.md)
- Para obter recursos e lista de comparação, consulte [a comparação de recursos do Azure SQL Database](sql-database-features.md).
- Para atualizações de lançamento e estado de problemas conhecidos, consulte [notas de versão do SQL Database](sql-database-release-notes.md)
- Para obter um início rápido que mostre como criar uma nova instância gerenciada, consulte [Criar uma instância gerenciada](sql-database-managed-instance-get-started.md).
