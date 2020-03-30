---
title: Remover protetor TDE - PowerShell
description: Guia de instruções para responder a um protetor de TDE potencialmente comprometido de um Data Warehouse ou Banco de Dados SQL do Azure usando TDE com o suporte BYOK (Bring Your Own Key).
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 02/24/2020
ms.openlocfilehash: 5a89c3f7d52c5717b902a69e9c64b3fcc422c481
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067207"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Remover um protetor de TDE (Transparent Data Encryption) usando PowerShell

## <a name="prerequisites"></a>Pré-requisitos

- É necessário ter uma assinatura do Azure e ser um administrador nessa assinatura
- Você deve ter o Azure PowerShell instalado e executado.
- Este guia de instruções assume que você já esteja usando uma chave do Azure Key Vault como protetor de TDE para um Data Warehouse ou Banco de Dados SQL do Azure. Consulte [Transparent Data Encryption com suporte de BYOK](transparent-data-encryption-byok-azure-sql.md) para saber mais.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

 Para obter instruções de instalação do módulo Az, confira [Instalar o Azure PowerShell](/powershell/azure/install-az-ps). Para cmdlets específicos, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> O módulo RM (PowerShell Azure Resource Manager, gerente de recursos do Azure) ainda é suportado pelo Banco de Dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo Az.Sql. O módulo AzureRM continuará recebendo correções de bugs até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. Para obter mais informações sobre sua compatibilidade, consulte [Introduzindo o novo módulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Para instalação, consulte [Install Azure CLI](/cli/azure/install-azure-cli).

* * *

## <a name="overview"></a>Visão geral

Este guia de instruções descreve como responder a um protetor de TDE potencialmente comprometido para um Data Warehouse ou Banco de Dados SQL do Azure que esteja usando o TDE com suporte para chaves gerenciadas pelo cliente no Azure Key Vault - Bring Your Own Key (BYOK). Para saber mais sobre o suporte BYOK para TDE, consulte a [página de visão geral](transparent-data-encryption-byok-azure-sql.md).

Os procedimentos a seguir devem ser feitos apenas em casos extremos ou em ambientes de teste. Revise cuidadosamente o guia de como fazer, pois a exclusão de protetores TDE usados ativamente do Azure Key Vault resultará em **indisponibilidade**no banco de dados .

Se uma chave é suspeita de ser comprometida, de tal forma que um serviço ou usuário teve acesso não autorizado à chave, é melhor excluir a chave.

Tenha em mente que uma vez que o protetor TDE seja excluído no Key Vault, em até 10 minutos todos os bancos de dados criptografados começarão a negar todas as conexões com a mensagem de erro correspondente e alterarão seu estado para [Inacessível](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql#inaccessible-tde-protector).

As etapas a seguir descrevem como verificar as impressões digitais do TDE Protector ainda em uso por Arquivos de Log Virtuais (VLF) de um determinado banco de dados.
A impressão digital do protetor TDE atual do banco de dados e o ID do banco de dados podem ser encontrados executando:

```sql
SELECT [database_id],
       [encryption_state],
       [encryptor_type], /*asymmetric key means AKV, certificate means service-managed keys*/
       [encryptor_thumbprint],
 FROM [sys].[dm_database_encryption_keys]
```

A consulta a seguir retorna os VLFs e as respectivas impressões digitais do criptografador em uso. Cada impressão digital diferente refere-se a uma chave diferente no Azure Key Vault (AKV):

```sql
SELECT * FROM sys.dm_db_log_info (database_id)
```

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

O comando PowerShell **Get-AzureRmSqlServerKeyVaultKey** fornece a impressão digital do Protetor TDE usado na consulta, para que você possa ver quais chaves manter e quais chaves excluir no AKV. Apenas as chaves não usadas pelo banco de dados podem ser excluídas com segurança do Azure Key Vault.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

O programa de chaves do servidor PowerShell **az sql**fornece a impressão digital do Protetor TDE usado na consulta, para que você possa ver quais chaves manter e quais teclas excluir no AKV. Apenas as chaves não usadas pelo banco de dados podem ser excluídas com segurança do Azure Key Vault.

* * *

Este guia de instruções abrange duas abordagens, dependendo do resultado desejado após a resposta ao incidente:

- Para manter os bancos de dados SQL/Data Warehouses do Azure **acessíveis**
- Para tornar os bancos de dados SQL/Data Warehouses do Azure **inacessíveis**

## <a name="to-keep-the-encrypted-resources-accessible"></a>Para manter os recursos criptografados acessíveis

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

1. Crie uma [nova chave no Key Vault](/powershell/module/az.keyvault/add-azkeyvaultkey). Certifique-se de que essa nova chave seja criada em um cofre de chaves separado do protetor de TDE potencialmente comprometido, já que o controle de acesso é provisionado em um nível de segurança.

2. Adicione a nova chave ao servidor usando os [cmdlets Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) e [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) e atualize-o como o novo protetor TDE do servidor.

   ```powershell
   # add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

   # set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> `
       -ServerName <LogicalServerName> -Type AzureKeyVault -KeyId <KeyVaultKeyId>
   ```

3. Certifique-se de que o servidor e quaisquer réplicas tenham sido atualizados para o novo protetor TDE usando o [cmdlet Get-AzSqlServerTransparentDataEncryptionProtector.](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector)

   > [!NOTE]
   > Pode demorar alguns minutos para o novo protetor de TDE se propagar em todos os bancos de dados e bancos de dados secundários no servidor.

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Faça um [backup da nova chave](/powershell/module/az.keyvault/backup-azkeyvaultkey) no Key Vault.

   ```powershell
   # -OutputFile parameter is optional; if removed, a file name is automatically generated.
   Backup-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName> -OutputFile <DesiredBackupFilePath>
   ```

5. Exclua a chave comprometida do Key Vault usando o [cmdlet Remove-AzKeyVaultKey.](/powershell/module/az.keyvault/remove-azkeyvaultkey)

   ```powershell
   Remove-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName>
   ```

6. Para restaurar uma chave para o Key Vault no futuro usando o cmdlet [Restore-AzKeyVaultKey:](/powershell/module/az.keyvault/restore-azkeyvaultkey)

   ```powershell
   Restore-AzKeyVaultKey -VaultName <KeyVaultName> -InputFile <BackupFilePath>
   ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Para referência de comando, consulte o [cofre de chaves Azure CLI](/cli/azure/keyvault/key).

1. Crie uma [nova chave no Key Vault](/cli/azure/keyvault/key#az-keyvault-key-create). Certifique-se de que essa nova chave seja criada em um cofre de chaves separado do protetor de TDE potencialmente comprometido, já que o controle de acesso é provisionado em um nível de segurança.

2. Adicione a nova chave ao servidor e atualize-a como o novo protetor TDE do servidor.

   ```azurecli
   # add the key from Key Vault to the server  
   az sql server key create --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>

   # set the key as the TDE protector for all resources under the server
   az sql server tde-key set --server-key-type AzureKeyVault --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

3. Certifique-se de que o servidor e quaisquer réplicas tenham sido atualizados para o novo protetor TDE.

   > [!NOTE]
   > Pode demorar alguns minutos para o novo protetor de TDE se propagar em todos os bancos de dados e bancos de dados secundários no servidor.

   ```azurecli
   az sql server tde-key show --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

4. Faça um backup da nova chave no Key Vault.

   ```azurecli
   # --file parameter is optional; if removed, a file name is automatically generated.
   az keyvault key backup --file <DesiredBackupFilePath> --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

5. Exclua a chave comprometida do Key Vault.

   ```azurecli
   az keyvault key delete --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

6. Para restaurar a chave do Key Vault no futuro.

   ```azurecli
   az keyvault key restore --file <BackupFilePath> --vault-name <KeyVaultName>
   ```

* * *

## <a name="to-make-the-encrypted-resources-inaccessible"></a>Para tornar os recursos criptografados inacessíveis

1. Remova os bancos de dados que estão sendo criptografados pela chave potencialmente comprometida.

   O backup do banco de dados e dos arquivos de log é feito automaticamente, portanto, uma recuperação pontual do banco de dados poderá ser realizada a qualquer momento (contanto que você forneça a chave). Os bancos de dados deverão ser removidos antes da exclusão de um protetor de TDE ativo para evitar uma possível perda de dados de até 10 minutos das transações mais recentes.

2. Faça backup do material da chave do protetor de TDE no Key Vault.
3. Remover a chave potencialmente comprometida do Key Vault

[!INCLUDE [sql-database-akv-permission-delay](includes/sql-database-akv-permission-delay.md)]

## <a name="next-steps"></a>Próximas etapas

- Saiba como girar o protetor TDE de um servidor para atender aos requisitos de segurança: [Gire o protetor de criptografia de dados transparente usando o PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- Introdução ao suporte Bring Your Own Key para TDE: [Ativar o TDE com sua própria chave do Key Vault usando PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)
