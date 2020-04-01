---
title: Use o PowerShell para configurar chaves gerenciadas pelo cliente
titleSuffix: Azure Storage
description: Saiba como usar o PowerShell para configurar chaves gerenciadas pelo cliente para criptografia de armazenamento do Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/10/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 590f129d0ce41c3a8afc80340f26bc31c2fc789a
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478189"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-powershell"></a>Configure as chaves gerenciadas pelo cliente com o Azure Key Vault usando o PowerShell

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Este artigo mostra como configurar um Azure Key Vault com chaves gerenciadas pelo cliente usando o PowerShell. Para aprender como criar um cofre de chaves usando o Azure CLI, consulte [Quickstart: Configure e recupere um segredo do Azure Key Vault usando o PowerShell](../../key-vault/quick-create-powershell.md).

## <a name="assign-an-identity-to-the-storage-account"></a>Atribuir uma identidade à conta de armazenamento

Para habilitar as chaves gerenciadas pelo cliente para sua conta de armazenamento, primeiro atribua uma identidade gerenciada atribuída ao sistema à conta de armazenamento. Você usará essa identidade gerenciada para conceder permissões da conta de armazenamento para acessar o cofre-chave.

Para atribuir uma identidade gerenciada usando o PowerShell, chame [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount). Lembre-se de substituir os valores de espaço reservado entre parênteses por seus próprios valores.

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Para obter mais informações sobre a configuração de identidades gerenciadas atribuídas ao sistema com o PowerShell, consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM Azure usando o PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Crie um novo cofre de chaves

Para criar um novo cofre de chaves usando o PowerShell, ligue para [o New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). O cofre-chave que você usa para armazenar chaves gerenciadas pelo cliente para criptografia de armazenamento Do Zure deve ter duas configurações de proteção chave ativadas, **Soft Delete** e Não **Purga .**

Lembre-se de substituir os valores de espaço reservado entre parênteses por seus próprios valores.

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

Para saber como ativar **o Soft Delete** e o Não **Purgar** em um cofre de chaves existente com o PowerShell, consulte as seções intituladas **Habilitando a exclusão suave** e permitindo a **proteção de purga** em como usar a [exclusão suave com o PowerShell](../../key-vault/key-vault-soft-delete-powershell.md).

## <a name="configure-the-key-vault-access-policy"></a>Configure a política de acesso ao cofre de chaves

Em seguida, configure a diretiva de acesso para o cofre de chaves para que a conta de armazenamento tenha permissões para acessá-la. Nesta etapa, você usará a identidade gerenciada que você atribuiu anteriormente à conta de armazenamento.

Para definir a política de acesso para o cofre de chaves, ligue para [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Lembre-se de substituir os valores de espaço reservado entre parênteses com seus próprios valores e usar as variáveis definidas nos exemplos anteriores.

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>Criar uma nova chave

Em seguida, crie uma nova chave no cofre da chave. Para criar uma nova chave, ligue para [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Lembre-se de substituir os valores de espaço reservado entre parênteses com seus próprios valores e usar as variáveis definidas nos exemplos anteriores.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

Apenas as chaves RSA e RSA-HSM de 2048 bits são suportadas com criptografia de armazenamento Azure. Para obter mais informações sobre as chaves, consulte **chaves do Cofre de Chaves** em [chaves, segredos e certificados do Azure Key Vault](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

## <a name="configure-encryption-with-customer-managed-keys"></a>Configurar criptografia com chaves gerenciadas pelo cliente

Por padrão, a criptografia do Azure Storage usa chaves gerenciadas pela Microsoft. Nesta etapa, configure sua conta do Azure Storage para usar chaves gerenciadas pelo cliente e especifique a chave para associar-se à conta de armazenamento.

Chamada [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) para atualizar as configurações de criptografia da conta de armazenamento, conforme mostrado no exemplo a seguir. Inclua a opção **-KeyvaultEncryption** para habilitar chaves gerenciadas pelo cliente para a conta de armazenamento. Lembre-se de substituir os valores de espaço reservado entre parênteses com seus próprios valores e usar as variáveis definidas nos exemplos anteriores.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

## <a name="update-the-key-version"></a>Atualize a versão-chave

Quando você criar uma nova versão de uma chave, você precisará atualizar a conta de armazenamento para usar a nova versão. Primeiro, ligue para [get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) para obter a versão mais recente da chave. Em seguida, ligue para [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) para atualizar as configurações de criptografia da conta de armazenamento para usar a nova versão da chave, como mostrado na seção anterior.

## <a name="use-a-different-key"></a>Use uma chave diferente

Para alterar a chave usada para a criptografia de armazenamento do Azure, ligue [para Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) como mostrado na [Configuração de criptografia com chaves gerenciadas pelo cliente](#configure-encryption-with-customer-managed-keys) e forneça o novo nome e versão da chave. Se a nova chave estiver em um cofre de chaves diferente, atualize também o URI do cofre principal.

## <a name="revoke-customer-managed-keys"></a>Revogar chaves gerenciadas pelo cliente

Se você acredita que uma chave pode ter sido comprometida, você pode revogar as chaves gerenciadas pelo cliente removendo a política de acesso ao cofre de chaves. Para revogar uma chave gerenciada pelo cliente, chame o comando [Remove-AzKeyVaultAccessPolicy,](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy) conforme mostrado no exemplo a seguir. Lembre-se de substituir os valores de espaço reservado entre parênteses com seus próprios valores e usar as variáveis definidas nos exemplos anteriores.

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

## <a name="disable-customer-managed-keys"></a>Desativar chaves gerenciadas pelo cliente

Quando você desativa chaves gerenciadas pelo cliente, sua conta de armazenamento é novamente criptografada com chaves gerenciadas pela Microsoft. Para desativar as chaves gerenciadas pelo cliente, ligue `-StorageEncryption` para [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) com a opção, conforme mostrado no exemplo a seguir. Lembre-se de substituir os valores de espaço reservado entre parênteses com seus próprios valores e usar as variáveis definidas nos exemplos anteriores.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

## <a name="next-steps"></a>Próximas etapas

- [Criptografia de armazenamento do Azure para dados em repouso](storage-service-encryption.md)
- [O que é o Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
