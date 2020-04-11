---
title: Transparent Data Encryption
description: Uma visão geral da criptografia de dados transparente para o SQL Database e o Synapse SQL no Azure Synapse Analytics. O documento abrange os benefícios e as opções de configuração que incluem Transparent Data Encryption gerenciado pelo serviço e Bring Your Own Key.
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
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113545"
---
# <a name="transparent-data-encryption-for-sql-database-and-azure-synapse"></a>Criptografia de dados transparente para banco de dados SQL e Sinapse Azure

[A criptografia de dados transparente (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) ajuda a proteger o Banco de Dados SQL do Azure, a instância gerenciada do Azure SQL e o Synapse SQL no Azure Synapse Analytics contra a ameaça de atividade off-line maliciosa criptografando dados em repouso. Ela realiza a criptografia e a descriptografia em tempo real do banco de dados, de backups associados e de arquivos de log de transações em repouso, sem a necessidade de alterações no aplicativo. Por padrão, o TDE está habilitado para todos os bancos de dados Azure SQL recém-implantados e precisa ser ativado manualmente para bancos de dados mais antigos do Azure SQL Database, Azure SQL Managed Instance ou Azure Synapse.

A TDE realiza a criptografia e a descriptografia de E/S em tempo real dos dados no nível da página. Cada página é descriptografada quando é lida na memória e, em seguida, criptografada antes de ser gravada no disco. O TDE criptografa o armazenamento de um banco de dados inteiro usando uma chave simétrica chamada de Chave de Criptografia de Banco de Dados (DEK). Na inicialização do banco de dados, o DEK criptografado é descriptografado e depois usado para descriptografia e recriptografia dos arquivos do banco de dados no processo SQL Server Database Engine. O DEK é protegido pelo protetor TDE. O protetor TDE é um certificado gerenciado por serviço (criptografia de dados transparente gerenciada por serviço) ou uma chave assimétrica armazenada no [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault) (criptografia de dados transparente gerenciada pelo cliente). 

Para o Azure SQL Database e o Azure Synapse, o protetor TDE é definido no nível lógico do servidor SQL e é herdado por todos os bancos de dados associados a esse servidor. Para a instância gerenciada do Azure SQL (recurso BYOK na pré-visualização), o protetor TDE é definido no nível de ocorrência e é herdado por todos os bancos de dados criptografados nessa instância. O termo *servidor* refere-se ao servidor e instância ao longo deste documento, a menos que indicado de forma diferente.

> [!IMPORTANT]
> Todos os bancos de dados Azure SQL recém-criados são criptografados por padrão usando criptografia de dados transparente gerenciada por serviço. Os bancos de dados SQL existentes criados antes de maio de 2017 e bancos de dados SQL criados por meio de restauração, georeplicação e cópia de banco de dados não são criptografados por padrão. Os bancos de dados de instância gerenciada existentes criados antes de fevereiro de 2019 não são criptografados por padrão. Bancos de dados de instância gerenciada criados através da restauração herdam o status de criptografia da fonte.

> [!NOTE]
> A TDE não pode ser utilizada para criptografar o banco de dados **mestre** lógico no Banco de Dados SQL.  O banco de dados **mestre** contém os objetos necessários para executar as operações de TDE nos bancos de dados do usuário.


## <a name="service-managed-transparent-data-encryption"></a>Transparent Data Encryption de serviço gerenciado

No Azure, a configuração padrão do TDE é que o DEK é protegido por um certificado de servidor incorporado. O certificado de servidor incorporado é exclusivo para cada servidor e o algoritmo de criptografia usado é o AES 256. Se um banco de dados estiver em uma relação de georeplicação, os bancos de dados primários e geosecundários serão protegidos pela chave de servidor pai do banco de dados principal. Se dois bancos de dados estiverem conectados ao mesmo servidor, eles também compartilharão o mesmo certificado interno.  A Microsoft gira automaticamente esses certificados em conformidade com a política de segurança interna e a chave raiz é protegida por um armazenamento secreto interno da Microsoft.  Os clientes podem verificar a conformidade do Banco de Dados SQL com políticas de segurança interna em relatórios de auditoria independentes de terceiros disponíveis no [Microsoft Trust Center](https://servicetrust.microsoft.com/).

A Microsoft também move e gerencia as chaves conforme necessário para replicação geográfica e restaurações.


## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>Transparent Data Encryption gerenciada pelo cliente – Bring Your Own Key

O TDE gerenciado pelo cliente também é referido como suporte bring your own key (BYOK) para TDE. Neste cenário, o TDE Protector que criptografa o DEK é uma chave assimétrica gerenciada pelo cliente, que é armazenada em um Azure Key Vault (sistema de gerenciamento de chaves externas baseado na nuvem do Azure) e nunca sai do cofre principal. O TDE Protector pode ser [gerado pelo cofre de chaves ou transferido para o cofre de chaves](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys) a partir de um dispositivo HSM (On Premises Hardware Security Module, módulo de segurança de hardware no local). O Banco de Dados SQL precisa ter permissões concedidas para o cofre de chaves de propriedade do cliente para descriptografar e criptografar a DEK. Se as permissões do servidor SQL lógico para o cofre de chaves forem revogadas, um banco de dados será inacessível e todos os dados serão criptografados

Com a integração do TDE ao Azure Key Vault, os usuários podem controlar as principais tarefas de gerenciamento, incluindo rotações de chave, permissões de cofre de chaves, backups de chaves e habilitar auditoria/relatório em todos os protetores de TDE usando a funcionalidade do Azure Key Vault. O Key Vault fornece gerenciamento de chaves centrais, aproveita HSMs monitorados rigorosamente e permite a separação de funções entre gerenciamento de chaves e dados para ajudar a atender à conformidade com as políticas de segurança.
Para saber mais sobre o BYOK for Azure SQL Database e Azure Synapse, consulte [criptografia de dados transparente com integração do Azure Key Vault](transparent-data-encryption-byok-azure-sql.md).

Para começar a usar o TDE com a integração do Azure Key Vault, consulte o guia como [ativar a criptografia de dados transparente usando sua própria chave do Key Vault](transparent-data-encryption-byok-azure-sql-configure.md).

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

Quando você exporta um banco de dados protegido por TDE, o conteúdo exportado do banco de dados não é criptografado. Esse conteúdo exportado é armazenado em arquivos BACPAC não criptografados. Certifique-se de proteger os arquivos BACPAC adequadamente e habilitar o TDE após a importação do novo banco de dados ser concluída.

Por exemplo, se o arquivo BACPAC for exportado de uma instância do SQL Server local, o conteúdo importado do novo banco de dados não será automaticamente criptografado. Da mesma forma, se o arquivo BACPAC for exportado para uma instância do SQL Server local, o novo banco de dados também não será automaticamente criptografado.

A única exceção é quando você exporta para e de um banco de dados SQL. O TDE está habilitado no novo banco de dados, mas o arquivo BACPAC em si ainda não está criptografado.


## <a name="manage-transparent-data-encryption"></a>Gerenciar criptografia de dados transparente
# <a name="portal"></a>[Portal](#tab/azure-portal)
Gerencie o TDE no portal Azure.

Para configurar o TDE através do portal Azure, você deve estar conectado como o Proprietário, Contribuinte ou Gerenciador de Segurança SQL do Azure.

Você liga e desliga o TDE no nível do banco de dados. Para habilitar o TDE em um banco de dados, acesse o [portal do Azure](https://portal.azure.com) e faça login com sua conta do Administrador ou Contribuinte do Azure. Localize as configurações da TDE em seu banco de dados do usuário. Por padrão, é utilizada a Transparent Data Encryption de serviço gerenciado. Um certificado TDE é gerado automaticamente para o servidor que contém o banco de dados. Para a instância gerenciada do Azure SQL, use o T-SQL para ativar e desativar o TDE em um banco de dados.

![Transparent Data Encryption de serviço gerenciado](./media/transparent-data-encryption-azure-sql/service-managed-transparent-data-encryption.png)  

Você define a tecla mestre TDE, conhecida como protetor TDE, no nível do servidor. Para usar o suporte ao TDE com suporte BYOK e proteger seus bancos de dados com uma chave do Key Vault, abra as configurações do TDE em seu servidor.

![Transparent Data Encryption com suporte Bring Your Own Key](./media/transparent-data-encryption-azure-sql/tde-byok-support.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Gerencie o TDE usando o PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pelo Banco de Dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para obter esses cmdlets, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

Para configurar a TDE por meio do PowerShell, você deve estar conectado como o proprietário do Azure, o Colaborador ou o Gerenciador de segurança do SQL.

### <a name="cmdlets-for-azure-sql-database-and-azure-synapse"></a>Cmdlets para Banco de Dados SQL Azure e Sinapse Azure

Use os seguintes cmdlets para O Banco de Dados SQL do Azure e para o Azure Synapse:

| Cmdlet | Descrição |
| --- | --- |
| [Set-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) |Habilita ou desabilita a Transparent Data Encryption para um banco de dados|
| [Get-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |Obtém o estado de Transparent Data Encryption para um banco de dados |
| [Get-AzSqlDatabaseTransparentDataEncryptionActivity](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) |Verifica o progresso de Transparent Data Encryption de um banco de dados |
| [Add-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/add-azsqlserverkeyvaultkey) |Adiciona uma chave do Key Vault a uma instância do SQL Server |
| [Get-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserverkeyvaultkey) |Obtém as chaves do Key Vault para um servidor do Banco de Dados SQL do Azure  |
| [Set-AzSqlServerTransparentDataArmEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) |Define o protetor de Transparent Data Encryption para uma instância do SQL Server |
| [Get-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) |Obtém o protetor de Transparent Data Encryption |
| [Remove-AzSqlServerKeyKeyKey](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) |Remove uma chave do Key Vault de uma instância do SQL Server |
|  | |

> [!IMPORTANT]
> Para a instância gerenciada do Azure SQL, use o comando T-SQL [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) para ativar e desativar o TDE em um nível de banco de dados e verifique o [script PowerShell de amostra](transparent-data-encryption-byok-azure-sql-configure.md) para gerenciar o TDE em um nível de instância.

# <a name="transact-sql"></a>[Transact-SQL](#tab/azure-TransactSQL)
Gerencie o TDE usando o Transact-SQL.

Conecte o banco de dados usando um logon que seja um administrador ou membro da função **dbmanager** no banco de dados mestre.

| Comando | Descrição |
| --- | --- |
| [ALTER DATABASE (Banco de Dados SQL do Azure)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) | SET ENCRYPTION ON/OFF criptografa ou descriptografa um banco de dados |
| [sys.dm_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Retorna informações sobre o estado de criptografia de um banco de dados e as chaves de criptografia de banco de dados associadas |
| [sys.dm_pdw_nodes_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Retorna informações sobre o estado de criptografia de cada nó do Azure Synapse e suas chaves de criptografia de banco de dados associadas |
|  | |

Você não pode mudar o protetor TDE para uma chave do Key Vault usando Transact-SQL. Use PowerShell o portal do Azure.

# <a name="rest-api"></a>[API REST](#tab/azure-RESTAPI)
Gerencie o TDE usando a API REST.

Para configurar o TDE através da API REST, você deve estar conectado como o Proprietário, Contribuinte ou Gerenciador de Segurança SQL do Azure.
Use o seguinte conjunto de comandos para O Banco de Dados SQL do Azure e sinapse do Azure:

| Comando | Descrição |
| --- | --- |
|[Criar ou atualizar o servidor](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Adiciona uma identidade do Azure Active Directory a uma instância do SQL Server (usada para conceder acesso ao Key Vault)|
|[Criar ou atualizar a chave do servidor](https://docs.microsoft.com/rest/api/sql/serverkeys/createorupdate)|Adiciona uma chave do Key Vault a uma instância do SQL Server|
|[Excluir chave do servidor](https://docs.microsoft.com/rest/api/sql/serverkeys/delete)|Remove uma chave do Key Vault de uma instância do SQL Server|
|[Obter chaves do servidor](https://docs.microsoft.com/rest/api/sql/serverkeys/get)|Obtém uma chave específica do Key Vault de uma instância do SQL Server|
|[Listar chaves do servidor por servidor](https://docs.microsoft.com/rest/api/sql/serverkeys/listbyserver)|Obtém as chaves do Key Vault para uma instância do SQL Server |
|[Criar ou atualizar o protetor de criptografia](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/createorupdate)|Define o protetor TDE para uma instância do SQL Server|
|[Obter protetor de criptografia](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/get)|Obtém o protetor TDE para uma instância do SQL Server|
|[Listar protetores de criptografia por servidor](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/listbyserver)|Obtém os protetores TDE para uma instância do SQL Server |
|[Criar ou atualizar a configuração de Transparent Data Encryption](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/createorupdate)|Habilita ou desativa o TDE para um banco de dados|
|[Obter configuração de Transparent Data Encryption](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/get)|Obtém a configuração TDE para um banco de dados|
|[Lista de resultados de configuração de Transparent Data Encryption](https://docs.microsoft.com/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|Obtém o resultado da criptografia para um banco de dados|

## <a name="see-also"></a>Consulte Também
- O SQL Server em execução em uma máquina virtual do Azure também pode usar uma chave assimétrica do Key Vault. As etapas de configuração são diferentes de usar uma chave assimétrica no Banco de Dados SQL e Instância Gerenciada do Banco de Dados SQL. Para obter mais informações, consulte [Gerenciamento de chave extensível usando Azure Key Vault (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server).
- Para obter uma descrição geral do TDE, consulte criptografia de [dados transparente](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).
- Para saber mais sobre o TDE com suporte ao BYOK para o Banco de Dados SQL Do Azure, a instância gerenciada do Azure SQL e o Azure Synapse, consulte [criptografia de dados transparente com suporte ao Bring Your Own Key](transparent-data-encryption-byok-azure-sql.md).
- Para começar a usar o suporte ao TDE com o suporte bring your own key, consulte o guia de como fazer, [ative a criptografia de dados transparente usando sua própria chave do Key Vault](transparent-data-encryption-byok-azure-sql-configure.md).
- Para obter mais informações sobre o Key Vault, consulte [O acesso seguro a um cofre de chaves](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).
