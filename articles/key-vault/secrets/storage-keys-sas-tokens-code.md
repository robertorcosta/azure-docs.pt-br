---
title: Buscar tokens de Assinatura de Acesso Compartilhado no código | Azure Key Vault
description: O recurso de conta de armazenamento gerenciada fornece uma integração perfeita entre o Azure Key Vault e uma conta de armazenamento do Azure.
ms.topic: tutorial
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: 6530434e36f7c9a9a60a9782bcf2dce7ba447dab
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88584906"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>Buscar tokens de assinatura de acesso compartilhado no código

Você pode gerenciar sua conta de armazenamento com tokens SAS (Assinatura de Acesso Compartilhado) armazenados no cofre de chaves. Para obter mais informações, confira [Permitir acesso limitado aos recursos do Armazenamento do Azure usando a SAS](../../storage/common/storage-sas-overview.md).

Este artigo fornece exemplos de código .NET que buscam um token SAS e executam operações com ele. Para obter informações sobre como criar e armazenar tokens SAS, confira [Gerenciar chaves de conta de armazenamento com o Key Vault e a CLI do Azure](overview-storage-keys.md) ou [Gerenciar chaves de conta de armazenamento com o Key Vault e o Azure PowerShell](overview-storage-keys-powershell.md).

## <a name="code-samples"></a>Exemplos de código

Neste exemplo, o código busca um token SAS do cofre de chaves, usa-o para criar uma conta de armazenamento e cria um cliente de serviço Blob.

```cs
// The shared access signature is stored as a secret in keyvault. 
// After you get a security token, create a new SecretClient with vault credentials and the key vault URI.
// The format for the key vault URI (kvuri) is https://<YourKeyVaultName>.vault.azure.net

var kv = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());

// Now retrive your storage SAS token from Key Vault using the name of the secret (secretName).

KeyVaultSecret secret = client.GetSecret(secretName);
var sasToken = secret.Value;

// Create new storage credentials using the SAS token.
StorageCredentials accountSAS = new StorageCredentials(sasToken);

// Use these credentials and your storage account name to create a Blob service client.
CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, "<storage-account>", endpointSuffix: null, useHttps: true);
CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();
```

Se o token de Assinatura de Acesso Compartilhado estiver prestes a expirar, você poderá buscá-lo no cofre de chaves e atualizar o código.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
KeyVaultSecret secret = client.GetSecret(secretName);
var sasToken = secret.Value;
accountSAS.UpdateSASToken(sasToken);
```


## <a name="next-steps"></a>Próximas etapas
- Saiba como [Permitir acesso limitado aos recursos de armazenamento do Azure usando a SAS](../../storage/common/storage-sas-overview.md).
- Saiba como [Gerenciar chaves de conta de armazenamento com o Key Vault e a CLI do Azure](overview-storage-keys.md) ou o [Azure PowerShell](overview-storage-keys-powershell.md).
- Confira [Exemplos de chaves de conta de armazenamento gerenciadas](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
