---
title: Enable SQL TDE with Azure Key Vault
description: Saiba como configurar um Data Warehouse e Banco de Dados SQL do Azure para começar a usar a TDE (Transparent Data Encryption) para criptografia em repouso usando PowerShell ou CLI.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: e60b83f8eb1e95733512180417bb2a1be679b0a0
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422453"
---
# <a name="powershell-and-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>PowerShell and CLI: Enable Transparent Data Encryption with customer-managed key from Azure Key Vault

Este artigo descreve como usar uma chave do Azure Key Vault para TDE (Transparent Data Encryption) em um Banco de Dados SQL ou Data Warehouse. Para saber mais sobre a integração da TDE com o Azure Key Vault - Suporte a Bring Your Own Key (BYOK), visite [TDE com chaves gerenciadas pelo cliente no Azure Key Vault](transparent-data-encryption-byok-azure-sql.md).

## <a name="prerequisites-for-powershell"></a>Pré-requisitos para PowerShell

- É necessário ter uma assinatura do Azure e ser um administrador nessa assinatura.
- [Recomendado, mas opcional] Tenha um HSM (Módulo de Segurança de Hardware) ou armazenamento de chave local para criar uma cópia local do material da chave do Protetor de TDE.
- You must have Azure PowerShell installed and running.
- Crie um Azure Key Vault e Chave para usar na TDE.
  - [Instruções para usar um HSM (Módulo de Segurança de Hardware) e Key Vault](../key-vault/key-vault-hsm-protected-keys.md)
    - O cofre de chaves deve ter a seguinte propriedade a ser usada para TDE:
  - [soft-delete](../key-vault/key-vault-ovw-soft-delete.md) and purge protection
- A chave deve ter os seguintes atributos a serem usados para TDE:
   - Sem data de validade
   - Não desabilitado
   - Capaz de realizar operações *get*, *codificar chave*, *decodificar chave*

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Para obter instruções de instalação do módulo Az, confira [Instalar o Azure PowerShell](/powershell/azure/install-az-ps). For specific cmdlets, see [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/).

For specifics on Key Vault, see [PowerShell instructions from Key Vault](../key-vault/quick-create-powershell.md) and [How to use Key Vault soft-delete with PowerShell](../key-vault/key-vault-soft-delete-powershell.md).

> [!IMPORTANT]
> The PowerShell Azure Resource Manager (RM) module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. The AzureRM module will continue to receive bug fixes until at least December 2020.  The arguments for the commands in the Az module and in the AzureRm modules are substantially identical. For more about their compatibility, see [Introducing the new Azure PowerShell Az module](/powershell/azure/new-azureps-module-az).

## <a name="assign-an-azure-ad-identity-to-your-server"></a>Atribuir uma identidade do Azure AD ao servidor

Se houver um servidor existente, use o seguinte para adicionar uma identidade do Azure AD ao servidor:

   ```powershell
   $server = Set-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -AssignIdentity
   ```

If you are creating a server, use the [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) cmdlet with the tag -Identity to add an Azure AD identity during server creation:

   ```powershell
   $server = New-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -Location <RegionName> `
       -ServerName <LogicalServerName> -ServerVersion "12.0" -SqlAdministratorCredentials <PSCredential> -AssignIdentity
   ```

## <a name="grant-key-vault-permissions-to-your-server"></a>Conceder permissões do Key Vault ao servidor

Use the [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) cmdlet to grant your server access to the key vault before using a key from it for TDE.

   ```powershell
   Set-AzKeyVaultAccessPolicy -VaultName <KeyVaultName> `
       -ObjectId $server.Identity.PrincipalId -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Adicionar a chave do Key Vault ao servidor e definir o Protetor de TDE

- Use the [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey?view=azps-2.4.0) cmdlet to retrieve the key ID from key vault
- Use the [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) cmdlet to add the key from the Key Vault to the server.
- Use the [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet to set the key as the TDE protector for all server resources.
- Use the [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) cmdlet to confirm that the TDE protector was configured as intended.

> [!NOTE]
> O comprimento combinado para o nome do cofre de chaves e o nome da chave não pode exceder 94 caracteres.

> [!TIP]
> Um exemplo de KeyId do Key Vault: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h

```powershell
# add the key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId>

# confirm the TDE protector was configured as intended
Get-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName>
```

## <a name="turn-on-tde"></a>Ativar TDE

Use the [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) cmdlet to turn on TDE.

```powershell
Set-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> -State "Enabled"
```

Agora, o banco de dados ou data warehouse possui a TDE habilitada com uma chave de criptografia no Key Vault.

## <a name="check-the-encryption-state-and-encryption-activity"></a>Verificar o estado de criptografia e a atividade de criptografia

Use the [Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) to get the encryption state and the [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) to check the encryption progress for a database or data warehouse.

```powershell
# get the encryption state
Get-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> `

# check the encryption progress for a database or data warehouse
Get-AzSqlDatabaseTransparentDataEncryptionActivity -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName>  
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

To install the required Command-Line Interface version 2.0 or later and connect to your Azure subscription, see [Install and Configure the Azure Cross-Platform Command-Line Interface 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

For specifics on Key Vault, see [Manage Key Vault using CLI 2.0](../key-vault/key-vault-manage-with-cli2.md) and [How to use Key Vault soft-delete with CLI](../key-vault/key-vault-soft-delete-cli.md).

## <a name="assign-an-azure-ad-identity-to-your-server"></a>Atribuir uma identidade do Azure AD ao servidor

```powershell
# create server (with identity) and database
az sql server create --name <servername> --resource-group <rgname>  --location <location> --admin-user <user> --admin-password <password> --assign-identity
az sql db create --name <dbname> --server <servername> --resource-group <rgname>
```

> [!TIP]
> Impeça que a "principalID" crie o servidor; a ID de objeto usada para atribuir permissões de cofre de chaves na próxima etapa

## <a name="grant-key-vault-permissions-to-your-server"></a>Conceder permissões do Key Vault ao servidor

```powershell
# create key vault, key and grant permission
az keyvault create --name <kvname> --resource-group <rgname> --location <location> --enable-soft-delete true
az keyvault key create --name <keyname> --vault-name <kvname> --protection software
az keyvault set-policy --name <kvname>  --object-id <objectid> --resource-group <rgname> --key-permissions wrapKey unwrapKey get
```

> [!TIP]
> Salve o URI da chave ou a keyID da nova chave para a próxima etapa, por exemplo: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Adicionar a chave do Key Vault ao servidor e definir o Protetor de TDE

```powershell
# add server key and update encryption protector
az sql server key create --server <servername> --resource-group <rgname> --kid <keyID>
az sql server tde-key set --server <servername> --server-key-type AzureKeyVault  --resource-group <rgname> --kid <keyID>
```

> [!NOTE]
> O comprimento combinado para o nome do cofre de chaves e o nome da chave não pode exceder 94 caracteres.

## <a name="turn-on-tde"></a>Ativar TDE

```powershell
# enable encryption
az sql db tde set --database <dbname> --server <servername> --resource-group <rgname> --status Enabled
```

Agora, o banco de dados ou data warehouse possui a TDE habilitada com uma chave de criptografia gerenciada pelo cliente no Azure Key Vault.

## <a name="check-the-encryption-state-and-encryption-activity"></a>Verificar o estado de criptografia e a atividade de criptografia

```powershell
# get encryption scan progress
az sql db tde list-activity --database <dbname> --server <servername> --resource-group <rgname>  

# get whether encryption is on or off
az sql db tde show --database <dbname> --server <servername> --resource-group <rgname>
```

* * *

## <a name="useful-powershell-cmdlets"></a>Cmdlets úteis do PowerShell

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

- Use the [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) cmdlet to turn off TDE.

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName> `
      -DatabaseName <DatabaseName> -State "Disabled”
   ```

- Use the [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) cmdlet to return the list of Key Vault keys added to the server.

   ```powershell
   # KeyId is an optional parameter, to return a specific key version
   Get-AzSqlServerKeyVaultKey -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

- Use the [Remove-AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) to remove a Key Vault key from the server.

   ```powershell
   # the key set as the TDE Protector cannot be removed
   Remove-AzSqlServerKeyVaultKey -KeyId <KeyVaultKeyId> -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

- For general database settings, see [az sql](/cli/azure/sql).

- For vault key settings, see [az sql server key](/cli/azure/sql/server/key).

- For TDE settings, see [az sql server tde-key](/cli/azure/sql/server/tde-key) and [az sql db tde](/cli/azure/sql/db/tde).

* * *

## <a name="troubleshooting"></a>Solução de Problemas

Se um problema ocorrer, verifique o seguinte:

- If the key vault cannot be found, make sure you're in the right subscription.

   # <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell
   Get-AzSubscription -SubscriptionId <SubscriptionId>
   ```

   # <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

   ```powershell
   az account show - s <SubscriptionId>
   ```

   * * *

- Se a nova chave não puder ser adicionada ao servidor, ou a nova chave não puder ser atualizada como o protetor de TDE, verifique o seguinte:
   - A chave não deve ter uma data de validade
   - A chave deve ter as operações *get*, *codificar chave* e *decodificar chave* habilitadas.

## <a name="next-steps"></a>Próximos passos

- Saiba como girar o protetor de TDE de um servidor para atender aos requisitos de segurança: [Girar o Protetor de TDE usando PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- Em caso de risco de segurança, saiba como remover um Protetor de TDE potencialmente comprometido: [Remover uma chave potencialmente comprometida](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md).
