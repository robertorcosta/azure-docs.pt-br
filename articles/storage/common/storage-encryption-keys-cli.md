---
title: Use o Azure CLI para configurar chaves gerenciadas pelo cliente
titleSuffix: Azure Storage
description: Aprenda a usar o Azure CLI para configurar chaves gerenciadas pelo cliente com o Azure Key Vault para criptografia de armazenamento Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: ea944d4cfa3006c33f1dee3dd8e6ee6088681aa7
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618639"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-azure-cli"></a>Configure as chaves gerenciadas pelo cliente com o Azure Key Vault usando o Azure CLI

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Este artigo mostra como configurar um Azure Key Vault com chaves gerenciadas pelo cliente usando o Azure CLI. Para aprender como criar um cofre de chaves usando o Azure CLI, consulte [Quickstart: Configure e recupere um segredo do Azure Key Vault usando o Azure CLI](../../key-vault/quick-create-cli.md).

## <a name="assign-an-identity-to-the-storage-account"></a>Atribuir uma identidade à conta de armazenamento

Para habilitar as chaves gerenciadas pelo cliente para sua conta de armazenamento, primeiro atribua uma identidade gerenciada atribuída ao sistema à conta de armazenamento. Você usará essa identidade gerenciada para conceder permissões da conta de armazenamento para acessar o cofre-chave.

Para atribuir uma identidade gerenciada usando o Azure CLI, ligue [para a atualização da conta de armazenamento az](/cli/azure/storage/account#az-storage-account-update). Lembre-se de substituir os valores de espaço reservado entre parênteses por seus próprios valores.

```azurecli-interactive
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Para obter mais informações sobre a configuração de identidades gerenciadas atribuídas ao sistema com o Azure CLI, consulte [Configure identidades gerenciadas para recursos do Azure em uma VM Azure usando o Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Crie um novo cofre de chaves

O cofre-chave que você usa para armazenar chaves gerenciadas pelo cliente para criptografia de armazenamento Do Zure deve ter duas configurações de proteção chave ativadas, **Soft Delete** e Não **Purga .** Para criar um novo cofre de chaves usando O MEDOuoc s imite cli com essas configurações ativadas, execute os seguintes comandos. Lembre-se de substituir os valores de espaço reservado entre parênteses por seus próprios valores.

Para criar um novo cofre-chave usando o Azure CLI, chame [az keyvault create](/cli/azure/keyvault#az-keyvault-create). Lembre-se de substituir os valores de espaço reservado entre parênteses por seus próprios valores.

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-soft-delete \
    --enable-purge-protection
```

Para saber como ativar **o Soft Delete** e o Não **Purgar** em um cofre de chaves existente com o Azure CLI, consulte as seções intituladas **Habilitando a exclusão suave** e permitindo a **proteção de purga** em como usar [soft-delete com CLI](../../key-vault/key-vault-soft-delete-cli.md).

## <a name="configure-the-key-vault-access-policy"></a>Configure a política de acesso ao cofre de chaves

Em seguida, configure a diretiva de acesso para o cofre de chaves para que a conta de armazenamento tenha permissões para acessá-la. Nesta etapa, você usará a identidade gerenciada que você atribuiu anteriormente à conta de armazenamento.

Para definir a política de acesso para o cofre de chaves, chame [aaz keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy). Lembre-se de substituir os valores de espaço reservado entre parênteses por seus próprios valores.

```azurecli-interactive
storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)
az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group>
    --object-id $storage_account_principal \
    --key-permissions get unwrapKey wrapKey
```

## <a name="create-a-new-key"></a>Criar uma nova chave

Em seguida, crie uma chave no cofre da chave. Para criar uma chave, chame [aaz keyvault key create](/cli/azure/keyvault/key#az-keyvault-key-create). Lembre-se de substituir os valores de espaço reservado entre parênteses por seus próprios valores.

```azurecli-interactive
az keyvault key create
    --name <key> \
    --vault-name <key-vault>
```

Apenas as chaves RSA e RSA-HSM de 2048 bits são suportadas com criptografia de armazenamento Azure. Para obter mais informações sobre as chaves, consulte **chaves do Cofre de Chaves** em [chaves, segredos e certificados do Azure Key Vault](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

## <a name="configure-encryption-with-customer-managed-keys"></a>Configurar criptografia com chaves gerenciadas pelo cliente

Por padrão, a criptografia do Azure Storage usa chaves gerenciadas pela Microsoft. Configure sua conta de armazenamento do Azure para chaves gerenciadas pelo cliente e especifique a chave para associar à conta de armazenamento.

Para atualizar as configurações de criptografia da conta de armazenamento, chame [a atualização da conta de armazenamento az,](/cli/azure/storage/account#az-storage-account-update)como mostrado no exemplo a seguir. Inclua `--encryption-key-source` o parâmetro e `Microsoft.Keyvault` configure-o para habilitar as chaves gerenciadas pelo cliente para a conta de armazenamento. O exemplo também consulta o URI do cofre chave e a versão de chave mais recente, ambos os valores necessários para associar a chave à conta de armazenamento. Lembre-se de substituir os valores de espaço reservado entre parênteses por seus próprios valores.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
key_version=$(az keyvault key list-versions \
    --name <key> \
    --vault-name <key-vault> \
    --query [-1].kid \
    --output tsv | cut -d '/' -f 6)
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

## <a name="update-the-key-version"></a>Atualize a versão-chave

Quando você criar uma nova versão de uma chave, você precisará atualizar a conta de armazenamento para usar a nova versão. Primeiro, consulte o URI do cofre-chave chamando [az keyvault show](/cli/azure/keyvault#az-keyvault-show)e para a versão-chave chamando [az keyvault key-versions](/cli/azure/keyvault/key#az-keyvault-key-list-versions). Em seguida, ligue para a atualização da conta de [armazenamento az](/cli/azure/storage/account#az-storage-account-update) para atualizar as configurações de criptografia da conta de armazenamento para usar a nova versão da chave, como mostrado na seção anterior.

## <a name="use-a-different-key"></a>Use uma chave diferente

Para alterar a chave usada para a criptografia do Azure Storage, ligue [para a atualização da conta de armazenamento az,](/cli/azure/storage/account#az-storage-account-update) conforme mostrado na [Configuração de criptografia com chaves gerenciadas pelo cliente](#configure-encryption-with-customer-managed-keys) e forneça o novo nome e versão da chave. Se a nova chave estiver em um cofre de chaves diferente, atualize também o URI do cofre principal.

## <a name="revoke-customer-managed-keys"></a>Revogar chaves gerenciadas pelo cliente

Se você acredita que uma chave pode ter sido comprometida, você pode revogar as chaves gerenciadas pelo cliente removendo a política de acesso ao cofre de chaves. Para revogar uma chave gerenciada pelo cliente, chame o comando [az keyvault delete-policy,](/cli/azure/keyvault#az-keyvault-delete-policy) conforme mostrado no exemplo a seguir. Lembre-se de substituir os valores de espaço reservado entre parênteses com seus próprios valores e usar as variáveis definidas nos exemplos anteriores.

```azurecli-interactive
az keyvault delete-policy \
    --name <key-vault> \
    --object-id $storage_account_principal
```

## <a name="disable-customer-managed-keys"></a>Desativar chaves gerenciadas pelo cliente

Quando você desativa chaves gerenciadas pelo cliente, sua conta de armazenamento é novamente criptografada com chaves gerenciadas pela Microsoft. Para desativar as chaves gerenciadas pelo cliente, ligue [para a atualização da conta de armazenamento az](/cli/azure/storage/account#az-storage-account-update) e defina o `--encryption-key-source parameter` para, `Microsoft.Storage`como mostrado no exemplo a seguir. Lembre-se de substituir os valores de espaço reservado entre parênteses com seus próprios valores e usar as variáveis definidas nos exemplos anteriores.

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-source Microsoft.Storage
```

## <a name="next-steps"></a>Próximas etapas

- [Criptografia de armazenamento do Azure para dados em repouso](storage-service-encryption.md) 
- [O que é o Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
