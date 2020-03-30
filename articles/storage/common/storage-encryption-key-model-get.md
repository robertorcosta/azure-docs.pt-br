---
title: Determine qual modelo de chave de criptografia está em uso para a conta de armazenamento
titleSuffix: Azure Storage
description: Use o portal Azure, o PowerShell ou o Azure CLI para verificar como as chaves de criptografia estão sendo gerenciadas para a conta de armazenamento. As chaves podem ser gerenciadas pela Microsoft (o padrão) ou pelo cliente. As chaves gerenciadas pelo cliente devem ser armazenadas no Azure Key Vault.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/13/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 0df0ba4ce76d249bcb4738b41c94677e061f14ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409841"
---
# <a name="determine-which-azure-storage-encryption-key-model-is-in-use-for-the-storage-account"></a>Determine qual modelo de chave de criptografia do Azure Storage está em uso para a conta de armazenamento

Os dados em sua conta de armazenamento são criptografados automaticamente pelo Azure Storage. A criptografia do Azure Storage oferece duas opções para gerenciar chaves de criptografia no nível da conta de armazenamento:

- **Chaves gerenciadas pela Microsoft.** Por padrão, a Microsoft gerencia as chaves usadas para criptografar sua conta de armazenamento.
- **Chaves gerenciadas pelo cliente.** Você pode optar opcionalmente por gerenciar chaves de criptografia para sua conta de armazenamento. As chaves gerenciadas pelo cliente devem ser armazenadas no Azure Key Vault.

Além disso, você pode fornecer uma chave de criptografia ao nível de uma solicitação individual para algumas operações de armazenamento Blob. Quando uma chave de criptografia é especificada na solicitação, essa chave substitui a chave de criptografia que está ativa na conta de armazenamento. Para obter mais informações, consulte [Especificar uma chave fornecida pelo cliente em uma solicitação de armazenamento Blob](../blobs/storage-blob-customer-provided-key.md).

Para obter mais informações sobre chaves de criptografia, consulte [a criptografia do Azure Storage para obter dados em repouso](storage-service-encryption.md).

## <a name="check-the-encryption-key-model-for-the-storage-account"></a>Verifique o modelo de chave de criptografia para a conta de armazenamento

Para determinar se uma conta de armazenamento está usando chaves gerenciadas pela Microsoft ou chaves gerenciadas pelo cliente para criptografia, use uma das seguintes abordagens.

# <a name="azure-portal"></a>[Portal Azure](#tab/portal)

Para verificar o modelo de criptografia da conta de armazenamento usando o portal Azure, siga estas etapas:

1. No portal do Azure, navegue até sua conta de armazenamento.
1. Selecione a **configuração Criptografia** e observe a configuração.

A imagem a seguir mostra uma conta de armazenamento criptografada com chaves gerenciadas pela Microsoft:

![Exibir conta criptografada com chaves gerenciadas pela Microsoft](media/storage-encryption-key-model-get/microsoft-managed-encryption-key-setting-portal.png)

E a imagem a seguir mostra uma conta de armazenamento criptografada com chaves gerenciadas pelo cliente:

![Captura de tela mostrando a configuração da chave de criptografia no portal Azure](media/storage-encryption-key-model-get/customer-managed-encryption-key-setting-portal.png)

# <a name="powershell"></a>[Powershell](#tab/powershell)

Para verificar o modelo de criptografia da conta de armazenamento usando o PowerShell, ligue para o comando [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) e verifique a propriedade **KeySource** para obter a conta.

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$account.Encryption.KeySource
```

Se o valor da propriedade `Microsoft.Storage` **KeySource** for, então a conta será criptografada com chaves gerenciadas pela Microsoft. Se o valor da propriedade `Microsoft.Keyvault` **KeySource** for, a conta será criptografada com chaves gerenciadas pelo cliente.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Para verificar o modelo de criptografia da conta de armazenamento usando o Azure CLI, ligue para o comando [az storage account show](/cli/azure/storage/account#az-storage-account-show) e verifique a propriedade **keySource** para a conta.

```azurecli-interactive
key_source=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query encryption.keySource \
    --output tsv)
```

Se o valor da propriedade `Microsoft.Storage` **keySource** for , então a conta será criptografada com chaves gerenciadas pela Microsoft. Se o valor da propriedade `Microsoft.Keyvault` **keySource** for , então a conta será criptografada com chaves gerenciadas pelo cliente.

---

## <a name="next-steps"></a>Próximas etapas

- [Criptografia de armazenamento do Azure para dados em repouso](storage-service-encryption.md)
- [Use chaves gerenciadas pelo cliente com o Azure Key Vault para gerenciar a criptografia de armazenamento do Azure](encryption-customer-managed-keys.md)