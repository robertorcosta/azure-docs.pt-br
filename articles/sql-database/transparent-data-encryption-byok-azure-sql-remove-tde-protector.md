---
title: Remover o protetor de TDE – PowerShell
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
ms.date: 03/12/2019
ms.openlocfilehash: ba591872f4f8af93e5f7e13e0fb69d0679fd1a0c
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75965760"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Remover um protetor de TDE (Transparent Data Encryption) usando PowerShell

## <a name="prerequisites"></a>Pré-requisitos

- É necessário ter uma assinatura do Azure e ser um administrador nessa assinatura
- Você deve ter Azure PowerShell instalado e em execução.
- Este guia de instruções assume que você já esteja usando uma chave do Azure Key Vault como protetor de TDE para um Data Warehouse ou Banco de Dados SQL do Azure. Consulte [Transparent Data Encryption com suporte de BYOK](transparent-data-encryption-byok-azure-sql.md) para saber mais.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

 Para obter instruções de instalação do módulo Az, confira [Instalar o Azure PowerShell](/powershell/azure/install-az-ps). Para obter cmdlets específicos, consulte [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell (RM) ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. O módulo AzureRM continuará a receber correções de bugs até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos. Para obter mais informações sobre sua compatibilidade, consulte [apresentando o novo módulo Azure PowerShell AZ](/powershell/azure/new-azureps-module-az).

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para instalação, consulte [instalar CLI do Azure](/cli/azure/install-azure-cli).

* * *

## <a name="overview"></a>Visão Geral

Este guia de instruções descreve como responder a um protetor de TDE potencialmente comprometido para um Data Warehouse ou Banco de Dados SQL do Azure que esteja usando o TDE com suporte para chaves gerenciadas pelo cliente no Azure Key Vault - Bring Your Own Key (BYOK). Para saber mais sobre o suporte BYOK para TDE, consulte a [página de visão geral](transparent-data-encryption-byok-azure-sql.md).

Os procedimentos a seguir devem ser feitos apenas em casos extremos ou em ambientes de teste. Revise cuidadosamente o guia de instruções, pois a exclusão de protetores de TDE ativamente usados do Azure Key Vault pode resultar na **perda de dados**.

Se houver a suspeita de que uma chave está comprometida, de modo que um serviço ou usuário tenha acesso não autorizado à chave, é melhor excluir a chave.

Lembre-se de que, depois que o protetor de TDE for excluído no Key Vault, **todas as conexões com os bancos de dados criptografados no servidor serão bloqueadas e esses bancos de dados ficarão offline e serão removidos em 24 horas**. Backups antigos criptografados com a chave comprometida não serão mais acessíveis.

As etapas a seguir descrevem como verificar as impressões digitais do protetor de TDE que ainda estão em uso por VLF (arquivos de log virtuais) de um determinado banco de dados.
A impressão digital do protetor de TDE atual do banco de dados e a ID do banco de dados podem ser encontradas executando:

```sql
SELECT [database_id],
       [encryption_state],
       [encryptor_type], /*asymmetric key means AKV, certificate means service-managed keys*/
       [encryptor_thumbprint],
 FROM [sys].[dm_database_encryption_keys]
```

A consulta a seguir retorna o VLFs e o criptografador respectivas impressões digitais em uso. Cada impressão digital diferente refere-se a uma chave diferente em Azure Key Vault (AKV):

```sql
SELECT * FROM sys.dm_db_log_info (database_id)
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

O comando **Get-AzureRmSqlServerKeyVaultKey** do PowerShell fornece a impressão digital do protetor de TDE usado na consulta, para que você possa ver quais chaves manter e quais chaves serão excluídas em akv. Somente as chaves que não são mais usadas pelo banco de dados podem ser excluídas com segurança do Azure Key Vault.

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

O comando **AZ SQL Server Key show** fornece a impressão digital do protetor TDE usado na consulta, para que você possa ver quais chaves manter e quais chaves excluir em akv. Somente as chaves que não são mais usadas pelo banco de dados podem ser excluídas com segurança do Azure Key Vault.

* * *

Este guia de instruções abrange duas abordagens, dependendo do resultado desejado após a resposta ao incidente:

- Para manter os bancos de dados SQL/Data Warehouses do Azure **acessíveis**
- Para tornar os bancos de dados SQL/Data Warehouses do Azure **inacessíveis**

## <a name="to-keep-the-encrypted-resources-accessible"></a>Para manter os recursos criptografados acessíveis

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

1. Crie uma [nova chave no Key Vault](/powershell/module/az.keyvault/add-azkeyvaultkey). Certifique-se de que essa nova chave seja criada em um cofre de chaves separado do protetor de TDE potencialmente comprometido, já que o controle de acesso é provisionado em um nível de segurança.

2. Adicione a nova chave ao servidor usando os cmdlets [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) e [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) e atualize-o como o novo protetor TDE do servidor.

   ```powershell
   # add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

   # set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> `
       -ServerName <LogicalServerName> -Type AzureKeyVault -KeyId <KeyVaultKeyId>
   ```

3. Verifique se o servidor e todas as réplicas foram atualizadas para o novo protetor TDE usando o cmdlet [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) .

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

5. Exclua a chave comprometida de Key Vault usando o cmdlet [Remove-AzKeyVaultKey](/powershell/module/az.keyvault/remove-azkeyvaultkey) .

   ```powershell
   Remove-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName>
   ```

6. Para restaurar uma chave para Key Vault no futuro usando o cmdlet [Restore-AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey) :

   ```powershell
   Restore-AzKeyVaultKey -VaultName <KeyVaultName> -InputFile <BackupFilePath>
   ```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para referência de comando, consulte o [CLI do Azure keyvault](/cli/azure/keyvault/key).

1. Crie uma [nova chave no Key Vault](/cli/azure/keyvault/key#az-keyvault-key-create). Certifique-se de que essa nova chave seja criada em um cofre de chaves separado do protetor de TDE potencialmente comprometido, já que o controle de acesso é provisionado em um nível de segurança.

2. Adicione a nova chave ao servidor e atualize-a como o novo protetor de TDE do servidor.

   ```powershell
   # add the key from Key Vault to the server  
   az sql server key create --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>

   # set the key as the TDE protector for all resources under the server
   az sql server tde-key set --server-key-type AzureKeyVault --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

3. Verifique se o servidor e as réplicas foram atualizados para o novo protetor de TDE.

   > [!NOTE]
   > Pode demorar alguns minutos para o novo protetor de TDE se propagar em todos os bancos de dados e bancos de dados secundários no servidor.

   ```powershell
   az sql server tde-key show --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

4. Faça um backup da nova chave em Key Vault.

   ```powershell
   # --file parameter is optional; if removed, a file name is automatically generated.
   az keyvault key backup --file <DesiredBackupFilePath> --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

5. Exclua a chave comprometida de Key Vault.

   ```powershell
   az keyvault key delete --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

6. Para restaurar uma chave para Key Vault no futuro.

   ```powershell
   az keyvault key restore --file <BackupFilePath> --vault-name <KeyVaultName>
   ```

* * *

## <a name="to-make-the-encrypted-resources-inaccessible"></a>Para tornar os recursos criptografados inacessíveis

1. Remova os bancos de dados que estão sendo criptografados pela chave potencialmente comprometida.

   O backup do banco de dados e dos arquivos de log é feito automaticamente, portanto, uma recuperação pontual do banco de dados poderá ser realizada a qualquer momento (contanto que você forneça a chave). Os bancos de dados deverão ser removidos antes da exclusão de um protetor de TDE ativo para evitar uma possível perda de dados de até 10 minutos das transações mais recentes.

2. Faça backup do material da chave do protetor de TDE no Key Vault.
3. Remover a chave potencialmente comprometida do Key Vault

## <a name="next-steps"></a>Próximos passos

- Saiba como girar o protetor de TDE de um servidor para atender aos requisitos de segurança: [Girar o Protetor de TDE usando PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- Introdução ao suporte Bring Your Own Key para TDE: [Ativar o TDE com sua própria chave do Key Vault usando PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)
