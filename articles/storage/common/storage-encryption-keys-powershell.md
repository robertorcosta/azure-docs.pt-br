---
title: Usar o PowerShell para configurar chaves gerenciadas pelo cliente
titleSuffix: Azure Storage
description: Saiba como usar o PowerShell para configurar chaves gerenciadas pelo cliente para a criptografia de armazenamento do Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/24/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 1c928056ec0e7b101d991c8d8c8db3bd659251ba
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88799121"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-powershell"></a>Configurar chaves gerenciadas pelo cliente com Azure Key Vault usando o PowerShell

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Este artigo mostra como configurar um Azure Key Vault com chaves gerenciadas pelo cliente usando o PowerShell. Para saber como criar um cofre de chaves usando CLI do Azure, consulte [início rápido: definir e recuperar um segredo de Azure Key Vault usando o PowerShell](../../key-vault/secrets/quick-create-powershell.md).

## <a name="assign-an-identity-to-the-storage-account"></a>Atribuir uma identidade à conta de armazenamento

Para habilitar chaves gerenciadas pelo cliente para sua conta de armazenamento, primeiro atribua uma identidade gerenciada atribuída pelo sistema à conta de armazenamento. Você usará essa identidade gerenciada para conceder permissões de conta de armazenamento para acessar o cofre de chaves.

Para atribuir uma identidade gerenciada usando o PowerShell, chame [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount). Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores.

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Para obter mais informações sobre como configurar identidades gerenciadas atribuídas pelo sistema com o PowerShell, consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando o PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Criar um novo cofre de chaves

Para criar um novo cofre de chaves usando o PowerShell, instale a versão 2.0.0 ou posterior do módulo do PowerShell [AZ. keyvault](https://www.powershellgallery.com/packages/Az.KeyVault/2.0.0) . Em seguida, chame [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) para criar um novo cofre de chaves.

O cofre de chaves que você usa para armazenar chaves gerenciadas pelo cliente para criptografia de armazenamento do Azure deve ter duas configurações de proteção de chave habilitadas, **exclusão reversível** e **não limpar**. Na versão 2.0.0 e posterior do módulo AZ. keyvault, a exclusão reversível é habilitada por padrão quando você cria um novo cofre de chaves.

O exemplo a seguir cria um novo cofre de chaves com a **exclusão reversível** e **não limpa** as propriedades habilitadas. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores.

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnablePurgeProtection
```

Para saber como habilitar a **exclusão reversível** e **não limpar** em um cofre de chaves existente com o PowerShell, consulte as seções intituladas **habilitar a exclusão reversível** e **habilitar a proteção de limpeza** em [como usar a exclusão reversível com o PowerShell](../../key-vault/general/soft-delete-powershell.md).

## <a name="configure-the-key-vault-access-policy"></a>Configurar a política de acesso do cofre de chaves

Em seguida, configure a política de acesso para o cofre de chaves para que a conta de armazenamento tenha permissões para acessá-la. Nesta etapa, você usará a identidade gerenciada que você atribuiu anteriormente à conta de armazenamento.

Para definir a política de acesso para o cofre de chaves, chame [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores e usar as variáveis definidas nos exemplos anteriores.

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

## <a name="create-a-new-key"></a>Criar uma nova chave

Em seguida, crie uma nova chave no cofre de chaves. Para criar uma nova chave, chame [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores e usar as variáveis definidas nos exemplos anteriores.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

A criptografia de armazenamento do Azure dá suporte às chaves RSA e RSA-HSM de tamanhos 2048, 3072 e 4096. Para obter mais informações sobre chaves, consulte **Key Vault chaves** em [sobre Azure Key Vault chaves, segredos e certificados](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

## <a name="configure-encryption-with-customer-managed-keys"></a>Configurar a criptografia com chaves gerenciadas pelo cliente

Por padrão, a criptografia de armazenamento do Azure usa chaves gerenciadas pela Microsoft. Nesta etapa, configure sua conta de armazenamento do Azure para usar chaves gerenciadas pelo cliente com Azure Key Vault e, em seguida, especifique a chave a ser associada à conta de armazenamento.

Ao configurar a criptografia com chaves gerenciadas pelo cliente, você pode optar por atualizar automaticamente a chave usada para criptografia quando a versão da chave for alterada no cofre de chaves associado. Como alternativa, você pode especificar explicitamente uma versão de chave a ser usada para criptografia até que a versão da chave seja atualizada manualmente.

> [!NOTE]
> Para girar uma chave, crie uma nova versão da chave em Azure Key Vault. O armazenamento do Azure não manipula a rotação da chave no Azure Key Vault, portanto, você precisará girar a chave manualmente ou criar uma função para girá-la em um agendamento.

### <a name="configure-encryption-to-automatically-update-the-key-version"></a>Configurar a criptografia para atualizar automaticamente a versão de chave

Para configurar a criptografia com chaves gerenciadas pelo cliente para atualizar automaticamente a versão da chave, instale o módulo [AZ. Storage](https://www.powershellgallery.com/packages/Az.Storage) , versão 2.0.0 ou posterior.

Para atualizar automaticamente a versão de chave de uma chave gerenciada pelo cliente, omita a versão da chave ao configurar a criptografia com chaves gerenciadas pelo cliente para a conta de armazenamento. Chame [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) para atualizar as configurações de criptografia da conta de armazenamento, conforme mostrado no exemplo a seguir, e inclua a opção **-KeyvaultEncryption** para habilitar chaves gerenciadas pelo cliente para a conta de armazenamento. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores e usar as variáveis definidas nos exemplos anteriores.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVaultUri $keyVault.VaultUri
```

### <a name="configure-encryption-for-manual-updating-of-key-versions"></a>Configurar a criptografia para atualização manual de versões de chave

Para especificar explicitamente uma versão de chave a ser usada para criptografia, forneça a versão de chave ao configurar a criptografia com chaves gerenciadas pelo cliente para a conta de armazenamento. Chame [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) para atualizar as configurações de criptografia da conta de armazenamento, conforme mostrado no exemplo a seguir, e inclua a opção **-KeyvaultEncryption** para habilitar chaves gerenciadas pelo cliente para a conta de armazenamento. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores e usar as variáveis definidas nos exemplos anteriores.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

Ao atualizar manualmente a versão da chave, você precisará atualizar as configurações de criptografia da conta de armazenamento para usar a nova versão. Primeiro, chame [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) para obter a versão mais recente da chave. Em seguida, chame [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) para atualizar as configurações de criptografia da conta de armazenamento para usar a nova versão da chave, conforme mostrado no exemplo anterior.

## <a name="use-a-different-key"></a>Usar uma chave diferente

Para alterar a chave usada para a criptografia de armazenamento do Azure, chame [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) conforme mostrado em [Configurar criptografia com chaves gerenciadas pelo cliente](#configure-encryption-with-customer-managed-keys) e forneça o novo nome e versão da chave. Se a nova chave estiver em um cofre de chaves diferente, atualize também o URI do cofre de chaves.

## <a name="revoke-customer-managed-keys"></a>Revogar chaves gerenciadas pelo cliente

Você pode revogar chaves gerenciadas pelo cliente removendo a política de acesso do cofre de chaves. Para revogar uma chave gerenciada pelo cliente, chame o comando [Remove-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy) , conforme mostrado no exemplo a seguir. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores e usar as variáveis definidas nos exemplos anteriores.

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

## <a name="disable-customer-managed-keys"></a>Desabilitar chaves gerenciadas pelo cliente

Quando você desabilita chaves gerenciadas pelo cliente, sua conta de armazenamento é novamente criptografada com chaves gerenciadas pela Microsoft. Para desabilitar as chaves gerenciadas pelo cliente, chame [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) com a `-StorageEncryption` opção, conforme mostrado no exemplo a seguir. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores e usar as variáveis definidas nos exemplos anteriores.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

## <a name="next-steps"></a>Próximas etapas

- [Criptografia do Armazenamento do Azure para dados em repouso](storage-service-encryption.md)
- [O que é Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
