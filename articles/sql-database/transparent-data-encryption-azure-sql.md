---
title: Transparent Data Encryption
description: Uma visão geral da Transparent Data Encryption para banco de dados SQL e Synapse SQL no Azure Synapse Analytics. O documento abrange os benefícios e as opções de configuração que incluem Transparent Data Encryption gerenciado pelo serviço e Bring Your Own Key.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 04/10/2020
ms.openlocfilehash: 87abdb37ff7773b0205a2ce3ee21689a10c459d7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81113545"
---
# <a name="transparent-data-encryption-for-sql-database-and-azure-synapse"></a>Transparent Data Encryption para banco de dados SQL e Azure Synapse

A [TDE (Transparent Data Encryption)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) ajuda a proteger o banco de dados SQL do Azure, o SQL instância gerenciada do Azure e o SQL Synapse no Azure Synapse Analytics contra a ameaça de atividades offline mal-intencionadas criptografando os dados em repouso. Ela realiza a criptografia e a descriptografia em tempo real do banco de dados, de backups associados e de arquivos de log de transações em repouso, sem a necessidade de alterações no aplicativo. Por padrão, a TDE está habilitada para todos os bancos de dados SQL do Azure implantados recentemente e precisa ser habilitada manualmente para bancos de dados do Azure SQL, Azure SQL Instância Gerenciada ou Azure Synapse.

A TDE realiza a criptografia e a descriptografia de E/S em tempo real dos dados no nível da página. Cada página é descriptografada quando é lida na memória e, em seguida, criptografada antes de ser gravada no disco. O TDE criptografa o armazenamento de um banco de dados inteiro usando uma chave simétrica chamada de DEK (chave de criptografia de banco de dados). Na inicialização do banco de dados, o DEK criptografado é descriptografado e, em seguida, usado para descriptografia e recriptografia dos arquivos de banco de dados no processo de Mecanismo de Banco de Dados SQL Server. DEK é protegido pelo protetor de TDE. O protetor de TDE é um certificado gerenciado por serviço (criptografia de dados transparente gerenciada por serviço) ou uma chave assimétrica armazenada em [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault) (Transparent Data Encryption gerenciada pelo cliente). 

Para o banco de dados SQL do Azure e o Azure Synapse, o protetor de TDE é definido no nível lógico do SQL Server e é herdado por todos os bancos de dados associados a esse servidor. Para o Azure SQL Instância Gerenciada (recurso BYOK na versão prévia), o protetor de TDE é definido no nível de instância e é herdado por todos os bancos de dados criptografados nessa instância. O termo *servidor* refere-se ao servidor e instância ao longo deste documento, a menos que indicado de forma diferente.

> [!IMPORTANT]
> Todos os bancos de dados SQL do Azure criados recentemente são criptografados por padrão usando a Transparent Data Encryption gerenciada por serviço. Os bancos de dados SQL existentes criados antes de 2017 de maio e de SQL criados por meio da restauração, da replicação geográfica e da cópia do banco de dados não são criptografados por padrão. Os bancos de dados Instância Gerenciada existentes criados antes de fevereiro de 2019 não são criptografados por padrão. Instância Gerenciada bancos de dados criados por meio da restauração herdam o status de criptografia da origem.

> [!NOTE]
> A TDE não pode ser utilizada para criptografar o banco de dados **mestre** lógico no Banco de Dados SQL.  O banco de dados **mestre** contém os objetos necessários para executar as operações de TDE nos bancos de dados do usuário.


## <a name="service-managed-transparent-data-encryption"></a>Transparent Data Encryption de serviço gerenciado

No Azure, a configuração padrão para TDE é que o DEK é protegido por um certificado de servidor interno. O certificado de servidor interno é exclusivo para cada servidor e o algoritmo de criptografia usado é o AES 256. Se um banco de dados estiver em um relacionamento de replicação geográfica, tanto os bancos de dados primários como os secundários geográficos serão protegidos pela chave de servidor pai do banco de dados primário. Se dois bancos de dados estiverem conectados ao mesmo servidor, eles também compartilharão o mesmo certificado interno.  A Microsoft gira automaticamente esses certificados em conformidade com a política de segurança interna e a chave raiz é protegida por um armazenamento secreto interno da Microsoft.  Os clientes podem verificar a conformidade do banco de dados SQL com políticas de segurança interna em relatórios de auditoria de terceiros independentes disponíveis na [central de confiabilidade da Microsoft](https://servicetrust.microsoft.com/).

A Microsoft também move e gerencia as chaves conforme necessário para replicação geográfica e restaurações.


## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>Transparent Data Encryption gerenciada pelo cliente – Bring Your Own Key

O TDE gerenciado pelo cliente também é conhecido como suporte de Bring Your Own Key (BYOK) para TDE. Nesse cenário, o protetor de TDE que criptografa o DEK é uma chave assimétrica gerenciada pelo cliente, que é armazenada em um Azure Key Vault gerenciado e de Propriedade do cliente (sistema de gerenciamento de chaves externas baseado em nuvem do Azure) e nunca deixa o cofre de chaves. O protetor de TDE pode ser [gerado pelo cofre de chaves ou transferido para o cofre de chaves](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys) de um dispositivo de HSM (módulo de segurança de hardware) local. O Banco de Dados SQL precisa ter permissões concedidas para o cofre de chaves de propriedade do cliente para descriptografar e criptografar a DEK. Se as permissões do SQL Server lógico para o cofre de chaves forem revogadas, um banco de dados ficará inacessível e todos eles serão criptografados

Com a integração do TDE ao Azure Key Vault, os usuários podem controlar as principais tarefas de gerenciamento, incluindo rotações de chave, permissões de cofre de chaves, backups de chaves e habilitar auditoria/relatório em todos os protetores de TDE usando a funcionalidade do Azure Key Vault. O Key Vault fornece gerenciamento de chaves central, aproveita os HSMs monitorados rigidamente e permite a separação de tarefas entre o gerenciamento de chaves e dados para ajudar a atender à conformidade com as políticas de segurança.
Para saber mais sobre o BYOK para o banco de dados SQL do Azure e o Azure Synapse, consulte [Transparent Data Encryption com integração de Azure Key Vault](transparent-data-encryption-byok-azure-sql.md).

Para começar a usar o TDE com a integração do Azure Key Vault, consulte o guia de instruções [ativar a Transparent Data Encryption usando sua própria chave de Key Vault](transparent-data-encryption-byok-azure-sql-configure.md).

## <a name="move-a-transparent-data-encryption-protected-database"></a>Mover um banco de dados protegido por Transparent Data Encryption

Não é necessário descriptografar bancos de dados para operações no Azure. As configurações de TDE no banco de dados de origem ou banco de dados primário são herdadas de forma transparente no destino. As operações incluídas envolvem:

- Restauração geográfica
- Restauração via autoatendimento point-in-time
- Restauração de um banco de dados excluído
- Replicação geográfica ativa
- Criação de uma cópia do banco de dados
- Restauração de arquivo de backup para a instância gerenciada do SQL do Azure

> [!IMPORTANT]
> Fazer backup somente cópia manual de um banco de dados criptografado por TDE gerenciada por serviço não é permitido na Instância Gerenciada do SQL do Azure, uma vez que o certificado usado para criptografia não está acessível. Use o recurso de ponto no tempo de restauração para mover esse tipo de banco de dados para outra Instância Gerenciada.

Quando você exporta um banco de dados protegido por TDE, o conteúdo exportado do banco de dados não é criptografado. Esse conteúdo exportado é armazenado em arquivos BACPAC não criptografados. Certifique-se de proteger os arquivos BACPAC adequadamente e habilite TDE após a conclusão da importação do novo banco de dados.

Por exemplo, se o arquivo BACPAC for exportado de uma instância do SQL Server local, o conteúdo importado do novo banco de dados não será automaticamente criptografado. Da mesma forma, se o arquivo BACPAC for exportado para uma instância do SQL Server local, o novo banco de dados também não será automaticamente criptografado.

A única exceção é quando você exporta para e de um banco de dados SQL. O TDE está habilitado no novo banco de dados, mas o próprio arquivo BACPAC ainda não está criptografado.


## <a name="manage-transparent-data-encryption"></a>Gerenciar a Transparent Data Encryption
# <a name="portal"></a>[Portal](#tab/azure-portal)
Gerenciar TDE no portal do Azure.

Para configurar o TDE por meio do portal do Azure, você deve estar conectado como o proprietário do Azure, o colaborador ou o Gerenciador de segurança do SQL.

Você ativa e desativa o TDE no nível do banco de dados. Para habilitar o TDE em um banco de dados, acesse o [portal do Azure](https://portal.azure.com) e entre com sua conta de administrador ou colaborador do Azure. Localize as configurações da TDE em seu banco de dados do usuário. Por padrão, é utilizada a Transparent Data Encryption de serviço gerenciado. Um certificado TDE é gerado automaticamente para o servidor que contém o banco de dados. Para o Azure SQL Instância Gerenciada use o T-SQL para ativar e desativar o TDE em um banco de dados.

![Transparent Data Encryption de serviço gerenciado](./media/transparent-data-encryption-azure-sql/service-managed-transparent-data-encryption.png)  

Você define a chave mestra TDE, conhecida como o protetor de TDE, no nível do servidor. Para usar o TDE com suporte do BYOK e proteger seus bancos de dados com uma chave de Key Vault, abra as configurações do TDE em seu servidor.

![Transparent Data Encryption com suporte Bring Your Own Key](./media/transparent-data-encryption-azure-sql/tde-byok-support.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Gerenciar TDE usando o PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, consulte [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos.

Para configurar a TDE por meio do PowerShell, você deve estar conectado como o proprietário do Azure, o Colaborador ou o Gerenciador de segurança do SQL.

### <a name="cmdlets-for-azure-sql-database-and-azure-synapse"></a>Cmdlets para o banco de dados SQL do Azure e Azure Synapse

Use os seguintes cmdlets para o banco de dados SQL do Azure e o Azure Synapse:

| Cmdlet | Descrição |
| --- | --- |
| [Set-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) |Habilita ou desabilita a Transparent Data Encryption para um banco de dados|
| [Get-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |Obtém o estado de Transparent Data Encryption para um banco de dados |
| [Get-AzSqlDatabaseTransparentDataEncryptionActivity](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) |Verifica o progresso de Transparent Data Encryption de um banco de dados |
| [Add-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/add-azsqlserverkeyvaultkey) |Adiciona uma chave do Key Vault a uma instância do SQL Server |
| [Get-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserverkeyvaultkey) |Obtém as chaves do Key Vault para um servidor do Banco de Dados SQL do Azure  |
| [Set-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) |Define o protetor de Transparent Data Encryption para uma instância do SQL Server |
| [Get-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) |Obtém o protetor de Transparent Data Encryption |
| [Remove-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) |Remove uma chave do Key Vault de uma instância do SQL Server |
|  | |

> [!IMPORTANT]
> Para o Azure SQL Instância Gerenciada, use o comando T-SQL [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) para ativar e desativar o TDE em um nível de banco de dados e verifique o [exemplo de script do PowerShell](transparent-data-encryption-byok-azure-sql-configure.md) para gerenciar o TDE em um nível de instância.

# <a name="transact-sql"></a>[Transact-SQL](#tab/azure-TransactSQL)
Gerencie o TDE usando o Transact-SQL.

Conecte o banco de dados usando um logon que seja um administrador ou membro da função **dbmanager** no banco de dados mestre.

| Comando | Descrição |
| --- | --- |
| [ALTER DATABASE (Banco de Dados SQL do Azure)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) | SET ENCRYPTION ON/OFF criptografa ou descriptografa um banco de dados |
| [sys.dm_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Retorna informações sobre o estado de criptografia de um banco de dados e as chaves de criptografia de banco de dados associadas |
| [sys.dm_pdw_nodes_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Retorna informações sobre o estado de criptografia de cada nó Synapse do Azure e suas chaves de criptografia de banco de dados associadas |
|  | |

Não é possível alternar o protetor TDE para uma chave de Key Vault usando Transact-SQL. Use PowerShell o portal do Azure.

# <a name="rest-api"></a>[REST API](#tab/azure-RESTAPI)
Gerencie o TDE usando a API REST.

Para configurar o TDE por meio da API REST, você deve estar conectado como o proprietário do Azure, o colaborador ou o Gerenciador de segurança do SQL.
Use o seguinte conjunto de comandos para o banco de dados SQL do Azure e o Azure Synapse:

| Comando | Descrição |
| --- | --- |
|[Criar ou atualizar o servidor](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Adiciona uma identidade do Azure Active Directory a uma instância do SQL Server (usada para conceder acesso ao Key Vault)|
|[Criar ou atualizar a chave do servidor](https://docs.microsoft.com/rest/api/sql/serverkeys/createorupdate)|Adiciona uma chave do Key Vault a uma instância do SQL Server|
|[Excluir chave do servidor](https://docs.microsoft.com/rest/api/sql/serverkeys/delete)|Remove uma chave do Key Vault de uma instância do SQL Server|
|[Obter chaves do servidor](https://docs.microsoft.com/rest/api/sql/serverkeys/get)|Obtém uma chave específica do Key Vault de uma instância do SQL Server|
|[Listar chaves do servidor por servidor](https://docs.microsoft.com/rest/api/sql/serverkeys/listbyserver)|Obtém as chaves do Key Vault para uma instância do SQL Server |
|[Criar ou atualizar o protetor de criptografia](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/createorupdate)|Define o protetor de TDE para uma instância de SQL Server|
|[Obter protetor de criptografia](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/get)|Obtém o protetor de TDE para uma instância de SQL Server|
|[Listar protetores de criptografia por servidor](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/listbyserver)|Obter os protetores de TDE para uma instância de SQL Server |
|[Criar ou atualizar a configuração de Transparent Data Encryption](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/createorupdate)|Habilita ou desabilita TDE para um banco de dados|
|[Obter configuração de Transparent Data Encryption](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/get)|Obter a configuração de TDE para um banco de dados|
|[Lista de resultados de configuração de Transparent Data Encryption](https://docs.microsoft.com/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|Obtém o resultado da criptografia para um banco de dados|

## <a name="see-also"></a>Consulte Também
- O SQL Server em execução em uma máquina virtual do Azure também pode usar uma chave assimétrica do Key Vault. As etapas de configuração são diferentes de usar uma chave assimétrica no Banco de Dados SQL e Instância Gerenciada do Banco de Dados SQL. Para obter mais informações, consulte [Gerenciamento de chave extensível usando Azure Key Vault (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server).
- Para obter uma descrição geral do TDE, consulte [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).
- Para saber mais sobre o TDE com suporte do BYOK para o banco de dados SQL do Azure, Azure SQL Instância Gerenciada e Azure Synapse, consulte [Transparent Data Encryption com suporte a Bring your own Key](transparent-data-encryption-byok-azure-sql.md).
- Para começar a usar o TDE com suporte a Bring Your Own Key, consulte o guia de instruções, [ativar a Transparent Data Encryption usando sua própria chave de Key Vault](transparent-data-encryption-byok-azure-sql-configure.md).
- Para obter mais informações sobre Key Vault, consulte [proteger o acesso a um cofre de chaves](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).
