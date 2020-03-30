---
title: Gire o protetor TDE - PowerShell
description: Saiba como girar o protetor de TDE (Transparent Data Encryption) para um servidor SQL do Azure.
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
ms.openlocfilehash: aaed06ac086893f63fde530e46b936b3fb637766
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067177"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector-using-powershell"></a>Girar o protetor de TDE (Transparent Data Encryption) usando PowerShell

Este artigo descreve a rotação de chave para um servidor SQL do Azure usando um protetor de TDE do Azure Key Vault. Girar o protetor TDE de um servidor Azure SQL significa mudar para uma nova chave assimétrica que protege os bancos de dados no servidor. A rotação de chaves é uma operação on-line e deve levar apenas alguns segundos para ser concluída, porque isso só descriptografa e recriptografa a chave de criptografia de dados do banco de dados, não todo o banco de dados.

Este guia aborda duas opções para girar o protetor de TDE no servidor.

> [!NOTE]
> Um SQL Data Warehouse pausado deve ser retomado antes das rotações de chave.

> [!IMPORTANT]
> Não exclua as versões anteriores da chave após um capotamento. Quando as teclas são substituídas, alguns dados ainda são criptografados com as chaves anteriores, como backups de banco de dados mais antigos.

## <a name="prerequisites"></a>Pré-requisitos

- Este guia de instruções assume que você já esteja usando uma chave do Azure Key Vault como protetor de TDE para um Data Warehouse ou Banco de Dados SQL do Azure. Consulte [Transparent Data Encryption com suporte de BYOK](transparent-data-encryption-byok-azure-sql.md).
- Você deve ter o Azure PowerShell instalado e executado.
- [Recomendado, mas opcional] Crie o material da chave para o protetor de TDE em um HSM (Módulo de Segurança de Hardware) ou o armazenamento de chaves local primeiro e importe o material da chave para o Azure Key Vault. Siga as [instruções para usar um HSM (Módulo de Segurança de Hardware) e Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started) para saber mais.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Para obter instruções de instalação do módulo Az, confira [Instalar o Azure PowerShell](/powershell/azure/install-az-ps). Para cmdlets específicos, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> O módulo RM (PowerShell Azure Resource Manager, gerente de recursos do Azure) ainda é suportado pelo Banco de Dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo Az.Sql. O módulo AzureRM continuará recebendo correções de bugs até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. Para obter mais informações sobre sua compatibilidade, consulte [Introduzindo o novo módulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Para instalação, consulte [Install Azure CLI](/cli/azure/install-azure-cli).

* * *

## <a name="manual-key-rotation"></a>Rotação manual de chaves

A rotação manual da tecla usa os seguintes comandos para adicionar uma chave completamente nova, que pode estar um novo nome de chave ou até mesmo outro cofre de chaves. Usar essa abordagem dá suporte à adição da mesma chave a diferentes cofres de chaves para dar suporte a cenários de alta disponibilidade e recuperação de desastres geográficos.

> [!NOTE]
> O comprimento combinado para o nome do cofre de chaves e o nome da chave não pode exceder 94 caracteres.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Use os [cmdlets Add-AzKeyVaultKey,](/powershell/module/az.keyvault/Add-AzKeyVaultKey) [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)e [Set-AzSqlServerTransparentDataEncryptionProtectProtector.](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)

```powershell
# add a new key to Key Vault
Add-AzKeyVaultKey -VaultName <keyVaultName> -Name <keyVaultKeyName> -Destination <hardwareOrSoftware>

# add the new key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
  
# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault -KeyId <keyVaultKeyId> `
   -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Use os comandos [de configuração de chave de chave az keyvault,](/cli/azure/keyvault/key#az-keyvault-key-create) [az sql server create](/cli/azure/sql/server/key#az-sql-server-key-create)e [az sql server tde-key set.](/cli/azure/sql/server/tde-key#az-sql-server-tde-key-set)

```azurecli
# add a new key to Key Vault
az keyvault key create --name <keyVaultKeyName> --vault-name <keyVaultName> --protection <hsmOrSoftware>

# add the new key from Key Vault to the server
az sql server key create --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>

# set the key as the TDE protector for all resources under the server
az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
```

* * *

## <a name="useful-powershell-cmdlets"></a>Cmdlets úteis do PowerShell

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

- Para alternar o protetor TDE do modo BYOK gerenciado pela Microsoft, use o [cmdlet Set-AzSqlServerTransparentDataEncryptionProtector.](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault `
       -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Para mudar o protetor TDE do modo BYOK para o gerenciado pela Microsoft, use o [cmdlet Set-AzSqlServerTransparentDataEncryptionProtector.](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type ServiceManaged `
       -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Os exemplos a seguir usam [conjunto de tecla tde-key do servidor az sql](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector).

- Para alternar o protetor TDE do modo BYOK gerenciado pela Microsoft,

   ```azurecli
   az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

- Para mudar o protetor TDE do modo BYOK para o gerenciado pela Microsoft,

   ```azurecli
   az sql server tde-key set --server-key-type ServiceManaged --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

* * *

## <a name="next-steps"></a>Próximas etapas

- Em caso de risco de segurança, saiba como remover um protetor TDE potencialmente comprometido: [Remova uma chave potencialmente comprometida](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)

- Comece com a integração do Azure Key Vault e traga seu próprio suporte de chave para TDE: [Ative o TDE usando sua própria chave do Key Vault usando o PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)
