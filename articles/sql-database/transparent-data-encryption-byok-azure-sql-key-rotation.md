---
title: Girar o protetor TDE – PowerShell
description: Saiba como girar o protetor de TDE (Transparent Data Encryption) para um servidor SQL do Azure.
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
ms.openlocfilehash: 5bfcacb9348940e0b36947f6e4e0d27839de35bb
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824677"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector-using-powershell"></a>Girar o protetor de TDE (Transparent Data Encryption) usando PowerShell

Este artigo descreve a rotação de chave para um servidor SQL do Azure usando um protetor de TDE do Azure Key Vault. Girar um protetor de TDE do servidor SQL do Azure significa alternar para uma nova chave assimétrica que protege os bancos de dados no servidor. A rotação de chave é uma operação online e deve demorar apenas alguns segundos para ser concluída, porque isso somente descriptografa e criptografa novamente a chave de criptografia de dados do banco de dados, não o banco de dados inteiro.

Este guia aborda duas opções para girar o protetor de TDE no servidor.

> [!NOTE]
> Um SQL Data Warehouse pausado deve ser retomado antes das rotações de chave.
>

> [!IMPORTANT]
> **Não Exclua** as versões anteriores da chave após uma substituição.  Quando as teclas são substituídas, alguns dados ainda são criptografados com as chaves anteriores, como backups de banco de dados mais antigos. 
>

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, consulte [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos.

- Este guia de instruções assume que você já esteja usando uma chave do Azure Key Vault como protetor de TDE para um Data Warehouse ou Banco de Dados SQL do Azure. Consulte [Transparent Data Encryption com suporte de BYOK](transparent-data-encryption-byok-azure-sql.md).
- Você deve ter Azure PowerShell instalado e em execução.
- [Recomendado, mas opcional] Crie o material da chave para o protetor de TDE em um HSM (Módulo de Segurança de Hardware) ou o armazenamento de chaves local primeiro e importe o material da chave para o Azure Key Vault. Siga as [instruções para usar um HSM (Módulo de Segurança de Hardware) e Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started) para saber mais.

## <a name="manual-key-rotation"></a>Rotação manual de chaves

A rotação de chave manual usa os cmdlets [Add-AzKeyVaultKey](/powershell/module/az.keyvault/Add-AzKeyVaultKey), [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)e [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) para adicionar uma chave totalmente nova, que pode estar sob um novo nome de chave ou até mesmo outra chave armazenadas. Usar essa abordagem dá suporte à adição da mesma chave a diferentes cofres de chaves para dar suporte a cenários de alta disponibilidade e recuperação de desastres geográficos.

>[!NOTE]
>O comprimento combinado para o nome do cofre de chaves e o nome da chave não pode exceder 94 caracteres.

   ```powershell
   # Add a new key to Key Vault
   Add-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -Destination <HardwareOrSoftware>

   # Add the new key from Key Vault to the server
   Add-AzSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
  
   <# Set the key as the TDE protector for all resources under the server #>
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```


## <a name="other-useful-powershell-cmdlets"></a>Outros cmdlets do PowerShell úteis

- Para alternar o protetor de TDE do modo gerenciado pela Microsoft para o BYOK, use o cmdlet [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) .

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Para alternar o protetor de TDE de modo BYOK para gerenciado pela Microsoft, use o cmdlet [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) .

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type ServiceManaged `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName> 
   ``` 

## <a name="next-steps"></a>Próximas etapas

- Em caso de risco de segurança, saiba como remover um protetor de TDE potencialmente comprometido: [Remover uma chave potencialmente comprometida](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md) 

- Introdução à integração de Azure Key Vault e Bring Your Own Key suporte para TDE: [ative o TDE usando sua própria chave de Key Vault usando o PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)
